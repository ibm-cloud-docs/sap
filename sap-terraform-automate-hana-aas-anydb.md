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

# VPC with Additional Application Server (AAS) ABAP on Linux for SAP HANA
{: #automate-hana-aas-anydb-terraform-ansible}

As of 28 March 2024, the {{site.data.keyword.at_full_notm}} service is deprecated and will no longer be supported as of 30 March 2025. Customers will need to migrate to {{site.data.keyword.logs_full_notm}} before 30 March 2025. During the migration period, customers can use {{site.data.keyword.at_full_notm}} along with {{site.data.keyword.logs_full_notm}}. Activity tracking events are the same for both services. For information about migrating from {{site.data.keyword.at_full_notm}} to {{site.data.keyword.logs_full_notm}} and running the services in parallel, see [migration planning](/docs/cloud-logs?topic=cloud-logs-migration-intro).
{: important}

You can use Terraform scripts to create a single-tier VPC and create the AAS to HANA and AnyDB infrastructure on the VPC. The Terraform scripts use the VPC information that you provide and then call the Ansible playbook to create the SAP architecture on the specified VPC. Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) infrastructure resources so that you can rapidly build complex, cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware that uses Intel&reg; Xeon CPUs and more Intel&reg; technologies.

You have two deployment methods to choose from:

*  Terraform scripts that run from the CLI on your bastion server.
* {{site.data.keyword.bpshort}} user interface accessed from your cloud dashboard menu.

You can create SAP AAS NetWeaver 7.x on the SAP HANA-based ABAP stack.

