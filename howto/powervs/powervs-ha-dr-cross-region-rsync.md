---
copyright:
  years: 2023, 2026
lastupdated: "2026-05-26"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP HANA Disaster Recovery , High Availability, HA, Disaster Recovery, DR, RSYNC, rsync, SAP HANA Cross Region DR, SAP HANA Cross Account DR
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Preparing SAP solutions for disaster recovery on IBM {{site.data.keyword.powerSys_notm}} using `rsync`
{: #ha-dr-hana-rsync-overview}

This document outlines recommendations for implementing a disaster recovery solution using `rsync` to enable cross‑region data replication for SAP systems on {{site.data.keyword.powerSysFull}}.

## Prerequisites
{: #prerequisites}

Before you begin the `rsync` disaster recovery (DR) configuration, complete the following requirements:

- For information about planning a high availability (HA) and disaster recovery (DR) solution for your SAP system, see [Setting Up File Systems for a High-Availability System](https://help.sap.com/docs/SLTOOLSET/39c32e9783f6439e871410848f61544c/0bcb648db9fa4d9e950d8af046b09721.html?version=LATEST&locale=en-US){: external}. This resource provides an overview of the different types of SAP directories.
- Make sure that your SAP system is running correctly. Identify the SAP system that serves as the primary system because it is the source that is replicated to the secondary site. If you do not currently have an active SAP system deployed, you can provision one using [{{site.data.keyword.powerSys_notm}} for SAP HANA](/docs/sap-powervs).
- Back up the primary SAP system and use the backup to prepare the secondary system for the disaster recovery scenario.
- Make sure that the secondary SAP system has different subnets, so they don’t overlap with the primary SAP system.
- Plan your SAP secondary system workload using the information about [{{site.data.keyword.cloud_notm}} regions](/docs/power-iaas?topic=power-iaas-ibm-cloud-reg&locale=en) which shows locations for {{site.data.keyword.powerSys_notm}} and {{site.data.keyword.vpc_full}}. Ensure that you understand how each VPC zone aligns with each {{site.data.keyword.powerSys_notm}} zone.
- For information about selecting a certified instance profile, see [SAP HANA certified instances on {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-hana-iaas-offerings-profiles-power-vs).
- Set up a VPN server that is needed to build a cross-account connection. See the tutorial [Connect using a client-to-site VPN](/docs/powervs-vpc?topic=powervs-vpc-solution-connect-client-vpn) that explains details how to proceed with a clied-to-site VPN.

IBM {{site.data.keyword.powerSys_notm}} supports the Global Replication Services (GRS), which you can use as an alternative to `rsync`. For information about enabling and configuring GRS, see [{{site.data.keyword.powerSys_notm}} Global Replication Services](/docs/power-iaas?topic=power-iaas-getting-started-GRS&locale=en).

Even if you use the `rsync` solution, check the supported GRS paired regions in case you decide to switch to GRS in the future.

## Adding a cross-account connection
{: #cross-account-connection}

{{site.data.keyword.tg_full}} interconnects {{site.data.keyword.cloud_notm}} infrastructure workloads worldwide. For details, see [About {{site.data.keyword.tg_full_notm}}](/docs/transit-gateway?topic=transit-gateway-about) which describes {{site.data.keyword.tg_full_notm}} features and interconnectivity patterns for different use cases. For introductory guidance, see [Getting started with {{site.data.keyword.tg_full_notm}}](/docs/transit-gateway?topic=transit-gateway-getting-started).

To connect {{site.data.keyword.cloud_notm}} networks between across different accounts, see [Adding a cross-account connection](/docs/transit-gateway?topic=transit-gateway-adding-cross-account-connections&interface=ui).

You might need to adjust access control list (ACL) and security groups on both sides to establish interconnectivity. For more information, see [Planning considerations](/docs/transit-gateway?topic=transit-gateway-adding-cross-account-connections&interface=ui#tg-ca-planning).

## Setting up {{site.data.keyword.filestorage_vpc_full_notm}}
{: #config-nfs}

To synchronize data between the primary and secondary locations, mount file share storages on the {{site.data.keyword.vpc_full}} (VPC) instances. You cannot mount the file share directly on {{site.data.keyword.powerSys_notm}} instances. Therefore, create a {{site.data.keyword.nlb_full}} in each VPC. To set up the file storage shares, see {{site.data.keyword.cloud_notm}} documentation [Accessing VPC regional file storage shares from IBM Power Virtual Server instances](/docs/sap?topic=sap-ha-nlb-rt-rfs-intro).

For example, mount the remote file storage shares by using the following command:

```sh
mount -t nfs4 -o rw,sec=sys <HOST_IP:/MOUNT_TARGET> <MOUNT_POINT>
```
{: pre}

For more information, see the SAP documents [Exporting and Mounting Global Directories](https://help.sap.com/docs/SLTOOLSET/39c32e9783f6439e871410848f61544c/72fd513ee3b14f8e8cf302bcc5eb09ae.html?version=LATEST&locale=en-US){: external} and [Exporting and Mounting the Transport Directory](https://help.sap.com/docs/SLTOOLSET/39c32e9783f6439e871410848f61544c/c85bb0a1a25f49e386aefea5ddbb846e.html?version=LATEST&locale=en-US){: external}.

For details about {{site.data.keyword.cloud_notm}}&reg; {{site.data.keyword.nlb_full}} (NBL), see [About network load balancers](/docs/vpc?topic=vpc-network-load-balancers).

The following architecture diagram shows a cross-region scenario for NFS file storage shares for SAP workloads on {{site.data.keyword.powerSys_notm}}:

![Cross-region scenario of the NFS file storage shares for SAP workloads on {{site.data.keyword.powerSys_notm}}](../../images/powervs-ha-dr-architecture-nfs-rsync.svg){: caption="Cross-region scenario of NFS file storage shares for SAP workloads on {{site.data.keyword.powerSys_notm}}" caption-side="bottom"}

The NFS file shares, as shown in the diagram, are regional, and can be accessed from all three zones of the region. You can control access to the file shares by using security groups. For more information, see [About File Storage for VPC](/docs/vpc?topic=vpc-file-storage-vpc-about).

## Cross-region replication with rsync
{: #cross-region-replication}

To replicate data between cross-regions where your SAP workloads run, use the `rsync` tool. For this use case, `rsync` provides the following benefits:

- Checks whether files already exist in the secondary location and avoids overwriting all files in the secondary location.
- Saves time and bandwidth during transfer because only modified files are transferred between regions.
- Synchronizes data locally between different SAP instances and remotely to other NFS file storage shares and hosts.
- Supports automation by configuring `cron` jobs for automated data synchronisation between cross-regions.

The following diagram shows how to use the `rsync` tool to replicate the SAP file systems from the primary region to the secondary region.

If the SAP HANA local secure store (LSS) exists, include it in the disaster recovery (DR) scenario. Use the `lssbackup` utility to back up and restore LSS files. To check how to configure and use the `lssbackup` tool, see [LSS Backup Utility (lssbackup)](https://help.sap.com/docs/SAP_HANA_PLATFORM/b3ee5778bc2e4a089d3299b82ec762a7/08cfffc6fbf64f2a8fbcae1658da2704.html?locale=en-US&version=LATEST){: external}.

For an overview of SAP HANA default file systems, see [Recommended File System Layout](https://help.sap.com/docs/SAP_HANA_PLATFORM/2c1988d620e04368aa4103bf26f17727/4c24d332a37b4a3caad3e634f9900a45.html?locale=en-US&version=LATEST){: external}.

![SAP file system replication using rsync](../../images/powervs-ha-dr-rsync-cross-region-data-replication.svg){: caption="SAP file system replication using rsync" caption-side="bottom"}

To execute the `rsync` tool on the primary host, use the following command with your own parameters for the secondary host:

```sh
rsync -avzh --delete --log-file=/var/log/<LOG_FILE_NAME>.log <PRIMARY_HOST_MOUNT_DIRECTORY> <SECONDARY_HOST_USER_NAME>@<SECONDARY_HOST_IP>:<SECONDARY_HOST_MOUNT_DIRECTORY>
```
{: pre}

You can modify the `rsync` command by using another rsync options. To display the `rsync` statistics after execution, add the `--stats` option as shown in the following example:

```sh
rsync -avzh --delete --stats --log-file=/var/log/<LOG_FILE_NAME>.log <PRIMARY_HOST_MOUNT_DIRECTORY> <SECONDARY_HOST_USER_NAME>@<SECONDARY_HOST_IP>:<SECONDARY_HOST_MOUNT_DIRECTORY>
```
{: pre}

To schedule `rsync` automatically, you can configure a `cron` job. However, it is recommended to implement a shell script that checks whether the `rsync` is already running, to prevent multiple processes from overlapping with each others while executing `rsync`.

## Cross-region traffic management
{: #global-traffic-management}

If a disaster recovery (DR) event occurs, {{site.data.keyword.dns_full}} helps maintain high availability for your data traffic. If the primary DNS server fails, the traffic automatically reroutes to the secondary DNS server in a different region. For more information, see [Understanding high availability and disaster recovery for DNS Services](/docs/dns-svcs?topic=dns-svcs-dns-svcs-ha-dr).

To create an instance of the {{site.data.keyword.dns_full}}, see [Getting started with IBM Cloud DNS Services](/docs/dns-svcs?topic=dns-svcs-getting-started).

To achieve the HA globally across multiple regions, you might need to create a Global {{site.data.keyword.loadbalancer_short}}. For more information about how to set up and use this service, see [Working with global load balancers](/docs/dns-svcs?topic=dns-svcs-global-load-balancers).
