---

copyright:
  years: 2023, 2024

lastupdated: "2024-11-06"

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

# Multizone region
export CLOUD_REGION=<CLOUD_REGION>       # Multizone region name
export APIKEY="APIKEY or path to file"   # API Key of the IBM Cloud IAM ServiceID for the resource agent
export API_TYPE="private or public"      # Use private or public API endpoints
export IBMCLOUD_CRN_1=<IBMCLOUD_CRN_1>   # Workspace 1 CRN
export IBMCLOUD_CRN_2=<IBMCLOUD_CRN_2>   # Workspace 2 CRN
export POWERVSI_1=<POWERVSI_1>           # Virtual server instance 1 id
export POWERVSI_2=<POWERVSI_2>           # Virtual server instance 2 id
export SUBNET_NAME="vip-${sid}-net"      # Name which is used to define the subnet in IBM Cloud
export CIDR="CIDR of subnet"             # CIDR of the subnet containing the service IP address
export VIP="Service IP address"          # IP address in the subnet
export JUMBO="true or false"             # Enable Jumbo frames
```
{: codeblock}

#### Setting extra environment variables for a single zone implementation
{: #ha-rhel-hana-sr-sz-prepare-environment-variables}

Review the information in [Reserving virtual IP addresses](/docs/sap?topic=sap-ha-vsi#ha-vsi-reserve-virtual-ip-addresses) and reserve a virtual IP address for the SAP HANA System Replication cluster.
Set the `VIP`environment variable to the reserved IP address.

#### Setting extra environment variables for a multizone region implementation
{: #ha-rhel-hana-sr-mz-prepare-environment-variables}

Set the `CLOUD_REGION`, `APIKEY`, `IBMCLOUD_CRN_?`, `POWERVSI_?` variables as described in [Collecting parameters for configuring a RHEL HA Add-On cluster](/docs/sap?topic=sap-ha-vsi#ha-vsi-create-service-api-key) section.
Set the `API_TYPE` variable to `private` to communicate with the IBM Cloud IAM and IBM Power Cloud API via private endpoints.
The `SUBNET_NAME` variable contains the name of the subnet.
The `CIDR` variable represents the *Classless Inter-Domain Routing (CIDR)* notation for the subnet in the format `<IPv4_address>/number`.
The `VIP` variable is the IP address of the virtual IP address resource and must belong to the `CIDR` of the subnet.
Set the `JUMBO` variable to `true` if you want to enable the subnet for a large MTU size.

The following is an example of how to set the extra environment variables that are required for a multizone region implementation.

```sh
export CLOUD_REGION="eu-de"
export IBMCLOUD_CRN_1="crn:v1:bluemix:public:power-iaas:eu-de-2:a/a1b2c3d4e5f60123456789a1b2c3d4e5:a1b2c3d4-0123-4567-89ab-a1b2c3d4e5f6::"
export IBMCLOUD_CRN_2="crn:v1:bluemix:public:power-iaas:eu-de-1:a/a1b2c3d4e5f60123456789a1b2c3d4e5:e5f6a1b2-cdef-0123-4567-a1b2c3d4e5f6::"
export POWERVSI_1="a1b2c3d4-0123-890a-f012-0123456789ab"
export POWERVSI_2="e5f6a1b2-4567-bcde-3456-cdef01234567"
export APIKEY="@/root/.apikey.json"
export API_TYPE="private"
export SUBNET_NAME="vip-mha-net"
export CIDR="10.40.11.100/30"
export VIP="10.40.11.102"
export JUMBO="true"
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

   Check that the hook logged some messages to the trace files.

   ```sh
   sudo -i -u ${sid}adm -- sh -c 'grep "ha_dr_SAPHanaSR.*crm_attribute" $DIR_INSTANCE/$VTHOSTNAME/trace/nameserver_* | cut -d" " -f2,3,5,17'
   ```
   {: pre}

   After you verify that the hooks function, you can restart the HA cluster.

1. Start the cluster.

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

The *SAPHanaTopology* resource gathers the status and configuration of SAP HANA System Replication on each node.
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

On NODE1, create the *SAPHana* resource by running the following command.

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

