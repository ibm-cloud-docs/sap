---
copyright:
  years: 2020, 2025
lastupdated: "2025-07-29"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Intel Bare Metal server certified profiles on Classic infrastructure for SAP application server
{: #nw-iaas-offerings-profiles-intel-bm}

## Profiles list for Intel servers
{: #nw-iaas-intel-bm-list}

The following tables provide an overview of the SAP-certified profiles with Bare Metal Intel servers.

### Profiles hosted on Intel Cascade Lake CPU
{: #nw-iaas-intel-bm-cl-list}


| **Profile** | **CPU Cores** | **CPU Threads (aka. vCPU)** | **Memory (RAM GB)** | **SAPS** |
| --- | --- | --- | --- | --- |
| [BI.S3.NW32](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=8451&packageId=1041&itemId=10831){: external} | 4 | 8 | 32 GB | 11,970 |
| [BI.S3.NW64](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=8451&packageId=1043&itemId=10831){: external} | 4 | 8 | 64 GB | 12,750 |
| [BI.S3.NW192](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=8451&packageId=989&itemId=10437){: external} | 36 | 72 | 192 GB | 78,850 |
| [BI.S3.NW384](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=8451&packageId=987&itemId=10437){: external} | 36 | 72 | 384 GB | 79,430 |
| [BI.S3.NW768](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=8451&packageId=985&itemId=10437){: external} | 36 | 72 | 768 GB | 79,630 |
| [BI.S4.NW192](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=8451&packageId=2640&itemId=13285){: external} | 32 | 64 | 192 GB | 82,470 |
| [BI.S4.NW384](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=8451&packageId=2642&itemId=13285){: external} | 32 | 64 | 384 GB | 85,130 |
| BI.S4.NW384_v3 | 16 | 32 | 384 GB | 60,420 |
| [BI.S4.NW768](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=8451&packageId=2644&itemId=13289){: external} | 40 | 80 | 768 GB | 112,830 |
| BI.S4.NW768_v2 | 48 | 96 | 768 GB | 124,620 |
| BI.S4.NW768_v3 | 16 | 32 | 768 GB |  60,420 |
| BI.S4.NW1500 | 56 | 112 | 1.5 TB | 147,220 |
| BI.S4.NW3000 | 56 | 112 | 3 TB | 135,127 |
{: caption="Intel servers certified for SAP application server" caption-side="top"}

### Profiles hosted on Intel Sapphire Rapids CPU
{: #nw-iaas-intel-bm-sr-list}

| **Profile** | **CPU Cores** | **CPU Threads (aka. vCPU)** | **Memory (RAM GB)** | **SAPS** | **aSAPS ⁽¹⁾** |
| --- | --- | --- | --- | --- | --- |
| BI.S5.NW256 | 36 | 72 | 256 GB | 132,630 |  25,300 |
| BI.S5.NW512 | 36 | 72 | 512 GB | 128,750 |  24,600 |
| BI.S5.NW1024 | 36 | 72 | 1,024 GB | 125,920 |  24,600 |
{: caption="Intel servers certified for SAP application server" caption-side="top"}

⁽¹⁾: aSAPS is the metric that is derived from the [SAP quote-to-cash (Q2C) Benchmark](https://www.sap.com/about/benchmark/appbm/q2c.html){: external}.


See also [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}.


## Understanding Bare Metal profile names
{: #nw-iaas-intel-bm-names}

The Bare Metal profile names are contextual and sequential, below uses a server that is certified for SAP application server as an example:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| BI.S3.NW384 | BI | {{site.data.keyword.cloud_notm}} Infrastructure |
| | S3 | Series 3 (CPU generation)\n* S3: Intel Skylake/Kaby Lake\n * S4: Intel Cascade Lake\n * S5: Intel Sapphire Rapids |
| | NW | SAP application server (NetWeaver) certified server |
| | H2 | HANA-(*) and SAP application server certified server, 2 sockets  |
| | 384 | 384 GB RAM |
{: caption="Profile naming for Intel servers certified for SAP application server" caption-side="top"}

(*) Only the servers that are listed in the [SAP Hardware Directory](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:28){: external} are supported for HANA workloads.
{: note}

## Profiles available on Hourly Consumption Billing
{: #nw-iaas-intel-bm-hourly}

The following Bare Metal servers are available on **Hourly** Consumption Billing:
- BI.S3.NW32   (OS Options)
- BI.S4.NW192  (OS Options)
- BI.S4.NW384  (OS Options)
- BI.S4.NW768  (OS Options)
