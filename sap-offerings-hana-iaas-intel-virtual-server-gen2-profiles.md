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
{:important: .important}

# Intel Virtual Server certified profiles for SAP HANA
{: #hana-iaas-offerings-profiles-intel-vs-vpc}

## Profiles list
{: #hana-iaas-intel-vs-vpc-list}

The published names are subject to change.
{: note}

The following list gives you an overview of the SAP-certified profiles with {{site.data.keyword.vsi_is_short}}:

| **Profile** | **vCPU** | **Memory (RAM GiB)** | **SAPS** | **SAP HANA Processing Type** |
| --- | --- | --- | --- | --- |
| **Memory optimized** | | | | |
| mx2-16x128 | 16 | 128 | 20,565 | OLTP |
| mx2-32x256 | 32 | 256 | 41,130 | OLTP |
| mx2-48x384 | 48 | 384 | 56,970 | OLTP |
| **Very High Memory** | | | | |
| vx2d-44x616 | 44 | 616 | 48,875 | OLAP |
| vx2d-88x1232 | 88 | 1,232 | 93,750 | OLAP |
| vx2d-72x2016 | 144 | 2,016 | 153,409 | OLAP |
| vx2d-176x2464 | 176 | 2,464 | 187,500 | OLAP |
{: caption="Table 1. {{site.data.keyword.cloud_notm}} {{site.data.keyword.vsi_is_short}} certified for SAP HANA" caption-side="bottom"}


For more information, see [SAP Note 2927211 - SAP Applications on IBM Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211){: external}.


For SAP HANA deployments using {{site.data.keyword.cloud_notm}} {{site.data.keyword.vsi_is_short}}, only single-node deployments are supported. This means multi-node / scale-out is not currently supported.
{: important}


## Understanding Virtual Server profile names
{: #hana-iaas-intel-vs-vpc-names}

With {{site.data.keyword.vsi_is_full}}, the profile families that are certified for SAP are: Balanced, Memory Optimized, Very High and Ultra High Memory.
- Balanced family profiles, provide a good mix of performance and scalability for more common workloads
- All the Memory family profiles, provide for memory intensive workloads, such as intensive database applications and in-memory analytics workloads, especially designed for SAP HANA workloads

For SAP HANA, only the memory profile families are used. The Virtual Server profile names are contextual and sequential.

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| mx2-16x128 | b | *Balanced* family, CPU to Memory ratio 1:4|
| | m | *Memory Optimized* family, higher CPU to Memory ratio 1:8|
| | v | *Very High Memory Optimized*  family, very high CPU to Memory ratio 1:14|
| | u | *Ultra High Memory Optimized* family, ultra high CPU to Memory ratio 1:28|
| | x | Intel x86_64 CPU Architecture |
| | 2 | The generation for the underlying hardware |
| | — | _spacer_ |
| | 16 | 16 vCPU |
| | x | _spacer_ |
| | 128 | 128 GiB RAM |
{: caption="Table 2. Profile naming for SAP HANA" caption-side="top"}

For more information, see [Intel Virtual Server for VPC Infrastructure documentation](/docs/vpc?topic=vpc-profiles).


## Profiles available on Hourly Consumption Billing
{: #hana-iaas-intel-vs-vpc-hourly}

All {{site.data.keyword.cloud_notm}} {{site.data.keyword.vsi_is_short}} are available with Hourly Consumption Billing, which includes Suspend Discounts and Sustained Usage Discounts. With Suspend Discounts, storage charges occur only if the server is in Shutdown state. With Sustained Usage Discount, the more a server is used, the less the cost per hour.

## Storage specifications
{: #hana-iaas-intel-vs-vpc-storage-specs}

When the virtual server profiles for SAP HANA are initially provisioned, the servers all have a basic boot layout of the file system:

| File system | Volume | Storage type | IOPS/GB | GB | \# IOPS |
| --- | --- | --- | --- | --- | --- |
| `/` | `vda1` | Pre-configured boot volume | N/A | 100 GB | 3,000 |
| `/boot` | `vda2` | Pre-configured boot volume | N/A | 0.25 GB | 3,000 |
{: caption="Table 3. Storage configuration of the default virtual server deployment (boot volume)" caption-side="top"}

To fulfill the size and I/O requirements for SAP HANA, more [{{site.data.keyword.block_storage_is_full}}](/docs/vpc?topic=vpc-block-storage-about) volumes need to be added as data volumes to the virtual server configuration.

Block Storage Volumes for Virtual Servers can be created based on different **volume profiles** that provide different levels of IOPS per gigabyte (IOPS/GB). For more information, see [IOPS tiers](/docs/vpc?topic=vpc-block-storage-profiles#tiers).

You must consider the total IOPS required for your installation and the performance characteristics of your database. One option is to colocate multiple directories into a single large volume with high IOPS, versus isolating directories into individual small volumes with an insufficient number of IOPS for the workload characteristics.

For an overview of all available storage profiles, see [VPC Block Storage Profiles](/docs/vpc?topic=vpc-block-storage-profiles).

To fulfill the KPIs defined by SAP HANA, each profile needs different storage volumes. The following sections list the storage needs for different profiles. **These are mandatory storage configurations, not sample storage configurations**.


### mx2-16x128 and mx2-32x256 profiles
{: #hana-iaas-intel-vs-vpc-mx2-16x128-32x256}

For Virtual Servers created based on the **mx2-16x128** and **mx2-32x256** profiles the storage volumes that are needed are:
- 3x 500 GB volumes; three 500 GB block storage volumes are required, with a custom volume profile that supports up to 10,000 **Max IOPS** attached to the Virtual Server
- _Optional:_ 1x 2,000 GB volume; one 2,000 GB block storage volume, with a lower 4,000 IOPS (medium performance) attached to the Virtual Server for backups

#### Disk mount points and volumes
{: #hana-iaas-intel-vs-vpc-mx2-16x128-32x256-logical}

After the three data volumes are attached, three new virtual disks appear in the Virtual Server. For the example in this table, those disks are `vdd`, `vde`, and `vdf`.

| Volume Group | Volume | Storage type | IOPS/GB | GB | \# IOPS |
| --- | --- | --- | --- | --- | --- |
| `hana_vg` | `vdd` | Data volume | 20IOPS/GB | 500 GB | 10,000 |
| `hana_vg` | `vde` | Data volume | 20IOPS/GB | 500 GB | 10,000 |
| `hana_vg` | `vdf` | Data volume | 20IOPS/GB | 500 GB | 10,000 |
{: caption="Table 4. Storage for mx2-16x128 and mx2-32x256 profile based VSIs" caption-side="top"}


### mx2-48x384 profile
{: #hana-iaas-intel-vs-vpc-mx2-48x384}

For Virtual Servers created based on the **mx2-48x384** profile the storage volumes that are needed are:
- 3x 500 GB volumes; three 500 GB block storage volumes are required, with a custom volume profile that supports up to 10,000 **Max IOPS** attached to the Virtual Server
- 4x 100 GB volumes; four 100 GB block storage volumes are required, with a custom volume profile that supports up to 6,000 **Max IOPS** attached to the Virtual Server
- _Optional:_ 1x 2,000 GB volume; one 2,000 GB block storage volume, with a lower 4,000 IOPS (medium performance) attached to the Virtual Server for backups

#### Disk mount points and volumes
{: #hana-iaas-intel-vs-vpc-mx2-48x384-logical}

After the seven data volumes are attached, seven new virtual disks appear in the Virtual Server. In the example in this table, those disks are `vdd`, `vde`, `vdf`, `vdg`, `vdh`, `vdi`, and `vdj`.

| Volume Group | Volume | Storage type | IOPS/GB | GB | \# IOPS |
| --- | --- | --- | --- | --- | --- |
| `hana_log_vg` | `vdd` | Data volume | 60IOPS/GB | 100 GB | 6,000 |
| `hana_log_vg` | `vde` | Data volume | 60IOPS/GB | 100 GB | 6,000 |
| `hana_log_vg` | `vdf` | Data volume | 60IOPS/GB | 100 GB | 6,000 |
| `hana_log_vg` | `vdg` | Data volume | 60IOPS/GB | 100 GB | 6,000 |
| `hana_vg` | `vdh` | Data volume | 20IOPS/GB | 500 GB | 10,000 |
| `hana_vg` | `vdi` | Data volume | 20IOPS/GB | 500 GB | 10,000 |
| `hana_vg` | `vdj` | Data volume | 20IOPS/GB | 500 GB | 10,000 |
{: caption="Table 5. Storage for mx2-48x384 profile based virtual servers" caption-side="top"}


### vx2d* profiles
{: #hana-iaas-intel-vs-vpc-vx2d}

[SAP's recommended file system layout](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.latest/en-US/4c24d332a37b4a3caad3e634f9900a45.html){: external} must be available for SAP HANA deployment. The following table shows the required volumes and related volume groups, if necessary, and their characteristics:

| Profile | File system | Volume Group | Volume | Size (GB) |
| --- | --- | --- | --- | --- |
| `vx2d-44x616` | `/hana/shared` |  | `vdd` | 616 |
| | `/hana/data` | | `vde` | 1,848 |
| | `/hana/log` | `hana_log_vg` | `vdf` | 192 |
| |  |  | `vdg` | 192 |
| |  |  | `vdh` | 192 |
| --- | --- | --- | --- | --- |
| `vx2d-88x1232` | `/hana/shared` |  | `vdd` | 1,250 |
| | `/hana/data` | | `vde` | 2,500 |
| | `/hana/log` | `hana_log_vg` | `vdf` | 192 |
| | | | `vdg` | 192 |
| | | | `vdh` | 192 |
| --- | --- | --- | --- | --- |
| `vx2d-144x2016` | `/hana/shared` |  | `vdd` | 2,048 |
| | `/hana/data` | `hana_data_vg` | `vde` | 1,024 |
| | | | `vdf` | 1,024 |
| | | | `vdg` | 1,024 |
| | | | `vdh` | 1,024 |
| | `/hana/log` | `hana_log_vg` | `vdi` | 192 |
| | | | `vdj` | 192 |
| | | | `vdk` | 192 |
| --- | --- | --- | --- | --- |
| `vx2d-176x2464` | `/hana/shared` |  | `vdd` | 2,500 |
| | `/hana/data` | `hana_data_vg` | `vde` | 1,280 |
| | | | `vdf` | 1,280 |
| | | | `vdg` | 1,280 |
| | | | `vdh` | 1,280 |
| | `/hana/log` | `hana_log_vg` | `vdi` | 192 |
| | | | `vdj` | 192 |
| | | | `vdk` | 192 |
{: caption="Table 5. Storage for mx2-48x384 profile based virtual servers" caption-side="top"}

All block storage volumes must be ordered with the predefined profile of 10 IOPS/GB (high performance). The volume names may differ - here we assume that the naming follows the sequence of ordering the storage, i.e. 1st order -> `vdd`, 2nd order -> `vde`, etc
{: note}

For all profiles optional: one appropriately sized  block storage volume or several equally sized volumes gathered to a volume group, with the predefined profile of 5 IOPS/GB (medium performance) attached to the Virtual Server for backups.


