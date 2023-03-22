---

copyright:
  years: 2022, 2023
lastupdated: "2023-03-17"

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

You can use Terraform scripts to create a VPC and create 2 clustered layers , one for SAP S/4HANA and 2nd for SAP HANA in-memory database in a HA Single Zone architecture on the bastion server that you create. Creating the bastion server is a prerequisite for all IBM SAP VPC automated solutions. The automation scripts use the VPC information that you provide and then call the Ansible playbook to create the SAP architecture on the specified VPC.

You have three deployment methods to choose from:

*   Terraform scripts rum from the CLI on your bastion server
*   Catalog tile user interfaced from the {{site.data.keyword.cloud_notm}} catalog
*   {{site.data.keyword.cloud_notm}} {{site.data.keyword.bpshort}} user interface accessed from the menu on your cloud dashboard

## Terraform scripts include
{: #automate-terraform-hana-ha-sz-contents}
{: terraform}

The scripts include Terraform scripts for deploying:
* One Power Placement group to include all the four VMs involved in this solution
* Four VSIs in an existing VPC with subnet and security group configurations. The VSIs scope: two for the HANA database cluster instance and two for the SAP application cluster.
* And configuring three Application Load Balancers like HANA DB, SAP ASCS/ERS
* And configuring one VPC DNS service that is used to map the ALB FQDN to the SAP ASCS/ERS and HANA virtual hostnames
* And configuring seven file shares for VPC

The scripts include Ansible scripts for:

* OS requirements installation and configuration for SAP applications
* Cluster components installation
* Ansible scripts for SAP application cluster configuration and SAP HANA cluster configuration
* HANA installation
* HANA DB backup
* HANA system replica configuration
* ASCS and ERS instances installation
* DB load
* Primary and extra application servers installation

Ansible is started by Terraform and must be available on the same host.
{: note}

## SAP Solution implemented 
{: #automate-s4hana-ha-sz-implemented}

SAP S/4HANA is an ERP system from SAP's ERP software product line. The software is based on the innovative SAP HANA database technology and was started as the fourth product generation in 2015. Users can choose between the SAP S/4HANA Cloud and On-Premise solution.

An ERP system is used for demand-oriented business resource planning. It is used to control processes and to link departments and functional areas in a meaningful way. Individual modules include applications for accounting, sales, production, and marketing. More complex tasks in customer or supply chain management can also be done by ERP software. As the successor to the core product SAP ECC, SAP S/4HANA was presented as the intelligent ERP system of the new generation. Thanks to modern technologies, the Software as Service (SaaS) version is designed to help companies standardize processes and make the leap to digitalization.

While previous SAP ERP solutions support the most common databases, SAP S/4HANA uses exclusively the SAP HANA in-memory database developed by SAP. This in-memory database offers users the greatest technical benefit and they benefit from increased performance. The "S" in S/4HANA stands for "simple", while the "4" refers to the generation sequence. Compared to the SAP core product SAP ECC, which is still used in most companies, SAP S/4HANA offers many innovative functions that revolutionize the system landscape from the ground up. As SAP plans to discontinue the mainstream maintenance of its existing ERP solutions by 2027, many SAP ECC users are already considering a migration to SAP HA SZ S/4HANA

## What is created
{: #automate-s4hana-ha-what-is-created}

The scripts work in two phases. The first phase automates creating the resources for the VPC provisioning process in an existing VPC created when you deployed the bastion VSI. The second phase creates the SAP architecture in a distributed environment. This phase creates the:

*   SAP HA SZ S/4HANA App cluster server on a distinct VSI as a single zone VPC  
*   SAP HANA cluster DB on a dedicated server type VSI as a Single Zone VPC

For more information about this architecture, see [SAP NetWeaver 7.x with SAP HANA IBM Cloud VPC](/docs/sap?topic=sap-sap-refarch-nw-hana&interface=ui).

During the first phase, the VPC is provisioned with these components: 
*   1 VPC where the virtual server instances are provisioned
*   1 security group. The rules for this security group allow: 
    *   Inbound DNS (port 53) and
    *   Inbound SSH (TCP port 22) connections to your virtual server instance
    *   All outbound traffic from the virtual server instance
*   1 subnet to enable networking in your VPC
*   2 X virtual server instances with SAP certified storage and network configurations
*   2 floating IP’s address that you use to access your VPC virtual server instance over the public network

During the second phase, the Ansible Playbook is called and the SAP High availability architecture is installed for both dedicated VSIs SAP App VSI machine and dedicated SAP HANA VSI box. The SAP architecture that is deployed is the SAP S/4HANA release on pacemaker cluster HA dedicated SAP HANA 2.0 VSI’s release as a distributed deployment model. For more information about this architecture, see [Automating SAP HANA stand-alone virtual server instance on IBM Cloud VPC by using Terraform and Ansible](/docs/sap?topic=sap-automate-terraform-sap-hana-vsi).

## Highly available system for SAP HANA database
{: #automate-s4hana-ha-for-hana-db}
  
![Figure 1. SAP HA for SAP applications cluster nodes PAS (Active) and AAS (Active) {{site.data.keyword.bpshort}} deploymentS](images/refarch-sap-hana-cluster-pas-aas.svg "SAP HA for SAP applications cluster nodes PAS (Active) and AAS (Active) {{site.data.keyword.bpshort}} deployment"){: caption="Figure 1. SAP HA for SAP applications cluster nodes PAS (Active) and AAS (Active) {{site.data.keyword.bpshort}} deployment" caption-side="bottom"}

![Figure 2. SAP HA for HANA DB instances cluster nodes primary (Active) and Secondary (Passive)](images/refarch-sap-ha-hana-cluster-prim-sec.svg "SAP HA for HANA DB instances cluster nodes primary (Active) and Secondary (Passive)"){: caption="Figure 2. SAP HA for HANA DB instances cluster nodes primary (Active) and Secondary (Passive)" caption-side="bottom"}
 
 Figure 2. SAP HA for HANA DB instances cluster nodes primary (Active) and Secondary (Passive) 

At the most basic level, a standard HA HANA cluster in an active-passive configuration has two nodes: one is the primary node and the other is the standby node. The primary node is actively serving the active SAP instances (PAS and AAS), while the standby node is waiting to jump in if necessary.

The cluster is set with a virtual hostname IP. Hostname is mapped to the FQDN of the HANA ALB through DNS, which is the same as SAP ASCS and ERS instances. App instances (PAS and AAS), are the details to be used on the SAP profiles to call that particular component. The cluster assigns that virtual IP to the active node and uses a heartbeat monitor to confirm the availability of the components. If the primary node stops responding, it triggers the automatic failover mechanism that calls the standby node to become the primary node. The ALB detects the change, redirects the traffic to the new active node, and assigns the virtual IP to it, restoring the component availability. After the failed node is fixed, it comes online as a standby node.

## Terraform deployment overview
{: #automate-s4hana-ha-terraform-deployment}
{: terraform}

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

The {{site.data.keyword.terraform-provider_full_notm}} on {{site.data.keyword.cloud_notm}} uses these configuration files to install **SAP S/4HANA High Availability on Single Zone** on the specified VPC in your {{site.data.keyword.cloud_notm}} account.

## {{site.data.keyword.bpshort}} deployment overview
{: #automate-s4hana-ha-schematics-deployment}
{: ui}

You use the {{site.data.keyword.bpshort}} user interface on {{site.data.keyword.cloud_notm}} and enter the GitHub repository https://github.com/IBM-Cloud/sap-s4hana-sz-ha/tree/main/schematics for SAP S/4HANA High Availability on Single Zone {{site.data.keyword.bpshort}}.

When you run the scripts with the {{site.data.keyword.bpshort}} interface, you:
*   Enter Workspace information.
*   Enter the GitHub path for the Terraform scripts used.
*   Modify the parameters in the {{site.data.keyword.bpshort}} interface.

## Catalog tile deployment
{: #automate-s4hana-ha-catalog-deployment}
{: ui}

When you use the catalog tile for deployment, you:
*   Select the SAP S/4HANA High Availability on Single Zone tile from the catalog
*   Enter information for your workspace. The catalog tile creates a {{site.data.keyword.bpshort}} workspace for you.
*   Modify the parameters for your bastion server, personal credential information, and other parameters specific to your solution.

## SAP Kits
{: #automate-s4hana-ha-sap-kits}

For each IBM Cloud region, IBM allocates temporary storage on a dedicated Jump host. It is your responsibility to download the necessary SAP and DB kits to your Deployment Server. All files archives are decompressed by Ansible during the automation deployment process. For more information, see the readme file.


## Support - Terraform and {{site.data.keyword.bpshort}}
{: #automate-s4hana-ha-terra-schem-support}

There are no warranties of any kind, and there is no service or technical support available for these materials from IBM. As a recommended practice, review carefully any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, readapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Support - catalog tile
{: #automate-s4hana-ha-cat-support}
{: ui}

The catalog tile offering is IBM Cloud supported. For more information, see [Getting help and support from IBM Cloud or SAP](/docs/sap?topic=sap-help-support&interface=ui).

If client issues are identified with SAP software, then SAP Support assists the client. Follow the recommendations of SAP Note 90835, which describes the SAP Incident Escalation Procedure. This SAP Note (and others) is found at https://support.sap.com/en/index.html


## Before you begin
{: #automate-s4hana-ha-begin}

Before you deploy SAP S/4HANA High Availability on Single Zone:

* Set up your account to access the VPC. Make sure that your account is [upgraded to a paid account](/docs/account?topic=account-accountfaqs#changeacct).
* If you have not already, create a bastion server to store the SAP kits. For more information, see [Automate SAP bastion server - SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server). You need the floating IP from your bastion server for deployment.
* Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. Ansible decompresses the files. For more information, see the `README` file, in the respective GitHub repository for [{{site.data.keyword.bpshort}}](https://github.com/IBM-Cloud/sap-s4hana-sz-ha/tree/main/schematics) and [Terraform](https://github.com/IBM-Cloud/sap-s4hana-sz-ha) and on the About page for the catalog tile.
* [Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey&interface=ui#create_user_key). The API key is used to authenticate with the {{site.data.keyword.cloud_notm}} platform and to determine your permissions for {{site.data.keyword.cloud_notm}} services.
* [Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.
* (Optional - catalog tile) create secrets for your credentials and passwords by using the [Secrets Manager](/docs/secrets-manager?topic=secrets-manager-arbitrary-secrets&interface=ui). {: ui}

## Deploying SAP HA S/4HANA by using Terraform with the bastion server CLI
{: #automate-s4hana-ha-deploy-terraform}
{: terraform}

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

## Deploying SAP S/4HANA High Availability on Single Zone with the catalog tile interface
{: #automate-deploy-s4hana-ha-catalog-tile}

Use these steps to configure the SAP HA SZ S/4HANA on your existing VPC by using the catalog tile interface. The scripts can take 2 - 3 hours to complete.

1.	From the IBM Cloud Catalog, select the **SAP S/4HANA High Availability on Single Zone** tile. The Tile opens the Create tab for SAP HA SZ S/4HANA. For more information about this deployment, see the About tab or the readme file link.
2.	On the SAP HA SZ S/4HANA page, configure your workspace:
    *   Enter a name for the workspace or use the default.
    *   Select the **Resource Group** to use to create resources. Use the Default or create a Resource Group.
    *   Select a **Location** to create your {{site.data.keyword.bpshort}} workspace. The workspace location does not have to match the resource location.

3.	Enter the required deployment values, review the default input variables, and provide values that match your solution. These parameters are specific to your deployment. For more information, see the [readme file - Input Parameters](https://github.com/IBM-Cloud/sap-s4hana-sz-ha/blob/main/schematics/README.md).

    |Parameter	|  Description  |
    |-----|-----|
    |APP-HOSTNAME-1	|APP VSI hostname-1  |
    |APP-HOSTNAME-2	|APP VSI hostname-2  |
    |BASTION_FLOATING_IP	|Input the floating IP of the Bastion Server you created before you started this deployment. For more information, see Automate SAP bastion server - SAP media storage.|
    |DB-HOSTNAME-1	|DB VSI hostname-1|
    |DB-HOSTNAME-2	|DB VSI hostname-2|
    |DOMAIN_NAME  |  Private Domain Name  |
    |REGION	| Cloud Region where resources are deployed|
    |RESOURCE_GROUP	|EXISTING Resource Group for VSIs and Volumes|
    |SECURITY_GROUP	|EXISTING Security group name|
    |SSH_KEYS	|SSH Keys ID list to access the VSI|
    |SUBNET	|EXISTING Subnet name|
    |VPC	|EXISTING VPC name|
    |ZONE	|Cloud Zone where resources are deployed|
    |ha_password | HA cluster password |
    |hana_components |  HANA Components, for example, server |
    |hana_main_password	|HANA main password or use a secret that is stored in Secrets Manager
    | hana_sid  | HANA sid  |
    |ibmcloud_api_key	|IBM Cloud API key or use a secret that is stored in Secrets Manager|
    |private_ssh_key	|Input id_rsa private key content or use a secret that is stored in Secrets Manager|
    |sap_main_password	|SAP main password or use a secret that is stored in Secrets Manager|
    | sap_sid  | SAOP sid  |

4.  Review and update the optional parameters. The Ansible scripts expect the SAP kits to be in the default locations listed. For more information, see the [readme file - Input Parameters](https://github.com/IBM-Cloud/sap-s4hana-sz-ha/blob/main/schematics/README.md).

    |Parameter	|Description|
    |-----|-------|
    |APP-IMAGE	|APP VSI OS image|
    |APP-PROFILE	|APP VSI profile|
    |ASCS-VIRT-HOSTNAME | ASCS Virtual hostname  |
    |DB-IMAGE	|DB VSI OS image|
    |DB-PROFILE	|DB VSI profile|
    |ERS-VIRT-HOSTNAME | ERS Virtual hostname  |
    |HANA-VIRT-HOSTNAME | HANA Virtual hostname  |
    |hana_sysno	|hana_sysno|
    |hana_system_usage	|hana_system_usage|
    |hdb_concurent_jobs	|hdb_concurent_jobs|
    |kit_hdbclient_file	|kit_hdbclient_file|
    |kit_igsexe_file	|kit_igsexe_file|
    |kit_igshelper_file	|kit_igshelper_file|
    |kit_s4hana_export	|kit_s4hana_export|
    |kit_sapcar_file	|kit_sapcar_file|
    |kit_sapexe_file	|kit_sapexe_file|
    |kit_sapexedb_file	|kit_sapexedb_file|
    |kit_saphana_file	|kit_saphana_file|
    |kit_saphotagent_file	|kit_saphotagent_file|
    |kit_swpm_file	|kit_swpm_file|
    |sap_aas_instance_number	|sap_aas_instance_number|
    |sap_ascs_instance_number	|sap_ascs_instance_number|
    |sap_ci_instance_number	|sap_ci_instance_number|
    |sap_ers_instance_number	|sap_ers_instance_number|
    |share_profile	| Enter the IOPs (IOPS per GB) tier for File Share storage.  Valid values are 3, 5, and 10.|
    |usrsap-as1 | FS Size in GB for usrsap-as1  |
    |usrsap-as2 | FS Size in GB for usrsap-as2  |
    |usrsap-sapascs | FS Size in GB for usrsap-sapascs  |
    |usrsap-sapers | FS Size in GB for usrsap-sapers  |
    |usrsap-sapsys | FS Size in GB for usrsap-sapsys  |
    |usrsap-trans | FS Size in GB for usrsap-trans  |


5.	Accept the license agreement.
6.	Select Install. The deployment starts and you are directed to the {{site.data.keyword.bpshort}} page that displays the script log files for you to monitor the deployment progress.

## Deploying SAP S/4HANA High Availability on Single Zone with the {{site.data.keyword.bpshort}} interface
{: #automate-s4hana-ha-deploy-schematics}
{: ui}

Use these steps to configure the SAP S/4HANA High Availability on Single Zone on your existing VPC by using the {{site.data.keyword.bpshort}} interface. The scripts can take 2 - 3 hours to complete.

1.	From the IBM Cloud menu, select *{{site.data.keyword.bpshort}}*.
2.	Click **Create workspace**.
3.	On the **Specify template** page:
    *   Enter the [URL](https://github.com/IBM-Cloud/sap-s4hana-sz-ha/tree/main/schematics) for the github repository that contains the {{site.data.keyword.bpshort}} code for this offering.
    *   Select the **Terraform version**.
    *   Click **Next**.
4.	On the **Workspace details** page:
    *   Enter a name for the workspace.
    *   Select a **Resource group**.
    *   Select a **Location** for your workspace. The workspace location does not have to match the resource location.
    *   Select **Next**.
5.	Select **Create** to create your workspace.
6.	On the workspace Settings page, in the Input variables section, review the default input variables and provide values that match your solution.

    For a more detailed description of each of the parameters, check the GitHub repo readme file, chapter “Input parameter file”. Also, make sure to mark the parameters that contain sensitive information like passwords, API, and ssh private keys as "sensitive". These parameters are marked as “sensitive” in the readme file, under “Input parameter file”.

    Save each parameter that you modify.

7.	On the workspace Settings page, click **Generate plan**. Wait for the plan to complete.
8.	Click **View log** to review the log files of your Terraform execution plan.
9.	Apply your Terraform template by clicking **Apply plan**.
10.	Review the log file to ensure that no errors occurred during the provisioning, modification, or deletion process.


## Next steps
{: #automate-s4hana-ha-next-steps}
{: terraform}

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







