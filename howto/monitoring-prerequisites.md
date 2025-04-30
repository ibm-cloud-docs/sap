---
copyright:
  years: 2024, 2025
lastupdated: "2025-04-29"
keywords: SAP, SAP Monitoring, {{site.data.keyword.cloud_notm}}, {{site.data.keyword.ibm_cloud_sap}}, {{site.data.keyword.powerSys_notm}}, Monitoring
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Prerequisites
{: #monitoring-prereqs}

Prerequisite is a running SAP HANA system. This guide doesn't detail the installation of SAP HANA database and SAP NetWeaver solution.
{: note}

This document outlines the necessary steps a user must follow to successfully implement a monitoring solution for SAP.

## Gather SAP parameters
{: #monitoring-prereqs-gather-sap-parameters}

Gather the below parameter values from an existing SAP system running in IBM cloud. These values are required by the [SAP HANA DB exporter](https://github.com/SUSE/hanadb_exporter){: external} which will be installed and confiured on the x86_64 Virtual Server Instance runnning in {{site.data.keyword.vpc_short}}.

For more information, see [Determine SAP Parameters](/docs/sap?topic=sap-mon-parameter-harvesting-troubleshooting-maintenance#mon-determining-parameters).

### Parameters from HANA DB server
{: #monitoring-prereqs-sap-parameters-hanadb}

1. **IPv4 address** of the SAP HANA DB server.
1. [SQL access port](/docs/sap?topic=sap-mon-parameter-harvesting-troubleshooting-maintenance#mon-determine-sql-ports) to the system database on the SAP HANA server.
1. [HTTP port](/docs/sap?topic=sap-mon-parameter-harvesting-troubleshooting-maintenance#mon-list-https-port-of-sap-instance) of the sapstartsrv web service on the SAP HANA server.
1. **SQL user credentials** of the system database. If you want to create a user with `ReadOnly` permissions, see [Creating a Read only system user](#monitoring-create-readonly-hdb-user)

### Parameters from SAP NetWeaver server
{: #monitoring-prereqs-sap-parameters-nw}

1. **IPv4 address** of server where the SAP ASCS instance is running.
1. **IPv4 address** of server where the SAP PAS instance is running.
1. **IPv4 address** of server where the SAP AAS instance is running (if any).
1. [HTTP port](/docs/sap?topic=sap-mon-parameter-harvesting-troubleshooting-maintenance#mon-list-https-port-of-sap-instance) of the sapstartsrv web service on the server where the SAP ASCS instance is running (for ENQ and MSG services).
1. [HTTP port](/docs/sap?topic=sap-mon-parameter-harvesting-troubleshooting-maintenance#mon-list-https-port-of-sap-instance) of the sapstartsrv web service on the server where the SAP PAS instance is running.
1. [HTTP port](/docs/sap?topic=sap-mon-parameter-harvesting-troubleshooting-maintenance#mon-list-https-port-of-sap-instance) of the sapstartsrv web service on the server where the SAP AAS instance is running(if any).

The following port numbers will be exposed from the **x86_64 virtual server instance** running in {{site.data.keyword.vpc_short}} which servers as a **monitoring host**.
The corresponding services are based on the [SAP HANA Database exporter](https://github.com/SUSE/hanadb_exporter/tree/main){: external} variables.

| Exposed port numbers      | Service description                                                |
| --------------------------| ------------------------------------------------------------------ |
| `5<sap_monitoring_nr>01`  |  Prometheus agent                                                  |
| `5<sap_monitoring_nr>02`  | `hanadb_exporter`                                                  |
| `5<sap_monitoring_nr>03`  | `sap_host_exporter` for the SAP HANA database.                     |
| `5<sap_monitoring_nr>04`  | `sap_host_exporter` for the ABAP Central Service (ASCS) server.    |
| `5<sap_monitoring_nr>05`  | `sap_host_exporter` is the primary or first SAP Application Server.|
| `5<sap_monitoring_nr>06`  | `sap_host_exporter` is the second SAP Application Server.          |
| `5<sap_monitoring_nr>07`  | `sap_host_exporter` is the third SAP Application Server.           |
{: caption="Exposed ports for services" caption-side="bottom"}
{: #monitoring-prereqs-exposed-ports}

If you have multiple SAP systems, you need to differentiate between them.
This means that you have to adjust the configuration file names.
That is, the names of the `systemd` processes and the numbers of the locally exposed HTTP ports.
{: note}

## Check firewall settings on the SAP system
{: #monitoring-prereqs-check-firewall-settings}

{{site.data.keyword.cloud}} provides configured ACLs (Access Control Lists) and security groups for each subnet.

* On each SAP system, the local operating system firewall and SELinux/AppArmor are disabled by default.
* If the operating system firewall is enabled, check the firewall settings on each SAP system for SQL ports and SAP instance ports.
* If you decide to use the firewalld service, open the ports that are used on the SAP system and on the monitoring host(x86_64 Virtual Server Instance running in {{site.data.keyword.vpc_short}}).
* Use the following commands to allow additional ports in the firewalld service.

```sh
    firewall-cmd --zone=public --permanent --add-port ${port_number}/tcp
```
{: pre}

```sh
    systemctl reload firewalld
```
{: pre}

## Creating an SAP HANA database monitoring user with ReadOnly permission(Optional)
{: #monitoring-create-readonly-hdb-user}

Skip this step if you already have a read only user and you are okay to use the same SAP HANA database user for monitoring role.

Do not use a user with administrator privileges for monitoring of the SAP HANA database.
{: attention}

To create a new SAP HANA database user with `ReadOnly` permissions for a monitoring role run the following commands
1. as the SAP HANA database system administrator user.
1. using the SAP HANA command line tool `hdbsql`.

For more information, see the section [Determine SAP Parameters](/docs/sap?topic=sap-mon-parameter-harvesting-troubleshooting-maintenance#mon-determining-parameters).

```sh
CREATE USER <sap_hana_sql_systemdb_user> PASSWORD <sap_hana_sql_systemdb_password> \
NO FORCE_FIRST_PASSWORD_CHANGE;
CREATE ROLE HANADB_EXPORTER_ROLE;
GRANT MONITORING TO HANADB_EXPORTER_ROLE;
GRANT HANADB_EXPORTER_ROLE TO <sap_hana_sql_systemdb_user>;
```
{: pre}

## Setting Up passwordless authentication for SAP monitoring
{: #monitoring-passwordless-authentication}

Configure your SAP System to allow the monitoring queries without user and password authentication.
{: shortdesc}

By default, all `sapstartsrv` methods that can modify the status of an instance or the system are classified as protected methods. These methods can only be executed after successful user authentication. To enable monitoring without authentication, configure the `sapstartsrv` service so that the methods required for monitoring queries are treated as unprotected.


```sh
SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList -GetEnvironment -ABAPGetSystemWPTable
```
{: screen}

Configure this setting for the following SAP instances:

* `HDB<ID>` on the SAP HANA database host
* `ASCS<ID>` on the SAP Application Server host
* `D<ID>` on all corresponding SAP Application Server hosts

Follow these steps for all `sapstartsrv` services.

1. List the instances and instance numbers.

   ```sh
   /usr/sap/hostctrl/exe/lssap
   ```
   {: pre}

1. Show the operating system user of the `sapstartsrv` service.

   ```sh
   ps aux|grep sapstart
   ```
   {: pre}

1. Switch the shell to `sapstartsrv` operating system user.

   ```sh
   su - <OS-user of sapstartsrv>
   ```
   {: pre}

1. List the configured web methods by using the instance numbers that were extracted with lssap.

   ```sh
   sapcontrol -nr <ID> -function ParameterValue service/protectedwebmethods
   ```
   {: pre}

1. If the output shows the following result,

   ```sh
   SDEFAULT
   ```
   {: screen}

then, the following configuration is required.

Add a line to the SAP default profile:

1. List the configuration files.

   ```sh
   sapcontrol -nr <ID> -function ListConfigFiles
   ```
   {: pre}

1. Edit the file named `/usr/sap/<sid>/SYS/profile/<sid>_<instance-name>_<host-name>`

1. Add a line at the end or change the existing line, if the entry `service/protectedwebmethods` exists.

   ```sh
   #IBM SAP monitoring
   service/protectedwebmethods = SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList -GetEnvironment -ABAPGetSystemWPTable
   ```
   {: screen}

1. Save the configuration file without changing the file name.

1. Restart the corresponding services.

   ```sh
   sapcontrol -nr <ID> -function RestartService
   ```
   {: pre}

   The restart might take some time.

1. Check the status by running the following command.

   ```sh
   sapcontrol -nr <ID> -function GetSystemInstanceList
   ```
   {: pre}

   The status shows `GREEN/ GRAY /YELLOW`.

   If the service does not return to **GREEN**, see the troubleshooting section.

1. Verify that the configuration change was successful.

   ```sh
   sapcontrol -nr <ID> -function ParameterValue service/protectedwebmethods
   ```
   {: pre}

   The following output is shown, if successful:

   ```sh
   SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList -GetEnvironment –ABAPGetSystemWPTable
   ```
   {: screen}

## Next steps
{: #monitoring-prereqs-next-steps}

After the prerequisites are met, you can proceed to the next step [Creating an {{site.data.keyword.mon_full_notm}} Instance](/docs/sap?topic=sap-mon-create-instance).