## SAP solution implemented
{: #ui-automate-hana-aas-anydb-solution}
{: ui}

Many SAP enterprise solutions are built on the SAP platform (SAP NetWeaver) including:

*  SAP HANA as Primary Persistence for SAP NetWeaver-based applications
*  SAP Business Suite applications (ERP, CRM, and SCM, and other applications),
*  SAP Business Warehouse (BW), and
*  Other SAP enterprise solutions

SAP NetWeaver has two distinct aspects, ABAP and Java. Many applications that are built on the SAP NetWeaver’s ABAP or Java (or both) application servers run on SAP DB owned HANA and ASE Sybase either in AnyDB platforms (MSSQL, Oracle, and Db2).

Technical interfaces are available for applications that are built on the SAP NetWeaver AS ABAP and AS Java to run on SAP HANA and AnyDB. However, specific development enablement is normally required for each application to ensure that it runs optimally on the SAP HANA. SAP Business Suite applications (ERP, CRM, SCM, and other applications), SAP Business Warehouse (BW), and other SAP NetWeaver-based applications were modified to run on SAP HANA and have many advantages. Also, various components and complimentary applications that are built on SAP NetWeaver can also run on SAP HANA or AnyDB by using the provided SAP NetWeaver DB interfaces.

The SAP HANA as primary persistence for SAP NetWeaver-based applications scenario has one restriction: 
SAP NetWeaver ABAP and Java application servers must run on separate hardware servers from the SAP HANA hardware.

## What is created
{: #ui-automate-hana-aas-anydb-what-created}
{: ui}

The scripts automate the virtual infrastructure resources, provisioning the processes for the SAP architecture in an existing VPC with a distributed environment. SAP AAS NetWeaver 7.x (HANA or ASE SYB) application server on a distinct VSI VPC system and SAP HANA DB on a dedicated server type VSI VPC box are provisioned. The scripts work in two phases.

During the first phase of [Automate SAP bastion server – SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server), the following virtual infrastructure resources based on the components from the existing VPC created by the bastion server are:
*  1 VPC where the virtual server instance is provisioned.
*  1 security group. The rules for this security group are: 
    *  Allow inbound DNS traffic (port 53).
    *  Allow inbound SSH traffic (TCP port 22).
    *  Allow all outbound traffic from the virtual server instance.
    *  Allow all traffic in the security group.
*  1 subnet to enable the networking in your VPC.
*  2 virtual server instances with SAP certified storage and network configurations.
*  1 floating IP address used to access your VPC virtual server instance over the public network.

During the second phase, the Ansible Playbooks is called and the SAP architecture is installed for both dedicated virtual server instance (VSI) SAP application; VSI system and dedicated SAP HANA VSI box. The SAP architecture that is deployed on the SAP NetWeaver 7.x release is a stand-alone dedicated SAP HANA 2.0 box release. For more information about this architecture, see [Automating SAP HANA stand-alone virtual server instance on {{site.data.keyword.cloud_notm}}® VPC by using Terraform and Ansible](/docs/sap?topic=sap-automate-terraform-sap-hana-vsi).

The [IBM Cloud Activity Tracker](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-getting-started) service should be used to capture the records of your {{site.data.keyword.cloud_notm}} activities and monitor the activity of your {{site.data.keyword.cloud_notm}} account. You can use this service to investigate abnormal activity, critical actions, and comply with regulatory audit requirements. In addition, you can be alert on the actions as they occur. The events that are collected comply with the Cloud Auditing Data Federation (CADF) standard.

You can deploy an Activity Tracker instance along with the SAP system by using the SAP deployment Automation or if you have already created one, you can specify the Activity Tracker name in the deployment variables. You can set the Activity Tracker plan variable according to your chosen [Service plans](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-service_plan). By default, the Lite (free) plan is selected. For more information on how to provision an Activity Tracker instance, see [here](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-getting-started).

Important:

* Every user who accesses the {{site.data.keyword.cloud_notm}} Activity Tracker service in your account must be assigned an access policy with an IAM user role defined. The policy determines what actions that the user can perform within the context of the service or instance you select. The allowable actions are customized and defined as operations that are allowed to be performed on the service. The actions are then mapped to IAM user roles. For more information, see [here](https://cloud.ibm.com/docs/services/activity-tracker?topic=activity-tracker-iam).

* You can provision only one instance of the service per {{site.data.keyword.cloud_notm}} region.

{{site.data.keyword.cloud_notm}} Activity Tracker provides a solution for administrators to capture, store, view, search, and monitor API activity in a single place. It also offers a notification feature to alert you by using any of the supported notification channels.

{{site.data.keyword.cloud_notm}} Activity Tracker collects and stores audit records for API calls made to resources that run in the {{site.data.keyword.cloud_notm}}. You can archive these events on {{site.data.keyword.cloud_notm}} for long-term storage.
{: note}

## Schematics deployment
{: #ui-automate-hana-aas-anydb-schematics-deployment}
{: ui}

When you run the scripts with the {{site.data.keyword.bpshort}} interface, you:

*  Enter the workspace information.
*  Enter the GitHub path for the chosen solution either on NetWeaver AAS for HANA.
*  Modify the parameters in the {{site.data.keyword.bpshort}} interface. They are the same parameters as the `input.auto.tfvars` file that you use with the cli.

## Virtual server instance configuration
{: #ui-automate-hana-aas-anydb-virtual-config}
{: #ui}

Following are the supported operating system images for SAP NetWeaver primary application server:

*  ibm-redhat-8-4-amd64-sap-applications-2
*  ibm-redhat-8-6-amd64-sap-applications-2
*  ibm-sles-15-3-amd64-sap-applications-2
*  ibm-sles-15-4-amd64-sap-applications-3

Following are the supported operating system images for SAP HANA database:

*  ibm-redhat-8-4-amd64-sap-hana-2
*  ibm-redhat-8-6-amd64-sap-hana-2
*  ibm-sles-15-3-amd64-sap-hana-2
*  ibm-sles-15-4-amd64-sap-hana-1

For both server instances there are:

*   Two SSH keys are configured to access SSH as `root`.
*   Three storage volumes as described in the `input.auto.tfvars` file.

## What is created
{: #automate-hana-aas-anydb-what-created}
{: terraform}

The scripts use the information that you provide for an existing VPC and deploy AAS to SAP HANA or AnyDB on a different host than CI (SAP Central Instance) VSI host. For more information about this architecture, see [SAP NetWeaver 7.x on UNIX with HANA or AnyDB on {{site.data.keyword.cloud_notm}} VPC on {{site.data.keyword.cloud_notm}} VPC](/docs/sap?topic=sap-sap-refarch-nw-sybase). You specify the information for the VPC to use in the `input.auto.tfvars` file.

The scripts call the Ansible Playbooks to install the SAP architecture.

## Script files
{: #automate-hana-aas-anydb-script-files}

The configuration and script files are provided on GitHub. Each supported interface for the SAP solution installation has its own folder in the GitHub repository:

*   [GitHub repository for Terraform – AAS HANA](https://github.com/IBM-Cloud/sap-abap-hana-aas)

## Terraform interface
{: #automate-hana-aas-anydb-terraform-interface}
{: terraform}

To run the Terraform script, you modify:

* The `input.auto.tfvars` file to specify the existing VPC resources for your solution. Specify the variables for the existing VPC:

    *   VPC name
    *   Security group
    *   Subnet
    *   Hostname
    *   Profile
    *   Image
    *   Up to two SSH keys

    You can change the default SAP system configuration settings to match your solution. You can also specify the location where you downloaded the SAP kits.

The {{site.data.keyword.cloud_notm}} Provider plug-in for Terraform on {{site.data.keyword.cloud_notm}} uses these configuration files to install AAS to SAP HANA and AnyDB on the specified VPC in your {{site.data.keyword.cloud_notm}} account.

## Support
{: #automate-hana-aas-anydb-support}

There are no warranties of any kind, and there is no service or technical support available for these materials from {{site.data.keyword.IBM}}. As a recommended practice, review carefully any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the {{site.data.keyword.IBM}} Service organization, your comments are welcomed by the developers, who reserve the right to revise, readapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Before you begin
{: #automate-hana-aas-anydb-before}

Before you use the scripts in the bastion cli:

* Set up your account to access the VPC. Make sure that your account is [upgraded to a paid account](/docs/account?topic=account-accountfaqs#changeacct).
* If you have not already, create a Bastion server to store the SAP kits. For more information, see [Automate SAP bastion server - SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server).
* Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. Ansible decompresses the files. For more information, see the [readme](https://github.com/IBM-Cloud/sap-abap-hana-aas#readme) file. 
* [Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey#create_user_key). The API key is used to authenticate with the {{site.data.keyword.cloud_notm}} platform and to determine your permissions for {{site.data.keyword.cloud_notm}} services.
* [Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.
* Terraform should already be installed on the bastion server that you deployed. For more information, see [Bastion server for SAP deployment](/docs/sap?topic=sap-sap-bastion-server).
* (Optional - Catalog Tile) create secrets for your credentials and passwords by using the [Secrets Manager](/docs/secrets-manager?topic=secrets-manager-arbitrary-secrets&interface=ui).
{: ui}

## Deploying SAP AAS NetWeaver 7.x on HANA by using the Schematics user interface
{: #ui-automate-hana-aas-anydb-schematics}
{: ui}

Use these steps to configure the SAP Additional Application Server (AAS) NetWeaver with HANA or AnyDB on your existing VPC by using the {{site.data.keyword.bpshort}} interface. The script takes 2 - 3 hours to complete.

1. From the {{site.data.keyword.cloud_notm}} menu, select **{{site.data.keyword.bpshort}}**.
2. Click **Create** workspace.
3. On the **Specify template** page:
     *  Enter the URL for the {{site.data.keyword.bpshort}} interface.
     *  Select the **Terraform version** that is listed in the readme file.
     *  Click **Next**.
4. On the **workspace details** page:
     *  Enter a name for the workspace.
     *  Select a **Resource group**.
     *  Select a **Location** for your workspace. The workspace location does not have to match the resource location.
   *  Select **Next**.
5. Select **Create** to create your workspace.
6. On the workspace settings page, in the input variables section, review the default input variables and provide values that match your solution.

    For a more detailed description of each parameter, check the GitHub repo [AAS HANA readme](https://github.com/IBM-Cloud/sap-abap-hana-aas) file, chapter “Input parameter file”. Also, make sure to mark the parameters that contain sensitive information like passwords, API, and ssh private keys as "sensitive". These parameters are marked as “sensitive” in the readme file, under “Input parameter file”.
7. On the workspace settings page, click **Generate plan**. Wait for the plan to complete.
8. Click **View log** to review the log files of your terraform execution plan.
9. Apply your Terraform template by clicking **Apply plan**.
10. Review the log file to ensure that no errors occur during the provisioning, modification, or deletion process.

## Deploying SAP AAS NetWeaver (ABAP) on HANA with the Deployable Architecture tile interface
{: #ui-automate-hana-aas-anydb-deploy-arch}
{: ui}

Use these steps to configure the SAP AAS NetWeaver (ABAP) on HANA on your existing VPC by using the catalog tile interface. The script takes 2 - 3 hours to complete.

1. From the {{site.data.keyword.cloud_notm}} catalog, select **VPC with Additional Application Server ABAP on Linux for SAP HANA** on HANA tile. The **Create** tab opens for VPC with Additional Application Server ABAP on Linux for SAP HANA. For more information about this deployment, see the About tab or the readme file link.
2. Select the latest version.
3. Select **VPC with Additional Application Server ABAP on Linux for SAP HANA on Deployable Architecture tile** variation.
4. Click **Review deployment** options:
   * **Add to project** to add this deployment to an {{site.data.keyword.cloud_notm}} project and combine it with other deployments. {{site.data.keyword.cloud_notm}} projects include several more pipeline steps before deployment, including deployment validation, cost calculation, compliance verification, and approval process.
   *  **Create from the CLI** to get the CLI command. With this command you can trigger the deployment from the CLI.
   * **Work with code** to embed the code into other terraform deployments.
   * **Deploy with {{site.data.keyword.cloud_notm}} {{site.data.keyword.bpshort}}** to trigger the deployment process directly.
5. Select **Deploy with IBM Cloud {{site.data.keyword.bpshort}}**. 
6. Add the input parameters for this installation. There are 3 categories of parameters:
    * **Workspace** - These parameters define the workspace that is automatically created in {{site.data.keyword.bpshort}}:
      * Enter a name for the workspace or use the default name.
      * The Resource Group used to create resources. Use default or create a Resource Group.
      * Select a location to create your {{site.data.keyword.bpshort}} workspace. The workspace location need not match the resource location.
    * **Required input variables** - Review the default input variables and provide values that match your solution. These parameters are specific to your deployment. For more detailed information, see the [Readme file](https://github.com/IBM-Cloud/sap-abap-hana-aas/blob/main/README.md).

    |Parameter	|Description|
    |-----|-----|
    |BASTION_FLOATING_IP	|Required only for Schematics Deployments. The Floating IP from the Bastion Server.|
    |HOSTNAME	|The hostname for the VSI. The hostname should be up to 13 characters as required by SAP. For more information on the rules regarding hostnames for SAP systems, check [SAP Note 611361: Hostnames of SAP ABAP Platform servers](https://accounts.sap.com/saml2/idp/sso)|
    |REGION	|The cloud region to deploy the solution. The regions and zones for VPC are listed [here](/docs/containers?topic=containers-regions-and-zones#zones-vpc). Review supported locations in IBM Cloud Schematics [here](https://cloud.ibm.com/docs/schematics?topic=schematics-locations). Sample value: eu-de.|
    |RESOURCE_GROUP	|The name of an existing Resource Group for VSIs and Volumes resources. Default value: "Default". The list of Resource Groups is available [here](https://cloud.ibm.com/account/resource-groups).|
    |SECURITY_GROUP	|The name of an existing Security group. The list of security groups is available [here](https://cloud.ibm.com/vpc-ext/network/securityGroups). |
    |SSH_KEYS	|The list of SSH Keys UUIDs that are allowed to SSH as root to the VSI can contain one or more IDs. The list of SSH Keys is available [here](https://cloud.ibm.com/vpc-ext/compute/sshKeys). Sample input (use your own SSH UUIDs from IBM Cloud): ["r010-57bfc315-f9e5-46bf-bf61-d87a24a9ce7a", "r010-3fcd9fe7-d4a7-41ce-8bb3-d96e936b2c7e"]|
    |SUBNET	|The name of an existing subnet. The list of subnets is available [here](https://cloud.ibm.com/vpc-ext/network/subnets).|
    |VPC	|The name of an existing VPC. The list of VPCs is available [here](https://cloud.ibm.com/vpc-ext/network/vpcs).|
    |ZONE	|The cloud zone where to deploy the solution. Sample value: eu-de-2.|
    |ibmcloud_api_key	|IBM Cloud API key (Sensitive* value).|
    |private_ssh_key	|Required only for Schematics Deployments - Input your id_rsa private key pair content in OpenSSH format (Sensitive* value). This private key should be used only during the terraform provisioning and it is recommended to be changed after the SAP deployment.|
    |hdb_instance_number	|The instance number of the SAP HANA database server.|
    |sap_aas_instance_number	|Technical identifier for the internal processes of the additional application server.|
    |sap_ascs_instance_number	|Technical identifier for the internal processes of ASCS.|
    |sap_ci_host	|IP address of the existing SAP Central Instance.|
    |sap_ci_hostname	|The hostname of the existing SAP Central Instance.|
    |sap_ci_instance_number	|Technical identifier for the internal processes of the Central Instance.|
    |sap_sid	|The SAP system ID identifies the entire SAP system.|
    |sap_main_password	|Common password for all users that are created during the installation (See Obs*).|

   * **Optional variables** - Review and update the optional input variables. The Ansible scripts expect the SAP kits to be in the default locations listed. For more information, see the [Readme file - Input Parameters](https://github.com/IBM-Cloud/sap-abap-hana-aas/blob/main/README.md).

    |Parameter	|Description|
    |-----|-----|
    |ID_RSA_FILE_PATH	|The file path for private_ssh_key is automatically generated by default. If it is changed, it must contain the relative path from Git repo folders. Default value: "ansible/id_rsa".|
    |IMAGE	|The OS image used for the VSI. A list of images is available [here](https://cloud.ibm.com/docs/vpc?topic=vpc-about-images).|
    |PROFILE	|The profile used for the VSI. A list of profiles is available [here](https://cloud.ibm.com/docs/vpc?topic=vpc-profiles&interface=ui). For more information about supported DB/OS and IBM Gen 2 Virtual Server Instances (VSI), check [SAP Note 2927211: SAP Applications on IBM Virtual Private Cloud](https://accounts.sap.com/saml2/idp/sso).|
    |VOL1	|Volume 1 Size - The size for the disks in GB that are to be attached to the VSI and used by SAP.|
    |VOL2	|Volume 2 Size - The size for the disks in GB that are to be attached to the VSI and used by SAP.|
    |kit_sapcar_file	|Path to the sapcar binary, as downloaded from SAP Support Portal.|
    |kit_swpm_file	|Path to the SWPM archive (SAR), as downloaded from SAP Support Portal.|
    |kit_saphostagent_file	|Path to the SAP Host Agent archive (SAR), as downloaded from SAP Support Portal.|
    |kit_hdbclient_file	|Path to the HANA DB client archive (SAR), as downloaded from SAP Support Portal.|

6. Accept the license agreement.
7. Select **Deploy**. The deployment starts and you are directed to the {{site.data.keyword.bpshort}} page that displays the script log files for you to monitor the deployment progress.

## Creating the infrastructure using Terraform with the bastion server CLI
{: #automate-hana-aas-anydb-create-terraform}
{: terraform}

Use these steps to configure the {{site.data.keyword.cloud_notm}} Provider plug-in and use Terraform to install SAP AAS to SAP HANA and AnyDB on your existing VPC on an already deployed SAP NetWeaver 7.X with SAP HANA 2.0 or ASE SYB as a Central Instance.

The script takes 1 - 2 hours to complete.

1. Access the bastion server cli.
2. Clone the solution repository and change to the folder.

    ASE SYB 16 
    Clone the solution repository from https://github.com/IBM-Cloud/sap-aas-abap-ase-syb and cd to the sap-aas-abap-ase-syb/cli folder.
    
    ```shell
    git clone https://github.com/IBM-Cloud/sap-aas-abap-ase-syb
    cd sap-aas-abap-ase-syb/cli/

    ```

    SAP HANA 2.0: 
    Clone the solution repository from https://github.com/IBM-Cloud/sap-abap-hana-aas and cd to the sap-abap-hana-aas folder.

    ```shell
    git clone https://github.com/IBM-Cloud/sap-abap-hana-aas.git
    cd sap-abap-hana-aas/
    ```

3. Modify the `input.auto.tfvars` file to specify the information for the existing VPC, your region, zone, networking component names, hostname for the AAS VSI,profile, and image. You need your 40-digit SSH key ID for this file. The second SSH key is optional. For more options for profile, see [Instance Profiles](/docs/vpc?topic=vpc-profiles). For more options, see [Images](/docs/vpc?topic=vpc-about-images). For descriptions of the variables, see the [readme](https://github.com/IBM-Cloud/sap-abap-hana-aas#readme) file. 

    The VSI OS images that are supported for this solution for Netweaver Additional Application Server are:

    *   ibm-redhat-8-4-amd64-sap-applications-2
    *   ibm-redhat-8-6-amd64-sap-applications-2
    *   ibm-sles-15-3-amd64-sap-applications-2
    *   ibm-sles-15-4-amd64-sap-applications-4


    ```terraform
    # Infra VPC variables for ASE SYB
    REGION			= "eu-de"
    ZONE			= "eu-de-2"
    VPC				= "ic4sap"                        # EXISTING Security group name
    SECURITY_GROUP	= "ic4sap-securitygroup"      # EXISTING Security group name
    RESOURCE_GROUP  = "wes-automation"
    SUBNET			= "ic4sap-subnet"               # EXISTING Subnet name
    SSH_KEYS                = [ "r010-57bfc315-f9e5-46bf-bf61-d87a24a9ce7a" , "r010-3fcd9fe7-d4a7-41ce-8bb3-d96e936b2c7e" ]

    # SAP AAS VSI variables:
    HOSTNAME = "sapnwase-as01"
    PROFILE = "bx2-4x16"
    IMAGE = "ibm-redhat-8-4-amd64-sap-applications-2
    ```

    ```terraform
    # Infra VPC variables for ABAP HANA
    REGION           = "eu-de"
    ZONE             = "eu-de-2"
    VPC              = "ic4sap" # EXISTING Security group name
    SECURITY_GROUP   = "ic4sap-securitygroup" # EXISTING Security group name
    RESOURCE_GROUP   = "wes-automation"
    SUBNET           = "ic4sap-subnet" # EXISTING Subnet name
    SSH_KEYS         = [ "r010-57bfc315-f9e5-46bf-bf61-d87a24a9ce7a" , "r010-3fcd9fe7-d4a7-41ce-8bb3-d96e936b2c7e" ]
    ID_RSA_FILE_PATH = "ansible/id_rsa"

    # SAP AAS variables:
    HOSTNAME = "sapnwapp"
    PROFILE = "bx2-4x16"
    IMAGE = "ibm-redhat-8-6-amd64-sap-applications-2"
    ```

    Edit your IBM Cloud Activity Tracker (only for ABAP stack) input variables:

    ``` terraform
    # Activity Tracker variables:
    ATR_PROVISION = "true"
    # Activity Tracker : Disable this to not provision Activity Tracker instance. 
    # If an Activity Tracker instance already exists in the same region where this solution is to be deployed then  
    # disable (ATR_PROVISION = "false") this to avoid provisioning an Activity Tracker instance. 
    # A new instance of Activity Tracker will be deployed with this solution if ATR_PROVISION=true
    # Example to create Activity Tracker instance: ATR_PROVISION = "true"
    # Example to integrate existing Activity Tracker instance : ATR_PROVISION = "false"
    ATR_NAME = "Activity-Tracker-COS-eu-de"
    # Provide the Activity Tracker instance name to create or 
    # provide the existing Activity Tracker instance name in the same region where this solution is to be be deployed.
    # Example: ATR_NAME = "Activity-Tracker-COS-eu-de"
    ATR_TAGS = [""]
    # Activity Tracker: (Optional) only if ATR_PROVISION = "true", tags that should be applied to the Activity Tracker instance.
    # example ATR_TAGS = ["activity-tracker-cos"]
    ATR_PLAN = "lite"
    # Mandatory only if ATR_PROVISION is set to true. Activity Tracker: The type of plan the service instance should run under (lite, 7-day, 14-day, or 30-day). 
    # The list of service plan is avaialble here: https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-service_plan#service_plan"
    # Example ATR_PLAN = "lite"
    ```

4. Customize your SAP system configuration. In the same file, input.auto.tfvars, edit the SAP system configuration variables that are passed to the Ansible automated deployment. For descriptions of the variables, see the [readme](https://github.com/IBM-Cloud/sap-abap-hana-aas#readme) file. 

    ```terraform
    # SAP system configuration - for ASE SYB
    sap_sid = "NWD"
    sap_ci_host = "10.243.132.10"
    sap_ci_hostname = "sapnwase"
    sap_ci_instance_number = "00"
    sap_ascs_instance_number = "01"
    sap_aas_instance_number = "00"

    # Kits paths
    kit_sapcar_file = "/storage/NW75SYB/SAPCAR_1010-70006178.EXE"
    kit_swpm_file = "/storage/NW75SYB/SWPM10SP31_7-20009701.SAR"
    kit_saphotagent_file = "/storage/NW75SYB/SAPHOSTAGENT51_51-20009394.SAR"
    ```

    ```terraform
    # SAP system configuration - for ABAP HANA
    sap_sid = "NWD"
    sap_ci_host = "10.243.132.10"
    sap_ci_hostname = "sapnwapp01"
    sap_ci_instance_number = "00"
    sap_ascs_instance_number = "01"
    hdb_instance_number = "00"
    sap_aas_instance_number = "00"

    # Kits paths
    kit_sapcar_file = "/storage/NW75HDB/SAPCAR_1010-70006178.EXE"
    kit_swpm_file = "/storage/NW75HDB/SWPM10SP31_7-20009701.SAR"
    kit_saphotagent_file = "/storage/NW75HDB/SAPHOSTAGENT51_51-20009394.SAR"
    kit_hdbclient_file = "/storage/NW75HDB/IMDB_CLIENT20_009_28-80002082.SAR"
    ```

    Ansible decompresses the rest of the SAP kit files. For more information, see the [readme](https://github.com/IBM-Cloud/sap-abap-hana-aas#readme) file. 

5. Initialize the Terraform CLI.

    `terraform init`

6. Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account.

    `terraform plan plan1`

    Enter an SAP main password and your API key.

    The SAP main password must be 10 - 14 characters long and contain at least one digit (0-9). It can contain only the following characters: a-z, A-Z, 0-9, @, #, $, _. This password cannot contain exclamation points '!'. The password must not start with a digit or an underscore (_).

7. Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the input.auto.tfvars file to correct resources and run terraform plan again.

8. Apply the saved plan.

    ```terraform
    terraform apply "plan1"
    ```
 
 The virtual private cloud and components are created and you see output similar to the `terraform plan` output.  

9. Add the SAP credentials and the virtual server instance IP to the SAP GUI. For more information about the SAP GUI, see [SAP GUI](https://help.sap.com/doc/7abd5470728810148a4b1a83b0e91070/1511%20000/en-US/frameset.htm).

## Next steps
{: #automate-hana-aas-anydb-next}
{: terraform}

If you need to rename your resources after they are created, modify the input.auto.tfvars file to change the names and run `terraform plan` and `terraform apply` again. Do not use the {{site.data.keyword.cloud_notm}} Dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results.

If you need to remove the SAP Netweaver 7.X on HANA or AnyDB installation, go to your project folder and run `terraform destroy`. The `terraform destroy` command does not remove the VPC in this scenario because the VPC was created before these Terraform scripts were run.

If the resources created with the SAP deployment automation are removed, the Activity Tracker instance is also removed, if it is provisioned at the same time with the SAP solution (when ATR_PROVISION parameter is set to `true` during the deployment of the SAP solution).

## Related information
{: #automate-nw-asesyb-related}

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Getting started with Terraform on {{site.data.keyword.cloud_notm}}](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

For more information about using Terraform for creating only a VPC for SAP, without the SAP architecture, see [Creating single-tier virtual private cloud for SAP by using Terraform](/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).

SAP One Support Notes that apply to this document:

* [SAP Note 84555 - Windows Server, Linux&reg;, and UNIX: Certified hardware](https://launchpad.support.sap.com/#/notes/84855)
* [SAP Note 2927211 - SAP Applications on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
* [SAP Note 2923773 - Linux&reg; on {{site.data.keyword.cloud_notm}} (IaaS): Adaption of your SAP license](https://launchpad.support.sap.com/#/notes/2923773)
* [SAP Note 2414097 - SAP Applications on {{site.data.keyword.cloud_notm}} Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)
* [SAP Note 2369910 - SAP Software on Linux&reg;: General information](https://launchpad.support.sap.com/#/notes/2369910)
* [SAP Note 171380 - Released IBM hardware (Intel processors) and IBM cloud services offers](https://launchpad.support.sap.com/#/notes/171380)
* [SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)

This document is referenced by:

* [SAP Note 2927211 - SAP Applications on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
* [SAP Note 2588225 - SAP on {{site.data.keyword.cloud_notm}}: Protect against speculative execution vulnerabilities](https://launchpad.support.sap.com/#/notes/2588225)
* [SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)
* [SAP Note 2414097 - SAP Applications on {{site.data.keyword.cloud_notm}} Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)
* [IBM Cloud Activity Tracker](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-getting-started)
