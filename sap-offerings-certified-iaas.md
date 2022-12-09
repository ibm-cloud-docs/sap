---

copyright:
  years: 2021, 2022
lastupdated: "2022-12-09"

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

# Infrastructure certified for SAP
{: #iaas-offerings}

Certified Infrastructure-as-a-Service for SAP HANA database servers and for SAP NetWeaver based applications is available in many variations, each with different capabilities and sizing available to fit many different SAP workload scenarios.
{: shortdesc}

For the official and full platform list of Infrastructure-as-a-Service from IBM that is SAP certified and supported for SAP HANA, see the [SAP Certified and Supported SAP HANA Hardware Directory - Certified IaaS Platforms - {{site.data.keyword.cloud_notm}}](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:28){: external}. For an official list of SAP NetWeaver supported bare metal and virtual servers, see [SAP Note 2927211](https://launchpad.support.sap.com/#/notes/2927211){: external}.

The documents provide detailed considerations and information for building your SAP environments at each layer for all offerings. However, if you are interested in quickly finding the information that is related specifically to one of the IaaS offerings, then you might consider using the Fast Path Site Maps for [Intel Bare Metal (Classic)](/docs/sap?topic=sap-fast-path-site-map-intel-bm), [Intel Bare Metal (VPC)](/docs/sap?topic=sap-fast-path-site-map-intel-bm-vpc), [Intel Virtual Servers (VPC)](/docs/sap?topic=sap-fast-path-site-map-intel-vs-gen2), [IBM Power Virtual Servers](/docs/sap?topic=sap-fast-path-site-map-power-vs), and [VMware SDDC](/docs/sap?topic=sap-fast-path-site-map-vmware-sddc).
{: tip}


## Intel Bare Metal servers on Classic Infrastructure
{: #iaas-intel-bm}

{{site.data.keyword.baremetal_long}} are physical servers with numerous customization capabilities.

The servers are dedicated for your use, or your customer's, and not shared in any part, including server resources, with other {{site.data.keyword.cloud_notm}} customers.

These servers are managed by the account holder, either you, your customer, or your services partner, depending on your business operations.

These servers are provisioned with your choice of operating system (OS) image that is directly installed to bare metal.

Because customization is controlled on bare metal servers, fast provisioning times of in the range 1 - 4 hours are obtainable with worldwide availability. For larger systems (greater 4 TB DRAM), there is a longer validation period for checking the hardware components (particularly RAM), but the machines are usually available within 24 hours.

For more information about Bare Metal servers on Classic Infrastructure, see [{{site.data.keyword.cloud_notm}} Bare Metal Servers](https://www.ibm.com/cloud/bare-metal-servers){: external} on ibm.com and [{{site.data.keyword.cloud_notm}} Bare Metal Servers for Classic - server options](/docs/bare-metal?topic=bare-metal-about-bm){: external} on {{site.data.keyword.cloud_notm}} Docs.


## Intel Bare Metal servers on Classic Infrastructure with Intel Optane DC Persistent Memory
{: #hana-iaas-intel-bm-optane}

Resource: [Intel Optane SSD](/docs/bare-metal?topic=bare-metal-ordering-ssd)

In addition, the {{site.data.keyword.cloud_notm}} Bare Metal Servers are also available with Intel&reg; Optane DC Persistent Memory, which is engineered for high-performance SAP production environments with ultra-high memory requirements and tuned by SAP to increase performance of SAP HANA 2.0 (SPS 03 and higher). Due to the design of Intel Optane DC PMEM, which uses dense storage format factor in DIMM slots with direct CPU access, the new storage medium has ultra low latency read/write and enables:
- Improved SAP HANA Dynamic Tiering with improved cost-efficiency and without reducing performance
- Reduced business downtime and improved RTO KPIs by moving the SAP HANA main data column store to Intel Optane DC Persistent Memory, with all data immediately accessible after planned or unplanned maintenance windows.



## Intel Virtual Servers on VPC Infrastructure
{: #iaas-intel-vson-vpc}

{{site.data.keyword.virtualmachineslong}} are virtual machine servers with extensive customization capabilities.

The servers run on a hypervisor that is managed by {{site.data.keyword.cloud_notm}}, providing you flexible compute by using a scalable infrastructure (available in multi-tenant or dedicated single-tenant).

These servers are managed by the account holder, either you, your customer, or your services partner, depending on your business operations.

These servers are provisioned with your choice of operating system (OS) image that is installed to {{site.data.keyword.virtualmachinesshort}}.

{{site.data.keyword.vpc_short}} Infrastructure is available for:
* Intel Virtual Servers, based on the latest hardware designs with large improvements across networking performance (up to 80 Gbps), provision times (5x faster), and a flexible selection of extra features

For more information about Virtual Servers on VPC Infrastructure, see [What is {{site.data.keyword.vpc_short}}?](https://www.ibm.com/cloud/learn/vpc){: external} and [{{site.data.keyword.cloud_notm}} Virtual Server for VPC](https://www.ibm.com/cloud/virtual-servers){: external} on ibm.com and [{{site.data.keyword.cloud_notm}} Intel Virtual Servers on VPC Infrastructure](/docs/vpc?topic=vpc-about-advanced-virtual-servers){: external} on {{site.data.keyword.cloud_notm}} Docs.



## Intel Bare Metal servers on VPC Infrastructure
{: #iaas-intel-bm-vpc}

{{site.data.keyword.baremetal_long}} are physical servers with numerous customization capabilities.

The servers are dedicated for your use, or your customer's, and not shared in any part, including server resources, with other {{site.data.keyword.cloud_notm}} customers.

These servers are managed by the account holder, either you, your customer, or your services partner, depending on your business operations.

These servers are provisioned with your choice of operating system (OS) image that is directly installed to bare metal.

Because customization is controlled on bare metal servers, fast provisioning times of in the range 1 - 4 hours are obtainable with worldwide availability. For larger systems (greater 3 TB DRAM), there is a longer validation period for checking the hardware components (particularly RAM), but the machines are usually available within 24 hours.

For more information about Bare Metal servers on VPC Infrastructure, see [Deploy {{site.data.keyword.cloud_notm}} Bare Metal Servers on VPC Infrastructure](https://www.ibm.com/cloud/bare-metal-servers/vpc){: external} on ibm.com and [About Bare Metal Servers for VPC](/docs/vpc?topic=vpc-about-bare-metal-servers){: external} on {{site.data.keyword.cloud_notm}} Docs.


## IBM Power Virtual Server
{: #iaas-power-vs}

This is a complementary offering from {{site.data.keyword.IBM_notm}} Power Systems, with low latency access to {{site.data.keyword.cloud_notm}} services
{: note}

{{site.data.keyword.IBM_notm}} Power Virtual Servers are virtual machine servers with enterprise-grade performance and extensive customization capabilities. These {{site.data.keyword.IBM_notm}} Power Virtual Servers are also known as an IBM Power Logical Partitions (LPARs).
{: shortdesc}

The servers run on IBM PowerVM (Type 1 hypervisor) managed by {{site.data.keyword.IBM_notm}} Power Systems and facilitated by {{site.data.keyword.cloud_notm}} are a form of Infrastructure-as-a-Service (IaaS) provisioned with your choice of operating system (IBM AIX or Linux&reg;) image that is installed and infrastructure customization (such as dedicated CPU performance or shared CPU for optimized costs). By using the IBM PowerVM underneath, the customization of the Virtual Servers is flexible, with fast self-service provisioning available in worldwide locations - all with pay-as-you-use billing that makes it easier for you to scale up and out.

{{site.data.keyword.IBM_notm}} Power Virtual Servers are colocated in the same {{site.data.keyword.cloud_notm}} data centers that are used by both {{site.data.keyword.cloud_notm}} Classic Infrastructure and VPC Infrastructure, but are separated from the rest of the {{site.data.keyword.cloud_notm}} servers with separate networks and storage. They can be connected to/from an on-premises network, the {{site.data.keyword.cloud_notm}} Classic Infrastructure or {{site.data.keyword.cloud_notm}} VPC Infrastructure networks by using {{site.data.keyword.dlc_full}}.

The {{site.data.keyword.IBM_notm}} Power Virtual Servers can be used for several workload scenarios such as disaster recovery, development environments, partial IT infrastructure moves and enabling you to stay competitive with flexible scaling of infrastructure capacity in a hybrid cloud deployment. This Infrastructure-as-a-Service (IaaS) offering is designed for large mission-critical workloads where scale-up over 12 TB of memory and density of 6,000 SAPS per CPU Core are required to meet performance.

As the {{site.data.keyword.IBM_notm}} Power Virtual Servers are IaaS once provisioned are managed by the account holder; either you, your customer, or your services partner depending on your business operations model.

{{site.data.keyword.IBM_notm}} Power Systems clients who rely on on-premises data center deployments for their infrastructure, can now quickly and economically extend their IBM Power resources into the cloud in a matter of minutes. The {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s provide:
- **Straightforward billing:** Hourly rates on Monthly Billing, with {{site.data.keyword.IBM_notm}} PowerLinux customers can use Bring-your-own-License (BYOL), to use their own licenses for the OS Images provided by {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s and reduce costs of their Cloud environment.
- **Enterprise Hybrid Cloud deployment:** run workloads on IBM Power in both Cloud IaaS and on-premises, accessing Cloud's self-service, fast delivery, elasticity, and connectivity to other {{site.data.keyword.cloud}} services. Although your Linux&reg; workloads are running in {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s, you keep the same scalable, resilient, production-ready features that Power Systems hardware is known to provide.
- **Infrastructure customization:** Flexibility of {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s hardware capabilities:
    * Cores (CPU)
    * Memory (RAM)
    * Data volume size
    * Data volume type / performance tier
    * Network interfaces
    * PowerVM Host Pinning Policy (soft or hard)
    * PowerVM Host CPU Binding (dedicated or shared)


For more explanation information about {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s, see [{{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s](https://www.ibm.com/cloud/power-virtual-server){: external} on ibm.com and [{{site.data.keyword.IBM_notm}} Power Systems Virtual Servers](/docs/power-iaas?topic=power-iaas-about-virtual-server){: external} on {{site.data.keyword.cloud_notm}} Docs.

If you'd like to compare your current environment's performance to what's available through the {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} service, see the [{{site.data.keyword.IBM_notm}} Power Systems performance report](https://www.ibm.com/downloads/cas/K90RQOW8){: new_window}{: external}. For a more condensed comparison, see [{{site.data.keyword.IBM_notm}} Power Systems CPW performance data comparison](https://www.itechsol.com/wp-content/uploads/2018/07/IBM-Power-Systems-CPW-Performance-Data-Comparison-P7-vs-P8-vs-P9-rev3-July-2018.pdf){: new_window}{: external}.
{: tip}


### Constructs for provisioning IBM Power Virtual Servers
{: #iaas-power-vs-constructs}

As the IBM Power Virtual Server is a complementary offering from {{site.data.keyword.IBM_notm}} Power Systems, it is accessed as an additional offering from the IBM Cloud catalog. To begin using IBM Power Virtual Servers, an instantiation of an IBM Power Virtual Server resource group must first be made.

The below sections explain this in more detail.

#### Resource versus Resource Group
{: #iaas-power-vs-constructs-resource-versus-group}

A **resource** in the context of {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s is not a user, it's anything that you can create from the catalog, for example, an {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}. A **resource group** contains multiple resources, for example, a set of servers used strictly for development activities. For more information, see [Creating resources](/docs/account?topic=account-manage_resource).

#### Service versus instance
{: #iaas-power-vs-constructs-service-versus-instance}

There is difference between an {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} **service** and an {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} **instance**. Think of the {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} **service** as a container for all {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} **instances** at a specific geographic location. The {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} **service** is available from the **Resource list** in the {{site.data.keyword.cloud}} UI. The service can contain multiple {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} **instances**.

For example, you can have two {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} **services**, one in Dallas and another in Washington DC. Each service can contain multiple {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} **instances**. For more information, see [Getting started with {{site.data.keyword.cloud_notm}} {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}](/docs/power-iaas?topic=power-iaas-getting-started).

All instances of an SAP system must run in the same service. Multiple systems can be distributed through different services, in which case IT operations teams must ensure that latency is acceptable for their scenarios.
{: note}


### IBM Power CPU type - dedicated or shared
{: #iaas-power-vs-dedicated-shared}

For **shared** processors, you choose the number of CPUs that the new server is entitled to use. This number should correspond to the number of CPUs that were the result of your sizing. The entitled CPUs should be sufficient for normal production operation and to cover workload during peak time. Don't count on additional CPUs that you might get out of the shared processor pool for uncapped processors.

For **dedicated** processors, the number of dedicated CPUs should correspond to the number of CPUs that were the result of your sizing.

For more information about shared and dedicated processors, see [Assigning the appropriate processor entitled capacity](https://www.ibm.com/support/pages/assigning-appropriate-processor-entitled-capacity) and [Power Virtual Servers processor types](/docs/power-iaas?topic=power-iaas-power-iaas-faqs#processor).

Depending on the SAP workload, supported processor options are restricted. For more information, see [SAP Note 2855850](https://launchpad.support.sap.com/#/notes/2855850){: external}.
{: note}


### SAP HANA and IBM Power Virtual Server
{: #iaas-power-vs-hana}

See [SAP Note 2947579 - SAP HANA on IBM Power Virtual Servers](https://launchpad.support.sap.com/#/notes/2947579){: external} for SAP HANA support on {{site.data.keyword.IBM_notm}} Power Virtual Servers.

SAP HANA workloads that use {{site.data.keyword.IBM_notm}} Power Virtual Servers run on IBM Power System E980, with Block Storage powered by [{{site.data.keyword.IBM_notm}} FlashSystem family](https://www.ibm.com/it-infrastructure/storage){: external} connected through the Fibre Channel protocol. For more information about these systems and how they're used inside the {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} service, see the data sheet below:

**Data sheet:**
- [IBM Power System E980 (9080-M9S)](https://www.ibm.com/downloads/cas/VX0AM0EP){: external}

For further information, see [hardware specifications for {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s](/docs/power-iaas?topic=power-iaas-about-virtual-server#hardware-specifications).


### SAP NetWeaver and IBM Power Virtual Server
{: #iaas-power-vs-netweaver}

See [SAP Note 2855850 - SAP Applications on IBM Power Virtual Servers](https://launchpad.support.sap.com/#/notes/2855850){: external} for SAP NetWeaver support on {{site.data.keyword.IBM_notm}} Power Virtual Servers.

SAP NetWeaver and SAP AnyDB workloads that use {{site.data.keyword.IBM_notm}} Power Virtual Servers are run on IBM Power System S922 and IBM Power System E980, with Block Storage powered by [{{site.data.keyword.IBM_notm}} FlashSystem family](https://www.ibm.com/it-infrastructure/storage){: external} connected through the Fibre Channel protocol. For more information about these systems and how they're used inside the {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} service, see the following data sheets:

**Data sheets:**
* [IBM Power System S922 (9009-22A)](https://www.ibm.com/downloads/cas/KQ4BOJ3N){: external}
* [IBM Power System E980 (9080-M9S)](https://www.ibm.com/downloads/cas/VX0AM0EP){: external}

For further information, see [hardware specifications for {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s](/docs/power-iaas?topic=power-iaas-about-virtual-server#hardware-specifications).


## VMware Software-Defined Data Center
{: #iaas-vmware}

{{site.data.keyword.cloud_notm}} and VMware partner to bring the capabilities of VMware for SAP into Cloud by using VMware vSphere installed to {{site.data.keyword.baremetal_long}}.

This enables secure single-tenant compute with full root control to the hypervisor, providing optimized performance with high agility, resiliency, and elastic compute costs. Since Dec-2007, when VMware first announced support for SAP, there has been continuous improvement to VMware-SAP capabilities, which provide flexible delivery of SAP project implementations and easier maintenance of SAP Systems; all these capabilities are available with VMware and {{site.data.keyword.cloud_notm}}.

In short, {{site.data.keyword.cloud_notm}} provides two levels of VMware, which are both SAP-certified:
- **Intel Bare Metal and VMware vSphere (ESXi OS)**, requires manual VMware setup and configuration
- **{{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated**, fully automated VMware SDDC setup and configuration
    - _includes optional BYOL and access to advanced VMware capabilities, such as VMware HCX for seamless bidirectional network extension and connection to existing VMware clusters on-premises_

For more information about {{site.data.keyword.cloud_notm}} for VMware on Classic Infrastructure, see [{{site.data.keyword.cloud_notm}} for VMware](https://www.ibm.com/cloud/vmware){: external}. Additional information is available on:
- [{{site.data.keyword.cloud_notm}} Intel Bare Metal and VMware vSphere (ESXi OS)](/docs/vmware?topic=vmware-about-vmware){: external} for the manual VMware setup and configuration
- [{{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated](/docs/vmwaresolutions?topic=vmwaresolutions-inst_comp_chart){: external} for fully automated VMware SDDC setup and configuration



## Compliance
{: #iaas-compliance}

IBM treats your data with the same safeguards as our own, the security is built to IBM’s rigorous standards and certified for compliance.

{{site.data.keyword.cloud_notm}} is designed for organizations who are building a cloud environment, which is security-rich, open, hybrid Cloud (i.e., on-premises data center workloads) and multi-cloud. Deployments to {{site.data.keyword.cloud_notm}} include many secure and regulated workloads, by using our extensive {{site.data.keyword.cloud_notm}} compliance programs with clear delineation of roles and responsibilities.

[{{site.data.keyword.cloud_notm}} compliance programs](https://www.ibm.com/cloud/compliance) provide compliance and trust certifications, which reaffirm IBM's commitment to protection of customer data and applications. These compliance programs are for regulations, standards, and frameworks across Global, Government, Industry, and Regional.

More supplementary information to the {{site.data.keyword.cloud_notm}} compliance programs is available on [{{site.data.keyword.cloud_notm}} service offering descriptions and terms](https://www-03.ibm.com/software/sla/sladb.nsf/sla/bm?OpenDocument) which contain links to individual **"Data Processing and Protection data sheets"** for {{site.data.keyword.cloud_notm}} offerings.

Each {{site.data.keyword.ibm_cloud_sap}} offering uses different infrastructure configurations and approaches to providing the service and will therefore be certified independent of each other. These certifications can be checked on the previous links or clarified by contacting {{site.data.keyword.cloud_notm}} or your IBM representative. Following is a small extract from the full list of recognized compliance, certifications, attestations, or reports available across the various {{site.data.keyword.ibm_cloud_sap}} offerings:

- ISO 27001
- ISO 27017
- ISO 27018
- EU-US Privacy Shield Policy
- GDPR Ready
- Germany Federal Office for Information Security (BSI) C5
- HIPAA for Healthcare USA
- ITAR Compliant
- PCI-DSS for Payment Card Industry USA
- Singapore Multi-Tier Cloud Security Standard (MTCS)
- SOC1 Type 2
- SOC2 Type 2
- SOC3
- ...more
