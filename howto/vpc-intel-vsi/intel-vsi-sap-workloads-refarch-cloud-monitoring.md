---
copyright:
  years: 2025
lastupdated: "2025-10-30"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SLES, HADR
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# IBM Cloud Logging, Monitoring, and Alerting
{: #cloud-monitoring}

This reference architecture describes how the IBM Cloud Logging and Monitoring services are integrated with Virtual Server Instances (VSIs) to provide centralized operational visibility, KPI monitoring, and alerting. This solution enables collection, filtering, visualization, and notification of system and application events across multiple environments.

## High-level diagram
{: #high-level-diagram}

![Figure 1. IBM Cloud account (Logging, Monitoring and Alerting)](../../images/vpc-intel-vsi-high-level-diagram-mont.svg "IBM Cloud account (Logging, Monitoring and Alerting)"){: caption="IBM Cloud account (Logging, Monitoring and Alerting)" caption-side="bottom"}

## Architecture components
{: #arch-components}

* **IBM Cloud Virtual Server Instances (VSIs):**
    * RHEL 8 and RHEL 9 instances host SAP workloads and applications.
    * Each VSI runs:
        * Fluent Bit Agent for log forwarding.
        * Sysdig Agent for system and application metrics.
* **IBM Cloud Logging:**
    * Centralized log aggregation from multiple VSIs.
    * Supports multiple log paths (for example, `/usr/sap/B4A/D05/work/dev_*` and `/usr/sap/B4A/ASE01/work/dev_*` ).
    * Filters are applied to file path, tag, or user context (metadata).
* **{{site.data.keyword.monitoringlong_notm}} (Sysdig):**
    * Collects metrics (CPU, memory, disk, SAP KPIs) from each VSI.
    * Dashboards provide visualization of system health and performance trends.
* **{{site.data.keyword.en_full_notm}} (EN):**
    * Subscribes to log events from IBM Cloud Logging.
    * Supports multiple destinations (Email, Slack, and so on).
    * Enables proactive response to operational issues.
* **Alerting through notification channels:**
    * **Monitoring Alerts:** Sysdig instance triggers alerts based on metric thresholds (for example, CPU > 80%).
    * **Logging Alerts:** Streams events into Event Notifications for log-based alerts (for example, SAP dev_* errors).
    * Alerts are delivered through slack channels to the operations team.

## Data flow
{: #data-flow}

1. **Log collection:**

    a. Fluent Bit tails configured log paths (default system logs + SAP dev_*).

    b. Logs are forwarded to IBM Cloud Logging instance.

2. **Metric collection:**

    a. Sysdig agent is installed on each VSI.

    b. Collects OS and application metrics and pushes to {{site.data.keyword.monitoringlong_notm}}.

3. **Filtering and Routing:**

    a. Logs are filtered by path, tag, or user in the Fluent Bit configuration.

    b. Metrics enriched with custom tags (for example, Sap:monitoring).

4. **Visualization:**

    a. Logs are available in the Logging instance dashboard.

    b. Metrics visualized through Sysdig dashboards.

5. **Alerting:**

    a. Logging instance > Event Notifications > Email alerts for log-based triggers.

    b. Monitoring instance > Notification Channels > Email alerts for metric-based thresholds.

## Security and Access
{: #security-access}

* IAM trusted profiles are used for agent authentication (instead of static keys).
* Role-based access ensures only authorized users can view logs, dashboards, or manage alert policies.

## Benefits
{: #benefits}

* Centralized log and metric collection for SAP and application workloads.
* End-to-end visibility into system performance and operational health.
* Flexible filtering (multi-path log collection).
* Integrated alerting through Event Notifications and Monitoring channels.
* Scalable architecture for onboarding additional VSIs.
