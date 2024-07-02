---

copyright:
years: 2024
lastupdated: "2024-07-02"

subcollection: sap

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:ui: .ph data-hd-interface="ui"}
{:terraform: .ph data-hd-interface="terraform"}

# Deploying SAP ASE Sybase stand-alone virtual server instance on {{site.data.keyword.cloud}} VPC
{: #automate-terraform-sap-ase-sybase-vsi}

As of 28 March 2024, the {{site.data.keyword.at_full_notm}} service is deprecated and will no longer be supported as of 30 March 2025. Customers will need to migrate to {{site.data.keyword.logs_full_notm}} before 30 March 2025. During the migration period, customers can use {{site.data.keyword.at_full_notm}} along with {{site.data.keyword.logs_full_notm}}. Activity tracking events are the same for both services. For information about migrating from {{site.data.keyword.at_full_notm}} to {{site.data.keyword.logs_full_notm}} and running the services in parallel, see [migration planning](/docs/cloud-logs?topic=cloud-logs-migration-intro).
{: important}

You can use Terraform scripts to create a VPC and SAP ASE Sybase database single VSI infrastructure in the VPC. The Terraform scripts use the information that you provide and then call the Ansible playbooks to create the SAP architecture in the specified VPC.
{: terraform}

You can use Schematics User Interface that calls Terraform scripts to create a VPC and SAP ASE Sybase database single VSI infrastructure in the VPC. The Terraform scripts use the information that you provide and then call the Ansible playbooks to create the SAP architecture in the specified VPC.
{: ui}

Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} VPC infrastructure resources so that you can rapidly build complex cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware by using Intel&reg; Xeon CPUs and additional Intel&reg; technologies.
{: shortdesc}

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Getting started with Terraform on {{site.data.keyword.cloud_notm}}](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

To create resources with Terraform, you can use Terraform configuration files that describe the {{site.data.keyword.cloud_notm}} resources that you need and how you want to configure them. Based on your configuration, Terraform creates an execution plan and describes the actions that need to be run to create the resources. You can review the execution plan, change it, or run the plan.
{: terraform}

You have two deployment methods to choose from:

* In CLI, by running the Terraform script on your bastion server.
* In Schematics User Interface, which is accessed from your {{site.data.keyword.cloud_notm}} dashboard menu.

## Prerequisites
{: #terraform-sap-ase-sybase-vsi-prerequisites}
{: terraform}

A deployment server (bastion server) deployed by using the automation solution [Automate SAP bastion server – SAP media storage repository](https://test.cloud.ibm.com/docs/sap?topic=sap-sap-bastion-server), must exist in the same VPC and same region. This should have the same subnet and security group that is configured for the SAP system VSI.

## What is created
{: #terraform-sap-ase-sybase-vsi-created}

The scripts automate the deployment of the virtual infrastructure resources and the provisioning processes for the SAP architecture in an existing VPC. An SAP ASE Sybase DB, on a VPC virtual server instance box, is provisioned. The scripts work in two phases.

During the first phase, a virtual server instance, with SAP certified storage and network configuration (the same subnet and security group as for the deployment server (bastion server)) is configured.

During the second phase, the Ansible playbooks are called and the SAP ASE Sybase 2.0 architecture is installed on the SAP ASE Sybase VSI box.

There are {{site.data.keyword.cloud_notm}} VPC SAP certified server profiles where this solution can be deployed.

### x86-64 instance profiles
{: #terraform-sap-ase-sybase-vsi-x86-64}

During provisioning IBM Cloud® Virtual Servers for Virtual Private Cloud, you can select from the following families of profiles:

* Compute Optimized
* Balanced 
* Memory Optimized 
* Very High Memory Optimized
* Ultra High Memory Optimized

A profile is a combination of instance attributes, such as the number of vCPUs, amount of RAM, network bandwidth, and default bandwidth allocation. The attributes define the size and capabilities of the virtual server instance that is provisioned. In the {{site.data.keyword.cloud_notm}} console, you can select the most recently used profile or click View All Profiles tab to choose the profile that best fits your needs.

For more information about SAP profiles, see [Intel Virtual Server certified profiles on VPC infrastructure for SAP NetWeaver](/docs/sap?topic=sap-nw-iaas-offerings-profiles-intel-vs-vpc).

The Terraform scripts use the information that you provide in `input.auto.tfvars` file, call the Ansible Playbooks and deploy SAP ASE Sybase database single VSI and the SAP architecture.

## Virtual server instance configuration
{: #terraform-sap-ase-sybase-vsi-config}

The following operating systems are supported:

* Red Hat Enterprise Linux 8.6 for SAP (amd64)
* Red Hat Enterprise Linux 8.4 for SAP (amd64)
* SUSE Linux Enterprise Server 15 SP 4 for SAP Applications (amd64)
* SUSE Linux Enterprise Server 15 SP 3 for SAP Applications (amd64)

The provided SSH keys are used to access the VSI through SSH, as a root user.

## Script files
{: #terraform-sap-ase-sybase-vsi-files}

The configuration and script files are available in the [GitHub repository](https://github.com/IBM-Cloud/sap-ase-db).

The `input.auto.tfvars` file allows the customization of the input parameters.

All other configuration files are provided and no modification is required.

## Schematics deployment
{: #ui-sap-ase-sybase-vsi-schematics}
{: ui}

When you are using the Schematics interface for the deployment, you need to:

* Provide the workspace information.
* Provide the GitHub repository path.
* Update the value of the parameters in the Schematics interface. The parameters are similar to the ones from `input.auto.tfvars` file, which is used in the deployment from CLI.

## Support
{: #terraform-sap-ase-sybase-vsi-support}

There are no warranties of any kind, and there is no service or technical support available for these materials from {{site.data.keyword.IBM}}. As a recommended practice, review carefully any materials that you download from this site before using them on a live system. 

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, readapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Before you begin
{: #automate-ase-sybase-standalone-vsi-before}

If you don't have a deployment server (bastion server) in the same VPC, create a deployment server to store the SAP kits. For more information, see [Automate SAP bastion server - SAP media storage repository](https://test.cloud.ibm.com/docs/sap?topic=sap-sap-bastion-server).

*	Log in to your Deployment Server and verify that Terraform and Ansible are installed. 
*	Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. Ansible decompresses all the archive kits. For more information, see the [Readme](https://github.com/IBM-Cloud/sap-ase-db/blob/main/README.md) file.
*  [Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey#create_user_key). The API key is used to authenticate with the {{site.data.keyword.cloud_notm}} platform and to determine your permissions for {{site.data.keyword.cloud_notm}} services.
*  [Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.

## Procedure
{: #automate-sae-sybase-standalone-vsi-procedure}
{: terraform}

1.	Log in to the Deployment Server (Bastion server) through `ssh`.
2.	Clone the GitHub repository from https://github.com/IBM-Cloud/sap-ase-db and go to the `sap-ase-db` folder.

    ```shell
	$ git clone https://github.com/IBM-Cloud/sap-ase-db.git
    	
	$ cd sap-ase-db
    ```

3.	Customize the values for VPC variable resources according to your existing VPC data. Your options can be specified by updating `input.auto.tfvars` file. You need a 40-digit SSH key ID for the deployment. Additional SSH key IDs are optional.

    The following input variable values must be provided:

    * REGION - Region for SAP HANA server. See the [Readme](https://github.com/IBM-Cloud/sap-ase-db/blob/main/README.md) file.
    * ZONE - Zone for SAP HANA server. See the [Readme](https://github.com/IBM-Cloud/sap-ase-db/blob/main/README.md) file.
    * VPC - The name of an existing VPC in the specified region.
    * SECURITY_GROUP - The name of an existing security group in the same VPC.
    * RESOURCE_GROUP - The name of an existing resource group, previously created by the user.
    * SUBNET - The name of an existing subnet in the same region and zone as the VSI.
    * SSH_KEYS - A list of SSH keys UUIDs allowed to connect through SSH to the VSIs.
    * ID_RSA_FILE_PATH - existing `id_rsa` private key file path in OpenSSH format with 0600 permissions.
    * DB_HOSTNAME - The hostname of the SAP HANA server, up to 13 characters. For more information, see the [Readme](https://github.com/IBM-Cloud/sap-ase-db/blob/main/README.md) file.

    ``` terraform
    ######################################################
    # General & Default VPC variables for CLI deployment
    ######################################################

    REGION = "eu-de"  
    # Region for the VSI. Supported regions: https://cloud.ibm.com/docs/containers?topic=containers-regions-and-zones#zones-vpc
    # Example: REGION = "eu-de"

    ZONE = "eu-de-1"    
    #   Availability zone for VSI. Supported zones: https://cloud.ibm.com/docs/containers?topic=containers-regions-and-zones#zones-vpc
    # Example: ZONE = "eu-de-1"

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

    SSH_KEYS = ["r010-8f72b994-c17f-4500-af8f-d05680374t3c", "r011-8f72v884-c17f-4500-af8f-d05900374t3c"]
    # List of SSH Keys UUIDs that are allowed to SSH as root to the VSI. The SSH Keys should be created for the same region as the VSI. The list of available SSH Keys UUIDs: https://cloud.ibm.com/vpc-ext/compute/sshKeys
    # Example: SSH_KEYS = ["r010-8f72b994-c17f-4500-af8f-d05680374t3c", "r011-8f72v884-c17f-4500-af8f-d05900374t3c"]

    ID_RSA_FILE_PATH = "ansible/id_rsa"
    # The id_rsa private key file path in OpenSSH format with 0600 permissions.
    # This private key is used only during the terraform provisioning and it is recommended to be changed after the SAP deployment.
    # It must contain the relative or absoute path from your Bastion.
    # Examples: "ansible/id_rsa_abap_syb_dst" , "~/.ssh/id_rsa_abap_syb_dst" , "/root/.ssh/id_rsa".

    ##########################################################
    # Activity Tracker variables:
    ##########################################################

    ATR_NAME="Activity-Tracker-SAP-eu-de"
    # The name of the Activity Tracker instance, in the same region chosen for SAP system deployment.
    # Example: ATR_NAME="Activity-Tracker-SAP-eu-de"

    ##########################################################
    # DB VSI variables:
    ##########################################################

    DB_HOSTNAME = "ic4sapdb"
    # The Hostname for the DB VSI. The hostname should be up to 13 characters, as required by SAP
    # Example: DB-HOSTNAME = "ic4sapdb"

    DB_PROFILE = "bx2-4x16"
    # The DB VSI profile. Supported profiles for DB VSI: bx2-4x16. The list of available profiles: https://cloud.ibm.com/docs/vpc?topic=vpc-profiles&interface=ui

    DB_IMAGE = "ibm-redhat-8-6-amd64-sap-applications-6"
    # OS image for DB VSI. Supported OS images for DB VSIs: ibm-redhat-8-6-amd64-sap-applications-6, ibm-redhat-8-4-amd64-sap-applications-10, ibm-sles-15-4-amd64-sap-applications-8, ibm-sles-15-3-amd64-sap-applications-11. 
    # The list of available VPC Operating Systems supported by SAP: SAP note '2927211 - SAP Applications on IBM Virtual Private Cloud (VPC) Infrastructure environment' https://launchpad.support.sap.com/#/notes/2927211; The list of all available OS images: https://cloud.ibm.com/docs/vpc?topic=vpc-about-images
    # Example: DB-IMAGE = "ibm-sles-15-4-amd64-sap-applications-8"
    ```

    * The hostname must be up to 13 characters as required by SAP. For more information about the rules that apply to hostnames for SAP systems, see SAP Note 611361 - Hostnames of SAP ABAP Platform servers.
    * Customize your SAP system configuration. Modify the `input.auto.tfvars` file to specify SAP ASE Sybase system configuration and enter the location of the downloaded SAP Kits.

    ``` terraform
    # SAP ASE SYBASE configuration

    ##########################################################
    # SAP system configuration
    ##########################################################

    ASE_SID = "NWD"
    # The SAP ASE system ID. Identifies the entire ASE system.
    # Consists of exactly three alphanumeric characters and the first character must be a letter.
    # Does not include any of the reserved IDs listed in SAP Note 1979280

    DATA_DISK_SIZE = "30"
    # The size of data disk, in GB.

    ##########################################################
    # Kit Paths
    ##########################################################

    KIT_ASE_FILE = "/storage/ASEDB/SP04/PL06/ASESERV160004P_6-80008862.TGZ"
    ```

4. Initialize the Terraform CLI.
    `terraform init`

5. Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account.
    `terraform plan --out plan1`

    You are asked to enter the IBM Cloud API key and the SAP ASE Sybase main password.
    The SAP ASE Sybase main password must contain at least one digit (0-9), one lowercase letter (a-z), and one uppercase letter (A-Z). It can contain the following characters only: a-z, A-Z, 0-9, !, @, #, $, _. It must not start with a digit or an underscore ( _ ).

6. Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the input.auto.tfvars file to correct resources and run `terraform plan --out plan1` again.

7. Deploy the SAP solution.
    `terraform apply "plan1"`

## Deploying SAP ASE Sybase database on a single VSI by using the Schematics user interface
{: #automate-ase-sybase-standalone-vsi-ui}
{: ui}

Follow the steps to deploy the SAP ASE Sybase database on a single VSI in your existing VPC by using the Schematics User Interface. The scripts can take 1 - 2 hours to complete.

1. From the {{site.data.keyword.cloud_notm}} menu, select [Schematics](https://cloud.ibm.com/schematics/overview).
2. Click **Create workspace**.
3. On the **Specify template** page:
    * Enter the GitHub URL for the code that you plan to deploy.
    * Select the **Terraform version** that is listed in the [Readme](https://github.com/IBM-Cloud/sap-ase-db/blob/main/README.md) file.
    * Click **Next**.
4. On the **Workspace details** page:
    * Enter a name for the workspace.
    * Select a **Resource group**.
    * Select a **Location** for your workspace. The workspace location does not have to match the resource location.
    * Click **Next**.
5. Select **Create** to create your workspace.
6. On the workspace **Settings** page, in the Input variables section, review the default input variables and provide values that match your solution:
    * Your IBM Cloud API key.
    * A private SSH key is used for the deployment.
    * (Optional) You can change the `ID_RSA_FILE_PATH` for your SSH key file that is autogenerated on Schematics and on the bastion server.
    * The IDs of the SSH keys that you created and uploaded to IBM Cloud. Enter the SSH key IDs in brackets and surrounded by quotation marks, for example [ "ibmcloud_ssh_key_UUID1", "ibmcloud_ssh_key_UUID2",... ].
    * The region for your resources.
    * The zone for your resources.
    * Existing VPC name
    * Existing Subnet name
    * VPC name
    * Subnet name
    * Security group name
    * Hostname
    * Profile
    * Image
    * Data disk size
    * SAP main password - must be at least 10 characters, upper and lowercase letters, a number, and a special character, not an exclamation point.
    * Click **Save** changes.

    For a more detailed description of the parameters, check the GitHub repo [Readme](https://github.com/IBM-Cloud/sap-ase-db/blob/main/README.md) file, chapter “Input parameter file”. Also, mark as “sensitive” for the parameters that contain sensitive information like passwords, IBM Cloud API, the SSH private keys (they are marked as “sensitive” in the [Readme](https://github.com/IBM-Cloud/sap-ase-db/blob/main/README.md) in the “Input parameter file”).

7. On the workspace Settings page, click **Generate** plan. Wait for the plan to complete.
8. Click **View log** to review the log files of your Terraform execution plan.
9. Apply your Terraform template by clicking **Apply plan**.
10. Review the log file to ensure that no errors occurred during the provisioning, modification, or deletion process.

## Next steps
{: #automate-ase-sybase-standalone-vsi-next}

Do not use the {{site.data.keyword.cloud_notm}} Dashboard and user interface to modify your resources after they are created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results.

For your SAP solution, in case you want to remove the resources that are created with the automation, then go to the Workspace page > Actions and select Destroy Resources.
{: ui}

If you need to remove the resources created with the automation for your SAP solution, go to your project folder and run terraform destroy.
{: terraform}

## Related information
{: #automate-ase-sybase-standalone-vsi-related}

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Terraform on IBM Cloud getting started tutorial](https://test.cloud.ibm.com/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

For more information about using Terraform for creating only a VPC for SAP, without the SAP architecture, see [Creating single-tier VPC for SAP on IBM Cloud® VPC with Terraform](https://test.cloud.ibm.com/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).

SAP One Support Notes that apply to this document:

*	[SAP Note 84555 - Windows Server, Linux&reg;, and UNIX: Certified hardware](https://launchpad.support.sap.com/#/notes/84855)
*	[SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
*	[SAP Note 2923773 - Linux&reg; on IBM Cloud (IaaS): Adaption of your SAP license](https://launchpad.support.sap.com/#/notes/2923773)
*	[SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)
*	[SAP Note 2369910 - SAP Software on Linux&reg;: General information](https://launchpad.support.sap.com/#/notes/2369910)
*	[SAP Note 171380 - Released IBM hardware (Intel processors) and IBM cloud services offers](https://launchpad.support.sap.com/#/notes/171380)
*	[SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)

This document is referenced by:

*	[SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
*	[SAP Note 2588225 - SAP on IBM Cloud: Protect against speculative execution vulnerabilities](https://launchpad.support.sap.com/#/notes/2588225)
*	[SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)
*	[SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)