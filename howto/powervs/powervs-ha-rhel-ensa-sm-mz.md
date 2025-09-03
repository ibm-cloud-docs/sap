---
copyright:
  years: 2023, 2025
lastupdated: "2025-09-01"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Configuring high availability for SAP S/4HANA (ASCS and ERS) on Red Hat Enterprise Linux HA Add-On clusters in a multizone region with simple mount
{: #ha-rhel-ensa-sm-mz}

The following information describes how to configure *ABAP SAP Central Services (ASCS)* and *Enqueue Replication Server (ERS)* in a Red Hat Enterprise Linux (RHEL) High Availability Add-On cluster.
The setup uses the *Simple Mount Structure*, a streamlined approach for mounting SAP application server directories.
The cluster runs on virtual server instances in [{{site.data.keyword.powerSysFull}}](https://www.ibm.com/products/power-virtual-server){: external}.
{: shortdesc}

This configuration example applies to the second-generation of the [Standalone Enqueue Server](https://help.sap.com/docs/ABAP_PLATFORM/cff8531bc1d9416d91bb6781e628d4e0/902412f09e134f5bb875adb6db585c92.html){: external}, also known as *ENSA2*.

Since SAP S/4HANA 1809, ENSA2 is installed by default and supports both two-node and multi-node cluster configurations.
This example demonstrates a two-node RHEL HA Add-On cluster setup using ENSA2.
If the *ASCS* service fails, it automatically restarts on the node hosting the *ERS* instance.
The lock entries are restored from the *ERS* instance’s copy of the lock table.
When the failed node is reactivated, the *ERS* instance relocates to the other node (anti-colocation) to maintain redundancy and protect the lock table copy.


It is recommended to install the SAP database and other SAP application server instances on virtual servers outside the two-node cluster used for *ASCS* and *ERS*.

## Before you begin
{: #ha-rhel-ensa-sm-mz-begin}

Review the general requirements, product documentation, support articles, and SAP notes listed in [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

## Prerequisites
{: #ha-rhel-ensa-sm-mz-prerequisites}

The following NFS file systems must be mounted by the operating system on both cluster nodes.
These file systems are not managed by the HA cluster.
   - `/usr/sap/<SID>`: Contains directories for both the *ASCS* and *ERS* instances.
   - `/sapmnt/<SID>`: Hosts the *sapmnt* share.
   - `usr/sap/trans`: May be required for shared transport directories (*saptrans*).

Ensure that a highly available NFS server is configured to provide these shared file systems.
Do not install the NFS server on any virtual server that is part of the *ENSA2* cluster.
This document does not cover the setup of file storage or the creation of cluster file system resources.
{: important}


Ensure that the virtual hostnames for the *ASCS* and *ERS* instances comply with the requirements outlined in [Hostnames of SAP ABAP Platform servers](https://me.sap.com/notes/611361){: external}.

The subnets and the virtual IP addresses for the *ASCS* and *ERS* instances must not exist in the {{site.data.keyword.powerSys_notm}} workspaces, as they are managed as cluster resources.
However, you must register the virtual IP addresses and hostnames for the *ASCS* and *ERS* instances in the Domain Name Service (DNS) and add them to the `etc/hosts` file on all cluster nodes.

## Preparing nodes to install ASCS and ERS instances
{: #ha-rhel-ensa-sm-mz-prepare-nodes}

The following information describes how to prepare the nodes for installing the SAP *ASCS* and *ERS* instances.

### Preparing environment variables
{: #ha-rhel-ensa-sm-mz-prepare-environment-variables}

To simplify the setup process, define the following environment variables for the `root` user on both cluster nodes.
These variables will be used in subsequent operating system commands.

On both nodes, set the following environment variables.

```sh
# General settings
export CLUSTERNAME="SAP_S01"        # Cluster name
export NODE1=<HOSTNAME_1>           # Virtual server instance 1 hostname
export NODE2=<HOSTNAME_2>           # Virtual server instance 2 hostname

export SID=<SID>                    # SAP System ID (uppercase)
export sid=<sid>                    # SAP System ID (lowercase)

# ASCS instance
export ASCS_INSTNO=<INSTNO>         # ASCS instance number
export ASCS_NET=<Subnet name>       # Name for the ASCS subnet in IBM Cloud
export ASCS_CIDR=<CIDR of subnet>   # CIDR of the ASCS subnet containing the service IP address
export ASCS_VH=<virtual hostname>   # ASCS virtual hostname
export ASCS_IP=<IP address>         # ASCS virtual IP address

# ERS instance
export ERS_INSTNO=<INSTNO>          # ERS instance number
export ERS_NET=<Subnet name>        # Name for the ERS subnet in IBM Cloud
export ERS_CIDR=<CIDR of subnet>    # CIDR of the ERS subnet containing the service IP address
export ERS_VH=<virtual hostname>    # ERS virtual hostname
export ERS_IP=<IP address>          # ERS virtual IP address

# Other multizone region settings
export CLOUD_REGION=<CLOUD_REGION>       # Multizone region name
export APIKEY="APIKEY or path to file"   # API key of the ServiceID for the resource agent
export API_TYPE="private or public"      # Use private or public API endpoints
export IBMCLOUD_CRN_1=<IBMCLOUD_CRN_1>   # Workspace 1 CRN
export IBMCLOUD_CRN_2=<IBMCLOUD_CRN_2>   # Workspace 2 CRN
export POWERVSI_1=<POWERVSI_1>           # Virtual server 1 instance id
export POWERVSI_2=<POWERVSI_2>           # Virtual server 2 instance id
export JUMBO="true or false"             # Enable Jumbo frames

# NFS settings
export NFS_SERVER="NFS server"           # Hostname or IP address of the highly available NFS server
export NFS_SHARE="NFS server directory"  # Exported file system directory on the NFS server
export NFS_OPTIONS="rw,sec=sys"          # Sample NFS client mount options
```
{: codeblock}

The following export commands are an example of how to set the extra environment variables that are required for a multizone region implementation.

```sh
# General settings
export CLUSTERNAME="SAP_S01"         # Cluster name
export NODE1="cl-s01-1"              # Virtual service instance 1 hostname
export NODE2="cl-s01-2"              # Virtual server instance 2 hostname

export SID="S01"                     # SAP System ID (uppercase)
export sid="s01"                     # SAP System ID (lowercase)

# ASCS instance
export ASCS_INSTNO="21"              # ASCS instance number
export ASCS_NET="s01-ascs-net"       # Name for the ASCS subnet in IBM Cloud
export ASCS_CIDR="10.40.21.100/30"   # CIDR of the ASCS subnet containing the service IP address
export ASCS_VH="s01ascs"             # ASCS virtual hostname
export ASCS_IP="10.40.21.102"        # ASCS virtual IP address

# ERS instance
export ERS_INSTNO="22"               # ERS instance number
export ERS_NET="s01-ers-net"         # Name for the ERS subnet in IBM Cloud
export ERS_CIDR="10.40.22.100/30"    # CIDR of the ERS subnet containing the service IP address
export ERS_VH="s01ers"               # ERS virtual hostname
export ERS_IP="10.40.22.102"         # ERS virtual IP address

# Other multizone region settings
export CLOUD_REGION="eu-de"
export IBMCLOUD_CRN_1="crn:v1:bluemix:public:power-iaas:eu-de-2:a/a1b2c3d4e5f60123456789a1b2c3d4e5:a1b2c3d4-0123-4567-89ab-a1b2c3d4e5f6::"
export IBMCLOUD_CRN_2="crn:v1:bluemix:public:power-iaas:eu-de-1:a/a1b2c3d4e5f60123456789a1b2c3d4e5:e5f6a1b2-cdef-0123-4567-a1b2c3d4e5f6::"
export POWERVSI_1="a1b2c3d4-0123-890a-f012-0123456789ab"
export POWERVSI_2="e5f6a1b2-4567-bcde-3456-cdef01234567"
export APIKEY="@/root/.apikey.json"
export API_TYPE="private"
export JUMBO="true"

# NFS settings
export NFS_SERVER="cl-nfs"           # Hostname or IP address of the highly available NFS server
export NFS_SHARE="/sapS01"           # Exported file system directory on the NFS server
export NFS_OPTIONS="rw,sec=sys"      # Sample NFS client mount options
```
{: screen}

### Creating mount points for the instance file systems
{: #ha-rhel-ensa-sm-mz-create-mountpoints}

On both cluster nodes, run the following command to create the required mount points for the SAP instance file systems.

```sh
mkdir -p /usr/sap/${SID} /sapmnt/${SID}
```
{: pre}

## Installing and setting up the RHEL HA Add-On cluster
{: #ha-rhel-ensa-sm-mz-set-up}

Follow the instructions in [Implementing a Red Hat Enterprise Linux High Availability Add-On cluster in a multizone region environment](/docs/sap?topic=sap-ha-rhel-mz) to install and configure the RHEL HA Add-On cluster.
After installation, configure and test cluster fencing as described in [Creating the fencing device](/docs/sap?topic=sap-ha-rhel-mz#ha-rhel-mz-create-fencing-device).

## Preparing cluster resources before the SAP installation
{: #ha-rhel-ensa-sm-mz-clres-ascs-ers}

Ensure that the RHEL HA Add-On cluster is active on both virtual server instances and that node fencing has been successfully tested.

### Configuring general cluster properties
{: #ha-rhel-ensa-sm-mz-configure-cluster-properties}

To prevent the cluster from relocating healthy resources — for example, when restarting a previously failed node — set the following default meta attributes.

- `resource-stickiness=1`: Ensures resources remain on their current node.
- `migration-threshold=3`: Limits the number of failures before a resource is moved.

On NODE1, run the following command.

```sh
pcs resource defaults update resource-stickiness=1
```
{: pre}

```sh
pcs resource defaults update migration-threshold=3
```
{: pre}

### Configuring the NFS mounts
{: #ha-rhel-ensa-sm-mz-nfs-mounts}

The operating system mounts the NFS file systems on all cluster nodes.
These mounts are not managed by the HA cluster and must be configured manually in the `/etc/fstab` file system table.

In this example, the NFS server exports the subdirectories `SAP_${SID}/sapmnt` and `SAP_${SID}/usrsap${sid}`.
Adjust the paths as needed to match your NFS server configuration.

Run the following commands on both cluster nodes.

1. Check whether /etc/fstab already contains entries for the *sapmnt* and *saptrans* file systems.
   If not, add the required mount entries.

   Make sure to adjust the export paths to match your NFS server layout.

   ```sh
   cat >> /etc/fstab << EOT
   ${NFS_SERVER}:${NFS_SHARE}/SAP_${SID}/sapmnt /sapmnt/${SID} nfs4 ${NFS_OPTIONS} 0 0
   EOT
   ```
   {: pre}

   ```sh
   cat >> /etc/fstab << EOT
   ${NFS_SERVER}:${NFS_SHARE}/SAP_${SID}/usrsap${sid} /usr/sap/${SID} nfs4 ${NFS_OPTIONS} 0 0
   EOT
   ```
   {: pre}

   Ensure that duplicate entries are not added to `/etc/fstab`.
   Use `cat >>` only if the entries do not already exist.
   {: important}

1. Mount the NFS file systems on both cluster nodes.

   ```sh
   mount /usr/sap/${SID}
   ```
   {: pre}

   ```sh
   mount /sapmnt/${SID}
   ```
   {: pre}

### Preparing to install the ASCS instance on NODE1
{: #ha-rhel-ensa-sm-mz-cfg-ascs-rg-prp}

On NODE1, run the following command to create a `powervs-subnet` cluster resource for the ASCS virtual IP address.

```sh
pcs resource create ${sid}_vip_ascs${ASCS_INSTNO} powervs-subnet \
    api_key=${APIKEY} \
    api_type=${API_TYPE} \
    cidr=${ASCS_CIDR} \
    ip=${ASCS_IP} \
    crn_host_map="${NODE1}:${IBMCLOUD_CRN_1};${NODE2}:${IBMCLOUD_CRN_2}" \
    vsi_host_map="${NODE1}:${POWERVSI_1};${NODE2}:${POWERVSI_2}" \
    jumbo=${JUMBO} \
    region=${CLOUD_REGION} \
    subnet_name=${ASCS_NET} \
    route_table=5${ASCS_INSTNO} \
    op start timeout=720 \
    op stop timeout=300 \
    op monitor interval=90 timeout=45 \
    group ${sid}_ascs${ASCS_INSTNO}_group \
    --future
```
{: pre}


### Preparing to install the ERS instance on NODE2
{: #ha-rhel-ensa-sm-mz-cfg-ers-rg-prp}

On NODE1, run the following command to create a `powervs-subnet` cluster resource for the ERS virtual IP address.

```sh
pcs resource create ${sid}_vip_ers${ERS_INSTNO} powervs-subnet \
    api_key=${APIKEY} \
    api_type=${API_TYPE} \
    cidr=${ERS_CIDR} \
    ip=${ERS_IP} \
    crn_host_map="${NODE1}:${IBMCLOUD_CRN_1};${NODE2}:${IBMCLOUD_CRN_2}" \
    vsi_host_map="${NODE1}:${POWERVSI_1};${NODE2}:${POWERVSI_2}" \
    jumbo=${JUMBO} \
    region=${CLOUD_REGION} \
    subnet_name=${ERS_NET} \
    route_table=5${ERS_INSTNO} \
    op start timeout=720 \
    op stop timeout=300 \
    op monitor interval=90 timeout=45 \
    group ${sid}_ers${ERS_INSTNO}_group \
    --future
```
{: pre}

### Verifying the cluster configuration
{: #ha-rhel-ensa-sm-mz-verify-cluster-config-before-sap-install}

On NODE1, run the following command to verify the current cluster configuration and ensure that all resources are correctly defined and active.

```sh
pcs status --full
```
{: pre}

Sample output:

```sh
# pcs status --full
Cluster name: SAP_S01
Cluster Summary:
  * Stack: corosync (Pacemaker is running)
  * Current DC: cl-s01-1 (1) (version 2.1.7-5.2.el9_4-0f7f88312) - partition with quorum
  * Last updated: Wed Mar  5 14:45:48 2025 on cl-s01-1
  * Last change:  Wed Mar  5 14:41:38 2025 by hacluster via hacluster on cl-s01-2
  * 2 nodes configured
  * 2 resource instances configured

Node List:
  * Node cl-s01-1 (1): online, feature set 3.19.0
  * Node cl-s01-2 (2): online, feature set 3.19.0

Full List of Resources:
  * Resource Group: s01_ascs21_group:
    * s01_vip_ascs21    (ocf:heartbeat:powervs-subnet):  Started cl-s01-1
  * Resource Group: s01_ers22_group:
    * s01_vip_ers22     (ocf:heartbeat:powervs-subnet):  Started cl-s01-2

Migration Summary:

Tickets:

PCSD Status:
  cl-s01-1: Online
  cl-s01-2: Online

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
  sbd: active/enabled
```
{: screen}

Ensure that the `${sid}_ascs${ASCS_INSTNO}_group` cluster resource group is running on NODE1, and that the `${sid}_ers${ERS_INSTNO}_group` cluster resource group is running on NODE2.
If needed, use the `pcs resource move <resource_group_name>` command to relocate the resource group to the appropriate node.


### Changing the ownership of the ASCS and ERS mount points
{: #ha-rhel-ensa-sm-mz-verify-change-mountpoint-owner}

The *ASCS* and *ERS* mount points must be owned by the *\<sid\>adm* user.
Before starting the instance installation, ensure that the required users and groups are defined and that the mount point ownership is correctly set.

Follow these steps on both nodes to configure the correct ownership.

1. Start the *SAP Software Provisioning Manager (SWPM)* to create the operating system users and groups.

   ```sh
   <swpm>/sapinst
   ```
   {: screen}

   In the SWPM web interface, navigate to *System Rename > Preparations > Operating System Users and Group*.
   Record the user and group IDss, and verify that they are identical on both nodes.

1. Change the ownership of the mount points.

   ```sh
   chown -R ${sid}adm:sapsys /sapmnt/${SID} /usr/sap/${SID}
   ```
   {: pre}

## Installing the ASCS and ERS instances
{: #ha-rhel-ensa-sm-mz-install-sap-instances}

Use SWPM to install both instances.

- Install *ASCS* and *ERS* instances on the cluster nodes.
   - On NODE1, use the virtual hostname `${ASCS_VH}`, which is associated with the *ASCS* virtual IP address, to install the *ASCS* instance.

   ```sh
   <swpm>/sapinst SAPINST_USE_HOSTNAME=${ASCS_VH}
   ```
   {: screen}

   - On NODE2, use the virtual hostname `${ERS_VH}`, which is associated with the *ERS* virtual IP address, to install the *ERS* instance.

   ```sh
   <swpm>/sapinst SAPINST_USE_HOSTNAME=${ERS_VH}
   ```
   {: screen}

- Install all other SAP application instances outside the cluster environment.

## Preparing the ASCS and ERS instances for cluster integration
{: #ha-rhel-ensa-sm-mz-prepare-ascs-ers}

{{../../_include-segments/powervs-ha-rhel-ensa-prepare-for-cluster.md}}

## Verifying the `resource-agents-sap` package version
{: #ha-rhel-ensa-sm-mz-verify-resource-agents-version}

The *Simple Mount* configuration for SAP application servers requires version **4.15.1 or later** of the `resource-agents-sap` package.
{: important}

To check the installed version, run the following command.
The output follows the format: `resource-agents-sap-<version>.<release>.noarch`

```sh
rpm -q resource-agents-sap
```
{: pre}

Sample output:

```sh
# rpm -q resource-agents-sap
resource-agents-sap-4.15.1-1.el9_4.1.noarch
```
{: screen}

## Configuring the ASCS and ERS cluster resources
{: #ha-rhel-ensa-sm-mz-cfg-ascs-ers}

At this stage, the following prerequisites are assumed to be complete:

- A RHEL HA Add-On cluster is running on both virtual server instances and node fencing is tested.
- The *sapmnt* share and the *usrsap* share are mounted on both cluster nodes.
- Two *powervs-subnet* cluster resources are configured for the virtual IP addresses of the *ASCS* and *ERS* instances.
- The *ASCS* instance is installed and active on NODE1.
- THE *ERS* instance is installed and active on NODE2.
- All steps described in [Prepare ASCS and ERS instances for the cluster integration](/docs/sap?topic=sap-ha-rhel-ensa#ha-rhel-ensa-prepare-ascs-ers) have been completed.

### Configuring the ASCS cluster resource group
{: #ha-rhel-ensa-sm-mz-cfg-ascs-rg}

On NODE1, run the following command to create a cluster resource that manages the SAP instance agent for the *ASCS* instance.

```sh
pcs resource create ${sid}_srv_ascs${ASCS_INSTNO} SAPStartSrv \
    InstanceName="${SID}_ASCS${ASCS_INSTNO}_${ASCS_VH}" \
    group ${sid}_ascs${ASCS_INSTNO}_group \
    --future
```
{: pre}

Next, on NODE1, create a cluster resource to manage the *ASCS* instance.

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
    group ${sid}_ascs${ASCS_INSTNO}_group \
    --future
```
{: pre}

The `meta resource-stickiness=5000` option balances the failover behavior for the ERS instance, ensuring that the resource remains on its original node and does not migrate unexpectedly within the cluster.
{: note}

To ensure that the *ASCS* instance remains on its designated node, add resource stickiness to the group.

```sh
pcs resource meta ${sid}_ascs${ASCS_INSTNO}_group \
    resource-stickiness=3000
```
{: pre}

### Configuring the ERS cluster resource group
{: #ha-rhel-ensa-sm-mz-cfg-ers-rg}

On NODE2, run the following command to create a cluster resource that manages the SAP instance agent for the *ERS* instance.

```sh
pcs resource create ${sid}_srv_ers${ERS_INSTNO} SAPStartSrv \
    InstanceName="${SID}_ERS${ERS_INSTNO}_${ERS_VH}" \
    group ${sid}_ers${ERS_INSTNO}_group \
    --future
```
{: pre}


Next, on NODE2, create a cluster resource to manage the *ERS* instance.

```sh
pcs resource create ${sid}_ers${ERS_INSTNO} SAPInstance \
    InstanceName="${SID}_ERS${ERS_INSTNO}_${ERS_VH}" \
    START_PROFILE=/sapmnt/${SID}/profile/${SID}_ERS${ERS_INSTNO}_${ERS_VH} \
    AUTOMATIC_RECOVER=false \
    IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 \
    op stop interval=0 timeout=600 \
    group ${sid}_ers${ERS_INSTNO}_group \
    --future
```
{: pre}

### Configuring the cluster constraints
{: #ha-rhel-ensa-sm-mz-cfg-constraints}

On NODE1, run the following commands to configure cluster constraints.

A colocation constraint ensures that the resource groups `${sid}_ascs${ASCS_INSTNO}_group` and `${sid}_ers${ERS_INSTNO}_group` do not run on the same node, provided that at least two nodes are available.
If only one node is available, the stickiness value of `-5000` allows both groups to run on the same node.

```sh
pcs constraint colocation add \
    ${sid}_ers${ERS_INSTNO}_group with ${sid}_ascs${ASCS_INSTNO}_group -- -5000
```
{: pre}

An order constraint ensures that `${sid}_ascs${ASCS_INSTNO}_group` starts before `${sid}_ers${ERS_INSTNO}_group`.

```sh
pcs constraint order start \
    ${sid}_ascs${ASCS_INSTNO}_group then stop ${sid}_ers${ERS_INSTNO}_group \
    symmetrical=false \
    kind=Optional
```
{: pre}

## Conclusion
{: #ha-rhel-ensa-sm-mz-conclusion}

The *ENSA2* cluster implementation in a multizone region environment is now complete.

Proceed with cluster validation by performing tests similar to those described in [Testing an SAP ENSA2 cluster](/docs/sap?topic=sap-ha-rhel-ensa#ha-rhel-ensa-test-sap-ensa-cluster).

The following shows a sample output of the `pcs status` command for a completed *ENSA2* cluster in a multizone region deployment.

```sh
Cluster name: SAP_S01
Cluster Summary:
  * Stack: corosync (Pacemaker is running)
  * Current DC: cl-s01-1 (version 2.1.7-5.2.el9_4-0f7f88312) - partition with quorum
  * Last updated: Wed Mar  5 17:14:01 2025 on cl-s01-1
  * Last change:  Wed Mar  5 17:11:36 2025 by root via root on cl-s01-1
  * 2 nodes configured
  * 4 resource instances configured

Node List:
  * Online: [ cl-s01-1 cl-s01-2 ]

Full List of Resources:

  * Resource Group: s01_ascs21_group:
    * s01_vip_ascs21    (ocf:heartbeat:powervs-subnet):  Started cl-s01-1
    * s01_srv_ascs21    (ocf:heartbeat:SAPStartSrv):     Started cl-s01-1
  * Resource Group: s01_ers22_group:
    * s01_vip_ers22     (ocf:heartbeat:powervs-subnet):  Started cl-s01-2
    * s01_srv_ers22     (ocf:heartbeat:SAPStartSrv):     Started cl-s01-2

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
  sbd: active/enabled
```
{: screen}
