---

copyright:
  years: 2021, 2022
lastupdated: "2022-05-23"

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

# Automating BW/4HANA on 3-tier IBM Cloud VPC with Terraform and Ansible
{: #bw-4hana-on-vpc}

Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) infrastructure resources so that you can rapidly build complex, cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware that uses Intel Xeon CPUs and other Intel technologies.
{: shortdesc}

You can use Terraform scripts to create a single-tier VPC and create the SAP and Db2 infrastructure on the VPC. The Terraform scripts create the VPC and then call the Ansible playbook to create the SAP architecture on the VPC. The recommended place to run the scripts is on your Deployment Server. The Deployment Server needs to have Terraform and Ansible installed.

## SAP Solution implemented 

SAP BW/4HANA is a packaged data warehouse based on SAP HANA. As the on-premises data warehouse layer of SAP’s Business Technology Platform, SAP BW/4HANA can be used to consolidate data across the enterprise to get a consistent, agreed-upon view of your data.

SAP BW/4HANA is a new, next-generation data warehouse product from SAP that, like SAP S/4HANA&reg;, is optimized for the SAP HANA platform, including inheriting the high performance, simplicity, and agility of SAP HANA. SAP BW/4HANA delivers real-time, enterprise-wide analytics that minimize the movement of data and can connect all the data in an organization into a single, logical view, including new data types and sources. Organizations now have a data warehousing solution that can meet their current and future business needs. A solution that handles more data, from more sources to deliver the solutions that increase an organization’s success in the next generation of business.

SAP BW/4HANA, combined with SAP S/4HANA and the SAP HANA platform, combines traditional data warehousing, such as operational reporting and historical analysis, with the future  - logical data warehouse, Internet of Things, and data lakes.

SAP BW/4HANA combines with SAP S/4HANA to meet your operational and historical analytics needs by using the same data as your applications, so you are always using fresh data.

With a logical data warehouse approach that uses smart data integration, SAP BW/4HANA eliminates duplication and expensive data movement to connect the data silos in your organization. All data sources can be connected, including SAP and non-SAP data sources.

## What is created
The scripts work in two phases. The first phase automates the VPC provisioning process. The second phase creates the SAP architecture in a distributed environment SAP BW/4HANA App server on a distinct VSI VPC machine and HANA DB on a dedicated server type VSI VPC box machine.

During the first phase, the VPC is provisioned with these components:

*	1 VPC where the virtual server instances are provisioned
*	1 security group. The rules for this security group allow: 
        *	Inbound DNS (port 53) and
        *	Inbound SSH (TCP port 22) connections to your virtual server instance
        *	All outbound traffic from the virtual server instance
*	1 subnet to enable networking in your VPC
*	2 X virtual server instance with SAP certified storage and network configurations
*	2 floating IP’s address that you use to access your VPC virtual server instance over the public network

During the second phase, the Ansible Playbook is called and the SAP architecture is installed for both dedicated VSI’s SAP App VSI machine and dedicated HANA VSI box. The SAP architecture that is deployed is the SAP BW/4HANA release on stand-alone dedicated Hana 2.0 box release. For more information about this architecture, see [How to automate SAP HANA stand-alone VSI on IBM VPC Cloud by using Terraform and Ansible](/docs/sap?topic=sap-background-for-automating-sap-hana-stand-alone-vsi).

## Single-host HANA system

A single-host system is the simplest system installation type that runs an SAP HANA system entirely on one host. You can scale the system up as needed. The single-host system has these components:

![Figure 1. SAP NetWeaver 7.x HANA single-host installation with AAS](images/refarch-sap-hana-single-host-only.svg "SAP NetWeaver 7.x HANA standard installation with AAS"){: caption="Figure 2. SAP NetWeaver 7.x HANA single-host installation with AAS" caption-side="bottom"}

