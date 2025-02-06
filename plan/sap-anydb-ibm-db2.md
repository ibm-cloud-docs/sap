---

copyright:
  years: 2020, 2025
lastupdated: "2025-02-06"

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

# AnyDB - IBM Db2
{: #anydb-ibm-db2}

The SAP systems in a landscape have specific requirements for servers, operating systems, network setup, and supported storage.
{: shortdesc}

Deploying SAP AnyDB on {{site.data.keyword.cloud_notm}} is similar to deployments with infrastructure with on-premises data centers. You can use the information that is provided from SAP and the RDBMS providers.

To assist your project's planning phase, more design considerations are provided for **SAP AnyDB - {{site.data.keyword.IBM_notm}} Db2** with {{site.data.keyword.ibm_cloud_sap}}.

## Overview of IBM Db2 for SAP with {{site.data.keyword.cloud}}
{: #anydb-ibm-db2-overview}

Several unique capabilities and features are available with {{site.data.keyword.IBM_notm}} Db2. All supported database features that are mentioned in [SAP Note 1555903 - DB6: Supported {{site.data.keyword.IBM_notm}} Db2 Database Features](https://me.sap.com/notes/1555903){: external} are available with {{site.data.keyword.cloud_notm}}, except for:
- The {{site.data.keyword.IBM_notm}} Db2 pureScale feature.
- Support for integrated cluster managers for HADR or shared disk high-availability clusters.

Before you start deploying the {{site.data.keyword.IBM_notm}} Db2 software, ensure that:
- Check all relevant {{site.data.keyword.IBM_notm}} Db2 information and prerequisites.
- Check all relevant SAP and {{site.data.keyword.IBM_notm}} Db2 information and prerequisites (for example, SAP Notes). Also, check the versions and fix pack levels of {{site.data.keyword.IBM_notm}} Db2 that are supported.
- All required packages are installed for the relevant OS that you are using for {{site.data.keyword.IBM_notm}} Db2.

### Documentation of IBM Db2 for SAP
{: #anydb-ibm-db2-documentation}

A good place to start is the [SAP community page for IBM Db2](https://pages.community.sap.com/topics/db2-for-linux-unix-windows){: external}.

{{site.data.keyword.IBM_notm}} Db2 support on SAP-certified Cloud IaaS:
- [IBM Knowledge Center for Db2 - Support for Db2 on public clouds (BYOSL, SAP Notes, Reference blueprints)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.5.0/com.ibm.db2.luw.licensing.doc/doc/r_suprt_db2_pblik_clouds.html){: external}

{{site.data.keyword.IBM_notm}} Db2 versions and fix pack levels that are supported by SAP:
- [SAP Note 101809 - DB6: Supported Db2 Versions and Fix Pack Levels](https://me.sap.com/notes/101809){: external}

{{site.data.keyword.IBM_notm}} Db2 11.5 on **UNIX/Linux** prerequisites documentation:
- [General IBM Db2 prerequisites on UNIX and Linux&reg;](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.5.0/com.ibm.db2.luw.qb.server.doc/doc/c0059823.html){: external}

{{site.data.keyword.IBM_notm}} Db2 and SAP NetWeaver on UNIX/Linux:
- [Installation of SAP Systems based on the Application Server **ABAP** of SAP NetWeaver 7.1 to 7.52 on UNIX: IBM Db2 LUW](https://help.sap.com/docs/SLTOOLSET/ce9e270ad34949969c16d09d1b099a26/930c6eefd5b94f02bd2fd9d90fbd47ee.html?version=CURRENT_VERSION){: external}
- [Installation of SAP Systems based on the Application Server **Java** of SAP NetWeaver 7.1 to 7.5 on UNIX: IBM Db2 LUW](https://help.sap.com/docs/SLTOOLSET/e85af73ba3324e29834015d03d8eea84/930c6eefd5b94f02bd2fd9d90fbd47ee.html?version=CURRENT_VERSION){: external}
- [SAP Note 1707361 - Inst. Systems based on NW 7.1 and Higher: UNIX Db2 for LUW](https://me.sap.com/notes/1707361){: external}

{{site.data.keyword.IBM_notm}} Db2 and SAP NetWeaver on Windows:
- [Installation of SAP Systems based on the Application Server **ABAP** of SAP NetWeaver 7.1 to 7.52 on Windows: IBM Db2 LUW](https://help.sap.com/docs/SLTOOLSET/77e3a615f633437e9c4549891bb6bad7/930c6eefd5b94f02bd2fd9d90fbd47ee.html?version=CURRENT_VERSION){: external}
- [Installation of SAP Systems based on the Application Server **Java** of SAP NetWeaver 7.1 to 7.5 on Windows: IBM Db2 LUW](https://help.sap.com/docs/SLTOOLSET/d65d4f66e8c34555ad9541734f9cd5b0/930c6eefd5b94f02bd2fd9d90fbd47ee.html?version=CURRENT_VERSION){: external}
- [SAP Note 1707362 - Inst. Systems based on NW 7.1 and Higher: Windows Db2 LUW](https://me.sap.com/notes/1707362){: external}

In the {{site.data.keyword.IBM_notm}} Knowledge Center for Db2, the documentation links provided refer to specific pages on an {{site.data.keyword.IBM_notm}} Db2 version. You can switch to the correct version by clicking the "Change version or product" in the upper left area of the {{site.data.keyword.IBM_notm}} Knowledge Center.
{: note}

### SAP on IBM Db2 using Intel Bare Metal
{: #anydb-ibm-db2-bare-metal}

See [SAP Note 2414097 - SAP Applications on {{site.data.keyword.IBM_notm}} Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external} for supported {{site.data.keyword.IBM_notm}} Db2 database versions.

A sample configuration is shown in:
- [Quick Study Tutorial - SAP NetWeaver deployment to Bare Metal on Classic Infrastructure, using RHEL](/docs/sap?topic=sap-quickstudy-bm-netweaver-rhel){: external}
- [Quick Study Tutorial - SAP NetWeaver deployment to Bare Metal on Classic Infrastructure, using Windows Server](/docs/sap?topic=sap-quickstudy-bm-netweaver-wins){: external}

### SAP on IBM Db2 using Intel Virtual Servers
{: #anydb-ibm-db2-intel-vs-vpc}

See [SAP Note 2927211 - SAP Applications on {{site.data.keyword.IBM_notm}} Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external} for supported {{site.data.keyword.IBM_notm}} Db2 database versions.

A sample configuration is shown in:
- [Quick Study Tutorial - SAP NetWeaver deployment to Intel Virtual Server on VPC Infrastructure, using RHEL](/docs/sap?topic=sap-quickstudy-vs-gen2-netweaver-rhel)

### SAP on IBM Db2 using IBM {{site.data.keyword.powerSys_notm}}s
{: #anydb-ibm-db2-power-vs}

This is a complementary offering from {{site.data.keyword.IBM_notm}} Power Systems, with low latency access to {{site.data.keyword.cloud_notm}} services
{: note}

See [SAP Note 2855850 - SAP Applications on {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s](https://me.sap.com/notes/2855850){: external} for supported {{site.data.keyword.IBM_notm}} Db2 database versions on AIX running on {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s.

## Infrastructure size considerations
{: #anydb-ibm-db2-infrastructure}

When you use {{site.data.keyword.IBM_notm}} Db2 with SAP applications, follow the general SAP sizing rules as described in the SAP Quick Sizer documentation. However, certain {{site.data.keyword.IBM_notm}} Db2 functions require special considerations.

When you use {{site.data.keyword.IBM_notm}} Db2 columnar organized tables, also known as {{site.data.keyword.IBM_notm}} Db2 BLU Acceleration, the minimal recommended server configuration is 64 GB memory and 4 CPU cores exclusively available for the database workload. For more information about sizing and usage of Db2 BLU Acceleration, see [SAP Note 1819734 - DB6: Use of BLU Acceleration](https://me.sap.com/notes/1819734){: external}.

### File Systems for IBM Db2
{: #anydb-ibm-db2-infrastructure-file-systems}

The following information describes the File Systems that are required for {{site.data.keyword.IBM_notm}} Db2 with SAP NetWeaver:

- [Recommended file system types for Db2](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/r0056470.html){: external}
- [Required file systems for {{site.data.keyword.IBM_notm}} Db2 with SAP NetWeaver ABAP (example is for Unix/Linux)](https://help.sap.com/docs/SLTOOLSET/ce9e270ad34949969c16d09d1b099a26/713eb64f45c6448c8dbe8a51b85680ee.html?version=CURRENT_VERSION){: external}

### SWPM and IBM Db2 port conflict
{: #anydb-ibm-db2-infrastructure-swpm-port}

The default port is `5912` for the TCP/IP communication between the Db2 server and the Db2 client when you are using SWPM to install SAP software.

However, this port is already defined and reserved by the [IANA Service Name and Transport Protocol Port Number Registry](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml?&page=96){: external}.

Choose a different port during the installation with SWPM.

Alternatively, it is possible to remove the entry for this port number from the `/etc/services` file:

```plaintext
...
cpdlc           5911/sctp               # Controller Pilot Data Link Communication
fis             5912/tcp                # Flight Information Services
fis             5912/udp                # Flight Information Services
fis             5912/sctp               # Flight Information Services
ads-c           5913/tcp                # Automatic Dependent Surveillance
...
```

## SAP application-specific considerations
{: #anydb-ibm-db2-app}

### SAP Business Warehouse with IBM Db2
{: #anydb-ibm-db2-app-bw}

When Using SAP Business Warehouse with the DB2 Database Partitioning Feature, a stable and fast network connections need to be established between all hosts in the Db2 DPF cluster. For details, check the following Information: [SAP Business Warehouse on {{site.data.keyword.IBM_notm}} Db2 for Linux, UNIX, and Windows 10.5 and Higher: Administration Tasks](https://help.sap.com/docs/DB6/4c49a344277943ad91358094fdaf9765/c289a552d161224fe10000000a445394.html?language=en-US){: external}.
