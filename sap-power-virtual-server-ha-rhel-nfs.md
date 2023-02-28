---
copyright:
  years: 2023
lastupdated: "2023-02-28"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, NFS Server, Linux

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Configuring an active or passive NFS server in a Red Hat High Availability cluster
{: #ha-rhel-nfs}

Red Hat documentation [Configuring an active-passive NFS server in a Red Hat High Availability cluster](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_and_managing_high_availability_clusters/assembly_configuring-active-passive-nfs-server-in-a-cluster-configuring-and-managing-high-availability-clusters#doc-wrapper){: external} describes the configuration of an active-passive NFS server on a two-node Red Hat Enterprise Linux High Availability Add-On cluster by using shared storage.
Check [How to configure HA-LVM Cluster by using system_id in RHEL 8](https://access.redhat.com/solutions/3796221){: external} about the steps to configure active-passive shared storage in RHEL 8.

## Overview
{: #ha-rhel-nfs-overview}

The following instructions describe the configuration of an active-passive NFS server in a RHEL HA Add-On cluster with Red Hat Enterprise Linux 8 by using virtual server instances in [{{site.data.keyword.powerSys_notm}}](https://www.ibm.com/products/power-virtual-server){: external} as cluster nodes.

## Prerequisites
{: #ha-rhel-nfs-prerequisites}

- A [Red Hat Customer Portal](https://access.redhat.com/){: external} account.
- An [IBM Cloud](/docs/account?topic=account-account-getting-started){: external}.
- The virtual server instances need to fulfill hardware and resource requirements for the NFS server in scope.
- This document demonstrates a setup by using shareable storage volumes accessible on both cluster nodes.
   All file systems that are used for NFS exports are created on shareable storage volumes.

   Storage volumes that are intended for the volume group of the exported file systems are attached to both virtual server instances.
   HA-LVM makes sure that the volume group is active on one node at a time.

   In the example setup, one shared volume group `nfs_vg` contains three logical volumes `lv1`, `lv2`, and `lv3`.
   XFS file systems are created on `lv1`, `lv2`, and `lv3` and are mounted on `/nfsexp`, `/nfsexp/export/sapmnt1`, `/nfsexp/export/trans1`.

- A virtual hostname and IP address is required for the NFS server.
   Make sure that the virtual IP addresses that are used by the instances are defined on the network adapter, and reachable in the network.
- Name resolution and reverse lookup for physical and virtual IP names and addresses are unique and consistent on all NFS server and client nodes.
- Details of the NFS clients (subnet, required NFS export options) are available.
   They need to be specified during the cluster setup.

## Preparing for a highly available NFS server
{: #ha-rhel-nfs-prepare-nodes}

Use the following information to prepare for a highly available NFS server.

### Installing NFS software packages
{: #ha-rhel-nfs-prepare-packages}

On both nodes, run the following commands.

```sh
dnf install nfs-utils -y
```
{: pre}

Stop and disable the local `nfs-lock` service on both nodes.
This service is controlled by the RHEL HA Add-On.

```sh
systemctl disable --now nfs-lock
```
{: pre}

### Preparing environment variables
{: #ha-rhel-nfs-prepare-environment-variables}

To simplify the set up, prepare the following environment variables for user ID `root` on both nodes.
These environment variables are used in subsequent commands in the remainder of this document.

On both nodes, create a file with the following environment variables.
Then, you need to adapt them to your configuration.

```sh
# virtual hostnames
export NFS_vh=<virtual hostname>     # virtual hostname for NFS Server
export NFS_ip=<IP address>           # virtual IP address for NFS Server

# LVM storage for NFS file systems
export NFS_pvid=<>
export NFS_vg=<vg name>              # volume group name for NFS exported directory

export NFS_lv1="lv1"                 # logical volume name export #1
export NFS_sz1="5%VG"                # sample size
export NFS_fs1="/nfsexp"             # shared file system
export NFS_root="${NFS_fs1}/export"  # base export directory
export NFS_lv2="lv2"                 # logical volume name export #2
export NFS_sz2="40%VG"               # sample size
export NFS_fs2="${NFS_root}/${SID}"  # shared file system
export NFS_lv3="lv3"                 # logical volume name export #3
export NFS_sz2="40%VG"               # sample size
export NFS_fs3="${NFS_root}/trans"   # shared file system
export NFS_spec=<clientspec>         # subnet and netmask for allowed NFS clients
export NFS_opts=<export options>     # options for NFS export
```
{: codeblock}

You must source this file before you use the sample commands in the remainder of this document.

For example, if you created a file that is named `nfs_envs.sh`, run the following command on both nodes to set the environment variables.

```shell
source nfs_envs.sh
```
{: pre}

Every time that you start a new terminal session, you must run the `source` command.
Alternatively, you can add the environment variables file to the `/etc/profile.d` directory during the cluster configuration.
In this example, the file is sourced automatically each time you log in to the server.
{: note}

### Preparing LVM objects
{: #ha-rhel-nfs-prepare-lvm-objects}

Shared storage is an important resource in a RHEL HA Add-On NFS cluster.
All cluster nodes need access to the shared storage volumes, but only one node has exclusive read and write access to a volume.

#### Preparing active-passive HA LVM
{: #ha-rhel-nfs-prepare-lvm}

On both nodes, edit file `/etc/lvm/lvm.conf` to include the system ID in the volume group.

```sh
vi /etc/lvm/lvm.conf
```
{: pre}

Search for parameter `system_id_source` and change its value to "uname".

Sample setting of the `system_id_source` parameter in `/etc/lvm/lvm.conf`:

```sh
system_id_source = "uname"
```
{: screen}

Verify that the `LVM system ID` for the node matches the `uname`.

```sh
lvm systemid
```
{: pre}

```sh
uname -n
```
{: pre}

Sample output:

```sh
# lvm systemid
  system ID: cl-nfs-1
# uname -n
  cl-nfs-1
```
{: screen}

#### Identifying the World Wide Names of shared storage volumes
{: #ha-rhel-nfs-identify-wwn}

Identify the World Wide Names (WWN) for all volumes that are used in the shared volume group.

1. Log in to IBM Cloud to the [Storage volumes](/power/storage){: external} view of {{site.data.keyword.powerSys_notm}}.
1. Select your **workspace**.
1. Filter for the *volume prefix* in the *Storage volumes* list, and identify all the **World Wide Names** of the volumes in scope (the *World Wide Name* is a 32-digit hexadecimal number).
    Make sure that the attribute **Shareable** is *On* for those volumes.

In the [Virtual server instances](/power/servers){: external} view, go to both virtual server instances of the cluster.
Verify that all volumes in scope are attached to both virtual server instances.

When you attach a new storage volume to a virtual server instance, make sure that you *rescan the SCSI bus* to detect the new volume.
Then, update the *multipath configuration* of the virtual server instance.

On the nodes with new storage volume attachments, run the following command.

```sh
rescan-scsi-bus.sh && sleep 10 && multipathd reconfigure
```
{: pre}

Log in to both cluster nodes, and add the **WWN** to the environment variables of user ID `root`.

The matching **WWN** values can also be found with the `pvs --all`command.
{: tip}

On both nodes, run the following command.

```sh
export NFS_pvid=3<World Wide Name>   # WWN of shared storage volume for NFS volume
```
{: screen}

Make sure that you set the environment variable by using the hexadecimal number in lowercase.
{: tip}

#### Creating physical volumes
{: #ha-rhel-nfs-create-nfs-pv}

On NODE1, run the following commands.

```sh
pvcreate /dev/mapper/${NFS_pvid}
```
{: pre}

```sh
pvcreate /dev/mapper/${NFS_pvid}
  Physical volume "/dev/mapper/360050768108103357000000000002ddc" successfully created.
```
{: screen}

#### Creating a volume group
{: #ha-rhel-nfs-create-nfs-vg}

On NODE1, create the volume group for the NFS export.

```sh
vgcreate ${NFS_vg} /dev/mapper/${NFS_pvid}
```
{: pre}

Verify that the *System ID* is set.

```sh
vgs -o+systemid
```
{: pre}

Sample output:

```sh
# vgs -o+systemid
  VG          #PV #LV #SN Attr   VSize   VFree   System ID
  nfsvg     1   0   0 wz--n- <50.00g <50.00g eh-cl-sap-1
```
{: screen}

#### Creating logical volumes
{: #ha-rhel-nfs-create-nfs-lv}

On NODE1, create three logical volumes for the NFS export.

```sh
lvcreate -l ${NFS_sz1} -n ${NFS_lv1} ${NFS_vg}
```
{: pre}

```sh
lvcreate -l ${NFS_sz2} -n ${NFS_lv2} ${NFS_vg}
```
{: pre}

```sh
lvcreate -l ${NFS_sz3} -n ${NFS_lv3} ${NFS_vg}
```
{: pre}

#### Creating the file systems
{: #ha-rhel-nfs-create-nfs-fs}

On NODE1, create the file systems for NFS exports.

The example uses file system type `xfs`.
Other file system types are possible. Then, the resource definitions need to be slightly changed.

```sh
mkfs.xfs /dev/${NFS_vg}/${NFS_lv1}
```
{: pre}

```sh
mkfs.xfs /dev/${NFS_vg}/${NFS_lv2}
```
{: pre}

```sh
mkfs.xfs /dev/${NFS_vg}/${NFS_lv3}
```
{: pre}

#### Creating mount point for the NFS export
{: #ha-rhel-nfs-create-nfs-mtpt}

On both nodes, run the following command.

```sh
mkdir -p ${NFS_fs1}
```
{: pre}

## Installing and setting up the RHEL HA Add-On cluster
{: #ha-rhel-nfs-install-setup}

Install and set up the RHEL HA Add-On cluster according to [Implement RHEL HA Add-On cluster on {{site.data.keyword.powerSys_notm}}](#ha-rhel){: external}.
Configure and test fencing as described in [Create the fencing device](#ha-rhel-create-fencing-device){: external}.

## Configuring NFS server resources in the cluster
{: #ha-rhel-nfs-cfg-resources}

Up to this point, the following items are assumed:

- A RHEL HA Add-On cluster is running on both virtual server instances.
- Fencing of the nodes is tested and works properly.

### Configuring NFS resource group and resources
{: #ha-rhel-nfs-cfg-nfs-rg}

Use the following steps to configure the NFS resources in the cluster.

#### Creating the LVM-activate resource
{: #ha-rhel-nfs-cfg-lvm-rg}

To make sure that all NFS resources run on the same node, configure them as part of the resource group `nfsgroup`.

This resource group is created together with the first resource.
Resources start in the order in which they are added to the group.
They stop in the reverse order.

On NODE1, run the following command.

```sh
pcs resource create nfs_vg ocf:heartbeat:LVM-activate \
    vgname=${NFS_vg} \
    vg_access_mode=system_id \
    --group nfsgroup
```
{: pre}

Do not configure more than one *LVM-activate* resource that uses the same LVM volume group in an active-passive HA configuration.
Otherwise, you risk data corruption.
Don't configure an LVM-activate resource as a clone resource in an active-passive HA configuration.

Check the status of the cluster and verify that resource `nfs_vg` is active.

On NODE1, run the following command.

```sh
pcs resource status
```
{: pre}

Sample output:
```sh
# pcs resource status
  * Resource Group: nfsgroup:
    * nfs_vg    (ocf::heartbeat:LVM-activate):   Started cl-nfs-1
```
{: screen}

The following command configures the `xfs` file system resources for the `nfsgroup` resource group.
The file systems use LVM volume group `${NFS_vg}` and the logical volumes (`${NFS_lv1}`, `${NFS_lv2}`, `${NFS_lv3}`) that were created before.

On NODE1, run the following command.

```sh
pcs resource create nfs_fs1 Filesystem \
    device=/dev/${NFS_vg}/${NFS_lv1} \
    directory=${NFS_fs1} \
    fstype=xfs \
    --group nfsgroup
```
{: pre}

You can specify mount options as part of the resource configuration for a file system resource by using the `options=<options>` parameter.
Run `pcs resource describe filesystem` for a complete list of configuration options.

Check the status of the cluster and verify that the resource `nfs_fs1` is active.

```sh
pcs resource status
```
{: pre}

Sample output:
```sh
# pcs resource status
  * Resource Group: nfsgroup:
    * nfs_vg    (ocf::heartbeat:LVM-activate):   Started cl-nfs-1
    * nfs_fs1   (ocf::heartbeat:Filesystem):     Started cl-nfs-1
```
{: screen}

On the node with the active resource group `nfsgroup`, create two subdirectories in `${NFS_fs1}`.
`${NFS_fs1}/stat` is used as `nfs_shared_infodir` for NFS lock info and `${NFS_fs1}/export` is used as NFS root.

```sh
mkdir ${NFS_fs1}/stat ${NFS_fs1}/export
```
{: pre}

Then, create the mount points for the exported file systems.

On both nodes, run the following command.

```sh
mkdir ${NFS_fs2} ${NFS_fs3}
```
{: pre}

Create the resources for the other two NFS file systems.

On NODE1, run the following commands.

```sh
pcs resource create nfs_fs2 Filesystem \
    device=/dev/${NFS_vg}/${NFS_lv2} \
    directory=${NFS_fs2} \
    fstype=xfs \
    --group nfsgroup
```
{: pre}

```sh
pcs resource create nfs_fs3 Filesystem \
    device=/dev/${NFS_vg}/${NFS_lv3} \
    directory=${NFS_fs3} \
    fstype=xfs \
    --group nfsgroup
```
{: pre}

Check the status of the cluster and verify that all three file system resources (`nfs_fs1`, `nfs_fs2`, `nfs_fs3`) are active.

```sh
pcs resource status
```
{: pre}

Sample output:
```sh
# pcs resource status
  * Resource Group: nfsgroup:
    * nfs_vg    (ocf::heartbeat:LVM-activate):   Started cl-nfs-1
    * nfs_fs1   (ocf::heartbeat:Filesystem):     Started cl-nfs-1
    * nfs_fs2   (ocf::heartbeat:Filesystem):     Started cl-nfs-1
    * nfs_fs3   (ocf::heartbeat:Filesystem):     Started cl-nfs-1
```
{: screen}

#### Creating the nfsserver resource
{: #ha-rhel-nfs-cfg-nfssrv-rg}

On NODE1, create a resource for managing the NFS server.

```sh
pcs resource create nfs_daemon nfsserver \
    nfs_shared_infodir=${NFS_base}/stat \
    nfs_no_notify=true \
    --group nfsgroup
```
{: pre}

The `nfs_shared_infodir` parameter of the `nfsserver` resource specifies a directory where the NFS server stores stateful information.

Check the status of the cluster and verify that the NFS server is started.

```sh
pcs resource status
```
{: pre}

Sample output:

```sh
# pcs resource status
  * Resource Group: nfsgroup:
    * nfs_vg    (ocf::heartbeat:LVM-activate):   Started cl-nfs-1
    * nfs_fs1   (ocf::heartbeat:Filesystem):     Started cl-nfs-1
    * nfs_fs2   (ocf::heartbeat:Filesystem):     Started cl-nfs-1
    * nfs_fs3   (ocf::heartbeat:Filesystem):     Started cl-nfs-1
    * nfs_daemon        (ocf::heartbeat:nfsserver):      Started cl-nfs-1
```
{: screen}

#### Creating the exportfs resource
{: #ha-rhel-nfs-cfg-exportfs-rg}

To export the `${NFS_root}` directory, add the exportfs resources to the `nfsgroup` group which builds a virtual directory for NFSv4 clients.
NFSv3 clients can access these exports too.

The following is an example for the `{NFS_spec}` and `{NFS_options}` variables.

```sh
NFS_spec="10.111.1.0/24"
NFS_options="rw,sync,no_root_squash,no_subtree_check,crossmnt"
```
{: codeblock}

On NODE1, run the following command.

```sh
pcs resource create nfs_export exportfs \
    clientspec={NFS_spec} \
    options=${NFS_opts} \
    directory=${NFS_root} \
    fsid=0 \
    --group nfsgrp
```
{: pre}

#### Configuring a floating IP address resource
{: #ha-rhel-nfs-cfg-vip-rg}

Review the information in [Reserving virtual IP addresses](#ha-rhel-reserve-virtual-ip-addresses) and reserve the virtual IP address for the NFS cluster.

Create a resource for the virtual IP address of the NFS Server.
NFS clients access the NFS share by using the floating IP address.

On NODE1, run the following command.

```sh
pcs resource create nfs_ip IPaddr2 \
    ip=${NFS_ip} \
    --group nfsgroup
```
{: pre}

#### Configuring a notify resource
{: #ha-rhel-nfs-cfg-notify-rg}

An *nfsnotify* resource sends FSv3 reboot notifications after the entire NFS deployment initializes.

On NODE1, run the following command.

```sh
pcs resource create nfs_notify nfsnotify \
    source_host=${NFS_ip} \
    --group nfsgroup
```
{: pre}

The NFS cluster setup is now complete.

On NODE1, run the following command to check the status.

```sh
pcs resource status
```
{: pre}

Sample output:

```sh
# pcs resource status
  * Resource Group: nfsgroup:
    * nfs_vg    (ocf::heartbeat:LVM-activate):   Started cl-nfs-1
    * nfs_fs1   (ocf::heartbeat:Filesystem):     Started cl-nfs-1
    * nfs_fs2   (ocf::heartbeat:Filesystem):     Started cl-nfs-1
    * nfs_fs3   (ocf::heartbeat:Filesystem):     Started cl-nfs-1
    * nfs_daemon        (ocf::heartbeat:nfsserver):      Started cl-nfs-1
    * nfs-root  (ocf::heartbeat:exportfs):       Started cl-nfs-1
    * nfs_ip    (ocf::heartbeat:IPaddr2):        Started cl-nfs-1
    * nfs_notify        (ocf::heartbeat:nfsnotify):      Started cl-nfs-1
```
{: screen}

## Testing the NFS server cluster
{: #ha-rhel-nfs-test-cluster}

You can validate the NFS resource configuration in a high availability cluster by using the following procedures.
You can mount the exported file system with either NFSv3 or NFSv4.
Run the following tests to verify the NFS cluster functionality.

### Test1 - Testing the NFS export
{: #ha-rhel-nfs-test-export}

Run all the commands the teting commands on a *NFS client node* outside the HA NFS cluster.

Verify the NFS exports.

```sh
showmount -e ${NFS_ip}
```
{: pre}

The `showmount` command displays information about file systems that are exported by a NFS Server (NFS v3).
Verify that the output lists all the exported directories.

Create a temporary directory on the *NFS client node*.
Then, mount the NFS file system and create the directory structure that is required for the SAP installation.

In the first example, only `/usr/sap/trans` and `/sapmnt/${SID}` are NFS mounted on the SAP application server instances.

Use a temporary mount point to create the required subdirectories, change the ownership, and change the permissions.

```shell
mkdir -p /tmp/saptmp
```
{: pre}

```shell
mount -t nfs ${NFS_vh}:/ /tmp/saptmp -o vers=4,minorversion=1,sec=sys
```
{: pre}

```shell
mkdir -p /tmp/saptmp/${SID}
```
{: pre}

```shell
chown -R ${sid}adm:sapsys /tmp/saptmp/${SID}
```
{: pre}

```shell
chown ${sid}adm:sapsys /tmp/saptmp/trans
```
{: pre}

```shell
chmod g+w /tmp/saptmp/trans
```
{: pre}

```shell
umount /tmp/saptmp​
```
{: pre}

Prepare the final mount points that are used for the SAP installation.

```sh
mkdir -p /sapmnt/${SID}
```
{: pre}

```sh
mkdir -p /usr/sap/trans
```
{: pre}

Change the attributes.

```sh
chattr +i /usr/sap/trans
```
{: pre}

```sh
chattr +i /sapmnt/${SID}
```
{: pre}

Add the new file systems to `/etc/fstab`.

```sh
​${NFS_vh}:/trans /usr/sap/trans  nfs vers=4,minorversion=1,sec=sys  0  0
${NFS_vh}:/${SID} /sapmnt/${SID}  nfs vers=4,minorversion=1,sec=sys  0  0
```
{: screen}

In the second example,  `/usr/sap/trans`, `/sapmnt/${SID}`, and all instance directories are NFS mounted on the SAP application server instances.

Use a temporary mount point to create the required subdirectories, change the ownership, and change the permissions.

```shell
mkdir -p /tmp/saptmp
```
{: pre}

```shell
mount -t nfs ${NFS_vh}:/ /tmp/saptmp -o vers=4,minorversion=1,sec=sys
```
{: pre}

```shell
mkdir -p /tmp/saptmp/${SID}/sapmnt
```
{: pre}

```shell
mkdir -p /tmp/saptmp/${SID}/ASCS
```
{: pre}

```shell
mkdir -p /tmp/saptmp/${SID}/ERS
```
{: pre}

```shell
mkdir -p /tmp/saptmp/${SID}/SYS
```
{: pre}

```shell
mkdir -p /tmp/saptmp/${SID}/PAS
```
{: pre}

```shell
mkdir -p /tmp/saptmp/${SID}/AAS
```
{: pre}

```shell
chown -R ${sid}adm:sapsys /tmp/saptmp/${SID}
```
{: pre}

```shell
umount /tmp/saptmp​
```
{: pre}

Prepare the final mount points that are used for the SAP installation.

```shell
mkdir -p /sapmnt/${SID}
```
{: pre}

```shell
mkdir -p /usr/sap/trans
```
{: pre}

```shell
mkdir -p /usr/sap/${SID}/SYS
```
{: pre}

```shell
mkdir -p /usr/sap/${SID}/ASCS${ASCS_nr}
```
{: pre}

```shell
mkdir -p /usr/sap/${SID}/ERS${ERS_nr}
```
{: pre}

Change the attributes.

```sh
chattr +i /usr/sap/trans
```
{: pre}

```sh
chattr +i /usr/sap/${SID}/SYS
```
{: pre}

```sh
chattr +i /usr/sap/${SID}/ASCS${ASCS_nr}
```
{: pre}

```sh
chattr +i /usr/sap/${SID}/ERS${ERS_nr}
```
{: pre}

```sh
chattr +i /sapmnt/${SID}
```
{: pre}

Add the new file systems to `/etc/fstab`.

```sh
​${NFS_vh}:/trans /usr/sap/trans  nfs vers=4,minorversion=1,sec=sys  0  0
${NFS_vh}:/${SID}/sapmnt /sapmnt/${SID}  nfs vers=4,minorversion=1,sec=sys  0  0
${NFS_vh}:/${SID}/ASCS /usr/sap/${SID}/ASCS${ASCS_nr} nfs vers=4,minorversion=1,sec=sys  0  0
${NFS_vh}:/${SID}/ERS  /usr/sap/${SID}/ERS${ERS_nr} nfs vers=4,minorversion=1,sec=sys  0  0
${NFS_vh}:/${SID}/SYS  /usr/sap/${SID}/SYS  nfs vers=4,minorversion=1,sec=sys  0  0
```
{: codeblock}

Verify that you can mount the NFS share with NFSv3.
Unlike NFSv4, NFSv3 does not use the virtual file system and you must mount a specific export.

### Test2 - Testing the failover of the NFS server
{: #ha-rhel-nfs-test-failover}

- Verify that the file system is mounted on a *NFS client node* outside the cluster, and that the applications can access the content.
- NFS server is active on NODE1.
- Crash NODE1 by sending a `fast-restart` system request.

On NODE1, run the following command.

```sh
sync; echo b > /proc/sysrq-trigger
```
{: pre}

#### Test2 - Expected behavior
{: #ha-rhel-nfs-test-failover-expected-behavior}

- NODE1 restarts.
- The cluster detects the failed node and sets its state to offline (UNCLEAN).
- The cluster acquires the **NFS Server** resources on NODE2. Check that all the resources are started on NODE2.

```sh
pcs resource status
```
{: pre}

Sample output:

```sh
# pcs resource status
  * Resource Group: nfsgroup:
    * nfs_vg    (ocf::heartbeat:LVM-activate):   Started cl-nfs-2
    * nfs_fs1   (ocf::heartbeat:Filesystem):     Started cl-nfs-2
    * nfs_fs2   (ocf::heartbeat:Filesystem):     Started cl-nfs-2
    * nfs_fs3   (ocf::heartbeat:Filesystem):     Started cl-nfs-2
    * nfs_daemon        (ocf::heartbeat:nfsserver):      Started cl-nfs-2
    * nfs-root  (ocf::heartbeat:exportfs):       Started cl-nfs-2
    * nfs_ip    (ocf::heartbeat:IPaddr2):        Started cl-nfs-2
    * nfs_notify        (ocf::heartbeat:nfsnotify):      Started cl-nfs-2
```
{: screen}

Verify that the file system is still mounted on the *NFS client node*, and that the applications can still access the content.

#### Test2 - Recovery procedure
{: #ha-rhel-nfs-test-recovery-procedure}

Wait until NODE1 is up, then restart the cluster framework.

On NODE1, run the following command.

```sh
pcs cluster start
```
{: pre}
