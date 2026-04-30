---
copyright:
  years: 2025, 2026
lastupdated: "2026-04-29"
keywords: SAP, FlashSystem, Power, storage tiers, IOPS, log, data, shared, cos, file storage, boot, block storage, sapmnt, plan
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Storage infrastructure for SAP workloads on {{site.data.keyword.powerSys_notm}}
{: #plan-storage-powervs}

This document provides guidance on planning storage infrastructure requirements for SAP workloads on {{site.data.keyword.powerSysFull}}.
{: shortdesc}

For SAP workloads hosted on a cloud service provider, the infrastructure as a service (IaaS) layer operates in a similar way to environments used to run SAP workloads in external data centers.
SAP systems in a landscape have specific storage requirements to support performance, availability, and data protection.

{{site.data.keyword.ibm_cloud_sap}} provides a rich set of capabilities that support these storage requirements and extend the SAP landscape beyond basic system hosting.

The following sections describe {{site.data.keyword.ibm_cloud_sap}} portfolio design considerations for storage on {{site.data.keyword.powerSysFull}}.

## Overview
{: #overview}

The storage architecture for {{site.data.keyword.powerSys_notm}} operates independently from {{site.data.keyword.vpc_full}} (VPC) and {{site.data.keyword.cloud_notm}} Classic Infrastructure.
IBM provisions, maintains, and manages the entire storage infrastructure in IBM‑operated data centers.

IBM {{site.data.keyword.powerSys_notm}} uses SAN-attached IBM FlashSystem&reg; storage arrays to deliver enterprise-grade, low-latency, and high-performance capabilities.
For more information, see [IBM FlashSystem&reg;](https://www.ibm.com/flashsystem){: external}.

Clients use storage in the form of boot volumes and data volumes.
IBM manages all underlying physical storage hardware.

The storage volumes provide the following key capabilities:
- Configurable storage tiers: You can provision volumes across multiple performance tiers.
- Flexible Sizing: You can increase the capacity of a volume after creation.
- Replication support: You can configure replication policies for volumes.
- Placement Rules: You can apply affinity and anti-affinity rules to optimize performance or resilience.
- Shareability: You can attach volumes to one or more {{site.data.keyword.powerSys_notm}} instances for different workload designs.

## Storage types
{: #types}

{{site.data.keyword.powerSys_notm}} supports multiple storage types that are designed for different workload requirements, performance characteristics, and use cases.


### Block storage
{: #block-storage}

Block storage is used for {{site.data.keyword.powerSys_notm}} instances and provides predictable performance based on input/output operations per second (IOPS). It is the primary storage type for operating system boot volumes and additional data volumes.

Boot and data volumes
:   Boot and data volumes have the following properties:

    - When you create a {{site.data.keyword.powerSys_notm}} instance, you specify the storage tier (Tier 0, Tier 1, Tier 3, or Fixed IOPS) for the boot volume.
    - All additional volumes that are provisioned during virtual machine creation are allocated from the same storage pool as the boot volume, regardless of the tier selected for those volumes.
    - You provision each storage volume at IBM {{site.data.keyword.powerSys_notm}} based on the IOPS that your workload requires.
    - Each volume has a storage tier that defines how many I/O operations per second (IOPS) can be started against that volume.
    - The IOPS for the storage tiers can scale according to the size of the volume (Tier 0, Tier 1, Tier 3), or by fixed IOPS allocations.

    Create storage volumes for SAP HANA&reg; systems after the deployment of the individual server instance is complete.
    {: note}

    The following table shows the supported storage tiers with corresponding IOPS.

    | Tier level | IOPS                          |
    |------------|-------------------------------|
    | Tier 0     | 25 IOPS/GB                    |
    | Tier 1     | 10 IOPS/GB                    |
    | Tier 3     | 3 IOPS/GB                     |
    | Fixed IOPS | 5000 IOPS, regardless of size |
    {: caption="Tier and IOPS mapping" caption-side="bottom"}

    Fixed IOPS volumes can be used only up to 200 GB, as 200 GB at 25 IOPS per GB (Tier 0) equals 5,000 IOPS.
    {: important}

Volume flexibility
:   The volume flexibility features include:

    - You can increase the size of an existing volume to manage data growth.
    - You cannot increase the boot volume size during provisioning. However, you can extend the boot volume after the {{site.data.keyword.powerSys_notm}} instance is deployed.
    - You can change the storage tier of an existing volume to adjust the I/O bandwidth. For more information, see [Storage tiers](/docs/power-iaas?topic=power-iaas-private-cloud-architecture#storage-tiers-spec-private-cloud).
    - Choose placement rule **affinity** to place storage volumes within the same storage pool for snapshots, cloning, and locality.
    - Choose placement rule **anti-affinity** to place storage volumes in different pools for high availability and mirroring.



### {{site.data.keyword.cos_full_notm}}
{: #cloud-object-storage}

{{site.data.keyword.cos_full}} supports the following use cases:
- Importing, exporting, storing, transferring, and staging OS images from {{site.data.keyword.powerSys_notm}} OS image management workflows
- Backup, restore, and archive or near-line storage purposes from {{site.data.keyword.powerSys_notm}} instances

{{site.data.keyword.cos_full_notm}} buckets are available in different storage classes. For more information, see [Cloud Object Storage classes](/docs/cloud-object-storage?topic=cloud-object-storage-classes).

A subnet in a {{site.data.keyword.powerSys_notm}} workspace does not provide direct private network access to {{site.data.keyword.cos_full}}.
To enable private access:
1. Create a Virtual Private Endpoint (VPE) in a VPC.
2. Configure network connectivity from {{site.data.keyword.powerSys_notm}} subnet to the VPE in the IBM VPC.

Communicating with the VPE gives secure access from your virtual server instance in {{site.data.keyword.powerSys_notm}} to {{site.data.keyword.cos_full}} over the private network.


### File storage
{: #file-storage}

{{site.data.keyword.powerSys_notm}} does not provide a native file storage solution.
Instead, use [File Storage for VPC](/docs/vpc?topic=vpc-file-storage-vpc-about), and access it by configuring network connectivity from your {{site.data.keyword.powerSys_notm}} workspace to VPC.
See [Accessing file storage shares for VPC](/docs/sap?group=file-storage-shares-for-vpc).

The table provides an overview about file systems on file storage:


| File system        | Default path       | Purpose / Scenario                                           | Typical size (GB)            | Description                                                           |
|--------------------|--------------------|--------------------------------------------------------------|------------------------------|-----------------------------------------------------------------------|
| Software repository| `/install`         | Staging area for installers, media, kernels                  | Varies (typically 200–1000+) | Shared or local; depends on the content to be stored.                      |
| Installation path  | `/hana/shared`     | Shared files between hosts in an SAP HANA multi-host system  | 1000                         | SAP HANA runtime and client, configuration, and instance files.     |
| SAP core path    | `/usr/sap/<SID>`   | Per‑SID instance directories (profiles, kernels, work, logs) | 10–20 per AS instance        | Size scales with the number of application server instances.          |
| SAP Mount          | `/sapmnt/<SID>`    | Files shared between application servers                     | 20–50                        | contains shared data, profiles, and executable replication directory.|
| SAP Transport      | `/usr/sap/trans`   | Central SAP transport directory (cofiles, data, EPS inbox)   | 50–200+                      | Shared for all systems; grows with project activity/transport volume. |
{: caption="File storage" caption-side="bottom"}

## SAP HANA storage guidelines
{: #hana-storage-guidelines}

When you provision storage for an SAP HANA system, you must adhere to SAP's mandatory storage requirements.

Refer to the following resources:

- [SAP HANA Tailored Data Center Integration](https://help.sap.com/docs/SAP_HANA_PLATFORM/eb3777d5495d46c5b2fa773206bbfb46/0b15a92d554c4941a452c9ca127f8c70.html){: external}
- [SAP Note 2493172 - SAP HANA Hardware and Cloud Measurement Tools](https://me.sap.com/notes/2493172){: external}.
- [HCMT guide](https://help.sap.com/docs/HANA_HW_CLOUD_TOOLS){: external}
- [Outlining how to use the Hardware Measurement Tool](https://learning.sap.com/courses/sap-hana-installation-and-administration/outlining-how-to-use-the-hardware-measurement-tool){: external}.

For file system sizes, see [SAP HANA Storage Requirements](https://www.sap.com/documents/2024/03/146274d3-ae7e-0010-bca6-c68f7e60039b.html){: external}

### Volume layout
{: #hana-layout}

To plan storage volumes for SAP HANA, refer to the tables provided in [SAP HANA certified instances on IBM Power Virtual Server](/docs/sap?topic=sap-hana-iaas-offerings-profiles-power-vs).
Locate the compute profile applicable to your deployment, and review the tabs in the table for the log file system, data file system, and shared file system configuration.
These tabs specify the required quantity of storage volumes, the storage tier, and the corresponding volume sizes for the file systems `/hana/log`, `/hana/data`, and `/hana/shared`.

- Configure **four** {{site.data.keyword.powerSys_notm}} storage volumes that provide a combined throughput of `more than 12,000 IOPS` for the SAP HANA `log file system`. The typical total capacity that is required for the SAP HANA log file system is up to `512 GB`. Stripe the logical volume for the `/hana/log` file system across all **four** storage volumes to achieve the required performance.
- Configure **four** {{site.data.keyword.powerSys_notm}} storage volumes that provide a combined throughput of `more than 8,000 IOPS` for the SAP HANA `data file system`. The typical total capacity depends on the memory size. Stripe the logical volume for the `/hana/data` file system across all **four** storage volumes to achieve the required performance.
- Configure **one** {{site.data.keyword.powerSys_notm}} storage volume, or file storage for VPC for the SAP HANA `shared file system /hana/shared`. Provide a minimum of `3,000 IOPS`.
- Configure **one** {{site.data.keyword.powerSys_notm}} storage volume for the `/usr/sap` file system on `Tier 0` or `Fixed IOPs` storage tier.
- Add more volumes for a backup or export file system.


## {{site.data.keyword.sap_app_server}} storage guidelines
{: #app-server-storage-guidelines}

To plan storage volumes for the {{site.data.keyword.sap_app_server}}, refer to the table in [{{site.data.keyword.sap_app_server}} certified instances on IBM Power Virtual Server](/docs/sap?topic=sap-nw-iaas-offerings-profiles-power-vs).
Then, review the **Storage** tab to identify the storage tier, and the size of the storage volume for the `/usr/sap` file system.

### Volume layout
{: #app-server-layout}

Configure an own {{site.data.keyword.powerSys_notm}} storage volume for the `/usr/sap` file system on `Tier 0` or `Fixed IOPs` storage tier, or create a directory in the root file system '/' of the boot volume.

## References
{: #references}

- [IBM FlashSystem&reg;](https://www.ibm.com/flashsystem){: external}
- [Storage tiers](/docs/power-iaas?topic=power-iaas-private-cloud-architecture#storage-tiers-spec-private-cloud)
- [File Storage for VPC](/docs/vpc?topic=vpc-file-storage-vpc-about)
- [Cloud Object Storage classes](/docs/cloud-object-storage?topic=cloud-object-storage-classes)
- [Accessing file storage shares for VPC](/docs/sap?group=file-storage-shares-for-vpc)
- [SAP HANA Tailored Data Center Integration](https://help.sap.com/docs/SAP_HANA_PLATFORM/eb3777d5495d46c5b2fa773206bbfb46/0b15a92d554c4941a452c9ca127f8c70.html){: external}
- [SAP Note 2493172 - SAP HANA Hardware and Cloud Measurement Tools](https://me.sap.com/notes/2493172){: external}
- [HCMT guide](https://help.sap.com/docs/HANA_HW_CLOUD_TOOLS){: external}
- [Outlining how to use the Hardware Measurement Tool](https://learning.sap.com/courses/sap-hana-installation-and-administration/outlining-how-to-use-the-hardware-measurement-tool){: external}
- [SAP HANA Storage Requirements](https://www.sap.com/documents/2024/03/146274d3-ae7e-0010-bca6-c68f7e60039b.html){: external}
- [SAP HANA certified instances on IBM Power Virtual Server](/docs/sap?topic=sap-hana-iaas-offerings-profiles-power-vs)
- [{{site.data.keyword.sap_app_server}} certified instances on IBM Power Virtual Server](/docs/sap?topic=sap-nw-iaas-offerings-profiles-power-vs)
