---

copyright:
  years: 2022
lastupdated: "2022-12-16"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads

subcollection: sap

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:note: .note}
{:tip: .tip}
{:important: .important}

# Deploying your Infrastructure
{: #bm-vpc-set-up-infrastructure}

Use the following information to deploy and set up SAP HANA and/or SAP NetWeaver on {{site.data.keyword.cloud}} Bare Metal Servers for Virtual Private Cloud (VPC).
{: shortdesc}


## Setting up a VPC and subnet
{: #bm-vpc-establish-vpc}

The following steps summarize the setup of VPC and subnets, which is detailed further in the [Create an {{site.data.keyword.vpc_short}}](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console).


1. Click **Menu icon** ![Menu icon](../../icons/icon_hamburger.svg) > **VPC Infrastructure** > **Network** > **VPCs**
1. Click **Create**.
1. Select a **Location** for the VPC. The location consists of a *Geography* and a *Region*.
1. Enter a unique **Name** for the VPC.
1. Select a **Resource group**. Use resource groups to organize your account resources for access control and billing purposes. For more information, see [Best practices for organizing resources in a resource group](/docs/account?topic=account-account_setup) and [What makes a good resource group strategy?](/docs/account?topic=account-account_setup#resource-group-strategy).
1. _Optional:_ Enter tags to help you organize and find your resources. You can add more tags later. For more information, see [Working with tags](/docs/account?topic=account-tag).
1. Select whether the **Default security group** allows inbound SSH and ping traffic to bare metal server instances in this VPC. You can modify the default security group later.
1. _Optional: Classic access_. Select whether you want to enable your VPC to access classic infrastructure resources. For more information, see [Setting up access to classic infrastructure](/docs/vpc?topic=vpc-setting-up-access-to-classic-infrastructure).

    You can enable only a VPC for classic access when it is created. You cannot update a VPC to add or remove classic access. In addition, you can have only one classic access VPC in your account at any time.
    {: important}

1. _Optional: Default address prefixes_. Disable this option if you don't want to assign default subnet address prefixes to each zone in your VPC. After you create your VPC, you can go to its details page and set your own subnet address prefixes. If you do disable this option, the **Subnets** section will be hidden, and will require manual definition after the VPC is created. Leave the option enabled.
1. **Subnets**. You can keep the default subnets for each zone in the selected location or can delete one or more of them and/or create a new subnet. Leave the default subnets.
1. Review the configuration **Summary** and then click **Create virtual private cloud**.

### Manually defining your subnets
{: #bm-vpc-manually-defining-vpc-subnets}

If you disabled _Default address prefixes_, which hid the _Subnets_ section on the VPC ordering page, you need to manually define your subnets before you provision your bare metal server. Use the following steps to set up your subnets if you didn't do so when you set up your VPC.

1. Click **Menu icon** ![Menu icon](../../icons/icon_hamburger.svg) > **VPC Infrastructure** > **Subnets** > **New Subnet**.
1. Select a **Location** for the subnet. The location consists of a *Geography*, a *Region*, and a *Zone*.
1. Enter a unique **Name**
1. Select a **Resource group**.
1. _Optional:_ Enter tags to help you organize and find your resources. You can add more tags later. For more information, see [Working with tags](/docs/account?topic=account-tag).
1. Select the **Virtual private cloud** that the subnet is to be associated with.
1. Leave all other values default.

1. Review the configuration **Summary** and then click **Create virtual private cloud**.

   If you want to learn more on VPC and subnet creation, read section [*Creating a VPC and subnet*](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#creating-a-vpc-and-subnet) in the VPC product tutorials.
   {: note}

## Provisioning your Bare Metal Server
{: #bm-vpc-provision-server}

Before you can create a bare metal server, you must create the VPC, and you must create an **SSH key** that you add to the server instance during its creation. For more information, see [SSH keys with bare metal servers](/docs/vpc?topic=vpc-ssh-keys). You can either create the VPC and SSH key before you follow the steps, or you can click the related links within the *Create bare metal server UI* to do the same.
{: note}

Use the following steps to order your bare metal server and necessary components. For more information about creating a bare metal server, see [Creating a bare metal server by using the UI](/docs/vpc?topic=vpc-creating-bare-metal-servers#creating-using-ui).

1. Log in to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com){: external} with your unique credentials.
1. Click **Menu icon ![Menu icon](../../icons/icon_hamburger.svg) > Compute > Bare metal servers**.
1. Select the *Region* and click **Create**.
1. Enter the information that is in Table 1.
1. Review the configuration **Summary** and then click **Create bare metal server**.

After the bare metal server is provisioned and is ready for SSH logon, you can begin installing SAP HANA or SAP NetWeaver applications.

Table 1 is a summary of the fields and values that are used to provision Bare Metal Servers for Virtual Private Cloud (VPC).

| Field | Value |
|-------|-------|
| **Location** | Locations are composed of regions (specific geographic areas) and zones (fault tolerant data centers within a region). Select the location where you want to create your bare metal server. |
| **Name** | Unique name for your bare metal server, which becomes the **hostname**. SAP hostnames must consist of a maximum of 13 alpha-numeric characters. For more information about SAP hostnames, see [SAP Notes 611361](https://launchpad.support.sap.com/#/notes/611361){: external} and [129997](https://launchpad.support.sap.com/#/notes/129997){: external}. |
| **Resource group** | Use resource groups to organize your account resources for access control and billing purposes. |
| | **Note:** The resource group can't be changed after the bare metal server is created. |
| _Optional_: **Tags** | You can assign labels to your server so that you can easily filter resources in your resource list. |
| **Operating System** | <ul><li>Linux distribution for SAP HANA workloads, select **Catalog image** > **`...-sap-hana-...`**</li><li>Linux distribution for SAP NetWeaver workloads, select **Catalog image** > **`...-sap-applications...`**</li><li>Windows Server for SAP NetWeaver workloads, select Windows Server</li></ul>. Choose an operating system version that is supported by SAP as documented in [SAP Note 2927211](https://launchpad.support.sap.com/#/notes/2927211){: external} |
| **Profile** |  Select one of the profiles that are outlined in [Bare Metal Server certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-bm-vpc) or [Bare Metal Server certified profiles for SAP NetWeaver](/docs/sap?topic=sap-nw-iaas-offerings-profiles-intel-bm-vpc). |
| **SSH Key** | Select an existing public SSH key or click **New SSH key** to add a new one. You must specify at least one SSH key. SSH keys are used to securely connect to a running instance. |
| | **Note:** Alpha-numeric combinations are limited to 100 characters. For more information, see [SSH keys](/docs/vpc?topic=vpc-ssh-keys). |
| _Optional_: **User data** | You can add user data to automatically complete common configuration tasks or run scripts. For more information, see [User data](/docs/vpc?topic=vpc-user-data). |
| **Virtual private cloud** | Specify the VPC where you want to create your server. You can use the default VPC, another existing VPC, or you can create a new VPC. |
| **Network interfaces** | Assign networking options to connect into the {{site.data.keyword.cloud_notm}} VPC. You can create and assign multiple up to 8 PCI network interfaces and 128 VLAN interfaces on top of network interfaces. |
{: caption="Table 1. Bare metal server provisioning selections" caption-side="top"}

## Creating the network interface
{: #bm-vpc-creating-network-interface}

Bare metal servers for VPC can be configured with two different types of network interfaces, up to 8 PCI (that is, physical interfaces) and 128 VLAN interfaces on-top the physical interfaces.

VLAN interfaces are strictly targeting advanced virtualization scenarios on top of the bare metal servers. Read the section [*Managing network interfaces for Bare Metal Servers on VPC*](/docs/vpc?topic=vpc-managing-nic-for-bare-metal-servers) carefully before you order the network interfaces.
{: note}

To add a new network interface to your Bare metal server for VPC instance, complete the following steps:
1.	In the Network interfaces section of the Instance details page, click **Create**. 
2.	On the Create network interface page, the interface name defaults to an incremented number. If this is the first new interface after your primary interface, the default name is *eth1*. You can change the name if you want. 
3.	Select a subnet from the subnets that are assigned to your existing network interfaces. 
4.	Select the method for choosing an IP address for the new interface. Either select a dedicated or let {{site.data.keyword.cloud}} reserve it for you. 
5.	Select the security group that you want to associate with the network interface. 
6.	Choose, if you want to create a VLAN or a PCI interface. For a VLAN interface, a VLAN ID needs to be provided. Choose *PCI interface* in this case.
7.	You can configure IPs as ‘floating’. For more information, see [Associating floating IPs with a network interface](/docs/vpc?topic=vpc-managing-nic-for-bare-metal-servers#add-fips-to-nic) for more information on floating IPs.
8.	Click **Create**. 

More information is shown on VPC Infrastructure - [Managing network interfaces](/docs/vpc?topic=vpc-using-instance-vnics).

## Bring your own OS product license
{: #bm-vpc-os-byol}

When you have your own operating system license, you can install it on your bare metal server based on the vendor's instructions. For more information about custom images, see [Importing and managing custom images](/docs/vpc?topic=vpc-managing-images).

The OS chosen must be certified for SAP and have access to the necessary OS Packages for SAP.

## Storage layout for Bare Metal Servers on VPC
{: #bm-vpc-storage-layout}

### Internal storage
{: #bm-vpc-internal-storage}

Your Bare metal server on VPC comes with a number of internal NVMEs, depending on its size. For SAP NetWeaver based deployment, you can use those NVMEs that are presented as block devices to the operating system. The NVMEs are listed under “/dev/nvmeXn1” (X from 0 to the number of NVMEs in total, minus 1). Use the NVMEs according to your requirements and needs. For SAP HANA based deployments, refer to [Bare Metal Server certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-bm-vpc) to see the details on laying out the storage.

### External storage
{: #bm-vpc-external-storage}

If more storage needs to be added to your bare metal server on VPC, for example, for backup purposes, NFS-based `file shares` can be created and mounted. Learn more details in the corresponding chapter [Creating file shares and mount targets](/docs/vpc?topic=vpc-file-storage-create).

