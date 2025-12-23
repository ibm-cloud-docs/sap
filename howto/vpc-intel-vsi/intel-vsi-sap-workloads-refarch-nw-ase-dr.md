---
copyright:
  years: 2025
lastupdated: "2025-12-23"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SLES, HADR
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# SAP NetWeaver 7.x with SAP ASE database on DR configuration
{: #sap-ase-db-intro}

The {{site.data.keyword.cloud}} architecture provides superior technical capabilities, such as a software-defined environment critical to a cloud infrastructure, programmable interfaces, and hundreds of hardware and network configurations. It is designed to deliver a higher level of flexibility by mixing virtual and dedicated servers to fit various workloads, automation of interfaces, and hybrid deployment options. The {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure offering provides you with a best fit selection of bare metal and virtualization-based servers on which the SAP software stack is run.

SAP ASE database is one of several databases that can be deployed on SAP NetWeaver in the {{site.data.keyword.cloud_notm}}. {{site.data.keyword.cloud_notm}} is certified for running SAP NetWeaver application servers like ABAP, Java, and SAP products based on these application server stacks.

The following reference architecture describes a possible implementation of a disaster recovery (DR) configuration in {{site.data.keyword.vpc_short}}, considering SAP NetWeaver Application Servers with SAP ASE database workloads runs on virtual server instances across two regions.

![Figure 1. SAP NetWeaver deployment in DR configuration across different regions](../../images/vpc-intel-vsi-arch-diagram-ase-dr.svg "SAP NetWeaver deployment in DR configuration across different regions"){: caption="SAP NetWeaver deployment in DR configuration across different regions" caption-side="bottom"}

Deployment of SAP system in a DR scenario is based on two system deployments in two separate {{site.data.keyword.vpc_short}} regions, which are geographically distant:

1. Installation or deployment is designated as a primary site, where all transaction processing take place (Primary Active).
2. Installation or deployment designated as a Secondary site (Secondary standby) which contains a copy of the databases from the primary server.

The following SAP technical components are deployed in this configuration:

* SAP ASE database - there are two installations on two VSIs and in 2 regions with asynchronous replication configuration support.
* SAP ASCS, Central Services, and PAS - the primary application server with dialog and batch processing.
* SAP Router (optional) - provides secured connections to the VSI and remote connections for support of the SAP AG remote services.

{{site.data.keyword.vpc_short}} services and components in support of SAP disaster recovery configuration:

* Network Services (VPN, Public gateway) - provides user access and internet connectivity.
* Jumphost - used to access, manage, and administer SAP VSI from the respective user zone, directly from their premises.
* {{site.data.keyword.tg_short}} - a service, which connects two VPCs from 2 regions and assures network connectivity between the private networks.
* {{site.data.keyword.dns_short}} - this is a global service, which permits connectivity on virtual names for SAP and ASE instances.

Clients on the customer-facing network (CFN) use a VPN service or a Floating IP address to access VSIs within the {{site.data.keyword.cloud_notm}}. Virtual server instances are hosted in availability zones (data centers) within the geographic regions.

The SAP virtual server instances can be in a separate security zone. The user connection to the jumphost follows the same rules as the direct connection from user premises to the virtual server instance SAP instances. The connection uses the Floating IP and security group 1 firewall rules from a designated public subnet. In this architecture, there are two security groups that are defined in every region; this arrangement is the simplest method for separating the public and private subnets. You can add more security groups if you require more isolation. The security groups rules must be customized in order to permit database and application connectivity between regions.

Storage can be local disks, or can be attached as File Shares. What is important here is that due to the nature of DB and application replication scenarios, no file systems are shared between the sites.

## Key aspects
{: #key-aspects-ase}

From a DR perspective, the purpose of separating the SAP application server from the ASE database host using a distributed installation mode is to allow some application server functionalities to continue even if the database host is unavailable. The two VSIs can be installed separately in two zones within the same region.

In the same time, the goal of this DR configuration is to maintain business continuity during a disaster, which brings down an entire site or region.

Due to the possible high network latency generated by the distant database connection, and in order to avoid any performance impact on the primary site, data replication can run in asynchronous mode.

SAP ASE database and the SAP Replication Server run on the same host in both regions. Also, the primary replication server runs and commits transactions without waiting for acknowledgments from the standby or secondary replication server.

![Figure 1. ASE database](../../images/vpc-intel-vsi-arch-diagram-ase.svg "ASE database"){: caption="ASE database" caption-side="bottom"}

There is also a data replication mechanism for the SAP application servers files, which are implemented by a “rsync” script. All application files that are generated on the primary site are available on the standby site. However, the secondary site application server is a mirror installation of the primary one and must be kept offline when the primary site is active.

When the ASE database failover takes place (the primary site is down and unusable) then a manual procedure and the new primary database is manually promoted. In this situation, there would be some data loss as some of the data will be stuck in the queue and not replicated to the standby site. When failover is run, no action must be taken regarding the file replication of the SAP application servers as this is synchronous due to “rsync” executable.

After a failover, the roles of the systems are reversed. That means, the DNS must be updated in order to bring up to date the virtual hosts name resolution. ASE virtual host and SAP Application virtual host will now point to the systems hosted in the secondary site, and now they have taken the primary role. When the manual failover is completed, users and related applications can reconnect to the same virtual host. As some DB data might be lost, the application investigation must be performed and some data must be re-created.

Bringing up again the former primary site can allow to setup a new replication configuration, the former primary can be now configured as the standby site. In order to achieve this, the former primary database must be rematerialized and replication restarted.

## Related information
{: #related-info-ase}

**SAP One support notes:**

* [SAP Note 84555 - Windows Server, Linux, and UNIX: Certified hardware](https://me.sap.com/notes/84855)
* [SAP Note 2927211 - SAP Applications on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211)
* [SAP Note 2923773 - Linux on {{site.data.keyword.cloud_notm}} (IaaS): Adaption of your SAP License](https://me.sap.com/notes/2923773)
* [SAP Note 2414097 - SAP Applications on {{site.data.keyword.cloud_notm}} Classic Infrastructure environment](https://me.sap.com/notes/2414097)
* [SAP Note 2369910 - SAP Software on Linux: General information](https://me.sap.com/notes/2369910)
* [2087322 - SYB: Where to find information about SAP applications on SAP ASE](https://me.sap.com/notes/2087322)
* [1650511 - SYB: High Availability Offerings with SAP Adaptive Server Enterprise](https://me.sap.com/notes/1650511)
* [1891560 - SYB: Business Suite Disaster Recovery Setup with SAP Adaptive Server Enterprise Always-On and SAP Replication Server](https://me.sap.com/notes/1891560)
* [2175528 - FAQ HADR with SRS certified for Business Suite](https://me.sap.com/notes/2175528)
* [SAP Note 171380 - Released IBM hardware (Intel processors) and {{site.data.keyword.cloud_notm}} services offers](https://me.sap.com/notes/171380)
* [SAP Note 1380654 - SAP support in IaaS environments](https://me.sap.com/notes/1380654)

**SAP best practice documents and guides:**

* [HADR Availability on SAP Adaptive Server Enterprise 16.0](https://community.sap.com/t5/technology-blog-posts-by-sap/hadr-availability-on-sap-adaptive-server-enterprise-16-0/ba-p/13209232)
* [SAP Business Suite running on SAP ASE](https://help.sap.com/docs/SUPPORT_CONTENT/sybase/3362696573.html)
* [HADR Users Guide](https://help.sap.com/docs/SAP_ASE/6ca21b96f7cb456fabb2b32b2121a6ae/a6645e28bc2b1014b54b8815a64b87ba.html?version=16.0.4.7)
* [HADR System with DR Node Users Guide](https://help.sap.com/docs/SAP_ASE/4bffede51dd7415680ec880c9bb63fd5/6bb9f5e240b14450bb1dff362ac46796.html?version=16.0.4.7)
