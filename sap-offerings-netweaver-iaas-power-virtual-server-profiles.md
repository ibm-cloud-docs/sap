---

copyright:
  years: 2020
lastupdated: "2020-12-17"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads

subcollection: sap

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:note: .note}
{:tip: .tip}

# IBM Power Virtual Server certified profiles for SAP NetWeaver
{: #nw-iaas-offerings-profiles-power-vs}

This is a complementary offering from {{site.data.keyword.IBM_notm}} Power Systems, with low latency access to {{site.data.keyword.cloud_notm}} services
{: note}

## Understanding IBM Power Virtual Server profile names
{: #nw-iaas-power-vs-names}

The IBM Power Virtual Server are available with fully adjustable CPU Cores and Memory (RAM GB). It is permitted to define a custom size of the IBM Power Virtual Server to use for SAP NetWeaver, in accordance with existing SAP NetWeaver or SAP AnyDB for {{site.data.keyword.IBM_notm}} Power Systems best practices and guidance from SAP.

Therefore, no profile names are used to define running SAP NetWeaver or SAP AnyDB that uses IBM Power Virtual Servers.

For SAP applications the following virtual server configurations are supported. Given SAP NetWeaver instance sizing is flexible, it must follow the standard SAP sizing guidelines by using the following SAPS benchmark calculation:

| **Power System type** | **SAPS per CPU Core** | **SAPS per CPU Thread (using SMT-8)** |
| -- | -- | -- |
| S922 | 5,570 | 696.25 |
| E980 | 6,000 | 750 |
{: caption="Table 1. {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} certified for SAP NetWeaver" caption-side="top"}

For more information, see [SAP Note 2855850 - SAP Applications on {{site.data.keyword.IBM_notm}} Power Systems Virtual Servers](https://launchpad.support.sap.com/#/notes/2855850){: external}


## Profiles available on Hourly Consumption Billing
{: #nw-iaas-power-vs-hourly}

All {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} are available with Hourly pro-rata on Monthly Billing.
