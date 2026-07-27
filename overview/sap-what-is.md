---
copyright:
  years: 2026
lastupdated: "2026-07-23"
keywords: SAP, SAP on IBM Cloud, SAP infrastructure, SAP HANA, SAP NetWeaver, SAP S/4HANA
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# What is SAP on IBM Cloud?
{: #what-is-sap}

SAP on IBM Cloud enables you to run enterprise SAP workloads on IBM Cloud's SAP-certified infrastructure, combining the power of SAP applications with the flexibility, security, and global reach of IBM Cloud.
{: shortdesc}

IBM Cloud provides multiple infrastructure options for SAP workloads, all hosted in IBM Cloud data centers and certified by SAP to meet stringent performance and compatibility requirements. You can choose from Classic Infrastructure with dedicated bare metal servers, VPC Infrastructure for modern cloud-native environments, and IBM Power Virtual Server for enterprise-grade, high-performance SAP HANA deployments on Power Systems architecture.

## Infrastructure options for SAP workloads
{: #infrastructure-options}

IBM Cloud offers three SAP-certified infrastructure platforms, each designed for specific deployment scenarios and performance requirements.

| Platform | Best for | OS support | SAP HANA max |
|---|---|---|---|
| **IBM Power Virtual Server** | Mission-critical SAP HANA production, AIX workloads | AIX, RHEL, SLES | Up to 30 TB |
| **VPC Infrastructure** | New deployments, dev/test, cloud-native architectures | RHEL, SLES, Windows | VSI: 5.7 TB, bare metal: 12 TB |
| **Classic Infrastructure** | Existing Classic deployments only | RHEL, SLES, Windows | Up to 12 TB |
{: caption="IBM Cloud infrastructure platforms for SAP" caption-side="bottom"}

For full platform comparisons across compute, storage, networking, and security, see [Infrastructure platforms for SAP workloads](/docs/sap?topic=sap-deployment-options-comparison).

## Supported SAP applications and databases
{: #supported-sap-workloads}

IBM Cloud supports the full spectrum of SAP applications and databases across all infrastructure platforms.

### SAP applications
{: #sap-applications}

| Application | Description |
|---|---|
| **SAP S/4HANA** | Next-generation intelligent ERP suite built on the SAP HANA in-memory database platform |
| **SAP ECC (ERP Central Component)** | Traditional SAP ERP system supporting SAP HANA, IBM Db2, Oracle, Microsoft SQL Server, and SAP ASE |
| **SAP BW/4HANA** | Data warehousing solution built for SAP HANA, providing real-time analytics and reporting |
| **SAP Business One** | ERP solution designed for small and medium-sized businesses |
| **SAP NetWeaver** | Application server platform for SAP business applications |
{: caption="Supported SAP applications" caption-side="bottom"}

### SAP databases
{: #sap-databases}

| Database | Description |
|---|---|
| **SAP HANA** | In-memory database platform that powers SAP S/4HANA, BW/4HANA, and other SAP applications. Supported on all three infrastructure platforms with optimized performance on IBM Power Virtual Server. |
| **SAP AnyDB** | Multiple third-party databases including IBM Db2, Oracle Database, Microsoft SQL Server, SAP ASE (Sybase), and SAP MaxDB |
{: caption="Supported SAP databases" caption-side="bottom"}

### SAP components
{: #sap-components}

IBM Cloud infrastructure supports all standard SAP components including SAP Solution Manager, SAP Web Dispatcher, SAP Fiori Front-end Server, SAP Gateway, SAP Content Server, and SAP Router.

For detailed coverage of each supported workload, configurations, and sizing guidance, see [Supported SAP workloads and scenarios](/docs/sap?topic=sap-supported-workloads).

## How SAP on IBM Cloud works
{: #how-it-works}

Deploying and running SAP on IBM Cloud follows a structured approach:

1. **Plan**: Review SAP-certified profiles and select appropriate infrastructure based on your workload requirements, performance needs, and operating system preferences. See [Planning your SAP deployment](/docs/sap?group=plan).

2. **Provision**: Deploy compute, storage, and network resources through the IBM Cloud console, CLI, API or Terraform. Provisioning times vary by platform, with virtual servers available in minutes and bare metal servers typically available within hours.

3. **Install**: Install SAP software using standard SAP installation procedures and tools such as SAP Software Provisioning Manager (SWPM).

4. **Configure**: Set up high availability, disaster recovery, backup, and monitoring solutions based on your business requirements.

5. **Operate**: Manage and maintain your SAP systems using SAP tools, IBM Cloud services, and your organization's operational procedures.

For detailed deployment procedures, see the [How-to guides](/docs/sap?group=howto).

## SAP certification
{: #sap-certification}

All infrastructure profiles documented in this guide are SAP-certified, which means they meet SAP's stringent requirements for performance, compatibility, and support. SAP certification ensures that:

- Infrastructure meets or exceeds SAP performance benchmarks
- Configurations are validated for compatibility with SAP software
- Deployments are supported by SAP for production workloads
- Profiles are regularly tested and updated to maintain certification

For current SAP certification details, see the [SAP Certified and Supported SAP HANA Hardware Directory](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas){: external} and [SAP Note 2414097 - SAP Applications on IBM Cloud](https://launchpad.support.sap.com/){: external}.

## Key benefits
{: #key-benefits}

| Benefit | Description |
|---|---|
| **Enterprise-grade infrastructure** | Leverage IBM's decades of experience running mission-critical workloads with enterprise-class hardware and support |
| **SAP-certified profiles** | Choose from preconfigured, SAP-certified infrastructure profiles designed to meet SAP performance requirements |
| **Global availability** | Deploy SAP workloads in IBM Cloud data centers across the globe |
| **Flexible deployment options** | Select from multiple infrastructure platforms (Power Virtual Server, VPC, Classic) and deployment models based on your specific requirements |
| **Security and compliance** | Benefit from IBM Cloud's comprehensive security features and compliance certifications including ISO 27001, SOC 2, and industry-specific standards |
| **Hybrid cloud integration** | Integrate seamlessly with on-premises SAP systems and other cloud services using IBM Cloud Direct Link and VPN connectivity |
{: caption="Key benefits of SAP on IBM Cloud" caption-side="bottom"}

## Common use cases
{: #use-cases}

| Use case | Description |
|---|---|
| **New SAP deployments** | Launch new SAP S/4HANA or other SAP systems directly in the cloud, taking advantage of rapid provisioning and cloud-native capabilities |
| **SAP migration to cloud** | Move existing on-premises SAP systems to IBM Cloud infrastructure with minimal disruption |
| **Development and test environments** | Create isolated development, test, and sandbox environments for SAP projects with flexible sizing and rapid provisioning |
| **High availability** | Deploy highly available SAP systems with automated failover capabilities using multi-zone deployments, SAP HANA System Replication, and cluster solutions |
| **Disaster recovery** | Implement disaster recovery solutions for on-premises or cloud-based SAP systems using IBM Cloud as a secondary site |
| **Capacity expansion** | Quickly add capacity for seasonal peaks, business growth, or new business units without capital investment in physical infrastructure |
| **SAP HANA migration** | Migrate from SAP AnyDB to SAP HANA on high-performance infrastructure, particularly IBM Power Virtual Server for optimal performance |
{: caption="Common SAP on IBM Cloud use cases" caption-side="bottom"}
