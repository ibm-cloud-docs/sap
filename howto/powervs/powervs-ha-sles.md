---
copyright:
  years: 2025
lastupdated: "2025-07-03"
keywords: SAP, SAP-Certified Infrastructure, SAP Workloads, SAP HANA, SAP HANA System Replication, high availability, HA, Linux, Pacemaker, SLES, SUSE, SLES High Availability Extension, SLES HAE
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Implementing a SUSE Linux Enterprise Server high availability cluster
{: #ha-sles}

Use the following information and procedures to implement a SUSE Linux&reg; Enterprise (SLE) cluster that uses the SUSE High Availability Extension (HAE).
The cluster uses instances in [{{site.data.keyword.powerSysFull}}](https://www.ibm.com/products/power-virtual-server){: external} are used as cluster nodes.
{: shortdesc}

The following information describes how to transform the individual virtual server instances into a cluster.

These procedures include installing the high availability packages and agents on each cluster node and configuring the fencing devices.

This information is intended for architects and specialists who are planning a high availability deployment of SAP applications on {{site.data.keyword.powerSys_notm}}. It is not intended to replace existing SAP or SUSE documentation.
{: note}

## Before you begin
{: #ha-sles-begin}

Before you begin, review the general requirements, product documentation, support articles, and SAP notes that are listed in [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-sles-refs).

The setup uses SBD fencing in combination with the hardware watchdog timer.
Create the virtual server instances by using one of the `Power10` machine types in the profile to enable hardware watchdog support.

## Create virtual server instances for the cluster
{: #ha-sles-create-virtual-server-instances}

Use the instructions in [Creating instances for a high availability cluster](/docs/sap?topic=sap-ha-vsi) to create the virtual server instances that you want to use as cluster nodes.

## Prepare the nodes for SLE High Availability Extension installation
{: #ha-sles-prepare-nodes-for-sles-hae-installation}

The following section describes the basic preparation steps on the cluster nodes.
Make sure that all steps are completed on both nodes.

Log in to each cluster node as the root user.

### Adding cluster node entries to the hosts file
{: #ha-sles-update-hosts-file}

On both nodes, add the IP addresses of all cluster nodes with their fully qualified hostname and short hostname to the `/etc/hosts` file.

For more information, see [hostname and IP address](https://documentation.suse.com/sle-ha/15-SP6/html/SLE-HA-all/cha-ha-requirements.html#sec-ha-requirements-other){: external} in the _System requirements and recommendation_ section of the _SUSE Linux Enterprise High Availability Administration Guide_.

### Preparing environment variables
{: #ha-sles-prepare-environment-variables}

To simplify the setup process, prepare some environment variables for the root user.
These environment variables are used with later operating system commands in this information.

On both nodes, create a file with the following environment variables and update to your environment.

```sh
# General settings
export CLUSTERNAME="SAP_CLUSTER"         # Cluster name

# Virtual server instance 1
export NODE1=<HOSTNAME_1>                # Virtual server instance hostname

# Virtual server instance 2
export NODE2=<HOSTNAME_2>                # Virtual server instance hostname
```
{: codeblock}

## Install and configure a SLE HA cluster
{: #ha-sles-install-and-configure-sles-ha-cluster}

Use the following steps to set up a two-node cluster.

The instructions are based on the SUSE documentation and articles that are listed in [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-sles-refs).

Some steps must be performed on both nodes, while others can be run on either NODE1 or NODE2.
{: tip}

### Installing SUSE HAE software
{: #ha-sles-install-sles-ha-software}

Use these steps to double check that the SUSE HAE repositories are available and install the required software packages.

#### Checking the SUSE HAE repositories
{: #ha-sles-check-sles-hae-repositories}

1. Check that the SUSE Linux(R) Enterprise High Availability Extension repositories are enabled by running the following command on both nodes.

   ```sh
   zypper repos --show-enabled-only | grep High
   ```
   {: pre}

   Skip the following steps if the output includes the SLE HAE repositories for both Pool and Updates.
   For example

   * `SUSE_Linux_Enterprise_High_Availability_Extension_ppc64le:SLE-Product-HA15-SP6-Pool`
   * `SUSE_Linux_Enterprise_High_Availability_Extension_ppc64le:SLE-Product-HA15-SP6-Updates`

1. If the HAE repositories are not listed, use `yast2` to enable them.

   * Software
   * Add-On Products
   * Add
   * Extensions and Modules from Registration Server
      * Yast2 loops over both repositories _Pool_ and _Updates_
      * Add both repositories
   * Quit the yast2 application

1. When finished, try the same command on both nodes again.

   ```sh
   zypper repos --show-enabled-only | grep High
   ```
   {: pre}

Both SLE HAE repositories are listed.

#### Installing the SLE HAE software packages
{: #ha-sles-install-hae-software-packages}

Use the following command to install the required software packages.

On both nodes, run the following command.

```sh
zypper install -t pattern ha_sles
```
{: pre}

### Configure a SLE high availability cluster
{: #ha-sles-configure-sles-ha-cluster}

Use the following information to configure a SLE high availability cluster.

#### Checking the hardware requirements
{: #ha-sles-configure-hardware-requirements}

See the following hardware requirements to configure a SLE high availability cluster.

For detailed hardware requirements, refer to the SUSE Linux Enterprise High Availability documentation in the [Installation and set up Quick Start](https://documentation.suse.com/sle-ha/15-SP6/html/SLE-HA-all/article-installation.html){: external}.

* Two virtual servers or logical partitions to serve as cluster nodes.
* A second network communication channel or a bonded network interface for Corosync.
* A shared volume to be used as a STONITH Block Device (SBD) device.
* A hardware watchdog timer for reliable node fencing.

#### Creating shared storage volumes
{: #ha-sles-configure-shared-volume}

Use the IBM Cloud PowerVS web interface to create one or three shared volumes to be used as SBD devices.
SUSE recommends creating three volumes to help ensure redundancy.
This information continues with the creation of a single SBD disk.

If you choose to use multiple disks, repeat the following steps for each volume as needed.

1. Go to the [Power Virtual Server - Storage Volumes](https://cloud.ibm.com/power/storage){: external} page.
1. Click **Create volume**.
1. In the **Create storage volume** form, enter the following information.
   * Enter a **Name** for the volume.
   * Set the **Size (GB)** to 1 (default).
   * Toggle **Shareable** to **ON**.
   * Accept the _Terms and Conditions_ by checking the corresponding box.
1. Click **Create volume** to finalize.

   Now you need to attach the volumes to both cluster nodes.

1. Modify the logical partition for the first cluster node.
1. Click **Attach existing** to attach the previously created shared volume to this node.
1. Repeat the same steps for the second cluster node.

1. On any node, run the following command to identify the device names of the 1 G volumes.

   ```sh
   multipath -l | grep -B1 'size=1G'
   ```
   {: pre}

   If the output of the `multipath -l` command is:

   ```screen
   36005076813810264e800000000006f10 dm-0 IBM,2145
   size=1G features='1 queue_if_no_path' hwhandler='1 alua' wp=rw
   ```

1. To confirm the device name associated with the shared volume, run the following command.

   ```sh
   ls /dev/disk/by-id/wwn-0x6005076813810264e800000000006f10
   ```
   {: pre}

   The SBD device must have the same device name on all cluster nodes to help ensure proper cluster operation.

   To simplify future steps, export the SBD device path as an environment variable for use during cluster initialization.

   ```sh
   export SBD_DEVICE=/dev/disk/by-id/wwn-0x6005076813810264e800000000006f10
   ```
   {: pre}

#### Configuring the hardware watchdog
{: #ha-sles-configure-hardware-watchdog}

The hardware watchdog timer is enabled by default on Power10 logical partitions.
To verify its availability, run the following command.

```sh
ls /dev/watchdog*
```
{: pre}

The output lists two devices: `/dev/watchdog` and `/dev/watchdog0`.

If no watchdog device is listed, run the following command to verify that the processor compatibility mode is set to Power10.

```sh
LD_SHOW_AUXV=1 /bin/true | grep _PLATFORM
```
{: pre}

Look for the following fields in the output.

* AT_BASE_PLATFORM – shows the actual processor type.
* AT_PLATFORM – shows the processor compatibility mode.

The processor compatibility mode must be set to Power10.
If it is set to an earlier version, the hypervisor watchdog isn't available.

#### Arbitrator
{: #ha-sles-configure-arbitrator}

For a two-node cluster, a third LPAR as arbitrator is recommended.
The arbitrator is a logical partition that is not part of the cluster. It hosts the QNetd server as a third instance to support finding the cluster quorum in split brain scenarios. With an even number of cluster nodes, arbitrators are essential because they add an instance to the quorum.

For more information, see the SUSE documentation [QDevice and QNetd](https://documentation.suse.com/sle-ha/15-SP6/html/SLE-HA-all/cha-ha-qdevice.html){: external}.

The arbitrator is typically set up after the cluster is running. Use the `crm cluster init qdevice` approach as described in the SUSE documentation.

#### Configuring time synchronization
{: #ha-sles-configure-time-sync}

Both cluster nodes require time synchronization to help ensure proper operation.
When logical partitions run on PowerVS, time synchronization is handled automatically through the Hardware Management Console (HMC).
In this setup, the chrony daemon is not required.

#### Configuring the first cluster node
{: #ha-sles-configure-first-node}

The SUSE Linux Enterprise High Availability Extension installation scripts automatically configure many key components of the cluster environment.

```sh
sudo crm cluster init --name "$CLUSTERNAME" --sbd-device "$SBD_DEVICE"
```
{: pre}

Press Enter to accept the default values, or enter `y` and press Enter when prompted.

```screen
INFO: Loading "default" profile from /etc/crm/profiles.yml
WARNING: chronyd.service is not configured to start at system boot.
Do you want to continue anyway (y/n)? y
INFO: The user 'hacluster' will have the login shell configuration changed to /bin/bash
Continue (y/n)? y
INFO: A new ssh keypair is generated for user hacluster.
INFO: Configuring csync2
INFO: Starting csync2.socket service on ths-3
INFO: BEGIN csync2 checking files
INFO: END csync2 checking files
INFO: Configure Corosync (unicast):
  This will configure the cluster messaging layer.  You will need
  to specify a network address over which to communicate (default
  is eth0's network, but you can use the network address of any
  active interface).

Address for ring0 [10.51.0.84]
Port for ring0 [5405]
INFO: Initializing SBD
INFO: Hawk cluster interface is now running. To see cluster status, open:
INFO:   https://10.51.0.84:7630/
INFO: Log in with username 'hacluster', password 'linux'
WARNING: You should change the hacluster password to something more secure!
INFO: Starting pacemaker.service on ths-3
INFO: BEGIN Waiting for cluster
...........                                                                     INFO: END Waiting for cluster
INFO: Loading initial cluster configuration
WARNING: "stonith-enabled" in crm_config is set to true, it was false
INFO: Configure Administration IP Address:
  Optionally configure an administration virtual IP
  address. The purpose of this IP address is to
  provide a single IP that can be used to interact
  with the cluster, rather than using the IP address
  of any specific cluster node.

Do you wish to configure a virtual IP address (y/n)? y
Virtual IP []10.51.0.12
INFO: BEGIN Configuring virtual IP (10.51.0.12)
.                                                                               INFO: END Configuring virtual IP (10.51.0.12)
INFO: Configure Qdevice/Qnetd:
  QDevice participates in quorum decisions. With the assistance of
  a third-party arbitrator Qnetd, it provides votes so that a cluster
  is able to sustain more node failures than standard quorum rules
  allow. It is recommended for clusters with an even number of nodes
  and highly recommended for 2 node clusters.

Do you want to configure QDevice (y/n)? n
INFO: Done (log saved to /var/log/crmsh/crmsh.log on ths-3)
```
{: screen}

The cluster is now up and running with a single node.

#### Configuring the second cluster node
{: #ha-sles-configure-second-node}

Use the following information so configure the second cluster node.

1. Log in to the second cluster node.
1. To integrate the node into the existing cluster, use the ha-cluster-join command. This command requires a user account and the hostname of the first cluster node.

   ```sh
   sudo crm cluster join -u root -c $NODE1
   ```
   {: pre}

1. Press Enter to accept the default values, or type `y` and press Enter when prompted.

   ```screen
   WARNING: chronyd.service is not configured to start at system boot.
   Do you want to continue anyway (y/n)? y
   INFO: The user 'hacluster' will have the login shell configuration changed to /bin/bash
   Continue (y/n)? y
   INFO: A new ssh keypair is generated for user hacluster.
   INFO: Configuring csync2
   INFO: Starting csync2.socket service
   INFO: BEGIN csync2 syncing files in cluster
   INFO: END csync2 syncing files in cluster
   INFO: Merging known_hosts
   Warning: Permanently added 'ths-4' (ED25519) to the list of known hosts.
   INFO: BEGIN Probing for new partitions
   INFO: END Probing for new partitions
   Address for ring0 [10.51.0.230]
   INFO: Got SBD configuration
   INFO: Hawk cluster interface is now running. To see cluster status, open:
   INFO:   https://10.51.0.230:7630/
   INFO: Log in with username 'hacluster', password 'linux'
   WARNING: You should change the hacluster password to something more secure!
   INFO: Starting pacemaker.service on ths-4
   INFO: BEGIN Waiting for cluster
   ..                                                                              INFO: END Waiting for cluster
   INFO: BEGIN Adjusting sbd related timeout values
   WARNING: "stonith-timeout" in crm_config is set to 83, it was 43
   INFO: END Adjusting sbd related timeout values
   INFO: Set property "priority" in rsc_defaults to 1
   WARNING: "priority-fencing-delay" in crm_config is set to 60, it was 0
   INFO: BEGIN Reloading cluster configuration
   INFO: END Reloading cluster configuration
   INFO: Done (log saved to /var/log/crmsh/crmsh.log on ths-4)
   ```
   {: screen}

1. To verify that the SLE cluster is running, run the following command.

   ```sh
   sudo crm status
   ```
   {: pre}

   If no errors occurred, the **Node List** section in the output shows both nodes and are listed as `Online`.

   ```screen
   Node List:
     * Online: [ ths-3 ths-4 ]
   ```
   {: screen}

   The **Resources** section lists two resources that are in the **Started** state, which indicates that they are active and running properly.

   ```screen
   Full List of Resources:
     * stonith-sbd	(stonith:external/sbd):	 Started ths-3
     * admin-ip	(ocf::heartbeat:IPaddr2):	 Started ths-3
   ```
   {: screen}

#### Setting a password for the hacluster user ID
{: #ha-sles-set-hacluster-password}


On both nodes, run the following command to set the password for the hacluster user account.

```sh
passwd hacluster
```
{: pre}

#### Checking the SBD status
{: #ha-sles-check-sbd-status}

Use the following information to check that the SBD_DEVICE variable is set.

To verify the status of the SBD slots from one of the cluster nodes, run the following command.

```sh
sbd -d $SBD_DEVICE list
```
{: pre}

The output lists both cluster nodes, each with a status of `clean`, indicating that the SBD mechanism is functioning correctly and no fencing actions are pending.

#### Configure the fencing action
{: #ha-sles-configure-fencing-action}

By default, the fenced node is automatically restarted after a fencing event.

However, an alternative approach is to power off the node and manually start it after an investigation of the root cause of the failure.

Manually activating the node has several advantages.

* It prevents unnecessary fencing loops, which can occur if the root issue persists.
* It makes sure that each fencing event is noticed and addressed, improving cluster stability and reliability.

To configure the fencing action to power off the node, use the following command.

```sh
sudo crm configure set cib-bootstrap-options.stonith-action off
```
{: pre}

Verify the current cluster configuration with the following command.

```sh
sudo crm configure show
```
{: pre}

#### Testing fencing operations
{: #ha-sles-test-fencing-operations}

To test the STONITH configuration, you need to manually trigger a fencing action on one of the nodes.

1. Run the following command to manually fence NODE2.

   ```sh
   crm node fence ${NODE2}
   ```
   {: pre}

1. Enter `y` and press Enter to proceed when prompted with the following message.

   ```screen
   Fencing ths-4 will shut down the node and migrate any resources that are running on it! Do you want to fence ths-4  (y/n)? y
   ```
   {: screen}

   As a result, NODE2 stops.

1. Activate NODE2, wait for it to rejoin the cluster, and test fencing from the opposite direction.

   On NODE2, run the following command.

   ```sh
   crm status
   ```
   {: pre}

1. When both nodes appear as `Online` in the cluster status, wait for about one minute to help ensure stability.
   Then, from NODE2, initiate a fencing action on NODE1 by running the following command.

   ```sh
   crm node fence ${NODE1}
   ```
   {: pre}

   NODE1 stops.

1. Activate NODE1, then start the cluster services on the node.
