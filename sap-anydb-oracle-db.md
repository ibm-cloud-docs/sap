---

copyright:
  years: 2020
lastupdated: "2020-09-21"

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
{:important: .important}

# AnyDB - Oracle DB
{: #anydb-oracle-db}

**_INCOMPLETE: This page only appears in Test/Staging, it is not complete or ready to be published_**
{: important}

The SAP systems in a landscape have specific requirements for servers, operating systems, network setup, and supported storage.
{: shortdesc}

To assist your project's planning phase, there are additional design considerations for **SAP AnyDB - Oracle DB**.


Oracle product deployments are only supportable in two circumstances:

1. **Oracle product deployments with IBM Power Virtual Servers**, a complementary offering from {{site.data.keyword.IBM_notm}} Power Systems with low latency access to {{site.data.keyword.cloud_notm}} services. With Oracle and IBM Power, these deployments are made to the same enterprise infrastructure setup. More information here > https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-use-case-oracle

2. **Oracle product deployments with support from an SAP Partner**, either Managed Services Provider (MSP) or Application Management Services (AMS), is contracted for support of Oracle products and is an Oracle Partner with the relevant Oracle Support contracts


Many customers run Oracle products on IBM Cloud today leveraging these methods.

In any other circumstsance, deployments of Oracle products to Infrastructure-as-a-Service offerings from IBM Cloud will not be supported by Oracle, which results in SAP not supporting Oracle-based workloads on the SAP-certified Infrastructure-as-a-Service from IBM Cloud.


## Infrastructure size considerations
{: #anydb-oracle-db-infrastructure}

Content

## Networking performance considerations
{: #anydb-oracle-db-network}

Content

## Storage performance considerations
{: #anydb-oracle-db-storage}

Content


## Oracle DB RAC
{: #anydb-oracle-db-dbrac}

Content



## COPIED

## SAP on Oracle
{: #sap_on_oracle}

See [SAP Note 2855850](https://launchpad.support.sap.com/#/notes/2855850){: external} for supported Oracle Database versions on AIX running on {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s. For the installation procedure, see the SAP online documentation: 

[Installation of SAP Systems Based on the Application Server ABAP of SAP NetWeaver 7.1 to 7.52 on UNIX : Oracle](https://help.sap.com/viewer/4b99f675d74f4990b75a8630869a0cd2/CURRENT_VERSION/en-US/){: external}

[Installation of SAP Systems Based on the Application Server Java of SAP NetWeaver 7.1 to 7.5 on UNIX: Oracle](https://help.sap.com/viewer/08baebca0fef470389e4a4ebcb46b879/CURRENT_VERSION/en-US/){: external}
