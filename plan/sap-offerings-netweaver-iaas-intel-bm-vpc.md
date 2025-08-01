---
copyright:
  years: 2020, 2025
lastupdated: "2025-07-29"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Bare Metal Server certified profiles on VPC Infrastructure for SAP application server
{: #nw-iaas-offerings-profiles-intel-bm-vpc}

## Profiles list
{: #nw-iaas-intel-bm-vpc-list}

These tables give you an overview of the SAP-certified bare metal profiles for VPC that represent dedicated servers that provide physical cores. vCPU measurements are used in profile naming only. vCPU to physical cores are a 2:1 ratio (e.g 96 vCPU = 48 physical cores). The term vCPU is kept for comparison with their virtual counterparts.

### Profiles hosted on Intel Cascade Lake CPU
{: #nw-iaas-intel-bm-cl-vpc-list}


| **Profile** | **vCPU** | **Memory (RAM GiB)** | **SAPS** |
| --- | --- | --- | --- |
| **Compute Optimized** | | | |
| cx2d-metal-96x192 | 96 | 192 | 101,070|
| **Balanced** | | | |
| bx2d-metal-96x384 | 96 | 384 | 124,130 |
| **Memory Optimized** | | | |
| mx2d-metal-96x768 | 96 | 768 | 127,620 |
| **Ultra High Memory Optimized** | | | |
| ux2d-metal-112x3072 | 112 | 3,072 | 140,730 |
| ux2d-metal-224x6144 | 224 | 6,144 | 294,730 |
{: caption="{{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC certified for SAP application server - Intel Cascade Lake CPU" caption-side="bottom"}

### Profiles hosted on Intel Sapphire Rapids CPU
{: #nw-iaas-intel-bm-sr-vpc-list}

| **Profile** | **vCPU** | **Memory (RAM GiB)** | **SAPS** | **aSAPS⁽¹⁾** |
| --- | --- | --- | --- | --- |
| **Compute Optimized** | | | | |
| cx3d-metal-48x128 | 48 | 128 | 62,029| n/a |
| cx3d-metal-64x128 | 64 | 128 | 63,950| n/a |
| **Balanced** | | | | |
| bx3d-metal-48x256 | 48 | 256 | 93,670 | 18,400 |
| bx3d-metal-64x256 | 64 | 256 | 124,520 | 24,600 |
| bx3d-metal-192x1024 | 192 | 1,024 | 297,770 | 57,400 |
| **Memory Optimized** | | | | |
| mx3d-metal-16x128 | 16 | 128 | 30,030 | n/a |
| mx3d-metal-48x512 | 48 | 512 | 97,830 | 18,700 |
| mx3d-metal-64x512 | 64 | 512 | 128,750 | 24,200 |
| mx3d-metal-96x1024 | 96 | 1,024 | 182,670 | 33,700 |
| mx3d-metal-128x1024 | 128 | 1,024 | 239,300 | 46,000 |
| **Very High Memory Optimized** | | | | |
| vx3d-metal-16x256 | 16 | 256 | 35,520 | 6,900 |
| **Ultra High Memory Optimized** | | | | |
| ux3d-metal-16x512 | 16 | 512 | 34,320 | 6,900 |
{: caption="{{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC certified for SAP application server - Intel Sapphire Rapids CPU" caption-side="bottom"}

⁽¹⁾: aSAPS is the metric that is derived from the [SAP quote-to-cash (Q2C) Benchmark](https://www.sap.com/about/benchmark/appbm/q2c.html){: external}.

For more information, see [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment]    (https://me.sap.com/notes/2927211){: external}.

## Understanding Bare Metal Server profile names
{: #nw-iaas-intel-bm-vpc-names}

With {{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC, the profile families that are certified for SAP are: *Compute Optimized*, *Balanced*, *Memory Optimized*, and *Ultra High Memory Optimized*.
- *Compute Optimized* family profiles provide more compute power, and they have more cores with less memory.
- *Balanced* family profiles provide a good mix of performance and scalability for more common workloads.
- *Memory Optimized*, *Very High Memory Optimized*, and *Ultra High Memory Optimized* family profiles cater to memory intensive workloads, such as demanding database applications and in-memory analytics workloads, and are especially designed for SAP HANA workloads.

For more information, see [x86-64 bare metal server profiles](/docs/vpc?topic=vpc-bare-metal-servers-profile).

The first letter of the profile name indicates the profile family mentioned above. One of the key instance attributes is the ratio of core (*number of vCPUs*) to RAM (*amount of GiB*):

| **First letter** | **Characteristics of the related profile family** | **Ratio Cascade Lake** | **Ratio Sapphire Rapids** |
| --- | --- | --- | --- |
| c | *Compute Optimized* family | 1:2 | 1:2 or 1:2.67 |
| b | *Balanced* family | 1:4 | 1:4 or 1:5.33 |
| m | *Memory Optimized* family| 1:8 | 1:8 or 1:10.67 |
| v | *Very High Memory Optimized* family | 1:27.43 | 1:16 |
| u | *Ultra High Memory Optimized* family | 1:27.43 | 1:32 |
{: caption="{{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC Profile Families" caption-side="top"}


The Bare Metal Server profile names are contextual and sequential. See the following example:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| mx2d-metal-96x768 | m | *Memory Optimized* family |
| | x | Intel x86_64 CPU architecture |
| | ? \n   2 \n   3 | The Intel generation for the underlying hardware \n   Cascade Lake \n   Sapphire Rapids |
| | d | the optional 'd' in the name indicates that the server is equipped with one or more additional NVMe SSD storage devices |
| | — | *spacer* |
| | metal | *metal* in the name indicates that this is a bare metal server |
| | — | *spacer* |
| | 96 | 96 vCPU |
| | x | *spacer* |
| | 768 | 768 GiB RAM |
{: caption="Profile naming for SAP application server" caption-side="top"}


## Profiles available on Hourly Consumption Billing
{: #nw-iaas-intel-bm-vpc-hourly}

All {{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC are available with Hourly Consumption Billing, which includes Suspend Discounts and Sustained Usage Discounts. With Suspend Discounts, storage charges occur only if the server is in Shutdown state. With Sustained Usage Discount, the more a server is used, the less the cost per hour.

## Storage specifications
{: #nw-iaas-intel-bm-vpc-storage-specs}

When the bare metal server profiles for SAP application server are initially provisioned, the servers have one or more pre-configured disks attached.

1. Servers that are hosted on Cascade Lake CPU have one disk (sda) with the following basic layout:

    | File system | Partition | Storage type | Size |
    | --- | --- | --- | --- |
    | | `sda1` | Pre-configured BIOS volume | 1 MB |
    | `/boot/efi` | `sda2` | Pre-configured boot volume | 100 MB |
    | `/` | `sda3` | Pre-configured root volume | 9.9 GB |
    {: caption="Storage configuration of the default bare metal server deployment (boot volume)" caption-side="top"}

1. Servers that are hosted on Sapphire Rapids CPU

    See the appropriate profile in [x86-64 bare metal server profiles](/docs/vpc?topic=vpc-bare-metal-servers-profile#bare-metal-servers-profile-list).


### Internal Storage
{: #nw-iaas-intel-bm-vpc-int-storage}

Your bare metal server on VPC comes with a number of internal NVMEs, depending on its size. For SAP application server based deployment, you can use the NVMEs that are listed as block devices to the operating system. The NVMEs are under “/dev/nvmeXn1” (X from 0 to the number of NVMEs in total, minus 1). Use the NVMEs according to your requirements and needs. However, to increase failure resilience, you might have to install Linux Logical Volume Manager (LVM) to add RAID configuration, like RAID1 or RAID5. Since NVMEs are provisioned, performance considerations are mostly not an issue.

### External Storage
{: #nw-iaas-intel-bm-vpc-ext-storage}

If more storage needs are to be added to your bare metal server on VPC, for example, for backup purposes, NFS-based `file shares` can be created and mounted. Learn more details in the corresponding chapter [Creating file shares and mount targets](/docs/vpc?topic=vpc-file-storage-create).
