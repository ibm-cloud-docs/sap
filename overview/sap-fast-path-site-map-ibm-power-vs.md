---
copyright:
  years: 2020, 2025
lastupdated: "2025-04-22"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Fast path of IBM {{site.data.keyword.powerSys_notm}}
{: #fast-path-site-map-power-vs}

Use this collection of shortcuts for rapid access to key documentation about SAP solutions on IBM {{site.data.keyword.powerSys_notm}}.
{: shortdesc}

## Overview
{: #fast-path-power-vs-learn}

An Infrastructure-as-a-Service (IaaS) environment consists primarily of compute, storage, network, and virtualization components from a specified region (such as the US) and a designated zone or data center.
For more information, see [Architecture for IBM {{site.data.keyword.powerSys_notm}} in IBM data center](/docs/power-iaas?topic=power-iaas-on-cloud-architecture).
For information about the zones, see [IBM Cloud regions](/docs/power-iaas?topic=power-iaas-ibm-cloud-reg).

## Planning
{: #fast-path-power-vs-plan}

### SAP solution architecture
{: #fast-path-power-vs-plan-sap-architecture}

Your business and functional requirements determine the scope for your SAP solutions.
Consider your nonfunctional requirements in addition, and map the application components to the infrastructure components.

Refer to [SAP on Power Virtual Server](/docs/pattern-sap-on-powervs) for a reference architecture that details how to host SAP solutions on IBM {{site.data.keyword.powerSys_notm}}.

### Connectivity to IBM {{site.data.keyword.powerSys_notm}}
{: #fast-path-power-vs-plan-connectivity}

- [Connectivity options within the IBM {{site.data.keyword.powerSys_notm}} network, connection through IBM Cloud](/docs/sap?topic=sap-determine-access#powervs-network-connectivity-and-security)  

### Deployment
{: #fast-path-power-vs-plan-deploy}

- Compute
   - [Sizing process for SAP Systems](/docs/sap?topic=sap-sizing)
   - [Mapping CPUs derived from SAPS to an IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-planning-your-system-landscape#selecting-iaas-power)
   - [SAP HANA certified instances on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-hana-iaas-offerings-profiles-power-vs)
   - [SAP NetWeaver certified instances on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-nw-iaas-offerings-profiles-power-vs)
   - [OS for IBM {{site.data.keyword.powerSys_notm}}s](/docs/sap?topic=sap-compute-os-design-considerations#os-power)
   - [Bring-your-own-OS (custom OS image and BYOL License)](/docs/sap?topic=sap-compute-os-design-considerations#os-byos)



- Storage
   - [General storage configurations on IBM Power Virtual Server Infrastructure](/docs/sap?topic=sap-storage-design-considerations#sample-power)



### High availability
{: #fast-path-power-vs-plan-high-availability}

- [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-ha-overview)



### Disaster recovery
{: #fast-path-power-vs-plan-disaster-recovery}

- [Planning Disaster Recovery for SAP solutions on IBM Cloud](/docs/sap?topic=sap-disaster-recovery-design-considerations-overview)



## Tutorials
{: #fast-path-power-vs-tutorial}

### Deployment
{: #fast-path-power-vs-tutorial-deploy}

- [Accessing File Storage for VPC from IBM {{site.data.keyword.powerSys_notm}} instances](/docs/sap?topic=sap-ha-nlb-rt-nfs-intro)

## How to
{: #fast-path-power-vs-how-to}

### Deployment
{: #fast-path-power-vs-how-to-deploy}

- Preparing the deployment
   - [Planning your deployment](/docs/sap?topic=sap-powervs-planning-items)
   - [Deploying IBM Cloud VPC infrastructure for {{site.data.keyword.powerSys_notm}} workloads](/docs/sap?topic=sap-powervs-set-up-vpc-infrastructure)
   - [Deploying SAP {{site.data.keyword.powerSys_notm}} workloads](/docs/sap?topic=sap-powervs-set-up-power-infrastructure)

- Running the deployment
   - [Deploying SAP applications on {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-powervs-set-up-power-instances)
   - [SAP license key with {{site.data.keyword.IBM_notm}} Power Systems Virtual Servers](/docs/sap?topic=sap-bring-your-own-sap-product-license#byol-netweaver-powervs)



### High availability
{: #fast-path-power-vs-how-to-high-availability}

- General preparation steps
   - [Creating instances for a high availability cluster](/docs/sap?topic=sap-ha-vsi)

- Cluster deployment in a single {{site.data.keyword.powerSys_notm}} workspace
   - [Implementing a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel)
   - [Configuring SAP HANA scale-up system replication in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-hana-sr)
   - [Configuring SAP HANA cost-optimized scale-up system replication in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-hana-sr-cost-optimized)
   - [Configuring SAP HANA active/active (read enabled) system replication in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-hana-sr-aa)
   - [Configuring SAP HANA multitier system replication in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-hana-sr-multitier)
   - [Configuring SAP HANA multitarget system replication in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-hana-sr-multitarget)
   - [Configuring high availability for SAP S/4HANA (ASCS and ERS) in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-ensa)
   - [Configuring an active-passive NFS server in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-nfs)

- Cluster deployment in a multizone region environment
   - [Implementing a Red Hat Enterprise Linux High Availability Add-On cluster in a multizone region environment](/docs/sap?topic=sap-ha-rhel-mz)
   - [Configuring high availability for SAP S/4HANA (ASCS and ERS) in a Red Hat Enterprise Linux High Availability Add-On cluster in a multizone region environment](/docs/sap?topic=sap-ha-rhel-ensa-mz)

### Backup and restore
{: #fast-path-power-vs-how-to-backup-restore}

- [Backup strategies for SAP HANA on IBM Power Virtual Server](/docs/sap?topic=sap-powervs-backup-strategies)



### Monitoring
{: #fast-path-power-vs-how-to-monitoring}

- Overview
   - [Getting started with IBM Cloud Monitoring for SAP systems](/docs/sap?topic=sap-mon-getting-started)
   - [Monitoring for {{site.data.keyword.IBM_notm}} Power Systems Virtual Servers](/docs/sap?topic=sap-planning-your-system-landscape#monitoring-power)

- Setting-up
   - [Prerequisites](/docs/sap?topic=sap-monitoring-prereqs)
   - [Creating a monitoring instance in {{site.data.keyword.cloud}}](/docs/sap?topic=sap-mon-create-instance)
   - [Setup and configuration of a monitoring host](/docs/sap?topic=sap-mon-exporter-setup-config)
   - [Configuration of Prometheus server metric forwarding](/docs/sap?topic=sap-mon-metric-forwarding)
   - [Launching the monitoring UI and working with dashboards](/docs/sap?topic=sap-mon-launch-web-ui-dashboards)

### Migration
{: #fast-path-power-vs-how-to-migration}

- [Overview - Migrating SAP servers between on-premises and {{site.data.keyword.cloud}} on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-sapmig-overview)
- [Hybrid Cloud Network Consideration for SAP applications on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-sapmig-hybrid-cloud-networking)
- [Migrating SAP S/4HANA to IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-sapmig-db-hana)
- [Migrating SAP ERP 6.0 with Oracle to IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-sapmig-db-oracle)
- [Migrating SAP ERP 6.0 with IBM Db2 to IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-sapmig-db-db2)
- [Migrating from SAP ERP 6.0 to S/4HANA to IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-sapmig-sum-dmo)



## Help
{: #fast-path-power-vs-help}

- [Getting help and support from {{site.data.keyword.cloud_notm}} or SAP](/docs/sap?topic=sap-help-support)
- [SAP-certified IBM {{site.data.keyword.powerSys_notm}}s](/docs/sap?topic=sap-faq-ibm-cloud-for-sap#faq-sap-certified-power-vsi)
