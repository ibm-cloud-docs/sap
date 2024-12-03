---

copyright:
  years: 2020, 2024
lastupdated: "2024-12-03"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, downloading SAP software, installing SAP software, SAP Download Manager

subcollection: sap

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:note: .note}

# Download and install SAP software and applications
{: #download-install-media}

SAP software installation media must be obtained from SAP directly, and requires valid license agreements with SAP in order to access these files.

All downloads of SAP software installation media are handled through the [SAP Support Portal](https://support.sap.com/en/index.html){: external} and the [SAP ONE Support Launchpad](https://launchpad.support.sap.com/). This was formerly called the SAP Marketplace (SMP) and may still be referred as this in some SAP documentation.

Downloads can be performed directly to a target server (when outbound internet connectivity is permitted), or could be downloaded to an administrator laptop and uploaded to an {{site.data.keyword.cloud_notm}} storage option (such as {{site.data.keyword.cloud}} Object Storage).

To perform a specific SAP installation, there are many different SAP software components and different installation media compressed archive files. The software might potentially use:
- SAP's own compression format called "SAP Archive (SAR)" which can only be decompressed with the SAPCAR program
- SAP's own compression format for Java called "Software Component Archive (SCA)"
- Provided in ZIP or RAR (self-extracting EXE) compression formats

The many various SAP software components to download can be downloaded individually by using a web browser, or these download items can be added to the [SAP Download Basket](https://me.sap.com/downloadbasket){: external} to make it easier for downloading all files by using the [SAP Download Manager](https://support.sap.com/en/my-support/software-downloads.html){: external} (a Java GUI application).

After the SAP software installation media downloads, follow the standard SAP installation procedure that is documented in the applicable [SAP installation guide](https://service.sap.com/instguides){: external} for your SAP version and components and the corresponding [SAP Notes](https://support.sap.com/en/my-support/knowledge-base.html){: external}. The installation guide and SAP Notes require an SAP S-user ID to view.

It is important to remember that any SAP software installation will require some OS configuration; some of this information is available under the [Compute and OS Design Considerations](/docs/sap?topic=sap-compute-os-design-considerations) section. This OS configuration can be done before or after the SAP software installation media downloads have completed.


For IBM Power, you may want to install AIX Toolbox for Linux Applications for easier handling of SAP software.
{: tip}

For more help with SAP Download Manager, see:
- [SAP Note 2272824 - How to Download SAP Solution Manager - SAP ONE Support Launchpad](https://me.sap.com/notes/2272824)
- [SAP Note 1371839 - How to install SAP Download Manager - SAP ONE Support Launchpad](https://me.sap.com/notes/1371839)
- [SAP Note 2624390 - What is the current version of SAP Download Manager?](https://me.sap.com/notes/2624390)
