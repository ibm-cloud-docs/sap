---
copyright:
  years: 2025
lastupdated: "2025-07-30"
keywords:
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Overview of SAP NetWeaver 7.5 with Db2
{: #overview-sap-db2}

This document describes the overview and the architecture of the SAP NetWeaver 7.5 solution with Db2 as the underlying database running on Red Hat Enterprise Linux&reg; 9.4 operating system in the IBM Cloud VPC environment.

The configuration refers to a 2-tier installation where the SAP application layer and the Db2 database layer are running on the same Virtual Server Instance (VSI) as part of the IaaS offering within the IBM Cloud VPC environment. The Db2 database versions that are mentioned in this documentation are 11.5.9 and 12.1.0, both are the latest supported versions by SAP.

The operating system where both applications run is Red Hat Enterprise Linux&reg; 9.4.

![Figure 1. Architecture Diagram - SAP NetWeaver 7.5 with Db2](../../images/vpc-intel-vsi-arch-diagram-db2.svg "Architecture Diagram - SAP NetWeaver 7.5 with Db2"){: caption="Architecture Diagram - SAP NetWeaver 7.5 with Db2" caption-side="bottom"}

The software that is used for this type of installation are as follows:

| ibm-redhat-9-4-amd64-sap-applications-4 |  Operating System Image for SAP System Installation |
| ---------: | ---------: |
|  51058203.ZIP |  Db2 FOR LUW 11.5 MP9 FP0 SAP5 LINUX x86_64 |
|  51058205.ZIP |  Db2 FOR LUW 11.5 MP9 FP0 SAP5 Client |
|  51058234.ZIP |  Db2 FOR LUW 12.1 MP0 FP0 LINUX x86_64 |
|  51058235.ZIP |  Db2 FOR LUW 12.1 MP0 FP0 Client |
|  SAPHOSTAGENT65_66-80004822.SAR |  SAP HOST AGENT 7.22 SP66 |
|  SAPEXE_400-80007612.SAR |  SAP Kernel 7.54 64-bit Unicode PL400 DB Independent |
|  SAPEXEDB_400-80007642.SAR |  SAP Kernel 7.54 64-bit Unicode PL400 for SAP Db2 |
|  igsexe_5-80007786.sar |  SAP IGS 7.54 |
|  igshelper_17-10010245.sar |  SAP IGS Helper |
|  51050829_3.ZIP |  SAP NW 7.5 ABAP Installation Export |
|  SWPM10SP42_5-20009701.SAR |  SoftwareProvisioningManager 1.0 SP42 PL5 |
|  SAPCAR_1300-70007716.EXE |  SAPCAR utility 7.53 PL1300 |
{: caption="Software installation" caption-side="top"}

Following are the deployment phases:

1. [Cloud infrastructure planning](/docs/sap?topic=sap-deploy-sap-db2#cloud-infra-plan)
2. [Cloud resource deployment](/docs/sap?topic=sap-deploy-sap-db2#cloud-resource-deploy)
3. [Filesystems preparation](/docs/sap?topic=sap-deploy-sap-db2#file-system-prep)
4. [Operating system preparation](/docs/sap?topic=sap-deploy-sap-db2#os-prep)
5. [Installation of SAP NetWeaver 7.5 with Db2 with Software Provisioning Manager (SWPM)](/docs/sap?topic=sap-deploy-sap-db2#install-sapnw-db2)
