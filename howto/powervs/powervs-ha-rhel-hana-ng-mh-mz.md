---
copyright:
  years: 2025
lastupdated: "2026-06-03"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, SAP HANA scale-out, High Availability, HA, Linux, Pacemaker, RHEL HA Add-On
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Configuring SAP HANA multiple-host system replication in a Red Hat Enterprise Linux High Availability Add-On cluster with the `sap-hana-ha` resource agent in an IBM Cloud multi-zone environment
{: #ha-rhel-hana-ng-mh-mz}

This information explains how to configure a Red Hat Enterprise Linux (RHEL) High Availability (HA) Add-On cluster to manage SAP HANA multiple-host system replication.
The cluster uses {{site.data.keyword.powerSysFull}} instances as cluster nodes distributed in two zones of one region. Deploy the majoritymaker host in the third zone as an {{site.data.keyword.vpc_short}} virtual server instance.
{: shortdesc}

This setup describes a performance-optimized scenario on a RHEL HA Add-On cluster.
It does not include SAP HANA-controlled host auto-failover configuration, because the RHEL HA cluster manages high availability.

This information is intended for architects and specialists who are planning a high availability deployment of SAP HANA on {{site.data.keyword.powerSys_notm}}.
{: note}

The information in this document outlines the steps to implement the scenario by first integrating SAP HANA with a RHEL High Availability Add-On cluster.
Afterward, the configuration is validated through failover testing.

## Before you begin
{: #ha-rhel-hana-ng-mh-mz-begin}

Review the general requirements, product documentation, support articles, and SAP Notes listed in [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs) and [Deploying SAP HANA Scale-Out System Replication High Availability](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html-single/deploying_sap_hana_scale-out_system_replication_high_availability/index){: external}.

Review the procedure in [Implementing a Red Hat Enterprise Linux High Availability Add-On cluster in a multi-zone region environment](/docs/sap?topic=sap-ha-rhel-mz).
 It describes how to prepare a multi-zone RHEL HA Add-On cluster for a virtual IP address resource.

RHEL supports the described configuration for a {{site.data.keyword.powerSys_notm}} cluster that uses one x86_64 node in an {{site.data.keyword.vpc_full}} environment as a quorum tiebreaker.
The tiebreaker participates in quorum voting but does not host cluster resources, as described in the following Red Hat support policies:
- [Support Policies for RHEL High Availability Clusters - IBM Power Systems Virtual Server (PowerVS) Virtual Machines as Cluster Members](https://access.redhat.com/articles/5651561){: external}
- [Support Policies for RHEL High Availability Clusters - Platforms and Architectures for Cluster Nodes](https://access.redhat.com/articles/3068791){: external}

## Prerequisites
{: #ha-rhel-hana-ng-mh-mz-prerequisites}

- Ensure that you have a valid `RHEL for SAP Applications` or `RHEL for SAP Solutions` subscription to access the repositories that are required to install SAP HANA and the high-availability resource agents.
- The minimum supported Red Hat Enterprise Linux (RHEL) version is RHEL 9.4. Use the same RHEL version on all cluster nodes.
- Deploy RHEL on at least four {{site.data.keyword.powerSys_notm}} instances in {{site.data.keyword.powerSys_notm}} and one VPC virtual server instance in {{site.data.keyword.vpc_short}} as the majoritymaker host.
   You can extend the setup to `2n + 1` nodes, where `n` is the number of SAP HANA nodes on one system replication site.
   Deploy the second replication site in the second Power zone.
   Deploy the majoritymaker host in {{site.data.keyword.vpc_short}} in the third zone.

   Ensure that the {{site.data.keyword.powerSys_notm}} instances meet the hardware and resource requirements for SAP HANA.
   For guidance, see [Planning your deployment](/docs/sap?topic=sap-powervs-planning-items).

   Use a dedicated VPC virtual server instance for the majoritymaker host. Do not run any other workload on it.
   A minimal configuration with 2 vCPUs and a 50 GB root partition is sufficient.

- Ensure that the hostnames of the virtual server instances comply with SAP HANA naming conventions.
- Install two SAP HANA systems in two different zones. Distribute each system across two {{site.data.keyword.powerSys_notm}} instances.
- Configure SAP HANA system replication between the two systems by following the procedures in [Configuring the SAP HANA system replication on RHEL](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html-single/deploying_sap_hana_scale-out_system_replication_high_availability/index#asmb_configuring_sr_v9-deploying-scale-out-system-replication){: external} and [General Prerequisites for Configuring SAP HANA System Replication](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/86267e1ed56940bb8e4a45557cee0e43.html){: external}.
- Disable the SAP HANA instance autostart on all SAP nodes as described in [Disabling SAP HANA instance autostart](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html-single/deploying_sap_hana_scale-out_system_replication_high_availability/index#proc_disabling_autostart_v9-deploying-scale-out-system-replication){: external}
- Adjust the SAP HANA nameserver configuration as described in [Adjusting the SAP HANA nameserver configuration](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html-single/deploying_sap_hana_scale-out_system_replication_high_availability/index#proc_adjusting_v9-deploying-scale-out-system-replication){: external}

   SAP HANA system replication setup is platform-independent and applies to {{site.data.keyword.powerSys_notm}}.
   Use shared regional NFS {{site.data.keyword.cloud_notm}} storage as described in [Accessing VPC regional file storage shares from IBM {{site.data.keyword.powerSys_notm}} instances](/docs/sap?topic=sap-ha-nlb-rt-rfs-intro).

   Configure hostname resolution in the global.ini file.
   Add the system_replication_hostname_resolution section and specify each SAP HANA node with its corresponding IP address.
   For details, see [Host Name Resolution for System Replication](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/c0cba1cb2ba34ec89f45b48b2157ec7b.html){: external}.

## Configuring SAP HANA system replication in a RHEL HA Add-On cluster on IBM {{site.data.keyword.powerSys_notm}}
{: #ha-rhel-hana-ng-mh-mz-configure-sr}

These instructions are based on Red Hat product documentation, Red Hat Knowledgebase articles. Use custom hook scripts from the `sap-hana-ha` resource agents.
For reference information, see [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

### Deploying local and shared NFS storage on SAP HANA nodes
{: #ha-rhel-hana-ng-mh-mz-nfs-storage}

The data and log volumes must not be shared between nodes.
Use storage area network (SAN) block storage volumes for `/hana/data` and `/hana/log`.
To attach and configure SAN block storage volumes, follow the instructions in [Creating extra storage volumes for SAP HANA {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-powervs-set-up-power-instances#powervs-set-up-power-hana-volumes).
For optimal performance, stripe the file systems across four volumes.

For volume sizing recommendations, see [General storage guidelines for SAP HANA on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-plan-storage-powervs#hana-storage-guidelines).
Ensure that the `/hana/data/<SID>` and `/hana/log/<SID>` file systems are mounted during the operating system startup on each virtual server instance.

An SAP HANA multiple-host installation requires shared storage for the `hana/shared` directory. The directory must be accessible on all SAP HANA nodes.
Each SAP site in the two Power Workspaces requires its own NFS mount target with regional availability.
Provision two {{site.data.keyword.cloud_notm}} regional NFS shares, one for each site.

For instructions on how to set up a multi-zone high availability NFS service, including NFS targets and client access, see [Accessing VPC regional file storage shares from IBM {{site.data.keyword.powerSys_notm}} instances](/docs/sap?topic=sap-ha-nlb-rt-rfs-intro).
The `hana/shared` file system must be mounted to the {{site.data.keyword.cloud_notm}} regional NFS during the operating system startup on each virtual server instance.

To mount the regional NFS share on all SAP HANA nodes at SITE_A, add the following line to `/etc/fstab`:

```sh
<IP-of-NFS-service-1>:/<Mount path> /hana/shared nfs vers=4,rw,sec=sys,hard,rsize=65536,wsize=65536,namlen=255
```
{: codeblock}

On all SAP HANA nodes at SITE_B, add the following line to `etc/fstab`:

```sh
<IP of NFS-service-2>:/<Mount path> /hana/shared nfs vers=4,rw,sec=sys,hard,rsize=65536,wsize=65536,namlen=255
```
{: codeblock}

Each site must mount its own NFS mount target to `/hana/shared` to ensure proper isolation and replication.

Before you start the SAP HANA installation, verify that the `/hana/shared` directory is mounted on all SAP HANA nodes by using the appropriate NFS mount target for each site.

```sh
mount /hana/shared
```
{: pre}

Install an SAP HANA scale-out system independently at each site.
Then, configure SAP HANA system replication to replicate data between the two sites and ensure high availability.

### Deploying subnets on the SAP HANA nodes
{: #ha-rhel-hana-ng-mh-mz-network}

This implementation uses a simple network topology with one subnet per workspace.
Attach all virtual server instances (VSIs) in the workspace to that subnet.
If you plan to use three or more subnets in a production environment, note that this configuration might require additional parameters for some setup options.

### Preparing environment variables
{: #ha-rhel-hana-ng-mh-mz-prepare-environment-variables}

To simplify the setup, define the following environment variables for the `root` user on both nodes.
These variables are used in later configuration steps.

Set the following environment variables on both nodes.

```sh
# General settings
export SID=<SID>            # SAP HANA System ID (uppercase)
export sid=<sid>            # SAP HANA System ID (lowercase)
export INSTNO=<INSTNO>      # SAP HANA instance number

# Cluster nodes in Site_A, HANA System A
export SITE_A="site1"          # HANA system replication site name
export NODE_A1=<HOSTNAME_A1>   # Virtual server instance hostname
export NODE_A2=<HOSTNAME_A2>   # Virtual server instance hostname

# Cluster nodes in Site B, HANA System B
export SITE_B="site2"          # HANA system replication site name
export NODE_B1=<HOSTNAME_B1>   # Virtual server instance hostname
export NODE_B2=<HOSTNAME_B2>   # Virtual server instance hostname

# Cluster node quorum host inside the cluster
export NODE_MAJORITYMAKER=<HOSTNAME_MAJORITYMAKER>   # Virtual server instance hostname

# multi-zone
export VIP=<IP address>     # SAP HANA system replication cluster virtual IP address
export CLOUD_REGION=<CLOUD_REGION>       # multi-zone region name
export APIKEY=<APIKEY> | <path to file>   # API Key of the IBM Cloud IAM ServiceID for the resource agent
export API_TYPE="private"      # Use private or public API endpoints  "private" or "public"

# resource agent powervs-move-ip only
export ROUTE_CRN1=<Route_CRN1>  # CRN of the static route in Workspace_1 with destination VIP (powervs-move-ip only)
export ROUTE_CRN2=<Route_CRN2>  # CRN of the static route in Workspace_2 with destination VIP (powervs-move-ip only)
export MON_API="true"           # Use cloud api in monitor command (powervs-move-ip only), "true" or "false"
```
{: codeblock}

Set the environment variables `CLOUD_REGION`, `ZONE_1`, `ZONE_2`, `ZONE_3`, `APIKEY`, `IBMCLOUD_CRN_`, `POWERVSI_` as described in [Collecting parameters for configuring a high availability cluster](/docs/sap?topic=sap-ha-vsi#ha-rhel-collect-parameters-for-cluster-config).

Set the `VIP` variable to a reserved virtual IP address for the SAP HANA system replication cluster that belongs to the subnet `CIDR` range. For details, see [Reserving virtual IP addresses](/docs/sap?topic=sap-ha-vsi#ha-vsi-reserve-virtual-ip-addresses).

The following export commands are an example of how to set the required environment variables:

```sh
export CLOUD_REGION="eu-de"
export SID=H01
export sid=h01
export INSTNO=00

export APIKEY="@/root/.apikey.json"
export API_TYPE="private"
export VIP="10.100.100.100"
export MON_API="true"
export NODE_A1=s01m1
export NODE_A2=s01w1
export NODE_B1=s02m1
export NODE_B2=s02w1
ROUTE_CRN1="crn:v1:bluemix:public:power-iaas:eu-de-1:a/0f4bdc805124434c83b6235752a0b500:b278ea15-0d06-4659-aa13-3516855cd811:route:0b19a551-259c-414a-99c9-e2c80e66fda4"
ROUTE_CRN2="crn:v1:bluemix:public:power-iaas:eu-de-2:a/0f4bdc805124434c83b6235752a0b500:93cf224a-cd3d-46d4-8634-eb6ca518b655:route:552c2f56-dc45-4259-bfcf-6817f6f43633"
...
```
{: codeblock}

### Installing the RHEL HA Add-On software packages
{: #ha-rhel-hana-ng-mh-mz-install-rhel-ha-packages}

Install the required software packages on all cluster nodes, including the majoritymaker node, by running the following commands:

```sh
dnf install -y pcs pacemaker sbd fence-agents-sbd
```
{: pre}

If the required packages are not available on a node, enable the High Availability software repository:

```sh
subscription-manager repos --enable="rhel-9-for-ppc64le-highavailability-e4s-rpms"
```
{: pre}

```sh
dnf clean all
```
{: pre}

```sh
dnf repolist
```
{: pre}

### Installing SAP HANA resource agents
{: #ha-rhel-hana-ng-mh-mz-install-sap-hana-resource-agents}

Install the required software packages on all cluster nodes and on the majoritymaker host.

The RHEL package `sap-hana-ha` includes three resource agents.
- The `SAPHanaTopology` resource agent collects status and configuration data for SAP HANA system replication on each node.
   It also starts and monitors the local *SAP HostAgent*, which is required to start, stop, and monitor SAP HANA instances.
- The `SAPHanaFilesystem` resource agent verifies access to an SAP HANA file system.
   This resource agent is optional.
   If the SAP HANA primary node experiences a storage failure, the agent fences the node to enable faster failover.
- The `SAPHanaController` resource agent manages SAP HANA instances in both single-host (scale-up) and multi-host (scale-out) configurations.
   If the SAP HANA primary fails, the agent initiates a takeover to the secondary system.

Run the following command on all cluster nodes to install the RHEL HA Add-On package `sap-hana-ha`:

```sh
dnf install -y sap-hana-ha
```
{: pre}

If the package is not available on a node, enable the software repository:

```sh
subscription-manager repos --enable="rhel-9-for-ppc64le-sap-solutions-e4s-rpms"
```
{: pre}

```sh
dnf clean all
```
{: pre}

```sh
dnf repolist
```
{: pre}

### Starting the SAP HANA system
{: #ha-rhel-hana-ng-mh-mz-start-hana-systems}

Start SAP HANA and verify that system replication is active.
For details, see [2.4. Checking SAP HANA System Replication state](https://access.redhat.com/articles/3004101#checking-sap-hana-system-replication-state){: external}.

Run the following commands on all SAP HANA nodes:

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

## Integrating SAP HANA with the Linux cluster
{: #ha-rhel-hana-ng-mh-mz-enable-hooks}

SAP HANA configuration changes are required to integrate with the Linux cluster.

The integration includes the following steps:
1. Stop the HANA system.
1. Configure the HANA system replication operation mode.
1. Implement `HanaSR.py` for the srConnectionChanged hook.
1. Implement `ChkSrv.py` for the srServiceStateChanged hook.
1. Grant sudo access to update cluster attributes to the `${sid}adm`- user.
1. Start the HANA system.

Use all hook scripts directly from the `sap-hana-ha` package.
Moving or copying these scripts can prevent future package updates from working correctly.
{: note}

### Stopping SAP HANA
{: #ha-rhel-hana-ng-mh-mz-stop-SAP-HANA}

Stop both SAP HANA systems before you integrate them into the Red Hat HA Add-On cluster.
On each master node, run the following commands:

```sh
sudo -i -u ${sid}adm -- HDB stop
```
{: pre}

```sh
sudo -i -u ${sid}adm -- sapcontrol -nr ${INSTNO} -function GetSystemInstanceList
```
{: pre}

### Enabling the SAP HANA hook scripts
{: #ha-rhel-hana-ng-mh-mz-enable-hana-sr-hook}

SAP HANA provides hooks that trigger notifications for specific events.
For details, see [Implementing a HA/DR Provider](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/1367c8fdefaa4808a7485b09815ae0f3.html){: external}.

The `srConnectionChanged()` hook enhances detection of SAP HANA system replication status changes that require cluster intervention.
It helps prevent data loss and corruption by avoiding accidental takeovers.

SAP HANA includes built-in functions to monitor the `indexserver` process.
If an issue occurs, SAP HANA attempts automatic recovery by stopping and restarting the process.
To complete the recovery or clean up after a crash, the Linux kernel must release all memory that is allocated to the process.
Cleanup operations on large databases can take a significant amount of time.
During cleanup, SAP HANA continues to operate and accept client requests, which can cause system replication to become out of sync.
If another error occurs before the indexserver restart completes, data consistency can be compromised.
The `srServiceStateChanged()` hook addresses this scenario by stopping the entire SAP HANA instance to enable faster recovery.

#### Configuring the `HanaSR` hook for all SAP HANA instances
{: #ha-rhel-hana-ng-mh-mz-activate-hana-sr-hook}

The `sap-hana-ha` package includes the `HanaSR.py` hook script in the `/usr/share/sap-hana-ha` directory.
Configure this hook on all SAP HANA cluster nodes.

1. Verify that the `/usr/share/sap-hana-ha/HanaSR.py` hook script is present on all cluster nodes.
1. Update the `global.ini` file on each SAP HANA master node to enable the `HanaSR` hook.

   Run the following command on all master nodes:

   ```sh
   sudo -i -u ${sid}adm -- <<EOT
       python \${DIR_INSTANCE}/exe/python_support/setParameter.py \
         -set SYSTEM/global.ini/ha_dr_provider_hanasr/provider=HanaSR \
         -set SYSTEM/global.ini/ha_dr_provider_hanasr/path=/usr/share/sap-hana-ha/ \
         -set SYSTEM/global.ini/ha_dr_provider_hanasr/execution_order=1 \
         -set SYSTEM/global.ini/trace/ha_dr_hanasr=info
   EOT
   ```
   {: pre}

1. Verify the updated configuration file.

   Run the following command on all master nodes:

   ```sh
   cat /hana/shared/${SID}/global/hdb/custom/config/global.ini
   ```
   {: pre}

   The output must include the following configuration entries:

   ```sh
   [ha_dr_provider_hanasr]
   provider = HanaSR
   path = /usr/share/sap-hana-ha/
   execution_order = 1

   [trace]
   ha_dr_hanasr = info
   ```
   {: codeblock}

#### Configuring the `ChkSrv` hook for all SAP HANA instances (optional)
{: #ha-rhel-hana-ng-mh-mz-enable-hana-servicestatechanged-hook}

The `sap-hana-ha` package includes the `ChkSrv.py` hook script in the `/usr/share/sap-hana-ha` directory.
Configure this hook on all cluster master nodes.
Ensure that the script is available on all cluster nodes, including the NODE_MAJORITYMAKER.

The `/usr/share/sap-hana-ha/ChkSrv.py` script stops the entire SAP HANA instance to enable faster recovery.
If `automated failover` is enabled and the secondary node is healthy, the cluster initiates a takeover.
Otherwise, a forced restart of the instance on the local node results in a fast recovery.

The hook script analyzes instance events, applies filters to event details, and triggers actions based on the results.
It distinguishes between an `indexserver` process that is managed by SAP HANA or the one that is stopped during a planned instance shutdown.

The hook behavior depends on the configuration of the `action_on_lost` parameter:

Ignore
:   Logs the event and decision details without taking further action.

Stop
:  Gracefully stops the SAP HANA instance by using the HDB command.

Kill
:   Forcefully stops the instance by using the `HDB kill-<signal>` command (default: signal 9).
    The signal value is configurable.

Both the `stop` and the `kill` actions result in a stopped SAP HANA instance.
The `kill` action is typically faster.

1. Verify that the `/usr/share/sap-hana-ha/ChkSrv.py` hook script is present on all cluster nodes.
1. Update the `global.ini` file on each SAP HANA system to enable the `ChkSrv` hook.

   Run the following command on NODE_A1 and NODE_B1:

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

1. Verify that the `/hana/shared/${SID}/global/hdb/custom/config/global.ini` file contains the following configuration entries:

   ```sh
   [ha_dr_provider_chksrv]
   provider = ChkSrv
   path = /usr/share/sap-hana-ha/
   execution_order = 2
   action_on_lost = stop

   [trace]
   ha_dr_chksrv = info
   ```
   {: codeblock}

   In this example, the `action_on_lost` parameter is set to `stop`.
   The default is `ignore`.

   Optional parameters include:
   - `stop_timeout`: Specifies the timeout for a graceful stop. The default value is 20 seconds.
   - `kill_signal`: Specifies the signal used for forced termination. The default value is 9.

#### Verifying all SAP HANA hooks
{: #ha-rhel-hana-ng-mh-mz-verify-all-hooks}

Verify that the `/hana/shared/${SID}/global/hdb/custom/config/global.ini` file contains the following entries after you configured all SAP HANA hooks:

```sh
[ha_dr_provider_hanasr]
provider = HanaSR
path = /usr/share/sap-hana-ha/
execution_order = 1

[ha_dr_provider_chksrv]
provider = ChkSrv
path = /usr/share/sap-hana-ha/
execution_order = 2
action_on_lost = stop

[trace]
ha_dr_hanasr = info
ha_dr_chksrv = info
```
{: codeblock}

These entries confirm that all SAP HANA HA/DR hook scripts are correctly registered and configured.

#### Creating sudo settings to allow the `${sid}adm` user to update cluster attributes
{: #ha-rhel-hana-ng-mh-mz-sudo-settings}

Create sudo settings for the SAP HANA operating system administrator user.
To enable the `${sid}adm` user to update node attributes during execution of the `srConnectionChanged()` hook, run the following commands on all SAP HANA nodes.

Create a sudoers file with the required command specifications for `${sid}adm`:

   ```sh
   cat >> /etc/sudoers.d/20-saphana << EOT
   Defaults:${sid}adm !requiretty
   ${sid}adm  ALL=(ALL) NOPASSWD: /usr/sbin/crm_attribute -n hana_*
   ${sid}adm ALL=(ALL) NOPASSWD: /usr/bin/SAPHanaSR-hookHelper

   EOT
   ```
   {: pre}

   Set appropriate ownership and permissions for the sudoers file, and then verify its contents:

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

Correct any issues reported by the `visudo -c` command to ensure that the sudoers file is valid.
{: note}

### Starting SAP HANA
{: #ha-rhel-hana-ng-mh-mz-start-SAP-HANA}

Start the SAP HANA system on both HANA master nodes.

```sh
sudo -i -u ${sid}adm -- HDB start
```
{: pre}

Wait until the `dispstatus` changes to `GREEN`, which confirms that the SAP HANA system is running.

### Verifying trace messages from HA/DR hooks
{: #ha-rhel-hana-ng-mh-mz-check-hooks}

The HA/DR hooks are activated when the SAP HANA system restarts.
Verify that the hooks logged some trace messages.

Run the following command on both SAP HANA master nodes:

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

## Configuring a RHEL HA Add-On cluster
{: #ha-rhel-hana-ng-mh-mz-ha-cluster}

### Adding cluster node entries to the hosts file
{: #ha-rhel-hana-ng-mh-mz-hosts-file}

On each cluster node, add the IP addresses and hostnames of all participating nodes to the `/etc/hosts` file.
For detailed guidance, see [Setting up /etc/hosts files on RHEL cluster nodes](https://access.redhat.com/solutions/81123){: external}.
Verify that all nodes can ping each other.
The {{site.data.keyword.tg_full}}, routes, and access control lists (ACLs) must be deployed correctly.

### Configuring firewall services
{: #ha-rhel-hana-ng-mh-mz-firewall}

If `firewalld.service` is installed and enabled, add the high availability service to the firewall.
Temporarily disable the firewall during the setup of HANA system replication. Re-enable the firewall after the configuration is complete.
The following example applies to an SAP HANA database with a single tenant.
If your system includes multiple tenants, configure additional firewall rules.

Run the following commands on all cluster nodes:

```sh
firewall-cmd --zone=public --add-port={1128,1129,2224,3${INSTNO}00,3${INSTNO}01,3${INSTNO}02,3${INSTNO}03,3${INSTNO}06,3${INSTNO}07,3${INSTNO}10,3${INSTNO}13,3${INSTNO}15,3${INSTNO}17,3${INSTNO}40,3${INSTNO}41,3${INSTNO}42,4${INSTNO}01,4${INSTNO}02,4${INSTNO}03,4${INSTNO}06,4${INSTNO}07,4${INSTNO}40,40406,40412,5${INSTNO}13,5${INSTNO}14}/tcp --permanent
```
{: pre}

```sh
firewall-cmd --add-service=high-availability --permanent
```
{: pre}

This service opens the following ports: 2224/TCP, 3121/TCP, 5403/TCP, 5404/UDP, 5405/UDP, 9929/TCP, 9929/UDP, 21064/TCP.
They are listed and explained in [RHEL documentation on Ports to Enable for High Availability Add-On](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-firewalls-HAAR#tb-portenable-HAAR){: external}.

```sh
firewall-cmd --reload
```
{: pre}

If your SAP HANA system includes multiple tenant databases, add more ports to the firewall rules.
Tenant databases do not use fixed port assignments.
For more information, see [Port Assignment in Tenant Databases](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/440f6efe693d4b82ade2d8b182eb1efb.html?locale=en-US&version=LATEST){: external}.
For port ranges for additional tenant databases, see [Connections for Distributed SAP HANA Systems](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/82cea8fe69604f3ab0d4624248b6e523.html?locale=en-US&version=LATEST){: external}.

The following port ranges are commonly used by tenant databases and their associated services:

| **TCP Port**   | **Service**    | **Note**                                                    |
| -------------- | -------------- | ------------------------------------------------------------|
| 4xx40 - 4xx97  | indexserver    | Log and data shipping (tenant databases)                    |
| 4xx40 - 4xx97  | scriptserver   | Log and data shipping (optional, tenant databases)          |
|4xx40 - 4xx97   | docstore       | Log and data shipping (optional, tenant databases)          |
|3xx04           |scriptserver    |Port used by the script server of the first tenant database  |
|3xx05           |diserver        |Tenant database ports                                        |
|3xx25           |diserver        |Port used by the diserver of the initial tenant database     |
|3xx4n           |diserver        |Tenant database ports                                        |
|3xx06           |webdispatcher   |Port used by the webdispatcher of the initial tenant database|
|3xx07           |xsengine        |Port used by the xsengine of the initial tenant database     |
|3xx08           |xsengine        |Port used by the xsengine of the initial tenant database created in a new installation or an upgraded single-container system                                             |
|3xx42           |xsengine        |Tenant database ports                                        |
|3xx11           |dpserver        |Port used by the dpserver of the initial tenant database     |
{: caption="Port ranges used by SAP tenant databases" caption-side="bottom"}

#### Starting the PCS service
{: #ha-rhel-hana-ng-mh-mz-start-pcsd-service}

Enable and start the PCS service, which controls and configures RHEL High Availability (HA) Add-On clusters.

Run the following command on each cluster node:

```sh
systemctl enable --now pcsd.service
```
{: pre}

Verify that the PCS service is running:

```sh
systemctl status pcsd.service
```
{: pre}

#### Setting a password for the hacluster user
{: #ha-rhel-hana-ng-mh-mz-set-hacluster-password}

Set the same password for the hacluster user on each cluster node by running the following command:

```sh
passwd hacluster
```
{: pre}

#### Authenticating the cluster nodes
{: #ha-rhel-hana-ng-mh-mz-authenticate-cluster-nodes}

Authenticate the hacluster user to the PCS daemon on the cluster nodes.
Enter the password that you set in the previous step when prompted.

Run the following command on NODE_A1:

```sh
pcs host auth ${NODE_A1} ${NODE_A2} ${NODE_B1} ${NODE_B2} ${NODE_MAJORITYMAKER} -u hacluster
```
{: pre}

Proceed only if all nodes return `Authorized`.

#### Configuring and starting the cluster nodes
{: #ha-rhel-hana-ng-mh-mz-configure-and-start-cluster-nodes}

Run the following command on NODE_A1 to configure the cluster and synchronize its configuration across the specified nodes:

```sh
pcs cluster setup ${CLUSTERNAME} ${NODE_A1} ${NODE_A2} ${NODE_B1} ${NODE_B2} ${NODE_MAJORITYMAKER}
```
{: pre}

In this setup, node names are used to resolve IP addresses.

Alternatively, you can define IP addresses explicitly.
A cluster setup, which spans multiple subnets, might look like the following:

```sh
pcs cluster setup ${CLUSTERNAME} \
  ${NODE_A1} addr=<IP_ETH1> addr=<IP_ETH2> \
  ${NODE_A2} addr=<IP_ETH1> addr=<IP_ETH2> \
  ${NODE_B1} addr=<IP_ETH1> addr=<IP_ETH2> \
  ${NODE_B2} addr=<IP_ETH1> addr=<IP_ETH2> \
  ${NODE_MAJORITYMAKER} addr=<IP_ETH1> addr=<IP_ETH2>
```
{: screen}

Start the cluster on all nodes by running the following command on a cluster node:

```sh
pcs cluster start --all
```
{: pre}

Optionally, you can use the command `pcs cluster enable --all` to enable the cluster to start automatically on system boot across all cluster nodes.
This automatic startup of the Corosync and Pacemaker services might be useful in environments where automatic cluster reintegration is preferred.
Use this option only if it aligns with your operational requirements.
{: note}

Run the following command to check the cluster status:

```sh
pcs cluster status
```
{: pre}

#### Creating the fencing device
{: #ha-rhel-hana-ng-mh-mz-create-fencing}

STONITH (Shoot the other node in the head) is a fencing mechanism that protects data from corruption in split-brain scenarios.

Enable STONITH (fencing) for a RHEL HA Add-On production cluster.
{: important}

You can test the fence agent by using the parameters that you collected in the [Collecting parameters for configuring a high availability cluster](/docs/sap?topic=sap-ha-vsi#ha-rhel-collect-parameters-for-cluster-config) section.

#### Creating the STONITH devices
{: #ha-rhel-hana-ng-mh-mz-create-stonith-device}

Use fencing method SBD (Storage-Based Death) poison-pill fencing in the multizone environment.
Each node constantly monitors connectivity to the storage device, and terminates itself in case the partition becomes unreachable.
SBD fencing requires shared disks and a watchdog device.
This configuration uses three iSCSI targets on {{site.data.keyword.vpc_short}} across three VPC zones for storage-based fencing.

Follow the instructions in
[Implementing SBD poison-pill fencing in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-sbd).

Complete the following tasks:

- Create the iSCSI target servers.
- Configure the watchdog.
- Create and enable three iSCSI SBD devices on the cluster nodes.

Restart the cluster to activate the changes.
Then, verify the status of the SBD devices.

Do not use the device paths `/dev/disk/by-id` as described,
but use the device paths `/dev/disk/by-path/` as they result in unique filepaths with the full LUN-path as file name.
`ls /dev/disk/by-path | awk '$1 ~ "3260" { count++; print "export SBD_DEV_" count "=/dev/disk/by-path/ip-*" $1}'`
{: note}

```sh
pcs stonith sbd status
```
{: pre}

After you enable the SBD service, restart the cluster to activate the changes.
Also, change the quorum timeout to avoid conflicts with the SBD watchdog timeout.

```sh
pcs cluster stop --all
```
{: pre}


On each cluster node, add the following line to the quorum section of the `/etc/corosync/corosync.conf` file:

```sh
    sync_timeout: 3000
```
{: pre}

For example:

```sh
...
quorum {
    provider: corosync_votequorum
    sync_timeout: 3000
}
...
```
{: pre}

On each cluster node, start the cluster to activate the changes.
```sh
pcs cluster start
```
Running pcs `cluster start --all` on a single cluster node might result in a timeout.
{: Note}



Extract and export the iSCSI device IDs by using the following command:

```sh
ls /dev/disk/by-path | awk '$1 ~ "3260" { count++; print "export SBD_DEV_" count "=/dev/disk/by-path/ip-*" $1}'
```
{: pre}


Configure SBD poison-pill fencing in the cluster:

```sh
pcs stonith create res_fence_sbd fence_sbd  devices="${SBD_DEV_1},${SBD_DEV_2},${SBD_DEV_3}" pcmk_reboot_timeout=90
```
{: pre}

Verify the configuration with the following commands:

```sh
pcs config
```
{: pre}

```sh
pcs status
```
{: pre}

```sh
pcs stonith config
```
{: pre}

```sh
pcs stonith status
```
{: pre}

#### Setting the stonith-action cluster property
{: #ha-rhel-set-stonith-action-cluster-property}

To speed up failover times in an IBM {{site.data.keyword.powerSys_notm}} cluster, you can change the cluster property `stonith-action` to `off`.
When the cluster initiates a fencing action, it triggers a `power off` operation instead of a `reboot` for the fenced instance.

After this change, you always need to log in to the {{site.data.keyword.cloud_notm}} Console, and manually start an instance that was fenced by the cluster.

```sh
pcs property set stonith-action=off
```
{: pre}

Verify that the property is updated.

```sh
pcs config
```
{: pre}

### Putting the cluster in maintenance mode
{: #ha-rhel-hana-ng-mh-mz-maintenance-mode}

Put the cluster in maintenance mode to prevent unintended actions while you configure the cluster resources.

```sh
pcs property set maintenance-mode=true
```
{: pre}

### Configuring general cluster properties
{: #ha-rhel-hana-ng-mh-mz-configure-cluster-properties}

To prevent resource failover during initial testing and post-production, set the following default values for the `resource-stickiness` and `migration-threshold` parameters
These default values do not apply to resources that specify custom overrides.

Run the following commands on NODE_A1.

```sh
pcs resource defaults update resource-stickiness=1000
```
{: pre}

```sh
pcs resource defaults update migration-threshold=5000
```
{: pre}

Verify the resource defaults.

```sh
pcs resource defaults
```
{: pre}

### Configuring the systemd-based SAP startup framework
{: #ha-rhel-hana-ng-mh-mz-configure-systemd-SAP}

On RHEL 9, SAP HANA 2.0 SPS07 revision 70 and later uses systemd integration by default.
In high availability environments, apply additional configuration to ensure proper integration of the various systemd services involved in the cluster operations.

Configure the pacemaker systemd service to manage the HANA instance systemd service in the correct order on all cluster nodes that are running HANA instances.

1. Create the /etc/systemd/system/pacemaker.service.d/ directory for the Pacemaker service drop-in file.

   ```sh
   mkdir /etc/systemd/system/pacemaker.service.d/
   ```
   {: pre}

1. Create the systemd drop-in file for the Pacemaker service with the following content:

   ```sh
   cat << EOF > /etc/systemd/system/pacemaker.service.d/00-pacemaker.conf
   [Unit]
   Description=Pacemaker needs the SAP HANA instance service
   Wants=SAP${SID}_${INSTNO}.service
   After=SAP${SID}_${INSTNO}.service
   EOF
   ```
   {: pre}

1. Reload the systemctl daemon.

   ```sh
   systemctl daemon-reload
   ```
   {: pre}

### Creating a cloned SAPHanaTopology resource
{: #ha-rhel-hana-ng-mh-mz-create-saphanatopology-resource}

Run the following command on NODE_A1 to create the `SAPHanaTopology` resource.

```sh
pcs resource create SAPHanaTopology_${SID}_${INSTNO} SAPHanaTopology \
    SID=${SID} \
    InstanceNumber=${INSTNO} \
    op start timeout=600 \
    op stop timeout=300 \
    op monitor interval=10 timeout=600 \
    clone meta clone-max=4 clone-node-max=1 interleave=true \
    --future \
    --disabled
```
{: pre}

Verify the resource configuration and cluster status by running the following commands:

```sh
pcs resource config SAPHanaTopology_${SID}_${INSTNO}
```
{: pre}

```sh
pcs resource config SAPHanaTopology_${SID}_${INSTNO}-clone
```
{: pre}

### Creating a cloned SAPHanaFilesystem resource
{: #ha-rhel-hana-ng-mh-mz-create-saphanafilesystem-resource}

The *SAPHanaFilesystem* resource monitors the file systems that are used by the SAP HANA system.

Run the following command on NODE_A1 to create the `SAPHanaFilesystem` resource:

```sh
pcs resource create SAPHanaFilesystem_${SID}_${INSTNO} SAPHanaFilesystem \
    SID=${SID} \
    InstanceNumber=${INSTNO} \
    op start interval=0 timeout=10 \
    op stop interval=0 timeout=20 \
    op monitor interval=120 timeout=120 \
    clone meta clone-max=4 clone-node-max=1 interleave=true \
    --future \
    --disabled
```
{: pre}

Verify the resource configuration and cluster status by running the following commands:

```sh
pcs resource config SAPHanaFilesystem_${SID}_${INSTNO}
```
{: pre}

```sh
pcs resource config SAPHanaFilesystem_${SID}_${INSTNO}-clone
```
{: pre}

### Creating a promotable SAPHanaController resource
{: #ha-rhel-hana-ng-mh-mz-create-saphana-resource}

The *SAPHanaController* resource manages the four SAP HANA instances that are configured as HANA system replication nodes.

Run the following command on NODE_A1 to create the `SAPHanaController` resource.

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
    promotable meta clone-max=4 clone-node-max=1 interleave=true \
    --future \
    --disabled
```
{: pre}

Verify the resource configuration and check the cluster status.

```sh
pcs resource config SAPHanaController_${SID}_${INSTNO}
```
{: pre}

```sh
pcs resource config SAPHanaController_${SID}_${INSTNO}-clone
```
{: pre}

### Creating a virtual IP address cluster resource
{: #ha-rhel-hana-ng-mh-mz-create-virtual-ip-resource}

Follow the procedures in [Implementing a Red Hat Enterprise Linux High Availability Add-On cluster in a multi-zone region environment](/docs/sap?topic=sap-ha-rhel-mz#ha-rhel-mz-install-powervs-move-ip-resource-agent).
For architectural details and additional information, see [Resource agent powervs-move-ip](/docs/sap?topic=sap-ha-overview#ha-overview-hana-network-ra-move-ip).

Install the `powervs-move-ip` resource agent on all cluster nodes, create the static route in the workspace for the virtual IP and create a service ID for the resource agent.

Create the cluster resource for the virtual IP:

```sh
pcs resource create vip_${SID}_${INSTNO} powervs-move-ip \
    api_key=${APIKEY} \
    api_type=${API_TYPE} \
    ip=${VIP} \
    route_host_map="${NODE_A1}:${ROUTE_CRN1};${NODE_B1}:${ROUTE_CRN2}" \
    region=${CLOUD_REGION} \
    monitor_api=${MON_API} \
    op start timeout=60 \
    op stop timeout=60 \
    op monitor interval=60 timeout=60 \
    --disabled
```
{: pre}

Verify the cluster resource for the virtual IP:

```sh
pcs resource config vip_${SID}_${INSTNO}
```
{: pre}

### Creating cluster resource constraints
{: #ha-rhel-hana-ng-mh-mz-create-constraints}

Ensure that the `SAPHanaTopology` resources start before the `SAPHana` resources.

Assign the virtual IP address to the node that hosts the primary `SAPHanaController` resource.

1. Create a resource constraint to start `SAPHanaTopology` before `SAPHanaController`.
   This constraint enforces the correct start order between the topology and controller resources.

   Run the following command on NODE_A1 to create the resource start-order constraint.

   ```sh
   pcs constraint order SAPHanaTopology_${SID}_${INSTNO}-clone \
       then SAPHanaController_${SID}_${INSTNO}-clone symmetrical=false
   ```
   {: pre}

   Verify the constraint configuration.

   ```sh
   pcs constraint
   ```
   {: pre}

1. Create a resource constraint to colocate the virtual IP address with the SAP HANA system replication primary instance.
   This constraint ensures that the virtual IP address resource is colocated with the `SAPHanaController` resource that is promoted as primary.

   Run the following command on NODE_A1 to create the colocation constraint.

   ```sh
   pcs constraint colocation add vip_${SID}_${INSTNO} \
       with Promoted SAPHanaController_${SID}_${INSTNO}-clone 2000
   ```
   {: pre}

   Verify the constraint configuration and check the cluster status.

   ```sh
   pcs constraint
   ```
   {: pre}

   The sample output shows:

   ```sh
   pcs constraint
   Colocation Constraints:
     Started resource 'vip_H01_00' with Promoted resource 'SAPHanaController_H01_00-clone'
       score=2000
   Order Constraints:
    start resource 'SAPHanaTopology_H01_00-clone' then start resource 'SAPHanaController_H01_00-clone'
      symmetrical=0
   ```
   {: screen}

1. Create a location constraint to prevent any SAP HANA Resources from running on the majoritymaker node.
   This constraint also prevents the cluster from checking the initial resource status on that node.
   The constraint definition uses a regexp expression to match all HANA resources.
   Adjust the pattern if your resource names differ.

   ```sh
   pcs constraint location add avoid-${NODE_MAJORITYMAKER} \
     regexp%.*SAPHana.* ${NODE_MAJORITYMAKER}  -- -INFINITY resource-discovery=never
   ```
   {: pre}

1. Verify the constraint configuration.

   ```sh
   pcs constraint location
   ```
   {: pre}

   The sample output shows:

   ```sh
   Location Constraints:
     resource pattern '.*SAPHana.*' avoids node 's03mm' with score INFINITY resource-discovery=never
   ```
   {: screen}

1. Enable the cluster on the majoritymaker node so that it starts automatically during system startup. Run the following command on any node:

   ```sh
   pcs cluster enable ${NODE_MAJORITYMAKER}
   ```
   {: pre}

   Adding the new node increases the vote count, which results in an odd number of votes.
   This configuration prevents an equal vote split during quorum decisions.

### Removing the cluster from maintenance mode
{: #ha-rhel-hana-ng-mh-mz-end-maintenance-mode}

Remove the cluster from maintenance mode to resume normal resource operations.

```sh
pcs property set maintenance-mode=false
```
{: pre}

### Managing quorum in cluster systems
{: #ha-rhel-hana-ng-mh-mz-quorum}

To maintain cluster integrity and availability, cluster systems use a concept that is called quorum to prevent data corruption and loss.
Quorum is established through a voting system.
When a cluster node becomes unresponsive or loses communication with the rest of the cluster, a majority of functioning nodes can vote to isolate and, if necessary, fence the affected node.
Pacemaker quorum features help prevent split-brain scenarios, where disconnected parts of the cluster continue operating independently.
A split-brain scenario can lead to conflicting writes and potential data corruption or loss.

A Red Hat Enterprise Linux High Availability Add-On cluster uses the `votequorum` service, together with fencing, to prevent split-brain scenarios.
Each system in the cluster contributes a defined number of votes.
Cluster operations proceed only when a majority of votes is available.
In this configuration, quorum is not managed through a separate mechanism.
Instead, decision-making relies on an uneven number of votes across cluster nodes, ensuring a clear majority and avoiding ambiguity during failure scenarios.

For more details, see [Quorum in cluster systems](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/configuring_and_managing_high_availability_clusters/assembly_overview-of-high-availability-configuring-and-managing-high-availability-clusters#quorum){: external}

Verify the quorum status.

```sh
pcs quorum status

```
{: pre}

### Enabling the cluster resources
{: #ha-rhel-hana-ng-mh-mz-enable-resources}

All cluster resources are initially created with the `--disabled` flag to prevent automatic startup during configuration.

To enable the resources, run the following commands on NODE_A:

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

Verify that all resources are active and the cluster is operating normally.

```sh
pcs status --full
```
{: pre}

The following example shows the output of an SAP HANA system replication cluster that is configured in a single zone.

```sh
# pcs status --full
Cluster name: PACEMAKER-MZ
Cluster Summary:
  * Stack: corosync (Pacemaker is running)
  * Current DC: s01m1 (1) (version 2.1.7-5.3.el9_4-0f7f88312) - partition with quorum
  * Last updated: Thu Dec  4 10:51:44 2025 on s01m1
  * Last change:  Thu Dec  4 10:51:38 2025 by root via root on s01m1
  * 5 nodes configured
  * 14 resource instances configured

Node List:
  * Node s01m1 (1): online, feature set 3.19.0
  * Node s01w1 (2): online, feature set 3.19.0
  * Node s02m1 (3): online, feature set 3.19.0
  * Node s02w1 (4): online, feature set 3.19.0
  * Node s03mm (5): online, feature set 3.19.0

Full List of Resources:
  * res_fence_sbd	(stonith:fence_sbd):	 Started s01m1
  * Clone Set: SAPHanaTopology_H01_00-clone [SAPHanaTopology_H01_00]:
    * SAPHanaTopology_H01_00	(ocf:heartbeat:SAPHanaTopology):	 Started s01m1
    * SAPHanaTopology_H01_00	(ocf:heartbeat:SAPHanaTopology):	 Started s01w1
    * SAPHanaTopology_H01_00	(ocf:heartbeat:SAPHanaTopology):	 Started s02m1
    * SAPHanaTopology_H01_00	(ocf:heartbeat:SAPHanaTopology):	 Started s02w1
  * Clone Set: SAPHanaFilesystem_H01_00-clone [SAPHanaFilesystem_H01_00]:
    * SAPHanaFilesystem_H01_00	(ocf:heartbeat:SAPHanaFilesystem):	 Started s01m1
    * SAPHanaFilesystem_H01_00	(ocf:heartbeat:SAPHanaFilesystem):	 Started s01w1
    * SAPHanaFilesystem_H01_00	(ocf:heartbeat:SAPHanaFilesystem):	 Started s02m1
    * SAPHanaFilesystem_H01_00	(ocf:heartbeat:SAPHanaFilesystem):	 Started s02w1
  * Clone Set: SAPHanaController_H01_00-clone [SAPHanaController_H01_00] (promotable):
    * SAPHanaController_H01_00	(ocf:heartbeat:SAPHanaController):	 Promoted s01m1
    * SAPHanaController_H01_00	(ocf:heartbeat:SAPHanaController):	 Unpromoted s01w1
    * SAPHanaController_H01_00	(ocf:heartbeat:SAPHanaController):	 Unpromoted s02m1
    * SAPHanaController_H01_00	(ocf:heartbeat:SAPHanaController):	 Unpromoted s02w1
  * vip_H01_00	(ocf:heartbeat:powervs-move-ip):	 Started s01m1

Node Attributes:
  * Node: s01m1 (1):
    * hana_h01_clone_state            	: PROMOTED
    * hana_h01_roles                  	: master1:master:worker:master
    * hana_h01_site                   	: site1
    * hana_h01_srah                   	: -
    * hana_h01_version                	: 2.00.078.00
    * hana_h01_vhost                  	: s01m1
    * master-SAPHanaController_H01_00 	: 150
  * Node: s01w1 (2):
    * hana_h01_clone_state            	: DEMOTED
    * hana_h01_roles                  	: slave:slave:worker:slave
    * hana_h01_site                   	: site1
    * hana_h01_srah                   	: -
    * hana_h01_version                	: 2.00.078.00
    * hana_h01_vhost                  	: s01w1
    * master-SAPHanaController_H01_00 	: -10000
  * Node: s02m1 (3):
    * hana_h01_clone_state            	: DEMOTED
    * hana_h01_roles                  	: master1:master:worker:master
    * hana_h01_site                   	: site2
    * hana_h01_srah                   	: -
    * hana_h01_version                	: 2.00.078.00
    * hana_h01_vhost                  	: s02m1
    * master-SAPHanaController_H01_00 	: 100
  * Node: s02w1 (4):
    * hana_h01_clone_state            	: DEMOTED
    * hana_h01_roles                  	: slave:slave:worker:slave
    * hana_h01_site                   	: site2
    * hana_h01_srah                   	: -
    * hana_h01_version                	: 2.00.078.00
    * hana_h01_vhost                  	: s02w1
    * master-SAPHanaController_H01_00 	: -12200

Migration Summary:

Tickets:

PCSD Status:
  s01m1: Online
  s01w1: Online
  s02m1: Online
  s02w1: Online
  s03mm: Online

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
  sbd: active/enabled
```
{: screen}

On the promoted cluster node, verify that the cluster service IP address is assigned and active.

```sh
ip address show
```
{: pre}

### Enabling automated registration of the secondary instance
{: #ha-rhel-hana-ng-mh-mz-enable-automated-registration}

Set the `AUTOMATED_REGISTER` parameter based on your operational requirements.
To retain the ability to revert to the previous primary SAP HANA instance, set `AUTOMATED_REGISTER=false`.
This setting prevents the automatic registration of the former primary as a new secondary.

If data issues occur after a cluster-initiated takeover, manual reversion is possible if `AUTOMATED_REGISTER=false`.

If `AUTOMATED_REGISTER=true`, the previous primary SAP HANA instance automatically registers as a secondary and cannot be restored to its previous state.
The advantage of setting `AUTOMATED_REGISTER=true` is that high availability is automatically restored when the failed node rejoins the cluster.

Use the default value `AUTOMATED_REGISTER=false` until the cluster is fully tested and failover scenarios are verified.

Use the `pcs resource update` command to modify resource attributes.
The following example sets `AUTOMATED_REGISTER=true`.

```sh
pcs resource update SAPHanaController_${SID}_${INSTNO} AUTOMATED_REGISTER=true
```
{: pre}

## Testing the SAP HANA system replication cluster
{: #ha-rhel-hana-ng-mh-mz-test-sap-hana-sr-cluster}


Test the cluster configuration thoroughly to ensure that all components operate as expected.
The following examples illustrate failover test scenarios.
This list is not exhaustive.

Each test case includes the following details:

- Component that is being tested
- Test description
- Prerequisites and cluster state before the failover is initiated
- Test procedure
- Expected behavior and results
- Recovery procedure

### Test 1 - Testing failure of the primary master node
{: #ha-rhel-hana-ng-mh-mz-test1-primary-master-database-failure}

Use the following procedure to test a failure of the SAP HANA primary master instance.

#### Test 1 - Description
{: #ha-rhel-hana-ng-mh-mz-test1-description}

Simulate a crash of the SAP HANA primary master instance that is running on NODE_A1.

#### Test 1 - Prerequisites
{: #ha-rhel-hana-ng-mh-mz-test1-prerequisites}

- A functional five-node RHEL HA Add-On cluster is configured for SAP HANA system replication.
- The cluster is running on all nodes.
- The `SAPHanaController_${SID}_${INSTNO}` resource is configured with `AUTOMATED_REGISTER=false`.
- SAP HANA system replication status:
   - The SAP HANA primary master instance is running on NODE_A1.
   - The SAP HANA secondary master instance is running on NODE_B1.
   - System replication is active and in sync.

#### Test 1 - Test procedure
{: #ha-rhel-hana-ng-mh-mz-test1-procedure}

To simulate a crash of the SAP HANA primary master instance, send a `SIGKILL` signal by running the following command on NODE_A1:

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test 1 - Expected behavior
{: #ha-rhel-hana-ng-mh-mz-test1-expected-behavior}

- The SAP HANA primary master instance on NODE_A1 crashes.
- The cluster detects the stopped instance and marks the resource as `failed`.
- The cluster promotes the SAP HANA secondary master instance on NODE_B1 to become the new primary.
- The virtual IP address is released from NODE_A1 and reassigned to NODE_B1.
- Applications such as SAP S/4HANA and SAP NetWeaver automatically reconnect to the new SAP HANA primary master instance, and query processing continues on the nodes at SITE_B.

#### Test 1 - Recovery procedure
{: #ha-rhel-hana-ng-mh-mz-test1-recovery-procedure}

Because the `SAPHanaController_${SID}_${INSTNO}` resource is configured with `AUTOMATED_REGISTER=false`, the cluster does not restart the failed SAP HANA instance or register it with the new primary.
As a result, the new primary master on NODE_B1 shows the former secondary in `CONNECTION TIMEOUT` status.

To manually reregister the previous primary master as a new secondary master, run the following command on NODE_A1:

```sh
sudo -i -u ${sid}adm -- <<EOT
    hdbnsutil -sr_register \
      --name=${SITE_A} \
      --remoteHost=${NODE_B1} \
      --remoteInstance=00 \
      --replicationMode=sync \
      --operationMode=logreplay \
      --online
EOT
```
{: pre}

To verify the SAP HANA system replication status, run the following command:

```sh
sudo -i -u ${sid}adm -- <<EOT
    hdbnsutil -sr_state
    HDBSettings.sh systemReplicationStatus.py
EOT
```
{: pre}

After manual registration and resource refresh, the new secondary master instance restarts and appears in `SOK` (synced) status.

Run the following command on NODE_A1 to refresh the resource:

```sh
pcs resource refresh SAPHanaController_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}

### Test 2 - Testing node failure of the primary master instance
{: #ha-rhel-hana-ng-mh-mz-test2-primary-instance-server-failure}

Use the following procedure to simulate and test a failure of the node that hosts the SAP HANA primary master instance.

#### Test 2 - Description
{: #ha-rhel-hana-ng-mh-mz-test2-description}

Simulate a crash of the node that hosts the SAP HANA primary master instance.

#### Test 2 - Preparation
{: #ha-rhel-hana-ng-mh-mz-test2-preparation}

Ensure that the `SAPHanaController_${SID}_${INSTNO}` resource is configured with `AUTOMATED_REGISTER=true`.

To update and verify the resource configuration, run the following commands on NODE_A1:

```sh
pcs resource update SAPHanaController_${SID}_${INSTNO} AUTOMATED_REGISTER=true
```
{: pre}

```sh
pcs resource config SAPHanaController_${SID}_${INSTNO}
```
{: pre}

#### Test 2 - Prerequisites
{: #ha-rhel-hana-ng-mh-mz-test2-prerequisites}

- A functional five-node RHEL HA Add-On cluster is configured for HANA system replication.
- The cluster is running on all nodes.
- Verify that SAP HANA system replication is active and synchronized:
   - The SAP HANA primary master instance is running on NODE_B1.
   - The SAP HANA secondary master instance is running on NODE_A1.
   - System replication is active and in sync.

#### Test 2 - Test procedure
{: #ha-rhel-hana-ng-mh-mz-test2-procedure}

To simulate a failure of NODE_B1, trigger an immediate shut off request by running the following command on NODE_B1:

```sh
sync; echo o > /proc/sysrq-trigger
```
{: pre}

#### Test 2 - Expected behavior
{: #ha-rhel-hana-ng-mh-mz-test2-expected-behavior}

- NODE_B1 shuts down.
- The cluster detects the node failure and marks its state as `OFFLINE`.
- The cluster promotes the SAP HANA secondary master instance on NODE_A1 to primary master.
- The virtual IP address is reassigned to NODE_A1.
- Applications such as SAP S/4HANA and SAP NetWeaver automatically reconnect to the new SAP HANA primary master instance, and query processing continues on the nodes at SITE_A.

#### Test 2 - Recovery procedure
{: #ha-rhel-hana-ng-mh-mz-test2-recovery-procedure}

Log in to the {{site.data.keyword.cloud}} console and start the NODE_B1 virtual server instance.
After NODE_B1 becomes available, restart the cluster framework.

Run the following commands on NODE_B1:

```sh
pcs cluster start
```
{: pre}

```sh
pcs status --full
```
{: pre}

Because `AUTOMATED_REGISTER=true` is configured for the `SAPHanaController_${SID}_${INSTNO}` resource, SAP HANA automatically restarts when NODE_B1 rejoins the cluster.
The former primary master instance is reregistered as a secondary master.

### Test 3 - Testing failure of the secondary master instance
{: #ha-rhel-hana-ng-mh-mz-test3-secondary-instance-database-failure}

Follow these steps to simulate a failure of the SAP HANA secondary master instance.

#### Test 3 - Description
{: #ha-rhel-hana-ng-mh-mz-test3-description}

This test simulates a crash of the SAP HANA secondary master to validate cluster failover behavior.

#### Test 3 - Prerequisites
{: #ha-rhel-hana-ng-mh-mz-test3-prerequisites}

- A functional five-node RHEL HA Add-On cluster is configured for HANA system replication.
- The cluster is running on all nodes.
- The `SAPHanaController_${SID}_${INSTNO}` resource is configured with `AUTOMATED_REGISTER=true`.
- Verify that SAP HANA system replication is active and synchronized:
   - The SAP HANA primary master instance is running on NODE_A1.
   - The SAP HANA secondary master instance is running on NODE_B1.
   - System replication is active and in sync.

#### Test 3 - Test Procedure
{: #ha-rhel-hana-ng-mh-mz-test3-procedure}

To simulate a crash of the SAP HANA secondary master instance, send a SIGKILL signal by running the following command on NODE_B1:

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test 3 - Expected behavior
{: #ha-rhel-hana-ng-mh-mz-test3-expected-behavior}

- The SAP HANA secondary master instance on NODE_B1 crashes.
- The cluster detects the failure and marks the corresponding resource as `failed`.
- The cluster restarts the SAP HANA secondary master instance.
- System replication is reestablished and returns to a synchronized state.

#### Test 3 - Recovery procedure
{: #ha-rhel-hana-ng-mh-mz-test3-recovery-procedure}

Wait until the SAP HANA secondary master instance starts and system replication status returns to `SOK`.
Then, clean up any failed resource actions as indicated by the output of the `pcs status` command.

Run the following commands on NODE_B1:

```sh
pcs resource refresh SAPHanaController_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}

### Test 4 - Testing manual move of a SAPHana resource to another node
{: #ha-rhel-hana-ng-mh-mz-test4-manual-move}

Follow these steps to manually move a `SAPHanaController` resource to another node.

#### Test 4 - Description
{: #ha-rhel-hana-ng-mh-mz-test4-description}

Use cluster management commands to relocate the SAP HANA primary master instance to another node for maintenance purposes.

#### Test 4 - Prerequisites
{: #ha-rhel-hana-ng-mh-mz-test4-prerequisites}

- A functional five-node RHEL HA Add-On cluster is configured for SAP HANA system replication.
- The cluster is running on all nodes.
- The `SAPHanaController_${SID}_${INSTNO}` resource is configured with `AUTOMATED_REGISTER=true`.
- Verify that SAP HANA system replication is active and synchronized:
   - The SAP HANA primary master instance is running on NODE_A1.
   - The SAP HANA secondary master instance is running on NODE_B1.
   - System replication is active and in sync.

#### Test 4 - Test procedure
{: #ha-rhel-hana-ng-mh-mz-test4-procedure}

To manually move the SAP HANA primary master instance to another node, run the following command on NODE_A1:

```sh
pcs resource move SAPHanaController_${SID}_${INSTNO}-clone
```
{: pre}

#### Test 4 - Expected behavior
{: #ha-rhel-hana-ng-mh-mz-test4-expected-behavior}

- The cluster creates temporary location constraints to relocate the resource to the designated target node.
- The cluster initiates a takeover, promoting the SAP HANA secondary master instance to the primary role.
- Applications such as SAP S/4HANA and SAP NetWeaver automatically reconnect to the new SAP HANA primary master instance, and query processing continues on the nodes at SITE_B.

#### Test 4 - Recovery procedure
{: #ha-rhel-hana-ng-mh-mz-test4-recovery-procedure}

Wait until the new SAP HANA primary master instance is fully operational.
After a short delay, the cluster automatically removes the location constraints.
The cluster then registers and starts the original primary master instance as the new secondary master instance.

To verify the cluster status, run the following commands on NODE_A1:

```sh
pcs constraint
```
{: pre}

```sh
pcs status --full
```
{: pre}

### Test 5 - Testing node failure of the primary worker instance
{: #ha-rhel-hana-ng-mh-mz-test5-primary-worker-instance-failure}

Follow this procedure to simulate and test a failure of the node that hosts the SAP HANA primary worker instance.

#### Test 5 - Description
{: #ha-rhel-hana-ng-mh-mz-test5-description}

This test simulates a crash of the node that hosts the SAP HANA primary worker instance.

#### Test 5 - Prerequisites
{: #ha-rhel-hana-ng-mh-mz-test5-prerequisites}

- A functional five-node RHEL HA Add-On cluster is configured for HANA system replication.
- The cluster is running on all nodes.
- Verify that SAP HANA system replication is active and synchronized:
   - The SAP HANA primary master instance is running on NODE_B1.
   - The SAP HANA secondary master instance is running on NODE_A1.
   - System replication is active and in sync.

#### Test 5 - Test procedure
{: #ha-rhel-hana-ng-mh-mz-test5-procedure}

To simulate a node failure on `NODE_B2`, trigger an immediate shut off request by running the following command on NODE_B2:

```sh
sync; echo o > /proc/sysrq-trigger
```
{: pre}

#### Test 5 - Expected behavior
{: #ha-rhel-hana-ng-mh-mz-test5-expected-behavior}

- NODE_B2 shuts down.
- The cluster detects the node failure and marks its state as `OFFLINE`.
- The cluster promotes the SAP HANA secondary master instance on NODE_A1 to primary master.
- The virtual IP address is reassigned to NODE_A1.
- Applications such as SAP S/4HANA and SAP NetWeaver automatically reconnect to the new SAP HANA primary master instance, and query processing continues on the nodes at SITE_A.

#### Test 5 - Recovery procedure
{: #ha-rhel-hana-ng-mh-mz-test5-recovery-procedure}

Log in to the {{site.data.keyword.cloud}} console and start the virtual server instance NODE_B2.
After NODE_B2 becomes available, restart the cluster framework.

Run the following commands on NODE_B2:

```sh
pcs cluster start
```
{: pre}

```sh
pcs status --full
```
{: pre}

Because `AUTOMATED_REGISTER=true` is configured for the `SAPHanaController_${SID}_${INSTNO}` resource, SAP HANA automatically restarts when NODE_B1 rejoins the cluster.
The former primary master instance is then reregistered as a secondary master.

### Test 6 - Testing node failure of the secondary worker instance
{: #ha-rhel-hana-ng-mh-mz-test6-secondary-worker-instance-failure}

Follow these steps to simulate a failure of the SAP HANA secondary worker instance.

#### Test 6 - Description
{: #ha-rhel-hana-ng-mh-mz-test6-description}

This test simulates a crash of the SAP HANA secondary worker instance to validate cluster failover behavior.

#### Test 6 - Prerequisites
{: #ha-rhel-hana-ng-mh-mz-test6-prerequisites}

- A functional five-node RHEL HA Add-On cluster is configured for HANA system replication.
- The cluster is running on all nodes.
- The `SAPHanaController_${SID}_${INSTNO}` resource is configured with `AUTOMATED_REGISTER=true`.
- Verify that SAP HANA system replication is active and synchronized:
   - The SAP HANA primary master instance is running on NODE_A1.
   - The SAP HANA secondary master instance is running on NODE_B1.
   - System replication is active and in sync.

#### Test 6 - Test Procedure
{: #ha-rhel-hana-ng-mh-mz-test6-procedure}

To simulate a failure of NODE_B2, trigger an immediate shut off request by running the following command on NODE_B2.

```sh
sync; echo o > /proc/sysrq-trigger
```
{: pre}

#### Test 6 - Expected behavior
{: #ha-rhel-hana-ng-mh-mz-test6-expected-behavior}

- NODE_B2 shuts down.
- The cluster detects the node failure and marks its state as `OFFLINE`.
- The SAP HANA secondary master NODE_B1 is marked as `UNDEFINED`.
   The SAP HANA processes `hdbcompileserver` and `hdbpreprocessor` are stopped.
- The SAP HANA primary master instance on NODE_A1 remains active.
- SAP HANA System Replication cannot connect to the secondary site.
- Applications such as SAP S/4HANA and SAP NetWeaver remain connected to the SAP HANA primary master instance, and query processing continues on the nodes at SITE_A.

#### Test 6 - Recovery procedure
{: #ha-rhel-hana-ng-mh-mz-test6-recovery-procedure}

Log in to the {{site.data.keyword.cloud}} console and start the virtual server instance NODE_B2.
After NODE_B2 becomes available, restart the cluster framework.

Run the following commands on NODE_B2:

```sh
pcs cluster start
```
{: pre}

Because `AUTOMATED_REGISTER=true` is configured for the `SAPHanaController_${SID}_${INSTNO}` resource, SAP HANA automatically restarts when NODE_B2 rejoins the cluster.
After the secondary worker instance is running again, the SAP HANA secondary master instance is automatically reregistered with the primary master instance.
System replication becomes active and synchronized.

To verify the cluster status, run the following command on NODE_A1:

```sh
pcs status --full
```
{: pre}
