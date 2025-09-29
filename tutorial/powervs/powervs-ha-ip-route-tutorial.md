---
copyright:
   years: 2025
lastupdated: "2025-09-29"
keywords: SAP, {{site.data.keyword.cloud_notm}}, {{site.data.keyword.ibm_cloud_sap}},
subcollection: sap
content-type: tutorial
services: power-iaas, transit-gateway, sap
account-plan: paid
completion-time: 2h
---

{{site.data.keyword.attribute-definition-list}}


# Creating a static route and an overlay IP address for a virtual server instance in IBM {{site.data.keyword.powerSys_notm}}
{: #ha-rt-ovl-ip-intro}
{: toc-content-type="tutorial"}
{: toc-services="power-iaas, transit-gateway, sap"}
{: toc-completion-time="2h"}

This tutorial might incur costs.
Use the [Cost Estimator](/estimator) to generate a cost estimate based on your projected usage.
{: tip}

In this tutorial, you learn how to configure a static route and assign an overlay IP address to a virtual server instance within an IBM {{site.data.keyword.powerSys_notm}} workspace.

A static route defines a fixed network path for network packets to reach a specific destination IP address.
In this scenario, the destination is an overlay IP address that does not belong to any of the CIDR ranges associated with the workspace's subnets.
You configure the next hop of the static route to the primary IP of a network adapter.

Then you configure the overlay IP address as IP alias address (secondary IP address) on the network adapter of the virtual server instance.

Once the static route is enabled, any packet destined for the overlay IP is directed to the next hop.
This leads the packet to the primary IP of the network adapter, effectively routing it to the virtual server instance.

The following architecture overview diagram illustrates this scenario.

![Architecture overview diagram](../../images/powervs-ha-static-route.svg){: figure caption="Static routes architecture"}

This tutorial covers the following aspects:

1. [Identifying the IP addresses for the static route](#ha-rt-ovl-ip-identify)
1. [Creating the static route in the workspace](#ha-rt-ovl-ip-create-rt)
1. [Configuring the overlay IP address on the virtual server instance](#ha-rt-ovl-ip-alias)

## Before you begin
{: #ha-rt-ovl-ip-prereqs}

- Create an IBM {{site.data.keyword.powerSys_notm}} workspace in the IBM Cloud region.

- Create a Transit Gateway and attach the IBM {{site.data.keyword.powerSys_notm}} workspace to the Transit Gateway.

- Check the user permissions.

   Make sure that your user account has permissions to create and manage static routes in the workspace.

- Use or create an SSH key to connect to the virtual server instances.

   If you don't have an SSH key, see [Getting started with SSH keys](/docs/vpc?topic=vpc-ssh-keys).

- Provision a virtual server instance running OS Red Had Enterprise Linux in the IBM {{site.data.keyword.powerSys_notm}} workspace. [RHEL]{: tag-red}


## Identifying the IP addresses for the static route
{: #ha-rt-ovl-ip-identify}
{: step}

A static route consists of two elements:
- Destination address: The IP address to which network packets are intended to be delivered.
- Next hop address: The IP address of the device that receives and forwards the packets toward the destination.

### Choosing the IP overlay address
{: #ha-rt-ovl-ip-select}

The overlay IP is an IP address that does not belong to any of the CIDR subnet blocks in the environment.
It is not assigned to a network interface during deployment of the virtual server instance, and must be manually configured later as an IP alias address (secondary IP) on the instance’s network adapter.

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

### Identifying the next hop IP address
{: #ha-rt-ovl-ip-next-hop}

Follow these steps to determine the next hop IP address for your static route configuration:

1. Open the {{site.data.keyword.powerSys_notm}} user interface in [IBM Cloud](https://cloud.ibm.com/power/overview){: external}.
1. Click **Workspaces** in the left navigation menu.
1. Select a workspace in which you want to create the static route.
   The "Workspace details" panel is displayed.
1. Click **View virtual servers**.
   The list of virtual server instances is displayed.
1. Identify your virtual server instance and its IP address.
   You need to enter that IP address as **Next Hop** in the route.
   In the example, the next hop IP address is `10.10.10.10`.

## Creating the static route in the workspace
{: #ha-rt-ovl-ip-create-rt}
{: step}

To create a static route in your {{site.data.keyword.powerSys_notm}} workspace, follow these steps:

1. Open the {{site.data.keyword.powerSys_notm}} user interface in [IBM Cloud](https://cloud.ibm.com/power/overview){: external}.
1. Click **Workspaces** in the left navigation menu.
1. Select a workspace in which you want to create the static route.
   The "Workspace details" panel is displayed.
1. Click **View virtual servers**.
1. In the navigation pane, click **Networking** > **Routes**.
   The **Static routes** page lists the existing static routes (if any).
1. Click **Create static route** to create a new route.
1. In the "Create static route" panel
   1. Enter a name for the static route in the **Name** field.
      *Example:* `test-route`.
   1. Optionally, enter user tags in the **User tags (optional)** field.
   1. In the **Destination** field, enter a valid IP address.
      The destination IP address must not belong to any of the CIDR blocks of the subnets in the scenario.
      *Example:* `10.100.100.100`.
   1. In the **Next hop** field, enter a valid IP address.
      The next hop IP address must
      - Belong to a CIDR ranges of a subnet in the workspace.
      - Match an primary IP address of a network adapter on the target virtual server instance.

      *Example:* `10.10.10.10`.

   1. **Advertise** and **Status**: Leave both switches set to **Enabled** (default).

      The **Advertise** switch controls whether the static route is propagated outside of the workspace to the Power Edge Router (PER).
      If **Advertise** is disabled, the route remains internal and is not visible to external network connections.

      The **Status** switch determines whether the static route is active within the network fabric.
      If **Status** is set to **Disabled**, the route is not used — even if **Advertise** is enabled.

   1. Click **Create route**.

## Configuring the overlay IP address on the virtual server instance
{: #ha-rt-ovl-ip-alias}
{: step}

[RHEL]{: tag-red}:

Login as user `root` to the virtual server instance in IBM {{site.data.keyword.powerSys_notm}}.
Manage the network configuration with the `nmcli` tool.

1. Display the list of available network connections.

   ```sh
   nmcli connection show
   ```
   {: pre}

1. Identify the target connection.

   Locate the network interface that has the primary address corresponding to the `next hop` in the route.
   Replace `<conn>` with your connection name.

   ```sh
   nmcli connection show <conn>
   ```
   {: pre}

   See the following example.

   ```sh
   # nmcli con show "System env2" | grep IP4.ADDRESS
   IP4.ADDRESS[1]:                         10.10.10.10/24
   ```
   {: codeblock}


1. Add the IP overlay IP address to the target connection

   Use `/32` for the network mask.

   ```sh
   nmcli connection modify <conn> +ipv4.ADDRESS <overlay IP>/32
   ```
   {: pre}

   See the following example.

   ```sh
   # nmcli connection modify "System env2" +ipv4.ADDRESS "10.100.100.100/32"
   ```
   {: codeblock}

1. Activate the changed connection

   ```sh
   nmcli connection up <conn>
   ```
   {: pre}

   See the following example.

   ```sh
   # nmcli con up "System env2"
   Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/5)
   ```
   {: codeblock}

1. Verify the connection

   ```sh
   nmcli connection show <conn> | grep IP4.ADDRESS
   ```
   {: pre}

   See the following example.

   ```sh
   # nmcli con show "System env2" | grep IP4.ADDRESS
   IP4.ADDRESS[1]:                         10.100.100.100/32
   IP4.ADDRESS[2]:                         10.10.10.10/24
   ```
   {: codeblock}
