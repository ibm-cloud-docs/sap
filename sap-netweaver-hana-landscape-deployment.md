---

copyright:
  years: 2023
lastupdated: "2023-11-21"

keywords: SAP, {{site.data.keyword.ibm_cloud_sap}}

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

# Business Value of Deployment Automations for SAP systems
{: #nw-hana-landscape-deployment-business-value}

SAP projects vary widely in scope and budget, but none of them are considered trivial.  Whether you are delivering a new SAP system or implementing changes to an existing one, the requirements for error-free execution and shortening project time to realized benefits are always present.

In many scenarios of SAP projects, the deployment of an SAP system is often a key and repeated task. In this Business Solution Guide we will discuss the automated deployment of SAP NetWeaver (ABAP) with HANA database on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) and two useful automations will be discussed later.

## Who does the automation help?
{: #nw-hana-landscape-deployment-help}

This deployment automation helps the business who has plans to migrate SAP databases to HANA while keeping NetWeaver as the application layer.

## Deployment automation of SAP NetWeaver with HANA
{: #nw-hana-landscape-deployment-deploy}

This deployment automation delivers two VSIs along with storage and secure network. You can choose the VSI profile size for the HANA DB and SAP NetWeaver application server.

### Prerequisites 
{: #nw-hana-landscape-deployment-prerequisites}

1. Acquiring the SAP NetWeaver (ABAP) with SAP HANA installation software from SAP, 
2. Deploying the bastion (SAP install kit) server.

The automation deploys SAP certified infrastructure and the application/database software in one step. The installation process follows SAP Master Installation Guides. For more information, please see [VPC for SAP HANA NetWeaver ABAP](https://cloud.ibm.com/catalog/architecture/deploy-arch-ibm-sap-vpc-automation-hana-nw-abap-c0fc9daf-791b-42d2-9fe3-406f267b89ac-global)

### Automation
{: #nw-hana-landscape-deployment-prerequisites}

Following are the two automations for the SAP NetWeaver application server.

1. After your HANA DB and SAP NetWeaver application servers are up and running, an additional SAP application server is deployed to increase the workload capacity. With this automation deploying infrastructure, performing SAP SW installation, and joining the existing SAP system in less than twenty minutes is possible. For more information, please see [VPC with Additional Application Server ABAP on Linux for SAP HANA](https://cloud.ibm.com/catalog/architecture/deploy-arch-ibm-sap-vpc-automation-hana-nw-abap-aas-51f5ccbc-23fe-42d0-a17c-7a90b73da835-global).

![Figure 1. Sample reference architecture](images/refarch-sap-hana-single-host-only.svg "SAP NetWeaver 7.x with SAP HANA standard 3-tier installation to {{site.data.keyword.cloud_notm}} VPC"){: caption="Figure 1. SAP NetWeaver 7.x with SAP HANA standard 3-tier installation to {{site.data.keyword.cloud_notm}} VPC" caption-side="bottom"}

2. And with this system being a key part of your SAP project, you will want to back up the HANA database.  Please see this <link> to automatically provision and integrate IBM Cloud Object Storage resources as a backup target within HANA Studio by the backint agent for IBM Cloud available from SAP.

