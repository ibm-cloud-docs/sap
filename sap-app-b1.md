---

copyright:
  years: 2020, 2021
lastupdated: "2021-05-27"

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

# SAP Business One (B1)
{: #b1}

SAP Business One is an enterprise resource planning (ERP) software that is especially designed for small-to-medium enterprises. It integrates business management - accounting and financials, purchasing and inventory, sales and customer relationships, and project management and operations - into one application.
{: shortdesc}

The single application eliminates the need for multiple installations and interfaces across separate modules. As your business grows, you can expand SAP Business One to fit your needs by adding one of over 500 add-on solutions from SAP Partners. It runs on both the SAP HANA and Microsoft SQL Server platforms and helps with the day-to-day operations of your enterprise. For more information, see [SAP Business One](https://www.sap.com/products/business-one.html){: external}

Several {{site.data.keyword.ibm_cloud_sap}} infrastructure options are certified to run SAP Business One.

Before you implement SAP Business One, considerations need to be made how are you going to use the application. For example, how many concurrent users will use the application at one time? Do you need only the Financial Management and Sales and Customer Management modules to start? Maybe you need all the modules (for more information about modules, see [SAP Business One Features](https://www.sap.com/products/business-one/features.html){: external}).


## SAP Business One installation guides
{: #b1-installation-guides}

You have the choice of working with an SAP partner or installing the software yourself onto Cloud IaaS.

**The main documentation to read for SAP Business One are:**
- [SAP Help Portal - SAP Business One on SAP HANA](https://help.sap.com/viewer/product/SAP_BUSINESS_ONE_VERSION_FOR_SAP_HANA/latest/en-US){: external}
- [SAP Help Portal - SAP Business One on Microsoft SQL Server](https://help.sap.com/viewer/product/SAP_BUSINESS_ONE/latest/en-US){: external}

**After which, the administrator guides to read for SAP Business One are:**
- [SAP Business One on SAP HANA Administrator Guide](https://help.sap.com/doc/4e7c047f2c9e4cbe97800ffaf7b68f8e/10.0/en-US/B1_for_SAP_HANA_Admin_Guide.pdf){: external}
- [SAP Business One on Microsoft SQL Administrator Guide](https://help.sap.com/doc/601fbd9113be4240b81d74626439cfa9/10.0/en-US/AdministratorGuide_SQL.pdf){: external}

**Lastly, review the hardware requirements guidance for SAP Business One (requires SAP ID login):**
- [Latest SAP Business One Hardware Requirements Guide](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/latest/en-US/B1_Hardware_Requirements_Guide.pdf){: external}
- [SAP Business One Platform Support Matrix](https://support.sap.com/en/offerings-programs/support-small-medium-enterprises/business-one.html){: external}

For a list of Microsoft Windows Server versions supported for the following SAP Business One server platforms, see the [SAP Business One Platform Support Matrix](https://help.sap.com/doc/011000358700000032462013e/9.3/en-US/B1_Platform_Support_Matrix.pdf){: external}.

More information on SAP HANA versions can be found in [SAP Note 2058870 - SAP Business One, version for SAP HANA on public Infrastructure-as-a-Service (IaaS) platforms](https://launchpad.support.sap.com/#/notes/2058870){: external} and [SAP Note 2586742 - Overview Note for SAP Business One 9.3 PL03, version for SAP HANA](https://launchpad.support.sap.com/#/notes/2586742){: external}.

The [SAP Business One community page](https://community.sap.com/topics/business-one){: external} has links to blogs where community members share their experiences with implementing and running SAP Business One.


### SAP Business One upgrade guides
{: #b1-upgrade-guides}

SAP Business One offers you several upgrade options when a new release is available

For more information, see the [SAP Business One Upgrade Strategy](https://help.sap.com/doc/011000358700001396142012e/9.3/en-US/B1_Upgrade_Strategy.pdf){: external} guide.

These options are available if you're using SAP HANA or Microsoft SQL. See the [SAP Business One upgrade patches and programs information](https://support.sap.com/en/offerings-programs/support-small-medium-enterprises/business-one/upgrades-patches.html){: external} for more detail on upgrades, patches, and hot fixes.


## SAP Business One training information
{: #b1-training}

Training is available on the integration framework for SAP Business One. The self-paced, online course takes you through scenario design basics, the integration of SAP Business One, and integrating the SAP Business One database. For more information, see the [OpenSAP courses on SAP Business One](https://open.sap.com/courses/ifb1){: external}.

Training is also available on the different SAP Business One modules. For more information about courses, see [SAP Training for Business One](https://training.sap.com/businessone){: external} or the online training available with SAP Learning Hub.


## Infrastructure options for SAP Business One
{: #b1-iaas}

[All the listed servers for SAP Business One on SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:28;v:120){: external} are currently certified for the SLES 12 SP4 and SLES 15 SP1 operating systems only.

SAP Business One is supported on the following Bare Metal servers:
- BI.S3.H2.192
- BI.S3.H2.384
- BI.S3.H2.768
- BI.S4.H2.192
- BI.S4.H2.384
- BI.S4.H2.768

SAP Business One is also supported for the following Intel virtual server profiles:
- mx2-16x128
- mx2-32x256
- mx2-48x384

{{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s are not available for SAP Business One
{: note}
