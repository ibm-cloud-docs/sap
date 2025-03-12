---
copyright:
  years: 2020, 2025
lastupdated: "2025-03-12"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# AnyDB - SAP MaxDB
{: #anydb-sap-maxdb}

The SAP systems in a landscape have specific requirements for servers, operating systems, network setup, and supported storage.
{: shortdesc}

Deploying SAP AnyDB on {{site.data.keyword.cloud_notm}} is similar to deploying the infrastructure with on-premises data centers. Therefore, use the information that is provided from SAP and the RDBMS providers.

To assist your project's planning phase, extra design considerations are listed for **SAP AnyDB - SAP Max DB** with {{site.data.keyword.ibm_cloud_sap}}.

## Overview of SAP MaxDB with {{site.data.keyword.cloud}}
{: #anydb-sap-maxdb-overview}

Before you start deploying SAP MaxDB software, be sure to:
- Check all relevant SAP MaxDB information and prerequisites (for example, SAP Notes)
- Verify that all required packages are installed for the relevant OS that that is used for SAP MaxDB

### Documentation of SAP MaxDB
{: #anydb-sap-maxdb-documentation}

A good entry point into the documentation is the [SAP community page for SAP MaxDB](https://pages.community.sap.com/topics/maxdb){: external}.

SAP MaxDB documentation:
- [SAP Help Portal - SAP MaxDB](https://help.sap.com/docs/SAP_MAXDB){: external}
- [SAP Note 767598 - Available SAP MaxDB documentation](https://me.sap.com/notes/767598){: external}
- [SAP Note 1020175 - FAQ: SAP MaxDB installation, upgrade, or applying a patch](https://me.sap.com/notes/1020175){: external}

For a current overview of the combinations of SAP MaxDB, SAP NetWeaver (or other SAP components), and operating systems, see the [Product Availability Matrix (PAM)](https://userapps.support.sap.com/sap/support/pam){: external}.

### SAP MaxDB using Intel Bare Metal
{: #anydb-sap-maxdb-bare-metal}

See [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external} for supported SAP MaxDB versions.

### SAP MaxDB using Intel Virtual Servers
{: #anydb-sap-maxdb-intel-vs-gen2}

See [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external} for supported SAP MaxDB versions.

### SAP MaxDB using IBM {{site.data.keyword.powerSys_notm}}s
{: #anydb-sap-maxdb-power-vs}

See [SAP Note 2855850 - SAP Applications on IBM {{site.data.keyword.powerSys_notm}}s](https://me.sap.com/notes/2855850){: external} for supported SAP MaxDB versions.
