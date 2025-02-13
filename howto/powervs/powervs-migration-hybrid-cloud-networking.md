---
copyright:
  years: 2024, 2025
lastupdated: "2025-02-13"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, VPN, Virtual Private Network, VPN for VPC, Direct Link, DL, Cloud connection, Software Defined Network, SDN, SD, Hybrid Cloud, Migration, AIX, Linux, RHEL, SuSE
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Hybrid Cloud Network Consideration for SAP applications on IBM {{site.data.keyword.powerSys_notm}}
{: #sapmig-hybrid-cloud-networking}

A hybrid cloud environment combines the on-premises site with {{site.data.keyword.powerSysFull}}.
To join SAP S/4HANA systems between both sites, multiple network connection options are available.

Due to typically huge SAP S/4HANA database sizes, a minimum network bandwidth is mandatory for productive systems.
{: shortdesc}

The following topics are covered in the following sections:

- A rough network migration minimum bandwidth considerations for SAP workloads
- A brief overview of hybrid cloud connection options that are available that includes links to the setup descriptions
- Extra required network services to migrate an SAP system
- Test of network connections for SAP workload

This information doesn't replace existing SAP or other vendor documentation.
{: note}

## Network bandwidth considerations
{: #sapmig-hybrid-cloud-networking-bandwidth}

Bandwidth is determined by the required connection speed between on-premises and {{site.data.keyword.cloud}} depends on project scenario, network protocols, migration path, and project target.

A hybrid scenario with collaborating systems in {{site.data.keyword.cloud}} and on-premises needs different requirements when compared with a one-time move of a system to {{site.data.keyword.cloud}} for isolated testing.

Network bandwidth is the main limiting factor for

- Full synchronization time of the database servers
- Delta replication time
- Outage time during takeover

The full synchronization is necessary as well for the initial upload as to fix synchronization issues when they occur.
{: important}

Discuss bandwidth with your network provider and network specialist, as every connection type is different.

### Example - Synchronization time estimation for an IPsec connection
{: #sapmig-hybrid-cloud-networking-ipsec-example}

As a rough estimation, consider an example that uses an IPsec VPN connection over a dedicated internet connection.
A full synchronization time can be estimated by using the following formula:

Time = 200% * (data transfer size) / Bandwidth

Estimation example.

- Data base size: 922 GB
- Expected changes while data transfers: 10%
- Data transfer size: 110% * 922 GB = 1024 GB
- Bandwidth: 500 MBit/s = 0.061 GB/s
- Estimated transfer time is 200% * 1024/0.061 = 33554 sec = 9:19 hours

The calculated +100% overhead is a broad approach for necessary TCP/IP headers, IPsec headers, and extra data transfers.
It does not consider data retransmission or shared network usage.
This estimation is intended to help determine the required minimum bandwidth.

The following table lists the calculated transfer times for a 1-Terabyte database:

| Data base transfer size |  Bandwidth | Overhead | Time for full sync |
| ---------------: | ---------: | -------: | -----------------: |
|       1024 GB |  10 GBps |    +100% |        00:27 hours |
|       1024 GB |   5 GBps |    +100% |        00:54 hours |
|       1024 GB |   2 GBps |    +100% |        02:16 hours |
|       1024 GB |   1 GBps |    +100% |        04:33 hours |
|       1024 GB | 500 MBps |    +100% |        09:19 hours |
|       1024 GB | 250 MBps |    +100% |        18:38 hours |
|       1024 GB | 150 MBps |    +100% |        31:38 hours |
{: caption="Calculated estimated time for IPsec VPN full synchronization" caption-side="bottom"}

As an example, if an SAP system needs to be productive again within 8 hours after a synchronization error occurred, for a 922 GB database size, the calculated network bandwidth must be greater than 500 MB/s.

This estimation is based on assumptions. It is not qualified or intended to determine the accurate replication time. Consult your network provider, or measure the time of a test replication to determine reliable values specific for your network setup.
{: important}

## Network connection option overview
{: #sapmig-hybrid-cloud-networking-connection-option-overview}

A hybrid cloud setup in general combines the customer network with a customer network segment in {{site.data.keyword.cloud}}. It is an extension of the existing customer network, as if you would connect a remote data center.

Connecting an on-premises network with IBM Power Servers in {{site.data.keyword.powerSys_notm}} in {{site.data.keyword.cloud}} is described in
[Network architecture diagrams](/docs/power-iaas?topic=power-iaas-network-architecture-diagrams).

If you are new to {{site.data.keyword.powerSys_notm}}, architectures from the [Power Edge Router (PER) use cases](/docs/power-iaas?topic=power-iaas-network-architecture-diagrams#per-use-cases) are the recommended ones and are the easiest to implement. If you have an existing {{site.data.keyword.powerSys_notm}} workspace, you might either consider migrating to the newer PER setup or inspect the [{{site.data.keyword.powerSys_notm}} networking environment](/docs/power-iaas?topic=power-iaas-network-architecture-diagrams#networking-environment) description.

Both PER and non-PER architectures offer these types of network connection options:

### {{site.data.keyword.cloud}} Direct Link 2.0
{: #sapmig-hybrid-cloud-networking-dl}

{{site.data.keyword.cloud}} Direct Link is a suite of offerings that enables the creation of direct, private connections between your on-premises network and {{site.data.keyword.cloud}}, without traversing the public internet. For more information, see [Getting started with {{site.data.keyword.cloud}}Direct Link (2.0)](/docs/dl?topic=dl-get-started-with-ibm-cloud-dl) and [Connecting an on-premises data center](/docs/power-iaas?topic=power-iaas-network-architecture-diagrams#per-on-orem).

### Internet VPN
{: #sapmig-hybrid-cloud-networking-vpn}

Internet VPN uses the public internet to connect on-premises networks and {{site.data.keyword.cloud}} networks through a VPN. At {{site.data.keyword.cloud}} the VPN is either terminated by a gateway device, gateway appliance, or a VPN as a service (VPNaaS). Read the IBM {{site.data.keyword.powerSys_notm}} specific [Creating VPN connections](/docs/power-iaas?topic=power-iaas-VPN-connections) article.

The easiest way to establish a site-to-site VPN is to start with the VPN-as-a-service (VPNaaS).
Read the article [FAQs for site-to-site VPN gateways](/docs/vpc?topic=vpc-faqs-vpn#faq-vpn-10) for more details.

## Required network services
{: #sapmig-network-services-required}

A hybrid cloud setup extends your company network to your instances in {{site.data.keyword.cloud}}.
Servers and clients in this hybrid network need to communicate with each other.

A hybrid cloud setup helps with the following points:

- Routing between both networks works
- Firewall rules permit traffic through required ports
- DNS name resolution for both networks works

## Testing network connections for an SAP workload
{: #sapmig-basic-network-connection-test}

SAP provides a tool ```niping``` that checks the network interface up to OSI layer 4, which means it verifies that transport layer TCP/UDP packages can be exchanged.
This SAP tool demonstrates a working connection, even if ICMP packages are dropped and a simple ping would fail.

Refer to [SAP Note 500235 - Network Diagnosis with NIPING](https://me.sap.com/notes/500235){: external} for further NIPING details.

For migrating SAP systems by using SAP HANA database replication between SAP HANA databases on-premises and {{site.data.keyword.powerSys_notm}}, the source and target servers are the two database servers.
The following two commands verify the connection between a source- and a target-system.

1. *NIPING server:*
   Use the following command to run the `niping` server on one system, for example named `host1`.
   ```sh
   niping -s
   ```
   {: pre}

1. *NIPING client:*
   Then, run the niping client application on the second system to verify the connection to the first system `host1`.
   ```sh
   niping -c -H host1
   ```
   {: pre}

An SAP Router is typically not involved in server-to-server communication. Consult the niping documentation to determine the proper connection string if you are using an SAP Router.
{: note}
