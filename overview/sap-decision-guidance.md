---
copyright:
  years: 2026
lastupdated: "2026-07-23"
keywords: SAP, decision guidance, infrastructure selection, choosing infrastructure, deployment decision
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Choosing infrastructure for SAP workloads
{: #choosing-infrastructure}

Select the most appropriate IBM Cloud infrastructure for your SAP workloads based on workload characteristics, performance requirements, and business needs. IBM Cloud recommends IBM Power Virtual Server for mission-critical SAP HANA workloads and VPC Infrastructure for modern cloud-native SAP deployments.
{: shortdesc}

## Quick decision guide
{: #quick-decision}

For a full use-case-to-platform mapping, see [Infrastructure platforms for SAP workloads](/docs/sap?topic=sap-deployment-options-comparison#use-case-recommendations).

Key rules of thumb:

- **AIX required or SAP HANA >12 TB** → IBM Power Virtual Server (only option)
- **Mission-critical production SAP HANA** → IBM Power Virtual Server
- **New deployments, development and test, application servers** → VPC Infrastructure

## Decision by workload size
{: #by-workload-size}

For complete workload sizing guidance including recommended infrastructure per memory range, see [Supported SAP workloads and scenarios](/docs/sap?topic=sap-supported-workloads#sizing-guidance).

Summary:

- **Less than 1 TB** → VPC virtual servers
- **1–2 TB** → VPC bare metal servers
- **2–12 TB** → VPC bare metal servers or IBM Power Virtual Server
- **Greater than 12 TB** → IBM Power Virtual Server

## Decision by environment type
{: #by-environment}

For a full description of each environment type and its recommended infrastructure, see [Supported SAP workloads and scenarios](/docs/sap?topic=sap-supported-workloads#environment-types).

Summary:

- **Production** → IBM Power Virtual Server (mission-critical) or VPC bare metal servers
- **Quality assurance** → VPC bare metal servers or virtual servers
- **Development and sandbox** → VPC virtual servers

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

**Requirements**: New SAP S/4HANA, 3 TB database, high availability

| Tier | Recommended infrastructure | Rationale |
|---|---|---|
| Database | IBM Power Virtual Server or VPC bare metal servers | Optimal SAP HANA performance |
| Application servers | VPC virtual servers | Cost-effective, flexible resources for application tier |
| Web Dispatcher | VPC virtual servers | Cost-effective, easily scaled |
{: caption="New SAP S/4HANA production system — recommended infrastructure" caption-side="bottom"}

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
| **IBM Power Virtual Server** | AIX operating system for SAP NetWeaver \n SAP HANA databases larger than 12 TB (up to 30 TB) \n Maximum SAP HANA performance for mission-critical systems \n Enterprise-grade reliability for production workloads |
| **VPC Infrastructure** | Modern cloud-native architecture for new SAP deployments \n Advanced networking and security features \n Flexible compute (virtual servers and bare metal) \n Cost-effective development and test environments |
| **Classic Infrastructure (existing deployments only)** | Continue operating existing Classic Infrastructure systems \n Plan migration to VPC or Power Virtual Server for new capabilities |
{: caption="Platform-specific considerations" caption-side="bottom"}

For new deployments, IBM Cloud recommends VPC Infrastructure or Power Virtual Server rather than Classic Infrastructure.

## Decision flowchart
{: #decision-flowchart}

Follow this decision flow to select infrastructure:

1. **Is AIX required?**
   - Yes → **IBM Power Virtual Server** (only option)
   - No → Continue to step 2

2. **What is the SAP HANA database size?**
   - Greater than 12 TB → **IBM Power Virtual Server**
   - 2-12 TB → **IBM Power Virtual Server** or **VPC bare metal servers**
   - Less than 2 TB → **VPC bare metal servers** or **VPC virtual servers**

3. **Is this production or non-production?**
   - Production → **IBM Power Virtual Server** or **VPC bare metal servers**
   - Non-production → **VPC virtual servers**

4. **What is your priority?**
   - Maximum performance → **IBM Power Virtual Server**
   - Balance of performance and cost → **VPC bare metal servers**
   - Cost optimization → **VPC virtual servers**

## Getting assistance
{: #getting-assistance}

| Resource | Description |
|---|---|
| **IBM Cloud Support** | Contact IBM Cloud support for infrastructure recommendations based on your specific requirements |
| **SAP on IBM Cloud specialists** | Engage with SAP on IBM Cloud specialists for architecture guidance and best practices |
| **Proof of concept** | Start with a proof-of-concept deployment on VPC virtual servers to validate requirements before production deployment |
{: caption="Resources for infrastructure selection assistance" caption-side="bottom"}
