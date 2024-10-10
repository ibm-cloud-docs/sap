---
copyright:
  years: 2024
lastupdated: "2024-10-10"

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, on-prem, on premises, Hybrid Cloud, Migration, Linux, Redhat, RHEL, SuSE, backup, restore

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Migrating SAP ECC on Oracle to IBM Power Virtual Server
{: #sapmig-db-oracle}

## Preparation Steps on Source System
{: #sapmig-db-oracle-source-prep}

### Target Audience and Intent
{: #sapmig-db-oracle-target-audience-intent}

This documentation presents Oracle Database Administrators (DBA's) with two options, both based on Oracle RMAN to migrate AIX-based Oracle databases from IBM Power to IBM Power Virtual Server (IBM PowerVS).

The target audience consists of solution and infrastructure architects and Oracle database administrators.

The intent is to present representative steps to execute plans and procedures to perform Oracle database migrations, recognizing that each migration scenario presents unique challenges in terms of deployment, configuration, and available resources.

In this documentation, the generic term *Discovery* indicates that the customers own Oracle DBA's responsibility to *Discover* and *document* their current Oracle/SAP infrastructure. This is useful should the customer have the need to raise incidents to report issues.

### Scope and Coverage
{: #sapmig-db-oracle-scope-coverage}

The following procedures assume availability of sufficient network connectivity to support data transfer and/or data replication as required.

A discussion of alternative procedures by using the optional mobile or container solutions such as *Seagate Lyve Mobile Solution* in combination with IBM Cloud Object Storage or *IBM Aspera Connect* to transfer On-Premises backups/database files to IBM PowerVS infrastructure is interspersed.  Links to detailed information about this service may be found in the IBM Cloud online documentation or in the folling links.

* [Seagate Lyve Mobile Solution](https://www.seagate.com/de/de/manuals/lyve-mobile-cloud-service/import-to-ibm-cloud/){: external}
* [Getting started with IBM Cloud Object Storage](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-getting-started-cloud-object-storage){: external}
* [IBM Aspera Connect](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-aspera){: external}

Task steps that are required will vary per implementation. Refer to cited Oracle documentation for details regarding the execution of specific commands.

For this migration procedure we use User Concept - Oracle Standard as outlined in the following documented SAP Link.

Please note that an SAP *S*-user access is required.

* [SAP Note 1915323 - OS User Concept for SAP NetWeaver for 12c and higher](https://me.sap.com/notes/1915323){: external}




### Disclaimer
{: #sapmig-db-oracle-ibm-disclaimer}

Any attempt to execute these procedures will be performed in context with Customer’s established procedures for operating and maintaining nonproduction and/or production systems.  Customer takes customary actions to ensure system availability for maintenance and/or reconfiguration as required, and schedule downtime as required.

The Customer is responsible for reviewing these representative procedures in the context of their particular environment and adjusting as required.

The Oracle Database migration options that are described are not necessarily specific to IBM PowerVS migrations. Oracle technical staff should recognize the procedures that are used and understand that not every technical detail or consideration have been explicitly identified.
The Oracle Database Admins executing the procedures are expected to understand the full scope of Oracle database backup and recovery methods - including those details not explicitly stated.

### Discovery and Migration Option Selection.
{: #sapmig-db-oracle-discovery-migration}

#### Discovery Process
{: #sapmig-db-oracle-discovery-process}

This document assumes that a detailed discovery has been previously collected:

* Business, technical, functional, nonfunctional requirements of the Oracle database being migrated (including access and availability requirements)
* Installation configuration, HW/SW inventory, and sizing information related to the source database and the underlying platform.
    * Collect relevant metrics related to sizing, change rate, service level requirements, tolerance for migration downtime, capacity of networks to support data transfer from source to target, and so on.
    * The target environment has been evaluated and deployed consistent with source system capacity/performance/availability as found in the discovery process.


#### Migration Options Covered By this Document.
{: #sapmig-db-oracle-discovery-options}

The following flowchart illustrates the two options that are currently covered by this document:

* *Option 1*: Consistent RMAN backup from source, with generated files to be used by the RMAN Duplicate Database procedure to restore to IBM PowerVS.
* *Option 2*: Inconsistent RMAN backup + archived redo logs (or level 0 + incremental backups) from source, with backup files to be used by the RMAN Restore/Recover Database procedure to restore to IBM PowerVS.
* *Option 3*: Leveraging Oracle Data Guard, currently being researched and will be the recommended method if it is the same platform – create a mirror and as soon as it is in sync you can switchover with just minutes of database downtime. The online documentation will be updated as soon as the testing and assessment phases have been completed.

![Figure 1. Collect relevant metrics](/images/sap-power-virtual-server-mig-db-oracle-mig-options.png "Image showing Preparation - Collect relevant metrics"){: caption="Collect relevant metrics" caption-side="bottom"}

#### Selected Considerations
{: #sapmig-db-oracle-selected-considerations}

Customer requirements and technical conditions affect the decision to select from the presented options.

1. Service Level Requirements, particularly when it comes to Restore Point Objectives and Restore Time Objectives.
    * *Option 1*: As presented assumes that the Customer is comfortable with a considerable service delay (RTO = *"Days"*) before the source database is migrated to the target database. This expectation is often the case for nonproduction workloads but rarely for production databases.
    * *Option 2*: As presented assumes that the Customer is comfortable with a service interruption in terms of *"Hours"*. This expectation is often the case for nonproduction workloads and some production databases.
    * *Option 3*: (Not yet presented in this document), incorporating Oracle Data Guard database synchronization, features a potential cutover of service from source to target in minutes.
2. Database Size, Network Throughput and Reliability.
    * Transporting backups of a large database across a network takes time. Consider the following transfer example of 600 GB of backups across an end-to-end network connection where available protocols and bandwidth support 80 megabytes/second throughput. At this rate, it will take 125 minutes to transfer the data. If the database backups are much bigger or if the network connection throughput is less, more transfer time will be required. Unreliable networks can disrupt the transfer, requiring you to restart the transfer process, incurring delay.

        * Customers can benefit from specialized transfer tools that compress data before/during transfer, use high-throughput protocols and decompress data after transfer.
        * Access to IBM’s Aspera, which can greatly accelerate data transfers from on-premise to IBM PowerVS locations is advantageous.
3. Availability of Skills to Execute.
    *	Options provided in this document require experienced Database Administrator skills and the ability to work with infrastructure teams to migrate database content to a new target database on Power Virtual Server within an IBM PowerVS workspace.

Any migration procedures a Customer elects to use should be executed in the context of a detailed, well-rehearsed transfer and cutover plan.


### Technical Details and Considerations
{: #sapmig-db-oracle-details-considerations}

The RMAN BACKUP command supports backing up the following types of files:

1. Data files and control files.
1. Server parameter file.
1. Archived redo logs
1. RMAN backups

RMAN does not backup these files associated with the Oracle database.
{: important}

Although the database depends on other types of files, such as network configuration files, password files, and the contents of the Oracle home, you cannot back up these files with RMAN.
Likewise, some features of Oracle Database, such as external tables, might depend upon files other than the data files, control files, and redo log. Also needed for this procedure is a parameter file from the source database, as well the files listed that follow:

1. Oracle database parameter file: `init<SID>.ora`
1. TNS network configuration files: (e. g. `listener.ora`, `tnsnames.ora`)
1. Oracle database password file, if this file exists

RMAN does not back up these files. So you need to make sure that these are included on your backup/recovery action,
best practice would be to copy those to a directory on the same mount as the RMAN database backups.

When you execute the BACKUP command in RMAN, the output is always either one or more backup sets or one or more image copies. A backup set is an RMAN-specific proprietary format. Whereas an image copy is a bit-for-bit copy of a file. By default, RMAN creates backup sets. This document only deals with backup sets.

* [Backup set explanation](https://docs.oracle.com/en/database/oracle/oracle-database/18/bradv/glossary.html#GUID-6AA43E7D-9946-4C41-A62F-01E5BACEB8A7){: external}
* [Image Copy explanation](https://docs.oracle.com/en/database/oracle/oracle-database/18/bradv/glossary.html#GUID-4CCAB7DD-FEA7-4237-AF2C-304BA3BD52B1){: external}


#### Assumptions
{: #sapmig-db-oracle-assumptions}

This document assumes that :

1. If the source database is a production system it likely won’t be shut down for a full backup, in which case RMAN incremental backups will be used.
1. No changes to the source database (schema and configuration) are expected by executing this procedure.
1. Database will be migrated from source to target using Oracle RMAN options: Duplicate database, or backup/restore/recover database.
1. The target environment has Oracle homes pre-installed with a version matching the source instance being migrated.
1. RMAN duplicate, restore/recovery provide the option to modify the database data file location, but this option is not covered in this document. The assumption is made that the location of database files, either AIX JFS2 file systems or Oracle ASM disk groups, is identical between the source and the target environment.
1. The target OS will be either :
    * An exact copy (an OVA or “Open Virtual Appliance” archive file) of the source operating system generated by using an mksysb procedure, or
    * A freshly deployed version of AIX that is supported by IBM and certified by Oracle to support the version of Oracle to be instantiated.

Documentation of a mksysb procedure might be found at:

* [Restoring AIX](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-restoring-aix-mksysb-image){: external}


### Preparation
{: #sapmig-db-oracle-preparation-source}

Discovery has confirmed or identified the following preperation steps.

#### Documentation of the current Source Database Sizing
{: #sapmig-db-oracle-prep-sizing}

Document current sizing and performance metrics related to compute, IOPS, and storage, of one or more source Oracle instances.
* The target IBM PowerVS Oracle instance should be constructed to at least match the sizing of the existing system.
* Very important on the target system to select disk tier and disk capacity to meet IOPS requirements.
* This discovery process should include execution of a performance test tool against the source database to capture metrics for future reference and comparison to target system deployment.

#### Confirm a Recent, Successful Backup Exists
{: #sapmig-db-oracle-prep-backup}

Confirm, by using your standard backup tools, that a recent, successful, full backup of the source Oracle system exists.
You must be able to completely restore the source system.

* If you cannot confirm it, the migration/discovery team should review RMAN backup procedures.
* In addition, the discovery team should work with customer DBA's or SMEs to validate database integrity and/or identify any existing corrupt blocks or schema problems.

#### Document Your Source Database
{: #sapmig-db-oracle-prep-doc-source-db}

Document the location of source database instances and known credentials for DBA administration access.


#### Document RMAN Configuration
{: #sapmig-db-oracle-prep-rman}

Document the current Oracle Recovery Manager (RMAN) configuration that is used to perform backups, per instance.

A simple way to obtain this information is by performing the following steps:

Use ssh to connect to the Oracle server.
```sh
ssh oracle@<hostname>
```
{: pre}

Check the variable `$ORACLE_SID` has been set.
```sh
echo $ORACLE_SID
```
{: pre}

Usually the SID value is shown. In case no value is shown, set one manually with this command:
```sh
setenv ORACLE_SID <SID value>
```
{: pre}

Please replace `<SID value>` with your SID value.
{: note}

Connect to the source database using the `rman` command:
```sh
rman target /
```
{: pre}

A typical output will look like this :
```text
Recovery Manager: Release 19.0.0.0.0 - Production on Thu May 2 13:24:05 2024
Version 19.22.0.0.0

Copyright (c) 1982, 2019, Oracle and/or its affiliates.  All rights reserved.
connected to target database: Exx (DBID=xxxxxxxx)
```
{: screen}

Display the rman configuration settings using:
```text
RMAN> SHOW ALL;
```
{: codeblock}


Store and/or document output.

Exit the RMAN session by typing:
```text
RMAN > exit;
```
{: codeblock}

Just for your reference, a typical `SHOW ALL` output will look like this:
```text
using target database control file instead of recovery catalog
RMAN configuration parameters for database with db_unique_name EXX are:
CONFIGURE RETENTION POLICY TO REDUNDANCY 1; # default
CONFIGURE BACKUP OPTIMIZATION OFF; # default
CONFIGURE DEFAULT DEVICE TYPE TO DISK; # default
CONFIGURE CONTROLFILE AUTOBACKUP ON; # default
CONFIGURE CONTROLFILE AUTOBACKUP FORMAT FOR DEVICE TYPE DISK TO '%F'; # default
CONFIGURE DEVICE TYPE DISK PARALLELISM 8 BACKUP TYPE TO BACKUPSET; # default
CONFIGURE DATAFILE BACKUP COPIES FOR DEVICE TYPE DISK TO 1; # default
CONFIGURE ARCHIVELOG BACKUP COPIES FOR DEVICE TYPE DISK TO 1; # default
CONFIGURE MAXSETSIZE TO UNLIMITED; # default
CONFIGURE ENCRYPTION FOR DATABASE OFF; # default
CONFIGURE ENCRYPTION ALGORITHM 'AES128'; # default
CONFIGURE COMPRESSION ALGORITHM 'BASIC' AS OF RELEASE 'DEFAULT' OPTIMIZE FOR LOAD TRUE ; # default
CONFIGURE RMAN OUTPUT TO KEEP FOR 7 DAYS; # default
CONFIGURE ARCHIVELOG DELETION POLICY TO NONE; # default
CONFIGURE SNAPSHOT CONTROLFILE NAME TO '/oracle/EXX/19/dbs/snapcf_EXX.f'; # default
```
{: screen}

#### Verify Disk Space
{: #sapmig-db-oracle-prep-disk-space}

Confirm that enough disk space is available, formatted, and mounted to accept the compressed *migration* database backup sets.
* Best performance is obtained if the disk space is local to the instance being backed up.
* Backing up database directly to Cloud Object Storage can introduce delays in backup execution due to comparative slowness of the network.
* This procedure will perform a backup, applying medium compression with the following considerations:
    * Compression results in backup file storage that takes approximately 25% of the space consumed by the Oracle database.
    * Local file system capacity should be sufficient to store the entire backup set (or sets) required to bring the target database to the restore point desired.
    * Target system requires access to the backup sets, via one of the following options:
        * A local filesystem to which the backup sets will be copied.
        * Installation of Aspera Agent on the On-Premise landscape,
        * An NFS direct mount to the Cloud Object Storage device or if using a Seagate Lyve Mobile transfer device. This does not yield the best performance and there have been other observations in the past.

Depending on network/access speed, it can be desirable to copy files from NFS mount to local storage.
{: note}

#### File System for Backup Sets on Source
{: #sapmig-db-oracle-prep-fs-bk-source}

A dedicated local file system e. g. `/backup/rman` for the migration backup sets is referenced.
As alternative option, a direct restore/recovery from the NFS-mounted Seagate Lyve Mobile device was also successfully tested using 10GbE and 100 GbE.

Perform the following commands to confirm available space for backups:
```sh
df -g /backup/rman
```
{: pre}

#### File System for Backup Sets on Target
{: #sapmig-db-oracle-prep-fs-bk-target}

The database duplication or restore/recover procedures require access to the set of backup files, via one of the following options:

* A local filesystem to which the backup sets will be copied.
* An NFS mounted to a Seagate Lyve Mobile transfer device, or
* An NFS mounted to IBM Cloud Object Storage to which the backup files have already been copied from the Seagate device.






## Back up the Source Oracle Database by using RMAN
{: #sapmig-db-oracle-backup}

In this section we cover the generation of the RMAN backup set presenting two options.
{: shortdesc }

### Reference
{: #sapmig-db-oracle-backup-reference}

For more details, please consult the following document.
[Oracle Database Backup and Recovery - February 2024](https://docs.oracle.com/en/database/oracle/oracle-database/19/rcmrf/database-backup-and-recovery-reference.pdf){: external}

### Assumptions
{: #sapmig-db-oracle-backup-assumptions}

* *Option 1* procedure assumes that a consistent *offline backup* will be taken of the source database.
* *Option 2* procedure requires that the source database is in *ARCHIVELOG* mode before the backup procedure is performed.

### Considerations
{: #sapmig-db-oracle-rman-considerations}

Customer should balance how parallelism and/or compression is applied (and associated resources that are allocated to support) with requirements for database availability and performance while backups are being performed.

Consider applying *section size* to backup configuration. Without specifying `section size` that you can end up with very few very large files which makes it challenging to handle or restart in case of a transfer failure. Parallelism at restore is also limited to the number of backup files available to process concurrently.

Using a `good section size` allows to control the size of each backup file that is generated and thereby also influence how many files are generated which can then be processed in parallel during restore in IBM PowerVS.

Note that Medium compression requires the Oracle Advanced Compression License. Basic compression is *good*, but significantly slower and achieves lower compression rates. High compression, is VERY CPU intensive on the compression side and only provides limited compression benefit as compared to Medium.
High compression also requires the Advanced Compression License.

Incremental backup, without database block change tracking activated, performs a full scan of all data files, which translates into a high reading workload.
Although enabling block change tracking is not expected to impact the performance of a running DB, testing should be performed in the Customer environment to validate this.

### Network Transfer - Standard Protocols versus IBM Aspera
{: #sapmig-db-oracle-standard-protocols-aspera}

A slower version of data transfer is using standard protocols such as scp/sftp. Backup files can be transfered either directly to an IBM AIX LPAR in PowerVS, or to IBM Cloud Object Storage (COS). Using scp/sftp with IBM COS assumes that you are using an IBM FileManage Gateway service or have installed and configured a sftp server within or next to the target IBM PowerVS environment to receive the transfer.

The faster option is using IBM’s high-performance Aspera product for data transfer. In many situations, IBM Aspera has been shown to transfer data several times faster than traditional TCP-based protocols.

Documentation for IBM Aspera can be found here:

[IBM Aspera Technologies - IBM Cloud](/docs/power-iaas?topic=power-iaas-additional-migration-strategies-power#aspera-technologies)

This reference also contains the [Accelerated network transfer migration guide](https://cloud.ibm.com/media/docs/downloads/power-iaas/accelerated_migration.pdf){: external}.
{: hint}

### Backup Procedure Options
{: #sapmig-db-oracle-backup-procedures}

![Figure 2. IBM Aspera backup procedure](/images/sap-power-virtual-server-mig-db-oracle-mig-timings.png "Image showing IBM Aspera backup procedure"){: caption="IBM Aspera backup procedure" caption-side="bottom"}

Procedures specific to both options are presented now.

### RMAN Option 1 - Offline Backup / Duplicate Database
{: #sapmig-db-oracle-rman-option1-aspera}

The following procedure is executed with the database offline and will produce a full consistent backup.

#### Application and Database Shutdown
{: #sapmig-db-oracle-rman1-shutdown}

Use standard operational procedures to shut down the SAP system and the Oracle database before performing the backup procedure.

When using the multitenant architecture, you must connect to the root container database (CDB) and the backups include the pluggable databases (PDBs).

More information can be found in:
* [About Performing Operations on CDBs and PDBs.](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/starting-interacting-with-rman-client.html#GUID-01DF892F-FED5-4ADE-9152-9C3CF0921DBA){: external}


#### Ensure that RMAN Configuration is Documented
{: #sapmig-db-oracle-rman1-rman-conf-dok}

As described in the preparation section [Document RMAN Configuration](/docs/sap?topic=sap-sapmig-db-oracle#sapmig-db-oracle-prep-rman), be sure to record current RMAN configuration and associated parameters.

After completing special backups for migration, you want to ensure that RMAN configuration is left as before so normally scheduled backup operations continue.

#### Backup Option 1 - Create Target Directory
{: #sapmig-db-oracle-rman1-create-fs-bk}

Login as the `oracle` user and execute the following commands to create the target backup directory to match the backup script that is described in the next section.

If the directory does not exist the rman script fails. Also if using an NFS mount, you need to make sure that the `oracle` user has the correct permissions in the definition file `exports` on the NFS server. The `oracle` user requires read and write (`rw`) permissions. Preferably using NFS is not advisable for the RMAN backup process. Using JFS2 or a locally mounted filesystem achieve better results.

The following two commands create the backup target location and set `ORACLE_SID` to the SID, please replace the term `<SID>` with the correct value for your system:
```sh
mkdir -p /backup/rman/<sid>_option1
setenv ORACLE_SID <SID>
```
{: pre}

You can of course use any target location as long as the oracle user can write to it and you modify the backup script from the next step to use your target directory.

#### Backup Option 1 - Backup Script
{: #sapmig-db-oracle-rman1-backup-skript}

The RMAN script is used to perform the backup option 1. The commands used in this script are discussed after the script. Copy and paste the script and make the necessary adjustments for your environment.

*`option1_backup.rman` script*
```sql
connect target /
SHUTDOWN IMMEDIATE
STARTUP MOUNT
CONFIGURE CHANNEL DEVICE TYPE DISK FORMAT '/backup/rman/<sid>_option1/option1_cmp_%d_%U';
CONFIGURE DEVICE TYPE DISK PARALLELISM 60;
CONFIGURE CONTROLFILE AUTOBACKUP ON;
CONFIGURE CONTROLFILE AUTOBACKUP FORMAT FOR DEVICE TYPE DISK TO '/backup/rman/<sid>_option1/option1_cf_%F';
CONFIGURE COMPRESSION ALGORITHM 'HIGH';
CONFIGURE ENCRYPTION FOR DATABASE ON ;
CONFIGURE ENCRYPTION ALGORITHM 'AES256' ;
SET ENCRYPTION ON IDENTIFIED BY passw0rd ONLY ;
BACKUP AS compressed BACKUPSET section size 6000M DEVICE TYPE DISK DATABASE TAG <YOUR TAG ID HERE> include current controlfile ;
alter database open;
quit;
```
{: codesection}

Execute this script from the command line by using the `oracle` user.
```sh
rman @option1_backup.rman
```
{: pre}

RMAN backs up data to the configured default device for the type of backup requested. By default, RMAN creates backups on disk. If a fast recovery area is enabled, and if you do not specify the FORMAT parameter, then RMAN creates backups in the recovery area and automatically gives them unique names. This is the reason for modifying FORMAT in the parameters before this and is repeated here for emphasis.

The following are the main components of the rman script used:

*Database Shutdown and Start in Mount Mode*
The database must be cleanly shut down and then started in “mount mode” for the offline backup option 1.
To ensure that the backup is consistent, the database must not be open.
Database shutdown + mount mode is accomplished by these two lines:

```sql
SHUTDOWN IMMEDIATE
STARTUP MOUNT
```
{: codesection}

*Backup Control File*
The database control file contains the RMAN catalog that is required to restore the backup pieces into a functioning database.

This command includes the control file into the backup:
```sql
CONFIGURE CONTROLFILE AUTOBACKUP ON
BACKUP AS compressed BACKUPSET section size 6000M DEVICE TYPE DISK DATABASE TAG EXX_100K_INITV3 include current controlfile ;
```
Note that the `CONFIGURE CHANNEL DEVICE TYPE DISK` and `CONFIGURE CONTROLFILE AUTOBACKUP FORMAT` for `DEVICE TYPE DISK` commands in the previous mentioned script include the backup file system location!

*Backup Parallelism*
Set disk device parallelism - likely to speed up the backup and to reduce the backup window. The optimal parallelism is dependent on several factors:
* Availability of CPU resources to run that many concurrent backup processes. With the selected compression and encryption each RMAN process typically uses all CPU cycles of a logical processor, assuming the storage subsystem can provide the data fast enough.
* Capability of storage subsystem to support the RMAN data file read and the write to backup location I/O throughput.
* Amount of free physical memory to support the backup processes to read, compress, encrypt the data.
* Parallelism 60 was used in our testing as shown in the rman script mentioned previously, but a parallelism of 8, as shown now, maybe a good starting point to find an optimal level during discovery:

```sql
CONFIGURE DEVICE TYPE DISK PARALLELISM 8 BACKUP TYPE TO BACKUPSET;
```
{: codesection}

*Backup Compression*
Set backup file compression. However HIGH has been tested in the script, however MEDIUM is advised. HIGH could be an option under certain circumstances. The use of 'MEDIUM' and 'HIGH' requires the Oracle Advanced Compression license!
* **Tested**
    `CONFIGURE COMPRESSION ALGORITHM 'HIGH' ;`
* **Advised preventing license cost increase**
    `CONFIGURE COMPRESSION ALGORITHM 'MEDIUM' ;`

*Backup encryption*
Set backup encryption on and modify the algorithm if desired (the default is AES128) and specify the encryption password to be used. Unless you are working with a TDE-encrypted database, every RMAN session requires the setting of the encryption and decryption password; otherwise, the session fails with a "wallet not open" error:

```sql
CONFIGURE ENCRYPTION FOR DATABASE ON ;
CONFIGURE ENCRYPTION ALGORITHM 'AES256' ;
SET ENCRYPTION ON IDENTIFIED BY passw0rd ONLY ;
```
{: codesection}

*Backup*
The final command then triggers the actual backup of the database to disk. The backup uses the RMAN-specific backup sets. Each RMAN process reads up to 6000M of consecutive data from a data file as a backup piece, compress and encrypt that data and then write it to the destination file system.

Of course the last action is to alter the database to open. It is advisable to create a specific tag (including the `ORACLE_SID <SID>` just for identification purposes). The goal of the section size is to limit the size of a backup piece so that a potentially required retransmit of a failed file transfer to the destination environment is manageable. The use of a TAG is recommended as it simplifies the management of multiple backups in an RMAN catalog:

```sql
BACKUP AS compressed BACKUPSET section size 6000M DEVICE TYPE DISK DATABASE TAG <SID>_100K_INITV3 include current controlfile ;
alter database open;
```
{: codesection}

*Backup Validation and Cross Check*
Backup Validation and Cross Check When backup is complete, still within RMAN, you can use some of the commands listed after this to validate and cross-check results.

On the RMAN prompt, use these commands:

```sql
RMAN> REPORT SCHEMA;
RMAN> LIST BACKUP SUMMARY;
RMAN> BACKUP VALIDATE CHECK LOGICAL DATABASE ARCHIVELOG ALL;
RMAN> VALIDATE DATAFILE 10;
RMAN> VALIDATE BACKUPSET 3;
RMAN> CROSSCHECK BACKUP;
```
{: codesection}

Explanation of commands:
* `REPORT SCHEMA ;` - Lists and displays information about the database files, tablespaces and so on.
* `LIST BACKUP SUMMARY ;` - Lists all existing backups. A SUMMARY option can be used.
* `LIST BACKUP <TAG ID> ;` - List a specific backup by TAG ID.
* `BACKUP VALIDATE CHECK LOGICAL DATABASE ARCHIVELOG ALL;`- Validate the contents of backup files.
* `VALIDATE DATAFILE 10;` - Validate a specific datafile.
* `VALIDATE BACKUPSET 3;` - Validate a specific backupset.
* `CROSSCHECK BACKUP;` - Synchronize the physical reality of backups and copies with their logical records in the RMAN repository.

*Create PFILE*
Create a plain text database parameter file (PFILE) from the binary server parameter file (SPFILE) as follows.
The database remains in mounted mode. Remember to use your `<SID>` in the command.

As the `oracle` user execute:
```sh
export ORACLE_SID=<SID>
sqlplus "/ as sysdba";
```
{: pre}

Inside SQL*Plus create the parameter file:
```sql
SQL> create pfile='/backup/rman/init<SID>.ora' from spfile;
```
{: codesection}

Be sure to restore all RMAN parameters back to original discovery settings when done.

For this *Option 1* we have created ONE backup to be transferred to and restored on the target system.
At this point, to be consistent with a migration scenario, the source database should be shut down and not used any longer.


### RMAN Option 2 - Online Backups
{: #sapmig-db-oracle-rman-option2-online-backup}

The following backup procedure is executed with the database online, and will produce a single RMAN Level 0 and one or more Level 1 (incremental) backups. It is required that the databases be in *archive log mode* and that it is ensured that all required archived *redo logs* are included in the backups.

An RMAN Incremental *Level 0* backup is a full backup. It includes the complete database.

An RMAN Incremental *Level 1* backup is an incremental backup, capturing the changes since the previous Level 0 or Level 1 backup. The `cummulative` option changes this behavior, please consult Oracle RMAN backup concepts documentation for more details.

The full backup (level 0) and all incremental backups (level 1) since the last full backup including the archived redo logs are required to restore and recover the database successfully.
When moving the database to a new server - which includes ending services on the source system, the last incremental backup is an offline backup.
{: note}

When using the *multitenant architecture*, you must connect to the root container database (CDB) and the backups include the pluggable databases (PDBs).

More information can be found in:
[Performing Operations on CDBs/PDBs](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/starting-interacting-with-rman-client.html#GUID-2C7C92ED-41ED-4A51-848E-34E695B107D4){: external}


#### Check Database Size
{: #sapmig-db-oracle-rman2-db-size}

Remember to make sure that you have enough space that is allocated on the filesystem to be able to back up the database.
Use the following SQL statement to determine the current database size:

```sql
SELECT SUM (bytes)/1024/1024/1024 AS GB FROM dba_segments;
```
{: pre}

#### Ensure that RMAN Configuration is Documented
{: #sapmig-db-oracle-rman2-rman-conf-dok}

As described in the preparation section [Document RMAN Configuration](/docs/sap?topic=sap-sapmig-db-oracle#sapmig-db-oracle-prep-rman), be sure to record current RMAN configuration and associated parameters. After completing special backups for migration, you want to ensure RMAN configuration is left as before so normally scheduled backup operations continue.


#### Backup Option 2 - Create Target Directories
{: #sapmig-db-oracle-rman2-create-fs-bk}

Execute the following commands as the `oracle` user.

The first two commands create the target backup directories for full and incremental backups. If you use a different directory, please adapt the two backup scripts in the next section.
The third command sets the environment variable `ORACLE_SID`, replace the term `<SID>` with the appropriate SID value of your system:
```sh
mkdir -p /backup/rman/<sid>_option2
mkdir -p /backup/rman/<sid>_option2_inc1

setenv ORACLE_SID <SID>
```
{: pre}

#### Backup Option 2 - Backup Scripts
{: #sapmig-db-oracle-rman2-backup-skripts}

##### Full Online Backup - level 0
{: #sapmig-db-oracle-rman2-full-onl-backup}

The first RMAN script `option2_backup_full.rman` configures the rman environment and creates the initial level 0 (full) online backup.
The key command to start the full (level 0) backup in this script is:
`BACKUP ... incremental level 0 ...`

Commands that are used in this script are discussed further into this procedure after the script.
Remember to replace the `<sid/SID>` entries to match your directory structure and system value:

*`option2_backup_full.rman` script*
```sql
connect target /
CONFIGURE CHANNEL DEVICE TYPE DISK FORMAT '/backup/rman/<sid>_option2/lev0_%d_%U';
CONFIGURE BACKUP OPTIMIZATION ON;
CONFIGURE DEVICE TYPE DISK PARALLELISM 60;
CONFIGURE CONTROLFILE AUTOBACKUP ON;
CONFIGURE CONTROLFILE AUTOBACKUP FORMAT FOR DEVICE TYPE DISK TO '/backup/rman/<sid>_option2/lev0_cf_%F';
CONFIGURE COMPRESSION ALGORITHM 'MEDIUM';
CONFIGURE ENCRYPTION FOR DATABASE ON ;
CONFIGURE ENCRYPTION ALGORITHM 'AES256' ;
SET ENCRYPTION ON IDENTIFIED BY passw0rd ONLY ;
BACKUP  tag '<Your TAG Here>' incremental level 0 AS compressed BACKUPSET section size 6000M DEVICE TYPE DISK DATABASE INCLUDE CURRENT CONTROLFILE PLUS ARCHIVELOG;
quit;
```
{: codesection}

You can call the script directly from the command line that uses user `oracle` and:
```sh
rman @option2_backup_level0.rman
```
{: pre}

After running the full (level 0) backup, the expected result should be:
```text
Recovery Manager complete.
```
{: screen}

RMAN backs up data to the configured default device for the type of backup requested. By default,
RMAN creates backups on disk. If a fast recovery area is enabled, and if you do not specify the FORMAT parameter,
then RMAN creates backups in the recovery area and automatically gives them unique names.  This is the reason for modifying FORMAT in the parameters before this and is repeated here for emphasis.

`RMAN` by default, includes ALL archived redo logs in a level 0 or level 1 backup if the `PLUS ARCHIVELOG` flag is specified. This can result in many duplicates of the same file within a consecutive set of incremental backups.

The following directive instructs RMAN to check whether a specific archived redo log was already included in a previous backup and, if yes, do not include the file in a new incremental backup.

If yes, do not include the file in a new incremental backup. Note that this option should NOT be used if other backups are taken of the database that are not to be transferred to the destination,
then this option should be set to `OFF`.
```sql
CONFIGURE BACKUP OPTIMIZATION ON;
```
{: codesection}

Restoring the database requires a copy of the database control file as it contains the RMAN catalog that is required to restore the backup pieces into a functioning database.

```sql
CONFIGURE CONTROLFILE AUTOBACKUP ON;

BACKUP AS compressed BACKUPSET section size 6000M DEVICE TYPE DISK DATABASE PLUS ARCHIVELOG TAG ECOM_option1 include current controlfile;
```
{: codesection}

Note that the script also contains `CONFIGURE CHANNEL DEVICE TYPE DISK` and `CONFIGURE CONTROLFILE AUTOBACKUP FORMAT for DEVICE TYPE DISK TO <directory>` commands, which define the backup type and file system location.

Set disk device parallelism - likely to speed up the backup and to reduce the backup window.

The optimal parallelism depends on several factors:
* Availability of CPU resources to run many concurrent backup processes. With the selected compression and encryption each RMAN process typically uses all CPU cycles of a logical processor, assuming the storage subsystem can provide the data fast enough.
* Capability of storage subsystem to support the RMAN data file read and write to backup location I/O throughput.
* Amount of free physical memory to support the backup processes to read, compress, encrypt the data.
* Size of the database.

Parallelism 60 was used in the our testing as shown in the rman script previously mentioned, but a parallelism of 8, as shown here, maybe a good starting point to find an optimal level during discovery.
```sql
CONFIGURE DEVICE TYPE DISK PARALLELISM 8 BACKUP TYPE TO BACKUPSET;
```
{: codesection}

Set backup file compression. MEDIUM is shown, HIGH could be an option under certain circumstances.
The use of 'MEDIUM' and 'HIGH' requires the `Oracle Advanced Compression license` !
```sql
CONFIGURE COMPRESSION ALGORITHM 'MEDIUM' ;
```
{: codesection}

Set backup encryption on and modify the algorithm if desired (the default is AES128) and specify the encryption password to be used. Unless you are working with a TDE-encrypted database, every RMAN session requires the setting of the encryption and decryption password; otherwise, the session fails with a "wallet not open" error.
```sql
CONFIGURE ENCRYPTION FOR DATABASE ON;
CONFIGURE ENCRYPTION ALGORITHM 'AES256';
SET ENCRYPTION ON IDENTIFIED BY passw0rd ONLY ;
```
{: codesection}

The final command then triggers the actual backup of the database to disk. The backup uses the RMAN-specific backup sets. Each RMAN process reads 6000M of consecutive data from a data file as a backup piece, compress and encrypt it and then write it to the destination file system. The goal of the section size is to limit the size of a backup piece so that a potentially required retransmit of a failed file transfer to the destination environment is manageable.

As pointed out before, it is essential that archived redo log files are included in the backup and the PLUS ARCHIVELOG flag ensures that RMAN picks up the existing archivelogs. The use of a TAG is strongly recommended as it simplifies the management of multiple backups in an RMAN catalog. “Incremental level 0” specifies this backup as an incremental backup at level 0, means all data will be included in the backup.

RMAN picks up the existing archivelogs. The use of a TAG is recommended as it simplifies the management of multiple backups in an RMAN catalog. “Incremental level 0” specifies this backup as an incremental backup at level 0, means that all data will be included in the backup.
```sql
BACKUP  tag '<Your TAG here>' incremental level 0 AS compressed BACKUPSET section size 6000M DEVICE TYPE DISK DATABASE INCLUDE CURRENT CONTROLFILE PLUS ARCHIVELOG TAG <SID>_LEV0;
```
{: codesection}

For additional incremental backups we suggest replacing occurrences of “inc1 / INC1” with corresponding “incN / INCN” in the following backup script. Note that it is suggested to store each incremental set of backup files into its own directory for easier management.

##### Incremental Online Backup - level 1
{: #sapmig-db-oracle-rman2-inc-onl-backup}

The second RMAN script `option2_backup_inc1.rman` configures the rman environment and creates the initial level 0 (full) online backup.
The key command to start the incremental (level 1) backup in this script is:
`BACKUP ... incremental level 1 ...`

Please replace the terms `<sid>` with the SID value from your system and  ensure the target backup directory defined in the script matches the directory that you have created in.
[Backup Option 2 - Create Target Directories](/docs/sap?topic=sap-sapmig-db-oracle#sapmig-db-oracle-rman2-create-fs-bk).

*`option2_backup_inc1.rman` script*
```sql
connect target /
CONFIGURE CHANNEL DEVICE TYPE DISK FORMAT '/backup/rman/<sid>_option2_inc1/inc1_%d_%U';
CONFIGURE BACKUP OPTIMIZATION ON;
CONFIGURE DEVICE TYPE DISK PARALLELISM 60;
CONFIGURE CONTROLFILE AUTOBACKUP ON;
CONFIGURE CONTROLFILE AUTOBACKUP FORMAT FOR DEVICE TYPE DISK TO '/backup/rman/<sid>_option2_inc1/inc1_cf_%F';
CONFIGURE COMPRESSION ALGORITHM 'MEDIUM';
CONFIGURE ENCRYPTION FOR DATABASE ON ;
CONFIGURE ENCRYPTION ALGORITHM 'AES256' ;
SET ENCRYPTION ON IDENTIFIED BY passw0rd ONLY ;
BACKUP TAG `<SID>`_INC1 incremental level 1 AS compressed BACKUPSET section size 6000M DEVICE TYPE DISK DATABASE INCLUDE CURRENT CONTROLFILE PLUS ARCHIVELOG;
quit;
```
{: codesection}

As user `oracle` set the environment variable `ORACLE_SID` with the SID value of your system:
```sh
setenv ORACLE_SID <SID>
```
{: pre}

And execute the incremental backup script:
```sh
rman @option2_backup_inc1.rman
```
{: pre}

The incremental level 1 scripts differ from the previously discussed level 0 rman script only in 3 areas:
* The directory where the backup files are written to.
* The TAG is used to identify the backup set.
* The specification of level 1 instead of level 0, indicating that this is an incremental backup, which by default, contains all changes since the last level 1 or level 0 backup.

The final incremental backup requires extra preparation steps to determine the date/time stamp to use in the final database recovery step for the destination environment. The time stamp *highlighted* in the *blue* box in the next step is the date/time the database will be recovered to. Any changes after that date/time will be discarded!

##### Inspect Server Time and Date
{: #sapmig-db-oracle-rman2-inspect}

Connect to the database as sysdba and execute the commands, as shown instructed.
Note that the database is only put into `mount` mode and not `opened`.

Setting the NLS Date format is used as the format model to implicitly cast from `date-to-string` or `string-to-dates`, this is important if you choose to perform a Point in Time recovery.


The second SQL command seen in the following image is forcing Oracle to write to a new redolog, the command ran after shows the date stamp of when the file was created and provides you with a timestamp to roll the database forward to by applying the applicable redologs.


![Figure 3. Incremental time stamp](/images/sap-power-virtual-server-mig-db-oracle-timestamp_incremental_1.png "Image showing Incremental time stamp"){: caption="Incremental time stamp" caption-side="bottom"}

Execute the final incremantal backup via RMAN.


##### Validate and Cross-Check Backups
{: #sapmig-db-oracle-rman2-validate}

When backup is complete, within RMAN, you can utilize some of the commands listed after this to validate and cross check results.

```sql
RMAN> REPORT SCHEMA;
RMAN> LIST BACKUP SUMMARY;
RMAN> LIST BACKUPSET <TAG ID> ;
RMAN> BACKUP VALIDATE CHECK LOGICAL DATABASE ARCHIVELOG ALL;
RMAN> VALIDATE DATAFILE 10;
RMAN> VALIDATE BACKUPSET 3;
RMAN> CROSSCHECK BACKUP;
```
{: codesection}

Lists and displays information about the database files, tablespaces and so on..

Explanation of commands:
* `REPORT SCHEMA ;` - Lists and displays information about the database files, tablespaces and so on.
* `LIST BACKUP SUMMARY ;` - Lists all existing backups. A SUMMARY option can be used.
* `LIST BACKUP <TAG ID> ;` - List a specific backup by TAG ID.
* `BACKUP VALIDATE CHECK LOGICAL DATABASE ARCHIVELOG ALL;`- Validate the contents of backup files.
* `VALIDATE DATAFILE 10;` - Validate a specific datafile.
* `VALIDATE BACKUPSET 3;` - Validate a specific backupset.
* `CROSSCHECK BACKUP;` - Synchronize the physical reality of backups and copies with their logical records in the RMAN repository.

##### Create PFILE
{: #sapmig-db-oracle-rman2-pfile}

Create a plain text database parameter file (PFILE) from the binary server parameter file (SPFILE) as follows.
The database remains in mounted mode. Remember to replace the term `<SID>` with your systems SID in the command.

Execute as user `oracle` these commands:
```sh
setenv ORACLE_SID <SID>
sqlplus "/ as sysdba";
```
{: pre}

In SQL*Plus create the parameter file:
```sql
SQL> create pfile='/backup/rman/init<SID>.ora' from spfile;
```
{: codesection}

Be sure to restore all RMAN parameters back to original discovery settings when done.

For this *Option 2* we have created TWO backup sets, to be transferred to and restored on the target system.
At this point, to be consistent with a migration scenario, the source database should be shut down and not used any longer.





## Restore the Oracle Database on Target System
{: #sapmig-db-oracle-restore}

Different options and steps to restore the database on the target system are covered in this section.
{: shortdesc}

### Two RMAN Restore Options
{: #sapmig-db-oracle-restore-options}

Two methods to restore an Oracle Database to the target system are provided:

1. RMAN Duplicate Database and
1. RMAN Restore/Recover Database.

Both methods will use the backups created in the previous RMAN backup section.

![Figure 4. Restore options](/images/sap-power-virtual-server-mig-db-oracle-restore-options.png "Image showing restore options"){: caption="Restore options" caption-side="bottom"}


Option 1 - RMAN Duplicate Database
: This option will use the `auxiliary instance` function of RMAN and can/should be used only when the full set of the consistent RMAN backup files are available on the target server.
:
: The RMAN “duplicate” command will automatically open the target database with `resetlogs`.
: The resetlogs operation increments the database incarnation number and, as a result, makes the target database completely independent of the source.  Importantly, no incremental level 1 backups or archived REDO logs can be applied to the target database at that point.

Option 2 - RMAN Restore/Recovery
: This option should be used to initially establish the target database with an RMAN incremental level 0 and, optionally, one or more incremental level 1 backup as described in the previous section.  However, it allows for later incremental level 1 backups and archived REDO logs to be applied to the target database over some amount of time.
:
: The expectation is that there will be a final cutover time, and the final incremental level 1 backup will be taken, copied/shipped to the target server and applied while the target database is still in a MOUNT state.  Once the final backup/archived logs are applied, the database will be manually opened and made available at the cutover time.

### Assumptions
{: #sapmig-db-oracle-restore2-assumptions}

1. An IBM cloud account has been established, along with an IBM PowerVS workspace.
1. Within the workspace, manual or automated procedures have been executed to install on the IBM PowerVS instance the necessary Oracle Grid  Infrastructure (if using ASM) and Oracle RDBMS homes on AIX. The AIX LPAR should be sized comparable to the source system in terms of compute, memory, storage capacity and performance, with compatible HW/SW versionings applied.
1.  All required backup files are accessible to the RMAN duplicate/restore/recover process.


### Connectivity and Preparation – Oracle on IBM PowerVS
{: #sapmig-db-oracle-restore-connect-prep}

From the target instance command line, you can use the following RMAN procedure to perform a restore.
1. Login as the user `oracle` to the AIX Power Virtual Server where you want to restore the database.
1. Confirm that:
    * Target IBM PowerVS system has a valid installation of the Oracle database software with the same version and patch level of software as in the source environment.
    * The device names for storage of Oracle datafiles and tablespace names are the same as with the source database. If not, `set newname` directives are required (steps are out of scope for this procedure)
    * ASM disk groups (if used) have been established with names and capacities that match the source environment.
    * Backup files are available to RMAN, whether resident on local file storage, Cloud Object Storage, NFS, and so on.

The following procedure options do not describe how to install the Oracle software, but rather how to restore the source database. As with the backup procedure, the presented steps are representative, and details differ from one database environment to another.

Reference the version-specific Oracle Database Installation Guide for AIX on Power Systems and the corresponding GRID Infrastructure documentation for ASM (and RAC, if relevant).

[Database Installation Guide (Version 19c)](https://docs.oracle.com/en/database/oracle/oracle-database/19/axdbi/index.html#Oracle%C2%AE-Database){: external}

[Grid Infrastructure Documentation (Version 19c)](https://docs.oracle.com/en/database/oracle/oracle-database/19/cwaix/installing-oracle-grid-infrastructure.html#GUID-D4E3FADF-360E-49EB-89A2-E4CBBB9CC61F){: external}

### Check Backup File Sets on Target Server
{: #sapmig-db-oracle-restore-bkup-file-sets}

Here is a sample view of backup files that have been staged in the `/backup/rman` folder:
Note that files were copied to a local `JFS/2` file system… partial list of files.

![Figure 5. Restore Options](/images/sap-power-virtual-server-mig-db-oracle-backup-list-example.png "Image showing restore options"){: caption="Restore options" caption-side="bottom"}

Displayed in a separate folder are miscellaneous files directly copied from the on-premises Oracle database, as recommended in the backup procedure before this.

![Figure 6. Backup folder tree](/images/sap-power-virtual-server-mig-db-oracle-backup-folder-tree.png "Image showing backup folder tree"){: caption="Backup folder tree" caption-side="bottom"}

### Check Configuration
{: #sapmig-db-oracle-restore-configuration}

The Oracle PFILE configuration is now viewed and settings from this file are verified.

#### Check PFILE
{: #sapmig-db-oracle-restore-ck-pfile}

We will now review the Oracle parameter file, or `PFILE`, stored within as `init<SID>.ora`.

Review database parameters that are contained within the saved PFILE and examine those with file name specifications. The parameter `audit_file_dest=` references a directory location that does not yet exist on the target server. The other file destinations reference ASM disk groups that should have already been created.

If the file locations in the target server do not match the source environment additional configuration changes are required in the rman scripts, so it is important that when `cloning` your Oracle server in IBM Cloud that you compare the configurations that are mentioned in the `init<SID>.ora` file and ensure that the locations exist on your target system.

![Figure 7. Audit file destination](/images/sap-power-virtual-server-mig-db-oracle-audit-file-dest-sap.png "Image showing audit file destination"){: caption="Audit file destination" caption-side="bottom"}

#### Check Audit Directory
{: #sapmig-db-oracle-restore-bkup-cfg-auditdir}

 Check and confirm that the audit file directory is present on the target server and as listed in the PFILE and give the proper ownership and mode.

![Figure 8. Backup folder tree](/images/sap-power-virtual-server-mig-db-oracle-audit-file-dest-create.png "Image showing backup folder tree"){: caption="Backup folder tree" caption-side="bottom"}

#### Check ASM Disk Groups and other Directory Definitions
{: #sapmig-db-oracle-restore-bkup-cfg-asm}

Confirm that ASM disk groups that are referenced in the PFILE exist on target and have sufficient free space.
As an oracle grid user, run:
```sh
asmcmd lsdg
```
{: pre}

If ASM groups are not used, review the PFILE and verify that referenced directories exist.
At this point, you are ready to proceed with one of the following restore options.


### Option 1 - RMAN Duplicate Database
{: #sapmig-db-oracle-restore1}

This procedure takes as input a consistent, Level 0 backup of the source database and restores the contents to a new Oracle instance on Power Virtual Server to create a duplicate database.

The following steps should be executed as the oracle user:


### Start Target Database in NOMOUNT Mode
{: #sapmig-db-oracle-restore1-strt-db}

Ensure the `ORACLE_SID` environment is set.
As user `oracle` set the correct ORACLE_SID for your system:
```sh
setenv ORACLE_SID <SID>
```
{: pre}

Start the Oracle instance in NOMOUNT mode, directly specifying the pfile to be used. Note that in our environment we are using Oracle in an SAP Environment so our ORACLE_HOME was `/oracle/EC9/19.0.0` your environment might differ, so please pay attention with copy/paste and remember to substitute your own Oracle `<SID>` in the commands mentioned after this.

As user `oracle` execute SQL*Plus and connect to the database as system database administrator:
```sh
sqlplus "/ as sysdba"
```
{: pre}

Inside SQL*Plus execute these SQL statements:
```sql
SQL> startup nomount pfile='/backup/rman/ora/initEC6.ora';
SQL> create spfile='/oracle/EC6/19.0.0/dbs/spfileEC6.ora' from pfile='/backup/rman/ora/initEC6.ora';
SQL> shutdown immediate;
SQL> startup nomount;
SQL> show parameter spfile;
```
{: codesection}

The spfile parameters that are printed will look like this:
```text
NAME                 TYPE            VALUE
---------          -------------    ---------------------------------
spfile               string          /oracle/EC6/19/dbs/spfileEC6.ora
```
{: screen}

### Script to Restore Database with RMAN - Option 1
{: #sapmig-db-oracle-restore1-script}

Create RMAN Duplicate Database script referencing the appropriate backup location. The file in this case is stored in the home directory of the ‘oracle’ user.

Optimally, you would create the number of restore channels using the reference to match the number of channels used for backup. If you recall during the backup process we observed that 60 channels were in use, so we match this number in the script.

The following script is appended to only show the first 15 rows, obviously because we need 60 channels, these need to be added to the script, you are required to add the additional rows after `allocate auxiliary channel ch[15-60] device type disk;`
{: note}

As user `oracle`create this script.

`rman_duplicate.cmd` script:
```sql
set encryption on identified by passw0rd;
set decryption identified by passw0rd;
run {
    allocate auxiliary channel ch1 device type disk;
    allocate auxiliary channel ch2 device type disk;
    allocate auxiliary channel ch3 device type disk;
    allocate auxiliary channel ch4 device type disk;
    allocate auxiliary channel ch5 device type disk;
    allocate auxiliary channel ch6 device type disk;
    allocate auxiliary channel ch7 device type disk;
    allocate auxiliary channel ch8 device type disk;
    allocate auxiliary channel ch9 device type disk;
    allocate auxiliary channel ch10 device type disk;
    allocate auxiliary channel ch11 device type disk;
    allocate auxiliary channel ch12 device type disk;
    allocate auxiliary channel ch13 device type disk;
    allocate auxiliary channel ch14 device type disk;
    allocate auxiliary channel ch15 device type disk;
    allocate auxiliary channel ch16 device type disk;
    allocate auxiliary channel ch17 device type disk;
    allocate auxiliary channel ch18 device type disk;
    allocate auxiliary channel ch19 device type disk;
    allocate auxiliary channel ch20 device type disk;
    allocate auxiliary channel ch21 device type disk;
    allocate auxiliary channel ch22 device type disk;
    allocate auxiliary channel ch23 device type disk;
    allocate auxiliary channel ch24 device type disk;
    allocate auxiliary channel ch25 device type disk;
    allocate auxiliary channel ch26 device type disk;
    allocate auxiliary channel ch27 device type disk;
    allocate auxiliary channel ch28 device type disk;
    allocate auxiliary channel ch29 device type disk;
    allocate auxiliary channel ch30 device type disk;
    allocate auxiliary channel ch31 device type disk;
    allocate auxiliary channel ch32 device type disk;
    allocate auxiliary channel ch33 device type disk;
    allocate auxiliary channel ch34 device type disk;
    allocate auxiliary channel ch35 device type disk;
    allocate auxiliary channel ch36 device type disk;
    allocate auxiliary channel ch37 device type disk;
    allocate auxiliary channel ch38 device type disk;
    allocate auxiliary channel ch39 device type disk;
    allocate auxiliary channel ch40 device type disk;
    allocate auxiliary channel ch41 device type disk;
    allocate auxiliary channel ch42 device type disk;
    allocate auxiliary channel ch43 device type disk;
    allocate auxiliary channel ch44 device type disk;
    allocate auxiliary channel ch45 device type disk;
    allocate auxiliary channel ch46 device type disk;
    allocate auxiliary channel ch47 device type disk;
    allocate auxiliary channel ch48 device type disk;
    allocate auxiliary channel ch49 device type disk;
    allocate auxiliary channel ch50 device type disk;
    allocate auxiliary channel ch51 device type disk;
    allocate auxiliary channel ch52 device type disk;
    allocate auxiliary channel ch53 device type disk;
    allocate auxiliary channel ch54 device type disk;
    allocate auxiliary channel ch55 device type disk;
    allocate auxiliary channel ch56 device type disk;
    allocate auxiliary channel ch57 device type disk;
    allocate auxiliary channel ch58 device type disk;
    allocate auxiliary channel ch59 device type disk;
    allocate auxiliary channel ch60 device type disk;
    duplicate database to <DBSID> backup location '/backup/rman/ec6_option1' nofilenamecheck noredo;
}
```
{: codesection}

As user `oracle` execute this script:
```sh
rman auxiliary / cmdfile=rman_duplicate.cmd
```
{: pre}

A typical output looks like this:
```sql
   sql clone "alter system set  db_name =
 ''EC6'' comment=
 ''Modified by RMAN duplicate'' scope=spfile";
   sql clone "alter system set  db_unique_name =
 ''EC6'' comment=
 ''Modified by RMAN duplicate'' scope=spfile";
   shutdown clone immediate;
   startup clone force nomount
   restore clone primary controlfile from  '/backup/rman/ec6_option1/option1_cf_c-2254911489-20240516-00';
   alter clone database mount;
```
{: screen}

Here, the database is successfully cloned and the resetlogs are also opened leading to the database open status and the message that the Recovery manager action has been successfully completed.

### Check the archive log status on the target system
{: #sapmig-db-oracle-restore1-successful-check-archivelog-status}

After the target database has been successfully restored, Use the following commands to check Archive log status.

As user `oracle` execute SQL*Plus command:
```sh
sqlplus "/ as sysdba"
```
{: pre}

Inside SQL*Plus excute these sql commands:
```sql
SQL> archive log list;
```
{: codesection}

The typical output looks like:
```text
Database log mode              No Archive Mode
Automatic archival             Disabled
Archive destination            /oracle/EC6/oraarch/EC6arch
Oldest online log sequence     1
Current log sequence           1
```
{: screen}


### Restart Database in Archive Mode
{: #sapmig-db-oracle-restore1-restart-db-archmode}

Still in SQL*Plus as sysdba, shut down the database instance by using the NORMAL, IMMEDIATE, or TRANSACTIONAL option:
```sql
SHUTDOWN IMMEDIATE
```
{: pre}

Start the instance and mount the database:
```sql
STARTUP MOUNT
```
{: pre}

And place the database into Archive Mode:
```sql
ALTER DATABASE ARCHIVELOG;
```
{: pre}

Now open the database on the server:
```sql
ALTER DATABASE OPEN;
```
{: pre}

And verify your changes:
```sql
ARCHIVE LOG LIST;
```
{: pre}

Finally create a new database backup on this target system that will also include the Archive files.
As described previously in the section [Back up the Source Oracle Database by using RMAN](/docs/sap?topic=sap-sapmig-db-oracle#sapmig-db-oracle-backup)


#### Why are `nofilenamecheck` and `noredo` Options Used With `duplicate database`
{: #sapmig-db-oracle-restore-noredo-nofilenamecheck}

`nofilenamecheck`
: The option `nofilenamecheck` will stop RMAN pre-checking the directory and file location of the target system by comparing the information that is contained with the controlfiles of the duplicate backup.
This is necessary if you want to restore a 1-2-1 copy of your source database to target using identical filesystem locations, SID and so on. If the option is not present then, you could receive a warning and the restore stops stating that there are discovered conflicts where target file locations where the datafiles will be restored to match source file locations. If this occurs add the `nofilenamecheck` to the restore command and retry, then the restore works.

`noredo`
: The `noredo` option has to be added to the duplicate database command in the `rman_duplicate.cmd` script as shown earlier.
: This is informing RMAN that you want to restore the full `offline` backup that is taken and do not check for any redologs. Oracle DBA's already know the issues when trying to create duplicate backups with the `PLUS ARCHIVELOG` mentioned in the backup command.




### Option 2 - RMAN Restore/Recover Database.
{: #sapmig-db-oracle-restore2}

This procedure establishes on Power Virtual Server an Oracle database from an initial inconsistent data set, then applies incremental backups and archived redo logs to create a recovered version of the database as of a specific point in time.

#### Start Target Database in NOMOUNT Mode
{: #sapmig-db-oracle-restore2-start-db-nomount}

The following steps should be executed as the user `oracle`.
As always please replace the term `<SID>` with the SID value of your system:
```sh
setenv ORACLE_SID <SID>
```
{: pre}

Start the Oracle instance in `NOMOUNT` mode, directly specifying the pfile to be used. Note that in our environment we are using Oracle in an SAP Environment so our ORACLE_HOME was `/oracle/EC9/19.0.0` your environment may differ, so please pay attention with copy & paste and remember to substitute your own Oracle `<SID>` in the commands mentioned after this.
```sh
sqlplus "/ as sysdba"
```
{: pre}

```sql
SQL> startup nomount pfile='/backup/rman/ora/initEC6.ora';
SQL> create spfile='/oracle/EC6/19.0.0/dbs/spfileEC6.ora' from pfile='/backup/rman/ora/initEC6.ora';
SQL> shutdown immediate;
SQL> startup nomount;
SQL> show parameter spfile;
```
{: codeblock}

The output showing the parameter typically looks like:
```text
NAME                 TYPE            VALUE
---------          -------------    ---------------------------------
spfile               string          /oracle/EC6/19/dbs/spfileEC6.ora
```
{: screen}

#### Determine Configuration File Path in Backup File Set
{: #sapmig-db-oracle-restore2-ctrl-file}

The control file (cf) had been included in the full backup (level 0).
Determine the full path of the control file in the backup option2 level0 file set.

Example:
```sh
find /backup/rman/ec6_option2 -name "lev0_cf_*"
```
{: pre}

The find command prints all filenames including path that match the name pattern:
```text
/backup/rman/ec6_option2/lev0_cf_c-2252531432-20240529-01
```
{: screen}

If no output is given, refer to the backup you created with the option 2 level 0 procedure for the control file location and check if and where the backup file set is on the target server.

### Script to Restore Database Level 0 Backup with RMAN - Option 2
{: #sapmig-db-oracle-restore2-script1}

Modify the recovery script after this to reflect the correct control file and then execute the rman script to restore the DB from level 0 backup and also apply any archived redo logs included in that backup via `recover database` as the `restore database` does not apply archived redo logs.

Note the use of the backup TAG to specify from which backup we want to restore. The RMAN catalog in the control file may list multiple backups. For our test I use the same backup tag that was used in the backup section that was previously created, hence why it is a good tip to use backup tags for specific backups.

Remember before script execution the Database should be started in `nomount` and the controlfile location must be adjusted to your file/backup location as we show an example here.


`option2_restore_level0.rman` script
```sql
connect target /
connect target /
set ENCRYPTION ALGORITHM 'AES256' ;
SET DECRYPTION IDENTIFIED BY passw0rd;
run {
  restore controlfile from '/backup/rman/ec6_option2/lev0_cf_c-2252531432-20240529-01';
  alter database mount;
  restore database from tag IBMECC02_EC6_LEV0;
  recover database;
}
```
{: codesection}

#### Explanation of Restore Script
{: #sapmig-db-oracle-restore2-script1-explanation}

Substitute the correct locations for archive, redologs, and controlfiles that are relevant to your Oracle Environment/Installation.

The backup files are encrypted and RMAN requires the encryption password to be able to restore the database files.
```sql
SET DECRYPTION IDENTIFIED BY passw0rd;
```
{: codesection}

We restore the database control file from the level 0 backup that contains the RMAN catalog we need to map backup pieces to data files as well as the list of data files and their expected locations.
```sql
restore controlfile from '/backup/rman/ec6_option2/lev0_cf_c-2252531432-20240529-01';
```
{: codesection}

We then alter the database to `mount mode` which is required for the next step.
```sql
alter database mount;
```
{: codesection}

Using the TAG we assigned to the level 0 backup that we instruct RMAN to restore the database from that specific backup.
```sql
restore database from tag IBMECC02_EC6_LEV0;
```
{: codesection}

As a final step we apply any in level 0 backup included archive logs to the restored database. Note that this does not open the database so that we can apply future incremental backups and/or archive logs to the database.
```sql
recover database;
```
{: codesection}

#### Execute Restore Database Level0 Script
{: #sapmig-db-oracle-restore2-exec-script1}

Ensure that the database instance was started with `startup nomount`

As user `oracle` execute the restore script:
```sh
rman @restore_option2_lev0.rman
```
{: pre}

Sample output is extensive and not listed here.


### Restore Incremental Backups (level 1)
{: #sapmig-db-oracle-restore2-exec-script}

The RMAN full backup level 0 was restored successfully.
The next step is restoring all incremental level 1 backup set.

#### Restore Incremental Backups (level 1) - All Except the Last One
{: #sapmig-db-oracle-restore2-inc-intermediate}

Catalog the remainder of the incremental level 1 backupsets and archived logs in the target directory. If multiple incremental backups are provided, this is an iterative process.

For all incremental backups, EXCEPT the last, you can simply execute a
```sql
recover database ;
```
{: pre}

With this step(s) the incremental backup set(s) are cataloged.

#### Catalog Final Incremental Backup (level 1)
{: #sapmig-db-oracle-restore2-inc-cat-final}

The final incremental backup requires that the recovery is only until a specific time to be able to open the database!

The target database can remain in `MOUNT` state and have incremental level 1 and archived REDO logs applied continuously, as needed. Catalog new backup pieces and archived logs and recover until ready for final cutover.
{: important}

The following command assumes that all incremental backups are stored in directories under `/backup/rman`.

Still in SQL*Plus, execute these SQL commands:
```sql
RMAN> catalog start with '/backup/rman';
```
{: codeblock}

Sample output, just showing backup files from the first incremental level 1 backup:
```text
searching for all files that match the pattern /backup/rman

List of Files Unknown to the Database
=====================================
File Name: /backup/rman/ec6_option2_inc1/inc1_EC6_dk2s0rg6_1460_1_1
File Name: /backup/rman/ec6_option2_inc1/inc1_EC6_dl2s0rgc_1461_1_1
File Name: /backup/rman/ec6_option2_inc1/inc1_EC6_dl2s0rgc_1461_2_1
File Name: /backup/rman/ec6_option2_inc1/inc1_EC6_dm2s0rgc_1462_1_1
File Name: /backup/rman/ec6_option2_inc1/inc1_EC6_dn2s0rgc_1463_1_1
File Name: /backup/rman/ec6_option2_inc1/inc1_EC6_do2s0rgc_1464_1_1
File Name: /backup/rman/ec6_option2_inc1/inc1_EC6_dp2s0rgc_1465_1_1
File Name: /backup/rman/ec6_option2_inc1/inc1_EC6_dq2s0rge_1466_1_1
File Name: /backup/rman/ec6_option2_inc1/inc1_EC6_dr2s0rgh_1467_1_1
File Name: /backup/rman/ec6_option2_inc1/inc1_EC6_ds2s0rgn_1468_1_1
        ...Lines Omitted ...
File Name: /backup/rman/ibmecc02/ec6_option2_inc1/inc1_EC6_eo2s0rlq_1496_1_1
File Name: /backup/rman/ibmecc02/ec6_option2_inc1/inc1_EC6_ep2s0rlu_1497_1_1
File Name: /backup/rman/ibmecc02/ec6_option2_inc1/inc1_EC6_eq2s0rm3_1498_1_1
File Name: /backup/rman/ibmecc02/ec6_option2_inc1/inc1_EC6_er2s0rm6_1499_1_1
File Name: /backup/rman/ibmecc02/ec6_option2_inc1/inc1_EC6_es2s0rma_1500_1_1
File Name: /backup/rman/ibmecc02/ec6_option2_inc1/inc1_EC6_eu2s0rmi_1502_1_1
File Name: /backup/rman/ibmecc02/ec6_option2_inc1/inc1_cf_c-2252531432-20240529-02

Do you really want to catalog the above files (enter YES or NO)? YES
cataloging files...
cataloging done
```
{: screen}


#### Restore Final Incremental Backup (level 1) to Point in Time
{: #sapmig-db-oracle-restore2-recover-point-in-time}

For all incremental backups, EXCEPT the final, you can then execute in RMAN:

Remember because the backup was created with encryption you need to update the configuration in RMAN to decrypt the backup that uses the password, otherwise you encounter errors such as `ORA-19913: unable to decrypt backup` and `ORA-28365: wallet is not open`
```sql
RMAN> SET ENCRYPTION ALGORITHM 'AES256' ;
RMAN> SET DECRYPTION IDENTIFIED BY passw0rd;
RMAN> recover database ;
```
{: codesection}

After cataloging the FINAL incremental backup pieces the recovery needs to be up to the *date/time* determined in the backup

After this final recovery action no further changes are expected to be applied to the database from a recovery perspective.
Set the time and date format with:
```sql
RMAN> alter session set nls_date_format='DD-MM-YYYY HH24:mi:SS' ;
```
{: codesection}

You will find an example date range here:
```sql
RMAN> recover database until time '13-01-2024 11:52:12';
```
{: codesection}

### Start Database
{: #sapmig-db-oracle-restore2-start-db}

After all interim archived REDO logs and the final RMAN increment level 1 have been cataloged and recovered as described, the database can be opened.

As user `oracle` execute sql plus:
```sh
sqlplus "/ as sysdba"
```
{: pre}

```sql
SQL> alter database open resetlogs ;
```
{: codesection}

### Check Database Mode
{: #sapmig-db-oracle-restore2-check-db-mode}

Check to make sure that after the database has been restored that the *Archive Mode* is now enabled.

As user `oracle` execute sqlplus command:
```sh
sqlplus "/ as sysdba"
```
{: pre}

And double check the archive logs list:
```sql
SQL> archive log list;
```
{: codesection}

This concludes the Restore/Recover approach to database migration to Power Virtual Server.
