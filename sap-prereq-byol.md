---

copyright:
  years: 2020, 2024
lastupdated: "2024-12-03"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP NetWeaver, BYOL, bring your own license, SAP certified, SAP License, SAP license key, saplikey, OEM, ASL, SAP OEM

subcollection: sap

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:table: .aria-labeledby="caption"}


# Bring your own SAP product license
{: #bring-your-own-sap-product-license}

The {{site.data.keyword.cloud}} SAP-Certified infrastructure is "bring your own license" (BYOL) capable for your SAP products. You need to apply the corresponding license for your SAP products after installation. If you are new to SAP, start exploring under [SAP All Products](https://www.sap.com/products.html){: external}. You can also contact SAP Sales or SAP Support for details on how to obtain the required licenses.
{: shortdesc}


## Database licenses for SAP general information
{: #byol-db}

It is recommended to check the license type of your Database Server for your SAP installation scenario onto {{site.data.keyword.cloud_notm}} to avoid unsupported scenarios. Typically the license types are:
- Full-use database license. Supports both SAP and non-SAP software, unrestricted usage of all functions
- Runtime database license. Solely to support software licensed from SAP, restricted usage of functions required by the SAP Business Application. This usage can include databases from other vendors if those databases were purchases as OEM products through SAP.

## SAP AnyDB - Bring your own IBM Db2 license
{: #byol-anydb-ibmdb2}

If you purchased your SAP and {{site.data.keyword.IBM_notm}} Db2 (for Linux, UNIX, Windows also known as LUW) licenses as part of an original equipment manufacturer (OEM) application-specific licensing (ASL) agreement, you can download and apply license files from the [SAP Support Portal)](https://support.sap.com/en/index.html){: external}; click **Download Software**. For more information, see [SAP Note 816773 - DB6: Installing the Application-Specific Db2 License from SAP](https://me.sap.com/notes/816773){: external}.

However, if you purchased {{site.data.keyword.IBM_notm}} Db2 from {{site.data.keyword.IBM_notm}} or an {{site.data.keyword.IBM_notm}} Business Partner, you must use the license files that are provided by your vendor instead.

## SAP HANA licenses
{: #byol-hana}

SAP HANA licenses are handled by the SAP HANA Cockpit.

## SAP NetWeaver and SAP Business Application licenses
{: #byol-netweaver}

Both the SAP NetWeaver application server license and the SAP Business Application license are handled by SAPGUI (or variation thereof).

### Creating an SAP license key
{: #byol-netweaver-create-key}

The SAP license key, `saplikey` or `SLICENSE`, displays a "hardware key" based on the hardware ID.

Use the following steps to create your SAP license key.

1. Run the `saplikey -get` command on the server that you're using as the SAP message server. As an alternative to the `saplikey` command and if your SAP system is already installed, you can use the `SLICENSE` transaction to retrieve the `HARDWARE KEY`.
2. Use the `HARDWARE KEY` output value to generate a valid SAP software license from [SAP Support](https://support.sap.com/en/index.html){: external}. Select **Request Keys** and enter you SAP S-user ID.
3. Use the SAP system data to create a valid license key.
4. Use the SAP transaction `SLICENSE` or the `saplikey` command to install the license on your SAP system.


### SAP license key with {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s
{: #byol-netweaver-powervs}

This is a complementary offering from {{site.data.keyword.IBM_notm}} Power Systems, with low latency access to {{site.data.keyword.cloud_notm}} services
{: note}

SAP changed the mechanism for licensing when an {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} is running on the {{site.data.keyword.cloud}}. In the {{site.data.keyword.cloud_notm}}, the product license is not dependent on the underlying hardware that's running the server. The license is associated with the virtual server.

The hardware key is no longer based on the hardware ID, but on the partition UUID of the logical partition (LPAR). The partition UUID is persisted by the platform across restarts, reconfigurations, OS reinstalls, and partition migrations, making it independent of the hardware that is running the virtual servers.

When you request an SAP software license, be sure to use the `HARDWARE KEY` that was generated with an SAP kernel that has the minimum level that is required for running on {{site.data.keyword.cloud_notm}}. The minimum SAP Kernel patch level of the kernel is found in [SAP Note 2879336](https://me.sap.com/notes/2879336){: external}. Otherwise, your SAP software license becomes invalid as soon as your virtual server is moved to different hardware in the {{site.data.keyword.cloud}}.
