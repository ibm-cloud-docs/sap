---

copyright:
  years: 2022
lastupdated: "2022-08-19"

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

# Deploying SAP NetWeaver 7.x and ASE SYB on an existing {{site.data.keyword.cloud_notm}} VPC with automation
{: #automate-nw-asesyb-terraform-ansible}

Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) infrastructure resources so that you can rapidly build complex, cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware that uses Intel Xeon CPUs and additional Intel technologies.

You can use Terraform scripts to create a single-tier VPC and create the SAP and ASE SYB infrastructure on the VPC. The Terraform scripts use the VPC information that you provide and then call the Ansible playbook to create the SAP architecture on the specified VPC.

## What is created
{: #automate-nw-asesyb-what-created}

The scripts use the information that you provide for an existing VPC and deploy NW7.X with ASE SYB. For more information about this architecture, see [SAP NetWeaver 7.x on UNIX with ASE SYB on {{site.data.keyword.cloud_notm}} VPC](/docs/sap?topic=sap-sap-refarch-nw-sybase). You specify the information for the VPC to use in the `input.auto.tfvars` file.

The scripts call the Ansible Playbook to install the SAP architecture.

## Script files
{: #automate-nw-asesyb-script-files}

The configuration and script files are provided on GitHub. Each supported interface for the SAP solution installation has its own folder in the GitHub repository:

*	Using Terraform on the Bastion server - https://github.com/IBM-Cloud/sap-automated-deployment-scripts/tree/main/sapnwase/cli. 


## Terraform interface
{: #automate-nw-asesyb-terraform-interface}
{: terraform}

To run the Terraform scripts, you modify:
*	The input.auto.tfvars file to specify the existing VPC resources for your solution. You specify the variables for the existing VPC:

    *	VPC name
    *	Security group
    *	Subnet
    *	HostName
    *	Profile
    *	Image
    *	Up to two SSH keys

You can change the default SAP system configuration settings to match your solution. You also specify the location where you downloaded the SAP kits.
The {{site.data.keyword.cloud_notm}} Provider plug-in for Terraform on {{site.data.keyword.cloud_notm}} uses these configuration files to install SAP NetWeaver 7.X with Db2 on the specified VPC in your {{site.data.keyword.cloud_notm}} account.

## Support
{: #automate-nw-asesyb-support}

There are no warranties of any kind, and there is no service or technical support available for these materials from {{site.data.keyword.ibm}}. As a recommended practice, review carefully any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the {{site.data.keyword.ibm}} Service organization, your comments are welcomed by the developers, who reserve the right to revise, re-adapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Before you begin
{: #automate-nw-asesyb-before}

Before you use the scripts in the Bastion cli or {{site.data.keyword.bpshort}}:

*	Set up your account to access the VPC. Make sure that your account is [upgraded to a paid account](/docs/account?topic=account-accountfaqs#changeacct).
*	If you have not already, create a Bastion server to store the SAP kits. For more information, see [Automate SAP bastion server - SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server).
*	Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. Ansible decompresses the files. For more information, see the readme file. UPDATE THE RIGHT LINK
*	[Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey#create_user_key). The API key is used to authenticate with the {{site.data.keyword.cloud_notm}} platform and to determine your permissions for {{site.data.keyword.cloud_notm}} services.
*	[Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.
*	Terraform should already be installed on the bastion server that you deployed. For more information, see Bastion server for SAP deployment. {: terraform}

## Creating the infrastructure by using Terraform with the Bastion server CLI
{: #automate-nw-asesyb-create-terraform}
{: terraform}

Use these steps to configure the {{site.data.keyword.cloud_notm}} Provider plug-in and use Terraform to install SAP NetWeaver 7.X with ASE SYB on your existing VPC. The scripts can take 1 - 2 hours to complete.

1.	Access the Bastion server cli.
2.	Clone the solution repository from https://github.com/IBM-Cloud/sap-automated-deployment-scripts/tree/main/sapnwase/cli and cd to the sap-automated-deployment-scripts/sapnwase/cli folder.

    ```
    $ git clone https://github.com/IBM-Cloud/sap-automated-deployment-scripts.git
	
    $ cd sap-automated-deployment-scripts/sapnwase/cli
    ```
3.	Specify your VPC. Modify the input.auto.tfvars file to specify the information for the existing VPC, your zone, VPC and component names, profile, and image. You need your 40-digit SSH key ID for this file. The second SSH key is optional. For more options for profile, see [Instance Profiles](/docs/vpc?topic=vpc-profiles). For more options for image, see [Images](/docs/vpc?topic=vpc-about-images). For descriptions of the variables, see the readme file. UPDATE THE RIGHT LINK

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
    ```

4.	Customize your SAP system configuration. In the same file, input.auto.tfvars, edit the SAP system configuration variables that are passed to the Ansible automated deployment. For descriptions of the variables, see the readme file. UPDATE THE RIGHT LINK

    ```
    ##SAP system configuration – UPDATE RIGHT LINES
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
5.	Remember, you must manually decompress the kit_export_dir, kit_db2_dir, and kit_db2client_dir files. Ansible decompresses the rest of the SAP kit files. For more information, see the readme file. UPDATE THE RIGHT LINK

6.	Initialize the Terraform CLI.
    `terraform init`

7.	Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account.

    `terraform plan --out plan1`

    You must enter an SAP main password and your API key.

    The SAP main password must be 10 - 14 characters long and contain at least one digit (0-9). It can contain only the following characters: a-z, A-Z, 0-9, @, #, $, _. This password cannot contain exclamation points '!'. The password must not start with a digit or an underscore ( _ ).

8.	Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the input.auto.tfvars file to correct resources and run terraform plan again.

9.	Create the virtual private cloud for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.

	```teraform
	 terraform apply "plan1"
	 ```
	
	The virtual private cloud and components are created and you see output similar to the `terraform plan` output.  

10.	Create the virtual private cloud for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.

11.	Add the SAP credentials and the virtual server instance IP to the SAP GUI. For more information about the SAP GUI, see [SAP GUI](https://help.sap.com/doc/7abd5470728810148a4b1a83b0e91070/1511%20000/en-US/frameset.htm).

## Next steps
{: #automate-nw-asesyb-next}
{: terraform}

If you need to rename your resources after they are created, modify the input.auto.tfvars file to change the names and run `terraform plan` and `terraform apply` again. Do not use the {{site.data.keyword.cloud_notm}} Dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results.

If you need to remove the Netweaver 7.X and ASE SYB  installation, go to your project folder and run `terraform destroy`. The `terraform destroy` command does not remove the VPC in this scenario because the VPC was created before these Terraform scripts were run.


## Related information
{: #automate-nw-asesyb-releted}

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Getting started with Terraform on {{site.data.keyword.cloud_notm}}](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

For more information about using Terraform for creating only a VPC for SAP, without the SAP architecture, see [Creating single-tier virtual private cloud for SAP by using Terraform](/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).


SAP One Support Notes that apply to this document:

*	[SAP Note 84555 - Windows Server, Linux&reg;, and UNIX: Certified hardware](https://launchpad.support.sap.com/#/notes/84855)
*	[SAP Note 2927211 - SAP Applications on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
*	[SAP Note 2923773 - Linux&reg; on {{site.data.keyword.cloud_notm}} (IaaS): Adaption of your SAP License](https://launchpad.support.sap.com/#/notes/2923773)
*	[SAP Note 2414097 - SAP Applications on {{site.data.keyword.cloud_notm}} Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)
*	[SAP Note 2369910 - SAP Software on Linux&reg;: General information](https://launchpad.support.sap.com/#/notes/2369910)
*	[SAP Note 171380 - Released IBM hardware (Intel processors) and IBM cloud services offers](https://launchpad.support.sap.com/#/notes/171380)
*	[SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)

This document is referenced by:

*	[SAP Note 2927211 - SAP Applications on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
*	[SAP Note 2588225 - SAP on {{site.data.keyword.cloud_notm}}: Protect against speculative execution vulnerabilities](https://launchpad.support.sap.com/#/notes/2588225)
*	[SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)
*	[SAP Note 2414097 - SAP Applications on {{site.data.keyword.cloud_notm}} Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)

