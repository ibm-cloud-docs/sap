---

copyright:
  years: 2020, 2021, 2022
lastupdated: "2022-01-28"

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

# Intel Virtual Server certified profiles on VPC infrastructure for SAP NetWeaver
{: #nw-iaas-offerings-profiles-intel-vs-vpc}

## Profiles list
{: #nw-iaas-intel-vs-vpc-list}

The published names are subject to change.
{: note}

The following list gives you an overview of the SAP-certified profiles with {{site.data.keyword.vsi_is_short}}:

| **Profile** | **vCPU** | **Memory (RAM GiB)** | **SAPS** |
| --- | --- | --- | --- |
| **Compute Optimized** | | | |
| cx2-2x4 | 2 | 4 | 2,238 |
| cx2-4x8 | 4 | 8 |  4,475 |
| cx2-8x16 | 8 | 16 |  8,950 |
| cx2-16x32 | 16 | 32 | 17,900 |
| cx2-32x64 | 32 | 64 | 35,800 |
| cx2-48x96 | 48 | 96 | 53,700 |
| cx2-64x128 | 64 | 128 | 71,600 |
| cx2-96x192 | 96 | 192 | 107,400 |
| cx2-128x256 | 128 | 256 | 143,200 |
| **Balanced** | | | |
| bx2-2x8 | 2 | 8 | 2,306 |
| bx2-4x16 | 4 | 16 |  4,613 |
| bx2-8x32 | 8 | 32 |  9,225 |
| bx2-16x64 | 16 | 64 | 18,450 |
| bx2-32x128 | 32 | 128 | 36,900 |
| bx2-48x192 | 48 | 192 | 55,350 |
| bx2-64x256 | 64 | 256 | 81,685 |
| bx2-96x384 | 96 | 384 | 122,528 |
| bx2-128x512 | 128 | 512 | 163,370 |
| **Memory Optimized** | | | |
| mx2-2x16 | 2 | 16 | 2,571 |
| mx2-4x32 | 4 | 32 | 5,141 |
| mx2-8x64 | 8 | 64 | 10,283 |
| mx2-16x128 | 16 | 128 | 20,565 |
| mx2-32x256 | 32 | 256 | 41,130 |
| mx2-48x384 | 48 | 384 | 56,970 |
| mx2-64x512 | 64 | 512 | 81,015 |
| mx2-96x768 | 96 | 768 | 121,523 |
| mx2-128x1024 | 128 | 1,024 | 162,030 |
| **Very High Memory Optimized** | |		
| vx2d-2x28 | 2 | 28 | 2,131 |
| vx2d-4x56 | 4 | 56 | 4,262 |
| vx2d-8x112 | 8 | 112 | 8,523 |
| vx2d-16x224| 16 | 224 |17,046 |
| vx2d-44x616 | 44 | 616 |46,875 |
| vx2d-88x1232 | 88 | 1,232 | 93,750 |
| vx2d-144x2016 | 144 | 2,016 | 153,410 |
| vx2d-176x2464 | 176 | 2,464 | 187,500 |
| **Ultra High Memory Optimized** | | | |
| ux2d-2x56 | 2 | 56 | 2,156 |
| ux2d-4x112 | 4 | 112 | 4,312 |
| ux2d-8x224 | 8 | 224 |  8,623 |
| ux2d-16x448 | 16 | 448 |  17,246 |
| ux2d-36x1008 | 36 | 1,008 |  38,803 |
| ux2d-48x1344| 48 | 1,344 | 51,737 |
| ux2d-72x2016 | 72 | 2,016 |  77,606 |
| ux2d-100x2800 | 100 | 2,800 | 107,785 |
| ux2d-200x5600 | 200 | 5,600 | 215,570 |
{: caption="Table 1. {{site.data.keyword.cloud_notm}} {{site.data.keyword.vsi_is_short}} certified for SAP NetWeaver" caption-side="bottom"}


For more information, see [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211){: external}.


## Understanding Virtual Server profile names
{: #nw-iaas-intel-vs-vpc-names}

With {{site.data.keyword.vsi_is_full}}, the profile families that are certified for SAP are: *Compute Optimized*, *Balanced*, *Memory Optimized*, *Very High Memory Optimized*, and *Ultra High Memory Optimized*.
- *Compute Optimized* family profiles provide more compute power, and they have more cores with less memory.
- *Balanced* family profiles provide a good mix of performance and scalability for more common workloads.
- All the memory family profiles cater to memory intensive workloads, such as demanding database applications and in-memory analytics workloads, and are especially designed for SAP HANA workloads.

For more information, see chapter [x86-64 instance profiles](/docs/vpc?topic=vpc-profiles).


The first letter of the profile name indicates the profile family mentioned above:

| First letter | Characteristics of the related profile family |
| --- | --- |
| c | *Compute Optimized* family, vCPU to memory ratio 1:2 |
| b | *Balanced* family, vCPU to memory ratio 1:4 |
| m | *Memory Optimized* family, higher vCPU to memory ratio 1:8 |
| v | *Very High Memory Optimized*  family, very high vCPU to memory ratio 1:14 |
| u | *Ultra High Memory Optimized* family, ultra high vCPU to memory ratio 1:28 |
{: caption="Table 2. {{site.data.keyword.vsi_is_full}} Profile Families" caption-side="top"}


For SAP HANA, only the memory profile families are used, for NetWeaver also the *Compute Optimized* and the *Balanced* families may be considered. The Virtual Server profile names are contextual and sequential. See here one example:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| mx2-16x128 | m | *Memory Optimized* family |
| | x | Intel x86_64 CPU Architecture |
| | 2 | The generation for the underlying hardware |
| | — | _spacer_ |
| | 16 | 16 vCPU |
| | x | _spacer_ |
| | 128 | 128 GiB RAM |
{: caption="Table 3. Profile naming for SAP HANA" caption-side="top"}


## Profiles available on Hourly Consumption Billing
{: #nw-iaas-intel-vs-vpc-hourly}

All {{site.data.keyword.cloud_notm}} {{site.data.keyword.vsi_is_short}} are available with Hourly Consumption Billing, which includes Suspend Discounts and Sustained Usage Discounts. With Suspend Discounts, storage charges occur only if the server is in Shutdown state. With Sustained Usage Discount, the more a server is used, the less the cost per hour.


## Storage specifications
{: #nw-iaas-intel-vs-vpc-storage-specs}

When the virtual server profiles for SAP HANA are initially provisioned, the servers all have one pre-configured volume (vda) attached with the following basic layout:

| File system | Partition | Storage type | size (GB) | Nr. of\nIOPS |
| --- | --- | --- | --- | --- |
| `/` | `vda1` | Pre-configured boot volume | 100 | 3,000 |
| `/boot` | `vda2` | Pre-configured boot volume | 0.25 | 3,000 |
{: caption="Table 4. Storage configuration of the default virtual server deployment (boot volume)" caption-side="top"}

To fulfill the size and I/O requirements for SAP NetWeaver or SAP AnyDB, more [{{site.data.keyword.block_storage_is_full}}](/docs/vpc?topic=vpc-block-storage-about) volumes need to be added as data volumes to the virtual server configuration.

Block Storage Volumes for Virtual Servers can be created based on different **volume profiles** that provide different levels of IOPS per gigabyte (IOPS/GB). For more information, see [IOPS tiers](/docs/vpc?topic=vpc-block-storage-profiles#tiers) for details.

You must consider the total IOPS required for your installation and the performance characteristics of your database. One option is to colocate multiple directories into a single large volume with high IOPS, versus isolating directories into individual small volumes with an insufficient number of IOPS for the workload characteristics.

For an overview of all available storage profiles, see [VPC Block Storage Profiles](/docs/vpc?topic=vpc-block-storage-profiles).

For SAP NetWeaver and SAP AnyDB, a minimum of 5 IOPS/GB is recommended.

Samples of storage configurations that use Intel Virtual Server (Gen2) profiles are available under [Storage design considerations](/docs/sap?topic=sap-storage-design-considerations).
