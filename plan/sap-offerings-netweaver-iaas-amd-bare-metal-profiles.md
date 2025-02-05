---

copyright:
  years: 2020, 2025
lastupdated: "2025-02-05"

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

# AMD Bare Metal server certified profiles on Classic infrastructure for SAP NetWeaver
{: #nw-iaas-offerings-profiles-amd-bm}

## Profiles list
{: #nw-iaas-amd-bm-list}

The published names are subject to change.
{: note}

The following is an overview of the SAP-certified profiles with AMD Bare Metal:

| **Profile** | **CPU Cores** | **CPU Threads (aka. vCPU)** | **Memory (RAM GB)** | **SAPS** |
| --- | --- | --- | --- | --- |
| BI.S4A.NW2000 | 96 | 192 | 2 TB | 265,650 |
| BI.S4A.NW4000 | 96 | 192 | 4 TB | 267,450 |
{: caption="SAP NetWeaver AMD servers" caption-side="top"}

See also [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}.


## Understanding Bare Metal profile names
{: #nw-iaas-amd-bm-names}

The Bare Metal profile names are contextual and sequential, below uses an SAP NetWeaver certified server as an example:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| BI.S4A.NW2000 | BI | {{site.data.keyword.cloud_notm}} Infrastructure |
| | S4A | Series 3 (processor generation)<br/><ul><li>S3: Intel Skylake/Kaby Lake</li><li>S4: Intel Cascade Lake</li><li>S4A: AMD 2nd Gen EPYC</li><li>S5: Intel Sapphire Rapids</li></ul> |
| | NW | NetWeaver-certified server |
| | 2000 | 2000 GB RAM |
{: caption="Profile naming for SAP NetWeaver AMD servers" caption-side="top"}
