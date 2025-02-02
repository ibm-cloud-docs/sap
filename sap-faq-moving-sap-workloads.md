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
{:faq: data-hd-content-type='faq'}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# FAQ of Moving SAP Workloads
{: #faq-moving-sap-workloads}


## How do I move an existing SAP workload to {{site.data.keyword.cloud_notm}}?
{: #faq-moving-sap-workloads-overview}

An existing {{site.data.keyword.ibm_cloud_sap}} workload can either be:
- Moved as-is, from on-premises data centers to Cloud IaaS. This method is often called "lift-and-shift"
- Migrating from one vendor or version to another:
    - Changing vendor or version of the database server (for example, IBM Db2 to SAP HANA)
    - Changing the version of the Application server (for example, SAP NetWeaver AS ABAP 7.0 to SAP NetWeaver AS ABAP 7.52)
    - Changing the version of the Business Application (for example, SAP ECC to SAP S/4HANA)

Moving workloads is an infrastructure-level change that affects the SAP systems because networking and storage changes are involved.

Migrating workloads is an application-level change that affects the SAP system installation because new software is being used.

_For VMware-based SAP workloads that are running in on-premises data centers, depending on the existing setup, the movement of these virtual machines into {{site.data.keyword.cloud_notm}} for VMware may potentially be simplified with the usage of VMware HCX_


## How do I move an existing SAP HANA database or relational database to an SAP HANA-certified server in the {{site.data.keyword.cloud_notm}}?
{: #faq-moving-db-to-cloud}

No move or migration services are included with Cloud Infrastructure-as-a-Service (IaaS).

Any move or migration activities are your responsibility.

While {{site.data.keyword.cloud_notm}} does not provide SAP application-level services, the various {{site.data.keyword.cloud_notm}} Business Partners (including {{site.data.keyword.IBM_notm}} Services) do provide their service capabilities by using {{site.data.keyword.ibm_cloud_sap}} portfolio.

This table shows a brief list of services and the Business Partner types who can provide the services for {{site.data.keyword.ibm_cloud_sap}}:

|**Service Partner type**|**Provided by**|**Description**|
|-----|-----|-----|
|Consulting and advisory and implementation|SAP "Global Systems Integrator" (GSI) providers|These providers advise and run SAP implementation and deployment projects.|
|Application management|SAP "Application Management Services" (AMS) providers|These providers manage and maintain an existing deployment of SAP Applications (optional: incremental functional or development changes).|
|End-to-end managed services|SAP "Managed Services Providers" (MSP)|These providers run the SAP implementation and deployment and manage the Infrastructure, OS, SAP Technical Applications, and SAP Business Applications. These services often do not include incremental functional or development changes.|
{: caption="Services Partner types" caption-side="top"}

For example, some of the services that are available in partnership between {{site.data.keyword.cloud_notm}} and IBM Services include:
- [GSI and IBM Services: SAP Consulting & Implementation Services](https://www.ibm.com/consulting/sap){: external}
- AMS and IBM Services: SAP Application Management and Development Solutions
- [MSP and IBM Services: Managed Applications for SAP](https://www.ibm.com/consulting/sap-managed){: external}


## At a high level, what are my options for moving and migrating an existing SAP system to Cloud?
{: #faq-moving-migrating-options}

This table is a high-level list of the options for moving and migrating existing SAP systems to cloud. For more information, see all of the necessary SAP documentation on moving and migrating SAP workloads, and liaise with your Systems Implementer for SAP.

|**Move and Migrate SAP workloads approach**|**Description**|**Common Usage**|**Downtime**|**Pre/Post Migration Work**|**Data Transfer**|
|-----|-----|-----|-----|-----|-----|
|Heterogeneous System Copy that uses SWPM|Move or Re-Platform to different CPU Architecture, OS, or database. Uses System Copy Export/Import of SWPM|Commonly used to change database server in preparation for more significant move; for example, move to SAP HANA DB with a Classical Migration approach|Yes|Significant preparation and post processing required.|System Copy Export dump|
|Homogeneous System Copy that uses SWPM _(Only option for System Copies that are running SAP HANA DB)_|Move or Re-Platform to more to newer OS or database version. Cannot be used to move between CPU Architecture or Endianness. Uses SAP Database Backup with SWPM.|Move to new SAP HANA target, or to move to new OS target with existing AnyDB. Also used to create fresh sandboxes of an existing system (with or without Logical System Name change).|Yes. Some reduction in downtime possible depending on preparation, change freeze, and delta change sync (of log files) in failover to target|Less preparation than heterogeneous System Copy; moderate to significant post processing required depending on scenario (in most cases, the Logical System Name is changed, for example, BDLS)|SAP Database Backup|
|SAP HANA System Replication (HSR) with replicated database mirror on cloud|Secondary failover site is hosted on cloud|HA and DR scenarios|Yes minimal, depends on design and failover factors (for example, cost-optimised or performance)|Using SAP Landscape Management (LaMa), set up and execution can be automated|Log or memory shipping; SYNCMEM, FULLSYNC, SYNC, or ASYNC modes|
|System Relocation that uses SAP LaMa to orchestrate move|Physically or virtually relocate running or shutdown instances. Uses SAP LaMa. Cannot relocate SAP HANA DB MDC Tenants.|Moving individual SAP instances to new infrastructure landscape that is already set up.|Yes minimal, running systems have downtimes as they are stopped, unprepared, then prepared and started|Using SAP LaMa, relocation can be automated|Package network transfer that uses LaMa or LaMa adapter for VMware|
|DMO for SUM with System Move execution _(Combines Migration, Unicode conversion, Upgrades, and more tasks)_|Supported for App Server (NetWeaver) upgrade or database conversion to SAP HANA. Moves the NW PAS + database server and upgrade - all at the same time.|Migrations to Business Suite on SAP HANA or part of SAP S/4HANA conversion migration (Brownfield)|Yes. Both PAS Target and database host in the target landscape (for example, {{site.data.keyword.cloud_notm}}) needs to be ready before DMO for SUM with System Move execution. |Significant preparation is required.|System Export is performed in source landscape. Import is performed in target landscape. The export/import can also be done in parallel.|
| Selective Data Transition, with Shell Conversion | Create shell of SAP System with Customizing and Development only; then upgrade / conversion to either SAP ECC or SAP S/4HANA. Migrate selective data from ECC to the upgraded shell system | Used in Business split scenarios (e.g. Divestitures), and Transformation/Conversion projects with SAP S/4HANA | Yes minimal, the target can be built and tested in advance (repeatedly testing the conversion steps and remediation). Data can be replicated to target in advance, so downtime is only for the delta data synchronization and replacing the old system with target. | Significant preparation is required. | SAP Landscape Transformation Replication Server, handled through a direct SAP engagement |
| Selective Data Transition, with Mix & Match | Merge of two or more system configurations to create a new SAP System with required configuration; then upgrade / conversion to SAP S/4HANA | Used in Business merge scenarios (e.g. Acquisitions) or multiple SAP system consolidation (e.g. ERPs for each Geographic Region or Business Units), and Transformation/Conversion projects with SAP S/4HANA | Yes minimal, the target can be built and tested in advance (repeatedly testing the conversion steps and remediation). Data can be replicated to target in advance, so downtime is only for the delta data synchronization and replacing the old system with target. | Significant preparation is required. | SAP Landscape Transformation Replication Server, handled through a direct SAP engagement |
{: caption="List of Move and Migrate SAP workloads approaches" caption-side="top"}
