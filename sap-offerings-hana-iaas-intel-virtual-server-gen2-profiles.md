---

copyright:
  years: 2020, 2021, 2022
lastupdated: "2022-01-20"

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

| **Profile** | **vCPU** | **Memory (RAM GiB)** | **SAPS** | **SAP HANA\nProcessing Type** |
| --- | --- | --- | --- | --- |
| **Memory optimized** | | | | |
| mx2-16x128 | 16 | 128 | 20,565 | OLTP |
| mx2-32x256 | 32 | 256 | 41,130 | OLTP |
| mx2-48x384 | 48 | 384 | 56,970 | OLTP |
| **Very High Memory** | | | | |
| vx2d-16x224 | 16 | 224 | 17,046 | OLTP |
| vx2d-44x616 | 44 | 616 | 46,875 | OLAP/OLTP |
| vx2d-88x1232 | 88 | 1,232 | 93,750 | OLAP/OLTP |
| vx2d-144x2016 | 144 | 2,016 | 153,410 | OLAP/OLTP |
| vx2d-176x2464 | 176 | 2,464 | 187,500 | OLAP/OLTP |
| **Ultra High Memory** | | | | |
| ux2d-8x224 | 8 | 224 | 8,623 | OLTP |
| ux2d-16x448 | 16 | 448 | 17,246 | OLTP |
| ux2d-36x1008 | 36 | 1,008 | 38,803 | OLTP |
| ux2d-48x1344 | 48 | 1,344 | 51,737 | OLTP |
| ux2d-72x2016 | 72 | 2,016 | 77,606 | OLTP |
| ux2d-100x2800 | 100 | 2,800 | 107,785 | OLTP |
| ux2d-200x5600 | 200 | 5,600 | 215,570 | OLTP |
{: caption="Table 1. {{site.data.keyword.cloud_notm}} {{site.data.keyword.vsi_is_short}} certified for SAP HANA" caption-side="bottom"}


For more information, see [SAP Note 2927211 - SAP Applications on IBM Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211){: external}.


For SAP HANA deployments using {{site.data.keyword.cloud_notm}} {{site.data.keyword.vsi_is_short}}, only single-node deployments are supported. This means multi-node / scale-out is not currently supported.
{: important}


