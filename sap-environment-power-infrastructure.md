---

copyright:
  years: 2020
lastupdated: "2020-12-17"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads

subcollection: sap

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:note: .note}
{:tip: .tip}
{:important: .important}

# {{site.data.keyword.IBM_notm}} Power Systems Infrastructure environment introduction
{: #power-env-introduction}

This is a complementary offering from {{site.data.keyword.IBM_notm}} Power Systems, with low latency access to {{site.data.keyword.cloud_notm}} services
{: note}

An Infrastructure-as-a-Service (IaaS) environment consists of many components - primarily compute, storage, and network from a specified region (such as the US) and a designated site location (also referred to as zone, which is a data center site).


## Deployment and management
{: #power-env-deployment-and-management}

{{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} is an {{site.data.keyword.IBM_notm}} Power Systems enterprise Infrastructure-as-a-service (IaaS) offering.

These {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s are physically located with low-latency connectivity to the {{site.data.keyword.cloud_notm}} Classic Infrastructure or VPC Infrastructure. In the data centers, the {{site.data.keyword.powerSys_notm}}s are separated from the rest of the {{site.data.keyword.cloud_notm}} servers with separate networks and direct-attached storage. This infrastructure design enables {{site.data.keyword.powerSys_notm}}s to maintain key enterprise software certification and support as its architecture is identical to certified on-premises infrastructure. The internal networks are fenced but have connectivity options to the rest of environments and services on {{site.data.keyword.cloud_notm}}.

{{site.data.keyword.IBM_notm}} Power Systems Infrastructure offerings (such as {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}) are deployed using the [IBM Power Infrastructure console available through {{site.data.keyword.cloud_notm}}](https://cloud.ibm.com/catalog/services/power-systems-virtual-server){: external}.

Alternatively, you can create and manage deployments with any of the following methods:
- IBM Power Infrastructure plug-in for {{site.data.keyword.cloud_notm}} CLI
- IBM Power Infrastructure API calls with an {{site.data.keyword.cloud_notm}} API key
- [Terraform Provider for {{site.data.keyword.cloud_notm}}](/docs/ibm-cloud-provider-for-terraform) with an {{site.data.keyword.cloud_notm}} API key

For more information, see [Managing {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s (IAM)](/docs/power-iaas?topic=power-iaas-managing-resources-and-users).


## Locations - Data centers
{: #power-env-locations-data-centers}

With data centers across North and South America, Europe, Asia, and Australia, you can provision IBM Power Infrastructure resources where (and when) you need them.

You can choose from multiple data centers globally.

Each data center with IBM Power Infrastructure uses a separate enterprise-grade network which is connected using {{site.data.keyword.cloud_notm}} Direct Link to the {{site.data.keyword.cloud_notm}} global private network, making data transfers faster and more efficient anywhere in the world.

For more information about {{site.data.keyword.cloud_notm}} availability zones / data centers and points of presence (PoPs) where IBM Power Infrastructure can be connected to, see the [global regions and availability zones / data centers map](https://www.ibm.com/cloud/data-centers){: external}.


## Networking
{: #power-env-networking}

The {{site.data.keyword.IBM_notm}} Power Systems Infrastructure network, is built upon IBM Power's enterprise-grade secure networking hardware and connectivity; it can be bridged to the separate {{site.data.keyword.cloud_notm}} networks (either Classic Infrastructure network or VPC Infrastructure network).

| IBM Power Virtual Server Group network on {{site.data.keyword.cloud_notm}} |
| -- |
| Global |
| Resource Group |
| Region |
| Datacenter |
| Datacenter colocation Room |
| VLAN (Public and Private) `used internally, effectively transparent to the administrator` |
| Subnet (Public and Private) |
{: caption="Networking component layers overview" caption-side="top"}

Connectivity is available to the {{site.data.keyword.IBM_notm}} Power Systems Infrastructure network leveraging connectivity options available from leading global network providers and TelCo providers in addition to the connectivity options available using the {{site.data.keyword.cloud_notm}} global footprint of more than 60 {{site.data.keyword.cloud_notm}} data centers and 28 points of presence (PoPs).


### Networking VLANs and Subnets
{: #power-env-networking-vlans-subnets}

The following information is a summary of [Configuring connectivity to Power Systems Virtual Server](/docs/power-iaas?topic=power-iaas-configuring-power) and [ Configuring and adding a private network subnet](/docs/power-iaas?topic=power-iaas-configuring-subnet).

The Virtual LAN (VLAN) on the {{site.data.keyword.IBM_notm}} Power Systems Infrastructure network, provides an enterprise-grade private network with full isolation and security. Each VLAN is Public or Private, and is assigned to a specific data center for a specific {{site.data.keyword.cloud_notm}} Account.

Each VLAN is associated with a single Subnet, for example:
- Public VLAN `(only one per region)`
    - Public Subnet
- Private VLAN
    - Private Subnet

A **Public Subnet** is the quickest and simplest way to connect to a {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} instance. The public network is protected by a firewall and only the following network protocols are allowed:
* SSH (port 22)
* HTTPS (port 443)
* Ping (ICMP)
* IBM i 5250 console emulation with SSL (port 992)

For the public network, other ports are blocked and can be routed through SSH.
{: note}

A **Private Subnet** is required for the connection of your virtual instances with systems outside of the {{site.data.keyword.IBM_notm}} data centers and for communication between multiple instances in an SAP three-tier system. This subnet is an internal network that can be used to connect individual {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s with each other.


If you want to separate different types of network traffic in your landscape, you can order more subnets (and their respective VLANs).

Keep in mind that the additional VLANs and subnets lead to traffic segregation, not increased performance; the increased performance is gained when additional VLANs and Subnets are associated to a host.
When multiple network interfaces are used, two performance increases are possible depending on the use case:
- Bonding of the network interfaces, creating a network path with the network throughput of both interfaces
- Traffic segregation using two networks, then isolating high volumes of traffic to a specific network which avoids a single network becoming a bottleneck. For example, a network for storage I/O only

With IBM Power Virtual Server as an example, a single threaded Linux network interface may reach 100% CPU Thread utilization even though the performance limits of the network path itself are still not reached. Additional network interfaces attached to another VLAN and Subnet will therefore increase performance

By default, your server has a Private IP address. If you use public subnets, a public IP address is assigned in addition.


### Networking connectivity
{: #power-env-networking-connectivity}

Issues with network connectivity can cause delays for your project if you do not plan properly, regardless of how you plan to use your system.

If you need to connect to your virtual server through the public internet, in other words, inbound to a virtual server, you can order **_Public IPs_** and attach them to the virtual server per vNIC.

**Various interconnectivity options available, for example:**
- {{site.data.keyword.IBM_notm}} Power Systems Infrastructure bridged to {{site.data.keyword.cloud_notm}} Classic Infrastructure
- {{site.data.keyword.IBM_notm}} Power Systems Infrastructure bridged to {{site.data.keyword.cloud_notm}} VPC Infrastructure
- {{site.data.keyword.IBM_notm}} Power Systems Infrastructure bridged to on-premises data centers by using IBM Direct Link


{{site.data.keyword.dlc_short}} is also used for closer integration into your backbone infrastructure, for more information, see [Connectivity to your SAP system landscape](/docs/sap?topic=sap-determine-access).

For more explanation information about {{site.data.keyword.IBM_notm}} Power Systems Infrastructure, see [IBM Power Virtual Servers](https://www.ibm.com/products/power-virtual-server){: external}.

Have your networking department contact the IBM Power Virtual Servers Support Team, handled by using [{{site.data.keyword.cloud_notm}} Support](/docs/account?topic=account-using-avatar#getting-support) after you determine the layout of your landscape and the connectivity that is required on the SAP application layer.
{: note}


## Storage
{: #power-env-storage}

Within {{site.data.keyword.IBM_notm}} Power Systems Infrastructure, there are two types of storage available:
- Block Storage Tier 1 (storage for mission critical application with best characteristics, e.g. NVMe flash storage)
- Block Storage Tier 3 (default storage type with optimized price/performance, e.g. SSD flash storage)

Do not mix storage types on a {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}.
{: note}

Block Storage is provided with your {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s and storage requirements are defined using input/output operations per second (IOPS) and the capacity (GB). High performance block storage is ideal for storage-intensive applications with high I/O needs, such as an OS, and database and application software. For SAP HANA workloads, Tier 1 of block storage is supported only.

All Block Storage is selected based on capacity (GB), currently the performance (IOPS) measurement cannot be fine-tuned.

Block Storage for {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s is powered underneath by [{{site.data.keyword.IBM_notm}} FlashSystem family](https://www.ibm.com/storage){: external} connected through the Fibre Channel protocol.

For further information, see [hardware specifications for {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s](/docs/power-iaas?topic=power-iaas-on-cloud-architecture#hardware-specifications-on-cloud).

Contact [{{site.data.keyword.cloud_notm}} Support](/docs/account?topic=account-using-avatar#getting-support) for extension options if the attached storage is insufficient for your workload.


## Compute
{: #power-env-compute}

There is only one type of IaaS available within the {{site.data.keyword.IBM_notm}} Power Systems Infrastructure environment:
1. {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s

The {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s are SAP-certified.

Currently following {{site.data.keyword.IBM_notm}} Power Systems hardware are utilised by {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s:
-	 S922 – optimized for SAP NetWeaver application server
-	 E980 – optimized for SAP HANA database server

For more information, see [Infrastructure certified for SAP](/docs/sap?topic=sap-iaas-offerings).
