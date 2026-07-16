---
copyright:
  years: 2023, 2026
lastupdated: "2026-07-15"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Overview: High availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} running Red Hat Enterprise Linux
{: #powervs-ha-rhel-overview}

{{../../_include-segments/powervs-ha-overview.md}}

## High availability cluster setup references
{: #powervs-ha-rhel-overview-references}

Table 3 provides links to documentation for configuring high availability clusters.

| Cluster environment |  Documentation                                                                                                                                |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| One workspace       | [Implementing a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel)                                      |
| One workspace       | [Configuring an active-passive NFS Server in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-nfs)   |
| Multizone region    | [Implementing a Red Hat Enterprise Linux High Availability Add-On cluster in a multizone region environment](/docs/sap?topic=sap-ha-rhel-mz) |
| Multizone region    | [Implementing SBD poison-pill fencing in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-sbd)            |
{: caption="Documentation for implementing a basic cluster" caption-side="bottom"}
{: tab-title="Implementing a basic cluster"}
{: tab-group="doc-link-group"}
{: class="simple-tab-table"}
{: #powervs-ha-rhel-overview-doc-link-table1}


| Deployment scenario                                | Documentation |
| --------------------------------------------       | ------------- |
| SAP HANA single-host, performance-optimized        | [Configuring SAP HANA scale-up system replication in a Red Hat Enterprise Linux High Availability Add-On cluster with the sap-hana-ha resource agent](/docs/sap?topic=sap-ha-rhel-hana-ng-sh) |
| SAP HANA single-host, performance-optimized        | [Configuring SAP HANA scale-up system replication in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-hana-sr) |
| SAP HANA single-host, cost-optimized               | [Configuring SAP HANA cost-optimized scale-up system replication in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-hana-sr-cost-optimized) |
| SAP HANA single-host, active/active (read enabled) | [Configuring SAP HANA active/active (read enabled) system replication in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-hana-sr-aa) |
| SAP HANA single-host, multitier system replication | [Configuring SAP HANA multitier system replication in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-hana-sr-multitier) |
| SAP HANA multiple-hosts                            | [Configuring SAP HANA multiple-host system replication in a Red Hat Enterprise Linux High Availability Add-On cluster with the sap-hana-ha resource agent](/docs/sap?topic=sap-ha-rhel-hana-ng-mh) |
{: caption="Documentation for configuring high availability for SAP HANA" caption-side="bottom"}
{: tab-title="High availability for SAP HANA"}
{: tab-group="doc-link-group"}
{: class="simple-tab-table"}
{: #powervs-ha-rhel-overview-doc-link-table2}

| Deployment scenario         | Documentation                                  |
| --------------------------- |----------------------------------------------- |
| ENSA2 in one workspace      | [Configuring high availability for SAP S/4HANA (ASCS and ERS) in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-ensa) |
| ENSA2 in a multizone region | [Configuring high availability for SAP S/4HANA (ASCS and ERS) on Red Hat Enterprise Linux HA Add-On clusters in a multizone region with simple mount](/docs/sap?topic=sap-ha-rhel-ensa-sm-mz) \n\n [Configuring high availability for SAP S/4HANA (ASCS and ERS) in a Red Hat Enterprise Linux High Availability Add-On cluster in a multizone region environment](/docs/sap?topic=sap-ha-rhel-ensa-mz) |
{: caption="Documentation for configuring high availability for ASCS" caption-side="bottom"}
{: tab-title="High availability for ASCS"}
{: tab-group="doc-link-group"}
{: class="simple-tab-table"}
{: #powervs-ha-rhel-overview-doc-link-table3}
