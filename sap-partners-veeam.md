---

copyright:
  years: 2020
lastupdated: "2020-09-21"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, Veeam Backup & Replication, Veeam Plug-in, SAP HANA Backup, SAP HANA Backint, SAP NetWeaver Backup, Backups, {{site.data.keyword.cloud_notm}} storage, {{site.data.keyword.blockstorageshort}}

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
{:deprecated: .deprecated}

# Veeam for SAP HANA backups
{: #veeam}

This document is out of date, it is being updated for Veeam 10.x and replaced in November-2020 with new content.
{:deprecated}

Veeam Backup & Replication can be provisioned and deployed from the {{site.data.keyword.cloud_notm}} catalog to provide protection for SAP HANA workloads on certified {{site.data.keyword.ibm_cloud_sap}} infrastructure. Veeam performs SAP HANA `Backint`-based backup and restore of databases and logs, and is lisited on the [SAP Certified Solutions Directory](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/solutions).
{: shortdesc}

## Veeam overview and considerations
{: #veeam-backup-replication-overview}

Below is high-level overview of how an SAP `Backint` database backup is performed:

![Figure 1. SAP HANA Backint database backup overview](/images/sap-partners-veeam_plug-in.png "SAP HANA Backint database backup overview"){: caption="Figure 1. SAP HANA Backint database backup overview" caption-side="bottom"}

Veeam Plug-in, which acts as a go-between for SAP HANA and Veeam Backup repository, is installed on the SAP HANA server. Veeam Backup & Replication is provisioned on either {{site.data.keyword.cloud_notm}} {{site.data.keyword.baremetal_short}} or {{site.data.keyword.cloud_notm}} Classic {{site.data.keyword.virtualmachinesshort}}.

{{site.data.keyword.cloud_notm}} Classic {{site.data.keyword.virtualmachinesshort}} are used for the sample deployment.
{: note}

Veeam Plug-in connects to the Veeam Backup & Replication server and creates a backup job. Once a backup job is requested, Veeam Plug-in starts a Veeam Transport Agent on the SAP HANA server and on the backup repository, which transports data to the backup repository. For more information, see [How Veeam Plug-in for SAP HANA Works](https://helpcenter.veeam.com/docs/backup/plugins/sap_hana_plugin.html?ver=95u4).

At this time, Veeam is able to provision with {{site.data.keyword.cloud}} for VMware Solutions Dedicated only.

### System considerations
{: #veeam-considerations}

Veeam Backup & Replication has a very high-performance data mover engine embedded within its components that is used for
* Reading VM data from production storage
* Applying compression/de-duplication
* Writing the resulting data stream to backup storage

The efficiency that it performs these operations is impacted by available resources, including network bandwidth, storage performance, and server processing capacity.

### Systems requirements
{: #veeam-system-requirements}

* **vSphere**. Veeam supports vSphere (ESXi) and vCenter environments v4.1 and greater. While Veaam is suited for backup of individual vSphere (ESXi) hosts, vCenter, if deployed, is the preferred interface into a vSphere environment. vCenter provides added configuration and ease of management.

  Veeam supports all OS types compatible with VMware.

  VMs with disks in the SCSI bus-sharing mode are not supported as VMware does not support snapshotting VMs with those types of disks.

  The following disk types are skipped from processing automatically and are not supported:
  - RDM virtual disks in physical mode,
  - independent disks,
  - disks connected via in-guest iSCSI initiator,
  - and VMs with pass-through virtual disks.

* **{{site.data.keyword.cloud_notm}} Classic {{site.data.keyword.virtualmachinesshort}}**. Veeam Backup & Replication components are provisioned on {{site.data.keyword.cloud_notm}} Classic {{site.data.keyword.virtualmachinesshort}}. Minimum requirements are
  * 4-core
  * 8 GB RAM
  * 1 Gb network uplink speed

  Veeam supports Windows Server 2012 and later, however, Windows Server 2016 is recommended for the advanced storage optimization and efficiency presented by the Resilient File System (ReFS).

* **Storage**. {{site.data.keyword.IBM_notm}} {{site.data.keyword.blockstorageshort}} Endurance block storage with LUN's from 1 TB to 2 TB, 0.25 IOPS/GB performance level is recommended for hosting the Veeam backup repository. You are encouraged to thoroughly test other performance levels before selecting the performance storage level for your production Veeam repository.

  [The Restore Point Simulator](https://rps.dewin.me/) can be used to help estimate your Veeam backup storage needs.

* **Throughput**. For the initial full backup, Veeam ignores empty or deleted VM storage blocks. For example, if a VM has been allocated 1 TB of storage, thin or thick provisioned, and only consumes 450 G, Veeam only processes 450 G for that VM. For ongoing incremental backups, Veeam only processes VM data blocks changed since the last backup.

  Based on preliminary results, the standard Veeam virtual server processes approximately 220 GB of VM data per hour, which means if total active VM data to be backed up is 1 TB, the initial backup takes approximately 4.5 hours. Based on a 10% per day VM change rate, the daily incremental backup takes approximately 45 minutes.

### Veeam Reference material
{: #veeam-references}

* [Veeam Backup & Replication Evaluator's Guide for VMware vSphere](https://helpcenter.veeam.com/evaluation/backup/vsphere/en/getting_started.html)
* [Veeam Support Knowledge Base](https://www.veeam.com/kb_search_results.html)
* [Veeam Backup & Replication Best Practices](https://bp.veeam.expert/)

