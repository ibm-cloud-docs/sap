---

copyright:
  years: 2020, 2025
lastupdated: "2025-02-02"

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

# Deploying your infrastructure
{: #vs-set-up-infrastructure}

Use the following information to deploy and set up SAP HANA and SAP NetWeaver on {{site.data.keyword.vsi_is_full}}.
{: shortdesc}


## Setting up a Virtual Private Cloud (VPC) and subnet
{: #vs-establish-vpc}

Before you can create a virtual server, you must create a VPC.
{: important}

The following steps summarize the setup of VPC and subnets, which is detailed further in the [Create an {{site.data.keyword.vpc_short}}](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console).


1. Click **Menu icon** ![Menu icon](../icons/icon_hamburger.svg) > **VPC Infrastructure** > **Network** > **VPCs**
1. Click **Create**.
1. Enter a unique **Name** for the VPC.
2. Select a **Resource group**. Use resource groups to organize your account resources for access control and billing purposes. For more information, see [Best practices for organizing resources in a resource group](/docs/account?topic=account-account_setup) and [What makes a good resource group strategy?](/docs/account?topic=account-account_setup#resource-group-strategy).
3. _Optional:_ Enter tags to help you organize and find your resources. You can add more tags later. For more information, see [Working with tags](/docs/account?topic=account-tag).
4. Select whether the **Default security group** allows inbound SSH and ping traffic to virtual server instances in this VPC. We'll configure more rules for the default security group later.
5. _Optional: Classic access_. Select whether you want to enable your VPC to access classic infrastructure resources. For more information, see [Setting up access to classic infrastructure](/docs/vpc?topic=vpc-setting-up-access-to-classic-infrastructure).

    You can only enable a VPC for classic access when it is created. You cannot update a VPC to add or remove classic access. In addition, you can have only one classic access VPC in your account at any time.
    {: important}

6. _Optional: Default address prefixes_. Disable this option if you don't want to assign default subnet address prefixes to each zone in your VPC. After you create your VPC, you can go to its details page and set your own subnet address prefixes. If you do disable this option, the **New subnet for VPC** section will be hidden, and will require manual definition after the VPC is created. Leave the value default.

### New subnet for VPC
{: #vs-new-subnet-for-vpc}

1. Enter a unique **Name** for the VPC subnet.
1. Select a **Resource group** for the subnet.
1. Select a **Location** for the subnet. The location consists of a region and a zone.

    The region that you select is used as the region of the VPC. All additional resources that you create in this VPC are created in the selected region.
    {: tip}

1. Enter an **Address prefix**, **Number of addresses**, and an **IP range** for the subnet. The IP range is entered in CIDR notation, for example: `10.240.0.0/24`. In most cases, you can use the default IP range. If you want to specify a custom IP range, you can use the IP range calculator to select a different address prefix or change the number of addresses.

    A subnet cannot be resized after it has been created.
    {: important}

1. Attach a public gateway to the subnet if you want to allow all attached resources to communicate with the public internet.

    You can also attach the public gateway after you create the subnet.
    {: tip}

1. Click **Create virtual private cloud**.

### Manually defining your subnets
{: #vs-manually-defining-vpc-subnets}

If you disabled _Default address prefixes_, which hid the _New subnet for VPC_ section on the VPC ordering page, you need to manually define your subnets before provisioning your {{site.data.keyword.vsi_is_short}}. Use the following steps to set up your subnets if you didn't do so when you set up your VPC.

1. Click **Menu icon** ![Menu icon](../icons/icon_hamburger.svg) > **VPC Infrastructure** > **Subnets** > **New Subnet**.
1. Enter a unique **Name** and select the **Virtual private cloud** that it's to be associated with.
1. Select a **Resource group**.
1. Select **Location**. The location consists of a region and zone. If you're manually creating the initial subnet, versus creating it when you created your VPC, the region (location) that you select is used as the region of the VPC. All additional resources that you in this VPC are created in the selected region.

   If you created a subnet when you created your VPC, more subnets must be created in the same region. You can create multiple subnets within VPC zones.

1. Click **Create subnet**.


## Provisioning your Intel virtual server
{: #vs-provision-server}

Before you can create a virtual server, you must create the VPC and you must create an **SSH key** that you add to the server instance during its creation - see more details on [SSH keys with virtual servers](/docs/vpc?topic=vpc-ssh-keys).
{: important}

Use the following steps to order your virtual server and necessary components. For more information about creating a virtual server, see [Creating virtual server instances by using the {{site.data.keyword.cloud_notm}} console](/docs/vpc?topic=vpc-creating-virtual-servers).

1. Log in to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com){: external} with your unique credentials.
1. Click **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > VPC Infrastructure > Virtual server instances**.
1. Click **Create**.
1. Enter a unique **Name** for the virtual server, which becomes the **hostname**. SAP hostnames must consist of a maximum of 13 alpha-numeric characters. For more information about SAP hostnames, see [SAP Notes 611361](https://me.sap.com/notes/611361){: external} and [129997](https://me.sap.com//#/notes/129997){: external}.
1. Choose a **Resource group**.

   The resource group can't be changed after the virtual server is created.
   {: note}

1. _Optional:_ Enter tags to help you organize and find your resources. You can add more tags later. For more information, see [Working with tags](/docs/account?topic=account-tag).
1. Select the same **Location** in which you created your VPC and subnets. The location consists of a region and zone.
1. Select your preferred **Operating System** from either Windows Server, Red Hat Linux or SUSE Linux to run SAP Netweaver, or from Red Hat or SUSE to run SAP HANA; you find the Linux versions in the tile **Catalog image** > **`...-sap-hana-...`** as the OS images for SAP HANA, or **Catalog image** > **`...-sap-applications-...`** as the OS images for SAP NetWeaver.
1. Select a **Profile** based on the guidance detailed in [Intel Virtual Server certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc) or [Intel Virtual Server certified profiles for SAP NetWeaver](/docs/sap?topic=sap-nw-iaas-offerings-profiles-intel-vs-vpc), which lists the profiles that are certified for SAP HANA and SAP NetWeaver.
1. Select the **SSH key** you want to add to the virtual server. For this step, you can create a new SSH key.
1. Click **New volume** for **Data volumes**. Data volumes are required.
   - For SAP HANA, these volumes must meet special KPI needs that are defined by SAP and are mandatory. See [Storage specifications - Intel Virtual Server certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc#hana-iaas-intel-vs-vpc-storage-specs) or [Storage design considerations](/docs/sap?topic=sap-storage-design-considerations) to learn these special needs and how you should configure the data volumes.
   - For SAP NetWeaver, these volumes are based on the requirements of the installed SAP NetWeaver instance. The standard tiered options are 3K, 5K, and 10K IOPS, and custom IOPS. These options can be used to attune to the specific requirements.
1. You can enable **Auto Delete** to automatically delete the data volume if the virtual server is deleted. This option is not recommended.
1. **Attach** the appropriate data volumes to your virtual server.
1. Under **Networking**, select the **Virtual private cloud** in which to attach the virtual server.
1. Under **Network interfaces**, the first virtual network interface is shown attached to the default subnet of the VPC. This can be amended as required.
1. Click **Create virtual server instance**. After the {{site.data.keyword.vsi_is_short}} is provisioned and ready for SSH logon, you can begin installing SAP HAAN or SAP NetWeaver applications.


Table 1 is a summary of the fields and values that are used to provision {{site.data.keyword.vsi_is_short}}.

| Field | Value |
|-------|-------|
| Name  | Unique name for your virtual server. |
| Virtual private cloud | Specify the VPC where you want to create your virtual server. |
| Resource group | Use resource groups to organize your account resources for access control and billing purposes. |
| Location | Locations are composed of regions (specific geographic areas) and zones (fault tolerant data centers within a region). Select the location where you want to create your virtual server. |
| Operating System | <ul><li>Linux distribution for SAP HANA workloads, select **Catalog image** > **`...-sap-hana-...`**</li><li>Linux distribution for SAP NetWeaver workloads, select **Catalog image** > **`...-sap-applications...`**</li><li>Windows Server for SAP NetWeaver workloads, select Windows Server</li></ul>. Choose an operating system version that is supported by SAP as documented in [SAP Note 2927211](https://me.sap.com/notes/2927211){: external} |
| Profile |  Select one of the profiles outlined in [Intel Virtual Server certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc) or [Intel Virtual Server certified profiles for SAP NetWeaver](/docs/sap?topic=sap-nw-iaas-offerings-profiles-intel-vs-vpc). |
| SSH Key | You must select an existing SSH key or upload a new SSH key before you can create the instance. SSH keys are used to securely connect to a running instance. |
| | **Note:** Alpha-numeric combinations are limited to 100 characters. For more information, see [SSH keys](/docs/vpc?topic=vpc-ssh-keys). |
| User data | You can add user data to automatically complete common configuration tasks or run scripts. For more information, see [User data](/docs/vpc?topic=vpc-user-data). |
| Boot volume | The default boot volume size for all profiles is 100 GB. You can edit the boot volume by clicking the pencil icon.|
| Data volume  | You can add one or more secondary data volumes when you provision the instance. To add volumes, click **New volume**. For more information about provisioning the volume, see [Create and attach a block storage volume when you create a new instance](/docs/vpc?topic=vpc-creating-block-storage#create-from-vsi). Attach data volumes to your virtual server depending on your SAP HANA or SAP NetWeaver virtual server profile. |
| Network interfaces | Assign networking options to connect into the {{site.data.keyword.cloud_notm}} VPC. You can create and assign up to five network interfaces to each instance - see below. |
{: caption="Instance provisioning selections" caption-side="top"}


## Adding {{site.data.keyword.block_storage_is_short}}
{: #vs-adding-vpc-block-storage}

1. Log in to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com){: external} with your unique credentials.
1. Click **Menu icon ![Menu icon](../icons/icon_hamburger.svg)** > **VPC Infrastructure** > **Block storage volumes** > **New Volume**.
1. Enter a unique **Name**.
1. Choose a **Resource group**.

   The resource group can't be changed after you create the virtual server.
   {: important}

1. Select the same **Location** in which you created your {{site.data.keyword.vsi_is_short}}. The location consists of a region and zone.
1. Select either **IOPS tiers** or **Custom**. As described in [Storage design considerations](/docs/sap?topic=sap-storage-design-considerations), the IOPS per GB depends on the workload anticipated.
1. Click **Create volume**.

   By default all block storage for VPC volumes use IBM-managed encryption. However, [new capabilities are available for customer-managed encryption](/docs/vpc?topic=vpc-block-storage-vpc-encryption).
   {: note}

1. Click **Menu icon ![Menu icon](../icons/icon_hamburger.svg)** > **VPC Infrastructure** > **Virtual server instances**.
1. Select the virtual server to which you want the new block storage to be attached.
1. Click **Attach volume**.
1. Select the new block storage volume and click **Attach**.
1. Log in to the virtual server over SSH, and perform the necessary activities for the block storage, such as add file system partitions, file systems, and mount points.

   See here more information about [Attaching a Block Storage for VPC volume](/docs/vpc?topic=vpc-attaching-block-storage).
   {: note}


## Creating the network interface
{: #vs-creating-network-interface}

For each network interface (vNIC) added, define the following:
- Network interface name (defaults to eth0 and so on, but this name is not what appears on the OS when it is running `ip link`)
- Subnet to assign to this network interface into

During virtual server provisioning, each virtual server requires a Primary network interface. If you are using a Floating IP, by default it is attached to the primary network interface.

By default during provisioning of a virtual server, the primary network interface is already added with the default Security Group. After a virtual server is created, the network interface is assigned its own Private IP from the selected Subnet range.


### Throughput of network interfaces
{: #vs-creating-network-interface-throughput}

Every virtual server profile has a maximum network bandwidth of 2 Gbps per vCPU, with a cap of 80 Gbps. Network bandwidth is distributed evenly across network interfaces. Each network interface has a cap of 16 Gbps that might limit the overall performance. You might need to attach multiple network interfaces to your virtual server instance to optimize network performance.

For example, if you choose a profile with 16 vCPU, the network cap for the profile is 32 Gbps. If you have just one network interface, the maximum network performance is 16 Gbps due to the network interface cap. You need to attach two network interfaces (16 Gbps each) to reach the profile cap of 32 Gbps.

For more information, see [VPC - Network performance notes for profiles](/docs/vpc?topic=vpc-profiles&interface=ui#bandwidth-allocation).


### Multiple network interfaces
{: #vs-creating-network-interface-multiple}

You can add up to 5 network interfaces (vNIC) to your virtual server instance, each is assigned with a single unique IP address from the selected Subnet range, which provides multiple IP addresses for your virtual server instance.

Each of the multiple networks interfaces attached to the virtual server are able to use the same Subnet or different Subnets. However, these subnets must be within the same Zone of the VPC.

You can create a new network interface during provisioning or after the virtual server is running.

After a new network interface is created, it is immediately available to the OS shown by using `ip link`.

If your virtual server instance is running when you complete the following steps to add the network interface, then it remains in `DOWN mode` and you must configure the network interface for the instance. You can either stop and then restart the instance, which puts the interface into `UP mode` and append to the route table, or you can manually configure the interface in the guest operating system _(for example, on a Linux-based operating system, you can use_ `ip link set dev <interface> up`_)_.


**To add a new network interface to your virtual server instance, complete the following steps:**

1. In the **Network interfaces** section of the Instance details page, click **New interface**.
2. On the **New network interface** page, the interface name defaults to an incremented number. If this is the first new interface after your primary interface, the default name is `eth1`. You can change the name if you want.
3. Select a subnet from the subnets that are assigned to your existing network interfaces.
4. Select the security group that you want to associate with the network interface.
5. Click **Create**.

More information is shown on [VPC Infrastructure - Managing network interfaces](/docs/vpc?topic=vpc-using-instance-vnics).


## Readying your Operating System
{: #vs-os}


### Bring your own OS product license
{: #vs-os-byol}

When you have your own operating system license, you can install it on your virtual server based on the vendor's instructions. For more information about custom images, see [Importing and managing custom images](/docs/vpc?topic=vpc-managing-custom-images&interface=ui).

Please consider, the OS chosen must be certified for SAP and have access to the necessary OS Packages for SAP.
