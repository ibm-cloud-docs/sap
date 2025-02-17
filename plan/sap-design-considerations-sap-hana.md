---
copyright:
  years: 2020, 2025
lastupdated: "2025-02-17"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# SAP HANA database design considerations
{: #hana-design-considerations}

It is important to consider the design of your SAP HANA configuration and deployment to ensure the SAP Business Applications use the full capabilities available with SAP HANA database server.

There are many decisions for SAP HANA design, which are taken to support the business requirements for the SAP Business Application. These design decisions for SAP HANA influence your infrastructure decisions.
In the table, some of the sample decisions for these SAP HANA design considerations in the high-level overview are explained in detail.

High-level overview breakdown of SAP HANA design considerations and example decisions:

| Design item | Example decision |
| --- | --- |
| Sizing Type | Standard sizing |
| Deployment Method | Appliance deployment |
| Deployment Type | MDC |
| System Type | Distributed, scale-out |
| Processing Type | OLAP, scale-out |
| Storage Type | Network File Storage (NFS) |
| Storage Filesystem | NFS mount points |
| High Availability fencing mechanism | STONITH |
| High Availability replication mode | SAP HANA System Replication, Full Synchronous replication within same Availability Zone or Data center |
| Disaster Recovery fencing mechanism | STONITH |
| Disaster Recovery replication mode | SAP HANA System Replication, Asynchronous replication to different Region |
| Backups | Backint native, daily complete backup + incremental backup every 30 minutes |
| SAP HANA Components | Live Cache Apps (LCAPPS), Extended Application Services Advanced (XSA - Cloud Foundry) |
{: caption="High-level overview breakdown of SAP HANA design considerations and example decisions" caption-side="top"}

## SAP HANA performance indicators and sizing
{: #hana-sizing-kpis}

You have various performance indicators which guide the design decisions for sizing and planning an SAP HANA deployment onto Cloud IaaS. Each of these performance indicators defined with consideration to meeting the business requirements, determine whether the infrastructure is suitable. These considerations include compute capacity, storage capacity and latency, network throughput and latency in addition to the design decisions for SAP HANA database server.

Examples of these performance indicators for SAP HANA include:

| Indicator | Description |
| --- | --- |
| **Memory** | * Leading factors for SAP Sizing (and cost of landscape + licenses)\n* Determined by data footprint (business and metadata in the column and row store) after compression and by extra SAP HANA components used (such as cache store) |
| **CPU** | * Compared to SAP AnyDB options, more CPU power is required to fully benefit from the parallel processing capabilities of SAP HANA for optimal response times.\n* The large parallelization in analytical scenarios influence on Response Times. Therefore, CPU requirement is more important for analytical scenarios.\n* Mixed transactional and analytic workloads are supported by SAP HANA but compete for shared resources. |
| **Disk capacity size**\n**Disk throughput (I/O)** | * Disk capacity that is required for data persistence for logging and cache data\n* Disk capacity size depends on the type of database store usage (such as row and column) \n* Sufficient I/O performance is required to enable processes to run with acceptable data throughput and storage system latency (that is, read/write from or to the disk) |
| **Network Load** | * Network throughput bandwidth in gigabits per second (Gbps):\n* _Amount of data transferred between SAP Application Servers and Database Servers_\n* _Amount of data transferred between SAP Application and End User_\n * Network latency roundtrip in milliseconds (ms):\n * _Time between SAP Application Servers and Database Servers_\n* _Time between hosts and any network-attached storage_\n* _Time between SAP Application and End User_ |
{: caption="Example performance indicators for SAP HANA" caption-side="top"}

## SAP HANA Sizing Type and Deployment Method
{: #hana-delivery-models}

Sizing type refers to the exercise of sizing SAP HANA, using either pre-defined or custom configurations.

Whereas the deployment method (sometimes referred as delivery model) refers to running IaaS certified for SAP HANA, which is either pre-defined or custom configurations.

Here is the summary of the Appliance and TDI deployment methods:

| **Appliance** | **TDI** |
| --- | --- |
| Application | Application |
| Database | Custom Database sizing (including CPU:DRAM ratios) |
| Linux Operating System | Select from defined range of supported Linux&reg; Operating System versions |
| _Virtualization (optional)_ | _Virtualization (optional)_ |
| Server | Server |
| Storage | Custom Storage |
{: caption="Appliance vs. TDI deployment methods" caption-side="top"}

The following sub-sections describe the appliance deployment method for Standard Sizing type, and the TDI deployment method for Expert Sizing. Detailed documentation regarding the methods and types are shown in SAP documentation:
- [SAP HANA Administration Guide for SAP HANA Platform](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/330e5550b09d4f0f8b6cceb14a64cd22.html){: external}
- [SAP HANA Server Installation and Update Guide](https://help.sap.com/docs/SAP_HANA_PLATFORM/2c1988d620e04368aa4103bf26f17727/7eb0167eb35e4e2885415205b8383584.html){: external}
- [SAP About Benchmarks - Sizing Types - Expert Sizing](https://www.sap.com/about/benchmark/sizing.expert-sizing.html){: external}
- [Expert Sizing & Methods of Sizing Validation](https://www.sap.com/documents/2016/10/822c3247-927c-0010-82c7-eda71af511fa.html){: external}
- [Sizing Methods and Tools](https://www.sap.com/documents/2016/10/6222114a-8f7c-0010-82c7-eda71af511fa.html){: external}

### Appliance deployment method for Standard sizing type
{: #hana-delivery-models-appliance}

#### Standard sizing type
{: #hana-delivery-models-appliance-sizing}

This term refers to a sizing exercise where pre-defined configuration sizes are defined based on hardware testing and t-shirt sizing for meeting specific benchmarks to arrive at a sizing result for the hardware requirements of an SAP application (such as network, CPU, Memory, Storage).

#### Appliance deployment method
{: #hana-delivery-models-appliance-deployment}

Supported hardware for SAP HANA depends on the deployment method. The appliance deployment method uses pre-defined validated SAP-optimized hardware by SAP-certified hardware partners that are running a specific operating system. These hardware options are offered in various configuration sizes.

Partners (such as Cloud Service Providers) offer appliances with multiple layers of redundant hardware, software and network components, which do not interrupt SAP HANA operations and defend against system outage. These components include:

* Redundant power supplies and fans and uninterrupted power supply (UPS)
* Enterprise grade error-correcting memories
* Fully redundant network switches and routers
* Disk storage systems that use batteries to guarantee writing even in the presence of power failure.
* Disk storage systems that use striping and mirroring for redundancy and recovery from disk failures.

In collaboration with SAP, a Cloud Service Provider defines the correct sizing when designing SAP-certified IaaS for SAP HANA with the appliance deployment method:

* Ensures maximum performance with the hardware capable of meeting specified workloads; providing dedicated memory for SAP HANA after the resident memory of OS and other programs is accounted for and with swapping to disk disabled.
* To maximize performance and throughput, SAP recommends that you scale up as far as possible (acquire the configuration with the highest processor and memory specification for the application workload) before scaling out (for deployments with greater data volume requirements).
* You can copy a database to machines from different SAP HANA appliance vendors with different hardware configurations, if both the source and target machines are compliant with the SAP HANA appliance specifications.

### TDI deployment method for Expert sizing
{: #hana-delivery-models-tdi}

#### Expert sizing type
{: #hana-delivery-models-tdi-sizing}

Expert sizing type refers to a sizing exercise where customer-specific data is analyzed and used to put more detail on the sizing result for the hardware requirements of an SAP application (such as network, CPU, Memory, Storage).

According to SAP, expert sizing typically includes "exploring some business processes in more detail, both on functional and technical level" (quotation source: [Sizing Types - Expert Sizing](https://www.sap.com/about/benchmark/sizing.html){: external}).

Therefore, with expert sizing, there are no standardized tools used to conduct the sizing and it will often require significant effort and SAP expertise. Projects that use expert sizing often use an external consulting and system implementation business partner to assist the internal SAP team.

For expert sizing, the following steps are likely to be performed (source: [Sizing Types - Expert Sizing](https://www.sap.com/about/benchmark/sizing.html){: external}):

- Identify the most important queries/apps/scenarios
- Identify, how they are used, for example, filter criteria, authorizations.
- Run these queries/apps/scenarios on representative test data (quality of test data and quantity of test data). Ideally, on a recent copy of the production data
- Measure resource consumption (CPU/memory) and response times
- Perform a forecast calculation based on the expected usage of the queries/apps/scenarios

#### TDI deployment method
{: #hana-delivery-models-tdi-deployment}

Supported hardware for SAP HANA depends on the deployment method. The TDI deployment method uses **custom-defined hardware** by SAP-certified hardware partners that use flexible OS or SAP HANA versions; these can be configured to any size (under the maximum configuration tested by SAP).

Partners (such as Cloud Service Providers) offer TDI with various configuration options and redundancy options. Thes options depend on whether you select scale-up or scale-out sizing, and must be installed by an appointed SAP HANA certified administrator. These **_may_** include:

* Redundant power supplies and fans and uninterrupted power supply (UPS)
* Enterprise grade error-correcting memories
* Fully redundant network switches and routers
* Disk storage systems use batteries to guarantee writing even in the presence of power failure
* Disk storage systems that use striping and mirroring for redundancy and recovery from disk failures

SAP and a cloud service provider agree to support the customer for a selected scale-up or scale-out sizing by using SAP-certified IaaS for SAP HANA with the TDI deployment method:

- This provides different system design options regarding scale-up and scale-out variations; the SAP HANA database must then be validated before use in Production systems that use the SAP HANA Hardware Configuration Check Tool for TDI testing when requested by the SAP Support organization.
- To maximize performance and throughput, SAP recommends that you scale up as far as possible (acquire the configuration with the highest processor and memory specification for the application workload) before scaling out (for deployments with greater data volume requirements).

## SAP HANA Deployment Types
{: #hana-deployment-types}

SAP HANA can be deployed in various layouts, with various configurations of abstraction and logical separation of database schemas. Different deployment types are designed for different use cases, and SAP defines those which are approved (with/without restrictions) for production SAP Systems and those which are not approved. See detailed information here on [SAP HANA Deployment Types - SAP HANA Server Installation and Update Guide](https://help.sap.com/docs/SAP_HANA_PLATFORM/2c1988d620e04368aa4103bf26f17727/6483b71787f2439c907bd99036559e72.html){: external} and a summary of this information:

* Approved for production
    * Dedicated also known as. Single Application on One SAP HANA System (SCOS)
    * Multitenant Database Containers (MDC)
* Approved for production (with restrictions)
    * Virtualized Single Tenant - restrictions to the hypervisor; see [SAP Note 1788665 - SAP HANA Support for virtualized / partitioned (multi-tenant) environments](https://me.sap.com/notes/1788665){: external}
    * Multiple Applications on One SAP HANA System (MCOD) - supported only for approved applications; see [SAP Note 1661202 - Support multiple applications one SAP HANA database / tenant DB](https://me.sap.com/notes/1661202){: external}
    * Multiple SAP HANA Systems on One Host (MCOS)

Multi-SID hosted with the same physical host, requires significant attention to detailed tasks related to system administration and performance management. For more information, see [SAP Note 1681092 - Multiple SAP HANA systems (SIDs) on the same underlying servers](https://me.sap.com/notes/1681092){: external}
{: note}

## SAP HANA System Type
{: #hana-system-type}

System Types are listed by SAP on [SAP HANA System Types](https://help.sap.com/docs/SAP_HANA_PLATFORM/2c1988d620e04368aa4103bf26f17727/df16fa40b3dc4ed3b86e575ad6d1198d.html){: external} as:
* Single-host system - one SAP HANA instance on one host server
* Multi-node / distributed / scale-out cluster

A single-host system is the simplest system installation type. It is possible to run an SAP HANA system entirely on one host server and then scale the system up as needed.

A multi-node / distributed / scale-out cluster is a system installation across multiple host servers with a limit on the CPU/RAM for each host node and a limit on the number of host nodes that can be used. _Information on the maximum scale-out configurations, is listed in the [SAP HANA Hardware Directory - Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas){: external}._

### SAP HANA scale-out cluster
{: #hana-system-type-scale-out}

Use of scale-out is primarily designed for SAP BW/4HANA or SAP BW on HANA. The [Scale-up and Scale-out for SAP BW/4HANA](/docs/sap?topic=sap-bw4hana#bw4hana-hana-scaling) considerations at the application-layer is covered separately. These consideration are in addition to database-layer considerations described in the following sections.

It is important to note that if your SAP HANA database server nodes or SAP NetWeaver application server components are distributed across multiple availability zones and data centers, SAP will not support your SAP HANA scale-out cluster (also known as, SAP HANA multi-node system).
{: note}

{{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s does not support scale-out for either OLAP or OLTP processing types.
{: important}

#### Networking
{: #hana-system-type-scale-out-networking}

SAP HANA multi-node requires certain networks be in place to function. Before you order other components of your system, these networks must be set up correctly and together with the database nodes. Separation of the network flows/traffic can improve performance (that is, keeping high storage traffic separate from the user traffic) when more network interfaces are attached to the server.

**As a summary of the network separation, you need in SAP HANA scale-out cluster to have:**
* A client-side network, which connects the SAP Advanced Business Application Programming (SAP ABAP) application servers, SAP HANA Studio clients, and any other network client to the multi-node system. The network throughput and availability options depend on the environment and usage scenario of your SAP HANA multi-node system. Consider the amount of data transferred from and to the SAP HANA database, and the availability key performance indicators (KPIs), required for your application.
* A storage network, which connects to the Network Storage (File/NFS or Block/iSCSI dependant on infrastructure selection). The network throughput and availability options depend on the environment and usage scenario of your SAP HANA multi-node system. Consider the throughput and latency required to provide 10,000 IOPS is available to each SAP HANA node.
* An internode network for SAP HANA internal communication that is set up equivalent to the storage network. The internode network is only used for communication between nodes and the data transfer that might be required between the nodes during operations.

Within each environment is a separate networking design. The classic infrastructure environment network is the forerunner and is the most robust option of many traditional and physical networking concepts. The VPC Infrastructure environment network is a software-defined network. The IBM Power environment network (as a complementary offering from {{site.data.keyword.IBM_notm}} Power Systems) is designed with networking principles for enterprise-grade performance.

Given these environment networks are different, configuring extra NIC throughput changes for the different infrastructure options:
* **Bare Metal, on Classic Infrastructure network:**  To maximize performance and redundancy, the physical network interfaces (NIC) are provided with 10 Gbps and then provisioned with bonding using Link Aggregation Control Protocol (LACP). The switches are configured automatically when ordering redundancy on the physical NIC. _Additional NIC cards might be added, depending on the physical machine specificiation and physical switch availability of ports._
* **Intel Virtual Server, on VPC Infrastructure network:** To maximize performance and redundancy, up to 5 network interfaces (vNIC) on multiple Subnets can be added.
* **IBM {{site.data.keyword.powerSys_notm}}, on IBM Power Infrastructure network:** To maximize performance redundancy, multiple network interfaces (vNIC) attached to different VLANs (and their respective Subnets) can be added.
* **VMware for SAP, on Classic Infrastructure network....**
    * **IBM Cloud for VMware Solutions, on Classic Infrastructure network:** redundant adapters for VMware are set up by the VMware vSphere Distributed Switch (VDS) using [VDS on NSX-T](/docs/vmwaresolutions?topic=vmwaresolutions-nsx-t-design#nsx-t-design-distr-switch), in accordance with current VMware best practices for SDDC. While subject to change, redundancy is configured by setting every distributed switch with the [Route Based on Originating Virtual Port](https://techdocs.broadcom.com/us/en/vmware-cis/vsphere/vsphere/6-7/vsphere-networking-6-7.html) load balancing algorithm. All port groups used by the algorithm should be configured to use teamings across 2 uplinks (Active: 0,1).
    * **IBM Cloud Bare Metal with VMware vSphere (manual configuration)**, on Classic Infrastructure network: adapters are suggested to utilise the best practices, however the vSwitch could use LACP bonding of the physical NIC adapters

#### Scale-out storage
{: #hana-system-type-scale-out-storage}

Data is distributed across the multiple SAP HANA nodes, which are hosting the single database.

Follow the guidelines in [Sizing SAP HANA - SAP HANA Master Guide](https://help.sap.com/docs/SAP_HANA_PLATFORM/eb3777d5495d46c5b2fa773206bbfb46/d4a122a7bb57101493e3f5ca08e6b039.html){: external} to determine the required total storage capacity size for your target SAP HANA system.

The SAP HANA shared volume, and each of the data and log volumes, must be accessible to all nodes (which may be easier to allow network storage access to all nodes within the Subnet used for storage connectivity). There are specific performance criteria that must be met by the attached Network File System (NFS) volumes:
* `/hana/data/` and `/hana/log` volumes, individual volumes are required for each node with a minimum of 10 IOPS/GB
* `/hana/shared` volume, required to be shared across all nodes with a minimum of 10 IOPS/GB and recommended to increment further to 12 IOPS/GB

For Classic Infrastructure:
* Read [SAP HANA on NetApp FAS Systems with NFS)](https://docs.netapp.com/us-en/netapp-solutions-sap/bp/saphana-fas-nfs_introduction.html){: external} to assist configuration of your SAP HANA multi-node system.
* Use the following Network File System (NFS) mount options in `/etc/fstab` for each volume to mount - `rw,bg,hard,timeo=600,intr,noatime,vers=4,minorversion=1,lock,rsize=1048576,wsize=1048576`.

After you mount all of your volumes to all the nodes, your multi-node servers are configured and ready to install the SAP HANA multi-node database. Follow the steps in the [SAP HANA Server Installation and Update Guide)](https://help.sap.com/docs/SAP_HANA_PLATFORM/2c1988d620e04368aa4103bf26f17727/7eb0167eb35e4e2885415205b8383584.html){: external} to install an SAP HANA database of your required version.

## SAP HANA performance
{: #hana-performance}

After an SAP HANA database server is operational, it is important to inspect the performance to ensure it will meet your business application requirements. This is particularly important for any deployments using the TDI deployment method.

### SAP HANA performance validation
{: #hana-performance-validation}

The [SAP HANA Hardware and Cloud Measurement Tools (HCMT)](https://help.sap.com/docs/HANA_HW_CLOUD_TOOLS){: external} replaces the previous _SAP HANA HW Configuration Check Tool (HWCCT)_. The HCMT binary executable is run before an SAP HANA installation (commonly), and performs a series of automated tests which analyses the system performance.

The output of the HCMT execution, is a result archive file - `hcmtresult-[timestamp].zip`.

This HCMT result archive file is then uploaded to the [SAP HANA Hardware and Cloud Measurement Analysis (HCMA)](https://hotui-supportportal.dispatcher.hana.ondemand.com){: external} for detailed analysis.

For information about downloading, installing, and configuring the HCMT tool, see [SAP Note 2493172 - SAP HANA Hardware and Cloud Measurement Tools](https://me.sap.com/notes/2493172){: external}.

### SAP HANA overheads impact on available memory
{: #hana-performance-overhead-memory}

Every SAP HANA database server reserves a small allocation of memory for the operating system and other services required to operate.

**SAP provide a rule of thumb for these overheads:**
* Reserved for OS = 10% of the first 64 GB + 3% of all remaining memory
* Reserved for SAP HANA services and caches = 50 GB

The example demonstrates the net capacity for SAP HANA when using 4TB Memory (DRAM) after the memory reservation overheads have been taken into consideration:

| **Physical Memory** | **4096 GB DRAM** |
| -- | -- |
| Reserved for OS | 127 GB |
| Available for SAP HANA | 3969 GB |
| Reserved for SAP HANA services and caches | 50 GB |
| Net capacity available for SAP HANA data + temporary disk space | 3919 GB |
{: caption="Example of SAP HANA net capacity" caption-side="top"}

This is shown in more detail on [SAP Note 2296290 - New Sizing Report for SAP BW/4HANA](https://me.sap.com/notes/2296290){: external} under attachment SAPBW4HANA_Sizing_V2.6.4.pdf

## SAP HANA High Availability and Disaster Recovery (HA/DR)
{: #hana-ha}

The first requirement for SAP HANA High Availability (HA) and Disaster Recovery (DR), is to use the correct Operating System (OS) add-ons for SAP High Availability. Be sure to discuss OS for SAP HA details with {{site.data.keyword.cloud_notm}} Support before your deployment.

The OS supported and deployed by {{site.data.keyword.cloud_notm}} for running SAP HANA with HA/DR are:
* Red Hat Enterprise Linux (RHEL)
* SUSE Enterprise Linux Server (SLES)

The {{site.data.keyword.cloud_notm}} environment does not support any preconfigured high availability (HA) scenarios. However, it does let you implement HA solutions for SAP HANA through Red Hat Enterprise Linux HA extensions, in a similar manner to existing deployments using Traditional On-Premises data centers.

**SAP HANA System Replication (HSR)** is configured with an automated fail-over from one server to a replica, using various **replication modes** designed by SAP to fit:
* Different SAP Business Applications
* Different business risk acceptance of unplanned downtime
* Different infrastructure resiliency cost profiles

Refer to SAP documentation on SAP HANA System Replication (HSR) and OS vendor documentation on SAP HANA HA/DR; or consult SAP for recommendations on your landscape design for further clarity.

For more information on system replication, and network throughput and latency, see

- [How To Perform System Replication for SAP HANA - Version 5.4 January 2018](https://www.sap.com/documents/2017/07/606a676e-c97c-0010-82c7-eda71af511fa.html){: external}
- [Network Configuration for SAP HANA System Replication](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html){: external}
- [SAP Help - SAP HANA System Replication Guide](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/676844172c2442f0bf6c8b080db05ae7.html){: external}
- [Troubleshoot System Replication - SAP HANA Troubleshooting and Performance Analysis Guide](https://help.sap.com/docs/SAP_HANA_PLATFORM/bed8c14f9f024763b0777aa72b5436f6/caca75c6f0cf4204af9d38ae0fe2d379.html){: external}
- [SAP Note 1999880 - FAQ: SAP HANA System Replication](https://me.sap.com/notes/1999880){: external}
- [SAP Note 2057595 - FAQ: SAP HANA High Availability](https://me.sap.com/notes/2057595){: external}

For more information on setting up the HA cluster extensions of the OS, view the Linux vendor documentation.

SUSE Linux Enterprise Server for SAP:
- [SAP HANA System Replication Scale-Up - Performance Optimized Scenario](https://documentation.suse.com/sbp/sap-12/html/SLES4SAP-hana-sr-guide-PerfOpt-12/index.html){: external}
- [SUSE Linux Enterprise High Availability Extension](https://documentation.suse.com/sle-ha/15-SP6/){: external}

Red Hat Enterprise Linux for SAP:
- [Supported HA Scenarios for SAP HANA, SAP S/4HANA, and SAP NetWeaver](https://access.redhat.com/articles/4079981){: external}
- [Automated SAP HANA System Replication in Scale-Up in pacemaker cluster](https://access.redhat.com/articles/3004101){: external}
