---
copyright:
  years: 2025
lastupdated: "2025-06-11"
keywords: SAP, SAP SCC Workload Protection, {{site.data.keyword.sysdigsecure_full_notm}}, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# {{site.data.keyword.sysdigsecure_short}} Dashboard Overview
{: #scc-wp-dashboard-overview}

The previous sections provided an overview over the functions {{site.data.keyword.sysdigsecure_short}} offers, how to deploy an instance, and how to install the agent on linux hosts and connect it as a data source. This section provides a brief overview over the sections of the {{site.data.keyword.sysdigsecure_full_notm}} dashboard.
{: shortdesc}

## Features
{: #scc-wp-dashboard-features}

The features {{site.data.keyword.sysdigsecure_short}} offers can be split into three main categories:
* Host scanning
* Posture management
* Threat detection

Below you find the brief introduction to each of them.

## Access the {{site.data.keyword.sysdigsecure_short}} Dashboard
{: #scc-wp-dashboard-overview-access-dashboard}

To check out these features, you need to open the {{site.data.keyword.sysdigsecure_short}} dashboard.

1. Open the [{{site.data.keyword.cloud_notm}} dashboard](https://cloud.ibm.com/login){: external}.

1.  Click the **Navigation menu** icon ![Navigation menu icon](../../icons/icon_hamburger.svg "Menu") > **Security** > **Resources**

1. From the list, select your {{site.data.keyword.sysdigsecure_short}} instance and click on it.

1. In the instance, click on **Open Dashboard**

## Host scanning
{: #scc-wp-dashboard-overview-host-scanning}

Host scanning can be used to find and prioritize software vulnerabilities.

1, After you open {{site.data.keyword.sysdigsecure_full_notm}}, click  **Vulnerabilities** >  and then click **Runtime**. You can see the systems that are being scanned.
1. Click the instance name to review the details. From the Vulnerabilities page, you can filter by various criteria, for example, filter by ‘Has fix’.
1. You can download a PDF report, or you can click **Vulnerabilities** > **Reporting** to build the report.

## Posture management
{: #scc-wp-dashboard-overview-posture-management}

To explore posture management, click **Compliance**. You can also click **Inventory** and check the posture for each inventory item.
These resources include both, cloud resources, and standalone hosts that you connected through the {{site.data.keyword.sysdigsecure_short}} agent.

Cloud resources only show up in this list when CSPM was enabled during instance creation.
{: note}


## Threat detection
{: #scc-wp-dashboard-overview-threat-detection}

* You can look at **Threats** -> **Host** or other platforms depending where you set up the agents.

* You can also look at **Integrations** -> **Sysdig Agents**.

## Additional Resources
{: #scc-wp-dashboard-overview-thread-detection}

* [Official {{site.data.keyword.sysdigsecure_short}} documentation](/docs/workload-protection?topic=workload-protection-getting-started&interface=ui)
