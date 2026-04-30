---
copyright:
  years: 2020, 2026
lastupdated: "2026-04-29"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, Storage, Classic, block storage, file storage, IOPS, performance, local storage
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Storage infrastructure for SAP workloads on {{site.data.keyword.baremetal_short}} in Classic Infrastructure
{: #plan-storage-classic-bm}

This document provides guidance on planning storage infrastructure requirements for SAP workloads on {{site.data.keyword.baremetal_short}} in Classic Infrastructure.
{: shortdesc}

For SAP workloads hosted on a cloud service provider, the infrastructure as a service (IaaS) layer operates in a similar way to environments used to run SAP workloads in external data centers.
SAP systems in a landscape have specific storage requirements to support performance, availability, and data protection.

{{site.data.keyword.ibm_cloud_sap}} provides a rich set of capabilities that support these storage requirements and extend the SAP landscape beyond basic system hosting.

The following sections describe {{site.data.keyword.ibm_cloud_sap}} portfolio design considerations for storage on {{site.data.keyword.baremetal_long}} in {{site.data.keyword.cloud_notm}} Classic Infrastructure.

## Overview
{: #overview}

The storage architecture for {{site.data.keyword.baremetal_short}} in Classic Infrastructure provides both local storage and network-attached storage options. IBM provisions and manages the physical infrastructure in IBM-operated data centers.

Clients use storage in the form of local disks, network block storage (iSCSI), and network file storage (NFS). The choice of storage depends on workload requirements, performance needs, and availability considerations.

The storage options provide the following key capabilities:
- Local storage: High-performance local disks for boot and data
- Network block storage: Flexible, scalable iSCSI-based block storage with configurable IOPS
- Network file storage: NFS-based shared storage for multi-server access
- Point-in-time copies and cross-data center replication for disaster recovery
   - [Snapshots](/docs/BlockStorage?topic=BlockStorage-snapshots) and [replication](/docs/BlockStorage?topic=BlockStorage-replication) for block storage
   - [Snapshots](/docs/FileStorage?topic=FileStorage-snapshots) and [replication](/docs/FileStorage?topic=FileStorage-replication) for file storage


## Storage types
{: #types}

{{site.data.keyword.baremetal_short}} in Classic Infrastructure support multiple storage types that are designed for different workload requirements, performance characteristics, and use cases.

### Local storage
{: #local-storage}

Local storage provides the highest performance for SAP workloads and is included with every bare metal server. Local disks are physically attached to the server and provide low-latency access.

- Local disk options:
   - SATA drives: Cost-effective storage for less demanding workloads.
   - SAS drives: Enterprise-grade storage with better performance and reliability.
   - SSD drives: Solid-state drives for high-performance workloads.
   - NVMe drives: Ultra-high-performance storage for demanding SAP HANA workloads.

- [RAID](/docs/bare-metal?topic=bare-metal-bm-raid-levels) configurations:
   - RAID levels can be configured during server provisioning.
   - Common RAID levels include RAID 0 (striping), RAID 1 (mirroring), RAID 5, RAID 6, and RAID 10.
   - RAID 10 is possible for SAP HANA log and data volumes for redundancy and performance if required.

- Summary:
   - Provides the highest performance with the lowest latency.
   - Data is not portable between servers.
   - You are responsible for backup and data protection.
   - Storage capacity is fixed at provisioning time.

For more information, see [Classic Infrastructure storage options](/docs/sap?topic=sap-classic-env-introduction#classic-env-storage).

### Block storage
{: #block-storage}

{{site.data.keyword.blockstorageshort}} provides persistent, high-performance iSCSI storage that can be attached to authorized bare metal servers through failsafe, redundant multi-path I/O (MPIO) connections. Block storage is ideal for databases, applications that require consistent performance, and workloads that need portable storage.

For more information, see [Getting started with File Storage](/docs/FileStorage?topic=FileStorage-getting-started).

Block storage offers two types of storage:

| Storage type | IOPS/GB | Description |
|--------------|---------|-------------|
| Endurance | 0.25, 2, 4, 10 IOPS/GB | Pre-defined performance tiers suitable for workloads with well-defined performance requirements |
| Performance | 100 to 48,000 IOPS | Custom IOPS allocation for workloads with specific performance requirements |
{: caption="Block storage types" caption-side="bottom"}

#### Endurance storage tiers
{: #block-storage-endurance-tiers}

| Tier | IOPS/GB | Use case |
|------|---------|----------|
| 0.25 IOPS/GB | 0.25 | Low-intensity workloads, backups, archives |
| 2 IOPS/GB | 2 | General-purpose workloads, small databases |
| 4 IOPS/GB | 4 | High-intensity workloads, active databases |
| 10 IOPS/GB | 10 | Demanding workloads, SAP HANA, high-performance databases |
{: caption="Endurance storage tiers" caption-side="bottom"}

#### Block storage characteristics
{: #block-storage-characteristics}

- Capacity range: 20 GB to 12,000 GB per volume.
- IOPS range: 100 to 48,000 IOPS (Performance storage).
- Attached via iSCSI protocol.
- Can be detached and reattached to different servers (portable storage).
- Supports snapshots for point-in-time recovery.
- Supports replication to another data center for disaster recovery.
- Encrypted at rest by using provider-managed encryption.

### File storage
{: #file-storage}

{{site.data.keyword.filestorage_short}} provides NFS-based shared file storage that can be accessed by multiple servers simultaneously. This storage type is ideal for shared file systems such as `/sapmnt`, `/usr/sap/trans`, and other shared directories in SAP landscapes.

For more information, see [Getting started with File Storage](/docs/FileStorage?topic=FileStorage-getting-started).

File storage offers the same performance tiers as block storage:

| Storage type | IOPS/GB | Description |
|--------------|---------|-------------|
| Endurance | 0.25, 2, 4, 10 IOPS/GB | Pre-defined performance tiers suitable for workloads with well-defined performance requirements |
| Performance | 100 to 48,000 IOPS | Custom IOPS allocation for workloads with specific performance requirements |
{: caption="File storage types" caption-side="bottom"}

#### File storage characteristics
{: #file-storage-characteristics}

- Capacity range: 20 GB to 12,000 GB per volume
- IOPS range: 100 to 48,000 IOPS (Performance storage)
- Accessed via NFS protocol (NFS v3 and v4)
- Can be mounted by multiple servers simultaneously
- Supports snapshots for point-in-time recovery
- Supports replication to another data center for disaster recovery
- Encrypted at rest by using provider-managed encryption

The following table provides an exemplary overview about file systems on file storage:

| File system | Default path | Purpose or Scenario | Typical size (GB) | Description |
|-------------|--------------|-------------------|-------------------|-------------|
| Software repository | `/install` | Staging area for installers, media, kernels | Varies (typically 200–1000+) | Shared or local; depends on the content to be stored. |
| Installation path | `/hana/shared` | Shared files between hosts in an SAP HANA multi-host system | 1000 | SAP HANA runtime and client, configuration, and instance files. |
| SAP core path | `/usr/sap/<SID>` | Per-SID instance directories (profiles, kernels, work, logs) | 10–20 per AS instance | Size scales with the number of application server instances. |
| SAP Mount | `/sapmnt/<SID>` | Files shared between application servers | 20–50 | Contains shared data, profiles, and executable replication directory. |
| SAP Transport | `/usr/sap/trans` | Central SAP transport directory (cofiles, data, EPS inbox) | 50–200+ | Shared for all systems; grows with project activity or transport volume. |
{: caption="File storage" caption-side="bottom"}

### {{site.data.keyword.cos_full_notm}}
{: #cloud-object-storage}

{{site.data.keyword.cos_full}} supports the following use cases:
- Backup, restore, and archive or near-line storage purposes from {{site.data.keyword.baremetal_short}}.
- Long-term retention of audit logs and compliance data.
- Disaster recovery copies.

{{site.data.keyword.cos_full_notm}} buckets are available in different storage classes. For more information, see [Cloud Object Storage classes](/docs/cloud-object-storage?topic=cloud-object-storage-classes).

To access {{site.data.keyword.cos_full}} from your bare metal server, you can use:
- Public endpoints over the internet
- Private endpoints over the IBM Cloud private network for secure access

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

To plan storage volumes for SAP HANA, refer to the tables provided in [SAP HANA certified profiles on Bare Metal Servers in Classic Infrastructure](/docs/sap?topic=sap-hana-iaas-offerings-profiles-classic-intel-bm). Locate the compute profile applicable to your deployment, and review the storage configuration requirements.

#### Storage options for SAP HANA
{: #hana-storage-options}

1. **Local storage (recommended for highest performance)**
   - Configure `/hana/log` and `/hana/data` using local SSD or NVMe drives in RAID 10 for redundancy and performance.
   - Configure `/hana/shared` on local storage or File Storage.
   - Configure `/usr/sap` on local storage.

2. **Network block storage (for flexibility and portability)**
   - Use 10 IOPS/GB Endurance tier or Performance storage with sufficient IOPS.
   - Create multiple volumes and stripe them using LVM (type RAID0) to achieve the required throughput.
   - Minimum 10 Gbps network connection required for adequate performance.
   - Configure `/hana/log` and `/hana/data` with multiple block storage volumes striped for performance.
   - Configure `/hana/shared` using File Storage for multi-host configurations.

3. **Hybrid approach**
   - Use local storage for `/hana/log` and `/hana/data` for maximum performance.
   - Use File Storage for `/hana/shared` in multi-host configurations.
   - Use Block Storage for backup volumes.

The exact configuration depends on your specific bare metal profile and performance requirements. Always refer to the storage configuration tables in the SAP HANA-certified profiles documentation for your chosen profile.

## {{site.data.keyword.sap_app_server}} with IBM Db2 storage guidelines
{: #anydb-ibm-db2-storage-guidelines}

{{site.data.keyword.sap_app_server}} with IBM Db2 requires specific storage configurations to ensure optimal database performance. The storage layout depends on the bare metal server profile and the expected database size.

### Volume layout
{: #anydb-ibm-db2-layout}

For {{site.data.keyword.sap_app_server}} with IBM Db2, consider the following storage options:

1. **Local storage**
   - Use local SSD drives for database data files and log files.
   - Configure RAID 10 for redundancy and performance.
   - Separate data and log files onto different RAID arrays if possible.

2. **Network block storage**
   - Use 4 IOPS/GB or 10 IOPS/GB Endurance tier for database data volumes.
   - Use 2 IOPS/GB or 4 IOPS/GB Endurance tier for backup volumes.
   - Create multiple volumes and stripe them for increased throughput.
   - Minimum 10 Gbps network connection recommended.

3. **File storage**
   - Use for shared file systems such as `/sapmnt` and `/usr/sap/trans`.
   - Use 2 IOPS/GB or 4 IOPS/GB Endurance tier for most workloads.

## {{site.data.keyword.sap_app_server}} with SAP ASE storage guidelines
{: #ase-storage-guidelines}

SAP Adaptive Server Enterprise (ASE), formerly known as Sybase, requires specific storage configurations to ensure optimal database performance.

### Volume layout
{: #ase-layout}

For {{site.data.keyword.sap_app_server}} with SAP ASE, consider the following storage options:

1. **Local storage**
   - Use local SSD drives for database data files and log files.
   - Configure RAID 10 for redundancy and performance.
   - Separate data and log files onto different RAID arrays.

2. **Network block storage**
   - Use 4 IOPS/GB or 10 IOPS/GB Endurance tier for database data volumes.
   - Use 4 IOPS/GB Endurance tier for database log volumes.
   - Use 2 IOPS/GB Endurance tier for backup volumes.

3. **File storage**
   - Use for shared file systems such as `/sapmnt` and `/usr/sap/trans`.
   - Use 2 IOPS/GB or 4 IOPS/GB Endurance tier.

For more information, see:
- [SAP Help Portal - SAP Adaptive Server Enterprise (ASE)](https://help.sap.com/docs/SAP_ASE){: external}
- [SAP Community page for SAP Adaptive Server Enterprise (ASE)](https://pages.community.sap.com/topics/applications-on-ase){: external}

## {{site.data.keyword.sap_app_server}} storage guidelines
{: #app-server-storage-guidelines}

{{site.data.keyword.sap_app_servers}} have less demanding storage requirements compared to database servers. However, proper storage planning is still important for optimal performance.

### Volume layout
{: #app-server-layout}

For {{site.data.keyword.sap_app_servers}}, configure storage as follows:

- Configure `/usr/sap` on local storage or Block Storage with 2 IOPS/GB or 4 IOPS/GB Endurance tier.
- Use File Storage for shared file systems such as `/sapmnt/<SID>` and `/usr/sap/trans`.
- Use 2 IOPS/GB or 4 IOPS/GB Endurance tier for File Storage.

## References
{: #references}

- [About {{site.data.keyword.baremetal_short}} on Classic Infrastructure](https://www.ibm.com/products/bare-metal-servers/classic){: external}
- [Getting started with Block Storage](/docs/BlockStorage?topic=BlockStorage-getting-started)
- [Getting started with File Storage](/docs/FileStorage?topic=FileStorage-getting-started)
- [Cloud Object Storage classes](/docs/cloud-object-storage?topic=cloud-object-storage-classes)
- [SAP Note 2493172 - SAP HANA Hardware and Cloud Measurement Tools](https://me.sap.com/notes/2493172){: external}
- [SAP Help Portal: SAP HANA Tailored Data Center Integration](https://help.sap.com/docs/SAP_HANA_PLATFORM/eb3777d5495d46c5b2fa773206bbfb46/0b15a92d554c4941a452c9ca127f8c70.html){: external}
- [SAP Help Portal: SAP HANA Hardware and Cloud Measurement Tools guide](https://help.sap.com/docs/HANA_HW_CLOUD_TOOLS/02bb1e64c2ae4de7a11369f4e70a6394/7e878f6e16394f2990f126e639386333.html){: external}
- [SAP Learning Resources: Outlining how to use the Hardware Measurement Tool](https://learning.sap.com/courses/sap-hana-installation-and-administration/outlining-how-to-use-the-hardware-measurement-tool){: external}
- [SAP HANA Storage Requirements](https://www.sap.com/documents/2024/03/146274d3-ae7e-0010-bca6-c68f7e60039b.html){: external}
- [SAP Help Portal - SAP Adaptive Server Enterprise (ASE)](https://help.sap.com/docs/SAP_ASE){: external}
- [SAP Community page for SAP Adaptive Server Enterprise (ASE)](https://pages.community.sap.com/topics/applications-on-ase){: external}
- [SAP HANA certified instances on Intel-powered bare metal servers in Classic Infrastructure](/docs/sap?topic=sap-hana-iaas-offerings-profiles-classic-intel-bm)
- [SAP application server certified instances on Intel-powered bare metal servers in Classic Infrastructure](/docs/sap?topic=sap-nw-iaas-offerings-profiles-classic-bm)
