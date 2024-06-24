---
copyright:
  years: 2023, 2024
lastupdated: 2024-06-24

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Configuring SAP HANA multitier system replication in a RHEL HA Add-On cluster
{: #ha-rhel-hana-sr-multitier}

The following information describes the configuration of a Red Hat Enterprise Linux (RHEL) HA add-on cluster for managing *SAP HANA&reg system replication* in a multitier replication scenario.
The cluster uses virtual server instances in [{{site.data.keyword.powerSysFull}}](https://www.ibm.com/products/power-virtual-server){: external} as cluster nodes.

You can connect multiple systems in an SAP HANA multitier system replication topology to achieve a higher level of availability.
The tertiary SAP HANA instance runs on a third virtual server instance in IBM {{site.data.keyword.powerSys_notm}} in another workspace.
The resource agents for SAP HANA in the Red Hat Enterprise Linux 8 (RHEL) HA add-on require that the tertiary SAP HANA instance is managed manually.
The tertiary SAP HANA system is installed on a virtual server instance outside the cluster.
After a takeover in the cluster, the tertiary SAP HANA instance must be reregistered manually.
{: shortdesc}

In a *multitier system replication* scenario, a tertiary SAP HANA system runs on a third virtual server instance.
The third virtual server instance is deployed in a different IBM {{site.data.keyword.powerSys_notm}} workspace in another geographical location or zone.
The SAP HANA system replication operation mode must be identical for all multitier replication levels.
The only exception is *logreplay_readaccess* between the primary and secondary combined with *logreplay* between the secondary and tertiary.

A takeover to the tertiary system on the *Disaster Recovery (DR)* site must be triggered manually.

This information is intended for architects and specialists that are planning a high-availability deployment of SAP HANA on {{site.data.keyword.powerSys_notm}}.
{: note}

## Before you begin
{: #ha-rhel-hana-sr-mt-begin}

Review the general requirements, product documentation, support articles, and SAP notes listed in [Implementing High Availability for SAP Applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

## Prerequisites
{: #ha-rhel-hana-sr-mt-prerequisites}

- A Red Hat High Availability cluster is deployed on two virtual server instances in one workspace in {{site.data.keyword.powerSys_notm}}.
   Use the instructions in the following documents.
   - [Implementing a RHEL HA Add-On cluster on {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-ha-rhel).
   - [Configuring SAP HANA Scale-Up System Replication in a RHEL HA Add-On cluster](/docs/sap?topic=sap-ha-rhel-hana-sr).
- A third virtual server instance is deployed in another workspace in {{site.data.keyword.powerSys_notm}}.
- SAP HANA is installed on the third virtual server instance with the same `SID` and `Instance Number`.
- Optional - you can reserve a virtual IP address for the system on NODE3 as described in [Reserving virtual IP addresses](/docs/sap?topic=sap-ha-vsi#ha-vsi-reserve-virtual-ip-addresses).
   Assigning and unassigning this virtual IP address on NODE3 is a manual task and not part of a cluster operation.

## Setting up the multitier scenario
{: #ha-rhel-hana-sr-mt-setup}

The multitier scenario is an extension of the setup that is described in [Configuring SAP HANA Scale-Up System Replication in a RHEL HA Add-On cluster](/docs/sap?topic=sap-ha-rhel-hana-sr).
Complete the setup for the system replication cluster before you continue with the following steps.

If the `AUTOMATED_REGISTER` cluster attribute is set to `true`, the reintegration of a failed node in the cluster can lead to a setup with a wrong SAP HANA system replication mode or an undesired SAP HANA system replication topology.
To avoid these problems, disable the automatic registration and use the `hdbnsutil` command to register the SAP HANA system manually before you start the cluster on a failed node.

On a cluster node, run the following command to disable the automatic registration.

```sh
pcs resource update SAPHana_${SID}_${INSTNO} AUTOMATED_REGISTER=false
```
{: pre}

### Providing network connectivity between the workspaces
{: #ha-rhel-hana-sr-mt-conn}

1. Use the information in [Creating the workspace](/docs/sap?topic=sap-ha-vsi#ha-vsi-create-workspace) to create another workspace in a different geographic location or region.
1. Create subnets and make sure that the IP ranges don't overlap with any subnet of the workspace that hosts the virtual server instances for the cluster. For more information, see [Creating subnets](/docs/sap?topic=sap-ha-vsi#ha-vsi-create-subnets).
1. Set up {{site.data.keyword.cloud}} connections up in both workspaces and activate *Enable IBM Transit Gateway*. For more information, see [Creating {{site.data.keyword.cloud}} connections](/docs/power-iaas?topic=power-iaas-cloud-connections#create-cloud-connections).
1. Deploy an {{site.data.keyword.cloud}} Transit Gateway to interconnect the two IBM {{site.data.keyword.powerSys_notm}} workspaces.

   {{site.data.keyword.cloud}} Transit Gateway enables the interconnection of IBM {{site.data.keyword.powerSys_notm}}, {{site.data.keyword.cloud}} classic, and Virtual Private Cloud (VPC) infrastructures and keeps data within the {{site.data.keyword.cloud}} networks. For more information about planning and deploying {{site.data.keyword.cloud}} Transit Gateway, see [Planning for {{site.data.keyword.cloud}} Transit Gateway](/docs/transit-gateway?topic=transit-gateway-helpful-tips) and [Ordering {{site.data.keyword.cloud}} Transit Gateway](/docs/transit-gateway?topic=transit-gateway-ordering-transit-gateway).
   {: note}

1. To add the connections to your transit gateway to establish network connectivity between your IBM {{site.data.keyword.powerSys_notm}}, open [{{site.data.keyword.cloud_notm}} console](/login){: external} and log in to your account.
1. Select the Menu icon ![Menu icon](../../icons/icon_hamburger.svg) on the upper left and click **Interconnectivity**.
1. Click **Transit Gateway** on the left navigation pane.
1. Select the name of your transit gateway.

   In the expanded view, click **View details**.
   {: tip}

1. Click **Add connection**.
1. Choose and configure the specific network connections that you want to add to the transit gateway.
1. Choose **Direct Link**, and select the names of your {{site.data.keyword.cloud}} connections.
1. Click **Add** to create a connection.

### Preparing environment variables on NODE3
{: #ha-rhel-hana-sr-mt-env}

To simplify the setup, prepare the following environment variables for user ID `root` on NODE3.
These environment variables are used in subsequent commands in the remainder of the instructions.

On NODE3, create a file with the following environment variables.
Then, adapt them according to the configuration of your SAP HANA system.

```sh
export SID=<SID>            # SAP HANA System ID (uppercase)
export sid=<sid>            # SAP HANA System ID (lowercase)
export INSTNO=<INSTNO>      # SAP HANA Instance Number

export DC3=<Site3>          # HANA System Replication Site Name 3

export NODE1=<Hostname 1>   # Hostname of virtual server instance 1 (production primary)
export NODE2=<Hostname 2>   # Hostname of virtual server instance 2 (production secondary)
export NODE3=<Hostname 3>   # Hostname of virtual server instance 3 (production tertiary)
```
{: codeblock}

You must source this file before you use the sample commands in the remainder of this document.

For example, if you created a file that is called `sap_tier3.sh`, run the following command on NODE3 to set the environment variables.

```sh
source sap_tier3.sh
```
{: pre}

Every time that you start a new terminal session, you must run the previous `source` command.
As an alternative, you can move add the environment variables file to the `/etc/profile.d` directory during the cluster configuration.
In this example, the file is sourced automatically each time you log in to the server.
{: important}

### Verifying network connectivity between the virtual server instances
{: #ha-rhel-hana-sr-mt-verify-conn}

Verify the network connectivity between the two cluster nodes (NODE1 and NODE2) and NODE3.

1. Log in to both NODE1 and NODE2, and `ping` NODE3.

   ```sh
   ping -c 3 ${NODE3}
   ```
   {: pre}

   Sample output:
   ```sh
   # ping -c 3 cl-hdb-3
   PING cl-hdb-3 (10.40.20.70) 56(84) bytes of data.
   64 bytes from 10.40.20.70 (10.40.20.70): icmp_seq=1 ttl=46 time=78.2 ms
   64 bytes from 10.40.20.70 (10.40.20.70): icmp_seq=2 ttl=46 time=78.3 ms
   64 bytes from 10.40.20.70 (10.40.20.70): icmp_seq=3 ttl=46 time=78.2 ms

   --- cl-hdb-3 ping statistics ---
   3 packets transmitted, 3 received, 0% packet loss, time 2003ms
   rtt min/avg/max/mdev = 78.197/78.233/78.264/0.027 ms
   ```


1. Log in to NODE3 and `ping` NODE1.

   ```sh
   ping -c 3 ${NODE1}
   ```
   {: pre}

   Sample output:
   ```sh
   # ping -c 3 cl-hdb-1
   PING cl-hdb-1 (10.40.10.60) 56(84) bytes of data.
   64 bytes from cl-hdb-1 (10.40.10.60): icmp_seq=1 ttl=46 time=78.3 ms
   64 bytes from cl-hdb-1 (10.40.10.60): icmp_seq=2 ttl=46 time=78.2 ms
   64 bytes from cl-hdb-1 (10.40.10.60): icmp_seq=3 ttl=46 time=78.3 ms

   --- cl-hdb-1 ping statistics ---
   3 packets transmitted, 3 received, 0% packet loss, time 2002ms
   rtt min/avg/max/mdev = 78.245/78.268/78.287/0.229 ms
   ```


1. Log in to NODE3 and `ping` NODE2.

   ```sh
   ping -c 3 ${NODE2}
   ```
   {: pre}

   Sample output:
   ```sh
   # ping -c 3 cl-hdb-2
   PING cl-hdb-2 (10.40.10.194) 56(84) bytes of data.
   64 bytes from cl-hdb-2 (10.40.10.194): icmp_seq=1 ttl=46 time=77.6 ms
   64 bytes from cl-hdb-2 (10.40.10.194): icmp_seq=2 ttl=46 time=79.1 ms
   64 bytes from cl-hdb-2 (10.40.10.194): icmp_seq=3 ttl=46 time=77.7 ms

   --- cl-hdb-2 ping statistics ---
   3 packets transmitted, 3 received, 0% packet loss, time 2003ms
   rtt min/avg/max/mdev = 77.649/78.129/79.071/0.703 ms
   ```


### Copying PKI SSFS storage certificate files to NODE3
{: #ha-rhel-hana-sr-mt-ssfs}

The SAP HANA 2.0 data and log transmission channels for the replication process require authentication by using the system *PKI SSFS* storage certificate files.
- [2369981 - Required configuration steps for authentication with HANA System Replication](https://me.sap.com/notes/2369981){: external})

The system *PKI SSFS* storage certificate files are stored in `/usr/sap/${SID}/SYS/global/security/rsecssfs/` in subdirectories `data` and `key`.

On NODE3, run the following commands to copy files `SSFS_${SID}.DAT` and `SSFS_${SID}.KEY` from NODE2.

```sh
scp ${NODE2}:/usr/sap/${SID}/SYS/global/security/rsecssfs/data/SSFS_${SID}.DAT /usr/sap/${SID}/SYS/global/security/rsecssfs/data/SSFS_${SID}.DAT
```
{: pre}

```sh
scp ${NODE2}:/usr/sap/${SID}/SYS/global/security/rsecssfs/key/SSFS_${SID}.KEY /usr/sap/${SID}/SYS/global/security/rsecssfs/key/SSFS_${SID}.KEY
```
{: pre}

The copied *PKI SSFS* storage certificates on NODE3 become active during start of the SAP HANA system.
Therefore, it is recommended to copy the files when the SAP HANA system on NODE3 is stopped.

### Registering NODE3 as tertiary SAP HANA system replication system
{: #ha-rhel-hana-sr-mt-tier3}

Register the SAP HANA system as a tertiary system replication instance.

1. On NODE2, run the following command to enable this site as a system replication source system.

   ```sh
   sudo -i -u ${sid}adm -- hdbnsutil -sr_enable
   ```
   {: pre}

   Sample output:

   ```sh
   $ hdbnsutil -sr_enable
   nameserver is active, proceeding ...
   successfully enabled system as system replication source site
   done.
   ```


1. On NODE3, stop the SAP HANA system.

   ```sh
   sudo -i -u ${sid}adm -- HDB stop
   ```
   {: pre}

1. On NODE3, register the tertiary system with NODE2.

   ```sh
   sudo -i -u ${sid}adm -- \
       hdbnsutil -sr_register \
         --name=${DC3} \
         --remoteHost=${NODE2} \
         --remoteInstance=${INSTNO} \
         --replicationMode=async \
         --operationMode=logreplay \
         --online
   ```
   {: pre}

1. On NODE3, start the tertiary SAP HANA system.

   ```sh
   sudo -i -u ${sid}adm -- HDB start
   ```
   {: pre}

### Checking the SAP HANA system replication status
{: #ha-rhel-hana-sr-mt-check-sr-status}

You can monitor the system replication status by using the following tools.

- *SAP HANA cockpit*
- *SAP HANA studio*
- `hdbnsutil` command-line tool
- `systemReplicationStatus.py` Python script
- SQL queries

The full output of the `systemReplicationStatus.py` script is available on only the primary system, as a database connection is required to obtain some of the status information.

On NODE1, check the system replication status by using the `systemReplicationStatus.py` Python script.

```sh
sudo -i -u ${sid}adm -- HDBSettings.sh systemReplicationStatus.py
```
{: pre}

Sample output:

```sh
# sudo -i -u hdbadm -- HDBSettings.sh systemReplicationStatus.py
|Database |Host     |Port  |Service Name |Volume ID |Site ID |Site Name |Secondary|Secondary |Secondary |Secondary |Secondary     |Replication |Replication |Replication    |Secondary    |
|         |         |      |             |          |        |          |Host     |Port      |Site ID   |Site Name |Active Status |Mode        |Status      |Status Details |Fully Synced |
|-------- |-------- |----- |------------ |--------- |------- |--------- |-------- |--------- |--------- |--------- |------------- |----------- |----------- |-------------- |------------ |
|SYSTEMDB |cl-hdb-1 |30001 |nameserver   |        1 |      1 |SiteA     |cl-hdb-2 |    30001 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |
|HDB      |cl-hdb-1 |30007 |xsengine     |        2 |      1 |SiteA     |cl-hdb-2 |    30007 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |
|HDB      |cl-hdb-1 |30003 |indexserver  |        3 |      1 |SiteA     |cl-hdb-2 |    30003 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |
|SYSTEMDB |cl-hdb-2 |30001 |nameserver   |        1 |      2 |SiteB     |cl-hdb-3 |    30001 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30007 |xsengine     |        2 |      2 |SiteB     |cl-hdb-3 |    30007 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30003 |indexserver  |        3 |      2 |SiteB     |cl-hdb-3 |    30003 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |

status system replication site "2": ACTIVE
status system replication site "3": ACTIVE
overall system replication status: ACTIVE

Local System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

mode: PRIMARY
site id: 1
site name: SiteA
```


An alternative view of the system replication status is available with the `hdbnsutil` command.

On all nodes, run the following command to check the system replication status.

```sh
sudo -i -u ${sid}adm -- hdbnsutil -sr_state
```
{: pre}

Sample output on NODE1:

```sh
# sudo -i -u hdbadm -- hdbnsutil -sr_state

System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~

online: true

mode: primary
operation mode: primary
site id: 1
site name: SiteA

is source system: true
is secondary/consumer system: false
has secondaries/consumers attached: true
is a takeover active: false
is primary suspended: false

Host Mappings:
~~~~~~~~~~~~~~

cl-hdb-1 -> [SiteC] cl-hdb-3
cl-hdb-1 -> [SiteB] cl-hdb-2
cl-hdb-1 -> [SiteA] cl-hdb-1


Site Mappings:
~~~~~~~~~~~~~~
SiteA (primary/primary)
    |---SiteB (syncmem/logreplay)
    |    |---SiteC (async/logreplay)

Tier of SiteA: 1
Tier of SiteB: 2
Tier of SiteC: 3

Replication mode of SiteA: primary
Replication mode of SiteB: syncmem
Replication mode of SiteC: async

Operation mode of SiteA: primary
Operation mode of SiteB: logreplay
Operation mode of SiteC: logreplay

Mapping: SiteA -> SiteB
Mapping: SiteB -> SiteC

Hint based routing site:
done.
```


Sample output on NODE2:

```sh
# sudo -i -u hdbadm -- hdbnsutil -sr_state

System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~

online: true

mode: syncmem
operation mode: logreplay
site id: 2
site name: SiteB

is source system: true
is secondary/consumer system: true
has secondaries/consumers attached: true
is a takeover active: false
is primary suspended: false
is timetravel enabled: false
replay mode: auto
active primary site: 1

primary masters: cl-hdb-1

Host Mappings:
~~~~~~~~~~~~~~

cl-hdb-2 -> [SiteC] cl-hdb-3
cl-hdb-2 -> [SiteB] cl-hdb-2
cl-hdb-2 -> [SiteA] cl-hdb-1


Site Mappings:
~~~~~~~~~~~~~~
SiteA (primary/primary)
    |---SiteB (syncmem/logreplay)
    |    |---SiteC (async/logreplay)

Tier of SiteA: 1
Tier of SiteB: 2
Tier of SiteC: 3

Replication mode of SiteA: primary
Replication mode of SiteB: syncmem
Replication mode of SiteC: async

Operation mode of SiteA: primary
Operation mode of SiteB: logreplay
Operation mode of SiteC: logreplay

Mapping: SiteA -> SiteB
Mapping: SiteB -> SiteC

Hint based routing site:
done.
```


Sample output on NODE3:

```sh
# sudo -i -u hdbadm -- hdbnsutil -sr_state

System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~

online: true

mode: async
operation mode: logreplay
site id: 3
site name: SiteC

is source system: false
is secondary/consumer system: true
has secondaries/consumers attached: false
is a takeover active: false
is primary suspended: false
is timetravel enabled: false
replay mode: auto
active primary site: 2

primary masters: cl-hdb-2

Host Mappings:
~~~~~~~~~~~~~~

cl-hdb-3 -> [SiteC] cl-hdb-3
cl-hdb-3 -> [SiteB] cl-hdb-2
cl-hdb-3 -> [SiteA] cl-hdb-1


Site Mappings:
~~~~~~~~~~~~~~
SiteA (primary/primary)
    |---SiteB (syncmem/logreplay)
    |    |---SiteC (async/logreplay)

Tier of SiteA: 1
Tier of SiteB: 2
Tier of SiteC: 3

Replication mode of SiteA: primary
Replication mode of SiteB: syncmem
Replication mode of SiteC: async

Operation mode of SiteA: primary
Operation mode of SiteB: logreplay
Operation mode of SiteC: logreplay

Mapping: SiteA -> SiteB
Mapping: SiteB -> SiteC

Hint based routing site:
done.
```


## Testing the SAP HANA system replication cluster
{: #ha-rhel-hana-sr-mt-test-sap-hana-sr-cluster}

It is vital to thoroughly test the cluster configuration to make sure that the cluster is working correctly.
The following information provides a few sample failover test scenarios, but is not a complete list of test scenarios.

For example, the description of each test case includes the following information.

- Component that is tested
- Description of the test
- Prerequisites and the initial state before the failover test
- Test procedure
- Expected behavior and results
- Recovery procedure

### Test1 - Testing the failure of the primary database instance
{: #ha-rhel-hana-sr-mt-test1-primary-instance-database-failure}

Use the following information to test the failure of the primary database instance.

#### Test1 - Description
{: #ha-rhel-hana-sr-mt-test1-description}

Simulate a crash of the primary SAP HANA database instance that runs on NODE1.

#### Test1 - Prerequisites
{: #ha-rhel-hana-sr-mt-test1-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both cluster nodes are active.
- Cluster is started on NODE1 and NODE2.
- Cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=false`.
- Check SAP HANA system replication status:
   - SAP HANA multitier system replication is activated and in sync.
   - The primary SAP HANA system runs on NODE1.
   - The secondary SAP HANA system runs on NODE2.
   - The tertiary SAP HANA system runs on NODE3 and is registered with NODE2.

Check the current system replication status on NODE1.

```sh
sudo -i -u ${sid}adm -- HDBSettings.sh systemReplicationStatus.py
```
{: pre}

Sample output:

```sh
# sudo -i -u hdbadm -- HDBSettings.sh systemReplicationStatus.py
|Database |Host     |Port  |Service Name |Volume ID |Site ID |Site Name |Secondary|Secondary |Secondary |Secondary |Secondary     |Replication |Replication |Replication    |Secondary    |
|         |         |      |             |          |        |          |Host     |Port      |Site ID   |Site Name |Active Status |Mode        |Status      |Status Details |Fully Synced |
|-------- |-------- |----- |------------ |--------- |------- |--------- |-------- |--------- |--------- |--------- |------------- |----------- |----------- |-------------- |------------ |
|SYSTEMDB |cl-hdb-1 |30001 |nameserver   |        1 |      1 |SiteA     |cl-hdb-2 |    30001 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |
|HDB      |cl-hdb-1 |30007 |xsengine     |        2 |      1 |SiteA     |cl-hdb-2 |    30007 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |
|HDB      |cl-hdb-1 |30003 |indexserver  |        3 |      1 |SiteA     |cl-hdb-2 |    30003 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |
|SYSTEMDB |cl-hdb-2 |30001 |nameserver   |        1 |      2 |SiteB     |cl-hdb-3 |    30001 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30007 |xsengine     |        2 |      2 |SiteB     |cl-hdb-3 |    30007 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30003 |indexserver  |        3 |      2 |SiteB     |cl-hdb-3 |    30003 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |

status system replication site "2": ACTIVE
status system replication site "3": ACTIVE
overall system replication status: ACTIVE

Local System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

mode: PRIMARY
site id: 1
site name: SiteA
```



#### Test1 - Test procedure
{: #ha-rhel-hana-sr-mt-test1-procedure}

Crash SAP HANA primary by sending a SIGKILL signal as user `${sid}adm`.

On NODE1, run the following command.

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test1 - Expected behavior
{: #ha-rhel-hana-sr-mt-test1-expected-behavior}

- SAP HANA primary instance on NODE1 crashes.
- The cluster detects the stopped primary and marks the resource as `undefined`.
- The cluster promotes the secondary SAP HANA system on NODE2, which takes over as primary.
- The cluster releases the virtual IP address on NODE1, and acquires it on the primary on NODE2.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

On NODE1, run the following command to check the cluster status.

```sh
pcs status --full
```
{: pre}

Sample output:

```sh
pcs status --full

Cluster name: HDB_cluster
Cluster Summary:
  * Stack: corosync
  * Current DC: cl-hdb-1 (1) (version 2.0.5-9.el8_4.5-ba59be7122) - partition with quorum
  * Last updated: Mon Jul 10 16:00:38 2023
  * Last change:  Mon Jul 10 15:58:50 2023 by root via crm_attribute on cl-hdb-2
  * 2 nodes configured
  * 6 resource instances configured

Node List:
  * Online: [ cl-hdb-1 (1) cl-hdb-2 (2) ]

Full List of Resources:
  * res_fence_ibm_powervs       (stonith:fence_ibm_powervs):     Started cl-hdb-1
  * vip_HDB_00_primary  (ocf::heartbeat:IPaddr2):        Started cl-hdb-2
  * Clone Set: SAPHanaTopology_HDB_00-clone [SAPHanaTopology_HDB_00]:
    * SAPHanaTopology_HDB_00    (ocf::heartbeat:SAPHanaTopology):        Started cl-hdb-1
    * SAPHanaTopology_HDB_00    (ocf::heartbeat:SAPHanaTopology):        Started cl-hdb-2
  * Clone Set: SAPHana_HDB_00-clone [SAPHana_HDB_00] (promotable):
    * SAPHana_HDB_00    (ocf::heartbeat:SAPHana):        Master cl-hdb-2
    * SAPHana_HDB_00    (ocf::heartbeat:SAPHana):        Stopped

Node Attributes:
  * Node: cl-hdb-1 (1):
    * hana_hdb_clone_state              : UNDEFINED
    * hana_hdb_op_mode                  : logreplay
    * hana_hdb_remoteHost               : cl-hdb-2
    * hana_hdb_roles                    : 1:P:master1::worker:
    * hana_hdb_site                     : SiteA
    * hana_hdb_srah                     : -
    * hana_hdb_srmode                   : sync
    * hana_hdb_sync_state               : SFAIL
    * hana_hdb_version                  : 2.00.070.00.1679989823
    * hana_hdb_vhost                    : cl-hdb-1
    * lpa_hdb_lpt                       : 10
    * master-SAPHana_HDB_00             : -9000
  * Node: cl-hdb-2 (2):
    * hana_hdb_clone_state              : PROMOTED
    * hana_hdb_op_mode                  : logreplay
    * hana_hdb_remoteHost               : cl-hdb-1
    * hana_hdb_roles                    : 4:P:master1:master:worker:master
    * hana_hdb_site                     : SiteB
    * hana_hdb_sra                      : -
    * hana_hdb_srah                     : -
    * hana_hdb_srmode                   : sync
    * hana_hdb_sync_state               : PRIM
    * hana_hdb_version                  : 2.00.070.00.1679989823
    * hana_hdb_vhost                    : cl-hdb-2
    * lpa_hdb_lpt                       : 1688997529
    * master-SAPHana_HDB_00             : 150

Migration Summary:
  * Node: cl-hdb-1 (1):
    * SAPHana_HDB_00: migration-threshold=5000 fail-count=1000000 last-failure='Mon Jul 10 15:56:06 2023'

Failed Resource Actions:
  * SAPHana_HDB_00_start_0 on cl-hdb-1 'not running' (7): call=51, status='complete', exitreason='', last-rc-change='2023-07-10 15:56:04 +02:00', queued=0ms, exec=1527ms

Tickets:

PCSD Status:
  cl-hdb-1: Online
  cl-hdb-2: Online

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```


On NODE2, run the following command to check the system replication status.

```sh
sudo -i -u ${sid}adm -- HDBSettings.sh systemReplicationStatus.py
```
{: pre}

Sample output:

```sh
# sudo -i -u hdbadm -- HDBSettings.sh systemReplicationStatus.py
|Database |Host     |Port  |Service Name |Volume ID |Site ID |Site Name |Secondary|Secondary |Secondary |Secondary |Secondary     |Replication |Replication |Replication    |Secondary    |
|         |         |      |             |          |        |          |Host     |Port      |Site ID   |Site Name |Active Status |Mode        |Status      |Status Details |Fully Synced |
|-------- |-------- |----- |------------ |--------- |------- |--------- |-------- |--------- |--------- |--------- |------------- |----------- |----------- |-------------- |------------ |
|SYSTEMDB |cl-hdb-2 |30001 |nameserver   |        1 |      2 |SiteB     |cl-hdb-3 |    30001 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30007 |xsengine     |        2 |      2 |SiteB     |cl-hdb-3 |    30007 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30003 |indexserver  |        3 |      2 |SiteB     |cl-hdb-3 |    30003 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |

status system replication site "3": ACTIVE
overall system replication status: ACTIVE

Local System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

mode: PRIMARY
site id: 2
site name: SiteB
```


#### Test1 - Recovery procedure
{: #ha-rhel-hana-sr-mt-test1-recovery-procedure}

As the cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=false`, you need to register the SAP HANA system on NODE1 manually with the primary on NODE2.

When you register SAP HANA on NODE1 as a secondary, the SAP HANA system replication topology changes.
Both SAP HANA systems on NODE3 at `SiteC` and NODE1 at `SiteA` are then registered as secondaries to the primary SAP HANA database that runs on NODE2 at `SiteB`.
{: important}

If you want to stay with a multitier topology, you need to unregister the SAP HANA system on NODE3 at `SiteC` first.
Then, you register the SAP HANA system on NODE1 at `SiteA` with the primary on NODE2 at `SiteB`.
Finally, you register the SAP HANA system on NODE3 at `SiteC` with secondary on NODE1 at `SiteA`.
{: important}

On NODE1, run the following command to register the system with the primary on NODE2.

```sh
sudo -i -u ${sid}adm -- \
    hdbnsutil -sr_register \
      --name=${DC1} \
      --remoteHost=${NODE2} \
      --remoteInstance=${INSTNO} \
      --replicationMode=syncmem \
      --operationMode=logreplay \
      --online
```
{: pre}

On NODE1, run the following command to verify the resource status.

```sh
pcs resource status
```
{: pre}

The cluster resource `SAPHana_${SID}_${INSTNO}-clone` remains in status `Stopped` on NODE1.

Sample output:

```sh
# pcs resource status
  * vip_HDB_00_primary  (ocf::heartbeat:IPaddr2):        Started cl-hdb-2
  * Clone Set: SAPHanaTopology_HDB_00-clone [SAPHanaTopology_HDB_00]:
    * Started: [ cl-hdb-1 cl-hdb-2 ]
  * Clone Set: SAPHana_HDB_00-clone [SAPHana_HDB_00] (promotable):
    * Masters: [ cl-hdb-2 ]
    * Stopped: [ cl-hdb-1 ]
```


On a cluster node, run the following command to clear the failure status of the resource.

```sh
pcs resource cleanup SAPHana_${SID}_${INSTNO}-clone
```
{: pre}

Sample output:

```sh
# pcs resource cleanup SAPHana_HDB_00-clone
Cleaned up SAPHana_HDB_00:0 on cl-hdb-2
Cleaned up SAPHana_HDB_00:0 on cl-hdb-1
Cleaned up SAPHana_HDB_00:1 on cl-hdb-2
Cleaned up SAPHana_HDB_00:1 on cl-hdb-1
Waiting for 1 reply from the controller
... got reply (done)
```


After a while, verify the system replication status on NODE2.

```sh
sudo -i -u ${sid}adm -- HDBSettings.sh systemReplicationStatus.py
```
{: pre}

Sample output:

```sh
# sudo -i -u hdbadm -- HDBSettings.sh systemReplicationStatus.py
|Database |Host     |Port  |Service Name |Volume ID |Site ID |Site Name |Secondary|Secondary |Secondary |Secondary |Secondary     |Replication |Replication |Replication    |Secondary    |
|         |         |      |             |          |        |          |Host     |Port      |Site ID   |Site Name |Active Status |Mode        |Status      |Status Details |Fully Synced |
|-------- |-------- |----- |------------ |--------- |------- |--------- |-------- |--------- |--------- |--------- |------------- |----------- |----------- |-------------- |------------ |
|SYSTEMDB |cl-hdb-2 |30001 |nameserver   |        1 |      2 |SiteB     |cl-hdb-3 |    30001 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30007 |xsengine     |        2 |      2 |SiteB     |cl-hdb-3 |    30007 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30003 |indexserver  |        3 |      2 |SiteB     |cl-hdb-3 |    30003 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|SYSTEMDB |cl-hdb-2 |30001 |nameserver   |        1 |      2 |SiteB     |cl-hdb-1 |    30001 |        1 |SiteA     |YES           |SYNCMEM     |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30007 |xsengine     |        2 |      2 |SiteB     |cl-hdb-1 |    30007 |        1 |SiteA     |YES           |SYNCMEM     |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30003 |indexserver  |        3 |      2 |SiteB     |cl-hdb-1 |    30003 |        1 |SiteA     |YES           |SYNCMEM     |ACTIVE      |               |        True |

status system replication site "3": ACTIVE
status system replication site "1": ACTIVE
overall system replication status: ACTIVE

Local System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

mode: PRIMARY
site id: 2
site name: SiteB
```


The SAP HANA system replication topology that is changed to a multitarget environment.
The primary runs on NODE2 at `SiteB`.
Both NODE3 at `SiteC` and NODE1 at `SiteA` are registered as secondaries.
If another takeover occurs and NODE1 at `SiteA` is promoted to primary again, NODE3 at `SiteC` is decoupled.
{: important}

To create a multitier landscape with NODE3 at `SiteC` as a tertiary system, repeat the steps similar to [Registering NODE3 as tertiary SAP HANA system replication system](#ha-rhel-hana-sr-mt-tier3) and register NODE3 to the secondary on NODE1.

1. On NODE1, run the following command to enable this site as a system replication source system.

   ```sh
   sudo -i -u ${sid}adm -- hdbnsutil -sr_enable
   ```
   {: pre}

   Sample output:

   ```sh
   # sudo -i -u hdbadm -- hdbnsutil -sr_enable
   nameserver is active, proceeding ...
   successfully enabled system as system replication source site
   done.
   ```


1. On NODE3, register the system with NODE1 at `SiteA`.

   ```sh
   sudo -i -u ${sid}adm -- \
       hdbnsutil -sr_register \
         --name=${DC3} \
         --remoteHost=${NODE1} \
         --remoteInstance=${INSTNO} \
         --replicationMode=async \
         --operationMode=logreplay \
         --online
   ```
   {: pre}

   ```sh
   # sudo -i -u hdbadm -- hdbnsutil -sr_register --name=SiteC --remoteHost=cl-hdb-1 --remoteInstance=00 --replicationMode=async --operationMode=logreplay --online
   adding site ...
   collecting information ...
   updating local ini files ...
   done.
   ```


1. Verify the system replication status on all three nodes as described in [Checking the SAP HANA system replication status](#ha-rhel-hana-sr-mt-check-sr-status).

### Test2 - Testing the manual move of a SAPHana resource to another node
{: #ha-rhel-hana-sr-mt-test2-manual-move}

Use the following information to test the manual move of the SAPHana resource to another node.

#### Test2 - Description
{: #ha-rhel-hana-sr-mt-test2-description}

Use cluster commands to move the primary instance to the other cluster node.

#### Test2 - Prerequisites
{: #ha-rhel-hana-sr-mt-test2-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both cluster nodes are active.
- Cluster is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=false`.
- Check SAP HANA system replication status:
   - HANA system replication is activated and in sync.
   - The primary SAP HANA system runs on NODE2.
   - The secondary SAP HANA system runs on NODE1.
   - The tertiary SAP HANA system runs on NODE3 and is registered with NODE1.

#### Test2 - Test procedure
{: #ha-rhel-hana-sr-mt-test2-procedure}

1. On NODE3, stop the tertiary HANA system before you perform the controlled move of the primary to NODE1.

   ```sh
   sudo -i -u ${sid}adm -- HDB stop
   ```
   {: pre}

1. On a cluster node, run the following command to move the primary back to NODE1.

   ```sh
   pcs resource move SAPHana_${SID}_${INSTNO}-clone
   ```
   {: pre}

1. Wait until the primary is up on NODE1.
   Then, register NODE2 with the primary on NODE1.

   On NODE2, run the following command.

   ```sh
   sudo -i -u ${sid}adm -- \
       hdbnsutil -sr_register \
         --name=${DC2} \
         --remoteHost=${NODE1} \
         --remoteInstance=${INSTNO} \
         --replicationMode=syncmem \
         --operationMode=logreplay \
         --online
   ```
1. On a cluster node, run the following commend to clear the resource.

   ```sh
   pcs resource clear SAPHana_${SID}_${INSTNO}-clone
   ```
   {: pre}

   This command clears the location constraint, which was created by the move command.
   The cluster starts the SAP HANA system on NODE2.

1. On NODE2, run the following command to enable this site as a system replication source system.

   ```sh
   sudo -i -u ${sid}adm -- hdbnsutil -sr_enable
   ```
   {: pre}

1. On NODE3, run the following command to register the system with NODE2.

   ```sh
   sudo -i -u ${sid}adm -- \
       hdbnsutil -sr_register \
         --name=${DC3} \
         --remoteHost=${NODE2} \
         --remoteInstance=${INSTNO} \
         --replicationMode=async \
         --operationMode=logreplay \
         --online
   ```
   {: pre}

1. On NODE3, start the tertiary HANA system.

   ```sh
   sudo -i -u ${sid}adm -- HDB start
   ```
   {: pre}

1. Verify the system replication status on all three nodes as described in [Checking the SAP HANA system replication status](#ha-rhel-hana-sr-mt-check-sr-status).

#### Test2 - Expected behavior
{: #ha-rhel-hana-sr-mt-test2-expected-behavior}

- The cluster creates a location constraint to move the resource.
- The cluster triggers a takeover to the secondary HANA system on NODE1.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.
- Register NODE2 with the primary on NODE1.
- Run `pcs resource clear` command to remove the location constraint.
   This command triggers the start of the secondary instance on NODE2.
- After you register and start the HANA system on NODE3 at `SiteC`, the tertiary HANA system is registered with NODE2.

#### Test2 - Recovery procedure
{: #ha-rhel-hana-sr-mt-test2-recovery-procedure}

No recovery procedure is required.
The test sequence reestablished the initial SAP HANA multitier system replication topology.

### Test3 - Testing failure of node that runs the primary database
{: #ha-rhel-hana-sr-mt-test3-primary-instance-server-failure}

Use the following information to test the failure of node that runs the primary database.

#### Test3 - Description
{: #ha-rhel-hana-sr-mt-test3-description}

Simulate a crash of the node that runs the primary HANA database.

#### Test3 - Prerequisites
{: #ha-rhel-hana-sr-mt-test3-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both cluster nodes are active.
- Cluster is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=false`.
- Check SAP HANA system replication status:
   - HANA system replication is activated and in sync.
   - The primary SAP HANA system runs on NODE1.
   - The secondary SAP HANA system runs on NODE2.
   - The secondary SAP HANA system runs on NODE3 and is registered with NODE2.

#### Test3 - Test procedure
{: #ha-rhel-hana-sr-mt-test3-procedure}

Crash the primary on NODE1 by sending a *crash* system request.

On NODE1, run the following command.

```sh
sync; echo c > /proc/sysrq-trigger
```
{: pre}

#### Test3 - Expected behavior
{: #ha-rhel-hana-sr-mt-test3-expected-behavior}

- NODE1 shuts down.
- The cluster detects the failed node and sets its state to `OFFLINE`.
- The cluster promotes the secondary HANA database on NODE2 to take over as new primary.
- The cluster acquires the virtual IP address on NODE2.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.
- The tertiary SAP HANA system that runs on NODE3 is still registered with NODE2.

Verify the SAP HANA system replication status on NODE2.

```sh
sudo -i -u ${sid}adm -- HDBSettings.sh systemReplicationStatus.py
```
{: pre}

Sample output:

```sh
# sudo -i -u hdbadm -- HDBSettings.sh systemReplicationStatus.py
|Database |Host     |Port  |Service Name |Volume ID |Site ID |Site Name |Secondary|Secondary |Secondary |Secondary |Secondary     |Replication |Replication |Replication    |Secondary    |
|         |         |      |             |          |        |          |Host     |Port      |Site ID   |Site Name |Active Status |Mode        |Status      |Status Details |Fully Synced |
|-------- |-------- |----- |------------ |--------- |------- |--------- |-------- |--------- |--------- |--------- |------------- |----------- |----------- |-------------- |------------ |
|SYSTEMDB |cl-hdb-2 |30001 |nameserver   |        1 |      2 |SiteB     |cl-hdb-3 |    30001 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30007 |xsengine     |        2 |      2 |SiteB     |cl-hdb-3 |    30007 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30003 |indexserver  |        3 |      2 |SiteB     |cl-hdb-3 |    30003 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |

status system replication site "3": ACTIVE
overall system replication status: ACTIVE

Local System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

mode: PRIMARY
site id: 2
site name: SiteB
```


#### Test3 - Recovery procedure
{: #ha-rhel-hana-sr-mt-test3-recovery-procedure}

Log in to the {{site.data.keyword.cloud}} console and start NODE1.

1. On NODE1, run the following command to register the system with the primary on NODE2.

   ```sh
   sudo -i -u ${sid}adm -- \
       hdbnsutil -sr_register \
         --name=${DC1} \
         --remoteHost=${NODE2} \
         --remoteInstance=${INSTNO} \
         --replicationMode=syncmem \
         --operationMode=logreplay \
         --online
   ```
   {: pre}

1. On NODE1, run the following command to start the cluster services.

   ```sh
   pcs cluster start
   ```
   {: pre}

1. On a cluster node, run the following command to check the cluster status.

   ```sh
   pcs status --full
   ```
   {: pre}

3. On NODE2, verify the SAP HANA system replication status.

   ```sh
   sudo -i -u ${sid}adm -- HDBSettings.sh systemReplicationStatus.py

   ```
   {: pre}

   Sample output:

   ```sh
   # sudo -i -u hdbadm -- HDBSettings.sh systemReplicationStatus.py
   |Database |Host     |Port  |Service Name |Volume ID |Site ID |Site Name |Secondary|Secondary |Secondary |Secondary |Secondary     |Replication |Replication |Replication    |Secondary    |
   |         |         |      |             |          |        |          |Host     |Port      |Site ID   |Site Name |Active Status |Mode        |Status      |Status Details |Fully Synced |
   |-------- |-------- |----- |------------ |--------- |------- |--------- |-------- |--------- |--------- |--------- |------------- |----------- |----------- |-------------- |------------ |
   |SYSTEMDB |cl-hdb-2 |30001 |nameserver   |        1 |      2 |SiteB     |cl-hdb-3 |    30001 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
   |HDB      |cl-hdb-2 |30007 |xsengine     |        2 |      2 |SiteB     |cl-hdb-3 |    30007 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
   |HDB      |cl-hdb-2 |30003 |indexserver  |        3 |      2 |SiteB     |cl-hdb-3 |    30003 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
   |SYSTEMDB |cl-hdb-2 |30001 |nameserver   |        1 |      2 |SiteB     |cl-hdb-1 |    30001 |        1 |SiteA     |YES           |SYNCMEM     |ACTIVE      |               |        True |
   |HDB      |cl-hdb-2 |30007 |xsengine     |        2 |      2 |SiteB     |cl-hdb-1 |    30007 |        1 |SiteA     |YES           |SYNCMEM     |ACTIVE      |               |        True |
   |HDB      |cl-hdb-2 |30003 |indexserver  |        3 |      2 |SiteB     |cl-hdb-1 |    30003 |        1 |SiteA     |YES           |SYNCMEM     |ACTIVE      |               |        True |

   status system replication site "3": ACTIVE
   status system replication site "1": ACTIVE
   overall system replication status: ACTIVE

   Local System Replication State
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   mode: PRIMARY
   site id: 2
   site name: SiteB
   ```


1. Run the steps in [Test1 - Recovery procedure](#ha-rhel-hana-sr-mt-test1-recovery-procedure) to rebuild the SAP HANA system replication multitier topology for NODE3 at `SiteC`.

1. Run the steps in [Test2 - Test the manual move of SAPHana resource to another node](#ha-rhel-hana-sr-mt-test2-manual-move) to revert to the initial topology.

### Test4 - Testing failure of the secondary database instance
{: #ha-rhel-hana-sr-mt-test4-secondary-instance-database-failure}

Use the following information to test the failure of the secondary database instance.

#### Test4 - Description
{: #ha-rhel-hana-sr-mt-test4-description}

Simulate a crash of the secondary HANA database.

#### Test4 - Prerequisites
{: #ha-rhel-hana-sr-mt-test4-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both nodes are active.
- Cluster is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=false`.
- Check SAP HANA system replication status:
   - HANA system replication is active and in sync.
   - The primary SAP HANA system runs on NODE1.
   - The secondary SAP HANA system runs on NODE2.
   - The tertiary SAP HANA system runs on NODE3 and is registered with NODE2.

#### Test4 - Test procedure
{: #ha-rhel-hana-sr-mt-test4-procedure}

Crash SAP HANA secondary by sending a SIGKILL signal as user `${sid}adm`.

On NODE2, run the following command.

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test4 - Expected behavior
{: #ha-rhel-hana-sr-mt-test4-expected-behavior}

- SAP HANA secondary on NODE2 crashes.
- The cluster detects the stopped secondary HANA system and marks the resource as `failed`.
- The cluster restarts the secondary HANA system.
- The cluster detects that the system replication is in sync again.
- The tertiary SAP HANA system that runs on NODE3 gets in sync again.

On NODE1, check the SAP HANA system replication status periodically to observe the recovery steps.

```sh
watch -n 5 sudo -i -u ${sid}adm -- HDBSettings.sh systemReplicationStatus.py
```
{: pre}

Sample output:

```sh
# sudo -i -u hdbadm -- HDBSettings.sh systemReplicationStatus.py
|Database |Host     |Port  |Service Name |Volume ID |Site ID |Site Name |Secondary|Secondary |Secondary |Secondary |Secondary          |Replication |Replication |Replication                       |Secondary    |
|         |         |      |             |          |        |          |Host     |Port      |Site ID   |Site Name |Active Status      |Mode        |Status      |Status Details                    |Fully Synced |
|-------- |-------- |----- |------------ |--------- |------- |--------- |-------- |--------- |--------- |--------- |------------------ |----------- |----------- |--------------------------------- |------------ |
|SYSTEMDB |cl-hdb-1 |30001 |nameserver   |        1 |      1 |SiteA     |cl-hdb-2 |    30001 |        2 |SiteB     |CONNECTION TIMEOUT |SYNCMEM     |ERROR       |Communication channel closed      |       False |
|HDB      |cl-hdb-1 |30007 |xsengine     |        2 |      1 |SiteA     |cl-hdb-2 |    30007 |        2 |SiteB     |CONNECTION TIMEOUT |SYNCMEM     |ERROR       |Communication channel closed      |       False |
|HDB      |cl-hdb-1 |30003 |indexserver  |        3 |      1 |SiteA     |cl-hdb-2 |    30003 |        2 |SiteB     |CONNECTION TIMEOUT |SYNCMEM     |ERROR       |Communication channel closed      |       False |
|SYSTEMDB |cl-hdb-2 |30001 |nameserver   |        1 |      2 |SiteB     |cl-hdb-3 |    30001 |        3 |SiteC     |                   |UNKNOWN     |UNKNOWN     |Site with id '2' is not reachable |       False |
|HDB      |cl-hdb-2 |30007 |xsengine     |        2 |      2 |SiteB     |cl-hdb-3 |    30007 |        3 |SiteC     |                   |UNKNOWN     |UNKNOWN     |Site with id '2' is not reachable |       False |
|HDB      |cl-hdb-2 |30003 |indexserver  |        3 |      2 |SiteB     |cl-hdb-3 |    30003 |        3 |SiteC     |                   |UNKNOWN     |UNKNOWN     |Site with id '2' is not reachable |       False |

status system replication site "2": ERROR
status system replication site "3": UNKNOWN
overall system replication status: ERROR

Local System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

mode: PRIMARY
site id: 1
site name: SiteA
```


#### Test4 - Recovery procedure
{: #ha-rhel-hana-sr-mt-test4-recovery-procedure}

Wait until the secondary HANA instance starts and synchronized again (`SOK`), then cleanup the failed resource actions that are shown in the `pcs status` output.

1. On a cluster node, run the following command.

   ```sh
   pcs resource refresh SAPHana_${SID}_${INSTNO}
   ```
   {: pre}

1. Check the cluster status.

   ```sh
   pcs status --full
   ```
   {: pre}

### Test5 - Testing DR activation on the node that runs the tertiary database
{: #ha-rhel-hana-sr-mt-test5-dr-drill}

Use the following information to test the failure of both nodes in the primary workspace.

#### Test5 - Description
{: #ha-rhel-hana-sr-mt-test5-description}

Simulate a crash of the nodes that run the primary and secondary SAP HANA database.

#### Test5 - Prerequisites
{: #ha-rhel-hana-sr-mt-test5-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both cluster nodes are active.
- Cluster is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=false`.
- Check SAP HANA system replication status:
   - HANA system replication is active and in sync.
   - The primary SAP HANA system runs on NODE1.
   - The secondary SAP HANA system runs on NODE2.
   - The tertiary SAP HANA system runs on NODE3 and is registered with NODE2.

#### Test5 - Test procedure
{: #ha-rhel-hana-sr-mt-test5-procedure}

Crash primary on NODE1 and secondary on NODE2 by sending a *crash* system request on both nodes.

1. On NODE1, run the following command.

   ```sh
   sync; echo c > /proc/sysrq-trigger
   ```
   {: pre}

1. On NODE2, run the following command.

   ```sh
   sync; echo c > /proc/sysrq-trigger
   ```
   {: pre}

1. On NODE3, run the following command to activate the HANA system as the new primary.

   ```sh
   sudo -i -u ${sid}adm -- hdbnsutil -sr_takeover
   ```
   {: pre}

   Sample output:

   ```sh
   # sudo -i -u hdbadm -- hdbnsutil -sr_takeover
   done.
   ```


#### Test5 - Expected behavior
{: #ha-rhel-hana-sr-mt-test5-expected-behavior}

- NODE1 and NODE2 halt immediately.
- After the manual takeover, NODE3 runs the primary SAP HANA system.
- An application, such as SAP NetWeaver, can connect to the SAP HANA system on NODE3.

On NODE3, run the following command to verify that the SAP HANA system runs as primary.

```sh
sudo -i -u ${sid}adm -- hdbnsutil -sr_state
```
{: pre}

Sample output:

```sh
# sudo -i -u hdbadm -- hdbnsutil -sr_state

System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~

online: true

mode: primary
operation mode: primary
site id: 3
site name: SiteC

is source system: true
is secondary/consumer system: false
has secondaries/consumers attached: false
is a takeover active: false
is primary suspended: false

Host Mappings:
~~~~~~~~~~~~~~

cl-hdb-3 -> [SiteC] cl-hdb-3


Site Mappings:
~~~~~~~~~~~~~~
SiteC (primary/primary)

Tier of SiteC: 1

Replication mode of SiteC: primary

Operation mode of SiteC: primary


Hint based routing site:
done.
```


#### Test5 - Recovery procedure
{: #ha-rhel-hana-sr-mt-test5-recovery-procedure}

The recovery procedure after a takeover to the tertiary SAP HANA system is complex and is documented as a separate test in the *Test6* section.

### Test6 - Restoring the original SAP HANA multitier system replication topology
{: #ha-rhel-hana-sr-mt-test6-revert-back}

Use the following information to revert to the original system replication topology after a takeover to the tertiary SAP HANA system.

Check the following SAP documentation.
- [Restore the Original SAP HANA Multitier System Replication Configuration](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/e76baba85d474b168bff1a652d2d0880.html){: external}

#### Test6 - Description
{: #ha-rhel-hana-sr-mt-test6-description}

Reactivate the cluster in the primary workspace and restore the original system replication topology.

#### Test6 - Prerequisites
{: #ha-rhel-hana-sr-mt-test6-prerequisites}

- A two-node RHEL HA Add-On cluster for HANA system replication in the primary workspace.
- Both virtual server instances of the cluster are stopped.
- The primary SAP HANA system runs on NODE3.

#### Test6 - Test procedure
{: #ha-rhel-hana-sr-mt-test6-procedure}

1. Log in to the {{site.data.keyword.cloud}} console and start both NODE1 and NODE2.
1. Wait until both nodes are available again.
1. Ensure that the Red Hat HA Add-On cluster services are stopped on both cluster nodes NODE1 and NODE2.
1. On NODE3, verify that SAP HANA system replication is enabled.

   ```sh
   sudo -i -u ${sid}adm -- hdbnsutil -sr_state
   ```
   {: pre}

1. On NODE1, run the following command to set an environment variable with the hostname of NODE3.

   ```sh
   export NODE3=<Hostname 3>   # Hostname of virtual server instance 3 (production tertiary)
   ```
   {: pre}

2. On NODE1, run the following command to register the SAP HANA system with the primary on NODE3.

   ```sh
   sudo -i -u ${sid}adm -- \
       hdbnsutil -sr_register \
         --name=${DC1} \
         --remoteHost=${NODE3} \
         --remoteInstance=${INSTNO} \
         --replicationMode=async \
         --operationMode=logreplay \
         --online
   ```
   {: pre}

3. On NODE1, check the system replication configuration.

   ```sh
   sudo -i -u ${sid}adm -- hdbnsutil -sr_state
   ```
   {: pre}

   Sample output:

   ```sh

   System Replication State
   ~~~~~~~~~~~~~~~~~~~~~~~~

   online: false

   mode: async
   operation mode: unknown
   site id: 1
   site name: SiteA

   is source system: unknown
   is secondary/consumer system: true
   has secondaries/consumers attached: unknown
   is a takeover active: false
   is primary suspended: false
   is timetravel enabled: false
   replay mode: auto
   active primary site: 3

   primary masters: cl-hdb-3
   done.
   ```


4. On NODE1, start the SAP HANA system to start the system replication.

   ```sh
   sudo -i -u ${sid}adm -- HDB start
   ```
   {: pre}

5. On NODE3, check the system replication status and wait until the secondary on NODE1 is fully synchronized.

   ```sh
   sudo -i -u ${sid}adm -- HDBSettings.sh systemReplicationStatus.py
   ```
   {: pre}

   Sample output:

   ```sh
   # sudo -i -u hdbadm -- HDBSettings.sh systemReplicationStatus.py
   |Database |Host     |Port  |Service Name |Volume ID |Site ID |Site Name |Secondary |Secondary |Secondary |Secondary |Secondary     |Replication |Replication |Replication    |Secondary    |
   |         |         |      |             |          |        |          |Host      |Port      |Site ID   |Site Name |Active Status |Mode        |Status      |Status Details |Fully Synced |
   |-------- |-------- |----- |------------ |--------- |------- |--------- |--------- |--------- |--------- |--------- |------------- |----------- |----------- |-------------- |------------ |
   |SYSTEMDB |cl-hdb-3 |30001 |nameserver   |        1 |      3 |SiteC     |cl-hdb-1  |    30001 |        1 |SiteA     |YES           |ASYNC       |ACTIVE      |               |        True |
   |HDB      |cl-hdb-3 |30007 |xsengine     |        2 |      3 |SiteC     |cl-hdb-1  |    30007 |        1 |SiteA     |YES           |ASYNC       |ACTIVE      |               |        True |
   |HDB      |cl-hdb-3 |30003 |indexserver  |        3 |      3 |SiteC     |cl-hdb-1  |    30003 |        1 |SiteA     |YES           |ASYNC       |ACTIVE      |               |        True |

   status system replication site "1": ACTIVE
   overall system replication status: ACTIVE

   Local System Replication State
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   mode: PRIMARY
   site id: 3
   site name: SiteC
   ```


You need a downtime window to perform the move of the primary role back to NODE1.
All application servers that are connected to NODE3 must be stopped.
{: important}

A *takeover with handshake* suspends all transactions on the primary system on NODE3 and the takeover is only executed when all remaining redo log is available on NODE1.

1. On NODE1, run the following command to takeover the primary role.

   ```sh
   sudo -i -u ${sid}adm -- hdbnsutil -sr_takeover --suspendPrimary
   ```
   {: pre}

1. On NODE1, check that the HANA system runs as primary.

   ```sh
   sudo -i -u ${sid}adm -- hdbnsutil -sr_state
   ```
   {: pre}

1. On NODE3, run the following command to verify the system replication status.

   ```sh
   sudo -i -u ${sid}adm -- HDBSettings.sh systemReplicationStatus.py
   ```
   {: pre}

   ```sh
   # sudo -i -u hdbadm -- HDBSettings.sh systemReplicationStatus.py
   |Database |Host     |Port  |Service Name |Volume ID |Site ID |Site Name |Secondary|Secondary |Secondary |Secondary |Secondary     |Replication |Replication |Replication                      |Secondary    |
   |         |         |      |             |          |        |          |Host     |Port      |Site ID   |Site Name |Active Status |Mode        |Status      |Status Details                   |Fully Synced |
   |-------- |-------- |----- |------------ |--------- |------- |--------- |-------- |--------- |--------- |--------- |------------- |----------- |----------- |-------------------------------- |------------ |
   |SYSTEMDB |cl-hdb-3 |30001 |nameserver   |        1 |      3 |SiteC     |cl-hdb-1 |    30001 |        1 |          |PRIMARY       |            |            |IS PRIMARY (e.g. after takeover) |       False |
   |HDB      |cl-hdb-3 |30007 |xsengine     |        2 |      3 |SiteC     |cl-hdb-1 |    30007 |        1 |          |PRIMARY       |            |            |IS PRIMARY (e.g. after takeover) |       False |
   |HDB      |cl-hdb-3 |30003 |indexserver  |        3 |      3 |SiteC     |cl-hdb-1 |    30003 |        1 |          |PRIMARY       |            |            |IS PRIMARY (e.g. after takeover) |       False |

   status system replication site "1": ERROR
   overall system replication status: ERROR

   Local System Replication State
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   mode: PRIMARY
   site id: 3
   site name: SiteC
   ```


The following summary shows the status after these steps.

- NODE1 runs as primary, but no application is connected.
- NODE2 is up, but SAP HANA is not started.
- NODE3 is up and SAP HANA is blocked in *suspendPrimary* mode.
- Red Hat HA Add-On cluster services are stopped on NODE1 and NODE2.

1. On NODE2, run the following command to register with the primary on NODE1.

   ```sh
   sudo -i -u ${sid}adm -- \
       hdbnsutil -sr_register \
         --name=${DC2} \
         --remoteHost=${NODE1} \
         --remoteInstance=${INSTNO} \
         --replicationMode=syncmem \
         --operationMode=logreplay \
         --online
   ```
   {: pre}

1. On NODE2, start SAP HANA to start the replication.

   ```sh
   sudo -i -u ${sid}adm -- HDB start
   ```
   {: pre}

1. On NODE1, check the system replication status and wait until the secondary on NODE2 is fully synchronized.

   ```sh
   sudo -i -u ${sid}adm -- HDBSettings.sh systemReplicationStatus.py
   ```
   {: pre}

   Sample output:

   ```sh
   # sudo -i -u hdbadm -- HDBSettings.sh systemReplicationStatus.py
   |Database |Host     |Port  |Service Name |Volume ID |Site ID |Site Name |Secondary |Secondary |Secondary |Secondary |Secondary     |Replication |Replication |Replication    |Secondary    |
   |         |         |      |             |          |        |          |Host      |Port      |Site ID   |Site Name |Active Status |Mode        |Status      |Status Details |Fully Synced |
   |-------- |-------- |----- |------------ |--------- |------- |--------- |--------- |--------- |--------- |--------- |------------- |----------- |----------- |-------------- |------------ |
   |SYSTEMDB |cl-hdb-1 |30001 |nameserver   |        1 |      1 |SiteA     |cl-hdb-2  |    30001 |        2 |SiteB     |YES           |SYNC        |ACTIVE      |               |        True |
   |HDB      |cl-hdb-1 |30007 |xsengine     |        2 |      1 |SiteA     |cl-hdb-2  |    30007 |        2 |SiteB     |YES           |SYNC        |ACTIVE      |               |        True |
   |HDB      |cl-hdb-1 |30003 |indexserver  |        3 |      1 |SiteA     |cl-hdb-2  |    30003 |        2 |SiteB     |YES           |SYNC        |ACTIVE      |               |        True |

   status system replication site "2": ACTIVE
   overall system replication status: ACTIVE

   Local System Replication State
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   mode: PRIMARY
   site id: 1
   site name: SiteA
   ```


The following summary shows the status after these steps.

- NODE1 runs as primary, but no application is connected.
- NODE2 runs as secondary.
- NODE3 is up and SAP HANA is blocked in *suspendPrimary* mode.
- Red Hat HA Add-On cluster services are stopped on NODE1 and NODE2.

1. On a cluster node, run the following command to start the cluster.

   ```sh
   pcs cluster start --all
   ```
   {: pre}

1. Check the cluster status and verify that it is fully operational again.

   ```sh
   pcs status --full
   ```
   {: pre}

The following summary shows the status after these steps.

- NODE1 runs as primary.
- NODE2 runs as secondary.
- Red Hat HA Add-On cluster services are started and the cluster manages SAP HANA system replication on NODE1 and NODE2.
- NODE3 is up and SAP HANA is blocked in *suspendPrimary* mode.

1. On NODE2, run the following command to enable it as system replication source site.

   ```sh
   sudo -i -u ${sid}adm -- hdbnsutil -sr_enable
   ```
   {: pre}

   Sample output:

   ```sh
   # sudo -i -u hdbadm -- hdbnsutil -sr_enable
   nameserver is active, proceeding ...
   successfully enabled system as system replication source site
   done.
   ```


1. On NODE2, check the system replication configuration.

   ```sh
   sudo -i -u ${sid}adm -- hdbnsutil -sr_state
   ```
   {: pre}

   Sample output:

   ```sh
   # sudo -i -u hdbadm -- hdbnsutil -sr_state

   System Replication State
   ~~~~~~~~~~~~~~~~~~~~~~~~

   online: true

   mode: sync
   operation mode: logreplay
   site id: 2
   site name: SiteB

   is source system: true
   is secondary/consumer system: true
   has secondaries/consumers attached: false
   is a takeover active: false
   is primary suspended: false
   is timetravel enabled: false
   replay mode: auto
   active primary site: 1

   primary masters: cl-hdb-1

   Host Mappings:
   ~~~~~~~~~~~~~~

   cl-hdb-2 -> [SiteB] cl-hdb-2
   cl-hdb-2 -> [SiteA] cl-hdb-1


   Site Mappings:
   ~~~~~~~~~~~~~~
   SiteA (primary/primary)
       |---SiteB (sync/logreplay)

   Tier of SiteA: 1
   Tier of SiteB: 2

   Replication mode of SiteA: primary
   Replication mode of SiteB: sync

   Operation mode of SiteA: primary
   Operation mode of SiteB: logreplay

   Mapping: SiteA -> SiteB

   Hint based routing site:
   done.
   ```


1. On NODE3, run the following command to register the system with NODE2.

   ```sh
   sudo -i -u ${sid}adm -- \
       hdbnsutil -sr_register \
         --name=${DC3} \
         --remoteHost=${NODE2} \
         --remoteInstance=${INSTNO} \
         --replicationMode=async \
         --operationMode=logreplay \
         --online
   ```
   {: pre}

1. On NODE1, run the following command to verify the new SAP HANA system replication topology.

   ```sh
   sudo -i -u ${sid}adm -- HDBSettings.sh systemReplicationStatus.py
   ```
   {: pre}

   The SAP HANA system on NODE3 at SiteC reappears in the SAP HANA system replication topology.
   When you run the `hdbnsutil -sr_register` command, the system stops and `CONNECTION TIMEOUT` is shown in the output.

   Sample output:

   ```sh
   # sudo -i -u hdbadm -- HDBSettings.sh systemReplicationStatus.py
   |Database |Host     |Port  |Service Name |Volume ID |Site ID |Site Name |Secondary|Secondary |Secondary |Secondary |Secondary          |Replication |Replication |Replication    |Secondary    |
   |         |         |      |             |          |        |          |Host     |Port      |Site ID   |Site Name |Active Status      |Mode        |Status      |Status Details |Fully Synced |
   |-------- |-------- |----- |------------ |--------- |------- |--------- |-------- |--------- |--------- |--------- |------------------ |----------- |----------- |-------------- |------------ |
   |SYSTEMDB |cl-hdb-1 |30001 |nameserver   |        1 |      1 |SiteA     |cl-hdb-2 |    30001 |        2 |SiteB     |YES                |SYNCMEM     |ACTIVE      |               |        True |
   |HDB      |cl-hdb-1 |30007 |xsengine     |        2 |      1 |SiteA     |cl-hdb-2 |    30007 |        2 |SiteB     |YES                |SYNCMEM     |ACTIVE      |               |        True |
   |HDB      |cl-hdb-1 |30003 |indexserver  |        3 |      1 |SiteA     |cl-hdb-2 |    30003 |        2 |SiteB     |YES                |SYNCMEM     |ACTIVE      |               |        True |
   |SYSTEMDB |cl-hdb-2 |30001 |nameserver   |        1 |      2 |SiteB     |cl-hdb-3 |    30001 |        3 |SiteC     |CONNECTION TIMEOUT |ASYNC       |UNKNOWN     |               |       False |
   |HDB      |cl-hdb-2 |30007 |xsengine     |        2 |      2 |SiteB     |cl-hdb-3 |    30007 |        3 |SiteC     |CONNECTION TIMEOUT |ASYNC       |UNKNOWN     |               |       False |
   |HDB      |cl-hdb-2 |30003 |indexserver  |        3 |      2 |SiteB     |cl-hdb-3 |    30003 |        3 |SiteC     |CONNECTION TIMEOUT |ASYNC       |UNKNOWN     |               |       False |

   status system replication site "2": ACTIVE
   status system replication site "3": UNKNOWN
   overall system replication status: UNKNOWN

   Local System Replication State
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   mode: PRIMARY
   site id: 1
   site name: SiteA
   ```


1. On NODE3, run the following command to start the tertiary HANA system.

   ```sh
   sudo -i -u ${sid}adm -- HDB start
   ```
   {: pre}

The following summary shows the final status after these steps.

- NODE1 runs as primary.
- NODE2 runs as secondary.
- Red Hat HA Add-On cluster services are started and the cluster manages SAP HANA system replication on NODE1 and NODE2.
- NODE3 runs as tertiary.

On NODE1, run the following command to verify the SAP HANA system replication topology.

```sh
sudo -i -u ${sid}adm -- HDBSettings.sh systemReplicationStatus.py
```
{: pre}

Sample output:

```sh
# sudo -i -u hdbadm -- HDBSettings.sh systemReplicationStatus.py
|Database |Host     |Port  |Service Name |Volume ID |Site ID |Site Name |Secondary |Secondary |Secondary |Secondary |Secondary     |Replication |Replication |Replication    |Secondary    |
|         |         |      |             |          |        |          |Host      |Port      |Site ID   |Site Name |Active Status |Mode        |Status      |Status Details |Fully Synced |
|-------- |---------|----- |------------ |--------- |------- |--------- |--------- |--------- |--------- |--------- |------------- |----------- |----------- |-------------- |------------ |
|SYSTEMDB |cl-hdb-1 |30001 |nameserver   |        1 |      1 |SiteA     |cl-hdb-2  |    30001 |        2 |SiteB     |YES           |SYNC        |ACTIVE      |               |        True |
|HDB      |cl-hdb-1 |30007 |xsengine     |        2 |      1 |SiteA     |cl-hdb-2  |    30007 |        2 |SiteB     |YES           |SYNC        |ACTIVE      |               |        True |
|HDB      |cl-hdb-1 |30003 |indexserver  |        3 |      1 |SiteA     |cl-hdb-2  |    30003 |        2 |SiteB     |YES           |SYNC        |ACTIVE      |               |        True |
|SYSTEMDB |cl-hdb-2 |30001 |nameserver   |        1 |      2 |SiteB     |cl-hdb-3  |    30001 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30007 |xsengine     |        2 |      2 |SiteB     |cl-hdb-3  |    30007 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30003 |indexserver  |        3 |      2 |SiteB     |cl-hdb-3  |    30003 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |

status system replication site "2": ACTIVE
status system replication site "3": ACTIVE
overall system replication status: ACTIVE

Local System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

mode: PRIMARY
site id: 1
site name: SiteA
```
{: pre}
