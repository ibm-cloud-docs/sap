---
copyright:
  years: 2023, 2024

lastupdated: "2024-09-11"

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn

subcollection: sap

---

{{site.data.keyword.attribute-definition-list}}

# Configuring SAP HANA Scale-Up System Replication in a RHEL HA Add-On Cluster
{: #ha-rhel-hana-sr}

The following information describes the configuration of a Red Hat Enterprise Linux (RHEL) HA Add-On cluster for managing *SAP HANA Scale-Up System Replication*.
The cluster uses virtual server instances in [{{site.data.keyword.powerSysFull}}](https://www.ibm.com/products/power-virtual-server){: external} as cluster nodes.
{: shortdesc}

The instructions describe how to automate SAP HANA Scale-Up System Replication for a single database deployment in a performance-optimized scenario on a RHEL HA Add-on cluster.

This information is intended for architects and specialists that are planning a high-availability deployment of SAP HANA on {{site.data.keyword.powerSys_notm}}.
{: note}

## Before you begin
{: #ha-rhel-hana-sr-begin}

Review the general requirements, product documentation, support articles, and SAP notes listed in [Implementing High Availability for SAP Applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

## Prerequisites
{: #ha-rhel-hana-sr-prerequisites}

- A Red Hat High Availability cluster is deployed on two virtual server instances in {{site.data.keyword.powerSys_notm}}.
   - Install and set up the RHEL HA Add-On cluster according to [Implementing a RHEL HA Add-On Cluster on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-ha-rhel).
   - Configure and verify fencing as described in the preceding document.
- The virtual server instances need to fulfill hardware and resource requirements for the SAP HANA systems in scope.
   Follow the guidelines in the [Planning your deployment](/docs/sap?topic=sap-power-vs-planning-items) document.
- The hostnames of the virtual server instances must meet the SAP HANA requirement.
- SAP HANA is installed on both virtual server instances and SAP HANA System Replication is configured.
   The installation of SAP HANA and setup of HANA System Replication is not specific to the {{site.data.keyword.powerSys_notm}} environment, and you need to follow the standard procedures.
- A valid *RHEL for SAP Applications* or *RHEL for SAP Solutions* subscription is required to enable the repositories that you need to install SAP HANA and the resource agents for HA configurations.

## Configuring SAP HANA System Replication in a RHEL HA Add-On cluster on IBM {{site.data.keyword.powerSys_notm}}
{: #ha-rhel-hana-sr-configure-sr}

The instructions are based on the Red Hat product documentation and articles that are listed in [Implementing High Availability for SAP Applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

### Preparing environment variables
{: #ha-rhel-hana-sr-prepare-environment-variables}

To simplify the setup, prepare the following environment variables for root on both nodes.
These environment variables are used in subsequent commands in the remainder of the examples.

On both nodes, run the following commands.

```sh
export SID=<SID>            # SAP HANA System ID (uppercase)
export sid=<sid>            # SAP HANA System ID (lowercase)
export INSTNO=<INSTNO>      # SAP HANA Instance Number

export DC1=<Site1>          # HANA System Replication Site Name 1
export DC2=<Site2>          # HANA System Replication Site Name 2

export NODE1=<Hostname 1>   # Hostname of virtual server instance 1
export NODE2=<Hostname 2>   # Hostname of virtual server instance 2
```
{: codeblock}

### Installing SAP HANA resource agents
{: #ha-rhel-hana-sr-install-sap-hana-resource-agents}

Run the following command to install the RHEL HA Add-On resource agents for SAP HANA System Replication.

```sh
dnf install -y resource-agents-sap-hana
```
{: pre}

### Starting the SAP HANA system
{: #ha-rhel-hana-sr-start-hana-systems}

Start SAP HANA and verify that HANA System Replication is active.
For more information, see [2.4. Checking SAP HANA System Replication state](https://access.redhat.com/articles/3004101#checking-sap-hana-system-replication-state){: external}.

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
{: #ha-rhel-hana-sr-enable-hana-sr-hook}

Recent versions of SAP HANA provide *hooks* so SAP HANA can send out notifications for certain events.
For more information, see [Implementing a HA/DR Provider](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/1367c8fdefaa4808a7485b09815ae0f3.html){: external}.

The *srConnectionChanged()* hook improves the ability of the cluster to detect a status change of HANA System Replication that requires an action from the cluster.
The goal is to prevent data loss and corruption by preventing accidental takeovers.

#### Activating the srConnectionChanged() hook on all SAP HANA instances
{: #ha-rhel-hana-sr-activate-hana-sr-hook}

1. Stop the cluster.

   On NODE1, run the following command.

   ```sh
   pcs cluster stop --all
   ```
   {: pre}

1. Install the hook script that is provided by the *resource-agents-sap-hana* package in the `/hana/shared/myHooks` directory for each HANA instance, and set the required ownership.

   On both nodes, run the following commands.

   ```sh
   mkdir -p /hana/shared/myHooks
   ```
   {: pre}

   ```sh
   cp /usr/share/SAPHanaSR/srHook/SAPHanaSR.py /hana/shared/myHooks
   ```
   {: pre}

   ```sh
   chown -R ${sid}adm:sapsys /hana/shared/myHooks
   ```
   {: pre}

1. Update the `global.ini` file on each HANA node to enable the hook script.

   On both nodes, run the following command.

   ```sh
   sudo -i -u ${sid}adm -- <<EOT
       python \$DIR_INSTANCE/exe/python_support/setParameter.py \
         -set SYSTEM/global.ini/ha_dr_provider_SAPHanaSR/provider=SAPHanaSR \
         -set SYSTEM/global.ini/ha_dr_provider_SAPHanaSR/path=/hana/shared/myHooks \
         -set SYSTEM/global.ini/ha_dr_provider_SAPHanaSR/execution_order=1 \
         -set SYSTEM/global.ini/trace/ha_dr_saphanasr=info
   EOT
   ```
   {: pre}

1. Verify the changed file.

   On both nodes, run the following command.

   ```sh
   cat /hana/shared/${SID}/global/hdb/custom/config/global.ini
   ```
   {: pre}

1. Create sudo settings for SAP HANA OS user.

   You need the following sudo settings to allow the `${sid}adm` user script can update the node attributes when the *srConnectionChanged()* hook runs.

   On both nodes, run the following commands.

   Create a file with the required sudo aliases and user specifications.

   ```sh
   cat >> /etc/sudoers.d/20-saphana << EOT
   Cmnd_Alias DC1_SOK = /usr/sbin/crm_attribute -n hana_${sid}_site_srHook_${DC1} -v SOK -t crm_config -s SAPHanaSR
   Cmnd_Alias DC1_SFAIL = /usr/sbin/crm_attribute -n hana_${sid}_site_srHook_${DC1} -v SFAIL -t crm_config -s SAPHanaSR
   Cmnd_Alias DC2_SOK = /usr/sbin/crm_attribute -n hana_${sid}_site_srHook_${DC2} -v SOK -t crm_config -s SAPHanaSR
   Cmnd_Alias DC2_SFAIL = /usr/sbin/crm_attribute -n hana_${sid}_site_srHook_${DC2} -v SFAIL -t crm_config -s SAPHanaSR
   ${sid}adm ALL=(ALL) NOPASSWD: DC1_SOK, DC1_SFAIL, DC2_SOK, DC2_SFAIL
   Defaults!DC1_SOK, DC1_SFAIL, DC2_SOK, DC2_SFAIL !requiretty
   EOT
   ```
   {: pre}

   Adjust the permissions and check for syntax errors.

   ```sh
   chown root:root /etc/sudoers.d/20-saphana
   ```
   {: pre}

   ```sh
   chmod 0440 /etc/sudoers.d/20-saphana
   ```
   {: pre}

   ```sh
   cat /etc/sudoers.d/20-saphana
   ```
   {: pre}

   ```sh
   visudo -c
   ```
   {: pre}

Any problems that are reported by the `visudo -c` command must be corrected.
{: note}

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

   Check that the hook has logged some messages to the trace files.

   ```sh
   sudo -i -u ${sid}adm -- sh -c 'grep "ha_dr_SAPHanaSR.*crm_attribute" $DIR_INSTANCE/$VTHOSTNAME/trace/nameserver_* | cut -d" " -f2,3,5,17'
   ```
   {: pre}

   After you verify that the hooks function, you can restart the HA cluster.

1. Start cluster.

   On NODE1, run the following commands.

   Start the cluster.

   ```sh
   pcs cluster start --all
   ```
   {: pre}

   Check the status of the cluster.

   ```sh
   pcs status --full
   ```
   {: pre}

### Configuring general cluster properties
{: #ha-rhel-hana-sr-configure-cluster-properties}

To avoid resource failover during initial testing and post-production, set the following default values for the resource-stickiness and migration-threshold parameters.

Keep in mind that defaults don't apply to resources that override them with their own defined values.

On NODE1, run the following commands.

```sh
pcs resource defaults update resource-stickiness=1000
```
{: pre}

```sh
pcs resource defaults update migration-threshold=5000
```
{: pre}

### Creating a cloned SAPHanaTopology resource
{: #ha-rhel-hana-sr-create-saphanatopology-resource}

The *SAPHanaTopology* resource gathers status and configuration of SAP HANA System Replication on each node.
It also starts and monitors the local *SAP HostAgent*, which is required for starting, stopping, and monitoring SAP HANA instances.

On NODE1, run the following commands.

Create the *SAPHanaTopology* resource.

```sh
pcs resource create SAPHanaTopology_${SID}_${INSTNO} SAPHanaTopology \
    SID=${SID} InstanceNumber=${INSTNO} \
    op start timeout=600 \
    op stop timeout=300 \
    op monitor interval=10 timeout=600 \
    clone clone-max=2 clone-node-max=1 interleave=true
```
{: pre}

Check the configuration and the cluster status by running the following commands.

```sh
pcs resource config SAPHanaTopology_${SID}_${INSTNO}
```
{: pre}

```sh
pcs resource config SAPHanaTopology_${SID}_${INSTNO}-clone
```
{: pre}

```sh
pcs status --full
```
{: pre}

### Creating a promotable SAPHana resource
{: #ha-rhel-hana-sr-create-saphana-resource}

The *SAPHana* resource manages two SAP HANA instances that are configured as HANA System Replication nodes.

On NODE1, create the *SAPHana* resource, by running the following command.

```sh
pcs resource create SAPHana_${SID}_${INSTNO} SAPHana \
    SID=${SID} InstanceNumber=${INSTNO} \
    PREFER_SITE_TAKEOVER=true \
    DUPLICATE_PRIMARY_TIMEOUT=7200 \
    AUTOMATED_REGISTER=false \
    op start timeout=3600 \
    op stop timeout=3600 \
    op monitor interval=61 role="Unpromoted" timeout=700 \
    op monitor interval=59 role="Promoted" timeout=700 \
    op promote timeout=3600 \
    op demote timeout=3600 \
    promotable notify=true clone-max=2 clone-node-max=1 interleave=true
```
{: pre}

Check the configuration and the cluster status.

```sh
pcs resource config SAPHana_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}

### Creating a virtual IP address resource in a singlezone enviroment
{: #ha-rhel-hana-sr-create-virtual-ip-resource}

Perform the following steps if both cluster nodes are running in a single {{site.data.keyword.powerSys_notm}} workspace.

Review the information in [Reserving virtual IP addresses](/docs/sap?topic=sap-ha-vsi#ha-vsi-reserve-virtual-ip-addresses) and reserve a virtual IP address for the SAP HANA System Replication cluster.

Use the reserved IP address to create a virtual IP address resource.
This virtual IP address is used to reach the System Replication primary instance.

On NODE1, assign the reserved IP address to a *VIP* environment variable and create the virtual IP address cluster resource by running the following commands.

```sh
export VIP=<reserved IP address>
```
{: sceen}

```sh
echo $VIP
```
{: pre}

```sh
pcs resource create vip_${SID}_${INSTNO} IPaddr2 ip=$VIP
```
{: pre}

Check the configured virtual IP address and the cluster status.

```sh
pcs resource config vip_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}

Proceed to the [Creating constraints](#ha-rhel-hana-sr-create-constraints) section.

### Creating a virtual IP address resource in a multizone region enviroment
{: #ha-rhel-hana-sr-create-virtual-ip--mz-resource}

If both cluster nodes are running in a multizone region environment, follow the instructions in [Creating a virtual IP address resource in the multizone region setup](/docs/sap?topic=sap-ha-rhel-mz#ha-rhel-mz-define-subnet-resource){: external} to define a resource for the virtual IP address.

### Creating constraints
{: #ha-rhel-hana-sr-create-constraints}

Make sure that *SAPHanaTopology* resources are started before you start the *SAPHana* resources.

The virtual IP address must be present on the node where the primary resource of "SAPHana" is running.

1. Create constraint to start "SAPHanaTopology" before "SAPHana".
   This constraint mandates the start order of these resources.

   On NODE1, use the following command to create the *SAPHanaTopology* order constraint:

   ```sh
   pcs constraint order SAPHanaTopology_${SID}_${INSTNO}-clone \
       then SAPHana_${SID}_${INSTNO}-clone symmetrical=false
   ```
   {: pre}

   Check the configuration.

   ```sh
   pcs constraint
   ```
   {: pre}

1. Create constraint to colocate the virtual IP address with primary.
   This constraint colocates the virtual IP address resource with the SAPHana resource that was promoted as primary.

   On NODE1, run the following command to create the virtual IP address colocation constraint.

   ```sh
   pcs constraint colocation add vip_${SID}_${INSTNO} \
       with Promoted SAPHana_${SID}_${INSTNO}-clone 2000
   ```
   {: pre}

   Check the configuration and the cluster status.

   ```sh
   pcs constraint
   ```
   {: pre}

   Sample output:

   ```sh
   # pcs constraint
   Location Constraints:
   Ordering Constraints:
     start SAPHanaTopology_HDB_00-clone then start SAPHana_HDB_00-clone (kind:Mandatory) (non-symmetrical)
   Colocation Constraints:
     vip_HDB_00 with SAPHana_HDB_00-clone (score:2000) (rsc-role:Started) (with-rsc-role:Promoted)
   Ticket Constraints:
   ```
   {: screen}


   Verify the cluster status.
   ```sh
   pcs status --full
   ```
   {: pre}

   On the promoted cluster node, verify that the cluster service IP address is active.

   ```sh
   ip addr show
   ```
   {: pre}

### Enabling the SAP HANA srServiceStateChanged() hook (optional)
{: #ha-rhel-hana-sr-enable-hana-servicestatechanged-hook}

SAP HANA has built-in functionality to monitor its `indexserver`.
In case of a problem, SAP HANA tries to recover automatically by stopping and restarting the process.
To stop the process or clean up after a crash, the Linux kernel must free all memory allocated by the process.
For large databases, this can take a very long time.
During this time, SAP HANA continues to operate and accept new client requests.
As a result, SAP HANA system replication may become out of sync.
If another error occurs in the SAP HANA instance before the restart and recovery of the `indexserver` is complete, data consistency is at risk.

The `ChkSrv.py` script for the `srServiceStateChanged()` hook reacts to such a situation and can stop the entire SAP HANA instance for faster recovery.
If `automated failover` is enabled in the cluster, and the secondary node is in a healthy state, a takeover operation is invoked.
Otherwise, recovery must continue locally, but is accelerated by the forced restart of the SAP HANA instance.

The SAP HANA `srServiceStateChanged()` hook is available with `resource-agents-sap-hana` version 0.162.3 and later.
{: note}

The hook script analyzes the events in the instance, applies filters to the event details, and triggers actions based on the results.
It distinguishes between a SAP HANA `indexserver` process that  is stopped and restarted by SAP HANA and the process that is stopped during an instance shutdown.

Depending on the configuration of the `action_on_lost` parameter, the hook takes different actions:

Ignore
:   This action simply logs the events and decision information to a log file

Stop
:  This action triggers a graceful stop of the SAP HANA instance by using the sapcontrol command.

Kill
:   This action triggers the HDB kill-`<signal>` command with a default signal 9.
    The signal can be configured.

Both the *stop* and the *kill* actions result in a stopped SAP HANA instance, with the *kill* action being slightly faster.

#### Activating the srServiceStateChanged() hook on all SAP HANA instances
{: #ha-rhel-hana-sr-activate-hana-servicestatechanged-hook}

The `srServiceStateChanged()` hook can be added while SAP HANA is running on both nodes.

1. For each HANA instance, install the hook script that is provided by the *resource-agents-sap-hana* package in the `/hana/shared/myHooks` directory and set the required ownership.

   On both nodes, run the following commands.

   ```sh
   cp /usr/share/SAPHanaSR/srHook/ChkSrv.py /hana/shared/myHooks
   ```
   {: pre}

   ```sh
   chown ${sid}adm:sapsys /hana/shared/myHooks/ChkSrv.py
   ```
   {: pre}

1. Update the `global.ini` file on each SAP HANA node to enable the hook script.

   On both nodes, run the following command.

   ```sh
   sudo -i -u ${sid}adm -- <<EOT
       python \$DIR_INSTANCE/exe/python_support/setParameter.py \
         -set SYSTEM/global.ini/ha_dr_provider_ChkSrv/provider=ChkSrv \
         -set SYSTEM/global.ini/ha_dr_provider_ChkSrv/path=/hana/shared/myHooks \
         -set SYSTEM/global.ini/ha_dr_provider_ChkSrv/execution_order=2 \
         -set SYSTEM/global.ini/ha_dr_provider_ChkSrv/action_on_lost=stop \
         -set SYSTEM/global.ini/trace/ha_dr_chksrv=info
   EOT
   ```
   {: pre}

   The `action_on_lost` parameter in this example is set to `stop`, the default would be `ignore`.
   You can optionally set the parameters `stop_timeout` (default: `20` seconds) and `kill_signal` (default: `9`).

1. Activate the `ChkSrv.py` hook

   On both nodes, run the following command to reload the HA-DR providers.

   ```sh
   sudo -i -u ${sid}adm -- hdbnsutil -reloadHADRProviders
   ```
   {: pre}

1. Check that the hook has logged some messages to the trace files.

   On both nodes, run the following command.

   ```sh
   sudo -i -u ${sid}adm -- sh -c 'grep "ha_dr_ChkSrv" $DIR_INSTANCE/$VTHOSTNAME/trace/nameserver_* | cut -d" " -f2,3,6-'
   ```
   {: pre}

### Enabling automated registration of secondary instance
{: #ha-rhel-hana-sr-enable-automated-registration}

You need to set the parameter `AUTOMATED_REGISTER` according to your operational requirements.
If you want to keep the ability to revert to the state of the previous primary SAP HANA instance, then `AUTOMATED_REGISTER=false` avoids an automatic registration of the previous primary as a new secondary.

If you experience an issue with the data after a takeover that was triggered by the cluster, you can manually revert if `AUTOMATED_REGISTER` is set to `false`.

If `AUTOMATED_REGISTER` is set to `true`, the previous primary SAP HANA instance automatically registers as secondary, and cannot be activated on its previous history.
The advantage of `AUTOMATED_REGISTER=true` is that high-availability capability is automatically reestablished after the failed node reappears in the cluster.

For now, it is recommended to keep `AUTOMATED_REGISTER` on default value `false` until the cluster is fully tested and that you verify that the failover scenarios work as expected.

The `pcs resource update` command is used to modify resource attributes and `pcs resource update SAPHana_${SID}_${INSTNO} AUTOMATED_REGISTER=true` sets the attribute to `true`.
{: tip}

## Testing SAP HANA System Replication cluster
{: #ha-rhel-hana-sr-test-sap-hana-sr-cluster}

It is vital to thoroughly test the cluster configuration to make sure that the cluster is working correctly.
The following information provides a few sample failover test scenarios, but is not a complete list of test scenarios.

For example, the description of each test case includes the following information.

- Component that is being tested
- Description of the test
- Prerequisites and the cluster state before you start the failover test
- Test procedure
- Expected behavior and results
- Recovery procedure

### Test1 - Testing failure of the primary database instance
{: #ha-rhel-hana-sr-test-primary-instance-database-failure}

Use the following information to test the failure of the primary database instance.

#### Test1 - Description
{: #ha-rhel-hana-sr-test1-description}

Simulate a crash of the primary HANA database instance that is running on NODE1.

#### Test1 - Prerequisites
{: #ha-rhel-hana-sr-test1-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both cluster nodes are active.
- Cluster that is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` that is configured with `AUTOMATED_REGISTER=false`.
- Check SAP HANA System Replication status:
   - Primary SAP HANA database is running on NODE1
   - Secondary SAP HANA database is running on NODE2
   - HANA System Replication is activated and in sync

#### Test1 - Test procedure
{: #ha-rhel-hana-sr-test1-procedure}

Crash SAP HANA primary by sending a SIGKILL signal as user `${sid}adm`.

On NODE1, run the following command.

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test1 - Expected behavior
{: #ha-rhel-hana-sr-test1-expected-behavior}

- SAP HANA primary instance on NODE1 crashes.
- The cluster detects the stopped primary HANA database and marks the resource as `failed`.
- The cluster promotes the secondary HANA database on NODE2 to take over as new primary.
- The cluster releases the virtual IP address on NODE1, and acquires it on the new primary on NODE2.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

#### Test1 - Recovery procedure
{: #ha-rhel-hana-sr-test1-recovery-procedure}

As cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=false`, the cluster doesn't restart the failed HANA database, and doesn't register it against the new primary. Which means that the status on the new primary (NODE2) also shows the secondary in status 'CONNECTION TIMEOUT'.

To reregister the previous primary as new secondary use the following commands.

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

Verify the system replication status:

```sh
sudo -i -u ${sid}adm -- <<EOT
    hdbnsutil -sr_state
    HDBSettings.sh systemReplicationStatus.py
EOT
```
{: pre}

After the manual register and resource refreshes, the new secondary instance restarts and shows up in status synced (`SOK`).

On NODE1, run the following command.

```sh
pcs resource refresh SAPHana_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}

### Test2 - Testing failure of the node that is running the primary database
{: #ha-rhel-hana-sr-test-primary-instance-server-failure}

Use the following information to test the failure of the node that is running the primary database.

#### Test2 - Description
{: #ha-rhel-hana-sr-test2-description}

Simulate a crash of the node that is running the primary HANA database.

#### Test2 - Preparation
{: #ha-rhel-hana-sr-test2-preparation}

Make sure that Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.

On NODE1, run the following command.

```sh
pcs resource update SAPHana_${SID}_${INSTNO} AUTOMATED_REGISTER=true
```
{: pre}

```sh
pcs resource config SAPHana_${SID}_${INSTNO}
```
{: pre}

#### Test2 - Prerequisites
{: #ha-rhel-hana-sr-test2-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both nodes active.
- Cluster is started on NODE1 and NODE2.
- Check SAP HANA System Replication status.
   - Primary SAP HANA database is running on NODE2
   - Secondary SAP HANA database is running on NODE1
   - HANA System Replication is activated and in sync

#### Test2 - Test procedure
{: #ha-rhel-hana-sr-test2-procedure}

Crash primary on NODE2 by sending a *shutoff* system request.

On NODE2, run the following command.

```sh
sync; echo o > /proc/sysrq-trigger
```
{: pre}

#### Test2 - Expected behavior
{: #ha-rhel-hana-sr-test2-expected-behavior}

- NODE2 shuts down.
- The cluster detects the failed node and sets its state to `OFFLINE`.
- The cluster promotes the secondary HANA database on NODE1 to take over as new primary.
- The cluster acquires the virtual IP address on NODE1 on the new primary.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

#### Test2 - Recovery procedure
{: #ha-rhel-hana-sr-test2-recovery-procedure}

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

As cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`, SAP HANA restarts when NODE2 rejoins the cluster and the former primary reregisters as a secondary.

### Test3 - Testing the failure of the secondary database instance
{: #ha-rhel-hana-sr-test-secondary-instance-database-failure}

Use the following information to test the failure of the secondary database instance.

#### Test3 - Description
{: #ha-rhel-hana-sr-test3-description}

Simulate a crash of the secondary HANA database.

#### Test3 - Prerequisites
{: #ha-rhel-hana-sr-test3-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both nodes are active.
- Cluster is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.
- Check SAP HANA System Replication status:
   - Primary SAP HANA database is running on NODE1
   - Secondary SAP HANA database is running on NODE2
   - HANA System Replication is activated and in sync

#### Test3 - Test Procedure
{: #ha-rhel-hana-sr-test3-procedure}

Crash SAP HANA secondary by sending a SIGKILL signal as user `${sid}adm`.

On NODE2, run the following command.

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test3 - Expected behavior
{: #ha-rhel-hana-sr-test3-expected-behavior}

- SAP HANA secondary on NODE2 crashes.
- The cluster detects the stopped secondary HANA database and marks the resource as `failed`.
- The cluster restarts the secondary HANA database.
- The cluster detects that the system replication is in sync again.

#### Test3 - Recovery procedure
{: #ha-rhel-hana-sr-test3-recovery-procedure}

Wait until the secondary HANA instance starts and syncs again (`SOK`), then cleanup the failed resource actions as shown in `pcs status`.

On NODE2, run the following command.

```sh
pcs resource refresh SAPHana_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}

### Test4 - Testing the manual move of a SAPHana resource to another node
{: #ha-rhel-hana-sr-test-manual-move}

Use the following information to test the manual move of a SAPHana resource to another node.

#### Test4 - Description
{: #ha-rhel-hana-sr-test4-description}

Use cluster commands to move the primary instance to the other node for maintenance purposes.

#### Test4 - Prerequisites
{: #ha-rhel-hana-sr-test4-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both nodes are active.
- Cluster is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.
- Check SAP HANA System Replication status:
   - Primary SAP HANA database is running on NODE1
   - Secondary SAP HANA database is running on NODE2
   - HANA System Replication is activated and in sync

#### Test4 - Test procedure
{: #ha-rhel-hana-sr-test4-procedure}

Move SAP HANA primary to other node by using the `pcs resource move` command.

On NODE1, run the following command.

```sh
pcs resource move SAPHana_${SID}_${INSTNO}-clone
```
{: pre}

#### Test4 - Expected behavior
{: #ha-rhel-hana-sr-test4-expected-behavior}

- The cluster creates location constraints to move the resource.
- The cluster triggers a takeover to the secondary HANA database.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

#### Test4 - Recovery procedure
{: #ha-rhel-hana-sr-test4-recovery-procedure}

The automatically created location constraints must be removed to allow automatic failover in the future.

Wait until the primary HANA instance is active and remove the constraints.

The cluster registers and starts the HANA database as new secondary instance.

On NODE1, run the following command.

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

```sh
pcs status --full
```
{: pre}
