---

copyright:
  years: 2020, 2022
lastupdated: "2022-12-09"

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

# Bare Metal Server certified profiles on VPC Infrastructure for SAP NetWeaver
{: #nw-iaas-offerings-profiles-intel-bm-vpc}

## Profiles list
{: #nw-iaas-intel-bm-vpc-list}

The published names are subject to change.
{: note}

This table gives you an overview of the SAP-certified profiles with bare metal servers for VPC. The vCPUs in this list are CPU cores and their secondary threads. The term vCPU is kept for comparison with their virtual counterparts.

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
{: caption="Table 1. {{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC certified for SAP NetWeaver" caption-side="bottom"}

For more information, see [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211){: external}. 

## Understanding Bare Metal Server profile names
{: #nw-iaas-intel-bm-vpc-names}

With {{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC, the profile families that are certified for SAP are: *Compute Optimized*, *Balanced*, *Memory Optimized*, and *Ultra High Memory Optimized*.
- *Compute Optimized* family profiles provide more compute power, and they have more cores with less memory.
- *Balanced* family profiles provide a good mix of performance and scalability for more common workloads.
- *Memory Optimized* and *Ultra High Memory Optimized* family profiles cater to memory intensive workloads, such as demanding database applications and in-memory analytics workloads, and are especially designed for SAP HANA workloads.

For more information, see [x86-64 bare metal server profiles](/docs/vpc?topic=vpc-bare-metal-servers-profile).

The first letter of the profile name indicates the profile family:

| First letter | Characteristics of the related profile family |
| --- | --- |
| c | *Compute Optimized* family, vCPU to memory ratio 1:2 |
| b | *Balanced* family, vCPU to memory ratio 1:4 |
| m | *Memory Optimized* family, higher vCPU to memory ratio 1:8 |
| u | *Ultra High Memory Optimized* family, even higher CPU to memory ratio 1:27.43 |
{: caption="Table 2. {{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC Profile Families" caption-side="top"}

<br/>
The Bare Metal Server profile names are contextual and sequential. See the following example:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| mx2d-metal-96x768 | m | *Memory Optimized* family |
| | x | Intel x86_64 CPU Architecture |
| | 2 | The generation for the underlying hardware | 
| | d | the optional 'd' in the name indicates that the server is equipped with one or more SSD storage devices |
| | — | _spacer_ |
| | metal | *metal* in the name indicates that this is a bare metal server |
| | — | _spacer_ |
| | 96 | 96 vCPU |
| | x | _spacer_ |
| | 768 | 768 GiB RAM |
{: caption="Table 3. Profile naming for SAP NetWeaver" caption-side="top"}


## Profiles available on Hourly Consumption Billing
{: #nw-iaas-intel-bm-vpc-hourly}

All {{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC are available with Hourly Consumption Billing, which includes Suspend Discounts and Sustained Usage Discounts. With Suspend Discounts, storage charges occur only if the server is in Shutdown state. With Sustained Usage Discount, the more a server is used, the less the cost per hour.

## Storage specifications
{: #nw-iaas-intel-bm-vpc-storage-specs}

When the bare metal server profiles for SAP NetWeaver are initially provisioned, the servers all have one pre-configured disk (sda) attached with the following basic layout:

| File system | Partition | Storage type | Size |
| --- | --- | --- | --- |
| | `sda1` | Pre-configured BIOS volume | 1 MB |
| `/boot/efi` | `sda2` | Pre-configured boot volume | 100 MB |
| `/` | `sda3` | Pre-configured root volume | 9.9 GB |
{: caption="Table 4. Storage configuration of the default bare metal server deployment (boot volume)" caption-side="top"}


### Internal Storage
{: #nw-iaas-intel-bm-vpc-int-storage}

Your bare metal server on VPC comes with a number of internal NVMEs, depending on its size. For SAP NetWeaver based deployment, you can use the NVMEs that are listed as block devices to the operating system. The NVMEs are under “/dev/nvmeXn1” (X from 0 to the number of NVMEs in total, minus 1). Use the NVMEs according to your requirements and needs. However, to increase failure resilience, you might have to install Linux Logical Volume Manager (LVM) to add RAID configuration, like RAID1 or RAID5. Since NVMEs are provisioned, performance considerations are mostly not an issue.

### External Storage
{: #nw-iaas-intel-bm-vpc-ext-storage}

If more storage needs are to be added to your bare metal server on VPC, for example, for backup purposes, NFS-based `file shares` can be created and mounted. Learn more details in the corresponding chapter [Creating file shares and mount targets](/docs/vpc?topic=vpc-file-storage-create).

