---
copyright:
  years: 2024, 2025
lastupdated: "2025-02-07"

keywords: SAP, SAP Monitoring, {{site.data.keyword.cloud_notm}}, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Checking and preparing SAP system for monitoring
{: #mon-preparing-system}

Use the following information to check and prepare your SAP system for monitoring.

## Collecting SAP system parameters
{: #mon-collecting-sap-parameters}

Gather parameter values from an SAP system.
The values are used to replace variables in the configuration files of the monitoring instance.
The table [SAP variables description](#mon-vars-description) describes details about the values that are assigned to the corresponding variables.
For more information, see [Determine SAP Parameters](/docs/sap?topic=sap-mon-parameter-harvesting-troubleshooting-maintenance#mon-determining-parameters).

SAP variables description:
{: #mon-vars-description}

| Variable name                      | Description           |
| ----------------------------- | --------------------- |
| `sap_monitoring_nr`        | Two-digit incremental number that starts with 01 up to 99. This ID is not an existing SAP system ID, but an arbitrary number to map a targeted SAP system. |
| `sap_monitoring_solution_name` |  Name of the targeted SAP system, for example: "PM2 6.1 (S/4 2022)" might include SID, SAP Product Version, SAP HANA edition. |
| `sap_hana_ip` | IPv4 address of the SAP HANA DB server. |
| `sap_ascs_ip` | IPv4 address of the SAP ASCS server. |
| `sap_app_server_ip_01`| IPv4 address of the first SAP (primary) application server. |
| `sap_app_server_ip_02` | The optional IPv4 address of the second SAP application server. |
| `sap_app_server_ip_03`| The optional IPv4 address of the third SAP application server. |
| `sap_app_server_ip_<n>` | The optional IPv4 address of any other SAP application servers. |
| `sap_hana_http_port`| HTTP port of the sapstartsrv web service on the SAP HANA server. |
|  `sap_ascs_http_port` | HTTP port of the sapstartsrv web service of the ASCS (ENQ and MSG Central Services) on the SAP ASCS server. |
|  `sap_app_server_port_01`| HTTP port of the sapstartsrv web service on the first application server. |
|  `sap_app_server_port_02`| HTTP port of the sapstartsrv web service on the second application Server. |
|  `sap_app_server_port_03` | HTTP port of the sapstartsrv web service on the third application server. |
|  `sap_app_server_port_<n>`| HTTP port of the sapstartsrv web service on the `n` application Server. |
|  `sap_hana_sql_systemdb_port`| SQL access port to the system database on the SAP HANA server. |
| `sap_hana_sql_systemdb_user` | The new SQL user of the system database that was created with `ReadOnly` permissions. |
| `sap_hana_sql_systemdb_password` | The new password of `sap_hana_sql_systemdb_user` that was created with `ReadOnly` permissions. |
| `ibmcloud_monitoring_instance_url` | URL for Prometheus remote write endpoint of the monitoring instance. The keyword `private.` is added to the URL and is used in the Prometheus agent configuration. Example `https://ingest.prws.private.eu-de.monitoring.cloud.ibm.com/prometheus/remote/write`. |
| `ibmcloud_monitoring_authorization_credentials` | Credentials for authorization to identify a monitoring instance in the customer account that is used in the Prometheus agent configuration file. |
{: caption="SAP variables description" caption-side="bottom"}

The values for variables such as `<ibmcloud_monitoring_instance_url>` and `<ibmcloud_monitoring_authorization_credentials>` are available after you create a monitoring instance.
For more information, see [Creating of monitoring instance in the IBM Cloud](/docs/sap?topic=sap-mon-create-instance).

You can set up two extra port numbers that you can use on the monitoring host in configuration files and exposed ports.
Use the variable `<sap_monitoring_nr>` to assign a unique variable value for each monitored SAP system.
This variable is used to compose the HTTP port numbers as listed in [Table 2. Exposed ports for services](#mon-exposed-ports).

The variable `<sap_monitoring_nr>` is a two-digit incremental number between 01 and 99.
For example, the number 01 is used to map the first (or only one existing) SAP system as `sap_monitoring_nr = 01`.
{: note}

The following port numbers are the required port numbers for an SAP system that is used as exposed ports on the monitoring host.
The corresponding services are based on the [SAP HANA Database exporter](https://github.com/SUSE/hanadb_exporter/tree/main){: external} variables.

| Exposed port numbers | Service description|
| ----------------------------- | --------------------- |
| `5<sap_monitoring_nr>01`  |  Prometheus agent|
| `5<sap_monitoring_nr>02`  |  `hanadb_exporter`|
| `5<sap_monitoring_nr>03`  |  `sap_host_exporter` for the SAP HANA database. |
| `5<sap_monitoring_nr>04`  |  `sap_host_exporter` for the ABAP Central Service (ASCS) server. |
|  `5<sap_monitoring_nr>05` | `sap_host_exporter` is the primary or first SAP Application Server.|
| `5<sap_monitoring_nr>06`  | `sap_host_exporter` is the second SAP Application Server. |
| `5<sap_monitoring_nr>07`  | `sap_host_exporter` is the third SAP Application Server. |
{: caption="Exposed ports for services" caption-side="bottom"}
{: #mon-exposed-ports}

If you have multiple SAP systems, you need to differentiate between them.
This means that you have to adjust the configuration file names.
That is, the names of the `systemd` processes and the numbers of the locally exposed HTTP ports.
{: note}

## Checking the firewall settings on the SAP system
{: #mon-checking-firewall-settings-on-sap-system}

{{site.data.keyword.cloud}} provides configured ACLs (Access Control Lists) and security groups for each subnet.

On each SAP system, the local operating system firewall and SELinux/AppArmor are disabled by default.
If the operating system firewall is enabled, check the firewall settings on each SAP system for SQL ports and SAP instance ports.

If you decide to use the firewalld service, open the ports that are used on the SAP system and on the monitoring host.
Use the following commands to allow additional ports in the firewalld service.

```sh
    firewall-cmd --zone=public --permanent --add-port ${port_number}/tcp
```
{: pre}

```sh
    systemctl reload firewalld
```
{: pre}

## Creating an SAP HANA database monitoring user with ReadOnly permissions
{: #mon-creating-new-readonly-hdb-user}

Create a new SAP HANA database user with `ReadOnly` permissions for a monitoring role.

Do not use a user with administrator privileges for monitoring of the SAP HANA database.
{: attention}

Run the following commands as the SAP HANA database system administrator user to create a new user for the monitoring role.
Use the SAP HANA command line tool hdbsql and execute the commands for all databases.
For more information, see the section [Determine SAP Parameters](/docs/sap?topic=sap-mon-parameter-harvesting-troubleshooting-maintenance#mon-determining-parameters).

```sh
CREATE USER <sap_hana_sql_systemdb_user> PASSWORD <sap_hana_sql_systemdb_password> \
NO FORCE_FIRST_PASSWORD_CHANGE;
CREATE ROLE HANADB_EXPORTER_ROLE;
GRANT MONITORING TO HANADB_EXPORTER_ROLE;
GRANT HANADB_EXPORTER_ROLE TO <sap_hana_sql_systemdb_user>;
```
{: pre}

## Configuring a user name and password free setup for the monitoring of SAP systems
{: #mon-configuring-user-password-free-setup}

Configure your SAP System to allow the monitoring queries without user and password authentication.
{: shortdesc}

With default configuration, all `sapstartsrv` methods that might change the status of the instance or the system are protected methods.
Protected methods can be run only after a successful user authentication.
Configure the `sapstartsrv` services that the methods required for the monitoring queries are unprotected.

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
{: #mon-preparing-system-next-steps}

After checking and preparing the SAP system, you can proceed to the next step [Creating of monitoring instance in {{site.data.keyword.cloud}}](/docs/sap?topic=sap-mon-create-instance).
