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

# Bare Metal Server certified profiles on VPC Infrastructure for SAP NetWeaver
{: #nw-iaas-offerings-profiles-intel-bm-vpc}

## Profiles list
{: #nw-iaas-intel-bm-vpc-list}

The published names are subject to change.
{: note}


## Profiles list
{: #nw-iaas-intel-bm-vpc-list}

The following table gives you an overview of the SAP-certified profiles with bare metal servers for VPC:

| **Profile** | **vCPU** | **Memory (RAM GiB)** | **SAPS** |
| --- | --- | --- | --- |
| **Balanced** | | | |
| bx2d-metal-96x384 | 96 | 384 | 123,630 |
| bx2d-metal-192x768 | 192 | 768 | 255,000 |
| **Compute optimized** | | | |
| cx2d-metal-96x192 | 96 | 192 | 101,070|
| **Memory optimized** | | | |
| mx2d-metal-96x768 | 96 | 768 | 126,830 |
{: caption="Table 1. {{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC certified for SAP NetWeaver" caption-side="bottom"}


For more information, see [SAP Note ####### - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/#######){: external}. The vCPUs in the above list are CPU cores and their secondary threads. The term vCPU is kept for an easier comparison with their virtual counterparts.


## Understanding Bare Metal Server profile names
{: #nw-iaas-intel-bm-vpc-names}

With IBM Cloud Bare Metal Servers for VPC, the profile families that are certified for SAP are: *Balanced*, *Memory Optimized*, and *Compute optimized*.
- *Balanced* family profiles, provide a good mix of performance and scalability for more common workloads.
- *Compute optimized* family profiles, provide more compute power, thus the have more cores with less memory
- *Memory optimized* family profiles are to the best advantage for memory intensive workloads, such as intensive database applications and in-memory analytics workloads.

For more information, see [Bare metal server profiles](/docs/vpc?topic=vpc-bare-metal-servers-profile).

The first letter of the profile name indicates the profile family mentioned above:

| First letter | Characteristics of the related profile family |
| --- | --- |
| b | *Balanced* family, CPU to Memory 1:2 |
| c | *Compute Optimized*  family, CPU to Memory ratio 1:4 |
| m | *Memory Optimized* family, higher CPU to Memory ratio 1:8 |
{: caption="Table 2. {{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC Profile Families" caption-side="top"}

<br/>
The Bare Metual Server profile names are contextual and sequential. See the following example:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| mx2d-metal-96x768 | m | *Memory Optimized* family |
| | x | Intel x86_64 CPU Architecture |
| | 2 | The generation for the underlying hardware |
| | — | _spacer_ |
| | 96 | 96 vCPU |
| | x | _spacer_ |
| | 768 | 788 GiB RAM |
{: caption="Table 3. Profile naming for SAP NetWeaver" caption-side="top"}


## Profiles available on Hourly Consumption Billing
{: #nw-iaas-intel-bm-vpc-hourly}

All {{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC are available with Hourly Consumption Billing, which includes Suspend Discounts and Sustained Usage Discounts. With Suspend Discounts, storage charges occur only if the server is in Shutdown state. With Sustained Usage Discount, the more a server is used, the less the cost per hour.

## Storage specifications
{: #nw-iaas-intel-bm-vpc-storage-specs}

When the bare metal server profiles for SAP NetWeaver are initially provisioned, the servers all have one pre-configured disk (sda) attached with the following basic layout:

| File system | Partition | Storage type | size |
| --- | --- | --- | --- |
| | `sda1` | Pre-configured BIOS volume | 1 MB |
| `/boot/efi` | `sda2` | Pre-configured boot volume | 100 MB |
| `/` | `sda3` | Pre-configured root volume | 9.9 GB |
{: caption="Table 4. Storage configuration of the default bare metal server deployment (boot volume)" caption-side="top"}


