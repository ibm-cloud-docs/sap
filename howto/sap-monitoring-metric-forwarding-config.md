---
copyright:
  years: 2024, 2025
lastupdated: "2025-02-06"

keywords: SAP, SAP Monitoring, {{site.data.keyword.cloud_notm}}, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Configuring Prometheus server metric forwarding
{: #mon-metric-forwarding}

The Prometheus metrics exporters, `sap_host_exporter` and `hanadb_exporter`, are configured to collect metrics from SAP HANA and the SAP system instances.
Each Prometheus exporter collects metrics from the SAP system and exposes the metrics to a local HTTP port. Use a different HTTP port for each process and use a port number greater than 9600.

A Prometheus server, available from the package `golang-github-prometheus-prometheus`, is configured to forward the collected metrics from the local HTTP port to the monitoring ingestion endpoints.

All these services are installed as `systemd` services to keep them running permanently.

The Prometheus server is used to forward the metrics to the {{site.data.keyword.cloud}} monitoring.
You can configure all exporter processes of `hanadb_exporter` and `sap_host_exporter` in one `prometheus.yml` file.

The Prometheus server in agent mode performs the following functions.

   * Collects metrics from HTTPS endpoints.
   * Forwards the metrics to a remote endpoint.

Use the following steps to configure metric forwarding for the Prometheus server.

1. Create a Prometheus configuration file as described in [Configuring Prometheus remote write](/docs/monitoring?topic=monitoring-prometheus_remote_write){: external}.

   All metrics from all exporter processes are collected by defining `targets`.

   The template for this configuration includes the following placeholders:

      * `<ibmcloud_monitoring_instance_url>` determines the {{site.data.keyword.cloud}} instance rewrite destination.
      * `<ibmcloud_monitoring_authorization_credentials>` is the authentication token for the {{site.data.keyword.cloud}} monitoring instance.
      * `<sap_monitoring_nr>` is the SAP system number where the exposed metrics are collected.

1. As the Prometheus user, run the following command to create the configuration file with the file name /etc/prometheus/<sap_monitoring_nr>.yml

   ```yaml
   global:
     scrape_interval: 15s
     evaluation_interval: 20s
   remote_write:
     - url: "<ibmcloud_monitoring_instance_url>"
       authorization:
         credentials: "<ibmcloud_monitoring_authorization_credentials>"
       write_relabel_configs:
         - target_label: instance
           replacement: 'SAP-<sap_monitoring_nr>-<sap_monitoring_solution_name>'
   scrape_configs:
     - job_name: "hanadb_exporter"
       static_configs:
         - targets: ['localhost:5<sap_monitoring_nr>02']
       relabel_configs:
         - target_label: domain
           replacement: 'SAP'
     - job_name: "sap_host_exporter"
       static_configs:
         - targets: ['localhost:5<sap_monitoring_nr>03','localhost:5<sap_monitoring_nr>04','localhost:5<sap_monitoring_nr>05']
         # 05 – n: as many as existing SAP Application server
       relabel_configs:
         - target_label: domain
           replacement: 'SAP'
   ```
   {: codeblock}

   Do not change the syntax of the Prometheus configuration file name: `/etc/prometheus/<sap_monitoring_nr>.yml`.
   {: note}

1. Use the following commands to enforce Prometheus user permissions in all Prometheus configuration files.

   ```sh
   sudo chown -R prometheus:prometheus /etc/prometheus
   ```
   {: pre}

   ```sh
   sudo chmod 0740 /etc/prometheus
   ```
   {: pre}

   ```sh
   sudo chmod 0640 /etc/prometheus/*
   ```
   {: pre}

   Test the Prometheus daemon manually.

   ```sh
   sudo -H -u prometheus /usr/bin/prometheus \
   --config.file=/etc/prometheus/${sap_monitoring_nr}.yml \
   --storage.agent.path=/opt/prometheus/${sap_monitoring_nr} \
   --enable-feature=agent --web.listen-address=localhost:5${sap_monitoring_nr}01
   ```
   {: pre}

As the root user, set the systemd service parameters to run the Prometheus server in agent mode by using the following steps.

1. Create and copy the following content to the `/etc/systemd/system/prometheus@.service` file.

   ```yaml
   [Unit]
   Description=Prometheus
   After=network.target
   [Service]
   User=prometheus
   Group=prometheus
   Type=simple
   Restart=always
   ExecStart=/usr/bin/prometheus --config.file=/etc/prometheus/%i.yml \
   --storage.agent.path=/opt/prometheus/%i --enable-feature=agent \
   --web.listen-address=localhost:5%i01
   ExecReload=/bin/kill -HUP $MAINPID
   TimeoutStopSec=20s
   SendSIGKILL=no
   [Install]
   WantedBy=multi-user.target
   ```
   {: codeblock}

1. Use the following commands to activate the configuration and start and enable the Prometheus agent.

   ```sh
   systemctl daemon-reload
   ```
   {: pre}

   ```sh
   sudo systemctl start prometheus@${sap_monitoring_nr}
   ```
   {: pre}

   ```sh
   sudo systemctl enable prometheus@${sap_monitoring_nr}
   ```
   {: pre}

   Check the status of the service:

   ```sh
   systemctl status prometheus@${sap_monitoring_nr}
   ```
   {: pre}

1. Restart the daemon after each update of the configuration in the file /etc/prometheus/<sap_monitoring_nr>.yml.

   ```sh
   chown a+r /etc/prometheus/*
   ```
   {: pre}

   ```sh
   systemctl reload prometheus@${sap_monitoring_nr}
   ```
   {: pre}

It takes some time for the metrics to appear in your dashboard.
{: note}

## Next steps
{: #mon-metric-forwarding-next-steps}

To visualize the metrics that are collected by the Prometheus server, see [Launch the monitoring web UI and work with dashboards](/docs/sap?topic=sap-mon-launch-web-ui-dashboards).
