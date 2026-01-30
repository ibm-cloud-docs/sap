---
copyright:
  years: 2022, 2026
lastupdated: "2026-01-23"
keywords:
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Deploying SAP BW/4HANA on 3-tier {{site.data.keyword.cloud}} VPC (Terraform and Ansible)
{: #bw4hana-automation-on-vpc}

## Single-host SAP HANA system
{: #bw4hana-automation-single-hana-system}

A single-host system is the simplest system installation type that runs an SAP HANA system entirely on one host. You can scale the system up as needed. The single-host system has these components:

![Figure 1. SAP NetWeaver 7.x SAP HANA single-host installation with AAS](../../images/vpc-intel-vsi-sap-hana-vpc-std-pas-aas.svg "SAP NetWeaver 7.x SAP HANA standard installation with AAS"){: caption="SAP NetWeaver 7.x SAP HANA single-host installation with AAS" caption-side="bottom"}

The scripts are designed to create a new VPC and install SAP (SAP BW/4HANA release) solution together with its dedicated DB Hana box in one task flow.

## What is created
{: #bw4hana-automation-created}

The scripts work in two phases. The first phase automates the resources for the VPC provisioning process in an existing VPC created when you deployed the bastion VSI. The second phase creates the SAP architecture in a distributed environment SAP BW/4HANA application server on a distinct VSI VPC system and SAP HANA database on a dedicated server type VSI VPC box system.

During the first phase, the VPC is provisioned in an existing VSI created when you deployed the bastion VSI:

* 1 VPC where the virtual server instances are provisioned.
* 1 security group. The rules for this security group allow:
    * Inbound DNS (port 53) and
    * Inbound SSH (TCP port 22) connections to your virtual server instance.
    * All outbound traffic from the virtual server instance.
*	1 subnet to enable networking in your VPC.
*	2 X virtual server instance with SAP certified storage and network configurations.

During the second phase, the Ansible Playbook is called and the SAP architecture is installed for both dedicated VSI’s SAP App VSI system and dedicated SAP HANA VSI box. The SAP architecture that is deployed is the SAP BW/4HANA release on stand-alone dedicated SAP HANA 2.0 box release. For more information about this architecture, see [Automating SAP HANA stand-alone virtual server instance on {{site.data.keyword.cloud}} VPC by using Terraform and Ansible](/docs/sap?topic=sap-automate-terraform-sap-hana-vsi).

## Script files
{: #bw4hana-automation-script-files}

The configuration and script files are provided in GitHub. There are two repositories for each SAP solution:

* Using the bastion server CLI to run the Terraform scripts - [GitHub repository](https://github.com/IBM-Cloud/sap-bw4hana/tree/main){: external}
* Using Schematics user interface on {{site.data.keyword.cloud_notm}} - [GitHub repository](https://github.com/ibm-cloud/sap-bw4hana/tree/main){: external}

## SAP Kits
{: #bw4hana-automation-sap-kits}

For each {{site.data.keyword.cloud_notm}} region, IBM allocates temporary storage on a dedicated Jump host. It is your responsibility to download the necessary SAP and DB kits to your Deployment (Bastion) Server. All file archives are decompressed by Ansible during the automatic deploying process. For more information, see the readme file.

## Schematics deployment
{: #bw4hana-automation-schematics}
{: ui}

You use the Schematics interface to deploy your infrastructure with the scripts in the [GitHub repository](https://github.com/ibm-cloud/sap-bw4hana/tree/main){: external}. When you run the scripts with the Schematics interface, you:

* Enter the URL for the GitHub repository for the Terraform files
* Modify the parameters in the Schematics interface. They are the same parameters as the `input.auto.tfvars` file that you use when you are running the Terraform scripts in the CLI.

## Catalog Tile deployment
{: #bw4hana-automation-single-catalog-tile}
{: ui}

When you use the Catalog Tile for deployment, you:
*   Select the **SAP BW/4HANA** tile from the catalog
*   Enter information for your workspace. The Catalog Tile creates a Schematics workspace for you.
*   Modify the parameters for your bastion server, personal credential information, and other parameters specific to your solution.

## Terraform deployment
{: #automate-bw4hana-terraform-ansible-terraform}
{: terraform}

You use the Bastion server CLI to run the Terraform scripts that are located in the [GitHub repository](https://github.com/IBM-Cloud/sap-bw4hana/tree/main){: external}.

To run the scripts to deploy the SAP BW/4HANA release on dedicated SAP HANA 2.0 BOX VSI, you need to:

*	The `input.auto.tfvars` file to customize the resources for your solution.
    *  Enter the floating IP and subnet information from the Bastion server.
    *  By default, the VSI is configured with:
        * Red Hat Enterprise Linux® 8.x for SAP Applications (amd64) or Suse Enterprise Linux 15 for SAP Applications (amd64),
        * At least one SSH key that is configured to access as `root` user on SSH,
        * Five storage volumes.

        You can change the default settings to match your solution.

    *  You can change the default SAP system configuration settings to match your solution.
    *  You also specify the location where you downloaded the SAP kits.

The {{site.data.keyword.cloud_notm}} Provider plug-in for Terraform on {{site.data.keyword.cloud_notm}} uses these configuration files to provision a VPC in your {{site.data.keyword.cloud_notm}} account.

For SAP BW/4HANA virtual server instance on {{site.data.keyword.vpc_short}}, you modify the `input.auto.tfvars` file to customize the resources for your solution. You specify zones, resource names, SSH keys, and SAP variables.

All of the other configuration files are provided and do not need to be modified.

The {{site.data.keyword.terraform-provider_full_notm}} on {{site.data.keyword.cloud_notm}} uses these configuration files to provision a VPC in your {{site.data.keyword.cloud_notm}} account.

## Support - Schematics and Terraform
{: #bw4hana-automation-support-schematics-terraform}

There are no warranties of any kind, and there is no service or technical support available for these materials from {{site.data.keyword.IBM}}. As a recommended practice, review carefully any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, re-adapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Support - Catalog Tile
{: #bw4hana-automation-support-catalog-tile}
{: ui}

The catalog tile offering is {{site.data.keyword.cloud_notm}} supported. For more information, see [Getting help and support from IBM Cloud or SAP](/docs/sap?topic=sap-help-support&interface=ui).

If client issues are identified with SAP software, then SAP Support will assist the client. Follow the recommendations of SAP Note 90835, which describes the SAP Incident Escalation Procedure. This SAP Note (and others) is found at https://support.sap.com/en/index.html.

## Virtual server instance configuration
{: #bw4hana-automation-vsi-config}
{: terraform}

The virtual server instance is configured with:

* Red Hat Enterprise Linux 8.6 for SAP HANA (x86_64)
* Minimum one or more SSH keys that are configured to access as root user on SSH

## Before you begin
{: #bw4hana-automation-before-begin}

*	Set up your account to access the VPC. Make sure that your account is upgraded to a [paid account](/docs/account?topic=account-upgrading-account).
*   If you have not already, create a bastion server to store the SAP kits. For more information, see [Automate SAP bastion server - SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server).
*	Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. Ansible decompresses all of the archive kits. For more information, see the readme file.
*  [Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey&interface=ui#create_user_key). The API key is used to authenticate with the IBM Cloud platform and to determine your permissions for IBM Cloud services.
*  [Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.

During configuration you can specify an existing VPC to use, instead of creating a new VPC.

For the detailed steps about using Terraform to create only a VPC for SAP, see [Creating single-tier virtual private cloud for SAP by using Terraform](/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).

## Deploying SAP BW/4HANA with the Catalog Tile interface
{: #bwhana-automation-procedure-schematics-ui}
{: ui}

Use these steps to configure the SAP BW/4HANA on your existing VPC by using the Catalog Tile interface. The scripts can take 1 - 2 hours to complete.

1.	From the {{site.data.keyword.cloud_notm}} Catalog, select the **SAP BW/4HANA** tile. The tile opens the Create tab for SAP BW/4HANA. For more information about this deployment, see the About tab or the Readme file link.
2.	On the SAP BW/4HANA page, configure your workspace:
    * Enter a name for the workspace or use the default.
    * The Resource Group to use to create resources. Use the  Default or create a Resource Group.
    * Select a **Location** to create your Schematics workspace. The workspace location does not have to match the resource location.
3.  Enter the required deployment values, review the default input variables, and provide values that match your solution. These parameters are specific to your deployment.  For more detailed information, see the [Readme file - Input Parameters](https://cloud.ibm.com/catalog/content/content-ibm-sap-vpc-automation-bw4hana-e608642e-1d96-4a77-a401-1fb30307d2b9-global/readme/terraform/terraform/da22b12c-a034-4c00-aaeb-0dcaf9f3e8af-global){: external}.

    |Parameter	|Description|
    |-----|-----|
    |APP_HOSTNAME	|APP VSI hostname|
    |BASTION_FLOATING_IP	|Input the floating IP of the Bastion Server you created before you started this deployment. For more information, see [Automate SAP bastion server - SAP media storage](/docs/sap?topic=sap-sap-bastion-server).|
    |DB_HOSTNAME	|DB VSI hostname|
    |REGION	|Cloud Region where resources are deployed |
    |RESOURCE_GROUP	|EXISTING Resource Group for VSIs and Volumes |
    |SECURITY_GROUP	|EXISTING Security group name |
    |SSH_KEYS	|SSH Keys ID list to access the VSI|
    |SUBNET	|EXISTING Subnet name |
    |VPC	|EXISTING VPC name |
    |ZONE	|Cloud Zone where resources are deployed
    |HANA_MAIN_PASSWORD	|HANA main password or use a secret stored in Secrets Manager|
    |IBMCLOUD_API_KEY	|IBM Cloud API key or use a secret stored in Secrets Manager|
    |PRIVATE_SSH_KEY	|Input id_rsa private key content or use a secret stored in Secrets Manager|
    |SAP_MAIN_PASSWORD	|SAP main password or use a secret stored in Secrets Manager|
    {: caption="Required Variables" caption-side="bottom"}

4.  Review and update the optional parameters. The Ansible scripts expect the SAP kits to be in the default locations listed.  For more detailed information, see the [Readme file - Input Parameters](https://cloud.ibm.com/catalog/content/content-ibm-sap-vpc-automation-bw4hana-e608642e-1d96-4a77-a401-1fb30307d2b9-global/readme/terraform/terraform/da22b12c-a034-4c00-aaeb-0dcaf9f3e8af-global){: external}.

    |Parameter	|Description|
    |-----|-----|
    |APP_IMAGE	|APP VSI OS image|
    |APP_PROFILE	|APP VSI profile|
    |DB_IMAGE	|DB VSI OS image|
    |DB_PROFILE	|DB VSI profile|
    |HANA_COMPONENTS	|SAP HANA Components|
    |HANA_SID	|The SAP system ID identifies the SAP HANA system|
    |HANA_SYSNO	|Specifies the instance number of the SAP HANA system|
    |HANA_SYSTEM_USAGE	|System Usage|
    |HDB_CONCURRENT_JOBS	|Number of concurrent jobs used to load and/or extract archives to HANA Host|
    |KIT_HDBCLIENT_FILE	|Path to HANA DB client archive (SAR)|
    |KIT_IGSEXE_FILE	|Path to IGS archive (SAR)|
    |KIT_IGSHELPER_FILE	|Path to IGS Helper archive (SAR)|
    |KIT_BW4HANA_EXPORT	|Path to BW/4HANA Installation Export dir|
    |KIT_SAPCAR_FILE	|Path to sapcar binary|
    |KIT_SAPEXE_FILE	|Path to SAP Kernel OS archive (SAR)|
    |KIT_SAPEXEDB_FILE	|Path to SAP Kernel DB archive (SAR)|
    |KIT_SAPHANA_FILE	|Path to SAP HANA ZIP file|
    |KIT_SAPHOSTAGENT_FILE	|Path to SAP Host Agent archive (SAR)|
    |KIT_SWPM_FILE	|Path to SWPM archive (SAR)|
    |SAP_ASCS-INSTANCE_NUMBER	|Technical identifier for internal processes of ASCS|
    |SAP_CI_INSTANCE_NUMBER	|Technical identifier for internal processes of CI|
    |SAP_SID	|The SAP system ID identifies the entire SAP system|
    {: caption="Optional Variables" caption-side="bottom"}

5.	Accept the license agreement.
6.  Select **Install**. The deployment starts and you are directed to the Schematics page that displays the script log files for you to monitor the deployment progress.

## Procedure for Schematics interface
{: #bw4hana-automation-schematics-procedure}
{: ui}

Use these steps to configure the  SAP BW/4HANA on your existing VPC by using the Schematics user interface. The scripts can take 1 - 2 hours to complete.

1.	From the IBM Cloud menu, select [Schematics](https://cloud.ibm.com/schematics/overview){: external}.
2.	Click **Create workspace**.
3.  On the **Specify template** page:
    * Enter the URL for the Schematics interface.
    * Select the **Terraform version**.
    * Click **Next**.
4.  On the **Workspace details** page:
    * Enter a name for the workspace.
    * Select a **Resource group**.
    * Select a **Location** for your workspace. The workspace location does not have to match the resource location.
    * Select **Next**.
5.  Select **Create** to create your workspace.
6.  On the workspace **Settings** page, in the Input variables section, review the default input variables and provide values that match your solution:
     * Your API key
     * Your private SSH key from your local machine
     * The ID for the SSH key that you created and uploaded to IBM Cloud. Enter the SSH key ID in square brackets and quotation marks, for example [ "ibmcloud_ssh_key_UUID1","ibmcloud_ssh_key_UUID2",... ].
     * The floating IP address for your bastion server.
     * The Region for your resources
     * The Zone for your resources
     * Whether to use an existing VPC or create one
     * Whether to use an existing subnet
     * Whether to create new port only when a new subnet is created
     * TCP port range, nimimun and maximum
     * VPC name
     * Subnet name
     * Security group name
     * Hostname
     * Profile
     * Image
     * Minimal recommended disk sizes
     * SAP HANA main password - This password must be 8 - 14 characters, upper and lowercase letters, a number, and a special character.
     * SAP main password - This password must be 10 - 14 characters, upper and lowercase letters, a number, and a special character that is not an exclamation point.
     * Click **Save changes**.

     For a more detailed description of each of the parameters, check the GitHub repo readme file, chapter “Input parameter file”. Also, make sure to mark the parameters that contain sensitive information like passwords, API, and ssh private keys as "sensitive". these parameters are marked as “sensitive” in the readme file, under “Input parameter file”.

7.	On the workspace Settings page, click **Generate plan**. Wait for the plan to complete.
8.	Click **View log** to review the log files of your Terraform execution plan.
9.	Apply your Terraform template by clicking **Apply plan**.
10.	Review the log file to ensure that no errors occurred during the provisioning, modification, or deletion process.

## Procedure for Terraform scripts
{: #bw4hana-automation-terraform}
{: terraform}

1.	Log in to the Deployment Server by using `ssh`.
2.	Clone the terraform and ansbile folders and readme file from https://github.com/IBM-Cloud/sap-bw4hana/tree/main


	    git clone https://github.com/IBM-Cloud/sap-bw4hana.git

	    cd sap-bw4hana/

3.	Edit the input parameter file, `input.auto.tfvars`, and modify the variables to match your solution. The file is preset with the minimal recommended disk sizes. For using an existing VPC, you must modify:

    * VPC - An existing VPC name. By defulat, the scripts expect an existing VPC name.
    * SECURITYGROUP - change `ic4sap` to the VPC name.
    * SUBNET - change `ic4sap` to the VPC name.
    * DB_HOSTNAME - enter a hostname up to 13 characters. For more information, see the readme file.
    * APP_HOSTNAME - enter a hostname up to 13 characters. For more information, see the readme file.

    Replace the SSH keys in the file with your SSH key 40-digit ID.

    ```terraform
    #General VPC variables
    REGION          = "ed-de"
    ZONE            = "eu-de-2"
    VPC             = "ic4sap"
    SECURITYGROUP   = "ic4sap-securitygroup"
    SUBNET          = "ic4sap-subnet"
    SSH_KEYS        = [ "ssh key1" , "ssh key2" ]

    # SAP Database VSI variables:
    DB_HOSTNAME     = "sapbw4db"
    DB_PROFILE      = "mx2-16x128"
    DB_IMAGE        = "ibm-redhat-7-6-amd64-sap-hana-1" # For any manual change in the terraform code, you have to make sure that you use a certified image based on the SAP Note: 2927211.

    # SAP APPs VSI variables:
    APP_HOSTNAME    = "sapbw4app"
    APP_PROFILE     = "bx2-4x16"
    APP_IMAGE       = "ibm-redhat-7-6-amd64-sap-applications-1" # For any manual change in the terraform code, you have to make sure that you use a certified image based on the SAP Note: 2927211.

    ```

    The hostname must have up to 13 characters as required by SAP. For more information about the rules that apply to hostnames for SAP systems, see SAP Note 611361 - Hostnames of SAP ABAP Platform servers
    {: note}

4.	In the same file (`input.auto.tfvars`), edit the SAP system configuration variables that are passed to the Ansible automated deployment.

    ```terraform
    #SAP HANA DB configuration
    HANA_SID = "BWH"
    HANA_SYSNO = "00"
    HANA_SYSTEM_USAGE = "custom"
    HANA_COMPONENTS = "server"

    #SAP HANA Installation kit path
    KIT_SAPHANA_FILE = "/storage/HANADB/51054623.ZIP"

    #SAP system configuration
    SAP_SID = "BWH"
    SAP_ASCS_INSTANCE_NUMBER = "01"
    SAP_CI_INSTANCE_NUMBER = "00"

    # Number of concurrent jobs used to load and/or extract archives to HANA Host
    HDB_CONCURRENT_JOBS = "6"

    #SAP BW4HANA APP Installation kit path
    KIT_SAPCAR_FILE = "/storage/BW4HANA/SAPCAR_1010-70006178.EXE"
    KIT_SWPM_FILE = "/storage/BW4HANA/SWPM20SP09_4-80003424.SAR"
    KIT_SAPEXE_FILE = "/storage/BW4HANA/SAPEXE_400-80004393.SAR"
    KIT_SAPEXEDB_FILE = "/storage/BW4HANA/SAPEXEDB_400-80004392.SAR"
    KIT_IGSEXE_FILE = "/storage/BW4HANA/igsexe_13-80003187.sar"
    KIT_IGSHELPER_FILE = "/storage/BW4HANA/igshelper_17-10010245.sar"
    KIT_SAPHOTAGENT_FILE = "/storage/BW4HANA/SAPHOSTAGENT51_51-20009394.SAR"
    KIT_HDBCLIENT_FILE = "/storage/BW4HANA/IMDB_CLIENT20_009_28-80002082.SAR"
    KIT_BW4HANA_EXPORT = "/storage/BW4HANA/export"
    ```

5. Initialize the Terraform CLI.

    ``terraform init``

6.	Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account.

    ``terraform plan``

    You must enter an SAP HANA main password and an SAP main password.

    The SAP HANA main password must consist of at least one digit (0-9), one lowercase letter (a-z), and one uppercase letter (A-Z). It can contain only the following characters: a-z, A-Z, 0-9, !, @, #, $, _. It must not start with a digit or an underscore ( _ ).

    The SAP main password must be 10 - 14 characters long and contain at least one digit (0-9). It can only contain the following characters: a-z, A-Z, 0-9, @, #, $, _. This password cannot contain `!`. It must not start with a digit or an underscore ( _ ).

7. Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the ``input.auto.tfvars`` file to correct resources and run ``terraform plan`` again.


8. Create the virtual private cloud for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.

    ``terraform apply``

     The virtual private cloud and components are created and you see output similar to the `terraform plan` output.

## Next steps
{: #bw4-automation-next-steps}
{: terraform}

If you need to rename your resources after they are created, modify the ``input.auto.tfvars`` file to change the names and run ``terraform plan`` and ``terraform apply`` again. Do not use the {{site.data.keyword.cloud_notm}} Dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results.

If you need to remove your VPC, go to your project folder and run ``terraform destroy``.

## Related information
{: #bw4-automation-related-info}

For more information about Terraform on IBM Cloud, see [Terraform on IBM Cloud getting started tutorial](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

For more information about using Terraform for creating only a VPC for SAP, without the SAP architecture, see [Creating single-tier virtual private cloud for SAP by using Terraform](/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).

SAP One Support Notes that apply to this document:

*	[SAP Note 84555 - Windows Server, Linux&reg;, and UNIX: Certified hardware](https://me.sap.com/notes/84855){: external}
*	[SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}
*	[SAP Note 2923773 - Linux&reg; on IBM Cloud (IaaS): Adaption of your SAP License](https://me.sap.com/notes/2923773){: external}
*	[SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}
*	[SAP Note 2369910 - SAP Software on Linux&reg;: General information](https://me.sap.com/notes/2369910){: external}
*	[SAP Note 171380 - Released IBM hardware (Intel processors) and IBM cloud services offers](https://me.sap.com/notes/171380){: external}
*	[SAP Note 1380654 - SAP support in IaaS environments](https://me.sap.com/notes/1380654){: external}

This document is referenced by:

*	[SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}
*	[SAP Note 2588225 - SAP on IBM Cloud: Protect against speculative execution vulnerabilities](https://me.sap.com/notes/2588225){: external}
*	[SAP Note 1380654 - SAP support in IaaS environments](https://me.sap.com/notes/1380654){: external}
*	[SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}
