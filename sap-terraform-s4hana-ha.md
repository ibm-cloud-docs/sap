---

copyright:
  years: 2022
lastupdated: "2022-12-15"

subcollection: sap

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip} 
{:ui: .ph data-hd-interface="ui"}
{:terraform: .ph data-hd-interface="terraform"}

# Automating SAP workload S/4HANA HA deployment on {{site.data.keyword.cloud}} VPC with Terraform and Ansible
{: #automate-s4hana-ha-terraform-ansible}

Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) infrastructure resources so that you can rapidly build complex cloud environments. {{site.data.keyword.vpc_short}} infrastructure consists of SAP certified hardware that uses Intel&reg; Xeon CPUs and other Intel&reg; technologies. 
{: shortdesc}

You can use Terraform scripts to create a single-tier VPC and create the SAP and Db2 infrastructure on the VPC. The Terraform scripts use the VPC information that you provide and then call the Ansible playbook to create the SAP architecture on the specified VPC.

You have the CLI deployment method from:

* Terraform scripts run from the CLI on your bastion server

It contains:
* Terraform scripts for deploying: one Power Placement group to include all the four VMs involved in this solution
* Terraform scripts for deploying four VSIs in an existing VPC with subnet and security group configurations. The VSIs scope: two for the HANA database cluster instance and two for the SAP application cluster.
* Terraform scripts for deploying and configuring three Application Load Balancers like HANA DB, SAP ASCS/ERS
* Terraform scripts for deploying and configuring one VPC DNS service that is used to map the ALB FQDN to the SAP ASCS/ERS and HANA virtual hostnames
* Terraform scripts for deploying and configuring seven file shares for VPC
* Ansible scripts for OS requirements installation and configuration for SAP applications
* Ansible scripts for cluster components installation
* Ansible scripts for SAP application cluster configuration and SAP HANA cluster configuration
* Ansible scripts for HANA installation
* Ansible scripts for HANA DB backup
* Ansible scripts for HANA system replica configuration
* Ansible scripts for ASCS and ERS instances installation
* Ansible scripts for DB load
* Ansible scripts for primary and additional application servers installation

Ansible is started by Terraform and must be available on the same host.
{: note}

