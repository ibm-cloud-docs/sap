---

copyright:
  years: 2023
lastupdated: "2023-09-20"

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

# Automating SAP workload SAP NetWeaver on ASE Sybase HA SZ deployment on IBM Cloud VPC with Terraform and Ansible
{: #automate-sap-ase-sybase-ha-sz-delpoyment-intro}

Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud}} Virtual Private Cloud (VPC) infrastructure resources so that you can rapidly build complex cloud environments. {{site.data.keyword.cloud}} VPC infrastructure consists of SAP certified hardware that uses Intel® Xeon CPUs and other Intel® technologies.

You can use Terraform scripts to create a VPC and 2 clustered layers, one for SAP NW(ABAP) and second for SAP ASE Sybase in-memory database in a HA Single Zone architecture on the bastion server. Creating the bastion server is a prerequisite for all IBM SAP VPC automated solutions. The automation scripts use the VPC information that you provide and then call the Ansible playbooks to create the SAP architecture on the specified VPC.

You have three deployment methods to choose from:

* Terraform scripts that run from the CLI on your bastion server.
* Catalog tile user interface from the {{site.data.keyword.cloud}} catalog.
* {{site.data.keyword.bpshort}} user interface accessed from your cloud dashboard menu.

## Terraform scripts include
{: #automate-sap-ase-sybase-ha-sz-delpoyment-scripts}

The terraform scripts included for deploying are:

* One Power Placement group to include all the four VMs involved in this solution.
* Four VSIs in an existing VPC with subnet and security group configurations:
    * Two for the ASE Sybase database HADR cluster instance 
    * Two for the SAP application cluster
* Configuring three Application Load Balancers like ASE Sybase db and SAP ASCS/ERS.
* Configuring one VPC DNS service used to map the ALB FQDN to the SAP ASCS/ERS and ASE Sybase virtual hostnames.
* Configuring seven file shares for VPC.

The ansible scripts included are:

* OS requirements for installation and configuration of SAP applications.
* Cluster components installation.
* Ansible scripts for SAP application cluster configuration and SAP ASE Sybase cluster configuration.
* ASE Sybase installation
* ASE Sybase db backup
* ASE Sybase system replica configuration
* ASCS and ERS instances installation
* Database load
* Primary and extra application servers installation

Ansible is started by Terraform and must be available on the same host.
{: note}

## SAP Solution implemented
{: #automate-sap-ase-sybase-ha-sz-delpoyment-solution}

SAP NetWeaver is the core foundation of the SAP technology stacks and is the platform that is used for ABAP and Java applications. The SAP system can be installed and configured in {{site.data.keyword.cloud}} for various system and database types.

For more information about SAP system architectures on {{site.data.keyword.cloud}} VPC, see the infrastructure reference architectures for SAP for each supported database type. For example, [SAP NetWeaver 7.x on UNIX with ASE SYB on {{site.data.keyword.cloud}} VPC](https://cloud.ibm.com/docs/sap?topic=sap-sap-refarch-nw-sybase) is the dedicated reference architecture for this SAP solution.

Deploying a VPC and installing a SAP system manually can be time-consuming. The terraform automation assures not only a quicker implementation, but also a standardized and less error-prone deployment. Terraform and Ansible are used for automating the deployment processes.

The Terraform scripts solution provides the automated deployment for SAP NetWeaver with ASE SYB and SAP application servers on Red Hat Enterprise Linux 8.

The SAP installation media used for this deployment is the default media for SAP NetWeaver 7.5 with ASE Sybase 16.0. The media is available at the SAP Support Portal under Installation and Upgrade area. You provide the installation media as an input parameter for Terraform.

An ERP system is used for demand-oriented business resource planning. This is used to control processes and to link departments and functional areas in a meaningful way. Individual modules include applications for accounting, sales, production, and marketing. More complex tasks in customer or supply chain management can also be done by ERP software. As the successor to the core product SAP ECC, SAP NetWeaver was presented as the intelligent ERP system of the new generation. Thanks to modern technologies, the Software-as-a-Service (SaaS) version is designed to help companies standardize processes and make the leap to digitalization.

## What is created
{: #automate-sap-ase-sybase-ha-sz-delpoyment-components}

The scripts work in two phases. The first phase automates creating the resources for the VPC provisioning process in an existing VPC deployed in the bastion VSI. The second phase creates the SAP architecture in a distributed environment. This phase creates the:

*  SAP HA SZ SAP NetWeaver App cluster servers on 2 VSIs as a single zone VPC
*  SAP ASE SYBASE on 2 dedicated server type VSIs in a Single Zone VPC

For more information about this architecture, see [SAP NetWeaver 7.x with SAP ASE Sybase {{site.data.keyword.cloud}} VPC](https://cloud.ibm.com/docs/sap?topic=sap-sap-refarch-nw-hana&interface=ui).

During the first phase, the VPC is provisioned with these components:

*	1 VPC where the virtual server instances are provisioned
*	1 security group. The rules for this security group allow:
    - Inbound DNS (port 53) and  
    - Inbound SSH (TCP port 22) connections to your virtual server instance
    - All outbound traffic from the virtual server instance
*	1 subnet to enable networking in your VPC
*	4 X virtual server instances with SAP certified storage and network configurations

During the second phase, the Ansible Playbooks is called and the SAP High availability architecture is installed for both dedicated VSIs SAP App VSI system and dedicated SAP ASE Sybase VSI box. The SAP architecture that is deployed is the SAP NetWeaver release on pacemaker cluster HA dedicated SAP ASE Sybase 16 SP0+VSI’s release as a distributed deployment model. For more information about this architecture, see [Automating SAP ASE Sybase stand-alone virtual server instance on {{site.data.keyword.cloud}} VPC by using Terraform and Ansible](https://cloud.ibm.com/docs/sap?topic=sap-automate-terraform-sap-hana-vsi).

## Highly available system for SAP ASE Sybase database
{: #automate-sap-ase-sybase-ha-sz-delpoyment-database}

![Figure 1. SAP NetWeaver ASE Sybase HA on VPC Single zone](images/sapnw-ase-syb-ha-on-vpc-singlezone.svg "SAP NetWeaver ASE Sybase HA on VPC Single zone"){: caption="Figure 1. SAP NetWeaver ASE Sybase HA on VPC Single zone" caption-side="bottom"}

At the most basic level, a standard HA ASE Sybase cluster in an active-passive configuration has two nodes: one is the primary node and the other is the standby node. The primary node is actively serving the active SAP instances (PAS and AAS), while the standby node is waiting to jump in if necessary.

The cluster is set with a virtual hostname IP. Hostname is mapped to the FQDN of the ASE Sybase ALB through DNS, which is the same as SAP ASCS and ERS instances. App instances (PAS and AAS), are the details to be used on the SAP profiles to call that particular component. The cluster assigns that virtual IP to the active node and uses a heartbeat monitor to confirm the availability of the components. If the primary node stops responding, it triggers the automatic failover mechanism that calls the standby node to become the primary node. The ALB detects the change, redirects the traffic to the new active node, and assigns the virtual IP to it, restoring the component availability. After the failed node is fixed, it comes online as a standby node.

## Terraform deployment overview
{: #automate-sap-ase-sybase-ha-sz-delpoyment-terraform-overview}

You use Terraform on the bastion server CLI to download and run the scripts that are located [here](https://github.com/IBM-Cloud/sap-nwase-sz-ha).

To run the Terraform scripts, you modify:

* The `input.auto.tfvars` file to specify the information for your solution: 
    * Enter existing VPC information: 
        * VPC name
        * Security group
        * Subnet
        * Hostname
        * Profile
        * Image
        * Up to two SSH keys
    * You can change the default SAP system configuration settings to match your solution.
    * You also specify the location where you downloaded the SAP kits.

The {{site.data.keyword.cloud}} Provider Plug-in for Terraform on {{site.data.keyword.cloud}} uses these configuration files to install SAP NetWeaver High Availability on Single Zone on the specified VPC in your {{site.data.keyword.cloud}} account.

## SAP Kits
{: #automate-sap-ase-sybase-ha-sz-delpoyment-sap-kits}

For each {{site.data.keyword.cloud}} region, IBM allocates temporary storage on a dedicated jump host. You need to download the necessary SAP and db kits to your deployment server. All the files archives are decompressed by Ansible during the automation deployment process. For more information, see the [Readme](https://github.com/IBM-Cloud/sap-nwase-sz-ha/blob/main/README.md) file.

## Support
{: #automate-sap-ase-sybase-ha-sz-delpoyment-support}

There are no warranties of any kind, and there is no service or technical support available for these materials from {{site.data.keyword.IBM}}. As a recommended practice, review carefully any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the {{site.data.keyword.IBM}} Service organization, your comments are welcomed by the developers, who reserve the right to revise, readapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Before you begin
{: #automate-sap-ase-sybase-ha-sz-delpoyment-before}

Before you deploy SAP NetWeaver High Availability on Single Zone:

* The automation for this deployment requires {{site.data.keyword.cloud}} File Storage for VPC to complete successfully. {{site.data.keyword.cloud}} File Storage for VPC is available for customers with special approval to preview this service in the Frankfurt, London, Dallas, Toronto, Washington, Sao Paulo, Sydney, Osaka, and Tokyo regions. Contact your IBM Sales representative to get access. For more information, see [{{site.data.keyword.cloud}} File Storage for VPC](https://cloud.ibm.com/docs/vpc?topic=vpc-file-storage-vpc-about).
* Set up your account to access the VPC. Make sure that your account is [upgraded to a paid account](https://cloud.ibm.com/docs/account?topic=account-accountfaqs#changeacct).
* If you have not already, create a bastion server to store the SAP kits. For more information, see [Automate SAP bastion server - SAP media storage repository](https://cloud.ibm.com/docs/sap?topic=sap-sap-bastion-server. You need the floating IP from your bastion server for deployment.
* Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. Ansible decompresses the files. For more information, see the README file, in the respective GitHub repository for [Schematics](https://github.com/IBM-Cloud/sap-nwase-sz-ha/tree/main#21-executing-the-deployment-of-sap-netweaver-and-ase-db-installation-in-gui-schematics) and [Terraform](https://github.com/IBM-Cloud/sap-nwase-sz-ha/tree/main#22-executing-the-deployment-of-sap-netweaver-and-ase-db-installation-in-cli) and on the About page for the catalog tile.
* [Create or retrieve an {{site.data.keyword.cloud}} API key](https://cloud.ibm.com/docs/account?topic=account-userapikey&interface=ui#create_user_key). The API key is used to authenticate with the {{site.data.keyword.cloud}} platform and to determine your permissions for {{site.data.keyword.cloud}} services.
* [Create or retrieve your SSH key ID](https://cloud.ibm.com/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.

## Deploying SAP HA SAP NetWeaver on ASE SYB by using Terraform with the bastion server CLI
{: #automate-sap-ase-sybase-ha-sz-delpoyment-bastion-server}

Use these steps to configure the {{site.data.keyword.cloud}} Provider plug-in and use Terraform to install SAP HA SAP NetWeaver on your existing VPC. The scripts can take 1 - 2 hours to complete.

1.  Access the bastion server cli.
2.  Clone the solution repository as:

    ```sh
    git clone https://github.com/IBM-Cloud/sap-nwase-sz-ha.git

    cd sap-nwase-sz-ha 
    ```

3.  Specify your VPC. Modify the `input.auto.tfvars` file to specify the information for the existing VPC, your zone, VPC and component names, profile, and image. 

   You need your 40-digit SSH key ID for this file. The second SSH key is optional. 

   For more options for profile, see [Instance Profiles](https://cloud.ibm.com/docs/vpc?topic=vpc-profiles). 
   For more options for images, see [Images](https://cloud.ibm.com/docs/vpc?topic=vpc-about-images). 
   For descriptions of the variables, see [README](https://github.com/IBM-Cloud/sap-nwase-sz-ha/blob/main/README.md) file.

     ```sh
     REGION = "eu-de"
    # Region for the VSI. Supported regions: https://cloud.ibm.com/docs/containers?topic=containers-regions-and-zones#zones-vpc
    # Example: REGION = "eu-de"

    ZONE = "eu-de-2"
    # Availability zone for VSI. Supported zones: https://cloud.ibm.com/docs/containers?topic=containers-regions-and-zones#zones-vpc
    # Example: ZONE = "eu-de-2"

    DOMAIN_NAME = "example.com"
    # The DOMAIN_NAME variable should contain at least one "." as a separator. It is a private domain and it is not reachable to and from the outside world.
    # The DOMAIN_NAME variable could be like a subdomain name. Example: staging.example.com
    # Domain names can only use letters, numbers, and hyphens.
    # Hyphens cannot be used at the beginning or end of the domain name.
    # You can't use a domain name that is already in use.
    # Domain names are not case sensitive.

    ASCS_VIRT_HOSTNAME = "sapascs"
    # ASCS Virtual Hostname
    # Default =  "sap($your_sap_sid)ascs"

    ERS_VIRT_HOSTNAME =  "sapers"
    # ERS Virtual Hostname
    # Default =  "sap($your_sap_sid)ascs"

    VPC = "ic4sap"
    # EXISTING VPC, previously created by the user in the same region as the VSI. The list of available VPCs: https://cloud.ibm.com/vpc-ext/network/vpcs
    # Example: VPC = "ic4sap"

    SECURITY_GROUP = "ic4sap-securitygroup"
    # EXISTING Security group, previously created by the user in the same VPC. It can be copied from the Bastion Server Deployment "OUTPUTS" at the end of "Apply plan successful" message.
    # The list of available Security Groups: https://cloud.ibm.com/vpc-ext/network/securityGroups
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

    ID_RSA_FILE_PATH = "/root/.ssh/id_rsa"
    # Input your existing id_rsa private key file path in OpenSSH format with 0600 permissions.
    # This private key it is used only during the terraform provisioning and it is recommended to be changed after the SAP deployment.
    # It must contain the relative or absoute path from your Bastion.
    # Examples: "ansible/id_rsa_ase-syb_ha" , "~/.ssh/id_rsa_ase-syb_ha" , "/root/.ssh/id_rsa"

     ##########################################################
     # File Shares variables:
     ##########################################################

     SHARE_PROFILE = "dp2"
    # Enter the profile for File Share storage.
    # More details on https://cloud.ibm.com/docs/vpc?topic=vpc-file-storage-profiles&interface=ui#dp2-profile.

    # Enter Custom File Shares sizes for SAP mounts.
    # File shares sizes:
    USRSAP_AS1      = "20"
    USRSAP_AS2      = "20"
    USRSAP_SAPASCS  = "20"
    USRSAP_SAPERS   = "20"
    USRSAP_SAPMNT   = "20"
    USRSAP_SAPSYS   = "20"
    USRSAP_TRANS    = "80"

     ##########################################################
     # DB VSI variables:
     ##########################################################
     DB_HOSTNAME_1 = "sybdb-1"
    # SYBASE Cluster VSI1 Hostname.
    # The Hostname for the DB VSI. The hostname should be up to 13 characters, as required by SAP
    # Default: DB_HOSTNAME_1 = "sybdb-$your_sap_sid-1"

    DB_HOSTNAME_2 = "sybdb-2"
    # SYBASE Cluster VSI2 Hostname.
    # The Hostname for the DB VSI. The hostname should be up to 13 characters, as required by SAP
    # Default: DB_HOSTNAME_2 = "sybdb-$your_sap_sid-2"

    DB_PROFILE = "bx2-4x16"
    # The DB VSI profile. Supported profiles for DB VSI: bx2-4x16. The list of available profiles: https://cloud.ibm.com/docs/vpc?topic=vpc-profiles&interface=ui

    DB_IMAGE = "ibm-redhat-8-6-amd64-sap-hana-4"
    # OS image for DB VSI. Supported OS images for DB VSIs: ibm-redhat-8-6-amd64-sap-hana-2, ibm-redhat-8-6-amd64-sap-hana-4
    # The list of available VPC Operating Systems supported by SAP: SAP note '2927211 - SAP Applications on IBM Virtual Private Cloud (VPC) Infrastructure environment' https://launchpad.support.sap.com/#/notes/2927211; The list of all available OS images: https://cloud.ibm.com/docs/vpc?topic=vpc-about-images
    # Example: DB_IMAGE = "ibm-redhat-8-4-amd64-sap-hana-4" 

     ##########################################################
     # SAP APP VSI variables:
     ##########################################################
    APP_HOSTNAME_1 = "sapapp-1"
    # SAP Cluster VSI1 Hostname.
    # The Hostname for the SAP APP VSI. The hostname should be up to 13 characters, as required by SAP
    # Default: APP_HOSTNAME_1 = "sapapp-$your_sap_sid-1"

    APP_HOSTNAME_2 = "sapapp-2"
    # SAP Cluster VSI2 Hostname.
    # The Hostname for the SAP APP VSI. The hostname should be up to 13 characters, as required by SAP
    # Default: APP_HOSTNAME_2 = "sapapp-$your_sap_sid-2"

    APP_PROFILE = "bx2-4x16"
    # The APP VSI profile. Supported profiles: bx2-4x16. The list of available profiles: https://cloud.ibm.com/docs/vpc?topic=vpc-profiles&interface=ui

    APP_IMAGE = "ibm-redhat-8-6-amd64-sap-hana-4"
    # OS image for SAP APP VSI. Supported OS images for APP VSIs: ibm-redhat-8-6-amd64-sap-hana-2, ibm-redhat-8-6-amd64-sap-hana-4
    # The list of available VPC Operating Systems supported by SAP: SAP note '2927211 - SAP Applications on IBM Virtual Private Cloud (VPC) Infrastructure environment' https://launchpad.support.sap.com/#/notes/2927211; The list of all available OS images: https://cloud.ibm.com/docs/vpc?topic=vpc-about-images
    # Example: APP_IMAGE = "ibm-redhat-8-4-amd64-sap-hana-4"
     ```

    Customize your SAP system configuration. In `input.auto.tfvars` file, edit the SAP system configuration variables that are passed to the Ansible automated deployment. For descriptions of the variables, see the [Readme](https://github.com/IBM-Cloud/sap-nwase-sz-ha/blob/main/README.md) file.

     ```sh
     #########################################################
     # SAP system configuration
     ##########################################################

     SAP_SID = "NWD"
     # SAP System ID
     # Obs. This will be used  also as identification number across different HA name resources. Duplicates are not allowed.

     SAP_ASCS_INSTANCE_NUMBER = "00"
     # The central ABAP service instance number. Should follow the SAP rules for instance number naming.
     # Example: SAP_ASCS_INSTANCE_NUMBER = "00"

     SAP_ERS_INSTANCE_NUMBER = "01"
     # The enqueue replication server instance number. Should follow the SAP rules for instance number naming.
     # Example: SAP_ERS_INSTANCE_NUMBER = "01"

     SAP_CI_INSTANCE_NUMBER = "10"
     # The primary application server instance number. Should follow the SAP rules for instance number naming.
     # Example: SAP_CI_INSTANCE_NUMBER = "10"

     SAP_AAS_INSTANCE_NUMBER = "20"
     # The additional application server instance number. Should follow the SAP rules for instance number naming.
     # Example: SAP_AAS_INSTANCE_NUMBER = "20"
     
     ##########################################################
     # SAP Kit Paths
     ##########################################################

     KIT_SAPCAR_FILE = "/storage/NW75SYB/SAPCAR_1010-70006178.EXE"
     KIT_SWPM_FILE =  "/storage/NW75SYB/SWPM10SP38_0-20009701.SAR"
     KIT_SAPHOSTAGENT_FILE = "/storage/NW75SYB/SAPHOSTAGENT61_61-80004822.SAR"
     KIT_SAPEXE_FILE = "/storage/NW75SYB/KERNEL/754UC/SAPEXE_200-80007612.SAR"
     KIT_SAPEXEDB_FILE = "/storage/NW75SYB/KERNEL/754UC/SAPEXEDB_200-80007655.SAR"
     KIT_IGSEXE_FILE = "/storage/NW75SYB/KERNEL/754UC/igsexe_2-80007786.sar"
     KIT_IGSHELPER_FILE = "/storage/NW75SYB/igshelper_17-10010245.sar"
     KIT_ASE_FILE = "/storage/NW75SYB/51056521_1_16_0_04_04.ZIP"
     KIT_EXPORT_DIR = "/storage/NW75SYB/EXP"
     ```

4.  Remember, you must manually decompress the `KIT_EXPORT_DIR` file. Ansible decompresses the rest of the SAP kit files. For more information, see the [Readme](https://github.com/IBM-Cloud/sap-nwase-sz-ha/blob/main/README.md) file.
5.  Initialize the Terraform CLI.
    `terraform init`
6.  Create a terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account.
    `terraform plan --out plan1`

    You must enter the HA password, your API key, and the SAP main password.

    The SAP main password must be 10 - 14 characters long and contain at least one digit (0-9). It can contain only these characters: a-z, A-Z, 0-9, @, #, $, _. The password cannot contain "!". It must not start with a digit or an underscore ( _ ).

8.  Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the `input.auto.tfvars` file to correct resources and run terraform plan again.
9.  Create the VPC for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.
10. Run `terraform apply "plan1"`The VPC and components are created and the output is similar to the terraform plan output.
11. Add the SAP credentials and the virtual server instance IP to the SAP GUI. For more information about the SAP GUI, see [SAP GUI](https://help.sap.com/docs/page-not-found?url=https%3A%2F%2Fhelp.sap.com%2Fdoc%2F7abd5470728810148a4b1a83b0e91070%2F1511).


<!--
## Deploying SAP NetWeaver High Availability on Single Zone with the catalog tile interface
{: #automate-sap-ase-sybase-ha-sz-delpoyment-catalog-tile}

Use these steps to configure the SAP HA SZ SAP NetWeaver on your existing VPC by using the catalog tile interface. The scripts can take 2 - 3 hours to complete.

1. From the {{site.data.keyword.cloud_notm}} Catalog, select **SAP NetWeaver High Availability on Single Zone** tile. The tile opens the **Create** tab for SAP HA SZ S/4ASE Sybase. For more information about this deployment, see the [Readme](https://github.com/IBM-Cloud/sap-nwase-sz-ha/blob/main/README.md) file.
2. On the SAP HA SZ SAP NetWeaver page, configure your workspace:
    * Enter a name for the workspace or use the default.
    * Select the **Resource Group** to use to create resources. Use the Default or create a Resource Group.
    * Select a **Location** to create your Schematics workspace. The workspace location does not have to match the resource location.
3. Enter the required deployment values, review the default input variables, and provide values that match your solution. These parameters are specific to your deployment. For more information, see the [Readme file - Input Parameters](https://github.com/IBM-Cloud/sap-nwase-sz-ha/blob/main/README.md).

    |Parameter	|Description|
    |-----|-----|
    |APP-HOSTNAME-1	|APP VSI hostname-1|
    |APP-HOSTNAME-2	|APP VSI hostname-2|
    |BASTION_FLOATING_IP	|Input the floating IP of the Bastion Server you created before you started this deployment. For more information, see [Automate SAP bastion server - SAP media storage](/docs/sap?topic=sap-sap-bastion-server).|
    |DB-HOSTNAME-1	|DB VSI hostname-1|
    |DB-HOSTNAME-2	|DB VSI hostname-2|
    |DOMAIN_NAME	|Private Domain Name|
    |REGION	|Cloud Region where resources are deployed |
    |RESOURCE_GROUP	|EXISTING Resource Group for VSIs and Volumes |
    |SECURITY_GROUP	|EXISTING Security group name |
    |SSH_KEYS	|SSH Keys ID list to access the VSI|
    |SUBNET	|EXISTING Subnet name |
    |VPC	|EXISTING VPC name |
    |ZONE	|Cloud Zone where resources are deployed
    |ha_password	|HA cluster password |
    |ASE SYBASE_components	|ASE Sybase Components, for example, server |
    |ASE SYBASE_main_password	|ASE Sybase main password or use a secret that is stored in Secrets Manager |
    |ASE SYBASE_sid	|ASE Sybase sid |
    |ibmcloud_api_key	|{{site.data.keyword.cloud_notm}} API key or use a secret stored in Secrets Manager|
    |private_ssh_key	|Input id_rsa private key content or use a secret stored in Secrets Manager|
    |sap_main_password	|SAP main password or use a secret stored in Secrets Manager |
    |sap_sid	|SAP sid |

    * Review and update the optional input variables. The Ansible scripts expect the SAP kits to be in the default locations listed. For more information, see the [Readme]
    {https://github.com/IBM-Cloud/sap-nwase-sz-ha/blob/main/README.md].

    |Parameter	|Description|
    |-----|-----|
    |APP-IMAGE	|APP VSI OS image|
    |APP-PROFILE	|APP VSI profile|
    |ASCS-VIRT-HOSTNAME	|ASCS Virtual hostname|
    |DB-IMAGE	|DB VSI OS image|
    |DB-PROFILE	|DB VSI profile|
    |ERS-VIRT-HOSTNAME	|ERS Virtual hostname|
    |ASE SYBASE-VIRT-HOSTNAME	|ASE Sybase Virtual hostname|
    |ASE SYBASE_sysno	|ASE Sybase|
    |ASE SYBASE_system_usage	|ASE SYBASE_system_usage|
    |hdb_concurent_jobs	|hdb_concurent_jobs|
    |kit_hdbclient_file	|kit_hdbclient_file|
    |kit_igsexe_file	|kit_igsexe_file|
    |kit_igshelper_file	|kit_igshelper_file|
    |kit_s4ASE SYBASE_export	|kit_s4ASE SYBASE_export|
    |kit_sapcar_file	|kit_sapcar_file|
    |kit_sapexe_file	|kit_sapexe_file|
    |kit_sapexedb_file	|kit_sapexedb_file|
    |kit_sapASE SYBASE_file	|kit_sapASE SYBASE_file|
    |kit_saphotagent_file	|kit_saphotagent_file|
    |kit_swpm_file	|kit_swpm_file|
    |sap_aas_instance_number	|sap_aas_instance_number|
    |sap_ascs_instance_number	|sap_ascs_instance_number|
    |sap_ci_instance_number	|sap_ci_instance_number|
    |sap_ers_instance_number	|sap_ers_instance_number|
    |share_profile	|Enter the IOPs (IOPS per GB) tier for File Share storage. Valid values are 3, 5, and 10.|
    |usrsap-as1	|FS Size in GB for usrsap-as1|
    |usrsap-as2	|FS Size in GB for usrsap-as2|
    |usrsap-sapascs	|FS Size in GB for usrsap-sapascs|
    |usrsap-sapers	|FS Size in GB for usrsap-sapers|
    |usrsap-sapsys	|FS Size in GB for usrsap-sapsys|
    |usrsap-trans	|FS Size in GB for usrsap-trans|

4. Accept the license agreement.
5. Select **Deploy**. The deployment starts and you are directed to the {{site.data.keyword.bpshort}} page that displays the script log files for you to monitor the deployment progress.
-->

## Next steps
{: #automate-sap-ase-sybase-ha-sz-delpoyment-next}
{: terraform}

If you need to rename your resources after they are created, modify the input.auto.tfvars file to change the names and run `terraform plan` and `terraform apply` again. Do not use the {{site.data.keyword.cloud_notm}} Dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results.

If you need to remove the SAP HA SAP NetWeaver installation, go to your project folder and run `terraform destroy`. The `terraform destroy` command does not remove the VPC in this scenario because the VPC was created before these Terraform scripts were run.

## Related information
{: #automate-sap-ase-sybase-ha-sz-delpoyment-related}

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Getting started with Terraform on {{site.data.keyword.cloud_notm}}](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

For more information about using Terraform for creating only a VPC for SAP, without the SAP architecture, see [Creating single-tier virtual private cloud for SAP by using Terraform](/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).

SAP One Support Notes that apply to this document:

* [SAP Note 84555 - Windows Server, Linux&reg;, and UNIX: Certified hardware](https://launchpad.support.sap.com/#/notes/84855)
* [SAP Note 2927211 - SAP Applications on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
* [SAP Note 2923773 - Linux&reg; on {{site.data.keyword.cloud_notm}} (IaaS): Adaption of your SAP License](https://launchpad.support.sap.com/#/notes/2923773)
* [SAP Note 2414097 - SAP Applications on {{site.data.keyword.cloud_notm}} Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)
* [SAP Note 2369910 - SAP Software on Linux&reg;: General information](https://launchpad.support.sap.com/#/notes/2369910)
* [SAP Note 171380 - Released IBM hardware (Intel processors) and IBM cloud services offers](https://launchpad.support.sap.com/#/notes/171380)
* [SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)

This document is referenced by:

* [SAP Note 2927211 - SAP Applications on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
* [SAP Note 2588225 - SAP on {{site.data.keyword.cloud_notm}}: Protect against speculative execution vulnerabilities](https://launchpad.support.sap.com/#/notes/2588225)
* [SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)
* [SAP Note 2414097 - SAP Applications on {{site.data.keyword.cloud_notm}} Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)
