---
copyright:
  years: 2024
lastupdated: "2024-03-04"

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, on-prem, on premises, Hybrid Cloud, Migration, Linux, Redhat, RHEL, SuSE, HANA, HSR, HANA System Replication, clean up, pre migration step, Row Store Re-organization, reorg, row, store

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Steps before you migrate an SAP S/4HANA database
{: #sapmig-db-hana-presteps-source}

The following sections cover several important advisories to prepare for the SAP HANA database migration.
Read and implement the relevant SAP notes.
{: shortdesc}

Before you attempt any data migration or replication action, check the source database for any existing issues.

If issues exist, it might be one of the following issues.

* Interrupted or failed garbage collection.
* Source databases still contain entries, tables, or data from actions such as client deletion, which leads to a false positive on the true size of the database.
* Reported inconsistencies during the database check.
* Extreme load or unload actions that lead to orphaned entries.
* Hardware issues that occurred during a delta merge from memory to disk.
* Excessive page memory dumps were detected, which can indicate page corruptions.
* Alerts that are displayed during an SAP HANA Mini Check.

 Incorrectly performing any data migration or replication action can result in data loss and application inconsistencies. Make sure that you read and understand the associated SAP Notes and correction notes before you perform any related task. {{site.data.keyword.cloud}} is not liabil for any data loss or application integrity.
{: important}

The following sections contain SAP-advised pre-steps to help make sure that the source database is in a consistent state.
Before any migration or backup or recovery operation begins that the consistency (such as Row store, Column Store, Pages) and trace files that are on the source database are closely examined for any existing issues.
These recommended steps must be completed before you start the migration.

## Checking and confirming database health
{: #sapmig-db-hana-pre-steps-source-health}

Check the health of your database to reduce the risk of the transferring existing issues to your target system.
Health checks prevent pre-existing issues (such as consistency or block corruption) from migrating onto the target SAP HANA system.
SAP HANA System Replication can't help you in this scenario, so it is important to perform these necessary checks.
Use the following SAP Notes to assist you.

* [SAP Note 2116157 - FAQ: SAP HANA Consistency Checks and Corruptions](https://me.sap.com/notes/2116157){: external}
* [SAP Note 2272121 - How To: Analyzing Physical Corruptions with the SAP HANA Persistence Diagnosis Tool](https://me.sap.com/notes/2272121){: external}
* [SAP Note 2380176 - FAQ: SAP HANA Database Trace](https://me.sap.com/notes/2272121){: external}

### Checking the database trace files
{: #sapmig-db-hana-pre-steps-source-health-trace}

The database trace is written to service specific files on operating system level. The trace directory is located here:

`/usr/sap/<SID>/HDB<inst>/<host>/trace/DB_<SID>/`

The following alias in the environment of the `<sid>adm` user helps you to quickly switch to the trace directory on the OS level:

`cdtrace`

The database trace files use the following naming convention:

`<service>_<host>.<port>.<counter>.trc`

In the context of dynamic tiering, a file with the following convention can exist (SAP Note 2871785):

`esserver_console_<host>.<port>.<counter>.trc`
Example:

`indexserver_saphana01.30003.024.trc`
You can access these files either directly, at the operating system level, or in one of the following ways:

* `SAP HANA Studio -> Administration -> Diagnosis Files`
* `DBACOCKPIT -> Diagnostics -> Diagnosis Files`

### More checking information
{: #sapmig-db-hana-pre-steps-additional-diag}

* [SAP HANA Administration Guide - Persistence Consistency Check](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/60e110ea8f9e457095210115487e11a6.html){: external}

The SQL statements that are in the following SAP notes indicate whether a database reorganization is required and the amount of space that is saved after the reorg action takes place.

This check serves two purposes.

   1. Highlights whether the reorg action is required on the SAP HANA database.
   1. If a reorg action is required, it provides an estimated size after space saving actions are completed.

## Reorganizing database row store
{: #sapmig-db-hana-pre-steps-source-health-reorg}

If your database is heavily fragmented, a row store reorganization is required.

Starting from SAP HANA 2.0 SPS04, online row store reorganization automatically triggers for large row store (allocated size >= 3.2 GB) if the utilization ratio is less than the defined threshold.

By default, the threshold is 60% and the utilization ratio is checked in the background once an hour.

* [SAP Note 2789255 - Automatic Online Row Store Reorganization](https://me.sap.com/notes/2789255){: external}

If your database is version is less than `SAP HANA 2.0 SPS04`, then follow the instructions that are in

* [SAP Note 1813245 - SAP HANA Row Store Reorganization](https://me.sap.com/notes/1813245){: external}

* [SAP Note 1977584 - Technical Consistency Checks for SAP HANA Databases](https://me.sap.com/notes/1977584){: external} This SAP Note contains useful SQL statements to check the CATALOG, DEPENDENCY, and TABLE CONSISTENCY.

Make sure that you pay attention to the instructions that are in these SAP Notes and follow each step that relates to your existing SAP HANA version.
{: important}

## SQL mini checks
{: #sapmig-db-hana-pre-steps-source-sql-statements}

Use this SQL statement to show the current size of the SAP HANA database.

```sql
SELECT HOST, PORT, TO_DECIMAL( SUM(FREE_SIZE)*100 / SUM(ALLOCATED_SIZE), 10,2) "Free Space Ratio in %",TO_DECIMAL(
SUM(ALLOCATED_SIZE)/1048576, 10, 2) "Allocated Size in MB",TO_DECIMAL( SUM(FREE_SIZE)/1048576, 10, 2) "Free Size in MB" FROM
M_RS_MEMORY WHERE ( CATEGORY = 'TABLE' ) and ( ALLOCATED_SIZE > 0 ) GROUP BY HOST, PORT
```
{: codeblock}

For more useful SQL statements, you can use the following SAP Note. This SAP Note includes some useful SQL statements that you can run from the command line by using the `hdbsql` executable file. Or, you can use the SQL Console that is built into SAP HANA Studio.

* [1969700 - SQL Statement Collection for SAP HANA](https://me.sap.com/notes/1969700){: external}

Commands from SQLStatements_Internal.zip impose an increased risk of instabilities such as crashes or terminations. If you run these commands, run them with care. Perhaps running them first on a DEV or POC system first.
{: important}

The following SQL statement helps you identify critical technical issues. When you download the SQL Collection compressed files, search for `SQL: "HANA_Configuration_MiniChecks"`.

`SQL: "HANA_Configuration_MiniChecks"` performs several mini checks and returns `C = 'X'`if it finds a potentially critical situation.
You can use the following SAP Note to interpret the results.

* [SAP Note 1999993 - How-To: Interpreting SAP HANA Mini Check Results](https://me.sap.com/notes/1999993){: external}

## Scheduling an SAP HANA sizing report on the source system
{: #sapmig-db-hana-pre-steps-source-sizing}

If you plan to migrate an existing SAP system from an on-premise site to your {{site.data.keyword.cloud}} environment, you need fristto run an SAP Sizing report.
The current version for the SAP HANA memory sizing report is `Advanced correction version 17`.

* [SAP Note 3338309 HANA memory Sizing report - Advanced correction 17](https://me.sap.com/notes/3338309){: external}

If you want to run the SAP HANA Sizing report, see the following SAP Note.

* [SAP Note 1872170 - ABAP on HANA sizing report (S/4HANA, Suite on HANA...)](https://me.sap.com/notes/1872170){: external}

It is advised that you use the most recent Advanced Correction of the SAP Sizing report.
When you run the report, make sure that you include the forecast for SAP HANA database growth. The generated report  states the anticipated required CPU, memory, and storage recommendations for your PowerVS instance target.
Go to {{site.data.keyword.cloud}} and select the most recent certified profile that is available for IBM Power Virtual Servers.

* [SAP 2947579 - SAP HANA on IBM Power Virtual Servers](https://me.sap.com/notes/2947579){: external}
* [SAP 2188482 - SAP HANA on IBM Power Systems: Supported hardware and features](https://me.sap.com/notes/2188482){: external}

### Extra sizing SAP Notes
{: #sapmig-db-hana-pre-steps-additional-sizing}

* [SAP Note 2363248 - SAP BW/4HANA Hardware Sizing](https://me.sap.com/notes/1872170){: external}
* [SAP SD benchmark results](https://www.sap.com/about/benchmark.html){: external}
* [Measuring in SAPS (SAP Application Performance Standard)](https://www.sap.com/about/benchmark/measuring.html){: external}

## Using the EarlyWatch Alert reports as an early indicator
{: #sapmig-db-hana-pre-steps-source-early-watch-reports}

If your on-premises landscape has an SAP Solution Manager set up, you can generate the EarlyWatch Alert report for your source system.

The report outlines specific issues that your on-premises source system might have.
You must address SQL performance indicators, Urgent performance KPI indicators immediately.
Issues that are classified as `Red` or `Severe problems detected` must be handled as soon as possible.

Check the EarlyWatch Alert report for existing issues with the source SAP HANA database and act upon each finding in the `Service Summary` or `Alert Overview` sections, based on its severity.

### More related SAP Notes for EarlyWatch Alert reports
{: #sapmig-db-hana-sap-notes-ewa}

* [SAP Note 1257308 - FAQ: Using EarlyWatch Alert](https://me.sap.com/notes/1257308){: external}
* [SAP Note 1958910 - EarlyWatch Alert For HANA Database](https://me.sap.com/notes/1958910){: external}
* [SAP Note 1911180 - HANA EarlyWatch Alerts (EWA) Issues](https://me.sap.com/notes/1911180){: external}
* [SAP Solution Manager - Help Portal Documentation](https://help.sap.com/docs/SUPPORT_CONTENT/sm/3518046483.html){: external}

## Source database credentials
{: #sapmig-db-hana-pre-steps-source-credentials}

When you add an SAP HANA system to the SAP HANA System Replication setup, remember that the replication process from source primary to the target secondary server overwrites the MDC User Tables `SAP${sid}.USR02`.
So, it is important to know (by checking the SAP HANA Studio) what the current user with SYS privileges that was used to register the MDC in SAP HANA Studio on the source.
As a `Best Practice`, make sure that you know the login credentials for the database user and the password for the source system.
If, for example, you forget the passwords and proceed with the SAP HANA System Replication from the source to the target you can test the secondary target by swapping the primary and secondary servers. If you do not know the login credentials for the database user and the password on the source system then you can't register the system in either SAP HANA Studio or an SAP HANA Cockpit setup.