## Understanding Virtual Server profile names
{: #hana-iaas-intel-vs-vpc-names}

With {{site.data.keyword.vsi_is_full}}, the profile families that are certified for SAP are: Balanced, Memory Optimized, Very High and Ultra High Memory.
- Balanced family profiles, provide a good mix of performance and scalability for more common workloads.
- All the Memory family profiles provide for memory intensive workloads, such as intensive database applications and in-memory analytics workloads, and are especially designed for SAP HANA workloads.

For more information, see [Intel Virtual Server for VPC Infrastructure documentation](/docs/vpc?topic=vpc-profiles).


The first letter of the profile name indicates the profile family mentioned above:

| First letter | Characteristics of the related profile family |
| --- | --- |
| b | *Balanced* family, CPU to Memory ratio 1:4 |
| m | *Memory Optimized* family, higher CPU to Memory ratio 1:8 |
| v | *Very High Memory Optimized*  family, very high CPU to Memory ratio 1:14 |
| u | *Ultra High Memory Optimized* family, ultra high CPU to Memory ratio 1:28 |
{: caption="Table 2. {{site.data.keyword.vsi_is_full}} Profile Families" caption-side="top"}


For SAP HANA, only the memory profile families are used. The Virtual Server profile names are contextual and sequential. See here one example:

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
{: #hana-iaas-intel-vs-vpc-hourly}

All {{site.data.keyword.cloud_notm}} {{site.data.keyword.vsi_is_short}} are available with Hourly Consumption Billing, which includes Suspend Discounts and Sustained Usage Discounts. With Suspend Discounts, storage charges occur only if the server is in Shutdown state. With Sustained Usage Discount, the more a server is used, the less the cost per hour.

## Storage specifications
{: #hana-iaas-intel-vs-vpc-storage-specs}

When the virtual server profiles for SAP HANA are initially provisioned, the servers all have one pre-configured volume (vda) attached with the following basic layout:

| File system | Partition | Storage type | IOPS/GB | GB | Nr. of\nIOPS |
| --- | --- | --- | --- | --- | --- |
| `/` | `vda1` | Pre-configured boot volume | N/A | 100 GB | 3,000 |
| `/boot` | `vda2` | Pre-configured boot volume | N/A | 0.25 GB | 3,000 |
{: caption="Table 4. Storage configuration of the default virtual server deployment (boot volume)" caption-side="top"}

### {{site.data.keyword.block_storage_is_full}}
{: #hana-iaas-intel-vs-vpc-storage-specs-general}

[{{site.data.keyword.block_storage_is_full}}](/docs/vpc?topic=vpc-block-storage-about) volumes for Virtual Servers can be created based on different **volume profiles** that provide different levels of IOPS per gigabyte (IOPS/GB). For more information, see [IOPS tiers](/docs/vpc?topic=vpc-block-storage-profiles#tiers).

You must consider the total IOPS required for your installation and the performance characteristics of your database. One option is to colocate multiple directories into a single large volume with high IOPS, versus isolating directories into individual small volumes with an insufficient number of IOPS for the workload characteristics.

For an overview of all available storage profiles, see [VPC Block Storage Profiles](/docs/vpc?topic=vpc-block-storage-profiles).

### Storage for SAP HANA
{: #hana-iaas-intel-vs-vpc-storage-specs-sap-hana}

To fulfill the KPIs defined by SAP HANA, each profile needs different storage volumes. The following sections list the storage needs for different profiles. **These are mandatory storage configurations, not sample storage configurations**.

Below there are the tested and certified storage layouts that comply to **SAP HANA Tailored Data Center Integration** (TDI) Phase 5. Customers who want to choose different layouts are advised to follow the [SAP HANA TDI Overview](https://www.sap.com/documents/2017/09/e6519450-d47c-0010-82c7-eda71af511fa.html){: external} and [SAP HANA TDI FAQ](https://www.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html){: external} when ordering different storage sizes and types.

For all following layouts please consider that the volume names may differ - we assume that the naming follows the sequence of ordering the storage, i.e. 1st order -> `vdd`, 2nd order -> `vde`, etc. **All block storage volumes** must be ordered with the predefined profile of **10 IOPS/GB** (high performance). One exception may be /hana/shared partition where 5 IOPS/GB (medium performance) are sufficient - but ONLY IF you have assigned a dedicated volume for this partition. For all profiles optional: one appropriately sized  block storage volume or several equally sized volumes gathered to a volume group, with the predefined profile of 5 IOPS/GB (medium performance) attached to the Virtual Server for backups.
{: note}

[SAP's recommended file system layout](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.latest/en-US/4c24d332a37b4a3caad3e634f9900a45.html){: external} must be available for SAP HANA deployment. 

### mx2-16x128 and mx2-32x256 profiles
{: #hana-iaas-intel-vs-vpc-mx2-small}

The following table shows the required physical volumes, related volume groups, logical volumes, and their characteristics. See the [step by step instructions for setting up the assets here](https://cloud.ibm.com/docs/sap?topic=sap-storage-design-considerations#hana-iaas-mx2-16x128-32x256-configure){: external}.

| Profile | File\nsystem | Logical\nVolume | LV Size\n(GB) | Volume Group | Physical\nVolume | PV Size\n(GB) |
| --- | --- | --- | --- | --- | --- | --- |
| `mx2-16x128`\nand\n`mx2-32x256`  | `/hana/shared` | `hana_shared_lv` | 256 | `hana_vg` | `vdd` | 500 |
| | `/hana/data` | `hana_data_lv` | 256 | | `vde` | 500 |
| | `/hana/log` | `hana_log_lv` | 988 | | `vdf` | 500 |
{: caption="Table 5. Storage for mx2-16x128 and mx2-32x256 profiles based virtual servers" caption-side="top"}

### mx2-48x384 profile
{: #hana-iaas-intel-vs-vpc-mx2-48x348}

The following table shows the required physical volumes, related volume groups, logical volumes, and their characteristics. See the [step by step instructions for setting up the assets here](https://cloud.ibm.com/docs/sap?topic=sap-storage-design-considerations#hana-iaas-mx2-48x384-configure){: external}. Mind the different volume names.

| Profile | File\nsystem | Logical\nVolume | LV Size\n(GB) | Volume Group | Physical\nVolume | PV Size\n(GB) |
| --- | --- | --- | --- | --- | --- | --- |
| `mx2-48x384` | `/hana/shared` | `hana_shared_lv` | 384 | `hana_vg` | `vdd` | 500 |
| | `/hana/data` | `hana_data_lv` | 616 | | `vde` | 500 |
| | | | | | `vdf` | 500 |
| | `/hana/log` | `hana_log_lv` | 400 | `hana_log_vg` | `vdg` | 100 |
| | | | | | `vdh` | 100 |
| | | | | | `vdi` | 100 |
| | | | | | `vdj` | 100 |
{: caption="Table 6. Storage for mx2-48x384 profile based virtual servers" caption-side="top"}


### vx2d-* profiles
{: #hana-iaas-intel-vs-vpc-vx2d}

The following table shows the required volumes and related volume groups, if necessary, and their characteristics:

| Profile | File system | Volume Group | Volume | Size (GB) |
| --- | --- | --- | --- | --- |
| `vx2d-16x224` | `/hana/shared` |  | `vdd` | 250 |
| | `/hana/data` | | `vde` | 750 |
| | `/hana/log` | `hana_log_vg` | `vdf` | 192 |
| |  |  | `vdg` | 192 |
| |  |  | `vdh` | 192 |
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
{: caption="Table 7. Storage for vx2* profile based virtual servers" caption-side="top"}


### ux2d-* profiles
{: #hana-iaas-intel-vs-vpc-ux2d}

The following table shows the required volumes and related volume groups, if necessary, and their characteristics:

| Profile | File system | Volume Group | Volume | Size (GB) |
| --- | --- | --- | --- | --- |
| `ux2d-8x224` | `/hana/shared` |  | `vdd` | 250 |
| | `/hana/data` | | `vde` | 750 |
| | `/hana/log` | `hana_log_vg` | `vdf` | 192 |
| |  |  | `vdg` | 192 |
| |  |  | `vdh` | 192 |
| --- | --- | --- | --- | --- |
| `ux2d-16x448` | `/hana/shared` |  | `vdd` | 450 |
| | `/hana/data` | | `vde` | 1,350 |
| | `/hana/log` | `hana_log_vg` | `vdf` | 192 |
| |  |  | `vdg` | 192 |
| |  |  | `vdh` | 192 |
| --- | --- | --- | --- | --- |
| `ux2d-36x1008` | `/hana/shared` |  | `vdd` | 1,000 |		ok
| | `/hana/data` | `hana_data_vg` | `vde` | 1,008 |
| | | | `vdf` | 1,008 |
| | `/hana/log` | `hana_log_vg` | `vdg` | 128 |
| | | | `vdh` | 128 |
| | | | `vdi` | 128 |
| | | | `vdj` | 128 |
| --- | --- | --- | --- | --- |
| `ux2d-48x1344` | `/hana/shared` |  | `vdd` | 1,344 |		ok
| | `/hana/data` | `hana_data_vg` | `vde` | 1,350 |
| | | | `vdf` | 1,350 |
| | `/hana/log` | `hana_log_vg` | `vdg` | 128 |
| | | | `vdh` | 128 |
| | | | `vdi` | 128 |
| | | | `vdj` | 128 |
| --- | --- | --- | --- | --- |
| `ux2d-72x2016` | `/hana/shared` |  | `vdd` | 2,048 |
| | `/hana/data` | `hana_data_vg` | `vde` | 1,024 |
| | | | `vdf` | 1,024 |
| | | | `vdg` | 1,024 |
| | | | `vdh` | 1,024 |
| | `/hana/log` | `hana_log_vg` | `vdi` | 192 |
| | | | `vdj` | 192 |
| | | | `vdk` | 192 |
| --- | --- | --- | --- | --- |
| `ux2d-100x2800` | `/hana/shared` |  | `vdd` | 2,800 |
| | `/hana/data` | `hana_data_vg` | `vde` | 2,100 |
| | | | `vdf` | 2,100 |
| | | | `vdg` | 2,100 |
| | | | `vdh` | 2,100 |
| | `/hana/log` | `hana_log_vg` | `vdi` | 192 |
| | | | `vdj` | 192 |
| | | | `vdk` | 192 |
| --- | --- | --- | --- | --- |
| `ux2d-200x5600` | `/hana/shared` |  | `vdd` | 5,600 |
| | `/hana/data` | `hana_data_vg` | `vde` | 4,200 |
| | | | `vdf` | 4,200 |
| | | | `vdg` | 4,200 |
| | | | `vdh` | 4,200 |
| | `/hana/log` | `hana_log_vg` | `vdi` | 192 |
| | | | `vdj` | 192 |
| | | | `vdk` | 192 |
{: caption="Table 8. Storage for ux2* profile based virtual servers" caption-side="top"}
