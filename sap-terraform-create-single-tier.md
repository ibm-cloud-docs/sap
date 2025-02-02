---

copyright:
  years: 2021, 2024
lastupdated: "2024-11-04"

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

# Creating 2-tier VPC for SAP on {{site.data.keyword.cloud}} VPC with Terraform
{: #create-terraform-single-tier-vpc-sap}

Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} VPC infrastructure resources so that you can rapidly build complex, cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware using Intel&reg; Xeon CPUs and additional Intel&reg; technologies.
{: shortdesc}

For more information about Terraform on {{site.data.keyword.cloud}}, see [Terraform on {{site.data.keyword.cloud_notm}} getting started tutorial](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

To create resources with Terraform, you use Terraform configuration files that describe the {{site.data.keyword.cloud_notm}} resources that you need and how you want to configure them. Based on your configuration, Terraform creates an execution plan and describes the actions that need to be run to create the resources. You can review the execution plan, change it, or run the plan. When you change your configuration, Terraform on {{site.data.keyword.cloud_notm}} can determine what changed and create incremental execution plans that you can apply to your existing {{site.data.keyword.cloud_notm}} resources.

## Script files
{: #terraform-single-tier-vpc-sap-files}

The configuration and script files are provided on the GitHub repository [https://github.com/IBM-Cloud/sap-infra-anydb-single/tree/main](https://github.com/IBM-Cloud/sap-infra-anydb-single/tree/main){: external}.

For single-tier virtual private cloud on SAP, you modify the ``input.auto.tfvars`` file to customize the resources for your solution. You specify zones, resource names, and SSH keys.

All of the other configuration files are provided and do not need to be modified.

The IBM Cloud Provider plug-in for Terraform on {{site.data.keyword.cloud_notm}} uses these configuration files to provision a VPC in your {{site.data.keyword.cloud_notm}} account.

## What is created
{: #terraform-single-tier-vpc-sap-components}

A VPC is a private space in {{site.data.keyword.cloud_notm}} where you can run an isolated environment with custom network policies. The variables that you define are used by the scripts to provision the following virtual private cloud infrastructure resources for you:

*	1 VPC where you provision your virtual server instance
*	1 security group and rules for this security group to allow DNS and SSH connections to your virtual server instance and all outbound traffic
*	1 subnet to enable networking in your VPC
*	1 virtual server instance
*	2 storage volumes, 1 for swap and 1 for data

## Support
{: #terraform-single-tier-vpc-sap-support}

There are no warranties of any kind, and there is no service or technical support available for these materials from IBM®. As a recommended practice, review carefully any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, re-adapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Before you begin

[Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey&interface=ui#create_user_key). The API key is used to authenticate with the IBM Cloud platform and to determine your permissions for IBM Cloud services.

[Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.

## Procedure

Use these steps to configure the {{site.data.keyword.cloud_notm}} Provider Plug-in and use Terraform to create a VPC for SAP.

1.	If you do not have Terraform installed, [Install the Terraform CLI and the {{site.data.keyword.cloud_notm}} Provider plug-in](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-setup_cli).

    If you are using Terraform 0.13 and higher, you do not need to install the {{site.data.keyword.cloud_notm}} Provider Plug-in. You modify the configuration files provided on the 1-Tier VPC for SAP GitHub repository to specify the plug-in version to use.

    If you are using Terraform 1.12.x and earlier, follow these {{site.data.keyword.cloud_notm}} Provider Plug-in [installation instructions](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-setup_cli#install-provider-v12). Do not configure the plug-in.

    Do not do any {{site.data.keyword.cloud_notm}} Provider Plug-in configuration because those files are provided for you.

2.  Create a project folder in the Terraform installation folder, and change directory to your project folder.

    `mkdir myproject && cd myproject`

3.  Copy the files from [https://github.com/IBM-Cloud/sap-infra-anydb-single/tree/main](https://github.com/IBM-Cloud/sap-infra-anydb-single/tree/main){: external} to the project folder that you created in the Terraform installation directory.

4.	Edit the ``input.auto.tfvars`` file to customize your solution. Modify the file to specify your zone, VPC component names, profile, and image. You need your 40-digit SSH key ID for this file. The second SSH key is optional. For more options for profile, see [Instance Profiles](/docs/vpc?topic=vpc-profiles). For more options for image, see [Images](/docs/vpc?topic=vpc-about-images).

    ```
    ZONE			= "eu-de-1"
    VPC			    = "test-vpc"
    SECURITYGROUP	= "test-securitygroup"
    SUBNET			= "test-subnet"
    HOSTNAME		= "test-vsi"
    PROFILE		    = "bx2-4x16"
    IMAGE = "ibm-redhat-8-6-amd64-sap-applications-4"
    SSH_KEYS		= [ "<SSH Key ID 1>" , "<SSH Key ID 2>" ]
    SWAP			= "16"
    VOL1			= "10"
    ```

5. Initialize the Terraform CLI.

   ```
   terraform init
   ```

6. Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the VPC instance in your account.

   ```
   terraform plan
   ```

7. Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the ``input.auto.tfvars`` file to correct resources and run ``terraform plan`` again.

8. Create the VPC for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.

   ```
   terraform apply
   ```
   The VPC and components are created and you see output similar to the terraform plan output.

This automation is offered at no cost; however, the provisioned infrastructure comes at cost.
{: note}

## Next steps

If you need to rename your resources after they are created, modify the ``input.auto.tfvars`` file to change the names and run ``terraform plan`` and ``terraform apply`` again. Do not use the {{site.data.keyword.cloud_notm}} Dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results.

If you need to remove your VPC, go to your project folder and run ``terraform destroy``.
