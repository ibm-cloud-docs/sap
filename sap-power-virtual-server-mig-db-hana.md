---
copyright:
  years: 2024, 2024
lastupdated: 2024-06-21

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, on-prem, on premises, Hybrid Cloud, Migration, Linux, Redhat, RHEL, SuSE, HANA, HSR, HANA System Replication, clean up, pre migration step, Row Store Re-organization, reorg, row, store, sync, Synchronize

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Migrating SAP S/4HANA to IBM Power Virtual Server
{: #sapmig-db-hana}

## Steps before you migrate an SAP S/4HANA database
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

### Checking and confirming database health
{: #sapmig-db-hana-pre-steps-source-health}

Check the health of your database to reduce the risk of the transferring existing issues to your target system.
Health checks prevent pre-existing issues (such as consistency or block corruption) from migrating onto the target SAP HANA system.
SAP HANA System Replication can't help you in this scenario, so it is important to perform these necessary checks.
Use the following SAP Notes to assist you.

* [SAP Note 2116157 - FAQ: SAP HANA Consistency Checks and Corruptions](https://me.sap.com/notes/2116157){: external}
* [SAP Note 2272121 - How To: Analyzing Physical Corruptions with the SAP HANA Persistence Diagnosis Tool](https://me.sap.com/notes/2272121){: external}
* [SAP Note 2380176 - FAQ: SAP HANA Database Trace](https://me.sap.com/notes/2272121){: external}

#### Checking the database trace files
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

#### More checking information
{: #sapmig-db-hana-pre-steps-additional-diag}

* [SAP HANA Administration Guide - Persistence Consistency Check](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/60e110ea8f9e457095210115487e11a6.html){: external}

The SQL statements that are in the following SAP notes indicate whether a database reorganization is required and the amount of space that is saved after the reorg action takes place.

This check serves two purposes.

   1. Highlights whether the reorg action is required on the SAP HANA database.
   1. If a reorg action is required, it provides an estimated size after space saving actions are completed.

### Reorganizing database row store
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

### SQL mini checks
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

### Scheduling an SAP HANA sizing report on the source system
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

#### Extra sizing SAP Notes
{: #sapmig-db-hana-pre-steps-additional-sizing}

* [SAP Note 2363248 - SAP BW/4HANA Hardware Sizing](https://me.sap.com/notes/1872170){: external}
* [SAP SD benchmark results](https://www.sap.com/about/benchmark.html){: external}
* [Measuring in SAPS (SAP Application Performance Standard)](https://www.sap.com/about/benchmark/measuring.html){: external}

### Using the EarlyWatch Alert reports as an early indicator
{: #sapmig-db-hana-pre-steps-source-early-watch-reports}

If your on-premises landscape has an SAP Solution Manager set up, you can generate the EarlyWatch Alert report for your source system.

The report outlines specific issues that your on-premises source system might have.
You must address SQL performance indicators, Urgent performance KPI indicators immediately.
Issues that are classified as `Red` or `Severe problems detected` must be handled as soon as possible.

Check the EarlyWatch Alert report for existing issues with the source SAP HANA database and act upon each finding in the `Service Summary` or `Alert Overview` sections, based on its severity.

#### More related SAP Notes for EarlyWatch Alert reports
{: #sapmig-db-hana-sap-notes-ewa}

* [SAP Note 1257308 - FAQ: Using EarlyWatch Alert](https://me.sap.com/notes/1257308){: external}
* [SAP Note 1958910 - EarlyWatch Alert For HANA Database](https://me.sap.com/notes/1958910){: external}
* [SAP Note 1911180 - HANA EarlyWatch Alerts (EWA) Issues](https://me.sap.com/notes/1911180){: external}
* [SAP Solution Manager - Help Portal Documentation](https://help.sap.com/docs/SUPPORT_CONTENT/sm/3518046483.html){: external}

### Source database credentials
{: #sapmig-db-hana-pre-steps-source-credentials}

When you add an SAP HANA system to the SAP HANA System Replication setup, remember that the replication process from source primary to the target secondary server overwrites the MDC User Tables `SAP${sid}.USR02`.
So, it is important to know (by checking the SAP HANA Studio) what the current user with SYS privileges that was used to register the MDC in SAP HANA Studio on the source.
As a `Best Practice`, make sure that you know the login credentials for the database user and the password for the source system.
If, for example, you forget the passwords and proceed with the SAP HANA System Replication from the source to the target you can test the secondary target by swapping the primary and secondary servers. If you do not know the login credentials for the database user and the password on the source system then you can't register the system in either SAP HANA Studio or an SAP HANA Cockpit setup.





## Creating the Target SAP HANA System on IBM Power Virtual Server
{: #sapmig-db-hana-presteps-target}

### Planning the IBM Power Virtual Server deployment
{: #mig-db-hana-target-system-pre-deploy}

A Power Server workspace in your IBM Cloud account is a prerequisite for the following steps.
Read details in [Hybrid Cloud Network Considerations for SAP on IBM Power Virtual Server](/docs/sap?topic=sap-sapmig-hybrid-cloud-networking).

A hybrid cloud network connection needs to be in place, as described in [Hybrid Cloud Network Considerations for SAP on IBM Power Virtual Server](/docs/sap?topic=sap-sapmig-hybrid-cloud-networking).

The Planning for a deployable SAP HANA infrastructure is described in [Planning your deployment](/docs/sap?topic=sap-power-vs-planning-items).

The sizing aspect of the target system is vital to your planning.
Follow the recommendations mentioned in the SAP HANA Sizing report on the source system. Also, consider the findings of the EarlyWatch Alert report (EWA report).
Both factors provide a realistic approach on the recommended size of your target system, see
[Sizing process for SAP Systems](/docs/sap?topic=sap-sizing).

### Comparing the required CPU, cores and storage for your target system
{: #mig-db-hana-target-system-certified-profile}

- [IBM Cloud Doclink SAP Planning/Sizing](/docs/sap?topic=sap-planning-your-system-landscape)

Check that the certified profiles in {{site.data.keyword.cloud}} are close to or match the recommendations that are mentioned in the source system sizing report and also consider the EWA report summary.

Select the correct IBM Power Systems Virtual Server Certified Profile from the following two links:

- [IBM Power VS Certified Profiles SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-power-vs)
- [SAP Note 2947579 - SAP HANA on IBM Power Virtual Servers](https://me.sap.com/notes/2947579){: external}

#### Target server must have equal or greater storage capacity than the source system and be sized correctly
{: #mig-db-hana-target-system-storage-tdi}

Remember to take SAP HANA database growth into consideration and the need to follow the IBM System Storage Architecture and Configuration Guidelines for SAP HANA TDI.

The following document outlines the required storage configuration for the target server in IBM Cloud:
- [IBM System Storage Architecture and Configuration Guide for SAP HANA TDI](https://www.ibm.com/support/pages/ibm-system-storage-architecture-and-configuration-guide-sap-hana-tdi-0){: external}

Consider the extra space that you need to create a file system mount point to store software executable files and the initial SAP HANA system backup.
Depending on your planned IBM Cloud PowerVS infrastructure, you can create the file system as an NFS mount to export to other systems in the architecture.

### Creating the software repository file system and transfer the installation packages
{: #mig-db-hana-target-system-software-transfer}

As used in previous demo systems, the mount point `swrepo` is created with at least 200 GB of free space.
Download the SAP HANA Software from SAP Marketplace - the version that matches your SAP HANA version from the source system.

- [Software Downloads main page](https://support.sap.com/en/my-support/software-downloads.html){: external}
   - Access Software Downloads in SAP for Me
   - Enter your SAP "S" user ID and password to proceed.

- [Software Center Catalog view](https://me.sap.com/softwarecenter){: external}
   - Support Packages & Upgrades
   - By Alphabetical Index (A-Z)
   - "H"
   - SAP HANA PLATFORM EDITION
   - SAP HANA PLATFORM EDITION 2.0
   - SAP HANA DATABASE 2.0
   - Make sure that the Selection box shows `LINUX ON POWER LE 64BIT`
   - Select the IMDB_SERVER20 that is installed on the Source System, and download to your laptop or PC
   - Go back to the SAP HANA PLATFORM EDITION 2.0 page
   - SAP HANA CLIENT 2.0
   - Make sure that the Selection box shows `LINUX ON POWER LE 64BIT`
   - Select the release that you installed on the source system (or one version higher if your version is not on the list)
   - Navigate back to * [Software Center Catalog view](https://me.sap.com/softwarecenter){: external}
   - Support Packages & Upgrades
   - On the right side, is a search box, search for `SAPCAR`
   - On the Displayed results list, select `SAPCAR 7.53` Maintenance Software Component
   - Select the file `SAPCAR_1200-70007726.EXE` and make sure that the Selection box shows `LINUX ON POWER LE 64BIT`
   - Download to your laptop or PC or Jump Host

Create a directory `/swrepo`on the target system.

```sh
sudo mkdir /swrepo
```
{: pre}

Make sure that your user owns this directory, so the user can work and extract files.

```sh
sudo chown $USER: /swrepo
```
{: pre}

Transfer the installation files and sapcar utility downloads to the target SAP HANA server `/swrepo` mount point.
The SAPCAR utility needs executable permissions to unpack the .SAR archive files.

```sh
chmod -R 755 /swrepo/SAPCAR_1200-70007726.EXE`
```
{: pre}

You can set an alias SAPCAR for this utility in the `.bash_profile`. This setting enables the SAPCAR command from any directory.
{: tip}

To add a line to your bash profile, use the following command.

```sh
echo "alias SAPCAR='/swrepo/SAPCAR_1200-70007726.EXE'" >>$HOME/.bash_profile
```
{: pre}

Use the source command to enable the new defined alias.

```sh
source $HOME/.bash_profile
```
{: pre}

Check whether it works by running `SAPCAR -v` to get the version list:

```sh
SAPCAR -v
```
{: pre}

### Unpacking the files
{: #mig-db-hana-target-system-unpack-files}

Use the following examples to unpack the files.

```sh
SAPCAR -xvf IMDB_CLIENT20_XXX_XX-XXXXXXXX.SAR -manifest /SAP_HANA_CLIENT/SIGNATURE.SMF
```
{: pre}

The sapcar file extraction output looks like the following example.

```text
x SAP_HANA_CLIENT/SIGNATURE.SMF
SAPCAR: 98 file(s) extracted

SAPCAR -xvf IMDB_SERVER20_XXX_XX-XXXXXXXX.SAR -manifest /SAP_HANA_DATABASE/SIGNATURE.SMF

x /SAP_HANA_DATABASE/SIGNATURE.SMF
SAPCAR: 355 file(s) extracted
```
{: screen}

During extraction directories `/swrepo/SAP_HANA_DATABASE` and `/swrepo/SAP_HANA_CLIENT` are created and contain the files that are required for the installation.

#### Making sure that the target server OS and patch level match the source server
{: #mig-db-hana-target-system-os-patch-level}

Check the operating system version and patch level on the target system.
For productive systems, the same level makes sure that the installation performs similar and migrating runs with ease.
For nonproductive systems, for example, a proof of concept system (POC) in IBM PowerVS, a higher operating system version is a valid option.

##### Target server - Both RHEL and SLES
{: #mig-db-hana-target-system-os-rhel-and-sles}

To determine the operating system version and patch level, run the following command.

```sh
cat /etc/os-release
```
{: pre}

Alternatively on Red Hat Linux systems you can use a second file.

```sh
cat /etc/redhat-release
```
{: pre}

On SUSE Linux Enterprise Server (for SAP Applications) the release and patch level can be listed with the following command.

```sh
lsb_release -a
```
{: pre}

#### Making sure that the file system and mount points match the source system
{: #mig-db-hana-target-system-filesystem-mountpoints}

The source and target systems must have the identical mapping for storage, LVM, and file systems.
Only on target is the larger storage capacity that is needed or the Migration.
File system structure requirements are also highlighted in the beginning of this section with the TDI requirements.
Also, consider that the mount point and file ownership UID and GUID match the source system.
Also, mount points need the same `<SID>` defined on both systems.
When you install SAP HANA on the target system, the same `<SID>` and `<instance number>` from the source system are used.

```text
export SID=<SID>                           # SAP HANA System ID (uppercase)
export sid=<sid>                           # SAP HANA System ID (lowercase)
export INSTNO=<INSTNO>                     # SAP HANA Instance Number

export SiteOnPrem=<PrimarySiteName>        # HANA System Replication Site Name 1 - Migration from On-Prem - Source
export SiteOnCloud=<secondarySiteName>     # HANA System Replication Site Name 2 - Migration to On-Cloud - Target

export NODE1=<Hostname 1>                  # Hostname of On-Prem Server
export NODE2=<Hostname 2>                  # Hostname of IBM Cloud PowerVS Instance
```
{: pre}

#### Entries in `/etc/hosts` for all systems involved in the migration project
{: #mig-db-hana-target-system-network-hosts-file}

The `/etc/hosts` file needs to contain entries for the Source System and any dependent SAP Netweaver or S/4 FES Application Server.
You can use a DNS server for your network resource resolution, but it helps if you include the IP addresses, short name, FQDN, and description to help identify servers in the landscape in the `/etc/hosts` file, especially if issues occur with network resolution or the DNS services.

#### Preparing and tuning the OS for SAP HANA
{: #mig-db-hana-target-system-Preparation-tuning}

Use the following SAP Notes to begin the preparation phase of the target system for the installation of SAP HANA.

* [SAP Note 2777782 - SAP HANA DB: Recommended OS Settings for RHEL 8](https://me.sap.com/notes/2777782){: external}
* [SAP Note 2772999 - Red Hat Enterprise Linux 8.x: Installation and Configuration](https://me.sap.com/notes/2772999){: external}
* [SAP Note 3018133 - Linux: Running SAP applications compiled with GCC 10.x](https://me.sap.com/notes/3018133){: external}

Make sure that you completed the tasks that are mentioned in the `Recommended OS Settings for RHEL 8` as these tasks are important tuning and performance settings that need to be applied. If ignored, it can impact the installation of SAP products and the performance thereafter.

#### Pre-SAP HANA checks by using the hcmt tool
{: #mig-db-hana-target-system-precheck-hcmt}

The SAP HANA hardware and cloud measurement tools `hcmt` help measure and analyze your hardware or cloud systems before you deploy SAP HANA or apply for SAP HANA certification. The tools consist of the following components:

- SAP HANA hardware and cloud measurement tool
- SAP HANA hardware and cloud measurement analysis

Use the following SAP Note to check and verify the OS and configuration before you install SAP HANA.

- [SAP Note 2493172 - SAP HANA Hardware and Cloud Measurement Tools](https://me.sap.com/notes/2493172){: external}

If you have a port issue when you run `hcmt`, open a second Terminal session. Navigate to the setup directory of `hcmt`, now start a session that keeps the required port open.
{: tip}

To run `hcmt` in server-client mode, you need to start two sessions:

1. `hcmt` server mode on - a jump host to collect test result from remote servers
1. `hcmt` client on the target systems that are intended to run SAP HANA, run `hcmt` performance test by using the full execution plan.

##### Hcmt server session
{: #mig-db-hana-target-system-precheck-hcmt-server}

The hcmt server collects data that is measured on hcmt client systems.
A typical system to run the hcmt server is a jump host or similar system.
Navigate to the directory where hcmt is installed, and run the following command

```sh
sudo ./hcmt -v -S
```
{: pre}

The following example is the expected output.

```text
hcmt-2.00.062.00.1650891137 (2022-04-25 15:12:20)

Server started, listening on port 50000 ...
```
{: screen}

##### Hcmt client session
{: #mig-db-hana-target-system-precheck-hcmt-client}

On the target system that you want to be the SAP HANA server, run the `hcmt` command as a client by using the full execution plan.

```sh
sudo ./hcmt -v -p /swrepo/HCMT/setup/config/full_executionplan.json

```
{: pre}

System output:

```text
hcmt-2.00.062.00.1650891137 (2022-04-25 15:12:20)
Loading executionplan
LogVolume (/hana/log):
DataVolume (/hana/data):
Hosts: <`Leave Blank!!!!`>  Leave this field blank, otherwise it will affect the test.
Start execution of plan
Executing Test C9C9F832-854F-492D-8E7EFB4609AC435C
Note: CPU Micro Benchmark
```
{: screen}

If you receive an error 'Port 50000 is already used', SAP HANA is probably installed already. Stop the SAP HANA system and then run the `hcmt` command again.
{: tip}

*Plan Variant: CPU Performance*

This command generates a hcmresult-YYYMMDDHHMMSS.zip file in the setup directory. You need to upload this file to the HCMT SAP website and review the results to make sure that the HANA is set up and configured correctly.

- [SAP HCMT Cloud Server URL](https://hotui-supportportal.dispatcher.hana.ondemand.com/index.html){: external}

If you experience issues, you can still use the old check tool.

- [SAP Note 1943937 – Hardware Configuration Check Tool](https://me.sap.com/notes/1943937){: external}

#### Installing SAP HANA on the target system
{: #mig-db-hana-target-system-install-target}

Remember the following variables:

```text
export SID=<SID>                           # SAP HANA System ID (uppercase)
export sid=<sid>                           # SAP HANA System ID (lowercase)
export INSTNO=<INSTNO>                     # SAP HANA Instance Number
```
{: codesection}

For this example, the installation is up to the point where you need to enter "Y" to continue.
Navigate back to the HANA_DATABASE directory.

Run the SAP HANA database lifecycle manager command.

```sh
sudo ./hdblcm
```
{: pre}

The following example is the expected output.

```text
SAP HANA Lifecycle Management - SAP HANA Database 2.00.061.00.1644229038
************************************************************************

- This will scan the directories for the required software.
```text
Scanning software locations...
Detected components:
    SAP HANA Database (2.00.061.00.1644229038) in /swrepo/HANA/SAP_HANA_DATABASE/server
    SAP HANA Database Client (2.11.20.1644165757) in /swrepo/HANA/SAP_HANA_CLIENT/client


Do you want to specify additional components location? (y/n) [n]: `n`
```
{: screen}

Choose `n` for no additional components location and continue.

```text
Choose an action

Index | Action                    | Description
-------------------------------------------------------------
1     | install                   | Install new system
2     | extract_components        | Extract components
3     | print_detected_components | Print detected components
4     | Exit (do nothing)         |

Enter selected action index [4]: `1`
```
{: screen}

Enter `1` and press `<enter>` key to install a new system.

Output continues with the following example.

```text
SAP HANA Database version '2.00.061.00.1644229038' will be installed.

Select additional components for installation:

Index | Components | Description
--------------------------------------------------------------------------------
1     | all        | All components
2     | server     | No additional components
3     | client     | Install SAP HANA Database Client version 2.11.20.1644165757

Enter comma-separated list of the selected indices [3]: `1`
```
{: screen}

Enter `1` and press `<enter>` to install all SAP HANA components.

Output continues with the following example.

```text

SAP HANA Database version '2.00.061.00.1644229038' will be installed.

Select additional components for installation:

Index | Components | Description
--------------------------------------------------------------------------------
1     | all        | All components
2     | server     | No additional components
3     | client     | Install SAP HANA Database Client version 2.11.20.1644165757

Enter comma-separated list of the selected indices [3]: `1`
```
{: screen}

Enter `1` and press `<enter>` to install all components.
Accept a series of defaults on the next line in the output.

```text
Enter Installation Path [`/hana/shared`]:
Enter Local Host Name [`Yourhostname`]:
Do you want to add hosts to the system? (y/n) [`n`]:
```
{: screen}

Enter `n' for no additional systems.
Check the source SAP HANA database system parameters:

   - source SAP HANA `SID`
   - source SAP HANA `Instance Number`

Continue with the same values for the target system:

```text
Enter SAP HANA System ID: `<Needs to match the source system>`
Enter Instance Number [00]: `<Needs to match the source system>`
Enter Local Host Worker Group [default]:

Index | System Usage | Description
-------------------------------------------------------------------------------
1     | production   | System is used in a production environment
2     | test         | System is used for testing, not production
3     | development  | System is used for development, not production
4     | custom       | System usage is neither production, test nor development

Select System Usage / Enter Index [4]: 2
```
{: screen}

Enter a number that represents the planned function.
In the example, `2` indicates a system for testing.

Accept more default values:

```text
Do you want to enable data and log volume encryption? [n]:
Enter Location of Data Volumes [/hana/data/<SID>]:
Enter Location of Log Volumes [/hana/log/<SID>]:
Restrict maximum memory allocation? [n]:
Apply System Size Dependent Resource Limits? (SAP Note 3014176) [y]:
```
{: screen}

Determine these passwords as set on the source system:

- `sapadm` password
- `<sid>adm` password
- System Database User `SYSTEM` password

Set the same passwords on the target system:

```text
Enter SAP Host Agent User (sapadm) Password: <Use the same password used on the source system>
Confirm SAP Host Agent User (sapadm) Password: <Use the same password used on the source system>
Enter System Administrator (<sid>adm) Password:  <Use the same password used on the source system>
Confirm System Administrator (<sid>adm) Password: <Use the same password used on the source system>
Enter System Administrator Home Directory [/usr/sap/<SID>/home]:
Enter System Administrator Login Shell [/bin/sh]:
Enter System Administrator User ID [1001]: <check that the user ID number matches the source system>
Enter ID of User Group (sapsys) [79]:  <Check that the GUID number matches the source system>
Enter System Database User (SYSTEM) Password: <Use the same password used on the source system>
Confirm System Database User (SYSTEM) Password: <Use the same password used on the source system>

Restart system after machine reboot? [n]:
Summary before execution
```
{: screen}

At the summary, you can check to make sure that the selections that you made for the installation are correct. Then, select "Y" to begin.
After about 20 minutes, you see the following message.

```text
Registering SAP HANA Database Components on Local Host...
- Deploying SAP Host Agent configurations...
Creating Component List...
SAP HANA Database System installed
Log file written to xxxxxx
```
{: screen}

#### Checking that SAP HANA is running and determining the version
{: #mig-db-hana-target-system-status}

Run the following `HDB proc` command to verify that all services started on the primary and secondary SAP HANA system.

```sh
sudo -i -u ${sid}adm -- HDB proc
```
{: pre}

#### SAP HANA version needs to be equal or greater than the primary server
{: #mig-db-hana-target-system-version}

To verify the SAP HANA database version, use the following command on both nodes.

```sh
sudo -i -u ${sid}adm -- HDB version
```
{: pre}

#### Initial backup of the MDC/SYSTEMDB SAP HANA database
{: #mig-db-hana-target-system-backup}

##### Backup SYSTEMDB
{: #mig-db-hana-target-system-systemdb-backup}

Add both the SYSTEMDB entry and the MDC to the HANA Studio Application. Or, If you have an SAP HANA Cockpit in your landscape, you can add the target system to your HANA Cockpit instead.
After both systems are added, complete an initial system backup.
On the SYSTEMDB entry, -> right click and select.

- Backup & Recovery
- Backup Up System Database
- Backup Type `Complete Data Backup`
- Destination `File`
- Backup Destination `/swrepo/backup/data/SYSTEMDB` make sure that this directory structure exists and is writable with user `${sid}adm`.
- Backup Prefix `COMPLETE_DATA_BACKUP_INITIAL_DDMMYYY`
Next
- `Review Backup Setup` and then select `Finish`

Make sure that the `SYSTEMDB@${SID}` backup completes successfully.

##### Backup MDC
{: #mig-db-hana-target-system-mdc-backup}

Backup & Recovery

- Backup Up Tenant Database
- Specify the Tenant Database `${sid}`.
Next
- Backup Type `Complete Data Backup`.
- Destination `File`.
- Backup Destination `/swrepo/backup/data/DB_${sid}` make sure that this directory structure exists and is writable with user `${sid}adm`.
- Backup Prefix `COMPLETE_DATA_BACKUP_INITIAL_DDMMYYY`.
Next
- `Review Backup Setup` and then select `Finish`.

Make sure that the backup of `DB_${sid}` completes without errors.

##### Check backup status
{: #mig-db-hana-target-system-check-backup-status}

On the SYSTEMDB entry, -> right click and select the following actions.

- Backup & Recovery
- Select Open `Backup Console`
- Select the tab `Backup Catalog`
- In the Database Field select `${sid}`for the MDC
- In the Database Field select `<SYSTEMDB>`for the SYSTEMDB

#### Optional check of the trace log files
{: #mig-db-hana-target-system-backup-trace-log}

The database trace is written to service specific files on operating system level. The trace directory is in the following location:

`/usr/sap/${sid}/HDB<inst>/<host>/trace/DB_${sid}/`

The following alias in the environment of the `${sid}adm` user allows to you quickly switch to the trace directory on the OS level:

`cdtrace`

The database trace files use the following naming convention:

`<service>_<host>.<port>.<counter>.trc`

In the context of dynamic tiering, a file with the following convention can exist (SAP Note 2871785):

`esserver_console_<host>.<port>.<counter>.trc`

- Example:

`indexserver_saphana01.30003.024.trc`
You can access these files either directly on the operating system level or in one of the following ways:

- `SAP HANA Studio -> Administration -> Diagnosis Files`
- `DBACOCKPIT -> Diagnostics -> Diagnosis Files`






## Migrating SAP S/4HANA by using SAP HANA System Replication
{: #sapmig-db-hana-replication}

### Pre-checks before you configure SAP HANA System Replication
{: #sapmig-db-hana-sr-prechecks}

Before you configure SAP HANA System Replication, a few prerequisites must be checked.
The described Steps are valid for Red Hat Enterprise Linux 8 (RHEL) and SUSE Enterprise Linux (SLES).
{: shortdesc}

#### Check the SAP HANA database user on the source system
{: #sapmig-db-hana-sr-user-prechecks}

Check with your SAP basis administration team or SAP HANA administrators, which SAP HANA database user is used to access the system.
Typically this user the `SYSTEM` user, or the SAP schema owner user if your SAP basis administration team implemented the SAP security advisories.

### SAP HANA landscape pre-steps for activating SAP HANA System Replication
{: #sapmig-db-hana-sr-oncloud-onprem-prechecks}

#### Set the environment variables on the primary and secondary SAP HANA systems
{: #sapmig-db-hana-sr-prepare-environment-variables}

To simplify the setup, prepare the following environment variables for `${sid}adm` on both nodes.
These environment variables are used in subsequent commands in the remainder of the examples.

On both nodes, run the following commands.
Remember that the variables must be the same on both systems, source and target.

```sh
export SID=<SID>                                 # SAP HANA System ID (uppercase)
export sid=<sid>                                 # SAP HANA System ID (lowercase)
export INSTNO=<INSTNO>                           # SAP HANA Instance Number
export DIR_INSTANCE=/usr/sap/${SID}/HDB${INSTNO} # "${sid}adm" home directory

export SiteOnPrem=<PrimarySiteName>        # HANA System Replication Site Name 1 - Migration from On-Prem - Source
export SiteOnCloud=<secondarySiteName>     # HANA System Replication Site Name 2 - Migration to On-Cloud - Target

export NODE1=<Hostname 1>                  # Hostname of On-Prem Server
export NODE2=<Hostname 2>                  # Hostname of IBM Cloud PowerVS Instance
```
{: codeblock}

#### Make sure that SAP HANA is running on both systems
{: #sapmig-db-hana-sr-oncloud-onprem-started}

As the operating system user `${sid}adm`, the command `HDB proc` can be used to verify that all services are started.

Run the following command on both systems, primary and secondary SAP HANA server.

```sh
sudo -i -u ${sid}adm -- HDB proc
```
{: pre}

#### SAP HANA version must be equal or greater than the primary server
{: #sapmig-db-hana-sr-oncloud-onprem-versioning}

Run the following command on each node to determine the SAP HANA server version.

```sh
sudo -i -u ${sid}adm -- HDB version
```
{: pre}

The target system version must be equal or larger compared with the source system version.
The only exception for the version is for an `Active/Active` read enabled configuration, here the HDB version must be identical on source and target system.

- Therefore, make sure that the system configuration is identical on both the source and target servers. Then, compare the settings in the ini-files on both systems.

- For a scale-out configuration, make sure that the number of worker nodes (scale-out) and roles are identical on both the source and target servers.

- The same `${sid}` and `instance numbers` must be used on both systems.

- Back up `PKI SSFS .key and the .dat files` from the primary and secondary systems.

- Copy existing PKI keys from the primary to the secondary system.

To make sure that you can recover to the original installed state, if needed, back up the existing keys on both primary and secondary systems.

```sh
sudo -i -u ${sid}adm -- cp -p /usr/sap/${SID}/SYS/global/security/rsecssfs/data/SSFS_${SID}.DAT /usr/sap/${SID}/SYS/global/security/rsecssfs/data/SSFS_${SID}.DAT_<hostname>
```
{: pre}

After the backup of the existing PKI SSFS `.key` and `.dat` files is done, you now need to copy the PKI SSFS `.key` and `.dat` files from primary system to the target system.

The SAP HANA 2.0 data and log transmission channels for the replication process require authentication by using the system *PKI SSFS* storage certificate files.

- [SAP Note 2369981 - Required configuration steps for authentication with HANA System Replication](https://me.sap.com/notes/2369981){: external}

The system *PKI SSFS* storage certificate files are stored in `/usr/sap/${SID}/SYS/global/security/rsecssfs/` in subdirectories `data` and `key`.

On NODE2, run the following commands to copy files `SSFS_${SID}.DAT` and `SSFS_${SID}.KEY` from NODE1.

As `${sid}adm` user, run the following two commands on NODE2.

```sh
scp ${NODE1}:/usr/sap/${SID}/SYS/global/security/rsecssfs/data/SSFS_${SID}.DAT /usr/sap/${SID}/SYS/global/security/rsecssfs/data/SSFS_${SID}.DAT
```
{: pre}

```sh
scp ${NODE1}:/usr/sap/${SID}/SYS/global/security/rsecssfs/key/SSFS_${SID}.KEY /usr/sap/${SID}/SYS/global/security/rsecssfs/key/SSFS_${SID}.KEY
```
{: pre}

The copied *PKI SSFS* storage certificates on NODE2 become active during the start of the SAP HANA system.

#### Check that the configuration parameter `log_mode` is set to normal
{: #sapmig-db-hana-sr-oncloud-onprem-logmode}

Make sure that the configuration parameter `log_mode` is set to *normal* in the **persistence** section of the `global.ini` on both the primary and secondary SAP HANA Servers.

Run the following command on both systems to verify the `log_mode` setting.

```sh
sudo -i -u ${sid}adm -- grep -i 'log_mode' /usr/sap/${SID}/HDB${INST}/exe/config/global.ini
```
{: pre}

The following output is expected.

```text
log_mode=normal
```
{: screen}

#### Register the primary server first
{: #sapmig-db-hana-sr-onprem-firstreg}

On the primary SAP HANA system, run the following command to register this node as the `primary` for SAP HANA System Replication.

```sh
sudo -i -u ${sid}adm -- hdbnsutil -sr_enable --name={SiteOnPrem}
```
{: pre}

The following output is expected.

```text
nameserver is active, proceeding ...
successfully enabled system as system replication source site
done.
```
{: screen}

#### Check whether the primary system is registered
{: #sapmig-db-hana-sr-onprem-firstreg-confirm}

Verify that the primary system is successfully registered by using the following command.

```sh
sudo -i -u ${sid}adm -- hdbnsutil -sr_state
```
{: pre}

The following output is expected.

```text
System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~

online: true

mode: primary
operation mode: primary
site id: 1
site name: SiteOnPrem

is source system: true
is secondary/consumer system: false
has secondaries/consumers attached: false
is a takeover active: false
is primary suspended: false

Host Mappings:
~~~~~~~~~~~~~~


Site Mappings:
~~~~~~~~~~~~~~
SiteCloud (primary/)

Tier of SiteCloud: 1

Replication mode of SiteCloud: primary

Operation mode of SiteOnPrem :


Hint based routing site:
done.
```
{: screen}

#### Make sure that SAP HANA is not active on the secondary site
{: #sapmig-db-hana-sr-oncloud-secondarysite-stop}

The secondary site must not be an active SAP HANA server.
Stop SAP HANA database services by using the following command.

```sh
sudo -i -u ${sid}adm -- HDB stop
```
{: pre}

The following output is expected.

```text
hdbdaemon will wait maximal 300 seconds for NewDB services finishing.
Stopping instance using: /usr/sap/${SID}/SYS/exe/hdb/sapcontrol -prot NI_HTTP -nr 10 -function Stop 400

10.08.2023 10:32:07
Stop
OK
Waiting for stopped instance using: /usr/sap/${SID}/SYS/exe/hdb/sapcontrol -prot NI_HTTP -nr 10 -function WaitforStopped 600 2


10.08.2023 10:32:51
WaitforStopped
OK
hdbdaemon is stopped.
```
{: screen}

#### Register the secondary system
{: #sapmig-db-hana-sr-oncloud-secondarysite-reg}

Now register the secondary system.

```sh
sudo -i -u ${sid}adm -- hdbnsutil -sr_register --name=<secondarySiteName> --remoteHost=<primary_host> --remoteInstance=<primary_systemnr>
--replicationMode=[sync|syncmem|async]--operationMode=[delta_datashipping|logreplay|logreplay_readaccess]
```
{: pre}

For example, if you use

- `SiteOnCloud` as the secondary site name
- `syncmem` as replication mode and
- `logreplay` as operation mode

The last command looks like the following example.

```sh
$ sudo -i -u ${sid}adm -- hdbnsutil -sr_register --name=SiteOnCloud --remoteHost=<primary_host> --remoteInstance=<primary_systemnr>  --replicationMode=syncmem --operationMode=logreplay
```
{: codeblock}

The following output is expected.

```text
Thu 10 Aug 10:36:13 CEST 2023
adding site ...
collecting information ...
updating local ini files ...
done.
```
{: screen}

#### Troubleshoot hdbnsutil errors with SELinux enabled
{: #sapmig-db-hana-sr-rhel-selinux-issue}

If security-enhanced Linux (SELinux) is enabled, the output of `hdbnsutil` is not as expected.
You can see one of the following two symptoms.

- `Command is not recognized` error message
- Usage information displayed

SELinux, when set to `enforcing`, prevents the command `hdbnsutil` from restarting the saphostagent in the `${sid}adm` user context.
You can either add proper SELinux security policies or as SAP recommends. Then, disable SELinux.

Check the current SELinux status with the following command.

```sh
sestatus
```
{: pre}

The following output is an example.

```text
SELinux status:                 enforcing
```
{: screen}

If `sestatus` command returns with `enforcing`, then commands even when run with root privileges can be blocked, depending on security policy.

To disable SELinux temporarily, run the following command.

```sh
sudo setenforce 0
```
{: pre}

SELinux is now temporarily disabled until the next restart.

Now check with `sestatus` again, the status shows `disabled`.

Check whether the saphostagent process is running with the following command.

```sh
sudo ps -ef | grep -i host
```
{: pre}

If output is empty and no process is displayed, manually restart the saphostagent.

```sh
sudo -i -u ${sid}adm -- /usr/sap/hostctrl/exe/saphostexec -restart /usr/sap/hostctrl/exe/host_profile
```
{: pre}

### Check the state on both sides of the SAP HANA System Replication
{: #sapmig-db-hana-sr-onprem-state}

#### Check the primary system state
{: #sapmig-db-hana-sr-onprem-state-primary}

Verify the system replication state on the primary node.
Run the following command on the primary server:

```sh
sudo -i -u ${sid}adm -- hdbnsutil -sr_state
```
{: pre}

The following output is expected.

```text
System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~

online: true

mode: primary
operation mode: primary
site id: 1
site name: SiteOnPrem_hostname

is source system: true
is secondary/consumer system: false
has secondaries/consumers attached: true
is a takeover active: false
is primary suspended: false

Host Mappings:
~~~~~~~~~~~~~~

<SiteOnCloud_hostname> -> [SiteOnPrem_hostname] <SiteOnPrem_hostname_hostname>
<SiteOnCloud_hostname> -> [SiteOnCloud] <SiteOnPrem_hostname_hostname>


Site Mappings:
~~~~~~~~~~~~~~
SiteOnPrem_hostname (primary/primary)
    |---SiteOnCloud (syncmem/logreplay)

Tier of SiteCloud: 1
Tier of SiteOnPrem_hostname: 2

Replication mode of SiteOnPrem_hostname: primary
Replication mode of SiteOnCloud: syncmem

Operation mode of SiteOnPrem_hostname: primary
Operation mode of SiteOnCloud logreplay

Mapping: SiteOnPrem_hostname -> SiteOnCloud

Hint based routing site:
done.
```
{: screen}

#### Check the secondary system state
{: #sapmig-db-hana-sr-onprem-state-secondary}

Now check the system replication state on the second node.
Run the same command on the second server.

```sh
sudo -i -u ${sid}adm -- hdbnsutil -sr_state
```
{: pre}

The following output is expected.

```text
System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~

online: false

mode: syncmem
operation mode: unknown
site id: 2
site name: SiteOnCloud

is source system: unknown
is secondary/consumer system: true
has secondaries/consumers attached: unknown
is a takeover active: false
is primary suspended: false
is timetravel enabled: false
replay mode: auto
active primary site: 1

primary masters: <SiteOnPrem_hostname_hostname>
done.
```
{: screen}

#### Restart the secondary server
{: #sapmig-db-hana-sr-oncloud-restart}

So far both SAP HANA servers are configured as replication partners.
Now restart the secondary SAP HANA server to complete the replication setup.

Run the following command on the secondary server.

```sh
sudo -i -u ${sid}adm -- HDB start
```
{: pre}

The following output is expected.

```text
StartService
OK

Starting instance using: /usr/sap/${SID}/SYS/exe/hdb/sapcontrol -prot NI_HTTP -nr 10 -function StartWait 2700 2
OK


10.08.2023 10:38:47
Start
OK

10.08.2023 10:40:17
StartWait
OK
```
{: screen}

Check `HDB info` or `HDB proc` on the secondary side to confirm that SAP HANA is running again.
When successful, run the `sr_state` command on the primary system.

```text
sudo -i -u ${sid}adm -- hdbnsutil -sr_state
```
{: pre}

The following output is expected.

```sh
System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~

online: true

mode: primary
operation mode: primary
site id: 1
site name: SitePrem

is source system: true
is secondary/consumer system: false
has secondaries/consumers attached: true
is a takeover active: false
is primary suspended: false

Host Mappings:
~~~~~~~~~~~~~~

<SiteOnCloud_hostname> -> [SiteOnPrem] <SiteOnPrem>
<SiteOnCloud_hostname> -> [SiteOnCloud] <SiteOnPrem>


Site Mappings:
~~~~~~~~~~~~~~
SiteOnPrem (primary/primary)
    |---SiteOnCloud (syncmem/logreplay)

Tier of SiteOnprem : 1
Tier of SiteOnCloud: 2

Replication mode of SiteOnPrem: primary
Replication mode of SiteOnCloud: syncmem

Operation mode of SiteOnPrem: primary
Operation mode of SiteOnCloud: logreplay

Mapping: SiteOnprem_hostname -> SiteOnCloud

Hint based routing site:
done.
```
{: screen}

#### Check replication status
{: #sapmig-db-hana-sr-onprem-oncloud-replicationstate}

After the secondary system is configured and SAP HANA is started on the secondary server, the replication process automatically starts synchronizing data with a `full replica`.
You can verify the initial replication on the primary server and watch the current completion status of the full replication action.

Run the Python script with the following command.

```sh
sudo -i -u ${sid}adm -- python ${DIR_INSTANCE}/exe/python_support/systemReplicationStatus.py
```
{: pre}

The following output is expected.

```text
|Database |Host     |Port  |Service Name |Volume ID |Site ID |Site Name |Secondary |Secondary |Secondary |Secondary  |Secondary     |Replication |Replication |Replication    |Secondary    |
|         |         |      |             |          |        |          |Host      |Port      |Site ID   |Site Name  |Active Status |Mode        |Status      |Status Details |Fully Synced |
|-------- |-------- |----- |------------ |--------- |------- |--------- |----------|--------- |--------- |---------- |------------- |----------- |----------- |-------------- |-------------|
|SYSTEMDB |<NODE1>  |31001 |nameserver   |        1 |      1 |SiteOnPrem|  <NODE2> |    31001 |        2 |SiteOnCloud|YES           |SYNCMEM     |ACTIVE      |               |         True|
|S4H      |<NODE1>  |31007 |xsengine     |        2 |      1 |SiteOnPrem|  <NODE2> |    31007 |        2 |SiteOnCloud|YES           |SYNCMEM     |ACTIVE      |               |         True|
|S4H      |<NODE1>  |31040 |docstore     |        5 |      1 |SiteOnPrem|  <NODE2> |    31040 |        2 |SiteOnCloud|YES           |SYNCMEM     |ACTIVE      |               |         True|
|S4H      |<NODE1>  |31003 |indexserver  |        3 |      1 |SiteOnPrem|  <NODE2> |    31003 |        2 |SiteOnCloud|YES           |SYNCMEM     |ACTIVE      |               |         True|
|S4H      |<NODE1>  |31011 |dpserver     |        4 |      1 |SiteOnPrem|  <NODE2> |    31011 |        2 |SiteOnCloud|YES           |SYNCMEM     |ACTIVE      |               |         True|

status system replication site "1": ACTIVE
overall system replication status: ACTIVE

Local System Replication State
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

mode: PRIMARY
site id: 1
site name: SiteOnPrem_hostname
```
{: screen}

#### Four methods to check the system replication status
{: #sapmig-db-hana-sr-replication-status-checks}

##### Option 1. landscapeHostConfiguration.py
{: #sapmig-db-hana-sr-landscapehostconfiguration}

The first option uses the Python script `landscapeHostConfiguration.py` for a server point of view.
This script displays a status line per SAP HANA server system.

Run the following command.

   ```sh
   sudo -i -u ${sid}adm -- python ${DIR_INSTANCE}/exe/python_support/landscapeHostConfiguration.py
   ```
   {: pre}

Make sure that each server that is listed in the output shows `OK` in the host status column.

##### Option 2. systemReplicationStatus.py
{: #sapmig-db-hana-sr-systemreplicationstatus}

The second alternative option uses the Python script `systemReplicationStatus.py` for a database view of the SAP HANA system replication.
This script displays one status line for each database and an overall status after the database table.

Run the Python script with the following command.

   ```sh
   sudo -i -u ${sid}adm -- python python ${DIR_INSTANCE}/exe/python_support/systemReplicationStatus.py
   ```
   {: pre}

Make sure that each database listed shows an `ACTIVE` in replication status column.
The expected script output contains the following line:

```text
overall system replication status: ACTIVE
```
{: screen}

##### Option 3. hdbcons
{: #sapmig-db-hana-sr-hdbcons}

Check the detailed status of the system replication with the `hdbcons`command and run as `${sid}adm` user.
This third option is a technical per server and per service view.

Run the SAP HANA DB Management Client Console `hdbcons` with the following command.

   ```sh
   sudo -i -u ${sid}adm -- hdbcons -e hdbindexserver "replication info"
   ```
   {: pre}

##### Option 4. Sql script
{: #sapmig-db-hana-sr-sql-script}

The fourth alternative uses an SQL statement that can be run, for example, in SAP HANA studio or cockpit.
This option is a hosts-per-site-view of SAP HANA system replication.

Check by running the following SQL statement.

   ```sql
   select host, SECONDARY_HOST, PORT, SITE_NAME, SECONDARY_SITE_NAME, REPLICATION_MODE, REPLICATION_STATUS, REPLICATION_STATUS_DETAILS,SECONDARY_ACTIVE_STATUS from M_SERVICE_REPLICATION;
   ```
   {: pre}

Check especially columns `REPLICATION_STATUS` and `REPLICATION_STATUS_DETAILS` in the SQL output.

#### Post replication completion
{: #sapmig-db-hana-sr-onprem-oncloud-post-replication}

Before you disable the replication setup, check the trace logs for any inconsistencies or anomalies after the replication action is performed.
After the replication completes, the database contains all active services on the primary system only.
But you can still examine the trace logs for any inconsistencies or issues.

### Checking the database trace files
{: #sapmig-db-hana-sr-corruptions-trace}

The database trace is written to service specific files on operating system level. The trace directory is located here:
`/usr/sap/<SID>/HDB<inst>/<host>/trace/DB_<SID>/`

The following alias in the `${sid}adm` user environment allows the `${sid}adm` user to quickly change to the trace directory on the operating system level:

```sh
cdtrace
```
{: pre}

The database trace files have the following naming convention:
`<service>_<host>.<port>.<counter>.trc`

In the context of dynamic tiering also a file with the following convention can exist (SAP Note 2871785):
`esserver_console_<host>.<port>.<counter>.trc`

Example:
`indexserver_saphana01.30003.024.trc`

You can access database trace files in three ways:

- Directly on the operating system level
- `SAP HANA Studio -> Administration -> Diagnosis Files`
- `DBACOCKPIT -> Diagnostics -> Diagnosis Files`

### SAP HANA System Replication resources
{: #sapmig-db-hana-sr-resources}

For more information, see the following links:

- [SAP Note 1999880 - HANA System Replication FAQ](https://me.sap.com/notes/1999880){: external}
- [SAP Note 11969700 - SQL Statement Collection for SAP HANA](https://me.sap.com/notes/1969700){: external}
- [SAP Note 3357978 - Configuring SAP HANA Multi Target System Replication](https://me.sap.com/notes/3357978){: external}
- [SAP HANA Replication Setups](https://help.sap.com/docs/SAP_HANA_PLATFORM/4e9b18c116aa42fc84c7dbfd02111aba/2e6c71ab55f147e19b832565311a8e4e.html){: external}
- [SAP HANA System Replication](https://help.sap.com/docs/SAP_HANA_PLATFORM/4e9b18c116aa42fc84c7dbfd02111aba/afac7100bc6d47729ae8eae32da5fdec.html){: external}
- [SAP HANA multitarget System Replication](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/ca6f4c62c45b4c85a109c7faf62881fc.html){: external}
- [SAP HANA System Replication](https://help.sap.com/doc/c81e9406d08046c0a118c8bef71f6bdc/2.0.07/en-US/SAP_HANA_System_Replication_Guide_en.pdf){: external}
- [SAP Help Portal Documentation HSR HANA 2.0 SP07](https://help.sap.com/docs/SAP_HANA_PLATFORM/4e9b18c116aa42fc84c7dbfd02111aba/afac7100bc6d47729ae8eae32da5fdec.html){: external}
