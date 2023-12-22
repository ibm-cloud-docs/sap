---

copyright:
  years: 2020,2023
lastupdated: "2023-03-08"

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

# Intel Virtual Server certified profiles on VPC infrastructure for SAP HANA
{: #hana-iaas-offerings-profiles-intel-vs-vpc}

## Profiles list
{: #hana-iaas-intel-vs-vpc-list}

The published names are subject to change.
{: note}

The following list gives you an overview of the SAP-certified profiles with {{site.data.keyword.vsi_is_short}}:

| **Profile** | **vCPU** | **Memory (RAM GiB)** | **SAPS** | **SAP HANA\nProcessing Type** |
| --- | --- | --- | --- | --- |
| **Memory Optimized** | | | | |
| mx2-8x64 | 8 | 64 | 10,280 | SAP Business One (\*\*) |
| mx2-16x128 | 16 | 128 | 20,565 | OLTP (\*)<br/>SAP Business One (\*\*) |
| mx2-32x256 | 32 | 256 | 41,130 | OLTP (\*)<br/>SAP Business One (\*\*)  |
| mx2-48x384 | 48 | 384 | 56,970 | OLTP (\*)<br/>SAP Business One (\*\*)  |
| **Very High Memory Optimized** | | | | |
| vx2d-16x224 | 16 | 224 | 17,046 | OLTP (\*) |
| vx2d-44x616 | 44 | 616 | 46,875 | OLAP/OLTP (\*) |
| vx2d-88x1232 | 88 | 1,232 | 93,750 | OLAP/OLTP (\*) |
| vx2d-144x2016 | 144 | 2,016 | 153,410 | OLAP/OLTP (\*) |
| vx2d-176x2464 | 176 | 2,464 | 187,500 | OLAP/OLTP (\*) |
| **Ultra High Memory Optimized** | | | | |
| ux2d-8x224 | 8 | 224 | 8,623 | OLTP (\*) |
| ux2d-16x448 | 16 | 448 | 17,246 | OLTP (\*) |
| ux2d-36x1008 | 36 | 1,008 | 38,803 | OLTP (\*) |
| ux2d-48x1344 | 48 | 1,344 | 51,737 | OLTP (\*) |
| ux2d-72x2016 | 72 | 2,016 | 77,606 | OLTP (\*) |
| ux2d-100x2800 | 100 | 2,800 | 107,785 | OLTP (\*) |
| ux2d-200x5600 | 200 | 5,600 | 215,570 | OLTP (\*) |
{: caption="Table 1. {{site.data.keyword.cloud_notm}} {{site.data.keyword.vsi_is_short}} certified for SAP HANA" caption-side="bottom"}

(\*): RHEL 7.6 for SAP Solutions, RHEL 7.9 for SAP Solutions, RHEL 8.1 for SAP Solutions, RHEL 8.2 for SAP Solutions, RHEL 8.4 for SAP Solutions, RHEL 8.6 for SAP Solutions, RHEL 9.0 for SAP Solutions<br/>
SLES 12 SP4, SLES 12 SP5, SLES 15, SLES 15 SP1, SLES 15 SP2, SLES 15 SP3, SLES 15 SP4

(\*\*): SLES 12 SP4, SLES 15, SLES 15 SP1, SLES 15 SP2, SLES 15 SP3

Regard the supported operated systems that are mentioned in the footnotes.
{: note}


For more information, see [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211){: external}.


## Understanding Virtual Server profile names
{: #hana-iaas-intel-vs-vpc-names}

With {{site.data.keyword.vsi_is_full}}, the profile families that are certified for SAP are: *Memory Optimized*, *Very High* and *Ultra High Memory Optimized*.
- All the Memory family profiles cater to memory intensive workloads, such as demanding database applications and in-memory analytics workloads, and are especially designed for SAP HANA workloads.

For more information, see chapter [x86-64 instance profiles](/docs/vpc?topic=vpc-profiles).


The first letter of the profile name indicates the profile family that is mentioned the profile list:

| First letter | Characteristics of the related profile family |
| --- | --- |
| m | *Memory Optimized* family, higher vCPU to memory ratio 1:8 |
| v | *Very High Memory Optimized* family, very high vCPU to memory ratio 1:14 |
| u | *Ultra High Memory Optimized* family, ultra high vCPU to memory ratio 1:28 |
{: caption="Table 2. {{site.data.keyword.vsi_is_full}} Profile Families" caption-side="top"}


<br/>
The Virtual Server profile names are contextual and sequential. See the following example:

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

| File system | Partition | Storage type | Size (GB) | Nr. of\nIOPS |
| --- | --- | --- | --- | --- |
| `/` | `vda1` | Pre-configured boot volume | 100 | 3,000 |
| `/boot` | `vda2` | Pre-configured boot volume | 0.25 | 3,000 |
{: caption="Table 4. Storage configuration of the default virtual server deployment (boot volume)" caption-side="top"}

### {{site.data.keyword.block_storage_is_full}}
{: #hana-iaas-intel-vs-vpc-storage-specs-general}

[{{site.data.keyword.block_storage_is_full}}](/docs/vpc?topic=vpc-block-storage-about) volumes for Virtual Servers can be created based on different **volume profiles** that provide different levels of IOPS per gigabyte (IOPS/GB). For more information, see [IOPS tiers](/docs/vpc?topic=vpc-block-storage-profiles#tiers).

You must consider the total IOPS required for your installation and the performance characteristics of your database. One option is to collocate multiple directories into a single large volume with high IOPS, versus isolating directories into individual small volumes with an insufficient number of IOPS for the workload characteristics.

For an overview of all available storage profiles, see [VPC Block Storage Profiles](/docs/vpc?topic=vpc-block-storage-profiles).

### Storage for SAP HANA - single-node
{: #hana-iaas-intel-vs-vpc-storage-specs-sap-hana}

To fulfill the KPIs defined by SAP HANA, each profile needs different storage volumes that are listed in details in the following sections. **These configurations are mandatory storage configurations, not sample storage configurations**, because they are the tested and certified storage layouts that comply with **SAP HANA Tailored Data Center Integration** (TDI) Phase 5. It is highly recommended to stick to these specific specifications.

Customers who want to choose different layouts are advised to follow the [SAP HANA TDI Overview](https://www.sap.com/documents/2017/09/e6519450-d47c-0010-82c7-eda71af511fa.html){: external} and [SAP HANA TDI FAQ](https://www.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html){: external} when they order different storage sizes and types. Then, they must run SAP's performance measurement tool HCMT - see [SAP Note 2493172 - SAP HANA Hardware and Cloud Measurement Tools](https://launchpad.support.sap.com/#/notes/2493172){: external} and follow the instructions of the [HCMT guide](https://help.sap.com/viewer/product/HANA_HW_CLOUD_TOOLS/latest/en-US){: external}. 
{: important}

For all of the following layouts consider that the volume names might differ - we assume that the naming follows the sequence of ordering the storage, that is, 1st order -> `vdd`, 2nd order -> `vde`, and so on. **All block storage volumes** must be ordered with the predefined profile of **10 IOPS/GB** (high performance). One exception might be /hana/shared partition where 5 IOPS/GB (medium performance) are sufficient - but ONLY IF you assigned a dedicated volume for this partition. For all profiles optional: one appropriately sized block storage volume or several equally sized volumes that are gathered to a volume group, with the predefined profile of 5 IOPS/GB (medium performance) attached to the Virtual Server for backups.
{: note}

[SAP's recommended file system layout](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.latest/en-US/4c24d332a37b4a3caad3e634f9900a45.html){: external} must be available for SAP HANA deployment. 

### mx2-* profiles - Storage Layouts
{: #hana-iaas-intel-vs-vpc-mx2-profiles}

The following table shows the required physical volumes, related volume groups, logical volumes, and their characteristics:

| Profile | File\nsystem | Logical\nVolume | LV Size\n(GB) | Volume Group | Physical\nVolume | PV Size\n(GB) |
| --- | --- | --- | --- | --- | --- | --- |
| `mx2-8x64`\nand\n`mx2-16x128`\nand\n`mx2-32x256`  | `/hana/shared` | `hana_shared_lv` | 256 | `hana_vg` | `vdd` | 500 |
| | `/hana/data` | `hana_data_lv` | 256 | | `vde` | 500 |
| | `/hana/log` | `hana_log_lv` | 988 | | `vdf` | 500 |
| --- | --- | --- | --- | --- | --- | --- |
| `mx2-48x384` | `/hana/shared` | `hana_shared_lv` | 384 | `hana_vg` | `vdd` | 500 |
| | `/hana/data` | `hana_data_lv` | 616 | | `vde` | 500 |
| | | | | | `vdf` | 500 |
| | `/hana/log` | `hana_log_lv` | 400 | `hana_log_vg` | `vdg` | 100 |
| | | | | | `vdh` | 100 |
| | | | | | `vdi` | 100 |
| | | | | | `vdj` | 100 |
{: caption="Table 5. Storage layout for mx2-* profiles based virtual servers" caption-side="top"}

### mx2-* profiles - Setup Instructions
{: #hana-iaas-intel-vs-vpc-mx2-setup}

See the step by step instructions for setting up the assets here. Mind the different volume names.
- [for the mx2-8x64, mx2-16x128 and mx2-32x256 profiles](/docs/sap?topic=sap-storage-design-considerations#hana-iaas-mx2-16x128-32x256-configure)
- [for the mx2-48x384 profile](/docs/sap?topic=sap-storage-design-considerations#hana-iaas-mx2-48x384-configure)


### vx2d-* profiles - Storage Layouts
{: #hana-iaas-intel-vs-vpc-vx2-profiles}

The following table shows the required volumes and related volume groups, if necessary, and their characteristics:

| Profile | File\nsystem | Logical\nVolume | LV Size\n(GB) | Volume Group | Physical\nVolume | PV Size\n(GB) |
| --- | --- | --- | --- | --- | --- | --- |
| `vx2d-16x224`  | `/hana/shared` | `hana_shared_lv` | 224 | `hana_vg` | `vde` | 1,120 |
| | `/hana/data` | `hana_data_lv` | 672 | | | |
| | `/hana/log` | `hana_log_lv` | 224 | | | |
| --- | --- | --- | --- | --- | --- | --- |
| `vx2d-44x616`  | `/hana/shared` |<td class="foot" colspan="2">n/a</td>| `vdd` | 616 |
| | `/hana/data` |<td class="foot" colspan="2">n/a</td>| `vde` | 1,848 |
| | `/hana/log` | `hana_log_lv` | 576 | `hana_log_vg` | `vdf` | 192 |
| | | | | | `vdg` | 192 |
| | | | | | `vdh` | 192 |
| --- | --- | --- | --- | --- | --- | --- |
| `vx2d-88x1232`  | `/hana/shared` |<td class="foot" colspan="2">n/a</td>| `vdd` | 1,232 |
| | `/hana/data` |<td class="foot" colspan="2">n/a</td>| `vde` | 3,696 |
| | `/hana/log` | `hana_log_lv` | 576 | `hana_log_vg` | `vdf` | 192 |
| | | | | | `vdg` | 192 |
| | | | | | `vdh` | 192 |
| --- | --- | --- | --- | --- | --- | --- |
| `vx2d-144x2016`  | `/hana/shared` |<td class="foot" colspan="2">n/a</td>| `vdd` | 2,016 |
| | `/hana/data` | `hana_data_lv` | 4,096 | `hana_data_vg` | `vde` | 1,024 |
| | | | | | `vdf` | 1,024 |
| | | | | | `vdg` | 1,024 |
| | | | | | `vdh` | 1,024 |
| | `/hana/log` | `hana_log_lv` | 576 | `hana_log_vg` | `vdi` | 192 |
| | | | | | `vdj` | 192 |
| | | | | | `vdk` | 192 |
| --- | --- | --- | --- | --- | --- | --- |
| `vx2d-176x2464`  | `/hana/shared` |<td class="foot" colspan="2">n/a</td>| `vdd` | 2,464 |
| | `/hana/data` | `hana_data_lv` | 5,120 | `hana_data_vg` | `vde` | 1,280 |
| | | | | | `vdf` | 1,280 |
| | | | | | `vdg` | 1,280 |
| | | | | | `vdh` | 1,280 |
| | `/hana/log` | `hana_log_lv` | 576 | `hana_log_vg` | `vdi` | 192 |
| | | | | | `vdj` | 192 |
| | | | | | `vdk` | 192 |
{: caption="Table 6. Storage for vx2* profile based virtual servers" caption-side="top"}


### vx2d-* profiles - Setup Instructions
{: #hana-iaas-intel-vs-vpc-vx2-setup}

See the step by step instructions for setting up the file systems here. The according volume sizes are captured in the table 6. Read the section [**Adding Block Storage for VPC**](/docs/sap?topic=sap-vs-set-up-infrastructure#vs-adding-vpc-block-storage) to see how to attach the volumes to the HANA server. Some disks are governed by the Linux Logical Volume Manager LVM or lvm2.

For each profile, consider the specified volume sizes in table 6 and always make sure that the correct disks are given for the respective commands. The Linux command `fdisk -l` shows which disk is to the volume, for example `/dev/vde`.
{: note}


#### vx2d-16x224
{: #hana-iaas-intel-vs-vpc-ux2-setup-small}

1. Create the volume group for LVM.

    ```shell
    [root@vx2d-16x224 ~]# pvcreate /dev/vde
    [root@vx2d-16x224 ~]# vgcreate hana_vg /dev/vde
    ```

2. After the volume group is created, three logical volumes are defined on top. These logical volumes reflect the file system size requirements for SAP HANA.

    ```shell
    [root@vx2d-16x224 ~]# lvcreate -L 224G -n hana_shared_lv hana_vg
    [root@vx2d-16x224 ~]# lvcreate -L 224G -n hana_log_lv hana_vg
    [root@vx2d-16x224 ~]# lvcreate -l 100%VG -n hana_data_lv hana_vg
    ```

3. Next, add these entries to /etc/fstab

    ```shell
    LABEL=HANA_SHARED /hana/shared xfs defaults,inode64 0 0
    LABEL=HANA_LOG /hana/log xfs defaults,swalloc,inode64 0 0
    LABEL=HANA_DATA /hana/data xfs defaults,largeio,swalloc,inode64 0 0
    ```

4. Finally, a file system needs to be created on top of each volume group and then mounted:

    ```shell
    [root@vx2d-16x224 ~]# mkfs.xfs -L HANA_SHARED /dev/mapper/hana_vg-hana_shared_lv
    [root@vx2d-16x224 ~]# mkfs.xfs -L HANA_LOG /dev/mapper/hana_vg-hana_log_lv 
    [root@vx2d-16x224 ~]# mkfs.xfs -L HANA_DATA /dev/mapper/hana_vg-hana_data_lv

    [root@vx2d-16x224 ~]# mkdir -p /hana/shared
    [root@vx2d-16x224 ~]# mkdir -p /hana/log
    [root@vx2d-16x224 ~]# mkdir -p /hana/data

    [root@vx2d-16x224 ~]# mount -a
    ```


#### vx2d-44x616 and vx2d-88x1232
{: #hana-iaas-intel-vs-vpc-vx2-setup-midsize}

1. Create the volume group for LVM. Only /hana/log is assigned to the LVM.

    ```shell
    [root@vx2d-44x616 ~]# pvcreate /dev/vdf /dev/vdg /dev/vdh
    [root@vx2d-44x616 ~]# vgcreate hana_log_vg /dev/vdf /dev/vdg /dev/vdh
    ```

2. After the volume group is created, the logical volume for /hana/log needs to be defined on top. This logical volume reflects the file system size requirement for SAP HANA.

    ```shell
    [root@vx2d-44x616 ~]# lvcreate -i 3 -I 64 -l 100%VG -n hana_log_lv hana_log_vg
    ```

3. Now proceed with the same instructions that are listed in steps 3 and 4 [profile vx2d-16x224](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc#vx2d-16x224).


#### vx2d-144x2016 and vx2d-176x2464
{: #hana-iaas-intel-vs-vpc-vx2-setup-gt2T}

1. Create the volume group for LVM. /hana/log and /hana/data are assigned to the LVM.

    ```shell
    [root@vx2d-144x2016 ~]# pvcreate /dev/vde /dev/vdf /dev/vdg /dev/vdh
    [root@vx2d-144x2016 ~]# pvcreate /dev/vdi /dev/vdj /dev/vdk
    [root@vx2d-144x2016 ~]# vgcreate hana_data_vg /dev/vde /dev/vdf /dev/vdg /dev/vdh
    [root@vx2d-144x2016 ~]# vgcreate hana_log_vg /dev/vdi /dev/vdj /dev/vdk
    ```

2. After the volume group is created, two logical volumes need to be defined on top. These logical volumes reflect the file system size requirements for SAP HANA.

    ```shell
    [root@vx2d-144x2016 ~]# lvcreate -i 4 -I 64 -l 100%VG -n hana_data_lv hana_data_vg
    [root@vx2d-144x2016 ~]# lvcreate -i 3 -I 64 -l 100%VG -n hana_log_lv hana_log_vg
    ```

3. Now proceed with the same instructions that are listed in steps 3 and 4 [profile vx2d-16x224](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc#vx2d-16x224).


### ux2d-* profiles - Storage Layouts
{: #hana-iaas-intel-vs-vpc-ux2-profiles}

The following table shows the required volumes and related volume groups, if necessary, and their characteristics:

| Profile | File\nsystem | Logical\nVolume | LV Size\n(GB) | Volume Group | Physical\nVolume | PV Size\n(GB) |
| --- | --- | --- | --- | --- | --- | --- |
| `ux2d-8x224`  | `/hana/shared` | `hana_shared_lv` | 224 | `hana_vg` | `vde` | 1,120 |
| | `/hana/data` | `hana_data_lv` | 672 | | | |
| | `/hana/log` | `hana_log_lv` | 224 | | | |
| --- | --- | --- | --- | --- | --- | --- |
| `ux2d-16x448`  | `/hana/shared` | `hana_shared_lv` | 448 | `hana_vg` | `vde` | 2,240 |
| | `/hana/data` | `hana_data_lv` | 1,344 | | | |
| | `/hana/log` | `hana_log_lv` | 448 | | | |
| --- | --- | --- | --- | --- | --- | --- |
| `ux2d-36x1008`  | `/hana/shared` |<td class="foot" colspan="2">n/a</td>| `vdd` | 1,008 |
| | `/hana/data` | `hana_data_lv` | 2,016 | `hana_data_vg` | `vde` | 1,008 |
| | | | | | `vdf` | 1,008 |
| | `/hana/log` | `hana_log_lv` | 576 | `hana_log_vg` | `vdg` | 192 |
| | | | | | `vdh` | 192 |
| | | | | | `vdi` | 192 |
| --- | --- | --- | --- | --- | --- | --- |
| `ux2d-48x1344`  | `/hana/shared` |<td class="foot" colspan="2">n/a</td>| `vdd` | 1,344 |
| | `/hana/data` | `hana_data_lv` | 2,700 | `hana_data_vg` | `vde` | 1,350 |
| | | | | | `vdf` | 1,350 |
| | `/hana/log` | `hana_log_lv` | 576 | `hana_log_vg` | `vdg` | 192 |
| | | | | | `vdh` | 192 |
| | | | | | `vdi` | 192 |
| --- | --- | --- | --- | --- | --- | --- |
| `ux2d-72x2016`  | `/hana/shared` |<td class="foot" colspan="2">n/a</td>| `vdd` | 2,016 |
| | `/hana/data` | `hana_data_lv` | 4,096 | `hana_data_vg` | `vde` | 1,024 |
| | | | | | `vdf` | 1,024 |
| | | | | | `vdg` | 1,024 |
| | | | | | `vdh` | 1,024 |
| | `/hana/log` | `hana_log_lv` | 576 | `hana_log_vg` | `vdi` | 192 |
| | | | | | `vdj` | 192 |
| | | | | | `vdk` | 192 |
| --- | --- | --- | --- | --- | --- | --- |
| `ux2d-100x2800`  | `/hana/shared` |<td class="foot" colspan="2">n/a</td>| `vdd` | 2,800 |
| | `/hana/data` | `hana_data_lv` | 8,400 | `hana_data_vg` | `vde` | 2,100 |
| | | | | | `vdf` | 2,100 |
| | | | | | `vdg` | 2,100 |
| | | | | | `vdh` | 2,100 |
| | `/hana/log` | `hana_log_lv` | 576 | `hana_log_vg` | `vdi` | 192 |
| | | | | | `vdj` | 192 |
| | | | | | `vdk` | 192 |
| --- | --- | --- | --- | --- | --- | --- |
| `ux2d-200x5600`  | `/hana/shared` |<td class="foot" colspan="2">n/a</td>| `vdd` | 5,600 |
| | `/hana/data` | `hana_data_lv` | 16,800 | `hana_data_vg` | `vde` | 4,200 |
| | | | | | `vdf` | 4,200 |
| | | | | | `vdg` | 4,200 |
| | | | | | `vdh` | 4,200 |
| | `/hana/log` | `hana_log_lv` | 576 | `hana_log_vg` | `vdi` | 192 |
| | | | | | `vdj` | 192 |
| | | | | | `vdk` | 192 |
{: caption="Table 7. Storage for ux2* profile based virtual servers" caption-side="top"}


### ux2d-* profiles - Setup Instructions
{: #hana-iaas-intel-vs-vpc-ux2-setup}

See the step by step instructions for setting up the file systems here. The according volume sizes are captured in the table 7. Read the section [**Adding Block Storage for VPC**](/docs/sap?topic=sap-vs-set-up-infrastructure#vs-adding-vpc-block-storage) to see how to attach the volumes to the HANA server. Some disks are governed by the Linux Logical Volume Manager LVM or lvm2.

For each profile, consider the specific volume sizes in table 7 and always make sure that the correct disks are given for the respective commands. The Linux command `fdisk -l` shows which disk has been mapped to the volume, for example `/dev/vde`.
{: note}


#### ux2d-8x224 and ux2d-16x448
{: #hana-iaas-intel-vs-vpc-ux2-setup-lt1T}

1. Create the volume group for LVM.

    ```shell
    [root@ux2d-8x224 ~]# pvcreate /dev/vde
    [root@ux2d-8x224 ~]# vgcreate hana_vg /dev/vde
    ```

2. After the volume group is created, three logical volumes are defined on top. These logical volumes reflect the file system size requirements for SAP HANA.

    ```shell
    [root@ux2d-8x224 ~]# lvcreate -L 224G -n hana_shared_lv hana_vg
    ## or lvcreate -L 448G -n hana_shared_lv hana_vg
    
    [root@ux2d-8x224 ~]# lvcreate -L 224G -n hana_log_lv hana_vg
    ## or lvcreate -L 448G -n hana_log_lv hana_vg
    
    [root@ux2d-8x224 ~]# lvcreate -l 100%VG -n hana_data_lv hana_vg
    ```

3. Now proceed with the same instructions that are listed in steps 3 and 4 [profile vx2d-16x224](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc#vx2d-16x224).


#### ux2d-36x1008 and ux2d-48x1344
{: #hana-iaas-intel-vs-vpc-ux2-setup-lt2T}

1. Create the volume groups for LVM. /hana/log and /hana/data are assigned to the LVM.

    ```shell
    [root@ux2d-36x1008 ~]# pvcreate /dev/vde /dev/vdf
    [root@ux2d-36x1008 ~]# pvcreate /dev/vdg /dev/vdh /dev/vdi
    [root@ux2d-36x1008 ~]# vgcreate hana_data_vg /dev/vdg /dev/vdh /dev/vdi
    [root@ux2d-36x1008 ~]# vgcreate hana_log_vg /dev/vdg /dev/vdh /dev/vdi
    ```

2. After the volume groups are created, two logical volumes need to be defined on top. These logical volumes reflect the file system size requirements for SAP HANA.

    ```shell
    [root@ux2d-36x1008 ~]# lvcreate -i 2 -I 64 -l 100%VG -n hana_data_lv hana_data_vg
    [root@ux2d-36x1008 ~]# lvcreate -i 3 -I 64 -l 100%VG -n hana_log_lv hana_log_vg
    ```

3. Now proceed with the same instructions that are listed in steps 3 and 4 [profile vx2d-16x224](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc#vx2d-16x224).


#### ux2d-72x2016, ux2d-100x2800, and ux2d-200x5600
{: #hana-iaas-intel-vs-vpc-ux2-setup-gt2T}

1. Create the volume groups for LVM. /hana/log and /hana/data are assigned to the LVM.

    ```shell
    [root@ux2d-72x2016 ~]# pvcreate /dev/vde /dev/vdf /dev/vdg /dev/vdh
    [root@ux2d-72x2016 ~]# pvcreate /dev/vdi /dev/vdj /dev/vdk
    [root@ux2d-72x2016 ~]# vgcreate hana_data_vg /dev/vde /dev/vdf /dev/vdg /dev/vdh
    [root@ux2d-72x2016 ~]# vgcreate hana_log_vg /dev/vdi /dev/vdj /dev/vdk
    ```

2. After the volume groups are created, two logical volumes need to be defined on top. These logical volumes reflect the file system size requirements for SAP HANA.

    ```shell
    [root@ux2d-72x2016 ~]# lvcreate -i 4 -I 64 -l 100%VG -n hana_data_lv hana_data_vg
    [root@ux2d-72x2016 ~]# lvcreate -i 3 -I 64 -l 100%VG -n hana_log_lv hana_log_vg
    ```

3. Now proceed with the same instructions that are listed in steps 3 and 4 [profile vx2d-16x224](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc#vx2d-16x224).

### Storage for SAP HANA - multi-node
{: #hana-iaas-intel-vs-vpc-vx2-storage-multi}

In an SAP HANA scale-out (multi-node) configuration, storage needs to be accessible from different nodes at the same time, and needs to be able to failover from one node to the other. 

Thus, for SAP HANA scale-out configurations, file shares need to be deployed, and local block storage is out of scope for the SAP HANA installation. Those file shares require so-called `mount targets` to be created to allow access to the file shares from dedicated subnets. {{site.data.keyword.cloud_notm}} recommends using the primary subnet for storage access because routes will not require any changes to access the storage servers for the file shares, if the mount target is defined on this subnet. Two additional subnets are required for SAP HANA inter-node (internal) communication, and for client access.

SAP HANA in scale-out configuration requires a shared volume for its `/hana/shared` file system, and a `/hana/log` and `/hana/data` volume for each node. Follow the [mount option recommendation by NetApp](https://docs.netapp.com/us-en/netapp-solutions-sap/bp/saphana_aff_nfs_introduction.html){: external} for setting up your target OS' fstab. See the following sample for an SAP HANA system of system ID 'BHB':

1. `/hana/shared`

    ```plaintext
    fsf-tok0551b-fz.adn.networklayer.com:/903586db_f968_4bf7_bbd5_0926fb7a26ce /hana/shared/BHB nfs      sec=sys,rw,vers=4,minorversion=1,hard,timeo=600,rsize=65536,wsize=65536,intr,noatime,lock 0 0
    ```
    
1. `/hana/data`
   
    ```plaintext
    fsf-tok0551a-fz.adn.networklayer.com:/2b33d3df_9081_47c2_910a_a29356716d51/BHB/mnt00001 /hana/data/BHB/mnt00001 nfs sec=sys,rw,vers=4,minorversion=1,hard,timeo=600,rsize=65536,wsize=65536,intr,noatime,lock 0 0
    fsf-tok0551b-fz.adn.networklayer.com:/ec39996c_346a_4815_a74f_4048382e6ecc/BHB/mnt00002   /hana/data/BHB/mnt00002 nfs sec=sys,rw,vers=4,minorversion=1,hard,timeo=600,rsize=65536,wsize=65536,intr,noatime,lock 0 0 
    ```

1. `/hana/log`
   
    ```plaintext
    fsf-tok0551b-fz.adn.networklayer.com:/7bdee46e_b95f_4ff7_89b8_5273e8f9199d/BHB/mnt00001  /hana/log/BHB/mnt00001 nfs sec=sys,rw,vers=4,minorversion=1,hard,timeo=600,rsize=65536,wsize=65536,intr,noatime,lock 0 0 
    fsf-tok0551b-fz.adn.networklayer.com:/2bca0419_3aef_40ca_b38f_8b9717c93905/BHB/mnt00002  /hana/log/BHB/mnt00002 nfs sec=sys,rw,vers=4,minorversion=1,hard,timeo=600,rsize=65536,wsize=65536,intr,noatime,lock 0 0 
    ```

You cannot follow NetApp's recommendation regarding `rsize` and `wsize` option, these parameters are limited to 65536 by the file share implementation.

To fulfill SAP's requirements about storage layout and through-put and latency KPIs, see details here: [Persistent Data Storage in the SAP HANA Database](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/be3e5310bb571014b3fbd51035bc2383.html){: external}). In any case, they must comply with the TDI performance KPIs (see [SAP Note 2613646](https://launchpad.support.sap.com/#/notes/2613646){: external}) verified by [SAP HANA Hardware and Cloud Measurement Tools](https://help.sap.com/docs/HANA_HW_CLOUD_TOOLS/02bb1e64c2ae4de7a11369f4e70a6394/7e878f6e16394f2990f126e639386333.html){: external} and also ensure SAP's support for it. {{site.data.keyword.cloud_notm}} recommends 10 IOPS per GB or Custom profile file shares for meeting SAP's KPIs. 
