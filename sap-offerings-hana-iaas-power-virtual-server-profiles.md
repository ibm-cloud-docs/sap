---

copyright:
  years: 2020, 2021
lastupdated: "2021-10-04"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, Balanced, bh1, Compute Intensive, ch1, High Memory, mh1, Ultra Memory, umh1, small, ush1, 

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

When you provision {{site.data.keyword.IBM_notm}} Power Systems, you can select from the following families of profiles: Small, Balanced, Compute Intensive, Ultra Memory, and High Memory.
{: shortdesc}

This is a complementary offering from {{site.data.keyword.IBM_notm}} Power Systems, with low latency access to {{site.data.keyword.cloud_notm}} services
{: note}

A profile is a combination of instance attributes, such as the number of vCPUs, amount of RAM, and network bandwidth. The attributes define the size and capabilities of the virtual server instance that is provisioned. In the IBM Cloud console, you can select the most recently used profile or click View All Profiles to choose the profile that best fits your needs.



## Profile families
{: #hana-iaas-power-vs-list}

The published names are subject to change.
{: note}

The following is an overview of the SAP-certified profiles with IBM Power Virtual Servers and the following profile families are available:

| Families | Description |
| -------- | ----------- |
| [Small](#small) | Small profiles are best for balanced workloads requiring less CPU and storage consumption. |
| [Balanced](#balanced) | Balanced profiles are best for midsize databases and common cloud applications with moderate traffic. |
| [Compute Intensive](#computeintensive)  | Compute Intenstive profiles are best for moderate to high web traffic workloads. Compute profiles are best for workloads with intensive CPU demands, such as high web traffic workloads, production batch processing, and front-end web servers. |
| [Ultra Memory](#umemory) | Ultra High Memory profiles offer the highest vCPU to memory ratios to serve in-memory OLTP databases, such as SAP. |
| [High Memory](#hmemory) | Very High Memory profiles are best for server OLAP databases, such as SAP NetWeaver. |

## Small
{: #small}

| **Profile** | **CPU Cores** | **CPU Threads (also known as. vCPU, using SMT-8)** | **GiB RAM** | **Minimum Storage (GiB)** | **SAPS** | **SAP HANA Processing Type** |
| -- | -- | -- | -- | -- | -- | --|
| ush1-4x128 | 4 | 32 | 128 | 500 | 24,000 | OLAP/OLTP |
| ush1-4x256 | 4 | 32 | 256 | 910 | 24,000 | OLAP/OLTP |
| ush1-4x384 | 4 | 32 | 384 | 1240 | 24,000 | OLAP/OLTP |
| ush1-4x512 | 4 | 32 | 512 | 1650 | 24,000 | OLAP/OLTP |
| ush1-4x768 | 4 | 32 | 768 | 2140 | 24,000 | OLAP/OLTP |
{: caption="Table 5. SAP HANA servers Small profile family" caption-side="top"}

## Balanced
{: #balanced}

| **Profile** | **CPU Cores** | **CPU Threads (also known as. vCPU, using SMT-8)** | **GiB RAM** | **Minimum Storage (GiB)** | **SAPS** | **SAP HANA Processing Type** |
| -- | -- | -- | -- | -- | -- | --|
| bh1-16x1600 | 16 | 128 | 1600 | 3170 | 96,000 | OLAP/OLTP |
| bh1-20x2000 | 20 | 160 | 2000 | 3570 | 120,000 | OLAP * |
| bh1-22x2200 | 22 | 178 | 2200 | 3810 | 132,000 | OLAP * |
| bh1-25x2500 | 25 | 200 | 2500 | 4130 | 150,000 | OLAP * |
| bh1-30x3000 | 30 | 240 | 3000 | 4610 | 180,000 | OLAP * |
| bh1-35x3500 | 35 | 280 | 3500 | 5090 | 210,000 | OLAP * |
| bh1-40x4000 | 40 | 320 | 4000 | 5570 | 240,000 | OLAP * |
| bh1-50x5000 | 50 | 400 | 5000 | 6610 | 300,000 | OLAP * |
| bh1-60x6000 | 60 | 480 | 6000 | 7570 | 360,000 | OLAP/OLTP |
| bh1-70x7000 | 70 | 560 | 7000 | 8610 | 420,000 | OLAP * |
| bh1-80x8000 | 80 | 640 | 8000 | 9570 | 480,000 | OLAP * |
| bh1-100x10000 | 100 | 800 | 10,000 | 11,970 | 600,000 | OLAP * |
| bh1-120x12000 | 120 | 900 | 12,000 | 13,570 | 720,000 | OLAP * |
| bh1-140x14000 | 140 | 1,120 | 14,000 | 15,970 | 840,000 | OLAP |
| bh1-125x22500 | 125 | 1000  | 22,500  | 25,570 | 750,000 | OLAP |
{: caption="Table 1. SAP HANA servers Balance profile family" caption-side="top"}


## Compute Intensive
{: #computeintensive}

| **Profile** | **CPU Cores** | **CPU Threads (also known as. vCPU, using SMT-8)** | **GiB RAM** | **Minimum Storage (GiB)** | **SAPS** | **SAP HANA Processing Type** |
| -- | -- | -- | -- | -- | -- | --|
| ch1-60x3000 | 60 | 480 | 3000 | 4610 | 360,000 | OLAP |
| ch1-70x3500 | 70 | 560 | 3500 | 5090 | 420,000 | OLAP * |
| ch1-80x4000 | 80 | 640 | 4000 | 5570 | 480,000 | OLAP * |
| ch1-100x5000 | 100 | 800 | 5000 | 6610 | 600,000 | OLAP * |
| ch1-120x6000 | 120 | 900 | 6000 | 7570 | 720,000 | OLAP * |
| ch1-140x7000 | 140 | 1120 | 7000 | 8610 | 840,000 | OLAP |
{: caption="Table 2. SAP HANA servers Compute Intensive profile family" caption-side="top"}

## High Memory
{: #hmemory}

| **Profile** | **CPU Cores** | **CPU Threads (also known as. vCPU, using SMT-8)** | **GiB RAM** | **Minimum Storage (GiB)** | **SAPS** | **SAP HANA Processing Type** |
| -- | -- | -- | -- | -- | -- | --|
| mh1-8x1440 | 8 | 64 | 1440 | 3010 | 48,000 | OLAP |
| mh1-10x1800 | 10 | 80 | 1800 | 3410 | 60,000 | OLAP |
| mh1-12x2160 | 12 | 96 | 2160 | 3730 | 72,000 | OLAP * |
| mh1-16x2880 | 16 | 128 | 2880 | 4450 | 96,000 | OLAP * |
| mh1-20x3600 | 20 | 160 | 3600 | 5170 | 120,000 | OLAP * |
| mh1-22x3960 | 22 | 176 | 3960 | 5570 | 132,000 | OLAP * |
| mh1-25x4500 | 25 | 200 | 4500 | 6130 | 150,000 | OLAP * |
| mh1-30x5400 | 30 | 240 | 5400 | 7010 | 180,000 | OLAP * |
| mh1-35x6300 | 35 | 280 | 6300 | 7890 | 210,000 | OLAP * |
| mh1-40x7200 | 40 | 320 | 7200 | 8770 | 240,000 | OLAP * |
| mh1-50x9000 | 50 | 400 | 9000 | 11170 | 300,000 | OLAP * |
| mh1-60x10800 | 60 | 460 | 10800 | 12770 | 360,000 | OLAP * |
| mh1-70x12600 | 70 | 560 | 12600 | 14370 | 420,000 | OLAP * |
| mh1-80x14400 | 80 | 640 | 14400 | 15970 | 480,000 | OLAP |
| mh1-90x16200 | 90 | 720| 16200 | 19170 | 540,000| OLAP |
| mh1-100x18000 | 100 | 800 | 18000 | 20770 | 600,000 | OLAP |
{: caption="Table 3. SAP HANA servers High Memory profile family" caption-side="top"}

## Ultra Memory
{: #umemory}

| **Profile** | **CPU Cores** | **CPU Threads (also known as. vCPU, using SMT-8)** | **GiB RAM** | **Minimum Storage (GiB)** | **SAPS** | **SAP HANA Processing Type** |
| -- | -- | -- | -- | -- | -- | --|
| umh1-4x960 | 4 | 32 | 960 | 2490 | 24,000 | OLTP |
| umh1-6x1440 | 6 | 48 | 1440 | 3010 | 36,000 | OLTP * |
| umh1-8x1920 | 8 | 64 | 1920 | 3490 | 48,000 | OLTP * |
| umh1-10x2400 | 10 | 80 | 2400 | 3970 | 60,000 | OLTP * |
| umh1-12x2880 | 12 | 96 | 2880 | 4450 | 72,000 | OLTP * |
| umh1-16x3840 | 16 | 128 | 3840 | 5410 | 96,000 | OLTP |
| umh1-20x4800 | 20 | 160 | 4800 | 6370 | 120,000 | OLTP * |
| umh1-22x5280 | 22 | 176 | 5280 | 6850 | 132,000 | OLTP * |
| umh1-25x6000 | 25 | 200 | 6000 | 7570 | 150,000 | OLTP * |
| umh1-30x7200 | 30 | 240 | 7200 | 8770 | 180,000 | OLTP * |
| umh1-35x8400 | 35 | 280 | 8400 | 10370 | 210,000 | OLTP * |
| umh1-40x9600 | 40 | 320 | 9600 | 11170 | 240,000 | OLTP * |
| umh1-50x12000 | 50 | 400 | 12000 | 13570 | 300,000 | OLTP * |
| umh1-60x14400 | 60 | 480 | 14400 | 15970 | 360,000 | OLTP |
{: caption="Table 4. SAP HANA servers Ultra Memory profile family" caption-side="top"}

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
* `ush1` = Small for OLAP/OLTP for those workloads that don't require as much CPU and storage consumption

The Virtual Server profile names are contextual and sequential, the following table uses an SAP HANA certified server as an example:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| umh1-16x3840 | umh1 | Ultra Memory SAP HANA for OLTP (higher on the CPU to Memory ratio), 1:240 ratio|
| | - | _spacer_ |
| | 16 | 16 CPU Cores |
| | x | _spacer_ |
| | 3840 | 3840 GB RAM |
{: caption="Table 2. Profile naming for SAP HANA" caption-side="top"}


| Directory | Purpose | 
| --- | --- |
| root volume | 100 GB fixed |
| /usr/sap | 52 GB required in the test system |
| /hana/data | Matches the GiB RAM value |
| /hana/log | Matches the GiB RAM value up to a maximum of 512 GiB|
| /hana/shared | Matches the GiB RAM value up to a maximum of 1 TB |
| /export | Local storage for exported images |
| /backup | A preliminary backup on disk |
{: caption="Table 3. Directory information for profiles" caption-side="top"}

## Profiles available on Hourly Consumption Billing
{: #hana-iaas-power-vs-hourly}

All {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} are available with Hourly pro-rata on Monthly Billing.
