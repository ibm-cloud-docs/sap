---
copyright:
  years: 2020, 2021
lastupdated: "2021-05-28"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Classic Infrastructure environment introduction
{: #classic-env-introduction}

An Infrastructure-as-a-Service (IaaS) environment consists of many components. These components are primarily compute, storage, a network from a specified region (such as the US), and a designated site location (also referred to as zone, which is a data center site).

## Deployment and management
{: #classic-env-deployment-and-management}

{{site.data.keyword.cloud_notm}} Classic Infrastructure offerings (such as {{site.data.keyword.baremetal_short}}) are deployed through the [{{site.data.keyword.cloud_notm}} Classic Infrastructure console](https://cloud.ibm.com/classic){: external}.

Alternatively, deployments can be made and managed by using:
- {{site.data.keyword.cloud_notm}} CLI
- {{site.data.keyword.cloud_notm}} Classic Infrastructure API (Softlayer API) calls that use an {{site.data.keyword.cloud_notm}} Classic Infrastructure API key
- [Terraform Provider for {{site.data.keyword.cloud_notm}}](/docs/ibm-cloud-provider-for-terraform) that uses an {{site.data.keyword.cloud_notm}} Classic Infrastructure API key

For more information, see [Managing {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s (IAM)](/docs/power-iaas?topic=power-iaas-managing-resources-and-users).


## Locations - Data centers
{: #classic-env-locations-data-centers}

With data centers across North and South America, Europe, Asia, and Australia, you can provision cloud resources where (and when) you need them. You can choose from many regions globally, and each region has multiple data centers. Each data center is connected to the {{site.data.keyword.cloud_notm}} global private network, making data transfers faster and more efficient anywhere in the world.

For more information about {{site.data.keyword.cloud_notm}} availability zones, data centers, and points of presence (PoPs), see the [global regions and availability zones and data centers map](https://www.ibm.com/cloud/data-centers){: external}.

## Networking
{: #classic-env-networking}

The classic infrastructure network, is robust, secure, and flexible; built upon matured networking principles combined with the latest in networking hardware.

| {{site.data.keyword.cloud_notm}} Classic Infrastructure network |
| -- |
| Global |
| Region |
| Data center |
| Data center Pod |
| VLAN (Public and Private) |
| Subnet (Public and Private) |
{: caption="Networking component layers overview" caption-side="top"}

Classic Infrastructure was formerly known as Softlayer.
{: note}

The {{site.data.keyword.cloud_notm}} Classic Infrastructure network provides connectivity across a global footprint of over 60 {{site.data.keyword.cloud_notm}} data centers and 28 points of presence (PoPs). Connections are available to leading global network providers and communications service providers.

The {{site.data.keyword.cloud_notm}} Classic Infrastructure network consists of three distinct and redundant network architectures that are seamlessly integrated in the secured network-within-a-network topology (multiple isolated logical networks, for example VLANs). This configuration means if an outage occurs in a data center on the public network, the traffic is routed and traverses through other established networks. Routing the traffic across other networks and through another data center provides continued server availability.

The three distinct and redundant network architectures within the secured network-within-a-network topology are:

- **Public network:** provides carrier grade internet connectivity to multi-home backbone carriers. On public IP, the connection is made to the {{site.data.keyword.cloud_notm}} network PoP closest to the origin request. Traffic travels directly across the {{site.data.keyword.cloud_notm}} data center to the data center network backbone into the correct data center, minimizing the network hops and handoffs between providers that add network latency.

- **Private network:** provides complete control of the secured networking traffic without performance degradation if significant public network traffic occurs at the same time; the private network has three functional areas:
    - **Host to and from Host**: network traffic in the private VLANs assigned to you. **Bandwidth is free and unmetered.**
    - **Host to and from Backend Services:** network traffic to and from the private VLAN to OS update servers, NTP, DNS resolvers, network storage and more. **Bandwidth is free and unmetered.**
    - **VPN and direct connections to and from VLAN:** network traffic to and from an existing internal network over direct connection or VPN that uses a distinct stand-alone network to the secure private VLAN. **Bandwidth is free and unmetered.**
    - **Data center to Data center interconnectivity:** provides secure connectivity between hosts across {{site.data.keyword.cloud_notm}} data center locations. **Bandwidth is free and unmetered.**

- **Management network:** provides Out-Of-Band Management (OOBM) accessible through VPN (for example the built-in SSL VPN for administration) or direct connection (for example {{site.data.keyword.cloud_notm}} Direct Link). Network management allows Remote Console access through the IPMI network interface for Bare Metals hosts on the private network. **Bandwidth is free and unmetered.**

This network-within-a-network topology design provides maximum accessibility, security, and control for your IT infrastructure. The topology provides the ease of a public network with the security of a private network by keeping systems accessible to administrators and safely off-limits to external users.


### Networking VLANs
{: #classic-env-networking-vlans}

The Virtual LAN (VLAN) assigned to you on the Classic Infrastructure network, provides an enterprise-grade private network with full isolation and security. Each VLAN is either public or private, and each VLAN is assigned to a specific data center for a specific IBM Cloud Account.

The following information is a summary of [Getting started with VLANs on Classic Infrastructure](/docs/vlans?topic=vlans-getting-started) and [About VLANs on Classic Infrastructure](/docs/vlans?topic=vlans-about-vlans).

A VLAN can have multiple Subnets for you to use to segregate traffic, for example:

- Public VLAN
    - Public Primary Subnet (default)
    - Public Secondary Portable Subnet
    - Public Secondary Static Subnet
- Private VLAN
    - Private Primary Subnet (default)
    - Private Secondary Portable Subnet

If you want to separate different types of network traffic in your landscape, use subnets in your network design to separate network traffic and use more Virtual LANs (VLANs) only when required.

Keep in mind that the additional VLANs and Subnets lead to traffic segregation, not increased performance; the increased performance is gained when additional VLANs and Subnets are associated to a host.
When multiple network interfaces are used, two performance increases are possible depending on the use case:
- Bonding of the network interfaces, creating a network path with the network throughput of both interfaces
- Traffic segregation using two networks, then isolating high volumes of traffic to a specific network which avoids a single network becoming a bottleneck. For example, a network for storage I/O only

By default, your server has a Primary Public IP address and Primary Private IP address. If you want your server to be private, you can turn off the public interface by either:
* Ordering your server as private-only
* Using the console or the OS after the server is provisioned

Alternatively, the server can be attached to a public VLAN and private VLAN that is associated with a Gateway Appliance. In this arrangement, the server has a DMZ with a public network facing VLAN and private network facing VLAN.


### Networking subnets
{: #classic-env-networking-subnets}

In Classic Infrastructure, the VLAN has different types of Subnets. The following information is a summary of [Getting started with subnet and IPs on Classic Infrastructure](/docs/subnets?topic=subnets-getting-started) and [About subnets and IP on Classic Infrastructure](/docs/subnets?topic=subnets-about-subnets-and-ips).

Different types of subnets in Classic Infrastructure:
1. **Public and Private Primary Subnet**
   - Auto-assigned when provisioning resources (for example, Bare Metal) into a Public and Private VLAN
   - [Primary subnets have limitations](/docs/subnets?topic=subnets-about-subnets-and-ips#primary-subnets)
2. **Public and Private Secondary Portable Subnet**
   - Appends new subnet to Public and Private VLAN
   - Provides IP addresses for assignment to any resource within a VLAN, can assign Portable IP to multiple resources as a Floating IP
   - Enables connection to multiple resources from a single Portable IP
3. **Public Secondary Static Subnet**
   - Appends new subnet to Public VLAN
   - Provides IP addresses for assignment to one resource within a VLAN (that uses the existing Primary IP or Portable IP of the resource as the "routing endpoint")
   - Enables connection to one resource by using any of the Static IPs plus the original Primary IP or Portable IP.
4. **Public Secondary Global Subnet also known as Global IP addresses**
   - Appends single Internet-accessible IP from {{site.data.keyword.cloud_notm}} private backbone to any VLAN worldwide
   - Provides single IP address for assignment to one resource within any VLAN worldwide (using the existing Primary IP or Portable IP of the resource as the "routing endpoint")
   - Enables connection to one resource from any data center or VLAN on {{site.data.keyword.cloud_notm}} backbone or from Public Internet


### Networking connectivity
{: #classic-env-networking-connectivity}

Issues with network connectivity can cause significant delays for your project. Plan your network carefully, regardless of how you plan to use your system.

Each provisioned server has network interfaces, available at a speed of 100 Mb/s, 1 Gb/s, and 10 Gb/s.

In general, you have two interface choices:
- An external interface with a **_Public IP_**
- An internal interface that is provided with a **_Private IP_**; in compliance with IETF RFC 1918. You can also choose a single internal interface with a “private IP.”

Both options can be made redundant through:
- On Linux&reg;, a "bonding interface"
- On Windows Server, a Windows Network Interface Card (NIC) Teaming

Depending on your use case, a Public IP might be acceptable for proof-of-concept (PoC) landscapes because the external IP might be easier to use. However, this arrangement has some implications:
- A potential security risk exists even though a basic firewall is installed and preconfigured.
- If you want to use a public interface, be sure to choose a sufficiently high value for **Public Bandwidth** when you order your server. This value determines the total amount of data that can be transferred through the interface during a one-month period. You either need to know the amount of data that is transferred at least by the order of magnitude or switch to the second option.

Access to the resources on the {{site.data.keyword.cloud_notm}} Classic Infrastructure is available with greater security:
- Using the SSL Virtual Private Network (VPN), default available through the {{site.data.keyword.cloud_notm}} Classic Infrastructure console
- Using the IPSec Virtual Private Network (VPN)
- Using a Gateway Appliance with Firewall, Network Address Translation (NAT)
- Direct Link

These connectivity options can provide you with a higher bandwidth, which help you in transferring larger amounts of data into an {{site.data.keyword.cloud_notm}} data center.

It is advised that your networking department contact [{{site.data.keyword.cloud_notm}} Support](/docs/account?topic=account-using-avatar#getting-support) after you determine the layout of your landscape and the connectivity that is required on the SAP application layer.
{: note}


## Storage
{: #classic-env-storage}

Within Classic Infrastructure, three types of storage are available:
- Local Disk Storage (SSD)
- Network Block Storage (LUNs only) that uses {{site.data.keyword.cloud_notm}} Block Storage (Classic)
- Network File Storage (file system installed) that uses {{site.data.keyword.cloud_notm}} File Storage (Classic)

Local disk storage is provided with your {{site.data.keyword.baremetal_short}} and available in your choice of RAID configuration (by using a dedicated RAID Controller). It is ideal for storage-intensive applications with high I/O needs, such as an OS, database, and application software. This storage is the perfect companion for SAP HANA workloads.

Bare Metals with local disk storage use disks physically inserted into the drive bays; it is not using a SmartNIC to present network block storage as if it were local disk storage.
{: note}

In addition to the local storage, you might require network storage (to expand beyond the Local SSD Disk total capacity, or for redundancy and backups). These require more network consumption to access Network Block Storage and Network File Storage that are using the same physical network interfaces and the impact should be validated.

Both {{site.data.keyword.cloud_notm}} {{site.data.keyword.blockstorageshort}} or {{site.data.keyword.filestorage_full_notm}} can serve as either backup space or as storage for extra software components that are installed on your server.

All {{site.data.keyword.cloud_notm}} {{site.data.keyword.blockstorageshort}} or {{site.data.keyword.filestorage_full_notm}} is selected based on capacity (GB) and performance (IOPS) measurements.

IOPS are measured based on 16 KB block size with a 50/50 read/write mix. To achieve a maximum I/O throughput, it's advisable to look at the tier and custom profiles available for storage and find the optimal combination of size and IOPS.

Block Storage for Classic volumes can be provisioned from 20 GB to 12 TB with two options:
- **Endurance** volumes that feature pre-defined performance levels (tiered IOPS profiles)
- **Performance** volumes that have custom IOPS
[See details here.](/docs/BlockStorage?topic=BlockStorage-orderingBlockStorage&interface=ui)

If you need more than the initially provisioned storage in your server, you can attach extra volumes later. Contact [{{site.data.keyword.cloud_notm}} Support](/docs/account?topic=account-using-avatar#getting-support) for extension options if the attached storage is insufficient for your workload.

For more information, see [Getting started with {{site.data.keyword.blockstorageshort}}](/docs/BlockStorage?topic=BlockStorage-getting-started#getting-started) and [Getting started with {{site.data.keyword.filestorage_full_notm}}](/docs/FileStorage?topic=FileStorage-getting-started#getting-started).


## Compute
{: #classic-env-compute}

Three primary types of IaaS are available within the {{site.data.keyword.cloud_notm}} Classic Infrastructure:
1. Bare Metal server
2. Classic Virtual Servers (not SAP certified)
3. VMware, available in two options:
   - **Intel Bare Metal and VMware vSphere (ESXi OS)**, requires manual VMware setup and configuration
   - **{{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated**

Bare Metal and both VMware options are SAP-certified.

Classic Virtual Servers are not SAP-certified. For this type of IaaS, the {{site.data.keyword.cloud_notm}} VPC Infrastructure environment provides Intel Virtual Servers which are SAP-certified.

For more information, see [Infrastructure certified for SAP](/docs/sap?topic=sap-iaas-offerings).
