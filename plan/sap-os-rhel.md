---

copyright:
  years: 2020
lastupdated: "2020-09-21"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, OS, Operating System

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
{:important: .important}

# Red Hat Enterprise Linux for SAP
{: #os-rhel}

**_INCOMPLETE: This page only appears in Test/Staging, it is not complete or ready to be published_**
{: important}


Red Hat Enterprise Linux for SAP is tailored to the needs of SAP workloads, with capabilities for SAP HANA, SAP NetWeaver, and business applications such as SAP S/4HANA or SAP BW/4HANA.
{: shortdesc}

The capabilities of Red Hat for SAP include:
- Tuning for higher performance,
- Clustering for Scale-out,
- Clustering for High Availability and Disaster Recovery,
- Lifecycle management and proactive optimization leveraging Smart Management and Red Hat Insights,
- Extended Update Support - both 2 year (EUS) and 4 year (E4S)

More features available starting with RHEL for SAP 8.x include:
- RHEL System Roles for SAP, which can be used to automate the configuration of a RHEL system to run SAP workloads,
- Extended capabilities with leveraging Smart Management and Red Hat Insights


## Variants of RHEL for SAP
{: #os-rhel-variants}

**There are two main variants of Red Hat Enterprise Linux for SAP:**
- RHEL for SAP Applications; designed for SAP NetWeaver only without HA/DR capabilities
- RHEL for SAP Solutions; designed for SAP HANA and SAP NetWeaver


| **Feature** | **RHEL for SAP Applications** | **RHEL for SAP Solutions** |
|-|-|-|
| OS Packages for SAP NetWeaver and other `†`_<br><br>compat-sap-c++<br>resource-agents-sap<br>tuned-profiles-sap<br>compat-locales-sap<br>sapconf_ | Yes | Yes |
| OS Packages for SAP HANA `†`_<br><br>compat-sap-c++<br>resource-agents-sap-hana<br>tuned-profiles-sap-hana_ |  | Yes |
| RHEL High-Availability Add-On |  | Yes |
| RHEL System Roles for SAP |  | Yes |
| Extended Update Support (EUS, 2 years) | Only in Premium | Yes |
| Extended Update Services for SAP Solutions (E4S), 4 years |  | Yes |
| Red Hat Smart Management Add-On |  | Yes `*` |
| Red Hat Insights usage | Yes `*` | Yes `*` |


`*` - Available when using Bring-your-OS (BYOS)

`†` - There are multiple changes to these OS Packages starting with RHEL 8.x, please see [RHEL 8 - Considerations in adopting RHEL 8 - Appendix A. Changes to packages](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/considerations_in_adopting_rhel_8/changes-to-packages_considerations-in-adopting-rhel-8){: external} for changes in package names and repositories


**Additional information is available in the Red Hat documentation:**
- [Red Hat KB - Overview of the Red Hat Enterprise Linux for SAP Solutions subscription](https://access.redhat.com/solutions/3082481){: external}
- [Red Hat KB - Overview of the Red Hat Enterprise Linux for SAP Applications subscription](https://access.redhat.com/solutions/34169){: external}

**Other variants of Red Hat Enterprise Linux for SAP include:**
- RHEL for SAP with High Availability (HA) and Update Services (US), through a Partner's Marketplace


### Variant of RHEL for SAP, available on-demand within the {{site.data.keyword.ibm_cloud_sap}} portfolio
{: #os-rhel-variants-ibm}

| **Environment** | **{{site.data.keyword.ibm_cloud_sap}} portfolio - IaaS** | **Availability** |
|-|-|-|
| Classic Infrastructure | Intel Bare Metal certified for SAP HANA | |
| Classic Infrastructure | Intel Bare Metal ceritifed for SAP NetWeaver | |
| VPC Infrastructure | Intel Virtual Server (Gen2) certified for SAP HANA | |
| VPC Infrastructure | Intel Virtual Server (Gen2) certified for SAP NetWeaver | |
| IBM Power Infrastructure | IBM Power Virtual Server certified for SAP HANA | |
| IBM Power Infrastructure | IBM Power Virtual Server certified for SAP NetWeaver | |
| Classic Infrastructure | IBM Cloud for VMware certified for SAP | BYOS only |


The Red Hat on-demand provisioning and subscriptions are provided with full support directly from IBM Cloud and triage to Red Hat.

### Variant of RHEL for SAP, available Bring-your-OS (BYOS) through the Red Hat Cloud Access program
{: #os-rhel-variants-cloudaccess}

Existing RHEL for SAP licenses purchased directly from Red Hat or Red Hat channel partners, can be used with the Red Hat Cloud Access program.

The Red Hat Cloud Access program enables existing Red Hat subscriptions to be used with certified cloud providers with full support directly from Red Hat.


## OS installation from {{site.data.keyword.ibm_cloud_sap}}
{: #os-rhel-installation}

Whether the RHEL4SAP installation is available for a specified profile from the {{site.data.keyword.ibm_cloud_sap}} portfolio, will always depend on whether it is listed in the [SAP Certified and Supported SAP HANA Hardware Directory - Certified IaaS Platforms - {{site.data.keyword.cloud_notm}}](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:28){: external}.


## OS Security considerations
{: #os-rhel-security}


## OS Preparation for SAP considerations
{: #os-rhel-preparation}


## OS on-demand license, subscriptions and package repositories
{: #os-rhel-license}

Each RHEL OS on-demand license is made of a few concepts:
- License
    - Activation Key: the alphanumerical sequence associated to a specific licence
    - Entitlement: number of systems where this license can be applied
    - Service Level: support level for the subscriptions
    - Subscriptions: 
      - Product:
        - Product Key:
      - Package Repositories:



### OS support procedure and package updates
{: #os-rhel-license-support-packages}

Extended Update Support (EUS) provides up to two years of extended update support.

Extended Update Services for SAP Solutions (E4S) provides up to four years of update services; this was first introduced with RHEL for SAP 7.4 with High Availability (HA) and Update Services (US) license.


[Red Hat Enterprise Linux (RHEL) Extended Update Support (EUS) Overview](https://access.redhat.com/articles/rhel-eus){: external}

Further information regarding the integrated support process, is shown:
- [Red Hat KB - Overview of the Red Hat Enterprise Linux for SAP Solutions subscription](https://access.redhat.com/solutions/3082481){: external}
- [Red Hat KB - Overview of the Red Hat Enterprise Linux for SAP Applications subscription](https://access.redhat.com/solutions/34169){: external}


#### RHEL 8.2 on Intel Virtual Server (Gen2), on IBM Cloud VPC Infrastructure environment
{: #os-rhel8-license-support-packages-intel-bm-example}

***Below is an abbreviated example listing all RHEL 8.x available repos for the system, including disabled repos:***

To activate a disabled repository, see documentation for command `subscription-manager repos --enable=xxxxxxxx-xxxxxxxx`
{: note}

```
[root ~]# subscription-manager repos --list
+----------------------------------------------------------+
    Available Repositories in /etc/yum.repos.d/redhat.repo
+----------------------------------------------------------+
Repo ID:   rhel-8-for-x86_64-appstream-rpms
Repo ID:   rhel-8-for-x86_64-appstream-eus-rpms
Repo ID:   rhel-8-for-x86_64-appstream-e4s-rpms
Repo ID:   rhel-8-for-x86_64-baseos-rpms
Repo ID:   rhel-8-for-x86_64-baseos-eus-rpms
Repo ID:   rhel-8-for-x86_64-baseos-e4s-rpms
Repo ID:   rhel-8-for-x86_64-supplementary-rpms
Repo ID:   rhel-8-for-x86_64-supplementary-eus-rpms
Repo ID:   rhel-8-for-x86_64-highavailability-rpms
Repo ID:   rhel-8-for-x86_64-highavailability-eus-rpms
Repo ID:   rhel-8-for-x86_64-highavailability-e4s-rpms
Repo ID:   rhel-8-for-x86_64-sap-netweaver-rpms
Repo ID:   rhel-8-for-x86_64-sap-netweaver-eus-rpms
Repo ID:   rhel-8-for-x86_64-sap-netweaver-e4s-rpms
Repo ID:   rhel-8-for-x86_64-sap-solutions-rpms
Repo ID:   rhel-8-for-x86_64-sap-solutions-eus-rpms
Repo ID:   rhel-8-for-x86_64-sap-solutions-e4s-rpms
```

***Below is an example listing all RHEL 8.x available subscriptions not yet attached to the system (including any that apply for other CPU Architectures):***

```
[root ~]# subscription-manager list --available
+-------------------------------------------+
    Available Subscriptions
+-------------------------------------------+
Subscription Name:   Red Hat Satellite - Add-Ons for Providers
Provides:            
                     Red Hat Enterprise Linux for SAP Applications for x86_64
                     Red Hat Enterprise Linux for SAP HANA for Power, little endian
                     Red Hat Enterprise Linux for SAP HANA for x86_64
                     Red Hat Enterprise Linux High Availability - Update Services for SAP Solutions
                     Red Hat Enterprise Linux High Availability (for IBM Power LE) - Update Services for SAP Solutions
                     Red Hat Enterprise Linux Server - Update Services for SAP Solutions
                     Red Hat Enterprise Linux Server (for IBM Power LE) - Update Services for SAP Solutions
                     RHEL for SAP - Extended Update Support
                     RHEL for SAP - Update Services for SAP Solutions
                     RHEL for SAP (for IBM Power LE) - Update Services for SAP Solutions
                     RHEL for SAP Applications for Power LE EUS
                     RHEL for SAP HANA - Extended Update Support
                     RHEL for SAP HANA - Update Services for SAP Solutions
                     RHEL for SAP HANA (for IBM Power LE) - Update Services for SAP Solutions
                     RHEL for SAP HANA for Power LE EUS
                     Red Hat 3scale API Management Platform
                     Red Hat Ansible Automation Platform
                     Red Hat Ansible Engine
                     Red Hat Beta
                     Red Hat Build of Jaeger
                     Red Hat Ceph Storage
                     Red Hat Ceph Storage Calamari
                     Red Hat Ceph Storage MON
                     Red Hat Ceph Storage OSD
                     Red Hat Certification (for RHEL Server)
                     Red Hat CloudForms
                     Red Hat CloudForms Beta
                     Red Hat CodeReady Linux Builder for ARM 64
                     Red Hat CodeReady Linux Builder for ARM 64 Beta
                     Red Hat CodeReady Linux Builder for ARM 64 High Touch Beta
                     Red Hat CodeReady Linux Builder for IBM z Systems
                     Red Hat CodeReady Linux Builder for IBM z Systems - Extended Update Support
                     Red Hat CodeReady Linux Builder for Power, little endian
                     Red Hat CodeReady Linux Builder for Power, little endian - Extended Update Support
                     Red Hat CodeReady Linux Builder for x86_64
                     Red Hat CodeReady Linux Builder for x86_64 - Extended Update Support
                     Red Hat CodeReady Workspaces for OpenShift
                     Red Hat Developer Tools (for RHEL Server for ARM 64)
                     Red Hat Developer Tools (for RHEL Server for ARM)
                     Red Hat Developer Tools (for RHEL Server for IBM Power LE)
                     Red Hat Developer Tools (for RHEL Server for IBM Power)
                     Red Hat Developer Tools (for RHEL Server for System Z)
                     Red Hat Developer Tools (for RHEL Server)
                     Red Hat Developer Tools (for RHEL Workstation)
                     Red Hat Developer Tools Beta (for RHEL Server for ARM 64)
                     Red Hat Developer Tools Beta (for RHEL Server for ARM)
                     Red Hat Developer Tools Beta (for RHEL Server for IBM Power LE)
                     Red Hat Developer Tools Beta (for RHEL Server for IBM Power)
                     Red Hat Developer Tools Beta (for RHEL Server for System Z)
                     Red Hat Developer Tools Beta (for RHEL Server)
                     Red Hat Developer Tools Beta (for RHEL Workstation)
                     Red Hat Developer Toolset (for RHEL Server)
                     Red Hat Directory Server
                     Red Hat Enterprise Linux Advanced Virtualization
                     Red Hat Enterprise Linux Advanced Virtualization Beta
                     Red Hat Enterprise Linux Atomic Host
                     Red Hat Enterprise Linux Fast Datapath
                     Red Hat Enterprise Linux Fast Datapath (for RHEL Server for IBM Power LE)
                     Red Hat Enterprise Linux Fast Datapath Beta for Power, little endian
                     Red Hat Enterprise Linux for ARM 64
                     Red Hat Enterprise Linux for ARM 64 Beta
                     Red Hat Enterprise Linux for ARM 64 High Touch Beta
                     Red Hat Enterprise Linux for IBM z Systems
                     Red Hat Enterprise Linux for IBM z Systems - Extended Update Support
                     Red Hat Enterprise Linux for Power 9
                     Red Hat Enterprise Linux for Power, big endian
                     Red Hat Enterprise Linux for Power, big endian - Extended Update Support
                     Red Hat Enterprise Linux for Power, little endian
                     Red Hat Enterprise Linux for Power, little endian - Extended Update Support
                     Red Hat Enterprise Linux for Power, little endian Beta
                     Red Hat Enterprise Linux for Real Time
                     Red Hat Enterprise Linux for x86_64
                     Red Hat Enterprise Linux for x86_64 - Extended Update Support
                     Red Hat Enterprise Linux High Availability (for IBM Power LE) - Extended Update Support
                     Red Hat Enterprise Linux High Availability (for IBM z Systems) - Extended Update Support
                     Red Hat Enterprise Linux High Availability for Power, little endian
                     Red Hat Enterprise Linux High Availability for x86_64
                     Red Hat Enterprise Linux High Availability for x86_64 - Extended Update Support
                     Red Hat Enterprise Linux High Performance Networking (for RHEL Server) - Extended Update Support
                     Red Hat Enterprise Linux Load Balancer (for RHEL Server)
                     Red Hat Enterprise Linux Load Balancer (for RHEL Server) - Extended Update Support
                     Red Hat Enterprise Linux Resilient Storage for IBM z Systems - Extended Update Support
                     Red Hat Enterprise Linux Resilient Storage for x86_64
                     Red Hat Enterprise Linux Resilient Storage for x86_64 - Extended Update Support
                     Red Hat Enterprise Linux Scalable File System (for RHEL Server)
                     Red Hat Enterprise Linux Scalable File System (for RHEL Server) - Extended Update Support
                     Red Hat Enterprise Linux Server
                     Red Hat Enterprise Linux Server - Extended Life Cycle Support
                     Red Hat Enterprise Linux Server - Extended Life Cycle Support (for IBM z Systems)
                     Red Hat Enterprise Linux Server for ARM
                     Red Hat Enterprise Linux Server for ARM Beta
                     Red Hat Enterprise MRG Messaging
                     Red Hat Enterprise Virtualization for Power, little endian
                     Red Hat Gluster Storage Management Console (for RHEL Server)
                     Red Hat Gluster Storage Nagios Server
                     Red Hat Gluster Storage Server for On-premise
                     Red Hat Gluster Storage Web Administration (for RHEL Server)
                     Red Hat JBoss Core Services
                     Red Hat Openshift Application Runtimes for IBM Power LE
                     Red Hat OpenShift Container Platform
                     Red Hat OpenShift Pipelines
                     Red Hat Openshift Serverless
                     Red Hat OpenShift Service Mesh
                     Red Hat OpenStack
                     Red Hat OpenStack - Extended Life Cycle Support
                     Red Hat OpenStack Beta
                     Red Hat OpenStack Director Deployment Tools
                     Red Hat OpenStack Director Deployment Tools Beta
                     Red Hat OpenStack Director Deployment Tools Beta for IBM Power LE
                     Red Hat OpenStack Director Deployment Tools for IBM Power LE
                     Red Hat Satellite Proxy
                     Red Hat Software Collections (for RHEL Server for ARM 64)
                     Red Hat Software Collections (for RHEL Server for ARM)
                     Red Hat Software Collections (for RHEL Server for IBM Power LE)
                     Red Hat Software Collections (for RHEL Server)
                     Red Hat Software Collections Beta (for RHEL Server for ARM 64)
                     Red Hat Software Collections Beta (for RHEL Server for ARM)
                     Red Hat Software Collections Beta (for RHEL Server for IBM Power LE)
                     Red Hat Software Collections Beta (for RHEL Server)
                     Red Hat Software Test Suite 5 (for RHEL Server)
                     Red Hat Storage Console
                     Red Hat Storage Console Node
                     Red Hat Storage for Public Cloud (via RHUI)
                     Red Hat Virtualization
                     Red Hat Virtualization - ELS
                     Red Hat Virtualization - Extended Update Support
                     Red Hat Virtualization for IBM Power LE
                     Red Hat Virtualization for IBM Power LE - Extended Update Support
                     Red Hat Virtualization Host
                     Red Hat Virtualization Host - Extended Update Support
                     Red Hat Virtualization Manager
                     dotNET on RHEL (for RHEL Server)
                     dotNET on RHEL Beta (for RHEL Server)
                     JBoss Enterprise Application Platform
                     OpenJDK Java (for Middleware)
                     OpenShift Tools and Services
                     Oracle Java (for RHEL Server)
                     Oracle Java (for RHEL Server) - Extended Update Support

SKU:                 RC0000000
Contract:            00000000
Pool ID:             00000000000000000000000000000000
Provides Management: Yes
Available:           000000
Suggested:           0
Service Type:        L1-L3
Roles:               
Service Level:       Premium
Usage:               
Add-ons:             
Subscription Type:   Stackable
Starts:              01/01/21
Ends:                17/01/22
Entitlement Type:    Physical
```

#### RHEL 7.6 on Intel Bare Metal, on IBM Cloud Classic Infrastructure environment
{: #os-rhel7-license-support-packages-intel-bm-example}

***Below is an abbreviated example listing all RHEL 7.x available repos for the system, including disabled repos:***

To activate a disabled repository, see documentation for command `subscription-manager repos --enable=xxxxxxxx-xxxxxxxx`
{: note}

```
[root ~]# subscription-manager repos --list
+----------------------------------------------------------+
    Available Repositories in /etc/yum.repos.d/redhat.repo
+----------------------------------------------------------+
Repo ID:   rhel-7-server-rh-common-rpms
Repo ID:   rhel-7-server-rpms
Repo ID:   rhel-7-server-eus-rpms
Repo ID:   rhel-7-server-e4s-rpms
Repo ID:   rhel-7-server-optional-rpms
Repo ID:   rhel-7-server-eus-optional-rpms
Repo ID:   rhel-7-server-supplementary-rpms
Repo ID:   rhel-7-server-eus-supplementary-rpms
Repo ID:   rhel-7-server-extras-rpms
Repo ID:   rhel-ha-for-rhel-7-server-rpms
Repo ID:   rhel-ha-for-rhel-7-server-eus-rpms
Repo ID:   rhel-ha-for-rhel-7-server-e4s-rpms
Repo ID:   rhel-sap-for-rhel-7-server-rpms
Repo ID:   rhel-sap-for-rhel-7-server-eus-rpms
Repo ID:   rhel-sap-for-rhel-7-server-e4s-rpms
Repo ID:   rhel-sap-hana-for-rhel-7-server-rpms
Repo ID:   rhel-sap-hana-for-rhel-7-server-eus-rpms
Repo ID:   rhel-sap-hana-for-rhel-7-server-e4s-rpms
```

***Below is an example listing all RHEL 7.x available subscriptions not yet attached to the system (including any that apply for other CPU Architectures):***

```
[root ~]# subscription-manager list --available
+-------------------------------------------+
    Available Subscriptions
+-------------------------------------------+
Subscription Name:   Red Hat Satellite - Add-Ons for Providers
Provides:            
                     Red Hat Enterprise Linux for SAP Applications for x86_64
                     Red Hat Enterprise Linux for SAP HANA for x86_64
                     Red Hat Enterprise Linux High Availability - Update Services for SAP Solutions
                     Red Hat Enterprise Linux High Availability (for IBM Power LE) - Update Services for SAP Solutions
                     Red Hat Enterprise Linux Server - Update Services for SAP Solutions
                     Red Hat Enterprise Linux Server (for IBM Power LE) - Update Services for SAP Solutions
                     RHEL for SAP - Extended Update Support
                     RHEL for SAP - Update Services for SAP Solutions
                     RHEL for SAP (for IBM Power LE) - Update Services for SAP Solutions
                     RHEL for SAP Applications for Power LE EUS
                     RHEL for SAP HANA - Extended Update Support
                     RHEL for SAP HANA - Update Services for SAP Solutions
                     RHEL for SAP HANA (for IBM Power LE) - Update Services for SAP Solutions
                     RHEL for SAP HANA for Power LE EUS
                     Red Hat 3scale API Management Platform
                     Red Hat Ansible Engine
                     Red Hat Ansible Tower
                     Red Hat Beta
                     Red Hat Ceph Storage
                     Red Hat Ceph Storage Calamari
                     Red Hat Ceph Storage MON
                     Red Hat Ceph Storage OSD
                     Red Hat Certification (for RHEL Server)
                     Red Hat CloudForms
                     Red Hat CloudForms Beta
                     Red Hat CodeReady Linux Builder for ARM 64
                     Red Hat CodeReady Linux Builder for ARM 64 Beta
                     Red Hat CodeReady Linux Builder for ARM 64 High Touch Beta
                     Red Hat CodeReady Linux Builder for IBM z Systems
                     Red Hat CodeReady Linux Builder for IBM z Systems - Extended Update Support
                     Red Hat CodeReady Linux Builder for Power, little endian
                     Red Hat CodeReady Linux Builder for Power, little endian - Extended Update Support
                     Red Hat CodeReady Linux Builder for x86_64
                     Red Hat CodeReady Linux Builder for x86_64 - Extended Update Support
                     Red Hat CodeReady Workspaces for OpenShift
                     Red Hat Developer Tools (for RHEL Server for ARM)
                     Red Hat Developer Tools (for RHEL Server for ARM 64)
                     Red Hat Developer Tools (for RHEL Server for IBM Power LE)
                     Red Hat Developer Tools (for RHEL Server for IBM Power)
                     Red Hat Developer Tools (for RHEL Server for System Z)
                     Red Hat Developer Tools (for RHEL Server)
                     Red Hat Developer Tools (for RHEL Workstation)
                     Red Hat Developer Tools Beta (for RHEL Server for ARM 64)
                     Red Hat Developer Tools Beta (for RHEL Server for ARM)
                     Red Hat Developer Tools Beta (for RHEL Server for IBM Power LE)
                     Red Hat Developer Tools Beta (for RHEL Server for IBM Power)
                     Red Hat Developer Tools Beta (for RHEL Server for System Z)
                     Red Hat Developer Tools Beta (for RHEL Server)
                     Red Hat Developer Tools Beta (for RHEL Workstation)
                     Red Hat Developer Toolset (for RHEL Server)
                     Red Hat Directory Server
                     Red Hat Enterprise Linux Advanced Virtualization
                     Red Hat Enterprise Linux Advanced Virtualization Beta
                     Red Hat Enterprise Linux Atomic Host
                     Red Hat Enterprise Linux Fast Datapath
                     Red Hat Enterprise Linux Fast Datapath (for RHEL Server for IBM Power LE)
                     Red Hat Enterprise Linux Fast Datapath Beta for Power, little endian
                     Red Hat Enterprise Linux for ARM 64
                     Red Hat Enterprise Linux for ARM 64 Beta
                     Red Hat Enterprise Linux for ARM 64 High Touch Beta
                     Red Hat Enterprise Linux for IBM z Systems
                     Red Hat Enterprise Linux for IBM z Systems - Extended Update Support
                     Red Hat Enterprise Linux for Power 9
                     Red Hat Enterprise Linux for Power, big endian
                     Red Hat Enterprise Linux for Power, big endian - Extended Update Support
                     Red Hat Enterprise Linux for Power, little endian
                     Red Hat Enterprise Linux for Power, little endian - Extended Update Support
                     Red Hat Enterprise Linux for Power, little endian Beta
                     Red Hat Enterprise Linux for Real Time
                     Red Hat Enterprise Linux for x86_64
                     Red Hat Enterprise Linux for x86_64 - Extended Update Support
                     Red Hat Enterprise Linux High Availability (for IBM Power LE) - Extended Update Support
                     Red Hat Enterprise Linux High Availability (for IBM z Systems) - Extended Update Support
                     Red Hat Enterprise Linux High Availability for x86_64
                     Red Hat Enterprise Linux High Availability for x86_64 - Extended Update Support
                     Red Hat Enterprise Linux High Performance Networking (for RHEL Server) - Extended Update Support
                     Red Hat Enterprise Linux Load Balancer (for RHEL Server)
                     Red Hat Enterprise Linux Load Balancer (for RHEL Server) - Extended Update Support
                     Red Hat Enterprise Linux Resilient Storage for IBM z Systems - Extended Update Support
                     Red Hat Enterprise Linux Resilient Storage for x86_64
                     Red Hat Enterprise Linux Resilient Storage for x86_64 - Extended Update Support
                     Red Hat Enterprise Linux Scalable File System (for RHEL Server)
                     Red Hat Enterprise Linux Scalable File System (for RHEL Server) - Extended Update Support
                     Red Hat Enterprise Linux Server
                     Red Hat Enterprise Linux Server - Extended Life Cycle Support
                     Red Hat Enterprise Linux Server - Extended Life Cycle Support (for IBM z Systems)
                     Red Hat Enterprise Linux Server for ARM
                     Red Hat Enterprise Linux Server for ARM Beta
                     Red Hat Enterprise MRG Messaging
                     Red Hat Enterprise Virtualization for Power, little endian
                     Red Hat Gluster Storage Management Console (for RHEL Server)
                     Red Hat Gluster Storage Nagios Server
                     Red Hat Gluster Storage Server for On-premise
                     Red Hat Gluster Storage Web Administration (for RHEL Server)
                     Red Hat JBoss Core Services
                     Red Hat Openshift Application Runtimes for IBM Power LE
                     Red Hat OpenShift Container Platform
                     Red Hat Openshift Serverless
                     Red Hat OpenShift Service Mesh
                     Red Hat OpenStack
                     Red Hat OpenStack - Extended Life Cycle Support
                     Red Hat OpenStack Beta
                     Red Hat OpenStack Director Deployment Tools
                     Red Hat OpenStack Director Deployment Tools Beta
                     Red Hat OpenStack Director Deployment Tools Beta for IBM Power LE
                     Red Hat OpenStack Director Deployment Tools for IBM Power LE
                     Red Hat Satellite Proxy
                     Red Hat Software Collections (for RHEL Server for ARM 64)
                     Red Hat Software Collections (for RHEL Server for ARM)
                     Red Hat Software Collections (for RHEL Server for IBM Power LE)
                     Red Hat Software Collections (for RHEL Server)
                     Red Hat Software Collections Beta (for RHEL Server for ARM 64)
                     Red Hat Software Collections Beta (for RHEL Server for ARM)
                     Red Hat Software Collections Beta (for RHEL Server for IBM Power LE)
                     Red Hat Software Collections Beta (for RHEL Server)
                     Red Hat Software Test Suite 5 (for RHEL Server)
                     Red Hat Storage Console
                     Red Hat Storage Console Node
                     Red Hat Storage for Public Cloud (via RHUI)
                     Red Hat Virtualization
                     Red Hat Virtualization - ELS
                     Red Hat Virtualization - Extended Update Support
                     Red Hat Virtualization for IBM Power LE
                     Red Hat Virtualization Host
                     Red Hat Virtualization Host - Extended Update Support
                     Red Hat Virtualization Manager
                     dotNET on RHEL (for RHEL Server)
                     dotNET on RHEL Beta (for RHEL Server)
                     JBoss Enterprise Application Platform
                     OpenJDK Java (for Middleware)
                     Oracle Java (for RHEL Server)
                     Oracle Java (for RHEL Server) - Extended Update Support

SKU:                 RC0000000
Contract:            00000000
Pool ID:             00000000000000000000000000000000
Provides Management: Yes
Available:           000000
Suggested:           0
Service Type:        L1-L3
Roles:
Service Level:       Premium
Usage:
Add-ons:
Subscription Type:   Stackable
Starts:              01/01/2020
Ends:                01/16/2021
Entitlement Type:    Physical
```
