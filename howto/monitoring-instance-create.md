---
copyright:
  years: 2024, 2025
lastupdated: "2025-09-22"
keywords: SAP, SAP Monitoring, {{site.data.keyword.cloud_notm}}, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA
subcollection: sap
content-type: tutorial
account-plan: paid
completion-time: 10m
---

{{site.data.keyword.attribute-definition-list}}


# Creating an {{site.data.keyword.mon_full_notm}} instance
{: #monitoring-instance}
{: toc-content-type="tutorial"}
{: toc-completion-time="10m"}

Use the following information to create a monitoring instance.

## Managing user access
{: #monitoring-instance-iam}
{: step}

To get started with {{site.data.keyword.cloud}} Monitoring for SAP systems, you need an IBM Cloud account and an [Administrator IAM role](/docs/monitoring?topic=monitoring-iam#iam_platform).
For more information, see [Getting started with {{site.data.keyword.cloud}} Monitoring](/docs/monitoring?topic=monitoring-getting-started#getting-started-step1).

Verify that the Virtual Routing and Forwarding (VRF) service endpoints are enabled in your Identity and Access Management (IAM) account settings.
If VRF service endpoints are not enabled, enable the setting in your IAM account.
For more information, see [Enabling VRF and service endpoints](/docs/account?topic=account-vrf-service-endpoint&interface=ui).

## Provisioning an {{site.data.keyword.mon_full_notm}} instnace
{: #monitoring-instance-provision}
{: step}

To create an {{site.data.keyword.mon_full_notm}} instance by using the IBM Cloud UI, use the following steps. For more information, see [Provision an instance of the Monitoring service](/docs/monitoring?topic=monitoring-getting-started#getting-started-step2).

1. Log in to the [{{site.data.keyword.cloud}} console](https://cloud.ibm.com){: external}.
1. Click **Navigation menu** > **Observability** > **Monitoring**.
1. Click **Create +**.
1. Select and complete the following settings for the monitoring instance:
   * Select the same location as the monitoring host and the deployed SAP systems.
   * Select the resource group of your deployable architecture (DA).
   * Enter a name or keep the default name.
   * Select the **Graduated Trier** pricing plan.
   * When you are ready to enable metrics, set the IBM platform metrics to **Enabled**.

## Collecting monitoring metrics
{: #monitoring-instance-metrics-collection}
{: step}

Retrieve the authorization credentials after you create the {{site.data.keyword.mon_full_notm}} instance. These credentials will be used by the Prometheus remote write application running on **x86_64 VSI in {{site.data.keyword.vpc_short}}** to send metrics data to the dashboard hosted on {{site.data.keyword.mon_full_notm}} instance.

1. In the {{site.data.keyword.cloud}} console, select **Observability** > **Monitoring** or go to [Monitoring](https://cloud.ibm.com/observe/monitoring){: external}.
1. Select your monitoring instance from the table and click **Open dashboard**.
1. Click **Get started**. Your credential values are in the dashboard.
1. Click **Optional: Connect your Prometheus Servers** to find the yaml-code with authorization credentials.
1. From this yaml code, you only need the URL value and the credentials values.
   The yaml code looks like the following example.

      ```yaml
      remote_write:
      - url: "https://ingest.prws.eu-de.monitoring.cloud.ibm.com/prometheus/remote/write"
         authorization:
         credentials: "123-abcdh-xxx-456"
      ```
      {: codeblock}

1. In the URL, replace the string `ingest.prws` with `ingest.prws.private` as the private ingestion endpoint.

   The new URL looks like the following example.

      ```yaml
      https://ingest.prws.private.<region>.monitoring.cloud.ibm.com/prometheus/remote/write
      ```
      {: screen}

1. For a list of all private ingestion endpoints per region, refer to the IBM Cloud documentation that is described in

[Collecting metrics by using Prometheus remote write](/docs/monitoring?topic=monitoring-prometheus_remote_write#prometheus_remote_write_endpoints).

## Next steps
{: #monitoring-instance-next-steps}
{: step}

Configure a monitoring host and send the SAP metrics data to the {{site.data.keyword.cloud}} monitoring instance. For more information, see [Setting up and configuring a monitoring host](/docs/sap?topic=sap-mon-exporter-setup-config).
