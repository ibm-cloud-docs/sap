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

# Automating NetWeaver 7.x and Db2 3-tier distributed architecture on {{site.data.keyword.cloud}} VPC with Terraform and Ansible
{: #create-terraform-3tier-nw-db2-vpc-ansible}

Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} VPC infrastructure resources so that you can rapidly build complex, cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware using Intel Xeon CPUs and additional Intel technologies.
{: shortdesc}

You can use Terraform scripts to create a 3-tier Virtual Private Cloud (VPC) and create the SAP and Db2 infrastructure on the VPC. The Terraform scripts create the VPC and then call the Ansible playbook to create the SAP architecture on the VPC. The recommended place to run the scripts is on your Deployment Server. The Deployment Server needs to have Terraform and Ansible installed. 
{: shortdesc}

## SAP Solution implemented
{: terraform-3tier-nw-db2-solution}

Reference architecture of [SAP NetWeaver 7.x on UNIX with Db2 on {{site.data.keyword.cloud}} VPC](/docs/sap?topic=sap-sap-refarch-nw-db2) SAP NetWeaver is the core foundation of the SAP technology stacks and is the platform that is used for Advanced Business Application Programming (ABAP) and Java applications. SAP NetWeaver components are built on the SAP NetWeaver Application Server and are written in ABAP or Java Platform, Enterprise Edition. ABAP systems, Java systems, and dual-stack systems are distinct systems.

## Distributed system
{: terraform-3tier-nw-db2-distributed}

In a distributed system, there are multiple virtual server instances and every instance can run on a separate host:

![Figure 1. Sample reference architecture](images/refarch-sap-db2-std-only.svg "SAP NetWeaver 7.x with Db2 standard installation with AAS on VSI to {{site.data.keyword.cloud_notm}} VPC"){: caption="Figure 1. SAP NetWeaver 7.x with Db2 standard installation with AAS on VSI to {{site.data.keyword.cloud_notm}} VPC" caption-side="bottom"}

The components in a distributed system are the same as the components in a standard system, but there are restrictions as to which instances can go on which hosts.

