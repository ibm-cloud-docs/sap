---
copyright:
  years: 2024, 2025
lastupdated: "2025-04-17"
keywords: SAP, SAP Monitoring, {{site.data.keyword.cloud_notm}}, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Getting started with {{site.data.keyword.cloud_notm}} Monitoring for SAP systems
{: #mon-getting-started}

Monitoring for SAP system refers to the tools, techniques, and practices used to ensure the optimal performance, health, and availability of an SAP system. Because of its critical role in business operations, continuous monitoring is essential to prevent issues, improve performance, and ensure high availability. SAP solutions based on SAP S/4HANA or SAP NetWeaver and in addition the SAP HANA DB can be monitored using this solution.

[{{site.data.keyword.mon_full}}](/docs/monitoring?topic=monitoring-getting-started) is a cloud-native, and container-intelligence management system that you can include as part of your {{site.data.keyword.cloud_notm}} architecture. Use it to gain operational visibility into the performance and health of your applications, services, and platforms. It offers administrators, DevOps teams and developers full-stack telemetry with advanced features to monitor and troubleshoot, define alerts, and design custom dashboards.

{{site.data.keyword.cloud}} Monitoring offers predefined SAP dashboards from {{site.data.keyword.cloud}} dashboard library free of charge.
{: shortdesc}

This monitoring solution applies to SAP workloads hosted on **x86_64 Virtual Server Instance and {{site.data.keyword.powerSys_notm}} Instances**.

## Dashboard features
{: #monitoring-dashboard}

Two separate dashboard templates for SAP system is supported by the {{site.data.keyword.mon_full}} instance.
1. [SAP HANA DB dashboard](#monitoring-dashboard-hana-db) - Designed specifically for monitoring SAP HANA databases.
1. [SAP System dashboard](#monitoring-dashboard-sap-system) - Tailored for monitoring all SAP instances of the configured SAP solution.

* All the metrics from the dashboards can be exported and saved in JSON or CSV format.
* Custom defined alerts based on user defined threshold can be created for each of the metrics and when the threshold is crossed, a notification can be triggered to different channels.

### SAP HANA DB dashboard
{: #monitoring-dashboard-hana-db}

SAP HANA DB dashboard displays the following metrics from the virtual server running SAP HANA database:
1. **System Health:** This involves tracking the overall health of the SAP HANA database system, including CPU usage, memory usage, file system usage, and system load.
1. **Database and Query Monitoring:** SQL Execution statistics, Memory usage by service and total connections.
1. **Network Metrics:** Receive Rate (KB/s), Transmission Rat (KB/s), Receive/Transmission Errors per Second, Received Requests per Second, Transmitted Requests per Second, Collisions per Second.
1. **Disk I/O:** I/O Throughput, Latency, I/O Service Time, Requests per Sec, Queue Length (Number of requests), I/O Wait Time
1. **Alerting and Notification:** Internal HANA database alert details along with datetime and severity.

### SAP system dashboard
{: #monitoring-dashboard-sap-system}

SAP system dashboard displays the following metrics from the virtual server running SAP application server and SAP HANA DB system:
1. **Instance status:** SAP instance names and their status of the configured SAP System which includes all SAP instances of the SAP HANA database, the SAP application server and the SAP central services.
1. **Process status:** OS Process names and their status of the configured SAP system which includes all processes of the SAP HANA database, the SAP application server and the SAP central services.
1. **Enqueue states:** Replication state, locks state, owner state and arguments state.
1. **Enqueue server:** Current lock tables entries, Peak lock tables entries, Requests/sec and Errors/sec.
1. **Work process dispatcher:** Dispatcher current queue length, dispatcher queue high, dispatcher queues reads/sec, dispatcher queues writes/sec and dispatcher queue max.

## Architecture
{: #monitoring-architecture}

![Figure 1. {{site.data.keyword.cloud}} Monitoring for SAP systems](../images/monitoring-architecture.svg "{{site.data.keyword.cloud}} Monitoring for SAP systems"){: caption="{{site.data.keyword.cloud}} Monitoring for SAP systems" caption-side="bottom"}
{: external download="sap-monitoring-in-ibm-cloud.svg"}

- The metrics of **SAP systems running on x86_64 VSI** in {{site.data.keyword.vpc_short}} are collected by an application that is **only supported** on **SUSE Linux Enterprise Server (SLES)** running on **x86_64 VSI in {{site.data.keyword.vpc_short}}**.
- The metrics of **SAP systems running on {{site.data.keyword.powerSys_notm}}** instance are collected by an application that is **only supported** on **SUSE Linux Enterprise Server (SLES)** running on **x86_64 VSI in {{site.data.keyword.vpc_short}}**.
- The collected metrics from the application running on **x86_64 VSI in {{site.data.keyword.vpc_short}}** are then pushed to the {{site.data.keyword.mon_full}} instance using the [collector endpoints](/docs/monitoring?topic=monitoring-endpoints#endpoints_ingestion) where they are rendered on the dashboard.

The detailed information about {{site.data.keyword.mon_full}} is described in [Collecting metrics by using Prometheus remote write](/docs/monitoring?topic=monitoring-about-monitor){: external}.

This setup does not support SAP systems in a High Availability setup.
{: restriction}

## Next steps
{: #monitoring-next-steps}

To set up SAP monitoring, you must perform the following tasks.

- [Prerequisites](/docs/sap?topic=sap-monitoring-prereqs)
- [Creating of monitoring instance in {{site.data.keyword.cloud}}](/docs/sap?topic=sap-mon-create-instance)
- [Setup and configuration of a monitoring host](/docs/sap?topic=sap-mon-exporter-setup-config)
- [Configuration of Prometheus server metric forwarding](/docs/sap?topic=sap-mon-metric-forwarding)
- [Launch the monitoring web UI and work with dashboards](/docs/sap?topic=sap-mon-launch-web-ui-dashboards)
