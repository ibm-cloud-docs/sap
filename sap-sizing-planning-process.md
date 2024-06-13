---

copyright:
  years: 2020
lastupdated: "2020-09-21"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP NetWeaver, SAP HANA, SAP Application Performance Standard, SAPS, SAP Quick Sizer

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

# Sizing process for SAP Systems
{: #sizing}

After you decide which SAP solutions you want to use, you need to determine the number of hosts that are required to support your SAP landscape and make sure that the host servers are correctly sized.
{: shortdesc}

SAP sizing is a detailed activity (and often a project in itself) to map business requirements into Infrastructure/Hardware requirements.

## Understanding SAP sizing
{: #sizing-methodology}

The SAP sizing methodology for SAP HANA or SAP NetWeaver technical applications is based on SAP benchmarks, such as information from SAP and actual customer experiences.

The base SAP workload unit is an SAP Application Performance Standard (SAPS).

The SAPS is a definition of throughput that is coined by SAP capacity planning and performance testing personnel. For example, 100 SAPs is defined as 2,000 fully business processed order-line items per hour in the standard SAP Sales and Distribution (SAP SD) application benchmark. This example is equivalent to 2,400 SAP SD transactions per hour with the SAP Enterprise Resource Planning (SAP ERP) solution.

The benchmarking test determines and rates the infrastructure processing power based on its capability to run and process transactions at close to 100% CPU load with a response time of less than 1 second.

The capability of processors is measured during the standard (SAP SD) benchmark test that is certified by SAP. For more information about the benchmark test that is certified by SAP, see [SAP Standard Application Benchmarks](https://www.sap.com/about/benchmark/measuring.html){: external} and [Practical Guidelines and Techniques for Sizing your SAP Landscape for Optimal Performance and Scalability](https://www.sap.com/documents/2016/10/c2206376-8f7c-0010-82c7-eda71af511fa.html){: external}.

**SAP sizing is primarily based on:**
- Business throughput (Throughput based sizing); includes database table size increase from new objects, CPU time to complete processing transactions or batch job runs within a time limit (decided by the business)
- Business concurrent users (User based sizing); includes usage patterns and categorization of user’s based upon their daily transactions
- Business need for High Availability and Disaster Recovery
- Business need for multiple SAP applications and add-ons, which are integrated into one landscape

**SAP sizing is used to determine:**
- Support for design of the Application Server structure and configurations:
    - SAP Systems
    - SAP Tiering/Tracks
    - SAP Instances
    - SAP Clients
- Support for design of the Database Server structure and configurations:
    - System Type e.g. Distributed
    - Deployment Type e.g. MDC
    - Processing Type e.g. OLAP
    - Sizing Type and Deployment Method e.g. Expert Sizing with TDIv5/6
    - High Availability and Disaster Recovery
- Processor and Memory requirements
- Storage requirements
- Network requirements and topology
- Backup strategy

**SAP sizing inaccuracies can cause risks to SAP implementation projects:**

|**Project risk from inaccurate SAP Sizing**|**Mitigations of SAP Sizing inaccuracy**|
|----|----|
| Incomplete/Insufficient input data | - Information discovery requires support from business departments and the IT organization  /n - Communication issues are a common cause of invalid data therefore assignment of a project manager to run workshops is recommended  /n - Any unknown data will become an assumption
| Assumptions are not verified |   /n - Assumptions must be documented and need a verification process included in the project plan
| Custom code and special data structures |   /n - Hard to predict scenarios, that are solved by custom code and special data structures, require a verification process to determine whether there is an impact to infrastructure (commonly processing and storage)
{: caption="Table 1. SAP Sizing inaccuracies can cause risks to SAP implementation projects" caption-side="top"}

## SAP logical structure and SAP Sizing impacts for infrastructure
{: #sizing-infrastructure-impacts}

There are multiple ways in which the SAP logical structure and SAP sizing activities can impact infrastructure-related requirements, three key ways are highlighted below:

**1. Business Requirements**

Business requirements, such as 99.99% uptime or 3 downtime windows per year, effect logical design and structure of the SAP Landscape. The logical structure is made up of:
- SAP Systems
- SAP Tiering
- SAP Tracks
- SAP Instances
- SAP Clients

**2. SAP sizing results**

Each decision in the logical structure is reflected by the SAP Sizing project/exercise outcome:
- SAPS benchmark threshold required
- Size of database (memory and disk storage)
- etc.

**3. Infrastructure requirements**

SAP sizing results will heavily impact Infrastructure needs:
- Number of hosts required
- Performance/Size of hosts required
- Storage capacity for hosts
- Networking performance and isolation between hosts
- etc.

## Migrating an existing SAP System
{: #sizing-migrating}

If you plan to migrate an existing SAP system (from any source) to your {{site.data.keyword.cloud_notm}} environment, you can determine the SAPS numbers from the SAPS numbers of your current environment.

Use the information about your current workload (the CPUs and RAM used) and get the SAPS equivalents for your CPU from the [SAP SD Benchmarks results](https://www.sap.com/about/benchmark.html){: external} of your existing hardware.

## Using the SAP Quick Sizer
{: #sizing-quicksizer}

The [SAP Quick Sizer](https://service.sap.com/quicksizer){: external} is a web-based tool that is provided by SAP; it is available to all customers and business partners of SAP. Sizing information is input directly into the tool. The tool sizes SAP HANA or SAP NetWeaver servers.

You need an SAP S-user ID to use the Quick Sizer.
{: note}

The Quick Sizer calculates the workload (in a measurement unit called SAPS) and adjusts the workload to allow for suitable processor use. So, if a workload of 4,800 SAP SD benchmark transactions per hour is required, the tool calculates this workload as 200 SAPS. For example, an IT department determined that to avoid system overloads during high-usage periods, a target processor load of 33% is allowed. A processor that can provide 200 SAPS at 33% load means that the processor is capable of 600 SAPS at 100% load. Therefore, a system capable of 600 SAPS becomes the benchmark to which any new infrastructure must adhere.

While the sizing method might be considered conservative, consider that all SAPS calculations for your servers are based on highly tuned SAP systems that run only specific SAP SD workloads. Depending on the type of SAP application, any custom configuration or custom coding in your system, your results can vary. Also, requirements for your project, such as proof-of-concept (PoC) or regarding performance and response time, might be different.

After you determine your SAP applications and the SAPS numbers are calculated through the SAP Quick Sizer, or based on your current landscape, you can choose from the {{site.data.keyword.ibm_cloud_sap}} portfolio and the various infrastructure sizes available (as profiles or customizable/flexible in some cases).

### Assessment of SAP HANA with the SAP Quick Sizer
{: #sizing-quicksizer-hana}

SAP HANA is supported in production on single-node and multi-node SAP HANA-certified servers. The SAP HANA database uses column storage for some tables and fields, reducing the storage below row storage in traditional Relation Database Management System (RDMS); data can be highly compressed and compression ratios can range from 3:1 to over 10:1 based on the source data and database.

Sizing SAP HANA correctly is key to the success of your project. It is a best practive to complete the sizing before you order any SAP HANA certified server from {{site.data.keyword.ibm_cloud_sap}}. Improperly sized memory or storage requirements can lead to an upgrade and migration to a larger server.

Main memory is one of the most important resources to consider when you size an SAP HANA-certified appliance.

The [*SAP HANA Master Guide*](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/latest/en-US){: external} provides a starting point for sizing-related topics.

The [*Sizing SAP HANA - SAP HANA Master Guide*](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/latest/en-US/d4a122a7bb57101493e3f5ca08e6b039.html){: external} information within the guide provides guidance on how to size your SAP HANA system. It points to the different installation and migration scenarios for both greenfield installations and existing systems.

This information includes a link to the SAP HANA version of the SAP Quick Sizer tool (an SAP S-user ID is required to access the tool). The page also lists the SAP Notes that are related to sizing your SAP HANA server.

## For more information on SAP Sizing
{: #sizing-moreinfo}

For more information about sizing, see the following resources:

* [Sizing SAP HANA - SAP HANA Master Guide](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/latest/en-US/d4a122a7bb57101493e3f5ca08e6b039.html){: external}
* [SAP Quick Sizer](http://service.sap.com/quicksizer){: external}
* [SAP Note 1736976 - Sizing Report for BW-on-HANA](https://service.sap.com/sap/support/notes/1736976){: external}
* [SAP Note 1872170 - Suite on HANA memory sizes](https://service.sap.com/sap/support/notes/1872170){: external}
* [SAP Note 1793345 - Sizing for SAP Suite on HANA](http://service.sap.com/sap/support/notes/1793345){: external}
* [SAP Note 1514966 - SAP HANA: Sizing SAP HANA](https://service.sap.com/sap/support/notes/1514966){: external}
* [SAP Certified and Supported SAP HANA Hardware](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN){: external}
* [SAP Note 2055470 - SAP HANA on Power Planning and Installation Specifics - Central Note](https://service.sap.com/sap/support/notes/2055470){: external}
* [SAP HANA TDI - Storage Requirements](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html){: external}
* [SAP Certified Enterprise Storage Hardware for SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=storage){: external}

## SAP applications effect on sizing
{: #sizing-applications-effect}

Primarily, when sizing SAP applications there are design considerations related to the Infrastructure, OS, Database Server and Application Server. These are covered in this topic group "Sizing and Planning SAP Workloads":
- Networking design considerations
- Storage design considerations
- Compute and OS design considerations
- SAP HANA Database design considerations
- SAP NetWeaver design considerations

However, for each SAP Business Application and SAP Technical Application there are different implementation design considerations - which will change for each business and the scenario.

Beyond the topic group "Sizing and Planning SAP Workloads", there are individual topic groups with additional information and considerations for your SAP implementation:

- **SAP Business Applications**
    - [SAP S/4HANA](/docs/sap?topic=sap-s4hana)
    - [SAP BW/4HANA](/docs/sap?topic=sap-bw4hana)
    - [SAP Commerce](/docs/sap?topic=sap-commerce)
    - [SAP Business One (B1)](/docs/sap?topic=sap-b1)
    - [SAP Data Hub 2.x](/docs/sap?topic=sap-data-hub)

- **SAP Technical Applications**
    - [SAP Content Server](/docs/sap?topic=sap-content-server)

- **SAP AnyDB databases**
    - [AnyDB - IBM Db2](/docs/sap?topic=sap-anydb-ibm-db2)
    - [AnyDB - SAP MaxDB](/docs/sap?topic=sap-anydb-sap-maxdb)
    - [AnyDB - SAP ASE](/docs/sap?topic=sap-anydb-sap-ase)
    - [AnyDB - SAP IQ](/docs/sap?topic=sap-anydb-sap-iq)

