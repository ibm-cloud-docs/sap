---
copyright:
  years: 2025
lastupdated: "2025-12-23"
keywords:
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Overview of SAP NetWeaver 7.5 with ASE
{: #overview-sap-ase}

This document describes the overview and the architecture of the SAP NetWeaver 7.5 solution with Adaptive Server Enterprise (ASE) Sybase as the underlying database that runs on Red Hat&reg; Enterprise Linux&reg; 9.4 operating system in the {{site.data.keyword.vpc_short}} environment.

The configuration refers to a 2-tier installation where the SAP Application layer and the ASE Sybase database layer are running on the same underlying virtual server instance (VSI) as part of the IaaS offering within the {{site.data.keyword.vpc_short}} environment.

The ASE Sybase database versions that are supported are ASE16 SP04 PL06. The operating system where both applications run is RHEL 9.4.

![Figure 1. Architecture Diagram - SAP NetWeaver 7.5 with ASE Sybase](../../images/vpc-intel-vsi-arch-diagram-ase-sybase.svg "Architecture Diagram - SAP NetWeaver 7.5 with ASE Sybase"){: caption="Architecture Diagram - SAP NetWeaver 7.5 with ASE Sybase" caption-side="bottom"}

The software that is used for this type of installation are as follows:

| ibm-redhat-9-4-amd64-sap-applications-4 |  Operating System Image for SAP System Installation |
| ---------: | ---------: |
|  `51057961_1.zip` |  SAP ASE16 SP04 PL06 |
|  `SAPHOSTAGENT66_66-80004822.SAR` |  SAP HOST AGENT 7.22 SP66 |
|  `SAPEXE_500-80007612.SAR` |  SAP Kernel 7.54 64-bit Unicode PL500 DB Independent |
|  `SAPEXEDB_500-80007655.SAR` |  SAP Kernel 7.54 64-bit Unicode PL500 for SAP ASE |
|  `igsexe_5-80007786.sar` |  SAP IGS 7.54 |
|  `igshelper_17-10010245.sar` |  SAP IGS Helper |
|  `51050829_3.ZIP` |  SAP NW 7.5 ABAP Installation Export |
|  `SWPM10SP43_0-20009701.SAR` |  SoftwareProvisioningManager 1.0 SP43 |
|  `SAPCAR_1300-70007716.EXE` |  SAPCAR utility 7.53 PL1300  |
{: caption="Software installation" caption-side="top"}

Following are the deployment phases:

1. [Cloud infrastructure planning](/docs/sap?topic=sap-deploy-sap-ase#cloud-infra-plan-ase)
2. [Cloud resource deployment](/docs/sap?topic=sap-deploy-sap-ase#cloud-resource-deploy-ase)
3. [File system preparation](/docs/sap?topic=sap-deploy-sap-ase#file-system-prep-ase)
4. [Operating system preparation](/docs/sap?topic=sap-deploy-sap-ase#os-prep-ase)
5. [Installation of SAP NetWeaver 7.5 with Db2 with Software Provisioning Manager (SWPM)](/docs/sap?topic=sap-deploy-sap-ase#install-sapnw-ase)
