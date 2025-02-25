---
copyright:
  years: 2020, 2025
lastupdated: "2025-02-24"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# FAQ of {{site.data.keyword.ibm_cloud_sap}}
{: #faq-ibm-cloud-for-sap}

## Introduction
{: #faq-intro}

This FAQ provides answers to questions about:
- [{{site.data.keyword.ibm_cloud_sap}} portfolio](#faq-ibm-cloud-for-sap-portfolio)
- [Licensing and pricing](#faq-licensing-pricing)
- [RISE with SAP Subscription Model](#faq-rise-with-sap-subscription)
- [SAP-certified IBM {{site.data.keyword.powerSys_notm}}s](#faq-sap-certified-power-vsi)
- [SAP HANA database](#faq-sap-hana)
- [SAP NetWeaver and SAP applications](#faq-sap-nw-generic)
- [SAP Notes for the {{site.data.keyword.ibm_cloud_sap}} portfolio](#faq-ibm-cloud-for-sap-portfolio-snotes)

Additional FAQs cover specific topics, such as:
- [Moving SAP Workloads](/docs/sap?topic=sap-faq-moving-sap-workloads)

## IBM Cloud for SAP portfolio
{: #faq-ibm-cloud-for-sap-portfolio}


### Can SAP workloads run on any IBM Cloud server?
{: #faq-sap-run-on-any-server}

No, SAP HANA and SAP NetWeaver run only on SAP-certified instances on IBM Power Virtual Server® and IBM Cloud instances.

For proof of concept (POC) or evaluation, a non-certified instance can be used to explore IBM Cloud at a lower cost. However, SAP software may not function as designed on a non-certified server and is not supported by SAP.

SAP considers both production and development/testing (dev/test) systems as productive systems, as specified in [SAP Note 2271345](https://me.sap.com/notes/2271345){: external}.
{: note}


### Can SAP software installation media and distributions be downloaded directly from IBM Cloud?
{: #faq-download-sap-installation-media}

No, all SAP software installation media is available directly from SAP at [sap.com](https://support.sap.com/en/my-support/software-downloads.html){: external}.

SAP ID credentials are required to download SAP software installation media.
{: note}


### Who is responsible for deploying SAP software on IBM Cloud?
{: #faq-who-responsible-for-deployment}

IBM Cloud offers two deployment models for running SAP workloads:
- RISE with SAP on Power Virtual Server – A software as a service (SaaS) offering from SAP.
- SAP as an infrastructure as a service (IaaS) on IBM Cloud – A customer-managed deployment model.

IBM Cloud’s IaaS is a customer-managed environment, meaning the customer or a contracted Business Partner is responsible for all changes to the operating system and deployed applications. The installation and configuration of SAP software must align with SAP's guidance for the intended business scenario and usage.

Service providers for IBM Cloud for SAP

The following table outlines service types and the business partner categories that provide them:

|Service type|Provided by|Description|
|-----|-----|-----|
|Consulting, advisory, and implementation|SAP Global Systems Integrator (GSI) providers|Advise and manage SAP implementation and deployment projects.|
|Application management|SAP Application Management Services (AMS) providers|Maintain existing SAP deployments, including optional incremental functional or development changes.|
|End-to-end managed services|SAP Managed Services Providers (MSP)|Oversee SAP implementation, deployment, and management of infrastructure, OS, SAP technical applications, and SAP business applications. These services typically exclude incremental functional or development changes.|
{: caption="Services and Business Partner Types" caption-side="top"}

For example, some of the services that are available in partnership between {{site.data.keyword.cloud_notm}} and IBM Services include:
- [GSI and IBM Services: SAP consulting and implementation services](https://www.ibm.com/consulting/sap){: external}
- [AMS and IBM Services: SAP application management and development solutions](https://www.ibm.com/new/announcements){: external}
- [MSP and IBM Services: Managed applications for SAP](https://www.ibm.com/cloud/sap){: external}


### Do I need IBM Db2 to run SAP NetWeaver on {{site.data.keyword.cloud_notm}}?
{: #faq-need-db2}

No, SAP NetWeaver supports multiple SAP AnyDB options and SAP HANA on IBM Cloud-certified infrastructure. IBM Db2 is not required.

Refer to [SAP Note 2414097 for SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external} and the [SAP Product Availability Matrix](https://support.sap.com/en/release-upgrade-maintenance.html#section_1969201630){: external} for details.

For IBM Db2 information, see [SAP Note 2927211](https://me.sap.com/notes/2927211){: external} and the SAP on IBM Db2 for Linux, UNIX, and Windows (LUW) page on [SAP Community page](https://pages.community.sap.com/topics/db2-for-linux-unix-windows){: external}.

### Why was {{site.data.keyword.Db2_on_Cloud_short}} chosen for {{site.data.keyword.cloud_notm}} certification?
{: #faq-why-db2-for-certification}

IBM Db2 is seamlessly integrated into SAP and offers:
- Lower total cost of ownership
- High performance
- Simplified administration

IBM Systems has used IBM Db2 in benchmark tests for over four decades, continuing this tradition.

### Can I split my distributed SAP environment between different data centers?
{: #faq-split-distributed-sap-env-between-dcs}

It is recommended to keep all nodes in the same location (for example, availability zone or datacenter) and networking constructs (for example, subnet, VPC, and VLAN) to avoid latency and timeouts that may affect system responsiveness.

### Can I split my distributed SAP environment between {{site.data.keyword.cloud}} Bare Metal and {{site.data.keyword.cloud}} virtual servers?
{: #faq-split-distributed-sap-env-between-cloud-offerings}

RDBMS on Intel Bare Metal Servers in the IBM Cloud Classic Infrastructure that comply to [SAP Note 2414097](https://me.sap.com/notes/2414097){: external} can be supported when connected to SAP application server on IBM Cloud Virtual Private Cloud (VPC), provided they are in the same datacenter or availability zone and use IBM Cloud transit gateway local routing.


### Can I achieve SAP high availability as defined by SAP architecture?
{: #faq-achieve-sap-ha}

High availability for SAP can be achieved for:
- SAP NetWeaver High Availability - [SAP NetWeaver design considerations for High Availability configuration](/docs/sap?topic=sap-netweaver-design-considerations#netweaver-ha)
- SAP HANA High Availability - [Implementing High Availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-ha-overview) and [SAP Note 2057595](https://me.sap.com/notes/2057595){: external}

- SAP AnyDB High Availability (for example, IBM Db2, MS SQL, and so on.)
   - [Db2](https://me.sap.com/notes/1612105/){: external}
   - MaxDB
   - [Oracle](https://me.sap.com/notes/527843/){: external}
   - SQL Server

High availability can be configured at:
- SAP Technical Application layer (for example, system replication, system clustering)
- Hardware layer (for example, storage replication)

See the respective topics in the **_Get Started_** section for:
* [SAP HANA design considerations for High Availability and Disaster Recovery (HA/DR)](/docs/sap?topic=sap-hana-design-considerations#hana-ha)
* [SAP HANA backups - Storage impacts on Recovery Time Objective (RTO)](/docs/sap?topic=sap-storage-design-considerations#storage-performance-backup-rto)


### How do I connect my SAP Systems running on {{site.data.keyword.cloud_notm}} to my on-premises systems?
{: #faq-connect-sap-to-on-premises}

IBM Cloud provides multiple secure connectivity options. Refer to [Connectivity to your SAP system landscape](/docs/sap?topic=sap-determine-access).


## Licensing and pricing
{: #faq-licensing-pricing}


### How does SAP licensing work with IBM Cloud for SAP infrastructure-as-a-service?
{: #faq-sap-licensing-for-sap-iaas}

IBM Cloud SAP-certified infrastructure follows the Bring Your Own License (BYOL) model, a standard approach for SAP workloads used for decades. This model applies to:
-	SAP business application licenses (for example, SAP S/4HANA, SAP ECC)
-	SAP technical application licenses (for example, SAP HANA, SAP NetWeaver)
-	SAP OEM licenses (for example, SAP AnyDB OEM, such as MS SQL)


### How does operating system (OS) licensing work with IBM Cloud for SAP infrastructure-as-a-service?
{: #faq-os-licensing-for-sap-iaas}

The OS license cost, including applicable subscriptions for SAP-specific OS packages and support, is included when selecting the OS in the order form for an SAP-certified server.


### How are the SAP-certified IBM Cloud servers priced?
{: #faq-sap-servers-pricing}

Pricing is available through the IBM Cloud catalogue during the ordering process. Any applicable discounts, such as those from reserved instances pricing agreements or subscription accounts, are automatically calculated.

For SAP-certified bare metal servers certified as HANA appliances, pricing includes high-performance local storage.

As a standard practice among cloud service providers, IBM Cloud for SAP virtual servers use redundant network block or file storage. These storage costs may not be included in the initial price estimate. Additionally, there are no network bandwidth charges for local network traffic between the virtual server host and the storage host on IBM Cloud.
{: note}

## RISE with SAP subscription model
{: #faq-rise-with-sap-subscription}

In addition to the Bring Your Own License model, RISE with SAP is a Software as a Service offering on IBM Power Virtual Server. SAP provides the software as a subscription-based service.

* [RISE with SAP on IBM {{site.data.keyword.powerSys_notm}}](https://www.ibm.com/cloud/rise-with-sap){: external} – A new Hyperscaler offering where SAP delivers RISE with SAP as a SaaS solution running on IBM Power Virtual Server. IBM Consulting and Global Systems Integrator (GSI) partners provide advisory, implementation, and migration services within the RISE with SAP framework.

* [BREAKTHROUGH with IBM for RISE with SAP](https://www.ibm.com/consulting/rise-with-sap){: external} – A single-partner solution where IBM offers SAP RISE on IBM Cloud, with IBM Consulting handling advisory, implementation, and migration services.

## SAP-certified IBM {{site.data.keyword.powerSys_notm}}s
{: #faq-sap-certified-power-vsi}

### What are the operating system (OS) requirements for Db2, AIX and SAP NetWeaver?
{: #faq-os-requirements-for-db2-and-netweaver}

Refer to the following SAP Notes for OS requirements:

- [SAP Note 1780629 - AIX: Minimal OS requirements for SAP kernel](https://me.sap.com/notes/1780629){: external}
- [SAP Note 2267287 - Using SAP systems with AIX 7.2](https://me.sap.com/notes/2267287){: external}


### Where is the `aioservers` setting in AIX 7.x for Oracle installs?
{: #faq-oracle-aioservers-setting}

The smit fast path for aioservers is unavailable because the AIO server setting updates automatically when the kernel extension detects an aio sync operation request. View the `ioo` command pages for settings and examples. For details, see [Checking Asynchronous Input Output Processes AIX 64-Bit](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/axdbi/checking-asynchronous-input-output-processes.html#GUID-2F121FA1-0A7F-48BB-95F2-1661D8046BDC){: external}.


### How do I activate the iocp device when it is "Defined" during an Oracle install?
{: #faq-oracle-iocp-activation}

Use `smitty iocp`, navigate to Change/Show Characteristics of I/O Completion Ports, and set the state to Available at system restart. This ensures the device remains active after reboot.

If using the command line, run:
`mkdev -l iocp0` (activates the device until the next reboot)
`mkdev -l iocp0 -P` (ensures activation persists after reboot by updating the ODM)

### What should I do if the Oracle installer precheck lists failed items?
{: #faq-oracle-precheck-failed-items}

The Oracle RUNINSTALLER precheck ensures system requirements are met before installation. Common failures include:

- Insufficient `/tmp` space: Oracle 12.2 requires 5 GB of free space. Increase available space if needed.
- Insufficient paging space: Oracle requires 12 GB of paging space. You can:
   - Extend the `hd6` Paging Logical Volume.
   - Create a new paging space using `smit mklv`, setting the LV type to paging.

After making adjustments, rerun the precheck. If no errors appear, proceed with the installation.


### Where can I find central SAP Notes for installing NetWeaver on Oracle and MaxDB?
{: #faq-sap-note-netweaver-on-oracle}

Refer to the following SAP Notes for installation details:
- Oracle: [SAP Note 2172935 - installation - SAP Systems based on SAP NetWeaver: Oracle Database](https://me.sap.com/notes/2172935){: external} provides hardware, storage requirements, and required file systems.
- MaxDB: [SAP Note 2365014 - Installation of SAP Systems Based on SAP NetWeaver: SAP MaxDB](https://me.sap.com/notes/2365014){: external} outlines installation steps and key details for MaxDB users.

## SAP HANA
{: #faq-sap-hana}

### What database virtualization, sharing, or isolation options are supported in IBM Cloud infrastructure?
{: #faq-supported-db-options}

IBM Cloud SAP-certified infrastructure does not include database virtualization, sharing, or isolation options such as Multitenant Database Containers (MDC) testing. Customers must follow SAP guidance to:

- Properly configure SAP HANA features and functions
- Maintain infrastructure compliance with SAP certification

### Is scale-out supported for SAP HANA?
{: #faq-sap-hana-scale-out}

Yes. For OLTP (SAP S/4HANA) see [SAP S/4HANA - Scale-up/Scale-out](/docs/sap?topic=sap-s4hana#s4hana-hana-scaling), and for OLAP (BW/4HANA) see [SAP BW/4HANA - Scale-up/Scale-out](/docs/sap?topic=sap-bw4hana#bw4hana-hana-scaling).

### How do I back up my SAP HANA-certified servers?
{: #faq-sap-hana-backup}

Server backup is not included with Cloud infrastructure-as-a-service. Options include:

- Ordering the Cobalt Iron backup option during provisioning. See [Backup for AIX and Linux instances](/docs/power-iaas?topic=power-iaas-backup-strategies#baas) for details.
- Using the IBM backint agent to back up SAP HANA data to IBM Cloud Object Storage.


### Is there a best practice configuration check for SAP HANA?
{: #faq-sap-hana-best-practices}

Yes, see [SAP Note 2903141 - Best practice configuration checks for SAP HANA](https://me.sap.com/notes/2903141){: external}


### How can I check parameters in SAP HANA?
{: #faq-sap-hana-check-parameters}

See [SAP Note 2600030 - Parameter Recommendations in SAP HANA Environments](https://me.sap.com/notes/2600030){: external}


### How can I optimize my network for SAP HANA?
{: #faq-sap-hana-optimize-network}

The OS image provided by IBM is preconfigured for network optimization. Adjustments can be made as needed. For details, see:

- [SAP Note 2382421 - Optimizing the Network Configuration on HANA- and OS-Level](https://me.sap.com/notes/2382421){: external}
- [SAP Note 2477204 - FAQ: SAP HANA Services and Ports](https://me.sap.com/notes/2477204){: external}


### Where can I find information on SAP ports and services?
{: #faq-sap-hana-ports-and-services}

See [TCP/IP Ports of All SAP Products](https://help.sap.com/docs/Security/575a9f0e56f34c6e8138439eefc32b16/616a3c0b1cc748238de9c0341b15c63c.html){: external} for detailed explanations of SAP ports and services


### How can I optimize my SAP HANA performance?
{: #faq-sap-hana-optimize-performance}

See [SAP Note 2000000 - FAQ: SAP HANA Performance Optimization](https://me.sap.com/notes/2000000){: external}


### How can I use SAP HANA Mini Checks to monitor and highlight potential issues with my HANA implementation?
{: #faq-sap-hana-mini-checks}

See [SAP Note 1999993 - How-To: Interpreting SAP HANA Mini Check Results](https://me.sap.com/notes/1999993){: external}

### Why is SAP HANA using large SWAP memory?
{: #faq-sap-hana-why-large-swap}

See [SAP Note 2779331 - HANA services use large SWAP memory](https://me.sap.com/notes/2779331){: external}

## SAP NetWeaver
{: #faq-sap-nw-generic}

### Which versions of SAP NetWeaver are supported?
{: #faq-sap-nw-gversions}

SAP NetWeaver Application Server (ABAP or Java) versions 7.0 or later are supported across the IBM Cloud for SAP portfolio.

For a full updated list of SAP NetWeaver versions and SAP Kernel Patch Levels supported for the various IaaS options, see the following SAP Notes:
- [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}
- [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}
- [SAP Note 2855850 - SAP Applications on IBM {{site.data.keyword.powerSys_notm}}s](https://me.sap.com/notes/2855850){: external}

## List of SAP Notes for the {{site.data.keyword.ibm_cloud_sap}} portfolio
{: #faq-ibm-cloud-for-sap-portfolio-snotes}

### {{site.data.keyword.cloud_notm}} Classic Infrastructure
{: #faq-ibm-cloud-for-sap-portfolio-snotes-classic}

- [SAP Note 2414820 - SAP on IBM Cloud: Support prerequisites](https://me.sap.com/notes/2414820){: external}
- [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}
- [SAP Note 2279688 - SAP on IBM Cloud: Support for SAP BusinessObjects](https://me.sap.com/notes/2279688){: external}
- [SAP Note 2686169 - Prerequisites for installing SAP Data Hub 2](https://me.sap.com/notes/2686169){: external}

### {{site.data.keyword.cloud_notm}} VPC Infrastructure
{: #faq-ibm-cloud-for-sap-portfolio-snotes-vpc}

- [SAP Note 2414820 - SAP on IBM Cloud: Support prerequisites](https://me.sap.com/notes/2414820){: external}
- [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}


### IBM Power Infrastructure connected to {{site.data.keyword.cloud_notm}}
{: #faq-ibm-cloud-for-sap-portfolio-snotes-power}

- [SAP Note 2923984 - SAP on IBM {{site.data.keyword.powerSys_notm}}s: Support prerequisites](https://me.sap.com/notes/2923984){: external}
- [SAP Note 2947579 - SAP HANA on IBM {{site.data.keyword.powerSys_notm}}s](https://me.sap.com/notes/2947579){: external}
- [SAP Note 2855850 - SAP Applications on IBM {{site.data.keyword.powerSys_notm}}s](https://me.sap.com/notes/2855850){: external}
- [SAP Note 2932766 - SAP on IBM {{site.data.keyword.powerSys_notm}}s: Key Monitoring Metrics](https://me.sap.com/notes/2932766){: external}


### Public Cloud and virtualized environments
{: #faq-ibm-cloud-for-sap-portfolio-snotes-csp}

- [SAP Note 1380654 - SAP support in IaaS environments](https://me.sap.com/notes/1380654){: external}
- [SAP Note 1122387 - Linux: SAP Support in virtualized environments](https://me.sap.com/notes/1122387){: external}
- [SAP Note 1409608 - Virtualization on Windows](https://me.sap.com/notes/1409608){: external}
- [SAP Note 1409604 - Virtualization on Windows: Enhanced Monitoring](https://me.sap.com/notes/1409604){: external}
- [SAP Note 2134316 - Can SAP ASE run in a cloud environment?](https://me.sap.com/notes/2134316){: external}
- [SAP Note 2923773 - Linux on IBM Cloud (IaaS): Adaption of your SAP License](https://me.sap.com/notes/2923773){: external}


### {{site.data.keyword.cloud_notm}} generic SAP Notes
{: #faq-ibm-cloud-for-sap-portfolio-snotes-generic}

- [SAP Note 2588225 - SAP on IBM Cloud: Protect against speculative execution vulnerabilities](https://me.sap.com/notes/2588225){: external}
