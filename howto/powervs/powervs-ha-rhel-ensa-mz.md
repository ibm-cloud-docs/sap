---
copyright:
  years: 2023, 2025
lastupdated: "2025-09-18"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Configuring high availability for SAP S/4HANA (ASCS and ERS) in a Red Hat Enterprise Linux High Availability Add-On cluster in a multizone region environment
{: #ha-rhel-ensa-mz}

The following information describes the configuration of *ABAP SAP Central Services (ASCS)* and *Enqueue Replication Server (ERS)* in a Red Hat Enterprise Linux (RHEL) High Availability Add-On cluster.
The cluster uses virtual server instances in [{{site.data.keyword.powerSysFull}}](https://www.ibm.com/products/power-virtual-server){: external} as cluster nodes.
{: shortdesc}

This example configuration applies to the second generation of the [Standalone Enqueue Server](https://help.sap.com/docs/ABAP_PLATFORM/cff8531bc1d9416d91bb6781e628d4e0/902412f09e134f5bb875adb6db585c92.html){: external}, also called *ENSA2*.

Starting with the release of SAP S/4HANA 1809, ENSA2 is installed by default, and can be configured in a two-node or multi-node cluster.
This example uses the *ENSA2* setup for a two-node RHEL HA Add-On cluster.
If the *ASCS* service fails in a two-node cluster, it restarts on the node where the *ERS* instance is running.
The lock entries for the SAP application are then restored from the copy of the lock table in the *ERS* instance.
When an administrator activates the failed cluster node, the *ERS* instance moves to the other node (anti-collocation) to protect its copy of the lock table.

Install the SAP database instance and other SAP application server instances on virtual server instances outside the two-node cluster that is used for the *ASCS* and *ERS* instances.

## Before you begin
{: #ha-rhel-ensa-mz-begin}

Review the general requirements, product documentation, support articles, and SAP notes listed in [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

## Prerequisites
{: #ha-rhel-ensa-mz-prerequisites}

- This information describes a setup that uses NFS-mounted storage for the instance directories.
   - The *ASCS* instance uses the mount point `/usr/sap/<SID>/ASCS<INSTNO>`.
   - The *ERS* instance uses the mount point `/usr/sap/<SID>/ERS<INSTNO>`.
   - Both instances use the `/sapmnt/<SID>` mount point with shared *read and write* access.
   - Other shared file systems such as *saptrans* `/usr/sap/trans` might be needed.

   Make sure that a highly available NFS server is configured to serve these shares.
   The NFS server must not be installed on a virtual server that is part of the *ENSA2* cluster.
   This document does not describe the steps for setting up file storage or creating cluster file systems.
   {: important}

- The virtual hostnames for the *ASCS* and *ERS* instances must meet the requirements as documented in [Hostnames of SAP ABAP Platform servers](https://me.sap.com/notes/611361){: external}.

- The subnets and the virtual IP addresses for the *ASCS* and *ERS* instances must not exist in the {{site.data.keyword.powerSys_notm}} workspaces.
   They are configured as cluster resources.
   However, you must add the virtual IP addresses and virtual hostnames of the *ASCS* and *ERS* instances to the Domain Name Service (DNS), and to the `/etc/hosts` file on all cluster nodes.

## Preparing nodes to install ASCS and ERS instances
{: #ha-rhel-ensa-mz-prepare-nodes}

The following information describes how to prepare the nodes for installing the SAP *ASCS* and *ERS* instances.

### Preparing environment variables
{: #ha-rhel-ensa-mz-prepare-environment-variables}

{{../../_include-segments/powervs-ha-rhel-ensa-mz-prepare-env-vars.md}}

### Creating mount points for the instance file systems
{: #ha-rhel-ensa-mz-create-mountpoints}

On both nodes, run the following command to create the mount points for the instance file systems.

```sh
mkdir -p /usr/sap/${SID}/{ASCS${ASCS_INSTNO},ERS${ERS_INSTNO}} /sapmnt/${SID}
```
{: pre}

## Installing and setting up the RHEL HA Add-On cluster
{: #ha-rhel-ensa-mz-set-up}

Follow the instructions in [Implementing a RHEL HA Add-On cluster on IBM {{site.data.keyword.powerSys_notm}} in a Multizone Region Environment](/docs/sap?topic=sap-ha-rhel-mz) to install and configure the RHEL HA Add-On cluster.
After the installation, configure and test cluster fencing as described in [Creating the fencing device](/docs/sap?topic=sap-ha-rhel-mz#ha-rhel-mz-create-fencing-device).

## Preparing cluster resources before the SAP installation
{: #ha-rhel-ensa-mz-clres-ascs-ers}

Ensure that the RHEL HA Add-On cluster is active on both virtual server instances, and verify that node fencing functions as expected.

### Configuring general cluster properties
{: #ha-rhel-ensa-mz-configure-cluster-properties}

To prevent the cluster from moving healthy resources to another node (for example, if you start the cluster on a previously failed node), you can set the default value for the `resource-stickiness` meta attribute to 1, and the default value for the `migration-threshold` meta attribute to 3.

On NODE1, run the following command.

```sh
pcs resource defaults update resource-stickiness=1
```
{: pre}

```sh
pcs resource defaults update migration-threshold=3
```
{: pre}

### Configuring the cluster resource for sapmnt
{: #ha-rhel-ensa-mz-cfg-shared}

On NODE1, run the following command to create a cloned *Filesystem* cluster resource that mounts *SAPMNT* from an NFS server on all cluster nodes.

```sh
pcs resource create fs_sapmnt Filesystem \
    device="${NFS_SERVER}:${NFS_SHARE}/sapmnt" \
    directory="/sapmnt/${SID}" \
    fstype='nfs' \
    options="${NFS_OPTIONS}" \
    clone interleave=true
```
{: pre}

### Preparing to install the ASCS instance on NODE1
{: #ha-rhel-ensa-mz-cfg-ascs-rg-prp}

On NODE1, run the following command to create a *Filesystem* cluster resource that mounts the *ASCS* instance directory.

```sh
pcs resource create ${sid}_fs_ascs${ASCS_INSTNO} Filesystem \
    device="${NFS_SERVER}:${NFS_SHARE}/ASCS" \
    directory=/usr/sap/${SID}/ASCS${ASCS_INSTNO} \
    fstype=nfs \
    options="${NFS_OPTIONS}" \
    force_unmount=safe \
    op start interval=0 timeout=60 \
    op stop interval=0 timeout=120 \
    --group ${sid}_ascs${ASCS_INSTNO}_group
```
{: pre}

{{../../_include-segments/powervs-ha-rhel-ensa-mz-create-ascs-vip.md}}

### Preparing to install the ERS instance on NODE2
{: #ha-rhel-ensa-mz-cfg-ers-rg-prp}

On NODE1, run the following command to create a *Filesystem* cluster resource to mount the *ERS* instance directory.

```sh
pcs resource create ${sid}_fs_ers${ERS_INSTNO} Filesystem \
    device="${NFS_SERVER}:${NFS_SHARE}/ERS" \
    directory=/usr/sap/${SID}/ERS${ERS_INSTNO} \
    fstype=nfs \
    options="${NFS_OPTIONS}" \
    force_unmount=safe \
    op start interval=0 timeout=60 \
    op stop interval=0 timeout=120 \
    --group ${sid}_ers${ERS_INSTNO}_group
```
{: pre}

{{../../_include-segments/powervs-ha-rhel-ensa-mz-create-ers-vip.md}}

### Verifying the cluster configuration
{: #ha-rhel-ensa-mz-verify-cluster-config-before-sap-install}

On NODE1, run the following command to verify the cluster configuration at this stage.

```sh
pcs status --full
```
{: pre}

Sample output:

```sh
# pcs status --full
Cluster name: SAP_S01
Status of pacemakerd: 'Pacemaker is running' (last updated 2024-11-20 14:04:05 +01:00)
Cluster Summary:
  * Stack: corosync
  * Current DC: cl-s01-2 (2) (version 2.1.5-9.el9_2.4-a3f44794f94) - partition with quorum
  * Last updated: Wed Nov 20 14:04:06 2024
  * Last change:  Wed Nov 20 13:51:19 2024 by hacluster via crmd on cl-s01-2
  * 2 nodes configured
  * 8 resource instances configured

Node List:
  * Node cl-s01-1 (1): online, feature set 3.16.2
  * Node cl-s01-2 (2): online, feature set 3.16.2

Full List of Resources:
  * fence_node1	(stonith:fence_ibm_powervs):	 Started cl-s01-2
  * fence_node2	(stonith:fence_ibm_powervs):	 Started cl-s01-2
  * Clone Set: fs_sapmnt-clone [fs_sapmnt]:
    * fs_sapmnt	(ocf:heartbeat:Filesystem):	 Started cl-s01-1
    * fs_sapmnt	(ocf:heartbeat:Filesystem):	 Started cl-s01-2
  * Resource Group: s01_ascs21_group:
    * s01_fs_ascs21	(ocf:heartbeat:Filesystem):	 Started cl-s01-1
    * s01_vip_ascs21	(ocf:heartbeat:powervs-subnet):	 Started cl-s01-1
  * Resource Group: s01_ers22_group:
    * s01_fs_ers22	(ocf:heartbeat:Filesystem):	 Started cl-s01-1
    * s01_vip_ers22	(ocf:heartbeat:powervs-subnet):	 Started cl-s01-1

Migration Summary:

Tickets:

PCSD Status:
  cl-s01-1: Online
  cl-s01-2: Online

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```
{: screen}

Make sure that the `${sid}_ascs${ASCS_INSTNO}_group` cluster resource group runs on NODE1 and the `${sid}_ers${ERS_INSTNO}_group` cluster resource group runs on NODE2.
If necessary, use the `pcs resource move <resource_group_name>` command to move the resource group to the correct node.

### Changing the ownership of the ASCS and ERS mount points
{: #ha-rhel-ensa-mz-verify-change-mountpoint-owner}

The *sidadm* user must own the mount points for the filesystems of the *ASCS* and *ERS* instances.
Create the required users and groups and set the mount point ownership before you start the instance installation.

On both nodes, use the following steps to set the required owner.

1. Start the *SAP Software Provisioning Manager (SWPM)* to create the operating system users and groups.

   ```sh
   <swpm>/sapinst
   ```
   {: screen}

   In the SWPM web interface, use the path *System Rename > Preparations > Operating System Users and Group*.
   Note the user and group IDs and make sure that they are the same on both nodes.
2. Change the ownership of the mount points.

   ```sh
   chown -R ${sid}adm:sapsys /sapmnt/${SID} /usr/sap/${SID}
   ```
   {: pre}

## Installing the ASCS and ERS instances
{: #ha-rhel-ensa-mz-install-sap-instances}

Use SWPM to install both instances.

- Install *ASCS* and *ERS* instances on the cluster nodes.
   - On NODE1, use the virtual hostname `${ASCS_VH}` that is associated with the *ASCS* virtual IP address and install an *ASCS* instance.

   ```sh
   <swpm>/sapinst SAPINST_USE_HOSTNAME=${ASCS_VH}
   ```
   {: screen}

   - On NODE2, use the virtual hostname `${ERS_VH}` that is associated with the *ERS* virtual IP address and install an *ERS* instance.

   ```sh
   <swpm>/sapinst SAPINST_USE_HOSTNAME=${ERS_VH}
   ```
   {: screen}

- Install all other SAP application instances outside the cluster.

## Preparing the ASCS and ERS instances for cluster integration
{: #ha-rhel-ensa-mz-prepare-ascs-ers}

{{../../_include-segments/powervs-ha-rhel-ensa-prepare-for-cluster.md}}

## Configuring the ASCS and ERS cluster resources
{: #ha-rhel-ensa-mz-cfg-ascs-ers}

Up to this point, the following are assumed:


- A RHEL HA Add-On cluster is running on both virtual server instances and node fencing is tested.
- A cloned *Filesystem* cluster resource is configured to mount the *sapmnt* share.
- Two *Filesystem* cluster resources are configured to mount the  *ASCS* and *ERS* instance file systems.
- Two *powervs-subnet* cluster resources are configured for the virtual IP addresses of the *ASCS* and *ERS* instances.
- The *ASCS* instance is installed and active on NODE1.
- THE *ERS* instance is installed and active on NODE2.
- All steps according to [Prepare ASCS and ERS instances for the cluster integration](/docs/sap?topic=sap-ha-rhel-ensa#ha-rhel-ensa-prepare-ascs-ers) are complete.

### Configuring the ASCS cluster resource group
{: #ha-rhel-ensa-mz-cfg-ascs-rg}

On NODE1, run the following commands to create a cluster resource for managing the *ASCS* instance.

```sh
pcs resource create ${sid}_ascs${ASCS_INSTNO} SAPInstance \
    InstanceName="${SID}_ASCS${ASCS_INSTNO}_${ASCS_VH}" \
    START_PROFILE=/sapmnt/${SID}/profile/${SID}_ASCS${ASCS_INSTNO}_${ASCS_VH} \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 \
    op stop interval=0 timeout=600 \
    --group ${sid}_ascs${ASCS_INSTNO}_group
```
{: pre}

The `meta resource-stickiness=5000` option is used to balance the failover constraint with ERS so that the resource stays on the node where it started and doesn't migrate uncontrollably in the cluster.
{: note}

Add a resource stickiness to the group to ensure that the *ASCS* instance stays on the node.

```sh
pcs resource meta ${sid}_ascs${ASCS_INSTNO}_group \
    resource-stickiness=3000
```
{: pre}

### Configuring the ERS cluster resource group
{: #ha-rhel-ensa-mz-cfg-ers-rg}

On NODE2, run the following command to create a resource for managing the *ERS* instance.

```sh
pcs resource create ${sid}_ers${ERS_INSTNO} SAPInstance \
    InstanceName="${SID}_ERS${ERS_INSTNO}_${ERS_VH}" \
    START_PROFILE=/sapmnt/${SID}/profile/${SID}_ERS${ERS_INSTNO}_${ERS_VH} \
    AUTOMATIC_RECOVER=false \
    IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 \
    op stop interval=0 timeout=600 \
    --group ${sid}_ers${ERS_INSTNO}_group
```
{: pre}

### Configuring the cluster constraints
{: #ha-rhel-ensa-mz-cfg-constraints}

On NODE1, run the following command to create the cluster constraints.

A colocation constraint prevents resource groups `${sid}_ascs${ASCS_INSTNO}_group` and `${sid}_ers${ERS_INSTNO}_group` from being active on the same node whenever possible.
If only a single node is available, the stickiness value of `-5000` ensures that they run on the same node.

```sh
pcs constraint colocation add \
    ${sid}_ers${ERS_INSTNO}_group with ${sid}_ascs${ASCS_INSTNO}_group -- -5000
```
{: pre}

An order constraint controls that `${sid}_ascs${ASCS_INSTNO}_group` starts before `${sid}_ers${ERS_INSTNO}_group`.

```sh
pcs constraint order start \
    ${sid}_ascs${ASCS_INSTNO}_group then stop ${sid}_ers${ERS_INSTNO}_group \
    symmetrical=false \
    kind=Optional
```
{: pre}

The following two order constraints ensure that the *SAPMNT* file system mounts before `${sid}_ascs${ASCS_INSTNO}_group` and `${sid}_ers${ERS_INSTNO}_group` start.

```sh
pcs constraint order fs_sapmnt-clone then ${sid}_ascs${ASCS_INSTNO}_group
```
{: pre}

```sh
pcs constraint order fs_sapmnt-clone then ${sid}_ers${ERS_INSTNO}_group
```
{: pre}


## Conclusion
{: #ha-rhel-ensa-mz-conclusion}

The *ENSA2* cluster implementation in a multizone region environment is now complete.

Now, run tests similar to the ones described in [Testing an SAP ENSA2 cluster](/docs/sap?topic=sap-ha-rhel-ensa#ha-rhel-ensa-test-sap-ensa-cluster) to validate the cluster.

The following shows a sample output of the `pcs status` command for a completed *ENSA2* cluster in a multizone region deployment.

```sh
Cluster name: SAP_S01
Status of pacemakerd: 'Pacemaker is running' (last updated 2024-11-22 09:42:15 +01:00)
Cluster Summary:
  * Stack: corosync
  * Current DC: cl-s01-1 (version 2.1.5-9.el9_2.4-a3f44794f94) - partition with quorum
  * Last updated: Fri Nov 22 09:42:15 2024
  * Last change:  Fri Nov 22 09:06:18 2024 by root via cibadmin on cl-s01-1
  * 2 nodes configured
  * 10 resource instances configured

Node List:
  * Online: [ cl-s01-1 cl-s01-2 ]

Full List of Resources:
  * fence_node1	(stonith:fence_ibm_powervs):	 Started cl-s01-1
  * fence_node2	(stonith:fence_ibm_powervs):	 Started cl-s01-2
  * Clone Set: fs_sapmnt-clone [fs_sapmnt]:
    * Started: [ cl-s01-1 cl-s01-2 ]
  * Resource Group: s01_ascs21_group:
    * s01_fs_ascs21	(ocf:heartbeat:Filesystem):	 Started cl-s01-1
    * s01_vip_ascs21	(ocf:heartbeat:powervs-subnet):	 Started cl-s01-1
    * s01_ascs21	(ocf:heartbeat:SAPInstance):	 Started cl-s01-1
  * Resource Group: s01_ers22_group:
    * s01_fs_ers22	(ocf:heartbeat:Filesystem):	 Started cl-s01-2
    * s01_vip_ers22	(ocf:heartbeat:powervs-subnet):	 Started cl-s01-2
    * s01_ers22	(ocf:heartbeat:SAPInstance):	 Started cl-s01-2

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```
{: screen}
