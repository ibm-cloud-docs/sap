---

copyright:
  years: 2021
lastupdated: "2021-06-23"

subcollection:

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}

# Creating single-tier virtual private cloud for SAP by using Terraform
{: #create-terraform-single-tier-vpc-sap}

Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} VPC infrastructure resources so that you can rapidly build complex, cloud environments.
{: shortdesc}

Terraform on {{site.data.keyword.cloud_notm}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} solutions. For more information about Terraform on {{site.data.keyword.cloud}}, see [Terraform on {{site.data.keyword.cloud_notm}} getting started tutorial](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

To create resources with Terraform, you use Terraform configuration files that describe the {{site.data.keyword.cloud_notm}} resources that you need and how you want to configure them. Based on your configuration, Terraform creates an execution plan and describes the actions that need to be run to create the resources. You can review the execution plan, change it, or run the plan. When you change your configuration, Terraform on {{site.data.keyword.cloud_notm}} can determine what changed and create incremental execution plans that you can apply to your existing {{site.data.keyword.cloud}} resources. 

## Script files

The configuration and script files are provided on the GitHub repository [sap-automated-deployment-scripts/sapsingletiervpc/](https://github.com/IBM-Cloud/sap-automated-deployment-scripts/tree/master/sapsingletiervpc). 

For single-tier virtual private cloud on SAP, you modify the:

*  ``terraform.tfvars`` file to add your {{site.data.keyword.cloud_notm}} API-key

*  ``input.auto.tfvars`` file to customize the resources for your solution. You specify zones, resource names, and SSH keys.  

All of the other configuration files are provided and do not need to be modified. 

The IBM Cloud Provider plug-in for Terraform on {{site.data.keyword.cloud_notm}} uses these configuration files to provision a VPC in your {{site.data.keyword.cloud_notm}} account. 

## What is created

A VPC is a private space in {{site.data.keyword.cloud_notm}} where you can run an isolated environment with custom network policies. The variables that you define are used by the scripts to provision the following virtual private cloud infrastructure resources for you: 

*	1 VPC where you provision your virtual server instance
*	1 security group and rules for this security group to allow DNS and SSH connections to your virtual server instance and all outbound traffic
*	1 subnet to enable networking in your VPC
*	1 virtual server instance 
*	2 storage volumes, 1 for swap and 1 for data
*	1 floating IP address that you use to access your VPC virtual server instance over the public network

## Support
 
There are no warranties of any kind, and there is no service or technical support available for these materials from IBM®. As a recommended practice, review carefully any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, re-adapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Before you begin

[Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey#create_user_key). The API key is used to authenticate with the IBM Cloud platform and to determine your permissions for IBM Cloud services.

[Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.

## Procedure

Use these steps to configure the {{site.data.keyword.cloud_notm}} Provider Plug-in and use Terraform to create a VPC for SAP.

1.	If you do not have Terraform installed, [Install the Terraform CLI and the {{site.data.keyword.cloud_notm}} Provider plug-in](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-setup_cli).  

    If you are using Terraform 0.13 and higher, you do not need to install the {{site.data.keyword.cloud_notm}} Provider Plug-in. You modify the configuration files provided on the 1-Tier VPC for SAP GitHub repository to specify the plug-in version to use. 

    If you are using Terraform 1.12.x and earlier, follow these {{site.data.keyword.cloud_notm}} Provider Plug-in [installation instructions](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-setup_cli#install-provider-v12). Do not configure the plug-in.

    Do not do any {{site.data.keyword.cloud_notm}} Provider Plug-in configuration because those files are provided for you. 

2.  Create a project folder in the Terraform installation folder, and change directory to your project folder.

    `mkdir myproject && cd myproject`

3.  Copy the files from [sap-automated-deployment-scripts/sapsingletiervpc/](https://github.com/IBM-Cloud/sap-automated-deployment-scripts/tree/master/sapsingletiervpc) to the project folder that you created in the Terraform installation directory. 

4.  Edit the ``terraform.tfvars`` variable file and enter the IBM Cloud API key that you retrieved. 

    ```ibmcloud_api_key = "<ibmcloud_apikey>"```

    Variables that are defined in the ``terraform.tfvars`` file are automatically loaded by Terraform when the {{site.data.keyword.cloud_notm}} Provider plug-in is initialized and you can reference them in every Terraform configuration file that you use.  

    Because the ``terraform.tfvars`` file contains confidential information, do not push this file to a version control system. Keep this file on your local system only. 
  
5.	Edit the ``input.auto.tfvars`` file to customize your solution. Modify the file to specify your zone, VPC component names, profile, and image. You need your 40-digit SSH key ID for this file. The second SSH key is optional. For more options for profile, see [Instance Profiles](/docs/vpc?topic=vpc-profiles). For more options for image, see [Images](/docs/vpc?topic=vpc-about-images).

    ```
    ZONE			= "eu-de-1"
    VPC			    = "test-vpc"
    SECURITYGROUP	= "test-securitygroup"
    SUBNET			= "test-subnet"
    HOSTNAME		= "test-vsi"
    PROFILE		    = "bx2-4x16"
    IMAGE			= "ibm-redhat-7-6-amd64-sap-applications-1"
    SSH_KEYS		= [ "<SSH Key ID 1>" , "<SSH Key ID 2>" ]
    SWAP			= "16"
    VOL1			= "10"
    ```
    {: codeblock}

6. Initialize the Terraform CLI. 

   ```
   terraform init
   ```

7. Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account.

   ```
   terraform plan
   ```
   An example of a Terraform plan is here.

   ```
    Resource actions are indicated with the following symbols:
      + create
     <= read (data resources)

    Terraform will perform the following actions:`

      # module.volumes.ibm_is_volume.swap will be created
      + resource "ibm_is_volume" "swap" {
         + capacity                = 16
         + crn                     = (known after apply)
         + encryption_key          = (known after apply)
         + id                      = (known after apply)
         + iops                    = (known after apply)
         + name                    = "test-vsi-swap"
         + profile                 = "10iops-tier"
         + resource_controller_url = (known after apply)
         + resource_crn            = (known after apply)
         + resource_group          = (known after apply)
         + resource_group_name     = (known after apply)
         + resource_name           = (known after apply)
         + resource_status         = (known after apply)
         + status                  = (known after apply)
         + status_reasons          = (known after apply)
         + tags                    = (known after apply)
         + zone                    = "eu-de-1"
    }

     # module.volumes.ibm_is_volume.vol1 will be created
      + resource "ibm_is_volume" "vol1" {
        + capacity                = 10
        + crn                     = (known after apply)
        + encryption_key          = (known after apply)
        + id                      = (known after apply)
        + iops                    = (known after apply)
        + name                    = "test-vsi-vol1"
        + profile                 = "10iops-tier"
        + resource_controller_url = (known after apply)
        + resource_crn            = (known after apply)
        + resource_group          = (known after apply)
        + resource_group_name     = (known after apply)
        + resource_name           = (known after apply)
        + resource_status         = (known after apply)
        + status                  = (known after apply)
        + status_reasons          = (known after apply)
        + tags                    = (known after apply)
        + zone                    = "eu-de-1"
    }

    # module.vpc.ibm_is_security_group.securitygroup will be created
    + resource "ibm_is_security_group" "securitygroup" {
      + crn                     = (known after apply)
      + id                      = (known after apply)
      + name                    = "test-securitygroup"
      + resource_controller_url = (known after apply)
      + resource_crn            = (known after apply)
      + resource_group          = (known after apply)
      + resource_group_name     = (known after apply)
      + resource_name           = (known after apply)
      + rules                   = (known after apply)
      + tags                    = (known after apply)
      + vpc                     = (known after apply)
    }

    # module.vpc.ibm_is_security_group_rule.inbound_dns_all will be created
    + resource "ibm_is_security_group_rule" "inbound_dns_all" {
      + direction   = "inbound"
      + group       = (known after apply)
      + id          = (known after apply)
      + ip_version  = "ipv4"
      + protocol    = (known after apply)
      + related_crn = (known after apply)
      + remote      = "0.0.0.0/0"
      + rule_id     = (known after apply)

      + udp {
          + port_max = 53
          + port_min = 53
        }
    }

    # module.vpc.ibm_is_security_group_rule.inbound_ssh_all will be created
    + resource "ibm_is_security_group_rule" "inbound_ssh_all" {
      + direction   = "inbound"
      + group       = (known after apply)
      + id          = (known after apply)
      + ip_version  = "ipv4"
      + protocol    = (known after apply)
      + related_crn = (known after apply)
      + remote      = "0.0.0.0/0"
      + rule_id     = (known after apply)

      + tcp {
          + port_max = 22
          + port_min = 22
        }
    }

    # module.vpc.ibm_is_security_group_rule.outbound_all will be created
    + resource "ibm_is_security_group_rule" "outbound_all" {
      + direction   = "outbound"
      + group       = (known after apply)
      + id          = (known after apply)
      + ip_version  = "ipv4"
      + protocol    = (known after apply)
      + related_crn = (known after apply)
      + remote      = "0.0.0.0/0"
      + rule_id     = (known after apply)
    }

    # module.vpc.ibm_is_subnet.subnet will be created
    + resource "ibm_is_subnet" "subnet" {
      + available_ipv4_address_count = (known after apply)
      + crn                          = (known after apply)
      + id                           = (known after apply)
      + ip_version                   = "ipv4"
      + ipv4_cidr_block              = (known after apply)
      + ipv6_cidr_block              = (known after apply)
      + name                         = "test-subnet"
      + network_acl                  = (known after apply)
      + resource_controller_url      = (known after apply)
      + resource_crn                 = (known after apply)
      + resource_group               = (known after apply)
      + resource_group_name          = (known after apply)
      + resource_name                = (known after apply)
      + resource_status              = (known after apply)
      + routing_table                = (known after apply)
      + status                       = (known after apply)
      + tags                         = (known after apply)
      + total_ipv4_address_count     = 32
      + vpc                          = (known after apply)
      + zone                         = "eu-de-1"
    }

    # module.vpc.ibm_is_vpc.vpc will be created
    + resource "ibm_is_vpc" "vpc" {
      + address_prefix_management   = "auto"
      + classic_access              = false
      + crn                         = (known after apply)
      + cse_source_addresses        = (known after apply)
      + default_network_acl         = (known after apply)
      + default_network_acl_name    = (known after apply)
      + default_routing_table       = (known after apply)
      + default_routing_table_name  = (known after apply)
      + default_security_group      = (known after apply)
      + default_security_group_name = (known after apply)
      + id                          = (known after apply)
      + name                        = "test-vpc"
      + resource_controller_url     = (known after apply)
      + resource_crn                = (known after apply)
      + resource_group              = (known after apply)
      + resource_group_name         = (known after apply)
      + resource_name               = (known after apply)
      + resource_status             = (known after apply)
      + security_group              = (known after apply)
      + status                      = (known after apply)
      + subnets                     = (known after apply)
      + tags                        = (known after apply)
    }

    # module.vsi.data.ibm_is_image.image will be read during apply
    # (config refers to values not yet known)
    <= data "ibm_is_image" "image"  {
      + architecture   = (known after apply)
      + checksum       = (known after apply)
      + crn            = (known after apply)
      + encryption     = (known after apply)
      + encryption_key = (known after apply)
      + id             = (known after apply)
      + name           = "ibm-redhat-7-6-amd64-sap-applications-1"
      + os             = (known after apply)
      + status         = (known after apply)
    }

    # module.vsi.data.ibm_is_security_group.securitygroup will be read during apply
    # (config refers to values not yet known)
    <= data "ibm_is_security_group" "securitygroup"  {
      + crn                     = (known after apply)
      + id                      = (known after apply)
      + name                    = "test-securitygroup"
      + resource_controller_url = (known after apply)
      + resource_crn            = (known after apply)
      + resource_group_name     = (known after apply)
      + resource_name           = (known after apply)
      + rules                   = (known after apply)
      + tags                    = (known after apply)
      + vpc                     = (known after apply)
    }

    # module.vsi.data.ibm_is_subnet.subnet will be read during apply
    # (config refers to values not yet known)
    <= data "ibm_is_subnet" "subnet"  {
      + available_ipv4_address_count = (known after apply)
      + crn                          = (known after apply)
      + id                           = (known after apply)
      + ipv4_cidr_block              = (known after apply)
      + ipv6_cidr_block              = (known after apply)
      + name                         = "test-subnet"
      + network_acl                  = (known after apply)
      + public_gateway               = (known after apply)
      + resource_controller_url      = (known after apply)
      + resource_crn                 = (known after apply)
      + resource_group               = (known after apply)
      + resource_group_name          = (known after apply)
      + resource_name                = (known after apply)
      + resource_status              = (known after apply)
      + status                       = (known after apply)
      + tags                         = (known after apply)
      + total_ipv4_address_count     = (known after apply)
      + vpc                          = (known after apply)
      + zone                         = (known after apply)
    }

    # module.vsi.data.ibm_is_vpc.vpc will be read during apply
    # (config refers to values not yet known)
   <= data "ibm_is_vpc" "vpc"  {
      + classic_access              = (known after apply)
      + crn                         = (known after apply)
      + cse_source_addresses        = (known after apply)
      + default_network_acl         = (known after apply)
      + default_network_acl_name    = (known after apply)
      + default_routing_table       = (known after apply)
      + default_routing_table_name  = (known after apply)
      + default_security_group      = (known after apply)
      + default_security_group_name = (known after apply)
      + id                          = (known after apply)
      + name                        = "test-vpc"
      + resource_controller_url     = (known after apply)
      + resource_crn                = (known after apply)
      + resource_group              = (known after apply)
      + resource_group_name         = (known after apply)
      + resource_name               = (known after apply)
      + resource_status             = (known after apply)
      + security_group              = (known after apply)
      + status                      = (known after apply)
      + subnets                     = (known after apply)
      + tags                        = (known after apply)
    }

    # module.vsi.ibm_is_floating_ip.fip will be created
    + resource "ibm_is_floating_ip" "fip" {
      + address                 = (known after apply)
      + id                      = (known after apply)
      + name                    = "test-vsi-fip"
      + resource_controller_url = (known after apply)
      + resource_crn            = (known after apply)
      + resource_group          = (known after apply)
      + resource_group_name     = (known after apply)
      + resource_name           = (known after apply)
      + resource_status         = (known after apply)
      + status                  = (known after apply)
      + tags                    = (known after apply)
      + target                  = (known after apply)
      + zone                    = (known after apply)
    }

    # module.vsi.ibm_is_instance.vsi will be created
    + resource "ibm_is_instance" "vsi" {
      + disks                   = (known after apply)
      + gpu                     = (known after apply)
      + id                      = (known after apply)
      + image                   = (known after apply)
      + keys                    = [
          + "r010-4a332095-4ff9-4b63-affc-367c0a14df69",
          + "r010-7945f2b4-06f2-4276-8d8f-d40922a8686d",
        ]
      + memory                  = (known after apply)
      + name                    = "test-vsi"
      + profile                 = "bx2-4x16"
      + resource_controller_url = (known after apply)
      + resource_crn            = (known after apply)
      + resource_group          = (known after apply)
      + resource_group_name     = (known after apply)
      + resource_name           = (known after apply)
      + resource_status         = (known after apply)
      + status                  = (known after apply)
      + tags                    = (known after apply)
      + vcpu                    = (known after apply)
      + volume_attachments      = (known after apply)
      + volumes                 = (known after apply)
      + vpc                     = (known after apply)
      + wait_before_delete      = true
      + zone                    = "eu-de-1"

      + boot_volume {
          + encryption = (known after apply)
          + iops       = (known after apply)
          + name       = (known after apply)
          + profile    = (known after apply)
          + size       = (known after apply)
        }

      + primary_network_interface {
          + allow_ip_spoofing    = false
          + id                   = (known after apply)
          + name                 = (known after apply)
          + primary_ipv4_address = (known after apply)
          + security_groups      = (known after apply)
          + subnet               = (known after apply)
        }
    }

    Plan: 10 to add, 0 to change, 0 to destroy.

    Changes to Outputs:
      + FLOATING-IP = (known after apply)

    ------------------------------------------------------------------------

    Note: You didn't specify an "-out" parameter to save this plan, so Terraform
    can't guarantee that exactly these actions will be performed if
    "terraform apply" is subsequently run.
    ```
    {: codeblock} 

8. Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the ``input.auto.tfvars`` file to correct resources and run ``terraform plan`` again.

9. Create the virtual private cloud for SAP instance and IAM access policy in {{site.data.keyword.cloud_notm}}.

   ```
   terraform apply
   ```
   The virtual private cloud and components are created and you see output similar to the terraform plan output.  


## Next steps

If you need to rename your resources after they are created, modify the ``input.auto.tfvars`` file to change the names and run ``terraform plan`` and ``terraform apply`` again. Do not use the {{site.data.keyword.cloud_notm}} Dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results. 

If you need to remove your VPC, go to your project folder and run ``terraform destroy``.