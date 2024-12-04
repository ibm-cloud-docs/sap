---

copyright:
  years: 2020, 2024
lastupdated: "2024-12-03"

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

# Intel Bare Metal server certified profiles on Classic infrastructure for SAP NetWeaver
{: #nw-iaas-offerings-profiles-intel-bm}

## Profiles list for Intel servers
{: #nw-iaas-intel-bm-list}

The following is an overview of the SAP-certified profiles with Bare Metal Intel servers:

| **Profile** | **CPU Cores** | **CPU Threads (aka. vCPU)** | **Memory (RAM GB)** | **SAPS** |
| --- | --- | --- | --- | --- |
| [BI.S3.NW32](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=8451&packageId=1041&itemId=10831) | 4 | 8 | 32 GB | 11,970 |
| [BI.S3.NW64](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=8451&packageId=1043&itemId=10831) | 4 | 8 | 64 GB | 12,750 |
| [BI.S3.NW192](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=8451&packageId=989&itemId=10437) | 36 | 72 | 192 GB | 78,850 |
| [BI.S3.NW384](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=8451&packageId=987&itemId=10437) | 36 | 72 | 384 GB | 79,430 |
| [BI.S3.NW768](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=8451&packageId=985&itemId=10437) | 36 | 72 | 768 GB | 79,630 |
| [BI.S4.NW192](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=8451&packageId=2640&itemId=13285) | 32 | 64 | 192 GB | 82,470 |
| [BI.S4.NW384](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=8451&packageId=2642&itemId=13285) | 32 | 64 | 384 GB | 85,130 |
| BI.S4.NW384_v3 | 16 | 32 | 384 GB | 60,420 |
| [BI.S4.NW768](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=8451&packageId=2644&itemId=13289) | 40 | 80 | 768 GB | 112,830 |
| BI.S4.NW768_v2 | 48 | 96 | 768 GB | 124,620 |
| BI.S4.NW768_v3 | 16 | 32 | 768 GB |  60,420 |
| BI.S4.NW1500 | 56 | 112 | 1.5 TB | 147,220 |
| BI.S4.NW3000 | 56 | 112 | 3 TB | 135,127 |
| BI.S5.H2.1000 | 96 | 192 | 1 TB | 297.370 |
| BI.S5.H2.2001 | 96 | 192 | 2 TB | 297.370 |
| BI.S5.H2.2000 | 120 | 240 | 2 TB | 322.550 |
| BI.S5.H2.3000 | 120 | 240 | 3 TB | 327.295 |
| BI.S5.H2.4000 | 120 | 240 | 4 TB | 329.350 |
{: caption="SAP NetWeaver Intel servers" caption-side="top"}


See also [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}.


## Understanding Bare Metal profile names
{: #nw-iaas-intel-bm-names}

The Bare Metal profile names are contextual and sequential, below uses an SAP NetWeaver certified server as an example:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| BI.S3.NW384 | BI | {{site.data.keyword.cloud_notm}} Infrastructure |
| | S3 | Series 3 (CPU generation)<br/><ul><li>S3: Intel Skylake/Kaby Lake</li><li>S4: Intel Cascade Lake</li><li>S5: Intel Sapphire Rapids</li></ul> |
| | NW | NetWeaver-certified server |
| | H2 | HANA-(*) and NetWeaver-certified server, 2 sockets  |
| | 384 | 384 GB RAM |
{: caption="Profile naming for SAP NetWeaver Intel servers" caption-side="top"}

(*) Only the servers that are listed in the [SAP Hardware Directory](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:28) are supported for HANA workloads.
{: note}

## Profiles available on Hourly Consumption Billing
{: #nw-iaas-intel-bm-hourly}

The following Bare Metal servers are available on **Hourly** Consumption Billing:
- BI.S3.NW32   (OS Options)
- BI.S4.NW192  (OS Options)
- BI.S4.NW384  (OS Options)
- BI.S4.NW768  (OS Options)
