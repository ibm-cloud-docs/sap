---
copyright:
   years: 2025
lastupdated: "2025-09-29"
keywords: SAP, {{site.data.keyword.cloud_notm}}, {{site.data.keyword.ibm_cloud_sap}}, NFS, File Storage Share, Network Load Balancer
subcollection: sap
content-type: tutorial
services: vpc, transit-gateway, power-iaas, sap
account-plan: paid
completion-time: 2h
---

{{site.data.keyword.attribute-definition-list}}


# Accessing File Storage for VPC with regional availability from IBM {{site.data.keyword.powerSys_notm}} instances
{: #ha-nlb-rt-rfs-intro}
{: toc-content-type="tutorial"}
{: toc-services="vpc, transit-gateway, power-iaas, sap"}
{: toc-completion-time="2h"}

This tutorial might incur costs.
Use the [Cost Estimator](/estimator) to generate a cost estimate based on your projected usage.
{: tip}

In this tutorial, you learn how to mount a file share with regional availability on IBM {{site.data.keyword.powerSys_notm}} server instances.
You cannot directly mount a file storage share on IBM {{site.data.keyword.powerSys_notm}} instances and must instead use a path through a network load balancer (NLB).
You create a file storage profile with regional availability and a mount target in IBM VPC.
You create two network load balancers with routing mode in different availability zones in IBM VPC, and configure the route tables.
Then, you mount the file storage share on the virtual server instances in IBM {{site.data.keyword.powerSys_notm}}.
{: shortdesc}

The following architecture overview diagram illustrates this scenario.

The virtual server instance in IBM {{site.data.keyword.powerSys_notm}} sends a request through a transit gateway to the file storage share.
According to the rule in the routing table of the VPC, the network traffic to the file storage share is directed to the network load balancer.
*Routing_mode* is enabled for the network load balancer.
It bypasses a back-end pool and sends requests directly to the destination IP address.
The file storage share responds and the response is sent directly to the virtual server instance in IBM {{site.data.keyword.powerSys_notm}}.
The network load balancer (NLB) with routing mode has two IP addresses (active and standby).
When a failover occurs, the route mode updates all routing rules that are created for the VPC with a `next_hop` of the standby IP.
Both the active IP and the standby IP are used during the lifetime of an NLB with route mode.

![Architecture overview diagram](../../images/powervs-ha-nlb-rt-rfs.svg){: figure caption="A diagram that shows the architecture for accessing File Storage for VPC with regional availability."}

## Before you begin
{: #ha-nlb-rt-rfs-prereqs}

- [Create a VPC](/docs/vpc?topic=vpc-getting-started&interface=ui) spanning at least two availability zones, or use an existing one.

- Create the address prefixes and the subnets for the multizone region VPC.
- Create two IBM {{site.data.keyword.powerSys_notm}} workspaces in the IBM Cloud region, with affinity to the availability zones of the VPC.

See the mapping between the VPC zones and the Power Virtual Server zones in [IBM Cloud regions](/docs/power-iaas?topic=power-iaas-ibm-cloud-reg).{: note}

- Create two virtual server instances in IBM {{site.data.keyword.powerSys_notm}}.
    Deploy one virtual server instance in each of the two workspaces.
    Note the IP addresses of the virtual server instances.

- Create two virtual server instances in IBM {{site.data.keyword.powerSys_notm}}.
    Deploy one virtual server instance in each of the two workspaces.
    Note the IP addresses of the virtual server instances.

- Create a Transit Gateway and attach the VPC subnets and the IBM {{site.data.keyword.powerSys_notm}} workspaces to the Transit Gateway.

- Check the user permissions.
   Make sure that your user account has permissions to create and manage VPC resources.
   See [Granting user permissions for VPC resources](/docs/vpc?topic=vpc-managing-user-permissions-for-vpc-resources).

- Use or create an SSH key to connect to the virtual server instances.
   If you don't have an SSH key, see [Getting started with SSH keys](/docs/vpc?topic=vpc-ssh-keys).

## Creating a security group and a file storage share
{: #ha-nlb-rt-rfs-create-sg-and-fs-share}
{: step}

### Creating a security group
{: #ha-nlb-rt-rfs-create-sg}

Create a security group and configure inbound rules for port 2049.

1. Go to [Security groups for VPC](/infrastructure/network/securityGroups) and click **Create**.
1. Verify or set the **Geography** and **Region** fields.
1. Enter `nfs-server-sg` for the **Name**.
1. Select the same **Resource group** as the VPC resource group.
1. Select your VPC in the **Virtual private cloud** list.
1. Create an inbound rule for each virtual server instance (NFS client).
   1. In the **Inbound rules** section, click **Create**.
   1. Configure the rule as follows:
      - **Protocol**: Select **TCP**.
      - **Port**: Select **Port range**, and enter `2049` for both **Port min** and **Port max**.
      - **Source type**: Select **IP or CIDR**.
         - Enter the IP address of each virtual server instance.
         - If your instances are in a subnet and you want to allow access from all members of the subnet, enter the subnet's CIDR block.
      - **Destination type**: Select **Any**.
   1. Click **Create** to save the rule.
1. Create a common outbound rule.
   1. In the **Outbound rules** section, click **Create**.
   1. Configure the rule as follows:
      - **Protocol**: Select **TCP**.
      - **Port**: Select **Any**.
      - **Destination type**: Select **Any**.
      - **Source type**: Select **Any**.
   1. Click **Create** to save the rule.
1. Finalize the security group.
   - Click **Create security group** to apply the configuration.

### Provisioning file storage with regional availability
{: #ha-nlb-rt-rfs-create-fs-share}

1. Go to [File storage shares for VPC](/infrastructure/storage/fileShares).

1. Click **Create** > **Create file share**.

1. In the **Availability** section, choose `Regional` availability.

The availability cannot be modified after provisioning.{: important}

1. In the **Location** section, select the **Geography** and the multizone **Region** (MZR).

1. In the **Details** section, enter `nfs-server` in the **Name** field.
   Select the same **Resource group** as the VPC resource group.

   - The profile is chosen automatically based on your data availability selection.
      If you chose regional availability, your file share uses the **rfs** profile.

   - Select the **Size** and **Max throughput** for your file share.
      You can increase the capacity later, and you can also adjust the bandwidth as needed.

   The **Mount target access mode** is always **Security groups**.
   The previous access mode `Virtual private cloud` is not available for file storage with regional availability.

1. In the **Mount targets (optional)** section, click **Create**.

   - In the **Mount target details** section, enter `nfs-server-mount-target` in the **Mount target name** field.

   - Select your **VPC**.

   - In the **Network interfaces** section, click the pencil icon on the new interface.
      Verify the selected subnet and click **Next**.

   - In the **Security groups** section, check the `nfs-server-sg` security group, clear the **VPC default** security group, and click **Next**.

   - Click **Next** a few times to get to the **Review** section, then click **Save**.

1. Click **Next** when you got back to the **Create mount target screen**.
   - Click **Next** in the **Encryption** step.
   - In the **Review** step, click **Create**.
1. Click **Create file share** on the right to provision the file storage and the mount target.

### Gathering the file storage IP address and mount path information
{: #ha-nlb-rt-rfs-get-mount-path}
{: step}

1. Go to [File storage shares for VPC](/infrastructure/storage/fileShares).
1. Click the **Name** `nfs-server`.
1. In the **Mount targets** section, click the **Name** of the mount target in the VPC to view the mount target details.

1. Make a note of the **Mount path**.
      In the example, the mount path is `10.20.30.40:/73a1ff96_4861_4463_aa09_8c8128b8e277fsf`.
      The first part of the mount path is the **primary IP** of the mount target: `10.20.30.40`.

Later, set the **Destination** parameter in the VPC route entry to the **primary IP** of the mount target.
Use the **Mount path** parameter as an argument for the `mount` command on the IBM {{site.data.keyword.powerSys_notm}} instance.

## Creating the private network load balancers with routing mode
{: #ha-nlb-rt-rfs-create-nlb-with-routing-mode}
{: step}

### Creating the service-to-service authentication policy
{: #ha-nlb-rt-rfs-create-auth-policy}

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

### Creating the network load balancers
{: #ha-nlb-rt-rfs-create-nlb}

You need to create two load balancers, each of them for the specific subnet in an availability zone.

1. Go to the [Load balancers for VPC](/infrastructure/network/loadBalancers) page and click **Create**.
1. Select **Network Load Balancer (NLB)** as the **Load balancer type**.
1. In the **Location** section, select the same **Geography** and **Region** that is used for the virtual private cloud.

1. Enter `nfs-server-nlb-1` in the **Name** field.

1. Select the same **Resource group** as the VPC resource group.
1. Select your VPC in the **Virtual private cloud** list.
1. Select the **Subnet** according to the subnet of the availability zone.

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

1. Click **Create a load balancer** to provision the load balancer.

As part of the process, you create a back-end pool.
However, you cannot define the back-end pool `Failsafe policy` directly, and it must be updated in the next step.
{: important}

Repeat the step and create the second load balancer `nfs-server-nlb-2` in the other VPC zone.

### Updating the network load balancer failsafe policy
{: #ha-nlb-rt-rfs-upd-nlb-failsafe-policy}

Update the `Failsafe policy` for the `nfs-server-fwd-pool` back-end pool.
The network load balancer then bypasses the back-end pool and sends requests directly to the destination IP address.

1. Go to [Load balancers for VPC](/infrastructure/network/loadBalancers).
1. Click the load balancer `nfs-server-nlb-1`.
1. Click the **Back-end pools** tab and select the pool `nfs-server-fwd-pool`.
1. Click `nfs-server-fwd-pool` and then `Edit`.
1. In the `Failsave policy` section, select `Bypass` as the **Action**.
1. Click `Save`.

Repeat those steps for load balancer `nfs-server-nlb-2`.

### Collecting the private IP addresses of the load balancer
{: #ha-nlb-rt-rfs-get-nlb-ip-addresses}
{: step}

1. Go to [Load balancers for VPC](/infrastructure/network/loadBalancers).
1. Click `nfs-server-nlb-1`.

1. In the **Load balancer details - Private IPs** section, make a note of the first IP address entry in the list.

Repeat those steps for load balancer `nfs-server-nlb-2`.

Later, you set the **Next hop** parameter in the VPC route entries to the active **Private IP** address of the load balancer.

## Creating a routing table and routes for VPC
{: #ha-nlb-rt-rfs-provision-create-table-and-routes}
{: step}

### Creating a routing table
{: #ha-nlb-rt-rfs-create-routing-table}

Customize the **Ingress routes** to route incoming traffic from external sources such as the IBM Cloud Transit Gateway.

Only one custom routing table is associated with an ingress source.
If an ingress routing table exists for the IBM Cloud Transit Gateway source, add the **routes** to that table.
{: note}

1. Go to [Routing tables for VPC](/infrastructure/network/routingTables).
1. Click **Create**.
1. In the **Location** section, select the same **Geography** and **Region** that is used for the virtual private cloud.
1. Enter `nfs-server-routing` in the **Name** field.
1. Select your VPC in the **Virtual private cloud** list.
1. Enable the **Transit gateway** flag in the **Traffic source (optional)** section.
1. Click **Create routing table** to provision the routing table.

### Updating the routing table
{: #ha-nlb-rt-rfs-update-routing-table}

To propagate routes outside the VPC address prefix range, enable **Advertise to** for the transit gateway.

1. Go to [Routing tables for VPC](/infrastructure/network/routingTables).
1. Click on the name of the ingress route that you created earlier.
1. In the **Traffic** section, click **Edit** to open the **Edit traffic** panel.
1. Under **Traffic source**, expand the **Transit Gateway** section.
1. Toggle the **Advertise to** switch to **On**.
1. Click **Save** to apply the changes to the routing table.

### Creating routes
{: #ha-nlb-rt-rfs-create-routes}

1. Go to [Routing tables for VPC](/infrastructure/network/routingTables).
1. Click the name `nfs-server-routing`.
1. Click **Create**.
1. Select the zone, according to the zone of the network load balancer, in the **Zone** field.

1. Enter `rfs-route-1` in the **Name** field.
1. Using CIDR notation, enter the IP address of the mount target as **Destination CIDR**.
1. Select `Deliver` as the **Action**.
1. Enter the `IP address of the network load balancer #1` as the **Next hop (IP address)**.

1. Switch `Advertise`to `On`.
1. Click **Save** to add the route to the table.

Repeat those steps, and use the name `rfs-route-2`, for the `IP address of the network load balancer #2`.

## Mounting the file share on the IBM {{site.data.keyword.powerSys_notm}} instance
{: #ha-nlb-rt-rfs-mount-file-share}
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
   mount -t nfs4 -o rw,sec=sys 10.20.30.40:/ea90ea14_0a1b_4f36_85c0_1cf83a2c8065 /mnt/test
   ```
   {: codeblock}
