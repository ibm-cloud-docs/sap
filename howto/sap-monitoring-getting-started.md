---
copyright:
  years: 2024, 2025
lastupdated: "2025-02-05"

keywords: SAP, SAP Monitoring, {{site.data.keyword.cloud_notm}}, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Getting started with {{site.data.keyword.cloud_notm}} Monitoring for SAP systems
{: #mon-getting-started}

The following information describes a specific use case for setting up monitoring for SAP.

To prepare for the monitoring scenario, deploy one {{site.data.keyword.cloud}} Monitoring instance and one virtual server in IBM VPC.

{{site.data.keyword.cloud}} Monitoring offers predefined SAP dashboards from {{site.data.keyword.cloud}} dashboard library free of charge.
{: shortdesc}

This setup does not support SAP systems in a High Availability setup.
{: restriction}

The metrics of monitored SAP systems are collected by an application that runs on the SLES VPC host. This means that you need an extra VPC host if your deployable architecture is RHEL-based.

The detailed information about Monitoring in {{site.data.keyword.cloud}} is described in [Collecting metrics by using Prometheus remote write](/docs/monitoring?topic=monitoring-about-monitor){: external}.

As a prerequisite for getting started you have to deploy an infrastructure and SAP system landscape that is up and running without issues.

The following architecture overview diagram shows {{site.data.keyword.cloud}} Monitoring for SAP systems when the infrastructure and SAP system landscape are built by a [deployable architecture](/docs/sap-powervs?topic=sap-powervs-sap-s4hana-bw4hana){: external}.

![Figure 1. {{site.data.keyword.cloud}} Monitoring for SAP systems](../images/sap-monitoring-in-ibm-cloud.svg "{{site.data.keyword.cloud}} Monitoring for SAP systems")
{: caption="{{site.data.keyword.cloud}} Monitoring for SAP systems" caption-side="bottom"}
{: external download="sap-monitoring-in-ibm-cloud.svg"}

To set up SAP monitoring, you must perform the following tasks.

- [Checking and preparing SAP system for monitoring](/docs/sap?topic=sap-mon-preparing-system)
- [Creating of monitoring instance in {{site.data.keyword.cloud}}](/docs/sap?topic=sap-mon-create-instance)
- [Setup and configuration of a monitoring host](/docs/sap?topic=sap-mon-exporter-setup-config)
- [Configuration of Prometheus server metric forwarding](/docs/sap?topic=sap-mon-metric-forwarding)
- [Launch the monitoring web UI and work with dashboards](/docs/sap?topic=sap-mon-launch-web-ui-dashboards)
