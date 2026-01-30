---
copyright:
  years: 2026
lastupdated: "2026-01-23"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, Sybase
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# SAP NetWeaver 7.x on Db2 with DR (RHEL)
{: #sap-refarch-nw-dr-rhel}

The {{site.data.keyword.cloud}} architecture provides superior technical capabilities, such as a software definable environment critical to a cloud infrastructure, programmable interfaces, and hundreds of hardware and network configurations. It is designed to deliver a higher level of flexibility by mixing virtual and dedicated servers to fit various workloads, automation of interfaces, and hybrid deployment options. The {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure offering provides a best-fit selection of bare metal and virtualization-based servers on which the SAP software stack is run.

## Architecture diagram (Async Replication)
{: #sap-db2-dr-rhel-arch}

The reference architecture provides a possible implementation of Disaster Recovery (DR) solution for SAP NetWeaver 7.x with Db2 database in a Disaster Recovery (DR) configuration on {{site.data.keyword.cloud_notm}}, designed to ensure business continuity in the event of system failure. It integrates multiple layers, starting with the SAP application layer, where ASCS and PAS instances are actively running on the primary site and preinstalled but offline at the DR site for quick activation. The Db2 database is configured with high availability disaster recovery (HADR) in an asynchronous mode where the primary Db2 instances resides at the production site and a standby Db2 instance is maintained at the DR site. Transaction logs are replicated from the primary to standby, ensuring minimal performance impacts while maintaining an acceptable recovery point object (RPO).

![Figure 1. SAP Netweaver in DR configuration across different regions](../../images/vpc-intel-vsi-db2-dr-rhel-arch-diagram.svg "SAP Netweaver in DR configuration across different regions"){: caption="SAP Netweaver in DR configuration across different regions" caption-side="bottom"}

**{{site.data.keyword.tg_full_notm}}** - you can create single or multiple transit gateways to connect VPCs together. You can also connect your {{site.data.keyword.cloud_notm}} classic infrastructure to a {{site.data.keyword.tg_short}} that provides seamless communication with classic infrastructure resources. Any new network connected to the {{site.data.keyword.tg_short}} is automatically made available to every other network connected to it so that you can scale your network as it grows.

**SAP file share** - this has two main concepts, sharing files from SAP applications using features like share links, or using a shared file system (like an NFS share) for high-availability SAP installations.

**Sync/Async replication** - synchronous replication waits for a confirmation from the secondary system before committing a transaction, ensuring zero data loss but causing higher latency. Asynchronous replication commits the transaction on the primary without waiting for a response from the secondary, which results in lower latency but can lead to some data loss.

## Technical components
{: #sap-db2-dr-tech-components}

Db2 database is one of several databases that can be deployed on SAP NetWeaver in the {{site.data.keyword.cloud}}. {{site.data.keyword.cloud_notm}} is certified for running SAP NetWeaver application servers such as ABAP, Java, and SAP products based on these application server stacks.

The following SAP technical components are deployed in this configuration:

* Db2 database performs two installations on 2 virtual server instances (VSIs) across 2 regions, with asynchronous replication configuration support.
* SAP ASCS, Central Services, and PAS are the primary application server with dialog and batch processing.
* SAP router (optional) provides secured connections to the VSI and remote connections for support of SAP AG remote services.

Following are the {{site.data.keyword.vpc_short}} services and components in support of SAP disaster recovery configuration:

* **Network services** like VPN and Public Gateway provide user access and internet connectivity.
* **Jumphost** are used to access, manage, and administer SAP VSI from the respective user zone, directly from their premises.
* **{{site.data.keyword.tg_short}}** is a service that connects two VPCs from two regions and assures network connectivity between the private networks.
* **{{site.data.keyword.dns_short}}** is a global service which permits connectivity on virtual names for SAP and Db2 instances.

Customers on the customer-facing network (CFN) use a VPN service or a Floating IP address to access VSI within the {{site.data.keyword.cloud_notm}}. VSIs are hosted in availability zones (data centers) within the geographic regions.

The SAP VSI can be in a separate security zone. The customer connection to the jump host follows the same rules as the direct connection from customer premises to the VSI SAP instances. The connection uses the Floating IP and security group one firewall rules from a designated public subnet. In this architecture, there are two security groups defined in every region; this plan is the simplest method for separating the public and private subnets. You can add more security groups if you require more isolation. The security groups rules must be customized to permit database and application connectivity between regions.

Storage can be local disks or can be attached as file shares. Based on database and application replication scenarios, no filesystems are shared between the sites.

## Additional information
{: #additional-info-dr-rhel}

Using this pattern, you can set up a disaster recovery system for an SAP system running on the Db2 database. In a disaster situation, business should be able to continue within your defined recovery time objective (RTO) and recovery point objective (RPO) requirements:

* RTO is the maximum acceptable delay between the interruption of service and restoration of service. This determines what is considered an acceptable time window when service is unavailable.
* RPO is the maximum acceptable amount of time since the last data recovery point. This determines what is considered an acceptable loss of data between the last recovery point and the interruption of service.

For information on FAQs related to HADR, see [SAP note #1612105 - DB6: FAQ on Db2 High Availability Disaster Recovery (HADR)](https://launchpad.support.sap.com/#/notes/1612105). You need SAP portal credentials to access the SAP notes.

## Related information
{: #related-information-dr-rhel}

The following SAP Notes and documentation references need to be followed when planning the installation:

* [2369910 - SAP Software on Linux: General information](https://me.sap.com/notes/2369910){: external}
* [2414820 - SAP on {{site.data.keyword.cloud_notm}}: Support prerequisites](https://me.sap.com/notes/2414820){: external}
* [2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}
* [2578899 - SUSE Linux Enterprise Server 15: Installation Note](https://me.sap.com/notes/2927211){: external}
* [1707361 - Inst. Systems Based on NW 7.31 and Higher: UNIX Db2 for LUW](https://me.sap.com/notes/1707361){: external}
* [1275776 - Linux: Preparing SLES for SAP environments](https://me.sap.com/notes/1275776){: external}
* [1597355 - Swap-space recommendation for Linux](https://me.sap.com/notes/1597355){: external}
* [405827 - Linux: Recommended file systems](https://me.sap.com/notes/1597355){: external}
* [611361 - Hostnames of SAP ABAP Platform servers](https://me.sap.com/notes/611361){: external}
* [1680045 - Release Note for Software Provisioning Manager 1.0 (recommended: SWPM 1.0 SP42)](https://me.sap.com/notes/1680045){: external}
* [1168456 - DB6: Support Process and End of Support Dates for IBM Db2 LUW](https://me.sap.com/notes/1168456){: external}
* [2751085 - DB6: Using Db2 11.5 with SAP Applications](https://me.sap.com/notes/2751085){: external}
* [1555903 - DB6: Supported IBM Db2 Database Features](https://me.sap.com/notes/1555903){: external}
* [101809 - DB6: Supported Db2 Versions and Fix Pack Levels](https://me.sap.com/notes/101809){: external}
* [2841297 - DB6: Installing Fix Packs for Db2 11.5 (UNIX + Linux)](https://me.sap.com/notes/2841297){: external}
* [1289413 - DB2 - Import Fix Pack in HADR environment](https://me.sap.com/notes/1289413){: external}
* [2751102 - DB6: Db2 11.5 Standard Parameter Settings](https://me.sap.com/notes/2751102){: external}
* [1612105 - DB6: FAQ on Db2 High Availability Disaster Recovery (HADR)](https://me.sap.com/notes/1612105){: external}
* [1568539 - DB6: HADR - Virtual IP or Automatic Client Reroute](https://me.sap.com/notes/1568539){: external}
* [3100330 - DB6: Using Db2 HADR with Pacemaker Cluster Software](https://me.sap.com/notes/3100330){: external}
* [3100287 - DB6: Db2 Support for Pacemaker Cluster Software](https://me.sap.com/notes/3100287){: external}
* [3264518 - DB6: FAQ: How to rebuild DR database in HADR Env](https://me.sap.com/notes/3264518){: external}
* [3535916 - DB6: HADR PRMIARY node error "recv with errno 104" in the db2diag.log, with Pacemaker Cluster Manager](https://me.sap.com/notes/3535916){: external}
* [3472573 - DB6: Pacemaker cannot initiate a takeover in HADR](https://me.sap.com/notes/3472573){: external}
* [3214778 - DBT3514W db2prereqcheck utility failed to find the 32-bit library files](https://me.sap.com/notes/3214778){: external}
* [3322030 - DB6: Standby server shutdown after re-initializing](https://me.sap.com/notes/3322030){: external}

SAP best practices documents:

* [SAP Product Availability Matrix](https://userapps.support.sap.com/sap/support/pam){: external}
* [Preparing to install Db2 database servers](https://www.ibm.com/docs/en/db2/12.1.x?topic=servers-preparing-install-db2-database){: external}
* [System requirements for IBM Db2 for Linux, UNIX, and Windows](https://www.ibm.com/support/pages/system-requirements-ibm-db2-linux-unix-and-windows){: external}
* [Db2 Recommended file systems](https://www.ibm.com/docs/en/db2/12.1.x?topic=databases-recommended-file-systems){: external}
* [SAP NetWeaver Installation Guide on Unix: IBM Db2 for LUW](https://help.sap.com/doc/4f95f7ac741a1014956dd879c2537334/CURRENT_VERSION/en-US/db6_inst_71x_unix_abap.pdf){: external}
* [Db2 Database Administration Guide](https://help.sap.com/doc/7367f81b468e4480b3c550669b3534aa/CURRENT_VERSION/en-US/DB6_Admin_Guide.pdf){: external}
