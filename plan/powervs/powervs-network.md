---
copyright:
  years: 2025, 2026
lastupdated: "2026-04-29"
keywords: SAP, PowerVS, network, connectivity, subnet, transit gateway, direct link, VPN, VPC, routes, BYOIP, private network, public network, plan
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Network connectivity requirements for SAP workloads on IBM {{site.data.keyword.powerSys_notm}}
{: #plan-network-connectivity-powervs}

This document provides guidance on planning network connectivity requirements for SAP workloads on {{site.data.keyword.powerSysFull}}. It focuses on enabling secure, high-performance, and highly available communication between {{site.data.keyword.IBM_notm}}, on‑premises, and external systems.
{: shortdesc}

Key planning considerations include:
- Designing appropriate Virtual Private Clouds (VPCs), subnet layouts, security groups, and routes.
- Implementing reliable connectivity to on‑premises systems by using VPN, Direct Link, or third-party services.
- Integrating with the {{site.data.keyword.IBM_notm}} network.
- Ensuring low latency for SAP application and database tiers.
- Considering {{site.data.keyword.powerSys_notm}} specific aspects like subnets.

Proper planning helps to achieve the required network throughput and availability for production landscapes. It also ensures compliance with enterprise security and network segmentation requirements.

{{site.data.keyword.ibm_cloud_sap}} provides comprehensive networking capabilities to support SAP landscape requirements.

The following sections describe {{site.data.keyword.ibm_cloud_sap}} portfolio design considerations for network connectivity on {{site.data.keyword.powerSys_notm}}.

## Overview
{: #network-overview}

The network architecture for {{site.data.keyword.powerSys_notm}} operates independently from both the {{site.data.keyword.vpc_full}} (VPC) environment and the {{site.data.keyword.cloud_notm}} Classic Infrastructure.
All network components within the {{site.data.keyword.cloud_notm}} data centers are fully provisioned, maintained, and administered by {{site.data.keyword.IBM_notm}}.

{{site.data.keyword.powerSys_notm}} delivers enterprise-grade networking capabilities and supports a wide range of connectivity options that are designed to meet the requirements of diverse SAP system deployment scenarios.
The architecture supports both private and public network connectivity and includes configurable routing and security controls.

For comprehensive network architecture diagrams and design patterns, refer to [Network architecture diagrams](/docs/power-iaas?topic=power-iaas-network-architecture-diagrams).

## Network subnets in the workspace
{: #network-subnets}

A {{site.data.keyword.powerSys_notm}} workspace contains one or more network subnets. These subnets provide connectivity for your virtual server instances.
Each subnet is isolated and provides a dedicated network segment for your workloads.

### Subnet types and characteristics
{: #network-subnet-types}

When you create a subnet in a {{site.data.keyword.powerSys_notm}} workspace, you define the following characteristics:

- **CIDR range**: The IP address range for the subnet in CIDR notation (for example, 192.168.0.0/24).
- **Network type**: Private or public network.
- **Gateway**: Optional gateway IP address for routing traffic outside the subnet.
- **DNS servers**: DNS server IP addresses for name resolution.
- **Maximum transmission unit (MTU)**: The largest data packet size allowed over a network. A larger MTU produces less overhead, and a smaller value can reduce the network delay.

Subnets in {{site.data.keyword.powerSys_notm}} are Layer 2 networks that provide connectivity between virtual server instances within the same workspace. You can create multiple subnets to separate traffic or implement security segmentation.

### Private or public networks
{: #network-private-public}

{{site.data.keyword.powerSys_notm}} supports both private and public network connectivity options.

#### Private networks
{: #network-private}

Private networks provide isolated, secure connectivity for your {{site.data.keyword.powerSys_notm}} instances without exposure to the public internet. They are recommended for SAP workloads and offer the following benefits:

- **Security**: No direct exposure to the public internet.
- **Performance**: Low latency and high bandwidth within the {{site.data.keyword.cloud_notm}} network.
- **Connectivity**: Connectivity to {{site.data.keyword.cloud_notm}} services, VPCs, and on-premises networks through {{site.data.keyword.tg_short}} or Direct Link.
- **Cost**: No data transfer charges for traffic within {{site.data.keyword.cloud_notm}} private networks.

Private networks are ideal for:
- SAP application and database servers.
- Internal communication between SAP system components.
- Connectivity to shared services like file storage, backup systems, and monitoring tools.
- Secure connections to on-premises data centers.

#### Public networks
{: #network-public}

Public networks provide direct internet connectivity for your {{site.data.keyword.powerSys_notm}} instances.
Each instance on a public network receives a public IP address that is accessible from the internet.

In {{site.data.keyword.powerSys_notm}}, you do not create public subnets manually.
The platform automatically provisions the public subnet when you deploy your first virtual server instance (VSI) with a public network interface enabled. Once the public subnet exists, you can view or modify selected attributes, such as the gateway or DNS servers, within the **Subnets** section of the {{site.data.keyword.powerSys_notm}} workspace.
{: note}

Public networks are protected by a firewall that allows only the following network protocols by default:
- SSH (port 22)
- HTTPS (port 443)
- Ping (ICMP)
- IBM i 5250 console emulation with SSL (port 992)

Public networks are suitable for:
- Initial setup and testing.
- Jump servers or bastion hosts.
- Systems that require direct internet access.
- Development and sandbox environments.

Use only private networks for SAP system workloads to ensure security and performance.
{: note}

### Bring your own IP (BYOIP)
{: #network-byoip}

{{site.data.keyword.powerSys_notm}} supports bring your own IP (BYOIP) address ranges for private subnets. Define subnet ranges carefully and ensure that they do not conflict with integrated networks.
{{site.data.keyword.powerSys_notm}} supports BYOIP at the platform level alongside VPC and overlay networks, as documented in IBM network architecture guidance.
This capability is necessary when you need to maintain existing IP addressing schemes or integrate with existing network infrastructure.

#### BYOIP requirements and considerations
{: #network-byoip-requirements}

When implementing BYOIP, apply the following requirements:

- **IP range ownership**: Use IP address ranges that you own or are authorized to use.
- **Non-overlapping ranges**: Ensure that the IP range does not overlap with {{site.data.keyword.cloud_notm}} reserved ranges or other subnets in your workspace.
- **Routing**: Configure routing to establish the required connectivity.

To implement BYOIP:
1. Plan your IP address ranges carefully to avoid conflicts.
1. Create a subnet in your {{site.data.keyword.powerSys_notm}} workspace by using your custom IP range.
1. Configure routing as necessary to enable connectivity.

## Network routes
{: #network-routes}

{{site.data.keyword.powerSys_notm}} uses network routes to control traffic flow between subnets, workspaces, and external networks.
Routes define the path that network traffic takes to reach its destination.

### Route types
{: #network-route-types}

{{site.data.keyword.powerSys_notm}} supports the following route types:

- **Subnet routes**: Routes within a subnet for local traffic.
- **Workspace routes**: Routes between subnets within the same workspace.
- **Static routes**: Routes to build an overlay network by using CIDRs that are not associated with subnets in your workspace.
- **{{site.data.keyword.tg_short}} routes**: Routes to other {{site.data.keyword.cloud_notm}} networks through {{site.data.keyword.tg_short}}.
- **Direct Link routes**: Routes to on-premises networks through Direct Link.

### Configuring routes
{: #network-route-config}

Configure routes at the workspace level to control traffic flow between subnets and external networks.
When you attach a workspace to a {{site.data.keyword.tg_short}}, routes are automatically configured to enable connectivity for all subnets in the workspace to other networks attached to the same Transit Gateway.

For more information about configuring routes, see [Managing network routes](/docs/power-iaas?topic=power-iaas-routes).

### Route considerations for SAP workloads
{: #network-route-sap}

When planning routes for SAP workloads, consider the following:

- **Application server to database connectivity**: Ensure that routes exist for {{site.data.keyword.sap_app_servers}} to communicate with database servers.
- **High availability**: Configure redundant routes for high availability scenarios.
- **Network segmentation**: Use routes to implement network segmentation between different SAP system tiers.
- **Performance**: Optimize routes to minimize network hops and latency.

## Reserving IP addresses
{: #network-ip-reservation}

Proper IP address management is critical for SAP workloads, especially for high availability configurations and cluster setups.
{{site.data.keyword.powerSys_notm}} provides options for reserving IP addresses within subnets and for virtual IP addresses.

### Reserving IP addresses in the subnet
{: #network-ip-reservation-subnet}

When you create a {{site.data.keyword.powerSys_notm}} instance, an IP address is automatically assigned from the subnet's CIDR range.
You can reserve specific IP addresses ranges within the subnet for future use or for specific purposes.

To reserve IP addresses in a subnet:
1. Identify the IP addresses you want to reserve.
1. Document the reserved IP addresses in your IP address management system.
1. Avoid assigning reserved IP addresses to instances.
1. Restrict the range of IP addresses of the subnet by specifying an **IP range** to only a subset of the full CIDR range.
1. Use reserved IP addresses for specific purposes like virtual IPs, load balancers, or future expansion.

Best practices for IP address reservation:
- Reserve IP addresses at the beginning or end of the subnet range for easier management.
- Document the purpose of each reserved IP address.
- Reserve IP addresses for high availability virtual IPs before deploying SAP systems.
- Plan for future growth and reserve additional IP addresses accordingly.

### Reserving virtual IP addresses
{: #network-ip-reservation-virtual}

Virtual IP addresses (VIPAs) are not permanently bound to a specific network interface. Instead, they can be dynamically reassigned between compute instances.
VIPAs play a critical role in SAP high‑availability deployments, The virtual IP address can automatically migrate to another virtual server instance to maintain service continuity during a node failure.

#### Virtual IP addresses for SAP high availability
{: #network-ip-virtual-ha}

For high availability SAP system installations, implement virtual IP addresses for the following components:
- The ASCS and ERS instances of the SAP system.
- The database instance of the SAP system (SAP HANA database or other database systems).
- Network or application load balancers can also provide a fixed IP address for client configurations.

Configure a virtual IP address by using one of the methods:

1. **Using an address within the subnet CIDR range**: Allocate an IP address from the subnet CIDR block and configure it as an IP alias on the virtual server instance network interface.
1. **Using an address outside the subnet CIDR range**: Allocate an IP address that is not part of the subnet CIDR block, configure it as an IP alias on the virtual server instance network interface, and define a static route to ensure that traffic destined for this IP address is correctly routed to the instance.

#### Static routes for virtual IP addresses
{: #network-ip-virtual-static-routes}

When you configure a virtual IP address that is outside the subnet CIDR range, configure a static route to direct the network traffic to the correct destination.

To configure static routes for virtual IP addresses, complete the following steps:
1. Choose an IP address outside the subnet CIDR range.
1. Configure the virtual IP address on the cluster nodes.
1. Create static routes in the {{site.data.keyword.powerSys_notm}} workspace to direct traffic for the virtual IP to the active cluster node.
1. Update the static routes during failover to point to the new active node.

For detailed guidance on configuring virtual IP addresses for SAP high availability, see the high availability documentation for your specific SAP deployment scenario.

## Connectivity options
{: #network-connectivity-options}

{{site.data.keyword.powerSys_notm}} provides multiple connectivity options to integrate your SAP workloads with other {{site.data.keyword.cloud_notm}} services, on-premises data centers, and external networks.

### Connecting workspaces to {{site.data.keyword.tg_short}}
{: #network-connectivity-tgw}

{{site.data.keyword.tg_full_notm}} is a network service that connects {{site.data.keyword.powerSys_notm}} workspaces, VPCs, and Classic Infrastructure networks.
{{site.data.keyword.tg_short}} provides a central hub for managing network connectivity across {{site.data.keyword.cloud_notm}} environments.

#### Attaching workspaces to {{site.data.keyword.tg_short}}
{: #network-connectivity-tgw-attach}

To connect a {{site.data.keyword.powerSys_notm}} workspace to a {{site.data.keyword.tg_short}}, complete the following steps:

1. Create a {{site.data.keyword.tg_short}} in the same region as your {{site.data.keyword.powerSys_notm}} workspace.
1. Add a connection to your {{site.data.keyword.powerSys_notm}} workspace.
1. The workspace attachment provides connectivity for all subnets within the workspace.
1. Configure routing as needed.

{{site.data.keyword.tg_short}} connection types:
- **Local {{site.data.keyword.tg_short}}**: Connects networks within the same region.
- **Global {{site.data.keyword.tg_short}}**: Connects networks across different regions.

Benefits of using {{site.data.keyword.tg_short}}:
- Centralized network management.
- Simplified routing between multiple networks.
- Workspace-level connectivity provides access for all subnets within the workspace.
- Support for multiple {{site.data.keyword.powerSys_notm}} workspaces and VPCs.
- High availability and redundancy.

For more information, see [Getting started with {{site.data.keyword.tg_full_notm}}](/docs/transit-gateway?topic=transit-gateway-getting-started).

### Connecting to a client-managed data center
{: #network-connectivity-onprem}

You can connect {{site.data.keyword.powerSys_notm}} to your on-premises data center by using {{site.data.keyword.cloud_notm}} Direct Link.
This provides a dedicated, high-bandwidth, low-latency connection for hybrid cloud scenarios.

#### Direct Link connectivity
{: #network-connectivity-directlink}

{{site.data.keyword.cloud_notm}} Direct Link provides a private connection between your on-premises infrastructure and {{site.data.keyword.cloud_notm}}.
Direct Link offers the following benefits:

- **High bandwidth**: Up to 10 Gbps or more.
- **Low latency**: Direct connection without internet routing.
- **Security**: Private connection is not exposed to the public internet.
- **Reliability**: SLA-backed service with redundancy options.

Direct Link connection options:
- **Direct Link Dedicated**: Direct fiber connection to {{site.data.keyword.cloud_notm}}.
- **Direct Link Connect**: Connection through a network service provider.
- **Direct Link Dedicated Hosting**: Connection from a colocation facility.

To connect {{site.data.keyword.powerSys_notm}} to your on-premises data center, complete the following steps:
1. Order a Direct Link connection.
2. Create a {{site.data.keyword.tg_short}}.
3. Attach your {{site.data.keyword.powerSys_notm}} workspace to the {{site.data.keyword.tg_short}}.
4. Attach the Direct Link connection to the {{site.data.keyword.tg_short}}.
5. Configure routing between your on-premises network and {{site.data.keyword.powerSys_notm}}.

For more information, see [Getting started with {{site.data.keyword.cloud_notm}} Direct Link](/docs/dl?topic=dl-get-started-with-ibm-cloud-dl).

#### VPN connectivity
{: #network-connectivity-vpn}

{{site.data.keyword.powerSys_notm}} does not provide a native VPN service.
You can establish VPN connectivity by using a VPC that is connected to your {{site.data.keyword.powerSys_notm}} workspace through {{site.data.keyword.tg_short}}.

To implement VPN connectivity, complete the following steps:
1. Create a VPC in the same region as your {{site.data.keyword.powerSys_notm}} workspace.
1. Deploy a {{site.data.keyword.vpn_vpc_short}} Gateway in the VPC.
1. Create a {{site.data.keyword.tg_short}} and attach both the VPC and {{site.data.keyword.powerSys_notm}} workspace.
1. Configure VPN connections to your on-premises network or remote sites.
1. Configure routing to enable traffic flow between {{site.data.keyword.powerSys_notm}} and the VPN.

For more information, see [VPN connections](/docs/power-iaas?topic=power-iaas-VPN-connections).

### Connecting to Virtual Private Cloud (VPC)
{: #network-connectivity-vpc}

{{site.data.keyword.powerSys_notm}} can be connected to {{site.data.keyword.vpc_full}} to enable integration with VPC-based services and resources.
This connectivity is essential for hybrid architectures that combine {{site.data.keyword.powerSys_notm}} and VPC resources.

#### Use cases for VPC connectivity
{: #network-connectivity-vpc-usecases}

Common use cases for connecting {{site.data.keyword.powerSys_notm}} to VPC include:

- **Jump servers**: Deploy Windows or Linux jump servers in {{site.data.keyword.vpc_short}} for administrative access.
- **SAP tools**: Run SAP HANA Studio, SAP GUI, or other SAP tools on VPC instances.
- **File storage**: Access {{site.data.keyword.filestorage_vpc_short}} from {{site.data.keyword.powerSys_notm}} instances.
- **Backup services**: Access {{site.data.keyword.cloud_notm}} backup and storage services through VPE for {{site.data.keyword.powerSys_notm}} workloads.
- **Monitoring and management**: Deploy monitoring and management tools in VPC.
- **Internet access**: Route internet traffic through VPC public gateways or proxy servers.

#### Establishing VPC connectivity
{: #network-connectivity-vpc-establish}

To connect {{site.data.keyword.powerSys_notm}} to VPC:
1. Create a VPC in the same region as your {{site.data.keyword.powerSys_notm}} workspace.
1. Create subnets in the VPC for your resources.
1. Create a {{site.data.keyword.tg_short}} (local or global depending on your requirements).
1. Attach your {{site.data.keyword.powerSys_notm}} workspace to the {{site.data.keyword.tg_short}} (this provides connectivity for all subnets in the workspace).
1. Attach your VPC to the {{site.data.keyword.tg_short}}.
1. Configure routing and security groups as needed.

Network security considerations:
- Configure VPC security groups to control traffic between VPC and {{site.data.keyword.powerSys_notm}}.
- Use VPC Network ACLs for subnet-level security.
- Implement least-privilege access principles.
- Monitor network traffic for security and compliance.

### Connecting to IBM Cloud services
{: #network-connectivity-cloud-services}

{{site.data.keyword.powerSys_notm}} instances can access {{site.data.keyword.cloud_notm}} services such as {{site.data.keyword.cos_full}}, Key Protect, and other platform services.

#### Cloud Object Storage connectivity
{: #network-connectivity-cos}

{{site.data.keyword.cos_full}} is commonly used for SAP HANA backups.

A subnet in a {{site.data.keyword.powerSys_notm}} workspace does not provide direct private-network access to {{site.data.keyword.cos_full}}.
To enable secure, private connectivity, create a Virtual Private Endpoint (VPE) within an {{site.data.keyword.cloud_notm}} VPC and configure routing from your {{site.data.keyword.powerSys_notm}} subnet to that VPE.
By routing traffic through the VPE, your virtual server instances in {{site.data.keyword.powerSys_notm}} can access {{site.data.keyword.cos_full}} securely over the {{site.data.keyword.cloud_notm}} private network.

To access {{site.data.keyword.cos_full}} from {{site.data.keyword.powerSys_notm}}:
1. Create a VPC in the same region.
1. Create a Virtual Private Endpoint (VPE) for {{site.data.keyword.cos_full}} in the VPC.
1. Connect the VPC to your {{site.data.keyword.powerSys_notm}} workspace using {{site.data.keyword.tg_short}}.
1. Configure routing to enable {{site.data.keyword.powerSys_notm}} instances to access the VPE.
1. Use the VPE endpoint URL to access {{site.data.keyword.cos_full}} over the private network.

Benefits of using VPE for {{site.data.keyword.cos_full}}:
- Private network connectivity without internet exposure.
- No data transfer charges for traffic within {{site.data.keyword.cloud_notm}}.
- Improved security and compliance.
- Better performance and reliability.

#### Other IBM Cloud services
{: #network-connectivity-other-services}

Similar VPE-based connectivity can be established for other {{site.data.keyword.cloud_notm}} services:
- **Key Protect**: For encryption key management.
- **{{site.data.keyword.secrets-manager_short}}**: For secrets and credentials management.
- **Activity Tracker**: For audit logging and compliance.
- **{{site.data.keyword.mon_short}}**: For metrics and monitoring.
- **{{site.data.keyword.logs_full_notm}}**: For log aggregation and analysis.

### Connecting multiple workspaces
{: #network-connectivity-multi-workspace}

For large SAP landscapes or multi-tenant environments, you might need to connect multiple {{site.data.keyword.powerSys_notm}} workspaces.
{{site.data.keyword.tg_short}} enables connectivity between multiple workspaces in the same region or across different regions.

#### Use cases for multiple workspace connectivity
{: #network-connectivity-multi-workspace-usecases}

Common scenarios for connecting multiple workspaces include:

- **SAP system landscape**: Separate workspaces for development, quality assurance, and production.
- **Multi-tenant environments**: Isolated workspaces for different business units or customers.
- **Disaster recovery**: Primary and disaster recovery workspaces in different regions.
- **Shared services**: Centralized services workspace connected to multiple application workspaces.
- **Network segmentation**: Separate workspaces for different security zones or compliance requirements.

#### Establishing multi-workspace connectivity
{: #network-connectivity-multi-workspace-establish}

To connect multiple {{site.data.keyword.powerSys_notm}} workspaces:

1. Create a {{site.data.keyword.tg_short}} (local for same-region or global for cross-region connectivity).
1. Attach each {{site.data.keyword.powerSys_notm}} workspace to the {{site.data.keyword.tg_short}}.
1. Configure routing between workspaces.
1. Implement network security controls using workspace-level routing and firewall rules.

Network design considerations:
- Plan IP address ranges carefully to avoid overlaps between workspaces.
- Implement network segmentation based on security and compliance requirements.
- Use consistent naming conventions for workspaces and subnets.
- Document routing and connectivity between workspaces.
- Monitor network traffic and performance between workspaces.

## Network security considerations
{: #network-security}

Network security is a critical aspect of SAP deployments on {{site.data.keyword.powerSys_notm}}.
Implement defense-in-depth strategies with multiple layers of security controls.

### Network security groups
{: #network-security-nsg}

{{site.data.keyword.powerSys_notm}} supports network security groups to control inbound and outbound traffic at the instance level.
Network security groups act as virtual firewalls that filter traffic based on rules you define.

For more information about NSGs, see [Network security groups](/docs/power-iaas?topic=power-iaas-nsg).

### Security best practices
{: #network-security-best-practices}

Implement the following security best practices for {{site.data.keyword.powerSys_notm}} network connectivity:

- **Use private networks**: Deploy SAP workloads on private networks whenever possible.
- **Implement network segmentation**: Separate different tiers of your SAP landscape using multiple subnets.
- **Configure NSGs**: Use Network Security Groups to control traffic at the instance level.
- **Limit public access**: Minimize the use of public networks and public IP addresses.
- **Use VPN or Direct Link**: Establish secure connections to on-premises networks.
- **Enable encryption**: Use encryption for data in transit between networks.
- **Monitor network traffic**: Implement logging and monitoring for network activity.
- **Regular security reviews**: Periodically review and update network security configurations.
- **Principle of least privilege**: Grant only the minimum required network access.

For comprehensive network security guidance, see [Network security](/docs/power-iaas?topic=power-iaas-network-security).

## References
{: #network-references}

For more information about IBM {{site.data.keyword.powerSys_notm}} networking, see the following resources:

- [Network architecture diagrams](/docs/power-iaas?topic=power-iaas-network-architecture-diagrams)
- [Network security groups](/docs/power-iaas?topic=power-iaas-nsg)
- [VPN connections](/docs/power-iaas?topic=power-iaas-VPN-connections)
- [Managing network routes](/docs/power-iaas?topic=power-iaas-routes)
- [Network security](/docs/power-iaas?topic=power-iaas-network-security)
- [Getting started with {{site.data.keyword.tg_full_notm}}](/docs/transit-gateway?topic=transit-gateway-getting-started)
- [Getting started with {{site.data.keyword.cloud_notm}} Direct Link](/docs/dl?topic=dl-get-started-with-ibm-cloud-dl)
- [About Networking for {{site.data.keyword.vpc_short}}](/docs/vpc?topic=vpc-about-networking-for-vpc)
- [{{site.data.keyword.filestorage_vpc_short}}](/docs/vpc?topic=vpc-file-storage-vpc-about)
- [Virtual Private Endpoints](/docs/vpc?topic=vpc-about-vpe)
