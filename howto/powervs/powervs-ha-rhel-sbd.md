---
copyright:
  years: 2025
lastupdated: "2025-08-18"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Implementing SBD poison-pill fencing in a Red Hat Enterprise Linux High Availability Add-On cluster
{: #ha-sbd}

This guide describes how to implement SBD (Storage-Based Death) poison-pill fencing in a Red Hat Enterprise Linux High Availability Add-On cluster on {{site.data.keyword.powerSys_notm}}.

It covers the setup of iSCSI target servers, the configuration of shared storage, and the configuration of the cluster resources.

Cluster fencing is critical for clusters to ensure split-brain protection and automated failover in HA clusters.
{: note}

SBD fencing requires shared disks and a watchdog device.

- The shared disks are provisioned as block devices from iSCSI target servers in IBM VPC.
- The Power Architecture Platform Watchdog driver `pseries-wdt` is used as the hardware watchdog.
   This driver is available on IBM Power10 servers and later.
{: shortdesc}

iSCSI is an open, standards-based technology that is defined in RFC 3720.
It enables the deployment of storage area networking over an IP network.
An iSCSI software target enables local storage to be shared over a network.
iSCSI initiators located in different zones or systems can access shared storage over IP networks by using the iSCSI protocol.
This protocol encapsulates SCSI commands within TCP/IP packets, enabling data transfer across standard Ethernet networks without requiring dedicated SAN infrastructure.

![Architectural diagram](../../images/powervs-ha-fence-sbd.svg){: figure caption="Architectural diagram for accessing iSCSI targets in IBM VPC from IBM {{site.data.keyword.powerSys_notm}} instances." caption-side="bottom"}

## Before you begin
{: #ha-sbd-begin}

Review the general requirements, product documentation, support articles, and SAP notes listed in [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

## Provisioning iSCSI target servers across availability zones
{: #ha-sbd-create-tgt}

To create an IBM VPC that spans three availability zones, start with [Getting started with Virtual Private Cloud (VPC)](/docs/vpc?topic=vpc-getting-started&interface=ui).
Next, follow the instructions in [Create virtual server instances](/docs/vpc?topic=vpc-creating-virtual-servers&interface=ui) to provision three iSCSI target server instances – one in each zone.

Each iSCSI target server can be used for multiple high availability clusters, allowing for efficient resource sharing.

### Creating an IBM VPC
{: #ha-sbd-create-vpc}

1. Browse to [Virtual private cloud](/infrastructure/provision/vpc) and click **Create**.
1. Select the **Location** where you want to create your virtual private cloud.
1. Select the **Geography** and **Region**.
1. In the **Details** section, enter `iscsi-tgt-vpc` in the **Name** field.
1. Select the **Resource group**.
1. In the **Subnets** section, modify the **Name** of each subnet in the list.
   - Click the **Pencil** icon for the subnet in the first zone.
   - Change the **Name** to `iscsi-tgt-zone-1-sn`, and click **Save**.
1. Repeat this step for the other zones, and change the names to `iscsi-tgt-zone-2-sn` and `iscsi-tgt-zone-3-sn`.
1. Click **Create Virtual Private Cloud**.

### Modifying the default security group for the IBM VPC
{: #ha-sbd-modify-sg}

Add the iSCSI port to the inbound rules of the default security group.

1. Browse to [Security groups for VPC](/infrastructure/network/securityGroups) and click the security group in the list.
1. Click **Manage rules** in the **Rules** section.
1. Click **Create** under **Inbound rules** to create a new inbound rule.
1. Select **Protocol** `TCP`, and specify the iSCSI port `3260` for both **Port min** and **Port max** fields.
   Leave **Source Type** and **Destination Type** set to `Any`.
1. Click **Create**.

### Attaching the VPC to the Transit Gateway
{: #ha-sbd-tgw-add}

Use an IBM Transit Gateway to connect your Virtual Private Cloud (VPC) infrastructure and {{site.data.keyword.powerSys_notm}} workspaces.

1. Browse to [Transit Gateway](/interconnectivity/transit).
1. Select the local transit gateway in the list, and click **Add connection**.
1. Select `VPC` in the **Network connection** list and select the **Region**.
1. Click **Add**.

### Creating virtual server instances for the cluster
{: #ha-sbd-create-vsis}

In this example, three iSCSI target servers are provisioned.
Each target server provides a dedicated iSCSI LUN that is accessed as a shared disk by all cluster nodes.

To configure virtual server instances as iSCSI targets, follow the steps below.
Deploy virtual machines that are running a supported version of Red Hat Enterprise Linux operating system.

A small VM instance profile such as `cx3d-2x5` or `bx3d-2x10` is sufficient.
When you create a virtual server instance, you must select an SSH key.
See [Getting started with SSH keys](/docs/vpc?topic=vpc-ssh-keys&interface=ui) to generate an SSH key.

1. Browse to [Virtual server instances for VPC](/infrastructure/compute/vs) and click **Create**.
1. Select the **Geography**, **Region**, and **Zone**
1. In the **Details** section, enter the **Name** for the VSI.
1. Select the **Resource Group**.
1. In the **Server configuration** section, click **Change image**.
1. Select a `Red Hat Enterprise Linux` image (9.x - minimal Install), and click **Save**.
1. Select a small footprint for the **Profile**, for example `bx2-2x8`.
1. Select an **SSH key**.
1. In the **Data Volumes** section, click **Create** and provision a 10 GB volume designated for hosting the iSCSI LUNs.
   - Enter a **Name** for the data volume.
   - Enter **10** for the **Storage size (GB)**.
   - Click **Create**.
1. In the **Networking** section, select the VPC that you created earlier.
1. Click **Create virtual server**.

The deployment of the virtual server instances starts.
Repeat the process to create the second and third virtual server instances in a different availability zone, ensuring they are deployed in separate physical locations.

### Preparing the operating system
{: #ha-sbd-prepare-os}

Login as the `root` user to all iSCSI target virtual machines.

1. Update the operating system.

   ```sh
   dnf -y update
   ```
   {: pre}

   A node restart may be required to apply the changes.

   ```sh
   shutdown -r now
   ```
   {: pre}

1. Identify the device name of the disk volume that you provisioned for the backstore.

   ```sh
   lsblk | grep 10G
   ```
   {: pre}

   In this example, the device is identified as `/dev/vdb`

   Sample output:
   ```text
   vdb    252:16   0   10G  0 disk
   ```
   {: screen}

1. Create an `xfs` file system with a block size of 512 bytes.

   ```text
   # mkfs.xfs -m crc=0 -b size=512 /dev/vdb
   ```
   {: screen}

   1. Create the mount point, add the file system entry to the `/etc/fstab` file, and mount the file system.

   Sample output:
   ```text
   # mkdir /sbd
   # echo "/dev/vdb    /sbd    xfs    defaults    0    0" >> /etc/fstab
   # systemctl daemon-reload
   # mount /sbd
   ```
   {: screen}

1. Install the iSCSI target software package.

   ```sh
   dnf -y install targetcli
   ```
   {: pre}

1. Start the iSCSI target and enable it to start automatically at boot time.

   ```sh
   systemctl enable --now target
   ```
   {: pre}

   Sample output:
   ```text
   # systemctl enable --now target
   Created symlink /etc/systemd/system/multi-user.target.wants/target.service → /usr/lib/systemd/system/target.service.
   ```
   {: screen}

### Creating iSCSI devices on the iSCSI target servers
{: #ha-sbd-prepare-dev}

As the `root` user, run the following commands on each iSCSI target virtual instance to create the iSCSI disks for the SBD devices.

This example uses the following names.
- `cluster1` is the cluster name of the HA cluster.
- `cl1n1` and `cl1n2` are the hostnames of the cluster nodes for `cluster1`.

Adjust the commands in the following instructions to match your specific environment configuration.

1. Prepare the LUNs for SBD for the high availability cluster `cluster1`.

   - Create a `fileio` backstore for the SBD LUN.
     Set the `write_back` parameter to `false` to disable the file system caching.

     ```sh
     targetcli backstores/fileio create cluster1_sbd /sbd/cluster1_sbd 50M write_back=false
     ```
     {: pre}

     Sample output:
     ```text
     # targetcli backstores/fileio create cluster1_sbd /sbd/cluster1_sbd 50M write_back=false
     Created fileio cluster1_sbd with size 52428800
     ```
     {: screen}

   - Create the iSCSI target definition.

     ```sh
     targetcli iscsi/ create iqn.2006-04.cluster1.local:cluster1
     ```
     {: pre}

     Sample output:
     ```text
     # targetcli iscsi/ create iqn.2006-04.cluster1.local:cluster1
     Created target iqn.2006-04.cluster1.local:cluster1.
     Created TPG 1.
     Global pref auto_add_default_portal=true
     Created default portal listening on all IPs (0.0.0.0), port 3260.
     ```
     {: screen}

   - Create the iSCSI LUN.

     ```sh
     targetcli iscsi/iqn.2006-04.cluster1.local:cluster1/tpg1/luns/ create /backstores/fileio/cluster1_sbd
     ```
     {: pre}

     Sample output:
     ```text
     # targetcli iscsi/iqn.2006-04.cluster1.local:cluster1/tpg1/luns/ create /backstores/fileio/cluster1_sbd
     Created LUN 0.
     ```
    {: screen}

   - Create the iSCSI ACLs.

     ```sh
     targetcli iscsi/iqn.2006-04.cluster1.local:cluster1/tpg1/acls/ create iqn.2006-04.cl1n1.local:cl1n1
     ```
     {: pre}

     Sample output:
     ```text
     # targetcli iscsi/iqn.2006-04.cluster1.local:cluster1/tpg1/acls/ create iqn.2006-04.cl1n1.local:cl1n1
     Created Node ACL for iqn.2006-04.cl1n1.local:cl1n1
     Created mapped LUN 0.
     ```
     {: screen}

     ```sh
     targetcli iscsi/iqn.2006-04.cluster1.local:cluster1/tpg1/acls/ create iqn.2006-04.cl1n2.local:cl1n2
     ```
     {: pre}

     Sample output:
     ```text
     # targetcli iscsi/iqn.2006-04.cluster1.local:cluster1/tpg1/acls/ create iqn.2006-04.cl1n2.local:cl1n2
     Created Node ACL for iqn.2006-04.cl1n2.local:cl1n2
     Created mapped LUN 0.
     ```
     {: screen}

     - Save the `targetcli` configuration.

     ```sh
     targetcli saveconfig
     ```
     {: pre}

     Sample output:
     ```text
     # targetcli saveconfig
     Configuration saved to /etc/target/saveconfig.json
     ```
     {: screen}

1. Check the configuration.

   ```sh
   targetcli ls
   ```
   {: pre}

   Sample output:
   ```text
   # targetcli ls
   o- / ......................................................................................................................... [...]
     o- backstores .............................................................................................................. [...]
     | o- block .................................................................................................. [Storage Objects: 0]
     | o- fileio ................................................................................................. [Storage Objects: 1]
     | | o- cluster1_sbd ........................................................... [/sbd/cluster1_sbd (50.0MiB) write-thru activated]
     | |   o- alua ................................................................................................... [ALUA Groups: 1]
     | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
     | o- pscsi .................................................................................................. [Storage Objects: 0]
     | o- ramdisk ................................................................................................ [Storage Objects: 0]
     o- iscsi ............................................................................................................ [Targets: 1]
     | o- iqn.2006-04.cluster1.local:cluster1 ............................................................................... [TPGs: 1]
     |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
     |     o- acls .......................................................................................................... [ACLs: 2]
     |     | o- iqn.2006-04.cl1n1.local:cl1n1 ........................................................................ [Mapped LUNs: 1]
     |     | | o- mapped_lun0 ......................................................................... [lun0 fileio/cluster1_sbd (rw)]
     |     | o- iqn.2006-04.cl1n2.local:cl1n2 ........................................................................ [Mapped LUNs: 1]
     |     |   o- mapped_lun0 ......................................................................... [lun0 fileio/cluster1_sbd (rw)]
     |     o- luns .......................................................................................................... [LUNs: 1]
     |     | o- lun0 ..................................................... [fileio/cluster1_sbd (/sbd/cluster1_sbd) (default_tg_pt_gp)]
     |     o- portals .................................................................................................... [Portals: 1]
     |       o- 0.0.0.0:3260 ..................................................................................................... [OK]
     o- loopback ......................................................................................................... [Targets: 0]
   ```
   {: screen}

## Creating the SBD device on the cluster nodes
{: #ha-sbd-create-sbd}

On the cluster nodes, discover and connect the iSCSI devices that you created earlier.
Run the following commands on both cluster nodes.

### Accessing the iSCSI devices on the cluster nodes
{: #ha-sbd-prepare-sbd}

#### Setting up the iSCSI initiator
{: #ha-sbd-setup-initiator}

1. On both cluster nodes, run the following commands to install or update the iSCSI initiator utilities.

   ```sh
   dnf install -y iscsi-initiator-utils
   ```
   {: pre}

1. On both cluster nodes, run the following command to install the required HA Add-On packages.

   ```sh
   dnf install -y pcs pacemaker sbd fence-agents-sbd
   ```
   {: pre}

   To install the `fence-agents-sbd` package, make sure you use version `4.10.0` with release `62.el9_4.15` or a more recent one.
   {: attention}

1. Each iSCSI initiator must have a unique IQN (iSCSI Qualified Name), which is used to control access to the iSCSI targets.
   A randomly generated IQN is assigned during installation, but it can be replaced with a more descriptive name.
   The IQN must match the name in the access control lists (ACLs) that you created for the iSCSI device on the iSCSI target server.

   - On NODE1, run the following command to change the IQN.

      ```sh
      echo "InitiatorName=iqn.2006-04.cl1n1.local:cl1n1" > /etc/iscsi/initiatorname.iscsi
      ```
      {: pre}

   - On NODE2, run the following command to change the IQN.

      ```sh
      echo "InitiatorName=iqn.2006-04.cl1n2.local:cl1n2" > /etc/iscsi/initiatorname.iscsi
      ```
      {: pre}

1. On both cluster nodes, run the following command to enable the iSCSI services.

   ```sh
   systemctl enable iscsid iscsi
   ```
   {: pre}

1. On both cluster nodes, run the following commands to restart the iSCSI services.

   ```sh
   systemctl restart iscsid
   ```
   {: pre}

   ```sh
   systemctl restart iscsi
   ```
   {: pre}

#### Discovering the iSCSI LUNs
{: #ha-sbd-setup-luns}

1. On both cluster nodes, run the following commands to connect the iSCSI devices.

   In the following example, the iSCSI target servers are accessed via the IP addresses of the iSCSI target virtual servers and iSCSI port `3260`.

   The IP addresses provided in the example are for illustrative purposes only.
   Be sure to replace them with values that match your specific VPC subnet configuration.
   {: attention}

   | VSI name       | IP address:Port     |
   | -------------- | ------------------: |
   | `iscsi-vm-001` | `10.20.30.4:3260`   |
   | `iscsi-vm-002` | `10.20.30.69:3260`  |
   | `iscsi-vm-003` | `10.20.30.132:3260` |
   {: caption="IP addresses of the iSCSI targets used in this example configuration." caption-side="bottom"}

   To simplify the setup, create environment variables for the communication details of the iSCSI target servers.

   ```sh
   export ISCSI_TGT_1="10.20.30.4:3260"                         # IP address and port of the first virtual server (first iSCSI target)
   export ISCSI_TGT_2="10.20.30.69:3260"                        # IP address and port of the second virtual server (second iSCSI target)
   export ISCSI_TGT_3="10.20.30.132:3260"                       # IP address and port of the third virtual server (third iSCSI target)
   export ISCSI_TGT_NAME="iqn.2006-04.cluster1.local:cluster1"  # iSCSI target name
   ```
   {: codeblock}

   Run the `iscsiadm` discovery command and verify that the target name is correct.
   Repeat the procedure for the second and the third iSCSI target servers.

   ```sh
   iscsiadm -m discovery --type=st --portal=${ISCSI_TGT_1}
   ```
   {: pre}

   Sample output:
   ```text
   # iscsiadm -m discovery --type=st --portal=${ISCSI_TGT_1}
   10.20.30.4:3260,1 iqn.2006-04.cluster1.local:cluster1
   # iscsiadm -m discovery --type=st --portal=${ISCSI_TGT_2}
   10.20.30.69:3260,1 iqn.2006-04.cluster1.local:cluster1
   # iscsiadm -m discovery --type=st --portal=${ISCSI_TGT_3}
   10.20.30.132:3260,1 iqn.2006-04.cluster1.local:cluster1
   ```
   {: screen}

   From the output of the `iscsiadm -m discovery` command, note the iSCSI target name and assign it to the ISCSI_TGT_NAME environment variable.
   {: tip}

1. Log in to the iSCSI target device.

   ```sh
   iscsiadm -m node -T ${ISCSI_TGT_NAME} --login --portal=${ISCSI_TGT_1}
   ```
   {: pre}

   Sample output:
   ```text
   # iscsiadm -m node -T ${ISCSI_TGT_NAME} --login --portal=${ISCSI_TGT_1}
   Logging in to [iface: default, target: iqn.2006-04.cluster1.local:cluster1, portal: 10.20.30.4,3260]
   Login to [iface: default, target: iqn.2006-04.cluster1.local:cluster1, portal: 10.20.30.4,3260] successful.
   ```
   {: screen}

   Repeat the step by using `${ISCSI_TGT_2}` and `${ISCSI_TGT_3}` as values for the `--portal` argument.

1. Enable automatic login to the iSCSI device.

   ```sh
   iscsiadm -m node -p ${ISCSI_TGT_1} -T ${ISCSI_TGT_NAME} --op=update --name=node.startup --value=automatic
   ```
   {: pre}

   Repeat the step by using `${ISCSI_TGT_2}` and `${ISCSI_TGT_3}` as values for the `-p` argument.

1. On both cluster nodes, run the following commands.

   Verify that the iSCSI devices are available, and record their device names for later reference.

   ```sh
   lsscsi -i | grep "LIO-ORG"
   ```
   {: pre}

   Sample output:
   ```text
   [5:0:0:0]    disk    LIO-ORG  cluster1_sbd     4.0   /dev/sdck  3600140500874d3cfa724e728b77046a3
   [6:0:0:0]    disk    LIO-ORG  cluster1_sbd     4.0   /dev/sdcl  3600140517566c8197884fb9b643b470a
   [7:0:0:0]    disk    LIO-ORG  cluster1_sbd     4.0   /dev/sdcm  36001405c2ed12b15b0a4c7f8d311ab49
   ```
   {: screen}

Retrieve the iSCSI device IDs by using the output of the lsscsi command.

The provided awk example extracts the device IDs, completes the device names, and prepares environment variable definitions for further use.

```sh
lsscsi -i | awk '$3 == "LIO-ORG" { count++; print "export SBD_DEV_" count "=/dev/disk/by-id/scsi-" $7}'
```
{: pre}

Sample output:
```text
lsscsi -i | awk '$3 == "LIO-ORG" { count++; print "export SBD_DEV_" count "=/dev/disk/by-id/scsi-" $7}'
export SBD_DEV_1=/dev/disk/by-id/scsi-3600140500874d3cfa724e728b77046a3
export SBD_DEV_2=/dev/disk/by-id/scsi-3600140517566c8197884fb9b643b470a
export SBD_DEV_3=/dev/disk/by-id/scsi-36001405c2ed12b15b0a4c7f8d311ab49
```
{: screen}

Review the devices entries in the output, then use the `eval` command to export the environment variables.

```sh
eval $(lsscsi -i | awk '$3 == "LIO-ORG" { count++; print "export SBD_DEV_" count "=/dev/disk/by-id/scsi-" $7}')
```
{: pre}

Verify the exported variables.

```sh
echo $SBD_DEV_1 $SBD_DEV_2 $SBD_DEV_3
```
{: pre}

Sample output:
```text
echo $SBD_DEV_1 $SBD_DEV_2 $SBD_DEV_3
/dev/disk/by-id/scsi-3600140500874d3cfa724e728b77046a3 /dev/disk/by-id/scsi-3600140517566c8197884fb9b643b470a /dev/disk/by-id/scsi-36001405c2ed12b15b0a4c7f8d311ab49
```
{: screen}

These environment variables are required for creating the SBD device.

## Configuring SBD poison-pill fencing in the cluster
{: #ha-sbd-configure-sbd}

SBD poison pill fencing over a block device requires both a watchdog timer device and shared storage to transmit poison pill messages.
To enable fencing, configure the cluster, activate the SBD devices on the shared storage, and set up cluster fencing.

### Configuring the watchdog on the cluster nodes
{: #ha-sbd-check-wdog}

The `pseries_wdt` Power hypervisor watchdog is the preferred watchdog and is available on IBM Power10 servers and later.

If you deploy virtual server instances with a Power9 profile, the Power hypervisor watchdog is not available.
As an alternative, the software watchdog `softdog` can be used, although it has limited support.
{: important}

To configure a watchdog, follow the steps outlined in one of the following sections, depending on your environment.

#### Configuring the`pseries_wdt` hardware watchdog (IBM Power10 and later)
{: #ha-sbd-check-hw-wdt}

On both cluster nodes, run the following commands to verify that the hypervisor watchdog driver is loaded.

```sh
lsmod | grep pseries_wdt
```
{: pre}

```text
# lsmod | grep pseries_wdt
pseries_wdt           262144  0
```
{: screen}

You can also use the following command to verify that the watchdog driver is loaded.

```sh
wdctl
```
{: pre}

Sample output:
```text
wdctl
Device:        /dev/watchdog0
Identity:      pseries-wdt [version 0]
Timeout:       15 seconds
Pre-timeout:    0 seconds
```
{: screen}

#### Configuring the `softdog` software watchdog (IBM Power9)
{: #ha-sbd-check-softdog}

Using the `softdog` software watchdog on an IBM Power9 server is an alternative, but has support limitations.
{: important}

Run the following commands on both cluster nodes to implement the software watchdog.

1. Load the `softdog` module.

   ```sh
   modprobe softdog
   ```
   {: pre}

1. Make sure that `softdog` is automatically loaded after a node restart.

    ```sh
    echo softdog > /etc/modules-load.d/watchdog.conf
    ```
    {: pre}

1. Restart the `systemd` modules.

   ```sh
   systemctl restart systemd-modules-load
   ```
   {: pre}

### Enabling the sbd devices
{: #ha-sbd-enable}

1. Use the basic cluster configuration steps, such as `pcs host auth` and `pcs cluster setup`.
   Then, start the cluster on both nodes.

1. On any cluster node, run the following commands to configure and enable the SBD devices.
   Use the iSCSI device IDs you recorded earlier to create the new SBD devices.

   ```sh
   pcs stonith sbd device setup \
       device=${SBD_DEV_1} \
       device=${SBD_DEV_2} \
       device=${SBD_DEV_3} \
       watchdog-timeout=30 \
       msgwait-timeout=60
   ```
   {: pre}

   Enter `yes` to confirm the device initialization.

   Sample output:
   ```text
   # pcs stonith sbd device setup \
        device=${SBD_DEV_1} \
        device=${SBD_DEV_2} \
        device=${SBD_DEV_3} \
        watchdog-timeout=30 \
        msgwait-timeout=60

      WARNING: All current content on device(s) '/dev/disk/by-id/scsi-3600140500874d3cfa724e728b77046a3', '/dev/disk/by-id/scsi-3600140517566c8197884fb9b643b470a', '/dev/disk/by-id/scsi-36001405c2ed12b15b0a4c7f8d311ab49' will be overwritten
      Type 'yes' or 'y' to proceed, anything else to cancel: yes
      Initializing devices '/dev/disk/by-id/scsi-3600140500874d3cfa724e728b77046a3', '/dev/disk/by-id/scsi-3600140517566c8197884fb9b643b470a', '/dev/disk/by-id/scsi-36001405c2ed12b15b0a4c7f8d311ab49'...
      Devices initialized successfully
   ```
   {: screen}

   Check the status.

   ```sh
   pcs stonith sbd status
   ```
   {: pre}

   Sample output:
   ```text
   # pcs stonith sbd status
   SBD STATUS
   <node name>: <installed> | <enabled> | <running>
   cl1n1: YES |  NO |  NO
   cl1n2: YES |  NO |  NO
   ```
   {: screen}

   Enable the SBD devices.

   ```sh
   pcs stonith sbd enable \
       device=${SBD_DEV_1} \
       device=${SBD_DEV_2} \
       device=${SBD_DEV_3} \
       SBD_WATCHDOG_TIMEOUT=60 \
       SBD_STARTMODE=always \
       SBD_DELAY_START=yes
   ```
   {: pre}

   Sample output:
   ```text
   # pcs stonith sbd enable \
       device=${SBD_DEV_1} \
       device=${SBD_DEV_2} \
       device=${SBD_DEV_3} \
       SBD_WATCHDOG_TIMEOUT=60 \
       SBD_STARTMODE=clean \
       SBD_DELAY_START=yes
   Running SBD pre-enabling checks...
   cl1n2: SBD pre-enabling checks done
   cl1n1: SBD pre-enabling checks done
   Distributing SBD config...
   cl1n1: SBD config saved
   cl1n2: SBD config saved
   Enabling sbd...
   cl1n2: sbd enabled
   cl1n1: sbd enabled
   Warning: Cluster restart is required in order to apply these changes.
   ```
   {: screen}

   Restart the cluster to activate the changes.
   {: Attention}

   - Stop the cluster.

      On any cluster node, run the following command.

      ```sh
      pcs cluster stop --all
      ```
      {: pre}

   - Start the cluster.

      On each cluster node, run the following command.

      ```sh
      pcs cluster start
      ```
      {: pre}

      Running `pcs cluster start --all` on a single cluster node might result in a timeout.
      {: note}

   - Verify the status of the `sbd` devices.

      ```sh
      pcs stonith sbd status
      ```
      {: pre}

      Sample output:
      ```text
      # pcs stonith sbd status
      SBD STATUS
      <node name>: <installed> | <enabled> | <running>
      cl1n2: YES | YES |  NO
      cl1n1: YES | YES | YES
      Messages list on device '/dev/disk/by-id/scsi-360014059ccb372b4eca43d1b5174f5bc':
      0       cl1n1        clear
      1       cl1n2        clear
      Messages list on device '/dev/disk/by-id/scsi-36001405218e6f5380264cb5bf3e98700':
      0       cl1n2        clear
      1       cl1n1        clear
      Messages list on device '/dev/disk/by-id/scsi-3600140563753b6cac47491facdaa856d':
      0       cl1n2        clear
      1       cl1n1        clear
      ```
      {: screen}

### Configuring the cluster fencing device
{: #ha-sbd-cfg-fence}

On any cluster node, run the `pcs stonith create` command to create the STONITH device.
Set the `pcmk_reboot_timeout` parameter to increase the timeout value for the reboot action.

```sh
pcs stonith create res_fence_sbd fence_sbd \
   devices="${SBD_DEV_1},${SBD_DEV_2},${SBD_DEV_3}" \
   pcmk_reboot_timeout=90
```
{: pre}

Test the cluster fencing.
Then, proceed with all other cluster configuration steps.
