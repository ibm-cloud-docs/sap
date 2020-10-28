---

copyright:
  years: 2020
lastupdated: "2020-09-21"

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

# Networking design considerations
{: #networking-design-considerations}

The SAP systems in a landscape have specific requirements for servers, operating systems, network setup, and supported storage.
{: shortdesc}

In some regards, SAP workloads that use a Cloud Service Provider (such as {{site.data.keyword.ibm_cloud_sap}}) Infrastructure-as-a-Service is similar to existing practices (over many decades) for running SAP workloads that use an external data center or a data center provider. An SAP landscape has specific requirements for connectivity, between hosts within Cloud IaaS and to external systems, {{site.data.keyword.ibm_cloud_sap}} provides a rich set of functions beyond hosting of SAP systems to improve your SAP landscape.

To assist your project's planning phase, the below sections provide {{site.data.keyword.ibm_cloud_sap}} portfolio design considerations for **Networking**.


## Preface: units of measure for data/information
{: #networking-units-of-measure}

Often the throughput Network Storage is shown in Mbps or Gbps.

It is important to note, that Mb (Megabits) is a decimal prefix and MiB (Mebibyte) is a binary prefix so they are on different scales. More confusion arises because MiB (Mebibyte) was commonly known in Microsoft Windows as `Megabyte`.

For future reference throughout the networking documentation, Mb (Megabits) and MiB (Mebibyte) is used based on the system of units (SI) defined by IEC and adopted by IEEE, ISO, and NIST.

For example,:
- 100 Mbps (Megabits per second), would be 12 MiB/s (Mebibyte per second)
- 1000 Mbps (Megabits per second) also known as 1 Gbps (Gigabits per second), would be 120 MiB/s (Mebibyte per second)
- 10 Gbps (Gigabits per second), would be 1200 MiB/s (Mebibyte per second)


## Networking connectivity considerations
{: #network-connectivity}

For an overview of the connectivity options available, see [Determining access to your SAP system landscape](/docs/sap?topic=sap-determine-access).

SAP Systems are often the focal point of business operations, with a great number of integrated applications (including legacy applications).

In most circumstances for SAP workloads, a connection to the existing internal network is required, and it is recommended to use **{{site.data.keyword.cloud}} Direct Link 2.0** to operate the secure, low-latency, high-throughput (available up to 10 Gbps) as a Routed OSI Layer-2/3 connection.

These connectivity options are dependent on business requirements, for example, whether the business wants to use Cloud and also decrease security risk by isolating network flows through their existing networking structure and security. In these "disconnected" or "private-only" connectivity designs, it is best to request {{site.data.keyword.cloud}} for additional information and discuss your use cases.

In addition, it is strongly not recommended by SAP to split the SAP System tiering across On-Premises location/s and Cloud locations and it is up to the business to evaluate this; for example, it is not recommended by SAP to retain SAP AnyDB run from infrastructure in an On-Premises location and connect to SAP NetWeaver run from infrastructure in a Cloud location. For {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s this split of SAP System tiering is not supported.


### Bring-your-own network (Subnet/CIDR/IP address range)
{: #network-connectivity-byo}

It is often a business preference to Bring-your-own Subnet/CIDR/IP address range (BYOIP) to your IBM Cloud account; this is available depending on the Infrastructure selection and environment.

#### VPC Infrastructure
{: #network-connectivity-byo-vpc}

When using VPC Infrastructure, it is possible to define and use your own subnet. See [VPC - Bring your own subnet](/docs/vpc?topic=vpc-configuring-address-prefixes).

This changes depending on whether [RFC 1918](https://tools.ietf.org/html/rfc1918){: external} IANA reserved IPv4 private network address spaces are in use, because any IP Address within these ranges is considered non-routable. These addresses are not unique on the Internet as they could be used by any private network without any coordination with IANA or an Internet registry, so these addresses are only unique within a private network. These IPv4 private network address spaces are:
- Class A - 10.0.0.0/8
- Class B - 172.16.0.0/12
- Class C - 192.168.0.0/16

If you use a bring-your-own subnet range **which is** defined under [RFC 1918](https://tools.ietf.org/html/rfc1918){: external} IANA reserved IPv4 private network address spaces, then connectivity to an existing internal network is possible when using any VPC functions (for example, Public Gateway or Floating IPs).

It is not supported to use a bring-your-own subnet range **not** defined under [RFC 1918](https://tools.ietf.org/html/rfc1918){: external} IANA reserved IPv4 private network address spaces because this would not permit connectivity to an existing internal network when used with a Public Gateway (PGW) and Floating IPs.


#### Classic Infrastructure with VMware
{: #network-connectivity-byo-classic-vmware}

If the existing business operates SAP on VMware, it is possible to use IBM Cloud for VMware along with VMware HCX and {{site.data.keyword.cloud}} Direct Link to create a bridged bidirectional network between existing Datacenter with VMware and IBM Cloud for VMware. This uses the existing VMware service mesh routing into VMware HCX and into overlay from VMWare NSX on IBM Cloud for VMware, which creates:
- An encrypted Migration Tunnel that uses HCX Cloud Gateway (CGW) + HCX WAN Optimizer (WAN-OPT)
- An encrypted Application Tunnel that uses HCX High Throughput Layer 2 Concentrator (HT L2C)

More information and is described on [IBM Cloud for VMware Solutions - VMware HCX overview](/docs/vmwaresolutions?topic=vmwaresolutions-hcx_considerations).


## Network Topology considerations
{: #network-topology}

Dependent on the count and configuration of SAP Systems that are combined with the arrangement of the network flows between these systems for security or performance reasons, the Network Topology will be significantly different. The design of this topology reflects the requirements from the business for security, performance, cost, flexibility, and connectivity.

Using the Enterprise Resource Planning (ERP) business application, for example, an SAP System hosting the Production instance that uses the SAP System Tiering approach using High Availability of SAP NetWeaver and SAP HANA:
- SAP NetWeaver, there are at least four hosts instead of 1:
  - Central Services (ASCS)
  - Primary Application Server (PAS), also known as Central Instance (CI)
  - Enqueue Replication Server Instance (ERS)
  - Additional Application Server (AAS)
- SAP HANA, there are at least 2 hosts (possibly 3) instead of 1:
  - SAP HANA primary node _(using SAP HANA System Replication)_
  - SAP HANA secondary failover node _(using SAP HANA System Replication)_
  - SAP HANA tertiary disaster recovery failover node _(using SAP HANA System Replication)_

This describes 1 SAP System within the SAP Landscape. An SAP Landscape might use:
- One Track, 5 SAP Systems (SBX > DEV > QA > PRE > PRD)
- Dual Track, 5 SAP Systems (SBX > Proj. DEV + Maint. DEV > Proj. QA + Maint. QA > PRE > PRD)

**Therefore for an SAP Landscape potentially 30 to 50 instances of SAP, spread across 10-50 host servers (physical or virtualized) might be required.** This is before more business applications are added for specific business operations, industry, or geographic functions.

The size of the SAP Landscapes have a direct impact on the Network Topology.

Typically, although this varies from case to case, the following networks are required to meet the scenarios and performance that is required by the business that uses SAP Business Applications:
* Internal network for communication between multiple instances in an SAP Landscape with different SAP System Tiering approaches
* Network for the management and storage transfers of database systems
* Network that is used in production

However, in the simplest scenario there might be one private network for all purposes. It depends on business requirements.

### Additional management systems to enable SAP Systems
{: #network-topology-management-systems}

Depending on your operating system, SAP workload, and network connectivity, you would need to configure access to many more SAP and non-SAP systems. The following is an example of the various management systems that your SAP workloads might require to operate:

* OS Packages update server; with the different subscription channels of the OS Packages for SAP HANA and SAP NetWeaver
* Software and patches download server; when the software is downloaded onto the server, you can use various protocols to transfer the files such as SCP or SFTP to transfer the software to the target server for installation
* Time server (NTP), using NTP on {{site.data.keyword.cloud_notm}} private backbone, public internet NTP or private NTP host.
* Gateway (and Proxy) and Firewall hosts
* Bastion/Jump host. Enables secured pass-through to your Cloud resources from public internet or other network access; often this uses tightly secured SSH on a non-default port.
* Jump host that is enabled with VNC or RDP. Enables GUI access to a target machine (if GUI and VNC or RDP is installed on the target).
* VPN hosts. Enables secured connection to your existing internal network.
* Network Routing hosts, via TelCo or Network Service Provider. Enables secured private high-throughput connection to your existing internal network.
* Backup management service hosts
* Network file storage, via Network File System (for example, NFSv3 or NFSv4.1). Runs file system commands encapsulated by TCP/IP packets.
* Network block storage, using iSCSI protocol controlled by MPIO. Runs SCSI commands encapsulated by TCP/IP packets.
* Network block storage, using Fibre Channel (FC) protocol. Runs SCSI commands encapsulated by FC frames.

Fibre Channel is only required for IBM Power Virtual Servers, and is handled for you during provisioning.
{: note}

### Hybrid Cloud setup for SAP
{: #network-topology-hybrid-cloud}

The following are specific configuration items that you need consideration when planning your SAP landscape, by using your existing on-premises SAP support systems in combination with {{site.data.keyword.ibm_cloud_sap}} portfolio to create a Hybrid Cloud setup:

*	[SAP Transport Management System (STMS also known as. TMS)](https://help.sap.com/viewer/4a368c163b08418890a406d413933ba7/latest/en-US/44b4a0137acc11d1899e0000e829fbbd.html){: external}. Configure STMS based on Transport Groups to prevent file sharing across data centers.
*	[SAProuter](https://support.sap.com/en/tools/connectivity-tools/saprouter.html){: external}. Provides access to SAP Online Service System (OSS). Use your on-premises SAProuter to access the OSS. This SAProuter can be used through further SAProuter hops if IP-based routing is not allowed between your {{site.data.keyword.cloud_notm}}-based systems and your on-premises SAProuter. Alternatively, you might consider setting up another SAProuter that is based on one {{site.data.keyword.cloud_notm}}-based server with a public IP and connect it to the SAP OSS system through the internet.
*	[SAP Solution Manager](https://support.sap.com/en/alm/solution-manager.html){: external}. Access to the SAP Solution Manager has different connectivity requirements between an SAP Solution Manager and its managed systems. The differences depend on your usage scenario. These scenarios require an understanding of the required network connectivity.
* If you are deploying public gateways or floating IPs, you need to look into the details of Network Address Translation (NAT) and the behavior of SAP applications. Refer to the [SAP document on NAT](https://wiki.scn.sap.com/wiki/display/ABAPConn/NAT+and+RFC){: external} to consider potential issues on the application layer, especially in the SAP Remote Function Calls (RFCs).


## Networking consumption considerations
{: #network-consumption}

Traditional SAP workload network communication is relatively small with under 100 MiB network traffic per day possible, such as:
- User transactions from SAPGUI; for Windows, for Java (used with macOS or Linux)
- User transactions from SAP WebGUI
- User transactions with SAP web Dynpro apps from SAP NetWeaver Business Client (NWBC)
- SAP Remote Function Call (SAP RFC) between SAP and non-SAP systems
- SAP iDOC Inbound/Outbound between SAP and non-SAP systems with third-parties (for example, banks, suppliers)

However, there are much larger SAP workload network communications too consuming significantly more network traffic, such as:
- SAPUI5 preinstall libraries and themes for SAP Fiori Launchpad and Apps
  - 10 MiB - 25 MiB (estimate) per new session that is loaded from SAP web Assistant (also known as. XRay), these preinstall libraries are then cached by the browser. _Once cached the libraries are available for use in any new browser tab, even after browser restart or SAP Fiori logout; until browser cache cleared_
  - 20 KiB - 500 KiB (estimate) per each new Fiori app that is loaded within the session
- SAP HANA System Replication (HSR) Sync or Async, streaming Gigabyte's of data per month from primary to secondary (or tertiary) nodes

With increased traffic of new design SAP software, it is possible to heavily exceed the amount of network traffic seen in past SAP usage.

Designing your SAP applications to use the {{site.data.keyword.cloud_notm}} private backbone for these data transfers is important, as there are no ingress/egress charges; whereas usage over public internet incurs egress charges.

You should estimate the amount of data that is transferred. During initial project implementation stages, this can be difficult. However at least by an order of magnitude estimate should be performed.


## Networking Throughput performance considerations
{: #network-performance-throughput}

SAP generally recommends 10 Gbps (redundant) network throughput for traffic between its application servers and SAP HANA databases, and for other SAP HANA clients, such as SAP Business Intelligence.

For deployments of SAP NetWeaver using SAP AnyDB with local storage, even for three-tier setups, 1 Gbps networks are usually sufficient.



## Networking Latency performance considerations
{: #network-performance-latency}

For your business operations and non-SAP dependent systems, you may required certain latency key performance indicators (KPIs) to be met. This should consider the site location of your hosts, and testing the latency by using the {{site.data.keyword.cloud_notm}} private backbone network.

The testing of latency by using the Round Trip Time (RTT) metric is necessary when designing High Availability (HA) and Disaster Recovery (DR) for SAP HANA.

If a HA failover is being designed within one site location, in almost all cases this will be achievable for SAP HANA System Replication requirements.

However, if designing High Availability for SAP HANA across multiple sites within a Region, or if designing Disaster Recovery across multiple Regions then the latency that uses the Round Trip Time (RTT) metric must be carefully tested and considered.

This is because {{site.data.keyword.cloud_notm}} seeks to ensure high availability of the platform, by using geographically dispersed site locations with fault tolerance (for example, different risk assessments). More information available on [How IBM Cloud ensures high availability and disaster recovery](/docs/overview?topic=overview-zero-downtime).

In particular, for VPC Infrastructure the Availability Zones are geographically dispersed locations within the Region. For most workloads, this design provides more redundancy across the Region. However, SAP HANA System Replication requires low network latency, which can become difficult to meet the necessary Round Trip Time (RTT) metric due to current technology physical data transfer limitations of cabling from a physics perspective (that is, speed of light over fiber Optic cable).



## Networking Ports security considerations
{: #network-security-ports}

The following information is a brief summary of [SAP Help Portal - TCP/IP Ports of All SAP Products](https://help.sap.com/viewer/ports){: external}, which provides an example of the considerations that are required for the security of your SAP Systems and entire IT infrastructure landscape on {{site.data.keyword.cloud_notm}}.

Depending on the SAP Technical Applications used, and the business scenarios being addressed, different hosts are need ports to be opened - traditionally this has been the use of Firewall Port Groups that are combined with Firewall Rules (or individual Firewall Rules per host, although is often becomes unmanageable).

The below table includes some of the key Ports to use with SAP Systems that use SAP NetWeaver and SAP HANA, which need correction, depending on the SAP Technical Application’s instance number; the instance numbers 00, 01, 02 are the defaults across the various SAP Technical Applications and will be in different patterns (these patterns are shown with `code blocks` highlighting):

| **SAP Technical Application** | **Component** | **Port** |
| --- | --- | --- |
| SAP Router | | |
| | SAP Router | 3200 |
| | SAP Router | 3299 |
| SAP NetWeaver | | |
| | SAP NetWeaver AS Primary App Server (PAS Dialog) Instance `01` | 32`01` |
| | SAP NetWeaver AS PAS Gateway Instance `01` | 33`01` |
| | SAP NetWeaver AS Central Services Messenger Server (ASCS MS) Instance 01 | 3602 |
| | SAP NetWeaver AS PAS Gateway (with SNC Enabled) Instance `01` | 48`01` |
| | SAP NetWeaver AS ICM HTTP (Port 80 prefix) | 8001 |
| | SAP NetWeaver AS ICM HTTPS (Secure, Port 443 prefix) | 44301 |
| | SAP NetWeaver sapctrl HTTP _(Dual Host install)_ | 50113 |
| | SAP NetWeaver sapctrl HTTPS _(Dual Host install)_ | 50114 |
| SAP HANA | | |
| | SAP HANA sapctrl HTTP _(One Host install)_ | 50013 |
| | SAP HANA sapctrl HTTPS _(One Host install)_ | 50014 |
| | SAP HANA Internal Web Dispatcher | 30006 |
| | SAP HANA MDC System Tenant SYSDB Index Server | 30013 |
| | SAP HANA MDC Tenant 1 Index Server | 30015 |
| | SAP HANA ICM HTTP Internal Web Dispatcher | 8000 |
| | SAP HANA ICM HTTPS (Secure) Internal Web Dispatcher | 4300 |
| SAP Web Dispatcher | | |
| | SAP Web Dispatcher ICM HTTP (Port 80 prefix) Instance Number `90` | 80`90` |
| | SAP Web Dispatcher ICM HTTPS (Secure, Port 443 prefix) Instance Number `90` | 443`90` |
| SAP HANA XSA | | |
| | SAP HANA XSA Instance 00 Client HTTPS via xsuaaserver to xscontroller Web Dispatcher | 3`00`32 |
| | SAP HANA XSA Instance 00 Internal HTTPS xscontroller to xsuaaserver User Authentication | 3`00`31 |
| | SAP HANA XSA Instance 00 Client HTTPS to xscontroller Web Dispatcher | 3`00`30 |
| | SAP HANA XSA Instance 00 Dynamic Range Internal HTTPS xscontroller router to xscontroller data access and application instances | 510`00`-515`00` |
| | SAP HANA XSA Instance 00 Internal HTTPS xsexecagent to xscontroller | 3`00`29 |
| | SAP HANA XSA Instance 00 Web Dispatcher HTTP(S) routing | 3`00`33 |
| SAP NetWeaver JAVA | | |
| | SAP NetWeaver AS JAVA P4 Port | 50304 |
| | SAP NetWeaver AS JAVA P4 Port | 50404 |
{: caption="Table 1. Common Ports used with SAP Technical Applications" caption-side="top"}



## Networking Traffic Segregation security considerations
{: #network-security-segregation}

You can separate different network traffic types in your landscape, either because of security restrictions or because of throughput considerations.

Segregation of networks is useful for the following SAP workload use cases:
  *	Multiple servers that exchange data
    * SAP Systems in a three-tier logical architecture, where the SAP database and SAP application instances are on different hosts.
  *	Multiple SAP Systems that exchange large amounts of data
    * Database servers, which need to have low network latency and high network throughput to network block/file storage therefore need to avoid firewalls. However the database still needs protection for other systems and networks access.

To use segregation of networks effectively, interconnectivity must be allowed under specific conditions.

### VPC Infrastructure separation of subnets
{: #network-security-vpc-separate-subnets}

To separate traffic, use multiple subnets.

Each VPC for a region can contain multiple subnets. These subnets within the VPC are enabled to communicate with each other, unless blocked by a Network ACL or Security Group. Therefore, two virtual servers in VPC infrastructure can have a virtual network interfaces (vNIC) on different subnets from each other.

The Network ACL or Security Group would allow specific network interconnectivity flows across these separate subnets.

However, a virtual server in VPC infrastructure cannot have multiple virtual network interfaces (vNICs) assigned to multiple subnets.
{: note}


### Classic infrastructure separation of subnets
{: #network-security-classic-separate-subnets}

To separate traffic, use multiple VLAN and subnets therein.

Each VLAN is either public or private and is specific to the data center and the data center Pod. The VLAN can contain multiple subnets. These subnets within the VLAN are enabled to communicate with each other, unless blocked by a Gateway Appliance.

Therefore, two bare metal hosts in classic infrastructure might have physical network interface cards that are assigned to different VLAN and subnets from each other.

The Gateway Appliance would allow specific network interconnectivity flows across these separate subnets.

A bare metal server by default (can change depending on the hardware specifications) use physical network interface cards (NICs) and consume four ethernet ports:
- `eth0` NIC / `eth2` redundant NIC
  - Public VLAN, as DMZ trunked to Gateway Appliance
    - Public primary subnet
      - Public IP behind DMZ
- `eth1` NIC / `eth3` redundant NIC
  - Private VLAN, as DMZ trunked to Gateway Appliance
    - Private primary subnet
      - Private IP behind DMZ
- `mgmt0`	--- IPMI (Admin Network Zone)

Bare metals can be reconfigured from default specific if the hardware specifications allow for it, with more subnets. This allows for maximum separation of traffic and can increase performance by using different network interface cards (NICs) to handle more network throughput in parallel. An example of this reconfiguration might be:

- `eth0` NIC / `eth2` redundant NIC
  - Public VLAN, as DMZ trunked to Gateway Appliance
    - Public primary subnet
      - Public IP behind DMZ
- `eth1` NIC / `altered to eth4` redundant NIC
  - Private VLAN, as DMZ trunked to Gateway Appliance
    - Private primary subnet
      - Private IP behind DMZ
- `eth3` additional NIC / `eth5` additional redundant NIC
  - Private VLAN
    - Private secondary portable subnet A
      - Private IP behind DMZ
    - Private secondary portable subnet B
      - Private IP behind DMZ
- `mgmt0`	--- IPMI (Admin Network Zone)

Such reconfiguration, as the example, will not be available in all scenarios.
{: note}

For SAP HANA and the high network performance and security that is required, additional VLANs can assist. Read the recommendations by SAP for on-premises environments on [SAP HANA Network Requirements](https://www.sap.com/documents/2016/08/1cd2c2fb-807c-0010-82c7-eda71af511fa.html){: external} and identify the suitable network configuration for meeting your business needs.


### VMware on classic infrastructure separation of subnets
{: #network-security-vmware-separate-subnets}

To separate traffic with {{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated, use multiple subnets within the VMware Overlay VXLAN (powered by VMware NSX).

In {{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated, the VMware Overlay VXLAN (powered by VMware NSX) is connected back to public VLAN and private VLAN on the classic infrastructure network as the underlay; the VMware NSX management utilizes the private secondary portable subnets to achieve the VXLAN. This provides full control of the network design when running on VMware, and allows assigning VMware VM's an IP from any defined range.

If instead using a manual deployment of VMware to a bare metal, the VMware vSwitch would directly use the private VLAN's secondary portable subnet to assign VMware VM's an IP address from the classic infrastructure network.

Traffic segregation needs to be considered carefully within VMware deployments, because elaborate VMware-based deployments, where different kinds of network traffic might need to be more strictly separated for security reasons.
