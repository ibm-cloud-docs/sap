---

copyright:
  years: 2021
lastupdated: "2021-05-23"

subcollection: sap

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}

# Creating 2nd and 3rd tier virtual private cloud for SAP anydb (non-HANA) by using Terraform
{: #create-terraform-multi-tier-vpc-anydb-sap}

Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} VPC infrastructure resources so that you can rapidly build complex, cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware using Intel Xeon CPUs and additional Intel technologies.
{: shortdesc}

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Terraform on {{site.data.keyword.cloud_notm}} getting started tutorial](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

To create resources with Terraform, you use Terraform configuration files that describe the {{site.data.keyword.cloud_notm}} resources that you need and how you want to configure them. Based on your configuration, Terraform creates an execution plan and describes the actions that need to be run to create the resources. You can review the execution plan, change it, or run the plan. When you change your configuration, Terraform on {{site.data.keyword.cloud_notm}} can determine what changed and create incremental execution plans that you can apply to your existing {{site.data.keyword.cloud_notm}} resources. 

## Script files
{: #terraform-multi-tier-vpc-sap-files}

The configuration and script files are provided on the GitHub repository [https://github.com/IBM-Cloud/sap-infra-anydb-distributed](https://github.com/IBM-Cloud/sap-infra-anydb-distributed). 

For 2nd and 3rd tier virtual private cloud on SAP, you modify the:

*  ``terraform.tfvars`` file to add your {{site.data.keyword.cloud_notm}} API-key

*  ``input.auto.tfvars`` file to customize the resources for your solution. You specify zones, resource names, and SSH keys.  

All of the other configuration files are provided and do not need to be modified. 

The IBM Cloud Provider plug-in for Terraform on {{site.data.keyword.cloud_notm}} uses these configuration files to provision a VPC in your {{site.data.keyword.cloud_notm}} account. 

## What is created
{: #terraform-multi-tier-vpc-sap-components}

A VPC is a private space in {{site.data.keyword.cloud_notm}} where you can run an isolated environment with custom network policies. The variables that you define are used by the scripts to provision the following virtual private cloud infrastructure resources for you: 

*	1 VPC where you provision your virtual server instance
*	1 security group and rules for this security group to allow DNS and SSH connections to your virtual server instance and all outbound traffic
*	1 subnet to enable networking in your VPC
*	2 virtual server instances (1 SAP App VSI and 1 DB(anydb) instance server VSI)
*	2 storage volumes, 1 for swap and 1 for data for SAP app VSI and 4 storage 1 x SWAP and 3 x DATA volumes for DB VSI 
*	2 floating IP address that you use to access your VPC virtual server instances over the public network


The VSIs are configured with Red Hat Enterprise Linux 7.x for SAP Applications (amd64) and they have at least two SSH keys that are configured to access as root user. The VSIs have the following storage volumes:

DB virtual server instance disks:
•	1x 40 GB disk with 10 IOPS / GB - SWAP
•	1 x 32 GB disk with 10 IOPS / GB - DATA (DB LOG)
•	1x 64 GB disk with 10 IOPS / GB - DATA (DB ARCHIVE LOG)
•	1 x 128/256 GB disk with 10 IOPS / GB – DATA
•	1 floating IP address that you use to access your VPC virtual server instance over the public network

SAP app virtual server instance disks:
•	1x 40 GB disk with 10 IOPS / GB - SWAP
•	1 x 128 GB disk with 10 IOPS / GB – DATA
•	1 floating IP address that you use to access your VPC virtual server instance over the public network


## Support
{: #terraform-multi-tier-anydb-support}
 
There are no warranties of any kind, and there is no service or technical support available for these materials from IBM®. As a recommended practice, review carefully any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, re-adapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Before you begin

1.  If you do not have Terraform installed, [Install the Terraform CLI and the {{site.data.keyword.cloud_notm}} Provider plug-in](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-setup_cli).  

    If you are using Terraform 0.13 and higher, you do not need to install the {{site.data.keyword.cloud_notm}} Provider Plug-in. You modify the configuration files provided on the 1-Tier VPC for SAP GitHub repository to specify the plug-in version to use. 

    If you are using Terraform 1.12.x and earlier, follow these {{site.data.keyword.cloud_notm}} Provider Plug-in [installation instructions](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-setup_cli#install-provider-v12). Do not configure the plug-in.

    Do not do any {{site.data.keyword.cloud_notm}} Provider Plug-in configuration because those files are provided for you. 

2.  [Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey#create_user_key). The API key is used to authenticate with the IBM Cloud platform and to determine your permissions for IBM Cloud services.

3.  [Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.

## Procedure

Use these steps to configure the {{site.data.keyword.cloud_notm}} Provider Plug-in and use Terraform to create a VPC for SAP.

1.  Create a project folder in the Terraform installation folder, and change directory to your project folder.

    `mkdir myproject && cd myproject`

2.  Copy the files from [https://github.com/IBM-Cloud/sap-infra-anydb-distributed/tree/main/cli](https://github.com/IBM-Cloud/sap-infra-anydb-distributed/tree/main/cli) to the project folder that you created in the Terraform installation directory. 

3.  Edit the ``terraform.tfvars`` variable file and enter the IBM Cloud API key that you retrieved. 

    ```
    ibmcloud_api_key = "<ibmcloud_apikey>"
    ```

    Variables that are defined in the ``terraform.tfvars`` file are automatically loaded by Terraform when the {{site.data.keyword.cloud_notm}} Provider plug-in is initialized and you can reference them in every Terraform configuration file that you use.  

    Because the ``terraform.tfvars`` file contains confidential information, do not push this file to a version control system. Keep this file on your local system only. 
  
4.	Edit the ``input.auto.tfvars`` file to customize your solution. Modify the file to specify your VPC name, subnet, security group, hostname, profile, image, SSH keys, and disk sizes. You must modify:

    * VPC - Unique VPC name.
    * SECURITYGROUP - Change ic4sap to the VPC name.
    * SUBNET - Change ic4sap to the VPC name.
    * DB/APP HOSTNAME - Enter a hostname up to 13 characters.  For more information, see the README file.

    For disk sizes, volumes are created with the required size and are attached to the VSIs. The size for the volumes is defined as a list in the VOLUME_SIZES variable with each value specifying capacity for a volume in GB.   

    You need your 40-digit SSH key ID for this file. The second SSH key is optional. 

    For more options for profile, see [Instance Profiles](/docs/vpc?topic=vpc-profiles). For more options for image, see [Images](/docs/vpc?topic=vpc-about-images).

    ```
    # General VPC variables:
    REGION        = "eu-de" # default value
    ZONE          = "eu-de-2" # default value
    VPC           = "ic4sap"
    SECURITYGROUP = "ic4sap-securitygroup"
    SUBNET        = "ic4sap-subnet"
    SSH_KEYS      = [ "r010-57bfc315-f9e5-46bf-bf61-d87a24a9ce7a" , "r010-3fcd9fe7-d4a7-41ce-8bb3-d96e936b2c7e" ]
    PROFIL        = "bx2-4x16" # default value
    IMAGE         = "ibm-redhat-7-6-amd64-sap-applications-1" # default value

    # SAP Database VSI variables:
    DB-HOSTNAME   = "ep12db"
    DB-VOLUME_SIZES = [ "40" , "32" , "64" , "128" ]


    # SAP APPs VSI variables:
    APP-HOSTNAME  = "ep12app" # default value
    APP-VOLUME_SIZES= [ "40" , "128" ]

    ```
    {: codeblock}

    |Parameter	|Description|
    |-----------|-----------|
    |REGION	   |The cloud region where the solution is deployed. The regions and zones for VPC are listed [here](/docs/containers?topic=containers-regions-and-zones#zones-vpc).|
    |ZONE	    |The cloud zone where the solution is deployed.	|
    |VPC  |The name of the VPC. The list of VPCs is available [here](https://cloud.ibm.com/vpc-ext/network/vpcs).|
    |SECURITYGROUP	    |The name of the Security Group. The list of Security Groups is available [here](https://cloud.ibm.com/vpc-ext/network/subnets)|
    |SUBNET	     |The name of the Subnet. The list of Subnets is available [here](https://cloud.ibm.com/vpc-ext/network/subnets)|
    |PROFILE	     |The profile used for the VSI. A list of profiles is available [here](https://cloud.ibm.com/docs/vpc?topic=vpc-profiles).|
    |IMAGE	|The OS image used for the VSI. A list of images is available [here](https://cloud.ibm.com/docs/vpc?topic=vpc-about-images).|
    |SSH_KEYS	   |List of SSH Keys IDs that are allowed to SSH as root to the VSI. Can contain one or more IDs. The list of SSH Keys is available [here](https://cloud.ibm.com/vpc-ext/compute/sshKeys).|
    |[DB/APP]-HOSTNAME	|The hostname for the VSI. The hostname must have up to 13 characters as required by SAP. For more information about rules regarding hostnames for SAP systems, see [SAP Note 611361 - Hostnames of SAP ABAP Platform servers](https://launchpad.support.sap.com/#/notes/%20611361).|


5. Initialize the Terraform CLI. 

   ```
   terraform init
   ```

6. Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account.

   ```
   terraform init
   ```

7. Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the ``input.auto.tfvars`` file to correct resources and run ``terraform plan`` again.

8. Create the virtual private cloud for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.

   ```
   terraform apply
   ```
   The virtual private cloud and components are created and you see output similar to the terraform plan output.  


## Next steps

If you need to rename your resources after they are created, modify the ``input.auto.tfvars`` file to change the names and run ``terraform plan`` and ``terraform apply`` again. Do not use the {{site.data.keyword.cloud_notm}} Dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results. 

If you need to remove your VPC, go to your project folder and run ``terraform destroy``.