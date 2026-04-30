---
copyright:
  years: 2025, 2026
lastupdated: "2026-04-29"
keywords: SAP, Bare Metal, VPC, networking, network planning, VPC networking, subnets, security groups, ACL, connectivity, private network, VPE, Direct Link, VPN, network topology, PCI, VLAN
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Network connectivity requirements for SAP workloads on {{site.data.keyword.bm_is_short}} in {{site.data.keyword.vpc_short}}
{: #plan-network-connectivity-vpc-bm}

This document provides guidance on planning network connectivity requirements for SAP workloads on {{site.data.keyword.bm_is_full}} in {{site.data.keyword.vpc_full}} (VPC). It focuses on enabling secure, high-performance, and highly available communication between {{site.data.keyword.IBM_notm}}, on‑premises, and external systems.
{: shortdesc}

Key planning considerations include:
- Designing appropriate Virtual Private Clouds (VPCs), subnet layouts, security groups, and routes.
- Implementing reliable connectivity to on-premises systems by using {{site.data.keyword.vpn_vpc_short}}, Direct Link, or third-party services.
- Integrating with the {{site.data.keyword.cloud_notm}} network.
- Ensuring low latency for SAP application and database tiers.
- Considering bare metal-specific networking features like PCI interfaces and virtual local area network (VLAN) tagging.

Proper planning helps to achieve the required network throughput and availability for production landscapes. It also ensures compliance with enterprise security and network segmentation requirements.


{{site.data.keyword.ibm_cloud_sap}} provides comprehensive networking capabilities to support your SAP landscape requirements.

The following sections outline {{site.data.keyword.ibm_cloud_sap}} portfolio design considerations for network connectivity on {{site.data.keyword.bm_is_short}} in {{site.data.keyword.vpc_short}}.

## Overview
{: #network-overview}

The network architecture for {{site.data.keyword.bm_is_short}} operates within the {{site.data.keyword.vpc_full}} infrastructure.
All network components within the {{site.data.keyword.cloud_notm}} data centers are fully provisioned, maintained, and administered by {{site.data.keyword.IBM_notm}}.

{{site.data.keyword.vpc_short}} delivers enterprise-grade networking capabilities and supports a broad range of connectivity options that are designed to meet the requirements of diverse SAP system deployment scenarios.
The architecture enables both private and public network connectivity and provides configurable routing and security controls to support your specific networking needs.

For comprehensive network architecture information, refer to [About Networking for VPC](/docs/vpc?topic=vpc-about-networking-for-vpc) and [Bare Metal Servers network](/docs/vpc?topic=vpc-bare-metal-servers-network).

### Key networking concepts for VPC
{: #network-concepts}

Understanding the following VPC networking concepts is essential for planning your SAP landscape:

- **Virtual Private Cloud (VPC)**: An isolated network environment within {{site.data.keyword.cloud_notm}} where you deploy your resources.
- **Subnets**: Subdivisions of your VPC's IP address range, associated with specific availability zones.
- **Availability zones**: Geographically dispersed locations within a region that provide fault tolerance and high availability.
- **Address prefixes**: IP address ranges assigned to availability zones within your VPC.
- **Security groups**: Instance-level firewalls that control inbound and outbound traffic for bare metal servers.
- **Network access control Lists (ACLs)**: Subnet-level firewalls that control traffic entering and leaving subnets.
- **Floating IPs**: Public IPv4 addresses that can be assigned to bare metal servers for internet access.
- **Public gateways**: Enable outbound internet connectivity for an entire subnet.
- **Virtual Private Endpoints (VPE)**: Private connectivity to {{site.data.keyword.cloud_notm}} services without traversing the public internet.

### Bare metal-specific networking features
{: #network-bare-metal-features}

{{site.data.keyword.bm_is_short}} provide additional networking capabilities beyond standard virtual server instances:

- **PCI network interfaces**: Bare metal servers support PCI network interfaces that provide direct hardware access for enhanced performance. Each PCI interface connects directly to the physical network adapter.
- **VLAN tagging**: Support for IEEE 802.1Q VLAN tagging enables network segmentation at the interface level. You can create multiple VLAN interfaces on a single PCI interface.
- **Multiple network interfaces**: Bare metal servers can be configured with multiple network interfaces for traffic separation and redundancy. The number of supported interfaces depends on the server profile.
- **Higher bandwidth**: Bare metal servers support higher network bandwidth compared to virtual server instances, suitable for demanding SAP HANA workloads. Network bandwidth scales with the server profile, with high-end profiles supporting up to 100 Gbps.
- **Consistent network performance**: Direct hardware access eliminates virtualization overhead, providing predictable network performance.
- **Jumbo frames**: Support for jumbo frames (MTU up to 9000 bytes) enables more efficient data transfer for large payloads, beneficial for SAP HANA replication and backup operations.
- **Allowed IP addresses**: You can configure additional IP addresses on network interfaces beyond the primary IP, useful for hosting multiple SAP instances or implementing virtual IP addresses for high availability clusters.

## Subnets and address planning
{: #network-subnets-address-prefixes-bm}

When you deploy a VPC in an availability zone, an [address prefix](/docs/vpc?topic=vpc-vpc-addressing-plan-design) is used for that specified zone. Each VPC zone contains one or more subnets where you deploy your bare metal servers.

For SAP workloads, consider creating separate subnets for different traffic types:
- SAP application and database servers.
- Management and administration systems.
- Backup and disaster recovery systems.

### Network interfaces on bare metal servers
{: #network-interfaces-bare-metal}

{{site.data.keyword.bm_is_short}} support multiple types of network interfaces, which you can use to separate different types of network traffic:

#### PCI and VLAN interfaces
{: #network-pci-vlan-interfaces}

Bare metal servers support PCI network interfaces (physical) and VLAN interfaces (virtual, using IEEE 802.1Q tagging):

PCI interfaces
:   - Primary PCI interface is automatically created and cannot be deleted.
:   - Secondary PCI interfaces can be added for traffic separation and redundancy.
:   - Each PCI interface attaches to a single subnet with a primary IP address.
:   - Support jumbo frames (MTU up to 9000 bytes).

VLAN interfaces
:   - Created on top of PCI interfaces with VLAN IDs (1-4094).
:   - Enable network segmentation without additional physical interfaces.
:   - Inherit security groups from the parent PCI interface.
:   - Useful for separating production, backup, and management traffic.

Allowed IP addresses
:   - Configure additional IP addresses beyond the primary IP on any interface.
:   - Essential for hosting multiple SAP instances or implementing virtual IPs for high availability clusters.
:   - Must be from the same subnet as the interface's primary IP address.

## Network connectivity options
{: #network-connectivity-options-bm}

{{site.data.keyword.vpc_short}} provides multiple connectivity options to meet different security, performance, and integration requirements for your SAP landscape.

### Connectivity within and between VPCs
{: #network-connectivity-vpcs-bm}

Resources within the same VPC communicate through the private network by default, subject to security group and ACL rules. For multi-VPC SAP landscapes, use {{site.data.keyword.tg_full_notm}} for Hub-spoke network topologies or VPC peering for simple two-VPC connections. For more information, see [Interconnectivity for VPC](/docs/vpc?topic=vpc-interconnectivity).

### Connectivity to on-premises networks
{: #network-connectivity-on-premises-bm}

Connecting an SAP landscape on {{site.data.keyword.bm_is_short}} to on-premises systems is essential for hybrid cloud scenarios. {{site.data.keyword.vpc_short}} provides multiple connectivity options:

#### VPN Gateway and Direct Link
{: #network-vpn-directlink-bm}

{{site.data.keyword.vpn_vpc_full}}
:   {{site.data.keyword.vpn_vpc_full}} provides IPsec-based encrypted connectivity over the public internet with up to 650 Mbps per tunnel. This option is suitable for development, test, disaster recovery, and administrative access. For more information, see [About VPN gateways](/docs/vpc?topic=vpc-using-vpn).

{{site.data.keyword.dl_full}}
:   {{site.data.keyword.dl_full}} provides dedicated private connectivity from 50 Mbps to 10 Gbps with lower latency and predictable performance. This option is recommended for production SAP systems, SAP HANA replication, and large data transfers. For more information, see [Getting started with {{site.data.keyword.cloud_notm}} Direct Link](/docs/dl?topic=dl-get-started-with-ibm-cloud-dl).

### Connectivity to IBM Cloud services
{: #network-connectivity-cloud-services-bm}

Use {{site.data.keyword.vpe_full}} to connect privately to {{site.data.keyword.cloud_notm}} services such as {{site.data.keyword.cos_short}}, {{site.data.keyword.databases-for}}, or {{site.data.keyword.keymanagementserviceshort}}, without traversing the public internet. {{site.data.keyword.vpe_short}} provides enhanced security, no bandwidth charges, and keeps traffic on the private network. For more information, see [About Virtual Private Endpoints for VPC](/docs/vpc?topic=vpc-about-vpe) and [End-to-end private connectivity using VPE](/docs/vpc?topic=vpc-end-to-end-private-connectivity-vpe).

### Internet connectivity
{: #network-internet-connectivity-bm}

Floating IPs
:   Assign public IPv4 addresses to specific bare metal servers for direct inbound and outbound internet access.

Public gateways
:   Enable outbound-only internet connectivity for all resources in a subnet (for example, for software updates and patches).

## Network security
{: #network-security-vpc-bm}

Securing your SAP landscape on {{site.data.keyword.bm_is_short}} requires implementing multiple layers of network security controls.

### Security groups
{: #network-security-groups-bm}

Security groups act as virtual firewalls that control inbound and outbound traffic at the network interface level.

Key characteristics:
- Applied to network interfaces on bare metal servers.
- Stateful firewall (return traffic is automatically allowed).
- Rules specify allowed traffic (default deny).
- Support for IP addresses, CIDR blocks, and other security groups as sources/destinations.
- Multiple security groups can be applied to a single network interface.

Best practices for SAP workloads:
- Create separate security groups for different SAP tiers (application, database, management).
- Use the principle of least privilege (allow only required traffic).
- Reference other security groups in rules to simplify management.
- Document the purpose of each security group and rule.
- Regularly review and update security group rules.

Example security group strategy:
- **SAP application tier**: Allow traffic from load balancers and SAP routers, allow outbound to database tier.
- **SAP database tier**: Allow traffic only from application tier, restrict outbound traffic.
- **Management tier**: Allow SSH/RDP from specific IP ranges, allow outbound for monitoring and updates.

For more information, see [Security in your VPC](/docs/vpc?topic=vpc-security-in-your-vpc).

### Network access control lists (ACLs)
{: #network-acls-bm}

Network ACLs provide subnet-level firewall capabilities that control traffic entering and leaving subnets.

Key characteristics:
- Applied at the subnet level.
- Stateless firewall (return traffic must be explicitly allowed).
- Rules specify both allowed and denied traffic.
- Rules are evaluated in order (lowest number first).
- Provide an additional layer of security beyond security groups.

Best practices for SAP workloads:
- Use network ACLs for broad traffic filtering at the subnet level.
- Use security groups for fine-grained control at the instance level.
- Ensure ACL rules allow required traffic for SAP operations.
- Consider using ACLs to enforce network segmentation policies.
- Test ACL changes carefully to avoid disrupting SAP operations.

### Traffic segregation
{: #network-traffic-segregation-bm}

Segregating different types of network traffic enhances security and performance for SAP workloads.

Recommended traffic segregation strategies:
**Production traffic:** Separate subnets and network interfaces for production SAP application and database traffic.
**Management traffic:** Dedicated subnet for administrative access (SSH, RDP, SAP GUI).
**Backup traffic:** Separate network interface or VLAN for backup operations to avoid impacting production traffic.
**Replication traffic:** Dedicated network path for SAP HANA system replication or database replication.
**Storage traffic:** Separate network interface for storage access (if using network-attached storage).

Implementation approaches:
- Use multiple subnets with different security groups and ACLs.
- Configure multiple network interfaces on bare metal servers.
- Use VLAN tagging to separate traffic on the same physical interface.
- Implement routing policies to control traffic flow between segments.

## Network performance considerations
{: #network-performance}

Network performance is critical for SAP workloads, especially for SAP HANA databases and high-transaction applications.

### Bandwidth and throughput
{: #network-bandwidth-throughput-bm}

{{site.data.keyword.bm_is_short}} provide high network bandwidth suitable for demanding SAP workloads:

- Network bandwidth scales with the bare metal server profile.
- Higher-end profiles support up to 100 Gbps network bandwidth.
- Multiple network interfaces can be used to aggregate bandwidth.
- Network performance is consistent due to dedicated hardware.


## References
{: #network-references-bm}

For more information about VPC networking for {{site.data.keyword.bm_is_short}}, see the following resources:

- [About Networking for VPC](/docs/vpc?topic=vpc-about-networking-for-vpc)
- [Bare Metal Servers network](/docs/vpc?topic=vpc-bare-metal-servers-network)
- [Security in your VPC](/docs/vpc?topic=vpc-security-in-your-vpc)
- [Interconnectivity for VPC](/docs/vpc?topic=vpc-interconnectivity)
- [Private network connectivity](/docs/vpc?topic=vpc-private-network-connectivity)
- [End-to-end private connectivity using VPE](/docs/vpc?topic=vpc-end-to-end-private-connectivity-vpe)
- [About VPN gateways](/docs/vpc?topic=vpc-using-vpn)
- [Getting started with {{site.data.keyword.cloud_notm}} Direct Link](/docs/dl?topic=dl-get-started-with-ibm-cloud-dl)
- [About Virtual Private Endpoints for VPC](/docs/vpc?topic=vpc-about-vpe)
- [VPC addressing plan design](/docs/vpc?topic=vpc-vpc-addressing-plan-design)
