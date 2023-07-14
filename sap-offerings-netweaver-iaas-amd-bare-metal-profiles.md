---

copyright:
  years: 2020, 2023
lastupdated: "2023-07-14"

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
{: caption="Table 1. SAP NetWeaver servers AMD" caption-side="top"}

See also [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097){: external}.


## Understanding Bare Metal profile names
{: #nw-iaas-amd-bm-names}

The Bare Metal profile names are contextual and sequential, below uses an SAP NetWeaver certified server as an example:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| BI.S4A.NW2000 | BI | {{site.data.keyword.cloud_notm}} Infrastructure |
| | S4A | Series 3 (processor generation)<br/><ul><li>S3 is Intel Skylake/Kaby Lake</li><li>S4 is Intel Cascade Lake</li><li>S4A is AMD 2nd Gen EPYC</li></ul> |
| | NW | NetWeaver-certified server |
| | 2000 | 2000 GB RAM |
{: caption="Table 2. Profile naming for SAP NetWeaver AMD" caption-side="top"}

