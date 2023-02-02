---
copyright:
  years: 2023
lastupdated: "2023-01-31"

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Configure SAP HANA Scale-Up System Replication in a RHEL HA Add-On cluster
{: #configure-hana-sr-cluster}

Running SAP HANA on IBM Power Systems offers customers a consistent platform for their HANA-based and traditional applications, best-in-class performance, resilience for critical workloads, and most flexible infrastructure.

## Overview
{: #overview}

The following information describes the configuration of a HA cluster for managing *SAP HANA Scale-Up System Replication* in a RHEL HA Add-On cluster with Red Hat Enterprise Linux 8 (RHEL) by using virtual server instances in [IBM Power Systems Virtual Server](https://www.ibm.com/products/power-virtual-server){: external} as cluster nodes.

The instructions describe how to automate SAP HANA Scale-Up System Replication for a single database deployment in a performance-optimized scenario on a RHEL HA Add-on cluster. 

For more information, see the following links:
- [Support Policies for RHEL High Availability Clusters - Management of SAP HANA in a Cluster](https://access.redhat.com/articles/3397471){: external}
- [Automating SAP HANA Scale-Up System Replication using the RHEL HA Add-On](https://access.redhat.com/articles/3004101){: external}
- [SAP HANA System Replication](https://help.sap.com/docs/SAP_HANA_PLATFORM/4e9b18c116aa42fc84c7dbfd02111aba/afac7100bc6d47729ae8eae32da5fdec.html){: external}.

This information is intended for architects and specialists that are planning a high-availability deployment of SAP HANA on Power Systems Virtual Server.
{: note}

## Prerequisites
{: #prerequisites}

- A Red Hat High Availability cluster deployed on two virtual server instances in Power Systems Virtual Server.
   - Install and setup the RHEL HA Add-On cluster according to [Implement RHEL HA Add-On cluster on Power Systems Virtual Server](/docs/sap?topic=sap-power-virtual-server-ha-rhel#create-fencing-device){: external}.
   - Configure and verify fencing as described in the preceding document.
- The virtual server instances need to fulfill hardware and resource requirements for the SAP HANA systems in scope.
   Follow the guidelines that are in the [Planning the Deployment](/docs/sap?topic=sap-power-vs-planning-items){: external}.
- The hostnames of the virtual server instances must meet the SAP HANA requirement.
- SAP HANA installed on both virtual server instances and SAP HANA System Replication is configured.
   The installation of SAP HANA and setup of HANA System Replication is not specific to the Power Systems Virtual Server environment, and you need to follow the standard procedures. For more information, see the following documentation.
   - Install SAP HANA on RHEL: 
      - [SAP Note 2772999 - Red Hat Enterprise Linux 8.x: Installation and Configuration](https://launchpad.support.sap.com/#/notes/2772999){: external}
      - [SAP Note 2777782 - SAP HANA DB: Recommended OS Settings for RHEL 8](https://launchpad.support.sap.com/#/notes/2777782){: external}
   - SAP HANA Server Installation:
      -  [iSAP HANA Server Installation and Update Guide](https://launchpad.support.sap.com/#/notes/2777782){: external}
   - SAP HANA System Replication:
      - [SAP HANA System Replication Guide](https://help.sap.com/docs/SAP_HANA_PLATFORM/4e9b18c116aa42fc84c7dbfd02111aba/afac7100bc6d47729ae8eae32da5fdec.html?locale=en-US){: external}
   - Configure SAP HANA System Replication:
      - [Automating SAP HANA Scale-Up System Replication by using the RHEL HA Add-On - 2. SAP HANA System Replication](https://access.redhat.com/articles/3004101#sap-hana-system-replication){: external}
- A valid *RHEL for SAP Applications* or *RHEL for SAP Solutions* subscription is required to enable the repositories that you need to install SAP HANA and the resource agents for HA configurations.
   The [RHEL for SAP Repositories and How to Enable Them](https://access.redhat.com/articles/6072011){: external} knowledge base article describes how to enable the required repositories.

## Configure SAP HANA System Replication in a RHEL HA Add-On cluster on IBM Power Systems Virtual Server
{: #configure-hana-sr-rhel-ha}

For the following steps, also check the documentation in the Red Hat article [Automating SAP HANA Scale-Up System Replication by using the RHEL HA Add-On](https://access.redhat.com/articles/3004101){: external}.

### Preparing environment variables
{: #prepare-environment-variables}

To simplify the setup, prepare the following environment variables for root on both nodes.
These environment variables are used in subsequent commands in the remainder of the examples.

On *both nodes*, run the following command.

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
{: #install-sap-hana-resource-agents}

Run the following command to install the RHEL HA Add-On resource agents for SAP HANA System Replication:

```sh
yum install -y resource-agents-sap-hana
```
{: pre}

### Starting the SAP HANA system
{: #start-hana-systems}

Start SAP HANA and verify that HANA System Replication is active. For more information, see [2.4. Checking SAP HANA System Replication state](https://access.redhat.com/articles/3004101#checking-sap-hana-system-replication-state){: external}).

On *both nodes*, run the following commands. 

```sh
su - ${sid}adm
HDB start
```
{: pre}

```sh
hdbnsutil -sr_state
HDBSettings.sh systemReplicationStatus.py
```
{: pre}

### Enable the SAP HANA srConnectionChanged() hook
{: #enable-hana-sr-hook}

Recent versions of SAP HANA provide *hooks* so SAP HANA can send out notifications for certain events.
For more information, see [Implementing a HA/DR Provider](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/1367c8fdefaa4808a7485b09815ae0f3.html){: external}.

The *srConnectionChanged()* hook improves the ability of the cluster to detect a status change of HANA System Replication that requires an action from the cluster. The goal is to prevent data loss and corruption by preventing accidental takeovers.

#### Activating the srConnectionChanged() hook on all SAP HANA instances
{: #activate-hana-sr-hook}

1. Stop the cluster.

   On *NODE1*, run the following command.

   ```sh
   pcs cluster stop --all
   ```
   {: pre}

1. Install the hook script that is provided by the *resource-agents-sap-hana* package in the `/hana/shared/myHooks` directory for each HANA instance, and set the required ownership.

   On *both nodes*, run the following commands.

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

   On *both nodes*, run the following command.

   ```sh
   cat >> /hana/shared/${SID}/global/hdb/custom/config/global.ini << EOT

   [ha_dr_provider_SAPHanaSR]
   provider = SAPHanaSR
   path = /hana/shared/myHooks
   execution_order = 1

   [trace]
   ha_dr_saphanasr = info
   EOT
   ```
   {: pre}

1. Verify the changed file.

   On *both nodes*, run the following command.

   ```sh
   cat /hana/shared/${SID}/global/hdb/custom/config/global.ini
   ```
   {: pre}

1. Create sudo settings for SAP HANA OS user.

   You need the following sudo settings to allow the `${sid}adm` user script can update the node attributes when the *srConnectionChanged()* hook runs.

   On *both nodes*, run the following commands.

   Create a file with the required *sudo* aliases and user specifications.

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
   visudo -c
   ```
   {: pre}

Any problems reported by the `visudo -c` command must be corrected.
{: note}

1. Verify that the hook functions.

   - Restart both HANA instances and verify that the hook script is working as expected.
   - Perform an action to trigger the hook, such as stopping a HANA instance.
   - Then check whether the hook logged anything in the trace files.

   On *both nodes*, run the following commands.

   Restart and then stop the HANA instance:

   ```sh
   su - ${sid}adm
   HDB restart
   HDB stop
   ```
   {: pre}

   Check messages in trace files:

   ```sh
   cdtrace
   awk '/ha_dr_SAPHanaSR.*crm_attribute/ { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*
   grep ha_dr_ *
   ```
   {: pre}

   Start the HANA instance:

   ```sh
   HDB start
   ```
   {: pre}

   After you verify that the hooks functions, you can restart the HA cluster.

1. Start cluster.

   On *NODE1*, run the following commands.

   Start the cluster:

   ```sh
   pcs cluster start --all
   ```
   {: pre}

   Check the status of the cluster:

   ```sh
   pcs status --full
   ```
   {: pre}

### Configuring general cluster properties
{: #configure-cluster-properties}

To avoid failovers of the resources during initial testing and post production, set the following default values for the resource-stickiness and migration-threshold parameters.

Keep in mind that defaults don't apply to resources that override them with their own defined values.

On *NODE1*, run the following commands.

```sh
pcs resource defaults update resource-stickiness=1000
```
{: pre}

```sh
pcs resource defaults update migration-threshold=5000
```
{: pre}

### Creating a cloned SAPHanaTopology resource
{: #create-saphanatopology-resource}

The *SAPHanaTopology* resource gathers status and configuration of SAP HANA System Replication on each node.
It also starts and monitors the local *SAP HostAgent*, which is required for starting, stopping, and monitoring SAP HANA instances.

On *NODE1*, run the following commands.

Create the *SAPHanaTopology* resource:

```sh
pcs resource create SAPHanaTopology_${SID}_${INSTNO} SAPHanaTopology \
  SID=${SID} InstanceNumber=${INSTNO} \
  op start timeout=600 \
  op stop timeout=300 \
  op monitor interval=10 timeout=600 \
  clone clone-max=2 clone-node-max=1 interleave=true
```
{: pre}

Check the configuration and the cluster status by running the following command.

```sh
pcs resource config SAPHanaTopology_${SID}_${INSTNO}
pcs resource config SAPHanaTopology_${SID}_${INSTNO}-clone
pcs status --full
```
{: pre}

### Creating master and slave SAPHana resources
{: #create-saphana-resource}

The *SAPHana* resource manages two SAP HANA instances that are configured as HANA System Replication nodes.

On *NODE1*, create the *SAPHana* resource, by running the following command.

```sh
pcs resource create SAPHana_${SID}_${INSTNO} SAPHana \
  SID=${SID} InstanceNumber=${INSTNO} \
  PREFER_SITE_TAKEOVER=true \
  DUPLICATE_PRIMARY_TIMEOUT=7200 \
  AUTOMATED_REGISTER=false \
  op start timeout=3600 \
  op stop timeout=3600 \
  op monitor interval=61 role="Slave" timeout=700 \
  op monitor interval=59 role="Master" timeout=700 \
  op promote timeout=3600 \
  op demote timeout=3600 \
  promotable notify=true clone-max=2 clone-node-max=1 interleave=true
```
{: pre}

Check the configuration and the cluster status.

```sh
pcs resource config SAPHana_${SID}_${INSTNO}
pcs status --full
```
{: pre}

### Creating Virtual IP address resource
{: #create-virtual-ip-resource}

Use the reserved IP address to create a virtual IP address resource. This virtual IP address is used to reach the System Replication primary instance.

On *NODE1*, verify the *VIP* environment variable and create the virtual IP address resource by running the following commands.

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
ip addr show
pcs status --full
```
{: pre}

### Creating constraints
{: #create-constraints}

Make sure that *SAPHanaTopology* resources are started before you start the *SAPHana* resources.

The virtual IP address must be present on the node where the primary resource of "SAPHana" is running.

1. Create constraint to start "SAPHanaTopology" before "SAPHana".

   This constraint mandates the start order of these resources.

   *On *NODE1*, use the following command to create the *SAPHanaTopology* order constraint:

   ```sh
    pcs constraint order SAPHanaTopology_${SID}_${INSTNO}-clone \
     then SAPHana_${SID}_${INSTNO}-clone symmetrical=false
   ```
   {: pre}

   Check the configuration:

   ```sh
   pcs constraint
   ```
   {: pre}

1. Create constraint to colocate the virtual IP address with primary.

   This constraint colocates the virtual IP address resource with the "SAPHana" resource that was promoted as primary.

   *On *NODE1*, run the following command to create the virtual IP address colocation constraint:

   ```sh
   pcs constraint colocation add vip_${SID}_${INSTNO} \
     with primary SAPHana_${SID}_${INSTNO}-clone 2000
   ```
   {: pre}

   Check the configuration and the cluster status:

   ```sh
   pcs constraint
   pcs status --full
   ```
   {: pre}

### Enable automated registration of secondary instance
{: #enable-automated-registration}

The parameter `AUTOMATED_REGISTER` need to be set according to your operational requirements. If you want to keep the ability to revert to the state of the previous primary SAP HANA instance, then
`AUTOMATED_REGISTER=false` avoids an automatic registration of the previous primary as a new secondary.

If you experience an issue with the data after a takeover that was triggered by the cluster, you can manually revert back by setting `AUTOMATED_REGISTER=false`.

If `AUTOMATED_REGISTER=true` the previous primary SAP HANA instance automatically registers as secondary, and cannot be activated on its previous history. The advantage of `AUTOMATED_REGISTER=true` is that high-availability capability is automatically reestablished after the failed node reappears in the cluster.

For now, it is recommended to keep `AUTOMATED_REGISTER` on default value `false` until the cluster is fully tested and that you verify that the failover scenarios work as expected.

The `pcs resource update` command is used to modify resource attributes and `pcs resource update SAPHana_${SID}_${INSTNO} AUTOMATED_REGISTER=true` sets the attribute to `true`.
{: tip}

## Testing SAP HANA System Replication cluster
{: #test-sap-hana-sr-cluster}

It is vital to thoroughly test the cluster configuration to make sure that the cluster is working correctly. The following information provides a few sample failover test scenarios, but is not a complete list of test scenarios.

For example, the description of each test case includes the following information.

- Which component is being tested
- Description of the test
- Prerequisites and the initial state before you start the failover test
- Test procedure
- Expected behavior and results
- Recovery procedure

### Test1 - Test failure of the primary database instance
{: #test-primary-instance-database-failure}

#### Test1 - Description
{: #test1-description}

Simulate a crash of the primary HANA database instance that is running on *NODE1*.

#### Test1 - Prerequisites
{: #test1-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both cluster nodes are active.
- Cluster is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=false`.
- Check SAP HANA System Replication status:
   - primary SAP HANA database is running on NODE1
   - secondary SAP HANA database is running on NODE2
   - HANA System Replication is activated and in sync

#### Test1 - Test procedure
{: #test1-procedure}

Crash SAP HANA primary by sending a SIGKILL signal as user `${sid}adm`.

On *NODE1*, run the following command.

```sh
su - ${sid}adm
HDB kill -9
```
{: pre}

#### Test1 - Expected behavior
{: #test1-expected-behavior}

- SAP HANA primary instance on `NODE1` crashes.
- The cluster detects the stopped primary HANA database and marks the resource as `failed`.
- The cluster promotes the secondary HANA database on `NODE2` to take over as new primary.
- The cluster releases the virtual IP address on `NODE1`, and acquires it on the new primary on `NODE2`.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

#### Test1 - Recovery procedure
{: #test1-recovery-procedure}

As cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=false`, the cluster doesn't restart the failed HANA database, and doesn't register it against the new primary. Which means that the status on the new primary (`NODE2`) also shows the secondary in status 'CONNECTION TIMEOUT'.

To reregister the previous primary as new secondary use the following commands.

On *NODE1*, run the following command.

```sh
su - ${sid}adm

hdbnsutil -sr_register --name=${DC1} --remoteHost=${NODE2} --remoteInstance=00 \
  --replicationMode=sync --operationMode=logreplay
```
{: pre}

Verify the system replication status:

```sh
hdbnsutil -sr_state
HDBSettings.sh systemReplicationStatus.py
```
{: pre}

After the manual register and resource refreshes, the new secondary instance restarts and shows up in status synced (`SOK`).

On *NODE1*, run the following command.

```sh
pcs resource refresh SAPHana_${SID}_${INSTNO}
pcs status --full
```
{: pre}

### Test2 - Test failure of node that is running the primary database
{: #test-primary-instance-server-failure}

#### Test2 - Description
{: #test2-description}

Simulate a crash of the node that is running the primary HANA database.

#### Test2 - Preparation
{: #test2-preparation}

Make sure that Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.

On *NODE1*, run the following command.

```sh
pcs resource update SAPHana_${SID}_${INSTNO} AUTOMATED_REGISTER=true
```
{: #pre}

```sh
pcs resource config SAPHana_${SID}_${INSTNO}
```
{: #pre}

#### Test2 - Prerequisites
{: #test2-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both nodes active.
- Cluster is started on `NODE1` and `NODE2`.
- Check SAP HANA System Replication status:
   - Primary SAP HANA database is running on `NODE2`.
   - Secondary SAP HANA database is running on `NODE1`.
   - HANA System Replication is activated and in sync.

#### Test2 - Test procedure
{: #test2-procedure}

Crash primary on `NODE2` by sending a *shutoff* system request.

On *NODE2*, run the following command.

```sh
sync; echo o > /proc/sysrq-trigger
```
{: pre}

#### Test2 - Expected behavior
{: #test2-expected-behavior}

- `NODE2` shuts down.
- The cluster detects the failed node and sets its state to `OFFLINE`.
- The cluster promotes the secondary HANA database on `NODE1` to take over as new primary.
- The cluster acquires the virtual IP address on `NODE1` on the new primary.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

#### Test2 - Recovery procedure
{: #test2-recovery-procedure}

Log in to the {{site.data.keyword.cloud}} Console and start the `NODE2` instance.
Wait until `NODE2` is available again, then restart the cluster framework.

On *NODE2*, run the following command.

```sh
pcs cluster start
```
{: pre}

```sh
pcs status --full
```
{: pre}

As cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`, SAP HANA restarts when *NODE2* rejoins the cluster and the former primary reregisters as a secondary.

### Test3 - Test failure of the secondary database instance
{: #test-secondary-instance-database-failure}

#### Test3 - Description
{: #test3-description}

Simulate a crash of the secondary HANA database.

#### Test3 - Prerequisites
{: #test3-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both nodes active.
- Cluster is started on `NODE1` and `NODE2`.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.
- Check SAP HANA System Replication status:
   - Primary SAP HANA database is running on `NODE1`.
   - Secondary SAP HANA database is running on `NODE2`.
   - HANA System Replication is activated and in sync.

#### Test3 - Test Procedure
{: #test3-procedure}

Crash SAP HANA secondary by sending a SIGKILL signal as user `${sid}adm`.

On *NODE2*, run the following command.

```sh
su - ${sid}adm
HDB kill -9
```
{: pre}

#### Test3 - Expected behavior
{: #test3-expected-behavior}

- SAP HANA secondary on `NODE2` crashes.
- The cluster detects the stopped secondary HANA database and marks the resource as `failed`.
- The cluster restarts the secondary HANA database.
- The cluster detects that the system replication is in sync again.

#### Test3 - Recovery Procedure
{: #test3-recovery-procedure}

Wait until the secondary HANA instance starts and synced again (`SOK`), then cleanup the failed resource actions as shown in `pcs status` or `crm_mon`.

On *NODE2*, run the following command.

```sh
pcs resource refresh SAPHana_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}

### Test4 - Test the manual move of SAPHana resource to another node
{: #test-manual-move}

#### Test4 - Description
{: #test4-description}

Use cluster commands to move the primary instance to the other node for maintenance purposes.

#### Test4 - Prerequisites
{: #test4-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both nodes active.
- Cluster is started on `NODE1` and `NODE2`.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.
- Check SAP HANA System Replication status:
   - Primary SAP HANA database is running on `NODE1`.
   - Secondary SAP HANA database is running on `NODE2`.
   - HANA System Replication is activated and in sync.

#### Test4 - Test Procedure
{: #test4-procedure}

Move SAP HANA primary to other node by using the `pcs resource move` command.

On *NODE1*, run the following command.

```sh
pcs resource move SAPHana_${SID}_${INSTNO}-clone
```
{: pre}

#### Test4 - Expected behavior
{: #test4-expected-behavior}

- The cluster creates location constraints to move the resource.
- The cluster triggers a takeover to the secondary HANA database.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

#### Test4 - Recovery procedure
{: #test4-recovery-procedure}

The automatically created location constraints must be removed to allow automatic failover in the future.

Wait until the primary HANA instance is active and remove the constraints.

The cluster registers and starts the HANA database as new secondary instance.

On *NODE1*, run the following command.

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
pcs status --full
```
{: pre}
