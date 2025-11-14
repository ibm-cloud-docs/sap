---
copyright:
  years: 2020, 2025
lastupdated: "2025-11-12"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Storage design considerations
{: #storage-design-considerations}

The SAP systems in a landscape have specific requirements for servers, operating systems, network setup, and supported storage.
{: shortdesc}

For SAP workloads that use a Cloud Service Provider, Infrastructure-as-a-Service is similar to existing practices used to run SAP workloads in external data centers or by a data center provider. An SAP landscape has specific requirements for connectivity, between hosts within Cloud IaaS and to external systems. {{site.data.keyword.ibm_cloud_sap}} provides a rich set of functions to improve your SAP landscape, beyond hosting SAP systems.

To assist your project's planning phase, the below sections provide {{site.data.keyword.ibm_cloud_sap}} portfolio design considerations for **Storage**.


## Preface: units of measure for data/information
{: #storage-units-of-measure}

Storage performance refers to the read/write performance from storage file system. Often the throughput Network Storage is shown in Mbps or Gbps, whereas for Local Disk storage is shown in MiB/s.

It is important to note, that Mb (Megabits) is a decimal prefix and MiB (Mebibyte) is a binary prefix so they are on different scales. More confusion arises because MiB (Mebibyte) was commonly known in Microsoft Windows as `Megabyte`.

For future reference throughout the storage documentation, Mb (Megabits) and MiB (Mebibyte) is used based on the system of units (SI) defined by IEC and adopted by IEEE, ISO, and NIST.

For example:
- 100 Mbps (Megabits per second), would be 12 MiB/s (Mebibyte per second)
- 1000 Mbps (Megabits per second) also known as 1 Gbps (Gigabits per second), would be 120 MiB/s (Mebibyte per second)
- 10 Gbps (Gigabits per second), would be 1200 MiB/s (Mebibyte per second)


## Storage configuration for SAP HANA
{: #storage-config-hana}

With any SAP HANA certified profile that is listed as Appliance, storage is already provided or must be attached precisely as described.

When you provision more storage for an SAP HANA instance, you must adhere to mandatory TDI storage requirements. See the [SAP HANA TDI Overview](https://www.sap.com/documents/2017/09/e6519450-d47c-0010-82c7-eda71af511fa.html){: external}, [SAP HANA TDI FAQ](https://www.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html){: external}, [SAP Note 2493172 - SAP HANA Hardware and Cloud Measurement Tools](https://me.sap.com/notes/2493172){: external}, and follow the instructions of the [HCMT guide](https://help.sap.com/docs/HANA_HW_CLOUD_TOOLS){: external}. Additional guidance you may find [here](https://learning.sap.com/learning-journeys/installing-and-administering-sap-hana/outlining-how-to-use-the-hardware-measurement-tool){: external}.

For more information for {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}, see [IBM System Storage Architecture and Configuration Guide for SAP HANA TDI v2.31.pdf](https://www.ibm.com/support/pages/system/files/inline-files/IBM%20System%20Storage%20Architecture%20and%20Configuration%20Guide%20for%20SAP%20HANA%20TDI%20v2.31.pdf){: external}.

The requirements include multiple volumes that are assigned to the DATA and LOG LVMs, with the striping and multipath enhancements increase I/O performance. For more information, see the following documents:
- [SAP HANA Tailored Data Center Integration (TDI) Overview](https://www.sap.com/documents/2017/09/e6519450-d47c-0010-82c7-eda71af511fa.html){: external}
- [SAP HANA Tailored Data Center Integration FAQ (Updated May 2020)](https://www.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html){: external}
- For file system sizes, see [SAP HANA Storage Requirements](https://www.sap.com/documents/2024/03/146274d3-ae7e-0010-bca6-c68f7e60039b.html){: external}


## Storage performance considerations
{: #storage-performance}

It is important to calculate your project requirements before you decide on a storage solution. This calculation is critical for selecting network storage because of storage variations and performance considerations.


### Storage impacts on Recovery Time Objective (RTO) of SAP HANA backups
{: #storage-performance-backup-rto}

If you need to restore an SAP HANA system, then the IOPS of your storage has a significant influence on your restore window. Backup windows are not as critical with SAP HANA since all backups are online backups no matter how you configure SAP HANA.

For example, by using {{site.data.keyword.cloud_notm}} {{site.data.keyword.blockstorageshort}}, you can calculate for an approximate 12 TB restore of SAP HANA at a maximum speed. You must create three physical storage devices (block storage iSCSI LUNs) because the maximum size per device is 4 TB. You can create a stripe over these three devices with the Linux&reg; Logical Volume Manager and create one logical device of 12 TB.

The 12 TB provides 3x10 IOPS/GB, which is a total of 122,880 IOPS/GB at 16 KB. This amount gives you a restore time of 1.875 GB per second, or a total restore time of under 2 hours. Since the measurement for the IOPS is taken at a 50/50 distribution of read and write, you can consider the numbers as a lower boundary of restore performance. It is advisable to do backup and restore tests if you rely on a certain restore window.


### Network Block Storage considerations
{: #storage-performance-network-block}

The following sections describe the storage considerations that use network block storage for SAP workload scenarios that use various IBM Cloud infrastructure options.


#### Network Block or File storage for VMware on Classic Infrastructure
{: #storage-performance-network-block-vmware}

{{site.data.content.vms-deprecated-note}}

Using VMware for SAP workloads on {{site.data.keyword.cloud_notm}} is certified. However it requires choice of the storage and would use the "TDI" delivery model for which you would need to run validation checks to gain SAP Support. Therefore, it is important to consider the correct storage for your VMware hosts when they are running SAP workloads.

For VMware clusters, where SAP workloads are run across multiple VMware vSphere hypervisor nodes, storage must be shared across these hypervisor nodes.

VMware is available to work with Block storage or File storage from {{site.data.keyword.cloud_notm}}. When you are using Network Block or File storage, do not expect that certification performance benchmarks to remain the same. Particularly after factoring in the hypervisor overheads as described in [Compute Profiles of SAP-certified VMware on Classic Infrastructure](/docs/sap?topic=sap-compute-os-design-considerations#compute-power).

For VMware datastores (where the virtual machine .VMDK virtual disks are located), the recommendations are:
- For SAP HANA, use Local SDD disks for the datastore in a RAID10 configuration
- For SAP HANA, with network storage, use 10 IOPS/GB with each vSphere node that hosts SAP that uses a network interface card with 10 Gbps connection
- For SAP NetWeaver or SAP AnyDB, with network storage, use at least 4 IOPS/GB with each vSphere node that hosts SAP that uses a network interface card with 10 Gbps connection

To achieve maximum IOPS on a volume, adequate network resources need to be in place. Other considerations include private network usage outside of storage and host side, and application-specific tunings (for example, IP stacks and queue depths). For more information, see [Getting started with Block Storage](/docs/BlockStorage?topic=BlockStorage-getting-started#getting-started) and [Getting started with File Storage](/docs/FileStorage?topic=FileStorage-getting-started#getting-started) for more information on storage tiers and performance.

The storage to use with either the VMware manual setup (Bare Metal with VMware OS Image) or VMware automated setup ({{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated), is described in:
* [Storage to use with VMware vSphere on {{site.data.keyword.cloud_notm}} Bare Metals](/docs/vmware?topic=vmware-vmware-storage) provides further direction on how to integrate storage in an ESX environment.
* [Storage to use with {{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated](/docs/vmwaresolutions?topic=vmwaresolutions-design_physicalinfrastructure#design_physicalinfrastructure-storage-design)


#### Block Storage for Virtual Servers on VPC Infrastructure
{: #storage-performance-network-block-vs-vpc}

For network storage, IOPS per GB is limited and performance depends on the workload. For relational database management systems (RDBMS), it might be advisable to use the same volume for both the database's log and data storage. This setup depends on the behavior of your application.

In general, for typical RDBMS-based applications, a 5 IOPS/GB profile is reasonable.

If your application uses dedicated key performance indicators (KPIs) on storage performance, test the storage throughput before you begin software deployment. By using volume manager-based software RAID (like LVM), you meet almost every KPI.


## Sample storage configurations on Classic Infrastructure
{: #sample-classic}

The following sections demonstrate storage configurations in various different SAP workload scenarios, which are using **Classic Infrastructure**.


### Sample storage configuration for IBM Db2 that use Intel Bare Metal
{: #sample-classic-db2}

Table 1 is a sample storage configuration for a 256 GB server with 50,000 [SAPS](/docs/sap?topic=sap-sizing), 1.5 TB at 6,000 IOPS for a central system with SAP. The system uses an IBM Db2 database with external [{{site.data.keyword.cloud_notm}} {{site.data.keyword.blockstorageshort}}](/docs/BlockStorage?topic=BlockStorage-getting-started#getting-started) or [{{site.data.keyword.cloud_notm}} {{site.data.keyword.filestorage_short}}](/docs/FileStorage?topic=FileStorage-getting-started#getting-started) (4 IOPS/GB). The calculation for the IOPS is:

* 6,000 IOPS/1,500 GB = 4 IOPS/GB needed for external storage. It is assumed that 3,000 GB is for backup at 2 IOPS/GB (medium performance.

| File system | number of volumes | Storage type | IOPS/GB | GB | IOPS |
| --- | --- | --- | --- | --- | --- |
| `/` | 1 | Internal | N/A | 150 GB | N/A |
| `/boot` | 1 | Internal | N/A | 0.25 GB | N/A |
| `swap` | 1 | Internal | N/A | 256 GB | N/A |
| `/db2` (including logs) | 1 | Internal | N/A | 250 GB | N/A |
| `sapdata` | 1 | External | 4 IOPS/GB | 1,500 GB | 6,000 |
| `backup/log and backup` | 1 | External | 2 IOPS/GB | 3,000 GB | 6,000 |
{: caption="Sample storage layout based on IOPS calculation" caption-side="top"}


## Sample storage configurations on VPC Infrastructure
{: #sample-vpc}

The following sections demonstrate storage configurations in various different SAP workload scenarios, when you are using **VPC Infrastructure**.

### Sample storage configuration for SAP AnyDB with IBM Db2 that uses Intel Virtual Server
{: #sample-intel-vs-vpc-anydb-ibm-db2}

For SAP AnyDB that uses IBM Db2 on `mx2-32x256` profile the volumes that are needed are:
- 1x 500 GB volumes; one block storage volume of 500 GB size, with a custom volume profile that supports up to 10,000 **Max IOPS** attached to the Virtual Server
- 1x 2,000 GB volume; one block storage volume of 2,000 GB size, with a lower 4,000 IOPS (medium performance) attached to the Virtual Server for backups

#### Disk mount points and volumes for IBM Db2
{: #sample-intel-vs-vpc-anydb-ibm-db2-mountpoints}

After you attach the two data volumes, two new virtual disks will appear in the Virtual Server, see the following table. In this example, those disks are `vdd`, `vde`, and `vdf`.

| File system | Volume | Storage type | IOPS/GB | GB | IOPS |
| --- | --- | --- | --- | --- | --- |
| `/` | `vdal` | Pre-configured boot volume | N/A | 100 GB | 3,000 |
| `/boot` | `vda2` | Pre-configured boot volume | N/A | 0.25 GB | 3,000 |
| `/db2` | `vdd` (can vary) | Data volume | 20 IOPS/GB | 500 GB | 10,000 |
| `backup/log` and `backup` | `vde` (can vary) | Data volume | 5 IOPS/GB | 2,000 GB | 4,000 |
{: caption="Sample storage configuration" caption-side="top"}

Table 1 shows a basic layout of the file system to support an IBM Db2 installation. Generally, an IBM Db2 installation uses subdirectories that can be segmented into independent volumes.

For example, `"/db2/<DBSID>"`, `"/db2/<DBSID>/log_dir"`, and several `"sapdata<n>"`, where the folder `"log_dir"` contains the online logs files of the database and the `"sapdata<n>"` contains the data itself. For example, see the Db2 documentation here: [Required File Systems for IBM Db2 for Linux, UNIX, and Windows](https://help.sap.com/docs/SLTOOLSET/ce9e270ad34949969c16d09d1b099a26/713eb64f45c6448c8dbe8a51b85680ee.html?version=CURRENT_VERSION){: external}.


### Sample storage configurations for SAP HANA
{: #hana-vs-storage-config}

Further information about [storage specifications for Virtual Server](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc#hana-iaas-intel-vs-vpc-storage-specs) are available, the below shows only the configuration steps required.

#### mx2-8x64, mx2-16x128 and mx2-32x256 profiles
{: #hana-iaas-mx2-16x128-32x256-configure}

The mx2-8x64 profile is certified for SAP Business One on HANA only.
{: note}


For Virtual Server created based on the **mx2-8x64**, **mx2-16x128** and **mx2-32x256** profiles, there are:
- 3x 500 GB volumes; three block storage volumes of 500 GB size, with a custom volume profile that supports up to 10,000 **Max IOPS** attached to the Virtual Server
- 1x 2,000 GB volume; one block storage volume of 2,000 GB size, with a lower 4,000 IOPS (medium performance) attached to the Virtual Server for backups

After attaching the three data volumes, three new virtual disks will appear in the virtual server, see the table that follows. In this example, those disks are `vdd`, `vde` and `vdf`.

The disks are visible in the operating system of the virtual server as follows:

```sh
[root@hana256-vsi ~]# fdisk -l

Disk /dev/vdd: 536.9 GB, 536870912000 bytes, 1048576000 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/vde: 536.9 GB, 536870912000 bytes, 1048576000 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/vdf: 536.9 GB, 536870912000 bytes, 1048576000 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

These three disks must be managed under the Linux&reg; Logical Volume Manager (LVM), and deployed as logical volumes. In order to achieve that, first put the three devices under LVM control. For example, make them physical volumes:

```sh
[root@hana256-vsi ~]# pvcreate /dev/vdd /dev/vde /dev/vdf
```

Then, create a volume group from the physical volumes. The name of the volume group can be chosen according to your preferences, in our sample it is `hana_vg`:

```sh
[root@hana256-vsi ~]# vgcreate hana_vg /dev/vdd /dev/vde /dev/vdf
```

After creating the volume group, three logical volumes need to be defined on top. These logical volumes reflect the file system size requirements for SAP HANA. The following commands are for a 256 GB virtual server:

```sh
[root@hana256-vsi ~]# lvcreate -i 3 -I 64K -L 256GB -n hana_log_lv hana_vg
[root@hana256-vsi ~]# lvcreate -i 3 -I 64K -L 256GB -n hana_shared_lv hana_vg
[root@hana256-vsi ~]# lvcreate -i 3 -I 64K -l 100%FREE -n hana_data_lv hana_vg
```

For a 128 GB virtual server, in the example above `-L 256GB` must be replaced by `-L 128GB` and for 64 GB by `-L 64GB` accordingly. These commands will not result in the smallest possible file system size, but they create the smallest configuration, which will fulfill the SAP HANA KPIs.
Finally, a file system needs to be created on top of each volume group:

```sh
[root@hana256-vsi ~]# mkfs.xfs /dev/mapper/hana_vg-hana_log_lv
[root@hana256-vsi ~]# mkfs.xfs /dev/mapper/hana_vg-hana_data_lv
[root@hana256-vsi ~]# mkfs.xfs /dev/mapper/hana_vg-hana_shared_lv
```

The following entries to `/etc/fstab` will mount the file systems after their mount points (`/hana/data`, `/hana/log` and  `/hana/shared`) have been created:

```sh
/dev/mapper/hana_vg-hana_log_lv    /hana/log xfs defaults,swalloc,nobarrier,inode64
/dev/mapper/hana_vg-hana_shared_lv /hana/shared xfs defaults,inode64 0 0
/dev/mapper/hana_vg-hana_data_lv   /hana/data xfs defaults,largeio,swalloc,inode64 0 0
```

#### mx2-48x384 profile
{: #hana-iaas-mx2-48x384-configure}

For Virtual Server created based on the **mx2-48x384** profile there are:
- 3x 500 GB volumes; three block storage volumes of 500 GB size, with a custom volume profile that supports up to 10,000 **Max IOPS** attached to the Virtual Server is required
- 4x 100 GB volumes; four block storage volumes of 100 GB size, with a custom volume profile that supports up to 6,000 **Max IOPS** attached to the Virtual Server is required
- _Optional:_ 1x 2,000 GB volume; one block storage volume of 2,000 GB size, with a lower 4,000 IOPS (medium performance) attached to the Virtual Server for backups

After attaching the seven data volumes, seven new virtual disks will appear in the Virtual Server, see the table that follows. In this example, those disks are `vdd`, `vde`, `vdf`, `vdg`, `vdh`, `vdi`, `vdj`.


These three disks must be managed under the Linux&reg; Logical Volume Manager (LVM), and deployed as logical volumes. In order to achieve that, first put the three devices under LVM control. For example, make them physical volumes:

```sh
[root@hana384-vsi ~]# pvcreate /dev/vd[d,e,f,g,h,i,j]
```

Then, two different volume groups need to be created:

```sh
[root@hana384-vsi ~]# vgcreate hana_vg /dev/vdh /dev/vdi /dev/vdj
[root@hana384-vsi ~]# vgcreate hana_log_vg /dev/vdd /dev/vde /dev/vdf /dev/vdg
```

Next, three logical volumes need to be defined on top. These logical volumes reflect the file system size requirements for SAP HANA. The following commands are for a 384 GB virtual server:

```sh
[root@hana384-vsi ~]# lvcreate -l 100%VG -i 4 -I 64K  -n hana_log_lv hana_log_vg
[root@hana384-vsi ~]# lvcreate -i 3 -L 384G -I 64K -n hana_shared_lv hana_vg
[root@hana384-vsi ~]# lvcreate -i 3 -l 100%FREE  -I 64K -n hana_data_lv hana_vg
```

Finally, a file system needs to be created on top of each volume group:

```sh
[root@hana384-vsi ~]# mkfs.xfs /dev/mapper/hana_log_vg-hana_log_lv
[root@hana384-vsi ~]# mkfs.xfs /dev/mapper/hana_vg-hana_data_lv
[root@hana384-vsi ~]# mkfs.xfs /dev/mapper/hana_vg-hana_shared_lv
```

The following entries to `/etc/fstab` mount the file systems after their mount points (`/hana/data`, `/hana/log` and  `/hana/shared`) have been created:

```sh
/dev/mapper/hana_log_vg-hana_log_lv    /hana/log xfs defaults,swalloc,nobarrier,inode64
/dev/mapper/hana_vg-hana_shared_lv /hana/shared xfs defaults,inode64 0 0
/dev/mapper/hana_vg-hana_data_lv   /hana/data xfs defaults,largeio,swalloc,inode64 0 0
```

## General storage configurations on IBM {{site.data.keyword.powerSys_notm}} Infrastructure
{: #sample-power}

The following sections provides general recommendations for storage configurations of different SAP workloads on IBM {{site.data.keyword.powerSys_notm}}s.

### General storage guidelines for SAP application on IBM {{site.data.keyword.powerSys_notm}}
{: #sap-app-server-storage-config}

* For boot volume it is recommended to use `Fixed IOPs` or `Tier 0`.
* An additional Capacity for `/usr/sap` using `Fixed IOPs` or `Tier 0` on a separate storage volume is recommended.

### General storage guidelines for SAP HANA on IBM {{site.data.keyword.powerSys_notm}}
{: #powervs-general-recommendation-storage}

* Use block storage volumes with minimum `12,000 IOPS` for SAP HANA `log` file system. SAP HANA log file system size is usually up to `512 GB`. We recommend to stripe file system over `4` storage volumes.
* Use block storage volumes with minimum `8,000 IOPS` for SAP HANA data file system. SAP HANA `data` file system size depends on memory size. SAP recommends to ensure `120-150%` of memory configured for the virtual machine. We recommend to stripe file system over `4` storage volumes.
* SAP does not provide any performance requirements for SAP HANA `shared` file system. We recommend to configure minimum `3000 IOPS` for the file system. Block storage volume striping is not required. As alternative, SAP HANA shared file system may reside on NFS volume.
* For OS boot volume we recommend to use `Fixed IOPs` or `Tier 0`.
* An additional capacity for /usr/sap on `Fixed IOPs` or `Tier 0` on a separate storage volume is recommended.
* See following documentation for [sample storage configurations for SAP HANA certified profiles on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-hana-iaas-offerings-profiles-power-vs#sr2-profiles-log).


### Sample storage configuration for Oracle DB on IBM AIX that use the IBM {{site.data.keyword.powerSys_notm}}
{: #sample-power-anydb-oracle-aix}

Table 2 is a sample configuration for an AIX {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} for an SAP NetWeaver application server that uses Oracle as the example.

The storage cannot be combined within the same {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}, and can be either Tier 1 or Tier 3. The recommendation is to provision three more disks to enable separation between the OS, database, and application layer. Disk size depends on if the installation is Greenfield or if the server is a copy of an "on-premises" AIX server you decided to use as a sizing reference.

The naming convention for the LVM entries is optional, but the advice is to include the SID of your SAP NetWeaver system, especially if you plan to install one or more instances.

| Storage | Volume group | Logical volume | Mount point |
| --- | --- | --- | --- |
| OS disk | Default configuration | Default configuration | Default configuration |
| Application disk | `app<sid>vg` | `lvusrsap` | `/usr/sap` |
| | | `lvusrsap{SID}` | `/usr/sap/{SID}` |
| | | `lvusrsapmnt` | `/sapmnt/{SID}` |
| | | `lvusrsaptrans` | `/usr/sap/trans` |
| | | `lvsapDAH` | `/usr/sap/DAH` |
| Database storage | `db<sid>vg` | `lv{SID}arch` | `/oracle/{SID}/oraarch` |
| | | `lv{SID}reorg` | `/oracle/{SID}/sapreorg` |
| | | `lv{SID}origlogA` | `/oracle/{SID}/origlogA` |
| | | `lv{SID}origlogB` | `/oracle/{SID}/origlogA` |
| | | `lv{SID}ora` | `/oracle/{SID}` |
| | | `lv{SID}sapdata1` | `/oracle/{SID}/sapdata1` |
| | | `lv{SID}sapdata2` | `/oracle/{SID}/sapdata2` |
| | | `lvorastage` | `/oracle/stage` |
| | | `lv{SID}sapdata3` | `/oracle/{SID}/sapdata3` |
| | | `lv{SID}sapdata4` | `/oracle/{SID}/sapdata4` |
| | | `lv{SID}oraclient` | `/oracle/client` |
{: caption="Sample storage layout for Oracle" caption-side="top"}

For more information, see [SAP Note 2172935](https://me.sap.com/notes/2172935){: external}.


### Sample storage configuration for {{site.data.keyword.Db2_on_Cloud_short}} on IBM AIX that use the IBM {{site.data.keyword.powerSys_notm}}
{: #sample-power-anydb-ibm-db2-aix}

Table 3 is a sample storage configuration for an AIX {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} for an {{site.data.keyword.Db2_on_Cloud_short}} server.

The storage cannot be combined within the same {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}, and can be either Tier 1 or Tier 3. The recommendations are to provision three more disks to enable separation between the OS, database, and application layer. Disk size depends on whether the installation is Greenfield or the server is a copy of an "on-premises" AIX server that you decided to use as a sizing reference.

The naming convention for the LVM entries is optional, but the advice is to include the SID of your SAP NetWeaver system especially if you plan to install one or more instances.

| Storage | Volume group | Logical volume | Mount point |
| --- | --- | --- | --- |
| OS disk | Default configuration | Default configuration | Default configuration |
| Application disk | `app<sid>vg` | `lvusrsap` | `/usr/sap` |
| | | `lvusrsap{SID}` | `/usr/sap/{SID}` |
| | | `lvusrsapmnt` | `/sapmnt/{SID}` |
| | | `lvusrsaptrans` | `/usr/sap/trans` |
| | | `lvsapDAH` | `/usr/sap/DAH` |
| Db2 database storage I | `<sid>db2vg` | `loglv{SID}` | NA |
| | | `lv{SID}db2` | `/db2/{SID}` |
| | | `lvhome{SID}` | `/db2/db2{SID}` |
| | | `lv{SID}db2dump` | `/db2/{SID}/db2dump` |
| | | `lv{SID}logdir` | `/db2/{SID}/log_dir` |
| | | `lv{SID}log_archive` | `/db2/{SID}/log_archive` |
| | | `lv{SID}saptmp` | `/db2/{SID}/saptemp1` |
| | | `lv{SID}db2sw` | `/db2/db2/<DBSID>/db2_sw` |
| Db2 database storage II | `<sid>db2datvg` | `lv{SID}sapdata1` | `/db2/{SID}/sapdata1` |
| | | `lv{SID}sapdata2` | `/db2/{SID}/sapdata2` |
| | | `lv{SID}sapdata3` | `/db2/{SID}/sapdata3` |
| | | `lv{SID}sapdata4` | `/db2/{SID}/sapdata4` |
{: caption="Sample storage layout for Db2 on Cloud" caption-side="top"}

For more information, see [Required File Systems for IBM Db2 for Linux, UNIX, and Windows](https://help.sap.com/docs/SLTOOLSET/4fbd902c7c76410bb82c6311dd4dc94b/713eb64f45c6448c8dbe8a51b85680ee.html?version=CURRENT_VERSION){: external} and [SAP Note 1707361](https://me.sap.com/notes/1707361){: external}.
