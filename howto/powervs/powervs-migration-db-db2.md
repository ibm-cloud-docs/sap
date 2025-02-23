---
copyright:
  years: 2024, 2025
lastupdated: "2025-02-20"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, on-prem, on premises, Hybrid Cloud, Migration, Linux, Red Hat, RHEL, SuSE, backup, restore, Db2, IBM Db2, HADR
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Migrating SAP ERP 6.0 on IBM Db2 to IBM {{site.data.keyword.powerSys_notm}}
{: #sapmig-db-db2}

Use the following guide to migrate your SAP Enterprise Resource Planning 6 (ERP) system from an IBM Db2 to an IBM {{site.data.keyword.powerSys_notm}}. You have different options to migrate IBM Db2 databases to a target system.
{: shortdesc}

## IBM Db2 SAP migration options
{: #sapmig-db-db2-options}

For SAP ERP on IBM Db2, you have two migration options:

- [Migration option 1 - Back up and restore](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option1-backup-restore) is based on standard administrative tasks such as start, stop, backup, and restore. It is a less complicated approach, but it requires downtime during migration.

- [Migration option 2 - IBM Db2 high availability and disaster recovery (HADR)](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option2-backup-restore-hadr) is a downtime-optimized approach for SAP production systems. It is based on IBM Db2 High Availability and Disaster Recovery (IBM Db2 HADR) Database Synchronization and an online back up. The system downtime is minimized, but the method requires a higher configuration approach compared to option 1.

## Migration option 1 - Back up and restore
{: #sapmig-db-db2-option1-backup-restore}

Back up and restore is a typical option to migrate development or test SAP systems. This migration option is based on a backup of the database from the source system and a database *restore* to fill the database on a preinstalled target system.

The following steps are required to complete the migration:

1. [Preparing for migration](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option1-step0-prep)
1. [Shutting down the SAP application and deactivating the database on the source server](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option1-step1-shutdown)
1. [Using the IBM Db2 offline backup on the source system](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option1-step2-backup)
1. [Transferring the backup files](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option1-step3-transfer)
1. [Restoring the database on the target system](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option1-step4-restore)
1. [Starting the SAP target system](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option1-step5-start)

Characteristics of the *Migration Option 1 - Back up and Restore* approach:

- *Downtime*: a planned downtime of the SAP system is required because the SAP system is not available during the migration process.
- *Fallback*: no changes were made to the source SAP system. The fallback action if required, stops the migration and restarts the source SAP system.
- *Complexity*: is based on standard administrative tasks. This migration option is the least complicated.

### Preparing for migration
{: #sapmig-db-db2-option1-step0-prep}

Use the following steps to prepare for the migration.

You need all the defined environment variables on both the source and target server.
Save the commands to a temporary text file while you run them on the source SAP server.
The exact same commands are required on the target server as shown in chapter [Restoring the database on the target system](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option1-step4-restore).
{: fast-path}

Set the following environment variables according to your needs.

1. Start a C shell session as `root` user on the source system:

   ```sh
   csh
   ```
   {: pre}

   The command syntax for defining environment variables depends on the shell type.
   Default shell type for Db2 and SAP administrator users `db2<sid>` and `<sid>adm` is the C shell.
   When using the same shell, you can copy the command examples and paste them into your session environment.

1. Define the hostname of the target migration system (example:`cdb6ecc1`) and replace it with your target hostname:

   ```sh
   set TARGETSERVER=cdb6ecc1
   ```
   {: pre}

   Make sure that the same versions of SAP ERP Software and IBM Db2 database are running on the source and target SAP systems.
   {: requirement}

1. Define the SAP instance administrator (this example is for the SAP instance `th1`) and change it to match your system.

   ```sh
   set SIDADM=th1adm
   ```
   {: pre}

1. Define the IBM Db2 database name - again `th1` is the example, and change it to match your system:

   ```sh
   set DBNAME=th1
   ```
   {: pre}

1. Define the IBM Db2 database administrator:

   ```sh
   set DB2ADM=db2th1
   ```
   {: pre}

1. Define a directory to store the backup files:

   ```sh
   set BACKUPDIR=/db2/backup
   ```
   {: pre}

1. Create the following directory if it does not exist:

   ```sh
   mkdir -p $BACKUPDIR
   ```
   {: pre}

   and transfer the ownership of the backup directory to the Db2 administrator:

   ```sh
   chown $DB2ADM $BACKUPDIR
   ```
   {: pre}

   This backup directory needs enough space to store the compressed backup files. Determine the current IBM Db2 database size by calling the `GET_DBSIZE_INFO` procedure. For more information, see the [GET_DBSIZE_INFO procedure](https://www.ibm.com/docs/en/db2/11.5?topic=views-get-dbsize-info-database-size-capacity){: external}.
   {: tip}

### Shutting down the SAP application and deactivating the database on the source server
{: #sapmig-db-db2-option1-step1-shutdown}

Use the following steps to shut down the SAP application and deactivate the database on the source server.

1. To start or stop the SAP system, use the SAP instance administrator account `$SIDADM`:

   ```sh
   su - $SIDADM
   ```
   {: pre}

1. To stop the SAP system and keep the database running, use the following command:

   ```sh
   stopsap r3
   ```
   {: pre}

   Optionally, you can check whether the SAP is in the required state by running the following command:

   ```sh
   stopsap check
   ```
   {: pre}

   The expected output looks like the following example:

   ```text
   Checking db6 Database
   Database is running
   -------------------------------------------
   Checking SAP TH1 Instance ASCS01
   -------------------------------------------
   Instance ASCS01 is not running
   Checking SAP TH1 Instance D00
   -------------------------------------------
   Instance D00 is not running
   ```
   {: screen}

1. For the Db2 commands, end the `$SIDADM` user session:

   ```sh
   exit
   ```
   {: pre}

1. Switch to the `$DB2ADM` user:

   ```sh
   su - $DB2ADM
   ```
   {: pre}

   Optionally, you can check whether any applications are still connected to the database before you deactivate the database by running the following command:

   ```sh
   db2 list applications
   ```
   {: pre}

   The following example is the expected output.

   ```text
   SQL1611W  No data was returned by Database System Monitor.
   ```
   {: screen}

   If applications are still listed, stop the applications and check again. Only if the external applications still don't stop, try disconnecting applications from the database server with `db2 force applications all`.
   {: tip}

1. Define the environment variables for the DB2ADM user again:

   ```sh
   set DBNAME=th1
   ```
   {: pre}

   ```sh
   set BACKUPDIR=/db2/backup
   ```
   {: pre}

1. The IBM Db2 offline backup requires that the database is deactivated. Use the following command to deactivate the database:

   ```sh
   db2 deactivate database $DBNAME
   ```
   {: pre}

   The following output is expected.

   ```text
   DB20000I  The DEACTIVATE DATABASE command completed successfully.
   ```
   {: screen}

### Using the IBM Db2 offline backup on the source system
{: #sapmig-db-db2-option1-step2-backup}

Use the following steps to use the Db2 offline backup on the source system.

A backup directory to store the compressed offline backup was created in the preparation step [Preparation for Migration](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option1-step0-prep).

1. Verify that the backup directory offers sufficient available space to store backup files:

   ```sh
   df -m $BACKUPDIR
   ```
   {: pre}

1. Start the IBM Db2 offline backup:

   ```sh
   db2 backup database $DBNAME to $BACKUPDIR compress
   ```
   {: pre}

   This command runs for a long time, depending on the database size.

   Backup progress can be tracked with the command `db2 list utilities show detail`
   {: tip}

   You can find a timestamp at the end of the backup command output.
   This timestamp is required to [Restoring the database on the target system](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option1-step4-restore).
   {: note}

   Example output:

   ```text
   Backup successful. The timestamp for this backup image is : 20240730170709
   ```
   {: screen}

   Memorize this timestamp, you need to refer to it in the next two steps.

1. Use an environment variable to store the timestamp:

   ```sh
   set TIMESTAMP=<your timestamp>
   ```
   {: pre}

   The IBM Db2 backup timestamp has the format YYYMMDDHHMMSS (Year-Month-Day-Hour-Minute-Second) and looks like `20240730170709`. This timestamp is needed on the target SAP server again. Append this `TIMESTAMP` definition command to the list of saved commands that are from [Preparation for Migration](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option1-step0-prep).
   {: fast-path}

1. Change to the backup folder by using the following command:

   ```sh
   cd $BACKUPDIR
   ```
   {: pre}

1. Check for files that contain your timestamp in their name:

   ```sh
   ls -l *$TIMESTAMP*
   ```
   {: pre}

   Example output:

   ```text
   TH1.0.db2th1.DBPART000.20240730170709.001
   ```
   {: screen}

Files that are listed in the output are the backup files that are transferred to the target system in the next step.

### Transferring the backup files
{: #sapmig-db-db2-option1-step3-transfer}

Use the following step to transfer the backup files.

1. Copy the backup files from the source SAP system to the target:

   ```sh
   scp ${BACKUPDIR}/*${TIMESTAMP}* \
       ${TARGETSERVER}:${BACKUPDIR}
   ```
   {: pre}

This example uses secure copy (SCP), a slower version of data transfer. You can transfer backup files either directly to an IBM AIX LPAR in {{site.data.keyword.powerSys_notm}}, or to IBM Cloud Object Storage. If you use SCP or SFTP with IBM Cloud Object Storage, it assumes that you are using an IBM FileManage Gateway service or installed and configured an SFTP server within or next to the target IBM {{site.data.keyword.powerSys_notm}} environment to receive the transfer.

The faster option is IBM’s high-performance Aspera product for data transfer. In many situations, IBM Aspera can transfer data several times faster than traditional TCP-based protocols. For more information, see [IBM Aspera Technologies](/docs/power-iaas?topic=power-iaas-additional-migration-strategies-power#aspera-technologies).

This reference also contains the [Accelerated network transfer migration guide](https://cloud.ibm.com/media/docs/downloads/power-iaas/accelerated_migration.pdf){: external}.
{: note}

### Restoring the database on the target system
{: #sapmig-db-db2-option1-step4-restore}

Use the following steps to restore a database on the target system.

1. Log in to the target server to start the restore procedure:

   ```sh
   ssh $DB2ADM@$TARGETSERVER
   ```
   {: pre}

   The target server can't know the environment variables that are defined on the source system. But variables `$DBNAME`, `$TIMESTAMP` and `$SIDADM` are needed on the target system. To define these variables, run the list of commands that you saved in step [Preparing for migration](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option1-step0-prep) and [Using the IBM Db2 offline backup on the source system](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option1-step2-backup) or define the environment variables again.
   {: requirement}

1. The full restore procedure requires a deactivated IBM Db2 database. Use the same steps as before on the preinstalled target SAP system:

   ```sh
   db2 list applications
   ```
   {: pre}

   The following output is expected:

   ```text
   SQL1611W  No data was returned by Database System Monitor.
   ```
   {: screen}

1. Deactivate the IBM Db2 database:

   ```sh
   db2 deactivate database $DBNAME
   ```
   {: pre}

1. Do a full database restore:

   ```sh
   db2 restore database $DBNAME \
       from $BACKUPDIR          \
       taken at $TIMESTAMP
   ```
   {: pre}

   Appending `replace existing` to the restore command avoids the overwrite prompt.
   {: tip}

1. Db2 command asks if you want to overwrite the existing database by restoring the backup:

   ```text
   SQL2523W  Warning!  Restoring to an existing database that is different from the database on the backup image, but have matching names. The target database is overwritten by the backup version. The Roll-forward recovery logs associated with the target database is deleted.

   Do you want to continue ? (y/n)
   ```
   {: screen}

   Enter `y` and press Enter.

   The following output is expected:

   ```text
   DB20000I  The RESTORE DATABASE command completed successfully.
   ```
   {: screen}

   If the restore did not succeed, drop the database on the target system in advance with `db2 drop database $DBNAME` and redo the restore command.
   {: tip}

1. To complete the restore, use the following command:

   ```sh
   db2 rollforward db $DBNAME to end of backup and stop
   ```
   {: pre}

The target database now contains the source system data.

### Starting SAP target system
{: #sapmig-db-db2-option1-step5-start}

Use the following steps to start the target system,

1. Switch to the SAP instance administrator:

   ```sh
   login $SIDADM
   ```
   {: pre}

1. Start the SAP application:

   ```sh
   startsap
   ```
   {: pre}

   The following example is the expected output:

   ```text
   Checking db6 Database
   Database is running
   -------------------------------------------
   Starting Startup Agent sapstartsrv
   OK
   Instance Service on host <TARGET> started
   -------------------------------------------
   starting SAP Instance ASCS01
   Startup-Log is written to /home/th1adm/startsap_ASCS01.log
   -------------------------------------------
   /usr/sap/TH1/ASCS01/exe/sapcontrol -prot NI_HTTP -nr 01 -function Start
   Instance on host <TARGET> started
   Starting Startup Agent sapstartsrv
   OK
   Instance Service on host <TARGET> started
   -------------------------------------------
   starting SAP Instance D00
   Startup-Log is written to /home/th1adm/startsap_D00.log
   -------------------------------------------
   /usr/sap/TH1/D00/exe/sapcontrol -prot NI_HTTP -nr 00 -function Start
   Instance on host <TARGET> started
   ```
   {: screen}

Adjust the SAP system DNS record to point to the target SAP server to make sure that client systems (the running SAP Logon GUI) are connected to the target server.
{: important}

The migration is complete.

- SAP system on the source server is down
- SAP system on the target server is up and running

## Migration option 2 - IBM Db2 high availability and disaster recovery (HADR)
{: #sapmig-db-db2-option2-backup-restore-hadr}

Migration option 2 is a downtime-optimized procedure.
It is a combination of backup, recovery, and synchronization of the databases on both sides by using IBM Db2 HADR. If databases are in sync and the environment is prepared for migration, the core migration switches to the preinstalled target SAP server.

If the source system uses an IBM Db2 cluster or has IBM Db2 HADR enabled, migration extends the existing HADR configuration.
[Special case - Migrating a source IBM Db2 cluster](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option2-source-cluster) describes and links to the required steps.

If IBM Db2 HADR is not enabled on the source side, the following steps are required:

1. [Preparing for migration](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option2-preparation)
1. [Making sure that IBM Db2 archive logging is enabled](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option2-archive-logging)
1. [Creating an online backup from the source system database](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option2-backup)
1. [Transferring backup files to the target system](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option2-transfer)
1. [Restoring the target system](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option2-restore)
1. [Defining HADR local and remote service ports on the target and source system](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option2-define-services)
1. [Configuring HADR on the target and source systems](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option2-hadr-configure)
1. [Starting HADR and checking the synchronized data](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option2-hadr-start)
1. [Running the core migration step](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option2-migration)

Characteristics of *Migration Option 2 -IBM Db2 HADR*:

- *Downtime, general*: it has a short downtime when the server switches from source to target. Before the switch, the source SAP system is running. After the switch, the target SAP system takes over.
- *Downtime, circular logging*: HADR requires that the IBM Db2 database is in `archive logging` mode. If your IBM Db2 database is in `circular logging` mode, you need to migrate the IBM Db2 database to `archive logging` mode before you use HADR. This step enforces a one-time database downtime.
- *Fallback*: means that the source SAP system is running during the complete migration until it switches. Before you make the switch, no special fallback option is required. If steps before or after the switch are unsuccessful, you can reverse the steps to bring the source SAP system up and running again.

### Special case - Migrating a source IBM Db2 cluster
{: #sapmig-db-db2-option2-source-cluster}

If the source SAP system is an IBM Db2 HADR cluster, the HADR configuration needs to extend to include the target server. The steps are easier in this case.

Most of the configuration steps that are described are already set up. The target system needs to be added as an auxiliary node to the existing IBM Db2 HADR configuration. For more information, see the [Steps for adding a new Auxiliary Standby to an existing Db2 HADR Pair](https://www.ibm.com/support/pages/steps-adding-new-auxiliary-standby-existing-db2-hadr-pair){: external} IBM Db2 Support Article.

### Preparing for migration
{: #sapmig-db-db2-option2-preparation}

Use the following steps to prepare the SAP system for migration.

Keep the following information in mind as you prepare for the migration.

- You need to define two TCP port numbers to use in your setup.

- Synchronizing with HADR requires two open network ports:
   - A local port for outgoing traffic
   - A remote port for incoming traffic

- Both port are configured on each of the two IBM Db2 systems, but with interchanged order.
   This example uses port number `5920/tcp` and `5921/tcp`, but you can adapt these ports to your needs.

- Firewalls must allow TCP traffic between the source and target system.
   Make sure that the required firewall rules are configured on all involved firewalls and on the source and target system.

Set the following environment variables according to your configuration.

All environment variables that are defined are needed on both the source and target server.
Save the commands while you run them on the source SAP server to a temporary text file.
The same commands are required on the target server as noted in [Restoring the database on the target system](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option1-step4-restore).
{: fast-path}

1. Define the hostname for the target migration system. `cdb6ecc1` is an example and replace it with your target hostname by using the following command:

   ```sh
   set TARGETSERVER=cdb6ecc1
   ```
   {: pre}

   Make sure that your target server is running SAP ERP Software and IBM Db2 database with the same version as the source SAP system.
   {: requirement}

1. Define the SAP instance administrator and change it to match your system by using the following command (this example is for the SAP instance `th1`):

   ```sh
   set SIDADM=th1adm
   ```
   {: pre}

1. Define the IBM Db2 database name - again `th1` is an example. Change it to match your system:

   ```sh
   set DBNAME=th1
   ```
   {: pre}

1. Define the IBM Db2 database administrator:

   ```sh
   set DB2ADM=db2th1
   ```
   {: pre}

1. Define a directory to store backup files:

   ```sh
   set BACKUPDIR=/db2/backup
   ```
   {: pre}

1. Create the following directory if it does not exist:

   ```sh
   mkdir -p $BACKUPDIR
   ```
   {: pre}

   and transfer the ownership of the backup directory to the Db2 administrator:

   ```sh
   chown $DB2ADM $BACKUPDIR
   ```
   {: pre}

   This backup directory needs enough space to store the compressed backup files. Determine the current IBM Db2 database size by calling the `GET_DBSIZE_INFO` procedure. For more information, see [GET_DBSIZE_INFO procedure](https://www.ibm.com/docs/en/db2/11.5?topic=views-get-dbsize-info-database-size-capacity){: external}.
   {: tip}

### Making sure that IBM Db2 archive logging is enabled
{: #sapmig-db-db2-option2-archive-logging}

IBM Db2 High Availability and Disaster Recovery (HADR) requires `archive logging` to be enabled.
`circular logging` is enabled by default after you install IBM Db2 HADR. Most SAP systems on IBM Db2 have `archive logging` enabled.

#### Checking the archive logging methods
{: #sapmig-db-db2-option2-archive-logging-check}

To check which logging method is configured, use the following steps.

1. Switch to the IBM Db2 database admin:

   ```sh
   su - $DB2ADM
   ```
   {: pre}

1. Retrieve both log archive method configuration options:

   ```sh
   db2 get db cfg for th2 | grep LOGARCHMET
   ```
   {: pre}

1. Continue with the next section, if at least one of the log archive methods is set to `ON`.
   Make sure that both are not set to `OFF`. This example shows what the output for enabled archive logging looks like:

   ```text
    First log archive method                 (LOGARCHMETH1) = DISK:/db2/logarch/
    Second log archive method                (LOGARCHMETH2) = OFF
   ```
   {: screen}

1. If both log archive methods are `OFF`, you need to configure archive logging first.
   The following example is the output:

   ```text
    First log archive method                 (LOGARCHMETH1) = OFF
    Second log archive method                (LOGARCHMETH2) = OFF
   ```
   {: screen}

#### Configuring archive logging
{: #sapmig-db-db2-option2-archive-logging-configure}

Archive logging is required for IBM Db2 HADR.
If LOGARCHMETH1 and LOGARCHMETH2 are set to `OFF`, use the following steps to enable archive logging.

A downtime is required to enable archive logging.

1. Start as SAP admin user to stop the SAP system:

   ```sh
   su - $SIDADM
   ```
   {: pre}

1. Stop the SAP system, but leave the database running:

   ```sh
   stopsap r3
   ```
   {: pre}

1. Quit the SAP administrative user:

   ```sh
   exit
   ```
   {: pre}

1. Switch to the IBM Db2 administration account:

   ```sh
   su - $DB2ADM
   ```
   {: pre}

1. Use the saved commands to ensure environment variables like `DBNAME` and `BACKUPDIR` are set.

1. Define a directory for log archive files:

   ```sh
   set LOGARCHDIR=/db2/log_archive
   ```
   {: pre}

   If possible, create the log archive directory on a separate partition. The archive log partition depends on the LPARs disk setup, which is out of the scope of this document. Keep in mind that if the source system is an IBM Db2 cluster, the log archive is a shared directory between the nodes.
   {: important}

1. Create the log archive directory, at minimum with this command:

   ```sh
   mkdir $LOGARCHDIR
   ```
   {: pre}

1. Check the created directory:

   ```sh
   ls -ld $LOGARCHDIR
   ```
   {: pre}

   Sample output:

   ```text
   drwxr-xr-x 7 db2th1 dbth1adm 256 Aug 30 11:24 /db2/log_archive
   ```
   {: screen}

1. Verify whether the directory owner is the Db2 administrator, which is `db2th1` in sample output.
   Then, check that the owner has full permissions `rwx`.

1. Configure Db2 to use a disk device with this directory for archive logging method 1:

   ```sh
   db2 "update db cfg for $DBNAME using LOGARCHMETH1 DISK:$LOGARCHDIR"
   ```
   {: pre}

   Expected output:

   ```text
   DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
   ```
   {: screen}

   IBM Db2 database enforces a backup after this change:

   ```sh
   db2 backup database $DBNAME to $BACKUPDIR compress
   ```
   {: pre}

   The backup takes a while to complete, depending on the database size.

1. Use the SAP admin account $SIDADM to start database and SAP system again:

   ```sh
   su - $SIDADM
   ```
   {: pre}

   ```sh
   startsap
   ```
   {: pre}

### Creating an online backup from the source system database
{: #sapmig-db-db2-option2-backup}

To minimize SAP system downtime, create an online Db2 database backup.

1. Switch to the Db2 admin user:

   ```sh
   su - $DB2ADM
   ```
   {: pre}

1. Start the online backup, including log files:

   ```sh
   db2 backup db $DBNAME online to $BACKUPDIR compress include logs
   ```
   {: pre}

   Keep in mind that the backup takes some time to complete.

   When the backup is complete, a timestamp is printed:

   ```text
   Backup successful. The time stamp for this backup image is : 20240913091058
   ```
   {: screen}

   Memorize this timestamp. You need it in the next two steps.

1. Use an environment variable to store the timestamp:

   ```sh
   set TIMESTAMP=<your timestamp>
   ```
   {: pre}

   The IBM Db2 backup timestamp uses the format YYYMMDDHHMMSS (Year-Month-Day-Hour-Minute-Second) and looks like `20240913091058`.
   This timestamp is needed on the target SAP server again.
   Append this `TIMESTAMP` definition command to the list of saved commands from step [Preparation for Migration](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option2-preparation).
   {: fast-path}

### Transferring backup files to the target system
{: #sapmig-db-db2-option2-transfer}

Use the following step to transfer the backup files to the target system.

1. Copy the backup files from the source SAP system to the target system:

   ```sh
   scp ${BACKUPDIR}/*${TIMESTAMP}* \
       ${TARGETSERVER}:${BACKUPDIR}
   ```
   {: pre}

### Restoring the target system
{: #sapmig-db-db2-option2-restore}

Use the following steps to restore the target system.

1. The database restore is done on the target server:

   ```sh
   ssh $DB2ADM@$TARGETSERVER
   ```
   {: pre}

   The target server cannot know the environment variables that are defined on the source system. But the variables `$DBNAME`, `$TIMESTAMP` and `$SIDADM` are needed on the target system.
   To define these variables, run the list of commands that you saved in step [Preparing for migration](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option2-preparation) and [Creating an online backup from the source system database](/docs/sap?topic=sap-sapmig-db-db2#sapmig-db-db2-option2-backup) or define the environment variables again.
   {: requirement}

1. Use the online backup to restore to the database.

   ```sh
   db2 restore database $DBNAME \
       from $BACKUPDIR          \
       taken at $TIMESTAMP
   ```
   {: pre}

   Db2 command asks if you want to overwrite the existing database by restoring the backup:

   ```text
   SQL2523W  Warning!  Restoring to an existing database that is different from the database on the backup image, but have matching names. The target database will be overwritten by the backup version. The Roll-forward recovery logs associated with the target database will be deleted. Do you want to continue? (y/n)
   ```
   {: screen}

1. Enter `y` and press Enter.

   The following example is the expected output:

   ```text
   DB20000I  The RESTORE DATABASE command completed successfully.
   ```
   {: screen}

   If the recovery was not successful, drop the database on the target system in advance with `db2 drop database $DBNAME` and redo the restore command.
   {: tip}

   In contrast to option 1, do not roll forward archive logs. HADR takes this state to synchronize changed data that is in the next step.
   If the source SAP system is operational, source data changes.

   To transfer the latest changes, configure IBM Db2 HADR database synchronization.

### Defining HADR local and remote service ports on the target and source system
{: #sapmig-db-db2-option2-define-services}

Use the following steps to define HADR local and remote service ports on the target and source system.

HADR uses two TCP ports to synchronize data.

1. Check that `/etc/services` does not contain these TCP port numbers on both systems.
   Configure port numbers in this file on both servers.

   If local and remote HADR ports are configured in `/etc/services`, IBM Db2 configuration is more readable by using service names instead of numbers.
   {: tip}

   In the example, ports `5920/tcp` and `5921/tcp` are used, but you can define different ports.

   Overview table of the port numbers to configure:

   | Services name      | Value on source server | Value on target server | Comment     |
   |--------------------|------------------------|------------------------|-------------|
   | db2th1ha_l         | `5921/tcp`               | `5920/tcp`               | Local port  |
   | db2th1ha_r         | `5920/tcp`               | `5921/tcp`               | Remote port |
   {: caption="TCP port assignment in /etc/services on both servers" caption-side="bottom"}

1. Use your favorite editor to change `/etc/services` on both servers.

1. Review if the configuration files are correct, by using the following command:

   ```sh
   grep -e '592[01]/' /etc/services
   ```
   {: pre}

   The following lines are the expected output on the source server:

   ```text
   db2th1ha_l        5921/tcp        # Db2 HADR local port
   db2th1ha_r        5920/tcp        # Db2 HADR remote port
   ```
   {: screen}

   Port numbers must be enabled on the target server. The following example is the expected output on the target node:

   ```text
   db2th1ha_l        5920/tcp        # Db2 HADR local port
   db2th1ha_r        5921/tcp        # Db2 HADR remote port
   ```
   {: screen}

### Configuring HADR on the target and source system
{: #sapmig-db-db2-option2-hadr-configure}

HADR needs the following set of configurations.

| Db2 HADR parameter | Value on source server    | Value on target server    | Comment  |
|--------------------|---------------------------|---------------------------|----------|
| HADR_LOCAL_HOST    | \<source hostname\>       | \<target hostname\>       | Hostname of the system that you are on         |
| HADR_LOCAL_SVC     | db2th1ha_l                | db2th1ha_l                | Local port as defined in `/etc/services`  |
| HADR_REMOTE_HOST   | \<target hostname\>       | \<source hostname\>       | The other host's hostname                  |
| HADR_REMOTE_SVC    | db2th1ha_r                | db2th1ha_r                | Remote port as defined in `/etc/services` |
| HADR_REMOTE_INST   | \<db2 instance name\>     | \<db2 instance name\>     | The other node's IBM Db2 instance name (not the database name) |
| LOGINDEXBUILD      | `ON`                      | `ON`                      | Set to `ON` on for both hosts             |
| HADR_SYNCMODE      | \<a valid sync mode\>     | \<a valid sync mode\>     | See [HADR Synchronization Mode](https://www.ibm.com/docs/en/db2/11.5?topic=hadr-synchronization-mode){: external} |
{: caption="HADR parameter overview, both servers" caption-side="bottom"}

Local and remote hostnames (`HADR_LOCAL_HOST` and `HADR_REMOTE_HOST`) must be turned on for both systems. `HADR_LOCAL_HOST` is always the hostname of the node. The configuration command is run on and the remote host is the hostname of the respective other system.

Local and remote service entries (`HADR_LOCAL_SVC` and `HADR_REMOTE_SVC`) are identical because the switch is already configured in `/etc/services`.

[High availability disaster recovery (HADR) synchronization mode](https://www.ibm.com/docs/en/db2/11.5?topic=hadr-synchronization-mode){: external} explains different IBM Db2 HADR synchronization options and their benefits.
{: note}

Use the following commands to configure both systems:

1. Local host is the system that the `hostname` command reports to:

   ```sh
   db2 "update db cfg for $DBNAME using HADR_LOCAL_HOST `hostname`"
   ```
   {: pre}

1. Local port is the local port definition from `/etc/services`:

   ```sh
   db2 "update db cfg for $DBNAME using HADR_LOCAL_SVC db2th1ha_l"
   ```
   {: pre}

1. For the remote host, verify that `$TARGETSERVER` points to the other server:

   ```sh
   db2 "update db cfg for $DBNAME using HADR_REMOTE_HOST $TARGETSERVER"
   ```
   {: pre}

1. The remote port is the remote port definition from `/etc/services`:

   ```sh
   db2 "update db cfg for $DBNAME using HADR_REMOTE_SVC db2th1ha_r"
   ```
   {: pre}

1. Configure the remote IBM Db2 instance.

   As orientation, if the database name is `th1`, then the instance name looks like `db2th1`:

   ```sh
   db2 "update db cfg for $DBNAME using HADR_REMOTE_INST $DBINST"
   ```
   {: pre}

1. Make sure that the log index build is set to ON on both systems:

   ```sh
   db2 "update db cfg for $DBNAME using LOGINDEXBUILD ON"
   ```
   {: pre}

1. Define the HADR synchronization mode.
   The value `async` is an example, change it to match the [HADR Synchronization Mode](https://www.ibm.com/docs/en/db2/11.5?topic=hadr-synchronization-mode){: external} suitable for your environment:

   ```sh
   db2 "update db cfg for $DBNAME using HADR_SYNCMODE async"
   ```
   {: pre}

### Starting HADR and checking the synchronized data
{: #sapmig-db-db2-option2-hadr-start}

Use the following steps to start HADR and to check whether the data is syncing.

1. Start HADR on the target server in standby.
   Switch to the target server as `$DB2ADM`, and run the following command:

   ```sh
   db2 start hadr on db $DBNAME as standby
   ```
   {: pre}

   Expected output looks like the following example:

   ```text
   DB20000I  The START HADR ON DATABASE command completed successfully.
   ```
   {: screen}

1. Start HADR on the source server as the primary.
   Switch to the source server as `$DB2ADM` and run the following command:

   ```sh
   db2 start hadr on db $DBNAME as primary
   ```
   {: pre}

   Expected output looks like the following example:

   ```text
   DB20000I  The START HADR ON DATABASE command completed successfully.
   ```
   {: screen}

1. Verify the HADR state with:

   ```sh
   db2pd -d $DBNAME -hadr
   ```
   {: pre}

   And check for these fields:

   | Field               | Source server | Target server |
   |---------------------|---------------|---------------|
   | HADR_ROLE           | `PRIMARY`     | `STANDBY`     |
   | HADR_STATE          | `PEER`        | `PEER`        |
   | HADR_CONNECT_STATUS | `CONNECTED`   | `CONNECTED`   |
   {: caption="HADR Status Values, both servers" caption-side="bottom"}

### Running the core migration
{: #sapmig-db-db2-option2-migration}

Use the following steps to start migrating the SAP system from source server to the target server.

Adjust the SAP system DNS record to point to the target SAP server. This adjustment makes sure that client systems (such as the system that runs the SAP logon GUI) connect to the target server.
{: important}

1. Stop the source servers SAP system that includes the Db2 database.

   On the source system login as `$SIDADM` and run the following command:

   ```sh
   stopsap
   ```
   {: pre}

   Wait for the command to complete.

1. Switch to the target server as `$DB2ADM` and initiate a takeover by using the following command:

   ```sh
   db2 takeover hadr on database $DBNAME
   ```
   {: pre}

   The takeover command has an option `by force` that can help if the source system wasn't cleanly shut down. For more information, see [TAKEOVER HADR command](https://www.ibm.com/docs/en/db2/11.5?topic=commands-takeover-hadr){: external}.
   {: tip}

1. As `$DB2ADM` on the target server. Verify that the IBM Db2 HADR role changed from `standby` to `primary` by using the following command:

   ```sh
   db2pd -d th2 -hadr | grep ROLE
   ```
   {: pre}

1. If the IBM Db2 HADR role is `primary` on the target server, you can start the SAP system on the target server.
   Switch to the `$SIDADM` user and run the following command:

   ```sh
   startsap
   ```
   {: pre}

   The IBM Db2 database synchronization is still configured, although not active at this stage.

   Leave HADR configuration as is if the target system is planned to migrate back (as for a disaster scenario).
   If the source system is removed after the migration, remove the HADR configuration as described in [How to remove existing HADR configuration](https://www.ibm.com/support/pages/how-remove-existing-hadr-configuration){: external}.

The migration is complete.

- SAP system on the source server is down
- SAP system on the target server is up and running
