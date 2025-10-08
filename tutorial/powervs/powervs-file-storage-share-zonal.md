---
copyright:
   years: 2025
lastupdated: "2025-10-08"
keywords: SAP, {{site.data.keyword.cloud_notm}}, {{site.data.keyword.ibm_cloud_sap}}, NFS, File Storage Share, Network Load Balancer
subcollection: sap
content-type: tutorial
services: vpc, transit-gateway, power-iaas, sap
account-plan: paid
completion-time: 2h
---

{{site.data.keyword.attribute-definition-list}}


# Accessing VPC zonal file storage shares from IBM {{site.data.keyword.powerSys_notm}} Instances
{: #nfs-zonal-powervs-intro}
{: toc-content-type="tutorial"}
{: toc-services="vpc, transit-gateway, power-iaas, sap"}
{: toc-completion-time="2h"}

This tutorial might incur costs.
Use the [Cost Estimator](/estimator) to generate a cost estimate based on your projected usage.
{: tip}

In this tutorial, you learn how to mount a file share on an IBM {{site.data.keyword.powerSys_notm}} server instance.
You cannot directly mount a file storage share on IBM {{site.data.keyword.powerSys_notm}} instances and must instead use a path through a network load balancer (NLB).
You create a file storage share and a mount target in IBM VPC.
You create a network load balancer with routing mode, and a route table in IBM VPC.
Then, you mount the file storage share on the virtual server instance in IBM {{site.data.keyword.powerSys_notm}}.
{: shortdesc}

The following architecture overview diagram illustrates this scenario.

The virtual server instance in IBM {{site.data.keyword.powerSys_notm}} sends a request through a transit gateway to the file storage share.
According to the rule in the routing table of the VPC, the network traffic to the file storage share is directed to the network load balancer.
The network load balancer has *Routing_mode* enabled.
It bypasses a back-end pool and sends requests directly to the destination IP address.
The file storage share responds and the response is sent directly to the virtual server instance in IBM {{site.data.keyword.powerSys_notm}}.
The network load balancer (NLB) with routing mode has two IP addresses (active and standby).
When a failover occurs, the route mode updates all routing rules that are created for the VPC with a `next_hop` of the standby IP.
Both the active IP and the standby IP are used during the lifetime of an NLB with route mode.

![Architecture overview diagram](../../images/nfs-zonal-powervs.svg){: figure caption="A diagram that shows the architecture for accessing File Storage for VPC."}

## Before you begin
{: #nfs-zonal-powervs-prereqs}

{{site.data.content.nfs-zonal-prereqs}}


## Creating a security group to allow NFS V4 traffic
{: #nfs-zonal-powervs-create-sg}
{: step}

{{site.data.content.nfs-create-sg}}

## Provisioning file storage with zonal availability
{: #nfs-zonal-powervs-create-fs-share}
{: step}

The availability cannot be modified after provisioning.{: important}

The encryption setting of a mount target cannot be modified after it has been created. To change the encryption, you must delete the existing mount target and recreate it. This process does not affect the underlying data, and no data will be lost.{: important}

{{site.data.content.nfs-create-zonal-share}}

## Gathering the file storage IP address and mount path information
{: #nfs-zonal-powervs-get-mount-path}
{: step}

{{site.data.content.nfs-get-mount-path}}

Later, the **Destination** parameter in the VPC route entry is set to the **Primary IP** of the mount target.
The **Mount path** parameter is used as an argument to the `mount` command on the IBM {{site.data.keyword.powerSys_notm}} instance.

## Creating the private network load balancer with routing mode
{: #nfs-zonal-powervs-create-nlb-with-routing-mode}
{: step}

### Creating the service-to-service authentication policy
{: #nfs-zonal-powervs-create-auth-policy}

{{site.data.content.nfs-create-auth-policy}}

### Creating the network load balancer
{: #nfs-zonal-powervs-create-nlb}

1. Go to [Load balancers for VPC](/infrastructure/network/loadBalancers) and click **Create**.
1. Select **Network Load Balancer (NLB)** as the **Load balancer type**.
1. In the **Location** section, select the same **Geography** and **Region** that is used for the virtual private cloud.
1. Enter `nfs-server-nlb` in the **Name** field.
1. Select the same **Resource group** as the VPC resource group.
1. Select your VPC in the **Virtual private cloud** list.
1. Select the **Subnet**.
1. Check **Private** in the **Type** section.
1. Set **Routing mode** to **On** to create a network load balancer with routing mode.
1. In the **Back-end pools** section, click **Create pool**.
   Set the parameters to the following values.
   - **Name**:  `nfs-server-fwd-pool`
   - **Pool protocol**: `TCP`
   - **Session stickiness**: `None`
   - **Method**: `Round robin`
   - Click **Create**.

1. In the **Front-end listeners** section, click **Create listener**.
   Select your **Back-end pool** and click **Save**.
1. In the **Security Groups** sections, check the `nfs-server-sg` security group, and clear the default security group.
1. Click **Create load balancer** to provision the load balancer.

As part of the process, you create a back-end pool.
However, you cannot define the back-end pool `Failsafe policy` directly, and it must be updated in the next step.
{: important}

### Updating the network load balancer failsafe policy
{: #nfs-zonal-powervs-upd-nlb-failsafe-policy}

Update the `Failsafe policy` for the `nfs-server-fwd-pool` back-end pool.
The network load balancer then bypasses the back-end pool and sends requests directly to the destination IPs.

1. Go to [Load balancers for VPC](/infrastructure/network/loadBalancers).
1. Click the load balancer `nfs-server-nlb`.
1. Click the **Back-end pools** tab and select the pool `nfs-server-fwd-pool`.
1. Click `nfs-server-fwd-pool` and then `Edit`.
1. In the `Failsave policy` section, select `Bypass` as the **Action**.
1. Click `Save`.

### Collecting the private IP addresses of the load balancer
{: #nfs-zonal-powervs-get-nlb-ip-addresses}

1. Go to [Load balancers for VPC](/infrastructure/network/loadBalancers).
1. Click `nfs-server-nlb`.
1. In the **Load balancer details - Private IPs** section, make a note of the first IP address entry in the list.

Later, the **Next hop** parameter in the VPC route entry is set to the active **Private IP** address of the load balancer.

## Creating a routing table and routes for VPC
{: #nfs-zonal-powervs-provision-create-table-and-routes}
{: step}

### Creating a routing table
{: #nfs-zonal-powervs-create-routing-table}

{{site.data.content.nfs-create-routing-table}}

### Updating the routing table
{: #nfs-regional-powervs-update-routing-table}

{{site.data.content.nfs-update-routing-table}}

### Creating a route
{: #nfs-zonal-powervs-create-route}

1. Go to [Routing tables for VPC](/infrastructure/network/routingTables).
1. Click the name `nfs-server-routing`.
1. Click **Create**.
1. Select the zone for your route in the **Zone** field.
1. Enter `nfs-server` in the **Name** field.
1. Using CIDR notation, enter the primary IP address of the file share as **Destination CIDR**.
1. Select `Deliver` as the **Action**.
1. Enter the `IP address of the network load balancer` as the **Next hop (IP address)**.
1. Click **Save** to add the route to the table.

## Mounting the file share on the IBM {{site.data.keyword.powerSys_notm}} instance
{: #nfs-zonal-powervs-mount-file-share}
{: step}

Log on as the `root` user to the server instance in IBM {{site.data.keyword.powerSys_notm}} where you want to mount the file share.

{{../../_include-segments/tutorial-file-storage-share-vpc-mount.md}}
