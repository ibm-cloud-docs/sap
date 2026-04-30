---
copyright:
  years: 2025, 2026
lastupdated: "2026-04-29"
keywords: SAP, Bare Metal, Classic, networking, network planning, Classic Infrastructure, VLANs, subnets, connectivity, private network, Direct Link, VPN, network topology
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Network connectivity requirements for SAP workloads on {{site.data.keyword.baremetal_short}} in Classic Infrastructure
{: #plan-network-connectivity-classic-bm}

This document provides guidance on planning network connectivity requirements for SAP workloads on {{site.data.keyword.baremetal_short}} in Classic Infrastructure. It focuses on enabling secure, high‑performance, and highly available communication between {{site.data.keyword.IBM_notm}}, on‑premises, and external systems.
{: shortdesc}

Key planning considerations include:
- Designing appropriate virtual local area networks (VLANs), subnet layouts, and network security.
- Implementing reliable connectivity to on-premises systems by using VPN, Direct Link, or third-party services.
- Ensuring low latency for SAP application and database tiers.
- Considering {{site.data.keyword.cloud_notm}} Classic infrastructure-specific networking features like bonding and traffic segregation.

Proper planning helps to achieve the required network throughput and availability for production landscapes. It also ensures compliance with enterprise security and network segmentation requirements.

{{site.data.keyword.ibm_cloud_sap}} provides comprehensive networking capabilities to support your SAP landscape requirements.

The following sections outline {{site.data.keyword.ibm_cloud_sap}} portfolio design considerations for network connectivity on {{site.data.keyword.baremetal_short}} in {{site.data.keyword.cloud_notm}} Classic Infrastructure.

## Overview
{: #network-overview}

The {{site.data.keyword.cloud_notm}} Classic Infrastructure network is robust, secure, and flexible. It is built upon matured networking principles combined with the latest networking hardware. All network components within the {{site.data.keyword.cloud_notm}} data centers are fully provisioned, maintained, and administered by {{site.data.keyword.IBM_notm}}.

The {{site.data.keyword.cloud_notm}} Classic Infrastructure network provides connectivity across a global footprint of more than 60 data centers and 28 points of presence (PoPs), with connections to leading global network providers.

{{site.data.keyword.cloud_notm}} Classic Infrastructure was formerly known as SoftLayer.
{: note}

### Network architecture
{: #network-architecture}

The {{site.data.keyword.cloud_notm}} Classic Infrastructure network consists of three distinct and redundant network architectures that are seamlessly integrated in a secured network-within-a-network topology:

Public network
:   The public network provides carrier-grade internet connectivity to multi-home backbone carriers. Traffic travels directly across the {{site.data.keyword.cloud_notm}} data center network backbone, minimizing network hops and latency.

Private network
:   The private network provides complete control of secured networking traffic without performance degradation. It includes:
:   - Host-to-host communication within private VLANs with free and unmetered bandwidth.
:   - Backend services access such as OS updates, NTP, DNS, network storage with free and unmetered bandwidth.
:   - VPN and direct connections to VLANs with free and unmetered bandwidth.
:   - Data center-to-data center interconnectivity with free and unmetered bandwidth.

Management network
:   The management network provides out-of-band management accessible through VPN or Direct Link. It enables remote console access through the Intelligent Platform Management Interface (IPMI) network interface with free and unmetered bandwidth.

### Network hierarchy
{: #network-hierarchy}

The {{site.data.keyword.cloud_notm}} Classic Infrastructure networking components are organized in the following hierarchy:

1. **Global**: {{site.data.keyword.cloud_notm}} global network backbone.
1. **Region**: Geographic location grouping multiple data centers.
1. **Data center**: Physical facility within a region.
1. **Data center pod**: Logical grouping within a data center.
1. **VLAN**: Virtual LAN (public and private) specific to a data center and pod.
1. **Subnet**: IP address range within a VLAN (public and private).

## VLANs and subnets
{: #network-vlans-subnets}

### VLANs
{: #network-vlans}

Virtual local area networks (VLANs) provide enterprise-grade private networks with full isolation and security. Each VLAN is either public or private and is assigned to a specific data center for a specific {{site.data.keyword.cloud_notm}} account.

Key characteristics:
- Each VLAN is specific to a data center and pod.
- VLANs can contain multiple subnets.
- Subnets within the same VLAN can communicate by default.
- Communication between VLANs requires a Gateway Appliance.

For more information, see [Getting started with VLANs](/docs/vlans?topic=vlans-getting-started) and [About VLANs](/docs/vlans?topic=vlans-about-vlans).

### Subnets
{: #network-subnets}

{{site.data.keyword.cloud_notm}} Classic Infrastructure supports different types of subnets:

Primary subnets
:   Primary subnets are automatically assigned when resources are provisioned into public and private VLANs. They have certain [limitations](/docs/subnets?topic=subnets-about-subnets-and-ips#primary-subnets).

Secondary portable subnets
:   Secondary portable subnets append a new subnet to VLANs. They provide IP addresses that are assignable to any resource within a VLAN. They enable floating IPs across multiple resources.

Secondary static subnets (public only)
:   Secondary static subnets append a new subnet to a public VLAN. They provide IP addresses for assignment to a single resource by using an existing primary or portable IP as the routing endpoint.

Global IP addresses
:   Global IP addresses are single internet-accessible IPs from the {{site.data.keyword.cloud_notm}} private backbone that can be assigned to any VLAN worldwide.

For SAP workloads, consider creating separate subnets for:
- SAP application and database servers.
- Management and administration systems.
- Backup and disaster recovery systems.
- Storage network traffic when using separate network interfaces.

For more information, see [Getting started with subnets and IPs](/docs/subnets?topic=subnets-getting-started) and [About subnets and IPs](/docs/subnets?topic=subnets-about-subnets-and-ips).

## Network interfaces and bonding
{: #network-interfaces-bonding}

Each provisioned bare metal server has network interfaces available at speeds of 100 Mbps, 1 Gbps, or 10 Gbps.

Interface options
:   - External interface with public IP address.
:   - Internal interface with private IP address (RFC 1918 compliant).
:   - Single internal interface with private IP only.

Network redundancy
:   Both public and private interfaces can be made redundant through:
:   - Linux bonding interface.
:   - Windows NIC Teaming.

Network bonding
:   Physical network interfaces can be bonded using Link Aggregation Control Protocol (LACP) for:
:   - Increased throughput (combining bandwidth of multiple interfaces).
:   - Redundancy and high availability.
:   - Automatic failover between interfaces.

For SAP HANA scale-out, bonding is typically configured with 10 Gbps interfaces to provide sufficient bandwidth for inter-node communication.

## Network connectivity options
{: #network-connectivity-options}

### Connectivity to on-premises networks
{: #network-connectivity-on-premises}

{{site.data.keyword.cloud_notm}} Classic Infrastructure provides multiple connectivity options to integrate on‑premises networks.

Classic SSL VPN
:   Classic SSL VPN provides a basic SSL tunnel with user and password to various PoPs or data centers. It is built into {{site.data.keyword.cloud_notm}} Classic Infrastructure. It is suitable for administrators during the initial deployment stages. It is not suitable for bulk users due to bandwidth caps.

Classic IPSec VPN
:   Classic IPSec VPN is a service from {{site.data.keyword.cloud_notm}} catalog with advanced configuration options for IPsec tunnels. It is suitable for moderate bandwidth requirements.

{{site.data.keyword.dl_full_notm}} for Classic Infrastructure
:   {{site.data.keyword.dl_full_notm}} for Classic Infrastructure provides the most robust connection option, supporting up to 10 Gbps as a routed OSI Layer‑2/3 connection. It is designed for enterprise workload connectivity. It is available in two types:
:   - **Direct Link Dedicated**: Provides a direct fiber connection to {{site.data.keyword.cloud_notm}}.
:   - **Direct Link Connect**: Provides connectivity through supported network service providers.

For more information, see [Direct Link 1.0](/docs/direct-link). To find network service providers from your location, use [Cloud Pathfinder for {{site.data.keyword.cloud_notm}}](https://ibm.find.cloud){: external}.

### Connectivity to VPC infrastructure
{: #network-connectivity-vpc-classic}

{{site.data.keyword.cloud_notm}} Classic Infrastructure can connect to {{site.data.keyword.vpc_short}} infrastructure through:

{{site.data.keyword.tg_full_notm}}
:   {{site.data.keyword.tg_full_notm}} provides flexible connectivity between {{site.data.keyword.cloud_notm}} Classic Infrastructure and {{site.data.keyword.vpc_short}} with increased routing capabilities. It is recommended for complex multi-environment architectures.

{{site.data.keyword.vpc_short}} Classic Access
:   {{site.data.keyword.vpc_short}} Classic Access provides a one-to-one association between {{site.data.keyword.vpc_short}} and {{site.data.keyword.cloud_notm}} Classic Infrastructure. It is a simpler setup for basic connectivity needs.

All options require upgrading the {{site.data.keyword.cloud_notm}} account to be VRF-enabled.
{: important}

For more information, see [Setting up access to Classic Infrastructure](/docs/vpc?topic=vpc-setting-up-access-to-classic-infrastructure) and [Getting started with {{site.data.keyword.tg_full_notm}}](/docs/transit-gateway?topic=transit-gateway-getting-started).

## Network security
{: #network-security}

{{site.data.keyword.cloud_notm}} Classic Infrastructure offers multiple firewall and security options:

Hardware Firewall (shared)
:   A shared firewall providing basic protection. For more information, see [Hardware Firewall (Shared)](/docs/hardware-firewall-shared?topic=hardware-firewall-shared-getting-started).

Vyatta Virtual Router Appliance (dedicated)
:   A dedicated virtual router appliance with advanced routing and firewall capabilities. For more information, see [About the VRA](/docs/virtual-router-appliance?topic=virtual-router-appliance-about-the-vra).

FortiGate Security Appliance (dedicated)
:   A dedicated hardware security appliance with comprehensive security features. For more information, see [Getting started with FortiGate Security Appliance](/docs/fortigate-10g?topic=fortigate-10g-getting-started).

Gateway Appliances
:   Provide demilitarized zone (DMZ) configuration with public-facing and private-facing VLANs for enhanced security.

### Traffic segregation
{: #network-traffic-segregation}

Multiple VLANs and subnets are used to separate different types of network traffic:

- Subnets within VLANs provide basic traffic separation.
- Additional VLANs are used when strict isolation is required.
- Gateway Appliances control traffic flow between VLANs.
- Multiple network interfaces support traffic segregation and performance optimization.

Traffic segregation strategies include:
- Separating production and non-production traffic.
- Isolating storage I/O traffic to dedicated network interfaces.
- Dedicating network paths for SAP HANA inter-node communication.
- Separating backup traffic from production traffic.

## Network performance considerations
{: #network-performance}

Bandwidth
:   Network interfaces available at 100 Mbps, 1 Gbps, or 10 Gbps. For SAP HANA scale-out, 10 Gbps interfaces are recommended for inter-node communication. Bonding multiple interfaces can increase total bandwidth.

Latency
:   Intra-data center latency is typically sub-millisecond. Inter-data center latency varies based on geographic distance. Deploy SAP application and database tiers in the same data center when possible.

Public bandwidth considerations
:   If using public interfaces, select sufficient public bandwidth allocation when ordering servers. This determines the total data transfer allowed per month. Consider switching to private network with VPN or Direct Link for production workloads.

SAP HANA scale-out requirements
:   - Minimum 10 Gbps network bandwidth between nodes.
:   - Low latency (sub-millisecond) for inter-node communication.
:   - Separate networks for client, storage, and inter-node traffic.
:   - Bonded interfaces using LACP for redundancy and increased throughput.

## Network topology patterns
{: #network-topology-patterns}

Single data center
:   A single data center topology is the simplest topology with all SAP components in one data center. It provides the lowest latency between components and is suitable for development, testing, and production with local redundancy.

Multi-data center
:   A multi-data center topology distributes SAP components across multiple data centers for disaster recovery. It introduces higher latency between data centers and uses Direct Link for reliable inter-data center connectivity.

Hybrid cloud
:   A hybrid cloud topology integrates on-premises SAP systems with {{site.data.keyword.cloud_notm}} Classic Infrastructure. It requires Direct Link or VPN connectivity and uses {{site.data.keyword.tg_short}} for complex multi-environment architectures.


## References
{: #network-references}

For more information about {{site.data.keyword.cloud_notm}} Classic Infrastructure networking, see the following resources:

- [Getting started with VLANs](/docs/vlans?topic=vlans-getting-started)
- [About VLANs](/docs/vlans?topic=vlans-about-vlans)
- [Getting started with subnets and IPs](/docs/subnets?topic=subnets-getting-started)
- [About subnets and IPs](/docs/subnets?topic=subnets-about-subnets-and-ips)
- [Direct Link 1.0](/docs/direct-link)
- [Hardware Firewall (Shared)](/docs/hardware-firewall-shared?topic=hardware-firewall-shared-getting-started)
- [About the VRA](/docs/virtual-router-appliance?topic=virtual-router-appliance-about-the-vra)
- [Getting started with FortiGate Security Appliance](/docs/fortigate-10g?topic=fortigate-10g-getting-started)
- [Setting up access to classic infrastructure](/docs/vpc?topic=vpc-setting-up-access-to-classic-infrastructure)
- [Getting started with {{site.data.keyword.tg_full_notm}}](/docs/transit-gateway?topic=transit-gateway-getting-started)
