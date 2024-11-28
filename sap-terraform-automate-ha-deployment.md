---

copyright:
  years: 2023
lastupdated: "2023-09-05"

subcollection: sap 

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:ui: .ph data-hd-interface="ui"}
{:terraform: .ph data-hd-interface="terraform"}

# Automating SAP workload HA deployment on IBM Cloud VPC with Terraform and Ansible
{: #automate-ha-deployment-on-vpc}

You can use Terraform to automate {{site.data.keyword.cloud}} VPC provisioning. The VPC provisioned includes virtual server instances with high network performance. The VPC infrastructure contains various Infrastructure-as-a-Service (IaaS) offerings, including virtual servers. After the VPC is provisioned, the scripts use the Ansible Playbooks to install the SAP system.

## IBM Cloud VPC introduction
{: #automate-ha-deployment-on-vpc-intro}

VPC is a public cloud offering that an enterprise uses to establish its own private cloud-like computing environment on shared [public cloud](https://www.ibm.com/cloud/public) infrastructure. VPC gives an enterprise the ability to define and control a virtual network that is logically isolated from all other public cloud tenants, creating a private and secure place on the public cloud.

Imagine that a cloud provider’s infrastructure is a residential apartment building and multiple families live inside. Being a public cloud tenant is akin to sharing an apartment with a few roommates. In contrast, having a VPC is like having your own private condominium; no one else has the key, and no one can enter the space without your permission.

VPC’s logical isolation is implemented by using virtual network functions and security features that give the enterprise customer granular control over which IP addresses or applications can access particular resources. It is analogous to the “friends-only” or “public/private” controls on social media accounts used to restrict who can or can’t see your otherwise public posts.

With {{site.data.keyword.cloud_notm}} VPC, you can use the UI, CLI, and API to manually provision virtual server instances for VPC with high network performance. VPC infrastructure contains various Infrastructure-as-a-Service (IaaS) offerings including virtual servers for VPC. 

Use the following information to understand a simple use-case for planning, creating, and configuring resources for your VPC, and learn more about VPC overviews and VPC tutorials. For more information about the VPC, see [Getting started with Virtual Private Cloud (VPC)](/docs/vpc?topic=vpc-getting-started).

## SAP products architecture on {{site.data.keyword.cloud_notm}} VPC
{: #automate-ha-deployment-on-vpc-architecture}

A [Virtual Private Cloud (VPC)](https://www.ibm.com/cloud/learn/vpc?mhsrc=ibmsearch_a&mhq=VPC){: external} contains one of the most secure and reliable cloud environments for SAP applications within your own VPC with virtual server instances. This represents an Infrastructure-as-a-Service (IaaS) within {{site.data.keyword.cloud_notm}} that offers all the benefits of isolated, secure, and flexible virtual cloud infrastructure from IBM. In comparison, the {{site.data.keyword.cloud_notm}} classic infrastructure virtual servers offering uses virtual instances with native and VLAN networking to communicate with each other within a data center; however, the instances are restricted in one well-working pod by using subnet and VLAN networking as a gap scale up of virtual resources should rely between the pods. The {{site.data.keyword.vpc_short}} network orchestrator layer concept eliminates the pod boundaries and restrictions, so this new concept handles all the networking for every virtual instance running within VPC across regions and zones.

## Highly available system for SAP NetWeaver on {{site.data.keyword.cloud_notm}} VPC
{: #automate-ha-deployment-on-vpc-system-nw}

In a Highly Available (HA) system, every instance can run on a separate {{site.data.keyword.cloud_notm}} virtual server instance. The cluster HA configuration for the SAP application server consists of two virtual server instances, each of them located in the same zone within the region by using placement groups. Placement groups assure that both cluster resources and cloud resources are also located in different compute nodes as specified in the following placement groups section:

![Figure 1. SAP HA for SAP applications cluster nodes PAS (Active) and AAS (Active)](images/sap-ha-vpc-single-zone.svg "SAP HA applications cluster nodes PAS (Active) and AAS (Active)"){: caption="SAP HA for SAP applications cluster nodes PAS (Active) and AAS (Active)" caption-side="bottom"}

### Placement groups on {{site.data.keyword.cloud_notm}} VPC for SAP HA architecture
{: #automate-ha-deployment-on-vpc-placement-groups}

Placement Groups (PG) for VPC have two different anti-affinity strategies for high availability. By using the placement strategies, you minimize the chance of service disruption with virtual server instances that are placed on different hosts or into an infrastructure with separate power and network supplies.

The design of placement groups for {{site.data.keyword.cloud_notm}} virtual servers solves this issue. Placement groups give a measure of control over the host on which a new public virtual server is placed. In this release, a “spread” rule is implemented, which means that the virtual servers within a placement group are spread onto different hosts. You can build a highly available application within a data center and know that your virtual servers are isolated from each other.

Placement groups with the spread rule are available to create in selected {{site.data.keyword.cloud_notm}} data centers. After a spread rule is created, you can provision a virtual server into that group and ensure that it is not on the same host as any of your other virtual servers. This feature comes with no cost.

You can create your placement group and assign upto four new virtual server instances. With the spread rule, each of your virtual servers are provisioned on different physical hosts. In the following configuration example, the “Power Spread” option is used:

![Figure 2. Placement groups host spread](images/sap-terraform-ha-pg-1.png "Placement groups host spread"){: caption="Placement groups host spread" caption-side="bottom"}
![Figure 3. Placement groups power spread](images/sap-terraform-ha-pg-2.png "Placement groups power spread"){: caption="Placement groups power spread" caption-side="bottom"}

Following are the SAP instances that are required for HA scenario:

* ABAP SAP Central Services (ASCS) instance - contains the ABAP message server and the ABAP enqueue server.
* Enqueue Replication Server (ERS) instance for the ASCS instance.
* Database instance
* Primary Application Server (PAS) instance on node 1.
* Additional Application Server (AAS) instance on node 2.

It is recommended to run both the ASCS instance and the ERS instance in a switchover cluster infrastructure.
{: note}

### {{site.data.keyword.filestorage_vpc_full_notm}} for SAP HA architecture
{: #automate-ha-deployment-on-vpc-file-storage}

[{{site.data.keyword.filestorage_vpc_full_notm}}](/docs/vpc?topic=vpc-file-storage-vpc-about) technology is used to make the SAP directories available to the SAP system. The technologies of choice are NFS, shared disks, and cluster file system. If you have decided to use the HA solution for your SAP system, make sure that you properly address the HA requirements of the SAP file systems in your SAP environment.
![Figure 4. Fire shares for VPC](images/sap-terraform-ha-file-shares.png "File shares for VPC"){: caption="File shares for VPC" caption-side="bottom"}

* File shares that are mounted as NFS permanent file systems on both cluster nodes for SAP HA application:
    * `/usr/sap/<SAPSID>/SYS`
    * `/sapmnt<SAPSID>`
    * `/usr/sap/trans`
* Cluster-managed file systems for SAP HA application: ASCS
    * `/usr/sap/<SAPSID>/ASCS00`
    * `/usr/sap/<SAPSID>/ERS01`
* Permanent NFS mount on SAP HA application node 1 PAS instance:
    * `/usr/sap/<SAPSID>/Dxx`
* Permanent NFS mount on SAP HA application node 2 dialog instance:
    * `/usr/sap/<SAPSID>/Dyy`

#### Prerequisites
{: #automate-ha-deployment-on-vpc-file-storage-prerequisites}

You need to install the hardware (hosts, disks, and network) and decide how to distribute the database, SAP instances, and if required, the Network File System (NFS) server over the cluster nodes.

#### Context
{: #automate-ha-deployment-on-vpc-file-storage-context}

Following are the types of SAP directories:

* Physically shared directories: `/<sapmnt>/<SAPSID>` and `/usr/sap/trans`
* Logically shared directories that are bound to a node, such as `/usr/sap`, with the following local directories:
    * `/usr/sap/<SAPSID>`
    * `/usr/sap/<SAPSID>/SYS`
    * `/usr/sap/hostctrl`
* Local directories that contain the SAP instances such as `/usr/sap/<SAPSID>/ASCS<Instance_Number>`
* The global transport directory may reside on a separate SAP transport host as a standard three systems transport layer configuration.

You need at least two nodes and a shared file system for distributed ASCS and ERS instances. The assumption is that the rest of the components are distributed on other nodes.

#### ASCS and ERS installation
{: #automate-ha-deployment-on-vpc-file-system-ascs-ers-installation}

In order for the ASCS and ERS instances to be able to move from one node to the other, they need to be installed on a shared file system and use virtual hostnames based on the virtual IP.

In this VPC-based SAP HA solution, the shared file system that is required by the cluster is replaced by the NFS-mounted file storage, and the virtual IP is replaced by the {{site.data.keyword.alb_full}} (ALB).

In this scenario, three ALBs are used, one for each Single Point of Failure (SPOF) component in order to replace the virtual IP requirement: ALB for ASCS, ALB for ERS, and ALB for ASE Sybase. Each ALB is configured as a backend for the corresponding cluster servers and redirects all of the communication that is received on the front-end ports to the active server in the backend pool.

![Figure 5. Application load balancer management of HA IPs mechanism](images/sap-terraform-ha-load-balancers.png "Application load balancer management of HA IPs mechanism"){: caption="Application load balancer management of HA IPs mechanism" caption-side="bottom"}

### Private application load balancer
{: #automate-ha-deployment-on-vpc-private-application-load-balancer}

A [private application load balancer](/docs/vpc?topic=vpc-load-balancers&interface=ui#private-load-balancer) is accessible through your private subnets that you configured to create the load balancer.

Similar to a public application load balancer, your private application load balancer service instance is assigned an FQDN; however, this domain name is registered with one or more private IP addresses.

{{site.data.keyword.cloud_notm}} operations change the number and value of your assigned private IP addresses over time, based on maintenance and scaling activities. The backend virtual server instances that host your application must run in the same region and under the same VPC.

Use the assigned ALB FQDN to send traffic to the private application load balancer to avoid connectivity problems to your applications during system maintenance or scaling down activities.

Each ALB sends traffic to the cluster node where the application (ASCS, ERS, ASE Sybase DB) is running. During the cluster failover, the ALB redirects all the traffic to the new node where the resources are up and running.

DNS-as-a-Service (DNSaaS) is the management {{site.data.keyword.vpc_short}} DNS service of HA and FQDN (IPs) mechanism.
{: note}

The ALB has a default of 50 seconds for client and server timeout, so after 50 seconds of inactivity, the connection is closed. To support SAP connections through ALB and not lose connection after 50 seconds, you need to request a change this value to a minimum of 300 seconds (client-side idle connection = minimum 300s and server-side idle connection = minimum 300s). To request this change, open a support ticket. This is an account-wide change that affects all of the ALBs in your account. For more information, see [Connection timeouts](docs/vpc?topic=vpc-advanced-traffic-management#connection-timeouts).
{: note}

### {{site.data.keyword.dns_short}} with VPC
{: #automate-ha-deployment-on-vpc-dns-services}

[{{site.data.keyword.dns_full_notm}}](/docs/vpc?topic=vpc-about-dns-srvcs) provide private DNS to VPC users. Private DNS zones are resolvable only on {{site.data.keyword.cloud_notm}} and from explicitly [permitted networks](/docs/dns-svcs?topic=dns-svcs-dns-concepts#permitted-networks) in an account. To get started, create a {{site.data.keyword.dns_short}} instance by using the {{site.data.keyword.cloud_notm}} console.

{{site.data.keyword.dns_short}} allows you to:

* Create the private DNS zones that are collections for holding the domain names.
* Create the DNS resource records under these DNS zones.
* Specify the access controls used for the DNS resolution of resource records on a zone-wide level.

{{site.data.keyword.dns_short}} also maintains its own worldwide set of DNS resolvers. Instances that are provisioned under {{site.data.keyword.cloud_notm}} on an {{site.data.keyword.cloud_notm}} network can use resource records that are configured through {{site.data.keyword.dns_full_notm}} by querying {{site.data.keyword.dns_short}} resolvers.

Resource records and zones that are configured through {{site.data.keyword.dns_short}} are:

* Separated from the wider public DNS, and their publicly accessible records.
* Hidden from the system outside of and not part of the {{site.data.keyword.cloud_notm}} private network.
* Accessible only from the system that you authorize on the {{site.data.keyword.cloud_notm}} private network.
* Resolvable only via the resolvers provided by the service.

The DNS service maps the FQDN of each ALB to the virtual hostnames of the ASCS, ERS, and ASE Sybase that are used by SAP applications.

![Figure 6. DNS records](images/sap-terraform-ha-dns.png "DNS records"){: caption="DNS records" caption-side="bottom"}

## Highly available system for SAP ASE Sybase database with HADR system
{: #automate-ha-deployment-on-vpc-ase-sybase-database}

![Figure 7. SAP HA for ASE Sybase DB instances cluster nodes Primary (Active) and Secondary (Companion)](images/sap-ha-hana-vpc-single-zone.svg "SAP HA for ASE Sybase DB instances cluster nodes Primary (Active) and Secondary (Companion)"){: caption="SAP HA for ASE Sybase DB instances cluster nodes primary (Active) and Secondary (Companion)" caption-side="bottom"}

At the most basic level, a standard HA ASE Sybase cluster in an active(primary)-passive(companion) configuration has two nodes: one is the primary node and the other is the standby node. This means that the primary node is actively serving the active SAP DB instances (Primary and Companion), while the standby node is waiting to jump in if there is any failure.

The cluster is set with a virtual hostname IP (hostname is mapped to the FQDN of the ASE Sybase ALB through DNS, which is the same as explained previously for SAP ASCS and ERS instances). Application instances (PAS and AAS) are used on the SAP profiles to call that particular component. The cluster assigns the virtual IP to the active node and uses a heartbeat monitor to confirm the availability of the components. If the primary node stops responding, it triggers the automatic failover mechanism that calls the standby node to step up to become the primary node. The ALB detects the change, redirects the traffic to the new active node, and assigns the virtual IP to it, restoring the component availability. Once fixed, the failed node comes online as a standby node.

## SAP Sybase HADR system supports synchronous replication
{: #automate-ha-deployment-on-vpc-sync-sybase-replication}

The SAP Sybase HADR system supports synchronous replication between the primary and standby servers for high availability. An active-active setup is a two-node configuration where both nodes in the cluster include SAP ASE managing independent workloads, capable of taking over each others workload in the event of a failure.

The SAP ASE server that takes over the workload is called a secondary companion, and the SAP ASE server that fails is called the primary companion. Together they are companion servers. This movement from one node to another is called failover. After the primary companion is ready to resume its workload, it is moved back to its original node. This movement is called a failback.

When a system fails over, clients that are connected to the primary companion and use the failover property automatically reestablish their network connections to the secondary companion. You must tune your operating system to successfully manage both servers during fail over. See your operating system documentation for information about configuring your system for high availability. An SAP ASE configured for failover in an active-active setup can be shut down using the shutdown command only after you have suspended SAP ASE from the companion configuration, at both the server level and the platform level.

The always-on option in a High Availability and Disaster Recovery (HADR) system consists of two SAP ASE servers: 
* Primary on which all transaction processing takes place.
* Warm standby (referred to as a "standby server" in DR mode, and as a "companion" in HA mode) for the primary server, and contains copies of designated databases from the primary server.

The HADR feature that is shipped with SAP ASE version 16.0 SP02 supports only a single-companion server.
{: note}

Some high-availability solutions (for example, the SAP Adaptive Server Enterprise Cluster Edition) share or use common resources between nodes. However, the HADR system is a "shared nothing" configuration, each node has separate resources including disks. 

In an HADR system, servers are separate entities and data is replicated from the primary server to the companion server. If the primary server fails, a companion server is promoted to the role of primary server either manually or automatically. Once the promotion is complete, clients can reconnect to the new primary server, and see all committed data, including data that was committed on the previous primary server.

Servers can be separated geographically, which makes an HADR system capable of withstanding the loss of an entire computing facility. 

The HADR system includes an embedded SAP Replication Server, which synchronizes the databases between the primary and companion servers. SAP ASE uses the Replication Management Agent (RMA) to communicate with Replication Server and SAP Replication Server uses Open Client connectivity to communicate with the companion SAP ASE.
{: note}

The Replication Agent detects any data changes made on the primary server and sends them to the primary SAP Replication Server. In the figure above, the unidirectional arrows indicate that, although both SAP Replication Servers are configured, only one direction is enabled at a time.

The HADR system supports synchronous replication between the primary and standby servers for high availability so the two servers can keep in sync with Zero Data Loss (ZDL). This requires a network link that is fast enough between the primary and standby server so that synchronous replication can keep up with the primary servers workload. Generally, this means that the network latency is approximately the same speed as the local disk IO speed, a few (fewer than 10) milliseconds. Anything longer than a few milliseconds may result in a slower response to write operations at the primary.

The HADR system supports asynchronous replication between the primary and standby servers for disaster recovery. The primary and standby servers by using asynchronous replication can be geographically distant, meaning they can have a slower network link. With asynchronous replication, Replication Agent Thread captures the primary servers workload, which is delivered asynchronously to SAP Replication Server. The SAP Replication Server applies these workload change to the companion server.

The most fundamental service that is offered by the HADR system is the failover; planned or unplanned from the primary to the companion server, which allows maintenance activity to occur on the old primary server, while applications continue on the new primary.

The HADR system provides protection in the event of a disaster. If the primary server is lost, the companion server can be used as a replacement. Client applications can switch to the companion server, and the companion server is quickly available for users. If the SAP Replication Server was in synchronous mode before the failure of the primary server, the Fault Manager automatically initiates failover with zero data loss. 

### Fault Manager installation on the SAP ASCS node
{: #automate-ha-deployment-on-vpc-fault-install-sap-ascs}

The required parameters are asked during the installation process to create a profile for the fault manager and then adds it to the instance start profile. It is also possible to run the installation by using an existing profile: `sybdbfm install pf=<SYBHA.PFL>`
In this case, the installation process will only ask for profile parameters missing in the profile.

Fault manger is integrated with ASCS on same SAP PAS/AAS cluster (start/stop/move together).
{: note}

There may be some data loss if the SAP Replication Server was in asynchronous mode and you must use manual intervention to failover for disaster recovery.

Connection attempts to the companion server without the necessary privileges are silently redirected to the primary companion via the login redirection mechanism, which is supported by Connectivity libraries. If login redirection is not enabled, client connections fail and are disconnected.

The SAP ASE HADR option installs the below components:

* SAP ASE 
* SAP Replication Server
* Replication Management Agent (RMA) 
* SAP Host Agent 
* Fault Manager
* SAP ASE Cockpit

This automation is offered at no cost; however, the provisioned infrastructure comes at cost.
{: note}
