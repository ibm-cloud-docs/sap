---
copyright:
  years: 2020, 2026
lastupdated: "2026-01-29"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, sapphire rapids, Skylake, Cascade lake, VPC, VSI, certified profiles
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# SAP Application Server certified instances on Intel-powered virtual servers on VPC Infrastructure
{: #nw-iaas-offerings-profiles-vpc-vsi}


## Profile naming convention
{: #naming-convention}

SAP Application Server profile names follow a contextual and sequential naming convention. The following table illustrates an example of an SAP Application certified profile:


The first letter of the profile name indicates the profile family that is mentioned the profile list, where the ratio depends on the Intel generation, Sapphire Rapids (SPR) or Cascade Lake (CL):

| **First letter** | **Characteristics of the related profile family**                                                                                                   |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| c                | *Compute Optimized* family profiles provide more compute power, and they have more cores with less memory ratio SPR 1:2.5, CL 1:2                   |
| b                | *Balanced* family profiles provide a good mix of performance and scalability for more common workloads, higher vCPU to memory ratio SPR 1:5, CL 1:4 |
| m                | *Memory Optimized* family, higher vCPU to memory ratio SPR 1:10 , CL 1:8                                                                            |
| v                | *Very High Memory Optimized* family profiles cater to memory intensive workloads, such as demanding database applications and in-memory analytics workloads, and are especially designed for SAP HANA workloads, very high vCPU to memory ratio SPR 1:16, CL 1:14 |
| u                | *Ultra High Memory Optimized* family profiles cater to memory intensive workloads, such as demanding database applications and in-memory analytics workloads, and are especially designed for SAP HANA workloads, ultra high vCPU to memory SPR ratio n/a, CL 1:28 |
{: caption="Characteristics" caption-side="top"}


SAP NetWeaver profile names follow a contextual and sequential naming convention.
The following table illustrates an example of an SAP NetWeaver certified instance profile:


| Profile name | Naming convention component | Description                                                                                                         |
| ------------ | --------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| mx2-16x128   | m                           | *Memory Optimized* family                                                                                           |
|              | x                           | *separator*                                                                                                         |
|              | ? \n   2 \n   3             | The Intel generation for the underlying hardware \n   Cascade Lake \n   Sapphire Rapids                             |
|              | d                           | the optional 'd' in the name indicates that the server is equipped with one or more internal SSD storage devices (*)|
|              | —                           | *separator*                                                                                                         |
|              | 16                          | 16 vCPU                                                                                                             |
|              | x                           | *separator*                                                                                                         |
|              | 128                         | 128 GiB RAM                                                                                                         |
{: caption="Profile naming scheme" caption-side="top"}


For virtual server instances (VSI) using temporary local SSD or NVMe storage: you must not place any SAP workload related data on such instance storage, because data loss may occur in certain situations - see more information here: [About instance storage](/docs/vpc?topic=vpc-instance-storage).
{: note}

For more information, see [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}.


The following tables provide an overview of the SAP-certified profiles for {{site.data.keyword.vsi_is_short}}:


## Profiles hosted on Intel Sapphire Rapids CPUs
{: #nw-sr-profiles}

### cx3d profiles
{: #nw-sr-cx3d-profiles}

| **Profile name** | **Virtual CPUs** | **Memory (GiB)** | **SAPs**  | **aSAPS** |
| -----------------| ---------------- | ---------------- | --------- |---------  |
| cx3d-2x5         | 2                | 5                | 2,661     |           |
| cx3d-4x10        | 4                | 10               | 5,321     |           |
| cx3d-8x20        | 8                | 20               | 10,642    |           |
| cx3d-16x40       | 16               | 40               | 21,284    |           |
| cx3d-24x60       | 24               | 60               | 31,926    |           |
| cx3d-32x80       | 32               | 80               | 42,568    |           |
| cx3d-48x120      | 48               | 120              | 63,851    |           |
| cx3d-64x160      | 64               | 160              | 85,135    |           |
| cx3d-96x240      | 96               | 240              | 127,702   |           |
| cx3d-128x320     | 128              | 320              | 170,270   |           |
| cx3d-176x440     | 176              | 440              | 234,120   |           |
{: caption="Compute optimized profiles on Intel Sapphire Rapids" caption-side="bottom"}

### bx3 profiles
{: #nw-sr-bx3d-profiles}

| **Profile name** | **Virtual CPUs** | **Memory (GiB)** | **SAPs** | **aSAPS** |
|------------------|------------------|------------------|----------|-----------|
| bx3d-2x10        | 2                | 10               | 2,616    | 500       |
| bx3d-4x20        | 4                | 20               | 5,232    | 1,000     |
| bx3d-8x40        | 8                | 40               | 10,463   | 2,000     |
| bx3d-16x80       | 16               | 80               | 20,925   | 4,000     |
| bx3d-24x120      | 24               | 120              | 31,387   | 6,000     |
| bx3d-32x160      | 32               | 160              | 41,850   | 8,100     |
| bx3d-48x240      | 48               | 240              | 62,774   | 12,100    |
| bx3d-64x320      | 64               | 320              | 83,699   | 16,200    |
| bx3d-96x480      | 96               | 480              | 125,548  | 24,300    |
| bx3d-128x640     | 128              | 640              | 167,397  | 32,400    |
| bx3d-176x880     | 176              | 880              | 230,170  | 44,600    |
{: caption="Compute optimized profiles on Intel Sapphire Rapids" caption-side="bottom"}


### mx3d profiles
{: #nw-sr-mx3d-profiles}

| **Profile name** | **Virtual CPUs** | **Memory (GiB)** | **SAPs** | **aSAPS** |
|------------------|------------------|------------------|----------|-----------|
| mx3d-2x20        | 2                | 20               | 2,590    | 500       |
| mx3d-4x40        | 4                | 40               | 5,180    | 1,000     |
| mx3d-8x80        | 8                | 80               | 10,359   | 2,000     |
| mx3d-16x160      | 16               | 160              | 20,717   | 4,100     |
| mx3d-24x240      | 24               | 240              | 31,075   | 6,100     |
| mx3d-32x320      | 32               | 320              | 41,433   | 8,200     |
| mx3d-48x480      | 48               | 480              | 62,150   | 12,300    |
| mx3d-64x640      | 64               | 640              | 82,866   | 16,400    |
| mx3d-96x960      | 96               | 960              | 124,299  | 24,700    |
| mx3d-128x1280    | 128              | 1,280            | 165,731  | 32,900    |
| mx3d-176x1760    | 176              | 1,760            | 227,880  | 45,300    |
{: caption="Memory optimized profiles on Intel Sapphire Rapids" caption-side="bottom"}

### vx3d profiles
{: #nw-sr-vx3d-profiles}

| **Profile name** | **Virtual CPUs** | **Memory (GiB)** | **SAPs** | **aSAPS** |
|------------------|------------------|------------------|----------|-----------|
| vx3d-2x32        | 2                | 32               | 2,513    | 485       |
| vx3d-4x64        | 4                | 64               | 5,026    | 969       |
| vx3d-8x128       | 8                | 128              | 10,051   | 1,937     |
| vx3d-16x256      | 16               | 256              | 20,102   | 3,873     |
| vx3d-24x384      | 24               | 384              | 30,153   | 5,810     |
| vx3d-32x512      | 32               | 512              | 40,204   | 7,746     |
| vx3d-48x768      | 48               | 768              | 60,306   | 11,619    |
| vx3d-64x1024     | 64               | 1,024            | 80,408   | 15,491    |
| vx3d-88x1408     | 88               | 1,408            | 110,560  | 21,300    |
| vx3d-96x1536     | 96               | 1,536            | 120,611  | 23,237    |
| vx3d-128x2048    | 128              | 2,048            | 160,815  | 30,982    |
| vx3d-176x2816    | 176              | 2,816            | 221,120  | 42,600    |
{: caption="Very High Memory optimized profiles on Intel Sapphire Rapids" caption-side="bottom"}


## Profiles hosted on Intel Cascade Lake CPUs
{: #nw-cl-profiles}

### cx2 profiles
{: #nw-cl-cx2-profiles}

| **Profile name**             | **Virtual CPUs** | **Memory (GiB)** | **SAPs**  |
| -----------------------------| ---------------- | ---------------- | --------- |
| cx2-2x4 \n cx2d-2x4          | 2                | 4                | 2,238     |
| cx2-4x8 \n cx2d-4x8          | 4                | 8                | 4,475     |
| cx2-8x16 \n cx2d-8x16        | 8                | 16               | 8,950     |
| cx2-16x32 \n cx2d-16x32      | 16               | 32               | 17,900    |
| cx2-32x64 \n cx2d-32x64      | 32               | 64               | 35,800    |
| cx2-48x96 \n cx2d-48x96      | 48               | 96               | 53,700    |
| cx2-64x128 \n cx2d-64x128    | 64               | 128              | 71,600    |
| cx2-96x192 \n cx2d-96x192    | 96               | 192              | 107,400   |
| cx2-128x256 \n cx2d-128x256  | 128              | 256              | 143,200   |
{: caption="Compute optimized profiles on Intel Cascade Lake" caption-side="bottom"}

### bx2 profiles
{: #nw-cl-bx2-profiles}

| **Profile name**             | **Virtual CPUs** | **Memory (GiB)** | **SAPs** |
| -----------------------------| ---------------- | ---------------- | ---------|
| bx2-2x8 \n bx2d-2x8          | 2                | 8                | 2,306    |
| bx2-4x16 \n bx2d-4x16        | 4                | 16               | 4,613    |
| bx2-8x32 \n bx2d-8x32        | 8                | 32               | 9,225    |
| bx2-16x64 \n bx2d-16x64      | 16               | 64               | 18,450   |
| bx2-32x128 \n bx2d-32x128    | 32               | 128              | 36,900   |
| bx2-48x192 \n bx2d-48x192    | 48               | 192              | 55,350   |
| bx2-64x256 \n bx2d-64x256    | 64               | 256              | 81,685   |
| bx2-96x384 \n bx2d-96x384    | 96               | 384              | 122,528  |
| bx2-128x512 \n bx2d-128x512  | 128              | 512              | 163,370  |
{: caption="Balanced optimized profiles on Intel Cascade Lake" caption-side="bottom"}


### mx2 profiles
{: #nw-cl-mx2-profiles}

| **Profile name**              | **Virtual CPUs** | **Memory (GiB)** | **SAPs** |
| ----------------------------- | ---------------- | ---------------- | ---------|
| mx2-2x16 \n mx2d-2x16         | 2                | 16               | 2,571    |
| mx2-4x32 \n mx2d-4x32         | 4                | 32               | 5,141    |
| mx2-8x64 \n mx2d-8x64         | 8                | 64               | 10,283   |
| mx2-16x128 \n mx2d-16x128     | 16               | 128              | 20,565   |
| mx2-32x256 \n mx2d-32x256     | 32               | 256              | 41,130   |
| mx2-48x384 \n mx2d-48x384     | 48               | 384              | 56,970   |
| mx2-64x512 \n mx2d-64x512     | 64               | 512              | 81,015   |
| mx2-96x768 \n mx2d-96x768     | 96               | 768              | 121,523  |
| mx2-128x1024 \n mx2d-128x1024 | 128              | 1,024            | 162,030  |
{: caption="Memory optimized profiles on Intel Cascade Lake" caption-side="bottom"}

### vx2 profiles
{: #nw-cl-vx2-profiles}

| **Profile name** | **Virtual CPUs** | **Memory (GiB)** | **SAPs** |
| -----------------| ---------------- | ---------------- | ---------|
| vx2d-2x28        | 2                | 28               | 2,131    |
| vx2d-4x56        | 4                | 56               | 4,262    |
| vx2d-8x112       | 8                | 112              | 8,523    |
| vx2d-16x224      | 16               | 224              | 17,046   |
| vx2d-44x616      | 44               | 616              | 46,875   |
| vx2d-88x1232     | 88               | 1,232            | 93,750   |
| vx2d-144x2016    | 144              | 2,016            | 153,410  |
| vx2d-176x2464    | 176              | 2,464            | 187,500  |
{: caption="Very High Memory optimized profiles on Intel Cascade Lake" caption-side="bottom"}

### ux2 profiles
{: #nw-cl-ux2-profiles}

| **Profile name** | **Virtual CPUs** | **Memory (GiB)** | **SAPs** |
| -----------------| ---------------- | ---------------- | ---------|
| ux2d-2x56        | 2                | 56               | 2,156    |
| ux2d-4x112       | 4                | 112              | 4,312    |
| ux2d-8x224       | 8                | 224              | 8,623    |
| ux2d-16x448      | 16               | 448              | 17,246   |
| ux2d-36x1008     | 36               | 1,008            | 38,803   |
| ux2d-48x1344     | 48               | 1,344            | 51,737   |
| ux2d-72x2016     | 72               | 2,016            | 77,606   |
| ux2d-100x2800    | 100              | 2,800            | 107,785  |
| ux2d-200x5600    | 200              | 5,600            | 215,570  |
{: caption="Ultra High Memory optimized profiles on Intel Cascade Lake" caption-side="bottom"}


## Storage specifications
{: #storage-specs}

When the virtual server profiles for SAP application server are initially provisioned, the servers all have one pre-configured volume (vda) attached with the following basic layout:

| File system | Partition | Storage type               | size (GB) | Nr. of\nIOPS |
| ------------| --------- | -------------------------- | --------- | ------------ |
| `/`         | `vda1`    | Pre-configured boot volume | 100       | 3,000        |
| `/boot`     | `vda2`    | Pre-configured boot volume | 0.25      | 3,000        |
{: caption="Storage configuration of the default virtual server deployment (boot volume)" caption-side="top"}

- To fulfill the size and I/O requirements for SAP application server or SAP AnyDB, more [{{site.data.keyword.block_storage_is_full}}](/docs/vpc?topic=vpc-block-storage-about) volumes need to be added as data volumes to the virtual server configuration.

- Block Storage Volumes for Virtual Servers can be created based on different **volume profiles** that provide different levels of IOPS per gigabyte (IOPS/GB). For more information, see [IOPS tiers](/docs/vpc?topic=vpc-block-storage-profiles#tiers) for details.

- You must consider the total IOPS required for your installation and the performance characteristics of your database. One option is to colocate multiple directories into a single large volume with high IOPS, versus isolating directories into individual small volumes with an insufficient number of IOPS for the workload characteristics.

- For an overview of all available storage profiles, see [VPC Block Storage Profiles](/docs/vpc?topic=vpc-block-storage-profiles).

- For SAP application server and SAP AnyDB, a minimum of 5 IOPS/GB is recommended.

- Samples of storage configurations that use Intel Virtual Server (Gen2) profiles are available under [Storage design considerations](/docs/sap?topic=sap-storage-design-considerations).


## Profiles available on hourly consumption billing
{: #nw-iaas-intel-vs-vpc-hourly}

All {{site.data.keyword.cloud_notm}} {{site.data.keyword.vsi_is_short}} are available with Hourly Consumption Billing, which includes Suspend Discounts and Sustained Usage Discounts. With Suspend Discounts, storage charges occur only if the server is in Shutdown state. With Sustained Usage Discount, the more a server is used, the less the cost per hour.
