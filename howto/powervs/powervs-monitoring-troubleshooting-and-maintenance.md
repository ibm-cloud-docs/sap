---
copyright:
  years: 2024, 2025
lastupdated: "2025-02-13"
keywords: SAP, SAP Monitoring, {{site.data.keyword.cloud_notm}}, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Parameter harvesting, troubleshooting, and maintenance
{: #mon-parameter-harvesting-troubleshooting-maintenance}

Use the following information for SAP parameter determination, troubleshooting, and maintenance.
{: shortdesc}

## Determining SAP parameters
{: #mon-determining-parameters}

Use the following commands to determine SAP parameters on your SAP HANA database and SAP application servers.

### List the sapstartsrv services and operating system user of the processes
{: #mon-list-sapstartsrv-services-and-os-user}

As a user `root`, use the following command to list the `sapstartsrv` services and the operating system user.

```sh
ps aux|grep sapstartsrv
```
{: pre}

### List details such as SID, Nr, Instance, SAPLOCALHOST, Version, DIR_EXECUTABLE
{: #mon-list-details-with-lssap}

As a root user, use the following command to list SAP HANA database and SAP application server details.

```sh
/usr/sap/hostctrl/exe/lssap
```
{: pre}

### Log in as an SAP administrator
{: #mon-log-in-as-sap-admin}

Use the following command to log in as an SAP administrator.

```sh
su - <SID>adm
```
{: pre}

### List HTTP/HTTPS ports of an SAP instance
{: #mon-list-https-port-of-sap-instance}

As an SAP administrator, use the following command to list the HTTP/HTTPS ports of an SAP instance.

```sh
sapcontrol -nr <instance_nr> -function GetSystemInstanceList
```
{: pre}

The following details are displayed: hostname, instanceNr, httpPort, httpsPort, startPriority, features, and dispstatus.

### Determine the SQL ports of the SAP SYSTEMDB and TenantDB
{: #mon-determine-sql-ports}

As an SAP administrator, use the following commands to determine the SQL ports of the SAP SYSTEMDB and TenantDB.

```sh
hdbsql -i <instance_nr> -d SYSTEMDB -u SYSTEM -p <sap_hana_sql_systemdb_password>

```
{: pre}

```sh
SELECT * FROM SYS_DATABASES.M_SERVICES
```
{: pre}

The following details are displayed: `DATABASE_NAME, HOST, PORT, SERVICE_NAME, PROCESS_ID, DETAIL, ACTIVE_STATUS, SQL_PORT, COORDINATOR_TYPE, IS_DATABASE_LOCAL`.

```sh
quit
```
{: pre}

### Show the status of an SAP HANA database
{: #mon-show-sap-hana-database-status}

As an SAP administrator, use the following commands to show the status of an SAP HANA database.

```sh
hdbsql -i <instance_nr> -d SYSTEMDB -u SYSTEM -p  <sap_hana_sql_systemdb_password>
```
{: pre}

```sh
SELECT * FROM SYS.M_DATABASES
```
{: pre}

The following details are displayed: `DATABASE_NAME, DESCRIPTION, ACTIVE_STATUS, ACTIVE_STATUS_DETAILS, OS_USER, OS_GROUP, RESTART_MODE, FALLBACK_SNAPSHOT_CREATE_TIME`.

```sh
quit
```
{: pre}

## Troubleshooting missing data on the monitoring dashboards
{: #mon-troubleshooting-missing-dashboard-data}

If you don't see any metric data on the dashboard and all exporters are configured and running, the missing data might be due to one of the following reasons.

   * Network ports are not open due to firewall settings or VPC ACL settings.
   * Incorrect port numbers or credentials.

### Check whether the SAP HANA database is running
{: #mon-checking-if-hdb-is-running}

To check whether the SAP HAHA database is running, log in to the SAP HANA host and run the following commands.

```sh
su - <SID>adm
```
{: pre}

```sh
HDB info
```
{: pre}

```sh
ps aux|grep -i hdb
```
{: pre}

### Check a connection to an SAP HANA database with `hdbsql`
{: #mon-checking-connection-to-hdb-with-hdbsql}

To check a connection to an SAP HANA database with `hdbsql`, run the following command as an SAP administrator.

```sh
hdbsql -i <instance_nr> -d SYSTEMDB -u SYSTEM -p <sap_hana_sql_systemdb_password>
```
{: pre}

### List processes of the SAP Control instances and the `sapstartsrv services`
{: #mon-showing-control-instances-and-sapstartsrv}

To list the processes of the SAP Control instances and the `sapstartsrv services`, run the following command as the root user.

```sh
ps aux|grep sapstartsrv
```
{: pre}

### View listening ports of an SAP HANA host or application server
{: #mon-show-listening-ports-hdb-or-app-server}

To show the listening ports of an SAP HANA host or application server, use the following command.

```sh
ss -tulpen | grep sap
```
{: pre}

### Test the `hanadb_exporter' manually
{: #mon-manual-test-hanadb-exporter}

To manually test the `hanabd_exporter`, use the following command.

```sh
hanadb_exporter -c /etc/hanadb_exporter/config-${sap_monitoring_nr}-SQL.json \
-m /etc/hanadb_exporter/metrics.json
```
{: pre}

### View the status of the Prometheus daemons on the monitoring host
{: #mon-show-sap-daemons-status}

Use the following commands to show the status of the prometheus daemons on the monitoring host.

```sh
systemctl status prometheus@${sap_monitoring_nr}
```
{: pre}

```sh
systemctl status prometheus-hanadb_exporter@config-${sap_monitoring_nr}-SQL
```
{: pre}

```sh
systemctl status sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-HANA
```
{: pre}

```sh
systemctl status sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-ASCS
```
{: pre}

```sh
systemctl status sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-DI-01
```
{: pre}

### View the status of `firewalld service`
{: #mon-show-firewalld-service-status}

To show the status of the `firewalld service`, use the following command.

```sh
systemctl status firewalld
```
{: pre}

### Display SAP exporter metrics on the monitoring host
{: #mon-show-exporter-metrics-mhost}

To show the SAP exporter metrics on the monitoring host, use the following command.

Use the variables `<sap_monitoring_nr>` and the last two digits (default: 01) for service "Prometheus agent" that are in [Table 2](/docs/sap?topic=sap-mon-preparing-system#mon-exposed-ports).

```sh
curl http://localhost:5${sap_monitoring_nr}01/metrics
```
{: pre}

### List the HTTP/HTTPS ports of each SAP instance on the SAP systems
{: #mon-list-https-ports-all-instances}

To list the HTTPS port of each SAP instance, use the following command.

```sh
sapcontrol -nr <instance_nr> -function GetSystemInstanceList
```
{: pre}

### View details about each running SAP instance on the SAP systems
{: #mon-details-each-system}

```sh
sapcontrol -nr <instance_nr> -function GetProcessList
```
{: pre}

You can also debug the status of SAP HANA database and application service by using the commands that are in [Determine SAP Parameters](/docs/sap?topic=sap-mon-parameter-harvesting-troubleshooting-maintenance#mon-determining-parameters).

## Remove monitoring for an SAP system
{: #mon-remove-exporter}

To remove monitoring for an SAP system, you must stop and remove the corresponding Prometheus exporter on the monitoring host.
After you stop the exporter, the metrics will no longer be sent to the {{site.data.keyword.cloud}} monitoring instance.

To stop the Prometheus server for an SAP system, use the following command.

```sh
sudo systemctl stop prometheus@${sap_monitoring_nr}
```
{: pre}

Remove the Prometheus server `systemd` configuration for an SAP system by using the following command.

```sh
sudo systemctl remove prometheus@${sap_monitoring_nr}
```
{: pre}

To delete the corresponding Prometheus configuration, use the following command.

```sh
sudo rm /etc/prometheus/${sap_monitoring_nr}.yml
```
{: pre}

To stop and remove the `hanadb_exporter systemd` configuration, use the following commands.

```sh
sudo systemctl stop prometheus-hanadb_exporter@config-${sap_monitoring_nr}-SQL
```
{: pre}

```sh
sudo systemctl remove prometheus-hanadb_exporter@config-${sap_monitoring_nr}-SQL
```
{: pre}

To delete the `handb_exporter` configuration, use the following command.

```sh
sudo rm /etc/hanadb_exporter/config-${sap_monitoring_nr}-SQL.json
```
{: pre}

To stop and remove the `sap_host_exporter systemd` configuration, use the following commands.

```sh
sudo systemctl stop sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-HANA
```
{: pre}

```sh
sudo systemctl remove sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-HANA
```
{: pre}

```sh
sudo systemctl stop sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-ASCS
```
{: pre}

```sh
sudo systemctl remove sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-ASCS
```
{: pre}

```sh
sudo systemctl stop sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-DI-01
```
{: pre}

```sh
sudo systemctl remove sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-DI-01
```
{: pre}

```sh
sudo systemctl stop sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-DI-02
```
{: pre}

```sh
sudo systemctl remove sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-DI-02
```
{: pre}

To delete the `sap_host_exporter` configurations, use the following commands.

```sh
sudo rm /etc/sap_host_exporter/sap_host_exporter-${sap_monitoring_nr}-HANA.yaml
```
{: pre}

```sh
sudo rm /etc/sap_host_exporter/sap_host_exporter-${sap_monitoring_nr}-ASCS.yaml
```
{: pre}

```sh
sudo rm /etc/sap_host_exporter/sap_host_exporter-${sap_monitoring_nr}-DI-01.yaml
```
{: pre}

```sh
sudo rm /etc/sap_host_exporter/sap_host_exporter-${sap_monitoring_nr}-DI-02.yaml
```
{: pre}

## Optional changes to `/etc/services`
{: #mon-optional-changes-to-etc-service}

You can use the `/etc/services` file to map service names to port numbers on the local host.
The current setup doesn't require any changes to this file, but you can add the following optional changes.

```sh
SAP_prometheus_agent_<sap_monitoring_nr> 	5<sap_monitoring_nr>01/tcp  # SAP monitoring \ prometheus agent to <sap_monitoring_nr>
SAP_prometheus_agent_<sap_monitoring_nr>	5<sap_monitoring_nr>01/udp  # SAP monitoring \ prometheus agent to <sap_monitoring_nr>
SAP_hanadb_exporter_<sap_monitoring_nr>   5<sap_monitoring_nr>02/tcp  # SAP monitoring \ hanadb_exporter to <sap_monitoring_nr>
SAP_hanadb_exporter_<sap_monitoring_nr>  5<sap_monitoring_nr>02/udp  # SAP monitoring \ hanadb_exporter to <sap_monitoring_nr>
SAP_sap_host_exporter_HANA_<sap_monitoring_nr>   5<sap_monitoring_nr>03/tcp  # SAP monitoring \ sap_host_exporter HANA to <sap_monitoring_nr>
SAP_sap_host_exporter_HANA_<sap_monitoring_nr>   5<sap_monitoring_nr>03/udp  # SAP monitoring \ sap_host_exporter HANA to <sap_monitoring_nr>
SAP_sap_host_exporter_ASCS_<sap_monitoring_nr>   5<sap_monitoring_nr>04/tcp  # SAP monitoring \ sap_host_exporter ASCS to <sap_monitoring_nr>
SAP_sap_host_exporter_ASCS_<sap_monitoring_nr>   5<sap_monitoring_nr>04/udp  # SAP monitoring \ sap_host_exporter ASCS to <sap_monitoring_nr>
SAP_sap_host_exporter_DI_01_<sap_monitoring_nr>  5<sap_monitoring_nr>05/tcp  # SAP monitoring \  sap_host_exporter DI 01 to <sap_monitoring_nr>
SAP_sap_host_exporter_DI_01_<sap_monitoring_nr>  5<sap_monitoring_nr>05/udp  # SAP monitoring \ \ sap_host_exporter DI 01 to <sap_monitoring_nr>
SAP_sap_host_exporter_DI_n_<sap_monitoring_nr>   5<sap_monitoring_nr>06/tcp  # SAP monitoring \ sap_host_exporter DI 02 to <sap_monitoring_nr>
SAP_sap_host_exporter_DI_n_<sap_monitoring_nr>   5<sap_monitoring_nr>06/udp  # SAP monitoring \ sap_host_exporter DI 02 to <sap_monitoring_nr>
```
{: codeblock}
