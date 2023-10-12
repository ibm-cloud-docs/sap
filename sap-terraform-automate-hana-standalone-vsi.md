---

copyright:
  years: 2021, 2022, 2023
lastupdated: "2023-10-12"

subcollection: sap

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:ui: .ph data-hd-interface="ui"}
{:terraform: .ph data-hd-interface="terraform"}

# Deploying SAP HANA stand-alone virtual server instance on {{site.data.keyword.cloud}} VPC
{: #automate-terraform-sap-hana-vsi}

You can use Terraform scripts to create a single-tier VPC and create the SAP HANA DB single VSI infrastructure on the VPC. The Terraform scripts use the VPC information that you provide and then call the Ansible playbook to create the SAP architecture on the specified VPC.

Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} VPC infrastructure resources so that you can rapidly build complex, cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware using Intel Xeon CPUs and additional Intel technologies.
{: shortdesc}

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Terraform on {{site.data.keyword.cloud_notm}} getting started tutorial](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

To create resources with Terraform, you use Terraform configuration files that describe the {{site.data.keyword.cloud_notm}} resources that you need and how you want to configure them. Based on your configuration, Terraform creates an execution plan and describes the actions that need to be run to create the resources. You can review the execution plan, change it, or run the plan. 

You have two deployment methods to choose from:

* Terraform scripts run from the CLI on your bastion server.
* Schematics user interface accessed from the menu on your cloud dashboard.

## Prerequisites
{: #terraform-sap-hana-vsi-prerequisites}
{: terraform}

A deployment server (bastion server) deployed using the automation solution [Automate SAP bastion server – SAP media storage repository](https://test.cloud.ibm.com/docs/sap?topic=sap-sap-bastion-server), should exist in the same VPC, same region, and have the same subnet and security group configured as for the SAP system VSIs.

## What is created
{: #terraform-sap-hana-vsi-created}

The scripts automate the deployment of the virtual infrastructure resources and the provisioning processes for the SAP architecture, in an existing VPC. An SAP HANA DB, on a virtual server instance VPC box, is provisioned. The scripts work in two phases.

During the first phase, a virtual server instance, with SAP certified storage and network configuration (the same subnet and security group as for the deployment server (bastion server)) are configured.

During the second phase, the Ansible playbooks are called and the SAP HANA 2.0 architecture is installed on the SAP HANA VSI box.

The scripts use the information that you provide for an existing VPC and deploy SAP HANA DB single VSI. For more information about this architecture, see [SAP HANA scale-out Reference Architecture](https://cloud.ibm.com/docs/sap?topic=sap-refarch-hana-scaleout&interface=ui). You specify the information for the VPC to use in the input.auto.tfvars file.

The scripts call the Ansible Playbooks to install the SAP architecture.

## Virtual server instance configuration
{: #terraform-sap-hana-vsi-config}
{: terraform}

The following operating systems and OS images are supported:

* Red Hat Enterprise Linux 8.6 for SAP HANA (amd64) - images: `ibm-redhat-8-6-amd64-sap-hana-<x>`
* Red Hat Enterprise Linux 8.4 for SAP HANA (amd64) - images: `ibm-redhat-8-4-amd64-sap-hana-<x>`
* SUSE Linux Enterprise Server 15 SP 4 for SAP Applications (amd64) - images: `ibm-sles-15-4-amd64-sap-hana-<x>`
* SUSE Linux Enterprise Server 15 SP 3 for SAP Applications (amd64) - images: `ibm-sles-15-3-amd64-sap-hana-<x>`

The provided SSH keys are used to access the VSI via SSH, as a `root` user.
The storage volumes are configured based on the [Intel Virtual Server certified profiles on VPC infrastructure for SAP HANA](https://cloud.ibm.com/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc).

## Script files
{: #terraform-sap-hana-vsi-files}

The configuration and script files are available in the [GitHub repository](https://github.com/IBM-Cloud/sap-hana-single-vsi).

The `input.auto.tfvars` file allows the customization of the resources and variable values should be provided.

All other configuration files are provided and no modification is required.

## Schematics deployment
{: #ui-sap-hana-vsi-schematics}
{: ui}

When you run the scripts with the Schematics interface, you:

* Enter workspace information.
* Enter the GitHub path.
* Modify the parameters in the Schematics interface. These are the same parameters as the `input.auto.tfvars` file that you use with the CLI.

## Support
{: #terraform-sap-hana-vsi-support}

There are no warranties of any kind, and there is no service or technical support available for these materials from {{site.data.keyword.IBM}}. As a recommended practice, review carefully any materials that you download from this site before using them on a live system. 

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, re-adapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Before you begin
{: #automate-hana-standalone-vsi-before}

If you don't have a deployment server (bastion server) in the same VPC, create a deployment server to store the SAP kits. For more information, see [Automate SAP bastion server - SAP media storage repository](https://test.cloud.ibm.com/docs/sap?topic=sap-sap-bastion-server).

*	Log in to your Deployment Server and verify that Terraform and Ansible are installed. 
*	Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. Ansible decompresses all of the archive kits. For more information, see the [Readme](https://github.com/IBM-Cloud/sap-hana-single-vsi/blob/main/README.md) file. 
*  [Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey#create_user_key). The API key is used to authenticate with the {{site.data.keyword.cloud_notm}} platform and to determine your permissions for {{site.data.keyword.cloud_notm}} services.
*  [Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.

## Procedure
{: #automate-hana-standalone-vsi-procedure}
{: terraform}

1.	Log in to the Deployment Server by using `ssh`.
2.	Clone the GitHub repository from https://github.com/IBM-Cloud/sap-hana-single-vsi and go to the sap-hana-single-vsi folder.

    ```shell
	$ git clone https://github.com/IBM-Cloud/sap-hana-single-vsi.git
    	
	$ cd sap-hana-single-vsi
    ```

3.	Customize the VPC variables according to your existing VPC data. Modify the `input.auto.tfvars` file to specify your zone, VPC component names, profile, and image. The file is preset with the minimal recommended disk sizes. You need a 40-digit SSH key ID for this file. Additional SSH keys are optional. For more options for profile, see [Intel Virtual Server certified profiles on VPC infrastructure for SAP HANA](https://cloud.ibm.com/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc). For more options about images, see [Images](https://test.cloud.ibm.com/docs/vpc?topic=vpc-about-images).

    The following variable values should be modified:

    * REGION - Region for the VSI. See the [Readme](https://github.com/IBM-Cloud/sap-hana-single-vsi/blob/main/README.md) file.
    * ZONE - Zone for the VSI. See the [Readme](https://github.com/IBM-Cloud/sap-hana-single-vsi/blob/main/README.md) file.
    * VPC - The name of an existing VPC in the specified region.
    * SECURITY_GROUP - The name of an existing Security group in the same VPC
    * RESOURCE_GROUP - The name of an existing Resource group, previously created by the user.
    * SUBNET - The name of an existing Subnet in the same region and zone as the VSI
    * SSH_KEYS - A list of SSH keys UUIDs allowed to connect via SSH to the VSIs
    * ID_RSA_FILE_PATH - existing id_rsa private key file path in OpenSSH format with 0600 permissions
    * HOSTNAME - The hostname of the database VSI, up to 13 characters. For more information, see the [Readme](https://github.com/IBM-Cloud/sap-hana-single-vsi/blob/main/README.md) file.

    ``` terraform
    General VPC variables:

    REGION = ""
    Region for the VSI. Supported regions: https://cloud.ibm.com/docs/containers?topic=containers-regions-and-zones#zones-vpc
    Example: REGION = "eu-de"

    ZONE = ""
    # Availability zone for VSI. Supported zones: https://cloud.ibm.com/docs/containers?topic=containers-regions-and-zones#zones-vpc
    # Example: ZONE = "eu-de-2"

    VPC = ""
    # EXISTING VPC, previously created by the user in the same region as the VSI. The list of available VPCs: https://cloud.ibm.com/vpc-ext/network/vpcs
    # Example: VPC = "ic4sap"

    SECURITY_GROUP = ""
    # EXISTING Security group, previously created by the user in the same VPC. The list of available Security Groups: https://cloud.ibm.com/vpc-ext/network/securityGroups
    # Example: SECURITY_GROUP = "ic4sap-securitygroup"

    RESOURCE_GROUP = ""
    # EXISTING Resource group, previously created by the user. The list of available Resource Groups: https://cloud.ibm.com/account/resource-groups
    # Example: RESOURCE_GROUP = "wes-automation"

    SUBNET = ""
    # EXISTING Subnet in the same region and zone as the VSI, previously created by the user. The list of available Subnets: https://cloud.ibm.com/vpc-ext/network/subnets
    # Example: SUBNET = "ic4sap-subnet"

    SSH_KEYS = [""]
    # List of SSH Keys UUIDs that are allowed to SSH as root to the VSI. The SSH Keys should be created for the same region as the VSI. The list of available SSH Keys UUIDs: https://cloud.ibm.com/vpc-ext/compute/sshKeys
    # Example: SSH_KEYS = ["r010-8f72b994-c17f-4500-af8f-d05680374t3c", "r011-8f72v884-c17f-4500-af8f-d05900374t3c"]

    ID_RSA_FILE_PATH = "ansible/id_rsa"
    # Input your existing id_rsa private key file path in OpenSSH format with 0600 permissions.
    # This private key it is used only during the terraform provisioning and it is recommended to be changed after the SAP deployment.
    # It must contain the relative or absoute path from your Bastion.
    # Examples: "ansible/id_rsa_hana_single_vsi" , "~/.ssh/id_rsa_hana_single_vsi" , "/root/.ssh/id_rsa".
    ```

    ``` terraform
    # DB VSI variables:

    HOSTNAME = ""
    # The Hostname for the DB VSI. The hostname should be up to 13 characters, as required by SAP
    # Example: HOSTNAME = "saphanadb"

    PROFILE = "mx2-16x128"
    # The DB VSI profile. The list of certified profiles for HANA VSIs: https://cloud.ibm.com/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc . 
    # Details about all x86 instance profiles: https://cloud.ibm.com/docs/vpc?topic=vpc-profiles&interface=ui

    IMAGE = "ibm-redhat-8-6-amd64-sap-hana-2"
    # OS image for DB VSI.
    # The list of available VPC Operating Systems supported by SAP: SAP note '2927211 - SAP Applications on IBM Virtual Private Cloud (VPC) Infrastructure environment' https://launchpad.support.sap.com/#/notes/2927211; The list of all available OS images: https://cloud.ibm.com/docs/vpc?topic=vpc-about-images
    # Example: IMAGE = "ibm-redhat-8-6-amd64-sap-hana-2"
    ```

    The hostname should be up to 13 characters as required by SAP. For more information about the rules that apply to hostnames for SAP systems, see SAP Note 611361 - Hostnames of SAP ABAP Platform servers

4.	Customize your SAP system configuration. Modify the `input.auto.tfvars` file to specify SAP HANA system configuration and enter the location of the downloaded SAP Kits.

    ``` terraform
    # SAP HANA configuration

    HANA_SID = "HDB"
    # SAP HANA system ID. Should follow the SAP rules for SID naming.
    # Example: HANA_SID = "HDB"

    HANA_SYSNO = "00"
    # SAP HANA instance number. Should follow the SAP rules for instance number naming.
    # Example: HANA_SYSNO = "01"

    HANA_SYSTEM_USAGE = "custom"
    # System usage. Default: custom. Suported values: production, test, development, custom
    # Example: HANA_SYSTEM_USAGE = "custom"

    HANA_COMPONENTS = "server"
    # SAP HANA Components. Default: server. Supported values: all, client, es, ets, lcapps, server, smartda, streaming, rdsync, xs, studio, afl, sca, sop, eml, rme, rtl, trp
    # Example: HANA_COMPONENTS = "server"

    KIT_SAPHANA_FILE = "/storage/HANADB/51055299.ZIP"
    # SAP HANA Installation kit path
    # Supported SAP HANA versions on Red Hat 8 and Suse 15: HANA 2.0 SP 5 Rev 57, kit file: 51055299.ZIP
    # Example for Red Hat 8 or Suse 15: KIT_SAPHANA_FILE = "/storage/HANADB/51055299.ZIP"
    ```
  
5. Initialize the Terraform CLI. 

    ``terraform init``

6.	Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account.

    ``terraform plan --out plan1``

    You will be asked to enter the IBM Cloud API key and the SAP HANA main password.

    The SAP HANA main password should contain at least one digit (0-9), one lowercase letter (a-z), and one uppercase letter (A-Z). It can only contain the following characters: a-z, A-Z, 0-9, !, @, #, $, _. It must not start with a digit or an underscore ( _ ).

7. Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the input.auto.tfvars file to correct resources and run `terraform plan --out plan1` again.

8. Deploy the SAP solution:

    ``terraform apply "plan1"``

## Deploying SAP HANA DB single VSI by using the Schematics user interface
{: #automate-hana-standalone-vsi-ui}
{: ui}

Use these steps to configure the SAP HANA DB single VSI on your existing VPC by using the Schematics user interface. The scripts can take 1 - 2 hours to complete.

1. From the {{site.data.keyword.cloud_notm}} menu, select [Schematics](https://cloud.ibm.com/schematics/overview).
2. Click **Create workspace**.
3. On the **Specify template** page:
    * Enter the GitHub URL for the code you plan to deploy.
    * Select the **Terraform version** that is listed in the [Readme](https://github.com/IBM-Cloud/sap-hana-single-vsi/blob/main/README.md) file.
    * Click **Next**.
4. On the **Workspace details** page:
    * Enter a name for the workspace.
    * Select a **Resource group**.
    * Select a **Location** for your workspace. The workspace location does not have to match the resource location.
    * Click **Next**.
5. Select **Create** to create your workspace.
6. On the workspace **Settings** page, in the Input variables section, review the default input variables and provide values that match your solution:
    * Your API key
    * Your private SSH key from your local machine
    * (Optional) You can change the `ID_RSA_FILE_PATH` for your SSH key that will be autogenerated on Schematics and bastion server
    * The ID for the SSH key that you created and uploaded to {{site.data.keyword.cloud_notm}}. Enter the SSH key ID in square brackets and quotation marks, for example [ "ibmcloud_ssh_key_UUID1","ibmcloud_ssh_key_UUID2",... ].
    * The region for your resources
    * The zone for your resources
    * Whether to use an existing VPC or create one
    * Whether to use an existing subnet
    * Whether to create new port only when a new subnet is created
    * TCP port range, minimum, and maximum
    * VPC name
    * Subnet name
    * Security group name
    * Hostname
    * Profile
    * Image
    * Minimal recommended disk sizes
    * SAP main password - must be at least 10 characters, upper and lowercase letters, a number, and a special character, not an exclamation point.
    * Click **Save** changes.

    For a more detailed description of each of the parameters, check the GitHub repo [Readme](https://github.com/IBM-Cloud/sap-hana-single-vsi/blob/main/README.md) file, chapter “Input parameter file”. Also, make sure to mark as “sensitive” the parameters that contain sensitive information like passwords, API, and SSH private keys (they are marked as “sensitive” in the [Readme](https://github.com/IBM-Cloud/sap-hana-single-vsi/blob/main/README.md) file in the “Input parameter file”).

7. On the workspace **Settings** page, click **Generate** plan. Wait for the plan to complete.
8. Click **View log** to review the log files of your Terraform execution plan
9. Apply your Terraform template by clicking **Apply plan**.
10. Review the log file to ensure that no errors occurred during the provisioning, modification, or deletion process.

## Next steps
{: #automate-hana-standalone-vsi-next}

Do not use the IBM Cloud Dashboard and user interface to modify your resources after they are created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results.

If you need to remove the resources created with the automation for your SAP solution, go to your project folder and run `terraform destroy`.

## Related information
{: #automate-hana-standalone-vsi-related}

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Terraform on IBM Cloud getting started tutorial](https://test.cloud.ibm.com/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

For more information about using Terraform for creating only a VPC for SAP, without the SAP architecture, see [Creating single-tier VPC for SAP on IBM Cloud® VPC with Terraform](https://test.cloud.ibm.com/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).

SAP One Support Notes that apply to this document:

*	[SAP Note 84555 - Windows Server, Linux&reg;, and UNIX: Certified hardware](https://launchpad.support.sap.com/#/notes/84855)
*	[SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
*	[SAP Note 2923773 - Linux&reg; on IBM Cloud (IaaS): Adaption of your SAP License](https://launchpad.support.sap.com/#/notes/2923773)
*	[SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)
*	[SAP Note 2369910 - SAP Software on Linux&reg;: General information](https://launchpad.support.sap.com/#/notes/2369910)
*	[SAP Note 171380 - Released IBM hardware (Intel processors) and IBM cloud services offers](https://launchpad.support.sap.com/#/notes/171380)
*	[SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)

This document is referenced by:

*	[SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
*	[SAP Note 2588225 - SAP on IBM Cloud: Protect against speculative execution vulnerabilities](https://launchpad.support.sap.com/#/notes/2588225)
*	[SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)
*	[SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)
