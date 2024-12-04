---

copyright:
  years: 2020, 2024
lastupdated: "2024-12-04"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP Landscape

subcollection: sap

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:note: .note}
{:table: .aria-labeledby="caption"}
{:faq: .faq}

# Define your SAP system landscape
{: #planning-your-system-landscape}

Your business and functional requirements determine the SAP solutions that are powered by the SAP HANA Database Server or SAP NetWeaver Application Server, and therefore determine how your applications are run the infrastructure available.

Your requirements have an influence on how you size your server. You have a wide selection of SAP NetWeaver-based applications (which may use SAP HANA) to choose from, including SAP S/4HANA, SAP ERP Central Component (ECC), SAP BW/4HANA, SAP BW and many more solutions for different business operations.

For a complete list of solutions, see [ABAP Platform and SAP NetWeaver](https://help.sap.com/docs/SAP_NETWEAVER){: external}. For information about supported operating systems and database platforms, see [SAP Product Availability Matrix (PAM)](https://userapps.support.sap.com/sap/support/pam){: external}. Search for Product Availability Matrix. An [SAP S-User ID](/docs/sap?topic=sap-necessary-credentials) is required.

## Determining your SAP applications
{: #determine-apps}

An SAP landscape is a group of two or more SAP systems that usually include development, quality and test, and production.

One SAP system consists of one or more *SAP instances*, which are a group of processes that are started and stopped at the same time. These *SAP instances* are grouped to form a specified SAP system for a defined use for a region or business unit. Then, the instances are grouped in a landscape as development, test, or production SAP systems with one or multiple tracks (such as "project" and "business"). This landscape design is up to each business, dependent on business requirements.

Landscapes have several possible configurations, such as server (CPU, RAM) size and storage size, for all SAP solutions in the market. These solutions include SAP NetWeaver-based products. SAP NetWeaver-based products range from older solutions, such as SAP ECC and SAP BW (that use "AnyDB" vendors that are approved by SAP), to the new range of SAP solutions, such as SAP S/4HANA and SAP BW/4HANA (that use SAP HANA database). Beyond the enterprise resource planning (ERP) and enterprise data warehouse (EDW) examples, there are many available SAP products or add-ons for different industries and business types or operating geographies.

SAP NetWeaver-based products are designed to run on SAP NetWeaver-certified hosts, and SAP HANA-based products are designed to run on SAP HANA-certified hosts. The certified operating systems and supported database systems for {{site.data.keyword.cloud_notm}} are listed in [SAP Note 2927211](https://me.sap.com/notes/2927211){: external}.

More solutions are available from SAP that are not SAP NetWeaver-based and many third-party software options that might integrate with SAP can affect the planning of your system landscape. For example, SAP HANA can run as a database for an SAP NetWeaver stack-based solution or as a stand-alone entity depending on your usage scenario.

Contact [SAP Support](https://support.sap.com/en/index.html){: external} if you plan to deploy and integrate non-SAP NetWeaver-based or third-party software into your SAP landscape on {{site.data.keyword.cloud_notm}}.
{: note}

You want to be as detailed as possible when you determine the size of your server based on the applications that you plan to run, potential growth, and performance. Additionally, keep in mind your storage and memory requirements for your applications. SAP systems in a landscape have specific requirements for connectivity, either among each other or to external systems.

**Questions during your determination of the SAP landscape:**
* How do you intend to use the applications? Is the intended use for development and testing, or production?
* How do you intend to connect your SAP workloads in Cloud to your existing network and systems?
* How will the database be used? Transactional (OLTP) or Analytical (OLAP)? Serving only the SAP Business Applications, or as part of your wider IT strategy extracting value from the advanced SAP HANA Components, which are available (such as predictive analytics or Cloud Foundry via XSA)
* How do you intend to deploy the applications and databases? And to what level of resiliency (that is, HA/DR strategy)?

If you plan to migrate an on-premises SAP installation into the {{site.data.keyword.IBM_notm}} Power Virtual Servers environment, make sure that you don't carry over existing performance issues. Run an up-to-date sizing report, and review a recent SAP Early Watch report of your SAP system. For more information, see [SAP EarlyWatch Alert](https://support.sap.com/en/offerings-programs/support-services/earlywatch-alert.html){: external} and [SAP Note 207223 - SAP EarlyWatch Alert Processed at SAP](https://me.sap.com/notes/207223){: external}.

Each deployment of SAP HANA Database Server or SAP NetWeaver Application Server will have items to consider. These are included under each relevant section of this documentation.

For further information regarding SAP Landscapes, guidance has been released by {{site.data.keyword.IBM_notm}} Power Systems, which provide excellent detailed agnostic information and guidance regarding SAP Landscapes components and setup (which applies to running SAP on any infrastructure, on-premises data centers or Cloud IaaS):
* [SAP on {{site.data.keyword.IBM_notm}} Power Systems Best Practices Guide](https://www-03.ibm.com/support/techdocs/atsmastr.nsf/WebIndex/WP102618){: external} (click the link in the middle of the page)
* [{{site.data.keyword.IBM_notm}} Power Systems Virtualization Operation Management for SAP Applications](http://www.redbooks.ibm.com/abstracts/redp5579.html?Open){: external}
* [SAP HANA on {{site.data.keyword.IBM_notm}} Power Systems and IBM System Storage - Guides](http://www-03.ibm.com/support/techdocs/atsmastr.nsf/WebIndex/WP102502){: external}

## Reviewing any relevant SAP and {{site.data.keyword.cloud_notm}} documentation
{: #review-documentation}

Review the following documentation to help you determine any prerequisites for the SAP products that you plan to install.

If your organization is new to {{site.data.keyword.cloud_notm}}, read the following SAP documentation to help with your planning phase and implementation:

* [SAP workloads on {{site.data.keyword.cloud}}](https://www.ibm.com/cloud/sap/certified-infrastructure){: external}
* [Get started with {{site.data.keyword.cloud}}](https://www.ibm.com/cloud/get-started){: external}
* [Creating an {{site.data.keyword.cloud}} account](/docs/account?topic=account-account-getting-started)
* [How to create an SAP S-user ID](https://www.youtube.com/watch?v=4wICiRTP8u0){: external} Note that only super administrators or S-users with the required authorization are allowed to create S-user IDs for your company's SAP Customer Number (SCN)
* The [Guide Finder for SAP NetWeaver and ABAP Platform](https://help.sap.com/docs/SAP_NETWEAVER/9e41ead9f54e44c1ae1a1094b0f80712/576f5c1808de4d1abecbd6e503c9ba42.html?language=en-US){: external} to search for SAP NetWeaver-related documentation, including installation guides.
* Applicable [installation guides](https://support.sap.com/en/my-support/software-downloads.html){: external}; requires an SAP S-user ID.
* SAP release notes, which can be found in the application help of the relevant SAP product documentation on the [SAP Help Portal](https://help.sap.com/docs){: external}; requires an SAP S-user ID.
* [SAP HANA Help](https://help.sap.com/docs/SAP_HANA_PLATFORM){: external}
* [SAP NetWeaver Help](https://help.sap.com/docs/SAP_NETWEAVER){: external}
* [SAP HANA Master Guide](https://help.sap.com/docs/SAP_HANA_PLATFORM/eb3777d5495d46c5b2fa773206bbfb46/326373b251c14f30b6e7f2f5efd3f6e3.html){: external}
* [SAP Product Availability Matrix (PAM)](https://support.sap.com/en/release-upgrade-maintenance.html#section_1969201630){: external}; requires an SAP S-user ID.
* [SAP Notes](https://support.sap.com/en/my-support/knowledge-base.html){: external}; requires an SAP S-user ID.
* Third-party documentation

## Selecting your SAP-certified infrastructure
{: #selecting-iaas}

This expands on the introduction [Comparing the different SAP-certified IaaS offerings](/docs/sap?topic=sap-overview-sap-offerings-overview#iaas-offerings-compare-summary), which summarises the benefits of each different Infrastructure option.

You are ready to define the number of host servers and size of those hosts after:
- the business has defined their requirements
- decided which SAP applications to use
- read the SAP installation documentation
- understood the various design considerations

Often your first filter of the infrastructure options is minimum SAPS, which has been calculated using the SAP Quick Sizer, and this will primarily define the CPU performance requirements. For the official certification documents, see the [SAP Standard Application Benchmarks](https://www.sap.com/about/benchmark.html){: external} which helps to confirm the IaaS you are choosing from {{site.data.keyword.cloud_notm}} is the correct IaaS for your needs.

Full details of the Profiles available for each Infrastructure option available through {{site.data.keyword.cloud_notm}}, were provided in the previous topic group, which lists all the IaaS Offerings available for either SAP HANA or SAP NetWeaver (and SAP AnyDB):

- SAP HANA profiles
    - [Intel Bare Metal server certified profiles for SAP HANA](/docs/sap/?topic=sap-hana-iaas-offerings-profiles-intel-bm)
    - [Intel Virtual Server certified profiles for SAP HANA](/docs/sap/?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc)
    - [IBM Power Virtual Server certified profiles for SAP HANA](/docs/sap/?topic=sap-hana-iaas-offerings-profiles-power-vs)
    - [VMware SDDC certified profiles for SAP HANA](/docs/sap/?topic=sap-hana-iaas-offerings-profiles-vmware)
- SAP NetWeaver profiles
    - [Intel Bare Metal server certified profiles for SAP NetWeaver](/docs/sap/?topic=sap-nw-iaas-offerings-profiles-intel-bm)
    - [Intel Virtual Server certified profiles for SAP NetWeaver](/docs/sap/?topic=sap-nw-iaas-offerings-profiles-intel-vs-vpc)
    - [IBM Power Virtual Server certified profiles for SAP NetWeaver](/docs/sap/?topic=sap-nw-iaas-offerings-profiles-power-vs)
    - [VMware SDDC certified profiles for SAP NetWeaver](/docs/sap/?topic=sap-nw-iaas-offerings-profiles-vmware)

### Distributing your SAP Landscape on IBM Cloud Bare Metal and Virtual Servers
{: #distributing-the-landscape}

Generally, the entire infrastructure for the operation of all closely coupled runtime components of an SAP software solution must be installed on either Intel Virtual Servers (Gen2) or on Bare Metal Servers from IBM Cloud.

To assist customers looking to combine performance for the database and flexibility for the application solution/s, testing has been performed when combining environments and networks.

Intel Bare Metal Servers from IBM Cloud in the IBM Cloud Classic Infrastructure environment may offer greater performance capabilities. Notably, this includes larger memory, local SSD storage in RAID arrays, access to IPMI, and more. Intel Virtual Servers (Gen2), on the other hand provide more flexibility.

RDBMs on Intel Bare Metal Servers in the older IBM Cloud Classic Infrastructure environment that comply to [SAP Note 2414097](https://me.sap.com/notes/2414097){: external}, are supported when connected to the SAP AS on Intel Virtual Server (Gen2) in the IBM Cloud VPC Infrastructure environment - when placed in the same location (that is, Datacenter / Availability Zone) and using an IBM Cloud Transit Gateway local routing.

### Mapping CPUs derived from SAPS to an IBM Power Virtual Server
{: #selecting-iaas-power}

This is a complementary offering from {{site.data.keyword.IBM_notm}} Power Systems, with low latency access to {{site.data.keyword.cloud_notm}} services
{: note}

When you create a {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} by using the {{site.data.keyword.cloud_notm}} console:
- For SAP NetWeaver, you select the number of CPUs of your server
- For SAP HANA, you select an instance profile with a predefined number of CPUs and memory size that suits your workload

While your data must fit into the instance memory with space for data growth defined by the business and SAP Sizing process, choosing an instance profile with more CPUs improves performance.

To find SAP certified profiles for Cloud IaaS, see [SAP Certified and Supported SAP HANA Hardware Directory - Certified IaaS Platforms - {{site.data.keyword.cloud_notm}}](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:28){: external}; this includes {{site.data.keyword.IBM_notm}} Power Virtual Servers, which can be found by using filter "CPU Architecture" and selecting IBM Power9.

To find SAP certified {{site.data.keyword.IBM_notm}} Power Systems Hardware, see [SAP Certified and Supported SAP HANA Hardware Directory - {{site.data.keyword.IBM_notm}} Power Systems](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=power){: external}.

For more information, see [Creating a {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}](/docs/power-iaas?topic=power-iaas-creating-power-virtual-server). For information about the pricing difference between CPU types, see [Pricing for {{site.data.keyword.IBM_notm}} Power Systems Virtual Servers on {{site.data.keyword.cloud}}](/docs/power-iaas?topic=power-iaas-pricing-virtual-server). For a description of the technical differences between dedicated, shared capped, and shared uncapped CPUs, see [this FAQ](/docs/power-iaas?topic=power-iaas-power-iaas-faqs#processor).

## Monitoring your system with SAP tools
{: #monitoring}

SAP system monitoring is available through the [SAP Host Agent](https://help.sap.com/docs/SAP_NETWEAVER_750/3ce0859db2164fe19541dda577d29020/48c6f9627a004da5e10000000a421937.html){: external}, which provides monitoring functions that are similar to on-premises installations.

### Monitoring for IBM Cloud Intel Virtual Servers (Gen2), on VPC Infrastructure
{: #monitoring-intel-vs-gen2}

The operating system metrics that the SAP Host Agent provides require the use of [{{site.data.keyword.cloud_notm}} Metrics Collector for SAP](/docs/sap?topic=sap-ibm-metrics-collector-for-sap-linux) and the correct SAP Host Agent patch level.

### Monitoring for {{site.data.keyword.IBM_notm}} Power Virtual Servers
{: #monitoring-power}

For Infrastructure as a Service (IaaS) environments such as {{site.data.keyword.IBM_notm}} Power Virtual Servers, the operating system metrics that the SAP Host Agent provides were enhanced. Make sure that you have the prerequisite SAP Host Agent patch level installed. For a description of the new metrics and required SAP Host Agent patch level, see [SAP Note 2932766 - SAP on IBM Power Virtual Servers: Key Monitoring Metrics](https://me.sap.com/notes/2932766){: external}.

## Support from IBM Cloud or SAP
{: #support}

For full information regarding support from IBM Cloud or SAP, please read [Getting help and support from IBM Cloud or SAP](/docs/sap?topic=sap-help-support).
