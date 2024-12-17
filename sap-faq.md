---

copyright:
  years: 2020, 2024
lastupdated: "2024-12-17"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads

subcollection: sap

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:tip: .tip}
{:important: .important}
{:external: target="_blank" .external}
{:faq: data-hd-content-type='faq'}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# FAQ of {{site.data.keyword.ibm_cloud_sap}}
{: #faq-ibm-cloud-for-sap}

## Introduction
{: #faq-intro}

This FAQ provides answers for the following:
- [{{site.data.keyword.ibm_cloud_sap}} portfolio questions](#faq-ibm-cloud-for-sap-portfolio)
- [Licensing and pricing](#faq-licensing-pricing)
- [SAP-certified IBM Power Virtual Servers](#faq-sap-certified-power-vsi)
- [SAP HANA generic questions](#faq-sap-hana)
- [SAP NetWeaver generic questions](#faq-sap-nw-generic)
- [SAP Notes full list of the {{site.data.keyword.ibm_cloud_sap}} portfolio](#faq-ibm-cloud-for-sap-portfolio-snotes)

Due to their length on specific topics, there are separate FAQs for:
- [Moving SAP Workloads](/docs/sap?topic=sap-faq-moving-sap-workloads)

## {{site.data.keyword.ibm_cloud_sap}} portfolio questions
{: #faq-ibm-cloud-for-sap-portfolio}


### Can SAP be run on any {{site.data.keyword.cloud_notm}} server?
{: #faq-sap-run-on-any-server}

No, SAP HANA or SAP NetWeaver can run only on the server models that are SAP-certified. Using SAP-certified servers ensures that the SAP workloads function correctly, a critical consideration since these applications run entire businesses.

IBM and SAP are stringent in our certification checks. These checks are key to our 40+ year partnership that helps businesses to run and succeed with their SAP Business Applications.

For POC or evaluation (for example, non-productive) purposes, a non-certified server can be used to evaluate {{site.data.keyword.cloud_notm}} without incurring the higher costs of the SAP-certified servers. However, on a non-certified server, the SAP software might not work as designed and is not supported by SAP.

Production SAP systems **and** development and testing (dev/test) SAP systems are both considered productive systems by SAP Support. Dev/Test SAP systems are considered productive because they also require support from SAP if errors occur.
{: note}


### Can I download the SAP software installation media and distributions directly from {{site.data.keyword.cloud_notm}}?
{: #faq-download-sap-installation-media}

All SAP software installation media is available from SAP directly, at [sap.com](https://support.sap.com/en/my-support/software-downloads.html){: external}. The single distribution point ensures the governance of licensing, compliance, and export control.

Each customer needs to generate an SAP Software Download Center basket (optionally created through SAP Maintenance Planner) and download the software to their target server.

SAP ID credentials are required to download SAP software installation media.
{: note}


### Who is responsible for deployment of SAP software on {{site.data.keyword.cloud_notm}}?
{: #faq-who-responsible-for-deployment}

The {{site.data.keyword.ibm_cloud_sap}} portfolio is primarily Infrastructure-as-a-Service which is certified for SAP workloads. All IaaS is considered "customer-managed" by Cloud Service Providers. All changes to the OS and any applications that are deployed are the customer's responsibility (or the responsibility of a contracted Business Partner).

The customer (or contracted Business Partner) is responsible for the installation and configuration of SAP software on {{site.data.keyword.cloud_notm}}, which must follow SAP's guidance for the business scenario and usage.

While {{site.data.keyword.cloud_notm}} does not provide SAP application-level services, the various {{site.data.keyword.cloud_notm}} Business Partners (including {{site.data.keyword.IBM_notm}} Services) do provide their service capabilities by using {{site.data.keyword.ibm_cloud_sap}} portfolio.

This table shows a brief list of services and the Business Partner types who can provide the services for {{site.data.keyword.ibm_cloud_sap}}:

|Service type|Provided by|Description|
|-----|-----|-----|
|Consulting and advisory and implementation|SAP "Global Systems Integrator" (GSI) providers|These providers advise and run SAP implementation and deployment projects.|
|Application management|SAP "Application Management Services" (AMS) providers|These providers manage and maintain an existing deployment of SAP Applications (optional: incremental functional or development changes).|
|End-to-end managed services|SAP "Managed Services Providers" (MSP)|These providers run the SAP implementation and deployment and manage the Infrastructure, OS, SAP Technical Applications, and SAP Business Applications. These services often do not include incremental functional or development changes).|
{: caption="Services and Business Partner Types" caption-side="top"}

For example, some of the services that are available in partnership between {{site.data.keyword.cloud_notm}} and IBM Services include:
- [GSI and IBM Services: SAP Consulting & Implementation Services](https://www.ibm.com/consulting/sap){: external}
- [AMS and IBM Services: SAP Application Management and Development Solutions](https://www.ibm.com/blogs/services/2019/05/08/unlocking-the-value-of-sap-applications-in-the-digital-cloud/){: external}
- [MSP and IBM Services: Managed Applications for SAP](https://www.ibm.com/cloud/sap){: external}


### Do I need IBM Db2 to run SAP NetWeaver on {{site.data.keyword.cloud_notm}}?
{: #faq-need-db2}

Multiple SAP AnyDB options and SAP HANA are available for use with SAP NetWeaver certified infrastructure on {{site.data.keyword.cloud_notm}}. You are not required to use IBM Db2.

Check [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external} regularly and also reference the [SAP Product Availability Matrix](https://support.sap.com/en/release-upgrade-maintenance.html#section_1969201630){: external} for details.

For more information about IBM Db2, see [SAP Note 2927211](https://me.sap.com/notes/2927211){: external} and **SAP on IBM Db2 for Linux&reg;, UNIX, and Windows (LUW)** on the [SAP Community page](https://pages.community.sap.com/topics/db2-for-linux-unix-windows){: external}.

Pay close attention to the provided operating systems in the SAP Note because different sets of service packs are required by your database.
{: note}


### Why was {{site.data.keyword.Db2_on_Cloud_short}} chosen for certification for {{site.data.keyword.cloud_notm}}?
{: #faq-why-db2-for-certification}

IBM Db2 is an {{site.data.keyword.IBM_notm}} product that is seamlessly integrated into SAP. IBM Db2 is a mature database solution for SAP applications with many advantages, including: reduced total cost of ownership, excellent performance, and simplified administration. Over the past four decades, most of the benchmark tests by IBM Systems used IBM Db2, and we continue this tradition.


### Can I split my distributed SAP environment between different data centers?
{: #faq-split-distributed-sap-env-between-dcs}

For a distributed SAP installation, it is best to have all nodes in the same location (for example, Availability Zone or Datacenter) and networking constructs (for example, Subnet and VPC/Subnet and VLAN). Deviation from this setup can cause latency and timeouts, which render your SAP system unresponsive.


### Can I split my distributed SAP environment between {{site.data.keyword.cloud}} Bare Metal and {{site.data.keyword.cloud}} Virtual Servers?
{: #faq-split-distributed-sap-env-between-cloud-offerings}

RDBMs on Intel Bare Metal Servers in the older {{site.data.keyword.cloud_notm}} Classic Infrastructure environment that comply to [SAP Note 2414097](https://me.sap.com/notes/2414097){: external}, are supported when connected to the SAP AS on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) Infrastructure environment - when placed in the same location (i.e. Datacenter / Availability Zone) and using an {{site.data.keyword.cloud_notm}} Transit Gateway local routing.


### Can I achieve SAP high availability as defined by SAP architecture?
{: #faq-achieve-sap-ha}

High availability for SAP can be achieved for:
- SAP NetWeaver High Availability
- SAP HANA High Availability
- SAP AnyDB High Availability (for example, IBM Db2, MS SQL etc.)

You can set up high availability at either the:
- SAP Technical Application layer (for example, system replication, system clustering)
- Hardware layer (for example, storage replication)

How you set up high availability differs depending on the infrastructure types and network types:
- For Intel Bare Metal that uses Classic Infrastructure network
- For Intel Virtual Servers that use VPC Infrastructure network _(does not currently support SAP NetWeaver high availability or SAP HANA high availability)_
- For IBM Power Virtual Servers, on {{site.data.keyword.cloud_notm}} that use IBM Power Virtual Server Group network
- For VMware that uses Classic Infrastructure network

See the respective topics in the **_Get Started_** section for:
* [SAP HANA design considerations for High Availability and Disaster Recovery (HA/DR)](/docs/sap?topic=sap-hana-design-considerations#hana-ha)
* [SAP HANA backups - Storage impacts on Recovery Time Objective (RTO)](/docs/sap?topic=sap-storage-design-considerations#storage-performance-backup-rto)
* [SAP NetWeaver design considerations for High Availability configuration](/docs/sap?topic=sap-netweaver-design-considerations#netweaver-ha)


### How do I connect my SAP Systems running on {{site.data.keyword.cloud_notm}} to my on-premises systems?
{: #faq-connect-sap-to-on-premises}

You have several connectivity options to create secure connections between your on-premises systems and the {{site.data.keyword.cloud_notm}}. These options are your responsibility to configure, and {{site.data.keyword.cloud_notm}} operates the underlying services according to the configuration. For more information see [Connectivity to your SAP system landscape](/docs/sap?topic=sap-determine-access).


## Licensing and pricing
{: #faq-licensing-pricing}


### How does SAP licensing work with the {{site.data.keyword.ibm_cloud_sap}} infrastructure-as-a-service?
{: #faq-sap-licensing-for-sap-iaas}

The {{site.data.keyword.cloud}} SAP-certified infrastructure uses the Bring Your Own License (BYOL) model. This model follows the industry standard of IaaS for SAP workloads and is the same approach that is used for many decades with SAP software.

No {{site.data.keyword.ibm_cloud_sap}} portfolio offering contains any SAP licensing.

This arrangement applies to:
- SAP Business Application licenses (for example, SAP S/4HANA, SAP ECC)
- SAP Technical Application licenses (for example, SAP HANA, SAP NetWeaver)
- Any OEM license from SAP (for example, SAP AnyDB OEM such as MS SQL)


### How does Operating System (OS) licensing work with the {{site.data.keyword.ibm_cloud_sap}} Infrastructure-as-a-Service?
{: #faq-os-licensing-for-sap-iaas}

The cost of the OS license (and applicable subscription additions for SAP-specific OS Packages and Support), is included when you select your OS on the order form for your SAP-certified server.


### How are the SAP-certified {{site.data.keyword.cloud_notm}} servers priced?
{: #faq-sap-servers-pricing}

Prices are provided through the {{site.data.keyword.cloud_notm}} catalog when you begin the ordering process. Any discounts available from Reserved Instances pricing agreements or usage of Subscription Accounts are also calculated.

For SAP-certified bare metal servers that are certified as HANA appliances, pricing includes the high-performance local storage.

As an industry practice across Cloud Service Providers, virtual servers from {{site.data.keyword.ibm_cloud_sap}} use redundant network block or file storage. These storage costs might not be included in the initial price estimation. In addition, there are **no network bandwidth charges** for local network traffic to or from the virtual server host and the storage host on {{site.data.keyword.cloud_notm}}.
{: note}

## SAP-certified IBM Power Virtual Servers
{: #faq-sap-certified-power-vsi}

### Which version of Db2 can I use to run SAP NetWeaver on {{site.data.keyword.IBM_notm}} Power Virtual Servers?
{: #faq-which-db2-version-on-power}

For information check [SAP Note 2855850](https://me.sap.com/notes/2855850){: external} regularly. You can also reference [SAP Product Availability Matrix](https://support.sap.com/en/release-upgrade-maintenance.html#section_1969201630){: external}.


### What are the operating system requirements for Db2, AIX and SAP NetWeaver?
{: #faq-os-requirements-for-db2-and-netweaver}

For operating system requirements, see:

- [SAP Note 1780629 - AIX: Minimal OS Requirements for SAP Kernel](https://me.sap.com/notes/1780629){: external}
- [SAP Note 2267287 - Using SAP systems with AIX 7.2](https://me.sap.com/notes/2267287){: external}


### For Oracle installs, I have to activate `aioservers` but it's missing in AIX 7x. Where is this setting?
{: #faq-oracle-aioservers-setting}

The smit fast path is missing because the AIO server setting is automatically updated when the kernel extension notices that the aio sync operation is requested. View the main pages for `ioo` to see command settings and examples. For the required settings, see [Checking Asynchronous Input Output Processes AIX 64-Bit](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/axdbi/checking-asynchronous-input-output-processes.html#GUID-2F121FA1-0A7F-48BB-95F2-1661D8046BDC){: external}.


### With an Oracle install, how do I activate the iocp device when it's "Defined"?
{: #faq-oracle-iocp-activation}

Use the smit menu with fast path command `smitty iocp`. Select **Change/Show Characteristics of I/O Completion Ports** and set the state to be configured at system restart to *Available*. This setting ensures that the device is available even after a system restart.

If you use the `mkdev -l iocp0` command on the command line, the devíce is available only when the system is rebooted, and the device shows as **Defined** again. To ensure that this device is activated after a system restart, add the `-P` flag so the ODM is updated: `mkdev -l iocp0 -P`
{: note}


### When I run the Oracle installer precheck, failed items are listed. What should I do?
{: #faq-oracle-precheck-failed-items}

Part of the RUNINSTALLER function is to ensure that you complete the necessary pre-checks before you install the product. Two of the common errors are:

* You don't have enough free space in `/tmp`. Oracle 12.2 requires 5 GB of free space. If necessary, increase the amount of free space.
* You don't have 12 GB of paging space available. You can define paging multiple ways. You can extend the Paging Logical Volume `hd6` in the volume group. You can also create a new paging space on a different volume by using `smit mklv`. When you see the LV type, specify paging. This setting creates a new paging space that is called `paging00` on the hard disk you selected.

When you finish making adjustments and defined at least the minimum required amount of space, go back to the Oracle INSTALLER and select rerun checks. If you see no errors, you can proceed with the installation.


### Is there a central SAP Note for installing NetWeaver on Oracle?
{: #faq-sap-note-netweaver-on-oracle}

[SAP Note 2172935 - Installation - SAP Systems based on SAP NetWeaver: Oracle Database](https://me.sap.com/notes/2172935){: external} provides hardware and storage requirements for Oracle and details the required file systems and mount points.


### Is there a central SAP Note for using MaxDB for NetWeaver installations?
{: #faq-sap-note-maxdb-on-netweaver}

[SAP Note 2365014 - Installation of SAP Systems Based on SAP NetWeaver: SAP MaxDB](https://me.sap.com/notes/2365014){: external} outlines the installation and provides other useful information for MaxDB users.

## SAP HANA generic questions
{: #faq-sap-hana}

### What hardware database virtualization/sharing/isolation options are supported in the {{site.data.keyword.cloud_notm}} infrastructure?
{: #faq-supported-db-options}

{{site.data.keyword.cloud_notm}} SAP-certified infrastructure offerings are certified for SAP and do not include database virtualization/sharing/isolation options such as Multitenant Database Containers (MDC) testing. The customer is responsible for following SAP guidance to:
* Correctly set up the SAP HANA features and functions
* Ensure that the infrastructure remains compliant with the SAP certification

### Is scale-out supported for SAP HANA?
{: #faq-sap-hana-scale-out}

Yes. For OLTP (i.e. SAP S/4HANA) see [SAP S/4HANA - Scale-up/Scale-out](/docs/sap?topic=sap-s4hana#s4hana-hana-scaling), and for OLAP (i.e. BW/4HANA) see [SAP BW/4HANA - Scale-up/Scale-out](/docs/sap?topic=sap-bw4hana#bw4hana-hana-scaling).Alternatively view the latest top specific available in the summary table on [FAQ of Profile List with Benchmarks and Specifications](/docs/sap?topic=sap-faq-profile-specs#faq-profile-specs-maximums).

### How do I back up my SAP HANA-certified servers?
{: #faq-sap-hana-backup}

Server backup isn't included with Cloud Infrastructure-as-a-Service (IaaS).

You can add backup options during the ordering process from the {{site.data.keyword.cloud_notm}} catalog, or install your own backup solution onto regular {{site.data.keyword.cloud_notm}} IaaS options.


### Is there a best practice configuration check for SAP HANA?
{: #faq-sap-hana-best-practises}

Yes, see [SAP Note 2903141 - Best practice configuration checks for SAP HANA](https://me.sap.com/notes/2903141){: external}


### How can I check parameters in SAP HANA?
{: #faq-sap-hana-check-parameters}

See [SAP Note 2600030 - Parameter Recommendations in SAP HANA Environments](https://me.sap.com/notes/2600030){: external}


### How can I optimize my network for SAP HANA?
{: #faq-sap-hana-optimize-network}

The OS image that {{site.data.keyword.IBM}} provides has the parameters set to optimize network performance. You can adjust them if necessary. For more information, see:

* [SAP Note 2382421 - Optimizing the Network Configuration on HANA- and OS-Level](https://me.sap.com/notes/2382421){: external}

* [SAP Note 2477204 - FAQ: SAP HANA Services and Ports](https://me.sap.com/notes/2477204){: external}


### Is there information on the ports and services that are used by SAP?
{: #faq-sap-hana-ports-and-services}

See [TCP/IP Ports of All SAP Products](https://help.sap.com/docs/Security/575a9f0e56f34c6e8138439eefc32b16/616a3c0b1cc748238de9c0341b15c63c.html){: external} for detailed explanations of SAP ports and services


### How can I optimize my SAP HANA performance?
{: #faq-sap-hana-optimize-performance}

See [SAP Note 2000000 - FAQ: SAP HANA Performance Optimization](https://me.sap.com/notes/2000000){: external}


### How can I use SAP HANA Mini Checks to monitor and highlight potential issues with my HANA implementation?
{: #faq-sap-hana-mini-checks}

See [SAP Note 1999993 - How-To: Interpreting SAP HANA Mini Check Results](https://me.sap.com/notes/1999993){: external}

### I see that HANA is using large SWAP memory. What is going on?
{: #faq-sap-hana-why-large-swap}

See [SAP Note 2779331 - HANA services use large SWAP memory](https://me.sap.com/notes/2779331){: external}

## SAP NetWeaver generic questions
{: #faq-sap-nw-generic}

### Which versions of SAP NetWeaver are supported?
{: #faq-sap-nw-gversions}

Most versions of SAP NetWeaver Application Server (ABAP or Java) from 7.0 or higher are supported across the {{site.data.keyword.ibm_cloud_sap}} portfolio.

For a full updated list of SAP NetWeaver versions and SAP Kernel Patch Levels supported for the various IaaS options, see the following SAP Notes:
- [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}
- [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}
- [SAP Note 2855850 - SAP Applications on IBM Power Virtual Servers](https://me.sap.com/notes/2855850){: external}

## Full list of SAP Notes for the {{site.data.keyword.ibm_cloud_sap}} portfolio
{: #faq-ibm-cloud-for-sap-portfolio-snotes}

### SAP Notes that are related to {{site.data.keyword.cloud_notm}} Classic Infrastructure
{: #faq-ibm-cloud-for-sap-portfolio-snotes-classic}

- [SAP Note 2414820 - SAP on IBM Cloud: Support prerequisites](https://me.sap.com/notes/2414820){: external}
- [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}
- [SAP Note 2279688 - SAP on IBM Cloud: Support for SAP BusinessObjects](https://me.sap.com/notes/2279688){: external}
- [SAP Note 2686169 - Prerequisites for installing SAP Data Hub 2](https://me.sap.com/notes/2686169){: external}

### SAP Notes that are related to {{site.data.keyword.cloud_notm}} VPC Infrastructure
{: #faq-ibm-cloud-for-sap-portfolio-snotes-vpc}

- [SAP Note 2414820 - SAP on IBM Cloud: Support prerequisites](https://me.sap.com/notes/2414820){: external}
- [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}


### SAP Notes that are related to IBM Power Infrastructure connected to {{site.data.keyword.cloud_notm}}
{: #faq-ibm-cloud-for-sap-portfolio-snotes-power}

- [SAP Note 2923984 - SAP on IBM Power Virtual Servers: Support prerequisites](https://me.sap.com/notes/2923984){: external}
- [SAP Note 2947579 - SAP HANA on IBM Power Virtual Servers](https://me.sap.com/notes/2947579){: external}
- [SAP Note 2855850 - SAP Applications on IBM Power Virtual Servers](https://me.sap.com/notes/2855850){: external}
- [SAP Note 2932766 - SAP on IBM Power Virtual Servers: Key Monitoring Metrics](https://me.sap.com/notes/2932766){: external}


### SAP Notes that are related to any Public Cloud or Virtualized Environments
{: #faq-ibm-cloud-for-sap-portfolio-snotes-csp}

- [SAP Note 1380654 - SAP support in IaaS environments](https://me.sap.com/notes/1380654){: external}
- [SAP Note 1122387 - Linux: SAP Support in virtualized environments](https://me.sap.com/notes/1122387){: external}
- [SAP Note 1409608 - Virtualization on Windows](https://me.sap.com/notes/1409608){: external}
- [SAP Note 1409604 - Virtualization on Windows: Enhanced Monitoring](https://me.sap.com/notes/1409604){: external}
- [SAP Note 2134316 - Can SAP ASE run in a cloud environment?](https://me.sap.com/notes/2134316){: external}
- [SAP Note 2923773 - Linux on IBM Cloud (IaaS): Adaption of your SAP License](https://me.sap.com/notes/2923773){: external}


### SAP Notes generic for {{site.data.keyword.cloud_notm}}
{: #faq-ibm-cloud-for-sap-portfolio-snotes-generic}

- [SAP Note 2588225 - SAP on IBM Cloud: Protect against speculative execution vulnerabilities](https://me.sap.com/notes/2588225){: external}
