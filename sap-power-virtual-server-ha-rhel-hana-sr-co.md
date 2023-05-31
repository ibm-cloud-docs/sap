---
copyright:
  years: 2023
lastupdated: "2023-05-31"

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Configuring SAP HANA Cost-Optimized Scale-Up System Replication in a RHEL HA Add-On cluster
{: #ha-rhel-hana-sr-cost-optimized}

The following information describes the configuration of a Red Hat Enterprise Linux 8 (RHEL) HA Add-On cluster for managing *SAP HANA Cost-Optimized Scale-Up System Replication*.
The cluster uses virtual server instances in IBM {{site.data.keyword.powerSys_notm}}{: external} as cluster nodes.
{: shortdesc}

## Overview
{: #ha-rhel-hana-sr-co-overview}

The following instructions describe how to automate SAP HANA Scale-Up System Replication for a single database deployment in a *cost-optimized* scenario on a RHEL HA Add-on cluster.

In a *cost-optimized* configuration, a nonproduction SAP HANA system runs on the secondary node during normal operation.
The hardware resources on the secondary node are shared between the nonproduction system and the SAP HANA System Replication secondary.
The memory usage of the production System Replication secondary is reduced by disabling the preload of column table data.

If a failover occurs, the nonproduction instance is stopped automatically before the node takes over the production workload.
The takeover time is longer compared to a performance optimized configuration.

For more information, see the following links:

- [Support Policies for RHEL High Availability Clusters - Management of SAP HANA in a Cluster](https://access.redhat.com/articles/3397471){: external}
- [Automating Cost-Optimized SAP HANA Scale-Up System Replication by using the RHEL HA Add-On](https://access.redhat.com/articles/6716961){: external}.
- [SAP HANA System Replication](https://help.sap.com/docs/SAP_HANA_PLATFORM/4e9b18c116aa42fc84c7dbfd02111aba/afac7100bc6d47729ae8eae32da5fdec.html){: external}.

This information is intended for architects and specialists that are planning a high-availability deployment of SAP HANA on {{site.data.keyword.powerSys_notm}}.
{: note}

## Prerequisites
{: #ha-rhel-hana-sr-co-prerequisites}

- A Red Hat High Availability cluster is deployed on two virtual server instances in {{site.data.keyword.powerSys_notm}}.
   - Install and set up the RHEL HA Add-On cluster according to [Implementing a RHEL HA Add-On cluster on {{site.data.keyword.powerSys_notm}}](#ha-rhel){: external}.
   - Configure and verify fencing as described in the preceding document.
- The virtual server instances need to fulfill hardware and resource requirements for the SAP HANA systems in scope.
   Follow the guidelines that are in the [Planning the Deployment](/docs/sap?topic=sap-power-vs-planning-items){: external}.
- The hostnames of the virtual server instances must meet the SAP HANA requirement.
- SAP HANA is installed on both virtual server instances and SAP HANA System Replication is configured.
   The installation of SAP HANA and setup of HANA System Replication is not specific to the {{site.data.keyword.powerSys_notm}} environment, and you need to follow the standard procedures. For more information, see the following documentation.
   - Install SAP HANA on RHEL:
      - [SAP Note 2772999 - Red Hat Enterprise Linux 8.x: Installation and Configuration](https://me.sap.com/notes/2772999){: external}
      - [SAP Note 2777782 - SAP HANA DB: Recommended OS Settings for RHEL 8](https://me.sap.com/notes/2777782){: external}
   - SAP HANA Server Installation:
      -  [SAP HANA Server Installation and Update Guide](https://help.sap.com/docs/SAP_HANA_PLATFORM/2c1988d620e04368aa4103bf26f17727/7eb0167eb35e4e2885415205b8383584.html){: external}
   - SAP HANA System Replication:
      - [SAP HANA System Replication Guide](https://help.sap.com/docs/SAP_HANA_PLATFORM/4e9b18c116aa42fc84c7dbfd02111aba/afac7100bc6d47729ae8eae32da5fdec.html){: external}
   - Configure SAP HANA System Replication:
      - [Automating SAP HANA Scale-Up System Replication by using the RHEL HA Add-On - 2. SAP HANA System Replication](https://access.redhat.com/articles/3004101#sap-hana-system-replication){: external}
- A valid *RHEL for SAP Applications* or *RHEL for SAP Solutions* subscription is required to enable the repositories that you need to install SAP HANA and the resource agents for HA configurations.
   The [RHEL for SAP Subscriptions and Repositories](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux_for_sap_solutions/8/html/rhel_for_sap_subscriptions_and_repositories/asmb_enable_repo_rhel-for-sap-subscriptions-and-repositories-8#con_hana_rhel-for-sap-subscriptions-and-repositories-8){: external} documenation describes how to enable the required repositories.
- A nonproduction SAP HANA System is installed on NODE2 with a different `SID` and `Instance Number` than the production system.
   The nonproduction system needs its own dedicated storage volumes and file systems.
   Restrict the `global_allocation_limit` in the `[memorymanager]` section of the `global.ini` configuration for the nonproduction system to ensure sufficient memory for the HANA system replication workload on the secondary.
- Optional, a virtual IP address is reserved for the nonproduction system as described in [Reserving virtual IP addresses](#ha-vsi-reserve-virtual-ip-addresses).

## Setting up the cost optimized scenario
{: #ha-rhel-hana-sr-co-setup}

The cost optimized scenario is an extension of the setup that is described in [Configure SAP HANA Scale-Up System Replication in a RHEL HA Add-On cluster](#ha-rhel-hana-sr).
Complete the setup for the production system System Replication cluster before you continue with the following steps.

### Configuring the nonproduction SAP HANA Instance on NODE2
{: #ha-rhel-hana-sr-co-non-prod}

To simplify the setup, prepare the following environment variables for user ID `root` on NODE2.
These environment variables are used in subsequent commands in the remainder of the instructions.

On NODE2, create a file with the following environment variables.
Then, adapt them according to the configuration of the nonproduction system.

```sh
export SID_np=<SID>            # SAP HANA System ID of non-production system (uppercase)
export sid_np=<sid>            # SAP HANA System ID of non-production system (lowercase)
export INSTNO_np=<INSTNO>      # SAP HANA Instance Number of non-production system
export NODE1=<Hostname 1>      # Hostname of virtual server instance 1 (production system, primary)
export NODE2=<Hostname 2>      # Hostname of virtual server instance 2 (non-production system, takeover for production)
export vIP_np=<vIP>            # Optional: dedicated virtual IP address assigned to the non-production system
```
{: codeblock}

You must source this file before you use the sample commands in the remainder of this document.

For example, if you created a file that is called `sap_non_prod.sh`, run the following command on NODE2 to set the environment variables.

```sh
source sap_non_prod.sh
```
{: pre}

Every time that you start a new terminal session, you must run the previous `source` command.
As an alternative, you can move add the environment variables file to the `/etc/profile.d` directory during the cluster configuration.
In this example, the file is sourced automatically each time you log in to the server.
{: important}

### Configuring the SAP HANA HA/DR provider hook
{: #ha-rhel-hana-sr-co-hook}

The SAP HANA nameserver provides a Python-based API that is called at important points during the HANA System Replication takeover process.
These API calls are used to run customer-specific operations ([Implementing a HA/DR Provider](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/1367c8fdefaa4808a7485b09815ae0f3.html){: external}).

In the cost-optimized scenario, the SAP HANA HA/DR provider hook is used to automatically reconfigure the SAP HANA instance during the takeover event.

The following section shows a sample set up of a hook script for a *cost-optimized* SAP HANA System Replication environment.
When you implement automation of the cost-optimized SAP HANA System Replication HA environment in the cluster, the takeover hook script must be thoroughly tested.
Run the tests manually.
Shut down the nonproduction SAP HANA instance on the secondary node, perform a takeover, and verify that the hook script correctly reconfigures the primary HANA DB.

#### Creating a database user in the SAP HANA production database
{: #ha-rhel-hana-sr-co-user}

Use the following steps to create a database user in the SAP HANA production database.

1. Create a database user in the *SystemDB* of the SAP HANA production system,
    or provide credentials of an existing user.
    The hook script uses this database user to connect to the production database and alter the configuration parameters.

    Log in to the *SystemDB* of the primary instance by using the SAP HANA database interactive terminal *hdbsql* or *SAP HANA Cockpit*, and create a new user.

    For example, connect to the database by using *hdbsql* in a terminal session.

    ```sh
    sudo -i -u ${sid}adm -- hdbsql -i ${INSTNO} -d SYSTEMDB -u SYSTEM
    ```
    {: pre}

   Create a user.

    ```sh
    CREATE USER HA_HOOK PASSWORD <Password> no force_first_password_change;
    ```
   {: codeblock}

   Grant the required privileges to the user.

   Grant privilege *INIFILE ADMIN* to allow for changes of profile parameters.
   ```sh
   GRANT INIFILE ADMIN TO HA_HOOK;
    ```
   {: codeblock}

   Verify the new *HA_HOOK* user.

   ```sh
    sudo -i -u ${sid}adm -- hdbsql -d SYSTEMDB -u SYSTEM select \* from users where user_name = \'HA_HOOK\';
   ```
   {: pre}

1. Add the user credentials to the secure user store *hdbuserstore*.

   On both nodes, run the following command.
   Use the password that you set in the previous step.

   ```sh
   sudo -i -u ${sid}adm -- hdbuserstore SET HA_HOOK_KEY localhost:3${INSTNO}13 HA_HOOK <Password>
   ```
   {: pre}

   Check the update to the *hdbuserstore*.

   ```sh
   sudo -i -u ${sid}adm -- hdbuserstore list
   ```
   {: pre}

   On the primary instance, test the connection with the stored user key.

   ```sh
   sudo -i -u ${sid}adm  -- hdbsql -U HA_HOOK_KEY select \* from m_inifiles;
   ```
   {: pre}

#### Creating the hook script
{: #ha-rhel-hana-sr-co-create-hook}

Python sample files for creating hook scripts are delivered as part of the SAP HANA installation.
The samples are located in directory `$DIR_INSTANCE/exe/python_support/hdb_ha_dr`.

The target directory `/hana/shared/myHooks` was already created for hook `SAPHanaSR.py`.
Create a HA/DR provider hook in `/hana/shared/myHooks`.
The following hook script is based on the `HADRdummy.py`sample.

On NODE2, edit the file `/hana/shared/myHooks/SAPHanaCostOptSR.py` and add the following content.

```python
"""
Sample for a HA/DR hook provider.

When using your own code in here, please copy this file to location on /hana/shared outside the HANA installation.
This file will be overwritten with each hdbupd call! To configure your own changed version of this file, please add
to your global.ini lines similar to this:

    [ha_dr_provider_<className>]
    provider = <className>
    path = /hana/shared/haHook
    execution_order = 1


For all hooks, 0 must be returned in case of success.
"""

from __future__ import absolute_import
from hdb_ha_dr.client import HADRBase, Helper
from hdbcli import dbapi
import os, time

class SAPHanaCostOptSR(HADRBase):

    def __init__(self, *args, **kwargs):
        # delegate construction to base class
        super(SAPHanaCostOptSR, self).__init__(*args, **kwargs)

    def about(self):
        return {"provider_company" :        "SAP",
                "provider_name" :           "SAPHanaCostOptSR", # provider name = class name
                "provider_description" :    "Handle reconfiguration event for cost-optimized system replication",
                "provider_version" :        "1.0"}

    def postTakeover(self, rc, **kwargs):
        """Post takeover hook."""

        # prepared SQL statements to remove memory allocation limit and pre-load of column tables
        stmnt1 = "ALTER SYSTEM ALTER CONFIGURATION ('global.ini','SYSTEM') UNSET ('memorymanager','global_allocation_limit') WITH RECONFIGURE"
        stmnt2 = "ALTER SYSTEM ALTER CONFIGURATION ('global.ini','SYSTEM') UNSET ('system_replication','preload_column_tables') WITH RECONFIGURE"

        myPort = int('3' + os.environ.get('DIR_INSTANCE')[-2:] + '15')
        myKey = self.config.get("userkey") if self.config.hasKey("userkey") else "HA_HOOK_KEY"

        self.tracer.info("%s.postTakeover method called with rc=%s" % (self.__class__.__name__, rc))
        self.tracer.info("%s.postTakeover method: userkey: %s, port: %s" % (self.__class__.__name__, myKey, myPort))

        if rc in (0, 1):
            # rc == 0: normal takeover succeeded
            # rc == 1: waiting for force takeover
            conn = dbapi.connect(userkey=myKey, address='localhost', port=myPort)
            self.tracer.info("%s: Connect using userkey %s - %s" % (self.__class__.__name__, myKey, conn.isconnected()))
            cursor = conn.cursor()
            rc1 = cursor.execute(stmnt1)
            self.tracer.info("%s: (%s) - %s" % (self.__class__.__name__, stmnt1, rc1))
            rc2 = cursor.execute(stmnt2)
            self.tracer.info("%s: (%s) - %s" % (self.__class__.__name__, stmnt2, rc2))
            return 0
        elif rc == 2:
            # rc == 2: error, something went wrong
            return 0
```
{: codeblock}

#### Activating the cost optimized hook
{: #ha-rhel-hana-sr-co-activate-hook}

Use the following steps to activate the cost optimized hook.

1. Stop the cluster.

   On any cluster node, run the following command.

   ```sh
   pcs cluster stop --all
   ```
   {: pre}

1. Set the file ownership of the hook script.

   On NODE2, run the following command.

   ```sh
   chown -R ${sid}adm:sapsys /hana/shared/myHooks
   ```
   {: pre}

1. Update the `global.ini` configuration file on NODE2 to enable the hook script.

   On NODE2, run the following command to add the required parameters to the `global.ini` file.

   ```sh
   sudo -i -u ${sid}adm -- <<EOT
       python \$DIR_INSTANCE/exe/python_support/setParameter.py \
         -set SYSTEM/global.ini/ha_dr_provider_SAPHanaCostOptSR/provider=SAPHanaCostOptSR \
         -set SYSTEM/global.ini/ha_dr_provider_SAPHanaCostOptSR/path=/hana/shared/myHooks \
         -set SYSTEM/global.ini/ha_dr_provider_SAPHanaCostOptSR/userkey=HA_HOOK_KEY \
         -set SYSTEM/global.ini/ha_dr_provider_SAPHanaCostOptSR/execution_order=2 \
         -set SYSTEM/global.ini/trace/ha_dr_saphanacostoptsr=info
   EOT
   ```
   {: pre}

1. Check the content of the `global.ini` file.

   ```sh
   cat /hana/shared/${SID}/global/hdb/custom/config/global.ini
   ```
   {: pre}

1. Verify that the hook functions.

   - Restart the HANA instance on NODE2 and verify that the hook script works as expected.
   - Trigger the hook with an SAP HANA takeover operation.
   - Check whether the hook logged anything in the trace files.

   ```sh
   sudo -i -u ${sid}adm -- \
       sh -c 'grep SAPHanaCostOptSR $DIR_INSTANCE/$VTHOSTNAME/trace/nameserver_*.trc'
   ```
   {: pre}

   After you verify that the hooks functions, you can restart the HA cluster.

1. Start the HA cluster.

   On any cluster node, run the following command.

   ```sh
   pcs cluster start --all
   ```
   {: pre}

   Check the status of the cluster.

   ```sh
   pcs status --full
   ```
   {: pre}

### Defining limits for SAP HANA resource usage on the secondary node
{: #ha-rhel-hana-sr-co-limit-resource}

All SAP HANA systems that are running on NODE2 share the avaible memory of the node.
Memory configuration of the secondary system SAP HANA *${SID}* must be limited to the amount required for system replication so that the nonproduction systems can use the remaining memory.

SAP documentation [Secondary System Usage](https://help.sap.com/docs/SAP_HANA_PLATFORM/4e9b18c116aa42fc84c7dbfd02111aba/9d62b8108063497f9d6aab08902b2e04.html?locale=en-US){: external} describes the different scenarios and provideds parameter recommendations.

The preload of column tables on the secondary system is disabled to restrict its memory consumption by setting the database configuration parameter `preload_column_tables = false`.
This parameter is found in the `[system_replication]` section of the instance configuration file for SAP HANA production system on NODE2.

The `global_allocation_limit` is set in the `[memorymanager]` section to limit memory allocation for the SAP HANA production system and the nonproduction system that is running on NODE2.

On NODE2, define an environment variable with the wanted memory limit for the secondary HANA production instance.

```sh
export GLOBAL_ALLOCATION_LIMIT=<memory_size_in_mb_for_hana_secondary>
```
{: pre}

Then, run the following command to update the `global.ini` configuration file.

```sh
sudo -i -u ${sid}adm -- <<EOT
    python \$DIR_INSTANCE/exe/python_support/setParameter.py \
      -set SYSTEM/global.ini/system_replication/preload_column_tables=false \
      -set SYSTEM/global.ini/memorymanager/global_allocation_limit=$GLOBAL_ALLOCATION_LIMIT
EOT
```
{: pre}

Verify the configuration file.
```sh
cat /hana/shared/${SID}/global/hdb/custom/config/global.ini
```
{: pre}

You cannot use `hdbsql` and `ALTER SYSTEM ALTER CONFIGURATION ...` statements on the secondary, no SQL connect is possible in this state.
Activate the change by using the `hdbnsutil –reconfig` command.

```sh
sudo -i -u ${sid}adm -- hdbnsutil -reconfig
```
{: pre}

Update the `global.ini` configuration file of the nonproduction instance to allow for the memory resource usage of the secondary.

On NODE2, define an environment variable with the wanted memory limit for the nonproduction HANA instance.

```sh
export NON_PROD_GLOBAL_ALLOCATION_LIMIT=<memory_size_in_mb_for_non_prod_hana>
```
{: pre}

Then, run the following command to update the `global.ini` configuration file.

```sh
sudo -i -u ${sid_np}adm -- <<EOT
    python \$DIR_INSTANCE/exe/python_support/setParameter.py \
      -set SYSTEM/global.ini/memorymanager/global_allocation_limit=$NON_PROD_GLOBAL_ALLOCATION_LIMIT \
      -reconfigure
EOT
```
{: pre}

Verify the configuration file.
```sh
cat /hana/shared/${SID_np}/global/hdb/custom/config/global.ini
```
{: pre}

Run the following command to check the current database memory limit.

```sh
sudo -i -u ${sid_np}adm -- hdbcons "mm globallimit" | grep limit
```
{: pre}

## Configuring cluster resources for the nonproduction instance
{: #ha-rhel-hana-sr-co-cfg-non-prod}

Use the following information to configure cluster resources for the nonproduction instance.

### Installing the SAPInstance resource agent
{: #ha-rhel-hana-sr-co-install-sap-resource-agent}

The `resource-agents-sap` package includes the *SAPInstance* cluster resource agent, which is used to manage the additional nonproduction SAP HANA instance.

On NODE2, run the following command to install the resource agent.

```sh
dnf install -y resource-agents-sap
```
{: pre}

If needed, use `subscription-manager` to enable the *SAP NetWeaver* repository.
```sh
subscription-manager repos --enable="rhel-8-for-ppc64le-sap-netweaver-e4s-rpms"
```
{: pre}
{: tip}

### Creating the cluster resource for managing the nonproduction instance
{: #ha-rhel-hana-sr-co-create-no-prod.cluster-resorce}

On NODE2, run the following command.

```sh
pcs resource create SAPHana_np_${SID_np}_HDB${INSTNO_np} SAPInstance \
    InstanceName="${SID_np}_HDB${INSTNO_np}_${NODE2}" \
    MONITOR_SERVICES="hdbindexserver|hdbnameserver" \
    START_PROFILE="/usr/sap/${SID_np}/SYS/profile/${SID_np}_HDB${INSTNO_np}_${NODE2}" \
    op start timeout=600 op stop timeout=600 op monitor interval=60 timeout=600 \
    --group group_${sid_np}_non_prod
```
{: pre}

If you want to assign a virtual IP address to the nonproduction instance, add a IPaddr2 cluster resource.

```sh
pcs resource create vip_np IPaddr2 \
    ip="${vIP_np}" \
    --group group_${sid_np}_non_prod
```
{: pre}

Create a cluster constraint to prevent that the nonproduction system starts on NODE1.

```sh
pcs constraint location add loc-${sid_np}-avoid-${NODE1} \
    ${sid_np}_non_prod ${NODE1} -INFINITY resource-discovery=never
```
{: pre}

When the production system assumes the *PRIMARY* role on NODE2 if a takeover occurs, the nonproduction system stops and its memory resources are released.
The following cluster constraints make sure that the primary production instance and the nonproduction instance never run together on one node, and that the nonproduction instance stops before the production instance is promoted.

```sh
pcs constraint colocation add ${sid_np}_non_prod with master SAPHana_${SID}_${INSTNO}-clone score=-INFINITY
```
{: pre}

```sh
pcs constraint order stop ${sid_np}_non_prod then promote SAPHana_${SID}_${INSTNO}-clone
```
{: pre}

The cluster configuration is complete.

Run the following command to check the status of the defined cluster resources.

```sh
pcs status --full
```
{: pre}

Sample output:
```sh
# pcs status --full
Cluster name: SAP_PRD
Cluster Summary:
  * Stack: corosync
  * Current DC: cl-prd-2 (2) (version 2.0.5-9.el8_4.5-ba59be7122) - partition with quorum
  * Last updated: Fri Apr 28 16:38:00 2023
  * Last change:  Fri Apr 28 16:37:49 2023 by hacluster via crmd on cl-prd-1
  * 2 nodes configured
  * 8 resource instances configured

Node List:
  * Online: [ cl-prd-1 (1) cl-prd-2 (2) ]

Full List of Resources:
  * res_fence_ibm_powervs	(stonith:fence_ibm_powervs):	 Started cl-prd-2
  * Clone Set: SAPHanaTopology_PRD_00-clone [SAPHanaTopology_PRD_00]:
    * SAPHanaTopology_PRD_00	(ocf::heartbeat:SAPHanaTopology):	 Started cl-prd-2
    * SAPHanaTopology_PRD_00	(ocf::heartbeat:SAPHanaTopology):	 Started cl-prd-1
  * Clone Set: SAPHana_PRD_00-clone [SAPHana_PRD_00] (promotable):
    * SAPHana_PRD_00	(ocf::heartbeat:SAPHana):	 Slave cl-prd-2
    * SAPHana_PRD_00	(ocf::heartbeat:SAPHana):	 Master cl-prd-1
  * vip_PRD_00	(ocf::heartbeat:IPaddr2):	 Started cl-prd-1
  * Resource Group: group_dev_non_prod:
    * vip_np	(ocf::heartbeat:IPaddr2):	 Started cl-prd-2
    * SAPHana_np_DEV_HDB10	(ocf::heartbeat:SAPInstance):	 Started cl-prd-2

Node Attributes:
  * Node: cl-prd-1 (1):
    * hana_prd_clone_state            	: PROMOTED
    * hana_prd_op_mode                	: logreplay
    * hana_prd_remoteHost             	: cl-prd-2
    * hana_prd_roles                  	: 4:P:master1:master:worker:master
    * hana_prd_site                   	: SiteA
    * hana_prd_srmode                 	: syncmem
    * hana_prd_sync_state             	: PRIM
    * hana_prd_version                	: 2.00.070.00.1679989823
    * hana_prd_vhost                  	: cl-prd-1
    * lpa_prd_lpt                     	: 1682692638
    * master-SAPHana_PRD_00           	: 150
  * Node: cl-prd-2 (2):
    * hana_prd_clone_state            	: DEMOTED
    * hana_prd_op_mode                	: logreplay
    * hana_prd_remoteHost             	: cl-prd-1
    * hana_prd_roles                  	: 4:S:master1:master:worker:master
    * hana_prd_site                   	: SiteB
    * hana_prd_srmode                 	: syncmem
    * hana_prd_sync_state             	: SOK
    * hana_prd_version                	: 2.00.070.00.1679989823
    * hana_prd_vhost                  	: cl-prd-2
    * lpa_prd_lpt                     	: 30
    * master-SAPHana_PRD_00           	: 100

Migration Summary:

Tickets:

PCSD Status:
  cl-prd-1: Online
  cl-prd-2: Online

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```
{: screen}

Run the following command to check the defined constraints.

Sample output:

```sh
# pcs constraint --full
Location Constraints:
  Resource: group_dev_non_prod
    Disabled on:
      Node: cl-prd-1 (score:-INFINITY) (resource-discovery=never) (id:loc-dev-avoid-cl-prd-1)
Ordering Constraints:
  start SAPHanaTopology_PRD_00-clone then start SAPHana_PRD_00-clone (kind:Mandatory) (non-symmetrical) (id:order-SAPHanaTopology_PRD_00-clone-SAPHana_PRD_00-clone-mandatory)
  stop group_dev_non_prod then promote SAPHana_PRD_00-clone (kind:Mandatory) (id:order-group_dev_non_prod-SAPHana_PRD_00-clone-mandatory)
Colocation Constraints:
  vip_PRD_00 with SAPHana_PRD_00-clone (score:2000) (rsc-role:Started) (with-rsc-role:Master) (id:colocation-vip_PRD_00-SAPHana_PRD_00-clone-2000)
  group_dev_non_prod with SAPHana_PRD_00-clone (score:-INFINITY) (rsc-role:Started) (with-rsc-role:Master) (id:colocation-group_dev_non_prod-SAPHana_PRD_00-clone-INFINITY)
Ticket Constraints:
```
{: screen}

### Enabling the automated registration of the secondary instance
{: #ha-rhel-hana-sr-co-enable-automated-registration}

You need to set the parameter `AUTOMATED_REGISTER` according to your operational requirements.
If you want to keep the ability to revert to the state of the previous primary SAP HANA instance, then `AUTOMATED_REGISTER=false` avoids an automatic registration of the previous primary as a new secondary.

If you experience an issue with the data after a takeover that was triggered by the cluster, you can manually revert if `AUTOMATED_REGISTER` is set to `false`.

If `AUTOMATED_REGISTER` is set to `true`, the previous primary SAP HANA instance automatically registers as secondary, and cannot be activated on its previous history.
The advantage of `AUTOMATED_REGISTER=true` is that high-availability is automatically reestablished after the failed node reappears in the cluster.

For now, it is recommended to keep `AUTOMATED_REGISTER` on default value `false` until the cluster is fully tested and that you verify that the failover scenarios work as expected.

The `pcs resource update` command is used to modify resource attributes and `pcs resource update SAPHana_${SID}_${INSTNO} AUTOMATED_REGISTER=true` sets the attribute to `true`.
{: tip}

## Testing the SAP HANA System Replication cluster
{: #ha-rhel-hana-sr-co-test-sap-hana-sr-cluster}

It is vital to thoroughly test the cluster configuration to make sure that the cluster is working correctly.
The following information provides a few sample failover test scenarios, but is not a complete list of test scenarios.

For example, the description of each test case includes the following information.

- Which component is being tested
- Description of the test
- Prerequisites and the initial state before you start the failover test
- Test procedure
- Expected behavior and results
- Recovery procedure

### Test1 - Testing the failure of the primary database instance
{: #ha-rhel-hana-sr-co-test1-primary-instance-database-failure}

Use the following information to test the failure of the primary database instance.

#### Test1 - Description
{: #ha-rhel-hana-sr-co-test1-description}

Simulate a crash of the primary HANA database instance that is running on NODE1.

#### Test1 - Prerequisites
{: #ha-rhel-hana-sr-co-test1-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both cluster nodes are active.
- Cluster is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=false`.
- Check SAP HANA System Replication status:
   - The primary SAP HANA database is running on NODE1.
   - The secondary SAP HANA database is running on NODE2.
   - HANA System Replication is activated and in sync.
- The secondary SAP HANA database on NODE2 is running with reduced memory configuration.
   - The `global_allocation_limit` is reduced.
   - Preload of column tables is disabled (`preload_column_tables = false`).
- A nonproduction SAP HANA system `${SID_np}`is running on NODE2.

#### Test1 - Test procedure
{: #ha-rhel-hana-sr-co-test1-procedure}

Crash SAP HANA primary by sending a SIGKILL signal as user `${sid}adm`.

On NODE1, run the following command.

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test1 - Expected behavior
{: #ha-rhel-hana-sr-co-test1-expected-behavior}

- SAP HANA primary instance on NODE1 crashes.
- The cluster detects the stopped primary HANA database and marks the resource as `failed`.
- The cluster promotes the secondary HANA database on NODE2 to take over as new primary.
   - The cluster stops the nonproduction database `${SID_np}` on NODE2.
   - During activation, the `global_allocation_limit` and `preload_column_tables` parameters are reset to default.
- The cluster releases the virtual IP address on NODE1, and acquires it on the new primary on NODE2.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

On NODE2, run the following commands to check that the `global_allocation_limit`and `preload_column_tables` are unset.

```sh
sudo -i -u ${sid}adm -- hdbcons "mm globallimit" | grep limit
```
{: pre}

```sh
grep -E "global_allocation_limit|preload_column_tables" \
     /hana/shared/${SID}/global/hdb/custom/config/global.ini
```
{: pre}

#### Test1 - Recovery procedure
{: #ha-rhel-hana-sr-co-test1-recovery-procedure}

As the cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=false`, the cluster doesn't restart the failed HANA database, and doesn't register it against the new primary.
The status on the new primary (NODE2) shows the secondary in status 'CONNECTION TIMEOUT'.


On NODE1, run the following commands to register the previous primary as new secondary.

```sh
sudo -i -u ${sid}adm -- <<EOT
    hdbnsutil -sr_register \
        --name=${DC1} \
        --remoteHost=${NODE2} \
        --remoteInstance=00 \
        --replicationMode=sync \
        --operationMode=logreplay
EOT
```
{: pre}

Verify the system replication status.

```sh
sudo -i -u ${sid}adm -- <<EOT
    hdbnsutil -sr_state
    HDBSettings.sh systemReplicationStatus.py
EOT
```
{: pre}

On NODE1, run the following command to start the cluster node.

```sh
pcs cluster start
```
{: pre}

The new secondary instance restarts and shows up in status synced (`SOK`).

```sh
pcs status --full
```
{: pre}

Configure cluster resource `SAPHana_${SID}_${INSTNO}` with `AUTOMATED_REGISTER=true`.

On NODE1, run the following command.

```sh
pcs resource update SAPHana_${SID}_${INSTNO} AUTOMATED_REGISTER=true
```
{: pre}

```sh
pcs resource config SAPHana_${SID}_${INSTNO}
```
{: pre}

### Test2 - Testing the manual move of SAPHana resource to another node
{: #ha-rhel-hana-sr-co-test2-manual-move}

Use the following information to test the manual move of SAPHana resource to another node.

#### Test2 - Description
{: #ha-rhel-hana-sr-co-test2-description}

Use cluster commands to move the primary instance back to the other node.

#### Test2 - Prerequisites
{: #ha-rhel-hana-sr-co-test2-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both cluster nodes are active.
- Cluster is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.
- Check SAP HANA System Replication status:
   - The primary SAP HANA database is running on NODE2.
   - The secondary SAP HANA database is running on NODE1.
   - HANA System Replication is activated and in sync.
- The nonproduction SAP HANA system `${SID_np}`is stopped on NODE2.

#### Test2 - Test Preparation
{: #ha-rhel-hana-sr-co-test2-preparation}

Unmanage the cluster resource for the nonproduction SAP HANA system to prevent that it starts when the memory resources of the secondary are not restricted.

On NODE1, run the following command.

```sh
pcs resource unmanage group_${sid_np}_non_prod
```
{: pre}

#### Test2 - Test Procedure
{: #ha-rhel-hana-sr-co-test2-procedure}

On NODE1, run the following command to move the SAP HANA primary back to NODE1.

```sh
pcs resource move SAPHana_${SID}_${INSTNO}-clone
```
{: pre}

#### Test2 - Expected behavior
{: #ha-rhel-hana-sr-co-test2-expected-behavior}

- The cluster creates a location constraint to move the resource.
- The cluster triggers a takeover to the secondary HANA database on NODE1.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.
- The resource of the nonproduction SAP HANA system *`${SID_np}`* is in the *unmanaged* state and isn't started automatically.

#### Test2 - Recovery procedure
{: #ha-rhel-hana-sr-co-test2-recovery-procedure}

Several steps need to be followed to reestablish the complete HA scenario.

1. Wait until the primary HANA instance is active.
    Then, reduce the memory footprint of the secondary.

    On NODE2, run the following commands to reduce the memory.

    ```sh
    export GLOBAL_ALLOCATION_LIMIT=<size_in_mb_for_hana_secondary>
    ```
    {: pre}

    ```sh
    sudo -i -u ${sid}adm -- <<EOT
        python \$DIR_INSTANCE/exe/python_support/setParameter.py \
          -set SYSTEM/global.ini/system_replication/preload_column_tables=false \
          -set SYSTEM/global.ini/memorymanager/global_allocation_limit=$GLOBAL_ALLOCATION_LIMIT
    EOT
    ```
    {: pre}

1. Remove the location constraint, which triggers the start of the secondary instance.

    ```sh
    pcs resource clear SAPHana_${SID}_${INSTNO}-clone
    ```
    {: pre}

    Verify that the constraint is cleared.

    ```sh
    pcs constraint
    ```
    {: pre}

    Check the cluster status.

    ```sh
    pcs status --full
    ```
    {: pre}

1. On NODE2, run the following commands to check that the `global_allocation_limit`and `preload_column_tables` are set.

   ```sh
   sudo -i -u ${sid}adm -- hdbcons "mm globallimit" | grep limit
   ```
   {: pre}

   ```sh
   grep -E "global_allocation_limit|preload_column_tables" \
        /hana/shared/${SID}/global/hdb/custom/config/global.ini
   ```
   {: pre}

1. Reactivate the resource for the nonproduction SAP HANA system.

   On NODE2, run the following command.

   ```sh
   pcs resource manage group_${sid_np}_non_prod
   ```
   {: pre}

   The resource of the nonproduction SAP HANA system `${SID_np}` is *managed* and the nonproduction system starts on NODE2.

   ```sh
   pcs status --full
   ```
   {: pre}

### Test3 - Testing failure of node that is running the primary database
{: #ha-rhel-hana-sr-co-test3-primary-instance-server-failure}

Use the following information to test the failure of node that is running the primary database.

#### Test3 - Description
{: #ha-rhel-hana-sr-co-test3-description}

Simulate a crash of the node that is running the primary HANA database.

#### Test3 - Prerequisites
{: #ha-rhel-hana-sr-co-test3-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both cluster nodes are active.
- Cluster is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.
- Check SAP HANA System Replication status:
   - The primary SAP HANA database is running on NODE1.
   - The secondary SAP HANA database is running on NODE2.
   - HANA System Replication is activated and in sync.
- The secondary SAP HANA database on NODE2 is running with reduced memory configuration.
   - The `global_allocation_limit` is reduced.
   - Preload of column tables is disabled (`preload_column_tables = false`).
- A nonproduction SAP HANA system `${SID_np}`is running on NODE2.

#### Test3 - Test procedure
{: #ha-rhel-hana-sr-co-test3-procedure}

Crash primary on NODE1 by sending a *shutoff* system request.

On NODE1, run the following command.

```sh
sync; echo o > /proc/sysrq-trigger
```
{: pre}

#### Test3 - Expected behavior
{: #ha-rhel-hana-sr-co-test3-expected-behavior}

- NODE1 shuts down.
- The cluster detects the failed node and sets its state to `OFFLINE`.
- The cluster promotes the secondary HANA database on NODE2 to take over as new primary.
   - The cluster stops the nonproduction database `${SID_np}` on NODE2.
   - During activation, the `global_allocation_limit` and `preload_column_tables` parameters of SAP HANA `${SID}`are reset.
- The cluster acquires the virtual IP address on NODE2 on the new primary.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

#### Test3 - Recovery procedure
{: #ha-rhel-hana-sr-co-test3-recovery-procedure}

Log in to the {{site.data.keyword.cloud}} console and start NODE1.
Wait until NODE1 is available again, then restart the cluster framework.

On NODE1, run the following command.

```sh
pcs cluster start
```
{: pre}

```sh
pcs status --full
```
{: pre}

As cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`, SAP HANA restarts when NODE1 joins the cluster and the former primary is registered as secondary.

Then, rerun the steps in [Test2 - Test the manual move of SAPHana resource to another node](#ha-rhel-hana-sr-co-test2-manual-move)
to revert to the initial situation.

### Test4 - Testing failure of the secondary database instance
{: #ha-rhel-hana-sr-co-test4-secondary-instance-database-failure}

Use the following information to test the failure of the secondary database instance.

#### Test4 - Description
{: #ha-rhel-hana-sr-co-test4-description}

Simulate a crash of the secondary HANA database.

#### Test4 - Prerequisites
{: #ha-rhel-hana-sr-co-test4-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both nodes active.
- Cluster is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.
- Check SAP HANA System Replication status:
   - The primary SAP HANA database is running on NODE1.
   - The secondary SAP HANA database is running on NODE2.
   - HANA System Replication is activated and sync.

#### Test4 - Test Procedure
{: #ha-rhel-hana-sr-co-test4-procedure}

Crash SAP HANA secondary by sending a SIGKILL signal as user `${sid}adm`.

On NODE2, run the following command.

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test4 - Expected behavior
{: #ha-rhel-hana-sr-co-test4-expected-behavior}

- SAP HANA secondary on NODE2 crashes.
- The cluster detects the stopped secondary HANA database and marks the resource as `failed`.
- The cluster restarts the secondary HANA database.
- The cluster detects that the system replication is in sync again.

#### Test4 - Recovery Procedure
{: #ha-rhel-hana-sr-co-test4-recovery-procedure}

Wait until the secondary HANA instance starts and synchronized again (`SOK`), then cleanup the failed resource actions as shown in `pcs status`.

On NODE2, run the following command.

```sh
pcs resource refresh SAPHana_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}
