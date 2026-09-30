---
copyright:
  years: 2026
lastupdated: "2026-09-30"
keywords: SAP, deployment options, VPC, Power Virtual Server, Classic Infrastructure, comparison, infrastructure
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# SAP deployment options comparison
{: #deployment-options-comparison}

IBM Cloud offers three infrastructure platforms for SAP workloads: VPC Infrastructure, IBM Power Virtual Server, and Classic Infrastructure. This comparison helps you select the optimal platform for your SAP deployment requirements.
{: shortdesc}

## Platform overview
{: #platform-overview}

| | IBM Power Virtual Server | VPC Infrastructure | Classic Infrastructure |
| --- | --- | --- | --- |
| **Status** | Preferred platform for SAP HANA workloads | Preferred platform for SAP AnyDB workloads and application servers | Legacy platform (migration recommended) |
| **Architecture** | Enterprise-grade Power Systems | Modern cloud-native platform | Legacy dedicated infrastructure |
| **Compute options** | Virtual servers on Power Systems | Virtual servers and bare metal | Bare metal servers only |
| **Best for** | SAP HANA (all sizes), AIX workloads | SAP AnyDB workloads, SAP application servers | Existing deployments only |
| **Provisioning time** | Minutes to hours | Minutes (VSI) to hours (bare metal) | Hours to days |
{: caption="Platform overview comparison" caption-side="bottom"}

## SAP certifications and support
{: #certifications-support}

| Feature | IBM Power Virtual Server | VPC Infrastructure | Classic Infrastructure |
| --- | --- | --- | --- |
| **SAP HANA maximum size** | Up to 30.5 TB | Virtual servers: 5.7 TB \n Bare metal: 12 TB | Up to 12 TB |
| **SAP HANA scale-out** | Supported | Supported | Supported |
| **SAP NetWeaver** | Supported on AIX and Linux | Supported on Linux and Windows | Supported on Linux and Windows |
| **SAP S/4HANA** | Fully certified | Fully certified | Fully certified |
| **SAP BW/4HANA** | Fully certified | Fully certified | Fully certified |
| **SAP AnyDB** | IBM Db2, Oracle | IBM Db2, Microsoft SQL Server, SAP ASE, SAP MaxDB | IBM Db2, Microsoft SQL Server, SAP ASE, SAP MaxDB |
| **AIX support** | Native support | Not supported | Not supported |
| **SAP certification notes** | [SAP Note 2855850](https://me.sap.com/notes/2855850/){: external} | [SAP Note 2927211](https://me.sap.com/notes/2927211/){: external} | [SAP Note 2414097](https://me.sap.com/notes/2414097/){: external} |
{: caption="SAP certifications and support comparison" caption-side="bottom"}

## Operating system support
{: #operating-systems}

| Operating system | IBM Power Virtual Server | VPC Infrastructure | Classic Infrastructure |
| --- | --- | --- | --- |
| **SUSE Linux Enterprise Server** | Supported | Supported | Supported |
| **Red Hat Enterprise Linux** | Supported | Supported | Supported |
| **Microsoft Windows Server** | Not supported | Supported | Supported |
| **AIX** | Supported | Not supported | Not supported |
{: caption="Operating system support comparison" caption-side="bottom"}

## Compute capabilities
{: #compute-capabilities}

| Feature | IBM Power Virtual Server | VPC Infrastructure | Classic Infrastructure |
| --- | --- | --- | --- |
| **Virtual servers** | Available with dedicated/shared processors | Available with flexible profiles | Not available |
| **Bare metal servers** | Not applicable (virtual on physical Power) | Available with VPC networking | Available |
| **Processor architecture** | IBM Power (Power9, Power10) | Intel x86 | Intel x86 |
| **Dedicated resources** | Dedicated processor option | Bare metal only | All servers dedicated |
| **Shared resources** | Shared processor option | Virtual servers | Not available |
| **Rapid scaling** | Flexible scaling | Virtual servers scale quickly | Limited scaling |
| **Performance optimization** | Optimized for SAP HANA in-memory computing | General-purpose and SAP-optimized | General-purpose |
{: caption="Compute capabilities comparison" caption-side="bottom"}

## Storage options
{: #storage-options}

| Feature | IBM Power Virtual Server | VPC Infrastructure | Classic Infrastructure |
| --- | --- | --- | --- |
| **Block storage** | Tier 0, Tier 1, Tier 3, Fixed IOPS | Customizable IOPS (3-48K) | SAN storage |
| **File storage** | Not available | NFS-based file storage | NAS storage |
| **Object storage** | IBM Cloud Object Storage integration | IBM Cloud Object Storage integration | IBM Cloud Object Storage integration |
| **Storage performance** | Tier-based (Tier 0 highest) | Customizable per volume | Fixed performance tiers |
| **Best for SAP HANA** | Tier 0 or Tier 1 NVMe | Block storage with high IOPS | SAN storage |
{: caption="Storage options comparison" caption-side="bottom"}

## Networking capabilities
{: #networking-capabilities}

| Feature | IBM Power Virtual Server | VPC Infrastructure | Classic Infrastructure |
| --- | --- | --- | --- |
| **Network architecture** | Private networking | Software-defined VPC | VLAN-based networking |
| **Network isolation** | Private network segments | Complete VPC isolation | VLAN isolation |
| **Load balancers** | Not built-in | Application and network load balancers | Load balancer service |
| **VPN connectivity** | Requires VPC integration | Built-in VPN gateways | VPN service |
| **Direct connectivity** | Direct Link 2.0 | Direct Link 2.0 | Direct Link |
| **Public internet** | Optional public IPs | Floating IPs, public gateways | Public IPs |
| **Security groups** | Not applicable | Stateful firewalls | Security groups |
| **Network ACLs** | Not applicable | Stateless subnet-level control | Not available |
| **Multi-zone support** | Multi-location deployments | Native multi-zone architecture | Multi-data center |
{: caption="Networking capabilities comparison" caption-side="bottom"}

## High availability and disaster recovery
{: #ha-dr}

| Feature | IBM Power Virtual Server | VPC Infrastructure | Classic Infrastructure |
| --- | --- | --- | --- |
| **Multi-zone deployments** | Multi-location deployments | Native support across availability zones | Multi-data center deployments |
| **SAP HANA System Replication** | Supported | Supported | Supported |
| **Cluster solutions** | Pacemaker on Linux, PowerHA on AIX | Pacemaker on Linux | Pacemaker on Linux |
| **Automated failover** | Supported with clustering | Supported with clustering | Supported with clustering |
| **Load balancing** | Requires external solution | Built-in load balancers | Load balancer service |
| **Backup integration** | IBM Cloud Object Storage | IBM Cloud Object Storage, Veeam | IBM Cloud Object Storage |
{: caption="High availability and disaster recovery comparison" caption-side="bottom"}

## Security features
{: #security-features}

| Feature | IBM Power Virtual Server | VPC Infrastructure | Classic Infrastructure |
| --- | --- | --- | --- |
| **Network isolation** | Private network isolation | Complete VPC isolation | VLAN isolation |
| **Encryption at rest** | IBM Key Protect, HPCS | IBM Key Protect, HPCS | Available |
| **Encryption in transit** | TLS/SSL | TLS/SSL | TLS/SSL |
| **Identity and access management** | IBM Cloud IAM | IBM Cloud IAM | IBM Cloud IAM |
| **Compliance certifications** | ISO 27001, SOC 2 | ISO 27001, SOC 2, PCI DSS | ISO 27001, SOC 2 |
| **Security groups** | Not applicable | Stateful instance-level firewalls | Security groups |
| **Network ACLs** | Not applicable | Stateless subnet-level firewalls | Not available |
{: caption="Security features comparison" caption-side="bottom"}

## Use case recommendations
{: #use-case-recommendations}

| Use case | Preferred platform | Rationale |
| --- | --- | --- |
| **SAP HANA (all sizes, all environments)** | IBM Power Virtual Server | Industry-leading HANA performance on Power Systems architecture; consistent platform from dev/test to the largest production systems (up to 30.5 TB) |
| **SAP S/4HANA production** | IBM Power Virtual Server | Optimal performance and enterprise-grade reliability for mission-critical ERP workloads |
| **SAP BW/4HANA and analytics** | IBM Power Virtual Server | Optimized for in-memory computing and large-scale analytics workloads |
| **SAP AnyDB workloads** (Db2, ASE, MaxDB, SQL Server) | VPC Infrastructure | Recommended platform for all SAP AnyDB databases; flexible compute and storage options |
| **SAP application servers** (ASCS, ERS, PAS, AAS) | VPC Infrastructure | Cost-effective, rapidly provisioned virtual servers for the application tier |
| **SAP Web Dispatcher / Gateway** | VPC Infrastructure | Advanced networking and flexible scaling for SAP connectivity components |
| **AIX-based SAP workloads** | IBM Power Virtual Server | Only cloud platform supporting AIX operating system |
| **Hybrid cloud integration** | VPC Infrastructure | Advanced networking features, Direct Link, and seamless on-premises integration |
| **Existing Classic Infrastructure deployments** | Classic Infrastructure (short-term) | Continue existing deployments while planning migration to VPC or Power Virtual Server |
| **SAP HANA dev/test (strict cost constraints)** | IBM Power Virtual Server (preferred); VPC virtual servers (acceptable) | PowerVS preferred for consistency; VPC acceptable when cost is the primary constraint |
{: caption="Use case recommendations" caption-side="bottom"}

## Migration paths
{: #migration-paths}

| From | To | Key considerations |
| --- | --- | --- |
| **Classic Infrastructure** | VPC Infrastructure | Modernize with cloud-native capabilities, enhanced security, advanced networking |
| **Classic Infrastructure** | IBM Power Virtual Server | Migrate SAP HANA for superior performance, migrate AIX workloads |
| **On-premises x86** | VPC Infrastructure | Lift-and-shift or re-architect for cloud-native deployment |
| **On-premises Power Systems** | IBM Power Virtual Server | Maintain Power architecture expertise, migrate AIX workloads |
| **On-premises AIX** | IBM Power Virtual Server | Only cloud option for AIX-based SAP NetWeaver |
| **Other cloud providers** | VPC Infrastructure or Power Virtual Server | Leverage IBM Cloud capabilities, competitive pricing |
{: caption="Migration paths" caption-side="bottom"}

## Pricing and cost considerations
{: #pricing-cost}

For a full breakdown of pricing models, cost components, and optimization strategies across all platforms, see [Pricing and cost considerations](/docs/sap?topic=sap-pricing).

IBM Power Virtual Server is typically priced compatitively and is used for SAP HANA workloads. 
VPC Infrastructure supports SAP AnyDB workloads and SAP application servers across virtual servers and bare metal servers.

## Next steps
{: #next-steps}

- Use the [Choosing infrastructure for SAP workloads](/docs/sap?topic=sap-choosing-infrastructure) guide to select the right platform based on your workload type and business requirements
- Explore [Planning](/docs/sap?group=plan) section to plan compute, storage, networking and to choose your operating system.
