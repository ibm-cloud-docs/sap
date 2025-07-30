---
copyright:
  years: 2020, 2025
lastupdated: "2025-07-29"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# AMD Bare Metal server certified profiles on Classic infrastructure for SAP application server
{: #nw-iaas-offerings-profiles-amd-bm}

## Profiles list
{: #nw-iaas-amd-bm-list}

The following is an overview of the SAP-certified profiles with AMD Bare Metal:

| **Profile** | **CPU Cores** | **CPU Threads (aka. vCPU)** | **Memory (RAM GB)** | **SAPS** |
| --- | --- | --- | --- | --- |
| BI.S4A.NW2000 | 96 | 192 | 2 TB | 265,650 |
| BI.S4A.NW4000 | 96 | 192 | 4 TB | 267,450 |
{: caption="AMD servers certified for SAP application server" caption-side="top"}

See also [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}.


## Understanding Bare Metal profile names
{: #nw-iaas-amd-bm-names}

The Bare Metal profile names are contextual and sequential, below uses a server that is certified for SAP application server as an example:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| BI.S4A.NW2000 | BI | {{site.data.keyword.cloud_notm}} Infrastructure |
| | S4A | Series 3 (processor generation)\n* S3: Intel Skylake/Kaby Lake\n* S4: Intel Cascade Lake\n* S4A: AMD 2nd Gen EPYC\n* S5: Intel Sapphire Rapids |
| | NW | SAP application server (NetWeaver) certified server |
| | 2000 | 2000 GB RAM |
{: caption="Profile naming for AMD servers certified for SAP application server" caption-side="top"}
