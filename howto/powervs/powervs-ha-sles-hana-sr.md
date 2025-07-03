---
copyright:
  years: 2025
lastupdated: "2025-07-03"
keywords: SAP, SAP-Certified Infrastructure, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, SLES, SUSE, SUSE High Availability Extension, SLES HAE
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Configuring SAP HANA scale-up system replication in a SUSE Linux Enterprise High Availability Extension cluster
{: #ha-sles-hana-sr}

The following information describes the configuration of a SUSE Enterprise Linux Server (SLES) High Availability Extension (HAE)cluster for managing *SAP HANA Scale-Up System Replication*.
The cluster uses virtual server instances in [{{site.data.keyword.powerSysFull}}](https://www.ibm.com/products/power-virtual-server){: external} as cluster nodes.
{: shortdesc}

The instructions describe how to automate SAP HANA Scale-Up System Replication for a single database deployment in a performance-optimized scenario on a SLES HA Extension cluster.

This information is intended for architects and specialists that are planning a high-availability deployment of SAP HANA on {{site.data.keyword.powerSys_notm}}.
{: note}

## Before you begin
{: #ha-sles-hana-sr-begin}

Review the general requirements, product documentation, support articles, and SAP notes listed in [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-sles-refs).

## Prerequisites
{: #ha-sles-hana-sr-prerequisites}

- A SUSE High Availability cluster is deployed on two virtual server instances in {{site.data.keyword.powerSys_notm}}.
   - Install and set up the SLES High Availability Extension cluster according to [Implementing a SUSE Linux Enterprise Server high availability cluster](/docs/sap?topic=sap-ha-sles).
   - Configure and verify fencing as described in the preceding document.
- The virtual server instances need to fulfill hardware and resource requirements for the SAP HANA systems in scope. Follow the guidelines in [Planning your deployment](/docs/sap?topic=sap-powervs-planning-items).
- The hostnames of the virtual server instances must meet the SAP HANA requirement.
- SAP HANA is installed on both virtual server instances and SAP HANA System Replication is configured Installing of SAP HANA and setting up HANA System Replication is not specific to the {{site.data.keyword.powerSys_notm}} environment. You need to follow the standard installation and set up procedures.
- A valid *SUSE Linux Enterprise Server for SAP Applications* license is required to enable the repositories that you need to install SAP HANA and the resource agents for HA configurations.
- See the [Prerequisites](https://documentation.suse.com/sles-sap/15-SP6/html/SLES-SAP-guide/cha-cluster.html#sec-hana-cluster-prerequisite){: external} chapter in the *SUSE Linux Enterprise Server for SAP applications* guide.

## Configuring SAP HANA System Replication in a SLES HA Extension cluster on IBM {{site.data.keyword.powerSys_notm}}
{: #ha-sles-hana-sr-configure-sr}

The instructions are based on the SUSE product documentation and articles that are listed in [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-sles-refs).

### Preparing environment variables
{: #ha-sles-hana-sr-prepare-environment-variables}

To simplify the setup, prepare the following environment variables for root on both nodes.
These environment variables are used with later operating system commands in this information.

On both nodes, set the following environment variables.

```sh
# General settings
export SID=<SID>            # SAP HANA System ID (uppercase)
export sid=<sid>            # SAP HANA System ID (lowercase)
export INSTNO=<INSTNO>      # SAP HANA instance number

# Cluster node 1
export NODE1=<HOSTNAME_1>   # Virtual server instance hostname
export DC1="Site1"          # HANA System Replication site name

# Cluster node 2
export NODE2=<HOSTNAME_2>   # Virtual server instance hostname
export DC2="Site2"          # HANA System Replication site name

# Single zone
export VIP=<IP address>     # SAP HANA System Replication cluster virtual IP address
```
{: codeblock}

#### Setting extra environment variables for implementing a single zone
{: #ha-sles-hana-sr-sz-prepare-environment-variables}

Review the information in [Reserving virtual IP addresses](/docs/sap?topic=sap-ha-vsi#ha-vsi-reserve-virtual-ip-addresses) and reserve a virtual IP address for the SAP HANA System Replication cluster.
Set the `VIP` environment variable to the reserved IP address.

### Installing SAP HANA resource agents
{: #ha-sles-hana-sr-install-sap-hana-resource-agents}

The [SAP Hana resource agent](https://documentation.suse.com/sles-sap/15-SP6/html/SLES-SAP-guide/cha-about.html#sec-hana-ra){: external} and the [SAP HanaTopology Resource agent](https://documentation.suse.com/sles-sap/15-SP6/html/SLES-SAP-guide/cha-about.html#sec-topology-ra){: extenal} are part of the SLES for SAP applications distribution.

To install the resource and topology agent, make sure that the package *yast2-sap-ha* is installed, as described in [Setting up an SAP HANA cluster](https://documentation.suse.com/sles-sap/15-SP6/html/SLES-SAP-guide/cha-cluster.html){: external} and follow the steps to configure the HANA cluster by using *yast2*.

For a scale-out scenarios, follow the [Installing additional Software](https://documentation.suse.com/sbp/sap-15/html/SLES4SAP-hana-sr-guide-PerfOpt-15/index.html#id-installing-additional-software){: external} section of the *SAP HANA System Replication Scale-Up - Performance-Optimized Scenario* guide.

### Starting the SAP HANA system
{: #ha-sles-hana-sr-start-hana-systems}

Start SAP HANA and verify that HANA System Replication is active.
For more information, see [Checking System Replication Status Details](https://documentation.suse.com/sbp/sap-15/html/SLES4SAP-hana-angi-perfopt-15/index.html#id-1.10.8.10){: external}.

On both nodes, run the following commands.

```sh
sudo -i -u ${sid}adm -- HDB start
```
{: pre}

```sh
sudo -i -u ${sid}adm -- <<EOT
    hdbnsutil -sr_state
    HDBSettings.sh systemReplicationStatus.py
EOT
```
{: pre}

### Enabling the SAP HANA srConnectionChanged() hook
{: #ha-sles-hana-sr-enable-hana-sr-hook}

Recent versions of SAP HANA provide *hooks* so that SAP HANA can send out notifications for certain events.
For more information, see [Implementing a HA/DR Provider](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/1367c8fdefaa4808a7485b09815ae0f3.html){: external}.

The *srConnectionChanged()* hook improves the ability of the cluster to detect a HANA System Replication status change that requires an action from the cluster.
The goal is to prevent data loss and corruption by preventing accidental takeovers.

#### Activating the srConnectionChanged() hook on all SAP HANA instances
{: #ha-sles-hana-sr-activate-hana-sr-hook}

1. Stop the cluster.

   On NODE1, run the following command.

   ```sh
   crm cluster stop --all
   ```
   {: pre}

   Then, follow the steps that are described in [Setting up SAP HANA HA/DR providers](https://documentation.suse.com/sbp/sap-15/html/SLES4SAP-hana-angi-perfopt-15/index.html#cha.s4s.hana-hook){: external}.

1. Verify that the hook functions.

   - Restart both HANA instances and verify that the hook script works as expected.
   - Perform an action to trigger the hook, such as stopping a HANA instance.
   - Check whether the hook logged anything in the trace files.

   On both nodes, run the following commands.

   Stop the HANA instance.

   ```sh
   sudo -i -u ${sid}adm -- HDB stop
   ```
   {: pre}

   Start the HANA instance.

   ```sh
   sudo -i -u ${sid}adm -- HDB start
   ```
   {: pre}

   Check that the hook logged messages to the trace files.

   ```sh
   sudo -i -u ${sid}adm -- sh -c 'grep "ha_dr_SAPHanaSR.*crm_attribute" $DIR_INSTANCE/$VTHOSTNAME/trace/nameserver_* | cut -d" " -f2,3,5,17'
   ```
   {: pre}

   After you verify that the hooks function, you can restart the HA cluster.

1. Start the cluster.

   On NODE1, run the following commands.

   Start the cluster.

   ```sh
   crm cluster start --all
   ```
   {: pre}

   Check the status of the cluster.

   ```sh
   crm status --full
   ```
   {: pre}

### Configuring general cluster properties
{: #ha-sles-hana-sr-configure-cluster-properties}

To avoid resource failover during initial testing and post-production, set the following default values for the resource-stickiness and migration-threshold parameters.

These steps are described in [Configuring the cluster](https://documentation.suse.com/sbp/sap-15/html/SLES4SAP-hana-angi-perfopt-15/index.html#cha.s4s.configure-cluster){: external}.

The IBM Power10 Systems provide an integrated hardware watchdog timer that is enabled by default.
The [Configuring the cluster](https://documentation.suse.com/sbp/sap-15/html/SLES4SAP-hana-angi-perfopt-15/index.html#cha.s4s.configure-cluster){: external} descriptions suggests as a fallback to use `softdog` as a software watchdog timer. Use the more reliable IBM Power10 hardware watchdog timer instead.
{: warning}

## Testing SAP HANA System Replication cluster
{: #ha-sles-hana-sr-test-sap-hana-sr-cluster}

It is vital to thoroughly test the cluster configuration to make sure that the cluster is working correctly.
The following information provides a few sample failover test scenarios. It's not a complete list of test scenarios.

For example, the description of each test case includes the following information.

- Component that is being tested
- Description of the test
- Prerequisites and the cluster state before you start the failover test
- Test procedure
- Expected behavior and results
- Recovery procedure

### Test 1 - Testing a failure of the primary database instance
{: #ha-sles-hana-sr-test-primary-instance-database-failure}

Use the following information to test the failure of the primary database instance.

#### Test 1 - Description
{: #ha-sles-hana-sr-test1-description}

Simulate a crash of the primary HANA database instance that is running on NODE1.

#### Test 1 - Prerequisites
{: #ha-sles-hana-sr-test1-prerequisites}

- A functional two-node SLES HA Extension cluster for HANA system replication.
- Both cluster nodes are active.
- Cluster that is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` that is configured with `AUTOMATED_REGISTER=false`.
- Check SAP HANA System Replication status:
   - Primary SAP HANA database is running on NODE1
   - Secondary SAP HANA database is running on NODE2
   - HANA System Replication is activated and in sync

A variation of Test 1 is described in [Test cases for semi-automation](https://documentation.suse.com/sbp/sap-15/html/SLES4SAP-hana-angi-perfopt-15/index.html#id-test-cases-for-semi-automation){: external}.

#### Test 1 procedure
{: #ha-sles-hana-sr-test1-procedure}

Use the following command to run Test 1.

Crash SAP HANA primary by sending a SIGKILL signal as the user `${sid}adm`.

On NODE1, run the following command.

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test 1 - Expected behavior
{: #ha-sles-hana-sr-test1-expected-behavior}

You can expect the following behavior from the test.

- SAP HANA primary instance on NODE1 crashes.
- The cluster detects the stopped primary HANA database and marks the resource as `failed`.
- The cluster promotes the secondary HANA database on NODE2 to take over as the new primary.
- The cluster releases the virtual IP address on NODE1, and acquires it on the new primary on NODE2.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

#### Test 1 - Recovery procedure
{: #ha-sles-hana-sr-test1-recovery-procedure}

As the cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=false`, the cluster doesn't restart the failed HANA database and doesn't register it against the new primary. Which means that the status on the new primary (NODE2) also shows the secondary in status 'CONNECTION TIMEOUT'.

To reregister the previous primary as a new secondary use the following commands.

On NODE1, run the following command.

```sh
sudo -i -u ${sid}adm -- <<EOT
    hdbnsutil -sr_register \
      --name=${DC1} \
      --remoteHost=${NODE2} \
      --remoteInstance=00 \
      --replicationMode=sync \
      --operationMode=logreplay \
      --online
EOT
```
{: pre}

Verify the system replication status by using the following command.

```sh
sudo -i -u ${sid}adm -- <<EOT
    hdbnsutil -sr_state
    HDBSettings.sh systemReplicationStatus.py
EOT
```
{: pre}

After the manual register and resource refreshes, the new secondary instance restarts and shows a synced (`SOK`) status.

On NODE1, run the following command.

```sh
crm resource refresh SAPHana_${SID}_${INSTNO}
```
{: pre}

### Test 2 - Testing a failure of the node that is running the primary database
{: #ha-sles-hana-sr-test-primary-instance-server-failure}

Use the following information to test the failure of the node that is running the primary database.

#### Test 2 - Description
{: #ha-sles-hana-sr-test2-description}

Simulate a crash of the node that is running the primary HANA database.

#### Test 2 - Prerequisites
{: #ha-sles-hana-sr-test2-prerequisites}

See the following prerequisites before you perform Test 2.

- You need a functional two-node SLES HA Extension cluster for HANA system replication.
- Make sure that both nodes are active.
- Confirm that the cluster is started on NODE1 and NODE2.
- Check SAP HANA System Replication status.
   - Primary SAP HANA database is running on NODE2.
   - Secondary SAP HANA database is running on NODE1.
   - HANA System Replication is activated and in sync.

#### Test 2 - Preparation
{: #ha-sles-hana-sr-test2-preparation}

Make sure that the cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.

On NODE1, run the following commands.

```sh
crm resource update SAPHana_${SID}_${INSTNO} AUTOMATED_REGISTER=true
```
{: pre}

```sh
crm resource config SAPHana_${SID}_${INSTNO}
```
{: pre}

#### Test 2 - Test procedure
{: #ha-sles-hana-sr-test2-procedure}

Crash primary on NODE2 by sending a *crash* system request.

On NODE2, run the following command.

```sh
sync; echo c > /proc/sysrq-trigger
```
{: pre}

#### Test 2 - Expected behavior
{: #ha-sles-hana-sr-test2-expected-behavior}

You can expect the following behavior from the test.

- NODE2 shuts down.
- The cluster detects the failed node and sets its state to `OFFLINE`.
- The cluster promotes the secondary HANA database on NODE1 to take over as the new primary.
- The cluster acquires the virtual IP address on NODE1 on the new primary.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

#### Test 2 - Recovery procedure
{: #ha-sles-hana-sr-test2-recovery-procedure}

Use the following information to recover from Test 2.

1. Log in to the {{site.data.keyword.cloud}} Console and start the NODE2 instance.
1. Wait until NODE2 is available again, then restart the cluster framework.

   - On NODE2, run the following commands.

   ```sh
   crm cluster start
   ```
   {: pre}

   ```sh
   crm status --full
   ```
   {: pre}

As the cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`, SAP HANA restarts when NODE2 rejoins the cluster and the former primary reregisters as a secondary.

### Test 3 - Testing a failure of the secondary database instance
{: #ha-sles-hana-sr-test-secondary-instance-database-failure}

Use the following information to test the failure of the secondary database instance.

#### Test 3 - Description
{: #ha-sles-hana-sr-test3-description}

Simulate a crash of the secondary HANA database.

#### Test 3 - Prerequisites
{: #ha-sles-hana-sr-test3-prerequisites}

See the following prerequisites before you perform Test 3.

- A functional two-node SLES HA Extension cluster for HANA system replication.
- Both nodes are active.
- Cluster is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.
- Check SAP HANA System Replication status.
   - Primary SAP HANA database is running on NODE1.
   - Secondary SAP HANA database is running on NODE2.
   - HANA System Replication is activated and in sync.

#### Test 3 - Test Procedure
{: #ha-sles-hana-sr-test3-procedure}

Crash SAP HANA secondary by sending a SIGKILL signal as the user `${sid}adm`.

On NODE2, run the following command.

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test 3 - Expected behavior
{: #ha-sles-hana-sr-test3-expected-behavior}

You can expect the following behavior from the test.

- SAP HANA secondary on NODE2 crashes.
- The cluster detects the stopped secondary HANA database and marks the resource as `failed`.
- The cluster restarts the secondary HANA database.
- The cluster detects that the system replication is in sync again.

#### Test 3 - Recovery procedure
{: #ha-sles-hana-sr-test3-recovery-procedure}

Use the following information to recover from Test 2.

1. Wait until the secondary HANA instance starts and syncs again (`SOK`), then cleanup the failed resource actions as shown in `crm status`.

1. On NODE2, run the following command.

   ```sh
   crm resource refresh SAPHana_${SID}_${INSTNO}
   ```
   {: pre}

   ```sh
   crm status --full
   ```
   {: pre}

### Test 4 - Testing a manual move of an SAP Hana resource to another node
{: #ha-sles-hana-sr-test-manual-move}

Use the following information to test the manual move of an SAP Hana resource to another node.

#### Test 4 - Description
{: #ha-sles-hana-sr-test4-description}

Use cluster commands to move the primary instance to the other node for maintenance purposes.

#### Test 4 - Prerequisites
{: #ha-sles-hana-sr-test4-prerequisites}

See the following prerequisites before you perform Test 4.

- A functional two-node SLES HA Extension cluster for HANA system replication.
- Both nodes are active.
- Cluster is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.
- Check SAP HANA System Replication status:
   - Primary SAP HANA database is running on NODE1
   - Secondary SAP HANA database is running on NODE2
   - HANA System Replication is activated and in sync

#### Test 4 - Test procedure
{: #ha-sles-hana-sr-test4-procedure}

Move SAP HANA primary to other node by using the `crm resource move` command.

On NODE1, run the following command.

```sh
crm resource move SAPHana_${SID}_${INSTNO}-clone
```
{: pre}

#### Test 4 - Expected behavior
{: #ha-sles-hana-sr-test4-expected-behavior}

You can expect the following behavior from the test.

- The cluster creates location constraints to move the resource.
- The cluster triggers a takeover to the secondary HANA database.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

#### Test 4 - Recovery procedure
{: #ha-sles-hana-sr-test4-recovery-procedure}

Use the following information to recover from Test 2.

The automatically created location constraints must be removed to allow automatic failover in the future.

Wait until the primary HANA instance is active and remove the constraints.

The cluster registers and starts the HANA database as a new secondary instance.

On NODE1, run the following command.

```sh
crm constraint
```
{: pre}

```sh
crm resource clear SAPHana_${SID}_${INSTNO}-clone
```
{: pre}

```sh
crm constraint
```
{: pre}

```sh
crm status --full
```
{: pre}
