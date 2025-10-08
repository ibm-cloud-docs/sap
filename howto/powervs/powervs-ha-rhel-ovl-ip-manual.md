---
copyright:
  years: 2025
lastupdated: "2025-10-08"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# PowerVS multi-zone overlay IP setup and manual failover (non-pacemaker)
{: #ha-rt-sap-ip}

This document provides guidance on the following topics:
1. [Creating a static route and an overlay IP address for an IBM PowerVS instance](#ha-rt-sap-ip-intro)
1. [Preparing the PowerVS Multi-Zone HA environment](#ha-rt-sap-ip-prep)
1. [Failover the HANA database](#ha-rt-sap-ip-hana-failover)
1. [Failover the ASCS instance](#ha-rt-sap-ip-ascs-failover)

## Creating a static route and an overlay IP address for a virtual server instance in IBM {{site.data.keyword.powerSys_notm}}
{: #ha-rt-sap-ip-intro}

This section shows how to configure a static route and assign an overlay IP address to a virtual server instance within an IBM PowerVS workspace.

A static route defines a fixed path for network packets to reach a specific destination IP address.
In this scenario, the destination is an overlay IP address—an address that lies outside the CIDR ranges of the workspace's subnets.

To establish the route:
- The overlay IP address is added as an IP alias (secondary IP) on the same network adapter of the virtual server instance.
- You configure the next hop to point to the primary IP address of a network adapter.

Once the static route is enabled, any packet destined for the overlay IP is directed to the next hop.
This leads the packet to the primary IP of the network adapter, effectively routing it to the virtual server instance.

The following architecture overview diagram illustrates this scenario.

![Architecture overview diagram](../../images/powervs-ha-static-route.svg){: figure caption="Static routes architecture"}

### Before you begin
{: #ha-rt-sap-ip-prereqs}

- Create an IBM PowerVS workspace in the IBM Cloud region.
- Create a Transit Gateway and attach the IBM PowerVS workspace to the Transit Gateway.
- Check the user permissions.
   Make sure that your user account has permissions to create and manage static routes in the workspace.

- Use or create an SSH key to connect to the virtual server instances.
   If you don't have an SSH key, see [Getting started with SSH keys](/docs/vpc?topic=vpc-ssh-keys).

- Provision a virtual server instance running Red Had Enterprise Linux in the PowerVS workspace. [RHEL]{: tag-red}

### Identifying the IP addresses for the static route
{: #ha-rt-sap-ip-identify}
{: step}

A static route consists of two elements:
- Destination address: The IP address to which network packets are intended to be delivered.
- Next hop address: The IP address of the device that receives and forwards the packets toward the destination.

#### Choosing the IP overlay address
{: #ha-rt-sap-ip-select}

An overlay IP address is an IP that lies outside the CIDR blocks of any subnet in the PowerVS environment.
It is not assigned to a network interface during the initial deployment of a virtual server instance and must be manually configured later as an IP alias (secondary IP) on the instance’s network adapter.

In the static route configuration:

- The destination is set to the overlay IP address.
- The next hop is set to an IP address of the virtual server instance.

This setup ensures that network packets destined for the overlay IP are routed correctly to the intended virtual server instance.

You can associate an application with an overlay IP, and later relocate both the application and the IP to another virtual server instance.
By updating the static route to reflect the new next hop, clients can continue accessing the application without requiring any reconfiguration.

When selecting an overlay IP address, ensure that it does not overlap with any CIDR range used by subnets in your environment.
In the example, the overlay IP address is set to `10.100.100.100/32`.

The static route destination is limited to a single IP address (CIDR `/32`).
{: note}

#### Identifying the next hop IP address
{: #ha-rt-sap-ip-next-hop}

Follow these steps to determine the next hop IP address for your static route configuration:

1. Open the PowerVS user interface in [IBM Cloud](https://cloud.ibm.com/power/overview){: external}.
1. Click **Workspaces** in the left navigation menu.
1. Select a workspace in which you want to create the static route.
   The "Workspace details" panel is displayed.
1. Click **View virtual servers**.
   The list of virtual server instances is displayed.
1. Identify your virtual server instance and its IP address.
   You need to enter that IP address as **Next Hop** in the route.
   In the example, the next hop IP address is `10.10.10.10/24`.

### Creating the static route in the workspace
{: #ha-rt-sap-ip-create-rt}
{: step}

To create a static route in your PowerVS workspace, follow these steps:

1. Open the PowerVS user interface in [IBM Cloud](https://cloud.ibm.com/power/overview){: external}.
1. Click **Workspaces** in the left navigation menu.
1. Select a workspace in which you want to create the static route.
   The "Workspace details" panel is displayed.
1. Click **View virtual servers**.
1. In the navigation pane, click **Networking** > **Routes**.
   The **Static routes** page displays any existing static routes.
1. Click **Create static route** to create a new route.
1. In the "Create static route" panel
   1. Enter a name for the static route in the **Name** field.
      *Example:* `test-route`.
   1. Optionally, enter user tags in the **User tags (optional)** field.
   1. In the **Destination** field, enter a valid IP address.
      This IP must not belong to any CIDR block of the workspace’s subnets.
      *Example*: `10.100.100.100`.

   1. In the **Next hop** field, enter a valid IP address.
      The next hop IP address must
      - Belong to a subnet CIDR range in the workspace.
      - Match an IP address of a network adapter on the target virtual server instance.

      *Example*: `10.10.10.10`.

   1. **Advertise** and **Status**: Leave both switches set to **Enabled** (default).
      The **Advertise** switch controls whether the static route is propagated outside of the workspace to the Power Edge Router (PER).
      If **Advertise** is disabled, the route remains internal and is not visible to external network connections.

      The **Status** switch determines whether the static route is active within the network fabric.
      If **Status** is set to **Disabled**, the route is not used—even if **Advertise** is enabled.

   1. Click **Create route**.

### Preparing the {{site.data.keyword.powerSys_notm}} multi-zone region environment
{: #ha-rt-sap-ip-prep}

The table below details the IP addresses utilized in this example configuration.

| Hostname | Role         | Overlay IP   | Next hop eu-de-1 | Next hop eu-de-2 |
|----------|--------------|--------------|------------------|------------------|
| prd-cs-1 | ASCS         | 10.40.21.42  | 10.40.20.54      | 10.40.10.8       |
| prd-cs-2 | ERS          | 10.40.21.46  | 10.40.20.54      | 10.40.10.8       |
| prd-db-1 | HANA Primary | 10.40.12.102 | 10.40.20.115      | 10.40.10.25      |
{: caption="Sample IP addresses"}

#### Adding the overlay IP address `prd-cs` to `prd-cs-1`
{: #ha-rt-sap-prep-ip-cs}

1. Login as user `root` to `prd-cs-1`.
1. Add the overlay IP address to adapter `env2`.

   ```shell
   ip address add 10.40.21.42/32 dev env2
   ```

#### Adding the overlay IP address `prd-er` to `prd-cs-2`
{: #ha-rt-sap-prep-ip-er}

1. Login as user `root` to `prd-cs-2`.
1. Add the overlay IP address to adapter `env2`.

   ```shell
   ip address add 10.40.21.46/32 dev env2
   ```

#### Adding the overlay IP address `prd-db` to `prd-db-1`
{: #ha-rt-sap-prep-ip-db}

1. Login as user `root` to `prd-db-1`.
1. Add the overlay IP address to adapter `env2`

   ```shell
   ip address add 10.40.12.102/32 dev env2
   ```

#### Creating the static routes for zone 1
{: #ha-rt-sap-create-routes-zone1}

1. Login to [Power Virtual Server workspaces](https://cloud.ibm.com/power/workspaces).
1. Select **sapha-eu-de-1-power-workspace** and click **View virtual servers**.
1. Click on **Routes** in the left pane.
1. Click **Create static route**

    1. Enter *prd-db-vip-route-zone1* as **Name**.
    1. Enter *10.40.12.102* as **Destination**.
    1. Enter *10.40.20.115* as **Next hop**
    1. Keep **Advertise** and **Status** as enabled, then click **Create route**.

1. Click **Create static route**.

    1. Enter *prd-cs-vip-route-zone1* as **Name**.
    1. Enter *10.40.21.42* as **Destination"**.
    1. Enter *10.40.20.54* as **Next hop**.
    1. Keep **Advertise** and **Status** as enabled, then click **Create route**.

1. Click **Create static route**.

    1. Enter *prd-er-vip-route-zone1* as **Name**.
    1. Enter *10.40.21.46* as **Destination"**.
    1. Enter *10.40.20.54* as **Next hop**
    1. Keep **Advertise** as enabled, set **Status** to disabled, then click **Create route**.

#### Creating the routes for zone 2
{: #ha-rt-sap-create-routes-zone2}

1. Login to [Power Virtual Server workspaces](https://cloud.ibm.com/power/workspaces).
1. Select *sapha-eu-de-2-power-workspace*, and click **View virtual servers**
1. Click on **Routes** in the left pane.
1. Click **Create static route**.

    1. Enter *prd-er-vip-route-zone2* as **Name**.
    1. Enter *10.40.21.46* as **Destination"**.
    1. Enter *10.40.10.8* as **Next hop**
    1. Keep **Advertise** and **Status** as enabled, then click **Create route**.

1. Click **Create static route**.

    1. Enter *prd-cs-vip-route-zone2* as **Name**.
    1. Enter *10.40.21.42* as **Destination"**.
    1. Enter *10.40.10.8* as **Next hop**.
    1. Keep **Advertise** as enabled, set **Status** to disabled, then click **Create route**.

1. Click **Create static route**.

    1. Enter *prd-db-vip-route-zone2* as **Name**.
    1. Enter *10.40.12.102* as **Destination"**.
    1. Enter *10.40.10.25* as **Next hop**.
    1. Keep **Advertise** as enabled, set **Status** to disabled, then click **Create route**.

### Install the SAP systems
{: #ha-rt-sap-install-sap-systems}

This section is outside the scope of this document.

### Starting the SAP systems
{: #ha-rt-sap-start-sap-systems}

This section is outside the scope of this document.

### Registering the instance agents on `prd-cs-1`
{: #ha-rt-sap-register-instance-agents-1}

1. Login as user `root` to `prd-cs-1`.
1. Set the `LD_LIBRARY_PATH` environment variable to include the instance executable directory.

   ```shell
   export LD_LIBRARY_PATH=/usr/sap/PRD/ASCS11/exe
   ```

1. Check the status of the `systemctl` service.

   ```shell
   systemctl status SAPPRD_11.service
   ```

1. Start the service.

   ```shell
   systemctl start SAPPRD_11.service
   ```

1. Disable the service.

   ```shell
   systemctl disable SAPPRD_11.service
   ```

1. Register the ERS instance.

   ```shell
   /usr/sap/PRD/ASCS11/exe/sapstartsrv -reg pf=/usr/sap/PRD/SYS/profile/PRD_ERS22_prd-er
   ```

1. Check the status of the `systemctl` service.

   ```shell
   systemctl status SAPPRD_22.service
   ```

1. Stop and disable the service.

   ```shell
   systemctl disable --now SAPPRD_22.service
   ```

### Registering the instance agents on `prd-cs-2`
{: #ha-rt-sap-register-instance-agents-2}

1. Login as user `root` to `prd-cs-2`.
1. Set the `LD_LIBRARY_PATH` environment variable to include the instance executable directory.

   ```shell
   export LD_LIBRARY_PATH=/usr/sap/PRD/ERS22/exe
   ```

1. Check the status of the `systemctl` service.

    ```shell
    systemctl status SAPPRD_22.service
    ```

1. Start the service.

   ```shell
   systemctl start SAPPRD_22.service
   ```

1. Disable the service.

   ```shell
   systemctl disable SAPPRD_22.service
   ```

1. Register the ASCS instance.

   ```shell
   /usr/sap/PRD/ERS22/exe/sapstartsrv -reg pf=/usr/sap/PRD/SYS/profile/PRD_ASCS11_prd-cs
   ```

1. Check the status of the  `systemctl` service.

    ```shell
    systemctl status SAPPRD_11.service
    ```

1. Stop and disable the service.

   ```shell
   systemctl disable --now SAPPRD_11.service
   ```

### Starting the ASCS instance on `prd-cs-1`
{: #ha-rt-sap-start-ascs}

1. Login as user `prdadm` to `prd-cs-1`.
1. Start the instance agent.

   ```shell
   sapcontrol -nr 11 -function StartService PRD
   ```

1. Start the ASCS instance.

   ```shell
   sapcontrol -nr 11 -function Start
   ```

### Starting the ERS instance on `prd-cs-2`
{: #ha-rt-sap-start-ers}

1. Login as user `prdadm` to `prd-cs-2`.
1. Start the instance agent.

   ```shell
   sapcontrol -nr 22 -function StartService PRD
   ```

1. Start the ERS instance.

   ```shell
   sapcontrol -nr 22 -function Start
   ```

### Starting the SAP HANA databases
{: #ha-rt-sap-start-hana}

#### Starting the HANA database on `prd-db-1`
{: #ha-rt-sap-start-hana-primary}

1. Login as user `prdadm` to `prd-db-1`.
1. Start the SAP HANA System.

   ```shell
   HDB start
   ```

#### Starting the HANA database on `prd-db-2`
{: #ha-rt-sap-start-hana-secondary}

1. Login as user `prdadm` to `prd-db-2`.
1. Start the SAP HANA System.

   ```shell
   HDB start
   ```

#### Verifying System Replication status on the primary `prd-db-1`
{: #ha-rt-sap-start-hana-check-sr}

1. Login as user `prdadm` to `prd-db-1`.
1. Check the HANA System Replication status.

   ```shell
   python $DIR_INSTANCE/exe/python_support/systemReplicationStatus.py
   ```

### Starting the SAP application server instance
{: #ha-rt-sap-start-sap-as}

1. Login as user `prdadm` to `prd-as-1`.
1. Start the dialog instance.

   ```shell
   sapcontrol -nr 00 -function Start
   ```

### Logon to the SAP system
{: #ha-rt-sap-start-sap-as}

Start a *sapgui* session to logon to the application server and verify the status.

## Simulating an outage of the source SAP HANA system and failover
{: #ha-rt-sap-ip-hana-failover}

### Verifying the System Replication status on the primary `prd-db-1`
{: #ha-rt-sap-ip-hana-failover-check-sr-state}

1. Login as user `prdadm` to node `prd-db-1`.
1. Check the HANA System Replication status.

   ```shell
   python $DIR_INSTANCE/exe/python_support/systemReplicationStatus.py
   ```

### Performing a hard stop of node `prd-db-1` to simulate an outage
{: #ha-rt-sap-ip-hana-failover-kill-node-1}

1. Login as user `root` to `prd-db-1`.
1. Run `echo o > /proc/sysrq-trigger` to trigger an immediate offline system request.

### Failing over to the target system
{: #ha-rt-sap-ip-hana-failover-to-node-2}

#### Disabling the route for workspace `sapha-eu-de-1-power-workspace`
{: #ha-rt-sap-ip-hana-failover-disable-route}

1. Login to [Power Virtual Server workspaces](https://cloud.ibm.com/power/workspaces).
1. Click *sapha-eu-de-1-power-workspace*, then click **View virtual servers**
1. Click **Routes** in the left pane.
1. Select *prd-db-vip-route-zone1* and click **Edit details**.
1. Toggle **Status** to *Disabled*.
1. Click **Edit route** to save the settings.

#### Performing a HANA database takeover on `prd-db-2`
{: #ha-rt-sap-ip-hana-failover-sr-takeover}

1. Login as user `prdadm` to node `prd-db-2`.
1. Perform a HANA database takeover.

   ```shell
   hdbnsutil -sr_takeover
   ```

1. Verify that the system runs as primary.

   ```shell
   hdbnsutil -sr_state
   ```

#### Adding the overlay IP address to the node
{: #ha-rt-sap-ip-hana-failover-add-ovl-ip}

1. Login as user `root` to node `prd-db-2`.
1. Add the overlay IP address to adapter `env2`.

   ```shell
   ip address add 10.40.12.102/32 dev env2
   ```

#### Enabling the route for workspace `sapha-eu-de-2-power-workspace`
{: #ha-rt-sap-ip-hana-failover-enable-route}

1. Login to [Power Virtual Server workspaces](https://cloud.ibm.com/power/workspaces).
1. Click *sapha-eu-de-2-power-workspace*, then click **View virtual servers**.
1. Click **Routes** in the left pane.
1. Select *prd-db-vip-route-zone2* and click **Edit details**
1. Toggle **Status** to *Enabled*.
1. Click **Edit route** to save the settings.

### Logon to the SAP system
{: #ha-rt-sap-ip-hana-failover-check-as-status}

Open a *sapgui* session to the application server and verify the status.

## Failover the ASCS instance
{: #ha-rt-sap-ip-ascs-failover}

### Simulate outage of the ASCS instance
{: #ha-rt-sap-ip-ascs-kill}

1. Perform a hard stop of node `prd-cs-1` to simulate an outage.

   1. Login as user `root` to `prd-cs-1`.
   1. Run `echo o > /proc/sysrq-trigger` to trigger an immediate offline system request.

1. Disable the route for workspace `sapha-eu-de-1-power-workspace`.

   1. Login to [Power Virtual Server workspaces](https://cloud.ibm.com/power/workspaces)
   1. Click *sapha-eu-de-1-power-workspace*, then click **View virtual servers**.
   1. Click on *Routes* in the left pane.
   1. Select *prd-cs-vip-route-zone1*, then click **Edit details**.
   1. Toggle the **Status** to *Disabled*.
   1. Click **Edit route** to save the settings.

### Adding the overlay IP address to the target node
{: #ha-rt-sap-ip-ascs-failover-add-ip}

1. Login as user `root` to `prd-cs-2`.
1. Add the virtual IP address to adapter `env2`.

   ```shell
   ip address add 10.40.21.42/32 dev env2
   ```

### Enabling the route for zone 2
{: #ha-rt-sap-ip-ascs-failover-enable-route}

1. Login to [Power Virtual Server workspaces](https://cloud.ibm.com/power/workspaces).
1. Click *sapha-eu-de-2-power-workspace*, then click **View virtual servers**.
1. Click on **Routes** in the left pane.
1. Select *prd-cs-vip-route-zone2* and click **Edit details**
1. Toggle **Status** to *Enabled*.
1. Click **Edit route** to save the settings.

### Starting the ASCS instance agent on  `prd-cs-2`
{: #ha-rt-sap-ip-ascs-failover-start-instance-agent}

1. Login as user `root` to node `prd-cs-2`.
1. Start the `systemctl` service.

    ```shell
    systemctl start SAPPRD_11.service
    ```

1. Login as user `prdadm` to node `prd-cs-2`.
1. Start the instance agent.

   ```shell
   sapcontrol -nr 11 -function StartService PRD
   ```

### Starting the ASCS instance on `prd-cs-2`
{: #ha-rt-sap-ip-ascs-failover-start-ascs-instance}

1. Start the ASCS instance.

   ```shell
   sapcontrol -nr 11 -function Start
   ```

1. Verify that the ASCS instance is running.

   ```shell
   sapcontrol -nr 11 -function GetProcessList
   ```

### Logon to the SAP system
{: #ha-rt-sap-ip-ascs-failover-verify}

Open a *sagpui* session to the application server and verify the status.
