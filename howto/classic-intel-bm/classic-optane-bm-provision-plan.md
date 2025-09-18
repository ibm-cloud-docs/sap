---
copyright:
  years: 2020
lastupdated: "2021-05-05"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Planning your deployment
{: #bm-optane-planning-items}

Make sure that you are already familiar with the fundamental components and options that are provided by {{site.data.keyword.cloud_notm}} Classic Infrastructure for SAP. Before you start with the deployment of servers, make sure that you also read the Get Started section.

Intel Optane persistent memory (PMem) is available on the Bare Metal servers. You have three memory options for PMem on the Bare Metal servers, 1.5 TB, 3.0 TB, and 6.0 TB. Which option you choose depends on the:

*  Application that you want to run, for example BW or BW/4 HANA.
*  SAP sizing, which determines the amount of memory and CPU that you need.

Network and storage configuration, disaster recovery, high availability, backups, and system replication are all configured and managed as part of the Bare Metal provisioning and operation.

## The 'Must-Reads' before you start deploying
{: #bm-optane-must-reads}

To ensure that your first deployment is a success, review the information in Provisioning SAP HANA and SAP NetWeaver [Planning your deployment](/docs/sap?topic=sap-bm-planning-items#bm-must-reads)

### Other useful documents
{: #bm-optane-other-useful-docs}

See the respective topics in the **_Get Started_** section for the following information:
* [SAP HANA design considerations for High Availability and Disaster Recovery (HA/DR)](/docs/sap?topic=sap-hana-design-considerations#hana-ha)
* [SAP HANA backups - Storage impacts on Recovery Time Objective (RTO)](/docs/sap?topic=sap-storage-design-considerations#storage-performance-backup-rto)
* [SAP NetWeaver design considerations for High Availability configuration](/docs/sap?topic=sap-netweaver-design-considerations#netweaver-ha)
