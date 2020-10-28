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

# Compute and OS design considerations
{: #compute-os-design-considerations}

The SAP systems in a landscape have specific requirements for servers, operating systems, network setup, and supported storage.
{: shortdesc}

In some regards, SAP workloads using a Cloud Service Provider (such as {{site.data.keyword.ibm_cloud_sap}}) Infrastructure-as-a-Service is very similar to existing practices (over many decades) for running SAP workloads using an external data center / datacenter provider. An SAP landscape will have specific requirements for connectivity, between hosts within Cloud IaaS and to external systems, {{site.data.keyword.ibm_cloud_sap}} provides a rich set of functions beyond hosting of SAP systems to improve your SAP landscape.

To assist your project's planning phase, the below sections provide {{site.data.keyword.ibm_cloud_sap}} portfolio design considerations for **Compute and OS**.


## Compute Performance considerations
{: #compute}

The {{site.data.keyword.ibm_cloud_sap}} portfolio is ideal for practically all SAP use case scenarios. You can use your servers for mission critical workloads, as your test environment, or your business continuity disaster recovery (BCDR) site.

### SAP NetWeaver work processes scheduling and scaling
{: #compute-workprocesses}

CPU thread consumption effects from:
* Dialog Work Process
* Update Work Process
* Background Work Process
* Enqueue Work Process
* Spool Work Process


### Compute Profiles of SAP-certified Bare Metal on Classic Infrastructure
{: #compute-baremetal}

You are offered an array of RAM and CPU combinations as the SAP-certified servers, which have a pre-configured amount of RAM and number of CPUs.

These combinations are "Appliance" delivery model of hardware certification. Therefore, the combination that you select cannot be changed during the ordering process or through a support ticket after servers are deployed.

Some {{site.data.keyword.ibm_cloud_sap}} Bare Metal profiles do allow alterations, and therefore would use the "TDI" delivery model for which you would need to run validation checks to gain SAP Support.


### Compute Profiles of SAP-certified Virtual Servers on VPC Infrastructure
{: #compute-vs-vpc}

{{site.data.keyword.ibm_cloud_sap}} provides SAP-certified infrastructure by using the latest Generation 2 Virtual Servers. These virtual servers are available with instantaneous provisioning, and are offered in different profiles that define CPU and RAM combinations.


### Compute Profiles of SAP-certified IBM Power Virtual Servers
{: #compute-power}

This is a complementary offering from IBM Power Systems, with low latency access to {{site.data.keyword.cloud_notm}} services
{: note}

You have two compute options for SAP workloads: Power System E980 and Power System S922. Both can run SAP NetWeaver application servers on AIX or Linux&reg;, or SAP database servers with IBM Db2 or Oracle on AIX. SAP HANA runs E980 on Linux.

Currently, Power System E980 is supported for SAP HANA.

Currently, SAP Workloads on {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} are available in Washington, D.C., Dallas, London, and Frankfurt. The number of supported locations is planned to be extended.

All SAP NetWeaver Application Server ABAP-based products and SAP NetWeaver Application Server Java-based products are supported on {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}. For SAP NetWeaver-based SAP products, see [SAP Note 2855850 - SAP Applications on IBM Power Systems Virtual Servers](https://launchpad.support.sap.com/#/notes/2855850){: external}.

All SAP HANA-based products are supported on {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s. For support requirements, see [SAP Note 2923984 - SAP on IBM Power Systems Virtual Servers: Support prerequisites](https://launchpad.support.sap.com/#/notes/2923984){: external}.

For all other software components or third-party products, contact [SAP Support](https://support.sap.com/en/index.html){: external}.

### Compute Profiles of SAP-certified VMware on Classic Infrastructure
{: #compute-vmware}

VMware runs on the same SAP-certified Bare Metals.  Therefore, the VMware vSphere (ESXi) installation on the certified hardware enables the VMware-SAP certification and agreements to be valid. Therefore, all VMware-SAP certification guidance must be followed (as described in SAP Notes for VMware-SAP).
{: note}

VMware SDDC is available as a customer-controlled root-access hypervisor, which are certified to run SAP workloads.  Providing VMware SDDC does not automatically provide a pre-sized virtual machine for SAP HANA or SAP NetWeaver upon provisioning either the OS Image with VMware vSphere (ESXi) for the Bare Metal or the fully automated setup from {{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated. YOu choose how to size and configure your SAP Workloads (for SAP HANA, this is under the SAP HANA TDI delivery model).

When you run SAP workloads on VMware, you have significant flexibility and the full capabilities which VMware has built to run SAP workloads over decades is available to use.

Using VMware for SAP workloads on {{site.data.keyword.cloud_notm}} is certified, by using the "TDI" delivery model for which you would need to run validation checks to gain SAP Support.

However, this is a Type 2 hypervisor and therefore does have a small overhead of CPU/RAM for running ESXi on the Bare Metal, to what is then available for virtual machines to use. On average this overhead is 10%, and is expected by VMware-SAP in virtualized environments. Therefore, customers are encouraged to size correctly and test performance before you go live [SAP Note 2393917 - SAP HANA on VMware vSphere 6.5 and 6.7 in production](https://launchpad.support.sap.com/#/notes/0002393917).

Also, both VMware and SAP agree to the physical to virtual overhead of <10% on average, and provide:
- The estimation of <10% average overhead with equation `"physical SAPS - 10%"` for virtualized SAPS to use when you size virtual machines
- The estimation of `"between 0.5% and 3%"` subtracted from total available physical RAM. Although, _"the actual RAM overhead can be defined only after the VMs are configured"_

Sources:
- [Page 120-121, Architecture Guidelines and Best Practices for Deployments of SAP HANA on VMware vSphere Architecture and Technical Considerations Guide](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/whitepaper/sap_hana_on_vmware_vsphere_best_practices_guide-white-paper.pdf){: external}
- [Page 19, The Winding Road to Virtual SAP HANA Application Workload Guidance Design for SAP S/4HANA on VMware vSphere 6.5](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/solutions/vmw-vsphere-virtual-saphana-application-workload-guidance-design.pdf){: external}
- Only for half-socket VMs / sharing of NUMA Node between two VMs, there is an `"additional performance impact"` and a `"sizing buffer of at least 15%"` of the CPU (SAPS) is recommended.
[SAP Community Wiki - SAP HANA on VMware vSphere](https://wiki.scn.sap.com/wiki/display/VIRTUALIZATION/SAP+HANA+on+VMware+vSphere){: external}


Several other SAP-defined rules must be followed to deploy SAP HANA in a VMware SDDC environment. For more information, see the following documentation:
* [SAP Note 2161991 - VMware vSphere configuration guidelines](https://launchpad.support.sap.com/#/notes/2161991){: external}
* [SAP Note 2393917 - SAP HANA on VMware vSphere 6.5 and 6.7 in production](https://launchpad.support.sap.com/#/notes/2393917){: external}
* [SAP Note 2779240 - Workload-based sizing for virtualized environments](https://launchpad.support.sap.com/#/notes/2779240){: external}
* [SAP HANA Tailored Data Center Integration Frequently Asked Questions)](https://www.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html){: external}


## Operating Systems considerations
{: #os}

The {{site.data.keyword.ibm_cloud_sap}} portfolio provides various Operating Systems for the Enterprise IT organization to select from.


### Operating Systems supported
{: #os-supported}

You need to consult [SAP Note 2414097](https://launchpad.support.sap.com/#/notes/2414097){: external} for a list of guest operating systems (OS) to deploy SAP HANA and SAP NetWeaver-based systems. An SAP S-user ID is required to access the SAP Note.

For the Operating System, the SAP HANA certified servers are available with the following operating systems:
* Red Hat Enterprise Linux for SAP HANA
* SUSE Linux Enterprise Server for SAP HANA
* VMware vSphere hypervisor (ESXi) + created Guest OS with RHEL/SLES

For the Operating System, the SAP NetWeaver certified servers are available with the following operating systems:
* IBM AIX
* Red Hat Enterprise Linux for SAP Applications
* SUSE Linux Enterprise Server for SAP Applications
* VMware vSphere hypervisor (ESXi) + created Guest OS with RHEL/SLES/WinS
* Windows Server

For SAP HANA release versions (including SPS and Revision/Patch numbers), support is only available for pre-defined and specific major.minor releases of an Operating System (for example, RHEL 7.6). This information is shown in [SAP 2235581 - SAP HANA: Supported Operating Systems](https://launchpad.support.sap.com/#/notes/2235581){: external}. An easier view of this available in the SAP Note attachment `SAP_HANA_OS_Release_Support_Matrix.pdf`.

For SAP NetWeaver release versions, support is available for each major release of an Operating System (for example, SLES 12) meaning each subsequent release is available for use (i.e. SLES 12 SPS4, SLES 12 SP5 etc.). This information is shown in the  [SAP Product Availability Matrix (PAM)](https://support.sap.com/en/release-upgrade-maintenance.html#section_1969201630){: external}.


### OS configuration for SAP
{: #os-configuration}

Each infrastructure has various Operating Systems, and there are various Images for those Operating Systems available on-demand.

Each of these on-demand OS Images (for example, RHEL 7.6 for SAP HANA) is provided as shipped (aka. the "general availability" / "stock image" release) by each of the vendors (for example, Red Hat). These OS Images are provided with access from the OS Package Manager (for example, RHEL4SAP this would be `yum`) to the OS package update channels specific to the OS Packages for SAP. The OS package update channels permit updates to the OS according to the latest SAP Notes for the relevant Operating System with the specified OS kernel versions, OS package versions, and OS package for SAP versions that are required.



Therefore for the OS Images it is still required to perform:
- OS configuration according to SAP guidance
- OS package updates according to SAP guidance, this includes updates to specified OS kernel versions (e.g. RHEL4SAP 7.6 ships with `3.10.0-957.el7.x86_64` however SAP requires the 1.3 version `3.10.0-957.1.3.el7.x86_64`)

### OS for IBM Power Virtual Servers
{: #os-power}

For the Linux&reg; versions to deploy for SAP HANA, see [SAP Note 2947579 - SAP HANA on IBM Power Systems Virtual Servers](https://launchpad.support.sap.com/#/notes/2947579){: external}. An SAP S-user ID is required to access the SAP Note. Licensing for Linux is covered by client's "bring your own license" model.

For the version of IBM AIX or Linux on Power to deploy for SAP NetWeaver-based systems, see [SAP Note 2855850 - SAP Applications on IBM Power Systems Virtual Servers](https://launchpad.support.sap.com/#/notes/2855850){: external}. An SAP S-user ID is required to access the SAP Note. License fees for AIX are covered by your monthly billing rate.

{{site.data.keyword.cloud_notm}} integrates your SUSE Linux&reg; Enterprise Server (SLES) capabilities into the {{site.data.keyword.cloud_notm}} experience to meet your specific business needs. Licensing for Linux is covered by the "bring your own license (BYOL)" model.

OS Packages update server with IBM Power Power Virtual Servers:
* For SAP NetWeaver you can use publicly available AIX SUMA or SUSE update repositories, or use your own AIX NIM or SUSE RMT servers.
* For SAP HANA you can use publicly available SUSE update repositories, or use your own private SUSE RMT servers.

### OS for Virtual Servers on VPC Infrastructure
{: #os-vs-vpc}

For a list of operating systems and databases available for SAP NetWeaver-based system deployments, see [SAP Note 2927211](https://launchpad.support.sap.com/#/notes/2927211){: external}.

An SAP S-user ID is required to access the SAP Note.
{: note}

### OS when you use VMware SDDC
{: #os-vmware}

VMware SDDC is available as a customer-controlled root-access hypervisor, which is available as an OS Image for the Bare Metal or available with fully automated setup from {{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated. The VMware licensing can be included or BYOL.

However, when you run a VMware SDDC, the Virtual Machine's Guest OS licensing and subscriptions (to the relevant package update channels, including OS Packages for SAP) is covered by you.

Only the Operating Systems listed below are supported as guest Operating Systems for VMware SDDC and SAP workloads:
* Red Hat Enterprise Linux (RHEL) for SAP
* SUSE Linux Enterprise Server for SAP
* Microsoft Windows Server

You need to make sure that the SAP HANA Tailored Data Center Integration (TDI) Key Performance Indicators (KPIs) are met for every virtual machine on which SAP HANA is deployed.
{: note}

See [SAP Note 2414097)](https://launchpad.support.sap.com/#/notes/2414097){: external} for version details.

Refer to [Installing VMware vSphere ESXi via Remote Console and Virtual Media](/docs/vmware?topic=vmware-installing-vsphere-esxi#installing-vsphere-esxi) and other VMware.com documentation to install a Guest OS.

## OS Bring-your-own-License and install image
{: #os-byol}

When you have your own operating system license, this can be used with {{site.data.keyword.cloud_notm}} and the OS install based on the vendor's instructions:

- OS BYOL and Custom Image with {{site.data.keyword.baremetal_short}} on Classic Infrastructure using the ["no OS" option during provisioning](/docs/bare-metal?topic=bare-metal-bm-no-os#bm-no-os)
- OS BYOL and Custom Image with Virtual Servers on VPC Infrastructure using [Importing and managing custom images](/docs/vpc?topic=vpc-managing-images)
- Linux OS BYOL and Custom Image with {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} on IBM Power Infrastructure using [Importing a boot image for IBM AIX or IBM i](/docs/power-iaas?topic=power-iaas-importing-boot-image#console-import-image) and [Linux within the Power Systems Virtual Server - Capturing and importing a SLES image](/docs/power-iaas?topic=power-iaas-using-linux#preparing-linux-image)
- OS BYOL and Images for VMware SDDC, use the standard Virtual Machine Guest OS guidance from VMware documentation
