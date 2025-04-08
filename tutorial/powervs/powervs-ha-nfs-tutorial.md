---
copyright:
   years: 2025
lastupdated: "2025-04-08"
keywords: SAP, {{site.data.keyword.cloud_notm}}, {{site.data.keyword.ibm_cloud_sap}}, NFS, File Storage Share, Network Load Balancer
subcollection: sap
content-type: tutorial
services: vpc, transit-gateway, power-iaas, sap
account-plan: paid
completion-time: 2h
---

{{site.data.keyword.attribute-definition-list}}


# Accessing File Storage for VPC from IBM {{site.data.keyword.powerSys_notm}} instances
{: #ha-nlb-rt-nfs-intro}
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

![Architecture overview diagram](../../images/powervs-ha-nlb-rt-nfs.svg){: figure caption="A diagram that shows the architecture for accessing File Storage for VPC."}

## Before you begin
{: #ha-nlb-rt-nfs-prereqs}

- [Create a VPC](/docs/vpc?topic=vpc-getting-started&interface=ui) or use an existing one.
- Create a subnet in the VPC for your preferred zone.
- Create an IBM {{site.data.keyword.powerSys_notm}} workspace in the IBM Cloud region.
- Create a Transit Gateway and attach the VPC subnet and the IBM {{site.data.keyword.powerSys_notm}} workspace to the Transit Gateway.

- Check the user permissions.
   Make sure that your user account has permissions to create and manage VPC resources.
   See [Granting user permissions for VPC resources](/docs/vpc?topic=vpc-managing-user-permissions-for-vpc-resources).

- Use or create an SSH key to connect to the virtual server instances.
   If you don't have an SSH key, see [Getting started with SSH keys](/docs/vpc?topic=vpc-ssh-keys).

## Creating a security group and a file storage share
{: #ha-nlb-rt-nfs-create-sg-and-fs-share}
{: step}

### Creating a security group to allow NFS V4 traffic
{: #ha-nlb-rt-nfs-create-sg}

Create a security group and configure inbound rules for the SSH (22) and NFS (2049) ports.

1. Browse to [Security groups for VPC](/infrastructure/network/securityGroups) and click **Create**.
1. Verify or set the **Geography** and **Region** fields.
1. Enter `nfs-server-sg` for the **Name**.
1. Select the same **Resource group** as the VPC resource group.
1. Select your VPC in the **Virtual private cloud** list.
1. Add the **Inbound rules** as shown in the following table.

   | Protocol   | Port range | Source type | Destination type |
   |------------|------------|-------------|------------------|
   | TCP        | 22-22      | Any         | Any              |
   | TCP        | 2049-2049  | Any         | Any              |
   {: caption="Inbound rules" caption-side="bottom"}

1. Add the **Outbound rules** as shown in the following table, then click **Create security group**.

   | Protocol   | Port | Destination type | Source type |
   |------------|------|------------------|-------------|
   | TCP        | Any  | Any              | Any         |
   | UDP        | Any  | Any              | Any         |
   {: caption="Outbound rules" caption-side="bottom"}

### Provisioning a file storage share
{: #ha-nlb-rt-nfs-create-fs-share}

1. Browse to [File storage shares for VPC](/infrastructure/storage/fileShares).
1. Click **Create** > **Create file share**.
1. In the **Location** section, select the same **Geography**, **Region**, and **Zone** as the virtual private cloud.
1. Enter `nfs-server` in the **Name** field.
   Select the same **Resource group** as the VPC resource group.
1. In the **Size** section, enter the `Storage size` in GB.
1. Enter a `Max IOPS` value.
1. In the **Mount target access mode** section, select **Security groups**.
1. In the **Mount targets (optional)** section, click **Create**.
   - Enter `nfs-server-mount-target`in the **Mount target name** field.
   - Select your **VPC**.
   - In the **Network interfaces** section, click the pencil icon on the new interface.
   - Verify the selected subnet and click **Next**.
   - In the **Security groups** section, check the `nfs-server-sg` security group, clear the **VPC default** security group, and click **Next**.
   - Click **Next** a few times to get to the **Review** section, then click **Save**.
   - Back on the **Create mount target screen**, click **Next**.
   - Click **Next** in the **Encryption** step.
   - In the **Review** step, click **Create**.
1. Click **Create file share** to provision the file storage and the mount target.

### Gathering the file storage IP address and mount path information
{: #ha-nlb-rt-nfs-get-mount-path}
{: step}

1. Browse to [File storage shares for VPC](/infrastructure/storage/fileShares).
1. Click the **Name** `nfs-server`.
1. In the **Mount targets** section, click the **Name** of the mount target in the VPC to view the mount target details.
1. Make a note of the **primary IP** and the **Mount path**.

Later, the **Destination** parameter in the VPC route entry is set to the **Primary IP** of the file storage share.
The **Mount path** parameter is used as an argument to the `mount` command on the IBM {{site.data.keyword.powerSys_notm}} instance.

## Creating the private network load balancer with routing mode
{: #ha-nlb-rt-nfs-create-nlb-with-routing-mode}
{: step}

### Creating the service-to-service authentication policy
{: #ha-nlb-rt-nfs-create-auth-policy}

To support routing mode, you must first create a *service-to-service* authentication policy for your NLB.

1. Log in to [IBM Access Management](/iam/authorizations/grant).
1. Click **Authorizations**, then click **Create**.
1. Select **This account** for **Source account** and click **Next**.
1. Select **VPC Infrastructure Services** for **Service** and click **Next**.
1. Select **Specific resources** > **Resource Type** > **Load Balancer for VPC** for the scope access and click **Next**.
1. Select **VPC Infrastructure Services** for the target service and click **Next**.
1. Select **Specific resources** > **Resource Type** > **Virtual Private Cloud** for the scope access and click **Next**.
1. Select the **Editor** checkbox to grant the Editor access role.
1. Click **Authorize**.

### Creating the network load balancer
{: #ha-nlb-rt-nfs-create-nlb}

1. Browse to the [Load balancers for VPC](/infrastructure/network/loadBalancers) page and click **Create**.
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
{: #ha-nlb-rt-nfs-upd-nlb-failsafe-policy}

Update the `Failsafe policy` for the `nfs-server-fwd-pool` back-end pool.
The network load balancer then bypasses the back-end pool and sends requests directly to the destination IPs.

1. Browse to [Load balancers for VPC](/infrastructure/network/loadBalancers).
1. Click the load balancer `nfs-server-nlb`.
1. Click the **Back-end pools** tab and select the pool `nfs-server-fwd-pool`.
1. Click `nfs-server-fwd-pool` and then `Edit`.
1. In the `Failsave policy` section, select `Bypass` as the **Action**.
1. Click `Save`.

### Collecting the private IP addresses of the load balancer
{: #ha-nlb-rt-nfs-get-nlb-ip-addresses}
{: step}

1. Browse to [Load balancers for VPC](/infrastructure/network/loadBalancers).
1. Click `nfs-server-nlb`.
1. In the **Load balancer details - Private IPs** section, make a note of the first IP address entry in the list.

Later, the **Next hop** parameter in the VPC route entry is set to the active **Private IP** address of the load balancer.

## Creating a routing table and routes for VPC
{: #ha-nlb-rt-nfs-provision-create-table-and-routes}
{: step}

### Creating a routing table
{: #ha-nlb-rt-nfs-create-routing-table}

Customize the **Ingress routes** to route incoming traffic from external sources such as the IBM Cloud Transit Gateway.

Only one custom routing table is associated with an ingress source.
If an ingress routing table exists for the IBM Cloud Transit Gateway source, add the **route** to that table.
{: note}

1. Browse to [Routing tables for VPC](/infrastructure/network/routingTables).
1. Click **Create**.
1. In the **Location** section, select the same **Geography** and **Region** that is used for the virtual private cloud.
1. Enter `nfs-server-routing` in the **Name** field.
1. Select your VPC in the **Virtual private cloud** list.
1. Enable the **Transit gateway** flag in the **Traffic source (optional)** section.
1. Click **Create routing table** to provision the routing table.

### Creating a route
{: #ha-nlb-rt-nfs-create-route}

1. Browse to [Routing tables for VPC](/infrastructure/network/routingTables).
1. Click the name `nfs-server-routing`.
1. Click **Create**.
1. Select the zone for your route in the **Zone** field.
1. Enter `nfs-server` in the **Name** field.
1. Using CIDR notation, enter the primary IP address of the file share as **Destination CIDR**.
1. Select `Deliver` as the **Action**.
1. Enter the `IP address of the network load balancer` as the **Next hop (IP address)**.
1. Click **Save** to add the route to the table.

## Mounting the file share on the IBM {{site.data.keyword.powerSys_notm}} instance
{: #ha-nlb-rt-nfs-mount-file-share}
{: step}

Log on as the `root` user to the server instance in IBM {{site.data.keyword.powerSys_notm}} where you want to mount the file share.

1. Install the NFS client packages on the instance.

   ```sh
   dnf install nfs-utils
   ```
   {: pre}

2. Create a directory for the mount point.

   ```sh
   mkdir <mount_point>
   ```
   {: pre}

3. Mount the remote file share.

   ```sh
   mount -t nfs4 -o <options> <host:/mount_target> <mount_point>
   ```
   {: pre}

   See the following example.

   ```sh
   mkdir /mnt/test
   mount -t nfs4 -o rw,sec=sys 10.30.40.5:/73a1ff96_4861_4463_aa09_8c8128b8e277fsf /mnt/test
   ```
   {: codeblock}
