---
copyright:
  years: 2025
lastupdated: "2025-07-08"
keywords: SAP, SAP Monitoring, SAP Monitoring Alerting, Monitoring Notification {{site.data.keyword.cloud_notm}}, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Alerting and notification in IBM Cloud Monitoring
{: #mon-alerting-notification}

## Alerting in IBM Cloud Monitoring
{: #mon-alerting}

Alerts are a core feature of IBM Cloud monitoring.
Detailed guidance on configuring alerts can be found in [IBM Cloud Docs Monitoring : Configuring an alert by using the alert editor](/docs/monitoring?topic=monitoring-alert-config) and [IBM Cloud Docs Monitoring: Threshold alerts](/docs/monitoring?topic=monitoring-alert-metric).

Alerts are triggered by user-defined event conditions, such as when specific metrics exceed configured threshold values.

### Example of an alert for 'sap_start_service_processes'
{: #mon-alerting-example-sap}

**Metrics of SAP processes**
The metric *sap_start_service_processes* reports the operational status of various SAP processes including:
`hdbnameserver, hdbindexserver, msg_server, hdbdiserver, hdbdaemon, hdbpreprocessor, icman, and others`.
Each metric value corresponds to the current state of an individual process, as defined in the following table.


| Value | State   |
| ----- | ------- |
| 1     | Stopped |
| 2     | Running |
| 3     | Pending |
| 4     | Error   |
{: caption="Mapping of metric values to process states" caption-side="bottom"}


### Example of configuring an alert
{: #mon-alerting-example}

This example demonstrates how to configure an alert that triggers when the status of any SAP process changes to `4` (Error).

#### Prerequisites
{: #mon-alerting-prereqs}


Ensure that a monitoring dashboard named **SAP System** is already set up and receiving relevant metrics.
Follow the steps outlined in [IBM Cloud Docs Monitoring: Configuring an alert by using the alert editor](/docs/monitoring?topic=monitoring-alert-config), by using the provided alert specifications.

#### Creating the alert
{: #mon-alerting-alert-create}

1. In the IBM Cloud Monitoring UI of the selected region, select **Alerts** and click **+ New Alert**.
1. Select the alert type: **Threshold**.
1. Name the alert:
   - Click the default alert name and enter a descriptive name, such as **SAP process status error alert**.
1. In the **Define Condition:** section:
    - Select **sap_start_service_processes** as **Metric**:
        - Click the **Metric** selection box.
        - Click **Load More** to display all available metrics.
        - Select **sap_start_service_processes** from the list.
1. In the **Define When to Alert You:** section:
    - Configure the alert condition as follows:
      - Click **option** and select the comparison parameter `=`.
      - Select `4` as the value parameter (corresponding to the `Error` state).
      - Select `1` as number scale.
      - Make sure that the query preview shows: `avg(avg_over_time(sap_start_service_instances[59s])) = 4`.

1. In the **Settings:** section:
    - **Alert Severity**: Select `High`.
    - **Link to Dashboard**: select an existing custom SAP system dashboard.
1. Click **Save** to finalize the alert configuration.

All configured Alerts are listed in the *Alert Dashboard*.

For more information about managing alerts, refer to the [IBM Cloud Docs Monitoring Alerts API](/docs/monitoring?topic=monitoring-alert_api)
{: note}

## Configuring notifications for alert events
{: #mon-alerting-notifications}

You can configure alert notifications by creating a notification pipeline that includes a notification event service.
Detailed instructions are available in the [IBM Cloud Docs Monitoring tutorial: Sending email notifications to IBM Cloud Event Notifications](/docs/monitoring?topic=monitoring-tutorial-en).

Supported notification destinations include email, SMS, Pager, Slack, Teams, and others.
For a complete list, refer to [IBM Cloud Docs Monitoring: Working with notification channels](/docs/monitoring?topic=monitoring-notifications).
{: note}

### Configuring email notifications for alert events
{: #mon-alerting-email-notifications}

To configure email notifications, follow the steps that are outlined in [IBM Cloud Monitoring: Sending email alerts by using IBM Cloud Event Notifications](/docs/monitoring?topic=monitoring-eventnotif).
Do not use the **Lite** pricing plan for the *Event Notification service instance*, as it is limited to 30 days.
Instead, select the **Standard** pricing plan to ensure continued service availability.

Ensure that all intended email addresses are added to the subscriptions list and are acknowledged, as described in [IBM Cloud Docs Monitoring: Notification email subscription](/docs/monitoring?topic=monitoring-tutorial-en#en-subscription).

After you configured the notification service, you can associate one or more notification channels with the alert that you defined in your monitoring instance.

1. In the IBM Cloud Monitoring UI, click **Alerts** in the side menu.
1. Select the `Alerts` option.
1. Select your previously configured alert, for example `SAP process status error alert`.
1. In the **Notifications:** section, choose one of the available notification channels.
1. Optional: Select the `Get notified every` slider control to receive alerts at selected time intervals.
1. Click `Save` to apply the updated alert configuration.
