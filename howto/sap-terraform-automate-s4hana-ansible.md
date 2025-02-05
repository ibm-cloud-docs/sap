---

copyright:
  years: 2023, 2025
lastupdated: "2025-02-05"

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

# Deploying SAP S/4HANA on 3-tier {{site.data.keyword.cloud}} VPC (Terraform and Ansible)
{: #automate-s4hana-terraform-ansible}

You can use Terraform scripts to create a single-tier VPC and create the SAP/SAP HANA in a distributed architecture on the bastion server. Creating the bastion server is a prerequisite for all IBM SAP VPC automated solutions. The automation scripts use the VPC information that you provide and then call the Ansible playbooks to create the SAP architecture on the specified VPC. Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} VPC infrastructure resources so that you can rapidly build complex, cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware that uses Intel&reg; Xeon CPUs and other Intel&reg; technologies.
{: shortdesc}

You have three deployment methods to choose from:

*   Terraform scripts that run from the CLI on your bastion server.
*   Catalog Tile user interface is accessed from the {{site.data.keyword.cloud_notm}} catalog.
*   {{site.data.keyword.cloud}} Schematics user interface accessed from the menu on your cloud dashboard.

## SAP Solution implemented
{: #automate-s4hana-terraform-ansible-solution}

SAP S/4HANA is an ERP system from SAP's ERP software product line. The software is based on the innovative SAP HANA database technology and was started as the fourth product generation in 2015. Users can choose between the SAP S/4HANA Cloud and On-Premise solution.

An ERP system is used for demand-oriented business resource planning. It is used to control processes and to link departments and functional areas in a meaningful way. Individual modules include applications for accounting, sales, production, and marketing. More complex tasks in customer or supply chain management can be done by ERP software. As the successor to the core product SAP ECC, SAP S/4HANA was presented as the intelligent ERP system of the new generation. With the modern technologies, the Software as Service (SaaS) version is designed to help companies standardize processes and make the leap to digitalization.

While previous SAP ERP solutions support the most common databases, SAP S/4HANA uses exclusively the SAP HANA in-memory database developed by SAP. This in-memory database offers users the greatest technical benefit and they benefit from increased performance. The "S" in S/4HANA stands for "simple", while the "4" refers to the generation sequence. Compared to the SAP core product SAP ECC, which is still used in most companies, SAP S/4HANA offers many innovative functions that revolutionize the system landscape from the ground up. As SAP plans to discontinue the mainstream maintenance of its existing ERP solutions by 2027, many SAP ECC users are already considering a migration to SAP S/4HANA.

 ![Figure 1. SAP S/4HANA architecture](images/refarch-sap-s4_hana.svg "SAP S/4HANA architecture"){: caption="SAP S/4HANA architecture" caption-side="bottom"}

## What is created
{: #automate-s4hana-terraform-ansible-created}

The scripts work in two phases. The first phase automates the provisioning of the {{site.data.keyword.cloud_notm}} resources in the VPC created when you deployed the [bastion VSI](/docs/sap?topic=sap-sap-bastion-server&interface=terraform). The second phase creates the SAP architecture in a distributed environment SAP S/4HANA application server on a distinct VSI VPC system and SAP HANA DB on a dedicated server type VSI or Bare Metal VPC box system. For more information about this architecture, see [SAP NetWeaver 7.x with SAP HANA IBM Cloud® VPC](/docs/sap?topic=sap-sap-refarch-nw-hana&interface=ui).

During the first phase, the VPC is provisioned with 2 X virtual server instances with SAP certified storage and network configurations.

During the second phase, the Ansible playbooks are called and the SAP architecture is installed for both dedicated VSIs SAP App VSI system and dedicated SAP HANA VSI or Bare Metal box. The SAP architecture that is deployed is the SAP S/4HANA release on a stand-alone dedicated SAP HANA 2.0 box release as a distributed deployment model. For more information about this architecture, see [Automating SAP HANA stand-alone virtual server instance on IBM Cloud® VPC by using Terraform and Ansible](/docs/sap?topic=sap-automate-terraform-sap-hana-vsi&interface=ui).

## Single-host SAP HANA system
{: #automate-s4hana-terraform-ansible-single}

A single-host system is the simplest system installation type that runs an SAP HANA database instance entirely on one host. With this automation you can deploy the HANA DB instance either on x86 VSI or Intel Virtual Bare Metal server types. You can scale the system up as needed. The single-host system has these components:

 ![Figure 2. Single-host SAP HANA system](images/refarch-sap-hana-single-host-only.svg "Single-host SAP HANA system"){: caption="Single-host SAP HANA system" caption-side="bottom"}

The scripts are designed to install SAP (SAP S/4HANA release) solution on an existing VPC, together with its dedicated DB SAP HANA box in one task flow.

## SAP Kits
{: #s4hana-automation-sap-kits}

It is your responsibility to download the necessary SAP and DB kits to your Deployment Server (bastion server). All files archives are decompressed by Ansible during the automation deployment process. For more information, see the readme file.

## Terraform deployment
{: #automate-s4hana-terraform-ansible-terraform}
{: terraform}

You can use the Bastion server CLI to run the Terraform scripts that are located in the [GitHub repository for SAP S/4HANA for Terraform](https://github.com/ibm-cloud/sap-s4hana){: external}.

To run the scripts to deploy the SAP S/4HANA release on dedicated SAP HANA 2.0 BOX VSI, you need to:

*	Customize the resources for your solution in `input.auto.tfvars` file.
    *  Enter the floating IP and subnet information from the Bastion server.
    *  By default, the VSIs are configured with:
        *	Red Hat Enterprise Linux® 8.6 for SAP HANA/Applications (amd64),
        *	SSH keys to access as root user on SSH,
        *	Storage volumes

        You can change the default settings to match your solution.

    *  You can change the default SAP system configuration settings to match your solution.
    *  You also specify the location where you downloaded the SAP kits.

The {{site.data.keyword.cloud_notm}} Provider plug-in for Terraform on {{site.data.keyword.cloud_notm}} uses these configuration files to provision a S/4HANA system in your {{site.data.keyword.cloud_notm}} account.

## Schematics deployment
{: #s4hana-automation-schematics}
{: ui}

You use the Schematics user interface on {{site.data.keyword.cloud}} and enter the [GitHub repository for S/4HANA Schematics](https://github.com/ibm-cloud/sap-s4hana){: external}.

When you run the scripts with the Schematics interface, you:

*   Enter Workspace information.
*   Enter the GitHub path.
*	Modify the parameters in the Schematics interface.

## Catalog Tile deployment
{: #s4hana-automation-catalog-tile}
{: ui}

When you use the Catalog Tile for deployment, you:

*   Select the **SAP S/4HANA** tile from the catalog
*   Enter information for your workspace. The Catalog Tile creates a Schematics workspace for you.
*   Modify the parameters for your bastion server, personal credential information, and other parameters specific to your solution.

## Support - Schematics and Terraform
{: #s4hana-automation-support}

There are no warranties of any kind, and there is no service or technical support available for these materials from IBM. As a recommended practice, review carefully any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, readapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Support - Catalog Tile
{: #s4hana-automation-support-catalog-tile}
{: ui}

The Catalog Tile offering is {{site.data.keyword.cloud_notm}} supported. For more information, see [Getting help and support from IBM Cloud or SAP](/docs/sap?topic=sap-help-support&interface=ui).

If client issues are identified with SAP software, then SAP Support assists the client. Follow the recommendations of SAP Note 90835, which describes the SAP Incident Escalation Procedure. This SAP Note (and others) is found at https://support.sap.com/en/index.html

## Before you begin
{: #s4hana-automation-before}

Before you deploy SAP S/4HANA:

*	Make sure that your account is upgraded to a [paid account](/docs/account?topic=account-upgrading-account).
*	If you have not already, create a bastion server to store the SAP kits. For more information, see [Automate SAP bastion server - SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server). You need the floating IP from your bastion server for deployment.

*	Download the SAP kits from the SAP Portal to your bastion Server. Make note of the download locations. Ansible decompresses all of the archive kits and needs the paths. For more information, see the Readme file in the GitHub repository and on the About page for the catalog tile.
*	[Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey&interface=ui#create_user_key). The API key is used to authenticate with the {{site.data.keyword.cloud_notm}} platform and to determine your permissions for {{site.data.keyword.cloud_notm}} services.
*	[Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.
* (Optional - Catalog Tile) create secrets for your credentials and passwords by using the [Secrets Manager](/docs/secrets-manager?topic=secrets-manager-arbitrary-secrets&interface=ui). {: ui}

## Deploying SAP S/4HANA using Terraform scripts
{: #s4hana-automation-procedure-terraform}
{: terraform}

Use these steps to deploy the SAP S/4HANA 3 tiers architecture on your existing VPC. The scripts can take 2 - 3 hours to complete. The scripts can take 1 - 2 hours to complete. The supported versions available for S/4HANA are 2020, 2021, 2022, and 2023. For more information, see the [readme](https://github.com/IBM-Cloud/sap-s4hana){: external} for recommended kits versions.

1.	Log in to the Deployment Server by using `ssh`.
2.	Clone the repository and change the path to the sap-s4hana folder.

    ```
	git clone https://github.com/IBM-Cloud/sap-s4hana.git

	cd sap-s4hana
    ```

3.	Edit the input parameter file `input.auto.tfvars`, and modify the variables to match your solution. The file is preset with the minimal recommended disk sizes. For using an existing VPC, you must modify:

    * VPC - An existing VPC name
    * REGION - Region for the VSIs
    * ZONE - Availability zone for VSIs
    * SECURITY_GROUP - Existing Security Group, previously created by the user in the same VPC.
    * SUBNET - Existing subnet in the same region and zone as the VSIs, previously created by the user.
    * RESOURCE_GROUP - Existing resource group, previously created by the user.
    * SSH_KEYS - List of SSH Keys UUIDs that are allowed to SSH as root to the VSIs.
    * ID_RSA_FILE_PATH - `id_rsa` private key file path in OpenSSH format with 0600 permissions.
    * APP_HOSTNAME/DB_HOSTNAME - Enter a hostname up to 12 characters. For more information, see the readme file.
    * DB_PROFILE/APP_PROFILE - The instance profile used for the HANA/APP VSI. HANA database instance can be deployed on x86 VSI or bare metal server types.
    * DB_IMAGE/APP_IMAGE - The OS image for DB/APP VSI.

    For more options for profile, see Instance Profiles. For more options for image, see Images. For descriptions of the variables, see the readme file.

    ```
	REGION          = ""
	ZONE            = ""
	VPC             = ""
	SECURITYGROUP   = ""
	SUBNET          = ""
	SSH_KEYS        = [""]
    ID_RSA_FILE_PATH   = "ansible/id_rsa"

    # SAP Database VSI variables:
	DB_HOSTNAME     = ""
	DB_PROFILE      = "mx2-16x128"
	DB_IMAGE        = "ibm-redhat-8-6-amd64-sap-hana-5" # For any manual change in the terraform code, you have to make sure that you use a certified image based on the SAP NOTE: 2927211.

	# SAP APPs VSI variables:
	APP_HOSTNAME    = ""
	APP_PROFILE     = "bx2-4x16"
	APP_IMAGE       = "ibm-redhat-8-6-amd64-sap-applications-5" # For any manual change in the terraform code, you have to make sure that you use a certified image based on the SAP NOTE: 2927211.
    ```

4.	Customize your SAP system configuration with the values to be passed to the Ansible playbooks for the automated deployment.

    ```
    # S/4HANA version
    S4HANA_VERSION = ""

	# SAP HANA DB configuration
	HANA_SID = "HDB"
	HANA_SYSNO = "00"
	HANA_SYSTEM_USAGE = "custom"
	HANA_COMPONENTS = "server"
    HANA_SERVER_TYPE = ""
    HANA_TENANT = ""

	# SAP HANA Installation kit path
	KIT_SAPHANA_FILE = "/storage/HANADB/51057281.ZIP"

	#SAP system configuration
	SAP_SID= "S4A"
	SAP_ASCS_INSTANCE_NUMBER = "01"
	SAP_CI_INSTANCE_NUMBER = "00"

	# Number of concurrent jobs used to load and/or extract archives to SAP HANA Host
	HDB_CONCURRENT_JOBS = "23"

	# SAP S4HANA APP Installation kit path
	KIT_SAPCAR_FILE = "/storage/S4HANA/SAPCAR_1010-70006178.EXE"
	KIT_SWPM_FILE = "/storage/S4HANA/SWPM20SP17_0-80003424.SAR"
	KIT_SAPEXE_FILE = "/storage/S4HANA/KERNEL/793/SAPEXE_60-70007807.SAR"
	KIT_SAPEXEDB_FILE = "/storage/S4HANA/KERNEL/793/SAPEXEDB_60-70007806.SAR"
	KIT_IGSEXE_FILE = "/storage/S4HANA/KERNEL/793/igsexe_4-70005417.sar"
	KIT_IGSHELPER_FILE = "/storage/S4HANA/igshelper_17-10010245.sar"
	KIT_SAPHOTAGENT_FILE = "/storage/S4HANA/SAPHOSTAGENT61_61-80004822.SAR"
	KIT_HDBCLIENT_FILE = "/storage/S4HANA/IMDB_CLIENT20_018_27-80002082.SAR"
	KIT_S4HANA_EXPORT = "/storage/S4HANA/2023"
    ```

5.	Initialize the Terraform CLI.

    ```
	terraform init
    ```

6.	Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account.

    ```
	terraform plan --out plan1
    ```

	You are asked to provide an SAP HANA main password, an SAP main password, and the {{site.data.keyword.cloud_notm}} API key.

    The SAP HANA main password must consist of at least one digit (0-9), one lowercase letter (a-z), and one uppercase letter (A-Z). It can contain only the following characters: a-z, A-Z, 0-9, !, @, #, $, _. It must not start with a digit or an underscore ( _ ).

    The SAP main password must be 10 - 14 characters long and contain at least one digit (0-9). It can contain only the following characters: a-z, A-Z, 0-9, @, #, $, _. This password cannot contain `!` and must not start with a digit or an underscore ( _ ).

	Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the `input.auto.tfvars` file to correct resources and run terraform plan again.

7.	Create the {{site.data.keyword.cloud_notm}} resources for S/4HANA and install the SAP system.

    ```
	terraform apply "plan1"
    ```

    The {{site.data.keyword.cloud_notm}} resources are created and you can see the output similar to the terraform plan output.

## Next steps for Terraform
{: #s4hana-automation-next-steps-terraform}
{: terraform}

If you need to remove your S/4HANA system, go to your project folder and run the `terraform destroy`.

## Deploying SAP S/4HANA with the Catalog Tile interface
{: #s4hana-automation-procedure-catalog-ui}
{: ui}

Use these steps to configure the SAP S/4HANA on your existing VPC by using the Catalog Tile interface. The available supported S/4HANA versions are 2020, 2021, 2022, and 2023. For more information, see the [readme](https://cloud.ibm.com/catalog/content/content-ibm-sap-vpc-automation-s4hana-ec60f4ee-c27d-4bcb-8aef-dee83a3f2659-global/readme/terraform/terraform/dc5f4da1-ff39-45b9-b78b-2123936bc21f-global){: external} file for recommended kits versions. The scripts can take 1 - 2 hours to complete.

1.	From the {{site.data.keyword.cloud_notm}} Catalog, select the **SAP S/4HANA** tile. The Tile opens the Create tab for SAP S/4HANA. For more information about this deployment, see the About tab or the Readme file link.
2.	On the SAP S/4HANA page, configure your workspace:
    * Enter a name for the workspace or use the default.
    * The Resource Group to use to create resources. Use the Default or create a Resource Group.
    * Select a **Location** to create your Schematics workspace. The workspace location does not have to match the resource location.
3.  Enter the required deployment values, review the default input variables, and provide values that match your solution. These parameters are specific to your deployment.  For more detailed information, see the [Readme file - Input Parameters](https://cloud.ibm.com/catalog/content/content-ibm-sap-vpc-automation-s4hana-ec60f4ee-c27d-4bcb-8aef-dee83a3f2659-global/readme/terraform/terraform/7265035e-c57d-41f4-b804-4e495ad4c4b7-global){: external}.

    |Parameter	|Description|
    |-----|-----|
    |BASTION_FLOATING_IP	|Input the floating IP of the Bastion Server you created before you started this deployment. For more information, see [Automate SAP bastion server - SAP media storage](/docs/sap?topic=sap-sap-bastion-server).|
    |REGION	|Cloud Region where resources are deployed |
    |RESOURCE_GROUP	|EXISTING Resource Group for VSIs and Volumes |
    |SECURITY_GROUP	|EXISTING Security group name |
    |SSH_KEYS	|SSH Keys ID list to access the VSI|
    |SUBNET	|EXISTING Subnet name |
    |VPC	|EXISTING VPC name |
    |ZONE	|Cloud Zone where resources are deployed
    |HANA_MAIN_PASSWORD	|HANA main password or use a secret that is stored in Secrets Manager |
    |IBMCLOUD_API_KEY	|IBM Cloud API key or use a secret that is stored in Secrets Manager|
    |PRIVATE_SSH_KEY	|Input id_rsa private key content or use a secret stored in Secrets Manager|
    |SAP_MAIN_PASSWORD	|SAP main password or use a secret that is stored in Secrets Manager |

4.  Review and update the optional parameters. The Ansible scripts expect the SAP kits to be in the default locations listed.  For more detailed information, see the [Readme file - Input Parameters](https://cloud.ibm.com/catalog/content/content-ibm-sap-vpc-automation-s4hana-ec60f4ee-c27d-4bcb-8aef-dee83a3f2659-global/readme/terraform/terraform/7265035e-c57d-41f4-b804-4e495ad4c4b7-global){: external}.

    |Parameter	|Description|
    |-----|-----|
    |APP_HOSTNAME	|APP VSI hostname|
    |APP_IMAGE	|APP VSI OS image|
    |APP_PROFILE	|APP VSI profile|
    |DB_HOSTNAME	|DB VSI hostname|
    |DB_IMAGE	|DB VSI OS image|
    |DB_PROFILE	|DB VSI profile|
    |HANA_COMPONENTS	|hana_components|
    |HANA_SID	|hana_sid|
    |HANA_SYSNO	|hana_sysno|
    |HANA_SYSTEM_USAGE	|hana_system_usage|
    |HANA_TENANT	|The name of the SAP HANA tenant|
    |HANA_SERVER_TYPE	|The type of SAP HANA Server. Allowed values: "virtual" or "bare metal".|
    |HDB_CONCURRENT_JOBS	|hdb_concurent_jobs|
    |KIT_HDBCLIENT_FILE	|kit_hdbclient_file|
    |KIT_IGSEXE_FILE	|kit_igsexe_file|
    |KIT_IGSHELPER_FILE	|kit_igshelper_file|
    |KIT_S4HANA_EXPORT	|kit_s4hana_export|
    |KIT_SAPCAR_FILE	|kit_sapcar_file|
    |KIT_SAPEXE_FILE	|kit_sapexe_file|
    |KIT_SAPEXEDB_FILE	|kit_sapexedb_file|
    |KIT_SAPHANA_FILE	|kit_saphana_file|
    |KIT_SAPHOTAGENT_FILE	|kit_saphotagent_file|
    |KIT_SWPM_FILE	|kit_swpm_file|
    |SAP_ASCS_INSTANCE_NUMBER	|sap_ascs_instance_number|
    |SAP_CI_INSTANCE_NUMBER	|sap_ci_instance_number|
    |SAP_SID	|sap_sid|
    |S4HANA_VERSION	|The version of S/4HANA|

5.	Accept the license agreement.
6.  Select **Install**. The deployment starts and you are directed to the Schematics page that displays the script log files for you to monitor the deployment progress.

## Deploying SAP S/4HANA with the Schematics interface
{: #s4hana-automation-procedure-schematics-ui}
{: ui}

Use these steps to configure the SAP S/4HANA on your existing VPC by using the Schematics interface. The scripts can take 1 - 2 hours to complete. The supported versions available for S/4HANA are 2020, 2021, 2022, and 2023. For more information, see the [readme](https://cloud.ibm.com/catalog/content/content-ibm-sap-vpc-automation-s4hana-ec60f4ee-c27d-4bcb-8aef-dee83a3f2659-global/readme/terraform/terraform/dc5f4da1-ff39-45b9-b78b-2123936bc21f-global){: external} for recommended kits versions.

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
     * Your private SSH key from your local system
     * The ID for the SSH key that you created and uploaded to {{site.data.keyword.cloud_notm}}. Enter the SSH key ID in square brackets and quotation marks, for example [ "ibmcloud_ssh_key_UUID1","ibmcloud_ssh_key_UUID2",... ].
     * The floating IP address for your bastion server.
     * Resource group
     * The Region for your resources
     * The Zone for your resources
     * VPC name
     * Subnet name
     * Security group name
     * Hostname
     * Profile
     * Image
     * SAP HANA main password - This password must be 8 - 14 characters, upper and lowercase letters, a number, and a special character.
     * SAP main password - This password must be 10 - 14 characters, upper and lowercase letters, a number, and a special character that is not an exclamation point.
     * Click **Save changes**.

     For a more detailed description of each of the parameters, check the GitHub repo readme file, chapter “Input parameter file”. Also, make sure to mark the parameters that contain sensitive information like passwords, API, and ssh private keys as "sensitive". These parameters are marked as “sensitive” in the readme file, under “Input parameter file”.

7.	On the workspace Settings page, click **Generate plan**. Wait for the plan to complete.
8.	Click **View log** to review the log files of your Terraform execution plan.
9.	Apply your Terraform template by clicking **Apply plan**.
10.	Review the log file to ensure that no errors occurred during the provisioning, modification, or deletion process.

## Related information

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [*Getting started with Terraform on IBM Cloud*](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started) tutorial.

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

This automation is offered at no cost; however, the provisioned infrastructure comes at cost.
{: note}
