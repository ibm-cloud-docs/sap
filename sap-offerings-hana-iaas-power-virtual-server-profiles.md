---

copyright:
  years: 2020, 2022
lastupdated: "2022-04-25"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, Balanced, bh1, Compute Intensive, ch1, High Memory, mh1, Ultra Memory, umh, Small, ush1, Nonproduction, np1

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

When you provision {{site.data.keyword.IBM_notm}} Power Virtual Servers, you can select from the following families of profiles: [Small](#small), [Balanced](#balanced), [Compute Intensive](#computeintensive), [Ultra Memory](#umemory), and [High Memory](#hmemory).
{: shortdesc}

Certified profiles for SAP HANA are a complementary offering from {{site.data.keyword.IBM_notm}} Power Systems, with low latency access to {{site.data.keyword.cloud_notm}} services
{: note}

A profile is a combination of instance attributes, such as the number of vCPUs, amount of RAM, and network bandwidth. The attributes define the size and capabilities of the virtual server instance that is provisioned. You can select the most recently used profile or click **View All Profiles** to choose the profile that best fits your needs.

## Profile families
{: #hana-iaas-power-vs-list}

The published names are subject to change.
{: note}

The following section provides an overview of the SAP-certified profiles with {{site.data.keyword.IBM_notm}} Power Virtual Servers and available profile families.

| Families | Description |
| -------- | ----------- |
| [Custom](#custom) | Custom profiles are for nonproduct development for testing or development use only. These profiles are not intended for production deployments and are not supported or certified for SAP production. |
| [Small](#small) | Small profiles are best for balanced workloads that require less CPU and storage consumption. |
| [Balanced](#balanced) | Balanced profiles are best for midsize databases and common cloud applications with moderate traffic. |
| [Compute Intensive](#computeintensive)  | Compute Intensive profiles are best for moderate to high web traffic workloads. Compute profiles are best for workloads with intensive CPU demands, such as high web traffic workloads, production batch processing, and front-end web servers. |
| [Ultra Memory](#umemory) | Ultra High Memory profiles offer the highest vCPU to memory ratios to serve in-memory OLTP databases, such as SAP. |
| [High Memory](#hmemory) | Very High Memory profiles are best for server OLAP databases, such as SAP NetWeaver. |

## Custom
{: #custom}

{{site.data.keyword.IBM_notm}} Power Virtual Servers are available with fully adjustable CPU Cores and Memory (RAM GiB). When you create a virtual server instance, you can define a custom size of the {{site.data.keyword.IBM_notm}} Power Virtual Server profile to use for nonproduction SAP HANA instances, in accordance with existing SAP HANA for {{site.data.keyword.IBM_notm}} Power Systems best practices and guidance from SAP. 

Custom profiles are for nonproduct development for testing or development use only. These profiles aren't intended for production deployments and aren't supported or certified for SAP production. You cannot use this profile to go from a nonproduction environment to a production environment.

Each profile must have at least two dedicated cores. You can choose between Tier 1 (NVMe-based flash storage) and Tier 3 (SSD flash storage) storage. The Tier 1 storage type is best for customers who require higher throughput. Customers who don't require exceptionally high throughput and are looking to minimize costs want to select Tier 3. For more information, see the [What is a Power Systems Virtual Server?](/power-iaas?topic=power-iaas-about-virtual-server#storage-tiers) storage tiers section.

## Small
{: #small}

| **Profile** | **CPU Cores** | **CPU Threads (also known as vCPU that uses SMT-8)** | **GiB RAM** | **Minimum Storage (GiB)** | **SAPS** | **SAP HANA Processing Type** |
| -- | -- | -- | -- | -- | -- | --|
| ush1-4x128 | 4 | 32 | 128 | 500 | 24,000 | OLAP/OLTP |
| ush1-4x256 | 4 | 32 | 256 | 910 | 24,000 | OLAP/OLTP |
| ush1-4x384 | 4 | 32 | 384 | 1,240 | 24,000 | OLAP/OLTP |
| ush1-4x512 | 4 | 32 | 512 | 1,650 | 24,000 | OLAP/OLTP |
| ush1-4x768 | 4 | 32 | 768 | 2,140 | 24,000 | OLAP/OLTP |
{: caption="Table 1. SAP HANA servers Small profile family" caption-side="top"}

## Balanced
{: #balanced}

| **Profile** | **CPU Cores** | **CPU Threads (also known as vCPU that uses SMT-8)** | **GiB RAM** | **Minimum Storage (GiB)** | **SAPS** | **SAP HANA Processing Type** |
| -- | -- | -- | -- | -- | -- | --|
| bh1-16x1600 | 16 | 128 | 1,600 | 3,170 | 96,000 | OLAP/OLTP |
| bh1-20x2000 | 20 | 160 | 2,000 | 3,570 | 120,000 | OLAP * |
| bh1-22x2200 | 22 | 178 | 2,200 | 3,810 | 132,000 | OLAP * |
| bh1-25x2500 | 25 | 200 | 2,500 | 4,130 | 150,000 | OLAP * |
| bh1-30x3000 | 30 | 240 | 3,000 | 4,610 | 180,000 | OLAP * |
| bh1-35x3500 | 35 | 280 | 3,500 | 5,090 | 210,000 | OLAP * |
| bh1-40x4000 | 40 | 320 | 4,000 | 5,570 | 240,000 | OLAP * |
| bh1-50x5000 | 50 | 400 | 5,000 | 6,610 | 300,000 | OLAP * |
| bh1-60x6000 | 60 | 480 | 6,000 | 7,570 | 360,000 | OLAP/OLTP |
| bh1-70x7000 | 70 | 560 | 7,000 | 8,610 | 420,000 | OLAP * |
| bh1-80x8000 | 80 | 640 | 8,000 | 9,570 | 480,000 | OLAP * |
| bh1-100x10000 | 100 | 800 | 10,000 | 11,970 | 600,000 | OLAP * |
| bh1-120x12000 | 120 | 900 | 12,000 | 13,570 | 720,000 | OLAP * |
| bh1-140x14000 | 140 | 1,120 | 14,000 | 15,970 | 840,000 | OLAP |
{: caption="Table 2. SAP HANA servers Balanced profile family" caption-side="top"}


## Compute Intensive
{: #computeintensive}

| **Profile** | **CPU Cores** | **CPU Threads (also known as vCPU that uses SMT-8)** | **GiB RAM** | **Minimum Storage (GiB)** | **SAPS** | **SAP HANA Processing Type** |
| -- | -- | -- | -- | -- | -- | --|
| ch1-60x3000 | 60 | 480 | 3,000 | 4,610 | 360,000 | OLAP |
| ch1-70x3500 | 70 | 560 | 3,500 | 5,090 | 420,000 | OLAP * |
| ch1-80x4000 | 80 | 640 | 4,000 | 5,570 | 480,000 | OLAP * |
| ch1-100x5000 | 100 | 800 | 5,000 | 6,610 | 600,000 | OLAP * |
| ch1-120x6000 | 120 | 900 | 6,000 | 7,570 | 720,000 | OLAP * |
| ch1-140x7000 | 140 | 1,120 | 7,000 | 8,610 | 840,000 | OLAP |
{: caption="Table 3. SAP HANA servers Compute Intensive profile family" caption-side="top"}

## High Memory
{: #hmemory}

| **Profile** | **CPU Cores** | **CPU Threads (also known as vCPU that uses SMT-8)** | **GiB RAM** | **Minimum Storage (GiB)** | **SAPS** | **SAP HANA Processing Type** |
| -- | -- | -- | -- | -- | -- | --|
| mh1-8x1440 | 8 | 64 | 1,440 | 3,010 | 48,000 | OLAP |
| mh1-10x1800 | 10 | 80 | 1,800 | 3,410 | 60,000 | OLAP |
| mh1-12x2160 | 12 | 96 | 2,160 | 3,730 | 72,000 | OLAP * |
| mh1-16x2880 | 16 | 128 | 2,880 | 4,450 | 96,000 | OLAP * |
| mh1-20x3600 | 20 | 160 | 3,600 | 5,170 | 120,000 | OLAP * |
| mh1-22x3960 | 22 | 176 | 3,960 | 5,570 | 132,000 | OLAP * |
| mh1-25x4500 | 25 | 200 | 4,500 | 6,130 | 150,000 | OLAP * |
| mh1-30x5400 | 30 | 240 | 5,400 | 7,010 | 180,000 | OLAP * |
| mh1-35x6300 | 35 | 280 | 6,300 | 7,890 | 210,000 | OLAP * |
| mh1-40x7200 | 40 | 320 | 7,200 | 8,770 | 240,000 | OLAP * |
| mh1-50x9000 | 50 | 400 | 9,000 | 11,170 | 300,000 | OLAP * |
| mh1-60x10800 | 60 | 460 | 10,800 | 12,770 | 360,000 | OLAP * |
| mh1-70x12600 | 70 | 560 | 12,600 | 14,370 | 420,000 | OLAP * |
| mh1-80x14400 | 80 | 640 | 14,400 | 15,970 | 480,000 | OLAP |
<!--| mh1-90x16200 | 90 | 720| 16,200 | 19,170 | 540,000| OLAP |-->
<!--| mh1-100x18000 | 100 | 800 | 18,000 | 20,770 | 600,000 | OLAP |-->
<!--| mh1-125x22500 | 125 | 1,000  | 22,500  | 25,570 | 750,000 | OLAP |-->
{: caption="Table 4. SAP HANA servers High Memory profile family" caption-side="top"}

## Ultra Memory
{: #umemory}

| **Profile** | **CPU Cores** | **CPU Threads (also known as vCPU that uses SMT-8)** | **GiB RAM** | **Minimum Storage (GiB)** | **SAPS** | **SAP HANA Processing Type** |
| -- | -- | -- | -- | -- | -- | --|
| umh-4x960 | 4 | 32 | 960 | 2,490 | 24,000 | OLTP |
| umh-6x1440 | 6 | 48 | 1,440 | 3,010 | 36,000 | OLTP * |
| umh-8x1920 | 8 | 64 | 1,920 | 3,490 | 48,000 | OLTP * |
| umh-10x2400 | 10 | 80 | 2,400 | 3,970 | 60,000 | OLTP * |
| umh-12x2880 | 12 | 96 | 2,880 | 4,450 | 72,000 | OLTP * |
| umh-16x3840 | 16 | 128 | 3,840 | 5,410 | 96,000 | OLTP |
| umh-20x4800 | 20 | 160 | 4,800 | 6,370 | 120,000 | OLTP * |
| umh-22x5280 | 22 | 176 | 5,280 | 6,850 | 132,000 | OLTP * |
| umh-25x6000 | 25 | 200 | 6,000 | 7,570 | 150,000 | OLTP * |
| umh-30x7200 | 30 | 240 | 7,200 | 8,770 | 180,000 | OLTP * |
| umh-35x8400 | 35 | 280 | 8,400 | 10,370 | 210,000 | OLTP * |
| umh-40x9600 | 40 | 320 | 9,600 | 11,170 | 240,000 | OLTP * |
| umh-50x12000 | 50 | 400 | 12,000 | 13,570 | 300,000 | OLTP * |
| umh-60x14400 | 60 | 480 | 14,400 | 15,970 | 360,000 | OLTP |
{: caption="Table 5. SAP HANA servers Ultra Memory profile family" caption-side="top"}

Profile types that are marked with an `*` asterisk, are _not_ listed on the SAP HANA Hardware Directory by SAP but **are** certified for running SAP HANA production systems. The directory lists the smallest, median, and largest within each profile family. This action was taken by SAP to avoid too many records, as the scalability of IBM POWER hardware enables significantly more granular sizing. For more information, see [SAP Note 2947579 - SAP HANA on {{site.data.keyword.IBM_notm}} Power Virtual Servers](https://launchpad.support.sap.com/#/notes/2947579){: external}.
{: note}

## Understanding IBM Power Virtual Server profile names
{: #hana-iaas-power-vs-names}

The IBM Power Virtual Server for SAP HANA have profile names that are contextual and sequential. SAP HANA has multiple families of profiles, each associated with the required Service Level Agreements (SLAs):

* `cnp1` = Nonproduct development for testing or development use only. Not intended for production deployments; not supported or certified for SAP production.
* `ush1` = Small for OLAP/OLTP workloads that don't require as much CPU and storage consumption
* `umh` = Ultra Memory HANA for OLTP that use 1:240 as the cpu:memory ratio
* `mh1` = High Memory for OLAP that use 1:180 as the cpu:memory ratio
* `bh1` = Balanced for OLAP that use 1:100 as the cpu:memory ratio
* `ch1` = Compute Intensive for OLAP that use 1:50 as the cpu:memory ratio

The Virtual Server profile names are contextual and sequential. The following table uses an SAP HANA certified server as an example:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| umh-16x3840 | umh | Ultra Memory SAP HANA for OLTP (higher on the CPU to Memory ratio), 1:240 ratio|
| | - | _spacer_ |
| | 16 | 16 CPU Cores |
| | x | _spacer_ |
| | 3840 | 3840 GiB RAM |
{: caption="Table 6. Profile naming scheme for SAP HANA" caption-side="top"}


| Directory | Purpose | 
| --- | --- |
| root volume | 100 GiB fixed |
| /usr/sap | 52 GiB required in the test system |
| /hana/data | Matches the GiB RAM value |
| /hana/log | Matches the GiB RAM value up to a maximum of 512 GiB|
| /hana/shared | Matches the GiB RAM value up to a maximum of 1 TB |
| /export | Local storage for exported images |
| /backup | A preliminary backup on disk |
{: caption="Table 7. Directory information for profiles" caption-side="top"}

## Profiles available on Hourly Consumption Billing
{: #hana-iaas-power-vs-hourly}

All {{site.data.keyword.IBM_notm}} Power Virtual Server are available with Hourly pro-rata on Monthly Billing.
