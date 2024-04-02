---
copyright:
  years: 2024
lastupdated: "2024-03-05"

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, on-prem, on premises, Hybrid Cloud, Migration, Linux, Redhat, RHEL, SuSE, HANA, HSR, HANA System Replication, clean up, pre migration step, Row Store Re-organization, reorg, row, store

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Creating the Target SAP HANA System on IBM Power Virtual Server
{: #sapmig-db-hana-presteps-target}

## Planning the IBM Power Virtual Server deployment
{: #mig-db-hana-target-system-pre-deploy}

A Power Server workspace in your IBM Cloud account is a prerequisite for the following steps.
Read details in [Hybrid Cloud Network Considerations for SAP on IBM Power Virtual Server](/docs/sap?topic=sap-sapmig-hybrid-cloud-networking).

A hybrid cloud network connection needs to be in place, as described in [Hybrid Cloud Network Considerations for SAP on IBM Power Virtual Server](/docs/sap?topic=sap--sapmig-hybrid-cloud-networking).

The Planning for a deployable SAP HANA infrastructure is described in [Planning your deployment](/docs/sap?topic=sap-power-vs-planning-items).

The sizing aspect of the target system is vital to your planning.
Follow the recommendations mentioned in the SAP HANA Sizing report on the source system. Also, consider the findings of the EarlyWatch Alert report (EWA report).
Both factors provide a realistic approach on the recommended size of your target system, see
[Sizing process for SAP Systems](/docs/sap?topic=sap-sizing).

## Comparing the required CPU, cores and storage for your target system
{: #mig-db-hana-target-system-certified-profile}

- [IBM Cloud Doclink SAP Planning/Sizing](/docs/sap?topic=sap-planning-your-system-landscape)

Check that the certified profiles in {{site.data.keyword.cloud}} are close to or match the recommendations that are mentioned in the source system sizing report and also consider the EWA report summary.

Select the correct IBM Power Systems Virtual Server Certified Profile from the following two links:

- [IBM Power VS Certified Profiles SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-power-vs)
- [SAP Note 2947579 - SAP HANA on IBM Power Virtual Servers](https://me.sap.com/notes/2947579){: external}

### Target server must have equal or greater storage capacity than the source system and be sized correctly
{: #mig-db-hana-target-system-storage-tdi}

Remember to take SAP HANA database growth into consideration and the need to follow the IBM System Storage Architecture and Configuration Guidelines for SAP HANA TDI.

The following document outlines the required storage configuration for the target server in IBM Cloud:
- [IBM System Storage Architecture and Configuration Guide for SAP HANA TDI](https://www.ibm.com/support/pages/ibm-system-storage-architecture-and-configuration-guide-sap-hana-tdi-0){: external}

Consider the extra space that you need to create a file system mount point to store software executable files and the initial SAP HANA system backup.
Depending on your planned IBM Cloud PowerVS infrastructure, you can create the file system as an NFS mount to export to other systems in the architecture.

## Creating the software repository file system and transfer the installation packages
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

## Unpacking the files
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

### Making sure that the target server OS and patch level match the source server
{: #mig-db-hana-target-system-os-patch-level}

Check the operating system version and patch level on the target system.
For productive systems, the same level makes sure that the installation performs similar and migrating runs with ease.
For nonproductive systems, for example, a proof of concept system (POC) in IBM PowerVS, a higher operating system version is a valid option.

#### Target server - Both RHEL and SLES
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

### Making sure that the file system and mount points match the source system
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

### Entries in `/etc/hosts` for all systems involved in the migration project
{: #mig-db-hana-target-system-network-hosts-file}

The `/etc/hosts` file needs to contain entries for the Source System and any dependent SAP Netweaver or S/4 FES Application Server.
You can use a DNS server for your network resource resolution, but it helps if you include the IP addresses, short name, FQDN, and description to help identify servers in the landscape in the `/etc/hosts` file, especially if issues occur with network resolution or the DNS services.

### Preparing and tuning the OS for SAP HANA
{: #mig-db-hana-target-system-Preparation-tuning}

Use the following SAP Notes to begin the preparation phase of the target system for the installation of SAP HANA.

* [SAP Note 2777782 - SAP HANA DB: Recommended OS Settings for RHEL 8](https://me.sap.com/notes/2777782){: external}
* [SAP Note 2772999 - Red Hat Enterprise Linux 8.x: Installation and Configuration](https://me.sap.com/notes/2772999){: external}
* [SAP Note 3018133 - Linux: Running SAP applications compiled with GCC 10.x](https://me.sap.com/notes/3018133){: external}

Make sure that you completed the tasks that are mentioned in the `Recommended OS Settings for RHEL 8` as these tasks are important tuning and performance settings that need to be applied. If ignored, it can impact the installation of SAP products and the performance thereafter.

### Pre-SAP HANA checks by using the hcmt tool
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

#### Hcmt server session
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

#### Hcmt client session
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

### Installing SAP HANA on the target system
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

### Checking that SAP HANA is running and determining the version
{: #mig-db-hana-target-system-status}

Run the following `HDB proc` command to verify that all services started on the primary and secondary SAP HANA system.

```sh
sudo -i -u ${sid}adm -- HDB proc
```
{: pre}

### SAP HANA version needs to be equal or greater than the primary server
{: #mig-db-hana-target-system-version}

To verify the SAP HANA database version, use the following command on both nodes.

```sh
sudo -i -u ${sid}adm -- HDB version
```
{: pre}

### Initial backup of the MDC/SYSTEMDB SAP HANA database
{: #mig-db-hana-target-system-backup}

#### Backup SYSTEMDB
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

#### Backup MDC
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

#### Check backup status
{: #mig-db-hana-target-system-check-backup-status}

On the SYSTEMDB entry, -> right click and select the following actions.

- Backup & Recovery
- Select Open `Backup Console`
- Select the tab `Backup Catalog`
- In the Database Field select `${sid}`for the MDC
- In the Database Field select `<SYSTEMDB>`for the SYSTEMDB

### Optional check of the trace log files
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
