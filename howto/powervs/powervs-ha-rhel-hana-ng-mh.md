---
copyright:
  years: 2025
lastupdated: "2026-01-23"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, SAP HANA scale-out, High Availability, HA, Linux, Pacemaker, RHEL HA Add-On
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Configuring SAP HANA multiple-host system replication in a Red Hat Enterprise Linux (RHEL) High Availability (HA) Add-On cluster with the `sap-hana-ha` resource agent
{: #ha-rhel-hana-ng-mh}

This document provides information on how to configure a Red Hat Enterprise Linux (RHEL) High Availability (HA) Add-On cluster to manage **SAP HANA Multiple-Host System Replication**.
The cluster uses {{site.data.keyword.powerSysFull}} instances as its nodes.
{: shortdesc}

The configuration represents a performance-optimized scenario on a RHEL HA Add-On cluster.
It does not include an SAP HANA-controlled host auto-failover configuration, as high availability is managed by the RHEL HA cluster.

This information is intended for system architects and specialists who are planning a high-availability deployment of SAP HANA on {{site.data.keyword.powerSys_notm}}.
{: note}

The content outlines the steps to implement the scenario by first integrating SAP HANA with a RHEL High Availability (HA) Add-On cluster.
The configuration is then validated through failover testing.

## Before you begin
{: #ha-rhel-hana-ng-mh-begin}

Review the general requirements, product documentation, support articles, and SAP Notes listed in [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs) and [Deploying SAP HANA Scale-Out System Replication High Availability](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html-single/deploying_sap_hana_scale-out_system_replication_high_availability/index){: external}.

## Prerequisites
{: #ha-rhel-hana-ng-mh-prerequisites}

- Ensure that you have a valid *RHEL for SAP Applications* or *RHEL for SAP Solutions* subscription to access the required repositories for installing SAP HANA and the HA resource agents.
- Deploy RHEL on at least five virtual server instances (VSIs) in {{site.data.keyword.powerSys_notm}}.
   The setup can be extended to *2n + 1* nodes, where *n* is the number of SAP HANA nodes on one system replication site.

   Ensure that the virtual server instances meet the hardware and resource requirements for SAP HANA.
   Refer to [Planning your deployment](/docs/sap?topic=sap-powervs-planning-items) for guidance.

   The majoritymaker host must be a dedicated virtual instance with no other workload.
   A minimal configuration of 2 vCPUs and a 50 GB root partition is sufficient.

- Ensure that the hostnames of the virtual server instances comply with SAP HANA naming conventions.
- Install two SAP HANA systems, each distributed across two virtual server instances.
- Configure SAP HANA system replication between the two systems, following the procedures outlined in:
    - [Configuring the SAP HANA system replication on RHEL](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html-single/deploying_sap_hana_scale-out_system_replication_high_availability/index#asmb_configuring_sr_v9-deploying-scale-out-system-replication){: external}
    - [General Prerequisites for Configuring SAP HANA System Replication](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/86267e1ed56940bb8e4a45557cee0e43.html){: external}
- Disable the SAP HANA instance autostart on all SAP nodes as described in [Disabling SAP HANA instance autostart](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html-single/deploying_sap_hana_scale-out_system_replication_high_availability/index#proc_disabling_autostart_v9-deploying-scale-out-system-replication){: external}
- Adjust the SAP HANA nameserver configuration according to [Adjusting the SAP HANA nameserver configuration](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html-single/deploying_sap_hana_scale-out_system_replication_high_availability/index#proc_adjusting_v9-deploying-scale-out-system-replication){: external}

   System replication setup is platform-independent and applies to {{site.data.keyword.powerSys_notm}}.
   Use shared NFS storage as described in the referenced guides.

   Configure hostname resolution in the global.ini file.
   Add the [system_replication_hostname_resolution] section and specify each SAP HANA node with its corresponding IP address.
   For more information, see [Host Name Resolution for System Replication](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/c0cba1cb2ba34ec89f45b48b2157ec7b.html){: external}.

## Configuring SAP HANA system replication in a RHEL HA Add-On cluster on IBM {{site.data.keyword.powerSys_notm}}
{: #ha-rhel-hana-ng-mh-configure-sr}

The instructions are derived from Red Hat product documentation, Red Hat Knowledgebase articles, and use custom hook scripts from the `sap-hana-ha` resource agents.
For reference information, see [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

### Deploying local and shared Network File System (NFS) storage on SAP HANA nodes
{: #ha-rhel-hana-ng-mh-nfs-storage}

To deploy local and shared Network File System (NFS) storage on SAP HANA nodes, start by preparing the local storage for the data and log volumes.
Use storage area network (SAN) block storage volumes for `/hana/data` and `/hana/log`.
These volumes must not be shared between nodes.
Attach and configure the SAN block storage volumes by following the instructions in [Creating extra storage volumes for SAP HANA Power Virtual Server](/docs/sap?topic=sap-powervs-set-up-power-instances#powervs-set-up-power-hana-volumes).
For optimal performance, stripe the file systems across four volumes.

Refer to [General storage guidelines for SAP HANA on IBM Power Virtual Server](/docs/sap?topic=sap-storage-design-considerations#powervs-general-recommendation-storage) for volume sizing recommendations.
Ensure that the file systems `/hana/data/<SID>` and `/hana/log/<SID>` are mounted during the operating system startup on each virtual server instance.

An SAP HANA multiple-host installation requires that the shared storage for the `hana/shared` directory is accessible from all SAP HANA nodes.
Each site requires its own NFS mount target.
Provision two NFS shares on the NFS service, one for each site.

To set up a HA NFS service, including NFS targets, {{site.data.keyword.loadbalancer_full}}, {{site.data.keyword.tg_full}}, and client-side NFS access, follow the instructions in [Accessing VPC zonal file storage shares from IBM Power Virtual Server Instances](/docs/sap?topic=sap-nfs-zonal-powervs-intro).
The `hana/shared` file system must be mounted from an external NFS server during the operating system startup on each virtual server instance.

To mount the NFS share on SAP HANA nodes at SITE_A, add the following line to `/etc/fstab`:

```sh
<IP-of-NFS-service>:/nfs_shared_site1 /hana/shared nfs vers=4,rw,sec=sys,hard,rsize=65536,wsize=65536,namlen=255
```
{: screen}

On the SAP HANA nodes at SITE_B, add the following line to `etc/fstab`:

```sh
<IP of NFS-service>:/nfs_shared_site2 /hana/shared nfs vers=4,rw,sec=sys,hard,rsize=65536,wsize=65536,namlen=255
```
{: screen}

Each site must mount its own NFS mount target to `/hana/shared` to ensure proper isolation and replication.

Before you start the SAP HANA installation, verify that the `/hana/shared` directory is mounted on all SAP HANA nodes by using the appropriate NFS mount target for each site.

```sh
mount /hana/shared
```
{: pre}

Install an SAP HANA scale-out system independently on each site.
Configure SAP HANA System Replication to replicate data between the two sites and ensure high availability.

### Deploying subnets on the SAP HANA nodes
{: #ha-rhel-hana-ng-mh-network}

This document describes an implementation of a simple network topology with a single subnet per workspace.
Attach all virtual server instances (VSIs) in the workspace to this subnet.
If you plan to use three or more subnets in a production environment, be aware that this configuration requires extra parameters for certain setup options.

### Preparing environment variables
{: #ha-rhel-hana-ng-mh-prepare-environment-variables}

To simplify the setup, define the following environment variables for the `root` user on both nodes.
These variables are used in later configuration steps.

Set the following environment variables on both nodes.

```sh
# General settings
export SID=<SID>            # SAP HANA System ID (uppercase)
export sid=<sid>            # SAP HANA System ID (lowercase)
export INSTNO=<INSTNO>      # SAP HANA instance number

# Cluster nodes in Site_A, HANA System A
export SITE_A="Site1"          # HANA system replication site name
export NODE_A1=<HOSTNAME_A1>   # Virtual server instance hostname
export NODE_A2=<HOSTNAME_A2>   # Virtual server instance hostname

# Cluster nodes in Site B, HANA System B
export SITE_B="Site2"          # HANA system replication site name
export NODE_B1=<HOSTNAME_B1>   # Virtual server instance hostname
export NODE_B2=<HOSTNAME_B2>   # Virtual server instance hostname

# Cluster node quorum host inside the cluster
export NODE_MAJORITYMAKER=<HOSTNAME_MAJORITYMAKER>   # Virtual server instance hostname

# Single zone
export VIP=<IP address>     # SAP HANA system replication cluster virtual IP address
export CLOUD_REGION=<CLOUD_REGION>       # Singlezone region name
export APIKEY="APIKEY or path to file"   # API Key of the IBM Cloud IAM ServiceID for the resource agent
export IBMCLOUD_CRN_1=<IBMCLOUD_CRN_1>   # Workspace 1 CRN
export GUID_1=<GUID_1>                   # Workspace GUID
export PROXY_IP=<IP_ADDRESS>             # Proxy server IP address
export POWERVSI_A1=<POWERVSI_A1>         # Virtual server instance A1 id
export POWERVSI_A2=<POWERVSI_A2>         # Virtual server instance A2 id
export POWERVSI_B1=<POWERVSI_B1>         # Virtual server instance B1 id
export POWERVSI_B2=<POWERVSI_B2>         # Virtual server instance B2 id
```
{: codeblock}

Set the environment variables `CLOUD_REGION`, `APIKEY`, `IBMCLOUD_CRN_`, `POWERVSI_` as described in [Collecting parameters for configuring a high availability cluster](/docs/sap?topic=sap-ha-vsi#ha-vsi-create-service-api-key) section.
The `VIP` variable must be set to a virtual IP address resource that belongs to the subnet's `CIDR` range.

Refer to [Reserving virtual IP addresses](/docs/sap?topic=sap-ha-vsi#ha-vsi-reserve-virtual-ip-addresses) to reserve a virtual IP address for the SAP HANA system replication cluster.
Then, set the `VIP` environment variable to the reserved IP address.

The following export commands are an example of how to set the required environment variables.

```sh
export CLOUD_REGION="eu-de"
export IBMCLOUD_CRN_1="crn:v1:bluemix:public:power-iaas:eu-de-1:a/0f4bdc805124434c83b6235752a0b500:b278ea15-0d06-4659-aa13-3516855cd811::"
export GUID_1="b278ea15-0d06-4659-aa13-3516855cd811"
export POWERVSI_A1="41248269-4d28-4c0a-84e5-bc50ec6b82d7"
export POWERVSI_A2="c49a4a74-11b7-4843-962b-3c3f384f04aa"
export POWERVSI_B1="3415ea83-bd2b-45cb-a67a-4d3995e55f9b"
export POWERVSI_B2="c4e4fb61-bd62-41b6-b2f5-5c13ab3b578a"
export POWERVSI_MAJORITYMAKER="ce00f3cf-323b-410a-86b4-08a21140509d"
...
export APIKEY="@/root/.apikey.json"
export API_TYPE="private"
export VIP="10.53.0.140"
```
{: codeblock}

### Installing the RHEL HA Add-On software packages
{: #ha-rhel-hana-ng-mh-install-rhel-ha-packages}

Install the required software packages on all cluster nodes, including the majoritymaker node, by running the following commands.

```sh
dnf install -y pcs pacemaker fence-agents-ibm-powervs
```
{: pre}

If the required packages are not available on a node, enable the high-availability software repository.

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
{: #ha-rhel-hana-ng-mh-install-sap-hana-resource-agents}

Install the required software packages on all cluster nodes, including the majoritymaker host.

The RHEL package `sap-hana-ha` includes three resource agents.
- The *SAPHanaTopology* resource agent collects status and configuration data for SAP HANA system replication on each node.
   It also starts and monitors the local *SAP Host Agent*, which is required to start, stop, and monitor SAP HANA instances.
- The *SAPHanaFilesystem* resource agent verifies access to an SAP HANA file system.
   This resource agent is optional.
   If the SAP HANA primary node experiences a storage failure, the agent fences the node to enable faster failover.
- The *SAPHanaController* resource agent manages SAP HANA instances in both single-host (scale-up) and multi-host (scale-out) configurations.
   If the SAP HANA primary fails, the agent initiates a takeover to the secondary system.

Run the following command on all cluster nodes to install the RHEL HA Add-On package `sap-hana-ha`.

```sh
dnf install -y sap-hana-ha
```
{: pre}

If the package is not available on a node, enable the software repository.

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
{: #ha-rhel-hana-ng-mh-start-hana-systems}

Start SAP HANA and verify that system replication is active.
For details, see [2.4. Checking SAP HANA System Replication state](https://access.redhat.com/articles/3004101#checking-sap-hana-system-replication-state){: external}.

Run the following commands on all SAP HANA nodes.

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
{: #ha-rhel-hana-ng-mh-enable-hooks}

SAP HANA configuration changes are required to integrate with the Linux cluster.

The integration steps include:
1. Stop the HANA system.
1. Configure the HANA system replication operation mode.
1. Implement HanaSR.py for the srConnectionChanged hook.
1. Implement ChkSrv.py for the srServiceStateChanged hook.
1. Grant sudo access to update cluster attributes to the *sidadm* user.
1. Start the HANA system.

Use all hook scripts directly from the `sap-hana-ha` package.
Moving or copying these scripts can prevent future package updates from working correctly.
{: note}

### Stopping SAP HANA
{: #ha-rhel-hana-ng-mh-stop-SAP-HANA}

Stop both SAP HANA systems before you integrate them into the Red Hat HA Add-On cluster.
On each master node, run the following commands:

```sh
sudo -i -u ${sid}adm -- sapcontrol -nr ${INSTNO} -function StopSystem
```
{: pre}

```sh
sudo -i -u ${sid}adm -- sapcontrol -nr ${INSTNO} -function WaitforStopped 300 20
```
{: pre}

```sh
sudo -i -u ${sid}adm -- sapcontrol -nr ${INSTNO} -function GetSystemInstanceList
```
{: pre}

### Enabling the SAP HANA hook scripts
{: #ha-rhel-hana-ng-mh-enable-hana-sr-hook}

SAP HANA provides *hooks* that trigger notifications for specific events.
For details, see [Implementing a HA/DR Provider](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/1367c8fdefaa4808a7485b09815ae0f3.html){: external}.

The *srConnectionChanged()* hook enhances detection of SAP HANA system replication status changes that require cluster intervention.
Its purpose is to prevent data loss and corruption by avoiding accidental takeovers.

SAP HANA includes built-in functions to monitor the `indexserver` process.
If an issue occurs, SAP HANA attempts automatic recovery by stopping and restarting the process.
To complete this recovery or clean up after a crash, the Linux kernel must release all memory that is allocated to the process, which can take considerable time on large databases.
During cleanup, SAP HANA remains operational and continues to accept client requests, potentially causing system replication to fall out of sync.
If another error occurs before the indexserver restart completes, data consistency can be compromised.
The *srServiceStateChanged()* hook mitigates this risk by stopping the entire SAP HANA instance, enabling faster recovery.

#### Configuring the `HanaSR` hook for all SAP HANA instances
{: #ha-rhel-hana-ng-mh-activate-hana-sr-hook}

The *sap-hana-ha* package includes the `HanaSR.py` hook script, which is located in the `/usr/share/sap-hana-ha` directory.
Configure this hook on all SAP HANA cluster nodes.

1. Verify that the hook script `/usr/share/sap-hana-ha/HanaSR.py` is present on all cluster nodes.
1. Update the `global.ini` file on each SAP HANA master node to enable the `HanaSR` hook.

   Run the following command on all master nodes.

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

1. Verify the updated configuration file.

   Run the following command on all master nodes.

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
{: #ha-rhel-hana-ng-mh-enable-hana-servicestatechanged-hook}

The *sap-hana-ha* package includes the `ChkSrv.py` hook script, which is located in the `/usr/share/sap-hana-ha` directory.
Configure this hook on all cluster master nodes.
Ensure that the script is available on all cluster nodes, including the NODE_MAJORITYMAKER.

The script `/usr/share/sap-hana-ha/ChkSrv.py` stops the entire SAP HANA instance to enable faster recovery.
If `automated failover` is enabled and the secondary node is healthy, the cluster initiates a takeover.
Otherwise, a forced restart of the instance on the local node results in a fast recovery.

The hook script analyzes instance events, applies filters to event details, and triggers actions based on the results.
It distinguishes between an `indexserver` process that is stopped and restarted by SAP HANA and one that is stopped during a planned instance shutdown.

The behavior of the hook depends on the configuration of the `action_on_lost` parameter.

Ignore
:   Logs the event and decision details without taking further action.

Stop
:  Gracefully stops the SAP HANA instance by using the sapcontrol command.

Kill
:   Forcefully stops the instance by using the `HDB kill-<signal>` command (default: signal 9).
    The signal value is configurable.

Both the *Stop* and the *Kill* actions result in a stopped SAP HANA instance.
The *kill* action is typically faster.

1. Verify that the hook script `/usr/share/sap-hana-ha/ChkSrv.py` is present on all cluster nodes.
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

1. Verify that the file `/hana/shared/${SID}/global/hdb/custom/config/global.ini` contains the following configuration entries:

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
   - `stop_timeout`: Specifies the timeout for a graceful stop (default: 20 seconds).
   - `kill_signal`: Defines the signal that is used for forced termination (default: 9).

#### Verifying all SAP HANA hooks
{: #ha-rhel-hana-ng-mh-verify-all-hooks}

Verify that the file `/hana/shared/${SID}/global/hdb/custom/config/global.ini` contains the following entries after you configured all SAP HANA hooks:

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
{: #ha-rhel-hana-ng-mh-sudo-settings}

Create sudo settings for the SAP HANA operating system administrator user.
To enable the `${sid}adm` user to update node attributes during execution of the *srConnectionChanged()* hook, configure the following sudo settings.
Run the following commands on all SAP HANA nodes

Create a sudoers file with the required command specifications for `${sid}adm`.

   ```sh
   cat >> /etc/sudoers.d/20-saphana << EOT
   Defaults:${sid}adm !requiretty
   ${sid}adm  ALL=(ALL) NOPASSWD: /usr/sbin/crm_attribute -n hana_*
   EOT
   ```
   {: pre}

   Set the appropriate ownership and permissions for the sudoers file, and verify its contents.

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
{: #ha-rhel-hana-ng-mh-start-SAP-HANA}

Start the SAP HANA system on both HANA master nodes.

```sh
sudo -i -u ${sid}adm -- sapcontrol -nr ${INSTNO} -function StartSystem
```
{: pre}

Wait until the `dispstatus` changes to `GREEN`, confirming that the SAP HANA system is running.

### Verifying trace messages from HA/DR hooks
{: #ha-rhel-hana-ng-mh-check-hooks}

The HA/DR hooks are activated when the SAP HANA system restarts.
Verify that the hooks logged some trace messages.

Run the following command on both SAP HANA master nodes.

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
{: #ha-rhel-hana-ng-mh-ha-cluster}

### Adding cluster node entries to the hosts file
{: #ha-rhel-hana-ng-mh-hosts-file}

On each cluster node, add the IP addresses and hostnames of all participating nodes to the `/etc/hosts` file.
For detailed guidance, see [Setting up /etc/hosts files on RHEL cluster nodes](https://access.redhat.com/solutions/81123){: external}.

### Configuring firewall services
{: #ha-rhel-hana-ng-mh-firewall}

If `firewalld.service` is installed and enabled, add the high-availability service to the firewall.
Temporarily disable the firewall while you set up SAP HANA System Replication, and re-enable it after the configuration completes successfully.
The following example applies to an SAP HANA database with a single tenant.
For systems with multiple tenants, more firewall rules are required.

Run the following commands on all cluster nodes.

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

If your SAP HANA system includes multiple tenant databases, more ports must be added to the firewall rules.
Tenant databases do not use fixed port assignments.
For guidance, refer to [Port Assignment in Tenant Databases](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/440f6efe693d4b82ade2d8b182eb1efb.html?locale=en-US&version=LATEST){: external}.
Port ranges for extra tenants are documented in [Connections for Distributed SAP HANA Systems](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/82cea8fe69604f3ab0d4624248b6e523.html?locale=en-US&version=LATEST){: external}.

The following port ranges are commonly used by tenant databases and their associated services:

```sh
4xx40 - 4xx97	indexserver	Log and data shipping (tenant databases)
4xx40 - 4xx97	scriptserver	Log and data shipping (optional, tenant databases)
4xx40 - 4xx97	docstore		Log and data shipping (optional, tenant databases)

3xx04			scriptserver	Port used by the script server of the first tenant database
3xx05			diserver		Tenant database ports
3xx25			diserver		Port used by the diserver of the initial tenant database
3xx4n			diserver		Tenant database ports
3xx06			webdispatcher	Port used by the webdispatcher of the initial tenant database
3xx07			xsengine	Port used by the xsengine of the initial tenant database
3xx08			xsengine	Port used by the xsengine of the initial tenant database created in a new installation or an upgraded single-container system.
3xx42 - 3xx99	xsengine	Tenant database ports
3xx11			dpserver	Port used by the dpserver of the initial tenant database
```
{: screen}

#### Starting the pcsd service
{: #ha-rhel-hana-ng-mh-start-pcsd-service}

Enable and start the *pcsd* service (Pacemaker/Corosync configuration daemon).
This service is used by the *pcs* command line tool to configure and manage cluster nodes.

Run the following command on each cluster node.

```sh
systemctl enable --now pcsd.service
```
{: pre}

Verify that the PCS service is running.

```sh
systemctl status pcsd.service
```
{: pre}

#### Setting a password for the hacluster user
{: #ha-rhel-hana-ng-mh-set-hacluster-password}

Set the same password for the hacluster user on each cluster node by running the following command.

```sh
passwd hacluster
```
{: pre}

#### Authenticating the cluster nodes
{: #ha-rhel-hana-ng-mh-authenticate-cluster-nodes}

Use the following command to authenticate the hacluster user to the PCS daemon on the cluster nodes.
Enter the password set in the previous step when prompted.

Run the following command on NODE_A1.

```sh
pcs host auth ${NODE_A1} ${NODE_A2} ${NODE_B1} ${NODE_B2} ${NODE_MAJORITYMAKER} -u hacluster
```
{: pre}

Proceed only if all nodes return `Authorized`.

#### Configuring and starting the cluster nodes
{: #ha-rhel-hana-ng-mh-configure-and-start-cluster-nodes}

Run the following command on NODE_A1 to configure the cluster and synchronize its configuration across the specified nodes.

```sh
pcs cluster setup ${CLUSTERNAME} ${NODE_A1} ${NODE_A2} ${NODE_B1} ${NODE_B2} ${NODE_MAJORITYMAKER}
```
{: pre}

In this setup, node names are used to resolve IP addresses.

Alternatively, you can define IP addresses explicitly.
A cluster setup, which spans multiple subnets, might look like the following.

```sh
pcs cluster setup ${CLUSTERNAME} \
  ${NODE_A1} addr=<IP_ETH1> addr=<IP_ETH2> \
  ${NODE_A2} addr=<IP_ETH1> addr=<IP_ETH2> \
  ${NODE_B1} addr=<IP_ETH1> addr=<IP_ETH2> \
  ${NODE_B2} addr=<IP_ETH1> addr=<IP_ETH2> \
  ${NODE_MAJORITYMAKER} addr=<IP_ETH1> addr=<IP_ETH2>
```
{: screen}

Start the cluster on all nodes by running the following command on a cluster node.

```sh
pcs cluster start --all
```
{: pre}

Optionally, you can run the command `pcs cluster enable --all` to enable the cluster to start automatically on system boot across all cluster nodes.
This command ensures the Corosync and Pacemaker services start automatically, which can be useful in environments that prefer automatic cluster reintegration.
Use this option only if it aligns with your operational requirements.
{: note}

Run the following command to check the cluster status.

```sh
pcs cluster status
```
{: pre}

#### Creating the fencing device
{: #ha-rhel-hana-ng-mh-create-fencing}

*Shoot The Other Node In The Head (STONITH)* is a fencing mechanism that prevents data corruption in split-brain scenarios.

You must enable STONITH (fencing) for a RHEL HA Add-On production cluster.
{: important}

The *fence_ibm_powervs* agent is the only supported STONITH fencing agent for clusters that run on {{site.data.keyword.powerSys_notm}}.

You must configure a fencing device for each of the two HANA System Replication sites.
The fence agent connects to the [Power Cloud API](https://cloud.ibm.com/apidocs/power-cloud){: external} by using the common `APIKEY` and `CLOUD_REGION` parameters.
The parameters `IBMCLOUD_CRN_<n>`, `GUID_<n>`, and the instance ID `POWERVSI_<n>` are specific to the workspace.
You can test the fence agent by using the parameters that you collected in the [Collecting parameters for configuring a high availability cluster](/docs/sap?topic=sap-ha-vsi#ha-rhel-collect-parameters-for-cluster-config) section.

#### Identifying the virtual server instances for fencing
{: #ha-rhel-hana-ng-mh-identify-virtual-instances-for-fencing}

Use the *list* option of the *fence_ibm_powervs* agent to identify or verify the instance IDs of your cluster nodes.

On any node, run the following command.

```sh
fence_ibm_powervs \
    --token=${APIKEY} \
    --crn=${IBMCLOUD_CRN_1} \
    --instance=${GUID_1} \
    --region=${CLOUD_REGION} \
    --api-type=public \
    -o list
```
{: pre}

If the virtual server instances are accessible only through a private network, use the `--api-type=private` option.
This option also requires specifying a proxy by using the `--proxy` option.

Example command:

```sh
fence_ibm_powervs \
    --token=${APIKEY} \
    --crn=${IBMCLOUD_CRN_1} \
    --instance=${GUID_1} \
    --region=${CLOUD_REGION} \
    --api-type=private \
    --proxy=http://${PROXY_IP}:3128 \
    -o list
```
{: pre}

The following examples use the `--api-type=private` option.

#### Checking the status of both virtual server instances
{: #ha-rhel-hana-ng-mh-check-virtual-instances-status}

Run the following commands on both SAP master nodes.

```sh
time fence_ibm_powervs \
    --token=${APIKEY} \
    --crn=${IBMCLOUD_CRN_1} \
    --instance=${GUID_1} \
    --region=${CLOUD_REGION} \
    --plug=${POWERVSI_A1} \
    --api-type=private \
    --proxy=http://${PROXY_IP}:3128 \
    -o status
```
{: pre}

```sh
time fence_ibm_powervs \
    --token=${APIKEY} \
    --crn=${IBMCLOUD_CRN_1} \
    --instance=${GUID_1} \
    --region=${CLOUD_REGION} \
    --plug=${POWERVSI_B1} \
    --api-type=private \
    --proxy=http://${PROXY_IP}:3128 \
    -o status
```
{: pre}

- Use the status action of the fencing agent with `--plug=<POWERVSI_n>` to display the power status of the specified virtual server instance.
- Both commands must return `Status: ON` for each node.
- The output from the time command can be useful later when you configure timeout values for the STONITH device.
- For more detailed output, add the -v flag to display additional information about the connection to the Power Cloud API and the power status query.



#### Creating the STONITH devices
{: #ha-rhel-hana-ng-mh-create-stonith-device}

Use the following command to display the device-specific options for the *fence_ibm_powervs* fencing agent.

```sh
pcs stonith describe fence_ibm_powervs
```
{: pre}

Create a STONITH device for all virtual server instances in the cluster.

On NODE_A1, run the following commands.

```sh
pcs stonith create fence_device_1 fence_ibm_powervs \
    token=${APIKEY} \
    crn=${IBMCLOUD_CRN_1} \
    instance=${GUID_1} \
    region=${CLOUD_REGION} \
    api_type=private \
    proxy=http://${PROXY_IP}:3128 \
    pcmk_host_map="${NODE_A1}:${POWERVSI_A1};${NODE_A2}:${POWERVSI_A2};${NODE_B1}:${POWERVSI_B1};${NODE_B2}:${POWERVSI_B2};${NODE_MAJORITYMAKER}:${POWERVSI_MAJORITYMAKER}"  \
    pcmk_reboot_timeout=600 \
    pcmk_monitor_timeout=600 \
    pcmk_status_timeout=60
```
{: pre}

Although the `fence_ibm_powervs` agent uses the `api-type` option when run from the shell, the STONITH resource must be created that uses `api_type`.
{: important}

Verify the STONITH configuration by using the following commands.

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
{: #ha-rhel-hana-ng-mh-set-stonith-action-cluster-property}

To reduce failover time in an IBM {{site.data.keyword.powerSys_notm}} cluster, set the cluster property *stonith-action* to *off*.
This causes the cluster to use a *power off* operation instead of a *reboot* when fencing an instance.

After the change is applied, you must restart each fenced instance manually by using the IBM Cloud Console.

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
{: #ha-rhel-hana-ng-mh-maintenance-mode}

Put the cluster in maintenance mode to prevent unintended actions while you configure the cluster resources.

```sh
pcs property set maintenance-mode=true
```
{: pre}

### Configuring general cluster properties
{: #ha-rhel-hana-ng-mh-configure-cluster-properties}

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
{: #ha-rhel-hana-ng-mh-configure-systemd-SAP}

On RHEL 9, SAP HANA 2.0 SPS07 revision 70 and later uses systemd integration by default.
In high-availability environments, extra configuration is required to ensure proper integration of the various systemd services involved in the cluster operations.

Configure the pacemaker systemd service to manage the HANA instance systemd service in the correct order on all cluster nodes that run SAP HANA instances.

Create the directory /etc/systemd/system/pacemaker.service.d/ for the pacemaker service drop-in file.

```sh
mkdir /etc/systemd/system/pacemaker.service.d/
```
{: pre}

Create the systemd drop-in file for the pacemaker service with the following content:

```sh
cat << EOF > /etc/systemd/system/pacemaker.service.d/00-pacemaker.conf
[Unit]
Description=Pacemaker needs the SAP HANA instance service
Wants=SAP${SID}_${INSTNO}.service
After=SAP${SID}_${INSTNO}.service
EOF
```
{: pre}

### Creating a cloned SAPHanaTopology resource
{: #ha-rhel-hana-ng-mh-create-saphanatopology-resource}

Run the following command on NODE_A1 to create the *SAPHanaTopology* resource.

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
{: #ha-rhel-hana-ng-mh-create-saphanafilesystem-resource}

The *SAPHanaFilesystem* resource monitors the file systems that are used by the SAP HANA system.

Run the following command on NODE_A1 to create the *SAPHanaFilesystem* resource.

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
{: #ha-rhel-hana-ng-mh-create-saphana-resource}

The *SAPHanaController* resource manages the four SAP HANA instances that are configured as HANA system replication nodes.

Run the following command on NODE_A1 to create the *SAPHanaController* resource.

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

### Creating a virtual IP address cluster resource in a single zone environment
{: #ha-rhel-hana-ng-mh-sz-create-virtual-ip-resource}

Use the reserved IP address to create a virtual IP address cluster resource.
This IP address enables access to the SAP HANA system replication primary instance.

Run the following command on NODE_A1 to create the virtual IP address cluster resource.

```sh
pcs resource create vip_${SID}_${INSTNO} IPaddr2 \
    ip=$VIP \
    --disabled
```
{: pre}

Verify the virtual IP address cluster resource configuration and check the cluster status.

```sh
pcs resource config vip_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}

Sample output:

```sh
# pcs resource config vip_H01_00
Resource: vip_H01_00 (class=ocf provider=heartbeat type=IPaddr2)
  Attributes: vip_H01_00-instance_attributes
    ip=10.53.0.140
  Operations:
    monitor: vip_H01_00-monitor-interval-10s
      interval=10s timeout=20s
    start: vip_H01_00-start-interval-0s
      interval=0s timeout=20s
    stop: vip_H01_00-stop-interval-0s
      interval=0s timeout=20s
```
{: screen}

### Creating cluster resource constraints
{: #ha-rhel-hana-ng-mh-create-constraints}

Ensure that the *SAPHanaTopology* resources start before the *SAPHana* resources.

The virtual IP address must be assigned to the node that hosts the primary *SAPHanaController* resource.

1. Create a resource constraint to start *SAPHanaTopology* before *SAPHanaController*.
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
   This constraint ensures that the virtual IP address resource is colocated with the SAPHanaController resource when it is promoted as primary.

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

   Sample output:

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

1. Create a location constraint to prevent any SAP HANA resources from running on the majoritymaker node.
   This constraint also prevents the cluster from checking the initial resource status on that node.
   The constraint definition uses a regexp expression to match all HANA resources.
   Adjust the pattern if your resource names differ.

   ```sh
   pcs constraint location add avoid-${NODE_MAJORITYMAKER} \
     regexp%.*SAPHana.* ${NODE_MAJORITYMAKER}  -- -INFINITY resource-discovery=never
   ```
   {: pre}

   Verify the constraint configuration.

   ```sh
   pcs constraint location --full
   ```
   {: pre}

   Adding the new node increases the vote count, which results in an odd number of votes.
   This configuration prevents an equal vote split during quorum decisions.

### Removing the cluster from maintenance mode
{: #ha-rhel-hana-ng-mh-end-maintenance-mode}

Remove the cluster from maintenance mode to resume normal resource operations.

```sh
pcs property set maintenance-mode=false
```
{: pre}

### Managing quorum in cluster systems
{: #ha-rhel-hana-ng-mh-quorum}

To maintain cluster integrity and availability, cluster systems use a concept that is called quorum to prevent data corruption and loss.
Quorum is established through a voting system.
When a cluster node becomes unresponsive or loses communication with the rest of the cluster, a majority of functioning nodes can vote to isolate and, if necessary, fence the affected node.
Pacemaker’s quorum features help prevent split-brain scenarios, where disconnected parts of the cluster continue operating independently.
A split-brain scenario can lead to conflicting writes and potential data corruption or loss.

A Red Hat Enterprise Linux High Availability Add-On cluster uses the *votequorum* service, together with fencing, to prevent split-brain scenarios.
Each system in the cluster contributes a defined number of votes.
Cluster operations proceed only when a majority of votes is available.
In this configuration, quorum is not managed through a separate mechanism.
Instead, decision-making relies on an uneven number of votes across cluster nodes, ensuring a clear majority and avoiding ambiguity during failure scenarios.

For more details, refer to [Quorum in cluster systems](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/configuring_and_managing_high_availability_clusters/assembly_overview-of-high-availability-configuring-and-managing-high-availability-clusters#quorum){: external}.

Verify the quorum status.

```sh
pcs quorum status

```
{: pre}

### Enabling the cluster resources
{: #ha-rhel-hana-ng-mh-enable-resources}

All cluster resources are initially created with the `--disabled` flag to prevent automatic startup during configuration.

To enable the resources, run the following commands on NODE_A

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
Cluster name: PACEMAKER1
Cluster Summary:
  * Stack: corosync (Pacemaker is running)
  * Current DC: h03 (5) (version 2.1.7-5.2.el9_4-0f7f88312) - partition with quorum
  * Last updated: Fri Oct 10 09:49:32 2025 on h01a1
  * Last change:  Fri Oct 10 09:49:20 2025 by root via root on h01a1
  * 5 nodes configured
  * 15 resource instances configured

Node List:
  * Node h01a1 (1): online, feature set 3.19.0
  * Node h01a2 (2): online, feature set 3.19.0
  * Node h02b1 (3): online, feature set 3.19.0
  * Node h02b2 (4): online, feature set 3.19.0
  * Node h03 (5): online, feature set 3.19.0

Full List of Resources:
  * fence_device_1	(stonith:fence_ibm_powervs):	 Started h03
  * Clone Set: SAPHanaTopology_H01_00-clone [SAPHanaTopology_H01_00]:
    * SAPHanaTopology_H01_00	(ocf:heartbeat:SAPHanaTopology):	 Started h01a1
    * SAPHanaTopology_H01_00	(ocf:heartbeat:SAPHanaTopology):	 Started h01a2
    * SAPHanaTopology_H01_00	(ocf:heartbeat:SAPHanaTopology):	 Started h02b1
    * SAPHanaTopology_H01_00	(ocf:heartbeat:SAPHanaTopology):	 Started h02b2
  * Clone Set: SAPHanaFilesystem_H01_00-clone [SAPHanaFilesystem_H01_00]:
    * SAPHanaFilesystem_H01_00	(ocf:heartbeat:SAPHanaFilesystem):	 Started h01a1
    * SAPHanaFilesystem_H01_00	(ocf:heartbeat:SAPHanaFilesystem):	 Started h01a2
    * SAPHanaFilesystem_H01_00	(ocf:heartbeat:SAPHanaFilesystem):	 Started h02b1
    * SAPHanaFilesystem_H01_00	(ocf:heartbeat:SAPHanaFilesystem):	 Started h02b2
  * Clone Set: SAPHanaController_H01_00-clone [SAPHanaController_H01_00] (promotable):
    * SAPHanaController_H01_00	(ocf:heartbeat:SAPHanaController):	 Promoted h01a1
    * SAPHanaController_H01_00	(ocf:heartbeat:SAPHanaController):	 Unpromoted h01a2
    * SAPHanaController_H01_00	(ocf:heartbeat:SAPHanaController):	 Unpromoted h02b1
    * SAPHanaController_H01_00	(ocf:heartbeat:SAPHanaController):	 Unpromoted h02b2
  * vip_H01_00	(ocf:heartbeat:IPaddr2):	 Started h01a1

Node Attributes:
  * Node: h01a1 (1):
    * hana_h01_clone_state            	: PROMOTED
    * hana_h01_roles                  	: master1:master:worker:master
    * hana_h01_site                   	: Site1
    * hana_h01_srah                   	: -
    * hana_h01_version                	: 2.00.078.00
    * hana_h01_vhost                  	: h01a1
    * master-SAPHanaController_H01_00 	: 150
  * Node: h01a2 (2):
    * hana_h01_clone_state            	: DEMOTED
    * hana_h01_roles                  	: slave:slave:worker:slave
    * hana_h01_site                   	: Site1
    * hana_h01_srah                   	: -
    * hana_h01_version                	: 2.00.078.00
    * hana_h01_vhost                  	: h01a2
    * master-SAPHanaController_H01_00 	: -10000
  * Node: h02b1 (3):
    * hana_h01_clone_state            	: DEMOTED
    * hana_h01_roles                  	: master1:master:worker:master
    * hana_h01_site                   	: site2
    * hana_h01_srah                   	: -
    * hana_h01_version                	: 2.00.078.00
    * hana_h01_vhost                  	: h02b1
    * master-SAPHanaController_H01_00 	: 100
  * Node: h02b2 (4):
    * hana_h01_clone_state            	: DEMOTED
    * hana_h01_roles                  	: slave:slave:worker:slave
    * hana_h01_site                   	: site2
    * hana_h01_srah                   	: -
    * hana_h01_version                	: 2.00.078.00
    * hana_h01_vhost                  	: h02b2
    * master-SAPHanaController_H01_00 	: -12200
  * Node: h03 (5):
    * hana_h01_vhost                  	: h03

Migration Summary:

Tickets:

PCSD Status:
  h01a1: Online
  h01a2: Online
  h02b1: Online
  h02b2: Online
  h03: Online

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```
{: screen}

On the promoted cluster node, verify that the cluster service IP address is assigned and active.

```sh
ip address show
```
{: pre}

### Enabling automated registration of the secondary instance
{: #ha-rhel-hana-ng-mh-enable-automated-registration}

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
{: #ha-rhel-hana-ng-mh-test-sap-hana-sr-cluster}


Test the cluster configuration thoroughly to ensure that all components operate as expected.
The following examples illustrate failover test scenarios.
This list is not exhaustive.

Each test case includes the following details.

- Component that is being tested
- Test description
- Prerequisites and cluster state before the failover is initiated
- Test procedure
- Expected behavior and results
- Recovery procedure

### Test 1 - Testing failure of the primary master node
{: #ha-rhel-hana-ng-mh-test1-primary-master-database-failure}

Use the following procedure to test a failure of the SAP HANA primary master instance.

#### Test 1 - Description
{: #ha-rhel-hana-ng-mh-test1-description}

Simulate a crash of the SAP HANA primary master instance that runs on NODE_A1.

#### Test 1 - Prerequisites
{: #ha-rhel-hana-ng-mh-test1-prerequisites}

- A functional five-node RHEL HA Add-On cluster is configured for SAP HANA system replication.
- The cluster is running on all nodes.
- The `SAPHanaController_${SID}_${INSTNO}` resource is configured with `AUTOMATED_REGISTER=false`.
- SAP HANA system replication status:
   - The SAP HANA primary master instance is running on NODE_A1.
   - The SAP HANA secondary master instance is running on NODE_B1.
   - System replication is active and in sync.

#### Test 1 - Test procedure
{: #ha-rhel-hana-ng-mh-test1-procedure}

To simulate a crash of the SAP HANA primary master instance, send a SIGKILL signal by running the following command on NODE_A1.

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test 1 - Expected behavior
{: #ha-rhel-hana-ng-mh-test1-expected-behavior}

- The SAP HANA primary master instance on NODE_A1 crashes.
- The cluster detects the stopped instance and marks the resource as `failed`.
- The cluster promotes the SAP HANA secondary master instance on NODE_B1 to become the new primary.
- The virtual IP address is released from NODE_A1 and reassigned to NODE_B1.
- Applications such as SAP S/4HANA and SAP NetWeaver automatically reconnect to the new SAP HANA primary master instance, and query processing continues on the nodes at SITE_B.

#### Test 1 - Recovery procedure
{: #ha-rhel-hana-ng-mh-test1-recovery-procedure}

Because the `SAPHanaController_${SID}_${INSTNO}` resource is configured with `AUTOMATED_REGISTER=false`, the cluster does not restart the failed SAP HANA instance or register it with the new primary.
As a result, the new primary master on NODE_B1 shows the former secondary in `CONNECTION TIMEOUT` status.

To manually reregister the previous primary master as a new secondary master, run the following command on NODE_A1.

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

Verify the SAP HANA system replication status.

```sh
sudo -i -u ${sid}adm -- <<EOT
    hdbnsutil -sr_state
    HDBSettings.sh systemReplicationStatus.py
EOT
```
{: pre}

After manual registration and resource refresh, the new secondary master instance restarts and appears in `SOK` (synced) status.

Run the following command on NODE_A1 to refresh the resource.

```sh
pcs resource refresh SAPHanaController_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}

### Test 2 - Testing node failure of the primary master instance
{: #ha-rhel-hana-ng-mh-test2-primary-instance-server-failure}

Use the following procedure to simulate and test a failure of the node that hosts the SAP HANA primary master instance.

#### Test 2 - Description
{: #ha-rhel-hana-ng-mh-test2-description}

Simulate a crash of the node that hosts the SAP HANA primary master instance.

#### Test 2 - Preparation
{: #ha-rhel-hana-ng-mh-test2-preparation}

Ensure that the `SAPHanaController_${SID}_${INSTNO}` resource is configured with `AUTOMATED_REGISTER=true`.

Run the following commands on NODE_A1 to update and verify the resource configuration.

```sh
pcs resource update SAPHanaController_${SID}_${INSTNO} AUTOMATED_REGISTER=true
```
{: pre}

```sh
pcs resource config SAPHanaController_${SID}_${INSTNO}
```
{: pre}

#### Test 2 - Prerequisites
{: #ha-rhel-hana-ng-mh-test2-prerequisites}

- A functional five-node RHEL HA Add-On cluster is configured for HANA system replication.
- The cluster is running on all nodes.
- Verify that SAP HANA system replication is active and synchronized:
   - The SAP HANA primary master instance is running on NODE_B1.
   - The SAP HANA secondary master instance is running on NODE_A1.
   - System replication is active and in sync.

#### Test 2 - Test procedure
{: #ha-rhel-hana-ng-mh-test2-procedure}

To simulate a failure of NODE_B1, trigger an immediate shut off request by running the following command on NODE_B1.

```sh
sync; echo o > /proc/sysrq-trigger
```
{: pre}

#### Test 2 - Expected behavior
{: #ha-rhel-hana-ng-mh-test2-expected-behavior}

- NODE_B1 shuts down.
- The cluster detects the node failure and marks its state as `OFFLINE`.
- The cluster promotes the SAP HANA secondary master instance on NODE_A1 to primary master.
- The virtual IP address is reassigned to NODE_A1.
- Applications such as SAP S/4HANA and SAP NetWeaver automatically reconnect to the new SAP HANA primary master instance, and query processing continues on the nodes at SITE_A.

#### Test 2 - Recovery procedure
{: #ha-rhel-hana-ng-mh-test2-recovery-procedure}

Log in to the {{site.data.keyword.cloud}} console and start the NODE_B1 virtual server instance.
After NODE_B1 becomes available, restart the cluster framework.

Run the following commands on NODE_B1.

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
{: #ha-rhel-hana-ng-mh-test3-secondary-instance-database-failure}

Follow these steps to simulate a failure of the SAP HANA secondary master instance.

#### Test 3 - Description
{: #ha-rhel-hana-ng-mh-test3-description}

This test simulates a crash of the SAP HANA secondary master to validate cluster failover behavior.

#### Test 3 - Prerequisites
{: #ha-rhel-hana-ng-mh-test3-prerequisites}

- A functional five-node RHEL HA Add-On cluster is configured for HANA system replication.
- The cluster is running on all nodes.
- The `SAPHanaController_${SID}_${INSTNO}` resource is configured with `AUTOMATED_REGISTER=true`.
- Verify that SAP HANA system replication is active and synchronized:
   - The SAP HANA primary master instance is running on NODE_A1.
   - The SAP HANA secondary master instance is running on NODE_B1.
   - System replication is active and in sync.

#### Test 3 - Test Procedure
{: #ha-rhel-hana-ng-mh-test3-procedure}

To simulate a crash of the SAP HANA secondary master instance, send a SIGKILL signal by running the following command on NODE_B1.

```sh
sudo -i -u ${sid}adm -- HDB kill-9
```
{: pre}

#### Test 3 - Expected behavior
{: #ha-rhel-hana-ng-mh-test3-expected-behavior}

- The SAP HANA secondary master instance on NODE_B1 crashes.
- The cluster detects the failure and marks the corresponding resource as `failed`.
- The cluster restarts the SAP HANA secondary master instance.
- System replication is reestablished and returns to a synchronized state.

#### Test 3 - Recovery procedure
{: #ha-rhel-hana-ng-mh-test3-recovery-procedure}

Wait until the SAP HANA secondary master instance starts and system replication status returns to `SOK`.
Then, clean up any failed resource actions as indicated by the output of the `pcs status` command.

Run the following commands on NODE_B1.

```sh
pcs resource refresh SAPHanaController_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}

### Test 4 - Testing manual move of a SAPHana resource to another node
{: #ha-rhel-hana-ng-mh-test4-manual-move}

Follow these steps to manually move a *SAPHanaController* resource to another node.

#### Test 4 - Description
{: #ha-rhel-hana-ng-mh-test4-description}

Use cluster management commands to relocate the SAP HANA primary master instance to another node for maintenance purposes.

#### Test 4 - Prerequisites
{: #ha-rhel-hana-ng-mh-test4-prerequisites}

- A functional five-node RHEL HA Add-On cluster is configured for SAP HANA system replication.
- The cluster is running on all nodes.
- The `SAPHanaController_${SID}_${INSTNO}` resource is configured with `AUTOMATED_REGISTER=true`.
- Verify that SAP HANA system replication is active and synchronized:
   - The SAP HANA primary master instance is running on NODE_A1.
   - The SAP HANA secondary master instance is running on NODE_B1.
   - System replication is active and in sync.

#### Test 4 - Test procedure
{: #ha-rhel-hana-ng-mh-test4-procedure}

To manually move the SAP HANA primary master instance to another node, run the following command on NODE_A1.

```sh
pcs resource move SAPHanaController_${SID}_${INSTNO}-clone
```
{: pre}

#### Test 4 - Expected behavior
{: #ha-rhel-hana-ng-mh-test4-expected-behavior}

- The cluster creates temporary location constraints to relocate the resource to the designated target node.
- The cluster initiates a takeover, promoting the SAP HANA secondary master instance to the primary role.
- Applications such as SAP S/4HANA and SAP NetWeaver automatically reconnect to the new SAP HANA primary master instance, and query processing continues on the nodes at SITE_B.

#### Test 4 - Recovery procedure
{: #ha-rhel-hana-ng-mh-test4-recovery-procedure}

Wait until the new SAP HANA primary master instance is fully operational.
After a short delay, the cluster automatically removes the location constraints.
The cluster then registers and starts the original primary master instance as the new secondary master instance.

Run the following commands on NODE_A1 to verify the cluster status.

```sh
pcs constraint
```
{: pre}

```sh
pcs status --full
```
{: pre}

### Test 5 - Testing node failure of the primary worker instance
{: #ha-rhel-hana-ng-mh-test5-primary-worker-instance-failure}

Follow this procedure to simulate and test a failure of the node that hosts the SAP HANA primary worker instance.

#### Test 5 - Description
{: #ha-rhel-hana-ng-mh-test5-description}

This test simulates a crash of the node that hosts the SAP HANA primary worker instance.

#### Test 5 - Prerequisites
{: #ha-rhel-hana-ng-mh-test5-prerequisites}

- A functional five-node RHEL HA Add-On cluster is configured for HANA system replication.
- The cluster is running on all nodes.
- Verify that SAP HANA system replication is active and synchronized:
   - The SAP HANA primary master instance is running on NODE_B1.
   - The SAP HANA secondary master instance is running on NODE_A1.
   - System replication is active and in sync.

#### Test 5 - Test procedure
{: #ha-rhel-hana-ng-mh-test5-procedure}

To simulate a node failure on `NODE_B2`, trigger an immediate shut off request by running the following command on NODE_B2.

```sh
sync; echo o > /proc/sysrq-trigger
```
{: pre}

#### Test 5 - Expected behavior
{: #ha-rhel-hana-ng-mh-test5-expected-behavior}

- NODE_B2 shuts down.
- The cluster detects the node failure and marks its state as `OFFLINE`.
- The cluster promotes the SAP HANA secondary master instance on NODE_A1 to primary master.
- The virtual IP address is reassigned to NODE_A1.
- Applications such as SAP S/4HANA and SAP NetWeaver automatically reconnect to the new SAP HANA primary master instance, and query processing continues on the nodes at SITE_A.

#### Test 5 - Recovery procedure
{: #ha-rhel-hana-ng-mh-test5-recovery-procedure}

Log in to the {{site.data.keyword.cloud}} console and start the virtual server instance NODE_B2.
After NODE_B2 becomes available, restart the cluster framework.

Run the following commands on NODE_B2.

```sh
pcs cluster start
```
{: pre}

```sh
pcs status --full
```
{: pre}

Because AUTOMATED_REGISTER=true is configured for the SAPHanaController_${SID}_${INSTNO} resource, SAP HANA automatically restarts when NODE_B1 rejoins the cluster.
The former primary master instance is then reregistered as a secondary master.

### Test 6 - Testing node failure of the secondary worker instance
{: #ha-rhel-hana-ng-mh-test6-secondary-worker-instance-failure}

Follow these steps to simulate a failure of the SAP HANA secondary worker instance.

#### Test 6 - Description
{: #ha-rhel-hana-ng-mh-test6-description}

This test simulates a crash of the SAP HANA secondary worker instance to validate cluster failover behavior.

#### Test 6 - Prerequisites
{: #ha-rhel-hana-ng-mh-test6-prerequisites}

- A functional five-node RHEL HA Add-On cluster is configured for HANA system replication.
- The cluster is running on all nodes.
- The SAPHanaController_${SID}_${INSTNO} resource is configured with AUTOMATED_REGISTER=true.
- Verify that SAP HANA system replication is active and synchronized:
   - The SAP HANA primary master instance is running on NODE_A1.
   - The SAP HANA secondary master instance is running on NODE_B1.
   - System replication is active and in sync.

#### Test 6 - Test Procedure
{: #ha-rhel-hana-ng-mh-test6-procedure}

To simulate a failure of NODE_B2, trigger an immediate shut-off request by running the following command on NODE_B2.

```sh
sync; echo o > /proc/sysrq-trigger
```
{: pre}

#### Test 6 - Expected behavior
{: #ha-rhel-hana-ng-mh-test6-expected-behavior}

- NODE_B2 shuts down.
- The cluster detects the node failure and marks its state as `OFFLINE`.
- The SAP HANA secondary master NODE_B1 is marked as `UNDEFINED`.
   The SAP HANA processes `hdbcompileserver` and `hdbpreprocessor` are stopped.
- The SAP HANA primary master instance on NODE_A1 remains active.
- SAP HANA System Replication cannot connect to the secondary site.
- Applications such as SAP S/4HANA and SAP NetWeaver remain connected to the SAP HANA primary master instance, and query processing continues on the nodes at SITE_A.

#### Test 6 - Recovery procedure
{: #ha-rhel-hana-ng-mh-test6-recovery-procedure}

Log in to the {{site.data.keyword.cloud}} console and start the virtual server instance NODE_B2.
After NODE_B2 becomes available, restart the cluster framework.

Run the following commands on NODE_B2.

```sh
pcs cluster start
```
{: pre}

Because AUTOMATED_REGISTER=true is configured for the SAPHanaController_${SID}_${INSTNO} resource, SAP HANA automatically restarts when NODE_B2 rejoins the cluster.
After the secondary worker instance is running again, the SAP HANA secondary master instance is automatically reregistered with the primary master instance.
System replication becomes active and synchronized.

Run the following command on NODE_A1 to verify the cluster status.

```sh
pcs status --full
```
{: pre}