## What is created
{: #terraform-3tier-nw-db2-components}

The scripts work in two phases. The first phase automates the VPC provisioning process. The second phase creates the SAP architecture in a distributed environment SAP NW 7.x  App server on a distinct virtual server instance VPC machine and DB2 DB on a dedicated server type virtual server instance VPC box machine.

During the first phase, the VPC is provisioned with these components: 

*	1 VPC where the virtual server instance is provisioned
*	1 security group. The rules for this security group allow:
    - Inbound DNS (port 53) and 
    - Inbound SSH (TCP port 22) connections to your virtual server instance 
    - All outbound traffic from the virtual server instance
*	1 subnet to enable networking in your VPC
*	2 virtual server instances with SAP certified storage and network configurations
*	2 floating IP address that you use to access your VPC virtual server instance over the public network

During the second phase, the Ansible Playbook is called and the SAP architecture is installed for both the dedicated SAP App virtual server instance machine and dedicated database virtual server instance Db2 box. The SAP architecture that is deployed is NetWeaver 7.X with Db2. For more information about this architecture, see [SAP NetWeaver 7.x on UNIX with Db2 on {{site.data.keyword.cloud}} VPC on distributed environment](/docs/sap?topic=sap-sap-refarch-nw-db2#installation-types)

## Single-host DB2 database VSI 
{: #terraform-3tier-nw-db2-single-host}

A single-host system is the simplest system installation type that runs an DB2 database instance  system entirely on one host. You can scale the system up as needed. The single-host system has these components:

The SAP systems in a landscape have specific requirements for servers, operating systems, network setup, and supported storage.

Deploying SAP AnyDB on {{site.data.keyword.cloud_notm}} is similar to deployments with infrastructure with on-premises data centers. You can use the information that is provided from SAP and the RDBMS providers.

To assist your project's planning phase, more design considerations are provided for [SAP AnyDB - IBM Db2](/docs/sap?topic=sap-anydb-ibm-db2) with {{site.data.keyword.cloud_notm}} for SAP.

The scripts are designed to create a new VPC and install SAP (NW 7.x release) solution together with its dedicated database DB2 box in one task flow.


## Script files
{: #terraform-3tier-nw-db2-ansible-files}

The configuration and script files are provided on the GitHub repository [`https://github.com/IBM-Cloud/sap-netweaver-abap-db2-distributed`](https://github.com/IBM-Cloud/sap-netweaver-abap-db2-distributed). This solution  performs an automated deployment of a distributed system (two nodes) with SAP Netweaver with DB2 on top of Red Hat Enterprise Linux 7.6 for SAP Applications :

To run the scripts to create a VPC and install NW 7.x and Db2, you modify:

*	The `terraform.tfvars` file to add your {{site.data.keyword.cloud_notm}} API-key.
*	The `input.auto.tfvars` file to customize the resources for your solution. By default, the VSI is configured with:
    - Red Hat Enterprise Linux&reg; 7.x for SAP Applications (amd64), 
    - Two SSH keys that are configured to access as root user on SSH, 
    - Five storage volumes. 
  
    You can change the default settings to match your solution. 

    You can change the default SAP system configuration settings to match your solution. You also specify the location where you downloaded the SAP kits.
    
All of the other configuration files are provided and do not need to be modified. 

The {{site.data.keyword.cloud_notm}} Provider plug-in for Terraform on {{site.data.keyword.cloud_notm}} uses these configuration files to provision a VPC in your {{site.data.keyword.cloud_notm}} account. 

During configuration you can specify an existing VPC to use, instead of creating a new VPC. The procedures for Creating 2 or 3-tier VPC for SAP anydb (non-HANA) by using terraform with proper infrastructure environment for SAP with anydb (non-HANA) is optimized for Db2. 

For the detailed steps about using Terraform to create only a VPC for SAP, see [Creating 2nd and 3rd tier virtual private cloud for SAP anydb (non-HANA) by using Terraform](/docs/sap?topic=sap-create-terraform-multi-tier-vpc-anydb-sap).

## SAP Kits
{: #terraform-3tier-nw-db2-sap-kits}

For each {{site.data.keyword.cloud_notm}} region, IBM allocates temporary storage on a dedicated temporary Deployment server (Bastion server) that is used for terraform environment. It is your responsibility to download the necessary SAP and DB kits to your Deployment (Bastion) Server. All file archives are decompressed by Ansible during the automatic deploying process. For more information, see the readme file in the dedicated github repository.

## Support
{: #terraform-nw-db2-support}
 
There are no warranties of any kind, and there is no service or technical support available for these materials from {{site.data.keyword.IBM}}. As a recommended practice, review carefully any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, re-adapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Virtual server instance configuration

The virtual server instance is configured with:
•	Red Hat Enterprise Linux 7.6 with SAP subscription 
•	Two SSH keys configured to access as root user on SSH
•	Three storage volumes as described in the input.auto.tfvars file.

## Before you begin
{: #terraform-3tier-nw-db2-ansible-before}

Before you use the scripts:

*  Log in to your previously created Deployment Server and verify that Terraform and Ansible are installed.  
*  If you have not already, create a bastion server to store the SAP kits.  For more information, see [Automate SAP bastion server - SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server).
*  Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. You must decompress the `kit_export_dir`, `kit_db2_dir`, and `kit_db2client_dir` files. Ansible decompresses the rest of the files. For more information, see the `README` [file](https://github.com/IBM-Cloud/sap-netweaver-abap-db2-distributed/). 
*  [Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey#create_user_key). The API key is used to authenticate with the {{site.data.keyword.cloud_notm}} platform and to determine your permissions for {{site.data.keyword.cloud_notm}} services.
*  [Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.

## Procedure

Use these steps to configure the {{site.data.keyword.cloud_notm}} Provider plug-in and use Terraform to create a VPC for SAP. The scripts can take 2 hours to complete. 

1.  Log in to the Deployment Server by using `ssh`.

2.  Clone the `terraform` and `ansbile` folders and `README` file from `https://github.com/IBM-Cloud/sap-netweaver-abap-db2-distributed/` and change to the `sap-netweaver-abap-db2-distributed/cli/terraform` folder.

    ```
    $ git clone https://github.com/IBM-Cloud/sap-netweaver-abap-db2-distributed.git
    
    $ cd sap-netweaver-abap-db2-distributed/cli/terraform
    ```

3.	Define your VPC. Modify the `input.auto.tfvars` file to specify your zone, VPC component names, profile, and image. You need your 40-digit SSH key ID for this file. The second SSH key is optional. For more options for profile, see [Instance Profiles](/docs/vpc?topic=vpc-profiles). For more options for image, see [Images](/docs/vpc?topic=vpc-about-images). For descriptions of the variables, see the `README` [file](https://github.com/IBM-Cloud/sap-netweaver-abap-db2-distributed/).

    Whether you are creating a VPC or using an existing VPC, you must modify:
    *	VPC - New or existing VPC name.
    *	SECURITYGROUP - change ic4sap to the VPC name.
    *	SUBNET - change ic4sap to the VPC name.
    *	HOSTNAME - enter a hostname up to 12 characters. For more information, see the readme file.

    Add your SSH key 40-digit ID to the SSH_KEYS.


    ```
    #Infra VPC variables
    REGION			= "eu-de"
    ZONE			= "eu-de-2"
    VPC			= "ic4sap"
    SECURITYGROUP		= "ic4sap-securitygroup"
    SUBNET			= "ic4sap-subnet"
    SSH_KEYS		= [ "r010-57bfc315-f9e5-46bf-bf61-d87a24a9ce7a" , "r010-3fcd9fe7-d4a7-41ce-8bb3-d96e936b2c7e"  ]
    
    # SAP Database VSI variables:
    DB-HOSTNAME		= "sapnwdb2"
    DB-PROFILE		= "bx2-4x16"
    DB-IMAGE		= "ibm-redhat-7-6-amd64-sap-applications-1"# For any manual change in the terraform code, you have to make sure that you use a certified image based on the SAP Note: 2927211.

    # SAP APPs VSI variables:
    APP-HOSTNAME	= "sapnwapp"
    APP-PROFILE		= "bx2-4x16"
    APP-IMAGE		= "ibm-redhat-7-6-amd64-sap-applications-1" # For any manual change in the terraform code, you have to make sure that you use a certified image based on the SAP Note: 2927211.

    ```

    The hostname must have up to 13 characters as required by SAP. For more information about the rules that apply to hostnames for SAP systems, see SAP Note 611361 - Hostnames of SAP ABAP Platform servers

4. Customize your SAP system configuration. Modify the ``input.auto.tfvars`` file to specify SAP system configuration and enter the location of the downloaded SAP Kits. For descriptions of the variables, see the `README` [file](https://github.com/IBM-Cloud/sap-netweaver-abap-db2-distributed/2). 
  
    ```
    #SAP system configuration
    sap_sid = "NWA"
    sap_ascs_instance_number = "01"
    sap_ci_instance_number = "00"

    #Kits paths
    kit_sapcar_file = "/storage/NW75DB2/SAPCAR_1010-70006178.EXE"
    kit_swpm_file =  "/storage/NW75DB2/SWPM10SP31_7-20009701.SAR"
    kit_saphotagent_file = "/storage/NW75DB2/SAPHOSTAGENT51_51-20009394.SAR"
    kit_sapexe_file = "/storage/NW75DB2/SAPEXE_800-80002573.SAR"
    kit_sapexedb_file = "/storage/NW75DB2/SAPEXEDB_800-80002603.SAR"
    kit_igsexe_file = "/storage/NW75DB2/igsexe_13-80003187.sar"
    kit_igshelper_file = "/storage/NW75DB2/igshelper_17-10010245.sar"
    kit_export_dir = "/storage/NW75DB2/51050829"
    kit_db2_dir = "/storage/NW75DB2/51051007/DB2_FOR_LUW_10.5_FP7SAP2_LINUX_"
    kit_db2client_dir = "/storage/NW75DB2/51051049"
    ```

5.	Edit the `terraform/main.tf` file and specify the VPC to use. You can have the scripts create a new VPC or you can use an existing VPC. By default the scripts use an existing VPC. If you are using an existing VPC, be sure that the VPC name is in the `input.auto.tfvars` file.

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

6. Initialize the Terraform CLI. 

   ```
   terraform init
   ```

7. Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account. During the Terraform plan, you are prompted to enter your API key, and initial SAP and DB passwords.  

   ```
   terraform plan --out plan1
   ```

8. Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the ``input.auto.tfvars`` file to correct resources and run ``terraform plan --out plan1` again.

9. Create the virtual private cloud for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.

    ```
     terraform apply "plan1"
     ```
     The virtual private cloud and components are created and you see output similar to the `terraform plan` output.  


## Next steps

If you need to rename your resources after they are created, modify the ``input.auto.tfvars`` file to change the names and run ``terraform plan`` and ``terraform apply`` again. Do not use the {{site.data.keyword.cloud_notm}} Dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results. 

If you need to remove your VPC, go to your project folder and run ``terraform destroy``.

## Related information

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Terraform on {{site.data.keyword.cloud_notm}} getting started tutorial](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

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
