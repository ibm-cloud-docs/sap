---

copyright:
   years: 2025
lastupdated: "2025-02-02"

keywords: SAP, {{site.data.keyword.cloud_notm}}, {{site.data.keyword.ibm_cloud_sap}}, NFS, File Storage Share, Network Load Balancer

subcollection: sap

content-type: tutorial
services: vpc, transit-gateway, power-iaas, sap
account-plan: paid
completion-time: 2h

---
{{site.data.keyword.attribute-definition-list}}

# Access VPC File Storage from IBM {{site.data.keyword.powerSys_notm}} instances
{: #ha-nlb-rt-nfs-intro}
{: toc-content-type="tutorial"}
{: toc-services="vpc, transit-gateway, power-iaas, sap"}
{: toc-completion-time="2h"}

This tutorial may incur costs. Use the [Cost Estimator](/estimator) to generate a cost estimate based on your projected usage.
{: tip}



In this tutorial, you learn how to mount a file storage share in IBM VPC on a server instance in IBM {{site.data.keyword.powerSys_notm}}.
You create a file storage share, together with a mount target, in IBM VPC.
You cannot directly create a mount target for a subnet in IBM {{site.data.keyword.powerSys_notm}}.
You create a network load balancer with routing mode, two virtual server instances, file storage, and a route table in IBM VPC.
Then, you mount the file storage share on the virtual server instance in IBM {{site.data.keyword.powerSys_notm}} using a mount target in IBM VPC.
{: shortdesc}



The following architectural diagram illustrates the scenario:

The virtual server instance in IBM {{site.data.keyword.powerSys_notm}} sends a TCP request to the file storage share that uses the NFS protocol through a transit gateway.
According to the rule in the routing table of the VPC, the network traffic to the file storage share is directed to the network load balancer.
The load balancer forwards the request to one of the virtual server instances of the back-end pool.
Two virtual server instances are attached to the back-end pool, as the NLB route mode currently does not directly support file storage share as a back-end pool.
`IP forwarding` is configured on the virtual server instances. The packets are forwarded to the file storage share back-end target.
The file storage share responds, and the response is sent directly to the virtual server instance in IBM {{site.data.keyword.powerSys_notm}}.
The network load balancer (NLB) with routing mode has two IP addresses (Active/Standby).
When a failover occurs, the route mode updates all routing rules that are created for the VPC with a `next_hop` of the previous standby IP.
Both active and standby IP are used during the lifetime of the NLB with route mode.

![Architectural diagram](images/sap-power-virtual-server-ha-nlb-rt-nfs.svg){: figure caption="A diagram that shows the architecture to access VPC file storage."}

## Before you begin
{: #ha-nlb-rt-nfs-prereqs}

- [Create a VPC](/docs/vpc?topic=vpc-getting-started&interface=ui), or use an existing VPC.
- Create a subnet in your preferred zone in your VPC.
- Create an IBM {{site.data.keyword.powerSys_notm}} workspace in the IBM Cloud region
- Create a Transit Gateway, and attach the VPC subnet and the IBM {{site.data.keyword.powerSys_notm}} workspace to the Transit Gateway.

- Check for user permissions.
   Be sure that your user account has sufficient permissions to create and manage VPC resources.
   See [Granting user permissions for VPC resources](/docs/vpc?topic=vpc-managing-user-permissions-for-vpc-resources) for VPC.

- Check or create an SSH key to connect to the virtual servers instances.
 If you don't have an SSH key, see [the instructions](/docs/vpc?topic=vpc-ssh-keys) for creating a key for VPC.







## Create a security group and provision virtual server instances
{: #ha-nlb-rt-nfs-1}
{: step}

### Create a security group to allow NFS V4 traffic
{: #ha-nlb-rt-nfs-1a}

Create a security group and configure inbound rules for the listening ports for SSH (22) and NFS (2049).

1. Browse to the [Security groups](/infrastructure/network/securityGroups) page and click **Create**.
1. Verify or set the **Geography** and **Region** fields.
1. Enter `nfs-server-sg` for the **Name**.
1. Select the same **Resource group** as the VPC resource group.
1. Select your virtual private cloud.
1. Add the same **Inbound rules** as found in the following table.

   | Protocol   | Port range | Source type | Destination type |
   |------------|------------|-------------|------------------|
   | TCP        | 22-22      | Any         | Any              |
   | TCP        | 2049-2049  | Any         | Any              |
   | UDP        | 2049-2049  | Any         | Any              |
   {: caption="Inbound rules" caption-side="bottom"}

1. Add the same **Outbound rules** as found in the following table and then click **Create security group**.

   | Protocol   | Port | Destination type | Source type |
   |------------|------|------------------|-------------|
   | TCP        | Any  | Any              | Any         |
   | UDP        | Any  | Any              | Any         |
   {: caption="Outbound rules" caption-side="bottom"}

### Provision virtual server instances
{: #ha-nlb-rt-nfs-1b}

1. Browse to the [Virtual server instances for VPC](/infrastructure/compute/vs) page.
1. Click **Create**.
   1. Verify or set the **Geography**, **Region**, and **Zone** fields.
   2. Enter `nlb-fwd-01-vsi` as your virtual server's name.
   3. Set the **Resource group** field.
1. Under the **Image and profile** section, click **Change image**.
1. In the **Search items** field, type `Red Hat` and pick any version of the image and click **Save**.
1. Under the **Profile** section, click **Change profile**.
1. Pick **Compute** with `8 vCPUs` and `16 GB RAM` as your profile and click **Save**.
1. Set the **SSH keys** to the SSH key that you created earlier.
1. Under **Network attachments with Virtual network interface**, click the pencil icon on the row for the **eth0** interface.
   * Select the subnet that you want to use.
   * Click **Next**.
   * In the **Network Configuration** section, enable **Allow IP spoofing**.
   * In the **Security groups** section, clear the **VPC default** security group, and check the `nfs-server-sg` security group.
   * Click **Next** a few times and then on **Save**.
1. Click **Create virtual server**.
1. Repeat the preceding steps to provision a second `nlb-fwd-02-vsi` virtual server.

## Enable IP Forward on the virtual server instances
{: #ha-nlb-rt-nfs-2}
{: step}

Log on as user `root` to the virtual server instances

1. Run following command to create a configuration file for the `ip_forward` kernel setting.

   ```sh
   cat >/etc/sysctl.d/1000-custom.conf <<EOF
   net.ipv4.ip_forward = 1
   EOF
   ```
   {: codeblock}

2. Restart the virtual server instance

   ```sh
   shutdown -r
   ```
   {: codeblock}

## Create the private network load balancer with routing mode
{: #ha-nlb-rt-nfs-3}
{: step}

### Create the service-to-service authentication policy
{: #ha-nlb-rt-nfs-3a}

To support routing mode, you must first create a service-to-service authentication policy for your NLB.

   1. Log in to [IBM Access Management](/iam/authorizations/grant) from your browser.
   1. Click **Authorizations**, then click **Create**.
   1. Select **This account** For the source account, and click **Next**.
   1. Select **VPC Infrastructure Services** for the source service.
   1. Select **Specific resources** > **Resource Type** > **Load Balancer for VPC** for scope access.
   1. Select **VPC Infrastructure Services** for the target service.
   1. Select **Specific resources** > **Resource Type** > **Virtual Private Cloud** for scope access.
   1. Select the **Editor** checkbox to give yourself Editor access, then click **Authorize**.

### Create the network load balancer
{: #ha-nlb-rt-nfs-3b}

1. Browse to the [Load balancers](/infrastructure/network/loadBalancers) page and click **Create**.
2. Select **Network Load Balancer (NLB)** as the Load balancer type.
3. Under the **Location** section, select the same **Geography** and **Region** that is used for the virtual private cloud.
3. Enter `nfs-server-nlb` for the name. Select the same **Resource group** as the VPC resource group.
2. Select the **Virtual private cloud**.
3. Select the **Subnets**.
4. Select the type **Private**.
5. Toggle the **Routing mode** to **On** to create a network load balancer with routing mode.
4. Click **Create pool** to create a back-end pool.
   Set the parameters with the following values and click **Create** when done.
   - **Name**:  `nfs-server-fwd-pool`
   - **Protocol**: `TCP`
   - **Session stickiness**: `None`
   - **Method**: `Round robin`
   - **Health protocol**: `TCP`
   - **Health port**: `22`
   - **Interval(sec)**: `15`
   - **Timeout(sec)**: `5`
   - **Max retries**: `2`
   - Click **Create**.
5. Click **Attach server** to add the server instances as members to the pool.
   - Select the VPC subnet in the **Subnets** dropdown.
   - Select the two instances that you created, and set `22` as the **port**.
   - Click **Attach** to complete the creation of a back-end pool.
6. Click **Create listener** to create a new front-end listener; A listener is a process that checks for connection requests.
   - **Back-end pool**: `nfs-server-fwd-pool`
   - **Protocol**: `TCP` and click **Create**.
7. Under **Security Groups**, check `nfs-server-sg` and clear the default security group.
8. Click **Create load balancer** to provision the load balancer.

### Identify the IP address of the load balancer
{: #ha-nlb-rt-nfs-3c}

1. Browse to the [Load balancers for VPC](/infrastructure/storage/loadBalancers) page.
1. Click **Name** `nfs-server-nlb`.
1. Write down the first entry of the list of **Private IPs**.

Later on, the parameter **Next hop** in the VPC route entry is configured to the active **Private IP** address of the load balancer.

## Provision a File storage share for VPC
{: #ha-nlb-rt-nfs-4}
{: step}

1. Browse to the [File storage shares for VPC](/infrastructure/storage/fileShares) page.
1. Click **Create**.
1. Under the **Location** section, select the same **Geography**, **Region**, and **Zone** used for the virtual private cloud.
1. Enter `nfs-server` for the name. Select the same **Resource group** as the VPC resource group.
1. Enter the `Storage Size` in GB. Enter the `Max IOPS` value.
1. Select **Security groups** as the **Mount target access mode**.
1. Click **Create file share** to provision the file storage.

## Identify the file storage IP address and mount path
{: #ha-nlb-rt-nfs-4a}

1. Browse to the [File storage shares for VPC](/infrastructure/storage/fileShares) page.
1. Click **Name** `nfs-server`.
1. Click the **Name** of the mount target in the VPC to get the mount target details.
1. Write down the **Primary IP** and the **Mount path** details.

Later on, the parameter **Destination** in the VPC route entry is configured as the **Primary IP** of the file storage share.
The parameter **Mount path** is specified as an argument for the `mount` command on the IBM {{site.data.keyword.powerSys_notm}} instance.

## Provision a Routing table and Routes for VPC
{: #ha-nlb-rt-nfs-5}
{: step}

### Provision a Routing table
{: #ha-nlb-rt-nfs-5a}

Customize **Ingress routes** to route incoming traffic from external sources like the IBM Cloud Transit Gateway.

A particular ingress traffic source is associated only with one custom routing table.
If a routing table for the IBM Cloud Transit Gateway source exists, then add the **VPC route** to this table.
{: note}

1. Browse to the [Routing tables for VPC](/infrastructure/storage/routingTables) page.
1. Click **Create**.
1. Under the **Location** section, select the same **Geography** and **Region** that is used for the virtual private cloud.
1. Enter `nfs-server-routing` for the name. Select the same **Resource group** as the VPC resource group.
1. Select **Transit Gateway** as **Traffic Source**.
1. Click **Create routing table** to provision the routing table.

### Provision a Route
{: #ha-nlb-rt-nfs-5b}

1. Browse to the [Routing tables for VPC](/infrastructure/storage/routingTables) page.
1. Click the name `nfs-server-routing`.
1. Click **Create**.
1. Enter `nfs-server` as the **Name** of the route.
1. Enter the primary IP address of the VPC file storage share in CIDR notation as **Destination CIDR**.
1. Select `Deliver` as the **Action**.
1. Enter the `IP address of the network load balancer` as **Next hop (IP address)**.
1. Click **Save** to add the route to the table.

## Mounting the file share on the server instance in IBM {{site.data.keyword.powerSys_notm}}
{: #ha-nlb-rt-nfs-6}
{: step}

Log on as user `root` on the server instance in IBM {{site.data.keyword.powerSys_notm}} where you want to mount the file share.
Then continue with following steps.

1. Install the NFS client packages on the instance.

   ```sh
   yum install nfs-utils
   ```
   {: pre}

2. Create a directory in your instance.

   ```sh
   mkdir /mnt/tst
   ```
   {: pre}

3. Mount the remote file share.

   ```sh
   mount -t nfs4 -o <options> <host:/mount_target> /mnt/tst
   ```
   {: pre}

   See the following example.

   ```sh
   mount -t nfs4 -o rw,sec=sys 10.30.40.5:/73a1ff96_4861_4463_aa09_8c8128b8e277fsf /mnt/tst
   ```
   {: codeblock}