### Creating a virtual IP address cluster resource
{: #ha-rhel-hana-sr-create-virtual-ip-resource}

Depending on the scenario, proceed to one of the following sections:

- [Creating a virtual IP address cluster resource in a single zone environment](#ha-rhel-hana-sr-sz-create-virtual-ip-resource) if the cluster nodes are running in a single {{site.data.keyword.powerSys_notm}} workspace
- [Creating a virtual IP address cluster resource in a multizone region environment](#ha-rhel-hana-sr-mz-create-virtual-ip-resource) if the cluster nodes are running in separate {{site.data.keyword.powerSys_notm}} workspaces

#### Creating a virtual IP address cluster resource in a single zone environment
{: #ha-rhel-hana-sr-sz-create-virtual-ip-resource}

Use the reserved IP address to create a virtual IP address cluster resource.
This virtual IP address is used to reach the SAP HANA System Replication primary instance.

Create the virtual IP address cluster resource with the following command.

```sh
pcs resource create vip_${SID}_${INSTNO} IPaddr2 ip=$VIP
```
{: pre}

Check the configured virtual IP address cluster resource and the cluster status.

```sh
pcs resource config vip_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}

Proceed to the [Creating cluster resource constraints](#ha-rhel-hana-sr-create-constraints) section.

#### Creating a virtual IP address cluster resource in a multizone region environment
{: #ha-rhel-hana-sr-mz-create-virtual-ip-resource}

Verify that you have completed all the steps in the [Preparing a multi-zone RHEL HA Add-On cluster for a virtual IP address resource](/docs/sap?topic=sap-ha-rhel-mz#ha-rhel-mz-create-vip) section.

Run the `pcs resource describe powervs-subnet` command to get information about the resource agent parameters.
{: note}

On NODE1, create a `powervs-subnet` cluster resource by running the following command.

```sh
pcs resource create vip_${SID}_${INSTNO} powervs-subnet \
    api_key=${APIKEY} \
    api_type=${API_TYPE} \
    cidr=${CIDR} \
    ip=${VIP} \
    crn_host_map="${NODE1}:${IBMCLOUD_CRN_1};${NODE2}:${IBMCLOUD_CRN_2}" \
    vsi_host_map="${NODE1}:${POWERVSI_1};${NODE2}:${POWERVSI_2}" \
    jumbo=${JUMBO} \
    region=${CLOUD_REGION} \
    subnet_name=${SUBNET_NAME} \
    op start timeout=720 \
    op stop timeout=300 \
    op monitor interval=60 timeout=30
```
{: pre}

If you set `API_TYPE` to `public`, you must also specify a `proxy` parameter.
{: note}

Ensure that both virtual server instances in the cluster have the status `Active` and the health status `OK` before running the `pcs resource config` command.
{: important}

Check the configured virtual IP address resource and the cluster status.

```sh
pcs resource config vip_${SID}_${INSTNO}
```
{: pre}

Sample output:

```sh
# pcs resource config vip_MHA_00
Resource: vip_MHA_00 (class=ocf provider=heartbeat type=powervs-subnet)
  Attributes: vip_MHA_00-instance_attributes
    api_key=@/root/.apikey.json
    api_type=private
    cidr=10.40.11.100/30
    crn_host_map=cl-mha-1:crn:v1:bluemix:public:power-iaas:eu-de-2:**********************************:************************************::;cl-mha-2:crn:v1:bluemix:public:power-iaas:eu-
        de-1:**********************************:************************************::
    ip=10.40.11.102
    jumbo=true
    proxy=http://10.30.40.4:3128
    region=eu-de
    subnet_name=vip-mha-net
    vsi_host_map=cl-mha-1:************************************;cl-mha-2:************************************
  Operations:
    monitor: res_vip_MHA_00-monitor-interval-60
      interval=60
      timeout=60
    start: res_vip_MHA_00-start-interval-0s
      interval=0s
      timeout=720
    stop: res_vip_MHA_00-stop-interval-0s
      interval=0s
      timeout=300
```
{: screen}


```sh
pcs status --full
```
{: pre}

The following example is a sample output of an SAP HANA System Replication cluster in a multizone region setup.

```sh
# pcs status --full
Cluster name: SAP_MHA
Status of pacemakerd: 'Pacemaker is running' (last updated 2024-07-31 11:37:49 +02:00)
Cluster Summary:
  * Stack: corosync
  * Current DC: cl-mha-2 (2) (version 2.1.5-9.el9_2.4-a3f44794f94) - partition with quorum
  * Last updated: Wed Jul 31 11:37:50 2024
  * Last change:  Wed Jul 31 11:37:31 2024 by root via crm_attribute on cl-mha-1
  * 2 nodes configured
  * 7 resource instances configured

Node List:
  * Node cl-mha-1 (1): online, feature set 3.16.2
  * Node cl-mha-2 (2): online, feature set 3.16.2

Full List of Resources:
  * fence_node1	(stonith:fence_ibm_powervs):	 Started cl-mha-1
  * fence_node2	(stonith:fence_ibm_powervs):	 Started cl-mha-2
  * Clone Set: SAPHanaTopology_MHA_00-clone [SAPHanaTopology_MHA_00]:
    * SAPHanaTopology_MHA_00	(ocf:heartbeat:SAPHanaTopology):	 Started cl-mha-2
    * SAPHanaTopology_MHA_00	(ocf:heartbeat:SAPHanaTopology):	 Started cl-mha-1
  * Clone Set: SAPHana_MHA_00-clone [SAPHana_MHA_00] (promotable):
    * SAPHana_MHA_00	(ocf:heartbeat:SAPHana):	 Unpromoted cl-mha-2
    * SAPHana_MHA_00	(ocf:heartbeat:SAPHana):	 Promoted cl-mha-1
  * vip_MHA_00	(ocf:heartbeat:powervs-subnet):	 Started cl-mha-1

Node Attributes:
  * Node: cl-mha-1 (1):
    * hana_mha_clone_state            	: PROMOTED
    * hana_mha_op_mode                	: logreplay
    * hana_mha_remoteHost             	: cl-mha-2
    * hana_mha_roles                  	: 4:P:master1:master:worker:master
    * hana_mha_site                   	: SiteA
    * hana_mha_sra                    	: -
    * hana_mha_srah                   	: -
    * hana_mha_srmode                 	: syncmem
    * hana_mha_sync_state             	: PRIM
    * hana_mha_version                	: 2.00.075.00
    * hana_mha_vhost                  	: cl-mha-1
    * lpa_mha_lpt                     	: 1722418651
    * master-SAPHana_MHA_00           	: 150
  * Node: cl-mha-2 (2):
    * hana_mha_clone_state            	: DEMOTED
    * hana_mha_op_mode                	: logreplay
    * hana_mha_remoteHost             	: cl-mha-1
    * hana_mha_roles                  	: 4:S:master1:master:worker:master
    * hana_mha_site                   	: SiteB
    * hana_mha_sra                    	: -
    * hana_mha_srah                   	: -
    * hana_mha_srmode                 	: syncmem
    * hana_mha_sync_state             	: SOK
    * hana_mha_version                	: 2.00.075.00
    * hana_mha_vhost                  	: cl-mha-2
    * lpa_mha_lpt                     	: 30
    * master-SAPHana_MHA_00           	: 100

Migration Summary:

Tickets:

PCSD Status:
  cl-mha-1: Online
  cl-mha-2: Online

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```
{: screen}

Proceed to the [Creating cluster resource constraints](#ha-rhel-hana-sr-create-constraints) section.

### Creating cluster resource constraints
{: #ha-rhel-hana-sr-create-constraints}

Make sure that *SAPHanaTopology* resources are started before you start the *SAPHana* resources.

The virtual IP address must be present on the node where the primary resource of "SAPHana" is running.

1. Create a resource constraint to start "SAPHanaTopology" before "SAPHana".
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

1. Create a resource constraint to colocate the virtual IP address with primary.
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

SAP HANA has built-in functions to monitor its `indexserver`.
In case of a problem, SAP HANA tries to recover automatically by stopping and restarting the process.
To stop the process or clean up after a crash, the Linux kernel must release all memory that is allocated by the process.
For large databases, this cleanup can take a long time.
During this time, SAP HANA continues to operate and accept new client requests.
As a result, SAP HANA system replication can become out of sync.
If another error occurs in the SAP HANA instance before the restart and recovery of the `indexserver` is complete, data consistency is at risk.

The `ChkSrv.py` script for the `srServiceStateChanged()` hook reacts to such a situation and can stop the entire SAP HANA instance for faster recovery.
If `automated failover` is enabled in the cluster, and the secondary node is in a healthy state, a takeover operation is started.
Otherwise, recovery must continue locally, but is accelerated by the forced restart of the SAP HANA instance.

The SAP HANA `srServiceStateChanged()` hook is available with `resource-agents-sap-hana` version 0.162.3 and later.
{: note}

The hook script analyzes the events in the instance, applies filters to the event details, and triggers actions based on the results.
It distinguishes between an SAP HANA `indexserver` process that is stopped and restarted by SAP HANA and the process that is stopped during an instance shutdown.

Depending on the configuration of the `action_on_lost` parameter, the hook takes different actions:

Ignore
:   This action simply logs the events and decision information to a log file.

Stop
:  This action triggers a graceful stop of the SAP HANA instance by using the sapcontrol command.

Kill
:   This action triggers the HDB kill-`<signal>` command with a default signal 9.
    The signal can be configured.

Both the *stop* and the *kill* actions result in a stopped SAP HANA instance, the *kill* action is slightly faster.

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

   The `action_on_lost` parameter in this example is set to `stop`, the default setting is `ignore`.
   You can optionally set the parameters `stop_timeout` (default: `20` seconds) and `kill_signal` (default: `9`).

1. Activate the `ChkSrv.py` hook

   On both nodes, run the following command to reload the HA-DR providers.

   ```sh
   sudo -i -u ${sid}adm -- hdbnsutil -reloadHADRProviders
   ```
   {: pre}

1. Check that the hook logged some messages to the trace files.

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

### Test 1 - Testing a failure of the primary database instance
{: #ha-rhel-hana-sr-test-primary-instance-database-failure}

Use the following information to test the failure of the primary database instance.

#### Test 1 - Description
{: #ha-rhel-hana-sr-test1-description}

Simulate a crash of the primary HANA database instance that is running on NODE1.

#### Test 1 - Prerequisites
{: #ha-rhel-hana-sr-test1-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both cluster nodes are active.
- Cluster that is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` that is configured with `AUTOMATED_REGISTER=false`.
- Check SAP HANA System Replication status:
   - Primary SAP HANA database is running on NODE1
   - Secondary SAP HANA database is running on NODE2
   - HANA System Replication is activated and in sync

#### Test 1 - Test procedure
{: #ha-rhel-hana-sr-test1-procedure}

Crash SAP HANA primary by sending a SIGKILL signal as the user `${sid}adm`.

On NODE1, run the following command.

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test 1 - Expected behavior
{: #ha-rhel-hana-sr-test1-expected-behavior}

- SAP HANA primary instance on NODE1 crashes.
- The cluster detects the stopped primary HANA database and marks the resource as `failed`.
- The cluster promotes the secondary HANA database on NODE2 to take over as the new primary.
- The cluster releases the virtual IP address on NODE1, and acquires it on the new primary on NODE2.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

#### Test 1 - Recovery procedure
{: #ha-rhel-hana-sr-test1-recovery-procedure}

As the cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=false`, the cluster doesn't restart the failed HANA database, and doesn't register it against the new primary. Which means that the status on the new primary (NODE2) also shows the secondary in status 'CONNECTION TIMEOUT'.

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

### Test 2 - Testing a failure of the node that is running the primary database
{: #ha-rhel-hana-sr-test-primary-instance-server-failure}

Use the following information to test the failure of the node that is running the primary database.

#### Test 2 - Description
{: #ha-rhel-hana-sr-test2-description}

Simulate a crash of the node that is running the primary HANA database.

#### Test 2 - Preparation
{: #ha-rhel-hana-sr-test2-preparation}

Make sure that the cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.

On NODE1, run the following command.

```sh
pcs resource update SAPHana_${SID}_${INSTNO} AUTOMATED_REGISTER=true
```
{: pre}

```sh
pcs resource config SAPHana_${SID}_${INSTNO}
```
{: pre}

#### Test 2 - Prerequisites
{: #ha-rhel-hana-sr-test2-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both nodes are active.
- Cluster is started on NODE1 and NODE2.
- Check SAP HANA System Replication status.
   - Primary SAP HANA database is running on NODE2
   - Secondary SAP HANA database is running on NODE1
   - HANA System Replication is activated and in sync

#### Test 2 - Test procedure
{: #ha-rhel-hana-sr-test2-procedure}

Crash primary on NODE2 by sending a *shutoff* system request.

On NODE2, run the following command.

```sh
sync; echo o > /proc/sysrq-trigger
```
{: pre}

#### Test 2 - Expected behavior
{: #ha-rhel-hana-sr-test2-expected-behavior}

- NODE2 shuts down.
- The cluster detects the failed node and sets its state to `OFFLINE`.
- The cluster promotes the secondary HANA database on NODE1 to take over as the new primary.
- The cluster acquires the virtual IP address on NODE1 on the new primary.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

#### Test 2 - Recovery procedure
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

As the cluster resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`, SAP HANA restarts when NODE2 rejoins the cluster and the former primary reregisters as a secondary.

### Test 3 - Testing a failure of the secondary database instance
{: #ha-rhel-hana-sr-test-secondary-instance-database-failure}

Use the following information to test the failure of the secondary database instance.

#### Test 3 - Description
{: #ha-rhel-hana-sr-test3-description}

Simulate a crash of the secondary HANA database.

#### Test 3 - Prerequisites
{: #ha-rhel-hana-sr-test3-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both nodes are active.
- Cluster is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.
- Check SAP HANA System Replication status:
   - Primary SAP HANA database is running on NODE1
   - Secondary SAP HANA database is running on NODE2
   - HANA System Replication is activated and in sync

#### Test 3 - Test Procedure
{: #ha-rhel-hana-sr-test3-procedure}

Crash SAP HANA secondary by sending a SIGKILL signal as the user `${sid}adm`.

On NODE2, run the following command.

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test 3 - Expected behavior
{: #ha-rhel-hana-sr-test3-expected-behavior}

- SAP HANA secondary on NODE2 crashes.
- The cluster detects the stopped secondary HANA database and marks the resource as `failed`.
- The cluster restarts the secondary HANA database.
- The cluster detects that the system replication is in sync again.

#### Test 3 - Recovery procedure
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

### Test 4 - Testing a manual move of a SAPHana resource to another node
{: #ha-rhel-hana-sr-test-manual-move}

Use the following information to test the manual move of a SAPHana resource to another node.

#### Test 4 - Description
{: #ha-rhel-hana-sr-test4-description}

Use cluster commands to move the primary instance to the other node for maintenance purposes.

#### Test 4 - Prerequisites
{: #ha-rhel-hana-sr-test4-prerequisites}

- A functional two-node RHEL HA Add-On cluster for HANA system replication.
- Both nodes are active.
- Cluster is started on NODE1 and NODE2.
- Cluster Resource `SAPHana_${SID}_${INSTNO}` is configured with `AUTOMATED_REGISTER=true`.
- Check SAP HANA System Replication status:
   - Primary SAP HANA database is running on NODE1
   - Secondary SAP HANA database is running on NODE2
   - HANA System Replication is activated and in sync

#### Test 4 - Test procedure
{: #ha-rhel-hana-sr-test4-procedure}

Move SAP HANA primary to other node by using the `pcs resource move` command.

On NODE1, run the following command.

```sh
pcs resource move SAPHana_${SID}_${INSTNO}-clone
```
{: pre}

#### Test 4 - Expected behavior
{: #ha-rhel-hana-sr-test4-expected-behavior}

- The cluster creates location constraints to move the resource.
- The cluster triggers a takeover to the secondary HANA database.
- If an application, such as SAP NetWeaver, is connected to a tenant database of SAP HANA, the application automatically reconnects to the new primary.

#### Test 4 - Recovery procedure
{: #ha-rhel-hana-sr-test4-recovery-procedure}

The automatically created location constraints must be removed to allow automatic failover in the future.

Wait until the primary HANA instance is active and remove the constraints.

The cluster registers and starts the HANA database as a new secondary instance.

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
