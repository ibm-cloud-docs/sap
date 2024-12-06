---

copyright:
  years: 2024
lastupdated: "2024-12-06"

subcollection: sap

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip} 
{:ui: .ph data-hd-interface="ui"}
{:terraform: .ph data-hd-interface="terraform"}

# Automation of Cross-Region Disaster Recovery Protection Enablement for SAP HANA in IBM Cloud VPC
{: #automate-hana-db-crossregion-dr}

You can use Terraform scripts and Ansible playbooks to create the required setup to deploy a non-HA SAP HANA secondary database system on a VSI in a different region than the primary system. Enable the connection with SAP HANA primary system, make the necessary configuration for Disaster Recovery (DR) protection enablement and start the replication. Terraform on {{site.data.keyword.cloud_notm}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) infrastructure resources so that you can rapidly build complex cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware that uses Intel® Xeon CPUs and additional Intel® technologies.
{: shortdesc}

The Terraform scripts deploy the {{site.data.keyword.cloud_notm}} VPC infrastructure resources based on the information that you provide and then call the Ansible playbooks to create the SAP architecture on the specified VPC. The Ansible playbooks are used for LVM configuration, OS setup and for the SAP HANA secondary system installation along with the DR setup and enablement.

You have two deployment methods to choose from:

* Terraform scripts run from the CLI on your Deployment Server (Bastion Server).
* Schematics user interface accessed from your cloud dashboard menu.

You can store the SAP HANA db backup into the Cloud Object Storage.

## SAP Solution implemented
{: #automate-hana-db-crossregion-dr-solution-implement}

The automation module for cross-region DR protection enablement of a non-HA SAP HANA on VSI is developed as a stand-alone automation module that can be used for any already deployed SAP solution running on SAP HANA database.

## What is created
{: #automate-hana-db-crossregion-dr-create}

The Disaster Recovery Protection is configured and enabled and a SAP HANA secondary system is deployed on one of the following operating systems:

* SUSE Linux Enterprise Server 15 SP 4 for SAP
* SUSE Linux Enterprise Server 15 SP 3 for SAP
* Red Hat Enterprise Linux 8.6 for SAP
* Red Hat Enterprise Linux 8.4 for SAP

## Script files
{: #automate-hana-db-crossregion-dr-script-files}
{: terraform}

The configuration and script files are available in [SAP HANA DR GitHub repository](https://github.com/IBM-Cloud/sap-hana-dr){: external}.

## Terraform interface
{: #automate-hana-db-crossregion-dr-interface}
{: terraform}

To run the Terraform script, you should modify:

* The `input.auto.tfvars` file, to specify the VPC resources for your solution as well as the SAP parameters. You should provide the values for the following variables: 
    * VPC name of the primary SAP HANA system
    * VPC name of the secondary SAP HANA system (new or existing)
    * Region of the primary SAP HANA system
    * Region for the secondary SAP HANA system
    * Zone for the secondary SAP HANA system
    * Security group of the primary SAP HANA system
    * Subnet for the secondary SAP HANA system (new or existing)
    * Bastion Subnet 
    * Resource Group
    * Transit Gateway (new or existent)
    * HostName(s) for the primary and secondary (new) SAP HANA system
    * SSH Keys UUIDs allowed to connect to the primary SAP HANA system
    * SSH Keys UUIDs allowed to connect to the secondary SAP HANA system
    * Profile of the primary SAP HANA system instance
    * OS image of the primary SAP HANA system

You can change the default SAP system configuration settings to match your solution. You must also specify the location where you downloaded the SAP kits.

## Schematics deployment
{: #automate-hana-db-crossregion-dr-schematics}
{: ui}

The configuration and script files are provided in [GitHub](https://github.com/IBM-Cloud/sap-hana-dr){: external} repository. When the Schematics interface is used, the following information should be provided:

* the workspace information.
* the [GitHub URL](https://github.com/IBM-Cloud/sap-hana-dr){: external} of the solution.
* the values for the parameters in the Schematics interface; they are the same as the parameters from `input.auto.tfvars` file, which are used in CLI.

## Support
{: #automate-hana-db-crossregion-dr-support}

There are no warranties of any kind, and there is no service or technical support available for these materials from {{site.data.keyword.IBM}}. As a recommended practice, review carefully any materials that you download from this site before using them on a live system. 

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, readapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Virtual server instance configuration
{: #automate-hana-db-crossregion-dr-config}
{: ui}

This solution can be used for the DR protection of a non-HA SAP HANA database deployed on a VSI in an existing VPC. This an be done by creating a SAP HANA secondary system in a different region by configuring and enabling the DR protection. For more information, see the SAP automated HANA family solutions:

* https://github.com/IBM-Cloud/sap-netweaver-abap-hana
* https://github.com/IBM-Cloud/sap-netweaver-java-hana
* https://github.com/IBM-Cloud/sap-s4hana
* https://github.com/IBM-Cloud/sap-bw4hana

## Before you begin
{: #automate-hana-db-crossregion-dr-before}

Before you use the scripts:

* A Deployment Server (Bastion Server) must exist in the same VPC as the primary SAP HANA system. For more information about how to create the Deployment Server (Bastion Server) and its corresponding VPC, see [Automate SAP bastion server - SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server).
* Terraform should be already installed on the deployment server (bastion server) that you deployed. For more information, see the [Bastion server for SAP deployment](/docs/sap?topic=sap-sap-bastion-server).{: terraform}
* Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the downloaded locations. Ansible decompresses the files. For more information, see the [Readme](https://github.com/IBM-Cloud/sap-hana-backup-cos/blob/main/README.md){: external} file.{: terraform}
* [Create or retrieve an IBM Cloud API key](https://cloud.ibm.com/docs/account?topic=account-userapikey&interface=ui#create_user_key){: external}. The API key is used to authenticate with the create or retrieve your SSH key ID.{: terraform}
* Log in to the {{site.data.keyword.cloud_notm}} platform to determine your permissions for {{site.data.keyword.cloud_notm}} services.{: terraform}
* A pair of SSH keys must be generated for the connection to the VSIs. The public key must be uploaded in {{site.data.keyword.cloud_notm}} and manually added on SAP HANA primary system VSI in `/root/.ssh/authorized_keys`.
* A deployed non-HA SAP HANA primary system, on a VSI, must exist in a different region than the one chose for SAP HANA secondary system (built on one of the following OS: SUSE Linux Enterprise Server 15 SP 4 for SAP, SUSE Linux Enterprise Server 15 SP 3 for SAP, Red Hat Enterprise Linux 8.6 for SAP or Red Hat Enterprise Linux 8.4 for SAP) in an {{site.data.keyword.cloud_notm}} Gen2 VPC, on a single host (with or without HA).
* A full database backup must be performed on the primary system for the system database and all tenant databases.

## SAP HANA cross-region DR Protection Enablement on an existing IBM Cloud VPC using the CLI
{: #automate-hana-db-crossregion-dr-deploy-cli}
{: terraform}

Use these steps and the “Disaster Recovery Protection Solution for SAP HANA System” automation module to configure the disaster recovery protection of a non-HA SAP HANA system on VSI into a different region then primary SAP HANA 2.0 DB instance.

The script takes 40 minutes to complete after a fresh install of SAP HANA primary system.

1. Access the Deployment Server (Bastion Server) CLI.

2. Clone the solution repository from https://github.com/IBM-Cloud/sap-hana-dr

    `git clone https://github.com/IBM-Cloud/sap-hana-dr.git`

3. Change directory to sap-hana-dr

    `cd sap-hana-dr`

4. Modify the `input.auto.tfvars` file to specify the information for the infrastructure resources and SAP. For descriptions of the variables, see the [Readme](https://github.com/IBM-Cloud/sap-hana-dr/blob/main/README.md){: external} file. The VSI OS images that are validated for this solution are:
    * SUSE Linux Enterprise Server 15 SP 4 for SAP
    * SUSE Linux Enterprise Server 15 SP 3 for SAP
    * Red Hat Enterprise Linux 8.6 for SAP or
    * Red Hat Enterprise Linux 8.4 for SAP

    The OS distribution and version for the secondary SAP HANA system should match the OS distribution and version for the primary SAP HANA system.{: note}

    Example:

    ```terraform
    ######################################################################
    # Existing PRIMARY VPC variables before that DR Configs to take place
    ######################################################################
    REGION = "eu-de"
    VPC = "ic4sap"
    SECURITY_GROUP = "ic4sap-securitygroup"
    BASTION_SUBNET = "ic4sap-subnet"
    SSH_KEYS = ["r010-5db21872-c98f-4945-9f69-71c637b1da50"]
    ##########################################################
    # RESOURCE GROUP for all resources
    ##########################################################
    RESOURCE_GROUP = "wes-automation"
    ##########################################################
    # id_rsa private key file path for all VSIs
    ##########################################################
    ID_RSA_FILE_PATH = "/root/.ssh/id_rsa"
    ##########################################################
    # VPC variables for DR Site
    ##########################################################
    ```

    ```
    REGION_DR = "eu-gb"
    ZONE_DR = "eu-gb-1"
    VPC_DR = "ic4sap-dr"
    VPC_DR_EXISTS = "no"
    SUBNET_DR = "ic4sap-subnetdr"
    SSH_KEYS_DR = ["r010-6dl2g976-c97f-7935-8dd9-72c637g1ja31"]
    ```

    ```terraform
    ##########################################################
    # IBM TRANSIT GATEWAY variables
    ##########################################################
    TRANSIT_GATEWAY = "ic4sap-tg"
    TRANSIT_GATEWAY_EXISTS = "no"
    SUBNETS_ALREADY_ADDED_ON_TG = "no"
    ##########################################################
    # SAP HANA Servers variables
    ##########################################################
    HANA_SERVER_TYPE = "virtual"
    DB_HOSTNAME_PRIMARY = "hanadb-vsi1"
    DB_HOSTNAME_DR = "hanadb-vsi2"
    DB_PROFILE = "mx2-16x128"
    DB_IMAGE = "ibm-redhat-8-6-amd64-sap-hana-6"
    ```

5. Customize your SAP system configuration. Edit the same `input.auto.tfvars` file by specifying the values for the SAP system configuration variables that are passed to the Ansible playbooks. For descriptions of the variables, see the [Readme](https://github.com/IBM-Cloud/sap-hana-dr/blob/main/README.md){: external} file.

    ```terraform
    ##########################################################
    # SAP HANA configuration
    ##########################################################
    KIT_SAPHANA_FILE = "/storage/HANADB/SP07/Rev73/51057281.ZIP"
    ```

6. Initialize the Terraform CLI.

    `terraform init`

7. Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are performed by the automation scripts based on the input variables.

    `terraform plan --out plan1`

    You should enter an SAP HANA main password (which should be the same for all your HANA SYSTEM db users) and your API key. The SAP main password should be 10 - 14 characters long and contain at least one digit (0-9). It can contain only the following characters: a-z, A-Z, 0-9, @, #, $, . This password cannot contain exclamation points '!'. The password should not start with a digit or an underscore ().

8. Verify that the plan shows all the resources that you want to create, the names and values are correct. If the plan needs to be adjusted, edit the `input.auto.tfvars` file to correct resources and run the terraform plan again.

9. Apply the saved terraform plan.

    `terraform apply "plan1"`

    Resources are created and you see the output similar to the terraform plan output.

10. Add the SAP credentials and the virtual server instance IP to the SAP GUI or HANA STUDIO. For more information about the SAP GUI, see SAP GUI.

## SAP HANA cross-region DR Protection Enablement in IBM Cloud VPC using the Schematics user interface
{: #automate-hana-db-crossregion-dr-deploy-schematics}
{: ui}

Follow these steps to enable cross-region DR protection for a non-HA SAP HANA System, on VSI, using the Schematics interface. The script takes 40 minutes to complete after a SAP HANA fresh install of the primary system.

1. From the {{site.data.keyword.cloud_notm}} menu, select **{{site.data.keyword.bpshort}}**.
2. Click **Create workspace**.
3. On the **Specify template** page:
    *   Enter the [GitHub URL](https://github.com/IBM-Cloud/sap-hana-dr){: external} for the github repository that contains the {{site.data.keyword.bpshort}} code for this offering.
    *   Select the **Terraform version** that is listed in the [Readme](https://github.com/IBM-Cloud/sap-hana-dr/blob/main/README.md){: external} file.
    *   Click **Next**.
4. On the **Workspace details** page:
    *   Enter a name for the workspace.
    *   Select a **Resource group**.
    *   Select a **Location** for your workspace. The workspace location does not have to match the resource location.
    *   Select **Next**.
5. Select **Create** to create your workspace.
6. On the workspace settings page, in the input variables section, review the default input variables and provide values that match your solution.

    |Parameter	|Description|
    |-----|-----|
    |IBMCLOUD_API_KEY	|{{site.data.keyword.cloud_notm}} API key (Sensitive* value).|
    |SSH_KEYS	|List of {{site.data.keyword.cloud_notm}} SSH Keys UUIDs from primary HANA region, that are allowed to connect via SSH, as root, to the primary SAP HANA system.|
    |BASTION_SUBNET	|The name of an existing subnet that belongs to the bastion VSI in the same VPC where the SAP HANA primary system is deployed.|
    |REGION	|The cloud region where the SAP HANA primary system is deployed. |
    |VPC	|The name of an existing VPC where the SAP HANA Primary System is deployed.|
    |SECURITY_GROUP	|The name of an existing Security group for the VPC where the SAP HANA primary system is deployed.|
    |REGION_DR	|The cloud region where to deploy the SAP HANA secondary system.|
    |ZONE_DR	|The cloud zone where to deploy the SAP HANA secondary system. |
    |VPC_DR	|The name of a new or existing VPC for the SAP HANA secondary system, in the same region as the SAP HANA secondary system.|
    |VPC_DR_EXISTS	|Specifies if the VPC for DR purpose, having the provided name, already exists.|
    |SUBNET_DR	|The name of a NEW or EXISTING Subnet in the same VPC and same zone as SAP HANA secondary system.|
    |SSH_KEYS_DR	|List of {{site.data.keyword.cloud_notm}} SSH Keys UUIDs from VPC DR region, that are allowed to connect via SSH, as root, to the SAP HANA secondary system.|
    |TRANSIT_GATEWAY	|The name of a new or an existing IBM transit gateway for the VPC where the SAP HANA primary system is deployed.|
    |TRANSIT_GATEWAY_EXISTS	|Specifies if the IBM transit gateway for the VPC where the SAP HANA primary system is deployed, having the provided name, already exists. |
    |SUBNETS_ALREADY_ADDED_ON_TG	|Specifies if the subnets from the primary VPC and from DR VPC are already added to the transit gateway as connections.|
    |DB_HOSTNAME_PRIMARY	|The hostname of SAP HANA primary system.|
    |DB_HOSTNAME_DR	|The hostname for SAP HANA secondary system that will be deployed in the VPC_DR.|
    |DB_PROFILE	|The instance profile of SAP HANA primary server.|
    |DB_IMAGE	|The OS image of SAP HANA primary server.|

    **{{site.data.keyword.cloud_notm}} resources input parameters (Optional):**

    |Parameter	|Description|
    |-----|-----|
    |ID_RSA_FILE_PATH	|The file path for PRIVATE_SSH_KEY. It will be automatically generated. Default value: `“ansible/id_rsa”`|
    |RESOURCE_GROUP	|The name of an existing resource group. Default value: “Default”|
    |HANA_SERVER_TYPE	|The type of SAP HANA server. Supported values: "virtual"|

    **Required SAP input parameters:**

    |Parameter	|Description|
    |-----|-----|
    |HANA_MAIN_PASSWORD	|HANA system master password (Sensitive* value). |
    |KIT_SAPHANA_FILE	|SAP HANA installation kit path.|

    For more detailed description of each parameters, check the GitHub repo [Readme](https://github.com/IBM-Cloud/sap-hana-dr/blob/main/README.md){: external} file, chapter “Input parameter file”. Also, ensure to mark the parameters that contain sensitive information like passwords, API, and ssh private keys as "sensitive". These parameters are marked as “sensitive” in the readme file, under the Input parameter file.
    Save each parameter that you modify.

7. On the workspace settings page, click **Generate plan**. Wait for the plan to complete.
8. Click **View log** to review the log files of your Terraform execution plan.
9. Apply your Terraform template by clicking **Apply plan**.
10. Review the log file to ensure that no errors occurred during the provisioning, modification, or deletion process.

## Next steps
{: #automate-hana-db-crossregion-dr-next}
{: ui}

Do not use the {{site.data.keyword.cloud_notm}} dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results.

## Related information
{: #automate-hana-db-crossregion-dr-related}

For more information about the Terraform on {{site.data.keyword.cloud_notm}}, see [Getting started with Terraform on {{site.data.keyword.cloud_notm}}](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

For more information about using the Terraform for creating only a VPC for SAP, without the SAP architecture, see [Creating single-tier virtual private cloud for SAP by using Terraform](/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).

SAP One Support Notes that apply to this document:

*	[SAP Note 84555 - Windows Server, Linux&reg;, and UNIX: Certified hardware](https://me.sap.com/notes/84855){: external}
*	[SAP Note 2927211 - SAP Applications on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}
*	[SAP Note 2923773 - Linux&reg; on {{site.data.keyword.cloud_notm}} (IaaS): Adaption of your SAP License](https://me.sap.com/notes/2923773){: external}
*	[SAP Note 2414097 - SAP Applications on {{site.data.keyword.cloud_notm}} Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}
*	[SAP Note 2369910 - SAP Software on Linux&reg;: General information](https://me.sap.com/notes/2369910){: external}
*	[SAP Note 171380 - Released IBM hardware (Intel processors) and IBM cloud services offers](https://me.sap.com/notes/171380){: external}
*	[SAP Note 1380654 - SAP support in IaaS environments](https://me.sap.com/notes/1380654){: external}

This document is referenced by:

*	[SAP Note 2927211 - SAP Applications on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}
*	[SAP Note 2588225 - SAP on {{site.data.keyword.cloud_notm}}: Protect against speculative execution vulnerabilities](https://me.sap.com/notes/2588225){: external}
*	[SAP Note 1380654 - SAP support in IaaS environments](https://me.sap.com/notes/1380654){: external}
*	[SAP Note 2414097 - SAP Applications on {{site.data.keyword.cloud_notm}} Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}

This automation is offered at no cost; however, the provisioned infrastructure comes at cost.
{: note}
