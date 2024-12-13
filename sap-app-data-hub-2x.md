---

copyright:
  years: 2020
lastupdated: "2020-09-21"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP Data Hub, {{site.data.keyword.cos_full_notm}}, {{site.data.keyword.cos_short}}, {{site.data.keyword.openshiftlong_notm}}, {{site.data.keyword.openshiftshort}}, Red Hat Enterprise Linux, SAP Data Hub on {{site.data.keyword.cloud_notm}}, data orchestration, data governance, data integration

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

# SAP Data Hub 2.x
{: #data-hub}

SAP Data Hub 2.x is a distributed data orchestration and management software for creation of data-driven business processes. SAP Data Hub 2.x is replaced by SAP Data Intelligence 3.x.

Installation of SAP Data Hub 2.x is available for Red Hat OpenShift on IBM Cloud. The OpenShift platform is managed by {{site.data.keyword.cloud_notm}}, but you have full control of the size and number of worker nodes available for deployment worldwide.

This documentation appends to Red Hat's article [SAP Data Hub 2 on OpenShift Container Platform 3](https://access.redhat.com/articles/3630111){: external} (referred as acronym `RHA`)
{: note}

Any considerations or guidance that is provided by {{site.data.keyword.ibm_cloud_sap}}, does not replace implementation documentation for Red Hat OpenShift and SAP Data Hub.

Many of the implementation steps are in the [SAP Data Hub 2 on OpenShift Container Platform 3 article (RHA)](https://access.redhat.com/articles/3630111){: external}. The How To guidance provides the additional {{site.data.keyword.cloud_notm}}-specific steps:
- [Planning your deployment](/docs/sap?topic=sap-rhos-planning-items)
- [Deploying your OpenShift cluster and jump host](/docs/sap?topic=sap-rhos-set-up-cluster)
- [Preparation and Installation of SAP Data Hub](/docs/sap?topic=sap-rhos-datahub)

## Before you begin with SAP Data Hub 2.x and Red Hat OpenShift on IBM Cloud
{: #data-hub-before-you-begin}

To get started, you need to be familiar with {{site.data.keyword.cloud_notm}}, OpenShift, and SAP Data Hub. Depending on your current level of knowledge, read some or all of the following documentation.

- Containers and Kubernetes...
   - [What are Containers?](https://www.ibm.com/cloud/learn/containers){: external}
   - [What is Kubernetes?](https://www.ibm.com/cloud/learn/kubernetes){: external}
   - [What is Red Hat OpenShift on {{site.data.keyword.cloud_notm}}?](https://www.ibm.com/cloud/openshift){: external}
- Red Hat OpenShift on IBM Cloud information...
   - [Getting started with Red Hat OpenShift on {{site.data.keyword.cloud_notm}}](/docs/openshift?topic=openshift-getting-started){: external}
   - [Tutorial: Creating Red Hat OpenShift on {{site.data.keyword.cloud_notm}} clusters](/docs/openshift?topic=openshift-openshift_tutorial){: external}
- SAP Data Hub information...
   - [What is SAP Data Hub?](https://www.sap.com/products/data-intelligence.html){: external}
   - [Review the Requirements for SAP Data Hub 2.7.x on OpenShift Container Platform 3.x](https://access.redhat.com/articles/3630111#requirements){: external}

## Usage considerations
{: #data-hub-usage}

SAP Data Hub provides a simple, scalable approach to manage, integrate, process, and govern data. It provides data orchestration and metadata management across heterogeneous data sources.

The SAP Data Hub is a fundamental technology in the SAP strategy. Part of the digital platform, SAP Data Hub is a connector and processing layer on which to build new applications. By enabling connections across the whole SAP product portfolio, SAP Data Hub helps the business build their Intelligent Enterprise.

### Common Scenarios
{: #data-hub-scenarios}

| Use Case | Details |
| ----- | ----- |
| IOT Sensor Data Ingestion | Integrate and transform data streams from any sensor or device into data that can be used by ERP, AI/ML, and other Big Data services |
| Big Data, Machine Learning, Artificial Intelligence | Move your data science projects from PowerPoint to operational applications, gleaning real insight from what you’re already collecting |
| Data Warehousing | Make it possible to add disparate data streams into your data warehouse, and transform it from an archival reporting system to a core business value |
| Master Data and Governance | Automate your metadata tagging and discovery, understand what data is coming from where, and control who can see the data based on security and privacy standards |
{: caption="Common scenarios for SAP Data Hub" caption-side="top"}
