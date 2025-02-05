---

copyright:
  years: 2024
lastupdated: "2024-12-13"

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

# Automating SAP NetWeaver(ABAP) 7.5 HA deployment on {{site.data.keyword.cloud}} VPC (Terraform and Ansible)
{: #automate-sap-abap-sz-mz-ha-deployment-intro}

Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) infrastructure resources so that you can rapidly build complex cloud environments. {{site.data.keyword.vpc_short}} infrastructure consists of SAP certified hardware that uses Intel&reg; Xeon CPUs and other Intel&reg; technologies.

Terraform scripts are used to create a VPC and 2 clustered layers, one for SAP NetWeaver 7.5 and another for SAP HANA in-memory database in a HA Single and Multi Zone architecture on the bastion server. Creating the bastion server is a prerequisite for all IBM SAP VPC automated solutions. The automation scripts use the VPC information that you provide and then call the Ansible playbook to create the SAP architecture on the specified VPC.
{: shortdesc}

You have two deployment methods to choose from:

*   Terraform scripts run from the CLI on your bastion server.
*   {{site.data.keyword.cloud_notm}} {{site.data.keyword.bpshort}} user interface accessed from the menu on your cloud dashboard.

## SAP solution implemented
{: #automate-sap-abap-sz-mz-ha-deployment-implement}

SAP NetWeaver 7.5 is a software stack for many of SAP SE's applications. The SAP NetWeaver 7.5   Application Server, sometimes referred to as WebAS, is the runtime environment for the SAP applications and all of the mySAP Business Suite runs on SAP WebAS: supplier relationship management (SRM), customer relationship management (CRM), supply chain management (SCM), product lifecycle management (PLM), enterprise resource planning (ERP), transportation management system (TMS).

The product is marketed as a service-oriented architecture for enterprise application integration. It can be used for custom development and integration with other applications and systems, and is built primarily using the ABAP programming language, but also uses C, C++, and Java. It can also be extended with, and interoperate with, technologies such as Microsoft .NET, Java EE, and IBM WebSphere.

SAP NetWeaver 7.5   7.5 enables technology trends like the Internet of Things (IoT), mobile, cloud, big data and analytics and offers a foundation for the easy and fast development of simple business applications. It supports Java 8 to enable Java-based hubs like SAP Enterprise Portal, SAP Process Orchestration, and SAP Business Process Management to benefit from new Java features.

The innovations in ABAP and Java, together with improvements to SAP NetWeaver 7.5   hubs, SAP Fiori, and lifecycle management offer significant benefits.

## What is created
{: #automate-sap-abap-sz-mz-ha-deployment-created}

The scripts work in two phases. The first phase automates creating the resources for the VPC provisioning process in an existing VPC created when you deploy the SAP bastion VSI. The second phase creates the SAP architecture in a distributed environment.

1. Architecture for SAP NetWeaver 7.5 HA in a single zone
    * SAP HA SZ SAP NetWeaver 7.5 App cluster server on a distinct VSI as a single zone VPC
    * SAP HANA cluster DB on a dedicated server type VSI as a single zone VPC

2. Architecture for SAP NetWeaver 7.5 HA in a multi-zone
    * SAP HA MZ SAP NetWeaver 7.5 App cluster server on a distinct VSI as multi-zone VPC
    * SAP HANA cluster DB on a dedicated server type VSI as multi-zone VPC

It is recommended to read the guidelines from the readme file when deploying this solution.
{: note}

For more information about this architecture, see [SAP NetWeaver 7.5 with SAP HANA IBM Cloud VPC](/docs/sap?topic=sap-automate-sap-abap-sz-mz-ha-deployment-overview&interface=ui).

During the first phase, the following resources are provisioned in the VPC:
*    1 Placement group for all 4 virtual machines created by this solution
*    4 VSIs, with Subnet and Security Group configurations
*    3 Application Load Balancers for HANA DB and SAP ASCS/ERS
*    1 VPC DNS service used to map the ALB FQDN to the SAP ASCS/ERS and HANA virtual hostnames
*    7 file shares for VPC

During the second phase, the Ansible Playbooks are called and the SAP High availability architecture is installed for both dedicated VSIs SAP App VSI box and dedicated SAP HANA VSI box. The SAP architecture that is deployed is the SAP NetWeaver 7.5 release on the pacemaker cluster. HA dedicated SAP HANA 2.0 VSI’s release as a distributed deployment model. For more information about this architecture, see [Automating SAP HANA stand-alone virtual server instance on {{site.data.keyword.cloud_notm}} VPC by using Terraform and Ansible](/docs/sap?topic=sap-automate-terraform-sap-hana-vsi).

## Highly available system for SAP NetWeaver 7.5 in a Single zone or Multi zone architectures
{: #automate-sap-abap-sz-mz-ha-deployment-highly}

![Figure 1. SAP HA for SAP applications cluster nodes PAS (Active) and AAS (Active) Schematics deployment (Single Zone architecture)](../images/sap-hana-ha-sz-sap-layer.svg "SAP HA for SAP applications cluster nodes PAS (Active) and AAS (Active) Schematics deployment (Single Zone architecture)"){: caption="SAP HA for SAP applications cluster nodes PAS (Active) and AAS (Active) Schematics deployment (Single Zone architecture)" caption-side="bottom"}

![Figure 2. SAP HA for HANA DB instances cluster nodes primary (Active) and Secondary (Passive) (Single Zone architecture)](../images/sap-ha-hana-vpc-single-zone.svg "SAP HA for HANA DB instances cluster nodes primary (Active) and Secondary (Passive) (Single Zone architecture)"){: caption="SAP HA for HANA DB instances cluster nodes primary (Active) and Secondary (Passive) (Single Zone architecture)" caption-side="bottom"}

At the most basic level, a standard HA HANA cluster in an active-passive configuration has two nodes: one is the primary node and the other is the standby node. The primary node is actively serving the active SAP instances (PAS and AAS), while the standby node is waiting to jump in if necessary.

The cluster is set with a virtual hostname IP. Hostname is mapped to the FQDN of the HANA ALB through DNS, which is the same as SAP ASCS and ERS instances. App instances (PAS and AAS), are the details to be used on the SAP profiles to call that particular component. The cluster assigns that virtual IP to the active node and uses a heartbeat monitor to confirm the availability of the components. If the primary node stops responding, it triggers the automatic failover mechanism that calls the standby node to become the primary node. The ALB detects the change, redirects the traffic to the new active node, and assigns the virtual IP to it, restoring the component availability. After the failed node is fixed, it comes online as a standby node.

## Terraform scripts
{: #automate-sap-abap-sz-mz-ha-deployment-scripts}
{: terraform}

The Terraform scripts included for deploying are:

* 1 Placement group to include all the 4 VMs involved in this solution.
* 4 VSIs in an existing VPC with subnet and security group configurations. The VSIs scope: 2 for the HANA database cluster instance and 2 for the SAP application cluster.
* Configuring 3 Application Load Balancers like HANA DB and SAP ASCS/ERS.
* Configuring 1 VPC DNS service that is used to map the ALB FQDN to the SAP ASCS/ERS and HANA virtual hostnames.
* Configuring 7 file shares for VPC.

The Ansible scripts that are included for deploying are:

* OS requirements installation and configuration for SAP applications.
* Cluster components installation
* Ansible scripts for SAP application cluster configuration and SAP HANA cluster configuration.
* HANA installation
* HANA DB backup
* HANA system replica configuration
* ASCS and ERS instances installation
* DB load
* Primary and extra application server installation

For CLI deployment, Ansible is started by Terraform and both must be available on the same host (bastion server).
{: note}

## SAP Kits
{: #automate-sap-abap-sz-mz-ha-deployment-sap-kits}

It is your responsibility to download the necessary SAP and DB kits to your Deployment Server (bastion server).All file archives are decompressed by Ansible during the automation deployment process. For more information, see the readme file.

## Terraform deployment overview
{: #automate-sap-abap-sz-mz-ha-deployment-terraform-overview}
{: terraform}

The automation scripts from the [GitHub repository](https://github.com/IBM-Cloud/sap-nwhana-ha) can be executed by running the Terraform scripts in CLI, from the bastion server (deployment server).

Edit the input parameter file `input.auto.tfvars`, and modify the variables to match your solution:
*    VPC - An existing VPC name
*    REGION - Region for the VSIs
*    DOMAIN_NAME - Private domain that is not reachable to and from the outside world
*    ZONE_1 - Availability zone for DB_HOSTNAME_1 and APP_HOSTNAME_1 VSIs
*    ZONE_2 - Availability zone for DB_HOSTNAME_2 and APP_HOSTNAME_2VSIs
*    SECURITY_GROUP - Existing Security Group, previously created by the user in the same VPC.
*    SUBNET_1 - The name of an existing subnet, in the same VPC, ZONE_1, where DB_HOSTNAME_1 and APP_HOSTNAME_1 VSIs will be created.
*    SUBNET_2 - The name of an existing subnet, in the same VPC, ZONE_2, where DB_HOSTNAME_2 and APP_HOSTNAME_2 VSIs will be created.
*    RESOURCE_GROUP - Existing resource group, previously created by the user.
*    SSH_KEYS - List of SSH Key UUIDs that are allowed to SSH as `root` to the VSIs.
*    ID_RSA_FILE_PATH - `id_rsa` private key file path in OpenSSH format with 0600 permissions
*    APP_HOSTNAME_1/ APP_HOSTNAME_2/DB_HOSTNAME_1/ DB_HOSTNAME_2 - Enter a hostname up to 12 characters. For more information, see the readme file.
*    DB_PROFILE/APP_PROFILE - The instance profile used for the HANA/APP VSI
*    DB_IMAGE/APP_IMAGE - OS image for DB/APP VSI

## {{site.data.keyword.bpshort}} deployment overview
{: #automate-sap-abap-sz-mz-ha-deployment-schematics-overview}
{: ui}

{{site.data.keyword.bpshort}} user interface is used on the {{site.data.keyword.cloud_notm}}. Enter the [GitHub repository](https://github.com/IBM-Cloud/sap-nwhana-ha) for SAP NetWeaver (ABAP){: external} on HANA database High Availability on single or multi zones {{site.data.keyword.bpshort}}.

When you run the scripts with the {{site.data.keyword.bpshort}} interface, you:

*   Enter workspace information.
*   Enter the GitHub path for the Terraform scripts used.
*   Modify the parameters in the {{site.data.keyword.bpshort}} interface.

## Support - Terraform and {{site.data.keyword.bpshort}}
{: #automate-sap-abap-sz-mz-ha-deployment-support}

There are no warranties of any kind, and there is no service or technical support available for these materials from IBM. As a recommended practice, carefully review any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, readapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Before you begin
{: #automate-sap-abap-sz-mz-ha-deployment-begin}

Before you deploy SAP NetWeaver 7.5 High Availability on Single Zone or Multi Zone:

* The automation for this deployment requires {{site.data.keyword.cloud_notm}} File Storage for VPC to complete successfully. {{site.data.keyword.cloud_notm}} File Storage for VPC is available for customers with special approval to preview this service in the Frankfurt, London, Dallas, Toronto, Washington, Sao Paulo, Sydney, Osaka, and Tokyo regions. Contact your IBM Sales representative to get access. For more information, see [{{site.data.keyword.cloud_notm}} File Storage for VPC](https://cloud.ibm.com/docs/vpc?topic=vpc-file-storage-vpc-about){: external}.
* Set up your account to access the VPC. Make sure that your account is [upgraded to a paid account](/docs/account?topic=account-accountfaqs#changeacct).
* If you have not already, create a bastion server to store the SAP kits. For more information, see [Automate SAP bastion server - SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server).
* Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the downloaded locations. Ansible decompresses the files. For more information, see the Readme file in the [GitHub repository](https://github.com/IBM-Cloud/sap-nwhana-ha){: external} and on the About page.
* [Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey&interface=ui#create_user_key). The API key is used to authenticate with the {{site.data.keyword.cloud_notm}} platform and to determine your permissions for {{site.data.keyword.cloud_notm}} services.
* [Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.
* (Optional) create secrets for your credentials and passwords by using the [Secrets Manager](/docs/secrets-manager?topic=secrets-manager-arbitrary-secrets&interface=ui). {: ui}

## Deploying SAP HA NetWeaver 7.5 by using Terraform with the bastion server CLI
{: #automate-sap-abap-sz-mz-ha-deployment-terraform}
{: terraform}

Use these steps to configure the {{site.data.keyword.cloud_notm}} Provider plug-in and use Terraform to install **SAP HA SZ or MZ S/4HANA** on your existing VPC. The scripts can take upto 4 hours to complete.
The supported versions available for SAP NetWeaver (ABAP) with HANA HA SZ and MZ is RHEL. For more information, see the [readme](https://github.com/IBM-Cloud/sap-nwhana-ha/blob/main/README.md){: external} for recommended kits versions.

1. Access the bastion server CLI.
2. Clone the solution repository from `https://github.com/IBM-Cloud/sap-nwhana-ha` and `cd` to the `sap-nwhana-ha` folder.

    ```terraform
    git clone https://github.com/IBM-Cloud/sap-nwhana-ha.git

    cd sap-nwhana-ha

    ```

3. Specify your VPC. Modify the `input.auto.tfvars` file to specify the information for the existing VPC, your zone, VPC and component names, profile, and image. You need your 40-digit SSH key ID for this file. The second SSH key is optional. For more options for profile, see [Instance Profiles](/docs/vpc?topic=vpc-profiles). For more options on images, see [Images](/docs/vpc?topic=vpc-about-images). For descriptions of the variables, see the [readme file](https://github.com/IBM-Cloud/sap-nwhana-ha/blob/main/README.md){: external}.

    ```terraform
    ##########################################################
    # General VPC variables:
    ##########################################################

    REGION = ""
    # The cloud region where to deploy the solution. Supported regions: https://cloud.ibm.com/docs/containers?topic=containers-regions-and-zones#zones-vpc
    # Example: REGION = "eu-de"

    DOMAIN_NAME = "ha.mzexample.com"
    # The DOMAIN_NAME variable should contain at least one "." as a separator. It is a private domain and it is not reacheable to and from the outside world.
    # The DOMAIN_NAME variable could be like a subdomain name. Ex.: staging.example.com
    # Domain names can only use letters, numbers, and hyphens.
    # Hyphens cannot be used at the beginning or end of the domain name.
    # You can't use a domain name that is already in use.
    # Domain names are not case sensitive.

    ASCS_VIRT_HOSTNAME = "sapascs"
    # ASCS Virtual Hostname
    # Default value: sapascs
    # When the default value is used, the virtual hostname will automatically be changed based on <SAP_SID> to "sap<sap_sid>ascs"

    ERS_VIRT_HOSTNAME = "sapers"
    # ERS Virtual Hostname
    # Default value: sapers
    # When the default value is used, the virtual hostname will automatically be changed based on <SAP_SID> to "sap<sap_sid>ers"

    HANA_VIRT_HOSTNAME = "dbhana"
    # Hana Virtual Hostname
    # Default value: dbhana
    # When the default value is used, the virtual hostname will automatically be changed based on <SAP_SID> to "db<hana_sid>hana"

    VPC = ""
    # The name of an EXISTING VPC. Must be in the same region as the solution to be deployed. The list of VPCs is available here: https://cloud.ibm.com/infrastructure/network/vpcs.
    # Example: VPC = "ic4sap"

    ZONE_1 = ""
    # Availability zone for DB_HOSTNAME_1 and APP_HOSTNAME_1 VSIs, in the same VPC. Supported zones: https://cloud.ibm.com/docs/containers?topic=containers-regions-and-zones#zones-vpc
    # Example: ZONE = "eu-de-1"

    SUBNET_1 = ""
    # The name of an EXISTING Subnet, in the same VPC, ZONE_1, where DB_HOSTNAME_1 and APP_HOSTNAME_1 VSIs will be created. The list of Subnets is available here: https://cloud.ibm.com/infrastructure/network/subnets
    # Example: SUBNET = "ic4sap-subnet_1"

    ZONE_2 = ""
    # Availability zone for DB_HOSTNAME_2 and APP_HOSTNAME_2 VSIs, in the same VPC. Supported zones: https://cloud.ibm.com/docs/containers?topic=containers-regions-and-zones#zones-vpc.
    # If the same value as for ZONE_1 is used, and the value for SUBNET_1 is the same with the value for SUBNET_2, the deployment will be done in a single zone. If the values for ZONE_1, SUBNET_1 are different than the ones for ZONE_2, SUBNET_2 then an SAP Multizone deployment will be done.
    # Example: ZONE = "eu-de-2"

    SUBNET_2 = ""
    # The name of an EXISTING Subnet, in the same VPC, ZONE_2, where DB_HOSTNAME_2 and APP_HOSTNAME_2 VSIs will be created. The list of Subnets is available here: https://cloud.ibm.com/infrastructure/network/subnets.
    # If the same value as for SUBNET_1 is used, and the value for ZONE_1 is the same with the value for ZONE_2, the deployment will be done in a single zone. If the values for ZONE_1, SUBNET_1 are different than the ones for ZONE_2, SUBNET_2 then it an SAP Multizone deployment will be done.
    # Example: SUBNET = "ic4sap-subnet_2"

    SECURITY_GROUP = ""
    # The name of an EXISTING Security group for the same VPC. It can be found at the end of the Bastion Server deployment log, in \"Outputs\", before \"Command finished successfully\" message. The list of Security Groups is available here: https://cloud.ibm.com/infrastructure/network/securityGroups.
    # Example: SECURITY_GROUP = "ic4sap-securitygroup"

    RESOURCE_GROUP = ""
    # EXISTING Resource group, previously created by the user. The list of available Resource Groups: https://cloud.ibm.com/account/resource-groups
    # Example: RESOURCE_GROUP = "wes-automation"

    SSH_KEYS = [""]
    # List of SSH Keys UUIDs that are allowed to connect via SSH, as root, to the VSI. Can contain one or more IDs. The list of SSH Keys is available here: https://cloud.ibm.com/infrastructure/compute/sshKeys.
    # Example: SSH_KEYS = ["r010-8f72b994-c17f-4500-af8f-d05680374t3c", "r011-8f72v884-c17f-4500-af8f-d05900374t3c"]

    ID_RSA_FILE_PATH = "ansible/id_rsa"
    # The path to an existing id_rsa private key file, with 0600 permissions. The private key must be in OpenSSH format.
    # This private key is used only during the provisioning and it is recommended to be changed after the SAP deployment.
    # It must contain the relative or absoute path from your Bastion.
    # Examples: "ansible/id_rsa_s4hana_ha" , "~/.ssh/id_rsa_s4hana_ha" , "/root/.ssh/id_rsa".

    ##########################################################
    # File Shares variables:
    ##########################################################

    SHARE_PROFILE = "dp2"
    # The Storage Profile for the File Share
    # More details on https://cloud.ibm.com/docs/vpc?topic=vpc-file-storage-profiles&interface=ui#dp2-profile."

    USRSAP_AS1      = "20"
    USRSAP_AS2      = "20"
    USRSAP_SAPASCS  = "20"
    USRSAP_SAPERS   = "20"
    USRSAP_SAPMNT   = "20"
    USRSAP_SAPSYS   = "20"
    USRSAP_TRANS    = "80"
    # Default File shares sizes:

    ##########################################################
    # DB VSI variables:
    ##########################################################

    DB_HOSTNAME_1 = "hanadb-1"
    # HANA DB VSI HOSTNAME 1 in SAP HANA Cluster. The hostname should be up to 13 characters, as required by SAP
    # Default value: "hanadb-1"
    # When the default value is used, the virtual hostname will automatically be changed based on <HANA_SID> to "hanadb-<hana_sid>-1"

    DB_HOSTNAME_2 = "hanadb-2"
    # HANA DB VSI HOSTNAME 2 in SAP HANA Cluster. The hostname should be up to 13 characters, as required by SAP
    # Default value: "hanadb-2"
    # When the default value is used, the virtual hostname will automatically be changed based on <HANA_SID> to "hanadb-<hana_sid>-2"

    DB_PROFILE = "mx2-16x128"
    # The instance profile used for the HANA VSI. The list of certified profiles for HANA VSIs: https://cloud.ibm.com/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc
    # Details about all x86 instance profiles: https://cloud.ibm.com/docs/vpc?topic=vpc-profiles).
    # For more information about supported DB/OS and IBM Gen 2 Virtual Server Instances (VSI), check [SAP Note 2927211: SAP Applications on IBM Virtual Private Cloud](https://launchpad.support.sap.com/#/notes/2927211){: external}
    # Default value: "mx2-16x128"

    DB_IMAGE = "ibm-redhat-8-6-amd64-sap-hana-6"
    # OS image for DB VSI. OS images validated for DB VSIs: ibm-redhat-8-6-amd64-sap-hana-6, ibm-redhat-8-4-amd64-sap-hana-10
    # The list of available VPC Operating Systems supported by SAP: SAP note '2927211 - SAP Applications on IBM Virtual Private Cloud (VPC) Infrastructure environment' https://launchpad.support.sap.com/#/notes/2927211; The list of all available OS images: https://cloud.ibm.com/docs/vpc?topic=vpc-about-images
    # Example: DB_IMAGE = "ibm-redhat-8-4-amd64-sap-hana-10"

    ##########################################################
    # SAP APP VSI variables:
    ##########################################################

    APP_HOSTNAME_1 = "sapapp-1"
    # APP VSI HOSTNAME 1 in SAP APP Cluster. The hostname should be up to 13 characters.
    # Default value: "sapapp-1"
    # When the default value is used, the virtual hostname will automatically be changed based on <SAP_SID> to "sapapp-<sap_sid>-1"

    APP_HOSTNAME_2 = "sapapp-2"
    # APP VSI HOSTNAME 2 in SAP APP Cluster. The hostname should be up to 13 characters.
    # Default value: "sapapp-2"
    # When the default value is used, the virtual hostname will automatically be changed based on <SAP_SID> to "sapapp-<sap_sid>-2"

    APP_PROFILE = "bx2-4x16"
    # The APP VSI profile. Supported profiles: bx2-4x16. The list of available profiles: https://cloud.ibm.com/docs/vpc?topic=vpc-profiles&interface=ui

    APP_IMAGE = "ibm-redhat-8-6-amd64-sap-hana-6"
    # OS image for SAP APP VSI. OS images validated for APP VSIs: ibm-redhat-8-6-amd64-sap-hana-6, ibm-redhat-8-4-amd64-sap-hana-10.
    # The list of available VPC Operating Systems supported by SAP: SAP note '2927211 - SAP Applications on IBM Virtual Private Cloud (VPC) Infrastructure environment' https://launchpad.support.sap.com/#/notes/2927211; The list of all available OS images: https://cloud.ibm.com/docs/vpc?topic=vpc-about-images
    # Example: APP_IMAGE = "ibm-redhat-8-4-amd64-sap-hana-10"
    ```

4. Customize your SAP system configuration. In the same `input.auto.tfvars` file, edit the SAP system configuration variables that are passed to the Ansible automated deployment. For descriptions of the variables, see the [readme file](https://github.com/IBM-Cloud/sap-nwhana-ha/blob/main/README.md){: external}.

    ```terraform
    ##########################################################
    # SAP HANA configuration
    ##########################################################

    HANA_SID = "HDB"
    # SAP HANA system ID. Should follow the SAP rules for SID naming.
    # Obs. This will be used  also as identification number across different HA name resources. Duplicates are not allowed.
    # Example: HANA_SID = "HDB"

    HANA_SYSNO = "00"
    # SAP HANA instance number. Should follow the SAP rules for instance number naming.
    # Example: HANA_SYSNO = "00"

    HANA_SYSTEM_USAGE = "custom"
    # System usage. Default: custom. Suported values: production, test, development, custom
    # Example: HANA_SYSTEM_USAGE = "custom"

    HANA_COMPONENTS = "server"
    # SAP HANA Components. Default: server. Supported values: all, client, es, ets, lcapps, server, smartda, streaming, rdsync, xs, studio, afl, sca, sop, eml, rme, rtl, trp
    # Example: HANA_COMPONENTS = "server"

    KIT_SAPHANA_FILE = "/storage/HANADB/51057281.ZIP"
    # SAP HANA Installation kit path
    # Validated SAP HANA versions for S/4HANA 2023 on Red Hat 8: HANA 2.0 SP 7 Rev 73, kit file: 51057281.ZIP
    # Validated SAP HANA versions for S/4HANA 2022, 2021, 2020 on Red Hat 8: HANA 2.0 SP 5 Rev 57, kit file: 51056441.ZIP
    # Example for Red Hat 8: KIT_SAPHANA_FILE = "/storage/HANADB/51056441.ZIP"

    ##########################################################
    # SAP system configuration
    ##########################################################

    SAP_SID= "NWD"
    # SAP System ID
    # Obs. This will be used  also as identification number across different HA name resources. Duplicates are not allowed.

    SAP_ASCS_INSTANCE_NUMBER = "00"
    # The central ABAP service instance number. Should follow the SAP rules for instance number naming.
    # Example: SAP_ASCS_INSTANCE_NUMBER = "00"

    SAP_ERS_INSTANCE_NUMBER = "01"
    # The enqueue replication server instance number. Should follow the SAP rules for instance number naming.
    # Example: SAP_ERS_INSTANCE_NUMBER = "01"

    SAP_CI_INSTANCE_NUMBER = "10"
    # The primary application server instance number. Should follow the SAP rules for instance number naming.
    # Example: SAP_CI_INSTANCE_NUMBER = "10"

    SAP_AAS_INSTANCE_NUMBER = "20"
    # The additional application server instance number. Should follow the SAP rules for instance number naming.
    # Example: SAP_AAS_INSTANCE_NUMBER = "20"

    HDB_CONCURRENT_JOBS = "23"
    # Number of concurrent jobs used to load and/or extract archives to HANA Host

    ##########################################################
    # SAP Kit example Paths (storage from SAP Bastion server VSI instance)
    ##########################################################

    KIT_SAPCAR_FILE = "/storage/NW75SYB/SAPCAR_1010-70006178.EXE"
    KIT_SWPM_FILE =  "/storage/NW75SYB/SWPM10SP38_0-20009701.SAR"
    KIT_SAPHOSTAGENT_FILE = "/storage/NW75SYB/SAPHOSTAGENT61_61-80004822.SAR"
    KIT_SAPEXE_FILE = "/storage/NW75SYB/KERNEL/754UC/SAPEXE_200-80007612.SAR"
    KIT_SAPEXEDB_FILE = "/storage/NW75SYB/KERNEL/754UC/SAPEXEDB_200-80007655.SAR"
    KIT_IGSEXE_FILE = "/storage/NW75SYB/KERNEL/754UC/igsexe_2-80007786.sar"
    KIT_IGSHELPER_FILE = "/storage/NW75SYB/igshelper_17-10010245.sar"
    KIT_ASE_FILE = "/storage/NW75SYB/51056521_1_16_0_04_04.ZIP"
    KIT_EXPORT_DIR = "/storage/NW75SYB/EXP"
    ```

    Ansible decompresses the rest of the SAP kit files. For more information, see the [readme file](https://github.com/IBM-Cloud/sap-nwhana-ha/blob/main/README.md){: external}.
    {: note}

5. Initialize the Terraform CLI.

    ```terraform
    terraform init
    ```

6. Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account.

    ```terraform
    terraform plan --out plan1
    ```

    You will be asked for the following sensitive variables:
    `'IBMCLOUD_API_KEY', 'SAP_MAIN_PASSWORD' HANA_MAIN_PASSWORD, and 'HA_PASSWORD'`.


    The SAP main password must be 10 - 14 characters long and contain at least one digit (0-9). It can contain only the following characters: a-z, A-Z, 0-9, @, #, $, _. This password cannot contain `!`. It must not start with a digit or an underscore ( _ ).

7. Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the `input.auto.tfvars` file to correct resources and run `terraform plan` again.
8. Create the {{site.data.keyword.cloud_notm}} resources for SAP NetWeaver 7.5 HA system and install the SAP system.

    ```terraform
    terraform apply "plan1"
    ```

    The virtual private cloud and components are created and you see output similar to the `terraform plan` output.

    ```terraform
    terraform destroy
    ```
    You will be asked for the following sensitive variables as a destroy confirmation phase:
    `'IBMCLOUD_API_KEY', 'SAP_MAIN_PASSWORD' HANA_MAIN_PASSWORD, and 'HA_PASSWORD'`.

## Deploying SAP NetWeaver 7.5 on HANA High Availability on Single Zone or Multi Zone with the {{site.data.keyword.bpshort}} interface
{: #automate-sap-abap-sz-mz-ha-deployment-schematics}
{: ui}

Use these steps to configure the SAP NetWeaver 7.5 on HANA High Availability on Single Zone or Multi Zone on your existing VPC by using the Schematics interface. The scripts can take 2 - 3 hours to complete. For more information, see the [readme](https://github.com/IBM-Cloud/sap-nwhana-ha/blob/main/README.md) for recommended kits versions.

1.	From the {{site.data.keyword.cloud_notm}} menu, select **{{site.data.keyword.bpshort}}**.
2.	Click **Create workspace**.
3.	On the **Specify template** page:
    *   Enter the [GitHub repository URL](https://github.com/IBM-Cloud/sap-nwhana-ha){: external} that contains the {{site.data.keyword.bpshort}} code for this offering.
    *   Select the **Terraform version**.
    *   Click **Next**.
4.	On the **Workspace details** page:
    *   Enter a name for the workspace.
    *   Select a **Resource group**.
    *   Select a **Location** for your workspace. The workspace location does not have to match the resource location.
    *   Select **Next**.
5.	Select **Create** to create your workspace.
6.	On the workspace Settings page, in the Input variables section, review the default input variables and provide values that match your solution.

    For more detailed description of each of these parameters, check the GitHub repo readme file, chapter “Input parameter file”. Also, make sure to mark the parameters that contain sensitive information like passwords, API, and ssh private keys as "sensitive". These parameters are marked as “sensitive” in the readme file, under “Input parameter file”.

    Save each parameter that you modify.

7.	On the workspace settings page, click **Generate plan**. Wait for the plan to complete.
8.	Click **View log** to review the log files of your Terraform execution plan.
9.	Apply your Terraform template by clicking **Apply plan**.
10.	Review the log file to ensure that no errors occurred during the provisioning, modification, or deletion process.

## Next steps
{: #automate-sap-abap-sz-mz-ha-deployment-nextsteps}
{: terraform}

If you need to remove the SAP HA NetWeaver 7.5 installation, go to your project folder and run `terraform destroy`. The `terraform destroy` command does not remove the VPC in this scenario because the VPC was not created with the same Terraform script.

## Related information
{: #automate-sap-abap-sz-mz-ha-deployment-related-info}

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Terraform on {{site.data.keyword.cloud_notm}} getting started tutorial](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

For more information about using Terraform for creating only a VPC for SAP, without the SAP architecture, see [Creating single-tier VPC for SAP on {{site.data.keyword.cloud}} VPC with Terraform](/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).

Following are the SAP One Support Notes, that apply to this document:

* [SAP Note 84555 - Windows Server, Linux&reg;, and UNIX: Certified hardware](https://launchpad.support.sap.com/#/notes/84855){: external}
* [SAP Note 2927211 - SAP Applications on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211){: external}
* [SAP Note 2923773 - Linux&reg; on {{site.data.keyword.cloud_notm}} (IaaS): Adaption of your SAP license](https://launchpad.support.sap.com/#/notes/2923773){: external}
* [SAP Note 2414097 - SAP Applications on {{site.data.keyword.cloud_notm}} Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097){: external}
* [SAP Note 2369910 - SAP Software on Linux&reg;: General information](https://launchpad.support.sap.com/#/notes/2369910){: external}
* [SAP Note 171380 - Released IBM hardware (Intel&reg; processors) and IBM cloud services offers](https://launchpad.support.sap.com/#/notes/171380){: external}
* [SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654){: external}

This document is referenced by:

* [SAP Note 2927211 - SAP Applications on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211){: external}
* [SAP Note 2588225 - SAP on {{site.data.keyword.cloud_notm}}: Protect against speculative execution vulnerabilities](https://launchpad.support.sap.com/#/notes/2588225){: external}
* [SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654){: external}
* [SAP Note 2414097 - SAP Applications on {{site.data.keyword.cloud_notm}} Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097){: external}
