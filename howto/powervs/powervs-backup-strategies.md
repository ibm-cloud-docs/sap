---

copyright:
  years: 2026
lastupdated: "2026-01-31"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, Backup, Compass, Cobalt Iron, Backint, agent, Cloud {{site.data.keyword.cos_short}}, COS
subcollection: sap

---

{{site.data.keyword.attribute-definition-list}}


# Backup strategies for SAP HANA on IBM {{site.data.keyword.powerSys_notm}}
{: #powervs-backup-strategies}

IBM Cloud offers a robust {{site.data.keyword.powerSysFull}} infrastructure to run SAP HANA, and this document outlines the steps and best practices for performing backups of SAP HANA database.

When deploying SAP HANA on {{site.data.keyword.powerSys_notm}} instance, it is essential to implement a reliable and efficient backup strategy to ensure the availability and recoverability of your data. Two primary methods are available for performing backups of SAP HANA database running on {{site.data.keyword.powerSys_notm}} instance:

1. [IBM SAP HANA Backint Agent for {{site.data.keyword.cos_full_notm}}](#powervs-backup-using-sap-backint-agent)
1. [Secure automated backup with Compass for SAP HANA](#powervs-backup-using-compass)


Both of these methods offer flexible, secure, and scalable solutions for backing up SAP HANA, and each has its own set of advantages based on your infrastructure and requirements.

## IBM SAP HANA Backint agent for {{site.data.keyword.cos_full_notm}}
{: #powervs-backup-using-sap-backint-agent}

* **Backint for SAP HANA** is an API that enables third-party solutions to directly connect their backup agents to the SAP HANA database.
* The **IBM Backint Agent for SAP HANA with {{site.data.keyword.cos_full_notm}}**, developed by IBM, is essential for performing backups of SAP HANA in compliance with SAP’s backup guidelines.
* IBM SAP HANA Backint agent is supported both on SUSE Linux Enterprise Server (SLES) and Red Hat Enterprise Linux (RHEL) platforms.

### Prerequisites
{: #powervs-backup-using-sap-backint-agent-prereqs}

Before you begin, ensure that the following prerequisites are met:

1. It is required to have an {{site.data.keyword.cos_full_notm}} (COS) instance. Within this instance, an {{site.data.keyword.cos_short}} Bucket is required. Refer to [{{site.data.keyword.cos_full_notm}}](/docs/cloud-object-storage?topic=cloud-object-storage-getting-started-cloud-object-storage) for more details.
   1. Log in to the [IBM Cloud console](https://cloud.ibm.com/){: external} and create an instance of **{{site.data.keyword.cos_full_notm}}**.
   1. Create a **bucket** where backups will be stored.
   1. Make sure to set the **bucket’s permissions** properly to control access.
   1. Obtain the **service credentials** (API key) required to authenticate your backups.
   1. Follow additional prerequisites mentioned in [github readme](https://github.com/IBM-Cloud/ibm-sap-hana-backint-cos/blob/main/README.md#prerequisites){: external}
1. Downloaded the latest release from [ibm-sap-hana-backint-cos github repository](https://github.com/IBM-Cloud/ibm-sap-hana-backint-cos/releases){: external}.

To improve the backup performance create an {{site.data.keyword.vpc_short}}(VPC) and create a [Virtual Private Endpoint gateway (VPE)](/docs/vpc?topic=vpc-ordering-endpoint-gateway&interface=ui) of type Cloud {{site.data.keyword.cos_short}}.
Add the virtual private endpoint IP address and name to the hosts file `/etc/hosts` on the {{site.data.keyword.powerSys_notm}} instance.
To access the VPE from the {{site.data.keyword.powerSys_notm}} instance, the VPC subnet and the {{site.data.keyword.powerSys_notm}} Workspace must be attached to the same {{site.data.keyword.tg_short}}.

### Installation and configuration of agent on host
{: #powervs-backup-using-sap-backint-agent-install}

1. For installation guide follow the [github readme](https://github.com/IBM-Cloud/ibm-sap-hana-backint-cos/blob/main/README.md#install-the-agent){: external}.
1. For configuring correct SAP HANA and backint agent parameters refer to the [github readme](https://github.com/IBM-Cloud/ibm-sap-hana-backint-cos/blob/main/README.md#configure-sap-hana-database-to-use-the-parameter-file){: external}.

Referenced in [SAP Note 3644731 -  Install and Configure IBM Backint agent for SAP HANA with IBM Cloud Object Storage](https://me.sap.com/notes/3644731){: external}.


## Secure automated backup with Compass for Linux
{: #powervs-backup-using-compass}

The Backup Offering is powered by Cobalt Iron Compass and is accessible from the IBM Cloud [catalog](https://cloud.ibm.com/catalog){: external}. The Backup Offering provides enterprise-class backup and restore features in a cloud-centric SaaS solution. Compass capabilities and security features, along with many other security functions provides protection and self-assessments to protect enterprise data and applications.

For more information, see [Cobalt Iron documentation](https://help.cobaltiron.com/getting-started-with-powervs-and-compass-commander/){: external}.

Cobalt Iron Compass protects various platforms, applications, and data classes. The Backup Offering includes the following unique features and functions for SAP HANA on {{site.data.keyword.powerSys_notm}}:

1. HDBackInt-integrated backup and restore of SAP HANA databases
1. HDBackInt-integrated backup and restore of SAP HANA redo log files
1. Support for the SAP HANA Cockpit for configuration, monitoring, and scheduling of backups

The Backup Offering provides various integrated security and operational features that includes:

1. Alerting, notifications, and ticketing features and integration
1. Automated auditing and validation of backup server landscape
1. Backup server automation that includes hands-free automation of all backup server tasks
1. Centralized policy management
1. Complete governance
1. Data reduction through compression and deduplication
1. Data replication across regions in IBM Cloud
1. Encryption of data in all phases from in-transit, to-storage, and at-rest
1. Extensive support for encryption, data immutability, and other security access controls
1. Multitenancy and unlimited sub-organizations
1. Role-based access control management.

{{../../../power-iaas/backup-strategies.md#baas-architechture}}


{{../../../power-iaas/backup-strategies.md#baas-deploy}}

Connectivity between {{site.data.keyword.powerSys_notm}} instances and the backup servers is established via a Transit Gateway connection to the backup VPC. Name resolution is for the backup server connections, which is also required. You can accomplish this using the agent system's /etc/hosts file, or by adding CNAME entries to your agent system's DNS server. These elements need to be deployed in your account (Transit Gateway and VPC provisioning and setup happens through automation when the Backup Offering is provisioned).
{: tip}

### Installation and configuration of agent on host
{: #powervs-backup-using-sap-compass-agent-install}

For detailed installation and configuration steps refer to the [SAP HANA agent setup process PDF](https://help.cobaltiron.com/wp-content/uploads/2024/01/CobaltIron-PowerVS-AgentSetup-SAPHANA-20240327.pdf){: external}

### Pricing
{: #powervs-backup-compass-pricing}

When you use the Backup Offering, you are billed monthly through {{site.data.keyword.BluSoftlayer_notm}} for the amount of data backed up for the region and are billed hourly. For more information about pricing plans, see [Cobalt Iron - Secure Automated Backup](https://cloud.ibm.com/catalog/services/cobalt-iron---secure-automated-backup-with-compass?catalog_query=aHR0cHM6Ly9jbG91ZC5pYm0uY29tL2NhdGFsb2cjYWxsX3Byb2R1Y3Rz){: external} page accessible from the {{site.data.keyword.BluSoftlayer_notm}} [catalog](https://cloud.ibm.com/catalog){: external}. You can generate an estimate of the cost based on your expected usage from the **Summary** pane.

{{../../../power-iaas/backup-strategies.md#baas-dcs}}

### Additional support
{: #powervs-backup-compass-support}

Support for the Backup Offering is provided by Cobalt Iron.

* For more information about the offering, see the [Cobalt Iron documentation](https://help.cobaltiron.com/getting-started-with-powervs-and-compass-commander/){: external}.
* For issues related to backup and restore, contact Cobalt Iron by opening a service ticket through `support.cobaltiron.com`.
