---

copyright:
  years: 2020, 2024
lastupdated: "2024-11-28"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads

subcollection: sap

---

{{site.data.keyword.attribute-definition-list}}


# SAP NetWeaver design considerations
{: #netweaver-design-considerations}

It is important to carefully consider the design your SAP stack configuration, deployment, and database.

With an SAP NetWeaver system, you have two deployment options:
    * Central system, which is a single-host installation (two-tier)
    * Distributed system, which is a multi-host installation (three-tier or multitier); this is required to achieve high-availability redundancy

This initial deployment option decision to distribute your workload to different servers or keep the workload on one server for simplicity, has many other decisions which are taken to support the business requirements for the SAP Business Application.

While the deployment option impacts how SAP NetWeaver application server will operate, there are many other options which influence your server choice.

## SAP System Tiering approaches
{: #network-topology-system-tiering}

The SAP System Tiering approach defines the logical architecture of the SAP System or Systems.

At a high level, SAP System Tiering has numerous different approaches:
- A two-tier logical architecture SAP System refers to Client and that uses one host for Application and Database.
- A three-tier logical architecture SAP System refers to Client, Application (host 1), and Database (host 2).
- A multitier logical architecture SAP System refers to any permeation of Client, Application, and Database where HA and DR create further separation of components to create redundancy

For SAP NetWeaver (ABAP runtime) that uses two-tier or three-tier, the following components run on a single host:
- Central Services (ASCS) = Message (MS), Enqueue (EN)
- Primary Application Server (PAS) also known as Central Instance (CI) = ICM, Gateway (GW), ABAP Dispatcher (DI/WP)
- Application Servers (AS) = ABAP Dispatcher (DI/WP)

When you create a high-availability environment for SAP Netweaver, spit each of these components out across different hosts:
- Central Services (ASCS) = Message (MS), Enqueue (EN)
- Primary Application Server (PAS) also known as Central Instance (CI) = ICM, Gateway (GW), ABAP Dispatcher (DI/WP)
- Enqueue Replication Server Instance (ERS) = Enqueue (EN) with Replication Table (of the ASCS EN Lock Table)
- Additional Application Servers (AAS) = ABAP Dispatcher (DI/WP)

## High-availability configuration for SAP NetWeaver
{: #netweaver-ha}

The {{site.data.keyword.cloud_notm}} environment does not support any preconfigured high-availability (HA) scenarios. However, you can configure HA scenarios based on the HA extension for the operating system you choose. You add the HA extension by adding the required hardware and the required software components to your landscapes. If you require additional software licenses, access to different software repositories, or both contact [{{site.data.keyword.cloud_notm}} Support](/docs/get-support?topic=get-support-using-avatar#getting-support) to help you with setting up any additional requirements.

This configuration information applies to HA software for SAP NetWeaver and to the HA software for the relational database management system (RDBMS) you choose. For example, the replication portion of your high-availability and disaster-recovery mechanisms for your RDBMS. Setup procedures do not differ from any setup procedures in an on-premises environment and require the same hardware and software configuration steps.

### Overview of SAP NetWeaver high-availability configurations
{: #netweaver-ha-overview-configs}

Numerous documents provide in-depth help on planning and installing an HA environment for SAP services. The documents include information on failover, replication, scale-out, and disaster recovery (DR). References to specific documents are provided where appropriate.

All the operating systems and distributions that are supported by {{site.data.keyword.cloud_notm}} for an SAP deployment (Windows Server, RHEL, and SLES) come with high-availability software and specific extensions. The supported OS and distributions are described in these documents:

* [New Failover Clustering Improvements in Windows Server 2012 and Its Benefits for SAP NetWeaver High Availability](https://blogs.sap.com/2013/10/16/new-failover-clustering-improvements-in-windows-server-2012-and-its-benefits-for-sap-netweaver-high-availability/){: external} provides a description based on Microsoft Windows Server Failover Clustering (WFSC) on the Windows OS with SAP NetWeaver.

* Two references for guidance on deploying SAP NetWeaver in a Linux-based HA environment with Linux&reg; Pacemaker are:
    * SUSE SAP NetWeaver High Availability Cluster 7.40 Setup Guide
    * Deploying Highly Available SAP NetWeaver-based Servers Using Red Hat Enterprise Linux HA add-on with Pacemaker

* [Building High Availability for SAP NetWeaver and SAP HANA on Linux](https://support.sap.com/content/dam/SAAP/SAP_Activate/AGS_70.pdf){: external} is an SAP best-practice document and provides an in-depth technical description with a strong focus on SAP HANA.

* [SAP NetWeaver High Availability and Business Continuity in Virtual Environments with VMware and Hyper-V on Microsoft Windows](https://archive.sap.com/documents/docs/DOC-44415){: external} covers the virtualization aspects if you’re planning to implement HA on virtualized servers.

For more high-availability products certified by SAP partners for high-availability scenarios, see [High Availability Partner Information](https://wiki.scn.sap.com/wiki/display/SI/High+Availability+Partner+Information){: external}.

For non-HANA SAP databases, more information on HA fail-over and DR configurations, is available in your database documentation.

Supporting HA system failover usually requires shared access to either:
* Network File System (NFS) protocol and filesystem; _in on-premises deployments may also use Common Internet File System (CIFS) storage_
* iSCSI-based logical unique number storage (LUNS)

Supporting DR system failover usually requires Local storage that is combined with a replication method.

As with on-premises installations, check the performance and latency requirements of the database product when you plan your deployment.

### Configure high availability in Classic Infrastructure
{: #netweaver-ha-classic}

The {{site.data.keyword.cloud}} environment does not support any pre-configured high-availability (HA) scenarios for SAP. However, you can configure HA scenarios based on the HA extension for the operating system you choose.
{: shortdesc}

[HA and storage fencing considerations](#netweaver-ha-storage-fencing) and [HA and network considerations](#netweaver-ha-network) provide lists of things that you need to consider during your deployment. Apart from the considerations outlined here, installing SAP NetWeaver and its database system in an HA environment doesn’t differ from other on-premises installations.

#### HA and storage fencing (that is, Quorum-based) considerations
{: #netweaver-ha-storage-fencing}

Cluster environments typically use IPMI-based components for the “fencing of cluster nodes” to ensure a quorum. Due to enterprise security in the {{site.data.keyword.cloud_notm}} Classic Infrastructure environment, network-based access to remote management devices through the Intelligent Platform Management Interface (IPMI) isn’t available. Although IPMI is still used to manage the physical device if necessary.

In the absence of an IPMI-enabled device, shared storage devices are used.

In an {{site.data.keyword.cloud_notm}} environment, shared storage devices are implemented by deploying an iSCSI LUN to your servers as a quorum device.

For example, a File Share Witness (FSW) on a Microsoft Cluster, and for storage-based death (SDB) for Linux Pacemaker's Stonith.
* Click [here](http://www.linux-ha.org/wiki/Cluster_Concepts){: external} for more information on Linux High Availability Cluster Concepts.
* Click [here](https://docs.microsoft.com/en-us/windows-server/failover-clustering/manage-cluster-quorum){: external} for more information on configuring and managing quorum servers for Windows Server 2012 and Windows Server 2012 R2.

{{site.data.keyword.cloud_notm}} Block Storage has built-in HA capabilities, so a single shared iSCSI LUN does not introduce a Single Point Of Failure (SPOF) because your network layout is redundant. However, the specifics of your cluster product might require multiple quorum devices.

#### HA and network considerations
{: #netweaver-ha-network}

An {{site.data.keyword.cloud_notm}} Classic Infrastructure environment-based installation comes with one of the following network configurations:
* Private network
* Public network
* Public and private networks
* Two private networks (on special request, dependent on the server type and physical hardware components configuration)

Like on-premises installations, extra network adapters can be ordered depending on the physical restrictions of the hardware. The restriction is the same for on-premises installations, how many NIC cards can fit into the Bare Metal.

Ordering redundant network adapters during hardware deployments helps prevent single point of failures (SPOF) across your network topology.

Redundant adapters for Bare Metals are set up in a failover configuration with Link Aggregation Control Protocol (LACP). For Linux, the setup uses bonding interfaces, and teaming adapters are used for Microsoft Windows. This creates a logical interface for redundancy and increased bandwidth.

When using IBM Cloud for VMware Solutions, redundant adapters for VMware are set up by the VMware vSphere Distributed Switch (VDS) using either [VDS on NSX-V](https://cloud.ibm.com/docs/vmwaresolutions?topic=vmwaresolutions-nsx-v-design#nsx-v-design-distr-switch) or [VDS on NSX-T](https://cloud.ibm.com/docs/vmwaresolutions?topic=vmwaresolutions-nsx-t-design#nsx-t-design-distr-switch), in accordance with current VMware best practices for SDDC. While subject to change, redundancy is configured by setting every Distributed Switch with the [Route Based on Originating Virtual Port](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-959E1CFE-2AE4-4A67-B4D4-2D2E13765715.html) load balancing algorithm, with all contained Port Groups using Teaming across 2 uplinks (Active: 0,1). When using IBM Bare Metal with VMware vSphere for a manual installation using vSwitch, LACP bonding of the physical NIC adapters could be used. This configuration choice depends on the need for increased throughput (e.g. bonding) versus redundant stability (e.g. load balancing with teaming).

The NIC adapters are connected to redundant switches on, so no additional SPOFs are introduced. The redundant infrastructure can be used by the ordered VLANs.

For some network requirements, such as DR setup replication scenarios, you must check the location of the connected devices and any new network requirements specific to the software/application in question. In some cases, the {{site.data.keyword.cloud_notm}} Classic Infrastructure's File or Block Storage with snapshot backups might fulfill your requirements. Check with {{site.data.keyword.cloud_notm}} Support to determine which solution works best for your business needs.

### Configure high availability for IBM Power Infrastructure
{: #netweaver-ha-power}

This is a complementary offering from {{site.data.keyword.IBM_notm}} Power Systems, with low latency access to {{site.data.keyword.cloud_notm}} services.
{: note}

The {{site.data.keyword.cloud}} environment does not support any pre-configured high-availability (HA) scenarios for SAP. However, you can configure HA scenarios based on the HA extension for the operating system you choose.
{: shortdesc}

You add the HA extension by adding the required hardware and the required software components to your landscapes. If you require more software licenses, access to different software repositories, or both, contact [{{site.data.keyword.cloud_notm}} Support](/docs/get-support?topic=get-support-using-avatar#getting-support) to help you with setting up any additional requirements. In general, setup procedures do not differ from any setup procedures in an on-premises environment and require the same hardware and software configuration steps. For specific instructions on how to set up HA for {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}, see [High Availability and Disaster Recover options in {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}](/docs/power-iaas?topic=power-iaas-ha-dr).
