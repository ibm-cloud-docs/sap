---
copyright:
  years: 2025, 2026
lastupdated: "2026-04-29"
keywords: SAP, Bare Metal, VPC, NVMe, storage, IOPS, HANA, log, data, shared, {{site.data.keyword.filestorage_vpc_short}}, sapmnt, {{site.data.keyword.cos_full_notm}}, VPE, plan, nvme, raid, local storage
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Storage infrastructure for SAP workloads on {{site.data.keyword.baremetal_short}} in {{site.data.keyword.vpc_short}}
{: #plan-storage-vpc-bm}

This document provides guidance on planning storage infrastructure requirements for SAP workloads on {{site.data.keyword.baremetal_short}} in {{site.data.keyword.vpc_full}}.
{: shortdesc}

For SAP workloads hosted on a cloud service provider, the infrastructure as a service (IaaS) layer operates in a similar way to environments used to run SAP workloads in external data centers.
SAP systems in a landscape have specific storage requirements to support performance, availability, and data protection.

{{site.data.keyword.ibm_cloud_sap}} provides a rich set of capabilities that support these storage requirements and extend the SAP landscape beyond basic system hosting.

The following sections describe {{site.data.keyword.ibm_cloud_sap}} portfolio design considerations for storage on {{site.data.keyword.bm_is_full}} in {{site.data.keyword.vpc_short}}.

## Overview
{: #overview}

The storage architecture for {{site.data.keyword.bm_is_short}} operates within the {{site.data.keyword.vpc_short}} infrastructure. IBM provisions, maintains, and manages the physical bare metal servers in IBM-operated data centers.

{{site.data.keyword.bm_is_short}} are dedicated physical servers with local storage. Storage is provided through local boot volumes and local data volumes based on Non-Volatile Memory Express (NVMe) U.2 solid-state drives (SSDs). You are responsible for managing all aspects of local storage, including RAID configuration, file system creation, encryption, monitoring, and backup.

The local storage provides the following key capabilities:
- High-performance NVMe SSDs: You can use NVMe based local storage to achieve low latency and high throughput for demanding SAP workloads.
- Customer-managed RAID: You can fully manage [RAID](/docs/bare-metal?topic=bare-metal-bm-raid-levels) levels and configurations.
- Flexible configuration: You can configure file systems and volume layouts to meet specific workload needs.
- Local data sovereignty: You can ensure that data remains on the physical bare metal server.

Storage for {{site.data.keyword.bm_is_short}} is unmanaged. You are responsible for encryption and backing up your data.
{: important}

## Storage types
{: #types}

{{site.data.keyword.bm_is_short}} supports multiple storage types that are designed for different workload requirements, performance characteristics, and use cases.

### Local storage
{: #local-storage}

Local storage is the primary storage type for {{site.data.keyword.bm_is_short}} and provides the highest performance for SAP workloads. All storage is physically attached to the bare metal server.

Boot volume
:   Boot volumes have the following properties:

    - All profiles include one 0.96 TB SATA M.2 mirrored SSD as the boot disk.
    - Configured as RAID 1 (mirrored) for redundancy.
    - Used for operating system installation.
    - Cannot be detached or resized.

Data volumes (NVMe U.2 SSDs)
:   Data volumes have the following properties:

    - All profiles provide an additional set of NVMe U.2 SSDs as secondary local storage.
    - NVMe SSDs provide fast and affordable storage to support options such as customer-managed RAID.
    - Presented as Just a Bunch of Disks (JBOD) for customer-managed software-based RAID or striping.
    - Quantity varies by profile (typically 8-16 NVMe devices).
    - You are responsible for RAID configuration, file system creation, and data management.

For more information, see [Bare Metal Servers for VPC storage](/docs/vpc?topic=vpc-bare-metal-servers-storage).

### {{site.data.keyword.cos_full_notm}}
{: #cloud-object-storage}

{{site.data.keyword.cos_full}} supports the following use cases:
- Backup, restore, and archive or near-line storage purposes from {{site.data.keyword.bm_is_short}}.
- Long-term retention of audit logs and compliance data.
- Disaster recovery copies.

{{site.data.keyword.cos_full_notm}} buckets are available in different storage classes. For more information, see [Cloud Object Storage classes](/docs/cloud-object-storage?topic=cloud-object-storage-classes).

To access {{site.data.keyword.cos_full}} from your bare metal server, you can use:
- Public endpoints over the internet
- Private endpoints through Virtual Private Endpoints (VPE) for secure, private network access

Communicating with the VPE gives secure access from your bare metal server to {{site.data.keyword.cos_full}} over the private network.

### File storage
{: #file-storage}

{{site.data.keyword.filestorage_vpc_full}} is a compatible network-attached storage solution for {{site.data.keyword.bm_is_short}} that are provisioned after 31 August 2023. This storage type is ideal for shared file systems such as `/sapmnt`, `/usr/sap/trans`, and other shared directories in SAP landscapes.

For more information, see [About File Storage for VPC](/docs/vpc?topic=vpc-file-storage-vpc-about).

{{site.data.keyword.filestorage_vpc_full}} offers two types of profiles with different availability characteristics:

| Profile name | IOPS/GB | Availability | Description |
|--------------|---------|--------------|-------------|
| dp2 (zonal) | 5 IOPS/GB | Single zone | Data is stored within a single availability zone. Suitable for most {{site.data.keyword.sap_app_server}} workloads. Lower cost compared to regional profiles. |
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

{{site.data.keyword.block_storage_is_full}} is not supported for {{site.data.keyword.bm_is_short}}.
{: note}

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

To plan storage volumes for SAP HANA, refer to the tables provided in [Bare Metal Server certified profiles on VPC infrastructure for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-vpc-intel-bm#sr-storage). Locate the compute profile applicable to your deployment, and review the Storage tab.

Use the local NVMe set for primary HANA paths, and use operating system native RAID and Logical Volume Manager (LVM) where required:

- Configure `/hana/log` and `/hana/data` using multiple NVMe devices in **RAID 10** (or RAID groups striped via LVM) to combine low latency with redundancy.
- Configure `/hana/shared` on a single local NVMe file system, use {{site.data.keyword.filestorage_vpc_short}} (NFS) based on single host or multi host shared requirements.
- Configure `/usr/sap` on local NVMe (preferred) or {{site.data.keyword.filestorage_vpc_short}}. Keep binaries and logs separate from boot.
- Configure `/boot` on the mirrored M.2 SSD (RAID 1). Keep OS minimal and stateless where possible.
- Add additional NVMe volumes for backup or export file systems as needed.

The exact RAID configuration and number of NVMe devices depend on your specific bare metal profile. Always refer to the storage configuration tables in the Bare Metal Server certified profiles documentation for your chosen profile.

## {{site.data.keyword.sap_app_server}} storage guidelines
{: #app-server-storage-guidelines}

{{site.data.keyword.sap_app_servers}} have less demanding storage requirements compared to database servers. However, proper storage planning is still important for optimal performance.

### Volume layout
{: #app-server-layout}

To plan storage volumes for the {{site.data.keyword.sap_app_server}}, refer to the storage section in [{{site.data.keyword.sap_app_server}} certified instances on Intel-powered bare metal servers in VPC Infrastructure](/docs/sap?topic=sap-nw-iaas-offerings-profiles-vpc-bm#nw-iaas-intel-bm-vpc-int-storage). Use the SAP application server file system tab to determine the storage tier and the size of the storage volume for the `/usr/sap` file system.

Configure storage as follows:

- Configure `/usr/sap` and local logs on local NVMe for simple, low-latency access.
- For shared binaries and transports, mount {{site.data.keyword.filestorage_vpc_short}} (NFS) to provide POSIX-shared paths across application servers such as `/sapmnt/<SID>` and `/usr/sap/trans`.

## References
{: #references}

- [About Bare Metal Servers for VPC](/docs/vpc?topic=vpc-about-bare-metal-servers)
- [Bare Metal Servers for VPC storage](/docs/vpc?topic=vpc-bare-metal-servers-storage)
- [About File Storage for VPC](/docs/vpc?topic=vpc-file-storage-vpc-about)
- [File Storage for VPC profiles](/docs/vpc?topic=vpc-file-storage-profiles)
- [Cloud Object Storage classes](/docs/cloud-object-storage?topic=cloud-object-storage-classes)
- [Accessing file storage shares for VPC](/docs/sap?group=file-storage-shares-for-vpc)
- [SAP Note 2493172 - SAP HANA Hardware and Cloud Measurement Tools](https://me.sap.com/notes/2493172){: external}
- [SAP Help Portal: SAP HANA Tailored Data Center Integration](https://help.sap.com/docs/SAP_HANA_PLATFORM/eb3777d5495d46c5b2fa773206bbfb46/0b15a92d554c4941a452c9ca127f8c70.html){: external}
- [SAP Help Portal: SAP HANA Hardware and Cloud Measurement Tools guide](https://help.sap.com/docs/HANA_HW_CLOUD_TOOLS/02bb1e64c2ae4de7a11369f4e70a6394/7e878f6e16394f2990f126e639386333.html){: external}
- [SAP Learning Resources: Outlining how to use the Hardware Measurement Tool](https://learning.sap.com/courses/sap-hana-installation-and-administration/outlining-how-to-use-the-hardware-measurement-tool){: external}
- [SAP HANA Storage Requirements](https://www.sap.com/documents/2024/03/146274d3-ae7e-0010-bca6-c68f7e60039b.html){: external}
- [Bare Metal Server certified profiles on VPC infrastructure for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-vpc-intel-bm#sr-storage)
- [Internal storage on bare metal servers in VPC Infrastructure](/docs/sap?topic=sap-nw-iaas-offerings-profiles-vpc-bm#internal-storage)
