---

copyright:
  years: 2021, 2024
lastupdated: "2024-12-11"

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

# Deploying SAP NetWeaver 7.x and Db2 on an existing {{site.data.keyword.cloud}} VPC (Terraform and Ansible)
{: #sap-terraform-nw-db2-existing-vpc}

Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) infrastructure resources so that you can rapidly build complex, cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware that uses Intel Xeon CPUs and additional Intel technologies.
{: shortdesc}

You can use Terraform scripts to create a single-tier VPC and create the SAP and Db2 infrastructure on the VPC. The Terraform scripts use the VPC information that you provide and then call the Ansible playbook to create the SAP architecture on the specified VPC.

You have three deployment methods to choose from:

*   Terraform scripts run from the CLI on your bastion server
*   Catalog Tile user interface accessed from the {{site.data.keyword.cloud_notm}} Catalog
*   Schematics user interface accessed from the menu on your cloud dashboard.

## What is created
{: #sap-terraform-nw-db2-existing-vpc-components}

The scripts use the information that you provide for an existing VPC and deploy NW7.X with Db2. For more information about this architecture, see [SAP NetWeaver 7.x on UNIX with Db2 on {{site.data.keyword.cloud}} VPC](/docs/sap?topic=sap-sap-refarch-nw-db2). You specify the information for the VPC to use in the `input.auto.tfvars` file.

The scripts call the Ansible Playbook to install the SAP architecture.

## Script files
{: #terraform-nw-db2-existing-vpc-files}

The configuration and script files are provided on GitHub. Each supported interface for the SAP solution installation has its own folder in the GitHub repository:

*  Using the Schematics user interface on {{site.data.keyword.cloud}} - [GitHub repository for IBM Schematics](https://github.com/IBM-Cloud/sap-netweaver-abap-db2-standard/tree/main){: external}.

## Terraform deployment
{: #sap-terraform-nw-db2-terraform}
{: terraform}

You use Terraform on the Bastion server CLI to download and run the scripts that are located in [SAP NetWeaver ABAP Db2 GitHub repository for Terraform](https://github.com/IBM-Cloud/sap-netweaver-abap-db2-standard){: external}.

To run the Terraform scripts, you modify:

*	The `input.auto.tfvars` file to specify the information for your solution:
    *   Enter the floating IP and subnet information from the Bastion server.
    *   Enter existing VPC information:
        *   VPC name
        *   Security group
        *   Subnet
        *   HostName
        *   Profile
        *   Image
        *   Up to two SSH keys

    *   You can change the default SAP system configuration settings to match your solution.
    *   You also specify the location where you downloaded the SAP kits.

The {{site.data.keyword.cloud_notm}} Provider plug-in for Terraform on {{site.data.keyword.cloud_notm}} uses these configuration files to install NW7.X with Db2 on the specified VPC in your {{site.data.keyword.cloud_notm}} account.

## Schematics deployment
{: #sap-terraform-nw-db2-schematics}
{: ui}

When you run the scripts with the Schematics interface, you:

*   Enter Workspace information.
*   Enter the GitHub path.
*   Modify the parameters in the Schematics interface. They are the same parameters as the `input.auto.tfvars` file that you use with the cli.

## Catalog Tile deployment
{: #nw-db2-abap-automation-catalog-tile}
{: ui}

When you use the Catalog Tile for deployment, you:

*   Select the **SAP NetWeaver (ABAP stack) with Db2 standard system** tile from the catalog
*   Enter information for your workspace. The Catalog Tile creates a Schematics workspace for you.
*   Modify the parameters for your bastion server, personal credential information, and other parameters specific to your solution.


## Support - Terraform and Schematics
{: #terraform-nw-db2-support-existing-vpc}

There are no warranties of any kind, and there is no service or technical support available for these materials from {{site.data.keyword.IBM}}. As a recommended practice, review carefully any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, re-adapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Support - Catalog Tile
{: #terraform-nw-db2-support-catalog-existing-vpc}
{: ui}

The Catalog Tile offering is {{site.data.keyword.cloud_notm}} supported. For more information, see [Getting help and support from IBM Cloud or SAP](/docs/sap?topic=sap-help-support&interface=ui).

If client issues are identified with SAP software, then SAP Support will assist client. Please follow the recommendations of SAP Note 90835, which describes the SAP Incident Escalation Procedure. This SAP Note (and others) is found at https://support.sap.com/en/index.html

## Before you begin
{: #terraform-nw-db2-ansible-before}

Before you use the scripts in the Bastion cli or Schematics:

*  Set up your account to access the VPC. Make sure that your account is [upgraded to a paid account](/docs/account?topic=account-accountfaqs#changeacct).

*  If you have not already, create a bastion server to store the SAP kits. For more information, see [Automate SAP bastion server - SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server). You need the floating IP from your bastion server for deployment.

*  Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. Ansible decompresses the files. For more information, see the readme file, in the respective GitHub repository for Schematics and Terraform and on the About page for the Catalog Tile.

*  [Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey#create_user_key&interface=ui). The API key is used to authenticate with the {{site.data.keyword.cloud_notm}} platform and to determine your permissions for {{site.data.keyword.cloud_notm}} services.

*  [Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.

* Terraform should be already installed on the bastion server that you deployed. For more information, see [Bastion server for SAP deployment](/docs/sap?topic=sap-sap-bastion-server). {: terraform}

* (Optional - Catalog Tile) create secrets for your credentials and passwords by using the [Secrets Manager](/docs/secrets-manager?topic=secrets-manager-arbitrary-secrets&interface=ui).


## Deploying SAP NetWeaver 7.x and Db2 by using Terraform with the Bastion server CLI
{: #create-vpc-terraform}
{: terraform}

Use these steps to configure the IBM Cloud Provider plug-in and use Terraform to install SAP NW7.X with Db2 on your existing VPC. The scripts can take 1 - 2 hours to complete.

1.	Access the Bastion server cli.
2.	Clone the solution repository from `https://github.com/IBM-Cloud/sap-netweaver-abap-db2-standard` and cd to the sap-netweaver-abap-db2-standard folder:

    ```sh
    $ git clone https://github.com/IBM-Cloud/sap-netweaver-abap-db2-standard.git
    $ cd sap-netweaver-abap-db2-standard
    ```

3.	Specify your VPC. Modify the `input.auto.tfvars` file to specify the information for the existing VPC, your zone, VPC and component names, profile, and image. You need your 40-digit SSH key ID for this file. The second SSH key is optional. For more options for profile, see [Instance Profiles](/docs/vpc?topic=vpc-profiles). For more options for image, see [Images](/docs/vpc?topic=vpc-about-images). For descriptions of the variables, see the [`README` file](https://github.com/IBM-Cloud/sap-netweaver-abap-db2-standard/tree/main#readme){: external}.

    ```terraform
    #Infra VPC variables
    REGION = "eu-de"
    ZONE = "eu-de-2"
    VPC = "ex-vpc"
    SECURITY_GROUP = "bastion-sg-ex-bastion"
    RESOURCE_GROUP = "exres-group"
    SUBNET = "ex-subnet"
    SSH_KEYS = [ "r018-6c00506b-6be1-4395-b02e-ab8b052e2a6a" , "r018-6c00506b-6be1-4395-b02e-bb8b052e2a6b" ]
    HOSTNAME = "exdb2nwss1"
    PROFILE = "bx2-4x16"
    IMAGE = "ibm-redhat-8-4-amd64-sap-applications-4"
    ```

4.  Customize your SAP system configuration. In the same file, `input.auto.tfvars`, edit the SAP system configuration variables that are passed to the Ansible automated deployment.

    For descriptions of the variables, see the [`README` file](https://github.com/IBM-Cloud/sap-netweaver-abap-db2-standard/tree/main#readme){: external}.

    ```terraform
    ##SAP system configuration
    sap_sid	= "DB2"
    sap_ci_instance_number = "00"
    sap_ascs_instance_number = "01"

    #Kits paths
    kit_sapcar_file = "/storage/NW75DB2/SAPCAR_1010-70006178.EXE"
    kit_swpm_file =  "/storage/NW75DB2/SWPM10SP37_2-20009701.SAR"
    kit_saphotagent_file = "/storage/NW75DB2/SAPHOSTAGENT51_51-20009394.SAR"
    kit_sapexe_file = "/storage/NW75DB2/SAPEXE_800-80002573.SAR"
    kit_sapexedb_file = "/storage/NW75DB2/SAPEXEDB_800-80002603.SAR"
    kit_igsexe_file = "/storage/NW75DB2/igsexe_13-80003187.sar"
    kit_igshelper_file = "/storage/NW75DB2/igshelper_17-10010245.sar"
    kit_export_dir = "/storage/NW75DB2/51050829"
    kit_db2_dir = "/storage/NW75DB2/51055138/DB2_FOR_LUW_11.5_MP6_FP0SAP2_LINUX_"
    kit_db2client_dir = "/storage/NW75DB2/51055140"
    ```

    Remember, you must manually decompress the `kit_export_dir`, `kit_db2_dir`, and `kit_db2client_dir` files. Ansible decompresses the rest of the SAP kit files. For more information, see the [`README` file](https://github.com/IBM-Cloud/sap-netweaver-abap-db2-standard/tree/main#readme){: external}.
    {: note}

5.  Initialize the Terraform CLI.

    ```terraform
     terraform init
     ```

6.  Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account.

    ```terraform
     terraform plan --out plan1
     ```
    You must enter an SAP main password and your API key.

    The SAP main password must be 10 - 14 characters long and contain at least one digit (0-9). It can contain only the following characters: a-z, A-Z, 0-9, @, #, $, _. This password cannot contain `!`. It must not start with a digit or an underscore ( _ ).

7.  Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the ``input.auto.tfvars`` file to correct resources and run ``terraform plan`` again.

8.  Create the virtual private cloud for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.

        ```teraform
         terraform apply "plan1"
         ```

        The virtual private cloud and components are created and you see output similar to the `terraform plan` output.

9. Create the virtual private cloud for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.

10. Add the SAP credentials and the virtual server instance IP to the SAP GUI. For more information about the SAP GUI, see [SAP GUI](https://help.sap.com/doc/7abd5470728810148a4b1a83b0e91070/1511%20000/en-US/frameset.htm){: external}.


## Deploying SAP NetWeaver 7.x and Db2 by using the Catalog Tile user interface
{: #create-vpc-catalog-tile-ui}
{: ui}

Use these steps to configure the SAP NetWeaver 7.X with Db2 on your existing VPC by using the Catalog Tile user interface. The scripts can take 1 - 2 hours to complete.


1.	From the {{site.data.keyword.cloud_notm}} Catalog, select the **SAP NetWeaver (ABAP stack) with Db2 standard system** tile. The Tile opens the Create tab for AP NetWeaver (ABAP stack) with Db2 standard system. For more information about this deployment, see the About tab or the Readme file link.
2.	On the SAP NetWeaver (ABAP stack) with Db2 standard system page, configure your workspace:
    * Enter a name for the workspace or use the default.
    * The Resource Group to use to create resources. Use the  Default or create a Resource Group.
    * Select a **Location** to create your Schematics workspace. The workspace location does not have to match the resource location.
3.  Enter the required deployment values, review the default input variables, and provide values that match your solution. These parameters are specific to your deployment.  For more detailed information, see the [Readme file](https://cloud.ibm.com/catalog/content/sapsingletierdb2-20099bf4-f33a-4ce6-a48b-6d8cb1ac7a27-global/readme/terraform/terraform/e4ec2c13-cf6d-435a-8513-55d6232d5283-global){: external}.

    |Parameter	|Description|
    |-----|-----|
    |BASTION_FLOATING_IP	|Input the floating IP of the Bastion Server you created before you started this deployment. For more information, see [Automate SAP bastion server - SAP media storage](/docs/sap?topic=sap-sap-bastion-server)|
    |HOSTNAME	|VSI hostname|
    |REGION	|Cloud Region where resources are deployed |
    |RESOURCE_GROUP	|EXISTING Resource Group for VSIs and Volumes |
    |SECURITY_GROUP	|EXISTING Security group name |
    |SSH_KEYS	|SSH Keys ID list to access the VSI|
    |SUBNET	|EXISTING Subnet name |
    |VPC	|EXISTING VPC name |
    |ZONE	|Cloud Zone where resources are deployed
    |ibmcloud_api_key	|IBM Cloud API key or use a secret that is stored in Secrets Manager|
    |private_ssh_key	|Input id_rsa private key content or use a secret that is stored in Secrets Manager|
    |sap_main_password	|SAP main password or use a secret that is stored in Secrets Manager |
    {: caption}

4.  Review and update the optional parameters. The Ansible scripts expect the SAP kits to be in the default locations listed. For more detailed information, see the [Readme file](https://cloud.ibm.com/catalog/content/sapsingletierdb2-20099bf4-f33a-4ce6-a48b-6d8cb1ac7a27-global/readme/terraform/terraform/e4ec2c13-cf6d-435a-8513-55d6232d5283-global){: external}.

    |Parameter	|Description|
    |-----|-----|
    |IMAGE	|VSI OS image|
    |PROFILE	|VSI profile|
    |kit_db2_dir	|kit_db2_dir|
    |kit_db2client_dir	|kit_db2client_dir|
    |kit_export_dir	|kit_export_dir|
    |kit_igsexe_file |kit_igsexe_file|
    |kit_igshelper_file |kit_igshelper_file|
    |kit_sapcar_file	|kit_sapcar_file|
    |kit_sapexe_file	|kit_sapexe_file|
    |kit_saphotagent_file	|kit_saphotagent_file|
    |kit_swpm_file	|kit_swpm_file|
    {: caption}

5.	Accept the license agreement.
6.  Select **Install**. The deployment starts and you are directed to the Schematics page that displays the script log files for you to monitor the deployment progress.


## Deploying SAP NetWeaver 7.x and Db2 by using the Schematics user interface
{: #create-vpc-schematics-ui}
{: ui}

Use these steps to configure the SAP NetWeaver 7.X with Db2 on your existing VPC by using the Schematics user interface. The scripts can take 1 - 2 hours to complete.

1.	From the {{site.data.keyword.cloud_notm}} menu, select [Schematics](https://cloud.ibm.com/schematics/overview){: external}.
2.	Click **Create workspace**.
3.  On the **Specify template** page:
    * Enter the GitHub URL with the code you plan to deploy.
    * Select the **Terraform version** that is listed in the readme file.
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
     * (Optional) you can change the ID_RSA_FILE_PATH for your SSH key that will be autogenerated on Schematics and Bastion Server
     * The ID for the SSH key that you created and uploaded to {{site.data.keyword.cloud_notm}}. Enter the SSH key ID in square brackets and quotation marks, for example [ "ibmcloud_ssh_key_UUID1","ibmcloud_ssh_key_UUID2",... ].
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
     * SAP main password - must be at least 10 characters, upper and lowercase letters, a number, and a special character, not an exclamation point.
     * Click **Save changes**.

     For a more detailed description of each of the parameters, check the GitHub repo readme file, chapter “Input parameter file”. Also, make sure to mark as “sensitive” the parameters that contain sensitive information like passwords, API, and ssh private keys (they are marked as “sensitive” in the readme file, under “Input parameter file”)

7.	On the workspace Settings page, click **Generate plan**. Wait for the plan to complete.
8.	Click **View log** to review the log files of your Terraform execution plan.
9.	Apply your Terraform template by clicking **Apply plan**.
10.	Review the log file to ensure that no errors occurred during the provisioning, modification, or deletion process.

## Next steps
{: #next-steps-terraform}
{: terraform}

If you need to rename your resources after they are created, modify the ``input.auto.tfvars`` file to change the names and run ``terraform plan`` and ``terraform apply`` again. Do not use the {{site.data.keyword.cloud_notm}} Dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results.

If you need to remove the SAP NetWeaver 7.X and Db2 installation, go to your project folder and run ``terraform destroy``. The `terraform destroy` command does not remove the VPC in this scenario because the VPC was not created with the Terraform scripts.

## Related information

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Terraform on {{site.data.keyword.cloud_notm}} getting started tutorial](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

For more information about using Terraform for creating only a VPC for SAP, without the SAP architecture, see [Creating single-tier VPC for SAP on {{site.data.keyword.cloud}} VPC with Terraform](/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).

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
