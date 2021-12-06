---

copyright:
  years: 2020
lastupdated: "2021-12-06"

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

# Get started
{: #get-started}

{{site.data.keyword.ibm_cloud_sap}} is the continuation of a 45+ year IBM-SAP alliance across hardware, software, and services.
{: shortdesc}

With over 60 {{site.data.keyword.IBM_notm}} data centers worldwide, the {{site.data.keyword.cloud}} SAP-Certified Infrastructure gives you the flexibility to run your SAP workloads in the {{site.data.keyword.cloud_notm}} when and where you need them. You can quickly address issues such as:

* Rapidly expanding or contracting capacity
* Moving SAP workloads to the cloud 
* Supplementing an existing private cloud architecture.

The following documentation provides design considerations and guidance for provisioning the infrastructure to support SAP workloads, including: 

* SAP Business Applications such as SAP S/4HANA or SAP BW/4HANA 
* SAP Technical Applications such SAP HANA database server and SAP NetWeaver application server

You can use this information to help planning your SAP installation and running your SAP workloads on {{site.data.keyword.cloud_notm}}.

The following documentation does not replace any SAP implementation-related documentation.

## Before you begin
{: #before-you-begin}

Before you can install SAP software components on {{site.data.keyword.cloud_notm}}, you need an:
* SAP S-User ID to review relevant SAP documentation and download SAP installation media
* IBM ID to create an {{site.data.keyword.cloud_notm}} account

This information is covered in the *Pre-Requisites for SAP Workloads* topic group, under [Necessary account credentials for SAP and {{site.data.keyword.cloud_notm}}](/docs/sap?topic=sap-necessary-credentials).


## Summary of an SAP landscape installation onto Cloud IaaS
{: #summary-steps}

This table summarizes the SAP landscape installation steps for you and your team:

**Task**|**Details**
-----|-----
| Read the [Overview of {{site.data.keyword.ibm_cloud_sap}}](/docs/sap?topic=sap-overview) | Identify the various offerings that are available for your SAP landscape. Provides a high-level comparison of your options. |
| Read the relevant documents in the following topic groups: | * Infrastructure environments section for your specific environment, such as [IBM Power Systems Infrastructure environment introduction](/docs/sap?topic=sap-power-env-introduction) \n * [Infrastructure certified for SAP](/docs/sap?topic=sap-iaas-offerings) \n * [Sizing and planning SAP workloads](/docs/sap?topic=sap-sizing) \n \n Read these documents to identify the detailed infrastructure options and design considerations that apply to your SAP landscape. |
_Optional:_ Read the relevant SAP Business Partner certified solutions documents|Various SAP Open Ecosystem Partners are available from {{site.data.keyword.cloud_notm}}, with documents on how to best use these solutions for your SAP deployment.
_Optional:_ Read the relevant IBM-SAP innovation solutions documents|{{site.data.keyword.ibm_cloud_sap}} provides more options for maximizing your SAP landscape, including Cloud Migration Acceleration and {{site.data.keyword.ibmwatson_notm}}.
Read and follow the documents in the *Pre-Requisites for SAP Workloads* topic group|Prepare the credentials, account structure, connectivity, software downloads, support procedures, and licensing that is needed before you begin your deployment.
Read and follow the *Provisioning* topic groups|For your specific infrastructure, follow the provisioning guidelines to set up the first servers at the sizes that are required to run your SAP systems. Planning your SAP landscape with the business is crucial to success. It is likely these documents might be read many months after the topics listed in the other steps.
Revisit *Task 3*, and follow the relevant SAP software documentation to install SAP on the infrastructure|It is important to follow SAP guidance clearly, including any additional reference guidance available on SAP Notes for your chosen applications. This stage is the same as installations into servers hosted at on-premises data centers.
{: caption="Table 1. Overview of your SAP landscape installation steps" caption-side="top"}

## Next steps
{: #next-steps}

Review the following documentionat for your relevant configuration:

* [Fast Path of IBM Cloud Intel Bare Metal](/docs/sap?topic=sap-fast-path-site-map-intel-bm)
* [Fast Path of IBM Cloud Intel Virtual Servers](/docs/sap?topic=sap-fast-path-site-map-intel-vs-gen2)
* [Fast Path of IBM Power Virtual Servers](/docs/sap?topic=sap-fast-path-site-map-power-vs)
* [Fast Path of IBM Cloud for VMware](/docs/sap?topic=sap-fast-path-site-map-vmware-sddc)
