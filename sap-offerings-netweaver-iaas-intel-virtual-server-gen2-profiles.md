---

copyright:
  years: 2020, 2021
lastupdated: "2021-05-27"

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

# Intel Virtual Server certified profiles for SAP NetWeaver
{: #nw-iaas-offerings-profiles-intel-vs-vpc}

## Profiles list
{: #nw-iaas-intel-vs-vpc-list}

The published names are subject to change.
{: note}

The following list gives you an overview of the SAP-certified profiles with {{site.data.keyword.vsi_is_short}}:

| **Profile** | **vCPU** | **Memory (RAM GB)** | **SAPS** |
| --- | --- | --- | --- |
| **Balanced** | | | |
| bx2-2x8 | 2 | 8 GB | 2,300 |
| bx2-4x16 | 4 | 16 GB |  4,600 |
| bx2-8x32 | 8 | 32 GB |  9,200 |
| bx2-16x64 | 16 | 64 GB | 18,450 |
| bx2-32x128 | 32 | 128 GB | 36,900 |
| bx2-48x192 | 48 | 192 GB | 55,350 |
| **Memory optimized** | | | |
| mx2-2x16 | 2 | 16 GB | 2,570 |
| mx2-4x32 | 4 | 32 GB | 5,140 |
| mx2-8x64 | 8 | 64 GB | 10,280 |
| mx2-16x128 | 16 | 128 GB | 20,560 |
| mx2-32x256 | 32 | 256 GB | 41,130 |
| mx2-48x384 | 48 | 384 GB | 56,970 |
{: caption="Table 1. {{site.data.keyword.cloud_notm}} {{site.data.keyword.vsi_is_short}} certified for SAP NetWeaver" caption-side="top"}

| **Profile** | **vCPU** | **Memory (RAM GiB)** | **Network Performance Cap (Gbps)** | **Storage (GB)** | **SAPS** |
| --- | --- | --- | --- | --- | --- |
| **Ultra High Memory** | | | | | | 
| ux2d-8x224 | 8 | 224 | 8 | 1x240 | 8,623 |
| ux2d-16x448 | 16 | 448 | 16 | 1x480 | 17,246 |
| ux2d-36x1008 | 36 | 1008 | 36 | 1x1080 | 38,803 |
| ux2d-52x1456 | 52 | 1456 | 52 | 2x780 | 56,049 |
| ux2d-72x2016 | 72 | 2016 | 72 | 2x1080 | 77,606 |
| ux2d-104x2912 | 104 | 2912 | 80 | 2x1560 | 112,097 |
| ux2d-200x5600 | 200 | 5600 | 80 | 2x3000 | 215,570 |
{: caption="Table 2. {{site.data.keyword.cloud_notm}} {{site.data.keyword.vsi_is_short}} certified for SAP NetWeaver" caption-side="top"}

For more information, see [SAP Note 2927211 - SAP Applications on IBM Virtual Private Cloud: Supported DB/OS and IBM Gen 2 Virtual Servers](https://launchpad.support.sap.com/#/notes/2927211){: external}.


## Understanding Virtual Server profile names
{: #nw-iaas-intel-vs-vpc-names}

With {{site.data.keyword.vsi_is_full}}, the three profile families are: Balanced, Compute, and Memory.
- Balanced family profiles, provide a good mix of performance and scalability for more common workloads
- Compute family profiles, provide for workloads with intensive CPU demands, such as high web traffic workloads, production batch processing, and front-end web servers
- Memory family profiles, provide for memory intensive workloads, such as intensive database applications and in-memory analytics workloads.

For SAP NetWeaver, multiple profile families are used.

The Virtual Server profile names are contextual and sequential; this example uses an SAP HANA certified server:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| mx2-16x128 | m | Memory family (higher on the CPU to Memory ratio), 1:8 ratio|
| | x | Intel x86_64 CPU Architecture |
| | 2 | The generation for the underlying hardware |
| | - | _spacer_ |
| | 16 | 16 vCPU |
| | x | _spacer_ |
| | 128 | 128 GB RAM |
{: caption="Table 2. Profile naming for SAP NetWeaver" caption-side="top"}

For more information, see [Intel Virtual Server for VPC Infrastructure documentation](/docs/vpc?topic=vpc-profiles).


## Profiles available on Hourly Consumption Billing
{: #nw-iaas-intel-vs-vpc-hourly}

All {{site.data.keyword.cloud_notm}} {{site.data.keyword.vsi_is_short}} are available with Hourly Consumption Billing, which includes Suspend Discounts and Sustained Usage Discounts. With Suspend Discounts, storage charges occur only if the server is in Shutdown state. With Sustained Usage Discount, the more a server is used, the less the cost per hour.


## Storage specifications
{: #nw-iaas-intel-vs-vpc-storage-specs}

When the server profiles for SAP NetWeaver are initially provisioned, the servers all have a basic boot layout of the file system:

| File system | Volume | Storage type | IOPS/GB | GB | \# IOPS |
| --- | --- | --- | --- | --- | --- |
| `/` | `vda1` | Pre-configured boot volume | N/A | 100 GB | 3,000 |
| `/boot` | `vda2` | Pre-configured boot volume | N/A | 0.25 GB | 3,000 |
{: caption="Table 3. Storage configuration of the default virtual server deployment (boot volume)" caption-side="top"}

To fulfill the size and I/O requirements for SAP NetWeaver or SAP AnyDB, more [{{site.data.keyword.block_storage_is_full}}](/docs/vpc?topic=vpc-block-storage-about) volumes need to be added as data volumes to the virtual server configuration.

Block Storage Volumes for Virtual Servers can be created based on different **volume profiles** that provide different levels of IOPS per gigabyte (IOPS/GB). For more information, see [IOPS tiers](/docs/vpc?topic=vpc-block-storage-profiles#tiers) for details.

You must consider the total IOPS required for your installation and the performance characteristics of your database. One option is to colocate multiple directories into a single large volume with high IOPS, versus isolating directories into individual small volumes with an insufficient number of IOPS for the workload characteristics.

For an overview of all available storage profiles, see [VPC Block Storage Profiles](/docs/vpc?topic=vpc-block-storage-profiles).

For SAP NetWeaver and SAP AnyDB, a minimum of 5 IOPS/GB is recommended.

Samples of storage configurations that use Intel Virtual Server (Gen2) profiles are available under [Storage design considerations](/docs/sap?topic=sap-storage-design-considerations).
