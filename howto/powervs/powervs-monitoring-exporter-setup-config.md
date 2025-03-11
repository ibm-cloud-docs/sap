---
copyright:
  years: 2024, 2025
lastupdated: "2025-03-10"
keywords: SAP Monitoring, {{site.data.keyword.cloud_notm}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Setting up and configuring a monitoring host
{: #mon-exporter-setup-config}

A monitoring agent collects the metrics from an SAP system and forwards them to the dashboard of the monitoring instance.
{: shortdesc}

## Deploying VPC virtual server instance host for a monitoring agent
{: #mon-deploying-vsi-host}

Use Prometheus exporters to collect the metrics. The section about [Collecting metrics](/docs/monitoring?topic=monitoring-about-collect-metrics){: external} explains details about metrics, labels, and monitoring agents.

The Prometheus exporter is based on the GitHub open source project [SAP HANA DB exporter](https://github.com/SUSE/hanadb_exporter){: external}.

Before you begin, check out the {{site.data.keyword.cloud}} documentation about [Creating virtual server instances](/docs/vpc?topic=vpc-creating-virtual-servers&interface=ui){: external} to create a monitoring host by using the{{site.data.keyword.cloud}} UI.

### Monitoring host consideration
{: #mon-host-considerations-sap}

Consider the following settings for the monitoring host.

* The monitoring host must be deployed in the same region and zone as the monitored SAP system.
* Make sure that you select the correct resource group that corresponds to your deployable architecture (DA).
* You must deploy the `ibm-sles-15-5-amd64-sap-applications-<version>` image - SUSE Linux Enterprise Server 15 SP5 for SAP Applications (AMD64) - with the smallest available size of 2 cores.
* Add your appropriate SSH keys as described in [Managing SSH keys](/docs/vpc?topic=vpc-managing-ssh-keys&interface=ui){: external}.

## Verifying ACLs and security groups
{: #mon-verifying-acls-and-security-groups}

After you create your monitoring agent host, go to your **ACLs and security groups** to verify that the settings are correct.

If you are using ingestion endpoints other than the country location of the monitoring host, you must adjust the {{site.data.keyword.cloud}} network ACL settings for a corresponding Private REST API endpoint.
For more information, see [Collecting metrics by using Prometheus remote write](/docs/monitoring?topic=monitoring-prometheus_remote_write#prometheus_remote_write_endpoints){: external}.
{: note}

## Exporting SAP variables as bash variables
{: #mon-exporting-sap-variables-as-bash-variables-sap}

Use the following command to export SAP variables as Bash variables.

```sh
export sap_monitoring_nr=<sap_monitoring_nr>
export sap_hana_ip=<sap_hana_ip>
export sap_ascs_ip=<sap_ascs_ip>
export sap_app_server_ip_01=<sap_app_server_ip_01>
export sap_app_server_port_01=<sap_app_server_port_01>
export sap_ascs_http_port=<sap_ascs_http_port>
export sap_hana_http_port=<sap_hana_http_port>
export sap_hana_sql_systemdb_port=<sap_hana_sql_systemdb_port>
export sap_hana_sql_systemdb_user=<sap_hana_sql_systemdb_user>
export sap_hana_sql_systemdb_password=<sap_hana_sql_systemdb_password>
export ibmcloud_monitoring_instance_url=<ibmcloud_monitoring_instance_url>
export ibmcloud_monitoring_authorization_credentials=<ibmcloud_monitoring_authorization_credentials>
```
{: codeblock}

## Checking connectivity for the database port and SAP instance ports
{: #mon-checking-connectivity-hdb-port-to-instance-ports}

Install the netcat tool by using the following command.

```sh
sudo zypper install netcat
```
{: pre}

Use netcat to check connectivity to the database port and SAP instance ports on the monitoring host.

```sh
nc –vz ${sap_hana_ip} ${sap_hana_sql_systemdb_port}
```
{: pre}

Check connectivity from the SAP HANA database server to the HTTP port of the sapstartsrv web service on the SAP HANA server.

```sh
nc –vz ${sap_hana_ip} ${sap_hana_http_port}
```
{: pre}

Check connectivity from the SAP ASCS server to the HTTP port of the sapstartsrv web service of the ASCS on the SAP ASCS server.

```sh
nc –vz ${sap_ascs_ip} ${sap_ascs_http_port}
```
{: pre}

Check connectivity from IP address of the first SAP (primary) application server to the HTTP port of the sapstartsrv web service on the first application server.

```sh
nc –vz ${sap_app_server_ip_01} ${sap_app_server_port_01}
```
{: pre}

## Checking the status of SLES operating system repositories
{: #mon-checking-status-sles-repos}

Make sure the appropriate `SLES OS` repositories are enabled by using the following command.

```sh
SLE-Module-SAP-Applications15-SP5-Updates
SLE-Module-Packagehub-Subpackages15-SP5-Pool
```
{: screen}

If the repositories are not enabled, use the following commands to enable them.

```sh
sudo SUSEConnect -p sle-module-sap-applications/15.5/x86_64
```
{: pre}

```sh
sudo SUSEConnect -p PackageHub/15.5/x86_64
```
{: pre}

If you are unable to activate these repositories, then you have installed the wrong version of the SLES operating system image.
{: tip}

## Installing SLES Prometheus packages
{: #mon-installing-sles-prometheus-packages}

Use the following steps to install SLES Prometheus packages.

1. Update all operating system packages to the latest version. Restart the host if necessary.

   ```sh
   sudo zypper update
   ```
   {: pre}

1. Install the following Prometheus packages.

   ```sh
   sudo zypper install prometheus-sap_host_exporter
   ```
   {: pre}

   ```sh
   sudo zypper install prometheus-hanadb_exporter
   ```
   {: pre}

   ```sh
   sudo zypper install golang-github-prometheus-prometheus
   ```
   {: pre}

1. Create the Prometheus data storage directory.

   ```sh
   sudo mkdir /opt/prometheus
   ```
   {: pre}

1. Change the user and group IDs of the Prometheus data storage directory to the Prometheus user.

   ```sh
   sudo chown -R prometheus:prometheus /opt/prometheus
   ```
   {: pre}

   ```sh
   sudo chmod u+rwx /opt/prometheus
   ```
   {: pre}

1. Copy the metric file `metrics.json` to the configuration directory `/etc/hanadb_exporter`.

   ```sh
   sudo cp /usr/share/doc/packages/prometheus-hanadb_exporter/metrics.json  /etc/hanadb_exporter/metrics.json
   ```
   {: pre}

## Installing the SAP Python driver hdbcli
{: #mon-installing-sap-python-driver-hdbcli-sap}

Use the following steps to install the SAP Python driver `hdbcli`.

1. Access the SAP Download Center by using the [SAP S-user authentication](https://me.sap.com/softwarecenter){: external}.

1. Retrieve the SAP HANA Python driver by downloading the official SAP HANA Client. For more detailed information, see [Install the SAP HANA Client](https://developers.sap.com/tutorials/hana-clients-install.html){: external}.

1. Install the SAP Python driver `hdbcli` as described in the [Installing the Python driver](https://help.sap.com/docs/SAP_HANA_PLATFORM/0eec0d68141541d1b07893a39944924e/39eca89d94ca464ca52385ad50fc7dea.html){: external}.

1. Choose the following SAP HANA Client package with the current `<version>` and `<patch>`.

   ```sh
   SUPPORT PACKAGES & PATCHES - SAP HANA PLATFORM EDITION/ - SAP HANA PLATFORM EDITION 2.0/ - SAP HANA CLIENT 2.0 - LINUX ON X86_64 64BIT - IMDB_CLIENT20_<version>_<patch>-80002082.SAR
   ```
   {: screen}

1. Download the `SAPCAR_*.EXE` package from the SAP download center.
1. Copy the two files `SAPCAR_*.EXE` and `IMDB_CLIENTXXX.SAR` to an arbitrary directory on the monitoring host.
1. As the root user, run the following command.

   ```sh
   chmod 755 SAPCAR_*.EXE
   ```
   {: pre}

1. Extract the SAP HANA Client from the SAR-file with SAPCAR.

   ```sh
   ./SAPCAR_*.EXE -xvf IMDB_CLIENT20*.SAR
   ```
   {: pre}

   The file name `SAPCAR_*.EXE` is a Linux binary file.
   {: note}

1. Use `hdbinst` to install the SAP HANA Client.

   ```sh
   cd SAP_HANA_CLIENT
   ```
   {: pre}

   ```sh
   ./hdbinst
   ```
   {: pre}

1. Enter the installation path: `[/usr/sap/hdbclient]: /usr/sap/hdbclient`.
1. Install the required pip package for Python 3.x.

   ```sh
   sudo zypper install python3-pip
   ```
   {: pre}

1. Install the SAP HANA Python driver.

   ```sh
   pip install /usr/sap/hdbclient/hdbcli-*.tar.gz
   ```
   {: pre}


## Creating the hdbuserstore key
{: #mon-creating-hdbuserstore-key}

Use the following steps to create the `hdbuserstore` key.

1. Create the stored user key with the same Python user that runs the hanadb_exporter. Use the ReadOnly SQL user as `<sap_hana_sql_systemdb_user>`.

   ```sh
   /usr/sap/hdbclient/hdbuserstore SET MONITORING-${sap_monitoring_nr}-KEY \
   "${sap_hana_ip}:${sap_hana_sql_systemdb_port}@SYSTEMDB" \
   ${sap_hana_sql_systemdb_user} ${sap_hana_sql_systemdb_password}
   ```
   {: pre}

   For more information, see [SAP HANA User Store (hdbuserstore)](https://help.sap.com/docs/SAP_HANA_CLIENT/f1b440ded6144a54ada97ff95dac7adf/708e5fe0e44a4764a1b6b5ea549b88f4.html?locale=en-US){: external} and [hdbuserstore Commands](https://help.sap.com/docs/SAP_HANA_CLIENT/f1b440ded6144a54ada97ff95dac7adf/db46cfc4c5db4692aa84b46e357d47b8.html?locale=en-US){: external}.

   For the next steps, the `userkey` is added to the configuration file for the hanadb_exporter as `"userkey": "MONITORING-<sap_monitoring_nr>-KEY"`.
   {: note}

1. Verify that your key was created successfully by listing all keys.

   ```sh
   /usr/sap/hdbclient/hdbuserstore list
   ```
   {: pre}

1. To list only your key, use the following command.

   ```sh
   /usr/sap/hdbclient/hdbuserstore list MONITORING-${sap_monitoring_nr}-KEY
   ```
   {: pre}

## Configuring the hanadb_exporter and activating the daemon
{: #mon-config-hdb-exporter-activating-daemon}

Use the following steps to configure the hanadb_exporter and activate the daemon.

The `hanadb_exporter` collects metrics from the SAP HANA database. All metrics from the database tenant SYSTEMDB are forwarded to the monitoring ingestion endpoint through Prometheus.

The GitHub repository and original documentation can be found at the following websites.

* [Hanadb exporter](https://github.com/SUSE/hanadb_exporter){: external}
* [SAP monitoring](https://documentation.suse.com/sles-sap/15-SP4/pdf/article-sap-monitoring_en.pdf){: external}
* [Hanadb exporter metrics](https://github.com/SUSE/hanadb_exporter/blob/main/metrics.json){: external}

The configuration consists of two configuration files:

   * `metrics.json` - contains all the SQL statements for querying the SAP HANA database
   * `config.json` contains `sap_hana_ip`, `sap_hana_sql_systemdb_port`, and `hdbuserkey`

To avoid needing administrator privileges on the database, use the ReadOnly SQL user from the hdbuserstore.
The specific parameters are added to the configuration file by replacing the placeholders `<…>`.

1. Create the configuration file as user root with the file name `/etc/hanadb_exporter/config-<sap_monitoring_nr>-SQL.json` by using the following command:

   ```json
   {
      "listen_address": "0.0.0.0",
      "exposition_port": 5<sap_monitoring_nr>02,
      "multi_tenant": false,
      "_comment_multi_tenant": "true, if you want another 1000 metrics",
      "timeout": 30,
      "hana": {
        "host": "<sap_hana_ip>",
        "port": <sap_hana_sql_systemdb_port>,
        "userkey": "MONITORING-<sap_monitoring_nr>-KEY",
        "ssl": true,
        "ssl_validate_cert": false
        }
   }
   ```
   {: codeblock}

1. For the value of the `userkey` parameter, use the `hdbuserkey` name that you created earlier.

You can list the keys by using the following command.

   ```sh
   /usr/sap/hdbclient/hdbuserstore list
   ```
   {: pre}

1. AChange the permissions of the configuration files so that only the root user can read them.

   ```sh
   chmod 600 /etc/hanadb_exporter/config*
   ```
   {: pre}

1. Before you define the configuration of `sap_host_exporters` for the `systemd service`, test the exporters manually by using the following command.

   ```sh
   hanadb_exporter -c /etc/hanadb_exporter/config-${sap_monitoring_nr}-SQL.json -m /etc/hanadb_exporter/metrics.json
   ```
   {: pre}

1. The process is blocking one terminal, so open a second terminal and verify that an exporter has correctly collected the metric. Run the following curl command.

   ```sh
   curl http://localhost:5${sap_monitoring_nr}02/metrics
   ```
   {: pre}

If you see the metrics, you should also see a status message.
If the metrics are listed correctly, stop the exporter process.

Configure the systemd application for the `hanadb_exporter`.

The `systemd` configuration file for the `hanadb_exporter` is included in the installation package. No changes to this configuration file are required. The systemd service can start any process with the configuration name (without the JSON extension as the file name).
{: note}

1. To enable the **systemd service** for the `hanadb_exporter`, use the following commands.

   ```sh
   sudo systemctl start prometheus-hanadb_exporter@config-${sap_monitoring_nr}-SQL
   ```
   {: pre}

   ```sh
   sudo systemctl enable prometheus-hanadb_exporter@config-${sap_monitoring_nr}-SQL
   ```
   {: pre}

1. To show the status of the `hanadb_exporter` run the command below.

   ```sh
   sudo systemctl status prometheus-hanadb_exporter@config-${sap_monitoring_nr}-SQL
   ```
   {: pre}

## Configuring the sap_host_exporter and activating the daemon
{: #mon-config-host-exporter-and-activation}

Use the following information to configure the sap_host_exporter and activate the daemon.

Prometheus `sap_host_exporter` uses stateless HTTP protocol to collect metrics from the SAP system instances. For more information, see the [official GitHub repository](https://github.com/SUSE/sap_host_exporter){: external}.

The SAP exporter configuration contains all the parameters for the connection and the HTTP port. Note that you need to create a separate configuration file for each connection.

Create the configuration files in the `/etc/sap_host_exporter/` directory. Name the configuration files according to the SAP instance by using the following command.

Use the following templates for the configuration files.

1. As the root user, create the SAP HANA configuration file with the file name `/etc/sap_host_exporter/sap_host_exporter-${sap_monitoring_nr}-HANA.yaml`.

   ```yaml
   # The listening TCP/IP address and port.
   address: "0.0.0.0"
   port: "5<sap_monitoring_nr>03"
   log-level: "info"
   sap-control-url: "http://<sap_hana_ip>:<sap_hana_http_port>"
   ```
   {: codeblock}

1. As the root user, create the SAP ASCS configuration file with the file name `/etc/sap_host_exporter/sap_host_exporter-${sap_monitoring_nr}-ASCS.yaml`.

   ```yaml
   # The listening TCP/IP address and port.
   address: "0.0.0.0"
   port: "5${sap_monitoring_nr}04"
   log-level: "info"
   # ASCS instance
   sap-control-url: "http://<sap_ascs_ip>:<sap_ascs_http_port>"
   ```
   {: codeblock}

1. As the root user, create the first SAP application server (PAS) configuration file with the name `/etc/sap_host_exporter/sap_host_exporter-${sap_monitoring_nr}-DI-01.yaml`.

   ```yaml
   # The listening TCP/IP address and port.
   address: "0.0.0.0"
   port: "5<sap_monitoring_nr>05"
   log-level: "info"
   # DI instance (metrics dispatcher server + resources)
   sap-control-url: "http://<sap_app_server_ip_01>:<sap_app_server_port_01>"
   ```
   {: codeblock}

1. As the root user, create the second SAP application server (AAS) configuration file with the name `/etc/sap_host_exporter/sap_host_exporter-<sap_monitoring_nr>-DI-02.yaml`.

   ```yaml
   # The listening TCP/IP address and port.
   address: "0.0.0.0"
   port: "5<sap_monitoring_nr>06"
   log-level: "info"
   # DI instance (metrics dispatcher server + resources)
   sap-control-url: "http://<sap_app_server_ip_02>:<sap_app_server_port_02>"
   ```
   {: codeblock}

   If there are multiple application servers, then create more configuration files for each SAP application server. Make sure that you increment the number of each configuration file.
   {: note}

1. Test each `sap_host_exporter` configuration.

   ```sh
   sap_host_exporter -c /etc/sap_host_exporter/sap_host_exporter-<sap_monitoring_nr>-HANA.yaml
   ```
   {: pre}

1. The process is blocking the terminal, so open a second terminal and run the following command.

   ```sh
   curl http://localhost:5<sap_monitoring_nr>03/metrics
   ```
   {: pre}

1. Verify that the metric data is displayed. If no metrics are displayed, you should see some error messages.
1. Repeat the same test with other exporter configurations:

   ```sh
   /etc/sap_host_exporter/sap_host_exporter-<sap_monitoring_nr>-ASCS.yaml
   ```
   {: pre}

    ```sh
    /etc/sap_host_exporter/sap_host_exporter-<sap_monitoring_nr>-DI-01.yaml
    ```
    {: pre}

If all of the exporters are working correctly, continue with the `systemd` configuration.

Configure the **systemd** server for the `sap_host_exporter`.

Only one extra `systemd` configuration file is required for all `sap_host_exporters`. The `systemd` service can start any process with the configuration name (without the JSON extension as file name).
{: note}

1. Create a file `sap_host_exporter@.service` as user root in the system directory.

   ```sh
   /etc/systemd/system/sap_host_exporter@.service
   ```
   {: screen}

1. Add the following content without making any changes.

   ```yaml
   [Unit]
   Description=Prometheus sap_host_exporter for Netweaver clusters metrics
   After=network.target
   Documentation=https://github.com/SUSE/sap_host_exporter
   [Service]
   Type=simple
   Restart=always
   ExecStart=/usr/bin/sap_host_exporter --config /etc/sap_host_exporter/%i.yaml
   ExecReload=/bin/kill -HUP $MAINPID
   [Install]
   WantedBy=multi-user.target
   DefaultInstance=default
   ```
   {: codeblock}

1. Run the following command as user root to reload the systemd configuration.

   ```sh
   systemctl daemon-reload
   ```
   {: pre}

1. As user root, start and enable the service for all configured `sap_host_exporters`.

   ```sh
   systemctl enable --now sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-HANA
   ```
   {: pre}

   ```sh
   systemctl enable --now sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-ASCS
   ```
   {: pre}

   ```sh
   systemctl enable --now sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-DI-01
   ```
   {: pre}

   ```sh
   systemctl enable --now sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-DI-02
   ```
   {: pre}

1. As user root, check the status of `systemd processes`.

   ```sh
   systemctl status sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-ASCS
   ```
   {: pre}

   ```sh
   systemctl status sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-HANA
   ```
   {: pre}

   ```sh
   systemctl status sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-DI-01
   ```
   {: pre}

   ```sh
   systemctl status sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-DI-02
   ```
   {: pre}

1. Enable `systemd autostart`.

   ```sh
   sudo systemctl enable sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-ASCS
   ```
   {: pre}

   ```sh
   sudo systemctl enable sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-HANA
   ```
   {: pre}

   ```sh
   sudo systemctl enable sap_host_exporter@sap_host_exporter-${sap_monitoring_nr}-DI-01
   ```
   {: pre}

## Next steps
{: #mon-exporter-setup-next-steps}

If the setup and configuration of the monitoring host was successful, continue with [Configuration of Prometheus server metric forwarding](/docs/sap?topic=sap-mon-metric-forwarding).
