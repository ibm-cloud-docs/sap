---
copyright:
  years: 2023, 2025
lastupdated: "2025-11-10"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Configuring SAP HANA scale-up system replication in a Red Hat Enterprise Linux High Availability Add-On cluster with the `sap-hana-ha` resource agent
{: #ha-rhel-hana-ng-sh}

The following information describes the configuration of a Red Hat Enterprise Linux (RHEL) High Availability Add-On cluster for managing *SAP HANA Scale-Up System Replication*.
The cluster uses virtual server instances in [{{site.data.keyword.powerSysFull}}](https://www.ibm.com/products/power-virtual-server){: external} as cluster nodes.
{: shortdesc}

The instructions describe how to automate SAP HANA scale-up system replication for a single database deployment in a performance-optimized scenario on a RHEL HA Add-on cluster.

This information is intended for architects and specialists who are planning a high-availability deployment of SAP HANA on {{site.data.keyword.powerSys_notm}}.
{: note}

## Before you begin
{: #ha-rhel-hana-ng-sh-begin}

Review the general requirements, product documentation, support articles, and SAP notes listed in [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

## Prerequisites
{: #ha-rhel-hana-ng-sh-prerequisites}

- A Red Hat High Availability cluster is deployed on two virtual server instances in {{site.data.keyword.powerSys_notm}}.
   - Install and configure the RHEL HA Add-On cluster according to [Implementing a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel).
   - Configure and verify fencing according to the same document.
- The virtual server instances must meet the hardware and resource requirements for the SAP HANA systems in scope.
   Follow the guidelines in [Planning your deployment](/docs/sap?topic=sap-powervs-planning-items).
- The hostnames of the virtual server instances must comply with SAP HANA naming requirements.
- SAP HANA is installed on both virtual server instances, and SAP HANA system replication is configured.
   The SAP HANA installation and system replication setup are not specific to the {{site.data.keyword.powerSys_notm}} environment.
   Follow the standard procedures.
- A valid *RHEL for SAP Applications* or *RHEL for SAP Solutions* subscription is required to enable the repositories that you need for installing SAP HANA and the high availability resource agents.

## Configuring SAP HANA system replication in a RHEL HA Add-On cluster on IBM {{site.data.keyword.powerSys_notm}}
{: #ha-rhel-hana-ng-sh-configure-sr}

The instructions are based on the Red Hat product documentation and articles that are listed in [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

### Preparing environment variables
{: #ha-rhel-hana-ng-sh-prepare-environment-variables}

To simplify the setup, define the following environment variables for the root user on both nodes.
These variables are used in later operating system commands throughout this procedure.

On both nodes, set the following environment variables.

```sh
# General settings
export SID=<SID>            # SAP HANA System ID (uppercase)
export sid=<sid>            # SAP HANA System ID (lowercase)
export INSTNO=<INSTNO>      # SAP HANA instance number

# Cluster node 1
export NODE1=<HOSTNAME_1>   # Virtual server instance hostname
export DC1="Site1"          # HANA system replication site name

# Cluster node 2
export NODE2=<HOSTNAME_2>   # Virtual server instance hostname
export DC2="Site2"          # HANA system replication site name

# Single zone and Multizone region
export VIP=<IP address>     # SAP HANA system replication cluster virtual IP address

# Multizone region only
export CLOUD_REGION=<CLOUD_REGION>       # Multizone region name
export APIKEY="APIKEY or path to file"   # API Key of the IBM Cloud IAM ServiceID for the resource agent
export API_TYPE="private or public"      # Use private or public API endpoints
# resource agent powervs-move-ip only
export ROUTE_CRN1=<Route_CRN1>  # CRN of the static route in Workspace_1 with destination VIP (powervs-move-ip only)
export ROUTE_CRN2=<Route_CRN2>  # CRN of the static route in Workspace_2 with destination VIP (powervs-move-ip only)
export MON_API="false or true"           # Use cloud api in monitor command (powervs-move-ip only)
# resource agent powervs-subnet only
export IBMCLOUD_CRN_1=<IBMCLOUD_CRN_1>   # Workspace 1 CRN (powervs-subnet only)
export IBMCLOUD_CRN_2=<IBMCLOUD_CRN_2>   # Workspace 2 CRN (powervs-subnet only)
export POWERVSI_1=<POWERVSI_1>           # Virtual server instance 1 id (powervs-subnet only)
export POWERVSI_2=<POWERVSI_2>           # Virtual server instance 2 id (powervs-subnet only)
export SUBNET_NAME="vip-${sid}-net"      # Name which is used to define the subnet in IBM Cloud (powervs-subnet only)
export CIDR="CIDR of subnet"             # CIDR of the subnet containing the service IP address (powervs-subnet only)
export JUMBO="true or false"             # Enable Jumbo frames (powervs-subnet only)
```
{: codeblock}

#### Setting extra environment variables for a single zone implementation
{: #ha-rhel-hana-ng-sh-sz-prepare-environment-variables}

Review [Reserving virtual IP addresses](/docs/sap?topic=sap-ha-vsi#ha-vsi-reserve-virtual-ip-addresses) and reserve a virtual IP address for the SAP HANA system replication cluster.
Set the `VIP` environment variable to the reserved IP address.

#### Setting extra environment variables for a multizone region implementation
{: #ha-rhel-hana-ng-sh-mz-prepare-environment-variables}

Set the `CLOUD_REGION`, `APIKEY`, `IBMCLOUD_CRN_?`, `POWERVSI_?` variables as described in the [Collecting parameters for configuring a high availability cluster](/docs/sap?topic=sap-ha-vsi#ha-vsi-create-service-api-key) section.
Set `API_TYPE` to `private` to enable communication with the IBM Cloud IAM and IBM Power Cloud API through private endpoints.

Prepare the variables for the `powervs-subnet` resource agent:
- `SUBNET_NAME` specifies the name of the subnet.
- `CIDR` defines the subnet in *Classless Inter-Domain Routing (CIDR)* format: `<IPv4_address>/number`.
- `VIP` is the virtual IP address and must fall within the defined `CIDR`.
- Set `JUMBO` to `true` to enable support for large MTU sizes on the subnet.

The subnet `SUBNET_NAME` must not exist,  and its CIDR range must not overlap with the IP ranges of any existing subnets in either workspace.
{: attention}

The following export command sample show how to set the required environment variables for a multizone region `powervs-subnet` implementation.

```sh
export CLOUD_REGION="eu-de"
export IBMCLOUD_CRN_1="crn:v1:bluemix:public:power-iaas:eu-de-2:a/a1b2c3d4e5f60123456789a1b2c3d4e5:a1b2c3d4-0123-4567-89ab-a1b2c3d4e5f6::"
export IBMCLOUD_CRN_2="crn:v1:bluemix:public:power-iaas:eu-de-1:a/a1b2c3d4e5f60123456789a1b2c3d4e5:e5f6a1b2-cdef-0123-4567-a1b2c3d4e5f6::"
export POWERVSI_1="a1b2c3d4-0123-890a-f012-0123456789ab"
export POWERVSI_2="e5f6a1b2-4567-bcde-3456-cdef01234567"
export APIKEY="@/root/.apikey.json"
export API_TYPE="private"
export SUBNET_NAME="vip-mh1-net"
export CIDR="10.40.41.100/30"
export VIP="10.40.41.102"
export JUMBO="true"
```
{: codeblock}

### Installing SAP HANA resource agents
{: #ha-rhel-hana-ng-sh-install-sap-hana-resource-agents}

The `sap-hana-ha` package includes three resource agents.
- *SAPHanaTopology*: Collects the status and configuration of SAP HANA system replication on each node.
   It also starts and monitors the local *SAP HostAgent*, which is required to start, stop, and monitor the SAP HANA instances.
- *SAPHanaFilesystem* (optional): Checks access to an SAP HANA file system.
   If the SAP HANA primary node experiences a storage failure, this agent fences the node to enable faster failover.
- *SAPHanaController*: Manages the SAP HANA instances for both single-host (scale-up) and multiple-host (scale-out) deployments.
   If the SAP HANA primary system fails, this agent triggers a takeover to the secondary system.

On both nodes, run the following command to install the `sap-hana-ha` resource agent package.

```sh
dnf install -y sap-hana-ha
```
{: pre}

The setup that is described here requires version 1.2.8-4 or later of the `sap-hana-ha package`.
{: important}

### Starting the SAP HANA system
{: #ha-rhel-hana-ng-sh-start-hana-systems}

Start SAP HANA and verify that system replication is active.
For details, see [2.4. Checking SAP HANA System Replication state](https://access.redhat.com/articles/3004101#checking-sap-hana-system-replication-state){: external}.

On both nodes, run the following commands to start SAP HANA.

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

### Configuring the `systemd` based SAP HANA startup framework
{: #ha-rhel-hana-ng-sh-systemd-units}

The SAP HANA startup framework is integrated by default with `systemd` on RHEL 9 for SAP HANA 2.0 SPS07 revision 70 and newer.
Apply extra modifications to manage the pacemaker systemd service and SAP HANA instance systemd service in the correct order.

   On both nodes, run the following command to check whether SAP HANA instance systemd integration is enabled.

   ```sh
   systemctl list-units --all SAP*
   ```
   {: pre}

   ```sh
   # systemctl list-units --all SAP*
   UNIT              LOAD      ACTIVE   SUB     DESCRIPTION
   SAPMH1_00.service loaded    active   running SAP Instance SAPMH1_00
   SAP.slice         loaded    active   active  SAP Slice
   ```
   {: screen}

   If the unit `SAP${SID}_${INSTNO}.service` appears in the command output, complete the following steps on both nodes.

   1. Create the directory for the `pacemaker` service drop-in file.

      ```sh
      mkdir /etc/systemd/system/pacemaker.service.d
      ```
      {: pre}

   1. Create the `pacemaker` service drop-in file.

      ```sh
      cat >> /etc/systemd/system/pacemaker.service.d/HA.conf << EOT
      [Unit]
      Description=Pacemaker needs the SAP HANA instance service
      Wants=SAP${SID}_${INSTNO}.service
      After=SAP${SID}_${INSTNO}.service
      EOT
      ```
      {: pre}

   1. Reload the `systemctl` daemon.

      ```sh
      systemctl daemon-reload
      ```
      {: pre}

### Enabling the SAP HANA hook scripts
{: #ha-rhel-hana-ng-sh-enable-hana-sr-hook}

SAP HANA provides *hooks* that send notifications when specific events occur.
For more information, see [Implementing a HA/DR Provider](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/1367c8fdefaa4808a7485b09815ae0f3.html){: external}.

The *srConnectionChanged()* hook helps to detect SAP HANA system replication status changes that require cluster intervention.
Its purpose is to prevent data loss or corruption by avoiding accidental takeovers.

SAP HANA includes built-in functions to monitor its `indexserver`.
If a problem occurs, SAP HANA attempts to recover automatically by stopping and restarting the process.
To complete this recovery, the Linux kernel must release all memory that is allocated to the process.
For large databases, this cleanup can take a significant amount of time.
During this period, SAP HANA continues to operate and accept client requests, which can cause system replication to become out of sync.
If another error occurs before the indexserver is fully restarted and recovered, data consistency can be compromised.
The srServiceStateChanged() hook addresses this scenario by stopping the entire SAP HANA instance to enable faster and safer recovery.

#### Configuring the hook `HanaSR` for all SAP HANA instances
{: #ha-rhel-hana-ng-sh-activate-hana-sr-hook}

The *sap-hana-ha* package includes the `HanaSR.py` hook script, which is located in the `/usr/share/sap-hana-ha` directory.
Configure this hook on all SAP HANA cluster nodes.

Enable and test the SAP HANA *srConnectionChanged()* hook before you continue with the cluster setup.
{: important}

1. Update the `global.ini` file on each SAP HANA node to enable the `HanaSR` hook script.

   On both nodes, run the following command.

   ```sh
   sudo -i -u ${sid}adm -- <<EOT
       python \$DIR_INSTANCE/exe/python_support/setParameter.py \
         -set SYSTEM/global.ini/ha_dr_provider_hanasr/provider=HanaSR \
         -set SYSTEM/global.ini/ha_dr_provider_hanasr/path=/usr/share/sap-hana-ha/ \
         -set SYSTEM/global.ini/ha_dr_provider_hanasr/execution_order=1 \
         -set SYSTEM/global.ini/trace/ha_dr_hanasr=info
   EOT
   ```
   {: pre}

1. Verify the updated `global.ini` file.

   On both nodes, run the following command.

   ```sh
   cat /hana/shared/${SID}/global/hdb/custom/config/global.ini
   ```
   {: pre}

1. Configure sudo settings for the SAP HANA operating system administrator *sidadm*.

   These settings allow the hook script *srConnectionChanged()* to update node attributes.

   Run the following commands on both nodes to create the required sudo configuration file.

   ```sh
   cat >> /etc/sudoers.d/20-saphana << EOT
   Cmnd_Alias DC1_SOK = /usr/sbin/crm_attribute -n hana_${sid}_site_srHook_${DC1} -v SOK -t crm_config -s SAPHanaSR
   Cmnd_Alias DC1_SFAIL = /usr/sbin/crm_attribute -n hana_${sid}_site_srHook_${DC1} -v SFAIL -t crm_config -s SAPHanaSR
   Cmnd_Alias DC2_SOK = /usr/sbin/crm_attribute -n hana_${sid}_site_srHook_${DC2} -v SOK -t crm_config -s SAPHanaSR
   Cmnd_Alias DC2_SFAIL = /usr/sbin/crm_attribute -n hana_${sid}_site_srHook_${DC2} -v SFAIL -t crm_config -s SAPHanaSR
   Cmnd_Alias FENCE_ME = /usr/bin/SAPHanaSR-hookHelper --sid=${SID} --case=fenceMe
   ${sid}adm ALL=(ALL) NOPASSWD: DC1_SOK, DC1_SFAIL, DC2_SOK, DC2_SFAIL, FENCE_ME
   Defaults!DC1_SOK, DC1_SFAIL, DC2_SOK, DC2_SFAIL, FENCE_ME !requiretty
   EOT
   ```
   {: pre}

   The `Cmnd_Alias FENCE_ME` is only necessary when you configure the `ChkSrv` hook script with the parameter `action_on_lost = fence`.
   {: note}

   Set the correct ownership and permissions and use `visudo` to validate its syntax.

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

Correct any issues reported by the `visudo -c` command.
{: note}

#### Configuring the `ChkSrv` hook for all SAP HANA instances (optional)
{: #ha-rhel-hana-ng-sh-enable-hana-servicestatechanged-hook}

The *sap-hana-ha* package includes the `ChkSrv.py` hook script, which is located in the `/usr/share/sap-hana-ha` directory.
Configure this hook on all SAP HANA cluster nodes.

You can configure the hook *srServiceStateChanged()* to accelerate recovery when an `indexserver` process fails.
This configuration is optional.
{: note}

The `ChkSrv.py` script stops the entire SAP HANA instance for faster recovery.
If `automated failover` is enabled in the cluster and the secondary node is in a healthy state, the system initiates a takeover operation.
If failover is not possible, the script forces a restart of the SAP HANA instance on the local node.

The hook script analyzes instance events, filters the event details, and triggers actions based on the filtered results.
It distinguishes between an SAP HANA `indexserver` process that is stopped and restarted by the system, and one that is stopped as part of an instance shutdown.

The actions that are triggered by the hook depend on the configuration of the `action_on_lost` parameter.

ignore
:   This action logs the event details and the corresponding decision logic to a file.

stop
:  This action gracefully stops the SAP HANA instance by using the `sapcontrol` command.

kill
:   This action triggers the `HDB kill-<signal>` command with a default signal 9.
    The signal can be configured.

Both the *stop* and the *kill* actions stop the SAP HANA instance, but the *kill* action is slightly faster.

fence
:   This action fences the cluster node when a failed `indexserver` process is detected.
    Grant `sudo` privileges to allow the *sidadm* user to run the `/usr/bin/SAPHanaSR-hookHelper` script.

1. Update the `global.ini` file on each SAP HANA node to enable the hook script.

   On both nodes, run the following command.

   ```sh
   sudo -i -u ${sid}adm -- <<EOT
       python \$DIR_INSTANCE/exe/python_support/setParameter.py \
         -set SYSTEM/global.ini/ha_dr_provider_chksrv/provider=ChkSrv \
         -set SYSTEM/global.ini/ha_dr_provider_chksrv/path=/usr/share/sap-hana-ha/ \
         -set SYSTEM/global.ini/ha_dr_provider_chksrv/execution_order=2 \
         -set SYSTEM/global.ini/ha_dr_provider_chksrv/action_on_lost=stop \
         -set SYSTEM/global.ini/trace/ha_dr_chksrv=info
   EOT
   ```
   {: pre}

   In this example, the `action_on_lost` parameter is set to `stop`; the default value is `ignore`.
   You can optionally configure the `stop_timeout` parameter (default: 20 seconds) and the `kill_signal` parameter (default: 9).

#### Activating the hooks
{: #ha-rhel-hana-ng-sh-activate-hooks}

1. Reload the hook configuration.

   On both nodes, run the following command to activate the hooks.

   ```sh
   sudo -i -u ${sid}adm -- hdbnsutil -reloadHADRProviders
   ```
   {: pre}

1. Verify that the hooks are active by checking the trace logs.

   On both nodes, run the following command.

   ```sh
   sudo -i -u ${sid}adm -- sh -c 'grep "ha_dr_provider" $DIR_INSTANCE/$VTHOSTNAME/trace/nameserver_* | cut -d" " -f2,3,6-'
   ```
   {: pre}

   Sample Output:

   ```text
   2025-02-27 15:47:21.855038   HADRProviderManager.cpp(00087) : loading HA/DR Provider 'ChkSrv' from /usr/share/sap-hana-ha/
   2025-02-27 15:47:22.648229   HADRProviderManager.cpp(00087) : loading HA/DR Provider 'HanaSR' from /usr/share/sap-hana-ha/
   ```
   {: screen}

### Configuring general cluster properties
{: #ha-rhel-hana-ng-sh-configure-cluster-properties}

To prevent unintended resource failover during initial testing and post-production phases, configure the following default values for the `resource-stickiness` and `migration-threshold` parameters.

These default values do not apply to resources that define custom values for these parameters.

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
{: #ha-rhel-hana-ng-sh-create-saphanatopology-resource}

On NODE1, run the following command to create the *SAPHanaTopology* resource.

```sh
pcs resource create SAPHanaTopology_${SID}_${INSTNO} SAPHanaTopology \
    SID=${SID} \
    InstanceNumber=${INSTNO} \
    op start timeout=600 \
    op stop timeout=300 \
    op monitor interval=10 timeout=600 \
    clone meta clone-max=2 clone-node-max=1 interleave=true \
    --future \
    --disabled
```
{: pre}

Verify the resource configuration and cluster status by running the following commands.

```sh
pcs resource config SAPHanaTopology_${SID}_${INSTNO}
```
{: pre}

```sh
pcs resource config SAPHanaTopology_${SID}_${INSTNO}-clone
```
{: pre}

### Creating a cloned SAPHanaFilesystem resource
{: #ha-rhel-hana-ng-sh-create-saphanafilesystem-resource}

The *SAPHanaFilesystem* resource monitors the file systems that are used by the SAP HANA system.

On NODE1, run the following command to create the *SAPHanaFilesystem* resource.

```sh
pcs resource create SAPHanaFilesystem_${SID}_${INSTNO} SAPHanaFilesystem \
    SID=${SID} \
    InstanceNumber=${INSTNO} \
    op start interval=0 timeout=10 \
    op stop interval=0 timeout=20 \
    op monitor interval=120 timeout=120 \
    clone meta clone-max=2 clone-node-max=1 interleave=true \
    --future \
    --disabled
```
{: pre}

Verify the resource configuration and cluster status by running the following commands.

```sh
pcs resource config SAPHanaFilesystem_${SID}_${INSTNO}
```
{: pre}

```sh
pcs resource config SAPHanaFilesystem_${SID}_${INSTNO}-clone
```
{: pre}

### Creating a promotable SAPHanaController resource
{: #ha-rhel-hana-ng-sh-create-saphana-resource}

The *SAPHanaController* resource manages the two SAP HANA instances that are configured for system replication.

On NODE1, run the following command to create the *SAPHanaController* resource.

```sh
pcs resource create SAPHanaController_${SID}_${INSTNO} SAPHanaController \
    SID=${SID} \
    InstanceNumber=${INSTNO} \
    HANA_CALL_TIMEOUT=120 \
    PREFER_SITE_TAKEOVER=true \
    DUPLICATE_PRIMARY_TIMEOUT=7200 \
    AUTOMATED_REGISTER=false \
    op start timeout=3600 \
    op stop timeout=3600 \
    op monitor interval=59 role="Unpromoted" timeout=700 \
    op monitor interval=61 role="Promoted" timeout=700 \
    op promote timeout=900 \
    op demote timeout=320 \
    meta priority=100 \
    promotable meta clone-max=2 clone-node-max=1 interleave=true \
    --future \
    --disabled
```
{: pre}

Verify the resource configuration and cluster status.

```sh
pcs resource config SAPHanaController_${SID}_${INSTNO}
```
{: pre}

### Creating a virtual IP address cluster resource
{: #ha-rhel-hana-ng-sh-create-virtual-ip-resource}

Choose the appropriate configuration path based on your deployment scenario:

- [Creating a virtual IP cluster resource in a single zone environment](#ha-rhel-hana-ng-sh-sz-create-virtual-ip-resource): Follows these steps if the cluster nodes are running in a single {{site.data.keyword.powerSys_notm}} workspace.
- [Creating a virtual IP cluster resource in a multizone region environment](#ha-rhel-hana-ng-sh-mz-create-virtual-ip-resource): Follow these steps if the cluster nodes are running in separate {{site.data.keyword.powerSys_notm}} workspaces.

#### Creating a virtual IP cluster resource in a single zone environment
{: #ha-rhel-hana-ng-sh-sz-create-virtual-ip-resource}

Use the reserved IP address to create a virtual IP address cluster resource.
This IP address enables access to the SAP HANA system replication primary instance.

Create the virtual IP address cluster resource with the following command.

```sh
pcs resource create vip_${SID}_${INSTNO} IPaddr2 \
    ip=$VIP \
    --disabled
```
{: pre}

Verify the virtual IP address resource configuration and cluster status.

```sh
pcs resource config vip_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}

Proceed to the [Creating cluster resource constraints](#ha-rhel-hana-sr-create-constraints) section.

#### Creating a virtual IP cluster resource in a multizone region environment
{: #ha-rhel-hana-ng-sh-mz-create-virtual-ip-resource}

Decide on the resource manager for virtual IP cluster resource in the [SAP HANA high availability solution in a multizone region environment - Network considerations](/docs/sap?topic=sap-ha-overview#ha-overview-hana-mzr-network) section.

Ensure that you completed all steps in the [Preparing a multi-zone RHEL HA Add-On cluster for a virtual IP address resource](/docs/sap?topic=sap-ha-rhel-mz#ha-rhel-mz-create-vip) section.

Use the `pcs resource describe powervs-move-ip` command to get information about the `powervs-move-ip` resource agent parameters. Use the `pcs resource describe powervs-subnet` command to get information about the `powervs-subnet` resource agent parameters.
{: note}

If you use the `powervs-move-ip` resource agent, run the following command on NODE1 to create a cluster resource for the virtual IP address.

```sh
pcs resource create vip_${SID}_${INSTNO} powervs-move-ip \
    api_key=${APIKEY} \
    api_type=${API_TYPE} \
    ip=${VIP} \
    route_host_map="${NODE1}:${ROUTE_CRN1};${NODE2}:${ROUTE_CRN2}" \
    region=${CLOUD_REGION} \
    monitor_api=${MON_API}
    op start timeout=60 \
    op stop timeout=60 \
    op monitor interval=60 timeout=60 \
    --disabled
```
{: pre}

Otherwise, run the following command on NODE1 to create a `powervs-subnet` cluster resource for the virtual IP address.

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
    op monitor interval=60 timeout=30 \
    --disabled
```
{: pre}

When `API_TYPE` is set to `public`, you must also specify the `proxy` parameter.
{: note}

Before you run the `pcs resource config` command, verify that both virtual server instances in the cluster have the status `Active`, and their health status is `OK`.
{: important}

Verify the configured virtual IP resource and cluster status.

```sh
pcs resource config vip_${SID}_${INSTNO}
```
{: pre}

Sample output for `powervs-subnet` resource agent:

```sh
# pcs resource config vip_MH1_00
Resource: vip_MH1_00 (class=ocf provider=heartbeat type=powervs-subnet)
  Attributes: vip_MH1_00-instance_attributes
    api_key=@/root/.apikey.json
    api_type=private
    cidr=10.40.41.100/30
    crn_host_map=cl-mh1-1:crn:v1:bluemix:public:power-iaas:eu-de-2:a/**********************************:**********************************::;cl-mh1-2:crn:v1:bluemix:public:power-iaas:eu-
        de-1:a/**********************************:**********************************::
    ip=10.40.41.102
    jumbo=true
    region=eu-de
    subnet_name=vip-mh1-net
    vsi_host_map=cl-mh1-1:**********************************;cl-mh1-2:**********************************
  Operations:
    monitor: vip_MH1_00-monitor-interval-60
      interval=60 timeout=30
    start: vip_MH1_00-start-interval-0s
      interval=0s timeout=720
    stop: vip_MH1_00-stop-interval-0s
      interval=0s timeout=300
```
{: screen}

### Creating cluster resource constraints
{: #ha-rhel-hana-ng-sh-create-constraints}

Ensure that the *SAPHanaTopology* resources start before the *SAPHanaController* resources.

The virtual IP address must be assigned to the node that hosts the primary *SAPHanaController* resource.

1. Create a resource constraint to enforce the start order.
   This constraint ensures that *SAPHanaTopology* starts before *SAPHanaController*.

   On NODE1, use the following command to create the *SAPHanaTopology* order constraint.

   ```sh
   pcs constraint order SAPHanaTopology_${SID}_${INSTNO}-clone \
       then SAPHanaController_${SID}_${INSTNO}-clone symmetrical=false
   ```
   {: pre}

   Verify that the constraint is applied correctly.

   ```sh
   pcs constraint
   ```
   {: pre}

2. Create a resource constraint to colocate the virtual IP address with the SAP HANA system replication primary instance.
   The constraint ensures that the virtual IP address is assigned to the node where the promoted *SAPHanaController* resource, the SAP HANA primary, is running.

   On NODE1, run the following command to create the virtual IP colocation constraint.

   ```sh
   pcs constraint colocation add vip_${SID}_${INSTNO} \
       with Promoted SAPHanaController_${SID}_${INSTNO}-clone 2000
   ```
   {: pre}

   Verify that the colocation constraint is applied.

   ```sh
   pcs constraint
   ```
   {: pre}

   Sample output:

   ```sh
   # pcs constraint
   Colocation Constraints:
     Started resource 'vip_MH1_00' with Promoted resource 'SAPHanaController_MH1_00-clone'
       score=2000
   Order Constraints:
     start resource 'SAPHanaTopology_MH1_00-clone' then start resource 'SAPHanaController_MH1_00-clone'
       symmetrical=0
   ```
   {: screen}

### Enabling the cluster resources
{: #ha-rhel-hana-ng-sh-enable-resources}

The cluster resources were initially created with the `--disabled` flag.

On NODE1, use the following commands to enable each cluster resource.

```sh
pcs resource enable SAPHanaTopology_${SID}_${INSTNO}-clone
```
{: pre}

```sh
pcs resource enable SAPHanaFilesystem_${SID}_${INSTNO}-clone
```
{: pre}

```sh
pcs resource enable SAPHanaController_${SID}_${INSTNO}-clone
```
{: pre}

```sh
pcs resource enable vip_${SID}_${INSTNO}
```
{: pre}

Verify that all resources are running.

```sh
pcs status --full
```
{: pre}

The following output is a sample for an SAP HANA system replication cluster that is deployed in a multizone region.

```sh
# pcs status --full
Cluster name: SAP_MH1
Cluster Summary:
  * Stack: corosync (Pacemaker is running)
  * Current DC: cl-mh1-1 (1) (version 2.1.7-5.2.el9_4-0f7f88312) - partition with quorum
  * Last updated: Thu Feb 27 17:43:57 2025 on cl-mh1-1
  * Last change:  Thu Feb 27 17:43:22 2025 by root via root on cl-mh1-1
  * 2 nodes configured
  * 8 resource instances configured

Node List:
  * Node cl-mh1-1 (1): online, feature set 3.19.0
  * Node cl-mh1-2 (2): online, feature set 3.19.0

Full List of Resources:
  * res_fence_sbd       (stonith:fence_sbd):     Started cl-mh1-1
  * Clone Set: SAPHanaTopology_MH1_00-clone [SAPHanaTopology_MH1_00]:
    * SAPHanaTopology_MH1_00    (ocf:heartbeat:SAPHanaTopology):         Started cl-mh1-2
    * SAPHanaTopology_MH1_00    (ocf:heartbeat:SAPHanaTopology):         Started cl-mh1-1
  * Clone Set: SAPHanaFilesystem_MH1_00-clone [SAPHanaFilesystem_MH1_00]:
    * SAPHanaFilesystem_MH1_00  (ocf:heartbeat:SAPHanaFilesystem):       Started cl-mh1-2
    * SAPHanaFilesystem_MH1_00  (ocf:heartbeat:SAPHanaFilesystem):       Started cl-mh1-1
  * Clone Set: SAPHanaController_MH1_00-clone [SAPHanaController_MH1_00] (promotable):
    * SAPHanaController_MH1_00  (ocf:heartbeat:SAPHanaController):       Unpromoted cl-mh1-2
    * SAPHanaController_MH1_00  (ocf:heartbeat:SAPHanaController):       Promoted cl-mh1-1
  * vip_MH1_00  (ocf:heartbeat:powervs-subnet):  Started cl-mh1-1

Node Attributes:
  * Node: cl-mh1-1 (1):
    * hana_mh1_clone_state              : PROMOTED
    * hana_mh1_roles                    : master1:master:worker:master
    * hana_mh1_site                     : SiteA
    * hana_mh1_srah                     : -
    * hana_mh1_version                  : 2.00.079.00
    * hana_mh1_vhost                    : cl-mh1-1
    * master-SAPHanaController_MH1_00   : 150
  * Node: cl-mh1-2 (2):
    * hana_mh1_clone_state              : DEMOTED
    * hana_mh1_roles                    : master1:master:worker:master
    * hana_mh1_site                     : SiteB
    * hana_mh1_srah                     : -
    * hana_mh1_version                  : 2.00.079.00
    * hana_mh1_vhost                    : cl-mh1-2
    * master-SAPHanaController_MH1_00   : 100

Migration Summary:

Tickets:

PCSD Status:
  cl-mh1-1: Online
  cl-mh1-2: Online

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```
{: screen}

On the promoted cluster node, verify that the cluster service IP address is active.

```sh
ip addr show
```
{: pre}

### Enabling automated registration of the secondary instance
{: #ha-rhel-hana-ng-sh-enable-automated-registration}

Set the `AUTOMATED_REGISTER` parameter based on your operational requirements.
To retain the ability to revert to the previous primary SAP HANA instance, set `AUTOMATED_REGISTER=false`.
This setting prevents an automatic registration of the previous primary as a new secondary.

If a cluster-triggered takeover results in data issues, you can manually revert the configuration if `AUTOMATED_REGISTER` is set to `false`.

When `AUTOMATED_REGISTER=true`, the previous primary SAP HANA instance automatically registers as a secondary.
The instance looses its history and cannot be reactivated on its old state.
The benefit of this setting is that high availability is automatically restored when the failed node rejoins the cluster.

Use the default value `false` for `AUTOMATED_REGISTER` until the cluster is fully tested and the failover scenarios are verified.

To enable automated registration, use `pcs resource update SAPHanaController_${SID}_${INSTNO} AUTOMATED_REGISTER=true` to update the resource attribute.
{: tip}

## Testing SAP HANA system replication cluster
{: #ha-rhel-hana-ng-sh-test-sap-hana-sr-cluster}

Thoroughly test the cluster configuration to help ensure that all components function as expected.
The following examples describe failover test scenarios, but the list is not comprehensive.
Each test case includes the following details.

- Component that is being tested
- Test description
- Prerequisites and cluster state before the failover is initiated
- Test procedure
- Expected behavior and results
- Recovery procedure

### Test 1 - Testing a failure of the primary database instance
{: #ha-rhel-hana-ng-sh-test-primary-instance-database-failure}

Use the following procedure to test a failure of the primary SAP HANA database instance.

#### Test 1 - Description
{: #ha-rhel-hana-ng-sh-test1-description}

Simulate a crash of the primary SAP HANA database instance that is running on NODE1.

#### Test 1 - Prerequisites
{: #ha-rhel-hana-ng-sh-test1-prerequisites}

- A functional two-node RHEL HA Add-On cluster configured for HANA system replication
- The cluster is running on both nodes
- The `SAPHanaController_${SID}_${INSTNO}` resource is configured with `AUTOMATED_REGISTER=false`
- Verify the SAP HANA system replication status:
   - Primary SAP HANA database is running on NODE1
   - Secondary SAP HANA database is running on NODE2
   - System replication is activated and in sync

#### Test 1 - Test procedure
{: #ha-rhel-hana-ng-sh-test1-procedure}

Simulate a crash of the primary SAP HANA instance by sending a SIGKILL signal as the *sidadm* user.

On NODE1, run the following command.

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test 1 - Expected behavior
{: #ha-rhel-hana-ng-sh-test1-expected-behavior}

- SAP HANA primary instance on NODE1 crashes.
- The cluster detects the stopped primary HANA database and marks the resource as `failed`.
- The cluster promotes the secondary HANA database on NODE2 to become the new primary.
- The virtual IP address is released from NODE1 and reassigned to NODE2.
- Applications such as SAP NetWeaver, if connected to a tenant database, automatically reconnect to the new primary.

#### Test 1 - Recovery procedure
{: #ha-rhel-hana-ng-sh-test1-recovery-procedure}

Because the `SAPHanaController_${SID}_${INSTNO}` resource is configured with `AUTOMATED_REGISTER=false`, the cluster does not restart the failed SAP HANA instance or register it against the new primary.
As a result, the new primary on NODE2 shows the former secondary in `CONNECTION TIMEOUT` status.

To manually reregister the previous primary as a new secondary, run the following command on NODE1.

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

Verify the SAP HANA system replication status.

```sh
sudo -i -u ${sid}adm -- <<EOT
    hdbnsutil -sr_state
    HDBSettings.sh systemReplicationStatus.py
EOT
```
{: pre}

After manual registration and resource refresh, the new secondary instance restarts and appears in `SOK` (synced) status.

On NODE1, run the following command.

```sh
pcs resource refresh SAPHanaController_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}

### Test 2 - Testing a failure of the node that is running the primary database
{: #ha-rhel-hana-ng-sh-test-primary-instance-server-failure}

Use the following procedure to simulate and test a failure of the node that hosts the primary SAP HANA database instance.

#### Test 2 - Description
{: #ha-rhel-hana-ng-sh-test2-description}

Simulate a crash of the node that hosts the primary SAP HANA database instance.

#### Test 2 - Preparation
{: #ha-rhel-hana-ng-sh-test2-preparation}

Make sure that the `SAPHanaController_${SID}_${INSTNO}` resource is configured with `AUTOMATED_REGISTER=true`.

On NODE1, run the following commands.

```sh
pcs resource update SAPHanaController_${SID}_${INSTNO} AUTOMATED_REGISTER=true
```
{: pre}

```sh
pcs resource config SAPHanaController_${SID}_${INSTNO}
```
{: pre}

#### Test 2 - Prerequisites
{: #ha-rhel-hana-ng-sh-test2-prerequisites}

- A functional two-node RHEL HA Add-On cluster configured for HANA system replication
- The cluster is running on both nodes
- Verify the SAP HANA system replication status:
   - Primary SAP HANA database is running on NODE2
   - Secondary SAP HANA database is running on NODE1
   - System replication is activated and in sync

#### Test 2 - Test procedure
{: #ha-rhel-hana-ng-sh-test2-procedure}

Simulate a node failure on NODE2 by triggering a system crash.

On NODE2, run the following command.

```sh
sync; echo o > /proc/sysrq-trigger
```
{: pre}

#### Test 2 - Expected behavior
{: #ha-rhel-hana-ng-sh-test2-expected-behavior}

- NODE2 shuts down.
- The cluster detects the failed node and marks its state as `OFFLINE`.
- The cluster promotes the secondary SAP HANA instance on NODE1 to become the new primary.
- The virtual IP address is reassigned to NODE1.
- Applications such as SAP NetWeaver, if connected to a tenant database, automatically reconnect to the new primary.

#### Test 2 - Recovery procedure
{: #ha-rhel-hana-ng-sh-test2-recovery-procedure}

Log in to the {{site.data.keyword.cloud}} Console and start the NODE2 instance.
After NODE2 becomes available, restart the cluster framework.

On NODE2, run the following commands.

```sh
pcs cluster start
```
{: pre}

```sh
pcs status --full
```
{: pre}

Because `AUTOMATED_REGISTER=true` is configured for the `SAPHanaController_${SID}_${INSTNO}` resource, SAP HANA automatically restarts when NODE_B1 rejoins the cluster.
The former primary instance is reregistered as a secondary.

### Test 3 - Testing a failure of the secondary database instance
{: #ha-rhel-hana-ng-sh-test-secondary-instance-database-failure}

Follow these steps to simulate a failure of the secondary database instance.

#### Test 3 - Description
{: #ha-rhel-hana-ng-sh-test3-description}

This test simulates a crash of the secondary SAP HANA instance to validate cluster failover behavior.

#### Test 3 - Prerequisites
{: #ha-rhel-hana-ng-sh-test3-prerequisites}

- A functional two-node RHEL HA Add-On cluster configured for HANA system replication
- The cluster is running in both nodes
- The `SAPHanaController_${SID}_${INSTNO}` resource is configured with `AUTOMATED_REGISTER=true`
- Verify the SAP HANA system replication status:
   - Primary SAP HANA database is running on NODE1
   - Secondary SAP HANA database is running on NODE2
   - HANA system replication is activated and in sync

#### Test 3 - Test Procedure
{: #ha-rhel-hana-ng-sh-test3-procedure}

Simulate a crash of the secondary SAP HANA instance by sending a SIGKILL signal as the *sidadm* user.

On NODE2, run the following command.

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test 3 - Expected behavior
{: #ha-rhel-hana-ng-sh-test3-expected-behavior}

- The secondary SAP HANA instance on NODE2 crashes.
- The cluster detects the stopped secondary HANA database and marks the resource as `failed`.
- The cluster restarts the secondary HANA instance.
- The cluster verifies that system replication is reestablished and in sync.

#### Test 3 - Recovery procedure
{: #ha-rhel-hana-ng-sh-test3-recovery-procedure}

Wait until the secondary SAP HANA instance starts and system replication status returns to `SOK`.
Then, clean up any failed resource actions as indicated by `pcs status`.

On NODE2, run the following commands.

```sh
pcs resource refresh SAPHanaController_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}

### Test 4 - Testing a manual move of a SAPHana resource to another node
{: #ha-rhel-hana-ng-sh-test-manual-move}

Follow these steps to manually move a *SAPHanaController* resource to another node.

#### Test 4 - Description
{: #ha-rhel-hana-ng-sh-test4-description}

Use cluster management commands to relocate the primary SAP HANA instance to another node for maintenance.

#### Test 4 - Prerequisites
{: #ha-rhel-hana-ng-sh-test4-prerequisites}

- A functional two-node RHEL HA Add-On cluster configured for SAP HANA system replication
- The cluster is running on both nodes
- The `SAPHanaController_${SID}_${INSTNO}` resource is configured with `AUTOMATED_REGISTER=true`
- Verify the SAP HANA system replication status:
   - Primary SAP HANA instance is running on NODE1
   - Secondary SAP HANA instance is running on NODE2
   - HANA system replication is activated and in sync

#### Test 4 - Test procedure
{: #ha-rhel-hana-ng-sh-test4-procedure}

Manually move the primary SAP HANA instance to another node by using the `pcs resource move` command.

On NODE1, run the following command.

```sh
pcs resource move SAPHanaController_${SID}_${INSTNO}-clone
```
{: pre}

#### Test 4 - Expected behavior
{: #ha-rhel-hana-ng-sh-test4-expected-behavior}

- The cluster creates temporary location constraints to relocate the resource to the target node.
- The cluster initiates a takeover, promoting the secondary SAP HANA instance to primary.
- Applications such as SAP S/4HANA and SAP NetWeaver automatically reconnect to the new SAP HANA primary master instance.

#### Test 4 - Recovery procedure
{: #ha-rhel-hana-ng-sh-test4-recovery-procedure}

As part of the resource move process, the cluster automatically registers and starts the original primary node as the new secondary instance.
After the move completes successfully, the cluster removes any temporary location constraints.

Run the following commands on NODE1 to confirm that temporary constraints are removed and to verify the cluster status.

```sh
pcs constraint
```
{: pre}

```sh
pcs status --full
```
{: pre}
