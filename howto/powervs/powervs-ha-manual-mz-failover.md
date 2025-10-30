---
copyright:
  years: 2025
lastupdated: "2025-10-29"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, RHEL, SLES
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Configuring and performing manual failover for SAP S/4HANA in a multizone region deployment
{: #ha-rt-sap-ip}

The following information provides step-by-step instructions for performing a manual failover of the SAP S/4HANA system.
This process helps ensure continued availability of critical services in a multizone deployment on {{site.data.keyword.powerSys_notm}} instances.
{: shortdesc}

It is intended for cloud architects and SAP specialists who plan high-availability deployments of SAP S/4HANA on {{site.data.keyword.powerSys_notm}}.

High availability is a critical requirement for enterprise SAP workloads.
In a multizone region deployment on {{site.data.keyword.powerSys_notm}}, redundancy and failover mechanisms help ensure business continuity during planned maintenance or unexpected infrastructure failures.

To achieve high availability, the architecture includes:

Redundant SAP HANA systems
:   Deploy two SAP HANA instances in separate zones within the same region.
    Configure the secondary system as a synchronous replica of the primary system to ensure zero data loss and minimal service interruption.
    This configuration achieves a recovery point objective (RPO) of 0 seconds and a recovery time objective (RTO) of only a few minutes.

SAP Central Services redundancy
:   Provision two virtual server instances in separate zones to host the ABAP SAP Central Services (ASCS) and the SAP Enqueue Replication Server (ERS).
    During normal operation, the ASCS instance runs on one server and the ERS instance on the other.
    If a failure occurs or maintenance is required, the ASCS instance can be manually relocated to the standby server.

Shared file systems
:   Use *File Storage for VPC* with regional shares to support SAP instance directories such as `SAPMNT` and `SAPTRANS` across zones.

## Preparing the SAP S/4HANA system environment
{: #ha-manual-mz-failover-prep}

To support manual failover in a multizone region, prepare the SAP S/4HANA environment with the following components.

- Provision IBM {{site.data.keyword.powerSys_notm}} workspaces:
   - Create two IBM {{site.data.keyword.powerSys_notm}} workspaces in separate zones in the same region.
     In this example, the workspaces are named `workspace-1` and `workspace-2`.

- Provision virtual server instances on either Red Hat Enterprise Linux (RHEL) or SUSE Linux Enterprise Server (SLES):
   - Ensure that you have a valid subscription for *RHEL for SAP Applications*, *RHEL for SAP Solutions*, or *SLES for SAP Applications*.
   - Create two virtual server instances for SAP HANA.
     In this example, the hostnames for the SAP HANA instances are `prd-db-1` and `prd-db-2`.

   - Create two virtual server instances for the ASCS and ERS instances.
     In this example, the hostnames for these instances are `prd-cs-1` and `prd-cs-2`.

   - Distribute each pair across the two IBM {{site.data.keyword.powerSys_notm}} workspaces to ensure zone-level redundancy.

- Provision SAP application servers:
   - Provision a virtual server instance for the SAP Primary Application Server (PAS).
     In this example, the hostname for PAS is `prd-as-1`.
   - Optionally, deploy SAP Additional Application Server (AAS) instances to support scalability.

- Configure shared storage:
   - Provision a regional file share for the SAP instance file systems, including the `SAPMNT` and `SAPTRANS` shares.
     Refer to [Accessing File Storage for VPC with regional availability from IBM {{site.data.keyword.powerSys_notm}} instances](/docs/sap?topic=sap-ha-nlb-rt-rfs-intro) for instructions on configuring shared storage with regional availability.
     In this example, the DNS name for the mount target of the regional file share is `mt-sapha-nfs.sapha.com`, and the name of the share is `/DC44A0EC27C6426F90D2CE6DE3155056`.

- Configure network connectivity:
   - Create a *Transit Gateway* and attach both IBM {{site.data.keyword.powerSys_notm}} workspaces to it.

- Verify user permissions:
   - Ensure that your user account has the required permissions to create and manage static routes in both workspaces.

The following table lists the IP addresses of the virtual server instances that are used in this example configuration.

| Hostname | Workspace   | Role                       | IP address     |
|----------|-------------|----------------------------|----------------|
| prd-db-1 | workspace-1 | HANA primary               | 192.168.20.115 |
| prd-cs-1 | workspace-1 | ASCS                       | 192.168.20.54  |
| prd-as-1 | workspace-1 | Primary Application Server | 192.168.20.97  |
| prd-db-2 | workspace-2 | HANA secondary             | 192.168.10.25  |
| prd-cs-2 | workspace-2 | ERS                        | 192.168.10.8   |
{: caption="Sample IP addresses of the virtual server instances" caption-side="bottom"}

### Preparing the virtual hostnames on the virtual server instances
{: #ha-manual-mz-failover-prep-vhost}

- The virtual hostname `prd-db` is used for communication with the database (DB) server.
- The ASCS instance is installed with the virtual hostname `prd-cs`, and the ERS instance is installed with the virtual hostname `prd-er`.

The following table lists the IP addresses assigned to the SAP instances in this example configuration.

| Virtual hostname | Role               | IP address     |
|------------------|--------------------|----------------|
| prd-cs           | ASCS               | 192.168.21.42  |
| prd-er           | ERS                | 192.168.21.46  |
| prd-db           | DB server          | 192.168.12.102 |
{: caption="Sample virtual IP addresses of the SAP instances" caption-side="bottom"}

- Add the virtual hostnames and corresponding IP addresses to DNS, or configure local name resolution by updating the `/etc/hosts` file on all instances.

The virtual hostnames correspond to overlay IP addresses within the IBM {{site.data.keyword.powerSys_notm}} workspaces.
These IP addresses fall outside the Classless Inter-Domain Routing (CIDR) ranges that are defined for the workspace subnets.

#### Configuring the overlay IP address for the ASCS instance
{: #ha-rt-sap-prep-ip-cs}

Configure the virtual hostname for the ASCS instance by adding it as an IP alias (secondary IP address) to the network adapter of the virtual server instance.

1. Log in as user `root` to the virtual server instance `prd-cs-1`.
1. Run the following command to add the overlay IP address to the network adapter `env2`.

   ```shell
   ip address add 192.168.21.42/32 dev env2
   ```
   {: screen}

#### Configuring the overlay IP address for the ERS instance
{: #ha-rt-sap-prep-ip-er}

Configure the virtual hostname for the ERS instance by adding it as an IP alias (secondary IP address) to the network adapter of the virtual server instance.

1. Log in as user `root` to the virtual server instance `prd-cs-2`.
1. Run the following command to add the overlay IP address to the network adapter `env2`.

   ```shell
   ip address add 192.168.21.46/32 dev env2
   ```
   {: screen}

#### Configuring the overlay IP address for the DB server
{: #ha-rt-sap-prep-ip-db}

Configure the virtual hostname for the database server by adding it as an IP alias (secondary IP address) to the network adapter of the virtual server instance.

1. Log in as user `root` to the virtual server instance `prd-db-1`.
1. Run the following command to add the overlay IP address to the network adapter `env2`.

   ```shell
   ip address add 192.168.12.102/32 dev env2
   ```
   {: screen}

### Configuring static routes in the workspaces
{: #ha-manual-mz-failover-prep-rt}

The following table shows the static route configuration that is used in this example.
Create three routes in each of the two workspaces to enable communication with the overlay IP addresses.

Enable each route only in the workspace where its destination IP address is active.
{: note}


| Workspace   | Route Name              | Destination     | Next hop       | Advertise | Status   |
|-------------|-------------------------|-----------------|----------------|-----------|----------|
| workspace-1 | prd-db-vip-route-zone1  | 192.168.12.102  | 192.168.20.115 | Enabled   | Enabled  |
| workspace-1 | prd-cs-vip-route-zone1  | 192.168.21.42   | 192.168.20.54  | Enabled   | Enabled  |
| workspace-1 | prd-er-vip-route-zone1  | 192.168.21.46   | 192.168.20.54  | Enabled   | Disabled |
| workspace-2 | prd-db-vip-route-zone2  | 192.168.12.102  | 192.168.10.25  | Enabled   | Disabled |
| workspace-2 | prd-cs-vip-route-zone2  | 192.168.21.42   | 192.168.10.8   | Enabled   | Disabled |
| workspace-2 | prd-er-vip-route-zone2  | 192.168.21.46   | 192.168.10.8   | Enabled   | Enabled  |
{: caption="Sample route configuration" caption-side="bottom"}

#### Creating the routes for `workspace-1`
{: #ha-rt-sap-create-routes-zone1}

Follow these steps to create static routes for the virtual IP addresses in `workspace-1`.

1. Log in to [Power Virtual Server workspaces](/power/workspaces){: external}.
1. Select **workspace-1**, and then click **View virtual servers**.
1. In the left pane, click **Routes**.

Create the route for the virtual IP address of the database.

1. Click **Create static route**.
1. Enter *prd-db-vip-route-zone1* as the **Name**.
1. Enter *192.168.12.102* as the **Destination**.
1. Enter *192.168.20.115* as the **Next hop**.
1. Keep **Advertise** and **Status** set to enabled.
1. Click **Create route**.

Create the route for the virtual IP address of the ASCS instance.

1. Click **Create static route**.
1. Enter *prd-cs-vip-route-zone1* as the **Name**.
1. Enter *192.168.21.42* as the **Destination**.
1. Enter *192.168.20.54* as the **Next hop**.
1. Keep **Advertise** and **Status** set to enabled.
1. Click **Create route**.

Create the route for the virtual IP address of the ERS instance.

1. Click **Create static route**.
1. Enter *prd-er-vip-route-zone1* as the **Name**.
1. Enter *192.168.21.46* as the **Destination**.
1. Enter *192.168.20.54* as the **Next hop**.
1. Set **Advertise** to enabled and **Status** to disabled.
1. Click **Create route**.

#### Creating the routes for `workspace-2`
{: #ha-rt-sap-create-routes-zone2}

Follow these steps to create static routes for the virtual IP addresses in `workspace-1`.

1. Log in to [Power Virtual Server workspaces](/power/workspaces){: external}.
1. Select **workspace-2**, and then click **View virtual servers**
1. In the left pane, click **Routes**.

Create the route for the virtual IP address of the database.

1. Click **Create static route**.
1. Enter *prd-db-vip-route-zone2* as the **Name**.
1. Enter *192.168.12.102* as the **Destination**.
1. Enter *192.168.10.25* as the **Next hop**.
1. Set **Advertise** to enabled and **Status** to disabled.
1. Click **Create route**.

Create the route for the virtual IP address of the ASCS instance.

1. Click **Create static route**.
1. Enter *prd-cs-vip-route-zone2* as the **Name**.
1. Enter *192.168.21.42* as the **Destination**.
1. Enter *192.168.10.8* as the **Next hop**.
1. Set **Advertise** to enabled and **Status** to disabled.
1. Click **Create route**.

Create the route for the virtual IP address of the ERS instance.

1. Click **Create static route**.
1. Enter *prd-er-vip-route-zone2* as the **Name**.
1. Enter *192.168.21.46* as the **Destination**.
1. Enter *192.168.10.8* as the **Next hop**.
1. Keep **Advertise** and **Status** set to enabled.
1. Click **Create route**.


### Installing the SAP S/4HANA Server system
{: #ha-rt-sap-install-sap-systems}

To install an SAP S/4HANA system:

1. Complete planning and preparation.
1. Install the SAP HANA database.
1. Use SAP Software Provisioning Manager (SWPM) to install the SAP S/4HANA server.

In this example, the SAP systems are installed with the following configuration.

| Parameter             | Value         |
|-----------------------|---------------|
| SAP HANA System ID    | PRD           |
| SAP HANA InstNo       | 00            |
| SAP System ID         | PRD           |
| ASCS InstNo           | 11            |
| ASCS virtual hostname | prd-cs        |
| ERS InstNo            | 22            |
| ERS virtual hostname  | prd-er        |
| PAS hostname          | prd-as-1      |
| PAS InstNo            | 00            |
{: caption="Sample system configuration" caption-side="bottom"}

#### Installing the SAP HANA database servers
{: #ha-manual-mz-failover-install-hana}

Install two SAP HANA systems by using the SAP HANA database lifecycle manager (HDBLCM), and configure SAP HANA System Replication to enable high availability.

The installation of SAP HANA and configuration of HANA System Replication is not specific to the {{site.data.keyword.powerSys_notm}} environment.
Follow the standard SAP HANA installation procedures as documented by SAP.


#### Mounting the file shares for the ASCS and the ERS instances
{: #ha-manual-mz-failover-prep-nfs}

Refer to [Accessing File Storage for VPC with regional availability from IBM {{site.data.keyword.powerSys_notm}} instances](/docs/sap?topic=sap-ha-nlb-rt-rfs-intro) for instructions on configuring shared storage with regional availability.

Create regional file shares for following NFS file systems:
   - `/usr/sap/PRD` contains directories for both the *ASCS* and *ERS* instances.
   - `/sapmnt/PRD` hosts the *sapmnt* share.
   - `/usr/sap/trans` is required for shared transport directories (*saptrans*).

Mount the file shares on the virtual server instances `prd-cs-1`, `prd-cs-2`, `prd-as-1`, and eventually on the other application servers.

1. Log in as the `root` user to the virtual server instance.
1. Run the following command to create the mount points for the SAP instance file systems.

   ```sh
   mkdir -p /usr/sap/PRD /sapmnt/PRD
   ```
   {: screen}

1. Configure the regional file shares in the `/etc/fstab` file system table.

   Check whether `/etc/fstab` already contains entries for the *sapmnt* and *usrsap* file shares.
   If not, add the required entries.

   Make sure to adjust the export paths to match your regional file share layout.

   ```sh
   cat >> /etc/fstab << EOT
   mt-sapha-nfs.sapha.com:/DC44A0EC27C6426F90D2CE6DE3155056/SAP_PRD/sapmnt /sapmnt/PRD nfs4 rw,sec=sys,hard,rsize=65536,wsize=65536,namlen=255 0 0
   EOT
   ```
   {: screen}

   ```sh
   cat >> /etc/fstab << EOT
   mt-sapha-nfs.sapha.com:/DC44A0EC27C6426F90D2CE6DE3155056/SAP_PRD/usrsap /usr/sap/PRD nfs4 rw,sec=sys,hard,rsize=65536,wsize=65536,namlen=255 0 0
   EOT
   ```
   {: screen}

   Ensure that duplicate entries are not added to `/etc/fstab`.
   Use `cat >>` only if the entries do not exist.
   {: important}

1. Run the following commands to mount the file shares.

   ```sh
   mount /usr/sap/PRD
   ```
   {: screen}

   ```sh
   mount /sapmnt/PRD
   ```
   {: screen}

#### Installing SAP S/4HANA
{: #ha-manual-mz-failover-install-s4hana}

Install the ASCS and ERS instances for the SAP S/4HANA system on the two designated virtual server instances.

Install the PAS on `prd-as-1`.
During installation, the SAP HANA database tenant is automatically loaded.
Optionally, add one or more AAS instances later to support application server availability, scalability, and load distribution.

After installation is complete, start the SAP S/4HANA system and verify that all components are operational.

### Configuring the systemd services for the ASCS instance and the ERS instance
{: #ha-rt-sap-configure-systemd}

Register the ASCS and ERS instance agents with systemd by running the `sapstartsrv` command with the `-reg` option.
This command performs the following actions:
- It registers the instance with systemd.
- It creates the corresponding systemd unit files.
- It updates the /usr/sap/sapservices configuration file.

To prevent the instance agents from starting automatically after a system restart, disable the systemd units `SAPPRD_11.service` (ASCS) and `SAPPRD_22.service` (ERS).
This step ensures that both services do not start simultaneously on both nodes, which could lead to conflicts in the high-availability setup.

#### Configuring the systemd services on the first node
{: #ha-rt-sap-configure-systemd-1}

1. Log in as user `root` to `prd-cs-1`.
1. Set the `LD_LIBRARY_PATH` environment variable to include the instance executable directory.

   ```shell
   export LD_LIBRARY_PATH=/usr/sap/PRD/ASCS11/exe
   ```
   {: screen}

1. Check the status of the ASCS systemd service.

   ```shell
   systemctl status SAPPRD_11.service
   ```
   {: screen}

1. Start the ASCS systemd service.

   ```shell
   systemctl start SAPPRD_11.service
   ```
   {: screen}

1. Disable the ASCS service to prevent automatic startup.

   ```shell
   systemctl disable SAPPRD_11.service
   ```
   {: screen}

1. Register the ERS instance.

   ```shell
   /usr/sap/PRD/ASCS11/exe/sapstartsrv -reg pf=/usr/sap/PRD/SYS/profile/PRD_ERS22_prd-er
   ```
   {: screen}

1. Check the status of the ERS systemd service.

   ```shell
   systemctl status SAPPRD_22.service
   ```
   {: screen}

1. Stop and disable the ERS systemd service.

   ```shell
   systemctl disable --now SAPPRD_22.service
   ```
   {: screen}

#### Configuring the systemd services on the second node
{: #ha-rt-sap-configure-systemd-2}

1. Log in as user `root` to `prd-cs-2`.
1. Set the `LD_LIBRARY_PATH` environment variable to include the instance executable directory.

   ```shell
   export LD_LIBRARY_PATH=/usr/sap/PRD/ERS22/exe
   ```
   {: screen}

1. Check the status of the ERS systemd service.

    ```shell
    systemctl status SAPPRD_22.service
    ```
    {: screen}

1. Start the ERS systemd service.

   ```shell
   systemctl start SAPPRD_22.service
   ```
   {: screen}

1. Disable the ERS service to prevent automatic startup.

   ```shell
   systemctl disable SAPPRD_22.service
   ```
   {: screen}

1. Register the ASCS instance.

   ```shell
   /usr/sap/PRD/ERS22/exe/sapstartsrv -reg pf=/usr/sap/PRD/SYS/profile/PRD_ASCS11_prd-cs
   ```
   {: screen}

1. Check the status of the ASCS systemd service.

    ```shell
    systemctl status SAPPRD_11.service
    ```
    {: screen}

1. Stop and disable the ASCS systemd service.

   ```shell
   systemctl disable --now SAPPRD_11.service
   ```
   {: screen}

### Starting the ASCS instance on `prd-cs-1`
{: #ha-rt-sap-start-ascs}

1. Log in as user `prdadm` to `prd-cs-1`.
1. Start the instance agent.

   ```shell
   sapcontrol -nr 11 -function StartService PRD
   ```
   {: screen}

1. Start the ASCS instance.

   ```shell
   sapcontrol -nr 11 -function Start
   ```
   {: screen}

### Starting the ERS instance on `prd-cs-2`
{: #ha-rt-sap-start-ers}

1. Log in as user `prdadm` to `prd-cs-2`.
1. Start the instance agent.

   ```shell
   sapcontrol -nr 22 -function StartService PRD
   ```
   {: screen}

1. Start the ERS instance.

   ```shell
   sapcontrol -nr 22 -function Start
   ```
   {: screen}

### Starting the SAP HANA databases
{: #ha-rt-sap-start-hana}

#### Starting the HANA database on `prd-db-1`
{: #ha-rt-sap-start-hana-primary}

1. Log in as user `prdadm` to `prd-db-1`.
1. Start the SAP HANA system.

   ```shell
   HDB start
   ```
   {: screen}

#### Starting the HANA database on `prd-db-2`
{: #ha-rt-sap-start-hana-secondary}

1. Log in as user `prdadm` to `prd-db-2`.
1. Start the SAP HANA system.

   ```shell
   HDB start
   ```
   {: screen}

#### Verifying the System Replication status on the SAP HANA primary
{: #ha-rt-sap-start-hana-check-sr}

1. Log in as user `prdadm` to `prd-db-1`.
1. Check the HANA System Replication status.

   ```shell
   python $DIR_INSTANCE/exe/python_support/systemReplicationStatus.py
   ```
   {: screen}

Verify the current system state:
- The primary SAP HANA database is running on `prd-db-1`.
- The secondary SAP HANA database is running on `prd-db-2`.
- SAP HANA System Replication is active and in sync.


#### Verifying the ASCS and ERS instances
{: #ha-rt-sap-check-ascs-ers}

Verify that the ASCS and ERS instances are running on their designated virtual server instances:

- The *ASCS* instance is running on `prd-cs-1`.
- The *ERS* instance is running on `prd-cs-2`.

### Starting the SAP application server instance
{: #ha-rt-sap-start-sap-as}

1. Log in as user `prdadm` to `prd-as-1`.
1. Start the dialog instance.

   ```shell
   sapcontrol -nr 00 -function Start
   ```
   {: screen}

### Verifying log on to the application server
{: #ha-rt-sap-start-sap-as}

Start an SAP GUI* session and log on to the application server to verify the system status.

## Testing manual failover by simulating an outage of the SAP HANA primary
{: #ha-manual-mz-failover-hana-failover}

### Verifying the System Replication status on the HANA primary
{: #ha-manual-mz-failover-hana-failover-check-sr-state}

1. Log in as user `prdadm` to node `prd-db-1`.
1. Check the HANA System Replication status.

   ```shell
   python $DIR_INSTANCE/exe/python_support/systemReplicationStatus.py
   ```
   {: screen}

Verify the current system state:
- The primary SAP HANA database is running on `prd-db-1`.
- The secondary SAP HANA database is running on `prd-db-2`.
- SAP HANA System Replication is active and in sync.

### Simulating an outage of the SAP HANA primary
{: #ha-manual-mz-failover-hana-failover-kill-node-1}

1. Log in as user `root` to `prd-db-1`.
1. Run the following command to trigger an immediate system shutdown.

   ```shell
   echo o > /proc/sysrq-trigger
   ```
   {: pre}

### Failing over to the SAP HANA secondary
{: #ha-manual-mz-failover-hana-failover-to-node-2}

#### Disabling the DB server route in workspace `workspace-1`
{: #ha-manual-mz-failover-hana-failover-disable-route}

1. Log in to [Power Virtual Server workspaces](/power/workspaces){: external}.
1. Click **workspace-1**, and then click **View virtual servers**.
1. In the left pane, click **Routes**.
1. Select **prd-db-vip-route-zone1**, and then click **Edit details**.
1. Toggle **Status** to *Disabled*.
1. Click **Edit route** to save the changes.

#### Performing a database takeover on the SAP HANA secondary
{: #ha-manual-mz-failover-hana-failover-sr-takeover}

1. Log in as user `prdadm` to node `prd-db-2`.
1. Run the following command to perform a HANA database takeover.

   ```shell
   hdbnsutil -sr_takeover
   ```
   {: pre}

1. Verify that the system is now running as a primary.

   ```shell
   hdbnsutil -sr_state
   ```
   {: pre}

#### Adding the overlay IP address to the node
{: #ha-manual-mz-failover-hana-failover-add-ovl-ip}

1. Log in as user `root` to node `prd-db-2`.
1. Run the following command to add the overlay IP address to the network adapter `env2`.

   ```shell
   ip address add 192.168.12.102/32 dev env2
   ```
   {: screen}

#### Enabling the DB server route in workspace `workspace-2`
{: #ha-manual-mz-failover-hana-failover-enable-route}

1. Log in to [Power Virtual Server workspaces](/power/workspaces){: external}.
1. Click **workspace-2**, and then click **View virtual servers**.
1. In the left pane, click **Routes**.
1. Select *prd-db-vip-route-zone2*, and then click **Edit details**.
1. Toggle **Status** to *Enabled*.
1. Click **Edit route** to save the changes.

### Verifying the SAP system status
{: #ha-manual-mz-failover-hana-failover-check-as-status}

Open an *SAP GUI* session to the application server and verify the system status.

## Testing manual failover by simulating an outage of the SAP ASCS instance
{: #ha-manual-mz-failover-ascs-failover}

### Simulating an outage of the ASCS instance
{: #ha-manual-mz-failover-ascs-kill}

1. Log in as user `root` to `prd-cs-1`.
1. Run the following command to trigger an immediate system shutdown.

   ```shell
   echo o > /proc/sysrq-trigger
   ```
   {: pre}

### Performing an ASCS failover to the ERS instance
{: #ha-manual-mz-failover-ascs-failover-to-node-2}

#### Disabling the ASCS route in workspace `workspace-1`
{: #ha-manual-mz-failover-ascs-failover-disable-route}

Disable the ASCS route in workspace `workspace-1`.

1. Log in to [Power Virtual Server workspaces](/power/workspaces){: external}.
1. Click **workspace-1**, and then click **View virtual servers**.
1. In the left pane, click *Routes*.
1. Select *prd-cs-vip-route-zone1*, and then click **Edit details**.
1. Toggle the **Status** to *Disabled*.
1. Click **Edit route** to save the changes.

#### Adding the ASCS overlay IP address to the ERS node
{: #ha-manual-mz-failover-ascs-failover-add-ip}

1. Log in as user `root` to `prd-cs-2`.
1. Run the following command to add the virtual IP address to the network adapter `env2`.

   ```shell
   ip address add 192.168.21.42/32 dev env2
   ```
   {: screen}

#### Enabling the ASCS route in workspace `workspace-2`
{: #ha-manual-mz-failover-ascs-failover-enable-route}

1. Log in to [Power Virtual Server workspaces](/power/workspaces){: external}.
1. Click **workspace-2**, and then click **View virtual servers**.
1. In the left pane, click **Routes**.
1. Select *prd-cs-vip-route-zone2*, and then click **Edit details**.
1. Toggle **Status** to *Enabled*.
1. Click **Edit route** to save the changes.

#### Starting the ASCS instance agent on `prd-cs-2`
{: #ha-manual-mz-failover-ascs-failover-start-instance-agent}

1. Log in as user `root` to `prd-cs-2`.
1. Run the following command to start the sytemd service.

    ```shell
    systemctl start SAPPRD_11.service
    ```
    {: screen}

1. Log in as user `prdadm` to `prd-cs-2`.
1. Run the following command to start the instance agent.

   ```shell
   sapcontrol -nr 11 -function StartService PRD
   ```
   {: screen}

#### Starting the ASCS instance on `prd-cs-2`
{: #ha-manual-mz-failover-ascs-failover-start-ascs-instance}

1. Run the following command to start the ASCS instance.

   ```shell
   sapcontrol -nr 11 -function Start
   ```
   {: screen}

1. Run the following command to verify that the ASCS instance is running.

   ```shell
   sapcontrol -nr 11 -function GetProcessList
   ```
   {: screen}

   ```shell
   GetProcessList
   OK
   name, description, dispstatus, textstatus, starttime, elapsedtime, pid
   msg_server, MessageServer, GREEN, Running, 2025 10 14 09:22:28, 8:58:34, 2106825
   enq_server, Enqueue Server 2, GREEN, Running, 2025 10 14 09:22:28, 8:58:34, 2106826
   ```
   {: screen}

#### Verifying the SAP system status
{: #ha-manual-mz-failover-ascs-failover-verify}

Open an SAP GUI* session to the application server and verify the system status.
