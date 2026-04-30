---
copyright:
  years: 2025, 2026
lastupdated: "2026-04-29"
keywords: SAP, VPC, Virtual Server Instance, networking, network planning, VPC networking, subnets, security groups, ACL, connectivity, private network, VPE, Direct Link, VPN, network topology
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Network connectivity requirements for SAP workloads on virtual server instances in {{site.data.keyword.vpc_short}}
{: #plan-network-connectivity-vpc-vsi}

This document provides guidance on planning network connectivity requirements for SAP workloads on {{site.data.keyword.vsi_is_full}} in {{site.data.keyword.vpc_full}} (VPC). It focuses on enabling secure, high-performance, and highly available communication between {{site.data.keyword.IBM_notm}}, on‑premises, and external systems.
{: shortdesc}

Key planning considerations include:
- Designing appropriate Virtual Private Clouds (VPCs), subnet layouts, security groups, and routes.
- Implementing reliable connectivity to on-premises systems by using VPN, Direct Link, or third-party services.
- Integration with the general IBM Cloud network.
- Ensuring low latency for SAP application and database tiers.
- Considering VPC-specific aspects like availability zones and address prefixes.

Proper planning helps to achieve the required network throughput and network availability for production landscapes, and compliance with enterprise security and network segmentation requirements.

{{site.data.keyword.ibm_cloud_sap}} provides comprehensive networking capabilities to support your SAP landscape requirements. The following sections outline {{site.data.keyword.ibm_cloud_sap}} portfolio design considerations for network connectivity on {{site.data.keyword.vsi_is_short}} in {{site.data.keyword.vpc_short}}.

## Overview
{: #network-overview}

The network architecture for {{site.data.keyword.vsi_is_short}} operates within the {{site.data.keyword.vpc_full}} infrastructure.
All network components within the {{site.data.keyword.cloud_notm}} data centers are fully provisioned, maintained, and administered by {{site.data.keyword.IBM_notm}}.

{{site.data.keyword.vpc_short}} delivers enterprise-grade networking capabilities and supports a broad range of connectivity options designed to meet the requirements of diverse SAP system deployment scenarios.
The architecture enables both private and public network connectivity and provides configurable routing and security controls to support your specific networking needs.

For comprehensive network architecture information, refer to [About Networking for VPC](/docs/vpc?topic=vpc-about-networking-for-vpc).

### Key networking concepts for VPC
{: #network-concepts}

Understanding the following VPC networking concepts is essential for planning your SAP landscape:

- **Virtual Private Cloud (VPC)**: An isolated network environment within {{site.data.keyword.cloud_notm}} where you deploy your resources.
- **Subnets**: Subdivisions of your VPC's IP address range, associated with specific availability zones.
- **Availability zones**: Geographically dispersed locations within a region that provide fault tolerance and high availability.
- **Address prefixes**: IP address ranges assigned to availability zones within your VPC.
- **Security groups**: Instance-level firewalls that control inbound and outbound traffic for virtual server instances.
- **Network access control lists (ACLs)**: Subnet-level firewalls that control traffic entering and leaving subnets.
- **Floating IPs**: Public IPv4 addresses that can be assigned to virtual server instances for internet access.
- **Public gateways**: Enable outbound internet connectivity for an entire subnet.
- **Virtual Private Endpoints (VPE)**: Private connectivity to {{site.data.keyword.cloud_notm}} services without traversing the public internet.

## VPC network architecture
{: #network-architecture}

The {{site.data.keyword.vpc_short}} network architecture consists of multiple layers that provide isolation, security, and connectivity.

### Network hierarchy
{: #network-hierarchy-vpc}

The VPC networking components are organized in the following hierarchy:

1. **Global**: {{site.data.keyword.cloud_notm}} global network backbone.
1. **Region**: Geographic location where a VPC is deployed, for example, us-south, eu-de.
1. **VPC**: An isolated virtual network within a region.
1. **Availability zone**: A fault-isolated data center within a region.
1. **Address prefix**: An IP address range assigned to an availability zone.
1. **Subnet**: A subdivision of an address prefix where resources are deployed.

Every {{site.data.keyword.vpc_full}} is created for a specific region and spans multiple availability zones within that region. This design provides high availability and fault tolerance for your SAP workloads.

### Subnets and address prefixes
{: #network-subnets-address-prefixes}

When you deploy a VPC in an availability zone, an [address prefix](/docs/vpc?topic=vpc-vpc-addressing-plan-design) is assigned to that zone. Each VPC zone contains one or more subnets that you can define manually by choosing the IP range and subnet mask, or by specifying the number of IP addresses needed.

Key considerations for subnet planning:

- Each subnet is associated with a single availability zone.
- Subnets within the same VPC can communicate with each other by default unless blocked by ACLs or security groups.
- Multiple subnets help to separate different types of network traffic.
- Virtual server instances are deployed into subnets and can attach to multiple subnets.

For SAP workloads, consider creating separate subnets for:
- SAP application servers
- SAP database servers
- Management and administration systems
- Backup and disaster recovery systems

## Network connectivity options
{: #network-connectivity-options}

{{site.data.keyword.vpc_short}} provides multiple connectivity options that meet the security, performance, and integration requirements of SAP landscapes.

### Connectivity within VPC
{: #network-connectivity-within-vpc}

Resources within the same VPC communicate with each other through the private network by default. This includes communication between:

- Virtual server instances in the same subnet.
- Virtual server instances in different subnets within the same VPC.
- Virtual server instances across different availability zones within the same VPC.

Communication between resources is controlled by security groups and network ACLs, which you configure based on your security requirements.

### Connectivity to the internet
{: #network-connectivity-internet}

For SAP workloads that require internet connectivity, {{site.data.keyword.vpc_short}} provides two options:

Floating IP
:   A public IPv4 address that can be attached to a virtual server instance network interface. This enables inbound connections from the internet to the instance. Floating IPs are typically used for:
    - Initial setup and configuration.
    - Jump servers or bastion hosts.
    - Web-facing SAP applications, with appropriate security controls.

Public gateway
:   Public gateway provides outbound-only internet access for all virtual server instances in a subnet. This enables instances to:
    - Download operating system updates and patches.
    - Access external services and APIs.
    - Connect to SAP support systems.

For production SAP systems, minimize or eliminate direct internet connectivity and use private connectivity options instead.
{: important}

### Connectivity to on-premises networks
{: #network-connectivity-on-premises}

Most SAP landscapes require secure connectivity between cloud-based systems and on-premises infrastructure. {{site.data.keyword.vpc_short}} provides two primary options:

#### VPN Gateway
{: #network-vpn-gateway}

{{site.data.keyword.vpc_short}} VPN Gateway provides secure Internet Protocol Security (IPsec) VPN connectivity between a VPC and on-premises networks. Key features include:

- Site-to-site VPN connections using industry-standard IPsec protocols
- Support for IKEv1 and IKEv2
- Multiple encryption and authentication options
- High availability with automatic failover
- Integration with on-premises VPN devices and software

VPN Gateway is suitable for:
- Development and test environments.
- Disaster recovery scenarios.
- Workloads with moderate bandwidth requirements, up to 650 Mbps per VPN connection.

For a tutorial on setting up VPN connectivity, see [Use a VPC/VPN gateway for secure and private on-premises access to cloud resources](/docs/solution-tutorials?topic=solution-tutorials-vpc-site2site-vpn).

#### {{site.data.keyword.dl_full}}
{: #network-direct-link}

{{site.data.keyword.dl_full}} 2.0 provides dedicated, high-speed connectivity between your on-premises infrastructure and {{site.data.keyword.cloud_notm}}. Key features include:

- Dedicated network connection, not shared with other customers.
- Bandwidth options from 50 Mbps to 10 Gbps.
- Low latency and consistent network performance.
- Access to both Classic Infrastructure and VPC Infrastructure.
- Support for multiple VPCs through a single connection.
- Bring your own IP (BYOIP) support.

{{site.data.keyword.dl_short}} is recommended for:
- Production SAP environments.
- Workloads requiring high bandwidth and low latency.
- Hybrid cloud architectures with significant data transfer.
- Mission-critical applications requiring consistent network performance.

{{site.data.keyword.dl_short}} can be established using:
- **Direct Link Dedicated**: Provides a direct fiber connection to {{site.data.keyword.cloud_notm}}.
- **Direct Link Connect**: Provides connectivity through supported network service providers.

For more information, see [Getting started with {{site.data.keyword.cloud_notm}} Direct Link](/docs/dl?topic=dl-get-started-with-ibm-cloud-dl). To find available network service providers for your location, use [Cloud Pathfinder for IBM Cloud](https://ibm.find.cloud){: external}.

### Connectivity to IBM Cloud services
{: #network-connectivity-cloud-services}

SAP workloads often need to integrate with other {{site.data.keyword.cloud_notm}} services such as {{site.data.keyword.cos_full_notm}}, {{site.data.keyword.databases-for}}, or {{site.data.keyword.keymanagementservicefull}}.

#### Virtual Private Endpoints (VPE)
{: #network-virtual-private-endpoints}

Virtual Private Endpoints enable private connectivity to supported {{site.data.keyword.cloud_notm}} services without traversing the public internet. Benefits include:

- Enhanced security by keeping traffic on the {{site.data.keyword.cloud_notm}} private network.
- No bandwidth charges for data transfer between VPC and {{site.data.keyword.cloud_notm}} services.
- Simplified network architecture (no need for public gateways or floating IPs).
- Improved performance with lower latency.

VPE is created by:
1. Creating an endpoint gateway for the target service.
1. Reserving IP addresses from your VPC subnets.
1. Binding the endpoint gateway to the reserved IPs.

For more information, see [About Virtual Private Endpoints for VPC](/docs/vpc?topic=vpc-about-vpe) and [End-to-end private connectivity using VPE](/docs/vpc?topic=vpc-end-to-end-private-connectivity-vpe).

### Connectivity between VPCs
{: #network-connectivity-between-vpcs}

For complex SAP landscapes spanning multiple VPCs, you can establish connectivity using:

{{site.data.keyword.tg_full}}
:   Connects multiple VPCs within the same region or across regions, enabling communication between resources in different VPCs. This is useful for:
    - Separating production and non-production environments.
    - Implementing hub-spoke network architectures.
    - Connecting SAP systems across different VPCs.

VPC peering
:   Establishes direct connectivity between two VPCs, allowing resources to communicate as if they were in the same network.

### Connectivity to Classic Infrastructure
{: #network-connectivity-classic}

If you have existing resources in {{site.data.keyword.cloud_notm}} Classic Infrastructure, you can connect them to your VPC using {{site.data.keyword.tg_short}}. This enables:

- Gradual migration from Classic to VPC Infrastructure.
- Hybrid deployments spanning both environments.
- Access to Classic Infrastructure services from VPC.

For more information, see [Setting up access to classic infrastructure](/docs/vpc?topic=vpc-setting-up-access-to-classic-infrastructure).

## Network performance considerations
{: #network-performance-considerations}

Network performance is critical for SAP workloads, particularly for database-intensive applications and high-availability configurations.

### Network bandwidth
{: #network-bandwidth}

{{site.data.keyword.vsi_is_short}} provides network bandwidth based on the virtual server instance profile.

- Total network bandwidth ranges from 4 Gbps to 64 Gbps depending on the profile.
- Each virtual network interface (vNIC) has a maximum throughput of 16 Gbps.
- You can attach up to 5 vNICs to a virtual server instance to maximize throughput.

For SAP workloads:
- SAP HANA systems typically require 10 Gbps or higher network bandwidth.
- {{site.data.keyword.sap_app_servers}} generally require 1-4 Gbps depending on the workload.
- Consider network bandwidth when sizing your virtual server instances.

### Network latency
{: #network-latency-considerations}

Network latency is particularly important for:

- Communication between SAP application servers and database servers.
- SAP HANA System Replication for high availability and disaster recovery.
- Real-time data processing and analytics workloads.

Key considerations:

- Latency within the same availability zone is typically less than 1 ms.
- Latency between availability zones in the same region is typically 2-5 ms.
- Latency between regions varies based on geographic distance.
- Test latency using Round Trip Time (RTT) measurements before implementing high availability or disaster recovery solutions.

For SAP HANA System Replication:
- Synchronous replication requires RTT less than 1 ms (typically the same availability zone).
- Asynchronous replication can tolerate higher latency (cross-region scenarios).

### Network throughput for SAP HANA scale-out
{: #network-throughput-hana-scaleout}

SAP HANA multi-node (scale-out) systems require specific network configurations:

Client network
:   Connects SAP application servers, SAP HANA Studio clients, and other clients to the SAP HANA system. Network throughput requirements depend on:
    - Amount of data transferred to and from the database.
    - Number of concurrent users and transactions.
    - Availability and performance requirements.

Storage network
:   Connects to network storage (file or block storage). Requirements include:
    - Sufficient throughput to provide 10,000 IOPS to each SAP HANA node.
    - Low latency for optimal storage performance.

Internode network
:   Used for communication between SAP HANA nodes. This network should have:
    - High bandwidth for data transfer between nodes.
    - Low latency for optimal cluster performance.
    - Equivalent performance to the storage network.

To maximize performance and redundancy for SAP HANA scale-out, you can attach up to 5 network interfaces (vNICs) on multiple subnets to each virtual server instance.

## Network security
{: #network-security}

{{site.data.keyword.vpc_short}} provides multiple layers of network security to protect your SAP workloads.

### Security groups
{: #network-security-groups}

Security groups act as virtual firewalls at the instance level, controlling inbound and outbound traffic for virtual server instances. Key characteristics:

- Applied to network interfaces (vNICs) of virtual server instances.
- Stateful: Return traffic is automatically allowed.
- Allow-only rules: You specify what traffic is permitted.
- Can be applied to multiple instances.
- Rules can reference other security groups.

Best practices for SAP workloads:
- Create separate security groups for different SAP components (application servers, database servers, web dispatchers).
- Implement least-privilege access: Only allow necessary ports and protocols.
- Use security group references instead of IP addresses for internal communication.
- Regularly review and update security group rules.

Default security group rules typically allow:
- SSH (port 22) for Linux instances.
- RDP (port 3389) for Windows instances.
- ICMP (ping) for network diagnostics.

You should restrict these rules to specific source IP addresses or ranges based on your security requirements.
{: important}

### Network access control lists (ACLs)
{: #network-acls-vpc}

Network ACLs provide subnet-level security, controlling traffic entering and leaving subnets. Key characteristics:

- Applied to subnets.
- Stateless: Both inbound and outbound rules must be configured.
- Support both allow and deny rules.
- Rules are evaluated in order (lowest priority number first).
- Default ACL allows all traffic.

Best practices for SAP workloads:
- Use ACLs for broad subnet-level controls.
- Use security groups for fine-grained instance-level controls.
- Consider the impact on required services (DNS, OS updates, monitoring).
- Document ACL rules and their purposes.
- Test ACL changes in non-production environments first.

For more information, see [Security in your VPC](/docs/vpc?topic=vpc-security-in-your-vpc).

### Network traffic segregation
{: #network-traffic-segregation}

Segregating network traffic improves both security and performance. Common segregation strategies for SAP workloads include:

Separate subnets for different tiers
:   - Front end subnet: Web dispatchers, SAP Fiori launchpad
    - Application subnet: {{site.data.keyword.sap_app_server}}
    - Database subnet: SAP HANA or other database systems
    - Management subnet: Jump servers, monitoring systems, backup infrastructure

Separate subnets for different environments
:   - Production subnet.
    - Non-production subnet (development, test, QA).
    - Disaster recovery subnet.

Separate subnets for different security zones
:   - DMZ subnet: Internet-facing components.
    - Internal subnet: Backend systems.
    - Restricted subnet: Highly sensitive data and systems.

While virtual server instances in VPC can be attached to multiple subnets, each vNIC can only be associated with one subnet. Plan your subnet architecture accordingly.
{: note}

## Network topology for SAP landscapes
{: #network-topology-sap-landscapes}

The network topology for your SAP landscape depends on factors such as:

- Number and types of SAP systems.
- High availability and disaster recovery requirements.
- Security and compliance requirements.
- Integration with on-premises systems.
- Performance requirements.

### Single-zone deployment
{: #network-topology-single-zone}

A single-zone deployment places all SAP components in one availability zone. This topology is suitable for:

- Development and test environments.
- Non-critical workloads.
- Proof-of-concept implementations.

Characteristics:
- Simplest network topology.
- Lowest latency between components.
- No high availability across zones.
- Lower cost.

### Multi-zone deployment
{: #network-topology-multi-zone}

A multi-zone deployment distributes SAP components across multiple availability zones within a region. This topology provides:

- High availability for SAP systems.
- Fault tolerance against zone failures.
- Disaster recovery within the same region.

Considerations:
- Higher network latency between zones (typically 2-5 ms).
- More complex network configuration.
- Higher cost due to redundant resources.
- Requires careful planning for SAP HANA System Replication.

### Multi-region deployment
{: #network-topology-multi-region}

A multi-region deployment spans multiple {{site.data.keyword.cloud_notm}} regions. This topology provides:

- Geographic redundancy.
- Disaster recovery across regions.
- Compliance with data residency requirements.
- Support for global SAP landscapes.

Considerations:
- Highest network latency between regions.
- Most complex network configuration.
- Highest cost.
- Requires asynchronous SAP HANA System Replication.
- May require multiple Direct Link connections.

### Hybrid cloud deployment
{: #network-topology-hybrid-cloud}

A hybrid cloud deployment combines on-premises infrastructure with {{site.data.keyword.vpc_short}}. This topology enables:

- Gradual migration to cloud.
- Burst capacity for peak workloads.
- Integration with existing SAP systems.
- Compliance with data residency requirements.

Considerations:
- Requires Direct Link or VPN connectivity.
- Network latency depends on connection type and distance.
- Bandwidth limitations may impact performance.
- Complex network routing and security configuration.

## Network planning for specific SAP scenarios
{: #network-planning-sap-scenarios}

### SAP HANA high availability
{: #network-scenario-hana-ha}

For SAP HANA high availability using System Replication:

- Deploy primary and secondary nodes in different availability zones (for zone-level HA).
- Ensure network latency between zones meets SAP requirements (typically < 1 ms for synchronous replication).
- Configure separate subnets for client, storage, and internode communication.
- Use security groups to control access between nodes.
- Consider using Virtual Private Endpoints for backup to {{site.data.keyword.cos_short}}.

### {{site.data.keyword.sap_app_server}} high availability
{: #network-scenario-netweaver-ha}

For {{site.data.keyword.sap_app_server}} high availability:

- Deploy ASCS/ERS instances in different availability zones.
- Use Application Load Balancer for distributing traffic to application servers.
- Configure security groups to allow cluster communication.
- Ensure network connectivity for shared file systems (NFS).
- Plan for floating IP addresses or a load balancer for failover.

### SAP S/4HANA three-tier architecture
{: #network-scenario-s4hana-three-tier}

For SAP S/4HANA three-tier deployments:

- Front end tier: Web Dispatcher in public subnet (if internet-facing) or private subnet.
- Application tier: {{site.data.keyword.sap_app_server}} in dedicated subnet.
- Database tier: SAP HANA in separate, more restricted subnet.
- Use security groups and ACLs to control traffic between tiers.
- Consider using Virtual Private Endpoints for integration with {{site.data.keyword.cloud_notm}} services.

### SAP landscape with multiple systems
{: #network-scenario-multiple-systems}

For complex SAP landscapes with multiple systems:

- Use separate VPCs for production and non-production environments.
- Connect VPCs using {{site.data.keyword.tg_short}}.
- Implement Hub-spoke network topology with shared services VPC.
- Use consistent subnet naming and IP addressing schemes.
- Document network topology and connectivity paths.

## Bring your own IP (BYOIP)
{: #network-byoip}

{{site.data.keyword.vpc_short}} supports bringing your own IP address ranges to your VPC. This enables:

- Using existing IP addressing schemes.
- Simplifying migration from on-premises to cloud.
- Maintaining consistent IP addresses across environments.

Requirements and considerations:

- IP ranges must be from RFC 1918 private address spaces:
   - Class A: 10.0.0.0/8.
   - Class B: 172.16.0.0/12.
   - Class C: 192.168.0.0/16.

- Non-RFC 1918 ranges are not supported for BYOIP in VPC.
- BYOIP ranges can be used with {{site.data.keyword.dl_short}} connectivity.
- Plan IP address ranges carefully to avoid conflicts with on-premises networks.

For more information, see [Bring your own subnet](/docs/vpc?topic=vpc-configuring-address-prefixes).

## Network monitoring and troubleshooting
{: #network-monitoring-troubleshooting}

Effective network monitoring is essential for maintaining SAP system performance and availability.

### Flow logs
{: #network-flow-logs}

{{site.data.keyword.fl_full}} capture information about IP traffic flowing to and from network interfaces. Use flow logs to:

- Monitor network traffic patterns.
- Troubleshoot connectivity issues.
- Detect security threats.
- Analyze network performance.
- Support compliance and auditing requirements.

### Network monitoring tools
{: #network-monitoring-tools}

Consider implementing:

- {{site.data.keyword.mon_full_notm}} for network performance metrics.
- {{site.data.keyword.at_full_notm}} for network configuration changes.
- Custom monitoring solutions for SAP-specific network metrics.
- Network latency monitoring between SAP components.

## References
{: #network-references}

For more information about VPC networking for {{site.data.keyword.vsi_is_short}}, see the following resources:

- [About Networking for VPC](/docs/vpc?topic=vpc-about-networking-for-vpc)
- [Security in your VPC](/docs/vpc?topic=vpc-security-in-your-vpc)
- [Interconnectivity options](/docs/vpc?topic=vpc-interconnectivity)
- [Private network connectivity](/docs/vpc?topic=vpc-private-network-connectivity)
- [End-to-end private connectivity using VPE](/docs/vpc?topic=vpc-end-to-end-private-connectivity-vpe)
- [Getting started with {{site.data.keyword.tg_full_notm}}](/docs/transit-gateway?topic=transit-gateway-getting-started)
- [Getting started with {{site.data.keyword.cloud_notm}} Direct Link](/docs/dl?topic=dl-get-started-with-ibm-cloud-dl)
- [VPN for VPC](/docs/vpc?topic=vpc-using-vpn)
- [Virtual Private Endpoints for VPC](/docs/vpc?topic=vpc-about-vpe)
- [Determining access to your SAP system landscape](/docs/sap?topic=sap-determine-access)
- [Networking design considerations](/docs/sap?topic=sap-networking-design-considerations)
