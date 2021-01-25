---

copyright:
  years: 2020, 2021
lastupdated: "2021-01-25"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, Balanced, bh1, Compute Intensive, ch1, High Memory, mh1, Ultra Memory, umh1

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

# IBM Power Virtual Server certified profiles for SAP HANA
{: #hana-iaas-offerings-profiles-power-vs}

This is a complementary offering from {{site.data.keyword.IBM_notm}} Power Systems, with low latency access to {{site.data.keyword.cloud_notm}} services
{: note}

## Profiles list
{: #hana-iaas-power-vs-list}

The published names are subject to change.
{: note}


The following is an overview of the SAP-certified profiles with IBM Power Virtual Servers:

| **Profile** | **CPU Cores** | **CPU Threads (also known as. vCPU, using SMT-8)** | **Memory (RAM GB)** | **SAPS** | **SAP HANA Processing Type** |
| -- | -- | -- | -- | -- | -- |
| **Balanced** |   |   |   |   |   |
| bh1-16x1600 | 16 | 128 | 1600 GB | 96,000 | OLAP/OLTP |
| bh1-20x2000 | 20 | 160 | 2000 GB | 120,000 | OLAP * |
| bh1-22x2200 | 22 | 178 | 2200 GB | 132,000 | OLAP * |
| bh1-25x2500 | 25 | 200 | 2500 GB | 150,000 | OLAP * |
| bh1-30x3000 | 30 | 240 | 3000 GB | 180,000 | OLAP * |
| bh1-35x3500 | 35 | 280 | 3500 GB | 210,000 | OLAP * |
| bh1-40x4000 | 40 | 320 | 4000 GB | 240,000 | OLAP * |
| bh1-50x5000 | 50 | 400 | 5000 GB | 300,000 | OLAP * |
| bh1-60x6000 | 60 | 480 | 6000 GB | 360,000 | OLAP/OLTP |
| bh1-70x7000 | 70 | 560 | 7000 GB | 420,000 | OLAP * |
| bh1-80x8000 | 80 | 640 | 8000 GB | 480,000 | OLAP * |
| bh1-100x10000 | 100 | 800 | 10000 GB | 600,000 | OLAP * |
| bh1-120x12000 | 120 | 900 | 12000 GB | 720,000 | OLAP * |
| bh1-140x14000 | 140 | 1,120 | 14,000 GB | 840,000 | OLAP |
| **Compute Intensive** |   |   |   |   |  |
| ch1-60x3000 | 60 | 480 | 3000 GB | 360,000 | OLAP |
| ch1-70x3500 | 70 | 560 | 3500 GB | 420,000 | OLAP * |
| ch1-80x4000 | 80 | 640 | 4000 GB | 480,000 | OLAP * |
| ch1-100x5000 | 100 | 800 | 5000 GB | 600,000 | OLAP * |
| ch1-120x6000 | 120 | 900 | 6000 GB | 720,000 | OLAP * |
| ch1-140x7000 | 140 | 1,120 | 7000 GB | 840,000 | OLAP |
| **High Memory** |   |   |   |   |  |
| mh1-8x1440 | 8 | 64 | 1440 GB | 48,000 | OLAP |
| mh1-10x1800 | 10 | 80 | 1800 GB | 60,000 | OLAP |
| mh1-12x2160 | 12 | 96 | 2160 GB | 72,000 | OLAP * |
| mh1-16x2880 | 16 | 128 | 2880 GB | 96,000 | OLAP * |
| mh1-20x3600 | 20 | 160 | 3600 GB | 120,000 | OLAP * |
| mh1-22x3960 | 22 | 176 | 3960 GB | 132,000 | OLAP * |
| mh1-25x4500 | 25 | 200 | 4500 GB | 150,000 | OLAP * |
| mh1-30x5400 | 30 | 240 | 5400 GB | 180,000 | OLAP * |
| mh1-35x6300 | 35 | 280 | 6300 GB | 210,000 | OLAP * |
| mh1-40x7200 | 40 | 320 | 7200 GB | 240,000 | OLAP * |
| mh1-50x9000 | 50 | 400 | 9000 GB | 300,000 | OLAP * |
| mh1-60x10800 | 60 | 460 | 10800 GB | 360,000 | OLAP * |
| mh1-70x12600 | 70 | 560 | 12600 GB | 420,000 | OLAP * |
| mh1-80x14400 | 80 | 640 | 14400 GB | 480,000 | OLAP |
| **Ultra Memory** |   |   |   |   |  |
| umh1-4x960 | 4 | 32 | 960 GB | 24,000 | OLTP |
| umh1-6x1440 | 6 | 48 | 1440 GB | 36,000 | OLTP * |
| umh1-8x1920 | 8 | 64 | 1920 GB | 48,000 | OLTP * |
| umh1-10x2400 | 10 | 80 | 2400 GB | 60,000 | OLTP * |
| umh1-12x2880 | 12 | 96 | 2880 GB | 72,000 | OLTP * |
| umh1-16x3840 | 16 | 128 | 3840 GB | 96,000 | OLTP |
| umh1-20x4800 | 20 | 160 | 4800 GB | 120,000 | OLTP * |
| umh1-22x5280 | 22 | 176 | 5280 GB | 132,000 | OLTP * |
| umh1-25x6000 | 25 | 200 | 6000 GB | 150,000 | OLTP * |
| umh1-30x7200 | 30 | 240 | 7200 GB | 180,000 | OLTP * |
| umh1-35x8400 | 35 | 280 | 8400 GB | 210,000 | OLTP * |
| umh1-40x9600 | 40 | 320 | 9600 GB | 240,000 | OLTP * |
| umh1-50x12000 | 50 | 400 | 12000 GB | 300,000 | OLTP * |
| umh1-60x14400 | 60 | 480 | 14400 GB | 360,000 | OLTP |
{: caption="Table 1. SAP HANA servers" caption-side="top"}

Those marked with an `*` asterisk, are _not_ listed on the SAP HANA Hardware Directory by SAP but **are** certified for running SAP HANA production systems. The directory lists the smallest, median and largest within each profile family. This action has been taken by SAP to avoid too many records, as the scalability of IBM POWER hardware enables significantly more granular sizing. See [SAP Note 2947579 - SAP HANA on {{site.data.keyword.IBM_notm}} Power Systems Virtual Servers](https://launchpad.support.sap.com/#/notes/2947579){: external} for additional detail.
{: note}


## Understanding IBM Power Virtual Server profile names
{: #hana-iaas-power-vs-names}

The IBM Power Virtual Server for SAP HANA have profile names that are contextual and sequential. There are multiple families of profiles for SAP HANA, each with associated with the required Service Level Agreements (SLAs):
* `np1` = Non-product development for testing or development use only. Not intended for production deployments; not supported or certified by SAP production.
* `umh1` = Ultra Memory HANA for OLTP that use 1:240 as the cpu:memory ratio
* `mh1` = High Memory for OLAP that use 1:180 as the cpu:memory ratio
* `bh1` = Balanced for OLAP that use 1:100 as the cpu:memory ratio
* `ch1` = Compute Intensive for OLAP that use 1:50 as the cpu:memory ratio

The Virtual Server profile names are contextual and sequential, the following table uses an SAP HANA certified server as an example:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| umh1-16x3840 | umh1 | Ultra Memory SAP HANA for OLTP (higher on the CPU to Memory ratio), 1:240 ratio|
| | - | _spacer_ |
| | 16 | 16 CPU Cores |
| | x | _spacer_ |
| | 3840 | 3840 GB RAM |
{: caption="Table 2. Profile naming for SAP HANA" caption-side="top"}

## Profiles available on Hourly Consumption Billing
{: #hana-iaas-power-vs-hourly}

All {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} are available with Hourly pro-rata on Monthly Billing.
