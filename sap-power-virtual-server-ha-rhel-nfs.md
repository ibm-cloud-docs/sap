---
copyright:
  years: 2023, 2024
lastupdated: "2024-10-31"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, NFS Server, Linux

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Configuring an Active-Passive NFS Server in a Red Hat High Availability Cluster
{: #ha-rhel-nfs}

The following information describes the configuration of an active-passive NFS server in a Red Hat Enterprise Linux (RHEL) HA Add-On cluster.
The cluster uses virtual server instances in [{{site.data.keyword.powerSysFull}}](https://www.ibm.com/products/power-virtual-server){: external} as cluster nodes.
{: shortdesc}

The described setup uses shareable storage volumes that are accessible on both cluster nodes.
The file systems for the NFS exports are created on those shareable storage volumes.
HA-LVM makes sure that the volume group is active on one node at a time.

In the example setup, one shared volume group `nfssharevg` contains three logical volumes `nfssharelv`, `sap${SID}lv`, and `saptranslv`.
XFS file systems are created on those logical volumes and are mounted on `/nfsshare`, `/nfshare/export/sap${SID}`, `/nfsshare/export/saptrans`.

The instructions are based on the Red Hat product documentation and articles that are listed in [Implementing High Availability for SAP Applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

## Before you begin
{: #ha-rhel-nfs-begin}

Review the general requirements, product documentation, support articles, and SAP notes listed in [Implementing High Availability for SAP Applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

## Prerequisites
{: #ha-rhel-nfs-prerequisites}

A virtual hostname and IP address is required for the NFS server.
Make sure that the virtual IP address is defined on the network interface, and reachable in the network.

Name resolution and reverse lookup for physical and virtual IP names and addresses must be unique and consistent on all NFS server and client nodes.
Details of the NFS clients (subnet, required NFS export options) must be available.
You need to enter them during the cluster setup.

## Preparing for a highly available NFS server
{: #ha-rhel-nfs-prepare-nodes}

Use the following information to prepare the environment for a highly available NFS server.

### Installing NFS software packages
{: #ha-rhel-nfs-prepare-packages}

On both nodes, run the following commands.

```sh
dnf install -y nfs-utils
```
{: pre}

### Preparing LVM objects
{: #ha-rhel-nfs-prepare-lvm-objects}

All cluster nodes need access to the shared storage volumes, but only one node has exclusive read and write access to a volume.

#### Preparing active-passive HA LVM
{: #ha-rhel-nfs-prepare-lvm}

On both nodes, edit file `/etc/lvm/lvm.conf` to include the system ID in the volume group.
Search for the configuration setting `system_id_source` and change its value to "uname".

Sample setting of `system_id_source` in `/etc/lvm/lvm.conf`:

```sh
# grep "system_id_source ="  /etc/lvm/lvm.conf
system_id_source = "uname"
```
{: screen}

Verify that the `LVM system ID` for the node matches the `uname -n` output.

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

1. Log in to {{site.data.keyword.cloud}} and go to the [Storage volumes](https://cloud.ibm.com/power/storage){: external} view of {{site.data.keyword.powerSys_notm}}.
1. Select your **workspace**.
1. Filter for the *volume prefix* in the *Storage volumes* list, and identify all the **World Wide Names** of the volumes in scope (the *World Wide Name* is a 32-digit hexadecimal number).

   Make sure that the attribute **Shareable** is *On* for those volumes.
   {: note}

1. In the [Virtual server instances](https://cloud.ibm.com/power/servers){: external} view, go to both virtual server instances of the cluster and verify that in scope volumes are attached to both virtual server instances.

### Discovering new SAN volumes on cluster nodes
{: #ha-rhel-nfs-discover-san-volumes}

When you attach a new storage volume to a virtual server instance, you need to rescan the SCSI bus to detect the new volume.
Then, update the *multipath configuration* of the virtual server instance.

On the nodes with new storage volume attachments, run the following command.

```sh
rescan-scsi-bus.sh && sleep 10 && multipathd reconfigure
```
{: pre}

The *WWN* value of a volume can also be found with the `pvs --all` command.
{: tip}

### Preparing environment variables
{: #ha-rhel-nfs-prepare-environment-variables}

To simplify the setup, prepare the following environment variables for user ID `root` on both nodes.
These environment variables are used in subsequent commands in the remainder of this document.

On both nodes, create a file with the environment variables.
Then, adapt them to your configuration.

Adapt `NFS_VH`, `NFS_IP`, `NFS_CLIENTSPEC`, and `NFS_OPTIONS` to your environment.
For `NFS_PVID`, use the *WWN* that you identified previously.
In addition to the file system that is used for the NFS share, the example shows two more file systems that are used for an SAP system landscape with system ID `${SID}` and the SAP transport directory.
The sample sizes `${NFS_SZ1}`, `${NFS_SZ2}`, and `${NFS_SZ3}` are percentages of the `${NFS_VG}` volume group size and need to be modified according to your requirements.
The volume group names and mount point names are suggestions and need to be changed to match your own naming conventions.

Make sure that you set the `NFS_PVID` environment variable by using lowercase letters in the hexadecimal number.
{: tip}

```sh
# virtual hostnames
export NFS_VH=<virtual hostname>        # virtual hostname for NFS server
export NFS_IP=<IP address>              # virtual IP address for NFS server

# LVM storage for NFS file systems
export NFS_PVID=3<WWN>                  # WWN of shareable storage volume used for NFS
export NFS_VG="nfssharevg"              # volume group name for NFS exported file systems

# NFS share file system
export NFS_LV1="nfssharelv"             # logical volume name export #1
export NFS_SZ1="5%VG"                   # logical volume size
export NFS_FS1="/nfsshare"              # file system mount point
export NFS_ROOT="${NFS_FS1}/export"     # base export directory

# NFS share file system for SAP system ID <SID>
export SID=<SID>                        # SAP system ID
export NFS_LV2="sap${SID}lv"            # logical volume name export #2
export NFS_SZ2="40%VG"                  # logical volume size
export NFS_FS2="${NFS_ROOT}/sap${SID}"  # file system mount point

# NFS share file system for SAP transport directory
export NFS_LV3="saptranslv"             # logical volume name export #3
export NFS_SZ3="40%VG"                  # logical volume size
export NFS_FS3="${NFS_ROOT}/saptrans"   # file system mount point

# NFS client options
export NFS_CLIENTSPEC="10.111.1.0/24"   # client specs (subnet and netmask) for allowed NFS clients
export NFS_OPTIONS="rw,sync,no_root_squash,no_subtree_check,crossmnt"   # options for NFS export
```
{: codeblock}

You must source this file before you use the sample commands in the remainder of this document.

For example, if you created a file that is named `nfs_envs.sh`, run the following command on both nodes to set the environment variables.

```sh
source nfs_envs.sh
```
{: pre}

Every time that you start a new terminal session, you must run the `source` command.
Alternatively, you can add the environment variables file to the `/etc/profile.d` directory during the cluster configuration.
In this example, the file is sourced automatically each time you log in to the server.
{: note}

### Creating LVM objects
{: #ha-rhel-nfs-create-lvm-objects}

Use the following information to create LVM objects.

#### Creating physical volumes
{: #ha-rhel-nfs-create-nfs-pv}

On NODE1, run the following command.

```sh
pvcreate /dev/mapper/${NFS_PVID}
```
{: pre}

Sample output:

```sh
pvcreate /dev/mapper/${NFS_PVID}
  Physical volume "/dev/mapper/360050768108103357000000000002ddc" successfully created.
```
{: screen}

#### Creating a volume group
{: #ha-rhel-nfs-create-nfs-vg}

On NODE1, create the volume group for the NFS export.

```sh
vgcreate ${NFS_VG} /dev/mapper/${NFS_PVID}
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
  nfssharevg    1   0   0 wz--n- <50.00g <50.00g cl-sap-1
```
{: screen}

#### Creating logical volumes
{: #ha-rhel-nfs-create-nfs-lv}

On NODE1, create three logical volumes for the NFS export.

```sh
lvcreate -l ${NFS_SZ1} -n ${NFS_LV1} ${NFS_VG}
```
{: pre}

```sh
lvcreate -l ${NFS_SZ2} -n ${NFS_LV2} ${NFS_VG}
```
{: pre}

```sh
lvcreate -l ${NFS_SZ3} -n ${NFS_LV3} ${NFS_vg}
```
{: pre}

#### Creating the file systems
{: #ha-rhel-nfs-create-nfs-fs}

On NODE1, create the file systems for NFS exports.

The example uses file system type `xfs`.
Other file system types are possible.
Then, the resource definitions need to be changed.

```sh
mkfs.xfs /dev/${NFS_VG}/${NFS_LV1}
```
{: pre}

```sh
mkfs.xfs /dev/${NFS_VG}/${NFS_LV2}
```
{: pre}

```sh
mkfs.xfs /dev/${NFS_VG}/${NFS_LV3}
```
{: pre}

#### Creating the mount point for the NFS export
{: #ha-rhel-nfs-create-nfs-mtpt}

On both nodes, run the following command.

```sh
mkdir -p ${NFS_FS1}
```
{: pre}

#### Making sure that a volume group is not activated on multiple cluster nodes
{: #ha-rhel-nfs-upd-vg-auto-activate}

Volume groups that are managed by Pacemaker must not activate automatically on startup.

For RHEL 8.5 and later, you can disable autoactivation for a volume group when you create the volume group by specifying the `--setautoactivation n` flag for the vgcreate command.
{: tip}

On both nodes, edit file `/etc/lvm/lvm.conf` and modify the `auto_activation_volume_list` entry to limit autoactivation to specific volume groups.
Search for parameter `auto_activation_volume_list` and add the volume groups, other than the volume group that you defined for the NFS cluster, as entries in that list.

Sample setting of the `auto_activation_volume_list` entry in `/etc/lvm/lvm.conf`:

```sh
auto_activation_volume_list = [ "rhel_root" ]
```
{: screen}

Rebuild the *initramfs* boot image to make sure that the boot image does not activate a volume group that is controlled by the cluster.

On both nodes, run the following command.

```sh
dracut -H -f /boot/initramfs-$(uname -r).img $(uname -r)
```
{: pre}

Reboot both nodes.

## Installing and setting up the RHEL HA Add-On cluster
{: #ha-rhel-nfs-cluster-install}

Use the following instructions to perform the initial cluster configuration.

- Install and set up the RHEL HA Add-On cluster according to [Implementing a RHEL HA Add-On Cluster on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-ha-rhel).
- Configure and test fencing as described in [Creating the fencing device](/docs/sap?topic=sap-ha-rhel#ha-rhel-create-fencing-device).

Sample output of the cluster status at this stage.

```sh
# pcs status
Cluster name: SAP_NFS
Cluster Summary:
  * Stack: corosync
  * Current DC: cl-nfs-1 (version 2.0.5-9.el8_4.5-ba59be7122) - partition with quorum
  * Last updated: Fri Mar 10 10:35:42 2023
  * Last change:  Fri Mar 10 09:52:08 2023 by root via cibadmin on cl-nfs-1
  * 2 nodes configured
  * 1 resource instance configured

Node List:
  * Online: [ cl-nfs-1 cl-nfs-2 ]

Full List of Resources:
  * res_fence_ibm_powervs	(stonith:fence_ibm_powervs):	 Started cl-nfs-1

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```
{: screen}

### Configuring general cluster properties
{: #ha-rhel-hana-nfs-configure-cluster-properties}

To prevent the cluster from moving healthy resources to another node (for example when you restart the cluster on a previously failed node), you can set the default value for the `resource-stickiness` meta attribute to 1.

On NODE1, run the following command.

```sh
pcs resource defaults update resource-stickiness=1
```
{: pre}

### Configuring NFS resource group and resources
{: #ha-rhel-nfs-cfg-nfs-resources}

Use the following steps to configure the NFS resources in the cluster.

#### Creating the LVM-activate resource
{: #ha-rhel-nfs-cfg-lvm-rg}

To make sure that all NFS resources run on the same node, configure them as part of the resource group `nfsgroup`.

This resource group is created with the first resource.
Resources start in the order in which they are added to the group.
The resources stop in reverse order.

On NODE1, run the following command.

```sh
pcs resource create nfs_vg ocf:heartbeat:LVM-activate \
    vgname=${NFS_VG} \
    vg_access_mode=system_id \
    --group nfsgroup
```
{: pre}

To avoid data corruption, don't configure more than one *LVM-activate* resource that uses the same LVM volume group in an active-passive HA configuration.
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
The file systems use LVM volume group `${NFS_vg}` and the logical volumes (`${NFS_LV1}`, `${NFS_LV2}`, `${NFS_LV3}`) that were created before.

On NODE1, run the following command.

```sh
pcs resource create nfs_fs1 Filesystem \
    device=/dev/${NFS_VG}/${NFS_LV1} \
    directory=${NFS_FS1} \
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

On the node with the active resource group `nfsgroup`, create two subdirectories in `${NFS_FS1}`.
`${NFS_FS1}/stat` is used as `nfs_shared_infodir` for NFS lock information and `${NFS_FS1}/export` is used as NFS root.

```sh
mkdir ${NFS_FS1}/stat ${NFS_FS1}/export
```
{: pre}

Create the mount points for the other exported file systems.

On both nodes, run the following command.

```sh
mkdir ${NFS_FS2} ${NFS_FS3}
```
{: pre}

Create the resources for the other two NFS file systems.

On NODE1, run the following commands.

```sh
pcs resource create nfs_fs2 Filesystem \
    device=/dev/${NFS_VG}/${NFS_LV2} \
    directory=${NFS_FS2} \
    fstype=xfs \
    --group nfsgroup
```
{: pre}

```sh
pcs resource create nfs_fs3 Filesystem \
    device=/dev/${NFS_VG}/${NFS_LV3} \
    directory=${NFS_FS3} \
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
    nfs_shared_infodir=${NFS_FS1}/stat \
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

To export the `${NFS_ROOT}` directory, add the *exportfs* resources to the `nfsgroup` group, which builds a virtual directory for NFSv4 clients.
NFSv3 clients can access these exports too.

On NODE1, run the following command.

```sh
pcs resource create nfs_export exportfs \
    clientspec=${NFS_CLIENTSPEC} \
    options=${NFS_OPTIONS} \
    directory=${NFS_ROOT} \
    fsid=0 \
    --group nfsgroup
```
{: pre}

#### Configuring a floating IP address resource
{: #ha-rhel-nfs-cfg-vip-rg}

Review the information in [Reserving virtual IP addresses](/docs/sap?topic=sap-ha-vsi#ha-vsi-reserve-virtual-ip-addresses) and reserve a virtual IP address for the NFS cluster.

Create a resource for the virtual IP address of the NFS Server.
NFS clients access the NFS share by using the floating IP address.

On NODE1, run the following command.

```sh
pcs resource create nfs_ip IPaddr2 \
    ip=${NFS_IP} \
    --group nfsgroup
```
{: pre}

#### Configuring a notify resource
{: #ha-rhel-nfs-cfg-notify-rg}

The *nfsnotify* resource sends *FSv3* reboot notifications after the entire NFS deployment initializes.

On NODE1, run the following command.

```sh
pcs resource create nfs_notify nfsnotify \
    source_host=${NFS_IP} \
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
    * nfs_export        (ocf::heartbeat:exportfs):       Started cl-nfs-1
    * nfs_ip    (ocf::heartbeat:IPaddr2):        Started cl-nfs-1
    * nfs_notify        (ocf::heartbeat:nfsnotify):      Started cl-nfs-1
```
{: screen}

## Testing the NFS server cluster
{: #ha-rhel-nfs-test-cluster}

You can validate the NFS resource configuration in a high availability cluster by using the following procedures.
You can mount the exported file system with either NFSv3 or NFSv4.
Run the following tests to verify that the NFS cluster functions.

### Test1 - Testing the NFS export
{: #ha-rhel-nfs-test-export}

Use the following information to test the NFS export.

Run all the commands on an *NFS client node* outside the HA NFS cluster.

Verify the NFS exports.

```sh
showmount -e ${NFS_IP}
```
{: pre}

The `showmount` command displays information about file systems that are exported by an NFS Server (NFS v3).
Verify that the output lists all the exported directories.

Create a temporary directory on the *NFS client node*.
Then, mount the NFS file system and create the directory structure that is required for the SAP installation.

In the first example, only `/usr/sap/trans` and `/sapmnt/${SID}` are NFS mounted on the SAP application server instance.

Prepare the mount points that are used for the SAP installation.

```sh
mkdir -p /sapmnt/${SID} \
         /usr/sap/trans
```
{: pre}

Change the attributes of the mount points.

```sh
chattr +i /sapmnt/${SID} \
          /usr/sap/trans
```
{: pre}

Mount the NFS shares.

```sh
mount -t nfs -o vers=4,minorversion=1,sec=sys ${NFS_VH}:/saptrans /usr/sap/trans
```
{: pre}

```sh
mount -t nfs -o vers=4,minorversion=1,sec=sys ${NFS_VH}:/sap${SID} /sapmnt/${SID}
```
{: pre}

Change the ownership and the permissions.

```sh
chown ${sid}adm:sapsys /usr/sap/trans
```
{: pre}

```sh
chmod g+w /usr/sap/trans
```
{: pre}

```sh
chown -R ${sid}adm:sapsys /sapmnt/${SID}
```
{: pre}

Unmount the file systems.

```sh
umount /usr/sap/trans
```
{: pre}

```sh
umount /sapmnt/${SID}
```
{: pre}

Add the new file systems to `/etc/fstab`.

```sh
cat >> /etc/fstab << EOT
​${NFS_VH}:/saptrans /usr/sap/trans  nfs vers=4,minorversion=1,sec=sys  0  0
${NFS_VH}:/sap${SID} /sapmnt/${SID}  nfs vers=4,minorversion=1,sec=sys  0  0
EOT
```
{: pre}

Check the updated file.

```sh
cat /etc/fstab
```
{: pre}


In the second example, `/usr/sap/trans`, `/sapmnt/${SID}`, and all instance directories are NFS mounted on the SAP application server instances.

Export environment variables for the *ASCS* and *ERS* system numbers.
Change the following numbers to the system numbers that you used during *ASCS* and *ERS* installation.

```sh
export ASCS_NR=01
```
{: pre}

```sh
export ERS_NR=02
```
{: pre}

Prepare the final mount points that are used for the SAP installation.

```sh
mkdir -p /sapmnt/${SID} \
         /usr/sap/trans \
         /usr/sap/${SID}/SYS \
         /usr/sap/${SID}/ASCS${ASCS_NR} \
         /usr/sap/${SID}/ERS${ERS_NR}
```
{: pre}

Change the attributes of the mount points.

```sh
chattr +i /sapmnt/${SID} \
          /usr/sap/trans \
          /usr/sap/${SID}/SYS \
          /usr/sap/${SID}/ASCS${ASCS_NR} \
          /usr/sap/${SID}/ERS${ERS_NR}
```
{: pre}

Mount the NFS shares to create the required subdirectories, change the ownership, and change the permissions.

```sh
mount -t nfs -o vers=4,minorversion=1,sec=sys ${NFS_VH}:/saptrans /mnt
```
{: pre}

```sh
chown ${sid}adm:sapsys /mnt
```
{: pre}

```sh
chmod g+w /mnt
```
{: pre}

```sh
umount /mnt
```
{: pre}

```sh
mount -t nfs -o vers=4,minorversion=1,sec=sys ${NFS_VH}:/sap${SID} /mnt
```
{: pre}

```sh
mkdir -p /mnt/sapmnt \
         /mnt/ASCS \
         /mnt/ERS \
         /mnt/SYS \
         /mnt/PAS \
         /mnt/AS1
```
{: pre}

```sh
chown -R ${sid}adm:sapsys /mnt
```
{: pre}

```sh
umount /mnt
```
{: pre}

Add the new file systems to `/etc/fstab`.

```sh
cat >> /etc/fstab < EOT
​${NFS_VH}:/saptrans /usr/sap/trans  nfs vers=4,minorversion=1,sec=sys  0  0
${NFS_VH}:/sap${SID}/sapmnt /sapmnt/${SID}  nfs vers=4,minorversion=1,sec=sys  0  0
${NFS_VH}:/sap${SID}/ASCS /usr/sap/${SID}/ASCS${ASCS_NR} nfs vers=4,minorversion=1,sec=sys  0  0
${NFS_VH}:/sap${SID}/ERS  /usr/sap/${SID}/ERS${ERS_NR} nfs vers=4,minorversion=1,sec=sys  0  0
${NFS_VH}:/sap${SID}/SYS  /usr/sap/${SID}/SYS  nfs vers=4,minorversion=1,sec=sys  0  0
EOT
```
{: pre}

Check the updated file.

```sh
cat /etc/fstab
```
{: pre}

### Test2 - Testing the failover of the NFS server
{: #ha-rhel-nfs-test-server-failover}

Use the following information to test the failover of the NFS server.

#### Test2 - Description
{: #ha-rhel-nfs-test2-description}

Simulate a crash of the cluster node that has the NFS resources.

#### Test2 - Prerequisites
{: #ha-rhel-nfs-test2-prerequisites}

- A functional two-node RHEL HA Add-On cluster for an NFS HA server.
- Cluster is started on both nodes.
- The file systems are mounted on an *NFS client node* outside the cluster and the applications can access the content.

#### Test2 - Test procedure
{: #ha-rhel-nfs-test2-procedure}

Crash the cluster node by sending a *shutoff* system request.

First, check the cluster status and identify the node where the *nfsgroup* resource group is running.

On NODE1, run the following command.

```sh
pcs status
```
{: pre}

Then, log in to the identified cluster node and send a *shutoff* system request.

```sh
sync; echo o > /proc/sysrq-trigger
```
{: pre}

#### Test2 - Expected behavior
{: #ha-rhel-nfs-test2-expected-behavior}

- The node with the active *nfsgroup* resource group shuts down.
- The cluster detects the failed node and starts a fencing action.
- The fencing operation sets the state of the fenced node to offline.
- The cluster acquires the *NFS Server* resources on the failover node.

Check that all the resources started on the failover node.

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
    * nfs_export        (ocf::heartbeat:exportfs):       Started cl-nfs-2
    * nfs_ip    (ocf::heartbeat:IPaddr2):        Started cl-nfs-2
    * nfs_notify        (ocf::heartbeat:nfsnotify):      Started cl-nfs-2
```
{: screen}

Verify that the file system is still mounted on the *NFS client node*, and that the applications can still access the content.

#### Test2 - Recovery procedure
{: #ha-rhel-nfs-test2-recovery-procedure}

Log in to the {{site.data.keyword.cloud_notm}} Console and start the stopped instance.
Wait until the cluster node is available again, then restart the cluster framework.

On the cluster node, run the following command.

```sh
pcs cluster start
```
{: pre}

Check the cluster status.

```sh
pcs status
```
{: pre}

Sample output:

```sh
# pcs status
Cluster name: SAP_NFS
Cluster Summary:
  * Stack: corosync
  * Current DC: cl-nfs-1 (version 2.0.5-9.el8_4.5-ba59be7122) - partition with quorum
  * Last updated: Mon Mar 20 08:11:28 2023
  * Last change:  Mon Mar 20 07:56:25 2023 by hacluster via crmd on cl-nfs-1
  * 2 nodes configured
  * 9 resource instances configured

Node List:
  * Online: [ cl-nfs-1 cl-nfs-2 ]

Full List of Resources:
  * res_fence_ibm_powervs	(stonith:fence_ibm_powervs):	 Started cl-nfs-1
  * Resource Group: nfsgroup:
    * nfs_vg	(ocf::heartbeat:LVM-activate):	 Started cl-nfs-2
    * nfs_fs1	(ocf::heartbeat:Filesystem):	 Started cl-nfs-2
    * nfs_fs2	(ocf::heartbeat:Filesystem):	 Started cl-nfs-2
    * nfs_fs3	(ocf::heartbeat:Filesystem):	 Started cl-nfs-2
    * nfs_daemon	(ocf::heartbeat:nfsserver):	 Started cl-nfs-2
    * nfs_export	(ocf::heartbeat:exportfs):	 Started cl-nfs-2
    * nfs_ip	(ocf::heartbeat:IPaddr2):	 Started cl-nfs-2
    * nfs_notify	(ocf::heartbeat:nfsnotify):	 Started cl-nfs-2

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```
{: screen}
