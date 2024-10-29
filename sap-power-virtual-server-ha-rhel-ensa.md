---

copyright:
  years: 2023, 2024
lastupdated: "2024-10-29"


keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn

subcollection: sap

---

{{site.data.keyword.attribute-definition-list}}

# Configuring High Availability for SAP S/4HANA (ASCS and ERS) in a RHEL HA Add-On Cluster
{: #ha-rhel-ensa}

The following information describes the configuration of *ABAP SAP Central Services (ASCS)* and *Enqueue Replication Service (ERS)* with Red Hat Enterprise Linux (RHEL) in a RHEL HA Add-On cluster.
The cluster uses virtual server instances in [{{site.data.keyword.powerSysFull}}](https://www.ibm.com/products/power-virtual-server){: external} as cluster nodes.
{: shortdesc}

The focus of this example configuration is on the second generation of the [Standalone Enqueue Server](https://help.sap.com/docs/ABAP_PLATFORM/cff8531bc1d9416d91bb6781e628d4e0/902412f09e134f5bb875adb6db585c92.html){: external}, or *ENSA2*.

Starting with the release of SAP S/4HANA 1809, ENSA2 is installed by default, and can be configured in a two-node or multi-node cluster.
This example uses the *ENSA2* setup for a two-node RHEL HA Add-On cluster.
If the *ASCS* service fails in a two-node cluster, it restarts on the node where *ERS* is running.
The lock entries for the SAP application are restored from the copy of the lock table in the *ERS*.
When an administrator activates the failed cluster node, the *ERS* instance moves to the other node (anti-collocation) to protect the lock table copy.

It is recommended that you install the SAP database instance and other SAP application server instances on virtual server instances outside the two-node cluster for *ASCS* and *ERS*.


## Before you begin
{: #ha-rhel-ensa-begin}

Review the general requirements, product documentation, support articles, and SAP notes listed in [Implementing High Availability for SAP Applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

## Prerequisites
{: #ha-rhel-ensa-prerequisites}

- The virtual server instances must meet the hardware and resource requirements of the SAP instances installed on them.
   Follow the guidelines on instance types, storage, and memory sizing in the [Planning your deployment](/docs/sap?topic=sap-power-vs-planning-items) document.
- This information describes a setup that uses shareable storage volumes accessible on both cluster nodes.
   Certain file systems are created on shareable storage volumes so that they can be mounted on both cluster nodes.
   This setup applies to both instance directories.
   - `/usr/sap/<SID>/ASCS<Inst#>` of the *ASCS* instance.
   - `/usr/sap/<SID>/ERS<Inst#>` of the *ERS* instance.

   Make sure that the storage volumes that were created for these file systems are attached to both virtual server instances.
   During SAP instance installation and RHEL HA Add-On cluster configuration, each instance directory must be mounted on its appropriate node.
   HA-LVM ensures that each of the two instance directories is mounted on only one node at a time.

   Different storage setups for the instance directories, such as NFS mounts, are possible.
   Storage setup steps for file storage or creation of cluster file system resources are not described in this document.
   {: important}

- The virtual hostname for *ASCS* instance and *ERS* instance must meet the requirements as documented in [Hostnames of SAP ABAP Platform servers](https://me.sap.com/notes/611361){: external}.
   Make sure that the virtual IP addresses for the SAP instances are assigned to a network adapter and that they can communicate in the network.
- SAP application server instances require a common shared file system *SAPMNT* `/sapmnt/<SID>` with *read and write* access, and other shared file systems such as *SAPTRANS* `/usr/sap/trans`.
   These file systems are typically provided by an external NFS server.
   The NFS server must be high-available and must not be installed on virtual servers that are part of the *ENSA2* cluster.

   [Configuring an Active-Passive NFS Server in a Red Hat High Availability Cluster](/docs/sap?topic=sap-ha-rhel-nfs) describes the implementation of an active-passive NFS server in a RHEL HA Add-On cluster with Red Hat Enterprise Linux 8 by using virtual server instances in {{site.data.keyword.powerSys_notm}}.
   
- Ensure that all SAP installation media is available.

## Preparing nodes for SAP installation
{: #ha-rhel-ensa-prepare-nodes}

The following information describes how to prepare the nodes for an SAP installation.

### Preparing environment variables
{: #ha-rhel-ensa-prepare-environment-variables}

To simplify the setup, prepare the following environment variables for user `root` on both cluster nodes.
These environment variables are used in subsequent commands in the remainder of the instructions.

On both nodes, create a file with the following environment variables.
Next, update these variables according to your configuration.

```sh
export SID=<SID>                   # SAP System ID (uppercase)
export sid=<sid>                   # SAP System ID (lowercase)

# ASCS instance
export ASCS_NO=<INSTNO>            # ASCS instance number
export ASCS_VH=<virtual hostname>  # ASCS virtual hostname
export ASCS_IP=<IP address>        # ASCS virtual IP address
export ASCS_VG=<vg name>           # ASCS volume group name
export ASCS_LV=<lv name>           # ASCS logical volume name

# ERS instance
export ERS_NO=<INSTNO>             # ERS instance number
export ERS_VH=<virtual hostname>   # ERS virtual hostname
export ERS_IP=<IP address>         # ERS virtual IP address
export ERS_VG=<vg name>            # ERS volume group name
export ERS_LV=<lv name>            # ERS logical volume name
```
{: codeblock}

It is recommended to use meaningful names for the volume groups and logical volumes that designate their content.
For example, include the *SID* and *ascs* or *ers* in the name.
Don't use hyphens in the volume group or logical volume names.

- s01ascsvg and s01ascslv
- s01ersvg and s01erslv

You must source this file before you use the sample commands in the remainder of the instructions.

For example, if you created a file that is named `sap_envs.sh`, run the following command on both nodes to set the environment variables.

```sh
source sap_envs.sh
```
{: pre}

Every time that you start a new terminal session, you must run the previous `source` command.
As an alternative, you can move the environment variables file to the `/etc/profile.d` directory during the cluster configuration.
In this example, the file is sourced automatically each time you log in to the server.
{: important}

### Assigning virtual IP addresses
{: #ha-rhel-ensa-assign-virtual-ip}

Review the information in [Reserving virtual IP addresses](/docs/sap?topic=sap-ha-vsi#ha-vsi-reserve-virtual-ip-addresses).

Check whether the virtual IP address for the SAP instance is present.
Otherwise, you need to identify the correct network adapter to assign the IP address.

On both nodes, check the list of currently active IP addresses.

```sh
ip -o -f inet address show | '/scope global/ {print $2, $4}'
```
{: pre}

Sample output of the previous command.

```sh
# ip -o -f inet address show | awk '/scope global/ {print $2, $4}'
env2 10.51.0.66/24
env3 10.52.0.41/24
env4 10.111.1.28/24
```
{: screen}

The device name of the network adapter appears in the first column.
The second column lists the active IP addresses and the number of bits that are reserved for the netmask - which are separated by a slash.

If the virtual IP address for the SAP instance is not present, make sure that it isn't erroneously set on another virtual server instance.

On NODE1, run the following command.

```sh
ping -c 3 ${ASCS_VH}
```
{: pre}

Sample output:

```sh
# ping -c 2 cl-sap-scs
PING cl-sap-scs (10.111.1.248) 56(84) bytes of data.
From cl-sap-1.tst.ibm.com (10.111.1.28) icmp_seq=1 Destination Host Unreachable
From cl-sap-1.tst.ibm.com (10.111.1.28) icmp_seq=2 Destination Host Unreachable

--- cl-sap-ers ping statistics ---
2 packets transmitted, 0 received, +2 errors, 100% packet loss, time 2112ms
pipe 3
```
{: screen}

If the `ping` output shows `Destination Host Unreachable`, the IP address is available, and you can assign the IP alias to the virtual server instance.
Use the correct device name *env* of the network adapter that matches the subnet of the IP address.

Example command on NODE1:

```sh
ip addr add ${ASCS_IP} dev env4
```
{: pre}

Example command on NODE2:

```sh
ip addr add ${ERS_IP} dev env4
```
{: pre}

According to your specific network configuration, the device name for the network adapter might be different.
{: note}

The IP address is required for the SAP installation, and is set manually.
Later, the virtual IP addresses are controlled by the Red Hat HA Cluster Add-on.

### Preparing volume groups, logical volumes, and shared file systems
{: #ha-rhel-ensa-activate-volume-groups}

Shared storage is an important resource in an *ENSA2* cluster.
*ASCS* and *ERS* must be able to run on both nodes, and their runtime environment is stored in the shared storage volumes.
All cluster nodes need to access the shared storage volumes, but only one node has exclusive read and write access to a volume.

#### Preparing High Availability Logical Volume Manager settings
{: #ha-rhel-ensa-prepare-lvm}

Edit the file `/etc/lvm/lvm.conf` to include the *system ID* in the volume group.

On both nodes, edit the `lvm.conf` file.

```sh
vi /etc/lvm/lvm.conf
```
{: pre}

Search for the `system_id_source` parameter and change its value to `uname`.

Sample setting for the `system_id_source` parameter in `etc/lvm/lvm.conf`.

```sh
system_id_source = "uname"
```
{: screen}

#### Identifying World Wide Names of shared storage volumes
{: #ha-rhel-ensa-identify-wwn}

Determine the World Wide Name (WWN) for each storage volume that is part of one of the shared volume groups.

1. Log in to {{site.data.keyword.cloud}} to the [Storage volumes](/power/storage){: external} view of {{site.data.keyword.powerSys_notm}}.
1. Select your **workspace**.
1. Filter on the *volume prefix* in the *Storage volumes* list, and identify all the **World Wide Names** of the volumes that are in scope for *ASCS* and *ERS* instances. The *World Wide Name* is a 32-digit hexadecimal number.

   Make sure that the attribute **Shareable** is *On* for those volumes.
   {: tip}

In the [Virtual server instances](/power/servers){: external} view, go to both virtual server instances of the cluster.
Verify that all volumes that are in scope for *ASCS* and *ERS* are attached to both virtual server instances.

When you attach a new storage volume to a virtual server instance, make sure that you *rescan the SCSI bus* to detect the new volume.
Afterward, update the *multipath configuration* of the virtual server instance.

On the nodes with new storage volume attachments, run the following command.

```sh
rescan-scsi-bus.sh && sleep 10 && multipathd reconfigure
```
{: pre}

Log in to both cluster nodes, and add the **WWN** to the environment variables of user `root`.

Use the `pvs --all` command to determine the appropriate **WWN** values.
{: tip}

On NODE1, export the `ASCS_PVID` environment variable.

```sh
export ASCS_PVID=3<WWN>  # WWN of shared storage volume for ASCS
```
{: screen}

On NODE2, export the `ERS_PVID` environment variable.

```sh
export ERS_PVID=3<WWN>   # WWN of shared storage volume for ERS
```
{: screen}

Make sure that you set the environment variable by using the hexadecimal number with lowercase letters.
{: tip}

#### Creating physical volumes
{: #ha-rhel-ensa-create-pv}

On NODE1, run the following command.

```sh
pvcreate /dev/mapper/${ASCS_PVID}
```
{: pre}

Sample output:

```sh
# pvcreate /dev/mapper/${ASCS_PVID}
  Physical volume "/dev/mapper/360050768108103357000000000002ddc" successfully created.
```
{: screen}

On NODE2, run the following command.

```sh
pvcreate /dev/mapper/${ERS_PVID}
```
{: pre}

Sample output:

```sh
# pvcreate /dev/mapper/${ERS_PVID}
  Physical volume "/dev/mapper/360050768108103357000000000002e31" successfully created.
```
{: screen}

#### Creating volume groups
{: #ha-rhel-ensa-create-vg}

Create the volume group for the ASCS.

On NODE1, run the following command.

```sh
vgcreate ${ASCS_VG} /dev/mapper/${ASCS_PVID}
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
  s01ascsvg     1   0   0 wz--n- <50.00g <50.00g cl-sap-1
```
{: screen}

Create the volume group for the *ERS*.

On NODE2, run the following command.

```sh
vgcreate ${ERS_VG} /dev/mapper/${ERS_PVID}
```
{: pre}

Verify that the System ID is set.

Sample output:
```sh
# vgs -o+systemid
  VG          #PV #LV #SN Attr   VSize   VFree   System ID
  s01ersvg     1   0   0 wz--n- <50.00g <50.00g cl-sap-2
```
{: screen}

#### Creating logical volumes and file systems
{: #ha-rhel-ensa-create-lv}

Create the logical volume for the *ASCS* and format it as an *XFS* file system.

On NODE1, run the following commands.

```sh
lvcreate -l 100%FREE -n ${ASCS_LV} ${ASCS_VG}
```
{: pre}

```sh
mkfs.xfs /dev/${ASCS_VG}/${ASCS_LV}
```
{: pre}

Create the logical volume for the *ERS* and format it as an *XFS* file system.

On NODE2, run the following commands.

```sh
lvcreate -l 100%FREE -n ${ERS_LV} ${ERS_VG}
```
{: pre}

```sh
mkfs.xfs /dev/${ERS_VG}/${ERS_LV}
```
{: pre}

#### Making sure that a volume group is not activated on multiple cluster nodes
{: #ha-rhel-ensa-upd-vg-auto-activate}

Volume groups that are managed by the cluster must not activate automatically on startup.

For RHEL 8.5 and later, disable autoactivation when creating the volume group by specifying the `--setautoactivation n` flag on the vgcreate command.
{: tip}

On both nodes, edit the `/etc/lvm/lvm.conf` file and modify the `auto_activation_volume_list` entry to limit autoactivation to specific volume groups.

```sh
vi /etc/lvm/lvm.conf
```
{: pre}

Locate the `auto_activation_volume_list` parameter and add all volume groups except the one you defined for the NFS cluster to this list.

See an example of how to set the `auto_activation_volume_list` entry in `/etc/lvm/lvm.conf`:

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

#### Mounting the file systems for SAP installation
{: #ha-rhel-ensa-mount-fs}

Activate the volume groups and mount the SAP instance file systems.

On *NODE1 (ASCS)*, run the following commands.

```sh
vgchange -a y ${ASCS_VG}
```
{: pre}

```sh
mkdir -p /usr/sap/${SID}/ASCS${ASCS_NO}
```
{: pre}

```sh
mount /dev/${ASCS_VG}/${ASCS_LV} /usr/sap/${SID}/ASCS${ASCS_NO}
```
{: pre}

On *NODE2 (ERS)*, run the following commands.

```sh
vgchange -a y ${ERS_VG}
```
{: pre}

```sh
mkdir -p /usr/sap/${SID}/ERS${ERS_NO}
```
{: pre}

```sh
mount /dev/${ERS_VG}/${ERS_LV} /usr/sap/${SID}/ERS${ERS_NO}
```
{: pre}

### Mounting the required NFS file systems
{: #ha-rhel-ensa-mount-nfs-file-systems}

On both nodes, make sure that the NFS file systems `/sapmnt` and `/usr/sap/trans` are mounted.

```sh
mount | grep nfs
```
{: pre}

## Installing SAP instances
{: #ha-rhel-ensa-install-sap-instances}

Use the SAP Software Provisioning Manager (SWPM) to install all instances.

- Install SAP instances on the cluster nodes.
   - Install an *ASCS* instance on NODE1 by using the virtual hostname `${ASCS_VH}` that is associated with the virtual IP address for *ASCS*:

   ```sh
   <swpm>/sapinst SAPINST_USE_HOSTNAME=${ASCS_VH}
   ```
   {: screen}

   - Install an *ERS* instance on NODE2 by using the virtual hostname `${ERS_VH}` that is associated with the virtual IP address for *ERS*:

   ```sh
   <swpm>/sapinst SAPINST_USE_HOSTNAME=${ERS_VH}
   ```
   {: screen}

- Install instances outside the cluster.
   - DB instance
   - PAS instance
   - AAS instances

## Installing and setting up the RHEL HA Add-On cluster
{: #ha-rhel-ensa-set-up}

Install and set up the RHEL HA Add-On cluster according to [Implementing a RHEL HA Add-On Cluster on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-ha-rhel).

Configure and test fencing as described in [Creating the fencing device](/docs/sap?topic=sap-ha-rhel#ha-rhel-create-fencing-device).

## Preparing ASCS and ERS instances for the cluster integration
{: #ha-rhel-ensa-prepare-ascs-ers}

Use the following steps to prepare the SAP instances for the cluster integration.

### Disabling the automatic start of the SAP instance agents for ASCS and ERS
{: #ha-rhel-ensa-disable-agents}

You must disable the automatic start of the `sapstartsrv` instance agents for both *ASCS* and *ERS* instances after a reboot.

#### Verifying the SAP instance agent integration type
{: #ha-rhel-ensa-verify-sap-services}

Recent versions of the SAP instance agent `sapstartsrv` provide native `systemd` support on Linux.
For more information, refer to the the SAP notes that are listed at [SAP Notes](/docs/sap?topic=sap-ha-rhel-refs#ha-rhel-refs-sap-notes).

On both nodes, check the content of the `/usr/sap/sapservices` file.
```sh
cat /usr/sap/sapservices
```
{: pre}

In the `systemd` format, the lines start with `systemctl` entries.

Example:

```sh
systemctl --no-ask-password start SAPS01_01 # sapstartsrv pf=/usr/sap/S01/SYS/profile/S01_ASCS01_cl-sap-scs
```
{: screen}

If the entries for ASCS and ERS are in `systemd` format, continue with the steps in [Disabling systemd services of the ASCS and the ERS SAP instance](#ha-rhel-ensa-disable-systemd-services).

In the `classic` format, the lines start with `LD_LIBRARY_PATH` entries.

Example:

```sh
LD_LIBRARY_PATH=/usr/sap/S01/ASCS01/exe:$LD_LIBRARY_PATH;export LD_LIBRARY_PATH;/usr/sap/S01/ASCS01/exe/sapstartsrv pf=/usr/sap/S01/SYS/profile/S01_ASCS01_cl-sap-scs -D -u s01adm
```
{: screen}

If the entries for ASCS and ERS are in `classic` format, then modify the `/usr/sap/sapservices` file to prevent the automatic start of the `sapstartsrv` instance agent for both *ASCS* and *ERS* instances after a reboot.

On both nodes, remove or comment out the `sapstartsrv` entries for both *ASCS* and *ERS* in the *SAP services* file.

```sh
sed -i -e 's/^LD_LIBRARY_PATH=/#LD_LIBRARY_PATH=/ sapservices
```
{: pre}

Example:

```sh
#LD_LIBRARY_PATH=/usr/sap/S01/ASCS01/exe:$LD_LIBRARY_PATH;export LD_LIBRARY_PATH;/usr/sap/S01/ASCS01/exe/sapstartsrv pf=/usr/sap/S01/SYS/profile/S01_ASCS01_cl-sap-scs -D -u s01adm
```
{: screen}

Now proceed to [Creating mount points for the instance file systems on the takeover node](#ha-rhel-ensa-create-mountpoints).

#### Disabling systemd services of the ASCS and the ERS SAP instances
{: #ha-rhel-ensa-disable-systemd-services}

On both nodes, disable the instance agent for the ASCS.

```sh
systemctl disable --now SAP${SID}_${ASCS_NO}.service
```
{: pre}

On both nodes, disable the instance agent for the ERS.

```sh
systemctl disable --now SAP${SID}_${ERS_NO}.service
```
{: pre}

#### Disabling `systemd` restart of a crashed ASCS or ERS instance
{: #ha-rhel-ensa-disable-crash-restart}

`Systemd` has its own mechanisms for restarting a crashed service.
In a high availability setup, only the HA cluster is responsible for managing the SAP ASCS and ERS instances.
Create `systemd drop-in files` on both cluster nodes to prevent `systemd` from restarting a crashed SAP instance.

On both nodes, create the directories for the drop-in files.

```sh
mkdir /etc/systemd/system/SAP${SID}_${ASCS_NO}.service.d
```
{: pre}

```sh
mkdir /etc/systemd/system/SAP${SID}_${ERS_NO}.service.d
```
{: pre}

On both nodes, create the drop-in files for ASCS and ERS.

```sh
cat >> /etc/systemd/system/SAP${SID}_${ASCS_NO}.service.d/HA.conf << EOT
[Service]
Restart=no
EOT
```
{: pre}

```sh
cat >> /etc/systemd/system/SAP${SID}_${ERS_NO}.service.d/HA.conf << EOT
[Service]
Restart=no
EOT
```
{: pre}

`Restart=no` must be in the `[Service]` section, and the drop-in files must be available on all cluster nodes.
{: note}

On both nodes, reload the `systemd` unit files.

```sh
systemctl daemon-reload
```
{: pre}

### Creating mount points for the instance file systems on the takeover node
{: #ha-rhel-ensa-create-mountpoints}

Create the mount points for the instance file systems and adjust their ownership.

On NODE1, run the following commands.

```sh
mkdir /usr/sap/${SID}/ERS${ERS_NO}
```
{: pre}

```sh
chown ${sid}adm:sapsys /usr/sap/${SID}/ERS${ERS_NO}
```
{: pre}

On NODE2, run the following commands.

```sh
mkdir /usr/sap/${SID}/ASCS${ASCS_NO}
```
{: pre}

```sh
chown ${sid}adm:sapsys /usr/sap/${SID}/ASCS${ASCS_NO}
```
{: pre}

### Installing permanent SAP license keys
{: #ha-rhel-ensa-install-lic-key}

When the SAP *ASCS* instance is installed on a {{site.data.keyword.powerSys_notm}} instance, the SAP license mechanism relies on the partition UUID.
For more information, see [SAP note 2879336 - Hardware key based on unique ID](https://me.sap.com/notes/2879336){: external}.

On both nodes, run the following command as user `<sid>adm` to identify the `HARDWARE KEY` of the node.

```sh
sudo -i -u ${sid}adm -- sh -c 'saplikey -get'
```
{: pre}

Sample output:

```sh
$ sudo -i -u ${sid}adm -- sh -c 'saplikey -get'

saplikey: HARDWARE KEY = H1428224519
```
{: screen}

Note the `HARDWARE KEY` of each node.

You need both hardware keys to request two different SAP license keys.
Check the following SAP notes for more information about requesting SAP license keys:
- [2879336 - Hardware key based on unique ID](https://me.sap.com/notes/2879336){: external}
- [2662880 - How to request SAP license keys for failover systems](https://me.sap.com/notes/2662880){: external}

### Installing SAP resource agents
{: #ha-rhel-ensa-install-sap-resource-agents}

Install the required software packages.
The `resource-agents-sap` includes the *SAPInstance cluster resource agent* for managing the SAP instances.

Unless `sap_cluster_connector` is configured for the SAP instance, the RHEL HA Add-On cluster considers any state change of the instance as an issue.
If other SAP tools such as `sapcontrol` are used to manage the instance, then `sap_cluster_connector` grants permission to control SAP instances that are running inside the cluster.
If the SAP instances are managed by only cluster tools, the implementation of `sap_cluster_connector`
is not necessary.

Install the packages for the resource agent and the *SAP Cluster Connector* library.
For more information, see [How to enable the SAP HA Interface for SAP ABAP application server instances managed by the RHEL HA Add-On](https://access.redhat.com/solutions/3606101){: external}

On both nodes, run the following commands.

If needed, use `subscription-manager` to enable the SAP NetWeaver repository.
The [RHEL for SAP Subscriptions and Repositories](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/8/html/rhel_for_sap_subscriptions_and_repositories/asmb_enable_repo_rhel-for-sap-subscriptions-and-repositories-8#con_hana_rhel-for-sap-subscriptions-and-repositories-8){: external} documentation describes how to enable the required repositories.

```sh
subscription-manager repos --enable="rhel-8-for-ppc64le-sap-netweaver-e4s-rpms"
```
{: pre}

Install the required packages.

```sh
dnf install -y resource-agents-sap  sap-cluster-connector
```
{: pre}

### Configuring SAP Cluster Connector
{: #ha-rhel-ensa-configure-sap-cluster-connector}

Add user `${sid}adm` to the `haclient` group.

On both nodes, run the following command.

```sh
usermod -a -G haclient ${sid}adm
```
{: pre}

### Adapting the SAP instance profiles
{: #ha-rhel-ensa-modify-sap-profiles}

Modify the start profiles of all SAP instances that are managed by *SAP tools* outside the cluster.
Both *ASCS* and *ERS* instances can be controlled by the RHEL HA Add-On cluster and its resource agents.
Adjust the SAP instance profiles to prevent an automatic restart of instance processes.

On NODE1, navigate to the SAP profile directory.

```sh
cd /sapmnt/${SID}/profile
```
{: pre}

Change all occurrences of `Restart_Program` to `Start_Program` in the instance profile of both *ASCS* and *ERS*.

```sh
sed -i -e 's/Restart_Program_\([0-9][0-9]\)/Start_Program_\1/' ${SID}_ASCS${ASCS_NO}_${ASCS_VH}
```
{: pre}

```sh
sed -i -e 's/Restart_Program_\([0-9][0-9]\)/Start_Program_\1/' ${SID}_ERS${ERS_NO}_${ERS_VH}
```
{: pre}

Add the following two lines at the end of the SAP instance profile to configure `sap_cluster_connector` for the *ASCS* and *ERS* instances.

```sh
service/halib = $(DIR_EXECUTABLE)/saphascriptco.so
service/halib_cluster_connector = /usr/bin/sap_cluster_connector
```
{: screen}

## Configuring ASCS and ERS cluster resources
{: #ha-rhel-ensa-cfg-ascs-ers}

Up to this point, the following are assumed:

- A RHEL HA Add-On cluster is running on both virtual server instances and fencing of the nodes was tested.
- The SAP System is running.
   - SAP *ASCS* is installed and active on node 1 of the cluster.
   - SAP *ERS* is installed and active on node 2 of the cluster.
- All steps in [Prepare ASCS and ERS instances for the cluster integration](#ha-rhel-ensa-prepare-ascs-ers) are complete.

### Configuring resource for sapmnt share
{: #ha-rhel-ensa-cfg-shared}

Create a cloned *Filesystem* cluster resource to mount the *SAPMNT* share from an external NFS server to all cluster nodes.

Make sure that the environment variable `${NFS_VH}` is set to the virtual hostname of your NFS server `${NFS_VH}`, and `${NFS_options}` according to your mount options.

Example mount options:

```sh
export NFS_options="rw,sec=sys"
```
{: codeblock}

Check SAP recommendations for NFS mount options at the [Recommended mount options for read-write directories](https://help.sap.com/docs/SUPPORT_CONTENT/basis/3354611703.html){: external} wiki page.
{: note}

On NODE1, run the following command.

```sh
pcs resource create fs_sapmnt Filesystem \
    device="${NFS_VH}:/${SID}" \
    directory="/sapmnt/${SID}" \
    fstype='nfs' \
    options="${NFS_options}" \
    clone interleave=true
```
{: pre}

### Configuring ASCS resource group
{: #ha-rhel-ensa-cfg-ascs-rg}

Create a resource for the virtual IP address of the ASCS.

On NODE1, run the following command.

```sh
pcs resource create ${sid}_vip_ascs${ASCS_NO} IPaddr2 \
    ip=${ASCS_IP} \
    --group ${sid}_ascs${ASCS_NO}_group
```
{: pre}

In this example of creating resources for an HA-LVM file system on a shared storage volume, you create resources for LVM-activate and for the instance file system of the ASCS.

```sh
pcs resource create ${sid}_fs_ascs${ASCS_NO}_lvm LVM-activate \
    vgname="${ASCS_VG}" \
    vg_access_mode=system_id \
    --group ${sid}_ascs${ASCS_NO}_group
```
{: pre}

```sh
pcs resource create ${sid}_fs_ascs${ASCS_NO} Filesystem \
    device="/dev/mapper/${ASCS_VG}-${ASCS_LV}" \
    directory=/usr/sap/${SID}/ASCS${ASCS_NO} \
    fstype=xfs \
    --group ${sid}_ascs${ASCS_NO}_group
```
{: pre}

In the alternative example that the instance file system of the *ASCS* is provided by an HA NFS server, only the file system resource is required.
Make sure that you defined the environment variable `${NFS_VH}` according to your *NFS server*, and that you created a directory `${SID}/ASCS` under the NFS root directory during the SAP installation of the *ASCS* instance.

```sh
pcs resource create ${sid}_fs_ascs${ASCS_NO} Filesystem \
    device="${NFS_VH}:${SID}/ASCS" \
    directory=/usr/sap/${SID}/ASCS${ASCS_NO} \
    fstype=nfs \
    options="${NFS_options}" \
    force_unmount=safe \
    op start interval=0 timeout=60 \
    op stop interval=0 timeout=120 \
    --group ${sid}_ascs${ASCS_NO}_group
```
{: pre}

Create a resource for managing the *ASCS* instance.

```sh
pcs resource create ${sid}_ascs${ASCS_NO} SAPInstance \
    InstanceName="${SID}_ASCS${ASCS_NO}_${ASCS_VH}" \
    START_PROFILE=/sapmnt/${SID}/profile/${SID}_ASCS${ASCS_NO}_${ASCS_VH} \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 \
    op stop interval=0 timeout=600 \
    --group ${sid}_ascs${ASCS_NO}_group
```
{: pre}

The `meta resource-stickiness=5000` option is used to balance the failover constraint with ERS so that the resource stays on the node where it started and doesn't migrate uncontrollably in the cluster.
{: note}

Add a resource stickiness to the group to make sure that the *ASCS* remains on the node.

```sh
pcs resource meta ${sid}_ascs${ASCS_NO}_group \
    resource-stickiness=3000
```
{: pre}

### Configuring the ERS resource group
{: #ha-rhel-ensa-cfg-ers-rg}

Create a resource for the virtual IP address of the *ERS*.

On NODE1, run the following command.

```sh
pcs resource create ${sid}_vip_ers${ERS_NO} IPaddr2 \
    ip=${ERS_IP} \
    --group ${sid}_ers${ERS_NO}_group
```
{: pre}

In the example of creating resources for an HA-LVM file system on a shared storage volume, you create resources for LVM-activate and for the instance file system of the *ERS*.

```sh
pcs resource create ${sid}_fs_ers${ERS_NO}_lvm LVM-activate \
    vgname="${ERS_VG}" \
    vg_access_mode=system_id \
    --group ${sid}_ers${ERS_NO}_group
```
{: pre}

```sh
pcs resource create ${sid}_fs_ers${ERS_NO} Filesystem \
    device="/dev/mapper/${ERS_VG}-${ERS_LV}" \
    directory=/usr/sap/${SID}/ERS${ERS_NO} \
    fstype=xfs \
    --group ${sid}_ers${ERS_NO}_group
```
{: pre}

In the alternative example that the instance file system of the *ERS* is provided by an HA NFS server, only the file system resource is required.
Make sure that you defined the environment variable `${NFS_VH}` according to your *NFS server*, and that you created a directory `${SID}/ERS` under the NFS root directory during the SAP installation of the *ERS* instance.

```sh
pcs resource create ${sid}_fs_ers${ERS_NO} Filesystem \
    device="${NFS_VH}:${SID}/ERS" \
    directory=/usr/sap/${SID}/ERS${ERS_NO} \
    fstype=nfs \
    options="${NFS_options}" \
    force_unmount=safe \
    op start interval=0 timeout=60 \
    op stop interval=0 timeout=120 \
    --group ${sid}_ers${ERS_NO}_group
```
{: pre}

Create a resource for managing the *ERS* instance.

```sh
pcs resource create ${sid}_ers${ERS_NO} SAPInstance \
    InstanceName="${SID}_ERS${ERS_NO}_${ERS_VH}" \
    START_PROFILE=/sapmnt/${SID}/profile/${SID}_ERS${ERS_NO}_${ERS_VH} \
    AUTOMATIC_RECOVER=false \
    IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 \
    op stop interval=0 timeout=600 \
    --group ${sid}_ers${ERS_NO}_group
```
{: pre}

### Configuring cluster resource constraints
{: #ha-rhel-ensa-cfg-constraints}

A colocation constraint prevents resource groups `${sid}_ascs${ASCS_NO}_group` and `${sid}_ers${ERS_NO}_group` from being active on the same node whenever possible.
The stickiness score of `-5000` makes sure that they run on the same node if only a single node is available.

```sh
pcs constraint colocation add \
    ${sid}_ers${ERS_NO}_group with ${sid}_ascs${ASCS_NO}_group -5000
```
{: pre}

An order constraint controls that resource group `${sid}_ascs${ASCS_NO}_group` starts before `${sid}_ers${ERS_NO}_group`.

```sh
pcs constraint order start \
    ${sid}_ascs${ASCS_NO}_group then stop ${sid}_ers${ERS_NO}_group \
    symmetrical=false \
    kind=Optional
```
{: pre}

The following two order constraints make sure that the file system *SAPMNT* mounts before resource groups `${sid}_ascs${ASCS_NO}_group` and `${sid}_ers${ERS_NO}_group` start.

```sh
pcs constraint order fs_sapmnt-clone then ${sid}_ascs${ASCS_NO}_group
```
{: pre}

```sh
pcs constraint order fs_sapmnt-clone then ${sid}_ers${ERS_NO}_group
```
{: pre}

The cluster setup is complete.

## Testing an SAP ENSA2 cluster
{: #ha-rhel-ensa-test-sap-ensa-cluster}

It is vital to thoroughly test the cluster configuration to make sure that the cluster is working correctly.
The following information provides a few sample failover test scenarios, but is not a complete list of test scenarios.

For example, the description of each test case includes the following information.

- Component under test
- Description of the test
- Prerequisites and the initial state before failover test
- Test procedure
- Expected behavior and results
- Recovery procedure

### Test 1 - Testing a failure of the ASCS instance
{: #ha-rhel-ensa-test-sap-central-services-failure}

#### Test 1 - Description
{: #ha-rhel-ensa-test1-description}

Simulate a crash of the SAP *ASCS* instance that is running on NODE1.

#### Test 1 - Prerequisites
{: #ha-rhel-ensa-test1-prerequisites}

- A functional two-node RHEL HA Add-On cluster for SAP ENSA2.
- Both cluster nodes are active.
- Cluster is started on NODE1 and NODE2.
   - Resource group ${sid}_ascs${ASCS_NO}_group is active on NODE1.
   - Resources ${sid}_vip_ascs${ASCS_NO}, ${sid}_fs_ascs${ASCS_nr}_lvm, ${sid}_fs_ascs${ASCS_nr} and ${sid}_ascs${ASCS_nr} are `Started` on NODE1.
   - Resource group ${sid}_ers${ERS_NO}_group is active on NODE2.
   - Resources ${sid}_vip_ers${ERS_NO}, ${sid}_fs_ers${ERS_NO}_lvm, ${sid}_fs_ers${ERS_NO} and ${sid}_ers${ERS_NO} are `Started` on NODE2.
- Check SAP instance processes:
   - *ASCS* instance is running on NODE1.
   - *ERS* instance is running on NODE2.

```sh
pcs status
```
{: pre}

Sample output:

```sh
# pcs status
Cluster name: SAP_ASCS
Cluster Summary:
  * Stack: corosync
  * Current DC: cl-sap-1 (version 2.0.5-9.el8_4.5-ba59be7122) - partition with quorum
  * Last updated: Tue Feb 14 07:59:16 2023
  * Last change:  Tue Feb 14 05:02:22 2023 by hacluster via crmd on cl-sap-1
  * 2 nodes configured
  * 11 resource instances configured

Node List:
  * Online: [ cl-sap-1 cl-sap-2 ]

Full List of Resources:
  * res_fence_ibm_powervs	(stonith:fence_ibm_powervs):	 Started cl-sap-2
  * Resource Group: s01_ascs01_group:
    * s01_vip_ascs01	(ocf::heartbeat:IPaddr2):	 Started cl-sap-1
    * s01_fs_ascs01_lvm	(ocf::heartbeat:LVM-activate):	 Started cl-sap-1
    * s01_fs_ascs01	(ocf::heartbeat:Filesystem):	 Started cl-sap-1
    * s01_ascs01	(ocf::heartbeat:SAPInstance):	 Started cl-sap-1
  * Resource Group: s01_ers02_group:
    * s01_vip_ers02	(ocf::heartbeat:IPaddr2):	 Started cl-sap-2
    * s01_fs_ers02_lvm	(ocf::heartbeat:LVM-activate):	 Started cl-sap-2
    * s01_fs_ers02	(ocf::heartbeat:Filesystem):	 Started cl-sap-2
    * s01_ers02	(ocf::heartbeat:SAPInstance):	 Started cl-sap-2
  * Clone Set: fs_sapmnt-clone [fs_sapmnt]:
    * Started: [ cl-sap-1 cl-sap-2 ]

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```
{: screen}

#### Test 1 - Test Procedure
{: #ha-rhel-ensa-test1-procedure}

To crash the SAP *ASCS* instance, send a SIGKILL signal to the enque server as user `${sid}adm`.

On NODE1, identify the PID of the enque server.

```sh
pgrep -af "(en|enq).sap"
```
{: pre}

Send a SIGKILL signal to the identified process.

Sample output:

```sh
# pgrep -af "(en|enq).sap"
30186 en.sapS01_ASCS01 pf=/usr/sap/S01/SYS/profile/S01_ASCS01_cl-sap-scs
# kill -9 30186
```
{: screen}

#### Test 1 - Expected behavior
{: #ha-rhel-ensa-test1-expected-behavior}

- SAP *ASCS* instance on NODE1 crashes.
- The cluster detects the crashed *ASCS* instance.
- The cluster stops the dependent resources on NODE1 (virtual IP address, file system `/usr/sap/${SID}/ASCS${ASCS_NO}`, and the LVM resources), and acquires them on NODE2.
- The cluster starts the *ASCS* on NODE2.
- The cluster stops the *ERS* instance on NODE2.
- The cluster stops the dependent resources on NODE1 (virtual IP address, file system `/usr/sap/${SID}/ERS${ERS_NO}`, and the LVM resources), and acquires them on NODE1.
- The cluster starts the *ERS* on NODE1.

After a few seconds, check the status with the following command.

```sh
pcs status
```
{: pre}

Sample output:

```sh
# pcs status
Cluster name: SAP_ASCS
Cluster Summary:
  * Stack: corosync
  * Current DC: cl-sap-1 (version 2.0.5-9.el8_4.5-ba59be7122) - partition with quorum
  * Last updated: Tue Feb 14 08:10:18 2023
  * Last change:  Tue Feb 14 05:02:22 2023 by hacluster via crmd on cl-sap-1
  * 2 nodes configured
  * 11 resource instances configured

Node List:
  * Online: [ cl-sap-1 cl-sap-2 ]

Full List of Resources:
  * res_fence_ibm_powervs	(stonith:fence_ibm_powervs):	 Started cl-sap-2
  * Resource Group: s01_ascs01_group:
    * s01_vip_ascs01	(ocf::heartbeat:IPaddr2):	 Started cl-sap-2
    * s01_fs_ascs01_lvm	(ocf::heartbeat:LVM-activate):	 Started cl-sap-2
    * s01_fs_ascs01	(ocf::heartbeat:Filesystem):	 Started cl-sap-2
    * s01_ascs01	(ocf::heartbeat:SAPInstance):	 Started cl-sap-2
  * Resource Group: s01_ers02_group:
    * s01_vip_ers02	(ocf::heartbeat:IPaddr2):	 Started cl-sap-1
    * s01_fs_ers02_lvm	(ocf::heartbeat:LVM-activate):	 Started cl-sap-1
    * s01_fs_ers02	(ocf::heartbeat:Filesystem):	 Started cl-sap-1
    * s01_ers02	(ocf::heartbeat:SAPInstance):	 Started cl-sap-1
  * Clone Set: fs_sapmnt-clone [fs_sapmnt]:
    * Started: [ cl-sap-1 cl-sap-2 ]

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```
{: screen}

### Test 2 - Testing a failure of the node that is running the ASCS instance
{: #ha-rhel-ensa-test-ascs-instance-server-failure}

Use the folling information to test a failure of the node that is running the ASCS instance.

#### Test 2 - Description
{: #ha-rhel-ensa-test2-description}

Simulate a crash of the node where the *ASCS* instance is running.

#### Test 2 - Prerequisites
{: #ha-rhel-ensa-test2-prerequisites}

- A functional two-node RHEL HA Add-On cluster for SAP ENSA2.
- Both cluster nodes are active.
- Cluster is started on NODE1 and NODE2.
   - Resource group ${sid}_ascs${ASCS_NO}_group is active on NODE2.
   - Resources ${sid}_vip_ascs${ASCS_NO}, ${sid}_fs_ascs${ASCS_nr}_lvm, ${sid}_fs_ascs${ASCS_nr} and ${sid}_ascs${ASCS_nr} are `Started` on NODE2.
   - Resource group ${sid}_ers${ERS_NO}_group is active on NODE1.
   - Resources ${sid}_vip_ers${ERS_NO}, ${sid}_fs_ers${ERS_NO}_lvm, ${sid}_fs_ers${ERS_NO} and ${sid}_ers${ERS_NO} are `Started` on NODE1.
- Check SAP instance processes:
   - *ASCS* instance is running on NODE2.
   - *ERS* instance is running on NODE1.

#### Test 2 - Test procedure
{: #ha-rhel-ensa-test2-procedure}

Crash NODE2 by sending a `fast-restart` system request.

On NODE2, run the following command.

```sh
sync; echo b > /proc/sysrq-trigger
```
{: pre}

#### Test 2 - Expected behavior
{: #ha-rhel-ensa-test2-expected-behavior}

- NODE2 restarts.
- The cluster detects the failed node and sets its state to offline (UNCLEAN).
- The cluster acquires the *ASCS* resources (virtual IP address, file system `/usr/sap/${SID}/ASCS${ASCS_NO}`, and the LVM items) on NODE1.
- The cluster starts the *ASCS* on NODE1.
- The cluster stops the *ERS* instance on NODE1.
- The cluster stops the dependent resources on NODE1 (virtual IP address, file system `/usr/sap/${SID}/ERS${ERS_NO}`, and the LVM resources), and releases them.

After a while, check the status with the following command.

The second node is offline and both resource groups are running on the first node.
{: note}

```sh
pcs status
```
{: pre}

Sample output:

```sh
# pcs status
Cluster name: SAP_ASCS
Cluster Summary:
  * Stack: corosync
  * Current DC: cl-sap-1 (version 2.0.5-9.el8_4.5-ba59be7122) - partition with quorum
  * Last updated: Tue Feb 14 08:34:16 2023
  * Last change:  Tue Feb 14 08:34:04 2023 by hacluster via crmd on cl-sap-1
  * 2 nodes configured
  * 11 resource instances configured

Node List:
  * Online: [ cl-sap-1 ]
  * OFFLINE: [ cl-sap-2 ]

Full List of Resources:
  * res_fence_ibm_powervs	(stonith:fence_ibm_powervs):	 Started cl-sap-1
  * Resource Group: s01_ascs01_group:
    * s01_vip_ascs01	(ocf::heartbeat:IPaddr2):	 Started cl-sap-1
    * s01_fs_ascs01_lvm	(ocf::heartbeat:LVM-activate):	 Started cl-sap-1
    * s01_fs_ascs01	(ocf::heartbeat:Filesystem):	 Started cl-sap-1
    * s01_ascs01	(ocf::heartbeat:SAPInstance):	 Started cl-sap-1
  * Resource Group: s01_ers02_group:
    * s01_vip_ers02	(ocf::heartbeat:IPaddr2):	 Started cl-sap-1
    * s01_fs_ers02_lvm	(ocf::heartbeat:LVM-activate):	 Started cl-sap-1
    * s01_fs_ers02	(ocf::heartbeat:Filesystem):	 Started cl-sap-1
    * s01_ers02	(ocf::heartbeat:SAPInstance):	 Started cl-sap-1
  * Clone Set: fs_sapmnt-clone [fs_sapmnt]:
    * Started: [ cl-sap-1 ]
    * Stopped: [ cl-sap-2 ]

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```
{: screen}

#### Test 2 - Recovery procedure
{: #ha-rhel-ensa-test2-recovery-procedure}

Wait until NODE2 restarts, then restart the cluster framework.

On NODE1, run the following command.

```sh
pcs cluster start
```
{: pre}

- The cluster starts on NODE2 and acquires the *ERS* resources (virtual IP address, file system `/usr/sap/${SID}/ERS${ERS_NO}`, and the LVM resources) on NODE2.
- The cluster starts the *ERS* instance on NODE2.

Wait a moment and check the status with the following command.
The *ERS* resource group moved to the second node.

```sh
pcs status
```

Sample output:

```sh
# pcs status
Cluster name: SAP_ASCS
Cluster Summary:
  * Stack: corosync
  * Current DC: cl-sap-1 (version 2.0.5-9.el8_4.5-ba59be7122) - partition with quorum
  * Last updated: Tue Feb 14 08:41:23 2023
  * Last change:  Tue Feb 14 08:34:04 2023 by hacluster via crmd on cl-sap-1
  * 2 nodes configured
  * 11 resource instances configured

Node List:
  * Online: [ cl-sap-1 cl-sap-2 ]

Full List of Resources:
  * res_fence_ibm_powervs	(stonith:fence_ibm_powervs):	 Started cl-sap-1
  * Resource Group: s01_ascs01_group:
    * s01_vip_ascs01	(ocf::heartbeat:IPaddr2):	 Started cl-sap-1
    * s01_fs_ascs01_lvm	(ocf::heartbeat:LVM-activate):	 Started cl-sap-1
    * s01_fs_ascs01	(ocf::heartbeat:Filesystem):	 Started cl-sap-1
    * s01_ascs01	(ocf::heartbeat:SAPInstance):	 Started cl-sap-1
  * Resource Group: s01_ers02_group:
    * s01_vip_ers02	(ocf::heartbeat:IPaddr2):	 Started cl-sap-2
    * s01_fs_ers02_lvm	(ocf::heartbeat:LVM-activate):	 Started cl-sap-2
    * s01_fs_ers02	(ocf::heartbeat:Filesystem):	 Started cl-sap-2
    * s01_ers02	(ocf::heartbeat:SAPInstance):	 Started cl-sap-2
  * Clone Set: fs_sapmnt-clone [fs_sapmnt]:
    * Started: [ cl-sap-1 cl-sap-2 ]

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```
{: screen}

### Test 3 - Testing a failure of the *ERS* instance
{: #test-ers-instance-failure}

Use the following information to test the failure of an ERS instance.

#### Test 3 - Description
{: #ha-rhel-ensa-test3-description}

Simulate a crash of the *ERS* instance.

#### Test 3 - Prerequisites
{: #ha-rhel-ensa-test3-prerequisites}

- A functional two-node RHEL HA Add-On cluster for SAP ENSA2.
- Both cluster nodes are active.
- Cluster starts on NODE1 and NODE2.
   - Resource group ${sid}_ascs${ASCS_NO}_group is active on NODE1.
   - Resources ${sid}_vip_ascs${ASCS_NO}, ${sid}_fs_ascs${ASCS_nr}_lvm, ${sid}_fs_ascs${ASCS_nr} and ${sid}_ascs${ASCS_nr} are `Started` on NODE1.
   - Resource group ${sid}_ers${ERS_NO}_group is active on NODE2.
   - Resources ${sid}_vip_ers${ERS_NO}, ${sid}_fs_ers${ERS_NO}_lvm, ${sid}_fs_ers${ERS_NO} and ${sid}_ers${ERS_NO} are `Started` on NODE2.
- Check SAP instance processes:
   - *ASCS* instance is running on NODE1.
   - *ERS* instance is running on NODE2.

#### Test 3 - Test Procedure
{: #ha-rhel-ensa-test3-procedure}

Crash the SAP *ERS* instance by sending a SIGKILL signal.

On NODE2, identify the PID of the enque replication server.

```sh
pgrep -af "(er|enqr).sap"
```
{: pre}

Send a SIGKILL signal to the identified process.

Sample output:

```sh
# pgrep -af "(er|enqr).sap"
2527198 er.sapS01_ERS02 pf=/usr/sap/S01/ERS02/profile/S01_ERS02_cl-sap-ers NR=01
# kill -9 2527198
```
{: screen}

#### Test 3 - Expected behavior
{: #ha-rhel-ensa-test3-expected-behavior}

- SAP Enqueue Replication Server on NODE2 crashes immediately.
- The cluster detects the stopped *ERS* and marks the resource as failed.
- The cluster restarts the *ERS* on NODE2.

Check the status with the following command.

```sh
pcs status
```
{: pre}

The `${sid}_ers${ERS_NO}` *ERS* resource restarted on the second node.
If you run the `pcs status` command too soon, you might see the *ERS* resource briefly in status `FAILED`.

Sample output:

```sh
# pcs status
Cluster name: SAP_ASCS
Cluster Summary:
  * Stack: corosync
  * Current DC: cl-sap-1 (version 2.0.5-9.el8_4.5-ba59be7122) - partition with quorum
  * Last updated: Tue Feb 14 08:50:53 2023
  * Last change:  Tue Feb 14 08:50:50 2023 by hacluster via crmd on cl-sap-2
  * 2 nodes configured
  * 11 resource instances configured

Node List:
  * Online: [ cl-sap-1 cl-sap-2 ]

Full List of Resources:
  * res_fence_ibm_powervs	(stonith:fence_ibm_powervs):	 Started cl-sap-1
  * Resource Group: s01_ascs01_group:
    * s01_vip_ascs01	(ocf::heartbeat:IPaddr2):	 Started cl-sap-1
    * s01_fs_ascs01_lvm	(ocf::heartbeat:LVM-activate):	 Started cl-sap-1
    * s01_fs_ascs01	(ocf::heartbeat:Filesystem):	 Started cl-sap-1
    * s01_ascs01	(ocf::heartbeat:SAPInstance):	 Started cl-sap-1
  * Resource Group: s01_ers02_group:
    * s01_vip_ers02	(ocf::heartbeat:IPaddr2):	 Started cl-sap-2
    * s01_fs_ers02_lvm	(ocf::heartbeat:LVM-activate):	 Started cl-sap-2
    * s01_fs_ers02	(ocf::heartbeat:Filesystem):	 Started cl-sap-2
    * s01_ers02	(ocf::heartbeat:SAPInstance):	 Started cl-sap-2
  * Clone Set: fs_sapmnt-clone [fs_sapmnt]:
    * Started: [ cl-sap-1 cl-sap-2 ]

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```
{: screen}

#### Test 3 - Recovery Procedure
{: #ha-rhel-ensa-test3-recovery-procedure}

On NODE2, run the following commands.

```sh
pcs resource refresh
```
{: pre}

```sh
pcs status --full
```
{: pre}

### Test 4 - Testing a manual move of the ASCS instance
{: #ha-rhel-ensa-test-manual-move}

Use the following information to test a manual move of an ASCS instance.

#### Test 4 - Description
{: #ha-rhel-ensa-test4-description}

Use *SAP Control* commands to move the *ASCS* instance to the other node for maintenance purposes.

#### Test 4 - Prerequisites
{: #ha-rhel-ensa-test4-prerequisites}

- A functional two-node RHEL HA Add-On cluster for SAP ENSA2.
- The `sap_cluster_connector` is installed and configured.
- Both cluster nodes are active.
- Cluster is started on NODE1 and NODE2.
   - Resource group ${sid}_ascs${ASCS_NO}_group is active on NODE1.
   - Resources ${sid}_vip_ascs${ASCS_NO}, ${sid}_fs_ascs${ASCS_nr}_lvm, ${sid}_fs_ascs${ASCS_nr} and ${sid}_ascs${ASCS_nr} are `Started` on NODE1.
   - Resource group ${sid}_ers${ERS_NO}_group is active on NODE2.
   - Resources ${sid}_vip_ers${ERS_NO}, ${sid}_fs_ers${ERS_NO}_lvm, ${sid}_fs_ers${ERS_NO} and ${sid}_ers${ERS_NO} are `Started` on NODE2.
- Check SAP instance processes:
   - *ASCS* instance is running on NODE1.
   - *ERS* instance is running on NODE2.

#### Test 4 - Test Procedure
{: #ha-rhel-ensa-test4-procedure}

Log in to NODE1 and run `sapcontrol` to move the *ASCS* instance to the other node.

```sh
sudo -i -u ${sid}adm -- sh -c "sapcontrol -nr ${ASCS_NO} -function HAFailoverToNode"
```
{: pre}

#### Test 4 - Expected behavior
{: #ha-rhel-ensa-test4-expected-behavior}

- `sapcontrol` interacts with the cluster through the `sap-cluster-connector`.
- The cluster creates location constraints to move the resource.

Check the status with the following command.
Keep in mind that the *ASCS* resource group moved to the second node.
If you run the `pcs status` command too soon, you might see some resources `stopping` and `starting`.

```sh
pcs status
```
{: pre}

Sample output:

```sh
# pcs status
Cluster name: SAP_ASCS
Cluster Summary:
  * Stack: corosync
  * Current DC: cl-sap-1 (version 2.0.5-9.el8_4.5-ba59be7122) - partition with quorum
  * Last updated: Tue Feb 14 09:03:19 2023
  * Last change:  Tue Feb 14 09:01:40 2023 by s01adm via crm_resource on cl-sap-1
  * 2 nodes configured
  * 11 resource instances configured

Node List:
  * Online: [ cl-sap-1 cl-sap-2 ]

Full List of Resources:
  * res_fence_ibm_powervs	(stonith:fence_ibm_powervs):	 Started cl-sap-1
  * Resource Group: s01_ascs01_group:
    * s01_vip_ascs01	(ocf::heartbeat:IPaddr2):	 Started cl-sap-2
    * s01_fs_ascs01_lvm	(ocf::heartbeat:LVM-activate):	 Started cl-sap-2
    * s01_fs_ascs01	(ocf::heartbeat:Filesystem):	 Started cl-sap-2
    * s01_ascs01	(ocf::heartbeat:SAPInstance):	 Started cl-sap-2
  * Resource Group: s01_ers02_group:
    * s01_vip_ers02	(ocf::heartbeat:IPaddr2):	 Started cl-sap-1
    * s01_fs_ers02_lvm	(ocf::heartbeat:LVM-activate):	 Started cl-sap-1
    * s01_fs_ers02	(ocf::heartbeat:Filesystem):	 Started cl-sap-1
    * s01_ers02	(ocf::heartbeat:SAPInstance):	 Started cl-sap-1
  * Clone Set: fs_sapmnt-clone [fs_sapmnt]:
    * Started: [ cl-sap-1 cl-sap-2 ]

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```
{: screen}

#### Test 4 - Recovery Procedure
{: #ha-rhel-ensa-test4-recovery-procedure}

Wait until the *ASCS* instance is active on NODE2.
After five minutes, the cluster removes the created location constraints automatically.

The following instructions show how to remove the constraints manually.

On NODE2, run the following command.

```sh
pcs constraint
```
{: pre}

Sample output:

```sh
# pcs constraint
Location Constraints:
  Resource: s01_ascs01_group
    Constraint: cli-ban-s01_ascs01_group-on-cl-sap-1
      Rule: boolean-op=and score=-INFINITY
        Expression: #uname eq string cl-sap-1
        Expression: date lt 2023-02-08 09:33:50 -05:00
Ordering Constraints:
  start s01_ascs01_group then stop s01_ers02_group (kind:Optional) (non-symmetrical)
  start fs_sapmnt-clone then start s01_ascs01_group (kind:Mandatory)
  start fs_sapmnt-clone then start s01_ers02_group (kind:Mandatory)
Colocation Constraints:
  s01_ers02_group with s01_ascs01_group (score:-5000)
Ticket Constraints:
```
{: screen}

```sh
pcs resource clear ${sid}_ascs${ASCS_NO}_group
```
{: pre}

The *Location constraints* are removed:

```sh
pcs constraint
```
{: pre}

Sample output:

```sh
# pcs constraint
Location Constraints:
Ordering Constraints:
  start s01_ascs01_group then stop s01_ers02_group (kind:Optional) (non-symmetrical)
  start fs_sapmnt-clone then start s01_ascs01_group (kind:Mandatory)
  start fs_sapmnt-clone then start s01_ers02_group (kind:Mandatory)
Colocation Constraints:
  s01_ers02_group with s01_ascs01_group (score:-5000)
Ticket Constraints:
```
{: screen}
