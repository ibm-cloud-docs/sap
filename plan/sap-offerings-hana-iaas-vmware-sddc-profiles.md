---
copyright:
  years: 2020, 2025
lastupdated: "2025-08-15"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# VMware SDDC certified profiles for SAP HANA
{: #hana-iaas-offerings-profiles-vmware}

## Profiles list
{: #hana-iaas-vmware-list}

The following table is an overview of the SAP-certified profiles with either:
- Intel Bare Metal and VMware vSphere (ESXi), manual VMware setup and configuration
- {{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated, automated VMware SDDC setup and configuration


| **Profile** | **CPU Cores** | **CPU Threads (also known as. vCPU)** | **Memory (RAM GiB)** | **SAPS (after VMware hypervisor 10%)** | **SAP HANA Processing Type** |
| --- | --- | --- | --- | --- | --- |
| BI.S3.H2.192 (VMware) | 36 | 72 | 192 GB | 70,965 | OLAP, OLTP |
| BI.S3.H2.384 (VMware) | 36 | 72 | 384 GB | 71,487 | OLAP, OLTP |
| BI.S3.H2.768 (VMware) | 36 | 72 | 768 GB | 71,667 | OLAP, OLTP |
| BI.S4.H2.192 (VMware) | 32 | 64 | 192 GB | 74,223 | OLAP, OLTP |
| BI.S4.H2.384 (VMware) | 32 | 64 | 384 GB | 76,617 | OLAP, OLTP |
| BI.S4.H2.768 (VMware) | 40 | 80 | 768 GB | 101,547 | OLAP, OLTP |
| BI.S4.H2.1500 (VMware) | 56 | 112 | 1536 GB | 132,498 | OLAP, OLTP |
| BI.S4.H2.3000 (VMware) | 56 | 112 | 3072 GB | 121,614 | OLAP, OLTP |
| BI.S4.H4.3000 (VMware) | 112 | 224 | 3072 GB | 257,373 | OLAP, OLTP |
| BI.S4.H4.6000 (VMware) | 112 | 224 | 6144 GB | 257,373 | OLAP, OLTP |
| BI.S4.H8.6000 (VMware) | 224 | 448 | 6144 GB | 495,603 | OLAP, OLTP |
{: caption="SAP HANA servers" caption-side="top"}


## Understanding Bare Metal profile names
{: #hana-iaas-vmware-names}

The Bare Metal profile names are contextual and sequential, below uses an SAP HANA certified server as an example:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| BI.S4.H4.3000 | BI | {{site.data.keyword.cloud_notm}} Infrastructure |
| | S3 | Series 3 (processor generation)  \n * S3 is Intel Skylake/Kaby Lake\n * S4 is Intel Cascade Lake |
| | H | HANA-certified server |
| | # | #-socket server (2, 4 or 8)|
| | #### | #### GiB RAM (rounded, exact RAM amount is in the table)|
{: caption="Profile naming for SAP HANA" caption-side="top"}
