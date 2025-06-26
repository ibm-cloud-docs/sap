---
copyright:
  years: 2020, 2025
lastupdated: "2025-06-26"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP NetWeaver, SAP HANA, Interconnectivity, Connectivity, Direct Link, VPC, VPN, Gateway

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Connectivity to your SAP system landscape
{: #determine-access}

{{site.data.keyword.cloud_notm}} has many connectivity options, including low latency worldwide connections between your private internal network and {{site.data.keyword.cloud_notm}}'s private network backbone.

You can securely connect to your infrastructure in multiple ways by using various protocols and ports, based on the infrastructure chosen and the different network types:

* **[Classic Infrastructure](/docs/sap?topic=sap-classic-env-introduction) network (formerly Softlayer network)**
    * Intel Bare Metal
    * Classic Intel Virtual Servers
    * VMware solutions

* **[VPC Infrastructure](/docs/sap?topic=sap-vpc-env-introduction) network**
    * Intel Virtual Servers (Generation 2)



## Interconnectivity between {{site.data.keyword.cloud_notm}} network
{: #determine-access-interconnectivity}

* **Transit Gateway**, handling interconnectivity across the {{site.data.keyword.cloud_notm}} private backbone between the networks with defined and controlled communication between resources worldwide across the {{site.data.keyword.cloud_notm}} network or across multiple {{site.data.keyword.cloud_notm}} accounts (useful for Managed Service Providers of SAP). Transit Gateways are used to support hybrid workloads, frequent data transfers, and private workloads by providing dynamic scalability, high availability, and private, in-transit data between hosts on {{site.data.keyword.cloud_notm}}.
    * Local routing, connect VPCs in same region
    * Global routing, connect VPCs across regions
    * Classic Infrastructure routing, connect to VLANs on Classic Infrastructure network
    * Cross-account connection (also known as account-to-account routing), connect VPCs across multiple {{site.data.keyword.cloud_notm}} accounts. See [Adding a cross-account connection (VPC only)](/docs/transit-gateway?topic=transit-gateway-adding-cross-account-connections&interface=ui)


## Connectivity options within the {{site.data.keyword.cloud_notm}} Classic Infrastructure network
{: #determine-access-connectivity-options-classic}

* **Classic SSL VPN**, basic SSL Tunnel with user/password to various PoP or Data centers, which is built in to {{site.data.keyword.cloud}} Classic Infrastructure, enabled per user account and is a good option for administrators during initial stages of deployments to {{site.data.keyword.cloud_notm}}. It is not for bulk users due to bandwidth caps.
* **Classic IPSecVPN**, service from the {{site.data.keyword.cloud_notm}} catalog, which can be provisioned and has advanced configuration options available for the IPsec Tunnel
* **{{site.data.keyword.cloud}} Direct Link for Classic Infrastructure**, the most robust connection available in varying types from your internal network to {{site.data.keyword.cloud_notm}}'s Availability Zones (also known as data centers) that use Network Service Providers, Point of Presence (PoP), or directly between the data center colocation Room (also called a Meet Me Room). This option is available up to 10 Gbps network throughput as a Routed OSI Layer-2/3 connection, and is designed for enterprise workload connections. `Note: If you are using VPC Infrastructure, this option is not necessary as {{site.data.keyword.cloud}} Direct Link 2.0 can also connect to Classic Infrastructure`
    * More information on [Direct Link 1.0](/docs/direct-link){: external}. To find from a specified site location to IBM Cloud and which network service providers are available, use [Cloud Pathfinder for IBM Cloud](https://ibm.find.cloud){: external} (powered by [Cloudscene](https://cloudscene.com/){: external})



{{site.data.keyword.cloud}} Classic Infrastructure offers firewalls that can provide your {{site.data.keyword.baremetal_short}} with a layer of security that is provisioned on demand and designed to eliminate service interruptions.

Within the Classic Infrastructure network, there are many Gateway Appliance and Firewalls to help prevent unwanted traffic from hitting your server, help reduce your attack vulnerability, and let your server resource be dedicated for its use. Based on your specific performance and feature requirements, you can choose one of the following options:
- [Hardware Firewall - shared](/docs/hardware-firewall-shared?topic=hardware-firewall-shared-getting-started),
- [Vyatta Virtual Router Appliance (VRA) - dedicated](/docs/virtual-router-appliance?topic=virtual-router-appliance-about-the-vra),
- [Fortinet FortiGate security hardware appliance - dedicated](/docs/fortigate-10g?topic=fortigate-10g-getting-started).


## Connectivity options within the {{site.data.keyword.cloud_notm}} VPC Infrastructure network
{: #determine-access-connectivity-options-vpc}

* **Floating IP**, a public internet IPv4 address, which can be configured with Security Groups to allow only certain network connection access on defined protocols and ports from specified source/target addresses. For initial tests option is often used, with more detail in the short guide on [Connecting to your Linux Virtual Server instance](/docs/vpc?topic=vpc-vsi_is_connecting_linux).
* **VPC IPSecVPN**, service from the {{site.data.keyword.cloud_notm}} catalog and deploys a VPN Gateway to a VPC and creating a VPN Connection with advanced configuration options available for the IPsec Tunnel; including integration with authentication strategies such as Microsoft Active Directory.
* **{{site.data.keyword.cloud}} Direct Link 2.0**, the latest enhancement and the most robust connection available, now with access to both Classic Infrastructure network and VPC Infrastructure network simultaneously from your internal network to {{site.data.keyword.cloud_notm}}'s Availability Zones (Data centers) that use Network Service Providers, Point of Presence (PoP), or directly between the data center colocation Room (also called a Meet Me Room). This is available up to 10 Gbps network throughput as a Routed OSI Layer-2/3 connection, and is designed for enterprise workload connections.
    * More information on [Direct Link 2.0](/docs/dl){: external}. To find from a specified site location to IBM Cloud and which network service providers are available, use [Cloud Pathfinder for IBM Cloud](https://ibm.find.cloud){: external} (powered by [Cloudscene](https://cloudscene.com/){: external})

### VPC VPN Gateway
{: #determine-access-vpc-vpn-gateway}

To connect to a virtual server on VPC through a secure IPsec tunnel, a VPN Gateway is created for the VPC. For a tutorial that describes the setup of connectivity to the VPC VPN Gateway using the open source `strongSwan` IPSec-based VPN client on an external network, refer to the tutorial ["Use a VPC/VPN gateway for secure and private on-premises access to cloud resources"](/docs/solution-tutorials?topic=solution-tutorials-vpc-site2site-vpn).

### {{site.data.keyword.cloud_notm}} Direct Link 2.0
{: #determine-access-vpc-direct-link}

Network back-bone infrastructure of a customer site can be directly connected to {{site.data.keyword.cloud_notm}}, by using {{site.data.keyword.cloud_notm}} Direct Link. On-premises resources can be connected to multiple VPCs, and VPC can provide Bring-your-own-IP or other custom IP ranges.

Technical requirements and restrictions exist in the availability of {{site.data.keyword.cloud_notm}} Direct Link in different regions. A detailed description of {{site.data.keyword.cloud_notm}} Direct Link can be found in [Getting started with {{site.data.keyword.cloud}} Direct Link](/docs/dl?topic=dl-get-started-with-ibm-cloud-dl).
{: note}

### Accessing the classic infrastructure
{: #determine-access-vpc-classic-access}

Optional setup.
{: note}

{{site.data.keyword.vpc_short}} infrastructure can access other resources on {{site.data.keyword.cloud_notm}} Classic Infrastructure, such as high-performance {{site.data.keyword.baremetal_long}} designed for SAP HANA.

You have multiple options to achieve this access, notably a one-to-one association, or {{site.data.keyword.tg_full}} with increased flexibility. This is described in the above section [Interconnectivity between {{site.data.keyword.cloud_notm}} network](#determine-access-interconnectivity).

All options require upgrading the {{site.data.keyword.cloud_notm}} account to be VRF-enabled.
{: important}

For more information on VPC access to Classic Infrastructure, see [Setting up access to classic infrastructure](/docs/vpc?topic=vpc-setting-up-access-to-classic-infrastructure). For more information on {{site.data.keyword.tg_short}}, see [Getting started with {{site.data.keyword.tg_full_notm}}](/docs/transit-gateway?topic=transit-gateway-getting-started).

## Network connectivity and network security for SAP systems running in IBM {{site.data.keyword.powerSys_notm}}
{: #powervs-network-connectivity-and-security}

### Network connectivity
{: #powervs-network-connectivity}

To arrange connection through to IBM Cloud or an on-premises network, a private subnet must exist for the IBM {{site.data.keyword.powerSys_notm}}.

- {{site.data.keyword.powerSys_notm}} workspace will be connected over [Transit gateway](/docs/transit-gateway?topic=transit-gateway-getting-started) to:
   1. The Virtual Private Cloud(VPC) with VPC instances (Windows, HANA Studio)
   1. Other {{site.data.keyword.powerSys_notm}} workspaces
   1. On-premises networks through [Direct Link](/docs/dl?topic=dl-get-started-with-ibm-cloud-dl).

- By using **local** transit gateway, the networks in the **same region** are connected. In order to connect networks from **another** regions, **global** transit gateway must be used.

- Other IBM Cloud services may be reached directly through public IBM Cloud service IPs or hostnames or over [Virtual Private Endpoints](/docs/overview?topic=overview-endpoints-support) configured in connected VPC (like IBM Cloud Object Storage etc.)

### Network connectivity over VPN
{: #powervs-network-connectivity-vpn}

IBM {{site.data.keyword.powerSys_notm}} do not support a native VPN Service. However, IBM Cloud provides two [VPN services](/docs/vpc?topic=vpc-vpn-overview):
1. VPN for VPC offers site-to-site gateways, which connect your on-premises network to the IBM Cloud VPC network.
2. Client VPN for VPC offers client-to-site servers, which allow clients on the internet to connect to VPN servers, while still maintaining secure connectivity.

- Once connectivity to VPC Network is established, it is then easier to reach the IBM {{site.data.keyword.powerSys_notm}} instances provided that the VPC and {{site.data.keyword.powerSys_notm}} workspace are attached to same Transit Gateway.
- Outgoing public internet and external network traffic from {{site.data.keyword.powerSys_notm}} instances goes over the internet proxy service running on Virtual Server Instance (VSI) in VPC.
- Incoming public internet and external network traffic to {{site.data.keyword.powerSys_notm}} instances are achieved using [Cloud Internet Service](/docs/cis?topic=cis-getting-started) and [Load Balancer Service](/docs/vpc?topic=vpc-nlb-vs-elb)


### Network Security
{: #powervs-network-security}

By considering all network connectivity options, we differentiate between connections coming to {{site.data.keyword.powerSys_notm}} workspace over VPC and directly through transit gateway.

On the target side (IBM Cloud networks or the on-premises network), it is required to perform the necessary configuration of the network security and permit connections to be established to/from IBM {{site.data.keyword.powerSys_notm}}s.
