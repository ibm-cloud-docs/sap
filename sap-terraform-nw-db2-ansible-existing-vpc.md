---

copyright:
  years: 2021, 2022
lastupdated: "2022-03-17"

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
{:cli: .ph data-hd-interface="cli"}

# Deploy NW7.X and Db2 on an existing {{site.data.keyword.cloud_notm}} VPC by using Terraform and Ansible
{: #sap-terraform-nw-db2-existing-vpc}

Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} VPC infrastructure resources so that you can rapidly build complex, cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware that uses Intel Xeon CPUs and additional Intel technologies.
{: shortdesc}

You can use Terraform scripts to create a single-tier Virtual Private Cloud (VPC) and create the SAP and Db2 infrastructure on the VPC. The Terraform scripts use the VPC information that you provide and then call the Ansible playbook to create the SAP architecture on the specified VPC. 

## What is created
{: #sap-terraform-nw-db2-existing-vpc-components}

The scripts use the information that you provide for an existing VPC and deploy NW7.X with Db2. For more information about this architecture, see [SAP NetWeaver 7.x on UNIX with Db2 on VPC IBM Cloud](/docs/sap?topic=sap-sap-refarch-nw-db2). You specify the information for the VPC to use in the `input.auto.tfvars` file. 

The scripts call the Ansible Playbook to install the SAP architecture. 

## Script files
{: #terraform-nw-db2-existing-vpc-files}

The configuration and script files are provided on GitHub. There are usually 2 repositories for each of the SAP solution:

*  Using the Bastion server CLI - [GitHub repository `sap-automated-deployment-scripts/sapsingletierdb2/`](https://github.com/IBM-Cloud/sap-automated-deployment-scripts/tree/master/sapsingletierdb2).
*  Using Schematics uiser interface on {{site.data.keyword.cloud}} - [GitHub repository sap-automated-deployment-scripts/sapsingletierdb2/ibm-schematics](https://github.com/IBM-Cloud/sap-automated-deployment-scripts/tree/master/sapsingletierdb2/ibm-schematics).

### Terraform CLI interface
{: #sap-terraform-nw-db2-cli}
{: cli}

To run the scripts, you modify:

*	The `terraform.tfvars` file to add your IBM Cloud API-key
*	The `input.auto.tfvars` file to specify the existing VPC resources for your solution. You specify the variables for the existing VPC:
    - VPC name
    - Security group
    - Subnet
    - HostName
    - Profile
    - Image
    - Up to two SSH keys  
    - Five storage volumes. 
  
    You can change the default SAP system configuration settings to match your solution. You also specify the location where you downloaded the SAP kits.
    
*  The `main.tf` file to tell the scripts to not create the VPC. You comment out the part of the files that creates the VPC and modify the volumes module to exclude the creation of the VPC.

All of the other configuration files are provided and do not need to be modified. 

The {{site.data.keyword.cloud_notm}} Provider plug-in for Terraform on {{site.data.keyword.cloud_notm}} uses these configuration files to install NW7.X with Db2 on the specified VPC in your {{site.data.keyword.cloud_notm}} account. 

### Schematics user interface
{: #sap-terraform-nw-db2-schematics}
{: ui}

When you run the scripts with the Schematics interface, you:
*  Enter the URL for the GitHub repository for the Terraform files
*  Modify the parameters in the Schematics interface. They are the same parameters as the `input.auto.tfvars` file that you use with the cli. 

## Support
{: #terraform-nw-db2-support-existing-vpc}
 
There are no warranties of any kind, and there is no service or technical support available for these materials from {{site.data.keyword.IBM}}. As a recommended practice, review carefully any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, re-adapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Before you begin
{: #terraform-nw-db2-ansible-before}

Before you use the scripts in the cli or Schematics:

*  Set up your account to access the VPC. Make sure that your account is [upgraded to a paid account](/docs/account?topic=account-accountfaqs#changeacct). 

*  If you have not already, create a bastion server to store the SAP kits. For more information, see [Automate SAP bastion server - SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server).

*  Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. Ansible decompresses the files. For more information, see the `README` [file](https://github.com/IBM-Cloud/sap-automated-deployment-scripts/tree/master/sapsingletierdb2). 

*  [Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey#create_user_key). The API key is used to authenticate with the IBM Cloud platform and to determine your permissions for IBM Cloud services.

*  [Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.

* Terraform should be already installed on the bastion server that you deployed. For more information, see [Bastion server for SAP deployment](/docs/sap?topic=sap-sap-bastion-server). {: cli}

## Creating the VPC by using the CLI
{: #create-vpc-cli}
{: cli}

Use these steps to configure the {{site.data.keyword.cloud_notm}} Provider plug-in and use Terraform to install SAP NW7.X with Db2 on your existing VPC. The scripts can take 1 - 2 hours to complete. 

1.  Access the Bastion server cli.

2.  Clone the `terraform` and `ansbile` folders and `README` file from `sap-automated-deployment-scripts/sapsingletierdb2/` and `cd` to the `sap-automated-deployment-scripts/sapsingletierdb2/terraform` folder.

    ```terraform
    $ git clone https://github.com/IBM-Cloud/sap-automated-deployment-scripts.git
    
    $ cd sap-automated-deployment-scripts/sapsingletierdb2/terraform
    ```

3.  Edit the ``terraform.tfvars`` variable file and enter the IBM Cloud API key that you retrieved. 

    ```ibmcloud_api_key = "<ibmcloud_apikey>"```

    Variables that are defined in the `terraform.tfvars` file are automatically loaded by Terraform when the {{site.data.keyword.cloud_notm}} Provider plug-in is initialized and you can reference them in every Terraform configuration file that you use.  

    Because the `terraform.tfvars` file contains confidential information, do not push this file to a version control system. Keep this file on your local system only. 
  
4.	Specify your VPC. Modify the `input.auto.tfvars` file to specify the information for the existing VPC, your zone, VPC and component names, profile, and image. You need your 40-digit SSH key ID for this file. The second SSH key is optional. For more options for profile, see [Instance Profiles](/docs/vpc?topic=vpc-profiles). For more options for image, see [Images](/docs/vpc?topic=vpc-about-images). For descriptions of the variables, see the `README`[file](https://github.com/IBM-Cloud/sap-automated-deployment-scripts/tree/master/sapsingletierdb2).

    ```terraform
    #Infra VPC variables
    ZONE			= "eu-de-2"
    VPC			= "sap"
    SECURITYGROUP		= "sap-securitygroup"
    SUBNET			= "sap-subnet"
    HOSTNAME		= "db2saps1"
    PROFILE			= "bx2-4x16"
    IMAGE			= "ibm-redhat-7-6-amd64-sap-applications-1"
    SSH_KEYS		= [ "r010-57bfc315-f9e5-46bf-bf61-d87a24a9ce7a" , "r010-3fcd9fe7-d4a7-41ce-8bb3-d96e936b2c7e" ]
    VOL1			= "32"
    VOL2			= "32"
    VOL3			= "64"
    VOL4			= "128"
    VOL5			= "256"
    ```

5.  Customize your SAP system configuration. In the same file, `input.auto.tfvars`, edit the SAP system configuration variables that are passed to the Ansible automated deployment.

    For descriptions of the variables, see the `README` [file](https://github.com/IBM-Cloud/sap-automated-deployment-scripts/tree/master/sapsingletierdb2). 

    ```terraform
    ##SAP system configuration
    sap_sid	= "NWS"
    sap_ci_instance_number = "00"
    sap_ascs_instance_number = "01"

    #Kits paths
    kit_sapcar_file = "/storage/NW75DB2/SAPCAR_1010-70006178.EXE"
    kit_swpm_file =  "/storage/NW75DB2/SWPM10SP31_7-20009701.SAR"
    kit_saphostagent_file = "/storage/NW75DB2/SAPHOSTAGENT51_51-20009394.SAR"
    kit_sapexe_file = "/storage/NW75DB2/SAPEXE_800-80002573.SAR"
    kit_sapexedb_file = "/storage/NW75DB2/SAPEXEDB_800-80002603.SAR"
    kit_igsexe_file = "/storage/NW75DB2/igsexe_13-80003187.sar"
    kit_igshelper_file = "/storage/NW75DB2/igshelper_17-10010245.sar"
    kit_export_dir = "/storage/NW75DB2/51050829"
    kit_db2_dir = "/storage/NW75DB2/51051007/DB2_FOR_LUW_10.5_FP7SAP2_LINUX_"
    kit_db2client_dir = "/storage/NW75DB2/51051049"
    ```

    Remember, you must manually decompress the `kit_export_dir`, `kit_db2_dir`, and `kit_db2client_dir` files. Ansible decompresses the rest of the SAP kit files. For more information, see the `README` [file](https://github.com/IBM-Cloud/sap-automated-deployment-scripts/tree/master/sapsingletierdb2). 
    {: note}
    
6.  Configure the scripts to use your existing VPC. Modify the `main.tf` file to comment out the `vpc` module and remove the `module.vpc` from the `depends_on` statement in the `vsi` module. When you are done, the file looks like this example:

    ```terraform
    /*module "vpc" {
      source		= "./modules/vpc"
      ZONE			= var.ZONE
      VPC			= var.VPC
      SECURITYGROUP 	= var.SECURITYGROUP
      SUBNET		= var.SUBNET
    }
    */
    module "volumes" {
      source		= "./modules/volumes"
      ZONE			= var.ZONE
      HOSTNAME		= var.HOSTNAME
      VOL1			= var.VOL1
      VOL2			= var.VOL2
      VOL3			= var.VOL3
      VOL4			= var.VOL4
      VOL5			= var.VOL5
    }
    module "vsi" {
      source		= "./modules/vsi"
      depends_on		= [  module.volumes ]
      ZONE			= var.ZONE
      VPC			= var.VPC
      SECURITYGROUP 	= var.SECURITYGROUP
      SUBNET		= var.SUBNET
      HOSTNAME		= var.HOSTNAME
      PROFILE		= var.PROFILE
      IMAGE			= var.IMAGE
      SSH_KEYS		= var.SSH_KEYS
      VOLUMES_LIST		= module.volumes.volumes_list
      SAP_SID		= var.sap_sid
    }
    ```

    If you do not comment out the `vpc` module, the scripts try to create the VPC that you specified in the input.auto.tfvars file and you get an error. 
    {: note}

7.  Initialize the Terraform CLI. 

    ```terraform
     terraform init
     ```

8.  Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account.      

    ```terraform
     terraform plan
     ```
    You must enter an SAP master password.

    The SAP master password must be 10 to 14 characters long and contain at least one digit (0-9). It can contain only the following characters: a-z, A-Z, 0-9, @, #, $, _. This password cannot contain `!`. It must not start with a digit or an underscore ( _ ).

9.  Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the ``input.auto.tfvars`` file to correct resources and run ``terraform plan`` again.

10.  Create the virtual private cloud for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.

        ```teraform
         terraform apply
         ```
        The virtual private cloud and components are created and you see output similar to the `terraform plan` output.  

11. Create the virtual private cloud for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.

12. Add the SAP credentials and the virtual server instance IP to the SAP GUI. For more information about the SAP GUI, see [SAP GUI](https://help.sap.com/doc/7abd5470728810148a4b1a83b0e91070/1511%20000/en-US/frameset.htm).

## Creating the VPC with Schematics user interface
{: #create-vpc-schematics-ui}
{: ui}

Use these steps to configure the  SAP NW7.X with Db2 on your existing VPC by using the Schematics user interface. The scripts can take 1 - 2 hours to complete. 


1.	From the IBM Cloud menu, select [Schematics](https://cloud.ibm.com/schematics/overview).
2.	Click **Create workspace**.
3.  On the **Specify template** page:
    * Enter the URL of bastion setup folder. 
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
     * The ID for the SSH key that you created and uploaded to IBM Cloud. Enter the SSH key Id in square brackets and quotes, for example [ "ibmcloud_ssh_key_UUID1","ibmcloud_ssh_key_UUID2",... ].
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
     * SAP master password -must be at least 10 characters, upper and lower case letters, a number, and a special character, not an exclamation point. 
     * Click **Save changes**.

     For a more detailed description of each of the parameters, check the github repo README file, chapter “Input parameter file”. Also make sure to mark as “sensitive” the parameters that contain sensitive information like passwords, API and ssh private keys (they are marked as “sensitive” in the README file, under “Input parameter file”)

7.	On the workspace Settings page, click **Generate plan**. Wait for the plan to complete.
8.	Click **View log** to review the log files of your Terraform execution plan.
9.	Apply your Terraform template by clicking **Apply plan**.
10.	Review the log file to ensure that no errors occurred during the provisioning, modification, or deletion process.

## Next steps
{: #next-steps-cli}
{: cli}

If you need to rename your resources after they are created, modify the ``input.auto.tfvars`` file to change the names and run ``terraform plan`` and ``terraform apply`` again. Do not use the {{site.data.keyword.cloud_notm}} Dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results. 

If you need to remove the NW7.X and Db2 installation, go to your project folder and run ``terraform destroy``. The `terraform destroy` command does not remove the VPC in this scenario because the VPC was not created with the Terraform scripts.

## Related information 

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Terraform on {{site.data.keyword.cloud_notm}} getting started tutorial](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

For more information about using Terraform for creating only a VPC for SAP, without the SAP architecture, see [Creating single-tier virtual private cloud for SAP by using Terraform](/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).

SAP One Support Notes that apply to this document:
*	[SAP Note 84555 Windows Server, Linux&reg;, and UNIX: Certified hardware](https://launchpad.support.sap.com/#/notes/84855)
*	[SAP Note 2927211 SAP Applications on IBM Virtual Private Cloud: Supported DB/OS and IBM Gen 2 Virtual Server Instances (VSI)](https://launchpad.support.sap.com/#/notes/2927211)
*	[SAP Note 2923773 Linux&reg; on IBM Cloud (IaaS): Adaption of your SAP License](https://launchpad.support.sap.com/#/notes/2923773)
*	[SAP Note 2414097 SAP Applications on IBM Cloud: Supported DB/OS and IBM Cloud Bare Metal Server Types](https://launchpad.support.sap.com/#/notes/2414097)
*	[SAP Note 2369910 SAP Software on Linux&reg;: General information](https://launchpad.support.sap.com/#/notes/2369910)
*	[SAP Note 171380 Released IBM hardware (Intel processors) and IBM cloud services offers](https://launchpad.support.sap.com/#/notes/171380)
*	[SAP Note 1380654 SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)

This document is referenced by:
*	[SAP Note 2927211 SAP Applications on IBM Virtual Private Cloud: Supported DB/OS and IBM Gen 2 Virtual Server Instances (VSI)](https://launchpad.support.sap.com/#/notes/2927211)
*	[SAP Note 2588225 How to protect against speculative execution vulnerabilities on IBM Cloud?](https://launchpad.support.sap.com/#/notes/2588225)
*	[SAP Note 1380654 SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)]
*	[SAP Note 2414097 SAP Applications on IBM Cloud: Supported DB/OS and IBM Cloud Bare Metal Server Types](https://launchpad.support.sap.com/#/notes/2414097)
