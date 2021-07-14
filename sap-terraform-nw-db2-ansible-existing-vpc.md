---

copyright:
  years: 2021
lastupdated: "2021-07-14"

subcollection: sap

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}

# Deploy NW7.X and Db2 on an existing {{site.data.keyword.cloud_notm}} VPC by using Terraform and Ansible
{: #sap-terraform-nw-db2-existing-vpc}

You can use Terraform scripts to create a single-tier Virtual Private Cloud (VPC) and create the SAP and Db2 infrastructure on the VPC. The Terraform scripts use the VPC information that you provide and then call the Ansible playbook to create the SAP architecture on the specified VPC. 
{: shortdesc}

## What is created
{: #sap-terraform-nw-db2-existing-vpc-components}

The scripts use the information that you provide for an existing VPC and deploy NW7.X with Db2. For more information about this architecture, see [SAP NetWeaver 7.x on UNIX with Db2 on VPC IBM Cloud](/docs/sap?topic=sap-sap-refarch-nw-db2). You specify the information for the VPC to use in the `input.auto.tfvars` file. 

The scripts call the Ansible Playbook to install the SAP architecture. 

If you want to create a new VPC and install SAP all in one task flow, see [Creating a single-tier Virtual Private Cloud (VPC) with NW7.X and Db2 on IBM Cloud by using Terraform and Ansible](/docs/sap?topic=sap-create-terraform-nw-db2-vpc-ansible).  

## Script files
{: #terraform-nw-db2-existing-vpc-files}

The configuration and script files are provided on the [GitHub repository `sap-automated-deployment-scripts/sapsingletierdb2/`](https://github.com/IBM-Cloud/sap-automated-deployment-scripts/tree/master/sapsingletierdb2)

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

## SAP Kits
{: #terraform-nw-db2-existing-vpc-sap-kits}

For each {{site.data.keyword.cloud_notm}} region {{site.data.keyword.IBM_notm}} allocates temporary storage on a dedicated Jump host. It is your responsibility to download the necessary SAP and DB kits to your local workstation. There are three files that you must manually decompress, the other files are decompressed by Ansible. For more information, see the `README` [file](https://github.com/IBM-Cloud/sap-automated-deployment-scripts/tree/master/sapsingletierdb2). 

## Support
{: #terraform-nw-db2-support-existing-vpc}
 
There are no warranties of any kind, and there is no service or technical support available for these materials from {{site.data.keyword.IBM}}. As a recommended practice, review carefully any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, re-adapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Before you begin
{: #terraform-nw-db2-ansible-before}

Before you use the scripts:

*  Set up your account to access the VPC. Make sure that your account is [upgraded to a paid account](/docs/account?topic=account-accountfaqs#changeacct). 

*  Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. You must decompress the `kit_export_dir`, `kit_db2_dir`, and `kit_db2client_dir` files. Ansible decompresses the rest of the files. For more information, see the `README` [file](https://github.com/IBM-Cloud/sap-automated-deployment-scripts/tree/master/sapsingletierdb2). 

*  [Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey#create_user_key). The API key is used to authenticate with the IBM Cloud platform and to determine your permissions for IBM Cloud services.

*  [Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.

* If Terraform is not already installed on your workstation, install Terraform v0.15.1 on your local workstation. For more information, see [Install the Terraform CLI and the {{site.data.keyword.cloud_notm}} Provider plug-in](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-setup_cli).  

    If you are using Terraform 0.13 and higher, you do not need to install the {{site.data.keyword.cloud_notm}} Provider plug-in. You modify the configuration files provided on the 1-Tier VPC for SAP GitHub repository to specify the plug-in version to use. 

    If you are using Terraform 1.12.x and earlier, follow these {{site.data.keyword.cloud_notm}} Provider plug-in [installation instructions](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-setup_cli#install-provider-v12). Do not configure the plug-in.

    Do not do any {{site.data.keyword.cloud_notm}} Provider plug-in configuration because those files are provided for you.  

* If Ansible is not already installed on your workstation, install Ansible 2.9.18 on your local workstation. For more information, see [Installing Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html).

## Procedure

Use these steps to configure the {{site.data.keyword.cloud_notm}} Provider plug-in and use Terraform to install SAP NW7.X with Db2 on your existing VPC. The scripts can take 1 - 2 hours to complete. 

1.  Clone the `terraform` and `ansbile` folders and `README` file from `sap-automated-deployment-scripts/sapsingletierdb2/` and `cd` to the `sap-automated-deployment-scripts/sapsingletierdb2/terraform` folder.

    ```
    $ git clone https://github.com/IBM-Cloud/sap-automated-deployment-scripts.git
    
    $ cd sap-automated-deployment-scripts/sapsingletierdb2/terraform
    ```

2.  Edit the ``terraform.tfvars`` variable file and enter the IBM Cloud API key that you retrieved. 

    ```ibmcloud_api_key = "<ibmcloud_apikey>"```

    Variables that are defined in the `terraform.tfvars` file are automatically loaded by Terraform when the {{site.data.keyword.cloud_notm}} Provider plug-in is initialized and you can reference them in every Terraform configuration file that you use.  

    Because the `terraform.tfvars` file contains confidential information, do not push this file to a version control system. Keep this file on your local system only. 
  
3.	Specify your VPC. Modify the `input.auto.tfvars` file to specify the information for the existing VPC, your zone, VPC and component names, profile, and image. You need your 40-digit SSH key ID for this file. The second SSH key is optional. For more options for profile, see [Instance Profiles](/docs/vpc?topic=vpc-profiles). For more options for image, see [Images](/docs/vpc?topic=vpc-about-images). For descriptions of the variables, see the `README`[file](https://github.com/IBM-Cloud/sap-automated-deployment-scripts/tree/master/sapsingletierdb2).

    ```
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

4. Customize your SAP system configuration. Modify the ``input.auto.tfvars`` file to specify SAP system configuration and enter the paths where you downloaded the SAP Kits. For descriptions of the variables, see the `README` [file](https://github.com/IBM-Cloud/sap-automated-deployment-scripts/tree/master/sapsingletierdb2). 

    Remember, you must manually decompress the `kit_export_dir`, `kit_db2_dir`, and `kit_db2client_dir` files. Ansible decompresses the rest of the SAP kit files. For more information, see the `README` [file](https://github.com/IBM-Cloud/sap-automated-deployment-scripts/tree/master/sapsingletierdb2). 
    {: note}

    ```
    ##SAP system configuration
    sap_sid	= "NWS"
    sap_ci_instance_number = "00"
    sap_ascs_instance_number = "01"
    sap_master_password = "Password#1"

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
    
5. Configure the scripts to use your existing VPC.  Modify the `main.tf` file to comment out the `vpc` module and remove the `module.vpc` from the `depends_on` statement in the `vsi` module. When you are done, the file looks like this example:

    If you do not comment out the `vpc` module, the scripts try to create the VPC that you specified in the input.auto.tfvars file and you get an error. 
    {: note}

    ``` 
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
    
6. Initialize the Terraform CLI. 

   ```
   terraform init
   ```

7. Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account.      

   ```
   terraform plan
   ```

8. Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the ``input.auto.tfvars`` file to correct resources and run ``terraform plan`` again.

9. Create the virtual private cloud for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.

    ```
     terraform apply
     ```
     The virtual private cloud and components are created and you see output similar to the `terraform plan` output.  

10. Add the SAP credentials and the virtual server instance IP to the SAP GUI. For more information about the SAP GUI, see [SAP GUI](https://help.sap.com/doc/7abd5470728810148a4b1a83b0e91070/1511%20000/en-US/frameset.htm).

## Next steps

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
