---

copyright:
  years: 2020, 2024
lastupdated: "2024-12-05"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads

subcollection: sap

---

{{site.data.keyword.attribute-definition-list}}

# SAP NetWeaver certified instances on IBM Power Virtual Server
{: #nw-iaas-offerings-profiles-power-vs}

SAP NetWeaver certified instances on IBM Power Virtual Servers are available with fully adjustable CPU Cores and Memory (RAM GiB). It is permitted to define a custom size of the IBM Power Virtual Server instance to use for SAP NetWeaver, in accordance with existing SAP NetWeaver or SAP AnyDB for IBM best practices and guidance from SAP.

For SAP applications the following virtual server configurations are supported. Given SAP NetWeaver instance sizing is flexible, it must follow the standard SAP sizing guidelines by using the following SAPS benchmark calculation:

| **Power System type** | **SAPS per CPU Core (using SMT-8)** | 
| -- | -- | 
| S1022 | 7,600 | 
| E1080 | 7,600 | 
| S922 | 5,570 | 
| E980 | 6,000 | 
{: caption="{{site.data.keyword.IBM_notm}} SAP NetWeaver certified instances on IBM Power Virtual Server" caption-side="top"}

SAP NetWeaver certified instances on E1050 are currently not supported.
{: note}

For more information, see [SAP Note 2855850 - SAP Applications on IBM Power Virtual Servers](https://launchpad.support.sap.com/#/notes/2855850){: external}
