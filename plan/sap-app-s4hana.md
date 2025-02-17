---
copyright:
  years: 2020, 2025
lastupdated: "2025-02-17"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# SAP S/4HANA
{: #s4hana}

SAP S/4HANA is the leading Enterprise Resource Planning (ERP) software that is designed for the largest enterprises, in any country worldwide within any industry, with extensive business processes and customization.
{: shortdesc}

ERP software integrates all business management and operations into one cohesive application to coordinate business execution, such as accounting and financing, purchasing and inventory, sales and customer relationships. An ERP can be considered a hub of all business operations. Various applications and all parts of the business can be connected into ERP, from the factory to the headquarters. The ERP software as a whole can have many add-on components (functional and industry) that provide different business functions for different lines of businesses and industries.

SAP S/4HANA is a major release of ERP software from SAP designed to run with the SAP HANA database exclusively. Previous major releases of ERP from SAP, known as SAP ECC and SAP R/3, could use various relational database vendors.

SAP S/4HANA acts as the "Digital Core" for large enterprises with upgraded UX, business workflows, and technology upgrades, to enable as many extensions as possible using Cloud Native technologies.

For more information, see [SAP S/4HANA](https://www.sap.com/products/erp/s4hana.html){: external}

{{site.data.keyword.ibm_cloud_sap}} infrastructure options are certified to SAP NetWeaver application server and SAP HANA database server, which run the SAP S/4HANA business application.

## Preface: variants of SAP S/4HANA
{: #s4hana-variants}

The SAP S/4HANA business application has multiple variants, each with different functional and customization levels, which are available as different operational models. The model that you select affects the SAP S/4HANA deployment.

Primarily the software operational models are grouped into two categories:
- **SAP S/4HANA "AnyPremise"** _(formerly "On-Premise" Edition)_, which is the same software installation and hosting by the business or a business’s subcontractors. This option provides the business **full** control over the functions and deployment of the software, but with **more** deployment effort and management overheads to keep the SAP Systems running.
- **SAP S/4HANA Cloud SaaS**, which is the same software but installed and hosted by SAP along with SAP Partner subcontractors. This model provides the business with **less** control over the software functions and deployment, but with **less** deployment effort and management overheads to keep the SAP Systems running.

Within each of the software operational models, multiple software deployment options are available:
- SAP S/4HANA "AnyPremise” Edition _(formerly "On-Premise" Edition)_
   - Deployment to existing Traditional On-Premises data center
   - Deployment to Cloud IaaS
- SAP S/4HANA Cloud SaaS
   - SAP S/4HANA Public Cloud **Extended (EX)** Edition, _SaaS provided by SAP along with SAP Partner subcontractors_
   - SAP S/4HANA Public Cloud **Essentials (ES)** Edition, _SaaS provided by SAP along with SAP Partner subcontractors_
   - SAP S/4HANA Private Cloud, _SaaS extension of SAP HANA Enterprise Cloud (HEC) provided by SAP along with SAP Partner subcontractors_

More information on the variants of SAP S/4HANA software is available from SAP. A concise explanation on the variants of SAP S/4HANA is available from SAP America, see [SAP Community Blogs - Product Information - SAP S/4HANA Cloud Deployment Options (June 17, 2019)](https://community.sap.com/t5/enterprise-resource-planning-blogs-by-sap/sap-s-4hana-cloud-deployment-options/ba-p/13406848){: external}.

Regarding SAP S/4HANA, the {{site.data.keyword.ibm_cloud_sap}} portfolio documentation refers to SAP HANA and SAP NetWeaver installations as using SAP-certified Cloud Infrastructure-as-a-Service options for running SAP S/4HANA "AnyPremise" deployment to Cloud IaaS. All further descriptions in the following sections refer to SAP S/4HANA "AnyPremise".
{: note}

### Additional decisions for implementation and maintenance
{: #s4hana-variants-implement-maintain}

In addition to selecting a variant of SAP S/4HANA (the operational model and the deployment model), many SAP-run customers need to make several other decisions, for example:

#### **SAP S/4HANA adoption strategy**
{: #s4hana-variants-adoption}

- New SAP customer first-time implementation
- ERP Migration (Brownfield)
   - System Conversion (also known as Brownfield)
   - Selective Data Transition (that uses Shell Conversion or Mix&Match)
- ERP Re-Implementation (Greenfield)

#### **SAP S/4HANA delivery model for the project implementation, from the list of SAP Partners**
{: #s4hana-delivery-model}

- A Global Systems Integrator (GSI) for SAP
- A Managed Services Provider (MSP) for SAP

#### **SAP S/4HANA maintenance model for ongoing support, from the list of SAP Partners**
{: #s4hana-maintenance-model}

- An Application Management Services provider (AMS) for SAP
- A Managed Services Provider (MSP) for SAP

A list of all SAP Partners is maintained on the [SAP Partner Finder tool](https://partnerfinder.sap.com/){: external}. The list has more information about SAP Partners, including:
* Partnership category (for example, Consulting & Implementation Services)
* Partnership level (for example, Platinum, Gold, Silver)

Awards for these SAP Partners are shown on the [SAP Partners information page](https://partnerfinder.sap.com/){: external}.

These choices particularly affect how your SAP S/4HANA "AnyPremise" Edition on Cloud IaaS is deployed, operated, and maintained. For example, a GSI has exceeding depth of experience in implementation, functional configuration and development - with the flexibility to create a bespoke solution for the business requirements. However, the GSI has less experience in maintenance. Conversely, an MSP has more restrictions on the implementation to ensure more successful maintenance.

Given the division of skills in traditional on-premises data center implementations of SAP workloads during previous decades, there were multiple tasks handled by the Data Center Provider. It is suggested to consider the skills of your SAP Partners in the following areas because the Cloud Service Provider is not responsible for those activities previously fullfilled by the Data Center Provider:

* Cloud Account and IAM setup
* Networking setup (including security)
* Storage setup
* Infrastructure Sizing for SAP
* OS Configuration (including security)

Further information on [Moving SAP Workloads](/docs/sap?topic=sap-faq-moving-sap-workloads) is described in the FAQ.

## Compute considerations
{: #s4hana-compute}

Depending on the business requirements and risk acceptance, the primary decision for any Cloud IaaS running SAP workloads which Cloud Tenancy model to use:
- Single-Tenant infrastructure, **dedicated** compute resources that are accessed with a private logical network within the Cloud provider network backbone that uses:
   - Bare Metal
   - Virtual Servers on Dedicated Hosts
   - VMware SDDC
- Multi-Tenant infrastructure, **shared** compute resources that are accessed with a private logical network within the Cloud provider network backbone that uses Virtual Servers

After you decide on the Cloud Tenancy model that meets your business and IT risk needs, the focus is then on sizing and throughput requirements:
* Output of your [SAP Sizing activities](/docs/sap?topic=sap-sizing#sizing-methodology)
* Benchmark metric "SAPS", which demonstrates the total transactional throughput of the Infrastructure

More information that compares different Infrastructure types is in [Comparing the different SAP-certified IaaS offerings](/docs/sap?topic=sap-overview-sap-offerings-overview#iaas-offerings-compare-summary){: external} with more detail under [Infrastructure certified for SAP](/docs/sap?topic=sap-iaas-offerings){: external}. All SAPS benchmarks are listed for each Profile under each Infrastructure type that is offered in the {{site.data.keyword.ibm_cloud_sap}} portfolio.

## SAP HANA considerations
{: #s4hana-hana}

The SAP S/4HANA business application is affected by multiple [SAP HANA Database Server design considerations](/docs/sap?topic=sap-hana-design-considerations).

SAP S/4HANA is considered a "Mixed Workload", as the business application primarily does Transactional processing (OLTP), but SAP S/4HANA also does Analytical processing (OLAP) through SAP S/4HANA **Embedded Analytics**.

During SAP Sizing, decision making, and Infrastructure selection, SAP S/4HANA is often considered as OLTP only. This representation is not a fully accurate view of the business application, but serves as the closest representation for use with SAP Benchmarks information. However, for the SAP HANA infrastructure, the sizing is most often determined by the Memory (DRAM) capacity size.

### Scale-up and Scale-out
{: #s4hana-hana-scaling}

It is important to note that if you are using SAP HANA in scale-out deployment the transaction throughput of the system might be affected.

[SAP Note 2428711 - S/4HANA Scale-Out Sizing](https://me.sap.com/notes/2428711){: external} limits an SAP HANA scale-out to up to four nodes in total. This limit applies to S/4HANA deployments on {{site.data.keyword.cloud_notm}} to prevent our customers from experiencing transactional throughput issues. {{site.data.keyword.ibm_cloud_sap}} does not actively publish those specifications.

Instead, we advise customers who have exceptionally large SAP HANA requirements for running an SAP S/4HANA production instance (particularly those requirements over 14-18TB of DRAM) to discuss their requirements with IBM-SAP. Discussions with {{site.data.keyword.IBM_notm}} and SAP worldwide technical experts can provide more accurate advice on the business problem and identify alternative paths forward.

Alternates can include, the creation of a Hybrid Cloud model with the use of the available high-performance scale-up options in traditional datacenter deployments.

An example of Hybrid Cloud used with SAP S/4HANA when exceeding 14-18TB of DRAM, would be the use of {{site.data.keyword.IBM_notm}} Power9 hardware provided by {{site.data.keyword.IBM_notm}} Power Systems, deployed into Traditional On-Premises datacenters. The maximum **IBM Power9 hardware can support 28TB of DRAM for SAP HANA 2.0 scale-up**. These complementary offerings are already successfully running many customers SAP workloads at these largest memory footprints, through a close partnership and engineering discussions with SAP, therefore may suit the business needs for such exceptional scale-up requirements. For more information regarding SAP HANA on {{site.data.keyword.IBM_notm}} Power Systems, see [SAP Note 2188482 - SAP HANA on {{site.data.keyword.IBM_notm}} Power Systems: Allowed Hardware](https://me.sap.com/notes/2188482){: external}.

## SAP NetWeaver considerations
{: #s4hana-netweaver}

The SAP S/4HANA business application is impacted by multiple [SAP NetWeaver Application Server design considerations](/docs/sap?topic=sap-netweaver-design-considerations).

### Versioning and upgrades
{: #s4hana-netweaver-version-upgrades}

SAP S/4HANA no longer has a stand-alone shipment of SAP NetWeaver Application Server (ABAP) which can be used.

The **SAP S/4HANA Server** component is required to install SAP S/4HANA. This component is briefly titled as SAP ABAP Platform and historically called SAP NetWeaver AS ABAP.

For SAP S/4HANA "AnyPremise" 20xx (for example, 2020), the **SAP S/4HANA Server contains**:
- SAP ABAP Platform 20xx and SAP Kernel 7.7x _(the version number of these components are only shown after the installation is completed)_
- ADT for Eclipse
- Other additional technology components to run SAP S/4HANA

For this reason, when you are running older SAP S/4HANA versions (such as 1511, 1610, 1709) it is not possible to upgrade SAP NetWeaver AS ABAP 7.5+ in isolation. All upgrades must be handled by using SAP Maintenance Planner for the entire stack on a specified OS (for example, Red Hat Linux&reg;, SUSE Linux, {{site.data.keyword.IBM_notm}} AIX, Windows Server).
{: important}
