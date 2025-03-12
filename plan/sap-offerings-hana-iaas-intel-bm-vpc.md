---
copyright:
  years: 2020, 2025
lastupdated: "2025-03-12"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Bare Metal Server certified profiles on VPC infrastructure for SAP HANA
{: #hana-iaas-offerings-profiles-intel-bm-vpc}

## Profiles list
{: #hana-iaas-intel-bm-vpc-list}

The following table gives you an overview of the SAP-certified profiles with bare metal servers for VPC. The vCPUs in this list are CPU cores and their secondary threads. The term vCPU is kept for comparison with their virtual counterparts.

| **Profile** | **vCPU** | **Memory (RAM GiB)** | **SAPS** | **SAP HANA\nProcessing Type** |
| --- | --- | --- | --- | --- |
| **Compute Optimized** | | | | |
| cx2d-metal-96x192 | 96 | 192 | 107,400 | SAP Business One (\*\*) |
| **Balanced** | | | | |
| bx2d-metal-96x384 | 96 | 384 | 124,130 | OLTP/OLAP (\*)\nSAP Business One (\*\*) |
| **Memory Optimized** | | | | |
| mx2d-metal-96x768 | 96 | 768 | 127,620 | OLTP/OLAP (\*)\nSAP Business One (\*\*) |
| **Ultra High Memory Optimized** | | | |
| ux2d-metal-112x3072 | 112 | 3,072 | 140,730 | OLTP/OLAP (\*) |
| ux2d-metal-224x6144 | 224 | 6,144 | 294,730 | OLTP/OLAP (\*) |
{: caption="{{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC certified for SAP HANA" caption-side="bottom"}


### Profiles hosted on Intel Sapphire Rapids CPU
{: #hana-iaas-intel-bm-sr-vpc-list}

| **Profile** | **vCPU** | **Memory (RAM GiB)** | **SAPS** | **aSAPS<sup>(1)</sup>** | **SAP HANA\nProcessing Type** |
| --- | --- | --- | --- | --- | --- |
| **Balanced** | | | | | |
| bx3d-metal-48x256 | 48 | 256 | 93,670 | 18,400 | OLTP/OLAP (\*) |
| bx3d-metal-64x256 | 64 | 256 | 124,520 | 24,600 | OLTP/OLAP (\*) |
| bx3d-metal-192x1024 | 192 | 1.024 | 297,770 | 57,400 | OLTP/OLAP (\*) |
| **Memory Optimized** | | | | | |
| mx3d-metal-48x512 | 48 | 512 | 97,830 | 18,700 | OLTP/OLAP (\*) |
| mx3d-metal-64x512 | 64 | 512 | 128,750 | 24,200 | OLTP/OLAP (\*) |
| mx3d-metal-96x1024 | 96 | 1.024 | 182,670 | 33,700 | OLTP/OLAP (\*) |
| mx3d-metal-128x1024 | 128 | 1.024 | 239,300 | 46,000 | OLTP/OLAP (\*) |
{: caption="{{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC certified for SAP NetWeaver - Intel Sapphire Rapids CPU" caption-side="bottom"}

<sup>(1)</sup>: aSAPS is the metric that is derived from the [SAP quote-to-cash (Q2C) Benchmark](https://www.sap.com/about/benchmark/appbm/q2c.html){: external}.

(\*): RHEL 8.4 for SAP Solutions, RHEL 8.6 for SAP Solutions, RHEL 8.8 for SAP Solutions, RHEL 8.10 for SAP Solutions, RHEL 9.0 for SAP Solutions, RHEL 9.2 for SAP Solutions, RHEL 9.4 for SAP Solutions\n
SLES 12 SP5, SLES 15 SP2, SLES 15 SP3, SLES 15 SP4, SLES 15 SP5, SLES 15 SP6

(\*\*): SLES 15 SP2, SLES 15 SP3, SLES 15 SP4, SLES 15 SP5


For more information, see [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}.

For SAP HANA deployments that use {{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC, only single-node deployments are supported. Multi-node / scale-out is not currently supported.
{: important}


## Understanding Bare Metal Server profile names
{: #hana-iaas-intel-bm-vpc-names}

With {{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC, the profile families that are certified for SAP are: *Compute Optimized*, *Balanced*, *Memory Optimized*, and *Ultra High Memory Optimized*.
- *Compute Optimized* family profiles provide more compute power, and they have more cores with less memory.
- *Balanced* family profiles provide a good mix of performance and scalability for more common workloads.
- *Memory Optimized* and *Ultra High Memory Optimized* family profiles cater to memory intensive workloads, such as demanding database applications and in-memory analytics workloads, and are especially designed for SAP HANA workloads.

For more information, see [x86-64 bare metal server profiles](/docs/vpc?topic=vpc-bare-metal-servers-profile).

The first letter of the profile name determines the profile family. 
The ratio of cores (*number of vCPUs*) to RAM (*amount of GiB*) is one of the key attributes of a profile family.

| **First letter** | **Characteristics of the related profile family** | **Ratio Cascade Lake** | **Ratio Sapphire Rapids** |
| --- | --- | --- | --- |
| c | *Compute Optimized* family | 1:2 | n/a |
| b | *Balanced* family | 1:4 | 1:4 or 1:5.33 |
| m | *Memory Optimized* family| 1:8 | 1:8 or 1:10.67 |
| u | *Ultra High Memory Optimized* family | 1:27.43 | n/a |
{: caption="{{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC Profile Families" caption-side="top"}

The bare metal server profile names are contextual and sequential. See the following example:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| mx2d-metal-96x768 | m | *Memory Optimized* family |
| | x | Intel x86_64 CPU architecture |
| | ? \n   2 \n   3 | The Intel generation for the underlying hardware \n   Cascade Lake \n   Sapphire Rapids |
| | d | the optional 'd' in the name indicates that the server is equipped with one or more SSD storage devices |
| | — | *spacer* |
| | metal | *metal* in the name indicates that this is a bare metal server |
| | — | *spacer* |
| | 96 | 96 vCPU |
| | x | *spacer* |
| | 768 | 768 GiB RAM |
{: caption="Profile naming for SAP HANA" caption-side="top"}

## Profiles available on Hourly Consumption Billing
{: #hana-iaas-intel-bm-vpc-hourly}

All {{site.data.keyword.cloud_notm}} Bare Metal Servers for VPC are available with Hourly Consumption Billing, which includes Suspend Discounts and Sustained Usage Discounts. With Suspend Discounts, storage charges occur only if the server is in shutdown state. With Sustained Usage Discount, the more a server is used, the less the cost per hour.

## Storage specifications
{: #hana-iaas-intel-bm-vpc-storage-specs}

When the bare metal server profiles for SAP HANA are initially provisioned, the servers all have one pre-configured disk (sda) and provide the root partition `/` with about 890 GB and the partition `/boot/efi` with 100 MB or less. Depending on storage volume type, the specific OS release and version the storage layout and partition sizes are differing a little.

In addition to these partitions, Bare Metal Servers for VPC have up to 8 NVMEs – depending on their RAM size – which need to be configured after the server deployment.

To fulfill the KPIs defined for SAP HANA, each profile needs different storage volumes that are listed in detail in the following sections. These storage configurations are recommended. They are certified storage layouts that comply with **SAP HANA Tailored Data Center Integration** (TDI) Phase 5.

If a specific memory sizing needs to be performed, customers are advised to follow [the instructions here](https://learning.sap.com/learning-journeys/installing-and-administering-sap-hana/performing-sap-hana-sizing) and if it turns out that different logic volume sizes are required then in addition the [SAP HANA TDI Overview](https://www.sap.com/documents/2017/09/e6519450-d47c-0010-82c7-eda71af511fa.html){: external} and [SAP HANA TDI FAQ](https://www.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html){: external} must be considered. In that case, users must run SAP's performance measurement tool HCMT - see [SAP Note 2493172 - SAP HANA Hardware and Cloud Measurement Tools](https://me.sap.com/notes/2493172){: external} and follow the instructions of the [HCMT guide](https://help.sap.com/docs/HANA_HW_CLOUD_TOOLS){: external} to check compliance with SAP’s KPIs.
{: important}

This holds true especially, if file shares are used for SAP HANA installations. They can be deployed and mounted in arbitrary ways to provide additional storage, for example for backups, as needed. For SAP HANA data and log files, however, they have to be evaluated.
{: note}

In any case, [SAP's recommended file system layout](https://help.sap.com/docs/SAP_HANA_PLATFORM/2c1988d620e04368aa4103bf26f17727/4c24d332a37b4a3caad3e634f9900a45.html){: external} must be available for SAP HANA deployment.

### Bare Metal Servers for VPC - Storage Layouts
{: #hana-iaas-intel-bm-vpc-mx2-profiles}

The following table shows the required physical volumes, related volume groups, logical volumes, and their characteristics:

| Profile | File\nsystem | Logical\nVolume | LV Size\n(GiB) | Volume Group | Physical\nVolume |
| --- | --- | --- | --- | --- | --- |
| `cx2d-metal-96x192`  | `/hana/shared` | `hana_shared_lv` | 192 | `vg0` | `nvme0n1-`\n`nvme3n1-` |
| | `/hana/log` | `hana_log_lv` | 192 | `vg0` | |
| | `/hana/data` | `hana_data_lv` | min. 576 | `vg1` | `nvme4n1-`\n`nvme7n1-` |
| --- | --- | --- | --- | --- | --- |
| `bx2d-metal-96x384`  | `/hana/shared` | `hana_shared_lv` | 384 | `vg0` | `nvme0n1-`\n`nvme3n1-` |
| | `/hana/log` | `hana_log_lv` | 384 | `vg0` | |
| | `/hana/data` | `hana_data_lv` | min. 1,152 | `vg1` | `nvme4n1-`\n`nvme7n1-` |
| --- | --- | --- | --- | --- | --- |
| `mx2d-metal-96x768`  | `/hana/shared` | `hana_shared_lv` | 768 | `vg0` | `nvme0n1-`\n`nvme3n1-` |
| | `/hana/log` | `hana_log_lv` | 512 | `vg0` | |
| | `/hana/data` | `hana_data_lv` | min. 2,304 | `vg1` | `nvme4n1-`\n`nvme7n1-` |
| --- | --- | --- | --- | --- | --- |
| `ux2d-metal-112x3072`  | `/hana/shared` | `hana_shared_lv` | 3,072 | `vg0` | `nvme0n1-`\n`nvme3n1-` |
| | `/hana/log` | `hana_log_lv` | 512 | `vg0` | |
| | `/hana/data` | `hana_data_lv` | min. 9,216 | `vg1` | `nvme4n1-`\n`nvme7n1-` |
| --- | --- | --- | --- | --- | --- |
| `ux2d-metal-224x6144`  | `/hana/shared` | `hana_shared_lv` | 6,144 | `vg0` | `nvme0n1-`\n`nvme1n1-` |
| | `/hana/log` | `hana_log_lv` | 512 | `vg0` | |
| | `/hana/data` | `hana_data_lv` | *the remaining space ~17,190*  | `vg0` | |
{: caption="Storage layout for Bare Metal Servers for VPC" caption-side="top"}


Profile `ux2d-metal-224x6144` is equipped with different set of disks, so jump directly to ["Steps for setting up storage for the `ux2d-metal-224x6144` profile"](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-bm-vpc#hana-iaas-intel-bm-vpc-storage-specs-6TB).
{: note}

### Steps for setting up storage for the profiles up to 3,072 GiB
{: #hana-iaas-intel-bm-vpc-storage-specs-upto-3TB}

Note, that both volume groups vg0 and vg1 are not fully used. Remaining space can be used to extend the listed sizes, which are only minimum sizes, or can be used for other purposes. However, do not point I/O load at the remaining space since that impacts SAP HANA’s performance.

To ensure a higher level of availability and failure resilience, RAID10 logical volumes are built on-top the under-laying NVMEs, based on Linux’s logical volume manager.

These steps show a step-by-step guide for setting up the volume groups, logical volumes, and file systems. Size information differs and can be retrieved from the storage layout table.

1. Log in to the OS and install the lvm2 package, if not installed already.

    ```sh
    [root@mx2d-metal-96x768 ~]# yum install lvm2
    ```
    This command applies to RHEL, on SLES use ‘zypper install’ instead.

2. Create the volume groups.

    ```sh
    [root@mx2d-metal-96x768 ~]# vgcreate vg0 /dev/nvme0n1 /dev/nvme1n1 /dev/nvme2n1 /dev/nvme3n1
    [root@mx2d-metal-96x768 ~]# vgcreate vg1 /dev/nvme4n1 /dev/nvme5n1 /dev/nvme6n1 /dev/nvme7n1
    ```

3. Create logical volumes on top of the volume groups.

    ```sh
    [root@mx2d-metal-96x768 ~]# lvcreate --type raid10 -i 2 -m 1 -L 768G -I 64 -n hana_shared_lv vg0
    ```

    `768G` needs to be adapted to the hana_shared volume size specific to your memory size in the sizing table. Sizes for hana_log can be found there, too, and can be used in the lvcreate command as well.
    {: note}

    ```sh
    [root@mx2d-metal-96x768 ~]# lvcreate --type raid10 -i 2 -m 1 -L 512G -I 64 -n hana_log_lv vg0
    ```

4. Create hana_data. Either modify the size with the `-L` option according to the sizing table, or the use the entire volume group with `-l 100%FREE`:

    ```sh
    [root@mx2d-metal-96x768 ~]# lvcreate --type raid10 -i 2 -m 1 -l 100%FREE -I 64 -n hana_data_lv vg1
    ```

5. Create file systems on the logical volumes. In this example, XFS is used and is mounted by label. Mount by label is not a requirement and can be adapted according to your needs:

    ```sh
    [root@mx2d-metal-96x768 ~]# mkfs.xfs -L HANA_SHARED -K /dev/mapper/vg0-hana_shared_lv
    [root@mx2d-metal-96x768 ~]# mkfs.xfs -L HANA_LOG -K /dev/mapper/vg0-hana_log_lv
    [root@mx2d-metal-96x768 ~]# mkfs.xfs -L HANA_DATA -K /dev/mapper/vg1-hana_data_lv
    ```

6. Add the following lines to `/etc/fstab` and create the required directory paths with mkdir.

    ```sh
    LABEL=HANA_SHARED /hana/shared xfs defaults 0 0
    LABEL=HANA_LOG /hana/log xfs defaults,swalloc,inode64 0 0
    LABEL=HANA_DATA /hana/data xfs defaults,largeio,swalloc,inode64 0 0
    ```
7.  You can now mount the file systems.

### Steps for setting up storage for the `ux2d-metal-224x6144` profile
{: #hana-iaas-intel-bm-vpc-storage-specs-6TB}

To ensure a higher level of availability and failure resilience, RAID1 logical volumes are built on-top the under-laying NVMEs, based on Linux’s logical volume manager.

These steps show a step-by-step guide for setting up the volume groups, logical volumes, and file systems.

1. Log in to the OS and install the lvm2 package, if not installed already.

    ```sh
    [root@ux2d-metal-224x6144 ~]# yum install lvm2
    ```
    This command applies to RHEL, on SLES use ‘zypper install’ instead.

2. Create the volume group.

    ```sh
    [root@ux2d-metal-224x6144 ~]# vgcreate vg0 /dev/nvme0n1 /dev/nvme1n1
    ```

3. Create logical volumes hana_shared_lv and hana_log_lv on top of the volume group.

    ```sh
    [root@ux2d-metal-224x6144 ~]# lvcreate --type raid1 -L 6144G -n hana_shared_lv vg0
    [root@ux2d-metal-224x6144 ~]# lvcreate --type raid1 -L 512G -n hana_log_lv vg0
    ```

4. Create logical volume hana_data_lv.

    ```sh
    [root@ux2d-metal-224x6144 ~]# lvcreate --type raid1 -l 100%FREE -n hana_data_lv vg0
    ```

5. Create file systems on the logical volumes. In this example, XFS is used and is mounted by label. Mount by label is not a requirement and can be adapted according to your needs:

    ```sh
    [root@ux2d-metal-224x6144 ~]# mkfs.xfs -L HANA_SHARED -K /dev/mapper/vg0-hana_shared_lv
    [root@ux2d-metal-224x6144 ~]# mkfs.xfs -L HANA_LOG -K /dev/mapper/vg0-hana_log_lv
    [root@ux2d-metal-224x6144 ~]# mkfs.xfs -L HANA_DATA -K /dev/mapper/vg0-hana_data_lv
    ```

6. Add the following lines to `/etc/fstab` and create the required directory paths with mkdir.

    ```sh
    LABEL=HANA_SHARED /hana/shared xfs defaults 0 0
    LABEL=HANA_LOG /hana/log xfs defaults,swalloc,inode64 0 0
    LABEL=HANA_DATA /hana/data xfs defaults,largeio,swalloc,inode64 0 0
    ```
7.  You can now mount the file systems.



Check [SAP Note 2777782](https://me.sap.com/notes/2777782){: external} for RHEL and [SAP Note 2684254](https://me.sap.com//#/notes/2684254){: external} for SLES to adapt your OS configuration settings according to the requirements for SAP HANA.
