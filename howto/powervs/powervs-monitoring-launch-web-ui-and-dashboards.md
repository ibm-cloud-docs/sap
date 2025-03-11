---
copyright:
  years: 2024, 2025
lastupdated: "2025-03-10"
keywords: SAP, SAP Monitoring, {{site.data.keyword.cloud_notm}}, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Launching the monitoring UI and working with dashboards
{: #mon-launch-web-ui-dashboards}

All metrics are sent to the {{site.data.keyword.cloud}} monitoring instance.
If monitoring metrics are received on the monitoring instance, two SAP Dashboards are visible in the dashboard library of the monitoring instance in the Dashboard library.

1. To view the Dashboard library, click the **Dashboard library** tab from **/Dashboard/Dashboard manager**.
1. Copy both dashboards to `My Dashboards`, click the three dots on the right side and click: `Copy to My Dashboards`. The two SAP Dashboards are visible under the `Dashboard/Dashboard Manager/My Dashboards/My Dashboards`:

   * `SAP HANA DB`
   * `SAP SYSTEM`

   All configured SAP systems are monitored by these dashboards.
   {: note}

1. To show all configured SAP system instances on the `SAP SYSTEM` dashboard, click the pen icon next to `Team Scope` on the panel.
1. Select **instance** from the first pull-down menu and select **in** from the second pull-down menu. Go to the third pull-down menu and select one or more available names with syntax `SAP<sap_monitoring_nr>`.
1. Click `Save`. For more information, see the [Sysdig dashboard documentation](https://docs.sysdig.com/en/docs/sysdig-monitor/dashboards/#define-dashboard-scope){: external}.

The official [SAP System Monitoring documentation](https://support.sap.com/en/alm/solution-manager/expert-portal/system-monitoring.html?anchorId=section){: external} describes all metric details to clarify the visualized values.

## Duplicating, editing, and publishing dashboards
{: #mon-duplicating-editing-publishing-dashboards}

Use the following information to duplicate, edit, and publish dashboards.

### Duplicating dashboards
{: #mon-duplicating-dashboards}

To duplicate a dashboard, use the following information.

1. Go to the **Dashboard Manager** and click the **My Dashboards** tab.
1. From the list of dashboards, find your dashboard and click the three dots icon, then click **Duplicate dashboard**.

### Editing of Dashboards
{: #mon-editing-dashboards}

You can change the dashboard layout by duplicating the dashboard. For more information, see [Dashboard panels](https://docs.sysdig.com/en/docs/sysdig-monitor/dashboards/dashboard-panels/){: external}.

### Publishing dashboards
{: #mon-publishing-dashboards}

Public dashboard sharing allows external users to review a dashboard without an {{site.data.keyword.cloud}} login. For more information about sharing dashboards publicly, see [Enabling Public Sharing](https://docs.sysdig.com/en/docs/sysdig-monitor/dashboards/#enable-public-sharing){: external}.