## What is created
{: #automate-s4hana-ha-what-is-created}

The scripts use the information that you provide for an existing VPC (provisioned with SAP Bastion solution) and deploy HA for S/4HANA with HANA DB. You specify the information for the VPC to use in the `input.auto.tfvars file`.

The scripts call the Ansible Playbook to install the SAP architecture.

## Script files
{: #automate-s4hana-ha-script-files}

The configuration and script files are provided on GitHub. Each supported interface for the SAP solution installation has its own folder in the GitHub repository.

## Terraform deployment
{: #automate-s4hana-ha-terraform-deployment}

You use Terraform on the bastion server CLI to download and run the scripts that are located in https://github.com/IBM-Cloud/sap-s4hana-sz-ha. 

To run the Terraform scripts, you modify:
* The `input.auto.tfvars` file to specify the information for your solution: 
    * Enter the floating IP and subnet information from the bastion server.
    * Enter existing VPC information:
        - VPC name
        - Security group
        - Subnet
        - Hostname
        - Profile
        - Image
        - Up to two SSH keys
    * You can change the default SAP system configuration settings to match your solution.
    * You also specify the location where you downloaded the SAP kits.

The {{site.data.keyword.terraform-provider_full_notm}} on {{site.data.keyword.cloud_notm}} uses these configuration files to install **SAP HA S/4HANA** on the specified VPC in your {{site.data.keyword.cloud_notm}} account.

## Support - Terraform and Schematics
{: #automate-s4hana-ha-support}

There are no warranties of any kind, and there is no service or technical support available for these materials from IBM®. As a recommended practice, review carefully any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, readapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Before you begin
{: #automate-s4hana-ha-begin}
 
Before you use the scripts in the bastion CLI or {{site.data.keyword.bpshort}}:

* Set up your account to access the VPC. Make sure that your account is [upgraded to a paid account](/docs/account?topic=account-accountfaqs#changeacct).
* If you have not already, create a bastion server to store the SAP kits. For more information, see [Automate SAP bastion server - SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server). You need the floating IP from your bastion server for deployment.
* Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. Ansible decompresses the files. For more information, see the `README` file, in the respective GitHub repository for {{site.data.keyword.bpshort}} and Terraform and on the About page for the Catalog Tile.
* [Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey&interface=ui#create_user_key). The API key is used to authenticate with the {{site.data.keyword.cloud_notm}} platform and to determine your permissions for {{site.data.keyword.cloud_notm}} services.
* [Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.
* (Optional - Catalog Tile) create secrets for your credentials and passwords by using the [Secrets Manager](/docs/secrets-manager?topic=secrets-manager-arbitrary-secrets&interface=ui).

## Deploying SAP HA S/4HANA by using Terraform with the bastion server CLI
{: #automate-s4hana-ha-deploy-terraform}

Use these steps to configure the {{site.data.keyword.cloud_notm}} Provider plug-in and use Terraform to install **SAP HA S/4HANA** on your existing VPC. The scripts can take 1 - 2 hours to complete.

1. Access the bastion server CLI.
2. Clone the solution repository from `https://github.com/IBM-Cloud/sap-s4hana-sz-ha` and `cd` to the `sap-s4hana-sz-ha/cli/terraform` folder.

    ```terraform
    git clone https://github.com/IBM-Cloud/sap-s4hana-sz-ha.git

    cd sap-s4hana-sz-ha/cli/terraform
    ```

3. Specify your VPC. Modify the `input.auto.tfvars` file to specify the information for the existing VPC, your zone, VPC and component names, profile, and image. You need your 40-digit SSH key ID for this file. The second SSH key is optional. For more options for profile, see [Instance Profiles](/docs/vpc?topic=vpc-profiles). For more options for images, see [Images](/docs/vpc?topic=vpc-about-images). For descriptions of the variables, see the [`README` file](https://github.com/IBM-Cloud/sap-s4hana-sz-ha/blob/main/README.md){: external}.

    ```terraform
    ######################################################
    # General VPC variables:
    ######################################################

    REGION = "eu-de"
    # Region for the VSI. Supported regions: https://cloud.ibm.com/docs/containers?topic=containers-regions-and-zones#zones-vpc
    # Example: REGION = "eu-de"

    ZONE = "eu-de-2"
    # Availability zone for VSI. Supported zones: https://cloud.ibm.com/docs/containers?topic=containers-regions-and-zones#zones-vpc
    # Example: ZONE = "eu-de-2"

    DOMAIN_NAME = "example.com"
    # The DOMAIN_NAME variable should contain at least one "." as a separator. It is a private domain and it is not reacheable to and from the outside world.
    # The DOMAIN_NAME variable could be like a subdomain name. Ex.: staging.example.com
    # Domain names can only use letters, numbers, and hyphens.
    # Hyphens cannot be used at the beginning or end of the domain name.
    # You can't use a domain name that is already in use.
    # Domain names are not case sensitive.

    ASCS-VIRT-HOSTNAME = "sapascs"
    # ASCS Virtual hostname
    # Default =  "sap($your_sap_sid)ascs"

    ERS-VIRT-HOSTNAME =  "sapers"
    # ERS Virtual Hostname  
    # Default =  "sap($your_sap_sid)ascs"

    HANA-VIRT-HOSTNAME = "dbhana"
    # Hana Virtual Hostname
    # Default = "db($your_hana_sid)hana"

    VPC = "ic4sap"
    # EXISTING VPC, previously created by the user in the same region as the VSI. The list of available VPCs: https://cloud.ibm.com/vpc-ext/network/vpcs
    # Example: VPC = "ic4sap"

    SECURITY_GROUP = "ic4sap-securitygroup"
    # EXISTING Security group, previously created by the user in the same VPC. The list of available Security Groups: https://cloud.ibm.com/vpc-ext/network/securityGroups
    # Example: SECURITY_GROUP = "ic4sap-securitygroup"

    RESOURCE_GROUP = "wes-automation"
    # EXISTING Resource group, previously created by the user. The list of available Resource Groups: https://cloud.ibm.com/account/resource-groups
    # Example: RESOURCE_GROUP = "wes-automation"

    SUBNET = "ic4sap-subnet"
    # EXISTING Subnet in the same region and zone as the VSI, previously created by the user. The list of available Subnets: https://cloud.ibm.com/vpc-ext/network/subnets
    # Example: SUBNET = "ic4sap-subnet"

    SSH_KEYS = [ "r010-57bfc315-f9e5-46bf-bf61-d87a24a9ce7a", "r010-3fcd9fe7-d4a7-41ce-8bb3-d96e936b2c7e" ]
    # List of SSH Keys UUIDs that are allowed to SSH as root to the VSI. The SSH Keys should be created for the same region as the VSI. The list of available SSH Keys UUIDs: https://cloud.ibm.com/vpc-ext/compute/sshKeys
    # Example: SSH_KEYS = ["r010-8f72b994-c17f-4500-af8f-d05680374t3c", "r011-8f72v884-c17f-4500-af8f-d05900374t3c"]

    ##########################################################
    # File Shares variables:
    ##########################################################

    share_profile = "tier-5iops"
    # Enter the IOPs (IOPS per GB) tier for File Share storage. Valid values are 3, 5, and 10.

    # File shares sizes:
    usrsap-as1      = "20"
    usrsap-as2      = "20"
    usrsap-sapascs  = "20"
    usrsap-sapers   = "20"
    usrsap-sapmnt   = "20"
    usrsap-sapsys   = "20"
    usrsap-trans    = "80"
    # Enter Custom File Shares sizes for SAP mounts.

    ##########################################################
    # DB VSI variables:
    ##########################################################
    DB-HOSTNAME-1 = "hanadb-1"
    # Hana Cluster VSI1 Hostname.
    # The Hostname for the DB VSI. The hostname should be up to 13 characters, as required by SAP
    # Default: DB-HOSTNAME-1 = "hanadb-$your_hana_sid-1"

    DB-HOSTNAME-2 = "hanadb-2"
    # Hana Cluster VSI2 Hostname.
    # The Hostname for the DB VSI. The hostname should be up to 13 characters, as required by SAP
    # Default: DB-HOSTNAME-2 = "hanadb-$your_hana_sid-2"

    DB-PROFILE = "mx2-16x128"
    # The DB VSI profile. Supported profiles for DB VSI: mx2-16x128. The list of available profiles: https://cloud.ibm.com/docs/vpc?topic=vpc-profiles&interface=ui

    DB-IMAGE = "ibm-redhat-8-4-amd64-sap-hana-4"
    # OS image for DB VSI. Supported OS images for DB VSIs: ibm-redhat-8-4-amd64-sap-hana-4
    # The list of available VPC Operating Systems supported by SAP: SAP note '2927211 - SAP Applications on IBM Virtual Private Cloud (VPC) Infrastructure environment' https://launchpad.support.sap.com/#/notes/2927211; The list of all available OS images: https://cloud.ibm.com/docs/vpc?topic=vpc-about-images
    # Example: DB-IMAGE = "ibm-redhat-8-4-amd64-sap-hana-4" 

    ##########################################################
    # SAP APP VSI variables:
    ##########################################################
    APP-HOSTNAME-1 = "sapapp-1"
    # SAP Cluster VSI1 Hostname.
    # The Hostname for the SAP APP VSI. The hostname should be up to 13 characters, as required by SAP
    # Default: APP-HOSTNAME-1 = "sapapp-$your_sap_sid-1"

    APP-HOSTNAME-2 = "sapapp-2"
    # SAP Cluster VSI2 Hostname.
    # The Hostname for the SAP APP VSI. The hostname should be up to 13 characters, as required by SAP
    # Default: APP-HOSTNAME-2 = "sapapp-$your_sap_sid-2"

    APP-PROFILE = "bx2-4x16"
    # The APP VSI profile. Supported profiles: bx2-4x16. The list of available profiles: https://cloud.ibm.com/docs/vpc?topic=vpc-profiles&interface=ui

    APP-IMAGE = "ibm-redhat-8-4-amd64-sap-hana-4"
    # OS image for SAP APP VSI. Supported OS images for APP VSIs: ibm-redhat-8-4-amd64-sap-hana-4.
    # The list of available VPC Operating Systems supported by SAP: SAP note '2927211 - SAP Applications on IBM Virtual Private Cloud (VPC) Infrastructure environment' https://launchpad.support.sap.com/#/notes/2927211; The list of all available OS images: https://cloud.ibm.com/docs/vpc?topic=vpc-about-images
    # Example: APP-IMAGE = "ibm-redhat-8-4-amd64-sap-hana-4"
    ```

4. Customize your SAP system configuration. In the same file, `input.auto.tfvars`, edit the SAP system configuration variables that are passed to the Ansible automated deployment. For descriptions of the variables, see the [`README` file](https://github.com/IBM-Cloud/sap-s4hana-sz-ha/blob/main/README.md){: external}.

    ```terraform
    ## SAP system configuration
    hana_sid = "HDB"
    # SAP HANA system ID. Should follow the SAP rules for SID naming.
    # Obs. This will be used also as identification number across different HA name resources. Duplicates are not allowed.
    # Example: hana_sid = "HDB"

    hana_sysno = "00"
    # SAP HANA instance number. Should follow the SAP rules for instance number naming.
    # Example: hana_sysno = "00"

    hana_system_usage = "custom"
    # System usage. Default: custom. Suported values: production, test, development, custom
    # Example: hana_system_usage = "custom"

    hana_components = "server"
    # SAP HANA Components. Default: server. Supported values: all, client, es, ets, lcapps, server, smartda, streaming, rdsync, xs, studio, afl, sca, sop, eml, rme, rtl, trp
    # Example: hana_components = "server"

    kit_saphana_file = "/storage/HANADB/51055299.ZIP"
    # SAP HANA Installation kit path
    # Supported SAP HANA versions on Red Hat 8.4 and Suse 15.3: HANA 2.0 SP 5 Rev 57, kit file: 51055299.ZIP
    # Supported SAP HANA versions on Red Hat 7.6: HANA 2.0 SP 5 Rev 52, kit file: 51054623.ZIP
    # Example for Red Hat 7: kit_saphana_file = "/storage/HANADB/51054623.ZIP"
    # Example for Red Hat 8 or Suse 15: kit_saphana_file = "/storage/HANADB/51055299.ZIP"

    ##########################################################
    # SAP system configuration
    ##########################################################

    sap_sid = "NWD"
    # SAP System ID
    # Obs. This will be used  also as identification number across different HA name resources. Duplicates are not allowed.

    sap_ascs_instance_number = "00"
    # The central ABAP service instance number. Should follow the SAP rules for instance number naming.
    # Example: sap_ascs_instance_number = "00"

    sap_ers_instance_number = "01"
    # The enqueue replication server instance number. Should follow the SAP rules for instance number naming.
    # Example: sap_ers_instance_number = "01"

    sap_ci_instance_number = "10"
    # The primary application server instance number. Should follow the SAP rules for instance number naming.
    # Example: sap_ci_instance_number = "10"

    sap_aas_instance_number = "20"
    # The additional application server instance number. Should follow the SAP rules for instance number naming.
    # Example: sap_aas_instance_number = "20"

    hdb_concurrent_jobs = "23"
    # Number of concurrent jobs used to load and/or extract archives to HANA Host

    ##########################################################
    # SAP S/4HANA APP Kit Paths
    ##########################################################

    kit_sapcar_file = "/storage/S4HANA/SAPCAR_1010-70006178.EXE"
    kit_swpm_file = "/storage/S4HANA/SWPM20SP13_1-80003424.SAR"
    kit_sapexe_file = "/storage/S4HANA/SAPEXE_100-70005283.SAR"
    kit_sapexedb_file = "/storage/S4HANA/SAPEXEDB_100-70005282.SAR"
    kit_igsexe_file = "/storage/S4HANA/igsexe_1-70005417.sar"
    kit_igshelper_file = "/storage/S4HANA/igshelper_17-10010245.sar"
    kit_saphotagent_file = "/storage/S4HANA/SAPHOSTAGENT51_51-20009394.SAR"
    kit_hdbclient_file = "/storage/S4HANA/IMDB_CLIENT20_009_28-80002082.SAR"
    kit_s4hana_export = "/storage/S4HANA/export"
    ```

    Remember, you must manually decompress the `kit_export_dir, kit_db2_dir` and `kit_db2client_dir` files. Ansible decompresses the rest of the SAP kit files. For more information, see the [`README` file](https://github.com/IBM-Cloud/sap-s4hana-sz-ha/blob/main/README.md){: external}.
    {: note}

5. Initialize the Terraform CLI.

    ```terraform
    terraform init
    ```

6. Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account.

    ```terraform
    terraform plan --out plan1
    ```

    You must enter an SAP main password and your API key.

    The SAP main password must be 10 - 14 characters long and contain at least one digit (0-9). It can contain only the following characters: a-z, A-Z, 0-9, @, #, $, _. This password cannot contain `!`. It must not start with a digit or an underscore ( _ ).

7. Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the `input.auto.tfvars` file to correct resources and run `terraform plan` again.
8. Create the virtual private cloud for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.

    ```terraform
    terraform apply "plan1"
    ```

    The virtual private cloud and components are created and you see output similar to the `terraform plan` output.
9. Add the SAP credentials and the virtual server instance IP to the SAP GUI. For more information about the SAP GUI, see [SAP GUI](https://help.sap.com/doc/7abd5470728810148a4b1a83b0e91070/1511%20000/en-US/frameset.htm){: external}.

## Next steps
{: #automate-s4hana-ha-next-steps}

If you need to rename your resources after they are created, modify the `input.auto.tfvars` file to change the names and run `terraform plan` and `terraform apply` again. Do not use the {{site.data.keyword.cloud_notm}} Dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results.

If you need to remove the **SAP HA S/4HANA** installation, go to your project folder and run `terraform destroy`. The `terraform destroy` command does not remove the VPC in this scenario because the VPC was not created with the Terraform scripts.

## Related information
{: #automate-s4hana-ha-related-information}

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Terraform on {{site.data.keyword.cloud_notm}} getting started tutorial](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

For more information about using Terraform for creating only a VPC for SAP, without the SAP architecture, see [Creating single-tier VPC for SAP on {{site.data.keyword.cloud}} VPC with Terraform](/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).

SAP One Support Notes that apply to this document:

* [SAP Note 84555 - Windows Server, Linux&reg;, and UNIX: Certified hardware](https://launchpad.support.sap.com/#/notes/84855)
* [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
* [SAP Note 2923773 - Linux&reg; on IBM Cloud (IaaS): Adaption of your SAP License](https://launchpad.support.sap.com/#/notes/2923773)
* [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)
* [SAP Note 2369910 - SAP Software on Linux&reg;: General information](https://launchpad.support.sap.com/#/notes/2369910)
* [SAP Note 171380 - Released IBM hardware (Intel&reg; processors) and IBM cloud services offers](https://launchpad.support.sap.com/#/notes/171380)
* [SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)

This document is referenced by:

* [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
* [SAP Note 2588225 - SAP on IBM Cloud: Protect against speculative execution vulnerabilities](https://launchpad.support.sap.com/#/notes/2588225)
* [SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)
* [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)







