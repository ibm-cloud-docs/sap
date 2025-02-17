---
copyright:
  years: 2020, 2025
lastupdated: "2025-02-17"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


{{site.data.keyword.attribute-definition-list}}

# AnyDB - Microsoft SQL Server
{: #anydb-ms-sql-server}

The SAP systems in a landscape have specific requirements for servers, operating systems, network setup, and supported storage.
{: shortdesc}

Deployment of SAP AnyDB on {{site.data.keyword.cloud_notm}} is similar to deployments with infrastructure with on-premises data centers. Therefore, use the information that is provided from SAP and the RDBMS providers.

To assist your project's planning phase, there are additional design considerations for **SAP AnyDB - Microsoft SQL Server** with {{site.data.keyword.ibm_cloud_sap}}.

## Overview of MS SQL Server for SAP with IBM Cloud
{: #anydb-ms-sql-server-overview}

Before you start deploying the MS SQL Server software, ensure that:
 * Check all relevant SAP and MS SQL Server information and prerequisites (for example, SAP Notes); including versions and fix pack levels of MS SQL Server that are supported
 * All required packages are installed for the relevant OS that you are using for MS SQL

## Documentation of MS SQL Server for SAP
{: #anydb-ms-sql-server-documentation}

A good entry point into the documentation is the [SAP community page for MS SQL Server](https://pages.community.sap.com/topics/sql-server){: external}
For a current overview of the combinations of MS SQL Server, SAP NetWeaver (or other SAP components), and operating systems - see the [Product Availability Matrix (PAM)](https://userapps.support.sap.com/sap/support/pam){: external}.

MS SQL Server database and SAP NetWeaver on Windows
 * [Installation of SAP Systems Based on the Application Server ABAP of SAP NetWeaver 7.3 EHP1 to 7.52 : MS SQL Server](https://help.sap.com/docs/SLTOOLSET/c22d9ecc82ca4ab591a91942fe5c0020/9420dabb130e4ae1996b3f39e202cc6e.html?version=CURRENT_VERSION){: external}
 * [Installation of SAP Systems Based on the Application Server Java of SAP NetWeaver 7.5 and SAP Solution Manager 7.2 SR2 Java : MS SQL Server](https://help.sap.com/docs/SLTOOLSET/34ba60e8526d4110921c9c0fd05b4b6d/9420dabb130e4ae1996b3f39e202cc6e.html?version=CURRENT_VERSION){: external}

MS SQL Server licensing supported by SAP
 * [SAP Note 1491158 - Information About the Microsoft SQL Server License Scope](https://me.sap.com/notes/1491158){: external}
 * [SAP Note 398136 - Support Policy for Microsoft SQL Server](https://me.sap.com/notes/398136){: external}

MS SQL Server release/support information
 * [SAP Note 2807743 - Release planning for Microsoft SQL Server 2019](https://me.sap.com/notes/2807743){: external}
 * [SAP Note 2779625 - Setting up Microsoft SQL Server 2019](https://me.sap.com/notes/2779625){: external}
 * [SAP Note 2492596 - Release planning for Microsoft SQL Server 2017](https://me.sap.com/notes/2492596){: external}
 * [SAP Note 2484674 - Setting up Microsoft SQL Server 2017](https://me.sap.com/notes/2484674){: external}
 * [SAP Note 2201059 - Release planning for Microsoft SQL Server 2016](https://me.sap.com/notes/2201059){: external}
 * [SAP Note 1177356 - MS SQL Server: End of Support for SAP Releases](https://me.sap.com/notes/1177356){: external}
 * [SAP Note 62988 - Service packs for MS SQL Server](https://me.sap.com/notes/62988){: external}

## SAP on MS SQL Server using Intel Bare Metal
{: #anydb-ms-sql-server-bm}

See [SAP Note 2414097 - SAP Applications on IBM Cloud: Supported DB/OS and IBM Cloud Bare Metal Server Types](https://me.sap.com/notes/2414097){: external} for supported MSSQL database versions.

A sample configuration is shown in:
 * [Quick Study Tutorial - SAP NetWeaver deployment to Bare Metal on Classic Infrastructure, using Windows Server](/docs/sap?topic=sap-quickstudy-bm-netweaver-wins)

##  SAP on MS SQL Server using Intel Virtual Servers (Gen2)
{: #anydb-ms-sql-server-vsi}

See [SAP Note 2927211 - SAP Applications on IBM Virtual Private Cloud: Supported DB/OS and IBM Gen 2 Virtual Server Instances](https://me.sap.com/notes/2927211){: external} for supported MSSQL database versions.

A sample configuration is shown in:
 * [SAP NetWeaver deployment to Intel Virtual Server (Gen2) on VPC Infrastructure that uses Windows Server](/docs/sap?topic=sap-quickstudy-vs-gen2-netweaver-wins)
