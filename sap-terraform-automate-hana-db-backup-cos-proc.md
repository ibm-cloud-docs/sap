---

copyright:
  years: 2023
lastupdated: "2023-06-14"

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

# Deploying SAP HANA db backup to Cloud Object Storage on existing {{site.data.keyword.cloud_notm}} VPC with automation
{: #sap-automate-hana-db-backup-cos-deploy}

You can use Terraform scripts to Hana db backup to Cloud Object Storage automation module on existing HANA database already deployed instance on the VPC. The Terraform scripts use the VPC information that you provide and then call the Ansible playbook to create the SAP architecture on the specified VPC.
Terraform on {{site.data.keyword.cloud_notm}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) infrastructure resources so that you can rapidly build complex, cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware that uses Intel Xeon CPUs and additional Intel technologies.
{: shortdesc}

## Solution implemented
 {: #sap-hana-db-backup-cos-proc}

![Figure 1. SAP HANA db backup to Cloud Object Storage from a standard SAP NW on HANA db architecture](images/sap-hana-backup-on-vpc.svg "SAP HANA db backup to Cloud Object Storage from a standard SAP NW on HANA db architecture"){: caption="Figure 1. SAP HANA db backup to Cloud Object Storage from a standard SAP NW on HANA db architecture" caption-side="bottom"}


## What is created
{: #sap-hana-db-backup-cos-created}

This automation solution is designed for the implementation of an SAP HANA Backup solution that uses Backint and [{{site.data.keyword.cloud_notm}} Object Storage](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-getting-started-cloud-object-storage). It is based on SAP note "2935898 - Install and Configure SAP HANA Backint Agent for Amazon S3".

The minimum version of SAP HANA Backint Agent for Amazon S3 to be used for IBM Cloud Object Storage is 1.2.17. "SAP HANA Backint Agent for Amazon S3" requires Python3.7, including SSL support. The Python package delivered as part of the SAP HANA 2 installation does not include SSL support. Python 3.7 with SSL support is installed in addition in /usr/local/bin directory, in case it isn't previously installed. SAP HANA Backint Agent for Amazon S3 is installed in `/hana/shared/< SID >/backint_agent` directory.

The setup runs onetime initial full data backup to Cloud Object Storage for SYSTEM and TENANT databases. The regular backup policy for the databases must be configured by the customer.

All data backup, log backup, and catalog backup files are saved in the same dedicated bucket (with no enforced storage quota) created in a Cloud Object Storage instance by using an existing bastion host with secure remote SSH access in the same {{site.data.keyword.cloud_notm}} Gen2 VPC as the SAP HANA system. The setup uses the REGIONAL Storage Class with the Smart Tier pricing option and the STANDARD pricing plan. The SAP HANA system must be previously deployed on one of the following Operating Systems: SUSE Linux Enterprise Server 15 SP 4 for SAP, SUSE Linux Enterprise Server 15 SP 3 for SAP, Red Hat Enterprise Linux 8.6 for SAP, Red Hat Enterprise Linux 8.4 for SAP.

## Script files
{: #sap-hana-db-backup-cos-script-files}

The configuration and script files are provided on GitHub. Each supported interface for the SAP solution installation has its own folder in the GitHub repository:

[GitHub repository for Terraform - HANA backup COS](https://github.com/IBM-Cloud/sap-hana-backup-cos)

## Terraform interface
{: #sap-hana-db-backup-terraform-interface}

To run the Terraform scripts, you modify:
*	The input.auto.tfvars file to specify the existing VPC resources for your solution. You specify the variables for the existing VPC:
    *	VPC name
    *	Security group
    *	Subnet
    *	HostName(s)

You can change the default SAP system configuration settings to match your solution. You also specify the location where you downloaded the SAP kits.

The {{site.data.keyword.cloud_notm}} Provider plug-in for Terraform on {{site.data.keyword.cloud_notm}} uses these configuration files to deploy HANA db backup to Cloud Object Storage automation module on already installed HANA database instance on the specified VPC in your {{site.data.keyword.cloud_notm}} account.

## Support
{: #sap-hana-db-backup-support-terraform}

There are no warranties of any kind, and there is no service or technical support available for these materials from {{site.data.keyword.ibm}}. As a recommended practice, review carefully any materials that you download from this site before you use them on a live system.

Though the materials provided herein are not supported by the IBM® Service organization, your comments are welcomed by the developers, who reserve the right to revise, re-adapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a Support case here.

## Before you begin
{: #sap-hana-db-backup-before}

Before you use the scripts in the Bastion cli:
You need the following to get started with {{site.data.keyword.cloud_notm}} Object Storage:
*	An [{{site.data.keyword.cloud_notm}} Platform account]( https://cloud.ibm.com/)
*	Set up your account to access the VPC. Make sure that your account is [upgraded to a paid account]( https://cloud.ibm.com/docs/account?topic=account-accountfaqs#changeacct)
*	If you have not already, create a Bastion server to store the SAP kits. For more information, see [Automate SAP bastion server - SAP media storage repository]( https://cloud.ibm.com/docs/sap?topic=sap-sap-bastion-server)
*	Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. Ansible decompresses the files. For more information, see the [readme]( https://github.com/IBM-Cloud/sap-hana-backup-cos/blob/main/README.md) file.
*	[Create or retrieve an {{site.data.keyword.cloud_notm}} API key]( https://cloud.ibm.com/docs/account?topic=account-userapikey#create_user_key). The API key is used to authenticate with the Create or retrieve your SSH key ID. 
*	Log in to the {{site.data.keyword.cloud_notm}} platform and to determine your permissions for {{site.data.keyword.cloud_notm}} services.
*	Terraform should already be installed on the bastion server that you deployed. For more information, see [Bastion server for SAP deployment]( https://cloud.ibm.com/docs/sap?topic=sap-sap-bastion-server).
*	To deploy HANA db backup to Cloud Object Storage automation module, the HANA database instance must be installed on the specified VPC in your {{site.data.keyword.cloud_notm}} account.
*	Ensure you have an identical password for all your HANA db SYSTEM users before you run this backup to Cloud Object Storage HANA backup module.

## Creating the infrastructure by using Terraform with the Bastion server CLI
{: #create-hana-db-backup-cli}

Use these steps to configure the SAP HANA db backup to Cloud Object Storage and use Terraform to install HANA db backup to Cloud Object Storage automation module on your existing VPC on an already deployed SAP HANA db 2.0.

The scripts can take 1 - 2 hours to complete. A full backup of System DB and Tennant DB instances at the end.

1.	Access the Bastion server cli.
2.	Clone the solution repository from https://github.com/IBM-Cloud/sap-hana-backup-cos:

    ```sh
    git clone https://github.com/IBM-Cloud/sap-hana-backup-cos

    cd sap-hana-backup-cos
    ```

3.	Specify your VPC. Modify the input.auto.tfvars file to specify the information for the existing VPC and component names. For descriptions of the variables, see the [readme](https://github.com/IBM-Cloud/sap-hana-backup-cos/blob/main/README.md) file.
    The VSI OS images that are validated for this solution for HANA db backup to Cloud Object Storage are:
    *	SUSE Linux Enterprise Server 15 SP 4 for SAP
    *	SUSE Linux Enterprise Server 15 SP 3 for SAP, 
    *	Red Hat Enterprise Linux 8.6 for SAP or 
    *	Red Hat Enterprise Linux 8.4 for SAP

    Note : Any of these OS images must be previously deployed in an {{site.data.keyword.cloud_notm}} Gen2 VPC, on a single HANA instance host (with or without HA). This HANA backup solution was implemented and tested on the following OS images available in {{site.data.keyword.cloud_notm}}: ibm-sles-15-4-amd64-sap-hana-3, ibm-sles-15-3-amd64-sap-hana-3, ibm-redhat-8-6-amd64-sap-hana-2, ibm-redhat-8-4-amd64-sap-hana-2. 

    ```terraform
    #Infra VPC variables
    REGION = "eu-de"
    VPC = "ic4sap"
    SECURITY_GROUP = "ic4sap-securitygroup"
    RESOURCE_GROUP = "wes-automation"
    SUBNET = "ic4sap-subnet"
    ID_RSA_FILE_PATH = "/root/.ssh/id_rsa_backup"

    # COS variables:

    LIFECYCLE_POLICY = "60"

    # HANA VSI variables:

    HA_CLUSTER = "yes/no"
    DB_HOSTNAME_1 = "hanadb-vsi1"
    DB_HOSTNAME_2 = ""
    ```
4.	Customize your SAP system configuration. In the same file, input.auto.tfvars, edit the SAP system configuration variables that are passed to the Ansible automated deployment. For descriptions of the variables, see the [readme](https://github.com/IBM-Cloud/sap-hana-backup-cos/blob/main/README.md) file.

    ```terraform
    # SAP HANA backup configuration

    HANA_SID = "HDB"
    HANA_SYSNO = "00"
    HANA_TENANTS = ["Ten_HDB1", "Ten_HDB2"]

    #Kits paths - for HANA db backup to Cloud Object Storage

    BACKINT_COS_KIT = "/storage/hdb_backup_kit_files/aws-s3-backint/aws-s3-backint-1.2.18-linuxx86_64.tar.gz"
    HANA_KIT_FOR_BACKINT_COS = ""
    CREATE_HDBBACKINT_SCRIPT ="/storage/hana_backup_kit_files/create_hdbbackint.py"
    ```

5.	Initialize the Terraform CLI. 

    ```sh
    terraform init
    ```

6.	Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are performed by the automation scripts based on the input variables.

    ```sh
    terraform plan --out plan1
    ```

    You must enter an SAP HANA main password (which should be identical) for all your HANA SYSTEM db users and your API key.
    The SAP main password must be 10 - 14 characters long and contain at least one digit (0-9). It can contain only the following characters: a-z, A-Z, 0-9, @, #, $, . This password cannot contain exclamation points '!'. The password must not start with a digit or an underscore ().
7.	Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the input.auto.tfvars file to correct resources and run terraform plan again.

8.	Apply the saved terraform plan.

    ```sh
    terraform apply "plan1"
    ```

    Resources are created and you see output similar to the terraform plan output.

10.	 IBM COS instance and bucket are created and the Hana DB system is configured to use backint backup.

11.	Add the SAP credentials and the virtual server instance IP to the SAP GUI or HANA STUDIO. For more information about the SAP GUI, see SAP GUI.

## Next steps
{: #sap-hana-db-backup-next}

If you need to rename your resources after they are created, modify the input.auto.tfvars file to change the names, and run terraform plan and terraform apply again. Do not use the {{site.data.keyword.cloud_notm}} Dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results.

If you need to remove the backup service:

Remove the "Hana backup to Cloud Object Storage" configuration and delete the Cloud Object Storage.

First change/adapt Hana backup configuration to not use backint/backup to IBM Cloud Object Storage:
1. Check and change/delete any Hana database backup job (for SYSTEM and tenant DBs) already scheduled, by using the same tool that you used to schedule the job (OS crontab, SAP DB13, SAP HANA Cockpit, Hana Studio) to not use the backint/backup to IBM Cloud Object Storage.
2. Make sure that the Hana "LOG Backup Settings" is changed from backint/backup to Cloud Object Storage to any other "Destination type" (file/disk, other backup tool). You can use SAP HANA Cockpit, Hana Studio, or other tool.
    Double check in the "Hana backup log" file and the new backup location to see whether Hana logs are automatically backed up as needed.
3. Make sure that Hana "Backup Catalog" destination is changed from backint/backup to Cloud Object Storage to any other "Destination type" (file/disk, other backup tool). You might use SAP Hana Cockpit, Hana Studio.
4. Make sure that you don't need the previous backups of Hana data and logs saved to Cloud Object Storage. Cloud Object Storage and all Hana data backups and logs backups are deleted. Make sure you saved them before in another location if you still need them.
5. Run a full DATA backup test for SYSTEM and Tenant DBs using the new backup location (file/disk, other backup tool), different from IBM Cloud Object Storage. Make sure that all backups complete succesfuly in the new location.
6. Check and reschedule full data backups for SYSTEM and tenant DBs using the new "Destination type" (file/disk, other backup tool).

Second, release the IBM Bucket and delete all the backups
1. Run Teraform destroy to delete the used IBM basket (Cloud Object Storage) and all its contents/backups
- The used files/directories for "Python 3.7 with ssl support" and "S3 backint"  are still be present at the OS level on Hana VSI but Hana will no longer use them.

## Related information
{: #sap-hana-db-backup-cos-related}

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see Getting started with Terraform on {{site.data.keyword.cloud_notm}}.

For more information about using Terraform for creating only a VPC for SAP, without the SAP architecture, see Creating single-tier virtual private cloud for SAP by using Terraform.

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
