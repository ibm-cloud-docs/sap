---
copyright:
  years: 2020, 2026
lastupdated: "2026-01-31"
keywords: SAP-Certified HANA profiles, VPC, baremetal, log, data, shared, sapphire rapids, Skylake, Cascade lake, boot only, bare metal
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# SAP HANA certified instances on Intel-powered bare metal servers in VPC Infrastructure
{: #hana-iaas-offerings-profiles-vpc-intel-bm}


A SAP HANA certified server or profile on Intel-powered bare metal server in VPC Infrastructure defines attributes, such as physical CPU cores and RAM, which determine the size and performance capabilities of the server.

With {{site.data.keyword.bm_is_full}}, the profile families that are certified for SAP are: *Memory Optimized*, *Very High* and *Ultra High Memory Optimized*. All the Memory family profiles cater to memory intensive workloads, such as demanding database applications and in-memory analytics workloads, and are especially designed for SAP HANA workloads.

For more information, see chapter [x86-64 instance profiles](/docs/vpc?topic=vpc-profiles).


For SAP HANA deployments that use {{site.data.keyword.bm_is_full_notm}}, only single-node deployments are supported. Multi-node / scale-out is not currently supported.
{: important}

## Hardware specifications
{: #supported-hardware}

Following Intel system types are certified to run SAP Application Server or SAP NetWeaver workloads:



## Profile naming convention
{: #naming-convention}


The first letter of the profile name indicates the profile family that is mentioned the profile list, where the ratio depends on the Intel generation, Sapphire Rapids (SPR) or Cascade Lake (CL):

| **First letter** | **Characteristics of the related profile family**                                                                      |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------- |
| c                | *Compute Optimized* family profiles provide more compute power, and they have more cores with less memory ratio CL 1:2 |
| b                | *Balanced* family profiles provide a good mix of performance and scalability for more common workloads, higher vCPU to memory ratio SPR 1:4 or 1:5.33, CL 1:4 |
| m                | *Memory Optimized* family, higher vCPU to memory ratio SPR 1:8 or 1:10.67, CL 1:8                                      |
| v                | *Very High Memory Optimized* family profiles cater to memory intensive workloads, such as demanding database applications and in-memory analytics workloads, and are especially designed for SAP HANA workloads, very high vCPU to memory ratio SPR 1:16 |
| u                | *Ultra High Memory Optimized* family profiles cater to memory intensive workloads, such as demanding database applications and in-memory analytics workloads, and are especially designed for SAP HANA workloads, ultra high vCPU to memory ratio CL 1:27.43 |
{: caption="{{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC Profile Families" caption-side="top"}


The virtual server profile names are contextual and sequential. The following table illustrates an example of an SAP HANA certified server profile:

| Profile name        | Naming convention component | Description                                                                                                         |
| ------------------- | --------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| mx3d-metal-128x1024 | m                           | *Memory Optimized* family                                                                                           |
|                     | x                           | *separator*                                                                                                         |
|                     | ? \n   2 \n   3             | The Intel generation for the underlying hardware \n   Cascade Lake \n   Sapphire Rapids                             |
|                     | d                           | the optional 'd' in the name indicates that the server is equipped with one or more internal SSD storage devices    |
|                     | metal                       | *metal* in the name indicates that this is a {{site.data.keyword.baremetal_short_sing}}                             |
|                     | —                           | *separator*                                                                                                         |
|                     | 128                         | 128 vCPU                                                                                                            |
|                     | x                           | *separator*                                                                                                         |
|                     | 1024                        | 1024 GiB RAM                                                                                                        |
{: caption="Profile naming scheme for SAP HANA" caption-side="top"}

Refer to [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external} for up-to-date information on certified SAP HANA profiles.
{: note}


## Profiles hosted on Intel Sapphire Rapids CPU
{: #sr-profiles}


| **Profile name**    | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** |
| --------------------| ------------- | ---------------- | ---------------- |
| bx3d-metal-48x256   | 24            | 48               | 256              |
| bx3d-metal-64x256   | 32            | 64               | 256              |
| bx3d-metal-192x1024 | 96            | 192              | 1,024            |
| mx3d-metal-48x512   | 24            | 48               | 512              |
| mx3d-metal-64x512   | 32            | 64               | 512              |
| mx3d-metal-96x1024  | 48            | 96               | 1,024            |
| mx3d-metal-128x1024 | 64            | 128              | 1,024            |
| vx3d-metal-16x256   | 8             | 16               | 256              |
| ux3d-metal-16x512   | 8             | 16               | 512              |
{: class="simple-tab-table"}
{: tab-group="sr-profiles"}
{: caption="Intel Bare Metal Sapphire Rapids profiles for SAP HANA" caption-side="bottom"}
{: #sr-profiles-table}
{: tab-title="Certified Profiles"}

| **Profile name**    | **Workload type** | **SAPS** | **aSAPS** |
| --------------------| ----------------- | -------- | --------- |
| bx3d-metal-48x256   | OLTP/OLAP         | 93,670   | 18,400    |
| bx3d-metal-64x256   | OLTP/OLAP         | 124,520  | 24,600    |
| bx3d-metal-192x1024 | OLTP/OLAP         | 297,770  | 57,400    |
| mx3d-metal-48x512   | OLTP/OLAP         | 97,830   | 18,700    |
| mx3d-metal-64x512   | OLTP/OLAP         | 128,750  | 24,200    |
| mx3d-metal-96x1024  | OLTP/OLAP         | 182,670  | 33,700    |
| mx3d-metal-128x1024 | OLTP/OLAP         | 239,300  | 46,000    |
| vx3d-metal-16x256   | OLTP/OLAP         | 35,520   | 6,900     |
| ux3d-metal-16x512   | OLTP/OLAP         | 34,320   | 6,900     |
{: class="simple-tab-table"}
{: tab-group="sr-profiles"}
{: caption="Intel Bare Metal Sapphire Rapids profiles for SAP HANA" caption-side="bottom"}
{: #sr-sizing}
{: tab-title="Sizing"}

| **Profile name**    | **Storage Array**        |
| --------------------|--------------------------|
| bx3d-metal-48x256   | 1 x 480 GB, 4 x 7,680 GB |
| bx3d-metal-64x256   | 1 x 480 GB, 4 x 7,680 GB |
| bx3d-metal-192x1024 | 1 x 480 GB, 8 x 7,680 GB |
| mx3d-metal-48x512   | 1 x 480 GB, 4 x 7,680 GB |
| mx3d-metal-64x512   | 1 x 480 GB, 4 x 7,680 GB |
| mx3d-metal-96x1024  | 1 x 480 GB, 8 x 7,680 GB |
| mx3d-metal-128x1024 | 1 x 480 GB, 8 x 7,680 GB |
| vx3d-metal-16x256   | 1 x 480 GB, 4 x 7,680 GB |
| ux3d-metal-16x512   | 1 x 480 GB, 4 x 7,680 GB |
{: class="simple-tab-table"}
{: tab-group="sr-profiles"}
{: caption="Intel Bare Metal Sapphire Rapids profiles for SAP HANA" caption-side="bottom"}
{: #sr-storage}
{: tab-title="Storage"}


## Profiles hosted on Intel Cascade Lake CPU
{: #cl-profiles}


| **Profile name**    | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** |
| --------------------| ------------- | ---------------- | ---------------- |
| cx2d-metal-96x192   | 48            | 96               | 192              |
| bx2d-metal-96x384   | 48            | 96               | 384              |
| mx2d-metal-96x768   | 48            | 96               | 768              |
| ux2d-metal-112x3072 | 56            | 112              | 3,072            |
| ux2d-metal-224x6144 | 122           | 224              | 6,144            |
{: class="simple-tab-table"}
{: tab-group="cl-profiles"}
{: caption="Intel Bare Metal Cascade Lake profiles for SAP HANA" caption-side="bottom"}
{: #cl-profiles-table}
{: tab-title="Certified Profiles"}

| **Profile name**    | **Workload type**          | **SAPS** |
| --------------------| -------------------------- | -------- |
| cx2d-metal-96x192   | SAP Business One           | 101,070  |
| bx2d-metal-96x384   | OLTP/OLAP/SAP Business One | 124,130  |
| mx2d-metal-96x768   | OLTP/OLAP/SAP Business One | 127,620  |
| ux2d-metal-112x3072 | OLTP/OLAP                  | 140,730  |
| ux2d-metal-224x6144 | OLTP/OLAP                  | 294,730  |
{: class="simple-tab-table"}
{: tab-group="cl-profiles"}
{: caption="Intel Bare Metal Cascade Lake profiles for SAP HANA" caption-side="bottom"}
{: #cl-sizing}
{: tab-title="Sizing"}

| **Profile name**    | **Storage Array**          |
| --------------------|----------------------------|
| cx2d-metal-96x192   |  1 x 960 GB, 8 x 3,200 GB  |
| bx2d-metal-96x384   |  1 x 960 GB, 8 x 3,200 GB  |
| mx2d-metal-96x768   |  1 x 960 GB, 8 x 3,200 GB  |
| ux2d-metal-112x3072 |  1 x 960 GB, 8 x 3,200 GB  |
| ux2d-metal-224x6144 |  1 x 960 GB, 2 x 2,5600 GB |
{: class="simple-tab-table"}
{: tab-group="cl-profiles"}
{: caption="Intel Bare Metal Cascade Lake profiles for SAP HANA" caption-side="bottom"}
{: #cl-storage}
{: tab-title="Storage"}

## Profiles available on hourly consumption billing
{: #hourly-profiles}

All {{site.data.keyword.bm_is_short}} are available with hourly consumption billing, which includes suspend discounts and sustained usage discounts. With suspend discounts, storage charges occur only if the server is in shutdown state. With sustained usage discount, the more a server is used, the less the cost per hour.
