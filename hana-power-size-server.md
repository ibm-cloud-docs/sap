---

copyright:
  years: 2024
lastupdated: "2024-12-04"

keywords: SAP HANA, SAP Application Performance Standard, SAPS, SAP Quick Sizer

subcollection: sap-hana-power

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}


# Sizing the server
{: #size_the_server}

After you decide which SAP solutions you plan to use, your next step is to determine the number of {{site.data.keyword.IBM}} {{site.data.keyword.powerSysShort}}s that you need to support your SAP landscape, and ensure that the servers are correctly sized.
{: shortdesc}

## Understanding the SAP sizing methodology
{: #size_method}

The SAP sizing methodology for SAP software is based on SAP benchmarks, such as information from SAP and actual customer experiences. The base SAP workload unit is an SAP Application Performance Standard (SAPS). The SAPS is a definition of throughput that is coined by SAP capacity planning and performance testing personnel. For example, 100 SAPS are defined as 2,000 fully business processed order-line items per hour in the standard SAP Sales and Distribution (SAP SD) application benchmark. This is equivalent to 2,400 SAP transactions per hour with the SAP Enterprise Resource Planning (SAP ERP) solution. The capability of processors is measured during the standard (SAP SD) benchmark test that is certified by SAP. For more information about the benchmark methodology, see [SAP Standard Application Benchmarks](https://www.sap.com/about/benchmark.html){: external}.

## Using the SAP Quick Sizer
{: #quicksizer}

The [SAP Quick Sizer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer=){: external} is a web-based tool that is provided by SAP for all partners and customers. Sizing information is input directly into the tool, where it sizes SAP NetWeaver-based applications and SAP HANA databases. Be sure to select the **classic version** of the tool for sizing SAP NetWeaver-based applications, and the **HANA version** of the tool to size SAP HANA databases. SAP Quick Sizer requires an SAP S-user ID.

It calculates the workload (in SAPS) and adjusts it to allow for suitable processor use. So, if a workload of 4,800 SAP SD benchmark transactions per hour was required, the tool calculates this as 200 SAPS. If a target processor load of 33% is allowed, adjust this to find a processor capable of 600 SAPS at 100% (=200 at 33%).

While the sizing method might be considered conservative, consider the fact that the SAPS numbers for your servers were calculated based on highly tuned SAP systems running only specific SAP SD workloads. Depending on the type of SAP application, any custom configuration or custom coding in your system, your results might vary. Also, requirements for your project, such as proof-of-concept (PoC) or regarding performance and response time, might be different.

## Choosing a Power Systems Virtual Server
{: #choose_server}

After you determine your SAP applications and the SAPS numbers are calculated through the SAP Quick Sizer, or based on your current landscape, you are ready to choose a server. Currently, e980 is the only supported server type. The list of supported server types will be extended in [SAP Note 2855850](https://me.sap.com/notes/2855850).

## Mapping CPUs derived from SAPS to instance profiles in a Power Systems Virtual Servers environment
{: #mapping-cpus}

The SAPS results from the sizing, and the choice of the server results in the number of CPUs and memory size that are required to support your workload. The number of CPUs and memory are grouped into instance profiles that are certified for the SAP HANA workload. To find certified profiles, see [Find Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas){: external}.

* [Click here](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:28){: external} to find certified profiles for {{site.data.keyword.cloud}}.
* [Click here](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:28;v:ca270;v:60ed2297-5cdd-4387-89c2-b0d3651d1206){: external} to find certified profiles for {{site.data.keyword.IBM}} {{site.data.keyword.powerSys_notm}}s.

When you create a {{site.data.keyword.powerSys_notm}} by using the {{site.data.keyword.cloud}} console, you select an instance profile with a predefined number of CPUs and memory size that suits your workload. While your data must fit into the instance memory, choosing an instance profile with more CPUs improves performance. Instances are structured with a standardized naming convention as follows: `AAA-BBxCCCC`

Where:

`AAA` = One of the following profile types that are associated with the required Service Level Agreements (SLAs):

* `np1` = Non-product development for testing or development use only. Not intended for production deployments; not supported or certified by SAP production.
* `umh` = Ultra Memory HANA for OLTP using 240:1 memory:core ratios
* `mh1` = High Memory for OLAP using 180:1 memory:core ratios
* `bh1` = Balanced for OLAP using 100:1 memory:core ratios
* `ch1` = Compute Intensive for OLAP using 50:1 memory:core ratios

`BB` = The number of CPU cores

`CCCC` = The amount of available reserved RAM memory in GB


## Migrating an existing SAP system
{: #migrating}

If you are planning to migrate an existing SAP system from any source to your {{site.data.keyword.powerSys_notm}}s environment, you can determine the SAPS numbers from the SAPS numbers of your current environment. Use the information on your current workload (the CPUs and RAM used) and get the SAPS equivalents for your CPU from the [SAP SD benchmark results](https://www.sap.com/about/benchmark.html){: external}.

## For more information
{: #more_info}

For more information about sizing, see the following resources:

* [Sizing SAP HANA](https://help.sap.com/docs/SAP_HANA_PLATFORM/eb3777d5495d46c5b2fa773206bbfb46/d4a122a7bb57101493e3f5ca08e6b039.html){: external}
* [Quick Sizer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer){: external}
* [SAP Note 1736976 - Sizing Report for BW-on-HANA](https://me.sap.com/notes/1736976){: external}
* [SAP Note 1872170 - Suite on HANA memory sizing](https://me.sap.com/notes/1872170){: external}
* [SAP Note 1793345 - Sizing for SAP Suite on HANA](http://service.sap.com/sap/support/notes/1793345){: external}
* [SAP Note 1514966 - SAP HANA: Sizing SAP HANA](https://me.sap.com/notes/1514966){: external}
* [SAP Certified and Supported SAP HANA Hardware](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/index.html){: external}
* [SAP Note 2055470 - SAP HANA on POWER Planning and Installation Specifics - Central Note](https://me.sap.com/notes/2055470){: external}
* [SAP HANA Storage Requirements](https://www.sap.com/documents/2024/03/146274d3-ae7e-0010-bca6-c68f7e60039b.html){: external}
* [SAP Certified Enterprise Storage Hardware for SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=storage){: external}


## Next Steps
{: #size-server-next-steps}

[Configuring high availability](/docs/sap-hana-power?topic=sap-hana-power-ha_config)

[Determining your configuration](/docs/sap-hana-power?topic=sap-hana-power-determine_configuration)
