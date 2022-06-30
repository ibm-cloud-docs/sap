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

# Automating NetWeaver 7.x and HANA 3-tier distributed architecture on {{site.data.keyword.cloud}} VPC with Terraform and Ansible
{: #create-terraform-3tier-nw-hana-vpc-ansible}

You can use Terraform scripts to deploy the SAP NW app server and HANA db infrastructure on the VPC created with the SAP Bastion Deployment Server. The Terraform scripts create the virtual infrastructure and then call the Ansible playbook to create the SAP architecture on the VPC. The recommended place to run the scripts is on the SAP Bastion Deployment Server which has Terraform and Ansible installed on it, along with the necessary storage space for SAP Kits. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware using Intel Xeon CPUs and additional Intel technologies.
{: shortdesc}

## SAP Solution implemented
{: terraform-3tier-nw-hana-solution}

Numerous SAP enterprise solutions are built on SAP’s pervasive platform, SAP NetWeaver, including:
*  SAP HANA as Primary Persistence for SAP NetWeaver-Based Applications
*  SAP Business Suite applications (ERP, CRM, and SCM, and other applications), 
*  SAP Business Warehouse (BW), and 
*  Other SAP enterprise solutions  

SAP NetWeaver has two distinct aspects, ABAP and Java. Many applications that are built on SAP NetWeaver’s ABAP or Java (or both) application servers can run “on” SAP HANA. In this instance, SAP HANA serves as the sole database in the architecture. 

Technical interfaces are available for applications that are built on SAP NetWeaver AS ABAP and AS Java to run on SAP HANA. However, specific development enablement is normally required for each application to ensure it runs optimally on SAP HANA. SAP Business Suite applications (ERP, CRM, SCM, and other applications), SAP Business Warehouse (BW), and other SAP NetWeaver-based applications were modified to run on SAP HANA and use its many advantages. Additionally, various components and complimentary applications that are built on SAP NetWeaver can also run on SAP HANA by using the provided SAP NetWeaver DB interfaces. 

There is one restriction in the SAP HANA as primary persistence for SAP NetWeaver-based applications scenario: SAP NetWeaver ABAP and Java application servers must run on separate hardware servers from the SAP HANA hardware. 


## What is created
{: #terraform-3tier-nw-db2-components}

The scripts automate the virtual infrastructure resources, provisioning processes for the  SAP architecture in an Existing VPC with a distributed environment SAP NW 7.x (ABAP or Java) App server on a distinct virtual server instance VPC machine and HANA DB on a dedicated server type virtual server instance VPC box machine. The scripts work in two phases. 

During the first phase ([Automate SAP bastion server – SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server)), the virtual infrastructure resources based on the components from the existing VPC created by the Bastion Server are:

*	1 VPC where the virtual server instance is provisioned
*	1 security group. The rules for this security group are:
    - Allow inbound DNS traffic (port 53)  
    - Allow nbound SSH traffic (TCP port 22)
    - Allow all outbound traffic from the virtual server instance
    - Allow all traffic in the security group
*	1 subnet to enable networking in your VPC
*	2 virtual server instances with SAP certified storage and network configurations
*	2 floating IP address that you use to access your VPC virtual server instance over the public network

During the second phase, the Ansible Playbook is called and the SAP architecture is installed for both dedicated VSI’s SAP App VSI machine and dedicated HANA VSI box. The SAP architecture that is deployed is the SAP NW 7.x release on stand-alone dedicated HANA 2.0 box release. For more information about this architecture, see [Automating SAP HANA stand-alone virtual server instance on {{site.data.keyword.cloud}} VPC by using Terraform and Ansible](/docs/sap?topic=sap-automate-terraform-sap-hana-vsi).


## Single-host HANA system
{: terraform-3tier-nw-hana-single}

A single-host system is the simplest system installation type that runs an SAP HANA system entirely on one host. You can scale the system up as needed. The single-host system has these components:

![Figure 1. Sample reference architecture](images/refarch-sap-hana-single-host-only.svg "SAP NetWeaver 7.x with HANA standard installation with AAS on VSI to VPC {{site.data.keyword.cloud_notm}}"){: caption="Figure 1. SAP NetWeaver 7.x with HANA standard installation with AAS on VSI to VPC {{site.data.keyword.cloud_notm}}" caption-side="bottom"}

The scripts are designed to create a new VPC and install SAP (SAP NW 7.x release) solution together with its dedicated DB HANA box in one task flow.

<!--- 
The mentioned reference link is not available. I did not find an approriate page/section to hyperlink to.

If you want to install SAP NW 7.x on an existing VPC with already installed and configured HANA box VSI, see [How to automate SAP HANA stand-alone VSI on {{site.data.keyword.cloud}} VPC by using Terraform and Ansible](/docs/sap?topic=sap-background-for-automating-sap-hana-stand-alone-vsi).
--->


## SAP Kits
{: #terraform-3tier-nw-db2-sap-kits}

For each {{site.data.keyword.cloud_notm}} region, IBM allocates temporary storage on a dedicated temporary Deployment server (Bastion Server) that is used for terraform environment. It is your responsibility to download the necessary SAP and DB kits to your Deployment (Bastion) Server. All file archives are decompressed by Ansible during the automatic deploying process. For more information, see the readme file in the dedicated GitHub repository.

## Script files
{: #terraform-3tier-nw-db2-ansible-files}

The configuration and script files are provided on the [GitHub repository github.com/IBM-Cloud/sap-netweaver-java-hana](https://github.com/IBM-Cloud/sap-netweaver-java-hana). 

For SAP HANA stand-alone virtual server instance on {{site.data.keyword.cloud_notm}} Virtual Private Cloud, you modify the:

*	The `input.auto.tfvars` file to customize the resources for your solution. You specify zones, resource names, SSH keys, and SAP variables.
    
All of the other configuration files are provided and do not need to be modified. 

## Support
{: #terraform-nw-db2-support}
 
There are no warranties of any kind, and there is no service or technical support available for these materials from {{site.data.keyword.IBM}}. As a recommended practice, review carefully any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, re-adapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Virtual server instance configuration

The virtual server instance is configured with:
•	Red Hat Enterprise Linux 7.6 for SAP HANA (x86_64) 
•	Two SSH keys configured to access as root user on SSH
•	Three storage volumes as described in the `input.auto.tfvars` file.

## Before you begin
{: #terraform-3tier-nw-db2-ansible-before}

Before you use the scripts:

*  Log in to your previously created Deployment Server and verify that Terraform and Ansible are installed.  
*  If you have not already, create a Bastion Server to store the SAP kits.  For more information, see [Automate SAP bastion server - SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server).
*  Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. You must decompress the `kit_export_dir`, `kit_db2_dir`, and `kit_db2client_dir` files. Ansible decompresses the rest of the files. For more information, see the `README` [file](https://github.com/IBM-Cloud/sap-netweaver-java-hana). 
*  [Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey#create_user_key). The API key is used to authenticate with the {{site.data.keyword.cloud_notm}} platform and to determine your permissions for {{site.data.keyword.cloud_notm}} services.
*  [Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.

During configuration you can specify an existing VPC to use, instead of creating a new VPC.
For the detailed steps about using Terraform to create only a VPC for SAP, see [Creating single-tier Virtual Private Cloud for SAP by using Terraform](/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).


## Procedure

Use these steps to configure the {{site.data.keyword.cloud_notm}} Provider plug-in and use Terraform to create VPC resources for SAP. The scripts can take 2 hours to complete. 

1.  Log in to the Bastion Deployment Server by using `ssh`.

2.  Clone the `terraform` and `ansible` folders and `README` file from `https://github.com/IBM-Cloud/sap-netweaver-java-hana` and change to the `sap-netweaver-java-hana/cli/terraform` folder.

    ```
    $ git clone https://github.com/IBM-Cloud/sap-netweaver-java-hana.git
    
    $ cd sap-netweaver-java-hana/cli/terraform
    ```

3.	Define your existing VPC variables. Modify the `input.auto.tfvars` file to specify your zone, VPC component names, profile, and image. The file is preset with the minimal recommended disk sizes. You need your 40-digit SSH key ID for this file. The second SSH key is optional. For more options for profile, see [Instance Profiles](/docs/vpc?topic=vpc-profiles). For more options for image, see [Images](/docs/vpc?topic=vpc-about-images). For descriptions of the variables, see the `README` [file](https://github.com/IBM-Cloud/sap-netweaver-java-hana).

    Whether you are creating a VPC or using an existing VPC, you must modify:
    *	VPC - Existing VPC name.
    *	SECURITYGROUP - Change ic4sap-securitygroup to the existing securitygroup name.
    *	SUBNET - Change ic4sap-subnet to the existing subnet name.
    *	DB-HOSTNAME - enter a hostname up to 13 characters. For more information, see the readme file.
    *	APP-HOSTNAME - enter a hostname up to 13 characters. For more information, see the readme file.

    Replace the SSH key with your 40-digit SSH key.


    ```
    #General VPC variables
    REGION          = "ed-de"
    ZONE			= "eu-de-2"
    VPC				= "ic4sap"
    SECURITYGROUP	= "ic4sap-securitygroup"
    SUBNET			= "ic4sap-subnet"
    SSH_KEYS		= [ "ssh key1" , "ssh key2" ]

    # SAP Database VSI variables:
    DB-HOSTNAME     = "sapjavadb"
    DB-PROFILE      = "mx2-16x128" 
    DB-IMAGE        = "ibm-redhat-7-6-amd64-sap-hana-1" # For any manual change in the terraform code, you have to make sure that you use a certified image based on the SAP Note: 2927211.

    # SAP APPs VSI variables:
    APP-HOSTNAME    = "sapjavci"
    APP-PROFILE     = "bx2-4x16"
    APP-IMAGE       = "ibm-redhat-7-6-amd64-sap-applications-1" # For any manual change in the terraform code, you have to make sure that you use a certified image based on the SAP Note: 2927211.

    ```

    The hostname must have up to 13 characters as required by SAP. For more information about the rules that apply to hostnames for SAP systems, see SAP Note 611361 - Hostnames of SAP ABAP Platform servers

4. Customize your SAP system configuration. Modify the ``input.auto.tfvars`` file to specify SAP system configuration and enter the location of the downloaded SAP Kits. For descriptions of the variables, see the `README` [file](https://github.com/IBM-Cloud/sap-netweaver-java-hana). 
  
    ```
    #HANA DB configuration
    hana_sid = "HDB"
    hana_sysno = "00"
    hana_system_usage = "custom"
    hana_components = "server"

    #SAP HANA Installation kit path
    kit_saphana_file = "/storage/HANADB/51054623.ZIP"

    #SAP system configuration
    sap_sid = "JV1"
    sap_ascs_instance_number = "01"
    sap_ci_instance_number = "00"

    #SAP S4HANA APP Installation kit path
    kit_sapcar_file = "/storage/NW75HDB/SAPCAR_1010-70006178.EXE"
    kit_swpm_file = "/storage/NW75HDB/SWPM10SP31_7-20009701.SAR"
    kit_sapexe_file = "/storage/NW75HDB/SAPEXE_801-80002573.SAR"
    kit_sapexedb_file = "/storage/NW75HDB/SAPEXEDB_801-80002572.SAR"
    kit_igsexe_file = "/storage/NW75HDB/igsexe_13-80003187.sar"
    kit_igshelper_file = "/storage/NW75HDB/igshelper_17-10010245.sar"
    kit_saphotagent_file = "/storage/NW75HDB/SAPHOSTAGENT51_51-20009394.SAR"
    kit_hdbclient_file = "/storage/NW75HDB/IMDB_CLIENT20_009_28-80002082.SAR"
    kit_sapjvm_file = "/storage/NW75HDB/SAPJVM8_73-80000202.SAR"
    kit_java_export = "/storage/NW75HDB/export"
    ```

5. Initialize the Terraform CLI. 

   ```
   terraform init
   ```

6. Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account. During the Terraform plan, you are prompted to enter your API key, and initial SAP and DB passwords.  

   ```
   terraform plan --out plan1
   ```
    You must enter an SAP main password.
    
    The SAP main password must be 10 - 14 characters long and contain at least one digit (0-9). It can contain only the following characters: a-z, A-Z, 0-9, @, #, $, _. This password cannot contain !. It must not start with a digit or an underscore ( _ ).

7. Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the ``input.auto.tfvars`` file to correct resources and run ``terraform plan --out plan1` again.

8. Create the virtual private cloud for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.

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
*	[SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)]
*	[SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)
