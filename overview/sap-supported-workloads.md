---
copyright:
  years: 2026
lastupdated: "2026-09-30"
keywords: SAP, SAP workloads, SAP HANA, SAP NetWeaver, SAP S/4HANA, SAP applications, SAP scenarios
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Supported SAP workloads and scenarios
{: #supported-workloads}

IBM Cloud supports the complete spectrum of SAP workloads, including SAP HANA databases, SAP application servers, and all major SAP business applications across production, quality assurance, development, and disaster recovery environments.
{: shortdesc}

## SAP HANA-based workloads
{: #hana-workloads}

SAP HANA is an in-memory database and application platform that powers modern SAP applications and real-time analytics.

### SAP S/4HANA
{: #s4hana}

SAP S/4HANA is SAP's next-generation intelligent ERP system designed to run exclusively on SAP HANA database. It provides real-time business processes and analytics with a simplified data model.

IBM Power Virtual Server is the preferred platform for SAP S/4HANA deployments. Power Systems architecture supports SAP HANA workloads across development, test, and production environments.

| Configuration | Preferred infrastructure |
| --- | --- |
| Development and test | IBM Power Virtual Server |
| Production (any size, up to 30.5 TB) | IBM Power Virtual Server |
| High availability and disaster recovery | IBM Power Virtual Server |
| Multi-host scale-out | IBM Power Virtual Server |
{: caption="SAP S/4HANA configurations and recommended infrastructure" caption-side="bottom"}

### SAP BW/4HANA
{: #bw4hana}

SAP BW/4HANA is SAP's next-generation data warehouse solution optimized for SAP HANA's in-memory capabilities, providing real-time analytics and reporting.

IBM Power Virtual Server is the preferred platform for all SAP BW/4HANA deployments, delivering the performance required for large-scale analytics and real-time data warehousing workloads.

| Configuration | Preferred infrastructure |
| --- | --- |
| Development and test environments | IBM Power Virtual Server |
| Data warehouse production (any size) | IBM Power Virtual Server |
| Large-scale analytics, maximum performance | IBM Power Virtual Server |
| Multi-host scale-out, high availability | IBM Power Virtual Server |
{: caption="SAP BW/4HANA configurations and preferred infrastructure" caption-side="bottom"}

### SAP HANA database
{: #hana-database}

SAP HANA can be deployed as a standalone database platform for SAP Business Suite applications, custom applications, or third-party applications.

IBM Power Virtual Server is the preferred platform for SAP HANA workloads. 
It supports SAP HANA systems that range from small development environments to production deployments of up to 30.5 TB.

| Deployment type | Preferred infrastructure |
| --- | --- |
| Development and test | IBM Power Virtual Server |
| Production (all sizes, up to 30 TB) | IBM Power Virtual Server |
| High availability with SAP HANA System Replication | IBM Power Virtual Server |
| Multi-host scale-out | IBM Power Virtual Server |
{: caption="SAP HANA database deployments and preferred infrastructure" caption-side="bottom"}

## SAP NetWeaver-based workloads
{: #netweaver-workloads}

SAP NetWeaver is the application server platform that runs SAP business applications and services.

### SAP ECC (ERP Central Component)
{: #ecc}

SAP ECC is SAP's traditional ERP system supporting multiple database platforms. While SAP S/4HANA is the strategic direction, SAP ECC remains widely deployed.

**Supported databases:**

| Database | Preferred infrastructure |
| --- | --- |
| SAP HANA | IBM Power Virtual Server |
| IBM Db2 | VPC Infrastructure (Linux); IBM Power Virtual Server (AIX) |
| Microsoft SQL Server | VPC Infrastructure |
| SAP ASE (Sybase) | VPC Infrastructure |
| SAP MaxDB | VPC Infrastructure |
{: caption="SAP ECC supported databases and preferred infrastructure" caption-side="bottom"}

**Infrastructure options:** IBM Power Virtual Server (AIX-based and Linux-based deployments), VPC bare metal servers, VPC virtual servers.

### SAP Business Suite
{: #business-suite}

SAP Business Suite is an integrated suite of SAP business applications built on SAP NetWeaver platform.

| Application | Description |
| --- | --- |
| **SAP ERP (ECC)** | Enterprise Resource Planning |
| **SAP CRM** | Customer Relationship Management |
| **SAP SCM** | Supply Chain Management |
| **SAP SRM** | Supplier Relationship Management |
| **SAP PLM** | Product Lifecycle Management |
{: caption="SAP Business Suite applications" caption-side="bottom"}

All Business Suite applications are supported on IBM Power Virtual Server, VPC bare metal servers, and VPC virtual servers. Deployment models include standalone applications, integrated suite with shared database, and hybrid deployments with SAP S/4HANA.

### SAP NetWeaver components
{: #netweaver-components}

| Component | Description |
| --- | --- |
| **SAP Web Dispatcher** | Load balancing and security component for distributing HTTP/HTTPS requests across SAP application servers |
| **SAP Gateway** | Enables OData services and connectivity for mobile applications and external systems |
| **SAP Fiori Front-end Server** | Dedicated server for SAP Fiori user interface, providing modern user experience |
| **SAP Solution Manager** | Central management and monitoring platform for SAP landscapes, supporting implementation, operations, and optimization |
| **SAP Router** | Secure network connection component for SAP support infrastructure access |
{: caption="SAP NetWeaver components" caption-side="bottom"}

All SAP NetWeaver components are supported on IBM Power Virtual Server (AIX and Linux), VPC bare metal servers, and VPC virtual servers.

### SAP Business One
{: #business-one}

SAP Business One is an ERP solution designed for small and medium-sized enterprises, providing integrated business management capabilities.

| Configuration | Supported databases | Recommended infrastructure |
| --- | --- | --- |
| Single-server deployment (small businesses) | SAP HANA, Microsoft SQL Server | VPC virtual servers |
| Distributed deployment (separate DB and app servers) | SAP HANA, Microsoft SQL Server | VPC bare metal servers (production), VPC virtual servers (cost-effective) |
{: caption="SAP Business One configurations" caption-side="bottom"}

## SAP AnyDB workloads
{: #anydb-workloads}

SAP applications support multiple database platforms beyond SAP HANA, collectively referred to as SAP AnyDB. 
VPC Infrastructure is the preferred platform for SAP AnyDB workloads.
It supports compute and storage options across VPC virtual servers and bare metal servers.

| Database | Description | Preferred infrastructure |
| --- | --- | --- |
| **IBM Db2** | IBM's enterprise relational database supporting SAP ECC, SAP Business Suite, and SAP NetWeaver applications | VPC Infrastructure (Linux); IBM Power Virtual Server (AIX only) |
| **Microsoft SQL Server** | Microsoft's relational database supporting SAP applications on Windows and Linux | VPC Infrastructure |
| **SAP ASE (Sybase)** | SAP's Adaptive Server Enterprise supporting SAP NetWeaver applications | VPC Infrastructure |
| **SAP MaxDB** | SAP MaxDB for SAP ECC and SAP NetWeaver-based systems on Linux and Windows | VPC Infrastructure |
{: caption="SAP AnyDB supported databases and preferred infrastructure" caption-side="bottom"}

For specific database version support and certification details, consult SAP documentation and IBM Cloud support.

## Additional SAP applications
{: #additional-applications}

| Application | Description |
| --- | --- |
| **SAP Content Server** | Document management and archiving solution for SAP systems |
| **SAP Data Intelligence** | Data orchestration and management platform for enterprise data landscapes |
| **SAP Commerce** | E-commerce platform for omnichannel customer experiences |
| **SAP Business Objects BI** | Business intelligence and analytics platform |
{: caption="Additional supported SAP applications" caption-side="bottom"}

For deployment guidance on these applications, consult IBM Cloud support and SAP documentation.

## Environment types
{: #environment-types}

| Environment | Purpose | SAP HANA | SAP AnyDB |
| --- | --- | --- | --- |
| **Production** | Mission-critical systems requiring high availability, disaster recovery, and maximum performance | IBM Power Virtual Server | VPC Infrastructure |
| **Quality assurance** | Testing and validation systems requiring production-like configurations | IBM Power Virtual Server | VPC Infrastructure |
| **Development** | Flexible environments for application development and customization | IBM Power Virtual Server | VPC Infrastructure |
| **Sandbox** | Proof-of-concept, training, and experimentation systems | IBM Power Virtual Server (or VPC for strict cost constraints) | VPC Infrastructure |
| **Disaster recovery** | Secondary systems for business continuity | IBM Power Virtual Server in a different region | VPC Infrastructure in a different region |
{: caption="SAP environment types and preferred infrastructure" caption-side="bottom"}

## Workload sizing guidance
{: #sizing-guidance}

Infrastructure selection is based on the database platform, not on database size.
IBM Power Virtual Server supports SAP HANA workloads that range from small development systems to production deployments of up to 30.5 TB.
You do not need to move to a different platform as workloads grow.

| Database platform | All sizes | Notes |
| --- | --- | --- |
| **SAP HANA** | IBM Power Virtual Server | Supports up to 30 TB; consistent platform from dev/test through production |
| **SAP AnyDB** (Db2, ASE, MaxDB, SQL Server) | VPC Infrastructure | Flexible compute (virtual servers and bare metal) for all AnyDB workloads |
{: caption="Workload platform guidance" caption-side="bottom"}

For detailed sizing guidance and certified profile specifications, see [Planning your SAP deployment](/docs/sap?group=plan).

## Architecture patterns
{: #architecture-patterns}

| Pattern | Description | Best for |
| --- | --- | --- |
| **Single-tier** | All SAP components (database and application servers) on a single server | Small systems, non-production environments |
| **Two-tier** | Database server and application servers separated | Most SAP deployments |
| **Three-tier** | Database server, application servers, and presentation layer separated | Large, distributed SAP landscapes |
| **Multi-tier** | Multiple application server layers, web dispatchers, and specialized components | Enterprise-scale SAP implementations |
| **Hybrid** | SAP systems distributed across on-premises and IBM Cloud infrastructure via Direct Link and VPN | Phased migration, DR-in-cloud scenarios |
{: caption="SAP architecture patterns" caption-side="bottom"}
