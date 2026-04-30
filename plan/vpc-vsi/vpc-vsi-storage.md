---
copyright:
  years: 2020, 2026
lastupdated: "2026-04-29"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, Storage, VPC, {{site.data.keyword.block_storage_is_short}}, {{site.data.keyword.filestorage_vpc_short}}, IOPS, performance, capacity, tiers
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Storage infrastructure for SAP workloads on virtual server instances in {{site.data.keyword.vpc_short}}
{: #plan-storage-vpc-vsi}

This document provides guidance on planning storage infrastructure requirements for SAP workloads on virtual server instances (VSIs) in {{site.data.keyword.vpc_full}}.
{: shortdesc}

For SAP workloads hosted on a cloud service provider, the infrastructure as a service (IaaS) layer operates in a similar way to environments used to run SAP workloads in external data centers.
SAP systems in a landscape have specific storage requirements to support performance, availability, and data protection.

{{site.data.keyword.ibm_cloud_sap}} provides a rich set of capabilities that support these storage requirements and extend the SAP landscape beyond basic system hosting.

The following sections describe {{site.data.keyword.ibm_cloud_sap}} portfolio design considerations for storage on {{site.data.keyword.vsi_is_full}} in {{site.data.keyword.vpc_short}}.

## Overview
{: #overview}

The storage architecture for {{site.data.keyword.vsi_is_short}} operates within the {{site.data.keyword.vpc_short}} infrastructure. IBM provisions, maintains, and manages the entire storage infrastructure in IBM-operated data centers.

Clients use storage in the form of storage volumes (boot volumes and data volumes) and file storage shares. IBM manages all underlying physical storage hardware.

The storage volumes and file shares provide the following key capabilities:
- Configurable storage profiles: You can provision volumes across multiple performance tiers based on IOPS requirements.
- Flexible sizing: You can increase the capacity of a volume after creation without disrupting operations.
- Encryption: Data is encrypted at rest by using IBM-managed or customer-managed encryption keys.
- Snapshots and backups: You can create point-in-time snapshots of block storage volumes for data protection.
- Shareability: File storage shares can be attached to multiple virtual server instances for shared file systems.

## Storage types
{: #types}

{{site.data.keyword.vsi_is_short}} supports multiple storage types that are designed for different workload requirements, performance characteristics, and use cases.

### Block storage
{: #block-storage}

Block storage is used for {{site.data.keyword.vsi_is_short}} and provides predictable performance based on input/output operations per second (IOPS). It is the primary storage type for operating system boot volumes and additional data volumes.

Boot and data volumes
:   Boot and data volumes have the following properties:

    - When you create a virtual server instance, a boot volume is automatically created and attached. The default boot volume size is 100 GB with 3,000 IOPS. You can customize the boot volume size and IOPS during provisioning.
    - You can attach up to 12 additional data volumes per virtual server instance, with each volume supports up to 16,000 IOPS.
    - You can provision each storage volume based on the IOPS that your workload requires, by either using tiered profiles or custom IOPS profiles.
    - Each volume has a storage profile that defines how many I/O operations per second (IOPS) can be started against that volume.
    - The IOPS for the storage profiles can scale according to the size of the volume (tiered profiles), or by custom IOPS allocations.

    Create storage volumes for SAP HANA&reg; systems after the deployment of the individual server instance is complete.
    {: note}

    The following table shows the supported block storage profiles with corresponding IOPS:

    | Profile name | IOPS/GB | Description |
    |--------------|---------|-------------|
    | General purpose (3iops-tier) | 3 IOPS/GB | Suitable for backup volumes and less critical workloads |
    | 5iops-tier | 5 IOPS/GB | Suitable for database data volumes and shared file systems |
    | 10iops-tier | 10 IOPS/GB | Suitable for SAP HANA log and data volumes |
    | Custom | 100 to 48,000 IOPS | Allows you to specify capacity (10 GB to 16,000 GB) and IOPS independently |
    {: caption="Block storage profiles and IOPS mapping" caption-side="bottom"}

    For more information, see:
    - [Block Storage profiles](/docs/vpc?topic=vpc-block-storage-profiles)
    - [Block Storage capacity and performance](/docs/vpc?topic=vpc-capacity-performance&interface=ui)


Volume flexibility
:   The volume flexibility feature includes:

    - You can increase the size of an existing data volume to manage data growth. For more information, see [Expanding Block Storage volume capacity](/docs/vpc?topic=vpc-expanding-block-storage-volumes).
    - You can increase the IOPS of a volume by changing to a higher-tier profile or adjusting the custom IOPS setting.
    - Volume expansion is performed online without disrupting operations or requiring a reboot.
    - You cannot decrease the size or IOPS of a volume after creation.

For more information, see the following documents:
- [About Block Storage for VPC](/docs/vpc?topic=vpc-block-storage-about)
- [Block Storage capacity and performance](/docs/vpc?topic=vpc-capacity-performance)
- [Bandwidth allocation for Block Storage volumes](/docs/vpc?topic=vpc-block-storage-bandwidth&interface=ui)
- [Bandwidth allocation for Block Storage volumes](/docs/vpc?topic=vpc-block-storage-bandwidth)

### {{site.data.keyword.cos_full_notm}}
{: #cloud-object-storage}

{{site.data.keyword.cos_full}} supports the following use cases:
- Backup, restore, and archive or near-line storage purposes from {{site.data.keyword.vsi_is_short}}.
- Long-term retention of audit logs and compliance data.
- Disaster recovery copies.

{{site.data.keyword.cos_full_notm}} buckets are available in different storage classes. For more information, see [Cloud Object Storage classes](/docs/cloud-object-storage?topic=cloud-object-storage-classes).

To access {{site.data.keyword.cos_full}} from your VPC, you can use:
- Public endpoints over the internet
- Private endpoints through Virtual Private Endpoints (VPE) for secure, private network access

Communicating with the VPE gives secure access from your virtual server to {{site.data.keyword.cos_full}} over the private network.

### File storage
{: #file-storage}

{{site.data.keyword.filestorage_vpc_full}} provides NFS-based shared file storage that can be accessed by multiple virtual server instances simultaneously. This storage type is ideal for shared file systems such as `/sapmnt`, `/usr/sap/trans`, and other shared directories in SAP landscapes. For more information, see [About File Storage for VPC](/docs/vpc?topic=vpc-file-storage-vpc-about).

{{site.data.keyword.filestorage_vpc_full_notm}} offers two types of profiles with different availability characteristics:

| Profile name | IOPS/GB | Availability | Description |
|--------------|---------|--------------|-------------|
| dp2 (zonal) | 5 IOPS/GB | Single zone | Data is stored within a single availability zone. Suitable for most SAP application server workloads. Lower cost compared to regional profiles. |
| rfs (regional) | 5 IOPS/GB | Multi-zone | Data is replicated across multiple availability zones within a region. Provides higher availability and resilience. Suitable for critical shared file systems requiring cross-zone redundancy. |
{: caption="File storage profiles" caption-side="bottom"}

Both the profiles support:
- Capacity range: 10 GB to 32,000 GB
- IOPS range: 100 to 96,000 IOPS
- NFS v4.1 protocol
- Encryption at rest: Data is encrypted by using IBM-managed or customer-managed encryption keys
- Mount targets: Mount targets control network access

For more information, see [File Storage for VPC profiles](/docs/vpc?topic=vpc-file-storage-profiles).

The following table provides an exemplary overview about file systems on file storage:

| File system | Default path | Purpose or Scenario | Typical size (GB) | Description |
|-------------|--------------|-------------------|-------------------|-------------|
| Software repository | `/install` | Staging area for installers, media, kernels | Varies (typically 200–1000+) | Shared or local; depends on the content to be stored. |
| Installation path | `/hana/shared` | Shared files between hosts in an SAP HANA multi-host system | 1000 | SAP HANA runtime and client, configuration, and instance files. |
| SAP core path | `/usr/sap/<SID>` | Per-SID instance directories (profiles, kernels, work, logs) | 10–20 per AS instance | Size scales with the number of application server instances. |
| SAP Mount | `/sapmnt/<SID>` | Files shared between application servers | 20–50 | Contains shared data, profiles, and executable replication directory. |
| SAP Transport | `/usr/sap/trans` | Central SAP transport directory (cofiles, data, EPS inbox) | 50–200+ | Shared for all systems; grows with project activity or transport volume. |
{: caption="File storage" caption-side="bottom"}

## SAP HANA storage guidelines
{: #hana-storage-guidelines}

Storage provisioning for an SAP HANA system must comply with mandatory SAP HANA storage requirements.

Refer to the following resources for more information:

- [SAP Note 2493172 - SAP HANA Hardware and Cloud Measurement Tools](https://me.sap.com/notes/2493172){: external}
- [SAP Help Portal: SAP HANA Tailored Data Center Integration](https://help.sap.com/docs/SAP_HANA_PLATFORM/eb3777d5495d46c5b2fa773206bbfb46/0b15a92d554c4941a452c9ca127f8c70.html){: external}
- [SAP Help Portal: SAP HANA Hardware and Cloud Measurement Tools guide](https://help.sap.com/docs/HANA_HW_CLOUD_TOOLS/02bb1e64c2ae4de7a11369f4e70a6394/7e878f6e16394f2990f126e639386333.html){: external}
- [SAP Learning Resources: Outlining how to use the Hardware Measurement Tool](https://learning.sap.com/courses/sap-hana-installation-and-administration/outlining-how-to-use-the-hardware-measurement-tool){: external}

For file system sizes, see [SAP HANA Storage Requirements](https://www.sap.com/documents/2024/03/146274d3-ae7e-0010-bca6-c68f7e60039b.html){: external}.

### Volume layout
{: #hana-layout}

To plan storage volumes for SAP HANA, refer to the tables provided in [SAP HANA certified instances on Intel-powered virtual servers on VPC Infrastructure](/docs/sap?topic=sap-hana-iaas-offerings-profiles-vpc-intel-vsi). Locate the compute profile applicable to your deployment, and review the tabs in the table for the log file system, data file system, and shared file system configuration. These tabs specify the required quantity of storage volumes, the storage tier, and the corresponding volume sizes for the file systems `/hana/log`, `/hana/data`, and `/hana/shared`.

Configure storage volumes as follows:

- Configure **multiple** block storage volumes with 10 IOPS per GB tier (10iops-tier) for the SAP HANA `log file system`. The typical total capacity that is required for the SAP HANA log file system is up to `512 GB`. Stripe the logical volume for the `/hana/log` file system across multiple storage volumes to achieve the required throughput.
- Configure **multiple** block storage volumes with 10 IOPS per GB tier (10iops-tier) for the SAP HANA `data file system`. The typical total capacity depends on the memory size. Stripe the logical volume for the `/hana/data` file system across multiple storage volumes to achieve the required throughput.
- Configure **one** block storage volume with 5 IOPS/GB tier (5iops-tier), or {{site.data.keyword.filestorage_vpc_short}} for the SAP HANA `shared file system /hana/shared`. Provide a minimum of `3,000 IOPS`.
- Configure **one** block storage volume for the `/usr/sap` file system by using 5 IOPS per GB tier (5iops-tier).
- Add more volumes for a backup or export file system.

The exact number of volumes and their sizes depend on your specific SAP HANA profile. Always refer to the storage configuration tables in the SAP HANA-certified instances documentation for your chosen profile.

## {{site.data.keyword.sap_app_server}} with IBM Db2 storage guidelines
{: #anydb-ibm-db2-storage-guidelines}

{{site.data.keyword.sap_app_server}} with IBM Db2 requires specific storage configurations to ensure optimal database performance. The storage layout depends on the virtual server instance profile and the expected database size.

### Volume layout
{: #anydb-ibm-db2-layout}

For {{site.data.keyword.sap_app_server}} with IBM Db2 on a typical `mx2-32x256` profile, the recommended storage configuration includes:

- **1× 500 GB volume**: One block storage volume with custom IOPS profile supporting up to 10,000 IOPS for the database data files
- **1× 2,000 GB volume**: One block storage volume with 5 IOPS/GB tier (5iops-tier) providing 10,000 IOPS for database backups and logs

After you attach the data volumes, new virtual disks appear in the virtual server instance. The following table shows a typical storage configuration for IBM Db2:

| File system | Volume | Storage type | IOPS/GB | GB | IOPS |
|-------------|--------|--------------|---------|-----|------|
| `/` | `vda1` | Pre-configured boot volume | N/A | 100 GB | 3,000 |
| `/boot` | `vda2` | Pre-configured boot volume | N/A | 0.25 GB | 3,000 |
| `/db2` | `vdd` (can vary) | Data volume | 20 IOPS/GB | 500 GB | 10,000 |
| `backup/log` and `backup` | `vde` (can vary) | Data volume | 5 IOPS/GB | 2,000 GB | 10,000 |
{: caption="Sample storage configuration for IBM Db2" caption-side="bottom"}

The table shows a basic layout of the file system to support an IBM Db2 installation. Generally, an IBM Db2 installation uses subdirectories that can be segmented into independent volumes.

For example, `/db2/<DBSID>`, `/db2/<DBSID>/log_dir`, and several `sapdata<n>` directories, where the folder `log_dir` contains the online log files of the database and the `sapdata<n>` directories contain the data itself.

For more information, see [Required File Systems for IBM Db2 for Linux, UNIX, and Windows](https://help.sap.com/docs/SLTOOLSET/ce9e270ad34949969c16d09d1b099a26/713eb64f45c6448c8dbe8a51b85680ee.html?version=CURRENT_VERSION){: external}.

## {{site.data.keyword.sap_app_server}} with SAP ASE storage guidelines
{: #ase-storage-guidelines}

SAP Adaptive Server Enterprise (ASE), formerly known as Sybase, requires specific storage configurations to ensure optimal database performance. The storage layout depends on the virtual server instance profile and the expected database size.

### Volume layout
{: #ase-layout}

For {{site.data.keyword.sap_app_server}} with SAP ASE, consider the following storage components:

- Choose the correct virtual server instance profile based on estimated user load, CPU, and RAM requirements. For more information, see [Virtual Server profile names](/docs/sap?topic=sap-intro-sap-cloud-vpc#virtual-profile-intel-vsi-sap-vpc).
- Select an SAP-certified OS image (RHEL, SUSE, or Windows) from the catalog. For more information, see [Catalog images on VPC](/docs/sap?topic=sap-intro-sap-cloud-vpc#catalog-images-sap-nw-db2-rhel).

For {{site.data.keyword.sap_app_server}} systems running on SAP Adaptive Server Enterprise, all database devices must reside on local or block‑level storage, in accordance with the SAP installation guides and [SAP Note 1748888](https://me.sap.com/notes/1748888){: external}.

1. **Block storage volumes**
   - Recommended for database data files and log files
   - Use 5 IOPS/GB tier (5iops-tier) or 10 IOPS/GB tier (10iops-tier) for data files
   - Use 5 IOPS/GB tier (5iops-tier) for log files
   - Use 3 IOPS/GB tier (general-purpose) for backup volumes

2. **File storage shares**
   - Suitable for shared file systems such as `/sapmnt` and `/usr/sap/trans`
   - Use dp2 profile (5 IOPS/GB) for most workloads
   - For more information, see [About File Storage for VPC](/docs/vpc?topic=vpc-file-storage-vpc-about).

The following table shows a typical storage configuration for SAP ASE:

| File system | Volume | Storage type | IOPS/GB | GB | IOPS |
|-------------|--------|--------------|---------|-----|------|
| `/` | `vda1` | Pre-configured boot volume | N/A | 100 GB | 3,000 |
| `/boot` | `vda2` | Pre-configured boot volume | N/A | 0.25 GB | 3,000 |
| `/sybase/<DBSID>` | `vdd` (can vary) | Data volume | 10 IOPS/GB | 500 GB | 5,000 |
| `/sybase/<DBSID>/sapdata_<n>` | `vde` (can vary) | Data volume | 10 IOPS/GB | 1,000 GB | 10,000 |
| `/sybase/<DBSID>/saplog_<n>` | `vdf` (can vary) | Data volume | 5 IOPS/GB | 200 GB | 1,000 |
| `/backup` | `vdg` (can vary) | Data volume | 3 IOPS/GB | 2,000 GB | 6,000 |
{: caption="Sample storage configuration for SAP ASE" caption-side="bottom"}

For more information, see the following documents:
- [SAP Help Portal - SAP Adaptive Server Enterprise (ASE)](https://help.sap.com/docs/SAP_ASE){: external}
- [SAP Community page for SAP Adaptive Server Enterprise (ASE)](https://pages.community.sap.com/topics/applications-on-ase){: external}

## SAP application server storage guidelines
{: #app-server-storage-guidelines}

SAP application servers have less demanding storage requirements compared to database servers. However, proper storage planning is still important for optimal performance.

### Volume layout
{: #app-server-layout}

Configure a block storage volume for the `/usr/sap` file system by using 5 IOPS/GB tier (5iops-tier) or 3 IOPS/GB tier (general-purpose), or create a directory in the root file system '/' of the boot volume.

For shared file systems, use {{site.data.keyword.filestorage_vpc_short}} for `/sapmnt/<SID>` and `/usr/sap/trans`.

## References
{: #references}

- [Catalog images on VPC](/docs/sap?topic=sap-intro-sap-cloud-vpc#catalog-images-sap-nw-db2-rhel)
- [File Storage profiles](/docs/vpc?topic=vpc-file-storage-profiles)
- [Block Storage profiles](/docs/vpc?topic=vpc-block-storage-profiles)
- [Capacity and performance](/docs/vpc?topic=vpc-capacity-performance)
- [About File Storage for VPC](/docs/vpc?topic=vpc-file-storage-vpc-about)
- [Bandwidth allocation for Block Storage volumes](/docs/vpc?topic=vpc-block-storage-bandwidth&interface=ui)
- [About Block Storage for VPC](/docs/vpc?topic=vpc-block-storage-about)
- [Cloud Object Storage classes](/docs/cloud-object-storage?topic=cloud-object-storage-classes)
- [Virtual Server profile names](/docs/sap?topic=sap-intro-sap-cloud-vpc#virtual-profile-intel-vsi-sap-vpc)
- [SAP HANA Storage Requirements](https://www.sap.com/documents/2024/03/146274d3-ae7e-0010-bca6-c68f7e60039b.html){: external}
- [Expanding Block Storage volume capacity](/docs/vpc?topic=vpc-expanding-block-storage-volumes)
- [SAP Note 2493172 - SAP HANA Hardware and Cloud Measurement Tools](https://me.sap.com/notes/2493172){: external}
- [SAP Help Portal: SAP HANA Tailored Data Center Integration](https://help.sap.com/docs/SAP_HANA_PLATFORM/eb3777d5495d46c5b2fa773206bbfb46/0b15a92d554c4941a452c9ca127f8c70.html){: external}
- [SAP Help Portal: SAP HANA Hardware and Cloud Measurement Tools guide](https://help.sap.com/docs/HANA_HW_CLOUD_TOOLS/02bb1e64c2ae4de7a11369f4e70a6394/7e878f6e16394f2990f126e639386333.html){: external}
- [SAP Learning Resources: Outlining how to use the Hardware Measurement Tool](https://learning.sap.com/courses/sap-hana-installation-and-administration/outlining-how-to-use-the-hardware-measurement-tool){: external}
- [Bandwidth allocation for Block Storage volumes](/docs/vpc?topic=vpc-block-storage-bandwidth)
- [Outlining how to use the Hardware Measurement Tool](https://learning.sap.com/courses/sap-hana-installation-and-administration/outlining-how-to-use-the-hardware-measurement-tool){: external}
- [SAP Help Portal - SAP Adaptive Server Enterprise (ASE)](https://help.sap.com/docs/SAP_ASE){: external}
- [SAP Community page for SAP Adaptive Server Enterprise (ASE)](https://pages.community.sap.com/topics/applications-on-ase){: external}
- [Required File Systems for IBM Db2 for Linux, UNIX, and Windows](https://help.sap.com/docs/SLTOOLSET/ce9e270ad34949969c16d09d1b099a26/713eb64f45c6448c8dbe8a51b85680ee.html?version=CURRENT_VERSION){: external}
- [SAP HANA certified instances on Intel-powered virtual servers on VPC Infrastructure](/docs/sap?topic=sap-hana-iaas-offerings-profiles-vpc-intel-vsi)
