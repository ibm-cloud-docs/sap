---

copyright:
  years: 2021
lastupdated: "2021-11-12"

subcollection: sap

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}

# Automating SAP HANA stand-alone virtual server instance on IBM Cloud by using Terraform
{: #automate-terraform-sap-hana-vsi}

Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} VPC infrastructure resources so that you can rapidly build complex, cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware using Intel Xeon CPUs and additional Intel technologies.
{: shortdesc}

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Terraform on {{site.data.keyword.cloud_notm}} getting started tutorial](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

To create resources with Terraform, you use Terraform configuration files that describe the {{site.data.keyword.cloud_notm}} resources that you need and how you want to configure them. Based on your configuration, Terraform creates an execution plan and describes the actions that need to be run to create the resources. You can review the execution plan, change it, or run the plan. When you change your configuration, Terraform on {{site.data.keyword.cloud_notm}} can determine what changed and create incremental execution plans that you can apply to your existing {{site.data.keyword.cloud_notm}} resources. 

## What is created
{: #terraform-sap-hana-vsi-created}

Terraform on {{site.data.keyword.cloud_notm}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} solutions. For more information about Terraform on {{site.data.keyword.cloud_notm}}, see Terraform on {{site.data.keyword.cloud_notm}} getting started tutorial. Terraform is used to provision of the infrastructure components in {{site.data.keyword.cloud_notm}}.  

To automate this process, the current solution uses a Terraform script to deploy a VPC and a VSI with SAP certified storage and network configuration. The VPC contains one subnet and one security group with three rules:

* Allow all outbound traffic from the VSI
* Allow inbound DNS traffic (UDP port 53
* Allow inbound SSH traffic (TCP port 22)

During configuration you can specify an existing VPC to use, instead of creating a new VPC.

For the detailed steps about using Terraform to create only a VPC for SAP, see [Creating single-tier virtual private cloud for SAP by using Terraform](/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).

## Script files
{: #terraform-sap-hana-vsi-files}

The configuration and script files are provided on the GitHub repository [sap-automated-deployment-scripts/tree/dev/saphanasinglehost](https://github.com/IBM-Cloud/sap-automated-deployment-scripts/tree/dev/saphanasinglehost). 

For SAP HANA stand-alone virtual server instance on {{site.data.keyword.IBM}} virtual private cloud, you modify the:

*  ``terraform.tfvars`` file to add your {{site.data.keyword.cloud_notm}} API-key

*  ``input.auto.tfvars`` file to customize the resources for your solution. You specify zones, resource names, SSH keys, and SAP variables.  

All of the other configuration files are provided and do not need to be modified. 

The IBM Cloud Provider plug-in for Terraform on {{site.data.keyword.cloud_notm}} uses these configuration files to provision a VPC in your {{site.data.keyword.cloud_notm}} account. 

## Support
{: #terraform-sap-hana-vsi-support}

There are no warranties of any kind, and there is no service or technical support available for these materials from {{site.data.keyword.IBM}}. As a recommended practice, review carefully any materials that you download from this site before using them on a live system. 

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, re-adapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Virtual server instance configuration
{: #terraform-sap-hana-vsi-support}

The virtual server instance is configured with:

* Red Hat Enterprise Linux 7.6 for SAP HANA (amd64)
* Two SSH keys configured to access as root user on SSH  
* Three storage volumes as described in the ``input.auto.tfvars`` file. 

## Before you begin

*	Log in to your Deployment Server and verify that Terraform and Ansible are installed. 
*	Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. Ansible decompresses all of the archive kits. For more information, see the README file. 
*  [Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey#create_user_key). The API key is used to authenticate with the IBM Cloud platform and to determine your permissions for IBM Cloud services.

*  [Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.

## Procedure

1.	Log in to the Deployment Server by using `ssh`.
2.	Clone the terraform and ansbile folders and readme file from ``sap-automated-deployment-scripts/saphanasinglehost/`` and change to the ``sap-automated-deployment-scripts/saphanasinglehost/terraform`` folder.

    ```
	$ git clone https://github.com/IBM-Cloud/sap-automated-deployment-scripts.git
    	
	$ cd sap-automated-deployment-scripts/saphanasinglehost/terraform
    ```
3.  Edit the ``terraform.tfvars`` variable file and enter the IBM Cloud API key that you retrieved. 

    ``ibmcloud_api_key = "<ibmcloud_apikey>"``

    Variables that are defined in the `terraform.tfvars` file are automatically loaded by Terraform when the {{site.data.keyword.cloud_notm}} Provider plug-in is initialized and you can reference them in every Terraform configuration file that you use.  

    Because the `terraform.tfvars` file contains confidential information, do not push this file to a version control system. Keep this file on your local system only. 

4.	Edit the input parameter file, `input.auto.tfvars`, and modify the variables to match your solution. The file is preset with the minimal recommended disk sizes. Whether you are creating a VPC or using an existing VPC, you must modify:

    * VPC - New or existing VPC name.
    * SECURITYGROUP - change ic4sap to the VPC name.
    * SUBNET - change ic4sap to the VPC name.
    * HOSTNAME - enter a hostname up to 12 characters.  For more information, see the README file. 

    Add your SSH key 40-digit ID to the SSH_KEYS.

    ```
    #Infra VPC variables
    ZONE			= "eu-de-2"
    VPC				= "ic4sap"
    SECURITYGROUP	= "ic4sap-securitygroup"
    SUBNET			= "ic4sap-subnet"
    HOSTNAME		= "saphanahost"
    PROFILE			= "mx2-16x128"
    IMAGE			= "ibm-redhat-7-6-amd64-sap-hana-1"
    SSH_KEYS		= [ "r010-57bfc315-f9e5-46bf-bf61-d87a24a9ce7a" , "r010-3fcd9fe7-d4a7-41ce-8bb3-d96e936b2c7e" ]
    VOL1			= "500"
    VOL2			= "500"
    VOL3			= "500"
    ```

    The hostname must have up to 13 characters as required by SAP. For more information about the rules that apply to hostnames for SAP systems, see SAP Note 611361 - Hostnames of SAP ABAP Platform servers
    {: note}

5.	In the same file (`input.auto.tfvars`), You must enter a `hana_master_password = ""`.

    The `hana_master_password` must consist of at least one digit (0-9), one lowercase letter (a-z), and one uppercase letter (A-Z). It can only contain the following characters: a-z, A-Z, 0-9, !, @, #, $, _. It must not start with a digit or an underscore ( _ ).

    When you are done creating your resources, the password remains in this file on your system unecrypted. You should remove the password from the file after you are done creating resources.

    When you are done creating your resources, the password remains in the `input.auto.tfvars` file on your system unencrypted and in its generated log file `terraform.tfstate`. You should save these files as they are in a secured archive or remove the values for `hana_master_password` from these 2 files after you are done creating resources. You must preserve these 2 files intact in case you want to destroy the created VPC at a later time.
    {: note}


    See the README file for variable descriptions.

    ```
    #HANA DB configuration
    hana_sid = "HDB"
    hana_sysno = "00"
    hana_master_password = ""
    hana_system_usage = "custom"  # default	value is: "custom"
    hana_components = "server"    # default	value is: "server"
    
    #SAP HANA Installation kit path
    kit_saphana_file = "/storage/HANADB/51054623.ZIP"
    ```

    For each IBM VPC cloud region, IBM allocates temporary storage on a dedicated Jump host (Deployment server). The Jump host is used for any IBM client to be able to download and decompress the necessary SAP and DB kits for their solutions in scope. Then, the kits variables (paths) need to be updated into the dedicated Terraform variables in order for the ansible playbook to identify them correctly during SAP and Db2 automation. 

6.	Edit the `sapsingletierdb2/terraform/main.tf` file and specify the VPC to use. You can have the scripts create a new VPC or you can use an existing VPC. By default the scripts use an existing VPC. If you are using an existing VPC, be sure that the VPC name is in the `input.auto.tfvars` file.

    To create a new VPC, uncomment the `# source = "./modules/vpc"` line and comment out the `source = "./modules/vpc/existing"` line. 
    
    To use an existing VPC, make sure the `source = "./modules/vpc/existing"` line is uncommented and leave the `# source = "./modules/vpc"` line commented out.

    ```terraform
    /*module "vpc" {
    #  source		= "./modules/vpc"
      source		= "./modules/vpc/existing"
      ZONE			= var.ZONE
      VPC			= var.VPC
      SECURITYGROUP 	= var.SECURITYGROUP
      SUBNET		= var.SUBNET
    }
    */
    ```
  
7. Initialize the Terraform CLI. 

    ``terraform init``

8.	Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account.

    ``terraform plan``

9. Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the ``input.auto.tfvars`` file to correct resources and run ``terraform plan`` again.


10. Create the virtual private cloud for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.

    ``terraform apply``

     The virtual private cloud and components are created and you see output similar to the `terraform plan` output.  

## Next steps

If you need to rename your resources after they are created, modify the ``input.auto.tfvars`` file to change the names and run ``terraform plan`` and ``terraform apply`` again. Do not use the {{site.data.keyword.cloud_notm}} Dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results. 

If you need to remove your VPC, go to your project folder and run ``terraform destroy``.

## Related information

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
