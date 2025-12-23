---
copyright:
  years: 2025
lastupdated: "2025-12-23"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, HANA
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# SAP Solution Manager with HANA (RHEL)
{: #sap-sol-mgr-hanadb-rhel}

The {{site.data.keyword.cloud}} architecture provides superior technical capabilities, such as a software definable environment critical to a cloud infrastructure, programmable interfaces, and hundreds of hardware and network configurations. It is designed to deliver a higher level of flexibility by mixing virtual and dedicated servers to fit various workloads, automation of interfaces, and hybrid deployment options. The {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure offering provides you with a best-fit selection of bare metal and virtualization-based servers on which the SAP software stack is run.

SAP HANA database is one of several databases that can be deployed on SAP Solution Manager 7.2 Advanced Business Application Programming (ABAP) and Java™ stack in the {{site.data.keyword.cloud}}. {{site.data.keyword.cloud_notm}} is certified for running SAP NetWeaver application servers ABAP, Java™, and SAP products based on these application server stacks.

## Overview
{: #sap-sol-mgr-hanadb-rhel-overview}

SAP Solution Manager enables users to manage their SAP and non-SAP applications in a better way. It centralizes, enhances, automates, and improves the management of the entire system landscape, thus reducing the total cost of ownership. SAP Solution Manager also supports users in adapting the landscape to new requirements, for example implementing new business processes.

SAP Solution Manager covers the entire application lifecycle as follows:

* Identification and documentation of as-is critical business processes for SAP solutions, including partner components, custom code, and interfaces.
* Identification of business needs and preparation of realization through collaborative processes.
* Alignment of new requirements with enhancements delivered by SAP and partners.
* Specification of custom developments, documentation of developed code, and definition of unit test requirements for custom code.
* Configuration of project scope, adaptation of standard SAP process documentation to the custom solution, and definition of unit test requirements.
* Definition of integration testing requirements and test scope, based on change impact analysis, development of automated and manual test cases, management of testers, comprehensive reporting of test progress and results.
* Synchronize technical objects from the development to the production environment across the technology stack.
* Analysis of the potential impact of solution updates on key solution performance indicators, such as stability and performance.
* Continuous control of mission-critical processes, interfaces, components, and jobs, based on business-driven key performance indicators (KPIs).
* Integrated solution-oriented incident management from user to SAP and partners, through the SAP Global Support Backbone, backed by service level agreement (SLAs).
* Technology-independent isolation of problems within a solution context, based on a unified SAP analysis framework.
* Comprehensive management of SAP support services from automatic alerting of service requirements, to delivery and follow-up, with a specific focus on Continuous Quality Checks (CQCs) and support advice.
* All capabilities for end-to-end monitoring, alerting, analysis, and administration of SAP solutions in heterogeneous system landscapes. Lifecycle management of corrective software packages from discovery and retrieval to test scope optimization and optional automatic deployment in the production environment.
* Fully supporting the transition to SAP S/4HANA.

## Architecture diagram
{: #sap-sol-mgr-hanadb-rhel-diagram}

![Figure 1. SAP Solution Manager with HANA DB on RHEL 9.4](../../images/vpc-intel-vsi-sol-mgr-hana-rhel.svg "SAP Solution Manager with HANA DB on RHEL 9.4"){: caption="SAP Solution Manager with HANA DB on RHEL 9.4" caption-side="bottom"}

You have one database for ABAP instance and one database for Java instance. This is because the database type is HANA DB.

In SAP HANA 2.0, the multi-tenant database container (MDC) model has replaced Multiple Components on One Database (MCOD) as the standard and only supported deployment mode. Here, a single SAP HANA system hosts multiple isolated "tenant databases" instead of multiple components on a single database. While the older versions used MCOD uses tenant databases for a more secure and performant solution.

SAP Solution Manager provides integrated methodologies, tools, and content needed to implement, support, operate, and monitor your enterprise SAP solutions.

A number of SAP Solution Manager applications run on the SAP Customer Relationship Management (SAP CRM) platform, including:

* Change management
* IT Service Management (incidents and problems)
* SAP Engagement and Service Delivery (issues and top issues)
* Process and Project Management (business requirements)
* Helps you manage core business processes and link the business processes with the underlying IT infrastructure.
* Minimizes risk and increases the reliability of IT solutions.
* Supports both SAP and non-SAP software and potentially reduces total cost of ownership (TCO) throughout the application lifecycle.

## Related information
{: #sap-sol-mgr-hanadb-rhel-rel-info}

The following SAP Notes and documentation articles need to be consulted when planning the installation:

* [2009879 - SAP HANA Guidelines for Red Hat Enterprise Linux (RHEL) Operating System](https://me.sap.com/notes/2009879){: external}
* [3108316 - Red Hat Enterprise Linux 9.x: Installation and Configuration](https://me.sap.com/notes/3108316){: external}
* [2378874 - Install SAP Solutions on Linux on IBM Power Systems (little endian)](https://me.sap.com/notes/2378874){: external}
* [2218464 - Supported products when running SAP HANA on IBM Power Systems](https://me.sap.com/notes/2218464){: external}
* [2235581 - SAP HANA: Supported Operating Systems](https://me.sap.com/notes/2235581){: external}
* [2493172 - SAP HANA Hardware and Cloud Measurement Tools](https://me.sap.com/notes/2493172){: external}
* [2158828 - Minimal database system platform requirements for SAP NetWeaver 7.5](https://me.sap.com/notes/2158828){: external}
* [3108302 - SAP HANA DB: Recommended OS Settings for RHEL 9](https://me.sap.com/notes/3108302){: external}
* [2886607 - Running SAP applications compiled with GCC 9.x](https://me.sap.com/notes/2886607){: external}
* [2403493 - Solution Manager: Enabling the Monitoring of HANA 2.0](https://me.sap.com/notes/2403493){: external}
* [2545473 - How to enable monitoring of HANA 2.0 according to Note 2403493 in Solution Manager 7.2 system](https://me.sap.com/notes/2545473/E){: external}
* [2538670 - Deployment options for SAP Solution Manager 7.2 on SAP HANA](https://me.sap.com/notes/2538670/E){: external}
* [2592457 - Outside Discovery and SAP HANA systems compatibility - Solution Manager 7.1/7.2](https://me.sap.com/notes/2592457/E){: external}
* [2466024 - Functionality of "HANA and BI Monitoring" in Solution Manager 7.2](https://me.sap.com/notes/2466024/E){: external}
* [2260715 - Preparation of SAP Solution Manager 7.2 on SAP HANA - Data Reduction (Application Operations)](https://me.sap.com/notes/2260715/E){: external}
* [2477204 - FAQ: SAP HANA Services and Ports](https://me.sap.com/notes/2477204){: external}
* [2789262 - Connection problems between Windows hosts and HANA database on Linux hosts](https://me.sap.com/notes/2789262){: external}
* [2380229 - SAP HANA Platform 2.0 - Central Note](https://me.sap.com/notes/2380229){: external}
* [401162 - Linux: Avoiding TCP/IP port conflicts and start problems](https://me.sap.com/notes/401162){: external}
