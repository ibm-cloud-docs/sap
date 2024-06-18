---
copyright:
  years: 2023, 2024
lastupdated: "2024-06-18"

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Configuring SAP HANA Active/Active (Read Enabled) System Replication in a RHEL HA Add-On cluster
{: #ha-rhel-hana-sr-aa}

The following information describes the configuration of a Red Hat Enterprise Linux (RHEL) HA Add-On cluster for managing *SAP HANA&reg Active-Active (Read Enabled) System Replication*.
The cluster uses virtual server instances in [{{site.data.keyword.powerSysFull}}](https://www.ibm.com/products/power-virtual-server){: external} as cluster nodes.
{: shortdesc}

In an *Active/Active (read enabled)* configuration, SAP HANA system replication allows read access to the database content on the secondary system.

This information is intended for architects and specialists that are planning a high-availability deployment of SAP HANA on {{site.data.keyword.powerSys_notm}}.
{: note}

## Before you begin
{: #ha-rhel-hana-sr-aa-begin}

Review the general requirements, product documentation, support articles, and SAP notes listed in [Implementing High Availability for SAP Applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

## Prerequisites
{: #ha-rhel-hana-sr-aa-prerequisites}

- A Red Hat High Availability cluster is deployed on two virtual server instances in {{site.data.keyword.powerSys_notm}}.
   - Install and set up the RHEL HA Add-On cluster according to [Implementing a RHEL HA Add-On cluster on {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-ha-rhel).
   - Configure and verify fencing as described in the preceding document.
- The virtual server instances need to fulfill hardware and resource requirements for the SAP HANA systems in scope.
   Follow the guidelines in the [Planning the Deployment](/docs/sap?topic=sap-power-vs-planning-items) document.
- The hostnames of the virtual server instances must meet the SAP HANA requirement.
- SAP HANA is installed on both virtual server instances and SAP HANA System Replication is configured.
   The installation of SAP HANA and setup of SAP HANA System Replication is not specific to the {{site.data.keyword.powerSys_notm}} environment, and you need to follow the standard procedures.

## Setting up the Active/Active (read enabled) scenario
{: #ha-rhel-hana-sr-aa-setup}

The Active/Active (read enabled) system replication scenario is an extension of the setup that is described in [Configure SAP HANA Scale-Up System Replication in a RHEL HA Add-On cluster](/docs/sap?topic=sap-ha-rhel-hana-sr).

Complete the setup for the production system System Replication cluster before you continue with the following steps.

### Changing the system replication operation mode to Active/Active (read enabled)
{: #ha-rhel-hana-sr-aa-change-sr-mode}

On the node that is running the secondary instance, run the following command to change the operation mode.

1. Put the cluster in maintenance mode.
   ```sh
   pcs property set maintenance-mode=true
   ```
   {: pre}

1. Stop the secondary SAP HANA instance.
   ```sh
   sudo -i -u ${sid}adm -- \
       HDB stop
   ```
   {: pre}

1. Change the system replication operation mode.
   ```sh
   sudo -i -u ${sid}adm -- \
       hdbnsutil -sr_changeOperationMode --mode=logreplay_readaccess
   ```
   {: pre}

1. Start the secondary SAP HANA instance.
   ```sh
   sudo -i -u ${sid}adm -- \
       HDB start
   ```
   {: pre}

1. Remove the cluster from maintenance mode.
   ```sh
   pcs property set maintenance-mode=false
   ```
   {: pre}

## Configuring cluster resources for an Active/Active (read enabled) scenario
{: #ha-rhel-hana-sr-aa-cfg-cluster-resources}

Use the following information to configure the additional cluster resources that are required for an Active/Active (read enabled) scenario.

### Creating a secondary virtual IP address resource
{: #ha-rhel-hana-sr-aa-create-2nd-ip-address-cluster-resource}

Review the information in [Reserving virtual IP addresses](/docs/sap?topic=sap-ha-vsi#ha-vsi-reserve-virtual-ip-addresses) and reserve a virtual IP address for the secondary.

Use the reserved IP address to create a virtual IP address resource.
This virtual IP address allows clients to connect to the secondary HANA instance for read-only queries.

On a cluster node, assign the reserved IP address to a *VIP_SECONDARY* environment variable and create the virtual IP address cluster resource by running the following commands.

```sh
export VIP_SECONDARY=<reserved IP address for SAP HANA secondary>
```
{: sceen}

```sh
echo $VIP_SECONDARY
```
{: pre}

```sh
pcs resource create vip_s_${SID}_${INSTNO} IPaddr2 ip=$VIP_SECONDARY
```
{: pre}

Check the configured virtual IP address and the cluster status.

```sh
pcs resource config vip_s_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}

### Creating location constraints for the secondary virtual IP address
{: #ha-rhel-hana-sr-aa-create-2nd-ip-address-constraint}

Create a cluster constraint to make sure that the secondary virtual IP address is placed on the cluster node that is running the secondary instance.

On a cluster node, run the following commands.

```sh
pcs constraint location vip_s_${SID}_${INSTNO} rule \
    score=INFINITY hana_${sid}_sync_state eq SOK \
    and hana_${sid}_roles eq 4:S:master1:master:worker:master
```
{: pre}

```sh
pcs constraint location vip_s_${SID}_${INSTNO} rule \
    score=2000 hana_${sid}_sync_state eq PRIM \
    and hana_${sid}_roles eq 4:P:master1:master:worker:master
```
{: pre}

These location constraints establish the following behavior for the second virtual IP resource:

- If both SAP HANA primary and SAP HANA secondary are available, and SAP HANA system replication state is `SOK`, then the secondary virtual IP address is assigned to the node where SAP HANA secondary is active.
- If the SAP HANA secondary node is not available or SAP HANA system replication state is not `SOK`, then the secondary virtual IP is assigned to the node where SAP HANA primary is active.
   When the SAP HANA secondary becomes available and the SAP HANA system replication state is `SOK` again, the second virtual IP address moves back to the node where the SAP HANA secondary is active.
- If SAP HANA primary or the node where it is running becomes unavailable then the SAP HANA secondary takes over the primary role.
   The second virtual IP remains on the node until the other node turns into SAP HANA secondary role and SAP HANA system replication state is `SOK` again.

This behavior maximizes the time that the secondary virtual IP resource is assigned to a node where a healthy SAP HANA instance is running.

The cluster configuration for the Active/Active (read enabled) scenario is complete.

### Checking the cluster configuration
{: #ha-rhel-hana-sr-aa-check-cluster-config}

On a cluster node, run the following command to check the status of the cluster resources.

```sh
pcs status --full
```
{: pre}

Sample output:
```sh
# pcs status --full
Cluster name: H4S_cluster
Cluster Summary:
  * Stack: corosync
  * Current DC: cl-h4s-1 (1) (version 2.0.5-9.el8_4.5-ba59be7122) - partition with quorum
  * Last updated: Mon Jul 31 11:46:11 2023
  * Last change:  Mon Jul 31 11:44:34 2023 by root via crm_attribute on cl-h4s-1
  * 2 nodes configured
  * 7 resource instances configured

Node List:
  * Online: [ cl-h4s-1 (1) cl-h4s-2 (2) ]

Full List of Resources:
  * res_fence_ibm_powervs	(stonith:fence_ibm_powervs):	 Started cl-h4s-1
  * vip_H4S_00_primary	(ocf::heartbeat:IPaddr2):	 Started cl-h4s-1
  * Clone Set: SAPHanaTopology_H4S_00-clone [SAPHanaTopology_H4S_00]:
    * SAPHanaTopology_H4S_00	(ocf::heartbeat:SAPHanaTopology):	 Started cl-h4s-2
    * SAPHanaTopology_H4S_00	(ocf::heartbeat:SAPHanaTopology):	 Started cl-h4s-1
  * Clone Set: SAPHana_H4S_00-clone [SAPHana_H4S_00] (promotable):
    * SAPHana_H4S_00	(ocf::heartbeat:SAPHana):	 Slave cl-h4s-2
    * SAPHana_H4S_00	(ocf::heartbeat:SAPHana):	 Master cl-h4s-1
  * vip_s_H4S_00	(ocf::heartbeat:IPaddr2):	 Started cl-h4s-2

Node Attributes:
  * Node: cl-h4s-1 (1):
    * hana_h4s_clone_state            	: PROMOTED
    * hana_h4s_op_mode                	: logreplay_readaccess
    * hana_h4s_remoteHost             	: cl-h4s-2
    * hana_h4s_roles                  	: 4:P:master1:master:worker:master
    * hana_h4s_site                   	: SiteA
    * hana_h4s_srmode                 	: syncmem
    * hana_h4s_sync_state             	: PRIM
    * hana_h4s_version                	: 2.00.070.00.1679989823
    * hana_h4s_vhost                  	: cl-h4s-1
    * lpa_h4s_lpt                     	: 1690796675
    * master-SAPHana_H4S_00           	: 150
  * Node: cl-h4s-2 (2):
    * hana_h4s_clone_state            	: DEMOTED
    * hana_h4s_op_mode                	: logreplay_readaccess
    * hana_h4s_remoteHost             	: cl-h4s-1
    * hana_h4s_roles                  	: 4:S:master1:master:worker:master
    * hana_h4s_site                   	: SiteB
    * hana_h4s_srmode                 	: syncmem
    * hana_h4s_sync_state             	: SOK
    * hana_h4s_version                	: 2.00.070.00.1679989823
    * hana_h4s_vhost                  	: cl-h4s-2
    * lpa_h4s_lpt                     	: 30
    * master-SAPHana_H4S_00           	: 100

Migration Summary:

Tickets:

PCSD Status:
  cl-h4s-1: Online
  cl-h4s-2: Online

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```
{: screen}

On a cluster node, run the following command to check the defined constraints.

```sh
pcs constraint --full
```
{: pre}

Sample output:

```sh
# pcs constraint --full
Location Constraints:
  Resource: vip_s_H4S_00
    Constraint: location-vip_s_H4S_00
      Rule: boolean-op=and score=INFINITY (id:location-vip_s_H4S_00-rule)
        Expression: hana_h4s_sync_state eq SOK (id:location-vip_s_H4S_00-rule-expr)
        Expression: hana_h4s_roles eq 4:S:master1:master:worker:master (id:location-vip_s_H4S_00-rule-expr-1)
    Constraint: location-vip_s_H4S_00-1
      Rule: boolean-op=and score=2000 (id:location-vip_s_H4S_00-1-rule)
        Expression: hana_h4s_sync_state eq PRIM (id:location-vip_s_H4S_00-1-rule-expr)
        Expression: hana_h4s_roles eq 4:P:master1:master:worker:master (id:location-vip_s_H4S_00-1-rule-expr-1)
Ordering Constraints:
  promote SAPHana_H4S_00-clone then start vip_H4S_00_primary (kind:Mandatory) (id:order-SAPHana_H4S_00-clone-vip_H4S_00_primary-mandatory)
  start SAPHanaTopology_H4S_00-clone then start SAPHana_H4S_00-clone (kind:Mandatory) (non-symmetrical) (id:order-SAPHanaTopology_H4S_00-clone-SAPHana_H4S_00-clone-mandatory)
Colocation Constraints:
  vip_H4S_00_primary with SAPHana_H4S_00-clone (score:2000) (rsc-role:Started) (with-rsc-role:Master) (id:colocation-vip_H4S_00_primary-SAPHana_H4S_00-clone-2000)
Ticket Constraints:
```
{: screen}

### Checking access to the read enabled secondary SAP HANA instance
{: #ha-rhel-hana-sr-aa-check-access-read-enabled}

You can use SAP HANA system replication *Active/Active (read enabled)* to connect to the secondary system for improved overall performance.
Two connection methods are available to access the read enabled secondary HANA instance:

- Explicit read-only connection
   The application opens an explicit connection to the secondary HANA instance.

- Hint-based statement routing
   An application, for example SAP S/4HANA, opens a connection to the primary HANA instance.
   On this connection, SQL statements with system replication-specific hints are first prepared, and then executed.
   During their execution, the SQL statements are automatically routed to the secondary system and processed there.
   For more information about hints, see the [SAP HANA SQL and System Views Reference Guide](https://help.sap.com/docs/SAP_HANA_PLATFORM/4fe29514fd584807ac9f2a04f6754767/b4b0eec1968f41a099c828a4a6c8ca0f.html){: external}.

Set the following two environment variables to the virtual IP addresses for the SAP HANA primary and secondary.

```sh
export VIP_PRIMARY=<virtual IP address of SAP HANA primary>
export VIP_SECONDARY=<virtual IP address of SAP HANA secondary>
```
{: screen}

The commands in the following two sections prompt for the password of the SAP HANA `SYSTEM` user.
The command output shows the hostname and the IP addresses of the SAP HANA system that ran the SQL statement.

#### Checking access by using an explicit read-only connection
{: #ha-rhel-hana-sr-aa-check-access-read-enabled-explicit-connection}

Verify the connection to the secondary instance by using an explicit read-only connection.

On a cluster node, run the following command.

```sh
sudo -i -u ${sid}adm -- \
    hdbsql -n $VIP_SECONDARY -i $INSTNO -d SYSTEMDB -u SYSTEM \
      "select * from m_host_information \
      where key = 'net_hostnames' or key = 'net_ip_addresses'"
```
{: pre}

The sample output shows that the statement ran on the SAP HANA secondary.

```sh
HOST,KEY,VALUE
"cl-h4s-2","net_hostnames","cl-h4s-2"
"cl-h4s-2","net_ip_addresses","10.40.10.132,10.40.10.211"
2 rows selected (overall time 7518 usec; server time 291 usec)
```
{: screen}

#### Checking access by using hint-based statement routing
{: #ha-rhel-hana-sr-aa-check-access-read-enabled-statement-routing}

Verify the connection to the secondary instance by using the hint-based statement routing.

1. Run a connection test by using an explicit connection to the SAP HANA primary without an SQL hint.

   On a cluster node, run the following command.

   ```sh
   sudo -i -u ${sid}adm -- \
       hdbsql -n $VIP_PRIMARY -i $INSTNO -d SYSTEMDB -u SYSTEM \
         "select * from m_host_information \
         where key = 'net_hostnames' or key = 'net_ip_addresses'"
   ```
   {: pre}

   The sample output shows that the statement ran on the SAP HANA primary.

   ```sh
     HOST,KEY,VALUE
   "cl-h4s-1","net_hostnames","cl-h4s-1"
   "cl-h4s-1","net_ip_addresses","10.40.10.162,10.40.10.201"
   2 rows selected (overall time 5239 usec; server time 361 usec)
   ```
   {: screen}

1. Run a connection test by using an explicit connection to the SAP HANA primary and the `result_lag` SQL hint.

   ```sh
   sudo -i -u ${sid}adm -- \
       hdbsql -n $VIP_PRIMARY -i $INSTNO -d SYSTEMDB -u SYSTEM \
         "select * from m_host_information \
         where key = 'net_hostnames' or key = 'net_ip_addresses' \
         with hint(result_lag('hana_sr'))"
   ```
   {: pre}

   The sample output shows that the statement ran on the SAP HANA secondary.

   ```sh
   HOST,KEY,VALUE
   "cl-h4s-2","net_hostnames","cl-h4s-2"
   "cl-h4s-2","net_ip_addresses","10.40.10.132,10.40.10.211"
   2 rows selected (overall time 40.722 msec; server time 16.428 msec)
   ```
   {: screen}

### Enabling the automated registration of the secondary instance
{: #ha-rhel-hana-sr-aa-enable-automated-registration}

You need to set the parameter `AUTOMATED_REGISTER` according to your operational requirements.
If you want to keep the ability to revert to the state of the previous primary SAP HANA instance, then `AUTOMATED_REGISTER=false` avoids an automatic registration of the previous primary as a new secondary.

If you experience an issue with the data after a takeover that was triggered by the cluster, you can manually revert if `AUTOMATED_REGISTER` is set to `false`.

If `AUTOMATED_REGISTER` is set to `true`, the previous primary SAP HANA instance automatically registers as secondary, and cannot be activated on its previous history.
The advantage of the setting `AUTOMATED_REGISTER=true` is that high-availability automatically reestablishes after the failed node reappears in the cluster.

For now, it is recommended to keep `AUTOMATED_REGISTER` on default value `false` until the cluster is fully tested and that you verify that the failover scenarios work as expected.

The `pcs resource update` command is used to modify resource attributes and `pcs resource update SAPHana_${SID}_${INSTNO} AUTOMATED_REGISTER=true` sets the attribute to `true`.
{: tip}

## Testing SAP HANA System Replication cluster
{: #ha-rhel-hana-sr-aa-test-sap-hana-sr-cluster}

It is important to thoroughly test the cluster configuration to make sure that the cluster is working correctly.
The following information provides a few sample failover test scenarios, but is not a complete list of test scenarios.

For example, the description of each test case includes the following information.

- Component that is being tested
- Description of the test
- Prerequisites and the cluster state before you start the failover test
- Test procedure
- Expected behavior and results
- Recovery procedure

### Test1 - Testing failure of the primary database instance
{: #ha-rhel-hana-sr-aa-test-primary-instance-database-failure}

Use the following information to test the failure of the primary database instance.

#### Test1 - Description
{: #ha-rhel-hana-sr-aa-test1-description}

Simulate a crash of the primary SAP HANA database instance that is running on NODE1.

#### Test1 - Prerequisites
{: #ha-rhel-hana-sr-aa-test1-prerequisites}

- A functional two-node RHEL HA Add-On cluster for SAP HANA system replication.
- Both cluster nodes are active.
- Cluster that is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` that is configured with `AUTOMATED_REGISTER=false`.
- Check SAP HANA System Replication status:
   - Primary SAP HANA database is running on NODE1
   - Secondary SAP HANA database is running on NODE2
   - SAP HANA System Replication is activated and in sync

#### Test1 - Test procedure
{: #ha-rhel-hana-sr-aa-test1-procedure}

Crash SAP HANA primary by sending a SIGKILL signal as the user `${sid}adm`.

On NODE1, run the following command.

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test1 - Expected behavior
{: #ha-rhel-hana-sr-aa-test1-expected-behavior}

- SAP HANA primary instance on NODE1 crashes.
- The cluster detects the stopped primary SAP HANA database and marks the resource as `failed`.
- The cluster promotes the secondary SAP HANA database on NODE2 to take over as the new primary.
- The cluster releases the virtual IP address on NODE1, and acquires it on the new primary on NODE2.
- After the takeover, the secondary SAP HANA instance is unavailable and the secondary virtual IP address stays on NODE2.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

#### Test1 - Recovery procedure
{: #ha-rhel-hana-sr-aa-test1-recovery-procedure}

As the cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=false`, the cluster doesn't restart the failed SAP HANA database, and doesn't register it against the new primary.
Which means that the status on the new primary (NODE2) also shows the secondary in status 'CONNECTION TIMEOUT'.

To reregister the previous primary as a new secondary use the following commands.

On NODE1, run the following command.

```sh
sudo -i -u ${sid}adm -- \
    hdbnsutil -sr_register \
      --name=${DC1} \
      --remoteHost=${NODE2} \
      --remoteInstance=00 \
      --replicationMode=sync \
      --operationMode=logreplay_readaccess \
      --online
```
{: pre}

Verify the system replication status:

```sh
sudo -i -u ${sid}adm -- \
    hdbnsutil -sr_state
```
{: pre}

On a cluster node, run the following command to refresh the cluster resource.
This command starts the secondary instance.

```sh
pcs resource refresh SAPHana_${SID}_${INSTNO}
```
{: pre}

When the secondary reaches the synced state (`SOK`), the secondary virtual IP address moves to NODE1.

On a cluster node, run the following command to check the cluster status.

```sh
pcs status --full
```
{: pre}

### Test2 - Testing failure of the node that is running the primary database
{: #ha-rhel-hana-sr-aa-test-primary-instance-server-failure}

Use the following information to test the failure of the node that is running the primary database.

#### Test2 - Description
{: #ha-rhel-hana-sr-aa-test2-description}

Simulate a crash of the node that is running the primary SAP HANA database.

#### Test2 - Preparation
{: #ha-rhel-hana-sr-aa-test2-preparation}

Make sure that the Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.

On NODE1, run the following command.

```sh
pcs resource update SAPHana_${SID}_${INSTNO} AUTOMATED_REGISTER=true
```
{: pre}

Verify the `AUTOMATED_REGISTER` setting in the resource configuration.

```sh
pcs resource config SAPHana_${SID}_${INSTNO} | grep Attributes
```
{: pre}

#### Test2 - Prerequisites
{: #ha-rhel-hana-sr-aa-test2-prerequisites}

- A functional two-node RHEL HA Add-On cluster for SAP HANA system replication.
- Both nodes are active.
- Cluster is started on NODE1 and NODE2.
- Check SAP HANA System Replication status.
   - Primary SAP HANA database is running on NODE2
   - Secondary SAP HANA database is running on NODE1
   - SAP HANA System Replication is activated and in sync
   - Secondary virtual IP address is active on NODE1

#### Test2 - Test procedure
{: #ha-rhel-hana-sr-aa-test2-procedure}

Crash primary on NODE2 by sending a *shutoff* system request.

On NODE2, run the following command.

```sh
sync; echo o > /proc/sysrq-trigger
```
{: pre}

#### Test2 - Expected behavior
{: #ha-rhel-hana-sr-aa-test2-expected-behavior}

- NODE2 shuts down.
- The cluster detects the failed node and sets its state to `OFFLINE`.
- The cluster promotes the secondary SAP HANA database on NODE1 to take over as the new primary.
- The cluster acquires the virtual IP address on NODE1 on the new primary.
- After the takeover, the secondary SAP HANA instance is unavailable and the secondary virtual IP address stays on NODE1.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

#### Test2 - Recovery procedure
{: #ha-rhel-hana-sr-aa-test2-recovery-procedure}

Log in to the {{site.data.keyword.cloud}} Console and start the NODE2 instance.
Wait until NODE2 is available again, then restart the cluster framework.

On NODE2, run the following command.

```sh
pcs cluster start
```
{: pre}

```sh
pcs status --full
```
{: pre}

As a cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`, SAP HANA restarts when NODE2 rejoins the cluster and the former primary reregisters as a secondary.
When the secondary reaches the synced state (`SOK`), the secondary virtual IP address moves to NODE2.

### Test3 - Testing the failure of the secondary database instance
{: #ha-rhel-hana-sr-aa-test-secondary-instance-database-failure}

Use the following information to test the failure of the secondary database instance.

#### Test3 - Description
{: #ha-rhel-hana-sr-aa-test3-description}

Simulate a crash of the secondary SAP HANA database.

#### Test3 - Prerequisites
{: #ha-rhel-hana-sr-aa-test3-prerequisites}

- A functional two-node RHEL HA Add-On cluster for SAP HANA system replication.
- Both nodes are active.
- Cluster is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.
- Check SAP HANA System Replication status:
   - Primary SAP HANA database is running on NODE1
   - Secondary SAP HANA database is running on NODE2
   - SAP HANA System Replication is activated and in sync
   - Secondary virtual IP address is active on NODE2

#### Test3 - Test procedure
{: #ha-rhel-hana-sr-aa-test3-procedure}

Crash SAP HANA secondary by sending a SIGKILL signal as the user `${sid}adm`.

On NODE2, run the following command.

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test3 - Expected behavior
{: #ha-rhel-hana-sr-aa-test3-expected-behavior}

- SAP HANA secondary on NODE2 crashes.
- The cluster detects the stopped secondary SAP HANA database and marks the resource as `failed`.
- The cluster moves the secondary virtual IP address to NODE1.
- The cluster restarts the secondary SAP HANA database.
- The cluster detects that the system replication is in sync again.
- The cluster moves the secondary virtual IP address back to NODE2.

#### Test3 - Recovery procedure
{: #ha-rhel-hana-sr-aa-test3-recovery-procedure}

Wait until the secondary SAP HANA instance starts and syncs again (`SOK`), then cleanup the failed resource actions as shown in `pcs status`.

On a cluster node, run the following commands.

```sh
pcs resource refresh SAPHana_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}

### Test4 - Testing the manual move of a SAPHana resource to another node
{: #ha-rhel-hana-sr-aa-test-manual-move}

Use the following information to test the manual move of a SAPHana resource to another node.

#### Test4 - Description
{: #ha-rhel-hana-sr-aa-test4-description}

Use cluster commands to move the primary instance to the other node for maintenance purposes.

#### Test4 - Prerequisites
{: #ha-rhel-hana-sr-aa-test4-prerequisites}

- A functional two-node RHEL HA Add-On cluster for SAP HANA system replication.
- Both nodes are active.
- Cluster is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.
- Check SAP HANA System Replication status:
   - Primary SAP HANA database is running on NODE1
   - Secondary SAP HANA database is running on NODE2
   - SAP HANA System Replication is activated and in sync
   - Secondary virtual IP address is active on NODE2

#### Test4 - Test procedure
{: #ha-rhel-hana-sr-aa-test4-procedure}

Move SAP HANA primary to other node by using the `pcs resource move` command.

On a cluster node, run the following command.

```sh
pcs resource move SAPHana_${SID}_${INSTNO}-clone
```
{: pre}

Sample output:

```sh
# pcs resource move SAPHana_H4S_00-clone
Warning: Creating location constraint 'cli-ban-SAPHana_H4S_00-clone-on-cl-hdb-1' with a score of -INFINITY for resource SAPHana_H4S_00-clone on cl-hdb-1.
        This will prevent SAPHana_H4S_00-clone from running on cl-hdb-1 until the constraint is removed
        This will be the case even if cl-hdb-1 is the last node in the cluster

```
{: screen}

#### Test4 - Expected behavior
{: #ha-rhel-hana-sr-aa-test4-expected-behavior}

- The cluster creates location constraints to move the resource.
- The cluster triggers a takeover to the secondary SAP HANA database.
- The secondary virtual IP address stays on NODE2.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

#### Test4 - Recovery procedure
{: #ha-rhel-hana-sr-aa-test4-recovery-procedure}

The automatically created location constraints must be removed to allow automatic failover in the future.

Wait until the primary SAP HANA instance is active and remove the constraints.

On a cluster node, run the following command.

```sh
pcs constraint
```
{: pre}

```sh
pcs resource clear SAPHana_${SID}_${INSTNO}-clone
```
{: pre}

```sh
pcs constraint
```
{: pre}

The cluster registers and starts the SAP HANA database as a new secondary instance.
After system replication status is in sync again (`SOK`), the cluster moves the secondary virtual IP address to NODE1.

```sh
pcs status --full
```
{: pre}
