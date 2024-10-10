---

copyright:
  years: 2020, 2021
lastupdated: "2021-05-27"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, Quick Study Tutorial

subcollection: sap

content-type: tutorial
completion-time: 90m

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
{:step: data-tutorial-type='step'}

# SAP NetWeaver deployment to Intel Virtual Server on VPC Infrastructure that uses RHEL
{: #quickstudy-vs-gen2-netweaver-rhel}
{: toc-content-type="tutorial"}
{: toc-completion-time="90m"}

*A Quick Study, someone who is able to learn new things quickly.*
{: note}

These Quick Study Tutorials provide a single sample configuration, with less detailed instructions, as an introduction for customers who prefer hands-on tasks to increase their pace of learning.
{: shortdesc}

The following information provides an introduction for customers who are new to {{site.data.keyword.vpc_full}} (VPC) Gen 2 environment. Two sample configurations are provided to help you through the ordering process to the start of the SAP installation.

The first configuration sample is simple, a single node 128 GB, 32 vCPU server. The second is an advanced configuration of two nodes by adding a second virtual server to the landscape. The sample layouts might not be your preferred layout. The purpose of this guidance is to show you two possibilities if you are not experienced with the Linux&reg; operating system or with VPC Gen 2.

![Figure 1. {{site.data.keyword.cloud_notm}} VPC](/images/quickstudy-intel-vs-gen2-image1.png "{{site.data.keyword.cloud_notm}} VPC Gen 2"){: caption="{{site.data.keyword.cloud_notm}} VPC" caption-side="bottom"}


## Securing Access
{: #quickstudy-vs-gen2-netweaver-rhel-secure-access}
{: step}

Security is one of the biggest concerns when you run your business-critical applications in a cloud environment. To secure your connection to your {{site.data.keyword.virtualmachineslong}}, a public SSH key can be uploaded to your account, per region. These public keys are deployed to your virtual servers instances to allow access to the servers.

Before you continue, create an SSH public key that you can upload later to the region of your choice when you are creating the virtual server instance. Follow the steps that are [documented here](/docs/vpc?topic=vpc-ssh-keys).
{: tip}

You use security groups to restrict access to and from IP ranges, protocols, and ports. Security groups aren't within the scope of this guidance, and the default security group that is deployed with your sample VPC can suffice. However, you might have to add extra ports for exceptions to the access restrictions, such as, the SAP Software Provisioning Manager and for the ports that are being used by your SAP NetWeaver based application.

## Creating an {{site.data.keyword.vpc_short}} and subnet
{: #quickstudy-vs-gen2-netweaver-rhel-vpc-subnet}
{: step}

{{site.data.keyword.cloud}} compute resources are kept in a global region within a VPC. Use the following steps to create a VPC and its subnet.

1. Log in to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com){: external} with your unique credentials.
1. Click **Menu icon** ![Menu icon](../../icons/icon_hamburger.svg) > **VPC Infrastructure** > **Network** > **VPCs** and click **New virtual private cloud** > **Create VPC for Gen 2**.

![Figure 2. Creating a VPC](/images/quickstudy-intel-vs-gen2-image6.png "Creating a VPC"){: caption="Creating a VPC" caption-side="bottom"}

1. Enter a unique **Name** for the VPC, for example, *sap-test-inst*.
1. Keep the default **Resource group**. Use resource groups to organize your account resources for access control and billing purposes. For more information, see [Best practices for organizing resources in a resource group](/docs/account?topic=account-account_setup). For this example, you can use the default value.
1. _Optional: Tags_. Enter tags to help you organize and find your resources. You can add more tags later. For more information, see [Working with tags](/docs/account?topic=account-tag).
1. Keep the **Default security group** settings, which allow inbound SSH and ping traffic to virtual server instances in this VPC.
1. _Optional: Classic access_. Select whether you want to enable your VPC to access classic infrastructure resources. For more information, see [Setting up access to classic infrastructure](/docs/vpc?topic=vpc-setting-up-access-to-classic-infrastructure).

    You can enable a VPC for classic access only while you are creating it. In addition, you can have only one classic access VPC in your account at any time.
    {: important}

1. _Optional: Default address prefixes_. Disable this option if you don't want to assign default subnet address prefixes to each zone in your VPC. After you create your VPC, you can go to its details page and set your own subnet address prefixes. If you do disable this option, the **New subnet for VPC** section will be hidden, and will require manual definition after the VPC is created. Leave the default value.

    If you want to create the subnet and your own subnet address prefixes later, become familiar with important details of VPC networking. For more information, see [About networking for VPC](/docs/vpc?topic=vpc-about-networking-for-vpc) and [Designing an addressing plan for a VPC](/docs/vpc?topic=vpc-vpc-addressing-plan-design).
    {: note}

### New subnet for VPC
{: #new-subnet-for-vpc-sample}
{: step}

1. Enter a unique **Name** for the VPC subnet, for example, *sap-subnet1*.
1. Select a **Resource group** for the subnet. For this example, keep the value default.
1. Select a **Location** for the subnet, for example, *LON*, *London 3*. The location consists of a region and a zone.

    The region that you select is used as the region of the VPC. All additional resources that you create in this VPC are created in the selected region.
    {: tip}

1. Enter an **Address prefix**, **Number of addresses**, and an **IP range** for the subnet. The IP range is entered in CIDR notation, for example: *10.240.0.0/24*. In most cases, you can use the default IP range. If you want to specify a custom IP range, you can use the IP range calculator to select a different address prefix or change the number of addresses.

    A subnet cannot be resized after it is created.
    {: important}

1. Keep the default value for **Subnet access control list**. A new default ACL is created that you can configure later following these steps in: [Configuring the ACL](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#configuring-the-acl).

1. Attach a public gateway to the subnet if you want to allow all attached resources to communicate with resources on the public internet. However, keep in mind, that public gateways are for 'outbound traffic', 'inbound traffic' would require a **Floating IP**. [See Preparing the virtual server instance for your workload](#prepare-virtual-server).

    You can also attach the public gateway after you create the subnet.
    {: tip}

1. Click **Create virtual private cloud**. The VPC appears in the Virtual Private Clouds page immediately.


## Creating a Virtual Server Instance
{: #new-vrtual-server}
{: step}

Use the following steps to create a virtual server instance.

1. Click **Virtual server instances** > **New instance**.
1. Enter a unique **Name** for the virtual server, for example, `sap-app-vsi`. The name that you enter becomes the hostname.

    SAP hostnames must consist of a maximum of 13 alpha-numeric characters. See [SAP Note 611361](https://launchpad.support.sap.com/#/notes/611361){: external} for further details.
    {: important}

1. Select the **Virtual private cloud** in which to attach the virtual server instance, for example, `sap-test-inst`.
1. Keep the **Resource group** default.
1. _Optional: Tags_. Enter tags to help you organize and find your resources. You can add more tags later. For more information, see [Working with tags](/docs/account?topic=account-tag).
1. The **Location** in which you created your subnets is already selected. The location consists of a region and a zone.
1. Select **Catalog image** > **`ibm-redhat-7-6-amd64-sap-applications-1`** as the OS image.

![Figure 3. Catalog image for SAP NetWeaver](/images/quickstudy-intel-vs-gen2-image8.png "Catalog image for SAP NetWeaver"){: caption="Catalog image for SAP NetWeaver" caption-side="bottom"}

    For every SUSE Linux&reg; Enterprise and Red Hat&reg; Enterprise Linux&reg; version there are two different Catalog Images available each: one for SAP HANA and one for SAP NetWeaver (Applications). In these images, the specific repositories are enabled, so you can install the OS packages that are required to install SAP HANA or SAP NetWeaver.
    {: note}

1. Click **All profiles** > **Balanced** and select *bx2-32x128*. For more information about SAP-certified profiles, see [Intel Virtual Server certified profiles for SAP NetWeaver](/docs/sap?topic=sap-nw-iaas-offerings-profiles-intel-vs-vpc).

![Figure 4. Balanced Profiles for SAP NetWeaver](/images/quickstudy-intel-vs-gen2-image9.png "Balanced Profiles for SAP NetWeaver"){: caption="Balanced profiles for SAP NetWeaver" caption-side="bottom"}

### Setting an SSH key
{: #ssh-key}
{: step}

If you uploaded your public key for the VPC's region, select it and skip to the next section (Attaching storage).
Otherwise, follow these steps.

1. Click **New key**.
1. Enter a unique **Name**, for example, *sap-ssh-key*.
1. Keep the default **Resource group**.
1. The **Region** in which you created your subnets is already selected.
1. _Optional: Tags_. Enter tags to help you organize and find your resources. You can add more tags later. For more information, see [Working with tags](/docs/account?topic=account-tag).
2. Paste the **Public key**, that you created according to [the guidelines mentioned in Securing Access](#quickstudy-vs-gen2-netweaver-rhel-secure-access).
3. Click **Add SSH key**.

4. _Optional:_ **User data**, leave blank.

### Attaching a block storage volume
{: #attach-block}
{: step}

To have file system space available beyond what is required by the operating system, you need to attach a block storage volume to your virtual server instance. This storage volume is used by the application that you're installing. In this example, the application is the Relational Database Management System (RDBMS) required for an SAP NetWeaver stack.

1. Click **New volume**.
1. Enter *sap-app-vol1* for **Name**.
1. Select *Custom* for **Profile**.
1. Enter *500* for **Size**.
1. Enter *10000* for **IOPS**. **Throughput** defaults to *156.25 MiBps*.
1. Keep the **Encryption** and **Auto Delete** defaults.

![Figure 5. Attaching a block storage volume](/images/quickstudy-intel-vs-gen2-image10.png "Attaching a block storage volume"){: caption="Attaching a block storage volume" caption-side="bottom"}

1. Click **Attach**.
1. Keep the **Network interfaces** default.
1. Click **Create virtual server instance**. After the {{site.data.keyword.vsi_is_short}} is provisioned and ready for SSH logon, you can begin installing the SAP NetWeaver applications.

## Preparing the virtual server instance for your workload
{: #prepare-virtual-server}
{: step}

{{site.data.keyword.virtualmachineslong}} are accessed through IPsec connections into your VPC. Configuring IPsec-based access to virtual server instances is beyond the scope of this guidance. For simplicity, and to quickly access the deployed instance, you can assign a _Floating IP_ to your virtual server instance. This IP is assigned to a gateway that forwards ports and protocols according to the defined security groups.

![Figure 6. Floating IP](/images/quickstudy-intel-vs-gen2-image12.png "Floating IP"){: caption="Floating IP" caption-side="bottom"}

By assigning the IP, you can directly `ssh` into your virtual server instance - in our example, the command is
```
ssh -i ~/.ssh/sap-ssh-key root@158.176.180.39
```

To update the operating system for your virtual server instance to the latest level, run `yum update` and restart the virtual server instance.

The SAP NetWeaver Software Provisioning Manager (SWPM) doesn't allow products to install to hostnames that don't resolve to an external IP on the server instance. Because of this restriction, the default settings in the virtual server instance need to be adapted. Edit **`/etc/hosts`** and comment out the lines that resolve the hostname to the IPv4 AND IPv6 localhost addresses. Instead, the hostname must resolve to the external IP address of your virtual server (see the example). In our example, the hostname resolves to `10.242.128.8`, the private IP displayed in Figure 6. In this example, we append a sample default domain. Adapt this example to your specific environment.

These lines are an example of an **`/etc/hosts`** file. Take care that both references of localhost to the hostname, IPv4 and IPv6 are in comments (or deleted).

```
  # The following lines are desirable for IPv4 capable hosts

  #127.0.0.1 sap-app-vsi sap-app-vsi
  127.0.0.1 localhost.localdomain localhost
  127.0.0.1 localhost4.localdomain4 localhost4

  # The following lines are desirable for IPv6 capable hosts

  #::1 sap-app-vsi sap-app-vsi
  ::1 localhost.localdomain localhost
  ::1 localhost6.localdomain6 localhost6
  10.242.128.8 sap-app-vsi.saptest.com sap-app-vsi
```

To prevent the {{site.data.keyword.cloud_notm}} `cloudinit` process from reverting the content of **`/etc/hosts`** to the previous values on the next restart, change the configuration in `/etc/cloud/cloud.cfg`. Change `manage_etc_host` from `True` to `False`.

Finally, you need to adapt your storage by creating a file system on the attached storage volume. You can identify the newly attached volume by its size by entering `/sbin/fdisk -l` and checking the sizes. To safely identify it, find the device ID by clicking **Device** on the Virtual server instances for VPC page.

![Figure 7. Data volumes](/images/quickstudy-intel-vs-gen2-image13.png "Data volumes"){: caption="Data volumes" caption-side="bottom"}

1. On the Overview page, check the first 20 digits in **Device**, and find the same ID under `/dev/disk/by-id`. Our example device is `07a7-184b...`.
  ```
  [root@sap-app-vsi ~]# ls -als /dev/disk/by-id/ | grep 07a7-184b4a2f-d768-4
  0 lrwxrwxrwx 1 root root   11 May   3 08:30 virtio-07a7-184b4a2f-d786-4 -> ../../vdb
  ```
In our example, it's `virtio-07a7-184b4a2f-d786-4`, which is linked to `/dev/vdb`.

1. Create a file system on this path:
  ```
  [root@sap-app-vsi ~]# mkfs.xfs /dev/vdb
  ```

1. Find the related UUID in `/dev/disk/by-uuid`:
  ```
  [root@sap-app-vsi ~]# ls -als /dev/disk/by-uuid/ | grep vdb
  0 lrwxrwxrwx 1 root root  11 May 10 08:31 1350230e-8058-4fe5-bbc0-cc27253ff778 -> ../../vdb
  ```

1. Add the UUID to `/etc/fstab`, in our example:
  ```
  UUID=1350230e-8058-4fe5-bbc0-cc27253ff778 /db2 xfs defaults 0 0
  ```

1. Create a file system to use for the greater part of your installation, since we are using IBM Db2, we choose:
  ```
  [root@sap-app-vsi ~]# mkdir /db2
  [root@sap-app-vsi ~]# mount /db2
  ```

1. Add swap space for SWPM. We are adding a minimum swap space to the system.
  ```
  [root@sap-app-vsi ~]# dd if=/dev/zero of=/swapfile bs=1M count=8192
  8192+0 records in
  8192+0 records out
  8589934592 bytes (8.6 GB) copied, 24.701 s, 348 MB/s

  [root@sap-app-vsi ~]# chmod 0600 /swapfile
  [root@sap-app-vsi ~]# mkswap /swapfile
  Setting up swapspace version 1, size = 8388604 KiB
  no label, UUID=e7a63777-521a-44a7-abcc-0d17e1876a78
  ```

1. Add the following line to your `/etc/fstab`.
  ```
  /swapfile    none    swap    sw      0 0
  ```

1. Activate the swap space:
  ```
  [root@sap-app-vsi ~]# swapon -a
  ```


You're now ready to install the SAP product of your choice. Your next step is to [download and install your SAP software and applications](/docs/sap?topic=sap-download-install-media) if a single virtual server sample is sufficient for your needs.

## Installing two virtual server instances in a 3-tier setup
{: #3-tier}
{: step}

A more complex scenario involves installing two virtual servers. One server is the SAP NetWeaver Application Server (`sap-app-vsi`) and the other server is the database server for SAP NetWeaver. Given that we have two virtual servers of the same layout, per the example, Figures 8 and 9 are an overview of the virtual servers.

![Figure 8. Virtual server instances](/images/quickstudy-intel-vs-gen2-image14.png "Virtual server instances"){: caption="Virtual server instances" caption-side="bottom"}

![Figure 9. Block storage volumes for VPC](/images/quickstudy-intel-vs-gen2-image15.png "Block storage volumes for VPC"){: caption="Block storage volumes for VPC" caption-side="bottom"}

Both virtual servers have one extra attached volume and a _Floating IP_. A smaller volume is attached to `sap-app-vsi`, which is the application server. `sap-app2-vsi` has a slightly larger volume to host the RDBMS and the SAP Central Services (ASCS) instance. A second volume is needed on `sap-app2-vsi` to host the SAP NetWeaver stack. Create another volume from the Block storage volumes for VPC page and name it `sap-app2-vol2`. Attach `sap-app2-vol2` to our virtual server by selecting its details screen.

![Figure 10. Block storage volumes for VPC](/images/quickstudy-intel-vs-gen2-image16.png "Block storage volumes for VPC"){: caption="Block storage volumes for VPC" caption-side="bottom"}

## Preparing your network
{: #prepare-network-3-tier}
{: step}

To segregate network traffic, as SAP recommends, deploy a second subnet. One network is used for client access, the other for communication between the SAP ABAP stack and the RDBMS.

Use Figure 11 as your guide to create a new subnet named `sap-test-net2`.

Click **Menu icon** ![Menu icon](../../icons/icon_hamburger.svg) > **VPC Infrastructure** > **Network** > **Subnets** and click **New subnet**.

![Figure 11. Create a subnet](/images/quickstudy-intel-vs-gen2-image2.png "Create a subnet"){: caption="Create a subnet" caption-side="bottom"}

After the new subnet is created, it is displayed on the Subnets for VPC page.

![Figure 12. Subnets for VPC page](/images/quickstudy-intel-vs-gen2-image3.png "Subnets for VPC page"){: caption="Subnets for VPC page" caption-side="bottom"}

The two virtual servers need to connect to the new network. Go back to the virtual server overview and click **New interface**.

![Figure 13. Data volumes](/images/quickstudy-intel-vs-gen2-image4.png "Data volumes"){: caption="Data volumes" caption-side="bottom"}

Maintain your `/etc/hosts` files according to the targeted setup. The following example is for `sap-app2-vsi`.

```
  # The following lines are desirable for IPv4 capable hosts

  #127.0.0.1 sap-app2-vsi sap-app2-vsi
  127.0.0.1 localhost.localdomain localhost
  127.0.0.1 localhost4.localdomain4 localhost4

  # The following lines are desirable for IPv6 capable hosts

  #::1 sap-app2-vsi sap-app2-vsi
  ::1 localhost.localdomain localhost
  ::1 localhost6.localdomain6 localhost6
  10.243.128.9 sap-app2-vsi.saptest.com sap-app2-vsi
  10.243.129.6 sap-app2-vsi-priv.saptest.com sap-app2-vsi-priv
  10.243.128.7 sap-app-vsi.saptest.com sap-app-vsi
  10.243.129.4 sap-app-vsi-priv.saptest.com sap-app-vsi-priv
```

## Preparing your storage
{: #prepare-storage-3-tier}
{: step}

You need to provision two volumes on the database virtual server with a file system for the database and for the SAP installation. In addition, `/sapmnt` needs to be Network File System (NFS) exported to the application server virtual server.

The application server virtual server has only one attached 20 GB volume. You can identify the volume without looking at the resource ID.

```
Disk /dev/vdd: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

We create a file system on the volume and mount it after you determine its `/dev/disk/by-uuid` path.

```
ls -als /dev/disk/by-uuid/ | grep vdd
0 lrwxrwxrwx 1 root root 9 May 13 03:23 cf5d6692-4176-47c4-b799-039c11103fd4 -> ../../vdd
```

The resulting `/etc/fstab` entry is in the following example.

```
UUID=cf5d6692-4176-47c4-b799-039c11103fd4 /usr/sap xfs defaults 0 0
```

You need to create the mount point and mount it.

```
[root@sap-app-vsi ~]# mkdir /usr/sap
[root@sap-app-vsi ~]# mount -a
```

On the database virtual server, you need to create three file systems. One for the RDBMS installation and two for `/usr/sap` and `/sapmnt`. Both attached volumes are created the same way and display as `/dev/vdb` and `/dev/vde`. In our example, we split the first file system in two partitions.

```
[root@sap-app2-vsi ~]# /sbin/fdisk /dev/vdb
Welcome to fdisk (util-linux 2.23.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.
Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0x8f5f8b5e.
Command (m for help): n
Partition type:
p primary (0 primary, 0 extended, 4 free)
e extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-419430399, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-419430399, default 419430399): +100G
Partition 1 of type Linux and of size 100 GiB is set
Command (m for help): n
Partition type:
p primary (1 primary, 0 extended, 3 free)
e extended
Select (default p): p
Partition number (2-4, default 2):
First sector (209717248-419430399, default 209717248):
Using default value 209717248
Last sector, +sectors or +size{K,M,G} (209717248-419430399, default 419430399):
Using default value 419430399
Partition 2 of type Linux and of size 100 GiB is set
Command (m for help): w
The partition table has been altered!
Calling ioctl() to re-read partition table.
Syncing disks.
```

Create the three file systems (output isn't shown in this example).

```
[root@sap-app2-vsi ~]# mkfs.xfs /dev/vdb1
[root@sap-app2-vsi ~]# mkfs.xfs /dev/vdb2
[root@sap-app2-vsi ~]# mkfs.xfs /dev/vde
[root@sap-app2-vsi ~]# mkdir /usr/sap /sapmnt /db2
```

Again, you need to determine the `/dev/disk/by-uuid` paths, as previously shown, and maintain `/etc/fstab` entries. As a final step, you need to set up the NFS to install SAP.

1. Install the NFS utilities on both virtual servers.
   ```
   [root@sap-app-vsi ~]# yum install nfs-utils
   ```
   ```
   [root@sap-app2-vsi ~]# yum install nfs-utils
   ```

1. Start the NFS server on the database virtual server.
   ```
   [root@sap-app2-vsi ~]# systemctl enable nfs-server
   [root@sap-app2-vsi ~]# systemctl start nfs-server
   ```

1. Use NFS to export /sapmnt and /usr/sap/trans from the database server to the application server by adding the required entry to /etc/exports of the database server:
   ```
   /sapmnt/C10 10.243.129.0/24(rw,no_root_squash,sync,no_subtree_check)
   /usr/sap/trans 10.17.139.0/24(rw,no_root_squash,sync,no_subtree_check)
   ```

    You need to adapt the subnet in the previous example to your actual IP range and subnet mask. Replace the value `C10` with the SAP System ID for your SAP system. `C10` is a sample value. You must create the directory before you export it.

1. Run on the following command from the command line.
   ```
   [root@sap-app2-vsi ~]# mkdir /sapmnt/C10
   [root@sap-app2-vsi ~]# mkdir -p /usr/sap/trans
   [root@sap-app2-vsi ~]# exportfs -a
   ```

1. Mount the NFS share on the application server by adding the following entry to its `/etc/fstab` and mount the application server from the command line by using the following command.
   ```
   [root@sap-app-vsi ~]# vi /etc/fstab
   ...
   sap-app2-vsi-priv:/sapmnt/C10 /sapmnt/C10 nfs defaults 0 0
   sap-app2-vsi-priv:/usr/sap/trans /usr/sap/trans nfs defaults 0 0
   ```

1. Create and mount the target directory.
   ```
   [root@sap-app-vsi ~]# mkdir /sapmnt/C10
   [root@sap-app-vsi ~]# mkdir /usr/sap/trans
   [root@sap-app-vsi ~]# mount /sapmnt/C10
   [root@sap-app-vsi ~]# mount /usr/sap/trans
   ```

Your servers are now prepared to host the components of a distributed SAP installation. For more information about more installation preparations, see [Downloading and installing SAP software and applications](/docs/sap?topic=sap-download-install-media).

## Installing your SAP landscape
{: #install-sap}
{: step}

### RPM package prerequisites
{: #rpm-package-prereqs}
{: step}

An SAP installation requires that certain prerequisites are met regarding the packages that are installed on the OS and the OS daemons that are running. See the latest installation guides and support notes from SAP for an up-to-date list of these prerequisites. Currently, the following extra packages are required for an SAP NetWeaver installation:
    - `compat-sap-c++-7`: Achieves compatibility of the C++ runtime with the compilers that are used by SAP
    - `uuidd`: Maintains OS support for the creation of UUIDs
    - `csh`: C shell support for the OS

1. Follow [SAP note 2195019](https://launchpad.support.sap.com/#/notes/2195019){: external} and install package `compat-sap-c++-7`. Create a specific soft-link, which is required by the SAP binary files.
   ```
   [root@sap-app-vsi ~]# yum install compat-sap-c++-7
   ...

   [root@sap-app-vsi tmp]# mkdir -p /usr/sap/lib
   [root@sap-app-vsi tmp]# ln -s /opt/rh/SAP/lib64/compat-sap-c++-7.so /usr/sap/lib/libstdc++.so.6
   ```

1. Check if uuid daemon (uuidd) is installed. If it’s not, install and start it.
   ```
   [root@sap-app-vsi ~]# rpm -qa | grep uuidd
   [root@sap-app-vsi ~]# yum install uuidd
   [root@sap-app-vsi ~]# systemctl enable uuidd
   [root@sap-app-vsi ~]# systemctl start uuidd
   ```

1. Install the tcsh package required for C shell support
   ```
   [root@sap-app-vsi ~]# yum install tcsh
   ```

### Installing the {{site.data.keyword.cloud_notm}} Metrics Collector for SAP
{: #install-metrics-collector}
{: step}

SAP requires the installation of the {{site.data.keyword.cloud_notm}} Metrics Collector for SAP to analyze your infrastructure if a support case is submitted. Install the collector by using the instructions in [{{site.data.keyword.cloud_notm}} Metrics Collector for SAP](/docs/sap?topic=sap-ibm-metrics-collector-for-sap-linux).

### Downloading your SAP software
{: #download-sap-software}
{: step}

Log in to the SAP Service Marketplace (https://support.sap.com/swdc) and download the required digital versatile discs (DVDs) to a local share drive and then transfer the DVDs to your provisioned server. Alternative option, download the SAP Software Download Manager, install it on your target server and directly download the DVD images to the server. For more information about the SAP Software Download Manager, see [http://service.sap.com](http://service.sap.com/){: external}.

You need an S-User ID and the Download Software authorization when you download the DVDs from the SAP Service Marketplace. To request an S-USer ID, see the [SAP Support Portal](https://support.sap.com/en/index.html).
{: note}

### Preparing for SAP’s Software Provisioning Manager (SWPM)
{: #prepare-swpm}
{: step}

Depending on your network bandwidth and latency, you might need to run the SAP SWPM GUI remotely in a virtual network computing (VNC) session. Another option is to run GUI locally and connect it to SWPM on the target machine. For the first option, you must run X11 in your virtual server and install a VNC server and a browser. You can run the browser locally on your desktop, and connect to SWPM in the virtual server. To connect to SWPM, check the port that SWPM is listening on. SWPM displays the port during startup when it lists the access URL. The port, typically 4237, needs to open in the security group of your VPC. You need to add a **New Inbound rule** for your IP source range (or 0.0.0.0/0) and the port number. Another possibility, and even more secure, is to tunnel the port through `ssh`.

```
[root@sap-app-vsi ~]# ssh -L 4237:localhost:4237 <your virtual server IP>
```

-L option for local tunnels and connecting your browser to that localhost port, instead of the remote IP. Remember to add the ports that are required by your SAP application (example: ports 3200-3299, depending on your SAP NetWeaver instance number) to the security group. For more information about ports, see [SAP ports](https://help.sap.com/viewer/ports){: external} for details.

![Figure 14. All security groups for VPC](/images/quickstudy-intel-vs-gen2-image5.png "All security groups for VPC"){: caption="All security groups for VPC" caption-side="bottom"}

### Installing SAP software
{: #install-sap-software}
{: step}

After you download the installation media, follow the standard SAP installation procedure that is documented in the [SAP installation guides](https://help.sap.com/viewer/index){: external} for your SAP version and components. Also, review the corresponding SAP notes. See more detailed information about SAP NetWeaver installation that uses Db2 as the RDBMS in [Considerations about IBM Db2](/docs/sap?topic=sap-anydb-ibm-db2).


### Relevant SAP Notes

* [SAP Note 2002167 - Red Hat Enterprise Linux 7.x: Installation and Upgrade](https://launchpad.support.sap.com/#/notes/2002167){: external}.
* [SAP Note 2923773 - Linux on IBM Cloud (IaaS): Adaption of your SAP License](https://launchpad.support.sap.com/#/notes/2923773){: external}.
