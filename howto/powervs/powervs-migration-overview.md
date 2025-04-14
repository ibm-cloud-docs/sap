---
copyright:
  years: 2024, 2025
lastupdated: "2025-04-11"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, Hybrid Cloud, Migration, AIX, Linux, RHEL, SuSE
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Overview - Migrating SAP servers between on-premises and {{site.data.keyword.cloud}} on IBM {{site.data.keyword.powerSys_notm}}
{: #sapmig-overview}

When you run SAP S/4HANA or SAP ERP 6.0 with IBM {{site.data.keyword.powerSysFull}} in a hybrid cloud environment, you have the following benefits:

- A flexible infrastructure
- Consistent platform for SAP HANA-based and traditional applications
- Best-in-class performance
- Freedom to run SAP workloads where you need them
{: shortdesc}

Use the following information to learn how to migrate an SAP system between on-premises and {{site.data.keyword.powerSys_notm}}.

## SAP workload-related hybrid cloud network considerations
{: #sapmig-overview-network}

Your choice of hybrid cloud network connections is the basis for all migration scenarios.

Choosing the network connection option and estimating the bandwidth requirements are the first steps. A typical SAP database size is large. The network connection needs to complete intersite data replication in sufficient time. For more information, see [Hybrid Cloud Network Considerations for SAP applications on {{site.data.keyword.powerSysFull}}](/docs/sap?topic=sap-sapmig-hybrid-cloud-networking).

## Migrating SAP S/4HANA to IBM {{site.data.keyword.powerSys_notm}}
{: #sapmig-overview-migrating-sap-s4hana}

Multiple options to migrate SAP S/4HANA between sites are possible. The following solution reduces SAP application downtime by using SAP HANA System Replication (HSR).

1. Read the SAP S/4HANA [Pre-migration steps](/docs/sap?topic=sap-sapmig-db-hana#sapmig-db-hana-presteps-source) section.
1. Install and prepare a target system in {{site.data.keyword.powerSys_notm}} as described in the [Creating the target SAP HANA system on IBM Power Systems Virtual Servers pre-step](/docs/sap?topic=sap-sapmig-db-hana#sapmig-db-hana-presteps-target) section.
1. Migrate the application data according to [Migrating SAP S/4HANA by using SAP HANA System Replication](/docs/sap?topic=sap-sapmig-db-hana#sapmig-db-hana-replication).
1. Perform an [SAP HANA System Replication Takeover](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/123f2c8579fd452da2e7debf7cc2bd93.html){: external} to activate services on the target site and make sure that clients connect to the new location.

## Migrating SAP ERP 6.0 with Oracle to IBM {{site.data.keyword.powerSys_notm}}
{: #sapmig-overview-migrating-sap-erp-oracle}

Different alternatives to migrate an SAP system with Oracle database are available. The documented solution optimizes Oracle licenses costs and migration efforts.

Use the following steps to migrate an SAP ERP 6.0 Core Component (ECC) System with Oracle.

1. Start with the [Preparation Steps on Source System](/docs/sap?topic=sap-sapmig-db-oracle#sapmig-db-oracle-source-prep) section.
1. [Back up the Source Oracle Database by using RMAN](/docs/sap?topic=sap-sapmig-db-oracle#sapmig-db-oracle-backup).
1. Finalize the migration with steps that are described in section [Restore the Oracle Database on Target System](/docs/sap?topic=sap-sapmig-db-oracle#sapmig-db-oracle-restore).

## Migration options for SAP ERP 6.0 with IBM Db2 to IBM {{site.data.keyword.powerSys_notm}}
{: #sapmig-overview-migrating-sap-erp-db2}

Multiple options to migrate SAP with IBM Db2 are possible. [Migrating SAP ERP 6.0 with IBM Db2 to IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-sapmig-db-db2) describes two major options:

- [Migration option 1 - Back up and restore](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option1-backup-restore) is the easier migration option, but has greater downtime during the complete migration.
- [Migration option 2 - IBM Db2 high availability and disaster recovery (HADR)](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option2-backup-restore-hadr) is a downtime-optimized version. This option is more complex, but it has minimum downtime.

## Migration from SAP ERP 6.0 to S/4HANA to IBM {{site.data.keyword.powerSys_notm}} considerations
{: #sapmig-overview-migrating-sap-sum-dmo}

Migrating an SAP ERP 6.0 (ECC) system from on-premises to an SAP S/4HANA system in IBM {{site.data.keyword.powerSys_notm}} is called a heterogeneous migration.

A heterogeneous migration typically includes the following actions:

- Changing the SAP version, for example, from SAP ERP 6.0 (ECC) on-premises to SAP S/4HANA in IBM {{site.data.keyword.powerSys_notm}}
- Changing the operating system, for example, from IBM AIX to Red Hat Enterprise Linux (RHEL) or SUSE Linux Enterprise Server (SLES)
- Changing the database system, for example, from an Oracle database to an SAP HANA database

Support from SAP migration experts for this complex SAP migration project is advised.

A heterogeneous migration typically uses a migration tool or migration option. SAP software update manager (SUM) offers the database migration option (DMO), which is used in
[Migration from SAP ERP 6.0 to S/4HANA to IBM {{site.data.keyword.powerSys_notm}} considerations](/docs/sap?topic=sap-sapmig-sum-dmo).
