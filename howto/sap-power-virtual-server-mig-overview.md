---
copyright:
  years: 2024, 2025
lastupdated: "2025-02-06"

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, Hybrid Cloud, Migration, AIX, Linux, RHEL, SuSE

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Overview - Migrating SAP servers between on-premises and {{site.data.keyword.cloud}} on IBM {{site.data.keyword.powerSys_notm}}
{: #sapmig-overview}

Running SAP S/4HANA or SAP ECC on IBM {{site.data.keyword.powerSysFull}} in a hybrid cloud environment offers:
- A flexible infrastructure
- Consistent platform for SAP HANA based and traditional applications
- Best-in-class performance
- Freedom to run SAP workload where you need it.
{: shortdesc}

Use the following information to understand how to migrate an SAP system between on-premises and {{site.data.keyword.powerSys_notm}}.

## SAP workload-related hybrid cloud network considerations
{: #sapmig-overview-network}

The hybrid cloud network connection is the base decision for all migration scenarios.

Deciding on the network connection option and estimating bandwidth requirements are the first steps. A typical SAP database size can be huge. The network connection must be able to complete intersite data replication in sufficient time. Read the [Hybrid Cloud Network Considerations for SAP applications on {{site.data.keyword.powerSysFull}}](/docs/sap?topic=sap-sapmig-hybrid-cloud-networking) section for details.

## Migrating SAP S/4HANA to IBM {{site.data.keyword.powerSys_notm}}
{: #sapmig-migrating-sap-s4hana}

Multiple options to migrate SAP S/4HANA between sites are possible. The following solution reduces SAP application downtime by using SAP HANA System Replication (HSR):

1. Check the SAP S/4HANA [Pre-Migration Steps](/docs/sap?topic=sap-sapmig-db-hana#sapmig-db-hana-presteps-source) section.
1. Install and prepare a target system in {{site.data.keyword.powerSys_notm}} as described in the [Creating the Target SAP HANA System on IBM Power Systems Virtual Servers pre-step](/docs/sap?topic=sap-sapmig-db-hana#sapmig-db-hana-presteps-target) section.
1. Migrate the application data according to [Migrating SAP S/4HANA by using SAP HANA System Replication](/docs/sap?topic=sap-sapmig-db-hana#sapmig-db-hana-replication).
1. Perform an [SAP HANA System Replication Takeover](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/123f2c8579fd452da2e7debf7cc2bd93.html){: external} to activate services on the target site and make sure that clients connect to the new location.


## Migrating SAP ECC on Oracle to IBM {{site.data.keyword.powerSys_notm}}
{: #sapmig-migrating-sap-ecc-oracle}

Different alternatives to migrate a SAP system on Oracle database are available. The following solution optimizes Oracle licenses costs and migration efforts.
Following steps describe how to migrate an SAP Enterprise Core Component (ECC) System on Oracle.
1. Start with the [Preparation Steps on Source System](/docs/sap?topic=sap-sapmig-db-oracle#sapmig-db-oracle-source-prep) section.
1. Next, [Backup the Source Oracle Database using RMAN](/docs/sap?topic=sap-sapmig-db-oracle#sapmig-db-oracle-backup).
1. Finalize the migration with steps described in section [Restore the Oracle Database on Target System](/docs/sap?topic=sap-sapmig-db-oracle#sapmig-db-oracle-restore).

## Migrating SAP ERP 6 on IBM Db2 to IBM {{site.data.keyword.powerSys_notm}}
{: #sapmig-migrating-sap-erp-db2}

Multiple options to migrate SAP on IBM Db2 are technically possible. The chapter [Migrating SAP ERP 6 on IBM Db2 to IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-sapmig-db-db2) describes these two major options:

- [Migration option 1 - Back up and restore](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option1-backup-restore) is the easier migration option with downtime during the complete migration
- [Migration option 2 - IBM Db2 high availability and disaster recovery (HADR)](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option2-backup-restore-hadr) is a downtime-optimized version, more complex but with minimum downtime.
