---
copyright:
  years: 2024
lastupdated: "2024-03-27"

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, Hybrid Cloud, Migration, Linux, RHEL, SuSE, HANA, HSR, HANA System Replication, Synchronize, sync, migration step

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Migrating SAP S/4HANA by using SAP HANA System Replication
{: #sapmig-db-hana-replication}

## Pre-checks before you configure SAP HANA System Replication
{: #sapmig-db-hana-sr-prechecks}

Before you configure SAP HANA System Replication, a few prerequisites must be checked.
The described Steps are valid for Red Hat Enterprise Linux 8 (RHEL) and SUSE Enterprise Linux (SLES).
{: shortdesc}

### Check the SAP HANA database user on the source system
{: #sapmig-db-hana-sr-user-prechecks}

Check with your SAP basis administration team or SAP HANA administrators, which SAP HANA database user is used to access the system.
Typically this user the `SYSTEM` user, or the SAP schema owner user if your SAP basis administration team implemented the SAP security advisories.

## SAP HANA landscape pre-steps for activating SAP HANA System Replication
{: #sapmig-db-hana-sr-oncloud-onprem-prechecks}

### Set the environment variables on the primary and secondary SAP HANA systems
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

### Make sure that SAP HANA is running on both systems
{: #sapmig-db-hana-sr-oncloud-onprem-started}

As the operating system user `${sid}adm`, the command `HDB proc` can be used to verify that all services are started.

Run the following command on both systems, primary and secondary SAP HANA server.

```sh
sudo -i -u ${sid}adm -- HDB proc
```
{: pre}

### SAP HANA version must be equal or greater than the primary server
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

### Check that the configuration parameter `log_mode` is set to normal
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

### Register the primary server first
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

### Check whether the primary system is registered
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

### Make sure that SAP HANA is not active on the secondary site
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

### Register the secondary system
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

### Troubleshoot hdbnsutil errors with SELinux enabled
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

## Check the state on both sides of the SAP HANA System Replication
{: #sapmig-db-hana-sr-onprem-state}

### Check the primary system state
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

### Check the secondary system state
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

### Restart the secondary server
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

### Check replication status
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

### Four methods to check the system replication status
{: #sapmig-db-hana-sr-replication-status-checks}

#### Option 1. landscapeHostConfiguration.py
{: #sapmig-db-hana-sr-landscapehostconfiguration}

The first option uses the Python script `landscapeHostConfiguration.py` for a server point of view.
This script displays a status line per SAP HANA server system.

Run the following command.

   ```sh
   sudo -i -u ${sid}adm -- python ${DIR_INSTANCE}/exe/python_support/landscapeHostConfiguration.py
   ```
   {: pre}

Make sure that each server that is listed in the output shows `OK` in the host status column.

#### Option 2. systemReplicationStatus.py
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

#### Option 3. hdbcons
{: #sapmig-db-hana-sr-hdbcons}

Check the detailed status of the system replication with the `hdbcons`command and run as `${sid}adm` user.
This third option is a technical per server and per service view.

Run the SAP HANA DB Management Client Console `hdbcons` with the following command.

   ```sh
   sudo -i -u ${sid}adm -- hdbcons -e hdbindexserver "replication info"
   ```
   {: pre}

#### Option 4. Sql script
{: #sapmig-db-hana-sr-sql-script}

The fourth alternative uses an SQL statement that can be run, for example, in SAP HANA studio or cockpit.
This option is a hosts-per-site-view of SAP HANA system replication.

Check by running the following SQL statement.

   ```sql
   select host, SECONDARY_HOST, PORT, SITE_NAME, SECONDARY_SITE_NAME, REPLICATION_MODE, REPLICATION_STATUS, REPLICATION_STATUS_DETAILS,SECONDARY_ACTIVE_STATUS from M_SERVICE_REPLICATION;
   ```
   {: pre}

Check especially columns `REPLICATION_STATUS` and `REPLICATION_STATUS_DETAILS` in the SQL output.

### Post replication completion
{: #sapmig-db-hana-sr-onprem-oncloud-post-replication}

Before you disable the replication setup, check the trace logs for any inconsistencies or anomalies after the replication action is performed.
After the replication completes, the database contains all active services on the primary system only.
But you can still examine the trace logs for any inconsistencies or issues.

## Checking the database trace files
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

## SAP HANA System Replication resources
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
