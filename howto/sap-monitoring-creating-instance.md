---
copyright:
  years: 2024
lastupdated: "2025-02-06"

keywords: SAP, SAP Monitoring, {{site.data.keyword.cloud_notm}}, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Creating a monitoring instance
{: #mon-create-instance}

Use the following information to create a monitoring instance.

## Step 1. Managing user access
{: #mon-managing-user-access}

To get started with {{site.data.keyword.cloud}} Monitoring for SAP systems, you need an IBM Cloud account and an [Administrator IAM role](/docs/monitoring?topic=monitoring-iam#iam_platform){: external}.
For more information, see [Getting started with {{site.data.keyword.cloud}} Monitoring](/docs/monitoring?topic=monitoring-getting-started#getting-started-step1){: external}.

Verify that the Virtual Routing and Forwarding (VRF) service endpoints are enabled in your Identity and Access Management (IAM) account settings.
If VRF service endpoints are not enabled, enable the setting in your IAM account.
For more information, see [Enabling VRF and service endpoints](/docs/account?topic=account-vrf-service-endpoint&interface=ui){: external}.

## Step 2. Provisioning a Monitoring service for an instance
{: #mon-provisioning-instance}

To create a monitoring instance by using the IBM Cloud UI, use the following steps. For more information, see [Provision an instance of the Monitoring service](/docs/monitoring?topic=monitoring-getting-started#getting-started-step2){: external}.

1. Log in to the [{{site.data.keyword.cloud}} console](https://cloud.ibm.com){: external}.
1. Click **Navigation menu** > **Observability** > **Monitoring**.
1. Click **Create +**.
1. Select and complete the following settings for the monitoring instance:
   * Select the same location as the monitoring host and the deployed SAP systems.
   * Select the resource group of your deployable architecture (DA).
   * Enter a name or keep the default name.
   * Select the **Graduated Trier** pricing plan.
   * When you are ready to enable metrics, set the IBM platform metrics to **Enabled**.

## Step 3. Collecting monitoring metrics
{: #mon-collecting-metrics-sap}

Retrieve the authorization credentials after you create the monitoring instance.

Enter the authorization credentials later on in the metric exporter configuration files.

1. In the {{site.data.keyword.cloud}} console, select **Observability** > **Monitoring** or go to [Monitoring](https://cloud.ibm.com/observe/monitoring){: external}.
1. Select your monitoring instance from the table and click **Open dashboard**.
1. Click **Get started**. Your credential values are in the dashboard.
1. Click **Optional: Connect your Prometheus Servers** to find the yaml-code with authorization credentials.

These credentials are used by the Prometheus monitoring agent, which uses them to send metrics data to the dashboard.
From this yaml code, you only need the URL value and the credentials values.
These values are defined as the `<ibmcloud_monitoring_instance_url>` and `<ibmcloud_monitoring_authorization_credentials>`, which identify the monitoring instance.
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
    ibmcloud_monitoring_instance_url=https://ingest.prws.private.<region>.monitoring.cloud.ibm.com/prometheus/remote/write
    ```
    {: screen}

1. For a list of all private ingestion endpoints per region, refer to the IBM Cloud documentation that is described in

[Collecting metrics by using Prometheus remote write](/docs/monitoring?topic=monitoring-prometheus_remote_write#prometheus_remote_write_endpoints){: external}.

## Next steps
{: #mon-creating-instance-next-steps}

You're ready to set up and configure a monitoring host and send the SAP metrics data to the {{site.data.keyword.cloud}} monitoring instance. For more information, see [Setting up and configuring a monitoring host](/docs/sap?topic=sap-mon-exporter-setup-config).
