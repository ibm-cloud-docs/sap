---

copyright:
  years: 2021, 2025
lastupdated: "2025-02-06"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads

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
{:deprecated: .deprecated}


# SAP NetWeaver 7.x on Windows Servers with MS SQL on {{site.data.keyword.cloud}} VPC
{: #sap-refarch-nw-mssql}

MS SQL Server is one of several databases that can be deployed on SAP NetWeaver in the {{site.data.keyword.cloud}}. The most common architecture deployments are standard and distributed systems. {{site.data.keyword.cloud_notm}} is certified for running SAP NetWeaver application servers ABAP, Java, and SAP products based on these application server stacks.

The MS SQL Server database for SAP is supported only on Windows servers and using only the Enterprise Edition of the software. Other SQL Server editions are currently not supported.

## SAP NetWeaver architecture
{: #sap-netweaver-arch-mssql}

SAP NetWeaver is the core foundation of the SAP technology stacks and is the platform that is used for Advanced Business Application Programming (ABAP) and Java applications. SAP NetWeaver components are built on the SAP NetWeaver Application Server and are written in ABAP or Java Platform, Enterprise Edition. ABAP systems, Java systems, and dual-stack systems are distinct systems.

### Core platform features
{: #refarch-nw-msssql-core-platform-features}

SAP NetWeaver uses ABAP or Java core platforms to support the SAP applications. SAP NetWeaver:
*  Has application lifecycle management capabilities.
*  Provides the basic structure for the on-premises versions of SAP Business Suite and other applications, as an application server.
*  Is the foundation for the on-premises SAP S/4HANA next-generation business suite, with SAP HANA serving as the sole underlying database.

SAP provides a list of the [SAP versions](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#section_486851042){: external} to learn more about the versions available in {{site.data.keyword.cloud_notm}}. Each support package stack has a leading software component version. The support package level of each component version is a key part of the stack and a unique identifier for the support package stack.

### Installation types
{: #sap-netweaver-mssql-inst-types}

The three installation types for SAP NetWeaver Application Server are:
*	ABAP System– You can run ABAP programs and some SAP Java apps
*	Java System– You can run only Java Platform, Enterprise Edition apps. No ABAP programs can be run on a Java system
*	Dual Stack – You can run both ABAP and Java Platform, Enterprise Edition in separate instances

## Architecture diagram
{: #sap-netweaver-arch-diag-mssql}

This diagram shows the SAP NetWeaver 7.X on MS SQL Server database integrated with {{site.data.keyword.cloud_notm}} on the SAP NetWeaver 7.x architecture:

![Figure 1. Standard installation](../images/refarch-sap-mssql-std-all.svg "SAP NetWeaver 7.x with MS SQL Server standard installation with AAS"){: caption="SAP NetWeaver 7.x with MS SQL Server standard installation with AAS" caption-side="bottom"}

## Access from an external network
{: #sap-refarch-nw-mssql-access}

Clients on the customer facing network (CFN) use a floating IP to access virtual server instances within the {{site.data.keyword.cloud_notm}}. Virtual server instances are hosted in availability zones (data centers) within geographic regions.

Within the Public Subnet, the [SAP router](https://support.sap.com/en/tools/connectivity-tools/saprouter.html) and the jumphost provide secure connections to the virtual server instances. The SAP router is a software application that provides a remote connection between the customer's network and SAP. The SAP Router and jumphost are within a single security group with rules for inbound and outbound traffic between the private subnets in the zone. SAP routers are used with traditional SAP products and analytics solutions and offerings that are acquired from MS SQL Server database. For a comprehensive list of which SAP Business Analytics products benefits from SAP router connections, see [SAP Note 1478974](https://me.sap.com/notes/1478974){: external}.

A jumphost is used to access, manage, and administer SAP virtual server instances from the same customer ZONE directly from their premises. These SAP virtual server instances can be in a separate security zone but should be on same {{site.data.keyword.cloud_notm}} region. The customer connection to the jumphost follows the same rules as the direct connection from customer premises to the virtual server instance SAP instances. The connection uses the CFN IP and security group 1 firewall rules from a designated public subnet. In this architecture, there are two security groups defined; this arrangement is the simplest method for separating the public and private subnets. You can add more security groups if you require more isolation.

## Virtual server instances on SAP NetWeaver 7.x APAB stack, Java stack, and dual stack (ABAP+Java) stack on Windows Servers with MS SQL Server DB
{: #sap-refarch-nw-mssql-vsis}

### Standard system
{: #sap-refarch-nw-mssql-standard}

In a standard system, all main instances run on a single virtual server instance within a private subnet.
The virtual server instance has these components:

![Figure 2. Standard installation](../images/refarch-sap-mssql-std-only.svg "SAP NetWeaver 7.x MS SQL Server standard installation with AAS"){: caption="SAP NetWeaver 7.x MS SQL Server standard installation with AAS" caption-side="bottom"}

**Architecture of SAP NetWeaver AS for ABAP**

SAP tools create a PAS Instance and an ASCS Instance. This method is the standard for Java Stack (System) and is now standard for ABAP Stack.

1. The Primary Application Server (PAS) - An instance is an administrative unit that contains various components of an SAP system. The components of an instance are parameterized in a shared instance profile. Each instance is identified by a system ID and an instance number and includes:

   * [SAP Web Dispatcher](https://help.sap.com/saphelp_snc700_ehp01/helpdata/en/f9/e2350eca7f4a109eb0a7bc63135e27/frameset.htm) & Work Process (DIA,BTC,UPD,SPOOL) - The SAP Web Dispatcher lies between the internet and your SAP system. The SAP Web Dispatcher is the entry point for HTTP and HTTPs requests into your system, which consists of one or more SAP NetWeaver application servers. As a “software web switch”, the SAP Web dispatcher can reject or accept connections. When it accepts a connection, it balances the load to ensure an even distribution across the servers. The SAP Web Dispatcher contributes to security and also balances the load in your SAP system.

     You can use the SAP Web Dispatcher in ABAP and Java systems, in pure Java systems, and in pure ABAP systems.

   * [SAP Gateway Service](https://help.sap.com/saphelp_snc700_ehp01/helpdata/en/f9/e2350eca7f4a109eb0a7bc63135e27/frameset.htm) - The SAP Gateway carries out RFC services within the SAP world, which are based on [TCP/IP](https://help.sap.com/saphelp_snc700_ehp01/helpdata/en/35/26b431afab52b9e10000009b38f974/content.htm){: external}. These services enable SAP Systems and external programs to communicate with one another. RFC services can be used either in the ABAP program or for the external programs that use the interfaces. RFC can be used between processes of an instance or a system, or between systems.

   * [ICM (Internet Communication Manager)](https://help.sap.com/saphelp_snc700_ehp01/helpdata/en/f9/e2350eca7f4a109eb0a7bc63135e27/frameset.htm) Service - Application server component that receives and dispatches Web requests (HTTP(S), SMTP, …). ICM evaluates the URL and forwards requests to AS ABAP or AS Java.
   *  IGS (Internet Graphic Server)

2. The ABAP Central Services Instances (ASCS) – This instance contains the message server, the enqueue server, and a separate start. The ASCS instance cannot process any dialog requests. It is used to manage locks, exchange messages, and balance workload in the SAP system. The ASCS instance includes:

    * [Message Server](https://help.sap.com/saphelp_snc700_ehp01/helpdata/en/f9/e2350eca7f4a109eb0a7bc63135e27/frameset.htm) - The SAP message server runs as a separate process, mostly on the same host as the central instance. If an SCS instance (SAP Central Services) or ASCS instance (ABAP SCS) is configured in the system, the message server is part of this instance.
    * [Stand-alone Enqueue Server](https://help.sap.com/saphelp_snc700_ehp01/helpdata/en/f9/e2350eca7f4a109eb0a7bc63135e27/frameset.htm) - Part of the central instance (ABAP or Java)that manages the SAP locks. In combination with the enqueue replication server, this single point-of-failure can be made into a high availability solution.
    * ABAP Central services instance (ASCS instance) - Contains the ABAP message server and the stand-alone Enqueue Server
    * The enqueue replication server instance is only mandatory in a high-availability system.

Optionally, you can install the ASCS instance with an integrated:
* SAP Web Dispatcher. For more information, see [ASCS Instance with Embedded SAP Web Dispatcher](https://help.sap.com/docs/SLTOOLSET/910828cec5d14d6685da380aec1dc4ae/2e708e2d42134b4baabdfeae953b24c5.html?version=CURRENT_VERSION){: external}.
* Gateway. For more information, see [ASCS Instance with Embedded Gateway](https://help.sap.com/docs/SLTOOLSET/ce9e270ad34949969c16d09d1b099a26/bf1d359ac8384441a781ae3b0b5bd1b5.html?version=CURRENT_VERSION){: external}.

**Architecture of SAP NetWeaver AS for Java**

1. Java central instance (J< nn > instance) – A Java instance is a unit in the AS Java cluster that is identified by its instance number. The elements that form an instance that is run on one physical machine. Also, it is possible to run several instances on one physical machine, but it is recommended that you split the different instances among different physical machines. An [AS Java Cluster Architecture](https://help.sap.com/docs/SAP_NETWEAVER_750/5bdacafd0bbd41648f4b80093a1bf9d6/4b1bc9db0ae17394e10000000a42189b.html?version=7.5.4){: external} consists of:

    *  Internet Communication Manager (ICM) - The ICM is an element of the Java instance that handles requests coming from clients and dispatches them to the available server processes. Data is transferred from the ICM to the server processes and vice versa by using the Fast Channel Architecture (FCA), which allows fast and reliable communication between them
    *  One or several server processes - The server processes of AS Java run the Java application. They are responsible for processing incoming requests that are assigned to them by the ICM. Each server process is multi-threaded, and can therefore process many requests simultaneously.

2. System Central Services instance (SCS instance) - Central services form the basis of communication and synchronization for the AS Java cluster. They are responsible for lock administration, message exchange, and load balancing within the cluster. Central services that are run on one physical machine and constitute a separate instance. This [SAP Central Services Instance (SCS)](https://help.sap.com/docs/SAP_NETWEAVER_740/5bdacafd0bbd41648f4b80093a1bf9d6/480728f74c6a3837e10000000a42189b.html?version=7.4.19){: external} comprises:

    *  Message Server - The message server keeps a list of all server processes in the AS Java cluster and provides information about their availability to Internet Communication Manager (ICM). It also represents the infrastructure for data exchange between the participating server processes.
    *  Enqueue Server - The enqueue server manages logical locks. The enqueue server runs on the Central Services instance of the Java cluster. It manages the lock table in the main memory and receives requests for setting or releasing locks. It maps the logical locks to the database.

**MS SQL for standard system**

* Database instance (DB) - MS SQL Server in this case.   The SAP systems in a landscape have specific requirements for servers, operating systems, network setup, and supported storage. Deployment of SAP AnyDB on I{{site.data.keyword.cloud_notm}} is similar to deployments with infrastructure with on-premises data centers. Use the information that is provided from SAP and the RDBMS providers. For more information, see [AnyDB - Microsoft SQL Server](/docs/sap?topic=sap-anydb-ms-sql-server) and [Infrastructure certified for SAP](/docs/sap?topic=sap-iaas-offerings).
* Primary application server instance (PAS instance)  - The global directories of the ASCS instance can be used as the global file system. That means that the host with the ASCS instance is the SAP global host. However, you can also separately install the global directories on any host of your SAP system landscape. You can also use the SAP transport host or the host with the global file system (SAP global host) as your primary application server instance host. Optionally, you can install one or more additional application server instances.
* Additional Application Server (AAS) - You can install one or more additional application server instances for an existing SAP system. Additional application server instances are optional and can be installed on separate hosts.

    An additional application server instance can run on:
    * The host of any instance of the existing SAP system
    * On a dedicated host
* SAP Dialog Instance (DI) / Additional Application Instance (AAS) - Dialog Instance (DI) is an additional application instance on top of the Central Instance (CI). Normally the DI is set up on a different host.

    Dialog instance consists of Gateway (GW), Internet Communication Manager (ICM), and Dispatcher Process (Disp) only. The DI has no Message Server and Enqueue Work Process.

    DI always starts after the CI starts because the DI depends on CI as the main instance where message server and enqueue server exist. DI is used to balance the load and handle more workload rather than use only the Central Instance. The new name for DI is Additional Application Server (AAS).

    Structure:

    DI/AAS = GW + ICM + Disp

    For more information about configuring and adding a AAS instance in heterogeneous SAP environment, see [SAP Note 680617 - INST: Appl. Server in Heterogeneous SAP System Environment](https://me.sap.com/notes/680617){: external}.

    The benefit of an AAS and DI is to balance the load from the PAS instance by distributing a significant percent of the workload, to an additional DI and AAS server. With help of SAP load balancer mechanism, the AAS and DI provide good performance. Having an AAS and additional DI increases the processing power as well, using the resources of its new server capacity for all system business workload.

    For more information, see [SAP Note 26317 - Set up for LOGON group for autom load balancing](https://me.sap.com/notes/26317){: external}.

## Distributed system
{: #sap-refarch-nw-mssql-dist}

In a distributed system, there are multiple virtual server instances and every instance can run on a separate host:

![Figure 3. Distributed installation](../images/refarch-sap-mssql-dist-only.svg "SAP NetWeaver 7.x MS SQL Server distributed installation with AAS"){: caption="SAP NetWeaver 7.x MS SQL Server distributed installation with AAS" caption-side="bottom"}

The components in a distributed system are the same as the components in a standard system, but there are restrictions as to which instances can go on which hosts.

## Related information
{: #sap-refarch-nw-mssql-relinfo}

SAP One Support Notes that apply to this document:

* [SAP Note 84555 - Windows Server, Linux, and UNIX: Certified hardware](https://me.sap.com/notes/84855){: external}
* [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}
* [SAP Note 2923773 - Linux on IBM Cloud (IaaS): Adaption of your SAP License](https://me.sap.com/notes/2923773){: external}
* [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}
* [SAP Note 2369910 - SAP Software on Linux: General information](https://me.sap.com/notes/2369910){: external}
* [SAP Note 171380 - Released IBM hardware (Intel processors) and IBM cloud services offers](https://me.sap.com/notes/171380){: external}
* [SAP Note 1380654 - SAP support in IaaS environments](https://me.sap.com/notes/1380654){: external}

This document is referenced by:

* [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}
* [SAP Note 2588225 - SAP on IBM Cloud: Protect against speculative execution vulnerabilities](https://me.sap.com/notes/2588225){: external}
* [SAP Note 1380654 - SAP support in IaaS environments](https://me.sap.com/notes/1380654){: external}
* [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}
