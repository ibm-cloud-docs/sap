---

copyright:
  years: 2020, 2024
lastupdated: "2024-12-03"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads

subcollection: sap

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:note: .note}
{:tip: .tip}

# AnyDB - SAP ASE
{: #anydb-sap-ase}

The SAP systems in a landscape have specific requirements for servers, operating systems, network setup, and supported storage.
{: shortdesc}

Deployment of SAP AnyDB on {{site.data.keyword.cloud_notm}} is similar to deployments with infrastructure with on-premises data centers. Therefore, use the information that is provided from SAP and the RDBMS providers.

To assist your project's planning phase, more design considerations are provided for **SAP AnyDB - SAP ASE** with {{site.data.keyword.ibm_cloud_sap}}.

## Overview of SAP ASE with {{site.data.keyword.cloud}}
{: #anydb-sap-ase-overview}

Before you start the software deployment of SAP ASE, ensure that:
- Check all relevant SAP ASE information and prerequisites (for example, SAP Notes)
- All required packages are installed for the relevant OS that is used for SAP ASE

### Documentation of SAP ASE
{: #anydb-sap-ase-documentation}

A good entry point into the documentation is the [SAP Community page for SAP Adaptive Server Enterprise (ASE)](https://community.sap.com/topics/applications-on-ase){: external}

SAP ASE documentation:
- [SAP Help Portal - SAP Adaptive Server Enterprise (ASE)](https://help.sap.com/viewer/product/SAP_ASE/latest/en-US){: external}
    - [SAP Help Portal - SAP Adaptive Server Enterprise (ASE) Installation and Upgrade Guide for Linux&reg;](https://help.sap.com/viewer/23c3bb4a29be443ea887fa10871a30f8/latest/en-US){: external}
    - [SAP Help Portal - SAP Adaptive Server Enterprise (ASE) Installation and Upgrade Guide for IBM AIX](https://help.sap.com/viewer/07e2d10774874e3bb4d991ef08d678e8/latest/en-US){: external}
    - [SAP Help Portal - SAP Adaptive Server Enterprise (ASE) Installation and Upgrade Guide for Windows](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/latest/en-US){: external}
- [SAP Note 1748888 - Installing Systems Based on NW 7.3 and Higher: SAP ASE](https://me.sap.com/notes/1748888){: external}
- [SAP Note 2489781 - SAP ASE 16.0 SP03 Supported Operating Systems and Versions](https://me.sap.com/notes/2489781){: external}

For a current overview of the combinations of SAP ASE, SAP NetWeaver (or other SAP components), and operating systems - see the [Product Availability Matrix (PAM)](http://support.sap.com/pam){: external}

SAP ASE and SAP NetWeaver **on UNIX/Linux**:
- [Installation of SAP Systems Based on the Application Server **ABAP** of SAP NetWeaver 7.3 to 7.52 on UNIX/Linux: SAP Adaptive Server Enterprise](https://help.sap.com/viewer/e345db692e3c43928199d701df58c0d8/CURRENT_VERSION/en-US){: external}
- [Installation of SAP Systems Based on the Application Server **Java** of SAP NetWeaver 7.3 to 7.5 on UNIX/Linux: SAP Adaptive Server Enterprise](https://help.sap.com/viewer/01f04921ac57452983980fe83a3ce10d/CURRENT_VERSION/en-US){: external}

SAP ASE and SAP NetWeaver **on Windows**:
- [Installation of SAP Systems Based on the Application Server **ABAP** of SAP NetWeaver 7.3 to 7.52 on Windows: SAP Adaptive Server Enterprise](https://help.sap.com/viewer/b0c437ae5d1a4c70847bb55973923352/CURRENT_VERSION/en-US)
- [Installation of SAP Systems Based on the Application Server **Java** of SAP NetWeaver 7.3 to 7.5 on Windows: SAP Adaptive Server Enterprise](https://help.sap.com/viewer/2b04bc65d45544bb8ba67c258d4fb2e2/CURRENT_VERSION/en-US)

### SAP ASE using Intel Virtual Servers
{: #anydb-sap-ase-intel-vs-vpc}

See [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external} for supported SAP ASE database versions.

### SAP ASE using IBM Power Virtual Servers
{: #anydb-sap-ase-power-vs}

This is a complementary offering from {{site.data.keyword.IBM_notm}} Power Systems, with low latency access to {{site.data.keyword.cloud_notm}} services.
{: note}

See [SAP Note 2855850 - SAP Applications on IBM Power Virtual Servers](https://me.sap.com/notes/2855850){: external} for supported SAP ASE database versions on AIX running on {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s.
