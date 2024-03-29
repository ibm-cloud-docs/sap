---

copyright:
  years: 2021, 2022, 2023, 2024
lastupdated: "2024-03-29"

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

# Deploying SAP S/4HANA on 3-tier {{site.data.keyword.cloud}} VPC (Terraform and Ansible)
{: #automate-s4hana-terraform-ansible}

You can use Terraform scripts to create a single-tier VPC and create the SAP and SAP HANA in a distributed architecture on the bastion server that you create. Creating the bastion server is a prerequisite for all IBM SAP VPC automated solutions. The automation scripts use the VPC information that you provide and then call the Ansible playbook to create the SAP architecture on the specified VPC. Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} VPC infrastructure resources so that you can rapidly build complex, cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware that uses Intel&reg; Xeon CPUs and other Intel&reg; technologies. 
{: shortdesc}

You have three deployment methods to choose from:

*   Terraform scrits run from the CLI on your bastion server
*   Catalog Tile user interface accessed from the {{site.data.keyword.cloud_notm}} Catalog
*   {{site.data.keyword.cloud}} Schematics user interface accessed from the menu on your cloud dashboard.

## SAP Solution implemented 
{: #automate-s4hana-terraform-ansible-solution}

SAP S/4HANA is an ERP system from SAP's ERP software product line. The software is based on the innovative SAP HANA database technology and was launched as the fourth product generation in 2015. Users can choose between the SAP S/4HANA Cloud and On-Premise solution.

An ERP system is used for demand-oriented business resource planning. It is used to control processes and to link departments and functional areas in a meaningful way. Individual modules include applications for accounting, sales, production, and marketing. More complex tasks in customer or supply chain management can also be done by ERP software. As the successor to the core product SAP ECC, SAP S/4HANA was presented as the intelligent ERP system of the new generation. Thanks to modern technologies, the Software as Service (SaaS) version is designed to help companies standardize processes and make the leap to digitalization.

While previous SAP ERP solutions support the most common databases, SAP S/4HANA uses exclusively the SAP HANA in-memory database developed by SAP. This in-memory database offers users the greatest technical benefit and they benefit from increased performance. The "S" in S/4HANA stands for "simple", while the "4" refers to the generation sequence. Compared to the SAP core product SAP ECC, which is still used in most companies, SAP S/4HANA offers many innovative functions that revolutionize the system landscape from the ground up. As SAP plans to discontinue the mainstream maintenance of its existing ERP solutions by 2027, many SAP ECC users are already considering a migration to SAP S/4HANA

## What is created
{: #automate-s4hana-terraform-ansible-created}

The scripts work in two phases. The first phase automates creating the resources for the VPC provisioning process in an existing VPC created when you deployed the bastion VSI. The second phase creates the SAP architecture in a distributed environment SAP S/4HANA App server on a distinct VSI VPC machine and SAP HANA DB on a dedicated server type VSI VPC box machine. For more information about this architecture, see [SAP NetWeaver 7.x with SAP HANA {{site.data.keyword.cloud}} VPC](/docs/sap?topic=sap-sap-refarch-nw-hana&interface=ui).

During the first phase, the VPC is provisioned with these components: 

*	1 VPC where the virtual server instances are provisioned
*	1 security group. The rules for this security group allow: 
    *	Inbound DNS (port 53) and 
    *	Inbound SSH (TCP port 22) connections to your virtual server instance 
    *	All outbound traffic from the virtual server instance
*   1 subnet to enable networking in your VPC
*	2 X virtual server instances with SAP certified storage and network configurations
*	2 floating IP’s address that you use to access your VPC virtual server instance over the public network

During the second phase, the Ansible Playbook is called and the SAP architecture is installed for both dedicated VSIs SAP App VSI machine and dedicated SAP HANA VSI box. The SAP architecture that is deployed is the SAP S/4HANA release on stand-alone dedicated SAP HANA 2.0 box release as a distributed deployment model. For more information about this architecture, see [Automating SAP HANA stand-alone virtual server instance on {{site.data.keyword.cloud}} VPC by using Terraform and Ansible](/docs/sap?topic=sap-automate-terraform-sap-hana-vsi).


## Single-host SAP HANA system
{: #automate-s4hana-terraform-ansible-single}

A single-host system is the simplest system installation type that runs an SAP HANA db system entirely on one host. You can scale the system up as needed. The single-host system has these components: 
 
 ![Figure 1. SAP NetWeaver 7.x SAP HANA single-host installation with AAS](images/refarch-sap-hana-single-host-only.svg "SAP NetWeaver 7.x SAP HANA standard installation with AAS"){: caption="Figure 1. SAP NetWeaver 7.x SAP HANA single-host installation with AAS" caption-side="bottom"}

The scripts are designed to install SAP (SAP S/4HANA release) solution on an existing VPC together with its dedicated DB SAP HANA box in one task flow.

## SAP Kits
{: #s4hana-automation-sap-kits}

For each {{site.data.keyword.cloud_notm}} region, IBM allocates temporary storage on a dedicated Jump host. It is your responsibility to download the necessary SAP and DB kits to your Deployment Server. All files archives are decompressed by Ansible during the automation deployment process. For more information, see the readme file.

## Terraform deployment
{: #automate-s4hana-terraform-ansible-terraform}
{: terraform}

You use the Bastion server CLI to run the Terraform scripts that are located in the [GitHub repository for SAP S/4HANA for Terraform](https://github.com/ibm-cloud/sap-s4hana/tree/main/cli).

To run the scripts to deploy the SAP S/4HANA release on dedicated SAP HANA 2.0 BOX VSI, you need to:

*	The input.auto.tfvars file to customize the resources for your solution. 
    *  Enter the floating IP and subnet information from the Bastion server.
    *  By default, the VSI is configured with:
        *	Red Hat Enterprise Linux® 7.x for SAP Applications (amd64), 
        *	Two SSH keys that are configured to access as root user on SSH, 
        *	Five storage volumes. 

        You can change the default settings to match your solution. 

    *  You can change the default SAP system configuration settings to match your solution. 
    *  You also specify the location where you downloaded the SAP kits.

The {{site.data.keyword.cloud_notm}} Provider plug-in for Terraform on {{site.data.keyword.cloud_notm}} uses these configuration files to provision a VPC in your {{site.data.keyword.cloud_notm}} account. 

## Schematics deployment
{: #s4hana-automation-schematics}
{: ui}

You use the Schematics user interface on {{site.data.keyword.cloud}} and enter the [GitHub repository for S/4HANA Schematics](https://github.com/ibm-cloud/sap-s4hana/tree/main/schematics).

When you run the scripts with the Schematics interface, you:

*   Enter Workspace information.  
*   Enter the Github path.
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

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, re-adapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Support - Catalog Tile
{: #s4hana-automation-support-catalog-tile}
{: ui}

The Catalog Tile offering is {{site.data.keyword.cloud_notm}} supported. For more information, see [Getting help and support from IBM Cloud or SAP](/docs/sap?topic=sap-help-support&interface=ui).

If client issues are identified with SAP software, then SAP Support will assist client. Please follow the recommendations of SAP Note 90835, which describes the SAP Incident Escalation Procedure. This SAP Note (and others) is found at https://support.sap.com/en/index.html

## Before you begin
{: #s4hana-automation-before}

Before you deploy SAP S/4HANA:

*	Set up your account to access the VPC. Make sure that your account is upgraded to a [paid account](/docs/account?topic=account-upgrading-account).
*	If you have not already, create a bastion server to store the SAP kits. For more information, see [Automate SAP bastion server - SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server). You need the floating IP from your bastion server for deployment.

*	Download the SAP kits from the SAP Portal to your bastion Server. Make note of the download locations. Ansible decompresses all of the archive kits and needs the paths. For more information, see the readme file for [Schematics](https://github.com/ibm-cloud/sap-s4hana/tree/main/schematics),  [Terraform](https://github.com/ibm-cloud/sap-s4hana/tree/main/cli), or the [Catalog Tile](https://cloud.ibm.com/catalog/content/content-ibm-sap-vpc-automation-s4hana-ec60f4ee-c27d-4bcb-8aef-dee83a3f2659-global/readme/terraform/terraform/7265035e-c57d-41f4-b804-4e495ad4c4b7-global). 
*	[Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey#create_user_key). The API key is used to authenticate with the {{site.data.keyword.cloud_notm}} platform and to determine your permissions for {{site.data.keyword.cloud_notm}} services.
*	[Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.
* (Optional - Catalog Tile) create secrets for your credentials and passwords by using the [Secrets Manager](/docs/secrets-manager?topic=secrets-manager-arbitrary-secrets&interface=ui). {: ui}

## Deploying SAP S/4HANA using Terraform scripts
{: #s4hana-automation-procedure-terraform}
{: terraform}

Use these steps to configure the SAP S/4HANA 3 tiers architecture on your existing VPC by using the catalog tile interface. The scripts can take 2 - 3 hours to complete. The scripts can take 1 - 2 hours to complete. The supported versions available for S/4HANA are 2020, 2021, 2022, and 2023. For more information, see the [readme](https://github.com/IBM-Cloud/sap-s4hana) for recommended kits versions.

1.	Log in to the Deployment Server by using `ssh`.
2.	Clone the terraform and ansbile folders and readme file from ``https://github.com/IBM-Cloud/sap-s4hana/tree/main/cli`` and change to the ``sap-s4hana/cli/terraform`` folder.

    ```
	$ git clone https://github.com/IBM-Cloud/sap-s4hana.git
	
	$ cd sap-s4hana/cli/terraform
    ```

3.	Edit the ``terraform.tfvars`` variable file and enter the {{site.data.keyword.cloud_notm}} API key that you retrieved. 

    ```
    ibmcloud_api_key = "<ibmcloud_apikey>"
    ```

    Variables that are defined in the `terraform.tfvars` file are automatically loaded by Terraform when the {{site.data.keyword.cloud_notm}} Provider plug-in is initialized and you can reference them in every Terraform configuration file that you use. 

    Because the terraform.tfvars file contains confidential information, do not push this file to a version control system. Keep this file on your local system only. 

4.	Edit the input parameter file, `input.auto.tfvars`, and modify the variables to match your solution. The file is preset with the minimal recommended disk sizes. For using an existing VPC, you must modify:

    * VPC - An existing VPC name.
    * SECURITYGROUP - Change ic4sap to the VPC name.
    * SUBNET - Change ic4sap to the VPC name.
    * HOSTNAME - Enter a hostname up to 12 characters. For more information, see the readme file. 

    You need your 40-digit SSH key ID for this file. The second SSH key is optional. 

    For more options for profile, see Instance Profiles. For more options for image, see Images. For descriptions of the variables, see the readme file.

    ```
	REGION          = "eu-de"
	ZONE            = "eu-de-2"
	VPC             = "ic4sap"
	SECURITYGROUP   = "ic4sap-securitygroup"
	SUBNET          = "ic4sap-subnet"
	SSH_KEYS        = [ "r010-57bfc315-f9e5-46bf-bf61-d87a24a9ce7a" , "r010-3fcd9fe7-d4a7-41ce-8bb3-d96e936b2c7e" , "r010-7945f2b4-06f2-4276-8d8f-d40922a8686d" , "r010-771e15dd-8081-4cca-8844-445a40e6a3b3" , "r010-09325e15-15be-474e-9b3b-21827b260717" ]
	# SAP Database VSI variables:
	DB-HOSTNAME     = "is110db"
	DB-PROFILE      = "mx2-16x128" 
	DB-IMAGE        = "ibm-redhat-7-6-amd64-sap-hana-1" # For any manual change in the terraform code, you have to make sure that you use a certified image based on the SAP NOTE: 2927211.

	# SAP APPs VSI variables:
	APP-HOSTNAME    = "is110apps"
	APP-PROFILE     = "bx2-4x16"
	APP-IMAGE       = "ibm-redhat-7-6-amd64-sap-applications-1" # For any manual change in the terraform code, you have to make sure that you use a certified image based on the SAP NOTE: 2927211.
    ```

5.	Customize your SAP system configuration.edit the SAP system configuration variables that are passed to the Ansible automated deployment.

    ```
	#SAP HANA DB configuration
	hana_sid = "HDB"
	hana_sysno = "00"
	hana_system_usage = "custom"
	hana_components = "server"

	#SAP HANA Installation kit path
	kit_saphana_file = "/storage/HANADB/51054623.ZIP"
	#SAP system configuration
	sap_sid = "S4A"
	sap_ascs_instance_number = "01"
	sap_ci_instance_number = "00"

	# Number of concurrent jobs used to load and/or extract archives to SAP HANA Host
	hdb_concurrent_jobs = "23"

	#SAP S4HANA APP Installation kit path
	kit_sapcar_file = "/storage/S4HANA/SAPCAR_1010-70006178.EXE"
	kit_swpm_file = "/storage/S4HANA/SWPM20SP09_4-80003424.SAR"
	kit_sapexe_file = "/storage/S4HANA/SAPEXE_100-70005283.SAR"
	kit_sapexedb_file = "/storage/S4HANA/SAPEXEDB_100-70005282.SAR"
	kit_igsexe_file = "/storage/S4HANA/igsexe_1-70005417.sar"
	kit_igshelper_file = "/storage/S4HANA/igshelper_17-10010245.sar"
	kit_saphotagent_file = "/storage/S4HANA/SAPHOSTAGENT51_51-20009394.SAR"
	kit_hdbclient_file = "/storage/S4HANA/IMDB_CLIENT20_009_28-80002082.SAR"
	kit_s4hana_export = "/storage/S4HANA/export"
    ```

6.	Initialize the Terraform CLI. 

    ```
	terraform init
    ```

7.	Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account. 

    ```
	terraform plan
    ```

	You must enter an SAP HANA main password and the SAP main password. 

    The SAP HANA main password must consist of at least one digit (0-9), one lowercase letter (a-z), and one uppercase letter (A-Z). It can contain only the following characters: a-z, A-Z, 0-9, !, @, #, $, _. It must not start with a digit or an underscore ( _ ). 
    
    The SAP main password must be 10 - 14 characters long and contain at least one digit (0-9). It can contain only the following characters: a-z, A-Z, 0-9, @, #, $, _. This password cannot contain `!` and must not start with a digit or an underscore ( _ ).

	Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the `input.auto.tfvars` file to correct resources and run terraform plan again.

8.	Create the virtual private cloud for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.

    ```
	terraform apply
    ```

    The virtual private cloud and components are created and you see output similar to the terraform plan output. 

9.	Add the SAP credentials and the virtual server instance IP to the SAP GUI. For more information about the SAP GUI, see SAP GUI.

## Next steps for Terraform
{: #s4hana-automation-next-steps-terraform}
{: terraform}

If you need to rename your resources after they are created, modify the `input.auto.tfvars` file to change the names and run `terraform plan` and `terraform apply` again. Do not use the {{site.data.keyword.cloud_notm}} Dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results. 

If you need to remove your VPC, go to your project folder and run `terraform destroy`.

## Deploying SAP S/4HANA with the Catalog Tile interface
{: #s4hana-automation-procedure-catalog-ui}
{: ui}

Use these steps to configure the SAP S/4HANA on your existing VPC by using the Catalog Tile interface. The available supported S/4HANA versions are 2020, 2021, 2022, and 2023. For more information, see the [readme](https://cloud.ibm.com/catalog/content/content-ibm-sap-vpc-automation-s4hana-ec60f4ee-c27d-4bcb-8aef-dee83a3f2659-global/readme/terraform/terraform/dc5f4da1-ff39-45b9-b78b-2123936bc21f-global) file for recommended kits versions. The scripts can take 1 - 2 hours to complete. 

1.	From the {{site.data.keyword.cloud_notm}} Catalog, select the **SAP S/4HANA** tile. The Tile opens the Create tab for SAP S/4HANA. For more information about this deployment, see the About tab or the Readme file link.
2.	On the SAP S/4HANA page, configure your workspace:
    * Enter a name for the workspace or use the default.
    * The Resource Group to use to create resources. Use the  Default or create a Resource Group.
    * Select a **Location** to create your Schematics workspace. The workspace location does not have to match the resource location.
3.  Enter the required deployment values, review the default input variables, and provide values that match your solution. These parameters are specific to your deployment.  For more detailed information, see the [Readme file - Input Parameters](https://cloud.ibm.com/catalog/content/content-ibm-sap-vpc-automation-s4hana-ec60f4ee-c27d-4bcb-8aef-dee83a3f2659-global/readme/terraform/terraform/7265035e-c57d-41f4-b804-4e495ad4c4b7-global).

    |Parameter	|Description|
    |-----|-----|
    |APP-HOSTNAME	|APP VSI hostname|
    |BASTION_FLOATING_IP	|Input the floating IP of the Bastion Server you created before you started this deployment. For more information, see [Automate SAP bastion server - SAP media storage](/docs/sap?topic=sap-sap-bastion-server).|
    |DB-HOSTNAME	|DB VSI hostname|
    |REGION	|Cloud Region where resources are deployed |
    |RESOURCE_GROUP	|EXISTING Resource Group for VSIs and Volumes |
    |SECURITY_GROUP	|EXISTING Security group name |
    |SSH_KEYS	|SSH Keys ID list to access the VSI|
    |SUBNET	|EXISTING Subnet name |
    |VPC	|EXISTING VPC name |
    |ZONE	|Cloud Zone where resources are deployed
    |hana_main_password	|HANA main password or use a secret stored in Secrets Manager |
    |ibmcloud_api_key	|IBM Cloud API key or use a secret stored in Secrets Manager|
    |private_ssh_key	|Input id_rsa private key content or use a secret stored in Secrets Manager|
    |sap_main_password	|SAP main password or use a secret stored in Secrets Manager |

4.  Review and update the optional parameters. The Ansible scripts expect the SAP kits to be in the default locations listed.  For more detailed information, see the [Readme file - Input Parameters](https://cloud.ibm.com/catalog/content/content-ibm-sap-vpc-automation-s4hana-ec60f4ee-c27d-4bcb-8aef-dee83a3f2659-global/readme/terraform/terraform/7265035e-c57d-41f4-b804-4e495ad4c4b7-global).

    |Parameter	|Description|
    |-----|-----|
    |APP-IMAGE	|APP VSI OS image|
    |APP-PROFILE	|APP VSI profile|
    |DB-IMAGE	|DB VSI OS image|
    |DB-PROFILE	|DB VSI profile|
    |hana_components	|hana_components|
    |hana_sid	|hana_sid|
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
    |sap_ascs_instance_number	|sap_ascs_instance_number|
    |sap_ci_instance_number	|sap_ci_instance_number|
    |sap_sid	|sap_sid|

5.	Accept the license agreement. 
6.  Select **Install**. The deployment starts and you are directed to the Schematics page that displays the script log files for you to monitor the deployment progress.

## Deploying SAP S/4HANA with the Schematics interface
{: #s4hana-automation-procedure-schematics-ui}
{: ui}

Use these steps to configure the SAP S/4HANA on your existing VPC by using the Schematics interface. The scripts can take 1 - 2 hours to complete. The supported versions available for S/4HANA are 2020, 2021, 2022, and 2023. For more information, see the [readme](https://cloud.ibm.com/catalog/content/content-ibm-sap-vpc-automation-s4hana-ec60f4ee-c27d-4bcb-8aef-dee83a3f2659-global/readme/terraform/terraform/dc5f4da1-ff39-45b9-b78b-2123936bc21f-global) for recommended kits versions.

1.	From the IBM Cloud menu, select [Schematics](https://cloud.ibm.com/schematics/overview).
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
     * The ID for the SSH key that you created and uploaded to {{site.data.keyword.cloud_notm}}. Enter the SSH key ID in square brackets and quotation marks, for example [ "ibmcloud_ssh_key_UUID1","ibmcloud_ssh_key_UUID2",... ].
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

     For a more detailed description of each of the parameters, check the GitHub repo readme file, chapter “Input parameter file”. Also, make sure to mark the parameters that contain sensitive information like passwords, API, and ssh private keys as "sensitive". These parameters are marked as “sensitive” in the readme file, under “Input parameter file”.

7.	On the workspace Settings page, click **Generate plan**. Wait for the plan to complete.
8.	Click **View log** to review the log files of your Terraform execution plan.
9.	Apply your Terraform template by clicking **Apply plan**.
10.	Review the log file to ensure that no errors occurred during the provisioning, modification, or deletion process.

## Related information

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [*Getting started with Terraform on IBM Cloud*](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started) tutorial.

For more information about using Terraform for creating only a VPC for SAP, without the SAP architecture, see [Creating single-tier virtual private cloud for SAP by using Terraform](/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).


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

This automation is offered at no cost; however, the provisioned infrastructure comes at cost.
{: note}