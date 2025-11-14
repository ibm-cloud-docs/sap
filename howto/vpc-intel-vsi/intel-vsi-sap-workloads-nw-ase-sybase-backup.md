---
copyright:
  years: 2025
lastupdated: "2025-11-13"
keywords:
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Snapshot backups for SAP on ASE Sybase
{: #snapshot-backup-ase-overview}

In modern SAP environments, ensuring data availability and minimizing downtime are critical for business continuity. A snapshot backup strategy is an efficient method that leverages fast, point-in-time copies of the SAP database volumes at the storage level. This approach significantly reduces the backup window by capturing consistent database states with minimal disruption to ongoing operations. In the IBM Cloud VPC environment, implementing snapshot backups allows SAP administrators to combine the benefits of cloud-native block storage snapshots with SAP’s database consistency requirements. This results in a robust, scalable backup solution that accelerates recovery times and supports disaster recovery objectives while optimizing storage utilization and operational efficiency.

## About Block Storage for VPC snapshots
{: #snapshot-ase-block-storage-about}

Block Storage for VPC snapshot is a regional offering that is used to create a point-in-time copy of your boot or data volume. The initial snapshot that you take is a full backup of the volume. Subsequent snapshots of the same volume are incremental, so they capture only the changes that occurred after the last snapshot was taken. You can restore data to a new volume during instance provisioning, from an existing instance, and when you create an unattached volume.
For more information on snapshots concepts, how it works, and its limitations, see [About Block Storage for VPC snapshots](/docs/vpc?topic=vpc-snapshots-vpc-about&interface=ui).

## Planning Block Storage for VPC snapshots
{: #snapshot-ase-block-storage-planning}

When you plan a snapshot strategy for your Block Storage for VPC volumes, you might find this checklist helpful. Remember, snapshots can be used to create copies of your volumes and the data they contain can be used to clone or replicate data to other availability zones and regions.
For more information on the prerequisites of a block storage snapshots, see [Planning Block Storage for VPC snapshots](/docs/vpc?topic=vpc-snapshots-vpc-planning&interface=ui).

## Creating Block Storage for VPC snapshots
{: #snapshot-ase-block-storage-create}

With UI, CLI, API, or Terraform you can create a snapshot of a first-generation Block Storage for VPC volume that is attached to a running virtual server instance. You can create a snapshot of a boot or a data volume. If the volume is not attached to a server instance, then you cannot create a snapshot.
For more information, see [Creating Block Storage for VPC snapshots](/docs/vpc?topic=vpc-snapshots-vpc-create&interface=ui).

### Creating a snapshot in the console
{: #snapshot-ase-block-storage-console}

In the console, you can create a snapshot of a Block Storage for VPC volume that is attached to a running virtual server instance.
For more information, see [Creating a snapshot in the console](/docs/vpc?topic=vpc-snapshots-vpc-create&interface=ui#snapshots-vpc-create-ui).

### Creating a snapshot from the CLI
{: #snapshot-ase-block-storage-cli}

**Requirement**: Before you can use the CLI, you must install the IBM Cloud CLI and the VPC CLI plug-in. For more information, see [CLI prerequisites](/docs/vpc?topic=vpc-set-up-environment&interface=cli#cli-prerequisites-setup).
Before you start, gather the following information:

* A unique name for the snapshot.
* The ID of the source volume.
* The resource group ID. You can't change the resource group after the snapshot is created.
* Any tags that you want to attach to the snapshot.

For more information, see [Creating a snapshot from the CLI](/docs/vpc?topic=vpc-snapshots-vpc-create&interface=cli).

### Creating a snapshot with the API
{: #snapshot-ase-block-storage-api}

You can create a snapshot by using the API.
Prerequisites for creating a snapshot with the API
You can create a snapshot by calling the VPC API. Before you start, gather the following information:
•	A unique name for the snapshot.
•	The ID of the source volume.
•	The resource group ID. You can't change the resource group after the snapshot is created.
•	Any tags that you want to attach to the snapshot.

For more information, see [Creating a snapshot with the API](/docs/vpc?topic=vpc-snapshots-vpc-create&interface=api).

### Creating a snapshot with Terraform
{: #snapshot-ase-block-storage-terraform}

You can create a snapshot by using Terraform. Before you start, gather the following information:

* A unique name for the snapshot.
* The ID of the source volume.
* The resource group ID. You can't change the resource group after the snapshot is created.
* Any tags that you want to attach to the snapshot.

**Requirement**: To use Terraform, download the Terraform CLI and configure the IBM Cloud® Provider plug-in.

For more information, see [Creating a snapshot with Terraform](/docs/vpc?topic=vpc-snapshots-vpc-create&interface=terraform).

## Restoring a volume from a snapshot
{: #snapshot-ase-block-storage-restore}

Restoring data from a snapshot creates a new, fully provisioned volume that you can use to start an instance or attach as auxiliary storage. You can restore boot and data volumes during instance creation or when you modify an existing instance. You can also create a stand-alone data volume from a snapshot. Volumes can be restored from snapshots that were created manually or by a backup policy. You can restore volumes from fast restore clones and cross-regional copies of snapshots, too. You can create volumes from snapshots in the console, from the CLI, with the API, or Terraform.
For more information, see [Restoring a volume from a snapshot](/docs/vpc?topic=vpc-snapshots-vpc-restore&interface=ui).

### Restoring volumes from snapshots in the console
{: #snapshot-ase-block-storage-restore-console}

You can create volumes from various pages in the IBM Cloud console. Restoring from a bootable snapshot creates a boot volume that you use to provision the virtual server instance. The boot volume uses a general-purpose profile and is limited to 250 GB. Data volumes are created and attached to the instance. You can restore volumes from a snapshot outside of instance provisioning as well, you can create stand-alone volumes and new auxiliary volumes for existing instances.

For more information, see [Restoring volumes from snapshots in the console](/docs/vpc?topic=vpc-snapshots-vpc-restore&interface=ui#snapshots-vpc-restore-ui).

### Restoring a volume from a snapshot from the CLI
{: #snapshot-ase-block-storage-restore-cli}

Restoring from a bootable snapshot creates a boot volume that you can use to provision a virtual server instance. The boot volume uses a general-purpose profile and is limited to 250 GB. Data volumes are created and can be attached to the instance. You can restore volumes from a snapshot outside of instance provisioning as well, you can create stand-alone volumes and new auxiliary volumes for existing instances.
**Requirement**: Before you can use the CLI, you must install the IBM Cloud CLI and the VPC CLI plug-in. For more information, see the CLI prerequisites.

For more information, see [Restoring a volume from a snapshot from the CLI](/docs/vpc?topic=vpc-snapshots-vpc-restore&interface=cli#snapshots-vpc-restore-CLI).

### Restoring a volume from a snapshot with the API
{: #snapshot-ase-block-storage-restore-api}

You can programmatically restore a volume during instance provisioning by calling the /instances method in the VPC API as shown in the following sample request. You can also create a stand-alone volume by calling the /volumes method in the VPC API.
Before you begin, gather information about the snapshot or snapshots that you want to use to restore a volume or volumes.

For more information, see [Restoring a volume from a snapshot with the API](/docs/vpc?topic=vpc-snapshots-vpc-restore&interface=api#snapshots-vpc-restore-API).

### Restoring a volume from a snapshot with Terraform
{: #snapshot-ase-block-storage-restore-terraform}

**Requirement**: To use Terraform, download the Terraform CLI and configure the IBM Cloud Provider plug-in. For more information, see Getting started with Terraform.

VPC infrastructure services use a specific regional endpoint, which targets to us-south by default. If your VPC is created in another region, make sure to target the appropriate region in the provider block in the `provider.tf` file.

For more information, see [Restoring a volume from a snapshot with Terraform](/docs/vpc?topic=vpc-snapshots-vpc-restore&interface=terraform#snapshots-vpc-restore-terraform).

## Backup and restore guidelines using snapshots
{: #snapshot-ase-block-storage-backup-restore}

This section describes the backup and restore guidelines using the snapshots.

### Backup guidelines
{: #snapshot-ase-block-storage-backup-guidelines}

The following steps outline the recommended procedure for preparing the database, capturing a snapshot in IBM Cloud VPC, and returning the system to normal operation:

* Put database in Quiesce mode.
* Create snapshots in IBM Cloud VPC for the relevant volumes.
* Release the database for write access.

### Restore guidelines
{: #snapshot-ase-block-storage-restore-guidelines}

The following steps outline the process of provisioning new volumes from snapshots, preparing the environment, and restoring the database to operational status:

* Create volumes from the snapshots taken during the backup procedure.
* Prepare the system for restore.
* Ensure ASE database is stopped.
* Unmount the existing database volumes (data and log).
* Deactivate the logical volumes and volume group.
* Detach the volumes from the virtual machines.
* Attach the newly created disk to the virtual machines.
* Activate the logical volumes and volume group (if not active).
* Mount the provisioned volume on the virtual machines.
* Verify the permission and ownership of the filesystem.
* Restore the database.

### Related information
{: #snapshot-ase-block-storage-related-info}

For additional information on backup and restore, refer to the [SAP notes](https://me.sap.com/notes/1887068).
