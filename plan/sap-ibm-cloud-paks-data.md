---

copyright:
  years: 2020
lastupdated: "2020-09-21"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, IBM Cloud Paks

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
{:important: .important}

# IBM Cloud Paks for Data with SAP
{: #ibm-cloud-paks-data-sap}

**_INCOMPLETE: This page only appears in Test/Staging, it is not complete or ready to be published_**
{: important}

Introduction
{: shortdesc}

IBM InfoSphere Information Server 11.7, Pack for SAP Applications (Version 8.2.0.4)
https://www.ibm.com/docs/en/iis/11.7?topic=applications-introduction
https://www.ibm.com/docs/en/iis/11.7?topic=rn-infosphere-information-server-pack-sap-applications-version-8204-release-notes

IBM InfoSphere Information Server 11.7, Pack for SAP BW (Version 4.4.0.2)
https://www.ibm.com/docs/en/iis/11.7?topic=bw-introduction
https://www.ibm.com/docs/en/iis/11.7?topic=iispsbrn-infosphere-information-server-pack-sap-bw-version-4402-release-notes

DataStage Cartridge Containers == CP4D?


IBM® InfoSphere® Information Server Pack for SAP Applications enables you to extract data from SAP applications, and enrich the extracted data by mapping, aggregating, and reformatting it for use in other applications.

IBM InfoSphere Information Server Pack for SAP Applications provides common facilities to manage connection configurations for SAP systems.
IBM InfoSphere Information Server Pack for SAP Applications includes the following stages:

    BAPI: The BAPI stage exchanges data with SAP systems by using the SAP Business Application Programming Interfaces (BAPIs).
    IDoc (intermediate documents): The IDoc Load and Extract Connector stages use the IDoc (intermediate document) interface to load or extract SAP data.
    ABAP: The ABAP Extract stage allows direct data extraction from internal SAP tables by running a generated ABAP program directly on the SAP system.
    Delta Extract: Allows you to extract data from SAP application systems by using SAP Datasource. Available from Pack for SAP Applications version 8.0.0.0.
    OData: Enables you to extract data from SAP systems (on-prem and cloud) by using the SAP exposed OData services. Available from Pack for SAP Applications version 8.2.0.0


The InfoSphere® Information Server Pack for SAP BW enable users to schedule and run the ETL jobs. It has a friendly user interface to design ETL jobs and to set up the data operations to be performed on SAP BI systems.

The Pack includes the following major components:

    BW Load Stage: Loads data from non-SAP data sources to an SAP BI system. BW Load Stage is an SAP-certified, data-loading integration solution implemented using the SAP Staging BAPI interface.
    BW Extract Stage: Extracts data from an SAP BI system. It is an SAP-certified, data-extraction integration solution based on the SAP Open Hub Service interface.
    BW RFC Server: Implements various functions that are invoked by an SAP BI system. It accepts the SAP BI initiated data-loading or data-extraction requests and triggers the DataStage jobs to execute the corresponding data operations.
    BW RFC Manager: Manages the BW RFC Server processes. It creates one BW RFC Server process per source system. It also provides the functions to start or stop BW RFC Server processes. A source system represents a logical or physical system that is external to an SAP BI system. A source system provides source data to an SAP BI system or accepts extracted data from an SAP BI system.
