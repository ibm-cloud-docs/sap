---
copyright:
  years: 2020, 2026
lastupdated: "2026-01-29"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, sapphire rapids, Skylake, Cascade lake, baremetal
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# SAP Application Server certified instances on Intel-powered bare metal servers in VPC Infrastructure
{: #nw-iaas-offerings-profiles-vpc-bm}

## Profile naming convention
{: #naming-convention}

SAP Application Server profile names follow a contextual and sequential naming convention. The following table illustrates an example of an SAP Application certified profile:


The first letter of the profile name indicates the profile family that is mentioned the profile list, where the ratio depends on the Intel generation, Sapphire Rapids (SPR) or Cascade Lake (CL):

| **First letter** | **Characteristics of the related profile family**                                                                      |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------- |
| c                | *Compute Optimized* family profiles provide more compute power, and they have more cores with less memory ratio SPR 1:2 or 1:2.67, CL 1:2 |
| b                | *Balanced* family profiles provide a good mix of performance and scalability for more common workloads, higher vCPU to memory ratio SPR 1:4 or 1:5.33, CL 1:4 |
| m                | *Memory Optimized* family, higher vCPU to memory ratio SPR 1:8 or 1:10.67, CL 1:8                                      |
| v                | *Very High Memory Optimized* family profiles cater to memory intensive workloads, such as demanding database applications and in-memory analytics workloads, and are especially designed for SAP HANA workloads, very high vCPU to memory ratio SPR 1:16, CL 1:27.43 |
| u                | *Ultra High Memory Optimized* family profiles cater to memory intensive workloads, such as demanding database applications and in-memory analytics workloads, and are especially designed for SAP HANA workloads, ultra high vCPU to memory ratio SPR 1:32, CL 1:27.43 |
{: caption="{{site.data.keyword.baremetal_short}} for VPC Profile families" caption-side="top"}


The virtual server profile names are contextual and sequential. The following table illustrates an example of an SAP Application certified profile:

| Profile name        | Naming convention component | Description                                                                                                         |
| ------------------- | --------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| mx3d-metal-128x1024 | m                           | *Memory Optimized* family                                                                                           |
|                     | x                           | *separator*                                                                                                         |
|                     | ? \n   2 \n   3             | The Intel generation for the underlying hardware \n   Cascade Lake \n   Sapphire Rapids                             |
|                     | d                           | the optional 'd' in the name indicates that the server is equipped with one or more internal SSD storage devices    |
|                     | metal                       | {{site.data.keyword.baremetal_short_sing}}                                                                          |
|                     | —                           | *separator*                                                                                                         |
|                     | 128                         | 128 Virtual CPU cores                                                                                               |
|                     | x                           | *separator*                                                                                                         |
|                     | 1024                        | 1024 GiB RAM                                                                                                        |
{: caption="Profile naming scheme for SAP application server" caption-side="top"}

For more information, see [x86-64 bare metal server profiles](/docs/vpc?topic=vpc-bare-metal-servers-profile#bare-metal-servers-profile-list).

For more information, see [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}.



## Profiles hosted on Intel Sapphire Rapids CPU
{: #nw-sr-profiles}


| **Profile name**    | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** | **SAPS**  | **aSAPS**    |
| --------------------| ------------- | ---------------- | ---------------- | --------- |------------- |
| cx3d-metal-48x128   | 24            | 48               | 128              | 62,029    | n/a          |
| cx3d-metal-64x128   | 32            | 64               | 128              | 63,950    | n/a          |
| bx3d-metal-48x256   | 24            | 48               | 256              | 93,670    | 18,400       |
| bx3d-metal-64x256   | 32            | 64               | 256              | 124,520   | 24,600       |
| bx3d-metal-192x1024 | 96            | 192              | 1,024            | 297,770   | 57,400       |
| mx3d-metal-16x128   | 8             | 16               | 128              | 30,030    | n/a          |
| mx3d-metal-48x512   | 24            | 48               | 512              | 97,830    | 18,700       |
| mx3d-metal-64x512   | 32            | 64               | 512              | 128,750   | 24,200       |
| mx3d-metal-96x1024  | 48            | 96               | 1,024            | 182,670   | 33,700       |
| mx3d-metal-128x1024 | 64            | 128              | 1,024            | 239,300   | 46,000       |
| vx3d-metal-16x256   | 8             | 16               | 256              | 35,520    | 6,900        |
| ux3d-metal-16x512   | 8             | 16               | 512              | 34,320    | 6,900        |
{: caption="Intel Sapphire Rapids servers certified for SAP application server" caption-side="bottom"}



## Profiles hosted on Intel Cascade Lake CPU
{: #nw-cl-profiles}

| **Profile name**    | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** | **SAPS** |
| --------------------| ------------- | ---------------- | ---------------- | --------- |
| cx2d-metal-96x192   | 48            | 96               | 192              | 101,070   |
| bx2d-metal-96x384   | 48            | 96               | 384              | 124,130   |
| mx2d-metal-96x768   | 48            | 96               | 768              | 127,620   |
| ux2d-metal-112x3072 | 56            | 112              | 3,072            | 140,730   |
| ux2d-metal-224x6144 | 112           | 224              | 6,144            | 294,730   |
{: caption="Intel Cascade Lake servers certified for SAP application server" caption-side="bottom"}


## Internal storage
{: #nw-iaas-intel-bm-vpc-int-storage}

Your bare metal server on VPC comes with a number of internal NVMEs, depending on its size. For SAP application server based deployment, you can use the NVMEs that are listed as block devices to the operating system. The NVMEs are under “/dev/nvmeXn1” (X from 0 to the number of NVMEs in total, minus 1). Use the NVMEs according to your requirements and needs. However, to increase failure resilience, you might have to install Linux Logical Volume Manager (LVM) to add RAID configuration, like RAID1 or RAID5. Since NVMEs are provisioned, performance considerations are mostly not an issue.

## Profiles available on hourly consumption billing
{: #nw-iaas-intel-vpc-bm-hourly}

All {{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC are available with hourly consumption billing, which includes suspend discounts and sustained usage discounts. With suspend discounts, storage charges occur only when the server is in shutdown state. With sustained usage discount, the more a server is used, the less the cost per hour.
