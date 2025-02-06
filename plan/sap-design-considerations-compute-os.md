---

copyright:
  years: 2020, 2025
lastupdated: "2025-02-06"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads

subcollection: sap

---

{{site.data.keyword.attribute-definition-list}}

# Compute and OS design considerations
{: #compute-os-design-considerations}

The SAP systems in a landscape have specific requirements for servers, operating systems, network setup, and supported storage.
{: shortdesc}

In some regards, SAP workloads that use a Cloud Service Provider (such as {{site.data.keyword.ibm_cloud_sap}}) Infrastructure-as-a-Service is similar to existing practices (over many decades) for running SAP workloads by using an external data center or data center provider. An SAP landscape has specific requirements for connectivity, between hosts within Cloud IaaS and to external systems, {{site.data.keyword.ibm_cloud_sap}} provides a rich set of functions beyond hosting of SAP systems to improve your SAP landscape.

To assist your project's planning phase, the following sections provide {{site.data.keyword.ibm_cloud_sap}} portfolio design considerations for **Compute and OS**.


## Compute Performance considerations
{: #compute}

The {{site.data.keyword.ibm_cloud_sap}} portfolio is ideal for practically all SAP use case scenarios. You can use your servers for mission-critical workloads, as your test environment, or your business continuity disaster recovery (BCDR) site.

### SAP NetWeaver work processes scheduling and scaling
{: #compute-workprocesses}


CPU thread consumption affects the following processes.

* Dialog Work Process
* Update Work Process
* Background Work Process
* Enqueue Work Process
* Spool Work Process


### Compute Profiles of SAP-certified Bare Metal on Classic and on VPC Infrastructure
{: #compute-baremetal}

You are offered an array of RAM and CPU combinations as the SAP-certified servers, which have a pre-configured amount of RAM and number of CPUs.

These combinations are "Appliance" delivery model of hardware certification. Therefore, the combination that you select cannot be changed during the ordering process or through a support ticket after servers are deployed.

Some {{site.data.keyword.ibm_cloud_sap}} Bare Metal profiles do allow alterations, and therefore - as far as HANA deployments are concerned - would use the "TDI" delivery model for which you would need to run validation checks to gain SAP Support.


### Compute Profiles of SAP-certified Virtual Servers on VPC Infrastructure
{: #compute-vs-vpc}

{{site.data.keyword.ibm_cloud_sap}} provides SAP-certified infrastructure by using the latest Virtual Servers. These virtual servers are available with instantaneous provisioning, and are offered in different profiles that define CPU and RAM combinations.


### SAP HANA certified instances on IBM {{site.data.keyword.powerSys_notm}}
{: #compute-power}

The [Infrastructure certified for SAP](/docs/sap?topic=sap-iaas-offerings) compute options provide different variations of capabilities and sizing for SAP workloads.
SAP HANA database servers and SAP NetWeaver applications run on {{site.data.keyword.IBM_notm}} Power Systems using [IBM {{site.data.keyword.powerSys_notm}} certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-power-vs).
For more information, see [SAP HANA and IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-iaas-offerings#iaas-power-vs-hana), [SAP NetWeaver and IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-iaas-offerings#iaas-power-vs-netweaver), and [SAP NetWeaver certified instances on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-nw-iaas-offerings-profiles-power-vs).

The {{site.data.keyword.cloud_notm}} SAP-certified Infrastructure-as-a-Service (IaaS) offerings are available on {{site.data.keyword.cloud_notm}} in many {{site.data.keyword.IBM_notm}} data centers around the world.
See the current data centers in [IBM Cloud regions](/docs/power-iaas?topic=power-iaas-ibm-cloud-reg).

All SAP NetWeaver Application Server ABAP-based products and SAP NetWeaver Application Server Java-based products are supported on {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s. For SAP NetWeaver-based SAP products, see [SAP Note 2855850 - SAP Applications on IBM {{site.data.keyword.powerSys_notm}}s](https://me.sap.com/notes/2855850){: external}.

All SAP HANA-based products are supported on {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s. For support requirements, see [SAP Note 2923984 - SAP on IBM {{site.data.keyword.powerSys_notm}}s: Support prerequisites](https://me.sap.com/notes/2923984){: external}.

For all other software components or third-party products, contact [SAP Support](https://support.sap.com/en/index.html){: external}.

### Compute Profiles of SAP-certified VMware on Classic Infrastructure
{: #compute-vmware}

VMware runs on the same SAP-certified Bare Metals. Therefore, the VMware vSphere (ESXi) installation on the certified hardware enables the VMware-SAP certification and agreements to be valid. Therefore, all VMware-SAP certification guidance must be followed (as described in SAP Notes for VMware-SAP).
{: note}

VMware SDDC is available as a customer-controlled root-access hypervisor, which is certified to run SAP workloads. Providing VMware SDDC does not automatically provide a pre-sized virtual machine for SAP HANA or SAP NetWeaver upon provisioning either the OS image with VMware vSphere (ESXi) for the Bare Metal or the fully automated setup from {{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated. You choose how to size and configure your SAP Workloads (for SAP HANA, size and configuration is under the SAP HANA TDI delivery model).

When you run SAP workloads on VMware, you have significant flexibility and the full capabilities which VMware built to run SAP workloads over decades is available to use.

Using VMware for SAP workloads on {{site.data.keyword.cloud_notm}} is certified, by using the "TDI" delivery model for which you would need to run validation checks to gain SAP Support.

However, VMware SDDC is a Type 2 hypervisor and therefore does have a small overhead of CPU/RAM that is used for running ESXi on the Bare Metal server. This CPU/RAM overhead is then available for virtual machines to use. On average this overhead is 10%, and is expected by VMware-SAP in virtualized environments. Therefore, customers are encouraged to size correctly and test performance before you go live [SAP Note 2393917 - SAP HANA on VMware vSphere 6.5 and 6.7 in production](https://me.sap.com/notes/0002393917){: external}.

Also, both VMware and SAP agree to the physical to virtual overhead of <10% on average, and provide:
- The estimation of <10% average overhead with equation `"physical SAPS - 10%"` for virtualized SAPS to use when you size virtual machines
- The estimation of `"between 0.5% and 3%"` subtracted from total available physical RAM. Although, _"the actual RAM overhead can be defined only after the VMs are configured"_

Sources:
- [Page 120-121, Architecture Guidelines and Best Practices for Deployments of SAP HANA on VMware vSphere Architecture and Technical Considerations Guide](https://www.vmware.com/docs/sap_hana_on_vmware_vsphere_best_practices_guide-white-paper){: external}
- [Page 19, The Winding Road to Virtual SAP HANA Application Workload Guidance Design for SAP S/4HANA on VMware vSphere 6.5](https://www.vmware.com/docs/vmw-vsphere-virtual-saphana-application-workload-guidance-design){: external}
- Only for half-socket VMs and sharing of NUMA Node between two VMs. Keep in mind that `"additional performance impact"` and a `"sizing buffer of at least 15%"` of the CPU (SAPS) is recommended.
[SAP Community Wiki - SAP HANA on VMware vSphere](https://help.sap.com/docs/SUPPORT_CONTENT/virtualization/3362185751.html){: external}


Several other SAP-defined rules must be followed to deploy SAP HANA in a VMware SDDC environment. For more information, see the following documentation:
* [SAP Note 2161991 - VMware vSphere configuration guidelines](https://me.sap.com/notes/2161991){: external}
* [SAP Note 2393917 - SAP HANA on VMware vSphere 6.5 and 6.7 in production](https://me.sap.com/notes/2393917){: external}
* [SAP Note 2779240 - Workload-based sizing for virtualized environments](https://me.sap.com/notes/2779240){: external}
* [SAP HANA Tailored Data Center Integration Frequently Asked Questions)](https://www.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html){: external}


## Operating Systems considerations
{: #os}

The {{site.data.keyword.ibm_cloud_sap}} portfolio provides various Operating Systems for the Enterprise IT organization to select from.


### Operating Systems supported
{: #os-supported}

You need to consult [SAP Note 2414097](https://me.sap.com/notes/2414097){: external} for a list of guest operating systems (OS) to deploy SAP HANA and SAP NetWeaver-based systems. An SAP S-user ID is required to access the SAP Note.

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

For SAP HANA release versions (including SPS and Revision and Patch numbers), support is only available for pre-defined and specific major.minor releases of an Operating System (for example, RHEL 7.6). This information is shown in [SAP 2235581 - SAP HANA: Supported Operating Systems](https://me.sap.com/notes/2235581){: external}. An example is available in the SAP Note attachment `SAP_HANA_OS_Release_Support_Matrix.pdf`.

For SAP NetWeaver release versions, support is available for each major release of an Operating System (for example, SLES 12) meaning each subsequent release is available for use (example: SLES 12 SPS4, SLES 12 SP5, and so on). This information is shown in the [SAP Product Availability Matrix (PAM)](https://support.sap.com/en/release-upgrade-maintenance.html#section_1969201630){: external}.


### OS configuration for SAP
{: #os-configuration}

Each infrastructure has various operating systems with various images for those operating systems available on-demand.

Each of these on-demand OS images (for example, RHEL 7.6 for SAP HANA) is provided as shipped (also known as the "general availability" / "stock image" release) by each of the vendors (for example, Red Hat). These OS images are provided with access from the OS Package Manager (for example, RHEL4SAP, which is `yum`) to the OS package update channels specific to the OS Packages for SAP. The OS package update channels permit updates to the OS according to the latest SAP Notes for the relevant Operating System with the specified OS kernel versions, OS package versions, and OS package for SAP versions that are required.

Therefore, for OS images, you need to perform the following actions.
- OS configuration according to SAP guidance.
- OS package updates according to SAP guidance, which includes updates to specified OS kernel versions (for example, RHEL4SAP 7.6 ships with `3.10.0-957.el7.x86_64`. However, SAP requires the 1.3 version `3.10.0-957.1.3.el7.x86_64`).

### OS for Virtual Servers on VPC Infrastructure
{: #os-vs-vpc}

For a list of operating systems and databases available for SAP NetWeaver-based system deployments, see [SAP Note 2927211](https://me.sap.com/notes/2927211){: external}.

An SAP S-user ID is required to access the SAP Note.
{: note}

### OS for IBM {{site.data.keyword.powerSys_notm}}s
{: #os-power}

For the Linux&reg; versions to deploy for SAP HANA, see [SAP Note 2947579 - SAP HANA on IBM {{site.data.keyword.powerSys_notm}}s](https://me.sap.com/notes/2947579){: external}. An SAP S-user ID is required to access the SAP Note. The OS image is provided by {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s, and the licensing for Linux is covered through a "bring your own license" (BYOL) model.

For the version of IBM AIX or Linux on Power to deploy for SAP NetWeaver-based systems, see [SAP Note 2855850 - SAP Applications on IBM {{site.data.keyword.powerSys_notm}}s](https://me.sap.com/notes/2855850){: external}. An SAP S-user ID is required to access the SAP Note. License fees for AIX are covered by your monthly billing rate.

OS Packages update server with IBM Power {{site.data.keyword.powerSys_notm}}s:
* For SAP NetWeaver you can use publicly available AIX SUMA or SUSE update repositories, or use your own AIX NIM or SUSE RMT servers.
* For SAP HANA you can use publicly available SUSE update repositories, or use your own private SUSE RMT servers.

| Operating system for SAP HANA | Operating system image (Bring your own license) | Operating system image (IBM subscription) |
|---------|---------|---------|
| Red Hat Enterprise Linux (RHEL) 8.4 | RHEL8-SP4-SAP-BYOL| RHEL8-SP4-SAP |
| Red Hat Enterprise Linux (RHEL) 8.6 | RHEL8-SP6-SAP-BYOL| RHEL8-SP6-SAP |
| Red Hat Enterprise Linux (RHEL) 8.8 | RHEL8-SP8-SAP-BYOL| RHEL8-SP8-SAP |
| Red Hat Enterprise Linux (RHEL) 8.10 | RHEL8-SP10-SAP-BYOL| RHEL8-SP10-SAP |
| Red Hat Enterprise Linux (RHEL) 9.2 | RHEL9-SP2-SAP-BYOL| RHEL9-SP2-SAP |
| Red Hat Enterprise Linux (RHEL) 9.4 | RHEL9-SP4-SAP-BYOL| RHEL9-SP4-SAP |
| SUSE Linux Enterprise Server (SLES) for SAP 15 SP2 | SLES15-SP2-SAP-BYOL | SLES15-SP2-SAP |
| SUSE Linux Enterprise Server (SLES) for SAP 15 SP3 | SLES15-SP3-SAP-BYOL | SLES15-SP3-SAP |
| SUSE Linux Enterprise Server (SLES) for SAP 15 SP4 | SLES15-SP4-SAP-BYOL | SLES15-SP4-SAP |
| SUSE Linux Enterprise Server (SLES) for SAP 15 SP5 | SLES15-SP5-SAP-BYOL  | SLES15-SP5-SAP |
{: caption="Operating systems for IBM {{site.data.keyword.powerSys_notm}}s on SAP HANA" caption-side="bottom"}

| Operating system for SAP NetWeaver | Operating system image (Bring your own license) | Operating system image (IBM subscription) |
|---------|---------|---------|
| Red Hat Enterprise Linux (RHEL) 8.4 | RHEL8-SP4-SAP-NETWEAVER-BYOL | RHEL8-SP4-SAP-NETWEAVER |
| Red Hat Enterprise Linux (RHEL) 8.6 | RHEL8-SP6-SAP-NETWEAVER-BYOL | RHEL8-SP6-SAP-NETWEAVER |
| Red Hat Enterprise Linux (RHEL) 8.8 | RHEL8-SP8-SAP-NETWEAVER-BYOL | RHEL8-SP8-SAP-NETWEAVER |
| Red Hat Enterprise Linux (RHEL) 8.10 | RHEL8-SP10-SAP-NETWEAVER-BYOL | RHEL8-SP10-SAP-NETWEAVER |
| Red Hat Enterprise Linux (RHEL) 9.2 | RHEL9-SP2-SAP-NETWEAVER-BYOL | RHEL9-SP2-SAP-NETWEAVER |
| Red Hat Enterprise Linux (RHEL) 9.4 | RHEL9-SP4-SAP-NETWEAVER-BYOL | RHEL9-SP4-SAP-NETWEAVER |
| SUSE Linux Enterprise Server (SLES) for SAP 15 SP2 | SLES15-SP2-SAP-NETWEAVER-BYOL | SLES15-SP2-SAP-NETWEAVER |
| SUSE Linux Enterprise Server (SLES) for SAP 15 SP3 | SLES15-SP3-SAP-NETWEAVER-BYOL | SLES15-SP3-SAP-NETWEAVER |
| SUSE Linux Enterprise Server (SLES) for SAP 15 SP4 | SLES15-SP4-SAP-NETWEAVER-BYOL | SLES15-SP4-SAP-NETWEAVER |
| SUSE Linux Enterprise Server (SLES) for SAP 15 SP5 | SLES15-SP5-SAP-NETWEAVER-BYOL | SLES15-SP5-SAP-NETWEAVER |
| AIX 7.2 | 7200-05-08 or later |
| AIX 7.3 | 7300-02-01 or later |
{: caption="Operating systems for IBM {{site.data.keyword.powerSys_notm}}s on SAP NetWeaver" caption-side="bottom"}

Update on new SAP HANA large t-shirt profiles feature
IBM supports SLES15 SP4 for SAP and RHEL8.6 for SAP OS images with all other features on all t-shirt profiles with fewer than 64 cores. These SLES15 SP4 for SAP and RHEL8.6 for SAP OS images while they are updating to support the larger t-shirt profiles. Until further notice, use the larger t-shirt profiles with the SLES15 SP3 for SAP OS image or the RHEL8.4 for SAP OS image.
{: note}

### OS when you use VMware SDDC
{: #os-vmware}

VMware SDDC is available as a customer-controlled root-access hypervisor, which is available as an OS image for the Bare Metal or available with fully automated setup from {{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated. The VMware licensing can be included or BYOL.

However, when you run a VMware SDDC, the Virtual Machine's Guest OS licensing and subscriptions (to the relevant package update channels, including OS Packages for SAP) is covered by you.

Only the following operating systems are supported as guest Operating Systems for VMware SDDC and SAP workloads.

* Red Hat Enterprise Linux (RHEL) for SAP
* SUSE Linux Enterprise Server for SAP
* Microsoft Windows Server

You need to make sure that the SAP HANA Tailored Data Center Integration (TDI) Key Performance Indicators (KPIs) are met for every virtual machine on which SAP HANA is deployed.
{: note}

See [SAP Note 2414097)](https://me.sap.com/notes/2414097){: external} for version details.

Refer to [Installing VMware vSphere ESXi by using Remote Console and Virtual Media](/docs/vmware?topic=vmware-installing-vsphere-esxi#installing-vsphere-esxi) and other VMware.com documentation to install a Guest OS.


## Bring-your-own-OS (custom OS image and BYOL License)
{: #os-byos}

When you have your own operating system image and license, it can be used with {{site.data.keyword.cloud_notm}} and the OS install based on the vendor's instructions.

| Infrastructure | BYOS | SAP workloads support |
| -- | -- | -- |
| Intel {{site.data.keyword.baremetal_short}} on Classic Infrastructure | OS BYOL and custom image (BYOS) by using the ["no OS" option during provisioning](/docs/bare-metal?topic=bare-metal-bm-no-os#bm-no-os) | SAP HANA by using TDI deployment  \n SAP NetWeaver AS |
| Intel Virtual Servers (Gen2) on VPC Infrastructure | OS BYOL and Custom image (BYOS) by using [Importing and managing custom images](/docs/vpc?topic=vpc-managing-custom-images&interface=ui) | SAP HANA by using TDI deployment  \n SAP NetWeaver AS |
| {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} in the IBM Power Infrastructure environment | Linux OS BYOL and Custom Image (BYOS) by using [Capturing and importing an RHEL image](/docs/power-iaas?topic=power-iaas-linux-with-powervs#import-rhel-image) and [Capturing and importing a SLES image](/docs/power-iaas?topic=power-iaas-using-linux#preparing-linux-image)  | Not supported for SAP HANA or SAP NetWeaver workloads, available for non-SAP workloads |
| {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} in the IBM Power Infrastructure environment | Unix OS BYOL and Custom Image (BYOS) by using [Importing a boot image for IBM AIX or IBM i](/docs/power-iaas?topic=power-iaas-importing-boot-image#console-import-image) | SAP NetWeaver AS |
| VMware SDDC on IBM Cloud |  OS BYOL and custom image (BYOS) by using standard Virtual Machine Guest OS guidance from VMware documentation | Supported according to SAP-VMware guidance |
{: caption="Bring your own customized OS image and BYOL License" caption-side="bottom"}
