---
copyright:
  years: 2024
lastupdated: "2024-06-27"

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, Hybrid Cloud, Migration, AIX, Linux, RHEL, SuSE

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Overview - Migrating SAP servers between on-premises and {{site.data.keyword.cloud}} on IBM Power Virtual Server
{: #sapmig-overview}

Running SAP S/4HANA or SAP ECC on IBM {{site.data.keyword.powerSysFull}} in a hybrid cloud environment offers:
- A flexible infrastructure
- Consistent platform for SAP HANA based and traditional applications
- Best-in-class performance and
- Freedom to run SAP workload where you need it.
{: shortdesc}

Use the following information to understand how to migrate an SAP system between on-premises and {{site.data.keyword.powerSys_notm}}.

## SAP workload-related hybrid cloud network considerations
{: #sapmig-overview-network}

The hybrid cloud network connection is the base decision for all migration scenarios.

Deciding on the network connection option and estimating bandwidth requirements are the first steps. A typical SAP database size can be huge. The network connection must be able to complete intersite data replication in sufficient time. Read the [Hybrid Cloud Network Considerations for SAP applications on {{site.data.keyword.powerSysFull}}](/docs/sap?topic=sap-sapmig-hybrid-cloud-networking) section for details.

## Migrating SAP S/4HANA to IBM Power Virtual Server
{: #sapmig-migrating-sap-s4hana}

Multiple options to migrate SAP S/4HANA between sites are possible. The following solution reduces SAP application downtime by using SAP HANA System Replication (HSR):

1. Check the SAP S/4HANA [Pre-Migration Steps](/docs/sap?topic=sap-sapmig-db-hana#sapmig-db-hana-presteps-source) section.
1. Install and prepare a target system in {{site.data.keyword.powerSys_notm}} as described in the [Creating the Target SAP HANA System on IBM Power Systems Virtual Servers pre-step](/docs/sap?topic=sap-sapmig-db-hana#sapmig-db-hana-presteps-target) section.
1. Migrate the application data according to [SAP HANA System Replication](/docs/sap?topic=sap-sapmig-db-hana#migrating-sap-s4hana-by-using-sap-hana-system-replication).
1. Perform an [SAP HANA System Replication Takeover](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/123f2c8579fd452da2e7debf7cc2bd93.html){: external} to activate services on the target site and make sure that clients connect to the new location.


