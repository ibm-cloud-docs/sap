---

copyright:
  years: 2020, 2025
lastupdated: "2025-02-07"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads

subcollection: sap

---

{{site.data.keyword.attribute-definition-list}}

# SAP NetWeaver design considerations
{: #netweaver-design-considerations}

It is important to carefully consider the configuration, deployment, and design of your SAP solution stack.

SAP NetWeaver-based systems can be deployed in two ways:
- A central system, which is a single-host installation (two-tier)
- A distributed system, which is a multi-host installation (three-tier); this option might be chosen for system scalability

You can choose to distribute the workload across multiple servers or keep the workload on one server for simplicity, depending on your business requirements.

## SAP system architecture models
{: #network-topology-system-tiering}

The architecture of SAP NetWeaver-based systems is based on a multitier client/server design that consists of three main layers: the presentation layer (user front-end), the application layer, and the database layer.

Two commonly architecture models are used for deploying SAP NetWeaver-based solutions.
- A two-tier architecture model refers to a separate presentation layer and an application and database layer.
   Both the application and the database are installed together on a single host.
- In the three-tier architecture model, the presentation, application, and database layers are installed on separate hosts.
   The three-tier architecture configuration is highly scalable.
   Multiple servers can be installed for the application layer, and in an SAP HANA scale-out implementation even for the database layer.

SAP NetWeaver Application Server ABAP systems consist of the following components, which can run on a single host or distributed across multiple hosts.
- ABAP Central Services (ASCS), which includes an ABAP message server and a Standalone Enqueue Server
- Primary Application Server (PAS), which includes an ABAP dispatcher process and ABAP work processes
- Additional Application Servers (AAS)

Since SAP NetWeaver release 7.5, the term PAS refers to the SAP application server that is installed first for the system.
The architecture of the PAS and the other application servers is the same (see [SAP Note 2360614 - Primary Application Server (PAS) Instance Directory renamed as of SAP Netweaver 7.50](https://me.sap.com/notes/2360614){: external}).
In previous releases, the ASCS services were integrated into the PAS instance and such instances were commonly referred to as the Central Instance.
{: note}

To create a high availability environment for SAP NetWeaver-based systems, distribute each of these components on separate hosts.
- Database server
- ABAP Central Services (ASCS)
- Enqueue Replication Server (ERS), which provides extra protection for the ASCS lock table
- PAS
- AAS

## Configuring high availability for SAP NetWeaver
{: #netweaver-ha}

{{site.data.keyword.cloud_notm}} provides automation for implementing selected high availability scenarios for SAP NetWeaver-based or S/4HANA deployments in {{site.data.keyword.cloud_notm}} VPC.

For details on available scenarios with automated deployment in {{site.data.keyword.cloud_notm}} VPC, see the following information.
- [SAP S/4HANA HA deployment on IBM Cloud VPC](/docs/sap?topic=sap-automate-sap-ha-deployment-overview).



Other high availability scenarios that aren't covered by the existing automation can be implemented based on the high availability solution available for your chosen operating system.

For a high availability configuration, you need to add extra hardware and software components to your landscape.

If you require extra software licenses, access to different software repositories, or both, contact [{{site.data.keyword.cloud_notm}} support](/docs/account?topic=account-using-avatar#getting-support) for assistance.

This configuration information applies to both the high availability software for SAP NetWeaver and the high availability software for your chosen relational database management system (RDBMS).
The setup procedures are no different from the setup procedures in an on-premises environment and require similar hardware and software configuration steps.

### Overview of SAP NetWeaver high availability configurations
{: #netweaver-ha-overview-configs}

A number of documents provide in-depth help on planning and installing an HA environment for SAP services.
The documents include information on failover, replication, scale-out, and disaster recovery (DR).
References to specific documents are provided where appropriate.

All operating systems and distributions that are supported by {{site.data.keyword.cloud_notm}} for an SAP solution deployment (Windows Server, Red Hat Enterprise Linux, and SUSE Linux Enterprise Server) come with high availability software and specific extensions.
The supported operating systems and distributions are described in these documents:

- [New Failover Clustering Improvements in Windows Server 2012 and Its Benefits for SAP NetWeaver High Availability](https://community.sap.com/t5/technology-blogs-by-sap/new-failover-clustering-improvements-in-windows-server-2012-and-its/ba-p/13023326){: external} provides a description based on Microsoft Windows Server Failover Clustering (WFSC) for SAP NetWeaver implementations.
- The following documents provide guidance on deploying SAP NetWeaver in a high availability Linux environment.
   - [Supported High Availability Solutions by SLES for SAP Applications](https://documentation.suse.com/sles-sap/sap-ha-support/html/sap-ha-support/article-sap-ha-support.html){: external}
   - [Red Hat HA Solutions for SAP HANA, S/4HANA and NetWeaver based SAP Applications](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html/red_hat_ha_solutions_for_sap_hana_s4hana_and_netweaver_based_sap_applications/index){: external}
   - [Building High Availability for SAP NetWeaver and SAP HANA on Linux](https://support.sap.com/content/dam/SAAP/SAP_Activate/AGS_70.pdf){: external} is an SAP best practice document and provides a detailed technical description with a strong focus on SAP HANA.


For more high availability products certified by SAP partners under the SAP Application Server High Availability Interface Certification Program, see [SAP High Availability - Certified HA-Interface Partners](https://help.sap.com/docs/SUPPORT_CONTENT/si/3362959202.html){: external}

For databases other than SAP HANA, refer to the documentation for your database for more information on high availability and disaster recovery configurations.

Shared access to one of these storage elements is required to support the failover of high availability systems:
- Network File System (NFS); _on-premises deployments can also use Common Internet File System (CIFS) storage_
- iSCSI-based storage

Local storage that is combined with a replication method is required to support DR system failover.

As with on-premises installations, consider the performance and latency requirements of the database product as part of your deployment planning.

### Configuring high availability in Classic Infrastructure
{: #netweaver-ha-classic}

The {{site.data.keyword.cloud_notm}} environment does not offer pre-configured high availability deployments for SAP solutions.
However, you can configure high availability scenarios based on the high availability solution that is available for your chosen operating system.

See [High availability and fencing considerations](#netweaver-ha-fencing) and [High availability and network considerations](#netweaver-ha-network) for lists of things that you need to consider for your deployment.
Apart from these considerations, configuring high availability for SAP NetWeaver and its database doesn't differ from other on-premises installations.

#### High availability and fencing considerations
{: #netweaver-ha-fencing}

To protect the integrity of shared resources in a high availability cluster, a fencing mechanism is required to isolate failing cluster nodes.
Intel processor-based clusters often use an Intelligent Platform Management Interface (IPMI) feature for fencing.
Due to the enterprise security implementation in the {{site.data.keyword.cloud_notm}} Classic Infrastructure environment, network-based access to remote management devices by using IPMI isn't available.

In the absence of an IPMI-enabled device, fencing mechanisms based on shared storage devices are used.
In an {{site.data.keyword.cloud_notm}} environment, shared storage devices are typically implemented by providing an iSCSI LUN to your servers.

For example, a File Share Witness (FSW) can be used on a Microsoft Windows cluster.
See [Managing quorum and witnesses](https://learn.microsoft.com/en-us/windows-server/failover-clustering/manage-cluster-quorum){: external} for information on configuring and managing quorum in Windows Server based deployments.

Linux-based clusters can use an SBD (Storage-Based Death or STONIT Block Device) based implementation for fencing.
For more information on cluster fencing and SBD, see [Pacemaker Explained - Fencing](https://clusterlabs.org/projects/pacemaker/doc/2.1/Pacemaker_Explained/singlehtml/index.html#document-fencing){: external} and [Using SBD With Pacemaker](https://projects.clusterlabs.org/w/fencing/using_sbd_with_pacemaker/){: external}.

{{site.data.keyword.cloud_notm}} block storage has built-in high availability features.
A single shared iSCSI LUN does not introduce a single point of failure (SPOF) as the network layout is redundant.
However, a specific cluster solution might require more than one shared device.

#### High availability and network considerations
{: #netweaver-ha-network}

An {{site.data.keyword.cloud_notm}} Classic Infrastructure environment-based installation comes with one of the following network configurations:
- Private network
- Public network
- Public and private networks
- Two private networks (on special request, depending on server type and physical hardware components configuration)

As with on-premises installations, extra network adapters can be ordered depending on the physical restrictions of the hardware.
The restriction is the same as for on-premises installations, which is the number of NIC cards that can fit into the server.

When deploying server hardware, avoid single points of failure in your network topology by ordering redundant network adapters.

Redundant adapters for bare metal servers are set up in a failover configuration by using Link Aggregation Control Protocol (LACP).
Bonding interfaces are used for Linux and teaming adapters are used for Microsoft Windows.
These setups provide a logical interface for redundancy and increased bandwidth.

When you deploy IBM Cloud for VMware Solutions, redundant adapters for VMware are set up using an NSX-T distributed switch.
This is in line with current VMware best practices for the _Software-Defined Data Center_ (see [VMware NSX-T design](https://cloud.ibm.com/docs/vmwaresolutions?topic=vmwaresolutions-nsx-t-design)){: external}.
Although subject to change, redundancy is configured by setting each _Distributed Switch_ to use the [Route Based on Originating Virtual Port](https://techdocs.broadcom.com/us/en/vmware-cis/vsphere/vsphere/6-7/vsphere-networking-6-7.html){: external} load balancing algorithm.
All included port groups use teaming over 2 uplinks (active: 0,1).

If you are deploying VMware vSphere on IBM Bare Metal in a manual installation that uses vSwitch, you can use LACP bonding of the physical NIC adapters.
This configuration choice depends on the need for increased throughput (for example bonding) versus redundant stability (for example load balancing with teaming).

The NIC adapters are connected to redundant switches, so no additional single point of failures is introduced.
The redundant infrastructure can be used by the ordered VLANs.

For some network requirements, such as disaster recovery replication scenarios, you need to consider the location of the connected devices and any new network requirements specific to the scenario.
Sometimes, the {{site.data.keyword.cloud_notm}} Classic Infrastructure's file or block storage with snapshot backups might fulfill your requirements.
Check with {{site.data.keyword.cloud_notm}} Support to determine which solution is best for your business needs.

### Configuring high availability in {{site.data.keyword.cloud_notm}} VPC
{: #netweaver-ha-vpc}

Review the documentation on automated high availability deployment at [SAP S/4HANA HA deployment on IBM Cloud VPC](/docs/sap?topic=sap-automate-sap-ha-deployment-overview).

For scenarios that are not covered by automation, or where the available automation doesn't fit, you can always implement a high availability solution manually.
In this case, the same information applies as in [Configuring high availability in Classic Infrastructure](#netweaver-ha-classic).

### Configuring high availability for IBM {{site.data.keyword.powerSys_notm}}
{: #netweaver-ha-power}

To implement high availability scenarios for SAP applications on IBM {{site.data.keyword.powerSys_notm}}, see [Implementing High Availability for SAP Applications on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-ha-overview){: external}.
