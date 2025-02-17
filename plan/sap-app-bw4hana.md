---
copyright:
  years: 2020, 2025
lastupdated: "2025-02-17"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# SAP BW/4HANA
{: #bw4hana}

SAP BW/4HANA is the leading enterprise data warehouse (EDW) software, which is designed for analyzing mass amounts of structured and unstructured data from multiple sources. EDW software deciphers business data into tangible and actionable insights that are used for reporting business performance against metrics and identifying opportunities or gaps in existing business practices.
{: shortdesc}

SAP BW/4HANA is a major release of EDW software from SAP designed to use the analytical capabilities of the SAP HANA database exclusively. Previous major releases of EDW from SAP were known as SAP BW, which might use various relational database vendors. SAP BW/4HANA acts as the insights engine for large enterprises with upgraded UX, data integration, and technologies upgrades to enable real-time decision making and digital business processes.

For more information, see [SAP BW/4HANA](https://www.sap.com/products/technology-platform/bw4hana-data-warehousing.html){: external}.

{{site.data.keyword.ibm_cloud_sap}} infrastructure options are certified to SAP NetWeaver application server and SAP HANA database server, which run the SAP BW/4HANA business application.

## Preface: variants of SAP BW/4HANA and other analytics solutions from SAP
{: #bw4hana-variants}

For analytics, there are multiple interlocking solutions from SAP and variants for each solution. The primary SAP analytics solutions are:
- SAP BW/4HANA, the EDW software using SAP HANA Platform with runtime database license
- SAP Data Warehouse Cloud (SaaS), the EDW software available as-a-Service using SAP HANA Cloud Services
- SAP HANA Platform Enterprise Edition, the database server and analytical components when leveraging the full-use license and can be used to construct custom/native EDW.
- _The analytical SAP HANA components include Self Service Analytics Library (SAL), Smart Data Access (SDA), Smart Data Integration (SDI), Smart Data Streaming (SDS), Remote Data Sync (RDS), and in addition, for Hadoop integration, the use of SAP HANA Spark Controller optionally with SAP Vora._

There are additional SAP analytics solutions, each with different software deployment options, some of which are summarized below:

- SAP BW/4HANA
   - Deployment to existing traditional on-premises data center
   - Deployment to Cloud IaaS

- SAP HANA Platform Enterprise Edition
   - Deployment to existing traditional on-premises data center
   - Deployment to Cloud IaaS
   - SAP HANA Enterprise Cloud (HEC), _Managed DBaaS_

- SAP HANA Cloud Services
   - SAP HANA Cloud, _DBaaS_ _`(released in 2020 to replace SAP HANA Service, which in 2018 replaced SAP HANA One)`_
   - SAP Analytics Cloud, _SaaS_
   - SAP Data Warehouse Cloud, _SaaS_

- SAP Business Objects Business Intelligence Suite (BOBJ/BO-BI)
- SAP Data Intelligence 3.x, using Kubernetes _`(released in 2019 to replace SAP Data Hub 2.x)`_

More information on the variants of SAP analytics solutions is available from SAP:
- [SAP.com - Business Analytics Tools and Solutions](https://www.sap.com/products/technology-platform/analytics.html){: external}
- [SAP Community Blogs - Technical Articles - SAP (HANA) Cheat Sheet](https://community.sap.com/t5/technology-blogs-by-sap/sap-hana-cheat-sheet/ba-p/13437403){: external}

Regarding SAP BW/4HANA, the {{site.data.keyword.ibm_cloud_sap}} portfolio documentation refers to SAP HANA and SAP NetWeaver installations as using SAP-certified Cloud Infrastructure-as-a-Service options for running SAP BW/4HANA deployment to Cloud IaaS.
{: note}

### Additional decisions for implementation and maintenance
{: #bw4hana-variants-implement-maintain}

The project implementation and maintenance/support of SAP BW/4HANA selects from the list of SAP Partners:
- Project implementations
   - A Global Systems Integrator (GSI) for SAP
   - A Managed Services Provider (MSP) for SAP

- Maintenance/support
   - An Application Management Services provider (AMS) for SAP
   - A Managed Services Provider (MSP) for SAP

A list of all SAP Partners is maintained on the [SAP Partner Finder tool](https://partnerfinder.sap.com){: external}. The list has more information about SAP Partners, including:
* Partnership category (for example, Consulting & Implementation Services)
* Partnership level (for example, Platinum, Gold, Silver)

These choices particularly affect how your SAP BW/4HANA on Cloud IaaS is deployed, operated, and maintained. For example, a GSI has exceeding depth of experience in implementation, functional configuration, and development - with the flexibility to create a bespoke solution for the business requirements. However, the GSI has less experience in maintenance. Conversely, an MSP has more restrictions on the implementation to ensure more successful maintenance.

Given the division of skills in traditional on-premises data center implementations of SAP workloads during previous decades, there were multiple tasks handled by the Data Center Provider. It is suggested to consider the skills of your SAP Partners in the following areas because the Cloud Service Provider is not responsible for those activities previously fulfilled by the Data Center Provider:

* Cloud account and IAM setup
* Networking setup (including security)
* Storage setup
* Infrastructure sizing for SAP
* OS configuration (including security)

Further information on [Moving SAP Workloads](/docs/sap?topic=sap-faq-moving-sap-workloads) is described in the FAQ.

## Compute considerations
{: #bw4hana-compute}

Depending on the business requirements and risk acceptance, the primary decision for any Cloud IaaS running SAP workloads is which Cloud Tenancy model to use:
- Single-tenant infrastructure, **dedicated** compute resources that are accessed with a private logical network within the cloud provider network backbone that uses:
   - Bare metal
   - Virtual servers on dedicated hosts
   - VMware SDDC
- Multi-tenant infrastructure, **shared** compute resources that are accessed with a private logical network within the cloud provider network backbone that uses virtual servers.

After you decide on the Cloud Tenancy model that meets your business and IT risk needs, the focus is then on sizing and throughput requirements:
* Output of your [SAP sizing activities](/docs/sap?topic=sap-sizing#sizing-methodology)
* Benchmark metric "SAPS", which demonstrates the total transactional throughput of the infrastructure

More information that compares different infrastructure types is in [Comparing the different SAP-certified IaaS offerings](/docs/sap?topic=sap-overview-sap-offerings-overview#iaas-offerings-compare-summary) with more detail under [Infrastructure certified for SAP](/docs/sap?topic=sap-iaas-offerings). All SAPS benchmarks are listed for each profile under each infrastructure type that is offered in the {{site.data.keyword.ibm_cloud_sap}} portfolio.

## SAP HANA considerations
{: #bw4hana-hana}

The SAP BW/4HANA business application is an analytical processing (OLAP) workload and is affected by multiple [SAP HANA Database Server design considerations](/docs/sap?topic=sap-hana-design-considerations).

During SAP sizing, decision making, and infrastructure selection for the SAP HANA infrastructure to support OLAP workloads, the sizing is most often determined by the memory (DRAM) capacity size.

### Scale-up and Scale-out
{: #bw4hana-hana-scaling}

SAP BW/4HANA is built to take advantage of the analytical capabilities of SAP HANA and is regularly used in scale-out scenarios to analyze huge volumes of data (including data beyond ERP transactional data, such as Hadoop data lakes).

It is important to understand SAP BW/4HANA certifications to ensure your infrastructure selection meets business requirements, particularly for business decisions on lead times of analytics and reporting or quantity of data to be analyzed.

All SAP-certified infrastructure on Cloud for OLAP (i.e. SAP BW/4HANA) is listed in [SAP HANA Directory - Certified IaaS Platforms - OLAP application type](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;v:105){: external}. For performance benchmarks of the SAP-certified infrastructure for SAP BW/4HANA, these are listed in the [SAP BW edition for SAP HANA benchmark directory (BWH)](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/bwh){: external}.

There are a few considerations to be mindful of when looking at the directories from SAP and comparing infrastructure performance for SAP BW/4HANA:
- The CSV Export file of the SAP BWH benchmark directory will not contain important configuration notes, such as "Segmentation" notes where the quoted memory is reduced (for example, 4,048 GB DRAM is only permitted with 3,904 GB DRAM). This often occurs when the certified infrastructure is a virtual machine. This is also not shown on the main site; it is only viewable on the certification PDF of each certified infrastructure.
- The benchmark certification is for a specific number of scale-out nodes; however, this is multiplied by SAP for the IaaS certification so that the same tested infrastructure configuration might be approved for additional scale-out (reaching larger total memory footprint).
   - Benchmark certification example: `8 scale-out nodes which includes the 1 Parent + 7 Child nodes; using infrastructure with 6,144 GB DRAM for total memory of 49,152 GB`
   - IaaS certification based on the same benchmark certification example: `16 scale-out nodes which includes 15 Active nodes (1 Parent + 14 Child nodes) + 1 Standby node; using infrastructure with 6,144 GB DRAM for total memory of 92,160 GB`

**The following table provides examples of how these benchmarks could be inferred to help the business perform sizing decisions for SAP BW/4HANA:**

| _Benchmark data point:_\n_(by sequence shown in benchmark reports)_ | **Phase 1: Data load (seconds)** | **Phase 2: Query executions per hour** | **Phase 2: Records selected** | **Phase 3: Runtime of complex query phase (seconds)** |
| --- | --- | --- | --- | --- |
| **Measurement interpretation:** | Lower is better | Higher is better | Higher is better | Lower is better |
| **Benchmark impacting factors:** | Impacted by quantity of initial records to load | Impacted by number of records selected in Phase 2 | | Impacted by number of records selected in Phase 2 |
| - | - | - | - | - |
| **Example comparison calculation for infrastructure selections (attempts to account for the impacting factors):** | _Seconds to load 1 billion records_ might help comparisons of read from storage \n \n `(for example, divide Phase 1 data load in seconds by initial records)` | _Total records parsed per hour during Query Execution_ might help comparisons of calculations performed by CPU \n \n `(for example, multiply query executions per hour by records selected)` | | _Total records parsed per minute during complex query_ might help comparisons of calculations performed by CPU \n \n `(for example, divide records selected by runtime of complex query seconds, then multiply by 60)` |
{: caption="Examples of inferring benchmark results into sizing decisions" caption-side="top"}

**Additional SAP Notes regarding SAP BW/4HANA and sizing for scale-out:**
- [SAP Note 2296290 - New Sizing Report for SAP BW/4HANA](https://me.sap.com/notes/2296290){: external}
- [SAP Note 2347382 - SAP BW/4HANA – General Information (Installation, SAP HANA, security corrections…)](https://me.sap.com/notes/2347382){: external}
- [SAP Note 2561976 - SAPBWNews SAP BW/4HANA 1.0 SP 08](https://me.sap.com/notes/2561976){: external}
- [SAP Note 2908965 - SAPBWNews SAP BW/4HANA 2.0 SP 06](https://me.sap.com/notes/0002908965){: external}
- [SAP Note 2671297 - SAP BW on SAP HANA and SAP BW/4HANA in a SAP HANA, active/active read-enabled option environment](https://me.sap.com/notes/2671297){: external}
