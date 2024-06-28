---

copyright:
  years: 2023, 2024
lastupdated: "2024-06-28"

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

# Deploying SAP HANA stand-alone virtual server instance on {{site.data.keyword.cloud}} VPC
{: #automate-terraform-sap-hana-vsi}

As of 28 March 2024, the {{site.data.keyword.at_full_notm}} service is deprecated and will no longer be supported as of 30 March 2025. Customers will need to migrate to {{site.data.keyword.logs_full_notm}} before 30 March 2025. During the migration period, customers can use {{site.data.keyword.at_full_notm}} along with {{site.data.keyword.logs_full_notm}}. Activity tracking events are the same for both services. For information about migrating from {{site.data.keyword.at_full_notm}} to {{site.data.keyword.logs_full_notm}} and running the services in parallel, see [migration planning](/docs/cloud-logs?topic=cloud-logs-migration-intro).
{: important}

You can use Terraform scripts to create a single-tier SAP HANA DB on VSI or Bare Metal Server in a VPC. The Terraform scripts use the VPC information that you provide and then call the Ansible playbooks to create the SAP architecture on the specified VPC.

Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} VPC infrastructure resources so that you can rapidly build complex cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware by using Intel&reg; Xeon CPUs and additional Intel&reg; technologies.
{: shortdesc}

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Getting started with Terraform on {{site.data.keyword.cloud_notm}}](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

To create resources with Terraform, you can use Terraform configuration files that describe the {{site.data.keyword.cloud_notm}} resources that you need and how you want to configure them. Based on your configuration, Terraform creates an execution plan and describes the actions that need to be run to create the resources. You can review the execution plan, change it, or run the plan. 

You have two deployment methods to choose from:

* Terraform scripts run from the CLI on your bastion server.
* The schematic user interface is accessed from the menu on your cloud dashboard.

## Prerequisites
{: #terraform-sap-hana-vsi-prerequisites}
{: terraform}

A deployment server (bastion server) deployed by using the automation solution [Automate SAP bastion server – SAP media storage repository](https://test.cloud.ibm.com/docs/sap?topic=sap-sap-bastion-server), should exist in the same VPC, same region, and have the same subnet and security group that is configured for the SAP system VSIs.

Required IAM permissions for deploying SAP HANA on Bare Metal: Bare Metal Console Administrator role to access the ESXi Direct Console User Interface (DCUI) and Bare Metal Advanced Network Operator role to modify IP spoofing and infrastructure NAT configuration on network interfaces. For more information, see [Planning for Bare Metal Servers on VPC](https://cloud.ibm.com/docs/vpc?topic=vpc-planning-for-bare-metal-servers).

## What is created
{: #terraform-sap-hana-vsi-created}

The scripts automate the deployment of the virtual infrastructure resources and the provisioning processes for SAP architecture in an existing VPC. An SAP HANA DB on a virtual server instance or bare metal server is provisioned in VPC. The deployment happens in two phases in one run.

During the first phase, a virtual server instance or bare metal server with SAP HANA certified storage and network configuration is created. The security group and a subnet used for the deployment server (bastion server) are required.

During the second phase, the Ansible playbooks are run and the SAP HANA 2.0 architecture is installed on the SAP HANA VSI box or on the SAP HANA bare metal server.

There are two types of servers where this solution can be deployed on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC): Virtual Server Instances (VSIs) and Bare Metal servers. For each server type, there are specific profiles that are certified by SAP. A profile is a combination of instance attributes, such as the number of vCPUs, amount of RAM, network bandwidth, and default bandwidth allocation. The attributes define the size and capabilities of the virtual server instance that is provisioned.

### x86-64 virtual server instance profiles
{: #x86-64-instance-profiles}

For {{site.data.keyword.cloud}} virtual servers for VPC, there are eight profile families:

* Balanced
* Compute
* Memory
* Very High Memory
* Ultra High Memory
* GPU
* Storage Optimized
* Confidential Compute

For more information, see [x86-64 instance profiles](/docs/vpc?topic=vpc-profiles&interface=ui).

When you provision {{site.data.keyword.cloud}} virtual server instances for SAP HANA on VPC, you can select from three families of certified profiles: Memory Optimized, Very High Memory Optimized and Ultra High Memory Optimized. For more information about virtual server instances certified profiles for SAP HANA, see [Intel Virtual Server certified profiles on VPC infrastructure for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc).

### x86-64 bare metal server profiles
{: x86-64-bare-metal-server-profiles}

{{site.data.keyword.cloud_notm}} Bare Metal Servers on VPC Infrastructure are dedicated bare metal servers that provide enhanced networking and connectivity through [Virtual Private Cloud (VPC)](https://www.ibm.com/topics/vpc?_gl=1*164pqfh*_ga*MTU0MzcxNzExMS4xNjcxNzI2MjI4*_ga_FYECCCS21D*MTY4NjY3MDgzOC43NC4xLjE2ODY2NzE2OTEuMC4wLjA.&_ga=2.80093343.964139216.1686574517-1543717111.1671726228) capabilities. For more information on bare metal servers, see [IBM Cloud Bare Metal Servers](https://www.ibm.com/products/bare-metal-servers?_gl=1*164pqfh*_ga*MTU0MzcxNzExMS4xNjcxNzI2MjI4*_ga_FYECCCS21D*MTY4NjY3MDgzOC43NC4xLjE2ODY2NzE2OTEuMC4wLjA.&_ga=2.80093343.964139216.1686574517-1543717111.1671726228).

The bare metal servers on VPC are available as an integrated part of [{{site.data.keyword.cloud}}](https://www.ibm.com/cloud?_gl=1*opgtue*_ga*MTU0MzcxNzExMS4xNjcxNzI2MjI4*_ga_FYECCCS21D*MTY4NjY3MDgzOC43NC4xLjE2ODY2NzE2OTEuMC4wLjA.&_ga=2.55889827.964139216.1686574517-1543717111.1671726228). For more information, see [x86-64 bare metal server profiles](/docs/vpc?topic=vpc-bare-metal-servers-profile&interface=ui) on {{site.data.keyword.cloud}}.
 
When you provision an {{site.data.keyword.cloud}} bare metal server for SAP HANA on VPC, you can select from four families of certified profiles:
* Balanced
* Compute Optimized
* Memory Optimized
* Ultra High Memory Optimized

For more information about the bare metal server certified profiles for SAP HANA, see [Bare metal server certified profiles on VPC infrastructure for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-bm-vpc).

## SAP HANA server configuration
{: #terraform-sap-hana-vsi-config}
{: terraform}

The following operating systems and OS images are supported:

* Red Hat Enterprise Linux 8.6 for SAP HANA (amd64) - images: `ibm-redhat-8-6-amd64-sap-hana-<x>`
* Red Hat Enterprise Linux 8.4 for SAP HANA (amd64) - images: `ibm-redhat-8-4-amd64-sap-hana-<x>`
* SUSE Linux Enterprise Server 15 SP 4 for SAP Applications (amd64) - images: `ibm-sles-15-4-amd64-sap-hana-<x>`
* SUSE Linux Enterprise Server 15 SP 3 for SAP Applications (amd64) - images: `ibm-sles-15-3-amd64-sap-hana-<x>`

The provided SSH keys are used to access the SAP HANA server via SSH, as a root user. The storage volumes for SAP HANA on VSI are configured based on the [Intel Virtual Server certified profiles on VPC infrastructure for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc). The storage volumes for SAP HANA on Bare Metal Server are configured based on the [Bare metal servers certified profiles on VPC infrastructure for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-bm-vpc).

## Script files
{: #terraform-sap-hana-vsi-files}

The deployment automation script files are available in the [GitHub repository](https://github.com/IBM-Cloud/sap-hana-db).

The scripts use the information that you provide for an existing VPC and deploy SAP HANA DB on a VSI or on a Bare Metal Server.

The Terraform scripts run the Ansible Playbooks to install the SAP architecture.

## Schematics deployment
{: #ui-sap-hana-vsi-schematics}
{: ui}

When you use the Schematics interface to deploy SAP HANA, you need to provide the following information:

* the workspace information
* the GitHub repository path
* the value for the parameters in the Schematics interface

## Support
{: #terraform-sap-hana-vsi-support}

There are no warranties of any kind, and there is no service or technical support available for these materials from {{site.data.keyword.IBM}}. As a recommended practice, review carefully any materials that you download from this site before using them on a live system. 

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, readapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Before you begin
{: #automate-hana-standalone-vsi-before}

If you don't have a deployment server (bastion server) in the same VPC, create a deployment server to store the SAP kits. For more information, see [Automate SAP bastion server - SAP media storage repository](https://test.cloud.ibm.com/docs/sap?topic=sap-sap-bastion-server).

*	Log in to your Deployment Server and verify that Terraform and Ansible are installed. 
*	Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. Ansible decompresses all of the archive kits. For more information, see the [Readme](https://github.com/IBM-Cloud/sap-hana-db/blob/main/README.md) file.
*  [Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey#create_user_key). The API key is used to authenticate with the {{site.data.keyword.cloud_notm}} platform and to determine your permissions for {{site.data.keyword.cloud_notm}} services.
*  [Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.
*  Required IAM permissions for deploying SAP HANA on Bare Metal: Bare Metal Console Administrator role to access the ESXi Direct Console User Interface (DCUI) and Bare Metal Advanced Network Operator role to modify IP spoofing and infrastructure NAT configuration on network interfaces, see: https://cloud.ibm.com/docs/vpc?topic=vpc-planning-for-bare-metal-servers.

## Procedure
{: #automate-hana-standalone-vsi-procedure}
{: terraform}

1.	Log in to the Deployment Server (Bastion server) by using `ssh` command.
2.	Clone the GitHub repository from https://github.com/IBM-Cloud/sap-hana-db and go to the **sap-hana-db** folder.

    ```shell
	$ git clone https://github.com/IBM-Cloud/sap-hana-db.git
    	
	$ cd sap-hana-db
    ```

3.	Customize the VPC variables according to your existing VPC data. Modify the `input.auto.tfvars` file to specify your options. You need a 40-digit SSH key ID for the deployment. Additional SSH key IDs are optional. For more information related to SAP HANA certified profiles, see [Intel Virtual Server certified profiles on VPC infrastructure for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc) and [Bare metal servers certified profiles on VPC infrastructure for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-bm-vpc). For more options about images, see [Images](https://test.cloud.ibm.com/docs/vpc?topic=vpc-about-images).

    The following input variable values must be provided:

    * REGION - Region for SAP HANA server. See the [Readme](https://github.com/IBM-Cloud/sap-hana-db/blob/main/README.md) file.
    * ZONE - Zone for SAP HANA server. See the [Readme](https://github.com/IBM-Cloud/sap-hana-db/blob/main/README.md) file.
    * VPC - The name of an existing VPC in the specified region.
    * SECURITY_GROUP - The name of an existing Security group in the same VPC.
    * RESOURCE_GROUP - The name of an existing Resource group, previously created by the user.
    * SUBNET - The name of an existing Subnet in the same region and zone as the SAP HANA server.
    * SSH_KEYS - A list of SSH keys UUIDs allowed to connect through SSH to the SAP HANA server.
    * ID_RSA_FILE_PATH - existing id_rsa private key file path in OpenSSH format with 0600 permissions.
    * HANA_SERVER_TYPE – The type of SAP HANA server: "virtual" server instance or "bare metal" server.
    * DB_HOSTNAME - The hostname of the SAP HANA server, up to 13 characters. For more information, see the [Readme](https://github.com/IBM-Cloud/sap-hana-db/blob/main/README.md) file.
    * DB_PROFILE – The server certified profile to be used for SAP HANA. See the [Readme](https://github.com/IBM-Cloud/sap-hana-db/blob/main/README.md) file.
    * ATR_NAME – The name of an existing Activity Tracker in the same region as the SAP HANA server.

    ``` terraform
    ######################################################
    # General VPC variables:
    ######################################################

    REGION = ""
    # Region for SAP HANA server. Supported regions: https://cloud.ibm.com/docs/containers?topic=containers-regions-and-zones#zones-vpc
    # Example: REGION = "eu-de"

    ZONE = ""
    # Availability zone for SAP HANA server. Supported zones: https://cloud.ibm.com/docs/containers?topic=containers-regions-and-zones#zones-vpc
    # Example: ZONE = "eu-de-1"

    VPC = ""
    # EXISTING VPC, previously created by the user in the same region as the SAP HANA server. The list of available VPCs: https://cloud.ibm.com/vpc-ext/network/vpcs
    # Example: VPC = "ic4sap"

    SECURITY_GROUP = ""
    # EXISTING Security group, previously created by the user in the same VPC. The list of available Security Groups: https://cloud.ibm.com/vpc-ext/network/securityGroups
    # Example: SECURITY_GROUP = "ic4sap-securitygroup"

    RESOURCE_GROUP = ""
    # EXISTING Resource group, previously created by the user. The list of available Resource Groups: https://cloud.ibm.com/account/resource-groups
    # Example: RESOURCE_GROUP = "wes-automation"

    SUBNET = ""
    # EXISTING Subnet in the same region and zone as the SAP HANA server, previously created by the user. The list of available Subnets: https://cloud.ibm.com/vpc-ext/network/subnets
    # Example: SUBNET = "ic4sap-subnet"

    SSH_KEYS = [""]
    # List of SSH Keys UUIDs that are allowed to SSH as root to the SAP HANA server. The SSH Keys should be created for the same region as the SAP HANA server. The list of available SSH Keys UUIDs: https://cloud.ibm.com/vpc-ext/compute/sshKeys
    # Example: SSH_KEYS = ["r010-8f72b994-c17f-4500-af8f-d05680374t3c", "r011-8f72v884-c17f-4500-af8f-d05900374t3c"]

    ID_RSA_FILE_PATH = "ansible/id_rsa"
    # Your existing id_rsa private key file path in OpenSSH format with 0600 permissions.
    # This private key it is used only during the terraform provisioning and it is recommended to be changed after the SAP deployment.
    # It must contain the relative or absoute path from your Bastion.
    # Examples: "ansible/id_rsa_s4hana" , "~/.ssh/id_rsa_s4hana" , "/root/.ssh/id_rsa".
    ```

    ``` terraform
    ##########################################################
    # SAP HANA Server variables:
    ##########################################################

    HANA_SERVER_TYPE = ""
    # The type of SAP HANA Server. Allowed vales: "virtual" or "bare metal"
    # Example: HANA_SERVER_TYPE = "bare metal"

    DB_HOSTNAME = ""
    # The Hostname for the DB VSI. The hostname must be up to 13 characters, as required by SAP
    # Example: DB_HOSTNAME = "icp4sapdb"

    DB_PROFILE = ""
    # The profile used for SAP HANA Server. 
    # The list of certified profiles for SAP HANA Virtual Servers is available here: https://cloud.ibm.com/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc
    # The list of certified profiles for SAP HANA Bare Metal Servers is available here: https://cloud.ibm.com/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-bm-vpc. 
    # Details about all x86 instance profiles are available here: https://cloud.ibm.com/docs/vpc?topic=vpc-profiles.
    # Example of Virtual Server Instance profile for SAP HANA: DB_PROFILE ="mx2-16x128". 
    # Example of Bare Metal profile for SAP HANA: DB_PROFILE = "bx2d-metal-96x384". 
    # For more information about supported DB/OS and IBM VPC, check SAP Note 2927211: "SAP Applications on IBM Virtual Private Cloud".

    DB_IMAGE = "ibm-redhat-8-6-amd64-sap-hana-5"
    # OS image for DB Server. Validated OS images for SAP HANA Server: ibm-redhat-8-6-amd64-sap-hana-5, ibm-redhat-8-4-amd64-sap-hana-9, ibm-sles-15-4-amd64-sap-hana-6, ibm-sles-15-3-amd64-sap-hana-9.
    # The list of available VPC Operating Systems supported by SAP: SAP note '2927211 - SAP Applications on IBM Virtual Private Cloud (VPC) Infrastructure environment' https://launchpad.support.sap.com/#/notes/2927211; The list of all available OS images: https://cloud.ibm.com/docs/vpc?topic=vpc-about-images
    # Example: DB_IMAGE = "ibm-sles-15-4-amd64-sap-hana-6"
    ##########################################################
    # Activity Tracker variables:
    ##########################################################

    ATR_NAME = ""
    # The name of an existent Activity Tracker instance, in the same region chosen for SAP system deployment.
    # Example: ATR_NAME="Activity-Tracker-SAP-eu-de"
    ```

    The hostname must be up to 13 characters as required by SAP. For more information about the rules that apply to hostnames for SAP systems, see SAP Note 611361 - Hostnames of SAP ABAP Platform servers.

4.	Customize your SAP system configuration. Modify the `input.auto.tfvars` file to specify SAP HANA system configuration and enter the location of the downloaded SAP HANA Kit file.

    ``` terraform
    ##########################################################
    # SAP HANA configuration
    ##########################################################

    HANA_SID = "HDB"
    # SAP HANA system ID. Should follow the SAP rules for SID naming.
    # Example: HANA_SID = "HDB"

    HANA_SYSNO = "00"
    # SAP HANA instance number. Should follow the SAP rules for instance number naming.
    # Example: HANA_SYSNO = "01"

    HANA_SYSTEM_USAGE = "custom"
    # System usage. Default: custom. Suported values: production, test, development, custom
    # Example: HANA_SYSTEM_USAGE = "custom"

    HANA_COMPONENTS = "server"
    # SAP HANA Components. Default: server. Supported values: all, client, es, ets, lcapps, server, smartda, streaming, rdsync, xs, studio, afl, sca, sop, eml, rme, rtl, trp
    # Example: HANA_COMPONENTS = "server"

    KIT_SAPHANA_FILE = "/storage/HANADB/SP07/Rev73/51057281.ZIP"
    # SAP HANA Installation kit path
    # Example for Red Hat 8 or Suse 15: KIT_SAPHANA_FILE = "/storage/HANADB/SP07/Rev73/51057281.ZIP"

    HDB_CONCURRENT_JOBS = "23"
    # Number of concurrent jobs used to load and/or extract archives to HANA Host
    ```
  
5. Initialize the Terraform CLI. 

    ``terraform init``

6.	Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account.

    ``terraform plan --out plan1``

    You are asked to enter the IBM Cloud API key and the SAP HANA main password.

    The SAP HANA main password must contain at least one digit (0-9), one lowercase letter (a-z), and one uppercase letter (A-Z). It can only contain the following characters: a-z, A-Z, 0-9, !, @, #, $, _. It must not start with a digit or an underscore ( _ ).

7. Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the input.auto.tfvars file to correct resources and run `terraform plan --out plan1` again.

8. Deploy the SAP solution:

    ``terraform apply "plan1"``

## Deploying SAP HANA DB on VSI or Bare Metal Server by using the Schematics user interface
{: #automate-hana-standalone-vsi-ui}
{: ui}

Use these steps to configure the SAP HANA DB single VSI on your existing VPC by using the Schematics user interface. The scripts can take 1 - 2 hours to complete.

1. From the {{site.data.keyword.cloud_notm}} menu, select [Schematics](https://cloud.ibm.com/schematics/overview).
2. Click **Create workspace**.
3. On the **Specify template** page:
    * Enter the GitHub URL for the code you plan to deploy.
    * Select the **Terraform version** that is listed in the [Readme](https://github.com/IBM-Cloud/sap-hana-db/blob/main/README.md) file.
    * Click **Next**.
4. On the **Workspace details** page:
    * Enter a name for the workspace.
    * Select a **Resource group**.
    * Select a **Location** for your workspace. The workspace location does not have to match the resource location.
    * Click **Next**.
5. Select **Create** to create your workspace.
6. On the workspace **Settings** page, in the Input variables section, review the default input variables and provide values that match your solution:
    * Your IBM Cloud API key.
    * A private SSH key to be used for the deployment.
    * (Optional) You can change the `ID_RSA_FILE_PATH` for your SSH key file that will be generated on Schematics and on the bastion server.
    * The ID(s) of the SSH key(s) that you created and uploaded to IBM Cloud. Enter the SSH key ID(s) in [] brackets and surrounded by quotation marks, for example [ "ibmcloud_ssh_key_UUID1", "ibmcloud_ssh_key_UUID2",... ].
    * The floating IP address of the bastion server (deployment server).
    * The resource group where the IBM Cloud resources are created.
    * The region for your resources.
    * The zone for your resources.
    * Existing VPC name
    * Existing Subnet name
    * Existing Security group name
    * SAP HANA server type: virtual server instances or bare metal
    * Hostname
    * Profile
    * Image
    * Existing Activity Tracker name
    * SAP HANA SID
    * SAP HANA main password - must be at least 10 characters, upper, and lowercase letters, a number, and a special character, not an exclamation point.
    * SAP HANA system number
    * SAP HANA system usage
    * SAP HANA components
    * SAP HANA kit file path
    * Number of SAP HANA concurrent jobs.
    * Click **Save** changes.

    For a more detailed description of the parameters, check the GitHub repo [Readme](https://github.com/IBM-Cloud/sap-hana-db/blob/main/README.md) file, chapter [General Input Variables](https://github.com/IBM-Cloud/sap-hana-db/blob/main/README.md#15-general-input-variables). Also, make sure to mark as “sensitive” the parameters that contain sensitive information like passwords, IBM Cloud API, the SSH private key and SAP HANA password (they are marked as “sensitive” in the [Readme](https://github.com/IBM-Cloud/sap-hana-db/blob/main/README.md) file in the [General Input Variables](https://github.com/IBM-Cloud/sap-hana-db/blob/main/README.md#15-general-input-variables)).

7. On the workspace **Settings** page, click **Generate** plan. Wait for the plan to complete.
8. Click **View log** to review the log files of your Terraform execution plan.
9. Apply your Terraform template by clicking **Apply plan**.
10. Review the log file to ensure that no errors occurred during the provisioning, modification, or deletion process.

## Next steps
{: #automate-hana-standalone-vsi-next}

Do not use the IBM Cloud Dashboard and user interface to modify your resources after they are created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results.

If you need to remove the resources created with the automation for your SAP solution, go to your project folder and run `terraform destroy`.

## Related information
{: #automate-hana-standalone-vsi-related}

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Terraform on IBM Cloud getting started tutorial](https://test.cloud.ibm.com/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

For more information about using Terraform for creating only a VPC for SAP, without the SAP architecture, see [Creating single-tier VPC for SAP on IBM Cloud® VPC with Terraform](https://test.cloud.ibm.com/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).

SAP One Support Notes that apply to this document:

*	[SAP Note 84555 - Windows Server, Linux&reg;, and UNIX: Certified hardware](https://launchpad.support.sap.com/#/notes/84855)
*	[SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
*	[SAP Note 2923773 - Linux&reg; on IBM Cloud (IaaS): Adaption of your SAP license](https://launchpad.support.sap.com/#/notes/2923773)
*	[SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)
*	[SAP Note 2369910 - SAP Software on Linux&reg;: General information](https://launchpad.support.sap.com/#/notes/2369910)
*	[SAP Note 171380 - Released IBM hardware (Intel processors) and IBM cloud services offers](https://launchpad.support.sap.com/#/notes/171380)
*	[SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)

This document is referenced by:

*	[SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
*	[SAP Note 2588225 - SAP on IBM Cloud: Protect against speculative execution vulnerabilities](https://launchpad.support.sap.com/#/notes/2588225)
*	[SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)
*	[SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)
