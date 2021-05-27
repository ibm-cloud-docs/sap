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

| **Profile** | **vCPU** | **Memory (RAM GB)** | **SAPS** | **SAP HANA Processing Type** |
| --- | --- | --- | --- | --- |
| **Memory optimized** | | | | |
| mx2-16x128 | 16 | 128 GB | 20,560 | OLTP |
| mx2-32x256 | 32 | 256 GB | 41,130 | OLTP |
| mx2-48x384 | 48 | 384 GB | 56,970 | OLTP |
{: caption="Table 1. {{site.data.keyword.cloud_notm}} {{site.data.keyword.vsi_is_short}} certified for SAP HANA" caption-side="top"}

For more information, see [SAP Note 2927211 - SAP Applications on IBM Virtual Private Cloud: Supported DB/OS and IBM Gen 2 Virtual Servers](https://launchpad.support.sap.com/#/notes/2927211){: external}.


For SAP HANA deployments using {{site.data.keyword.cloud_notm}} {{site.data.keyword.vsi_is_short}}, only single-node deployments are supported. This means multi-node / scale-out is not currently supported.
{: important}


## Understanding Virtual Server profile names
{: #hana-iaas-intel-vs-vpc-names}

With {{site.data.keyword.vsi_is_full}}, the three profile families are: Balanced, Compute, and Memory.
- Balanced family profiles, provide a good mix of performance and scalability for more common workloads
- Compute family profiles, provide for workloads with intensive CPU demands, such as high web traffic workloads, production batch processing, and front-end web servers
- Memory family profiles, provide for memory intensive workloads, such as intensive database applications and in-memory analytics workloads.

For SAP HANA, only the Memory family is used.

The Virtual Server profile names are contextual and sequential; this example uses an SAP HANA certified server:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| mx2-16x128 | m | Memory family (higher on the CPU to Memory ratio), 1:8 ratio|
| | x | Intel x86_64 CPU Architecture |
| | 2 | Generation 2 |
| | - | _spacer_ |
| | 16 | 16 vCPU |
| | x | _spacer_ |
| | 128 | 128 GB RAM |
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
