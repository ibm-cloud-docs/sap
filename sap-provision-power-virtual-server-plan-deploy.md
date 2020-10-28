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

# Planning your deployment
{: #power-vs-planning-items}

This is a complementary offering from IBM Power Systems, with low latency access to {{site.data.keyword.cloud_notm}} services
{: note}

You should be familiar with the fundamental components and options that are provided by IBM Power Systems Infrastructure offerings for SAP. Before you start deploying the servers, make sure that you read the important information in the **_Get Started_** section.
{: shortdesc}


## The 'Must-Reads' before you start deployment
{: #power-vs-must-reads}

To ensure that your first deployment is a success, read:
* [IBM Power Systems Infrastructure environment introduction](/docs/sap?topic=sap-power-env-introduction)
* The _Plan your SAP workloads_ topic group
* The _Infrastructure for SAP design considerations_ topic group - especially the chapter [Sample storage configurations on IBM Power Infrastructure](/docs/sap?topic=sap-storage-design-considerations#sample-power)

### Other useful documents
{: #power-vs-other-useful-docs}

See the respective topics in the **_Get Started_** section for:
* [SAP HANA design considerations for High Availability and Disaster Recovery (HA/DR)](/docs/sap?topic=sap-hana-design-considerations#hana-ha)
* [SAP HANA backups - Storage impacts on Recovery Time Objective (RTO)](/docs/sap?topic=sap-storage-design-considerations#storage-performance-backup-rto)
* [SAP NetWeaver design considerations for High Availability configuration](/docs/sap?topic=sap-netweaver-design-considerations#netweaver-ha)


## IBM Power Virtual Server specific information
{: #power-vs-specific}

As the IBM Power Virtual Server is a complementary offering from IBM Systems, it is accessed as an extra offering from the IBM Cloud catalog.

To begin using IBM Power Virtual Servers, first make an instance of an IBM Power Virtual Server group from the IBM Cloud catalog. This server group is called a **resource**, and is contained within a **resource group**.

After the IBM Power Virtual Server group exists, it is shown as a **resource** in the Dashboard and can be accessed. Opening the resource shows the **service** page of the IBM Power Virtual Server group and new **instances** of IBM Power Virtual Servers can be provisioned.

More information and descriptions are available previously in [Constructs for provisioning IBM Power Virtual Servers](/docs/sap?topic=sap-iaas-offerings#iaas-power-vs-constructs)

