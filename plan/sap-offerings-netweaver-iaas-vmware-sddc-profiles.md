---
copyright:
  years: 2020, 2025
lastupdated: "2025-07-29"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# VMware SDDC certified profiles for SAP NetWeaver
{: #nw-iaas-offerings-profiles-vmware}

## Profiles list
{: #nw-iaas-vmware-list}

The following tables provide an overview of the SAP-certified profiles with either:
- Intel Bare Metal and VMware vSphere (ESXi), manual VMware setup and configuration
- {{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated, automated VMware SDDC setup and configuration


### Profiles hosted on Intel Cascade Lake CPU
{: #nw-iaas-vmware-cl-list}


| **Profile** | **CPU Cores** | **CPU Threads (also known as. vCPU)** | **Memory (RAM GiB)** | **SAPS  (after VMware hypervisor 10%)** |
| --- | --- | --- | --- | --- |
| BI.S3.NW192 (VMware) | 36 | 72 | 192 GB | 70,965 |
| BI.S3.NW384 (VMware) | 36 | 72 | 384 GB | 71,487 |
| BI.S3.NW768 (VMware) | 36 | 72 | 768 GB | 71,667 |
| BI.S4.NW192 (VMware) | 32 | 64 | 192 GB | 74,223 |
| BI.S4.NW384 (VMware) | 32 | 64 | 384 GB | 76,617 |
| BI.S4.NW768 (VMware) | 40 | 80 | 768 GB | 101,547 |
| BI.S4.NW1500 (VMware) | 56 | 112 | 1536 GB | 132,498 |
| BI.S4.NW3000 (VMware) | 56 | 112 | 3072 GB | 121,614 |
{: caption="Intel servers certified for SAP application server - Intel Cascade Lake CPU" caption-side="top"}

### Profiles hosted on Intel Sapphire Rapids CPU
{: #nw-iaas-vmware-sr-list}


| **Profile** | **CPU Cores** | **CPU Threads (also known as. vCPU)** | **Memory (RAM GiB)** | **SAPS / aSAPS⁽¹⁾  (after VMware hypervisor 10%)** |
| --- | --- | --- | --- | --- |
| BI.S5.NW256 (VMware) | 36 | 72 | 256 GB | 119,367 / 22,770 |
| BI.S5.NW512 (VMware) | 36 | 72 | 512 GB | 115,875 / 22,140 |
| BI.S5.NW1024 (VMware) | 36 | 72 | 1,024 GB | 113,328 / 22,140 |
{: caption="Intel servers certified for SAP application server - Intel Sapphire Rapids CPU" caption-side="top"}

⁽¹⁾: aSAPS is the metric that is derived from the [SAP quote-to-cash (Q2C) Benchmark](https://www.sap.com/about/benchmark/appbm/q2c.html){: external}.

## Understanding Bare Metal profile names
{: #nw-iaas-vmware-names}

The Bare Metal profile names are contextual and sequential, below uses an SAP HANA certified server as an example:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| BI.S3.NW768 | BI | {{site.data.keyword.cloud_notm}} Infrastructure |
| | S4 | Series 4 (CPU generation)\n* S3: Intel Skylake/Kaby Lake\n* S4 is Intel Cascade Lake\n * S5: Intel Sapphire Rapids |
| | NW | SAP application server (NetWeaver) certified server |
| | ### | ### GiB RAM (rounded, exact RAM amount is in the table)|
{: caption="Profile naming for Intel servers certified for SAP application server" caption-side="top"}
