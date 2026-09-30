---
copyright:
  years: 2026
lastupdated: "2026-09-30"
keywords: SAP, decision guidance, infrastructure selection, choosing infrastructure, deployment decision
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Choosing infrastructure for SAP workloads
{: #choosing-infrastructure}

Select the IBM Cloud infrastructure for your SAP workloads based on workload type, performance requirements, and business needs.
IBM Power Virtual Server is the preferred platform for SAP HANA workloads. 
VPC Infrastructure is the preferred platform for SAP AnyDB workloads.
{: shortdesc}

## Quick decision guide
{: #quick-decision}

For a full use-case-to-platform mapping, see [Infrastructure platforms for SAP workloads](/docs/sap?topic=sap-deployment-options-comparison#use-case-recommendations).

Key rules of thumb:

- Use IBM Power Virtual Server for SAP HANA workloads of any size.
- Use VPC Infrastructure for SAP AnyDB workloads, including Db2, ASE, MaxDB, and SQL Server.
- Use VPC Infrastructure for SAP application servers, including ASCS, ERS, PAS, AAS, and Web Dispatcher.
- If AIX is required, use IBM Power Virtual Server.

## Decision by workload type
{: #by-workload-type}

The primary decision factor is the database platform, not the database size.

| Workload | Preferred platform | Rationale |
|---|---|---|
| SAP HANA, including SAP S/4HANA, SAP BW/4HANA, and standalone SAP HANA | IBM Power Virtual Server | Supports SAP HANA workloads from development environments to production deployments of up to 30.5 TB |
| SAP AnyDB, including IBM Db2, SAP ASE, SAP MaxDB, and Microsoft SQL Server | VPC Infrastructure | Supports compute and storage options for SAP AnyDB workloads |
| SAP application servers, including ASCS, ERS, PAS, and AAS | VPC Infrastructure | Cost-effective, rapidly provisioned virtual servers ideal for the application tier |
| SAP Web Dispatcher, SAP Gateway, and SAP Router | VPC Infrastructure | Supports lightweight components benefit from VPC's flexible scaling and advanced networking |
{: caption="Infrastructure selection by workload type" caption-side="bottom"}

## Decision by environment type
{: #by-environment}

For a full description of each environment type and its recommended infrastructure, see [Supported SAP workloads and scenarios](/docs/sap?topic=sap-supported-workloads#environment-types).

Summary:

- Use IBM Power Virtual Server for SAP HANA production workloads.
- *Use IBM Power Virtual Server for SAP HANA development and test workloads. You can use VPC virtual servers for cost-sensitive non-production environments.
- Use VPC Infrastructure for SAP AnyDB workloads in all environments.
- Use VPC Infrastructure for SAP application servers in all environments

## Decision by key requirements
{: #by-requirements}

| Requirement | Recommended platform | Reason |
|---|---|---|
| **Maximum SAP HANA performance** | IBM Power Virtual Server | Industry-leading performance on Power Systems architecture |
| **High performance** | VPC bare metal servers | Dedicated physical resources without virtualization overhead |
| **Standard performance / cost-sensitive** | VPC virtual servers | Cost-effective resources with sufficient performance |
| **Minimize costs** | VPC virtual servers | Pay-as-you-go, sustained usage discounts, can suspend when not in use |
| **Balance cost and performance** | VPC bare metal servers | Dedicated resources at moderate pricing |
| **AIX operating system** | IBM Power Virtual Server | Only platform supporting AIX for SAP NetWeaver applications |
| **Linux (RHEL or SLES)** | Any platform | All platforms support Linux — select based on other requirements |
| **Windows Server** | VPC Infrastructure | IBM Power Virtual Server does not support Windows |
{: caption="Infrastructure selection by key requirements" caption-side="bottom"}

## Common deployment scenarios
{: #deployment-scenarios}

### New SAP S/4HANA production system
{: #scenario-new-s4hana}

**Requirements**: New SAP S/4HANA, HANA database, high availability

| Tier | Preferred infrastructure | Rationale |
|---|---|---|
| SAP HANA database | IBM Power Virtual Server | Optimal SAP HANA performance at any scale; enterprise-grade reliability |
| SAP application servers | VPC virtual servers | Cost-effective, flexible resources for the application tier |
| SAP Web Dispatcher | VPC virtual servers | Cost-effective, easily scaled |
{: caption="New SAP S/4HANA production system and infrastructure selection" caption-side="bottom"}

### Development and test landscape
{: #scenario-dev-test}

**Requirements**: Development, QA, and sandbox systems with cost optimization

| Tier | Recommended infrastructure | Rationale |
|---|---|---|
| All systems | VPC virtual servers | Most cost-effective option with rapid provisioning (minutes), flexible sizing, and ability to suspend when not in use |
{: caption="Development and test landscape — recommended infrastructure" caption-side="bottom"}

### Mission-critical SAP BW/4HANA
{: #scenario-mission-critical}

**Requirements**: SAP BW/4HANA production, 8 TB database, 99.99% availability

| Tier | Recommended infrastructure | Rationale |
|---|---|---|
| Database | IBM Power Virtual Server | Maximum performance for large SAP HANA databases with enterprise-grade reliability |
| Application servers | IBM Power Virtual Server or VPC virtual servers | Match performance tier of database or use cost-effective VPC option |
{: caption="Mission-critical SAP BW/4HANA — recommended infrastructure" caption-side="bottom"}

### Hybrid cloud deployment
{: #scenario-hybrid}

**Requirements**: Production on-premises, disaster recovery and development in cloud

| Tier | Recommended infrastructure | Rationale |
|---|---|---|
| DR system | VPC bare metal servers | Matches on-premises configuration |
| Development and test | VPC virtual servers | Cost-effective, rapidly provisioned |
| Connectivity | IBM Cloud Direct Link | Secure, high-bandwidth connection to on-premises systems |
{: caption="Hybrid cloud deployment — recommended infrastructure" caption-side="bottom"}

## Platform-specific considerations
{: #platform-considerations}

| Platform | Choose when you need |
|---|---|
| **IBM Power Virtual Server** | Any SAP HANA workload — development, test, or production (up to 30.5 TB) \n AIX operating system for SAP NetWeaver \n Maximum SAP HANA performance and enterprise-grade reliability |
| **VPC Infrastructure** | SAP AnyDB workloads (IBM Db2, SAP ASE, SAP MaxDB, Microsoft SQL Server) \n SAP application servers (ASCS, ERS, PAS, AAS, Web Dispatcher) \n Modern cloud-native architecture with advanced networking and security |
| **Classic Infrastructure (existing deployments only)** | Continue operating existing Classic Infrastructure systems \n Plan migration to VPC or Power Virtual Server for new capabilities |
{: caption="Platform-specific considerations" caption-side="bottom"}

For new deployments, IBM Cloud recommends IBM Power Virtual Server for SAP HANA and VPC Infrastructure for SAP AnyDB workloads and application servers.

## Decision flowchart
{: #decision-flowchart}

Follow this decision flow to select infrastructure:

1. **Is AIX required?**
   - Yes → **IBM Power Virtual Server** (only option)
   - No → Continue to step 2

2. **What database platform are you running?**
   - SAP HANA → **IBM Power Virtual Server**
   - SAP AnyDB (IBM Db2, SAP ASE, SAP MaxDB, Microsoft SQL Server) → **VPC Infrastructure**

3. **Are these SAP application servers only (no database)?**
   - Yes → **VPC Infrastructure**
   - No → Decision already made in step 2

4. **Is this SAP HANA dev/test with strict cost constraints?**
   - Yes → VPC virtual servers are acceptable for non-production HANA
   - No → **IBM Power Virtual Server** for all SAP HANA environments

## Getting assistance
{: #getting-assistance}

| Resource | Description |
|---|---|
| **IBM Cloud Support** | Contact IBM Cloud support for infrastructure recommendations based on your specific requirements |
| **SAP on IBM Cloud specialists** | Engage with SAP on IBM Cloud specialists for architecture guidance and best practices |
| **Proof of concept** | Start with a proof-of-concept deployment on VPC virtual servers to validate requirements before production deployment |
{: caption="Resources for infrastructure selection assistance" caption-side="bottom"}