The scripts are designed to create a new VPC and install SAP (BW/4HANA release) solution together with its dedicated DB Hana box in one task flow. If you want to install SAP BW/4HANA on an existing VPC with already installed and configured HANA box VSI, see [How to automate SAP HANA stand-alone VSI on IBM VPC Cloud by using Terraform and Ansible](/docs/sap?topic=sap-background-for-automating-sap-hana-stand-alone-vsi).

## SAP Kits

For each {{site.data.keyword.cloud_notm}} region IBM allocates temporary storage on a dedicated Jump host. It is your responsibility to download the necessary SAP and DB kits to your Deployment (Bastion) Server. All file archives are decompressed by Ansible during the automatic deploying process. For more information, see the readme file.

## Support

There are no warranties of any kind, and there is no service or technical support available for these materials from IBM®. As a recommended practice, review carefully any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, re-adapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Script files
{: #terraform-sap-hana-vsi-files}

The configuration and script files are provided on the GitHub repository [https://github.com/IBM-Cloud/sap-bw4hana/tree/main/cli](https://github.com/IBM-Cloud/sap-bw4hana/tree/main/cli). 

For SAP BW/4HANA virtual server instance on {{site.data.keyword.IBM}} virtual private cloud, you modify the:

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

* Red Hat Enterprise Linux 7.6 for SAP HANA (x86_64)
* Two SSH keys configured to access as root user on SSH  
* Three storage volumes as described in the ``input.auto.tfvars`` file. 

## Before you begin

*	Log in to your Deployment (Bastion) Server and verify that Terraform and Ansible are installed. 
*   If you have not already, create a bastion server to store the SAP kits.  For more information, see [Automate SAP bastion server - SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server).
*	Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. Ansible decompresses all of the archive kits. For more information, see the readme file. 
*  [Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey#create_user_key). The API key is used to authenticate with the IBM Cloud platform and to determine your permissions for IBM Cloud services.
*  [Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.

During configuration you can specify an existing VPC to use, instead of creating a new VPC.

For the detailed steps about using Terraform to create only a VPC for SAP, see [Creating single-tier virtual private cloud for SAP by using Terraform](/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).

## Procedure

1.	Log in to the Deployment Server by using `ssh`.
2.	Clone the terraform and ansbile folders and readme file from ``https://github.com/IBM-Cloud/sap-bw4hana/tree/main/cli`` and change to the ``sap-bw4hana/terraform`` folder.

    
	    $ git clone https://github.com/IBM-Cloud/sap-bw4hana.git
    	
	    $ cd sap-bw4hana/cli/terraform
    
3.  Edit the ``terraform.tfvars`` variable file and enter the IBM Cloud API key that you retrieved. 

    ``ibmcloud_api_key = "<ibmcloud_apikey>"``

    Variables that are defined in the `terraform.tfvars` file are automatically loaded by Terraform when the {{site.data.keyword.cloud_notm}} Provider plug-in is initialized and you can reference them in every Terraform configuration file that you use.  

    Because the `terraform.tfvars` file contains confidential information, do not push this file to a version control system. Keep this file on your local system only. 

4.	Edit the input parameter file, `input.auto.tfvars`, and modify the variables to match your solution. The file is preset with the minimal recommended disk sizes. Whether you are creating a VPC or using an existing VPC, you must modify:

    * VPC - New or existing VPC name. By defulat, the scripts expect an existing VPC name.  
    * SECURITYGROUP - change `ic4sap` to the VPC name.
    * SUBNET - change `ic4sap` to the VPC name.
    * DB-HOSTNAME - enter a hostname up to 13 characters. For more information, see the readme file. 
    * APP-HOSTNAME - enter a hostname up to 13 characters. For more information, see the readme file. 

    Replace the SSH keys in the file with your SSH key 40-digit ID.

    ```terraform
    #General VPC variables
    REGION          = "ed-de"
    ZONE			= "eu-de-2"
    VPC				= "ic4sap"
    SECURITYGROUP	= "ic4sap-securitygroup"
    SUBNET			= "ic4sap-subnet"
    SSH_KEYS		= [ "ssh key1" , "ssh key2" ]

    # SAP Database VSI variables:
    DB-HOSTNAME     = "sapbw4db"
    DB-PROFILE      = "mx2-16x128" 
    DB-IMAGE        = "ibm-redhat-7-6-amd64-sap-hana-1" # For any manual change in the terraform code, you have to make sure that you use a certified image based on the SAP NOTE: 2927211.

    # SAP APPs VSI variables:
    APP-HOSTNAME    = "sapbw4app"
    APP-PROFILE     = "bx2-4x16"
    APP-IMAGE       = "ibm-redhat-7-6-amd64-sap-applications-1" # For any manual change in the terraform code, you have to make sure that you use a certified image based on the SAP NOTE: 2927211.

    ```

    The hostname must have up to 13 characters as required by SAP. For more information about the rules that apply to hostnames for SAP systems, see SAP Note 611361 - Hostnames of SAP ABAP Platform servers
    {: note}

5.	In the same file (`input.auto.tfvars`), edit the SAP system configuration variables that are passed to the Ansible automated deployment. 

    ```
    #HANA DB configuration
    hana_sid = "BWH"
    hana_sysno = "00"
    hana_system_usage = "custom"
    hana_components = "server"

    #SAP HANA Installation kit path
    kit_saphana_file = "/storage/HANADB/51054623.ZIP"

    #SAP system configuration
    sap_sid = "BWH"
    sap_ascs_instance_number = "01"
    sap_ci_instance_number = "00"

    # Number of concurrent jobs used to load and/or extract archives to HANA Host
    hdb_concurrent_jobs = "6"

    #SAP BW4HANA APP Installation kit path
    kit_sapcar_file = "/storage/BW4HANA/SAPCAR_1010-70006178.EXE"
    kit_swpm_file = "/storage/BW4HANA/SWPM20SP09_4-80003424.SAR"
    kit_sapexe_file = "/storage/BW4HANA/SAPEXE_400-80004393.SAR"
    kit_sapexedb_file = "/storage/BW4HANA/SAPEXEDB_400-80004392.SAR"
    kit_igsexe_file = "/storage/BW4HANA/igsexe_13-80003187.sar"
    kit_igshelper_file = "/storage/BW4HANA/igshelper_17-10010245.sar"
    kit_saphotagent_file = "/storage/BW4HANA/SAPHOSTAGENT51_51-20009394.SAR"
    kit_hdbclient_file = "/storage/BW4HANA/IMDB_CLIENT20_009_28-80002082.SAR"
    kit_bw4hana_export = "/storage/BW4HANA/export"
    ```


6.	Edit the `terraform/main.tf` file and specify the VPC to use. You can have the scripts create a new VPC or you can use an existing VPC. By default the scripts use an existing VPC. If you are using an existing VPC, be sure that the VPC name is in the `input.auto.tfvars` file.

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

    You must enter a HANA maste password and an SAP master password. 

    The HANA master password must consist of at least one digit (0-9), one lowercase letter (a-z), and one uppercase letter (A-Z). It can only contain the following characters: a-z, A-Z, 0-9, !, @, #, $, _. It must not start with a digit or an underscore ( _ ). 
    
    The SAP master password must be 10 to 14 characters long and contain at least one digit (0-9). It can only contain the following characters: a-z, A-Z, 0-9, @, #, $, _. This password cannot contain `!`. It must not start with a digit or an underscore ( _ ).

9. Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the ``input.auto.tfvars`` file to correct resources and run ``terraform plan`` again.


10. Create the virtual private cloud for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.

    ``terraform apply``

     The virtual private cloud and components are created and you see output similar to the `terraform plan` output.  

## Next steps

If you need to rename your resources after they are created, modify the ``input.auto.tfvars`` file to change the names and run ``terraform plan`` and ``terraform apply`` again. Do not use the {{site.data.keyword.cloud_notm}} Dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results. 

If you need to remove your VPC, go to your project folder and run ``terraform destroy``.

## Related information

For more information about Terraform on IBM Cloud, see [Terraform on IBM Cloud getting started tutorial](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

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

