---
copyright:
  years: 2023, 2024
lastupdated: "2024-09-11"

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn

subcollection: sap

---

sap-power-virtual-server-ha-rhel-hana-sr-multitarget.md

{{site.data.keyword.attribute-definition-list}}

# Configuring SAP HANA Multitarget System Replication in a RHEL HA Add-On Cluster
{: #ha-rhel-hana-sr-multitarget}

The following information describes the configuration of a Red Hat Enterprise Linux (RHEL) HA add-on cluster for managing *SAP HANA&reg system replication* in a multitarget replication scenario.
The cluster uses virtual server instances in [{{site.data.keyword.powerSysFull}}](https://www.ibm.com/products/power-virtual-server){: external} as cluster nodes.

You can connect multiple systems in an SAP HANA multitarget system replication topology to achieve a higher level of availability.
A third SAP HANA instance runs on a virtual server instance in IBM {{site.data.keyword.powerSys_notm}} in another workspace.
The resource agents for SAP HANA in the Red Hat Enterprise Linux 8 (RHEL) HA add-on require that the third SAP HANA instance is managed manually and is installed on a virtual server instance outside the cluster.
{: shortdesc}

In a *multitarget system replication* scenario, one secondary SAP HANA system runs on a virtual server instance in the cluster and another secondary HANA system runs on a virtual server instance that is deployed in a *Disaster Recovery (DR) site*.
The *DR site* is implemented in a different IBM {{site.data.keyword.powerSys_notm}} workspace in another geographical location or zone.
The SAP HANA system replication operation mode must be identical for all multitarget replication levels.

A takeover of the secondary system in the *DR site* must be triggered manually.

This information is intended for architects and specialists that are planning a high-availability deployment of SAP HANA on {{site.data.keyword.powerSys_notm}}.
{: note}

## Before you begin
{: #ha-rhel-hana-sr-mtgt-begin}

Review the general requirements, product documentation, support articles, and SAP notes listed in [Implementing High Availability for SAP Applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

## Prerequisites
{: #ha-rhel-hana-sr-mtgt-prerequisites}

- A Red Hat High Availability cluster is deployed on two virtual server instances in one workspace in {{site.data.keyword.powerSys_notm}}.
   Follow the instructions in the following documents.
   - [Implementing a RHEL HA Add-On Cluster on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-ha-rhel).
   - [Configuring SAP HANA Scale-Up System Replication in a RHEL HA Add-On Cluster](/docs/sap?topic=sap-ha-rhel-hana-sr).
- A third virtual server instance is deployed in another workspace in {{site.data.keyword.powerSys_notm}}.
- SAP HANA is installed on the third virtual server instance with the same `SID` and `Instance Number`.
- Optional - you can reserve a virtual IP address for the system on NODE3 as described in [Reserving virtual IP addresses](/docs/sap?topic=sap-ha-vsi#ha-vsi-reserve-virtual-ip-addresses).
   Assigning and unassigning this virtual IP address on NODE3 is a manual task and not part of a cluster operation.

## Setting up a multitarget scenario
{: #ha-rhel-hana-sr-mtgt-setup}

A multitarget scenario is an extension of the setup that is described in [Configuring SAP HANA Scale-Up System Replication in a RHEL HA Add-On Cluster](/docs/sap?topic=sap-ha-rhel-hana-sr).
Make sure that you complete the setup for the system replication cluster before you continue with the following steps.

To simplify the cluster operations, you can set the `AUTOMATED_REGISTER` cluster attribute of the `SAPHana` resource to `true`.
With `AUTOMATED_REGISTER=true`, the cluster performs an automatic registration of the previous primary as a new secondary after the failed node reappears in the cluster.

On a cluster node, run the following command to verify the `AUTOMATED_REGISTER` cluster attribute of the resource.

```sh
pcs resource config SAPHana_${SID}_${INSTNO}
```
{: pre}

Sample output:
```sh
# pcs resource config SAPHana_${SID}_${INSTNO}
 Resource: SAPHana_HDB_00 (class=ocf provider=heartbeat type=SAPHana)
  Attributes: AUTOMATED_REGISTER=true DUPLICATE_PRIMARY_TIMEOUT=900 InstanceNumber=00 PREFER_SITE_TAKEOVER=True SID=HDB
  Operations: demote interval=0s timeout=3600 (SAPHana_HDB_00-demote-interval-0s)
              methods interval=0s timeout=5 (SAPHana_HDB_00-methods-interval-0s)
              monitor interval=121 role=Slave timeout=700 (SAPHana_HDB_00-monitor-interval-121)
              monitor interval=119 role=Master timeout=700 (SAPHana_HDB_00-monitor-interval-119)
              promote interval=0s timeout=3600 (SAPHana_HDB_00-promote-interval-0s)
              reload interval=0s timeout=5 (SAPHana_HDB_00-reload-interval-0s)
              start interval=0s timeout=3600 (SAPHana_HDB_00-start-interval-0s)
              stop interval=0s timeout=3600 (SAPHana_HDB_00-stop-interval-0s)
```
{: screen}

If the `AUTOMATED_REGISTER` cluster attribute is currently set to `false`, use the following command to enable the automatic registration.

```sh
pcs resource update SAPHana_${SID}_${INSTNO} AUTOMATED_REGISTER=true
```
{: pre}

### Providing network connectivity between the workspaces
{: #ha-rhel-hana-sr-mtgt-conn}

1. Use the information in [Creating the workspace](/docs/sap?topic=sap-ha-vsi#ha-vsi-create-workspace) to create another workspace in a different geographic location or region.
1. Create subnets and make sure that the IP ranges don't overlap with any subnet of the workspace that hosts the virtual server instances for the cluster. For more information, see [Creating private network subnets](/docs/sap?topic=sap-ha-vsi#ha-vsi-create-subnets).
1. Set up {{site.data.keyword.cloud}} connections up in both workspaces and activate *Enable IBM Transit Gateway*. For more information, see [Creating Power Virtual Server Cloud Connections](/docs/power-iaas?topic=power-iaas-cloud-connections#create-cloud-connections).
1. Deploy an {{site.data.keyword.cloud_notm}} Transit Gateway to interconnect the two IBM {{site.data.keyword.powerSys_notm}} workspaces.

   {{site.data.keyword.cloud_notm}} Transit Gateway enables the interconnection of IBM {{site.data.keyword.powerSys_notm}}, {{site.data.keyword.cloud_notm}} classic, and Virtual Private Cloud (VPC) infrastructures and keeps data within the {{site.data.keyword.cloud_notm}} networks. For more information about planning and deploying {{site.data.keyword.cloud_notm}} Transit Gateway, see [Planning for {{site.data.keyword.cloud_notm}} Transit Gateway](/docs/transit-gateway?topic=transit-gateway-helpful-tips) and [Ordering {{site.data.keyword.cloud_notm}} Transit Gateway](/docs/transit-gateway?topic=transit-gateway-ordering-transit-gateway).
   {: note}

1. To add the connections to your transit gateway to establish network connectivity between your IBM {{site.data.keyword.powerSys_notm}}, open [{{site.data.keyword.cloud_notm}} console](/login){: external} and log in to your account.
1. Select the Menu icon ![Menu icon](../../icons/icon_hamburger.svg) on the upper left and click **Interconnectivity**.
1. Click **Transit Gateway** on the left navigation pane.
1. Select the name of your transit gateway.

   In the expanded view, click **View details**.
   {: tip}

1. Click **Add connection**.
1. Choose and configure the specific network connections that you want to add to the transit gateway.
1. Choose **Direct Link**, and select the names of your {{site.data.keyword.cloud_notm}} connections.
1. Click **Add** to create a connection.

### Preparing environment variables on NODE3
{: #ha-rhel-hana-sr-mtgt-env}

To simplify the setup, prepare the following environment variables for user ID `root` on NODE3.
These environment variables are used in subsequent commands in the remainder of the instructions.

On NODE3, create a file with the following environment variables.
Then, adapt the variables according to the configuration of your SAP HANA system.

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

You must source this file before you can use the sample commands in the remainder of this document.

For example, if you created a file that is named `sap_dr_site.sh`, run the following command on NODE3 to set the environment variables.

```sh
source sap_dr_site.sh
```
{: pre}

Every time that you start a new terminal session, you must run the previous `source` command.
Alternatively, you can move the environment variables file to the `/etc/profile.d` directory for the duration of the cluster configuration.
In this example, the file is sourced automatically each time you log in to the server.
{: important}

### Verifying network connectivity between the virtual server instances
{: #ha-rhel-hana-sr-mtgt-verify-conn}

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
   {: screen}

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
   {: screen}

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
   {: screen}

### Copying PKI SSFS storage certificate files to NODE3
{: #ha-rhel-hana-sr-mtgt-ssfs}

The SAP HANA 2.0 data and log transmission channels for the replication process require authentication by using the system *PKI SSFS* storage certificate files.
- [2369981 - Required configuration steps for authentication with HANA System Replication](https://me.sap.com/notes/2369981){: external})

The system *PKI SSFS* storage certificate files are stored in `/usr/sap/${SID}/SYS/global/security/rsecssfs/` in subdirectories `data` and `key`.

On NODE3, run the following commands to copy files `SSFS_${SID}.DAT` and `SSFS_${SID}.KEY` from NODE1.

```sh
scp ${NODE1}:/usr/sap/${SID}/SYS/global/security/rsecssfs/data/SSFS_${SID}.DAT /usr/sap/${SID}/SYS/global/security/rsecssfs/data/SSFS_${SID}.DAT
```
{: pre}

```sh
scp ${NODE1}:/usr/sap/${SID}/SYS/global/security/rsecssfs/key/SSFS_${SID}.KEY /usr/sap/${SID}/SYS/global/security/rsecssfs/key/SSFS_${SID}.KEY
```
{: pre}

The copied *PKI SSFS* storage certificates on NODE3 become active during the start of the SAP HANA system.
Therefore, it is recommended to copy the files when the SAP HANA system on NODE3 is stopped.

### Registering NODE3 as a secondary SAP HANA DR system replication system
{: #ha-rhel-hana-sr-mtgt-tier2}

Register the SAP HANA system as a secondary DR system replication instance.

1. On NODE3, stop the SAP HANA system.

   ```sh
   sudo -i -u ${sid}adm -- HDB stop
   ```
   {: pre}

1. On NODE3, register the secondary SAP HANA instance with NODE1.

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

1. On NODE3, start the secondary SAP HANA instance.

   ```sh
   sudo -i -u ${sid}adm -- HDB start
   ```
   {: pre}

### Checking the SAP HANA system replication status
{: #ha-rhel-hana-sr-mtgt-check-sr-status}

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
# sudo -i -u ${sid}adm -- HDBSettings.sh systemReplicationStatus.py
|Database |Host     |Port  |Service Name |Volume ID |Site ID |Site Name |Secondary |Secondary |Secondary |Secondary |Secondary     |Replication |Replication |Replication    |Secondary    |
|         |         |      |             |          |        |          |Host      |Port      |Site ID   |Site Name |Active Status |Mode        |Status      |Status Details |Fully Synced |
|-------- |-------- |----- |------------ |--------- |------- |--------- |--------- |--------- |--------- |--------- |------------- |----------- |----------- |-------------- |------------ |
|SYSTEMDB |cl-hdb-1 |30001 |nameserver   |        1 |      1 |SiteA     |cl-hdb-3  |    30001 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-1 |30007 |xsengine     |        2 |      1 |SiteA     |cl-hdb-3  |    30007 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-1 |30003 |indexserver  |        3 |      1 |SiteA     |cl-hdb-3  |    30003 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|SYSTEMDB |cl-hdb-1 |30001 |nameserver   |        1 |      1 |SiteA     |cl-hdb-2  |    30001 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |
|HDB      |cl-hdb-1 |30007 |xsengine     |        2 |      1 |SiteA     |cl-hdb-2  |    30007 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |
|HDB      |cl-hdb-1 |30003 |indexserver  |        3 |      1 |SiteA     |cl-hdb-2  |    30003 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |

status system replication site "3": ACTIVE
status system replication site "2": ACTIVE
overall system replication status: ACTIVE

Local System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

mode: PRIMARY
site id: 1
site name: SiteA
```
{: screen}

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
    |---SiteC (async/logreplay)
    |---SiteB (syncmem/logreplay)

Tier of SiteA: 1
Tier of SiteC: 2
Tier of SiteB: 2

Replication mode of SiteA: primary
Replication mode of SiteC: async
Replication mode of SiteB: syncmem

Operation mode of SiteA: primary
Operation mode of SiteC: logreplay
Operation mode of SiteB: logreplay

Mapping: SiteA -> SiteC
Mapping: SiteA -> SiteB

Hint based routing site:
done.
```
{: screen}

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
has secondaries/consumers attached: false
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
    |---SiteC (async/logreplay)
    |---SiteB (syncmem/logreplay)

Tier of SiteA: 1
Tier of SiteC: 2
Tier of SiteB: 2

Replication mode of SiteA: primary
Replication mode of SiteC: async
Replication mode of SiteB: syncmem

Operation mode of SiteA: primary
Operation mode of SiteC: logreplay
Operation mode of SiteB: logreplay

Mapping: SiteA -> SiteC
Mapping: SiteA -> SiteB

Hint based routing site:
done.
```
{: screen}

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
active primary site: 1

primary masters: cl-hdb-1

Host Mappings:
~~~~~~~~~~~~~~

cl-hdb-3 -> [SiteC] cl-hdb-3
cl-hdb-3 -> [SiteB] cl-hdb-2
cl-hdb-3 -> [SiteA] cl-hdb-1


Site Mappings:
~~~~~~~~~~~~~~
SiteA (primary/primary)
    |---SiteC (async/logreplay)
    |---SiteB (syncmem/logreplay)

Tier of SiteA: 1
Tier of SiteC: 2
Tier of SiteB: 2

Replication mode of SiteA: primary
Replication mode of SiteC: async
Replication mode of SiteB: syncmem

Operation mode of SiteA: primary
Operation mode of SiteC: logreplay
Operation mode of SiteB: logreplay

Mapping: SiteA -> SiteC
Mapping: SiteA -> SiteB

Hint based routing site:
done.
done.
```
{: screen}

On all nodes, run the following command to check the replication mode and the operation mode.

```sh
sudo -i -u ${sid}adm -- \
    hdbnsutil -sr_state \
        --sapcontrol=1 2>/dev/null | grep -E "site(Operation|Replication)Mode"
```
{: pre}

Sample output:

```sh
# sudo -i -u ${sid}adm -- hdbnsutil -sr_state --sapcontrol=1 2>/dev/null | grep -E "site(Operation|Replication)Mode"
siteReplicationMode/SiteA=primary
siteReplicationMode/SiteB=syncmem
siteOperationMode/SiteA=primary
siteOperationMode/SiteB=logreplay
```
{: screen}

## Enabling automatic registration of secondaries after a takeover
{: #ha-rhel-hana-sr-mtgt-enable-auto-registration}

In multitarget replication scenarios, SAP HANA can automatically reregister the secondaries that were previously registered before a takeover.
To enable this feature, set the parameter `register_secondaries_on_takeover` in the `[system_replication]` section in the `global.ini` file to `true`.
After a failover of an SAP HANA primary system to a secondary, the other secondary system reregisters automatically to the new primary system.

This option must be added to the `global.ini` file on all potential primary sites.

On all three nodes, run the following command to change the parameter.

```sh
sudo -i -u ${sid}adm -- <<EOT
    python \$DIR_INSTANCE/exe/python_support/setParameter.py \
      -set SYSTEM/global.ini/system_replication/register_secondaries_on_takeover=true
EOT
```
{: pre}

Verify the `[system_replication]` section in the `global.ini` configuration file.

```sh
cat /hana/shared/${SID}/global/hdb/custom/config/global.ini
```
{: pre}

## Testing the SAP HANA system replication cluster
{: #ha-rhel-hana-sr-mtgt-test-sap-hana-sr-cluster}

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
{: #ha-rhel-hana-sr-mtgt-test1-primary-instance-database-failure}

Use the following information to test the failure of the primary database instance.

#### Test1 - Description
{: #ha-rhel-hana-sr-mtgt-test1-description}

Simulate a crash of the primary SAP HANA database instance that runs on NODE1.

#### Test1 - Prerequisites
{: #ha-rhel-hana-sr-mtgt-test1-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both cluster nodes are active.
- The cluster is started on NODE1 and NODE2.
- The cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.
- Check SAP HANA system replication status:
   - SAP HANA multitarget system replication is activated and in sync.
   - The primary SAP HANA system runs on NODE1.
   - The secondary SAP HANA system runs on NODE2.
   - Another secondary SAP HANA system runs on NODE3 at the *DR site* and is registered with NODE1.

Check the current system replication status on NODE1.

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
|SYSTEMDB |cl-hdb-1 |30001 |nameserver   |        1 |      1 |SiteA     |cl-hdb-3  |    30001 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-1 |30007 |xsengine     |        2 |      1 |SiteA     |cl-hdb-3  |    30007 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-1 |30003 |indexserver  |        3 |      1 |SiteA     |cl-hdb-3  |    30003 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|SYSTEMDB |cl-hdb-2 |30001 |nameserver   |        1 |      2 |SiteA     |cl-hdb-2  |    30001 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30007 |xsengine     |        2 |      2 |SiteA     |cl-hdb-2  |    30007 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30003 |indexserver  |        3 |      2 |SiteA     |cl-hdb-2  |    30003 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |

status system replication site "3": ACTIVE
status system replication site "2": ACTIVE
overall system replication status: ACTIVE

Local System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

mode: PRIMARY
site id: 1
site name: SiteA
```
{: screen}


#### Test1 - Test procedure
{: #ha-rhel-hana-sr-mtgt-test1-procedure}

Crash SAP HANA primary by sending a SIGKILL signal as user `${sid}adm`.

On NODE1, run the following command.

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test1 - Expected behavior
{: #ha-rhel-hana-sr-mtgt-test1-expected-behavior}

- The SAP HANA primary instance on NODE1 crashes.
- The cluster detects the stopped primary and marks the resource as `undefined`.
- The cluster promotes the secondary SAP HANA system on NODE2, which takes over as primary.
- The cluster releases the virtual IP address on NODE1, and acquires it on the primary on NODE2.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.
- The secondary HANA system that runs on NODE3 at the *DR site* is automatically reregistered to the new primary that runs on NODE2.
- The cluster waits until the primary on NODE2 is fully active and registers the failed instance on NODE1 as a secondary.
- The cluster starts the secondary HANA instance on NODE1.

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
  * Last updated: Mon Oct  9 10:46:59 2023
  * Last change:  Mon Oct  9 10:46:54 2023 by root via crm_attribute on cl-hdb-2
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
    * SAPHana_HDB_00    (ocf::heartbeat:SAPHana):        Slave cl-hdb-1
    * SAPHana_HDB_00    (ocf::heartbeat:SAPHana):        Master cl-hdb-2

Node Attributes:
  * Node: cl-hdb-1 (1):
    * hana_hdb_clone_state              : DEMOTED
    * hana_hdb_op_mode                  : logreplay
    * hana_hdb_remoteHost               : cl-hdb-2
    * hana_hdb_roles                    : 4:S:master1:master:worker:master
    * hana_hdb_site                     : SiteA
    * hana_hdb_sra                      : -
    * hana_hdb_srah                     : -
    * hana_hdb_srmode                   : syncmem
    * hana_hdb_sync_state               : SOK
    * hana_hdb_version                  : 2.00.070.00
    * hana_hdb_vhost                    : cl-hdb-1
    * lpa_hdb_lpt                       : 30
    * master-SAPHana_HDB_00             : 100
  * Node: cl-hdb-2 (2):
    * hana_hdb_clone_state              : PROMOTED
    * hana_hdb_op_mode                  : logreplay
    * hana_hdb_remoteHost               : cl-hdb-1
    * hana_hdb_roles                    : 4:P:master1:master:worker:master
    * hana_hdb_site                     : SiteB
    * hana_hdb_sra                      : -
    * hana_hdb_srah                     : -
    * hana_hdb_srmode                   : syncmem
    * hana_hdb_sync_state               : PRIM
    * hana_hdb_version                  : 2.00.070.00
    * hana_hdb_vhost                    : cl-hdb-2
    * lpa_hdb_lpt                       : 1696841214
    * master-SAPHana_HDB_00             : 150

Migration Summary:
  * Node: cl-hdb-1 (1):
    * SAPHana_HDB_00: migration-threshold=5000 fail-count=1 last-failure='Mon Oct  9 10:39:58 2023'

Failed Resource Actions:
  * SAPHana_HDB_00_monitor_119000 on cl-hdb-1 'master (failed)' (9): call=31, status='complete', exitreason='', last-rc-change='2023-10-09 10:39:58 +02:00', queued=0ms, exec=0ms

Tickets:

PCSD Status:
  cl-hdb-1: Online
  cl-hdb-2: Online

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```
{: screen}

On NODE2, run the following command to check the system replication status.

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
|SYSTEMDB |cl-hdb-2 |30001 |nameserver   |        1 |      2 |SiteB     |cl-hdb-3  |    30001 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30007 |xsengine     |        2 |      2 |SiteB     |cl-hdb-3  |    30007 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30003 |indexserver  |        3 |      2 |SiteB     |cl-hdb-3  |    30003 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|SYSTEMDB |cl-hdb-2 |30001 |nameserver   |        1 |      2 |SiteB     |cl-hdb-1  |    30001 |        1 |SiteA     |YES           |SYNCMEM     |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30007 |xsengine     |        2 |      2 |SiteB     |cl-hdb-1  |    30007 |        1 |SiteA     |YES           |SYNCMEM     |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30003 |indexserver  |        3 |      2 |SiteB     |cl-hdb-1  |    30003 |        1 |SiteA     |YES           |SYNCMEM     |ACTIVE      |               |        True |

status system replication site "3": ACTIVE
status system replication site "1": ACTIVE
overall system replication status: ACTIVE

Local System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

mode: PRIMARY
site id: 2
site name: SiteB
```
{: screen}

The SAP HANA primary runs on NODE2 at `SiteB`.
The secondary on NODE3 is automatically reregistered to the new primary that runs on NODE2.
As the cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`, the cluster registers the SAP HANA system on NODE1 automatically as a secondary to the primary on NODE2.

### Test2 - Testing the manual move of a SAPHana resource to another node
{: #ha-rhel-hana-sr-mtgt-test2-manual-move}

Use the following information to test the manual move of the SAPHana resource to another node.

#### Test2 - Description
{: #ha-rhel-hana-sr-mtgt-test2-description}

Use cluster commands to move the primary instance to the other cluster node.

#### Test2 - Prerequisites
{: #ha-rhel-hana-sr-mtgt-test2-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both cluster nodes are active.
- The cluster is started on NODE1 and NODE2.
- The cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.
- Check SAP HANA system replication status:
   - HANA system replication is activated and in sync.
   - The primary SAP HANA system runs on NODE2.
   - The secondary SAP HANA system runs on NODE1.
   - Another secondary SAP HANA system runs on NODE3 at the *DR site* and is registered with NODE2.

#### Test2 - Test procedure
{: #ha-rhel-hana-sr-mtgt-test2-procedure}

1. On a cluster node, run the following command to move the primary back to NODE1.

   ```sh
   pcs resource move SAPHana_${SID}_${INSTNO}-clone
   ```
   {: pre}

   Sample output:

   ```sh
   # pcs resource move SAPHana_${SID}_${INSTNO}-clone
   Warning: Creating location constraint 'cli-ban-SAPHana_HDB_00-clone-on-cl-hdb-2' with a score of -INFINITY for resource SAPHana_HDB_00-clone on cl-hdb-2.
        This will prevent SAPHana_HDB_00-clone from running on cl-hdb-2 until the constraint is removed
        This will be the case even if cl-hdb-2 is the last node in the cluster
   ```
   {: screen}

   After the primary is active on NODE1, SAP HANA automatically reregisters the instance on NODE3 as a secondary to NODE1.

1. Wait until the primary is up on NODE1.
   Then, remove the location constraint.

   ```sh
   pcs resource clear SAPHana_${SID}_${INSTNO}-clone
   ```
   {: pre}

   Sample output:
   ```sh
   # pcs resource clear SAPHana_${SID}_${INSTNO}-clone
   Removing constraint: cli-ban-SAPHana_HDB_00-clone-on-cl-hdb-2
   ```
   {: screen}

   This command clears the location constraint that was created by the move command.
   The cluster starts the SAP HANA system on NODE2.

1. Verify the system replication status on all three nodes as described in [Checking the SAP HANA system replication status](#ha-rhel-hana-sr-mtgt-check-sr-status).

#### Test2 - Expected behavior
{: #ha-rhel-hana-sr-mtgt-test2-expected-behavior}

- The cluster creates a location constraint to move the resource.
- The cluster triggers a takeover to the secondary HANA system on NODE1.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.
- Register NODE2 with the primary on NODE1.
- Run `pcs resource clear` command to remove the location constraint.
   This command triggers the start of the secondary instance on NODE2.
- The secondary HANA system that runs on NODE3 at the *DR site* is automatically reregistered to the new primary that runs on NODE1.

#### Test2 - Recovery procedure
{: #ha-rhel-hana-sr-mtgt-test2-recovery-procedure}

No recovery procedure is required.
The test sequence reestablished the initial SAP HANA multitarget system replication topology.

### Test3 - Testing failure of node that runs the primary database
{: #ha-rhel-hana-sr-mtgt-test3-primary-instance-server-failure}

Use the following information to test the failure of the node that runs the primary database.

#### Test3 - Description
{: #ha-rhel-hana-sr-mtgt-test3-description}

Simulate a crash of the node that runs the primary HANA database.

#### Test3 - Prerequisites
{: #ha-rhel-hana-sr-mtgt-test3-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both cluster nodes are active.
- The cluster is started on NODE1 and NODE2.
- The cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.
- Check SAP HANA system replication status:
   - HANA system replication is activated and in sync.
   - The primary SAP HANA system runs on NODE1.
   - The secondary SAP HANA system runs on NODE2.
   - Another secondary SAP HANA system runs on NODE3 at the *DR site* and is registered with NODE1.

#### Test3 - Test procedure
{: #ha-rhel-hana-sr-mtgt-test3-procedure}

Crash the primary on NODE1 by sending a *crash* system request.

On NODE1, run the following command.

```sh
sync; echo c > /proc/sysrq-trigger
```
{: pre}

#### Test3 - Expected behavior
{: #ha-rhel-hana-sr-mtgt-test3-expected-behavior}

- NODE1 crashes.
- The cluster detects the failed node and sets its state to `OFFLINE`.
- The cluster promotes the secondary HANA database on NODE2 to take over as the new primary.
- The cluster acquires the virtual IP address on NODE2.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.
- The secondary SAP HANA system that runs on NODE3 at the *DR site* is automatically reregistered to NODE2.

Verify the SAP HANA system replication status on NODE2.

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
|SYSTEMDB |cl-hdb-2 |30001 |nameserver   |        1 |      2 |SiteB     |cl-hdb-3  |    30001 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30007 |xsengine     |        2 |      2 |SiteB     |cl-hdb-3  |    30007 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
|HDB      |cl-hdb-2 |30003 |indexserver  |        3 |      2 |SiteB     |cl-hdb-3  |    30003 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |

status system replication site "3": ACTIVE
overall system replication status: ACTIVE

Local System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

mode: PRIMARY
site id: 2
site name: SiteB
```
{: screen}

#### Test3 - Recovery procedure
{: #ha-rhel-hana-sr-mtgt-test3-recovery-procedure}

1. Log in to the {{site.data.keyword.cloud_notm}} console and start NODE1.

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
   |Database |Host     |Port  |Service Name |Volume ID |Site ID |Site Name |Secondary |Secondary |Secondary |Secondary |Secondary     |Replication |Replication |Replication    |Secondary    |
   |         |         |      |             |          |        |          |Host      |Port      |Site ID   |Site Name |Active Status |Mode        |Status      |Status Details |Fully Synced |
   |-------- |-------- |----- |------------ |--------- |------- |--------- |--------- |--------- |--------- |--------- |------------- |----------- |----------- |-------------- |------------ |
   |SYSTEMDB |cl-hdb-2 |30001 |nameserver   |        1 |      2 |SiteB     |cl-hdb-3  |    30001 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
   |HDB      |cl-hdb-2 |30007 |xsengine     |        2 |      2 |SiteB     |cl-hdb-3  |    30007 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
   |HDB      |cl-hdb-2 |30003 |indexserver  |        3 |      2 |SiteB     |cl-hdb-3  |    30003 |        3 |SiteC     |YES           |ASYNC       |ACTIVE      |               |        True |
   |SYSTEMDB |cl-hdb-2 |30001 |nameserver   |        1 |      2 |SiteB     |cl-hdb-1  |    30001 |        1 |SiteA     |YES           |SYNCMEM     |ACTIVE      |               |        True |
   |HDB      |cl-hdb-2 |30007 |xsengine     |        2 |      2 |SiteB     |cl-hdb-1  |    30007 |        1 |SiteA     |YES           |SYNCMEM     |ACTIVE      |               |        True |
   |HDB      |cl-hdb-2 |30003 |indexserver  |        3 |      2 |SiteB     |cl-hdb-1  |    30003 |        1 |SiteA     |YES           |SYNCMEM     |ACTIVE      |               |        True |

   status system replication site "3": ACTIVE
   status system replication site "1": ACTIVE
   overall system replication status: ACTIVE

   Local System Replication State
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   mode: PRIMARY
   site id: 2
   site name: SiteB
   ```
   {: screen}

1. Run the steps in [Test2 - Test the manual move of SAP Hana resource to another node](#ha-rhel-hana-sr-mtgt-test2-manual-move) to revert to the initial topology.

### Test4 - Testing DR activation on the node that runs at the DR site
{: #ha-rhel-hana-sr-mtgt-test5-dr-drill}

Use the following information to test the failure of both nodes in the primary workspace.

#### Test4 - Description
{: #ha-rhel-hana-sr-mtgt-test5-description}

Simulate a crash of the nodes that run the primary and secondary SAP HANA databases.

#### Test4 - Prerequisites
{: #ha-rhel-hana-sr-mtgt-test5-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both cluster nodes are active.
- The cluster is started on NODE1 and NODE2.
- The cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.
- Check SAP HANA system replication status:
   - SAP HANA multitarget system replication is activated and in sync.
   - The primary SAP HANA system runs on NODE1.
   - The secondary SAP HANA system runs on NODE2.
   - Another secondary SAP HANA system runs on NODE3 at the *DR site* and is registered with NODE1.

#### Test4 - Test procedure
{: #ha-rhel-hana-sr-mtgt-test5-procedure}

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

1. On NODE3, run the following command to activate the HANA system as primary.

   ```sh
   sudo -i -u ${sid}adm -- hdbnsutil -sr_takeover
   ```
   {: pre}

   Sample output:

   ```sh
   # sudo -i -u hdbadm -- hdbnsutil -sr_takeover
   done.
   ```
   {: screen}

#### Test4 - Expected behavior
{: #ha-rhel-hana-sr-mtgt-test5-expected-behavior}

- NODE1 and NODE2 halt immediately.
- After the manual takeover, NODE3 runs the primary SAP HANA system.
- An application, such as SAP NetWeaver, can connect to the SAP HANA system on NODE3.

NODE3 is not part of the cluster and does not takeover the virtual IP address after a HANA system replication takeover.
The start up of application servers that connect to NODE3 at the *DR site* requires extra effort, which is not described in this document.
{: important}

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
cl-hdb-3 -> [SiteB] cl-hdb-2


Site Mappings:
~~~~~~~~~~~~~~
SiteC (primary/primary)

Tier of SiteC: 1

Replication mode of SiteC: primary

Operation mode of SiteC: primary


Hint based routing site:
done.

```
{: screen}

#### Test4 - Recovery procedure
{: #ha-rhel-hana-sr-mtgt-test5-recovery-procedure}

The recovery procedure after a takeover to the *DR site* is complex and is documented as a separate test in the *Test5* section.

### Test5 - Restoring the original SAP HANA multitarget system replication topology
{: #ha-rhel-hana-sr-mtgt-test6-revert-back}

Use the following information to revert to the original system replication topology after a takeover to the SAP HANA system that runs at the *DR site*.

Check the following SAP documentation.
- [Restore the Original SAP HANA multitarget System Replication Configuration](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/e76baba85d474b168bff1a652d2d0880.html){: external}

#### Test5 - Description
{: #ha-rhel-hana-sr-mtgt-test6-description}

Restore the original system replication topology and reactivate the cluster in the primary workspace.

#### Test5 - Prerequisites
{: #ha-rhel-hana-sr-mtgt-test6-prerequisites}

- A two-node RHEL HA Add-On cluster for HANA system replication in the primary workspace.
- Both virtual server instances of the cluster are stopped.
- The primary SAP HANA system runs on NODE3 at the *DR site*.

#### Test5 - Test procedure
{: #ha-rhel-hana-sr-mtgt-test6-procedure}

1. Restart virtual server instances in the primary workspace.

   1. Log in to the {{site.data.keyword.cloud_notm}} console and start both NODE1 and NODE2.
   1. Wait until both nodes are available.
   1. Make sure that the Red Hat HA Add-On cluster services are stopped on both cluster nodes.

1. Register the SAP HANA system on NODE1 as a secondary.

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

   1. On NODE1, run the following command to register the SAP HANA system with the primary on NODE3.

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

   1. On NODE1, check the system replication configuration.

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
      {: screen}

   1. On NODE1, start the SAP HANA system to start the system replication.

      ```sh
      sudo -i -u ${sid}adm -- HDB start
      ```
      {: pre}

   1. On NODE3, check the system replication status and wait until the secondary on NODE1 is fully synchronized.

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
      {: screen}

1. Initiate a fallback to the primary workspace.

   You need a downtime window to perform the move of the primary role back to NODE1.
   {: important}

   To optimize the downtime window, the SAP HANA system on NODE2 can be registered as secondary to NODE3 now before the downtime window.
   The drawback is that a higher amount of data is transferred between the two {{site.data.keyword.powerSys_notm}} workspaces.

   In the following, the SAP HANA system on NODE2 is registered as secondary to NODE1 after NODE1 becomes primary again.

   1. Stop all applications and SAP application servers that are connected to NODE3.

   1. On NODE1, run the following command to takeover the primary role.

      A *takeover with handshake* suspends all transactions on the primary system on NODE3 and the takeover is only executed when all remaining redo log is available on NODE1.

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
      sudo -i -u ${sid}adm -- hdbnsutil -sr_state
      ```
      {: pre}

      Sample output:

      ```sh
      # sudo -i -u ${sid}adm -- hdbnsutil -sr_state

      System Replication State
      ~~~~~~~~~~~~~~~~~~~~~~~~

      online: true

      SUSPEND PRIMARY ACTIVE
      mode: primary
      operation mode: primary
      site id: 3
      site name: SiteC

      is source system: true
      is secondary/consumer system: false
      has secondaries/consumers attached: true
      is a takeover active: false
      is primary suspended: true

      Host Mappings:
      ~~~~~~~~~~~~~~

      cl-hdb-3 -> [SiteC] cl-hdb-3
      cl-hdb-3 -> [SiteB] cl-hdb-2
      cl-hdb-3 -> [SiteA] cl-hdb-1


      Site Mappings:
      ~~~~~~~~~~~~~~
      SiteC (primary/primary)
          |---SiteA (async/logreplay)

      Tier of SiteC: 1
      Tier of SiteA: 2

      Replication mode of SiteC: primary
      Replication mode of SiteA: async

      Operation mode of SiteC: primary
      Operation mode of SiteA: logreplay

      Mapping: SiteC -> SiteA

      Hint based routing site:
      done.
      ```
      {: screen}

   The following summary shows the status after these steps.

   - NODE1 runs as primary, but no application is connected.
   - NODE2 is up, but SAP HANA is not started.
   - NODE3 is up and SAP HANA is blocked in *suspendPrimary* mode.
   - Red Hat HA Add-On cluster services are stopped on NODE1 and NODE2.

1. Register the SAP HANA system on NODE2 as a secondary.

   1. On NODE2, run the following command to register the SAP HANA instance with the primary on NODE1.

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
      |SYSTEMDB |cl-hdb-1 |30001 |nameserver   |        1 |      1 |SiteA     |cl-hdb-2  |    30001 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |
      |HDB      |cl-hdb-1 |30007 |xsengine     |        2 |      1 |SiteA     |cl-hdb-2  |    30007 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |
      |HDB      |cl-hdb-1 |30003 |indexserver  |        3 |      1 |SiteA     |cl-hdb-2  |    30003 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |

      status system replication site "2": ACTIVE
      overall system replication status: ACTIVE

      Local System Replication State
      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      mode: PRIMARY
      site id: 1
      site name: SiteA
      ```
      {: screen}

   The following summary shows the status after these steps.

   - NODE1 runs as primary, but no application is connected.
   - NODE2 runs as secondary.
   - NODE3 is up and SAP HANA is blocked in *suspendPrimary* mode.
   - Red Hat HA Add-On cluster services are stopped on NODE1 and NODE2.

1. Restart the cluster on NODE1 and NODE2.

   1. Stop the SAP HANA systems on NODE1 and NODE2.

      On NODE1, run
      ```sh
      sudo -i -u ${sid}adm -- HDB stop
      ```
      {: pre}

      On NODE2, run
      ```sh
      sudo -i -u ${sid}adm -- HDB stop
      ```
      {: pre}


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

   1. On NODE1, check the system replication status.

      ```sh
      sudo -i -u ${sid}adm -- HDBSettings.sh systemReplicationStatus.py
      ```
      {: pre}

      Sample output:

      ```sh
      # sudo -i -u ${sid}adm -- HDBSettings.sh systemReplicationStatus.py
      |Database |Host     |Port  |Service Name |Volume ID |Site ID |Site Name |Secondary |Secondary |Secondary |Secondary |Secondary     |Replication |Replication |Replication    |Secondary    |
      |         |         |      |             |          |        |          |Host      |Port      |Site ID   |Site Name |Active Status |Mode        |Status      |Status Details |Fully Synced |
      |-------- |-------- |----- |------------ |--------- |------- |--------- |--------- |--------- |--------- |--------- |------------- |----------- |----------- |-------------- |------------ |
      |SYSTEMDB |cl-hdb-1 |30001 |nameserver   |        1 |      1 |SiteA     |cl-hdb-2  |    30001 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |
      |HDB      |cl-hdb-1 |30007 |xsengine     |        2 |      1 |SiteA     |cl-hdb-2  |    30007 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |
      |HDB      |cl-hdb-1 |30003 |indexserver  |        3 |      1 |SiteA     |cl-hdb-2  |    30003 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |

      status system replication site "2": ACTIVE
      overall system replication status: ACTIVE

      Local System Replication State
      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      mode: PRIMARY
      site id: 1
      site name: SiteA
      ```
      {: screen}

   The following summary shows the status after these steps.

   - NODE1 runs as primary.
   - NODE2 runs as secondary.
   - Red Hat HA Add-On cluster services are started and the cluster manages SAP HANA system replication on NODE1 and NODE2.
   - NODE3 is up and SAP HANA is blocked in *suspendPrimary* mode.

1. Register the SAP HANA system on NODE3 as a secondary.

   1. On NODE3, run the following command to register the system with NODE1.

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

   1. On NODE1, run the following command to verify the new SAP HANA system replication topology.

      ```sh
      sudo -i -u ${sid}adm -- HDBSettings.sh systemReplicationStatus.py
      ```
      {: pre}

      The previous `hdbnsutil -sr_register` command triggers a restart of the SAP HANA system.
      During this restart, you might observe a `CONNECTION TIMEOUT` status in the output.

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
      {: screen}

      In case the system does not automatically restart after the `hdbnsutil -sr_register` command, you need to stop and start it manually.

      The following is a sample output of such a situation.
      The replication status of NODE3 shows `IS PRIMARY (e.g. after takeover)` and it does not change when you check the status multiple times.

      ```sh
      # sudo -i -u ${sid}adm -- HDBSettings.sh systemReplicationStatus.py
      |Database |Host     |Port  |Service Name |Volume ID |Site ID |Site Name |Secondary|Secondary |Secondary |Secondary |Secondary     |Replication |Replication |Replication                      |Secondary    |
      |         |         |      |             |          |        |          |Host     |Port      |Site ID   |Site Name |Active Status |Mode        |Status      |Status Details                   |Fully Synced |
      |-------- |-------- |----- |------------ |--------- |------- |--------- |-------- |--------- |--------- |--------- |------------- |----------- |----------- |-------------------------------- |------------ |
      |SYSTEMDB |cl-hdb-1 |30001 |nameserver   |        1 |      1 |SiteA     |cl-hdb-3 |    30001 |        3 |          |PRIMARY       |            |            |IS PRIMARY (e.g. after takeover) |       False |
      |HDB      |cl-hdb-1 |30007 |xsengine     |        2 |      1 |SiteA     |cl-hdb-3 |    30007 |        3 |          |PRIMARY       |            |            |IS PRIMARY (e.g. after takeover) |       False |
      |HDB      |cl-hdb-1 |30003 |indexserver  |        3 |      1 |SiteA     |cl-hdb-3 |    30003 |        3 |          |PRIMARY       |            |            |IS PRIMARY (e.g. after takeover) |       False |
      |SYSTEMDB |cl-hdb-1 |30001 |nameserver   |        1 |      1 |SiteA     |cl-hdb-2 |    30001 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |                                 |        True |
      |HDB      |cl-hdb-1 |30007 |xsengine     |        2 |      1 |SiteA     |cl-hdb-2 |    30007 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |                                 |        True |
      |HDB      |cl-hdb-1 |30003 |indexserver  |        3 |      1 |SiteA     |cl-hdb-2 |    30003 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |                                 |        True |

      status system replication site "3": ERROR
      status system replication site "2": ACTIVE
      overall system replication status: ERROR

      Local System Replication State
      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      mode: PRIMARY
      site id: 1
      site name: SiteA
      ```
      {: screen}

      On NODE3, run the following command to restart the secondary HANA system.

      ```sh
      sudo -i -u ${sid}adm -- HDB restart
      ```
      {: pre}

   The following summary shows the final status after these steps.

   - NODE1 runs as primary.
   - NODE2 runs as secondary.
   - NODE3 runs as another secondary at the *DR site*.
   - NODE2 and NODE3 are both registered to NODE1.
   - Red Hat HA Add-On cluster services are started and the cluster manages SAP HANA system replication on NODE1 and NODE2.

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
   |SYSTEMDB |cl-hdb-1 |30001 |nameserver   |        1 |      1 |SiteA     |cl-hdb-2  |    30001 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |
   |HDB      |cl-hdb-1 |30007 |xsengine     |        2 |      1 |SiteA     |cl-hdb-2  |    30007 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |
   |HDB      |cl-hdb-1 |30003 |indexserver  |        3 |      1 |SiteA     |cl-hdb-2  |    30003 |        2 |SiteB     |YES           |SYNCMEM     |ACTIVE      |               |        True |
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
