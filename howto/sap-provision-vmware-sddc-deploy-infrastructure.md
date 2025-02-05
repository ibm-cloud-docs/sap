---

copyright:
  years: 2020, 2025
lastupdated: "2025-02-05"

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
{:important: .important}

# Deploying your infrastructure
{: #vmware-sddc-set-up-infrastructure}

This topic contains information on how to deploy and set up SAP HANA and SAP NetWeaver on VMware that runs on {{site.data.keyword.cloud_notm}}. You will find references to previous topics, including setting up your network, security, storage, and operating system (OS).
{: shortdesc}

There are variations of VMware available from {{site.data.keyword.cloud_notm}} that support SAP. You need to decide whether you want to build your own VMware SDDC or use an automated build and configuration according to VMware best practices. For more information on these two choices, see:
- [{{site.data.keyword.cloud_notm}} Intel Bare Metal and VMware vSphere (ESXi OS)](/docs/vmware?topic=vmware-about-vmware){: external} for the manual VMware setup and configuration
- [{{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated](/docs/vmwaresolutions){: external} for fully automated VMware SDDC setup and configuration, which includes optional BYOL, configuration of different VMware components (such as VMware NSX, VMware vRealize) and access to advanced VMware capabilities (such as VMware HCX)
    - **_vCenter Server (VCS)_** solution type, where the entire VMware SDDC setup is automated and creates a hosted vSphere stack as a service
    - **_VMware vSphere (VSS)_** solution type, where selectable VMware SDDC components setup is automated and creates a customizable VMware with maximum flexibility. This solution type is the intermediary between manual with Bare Metal and fully auotmated from IBM Cloud for VMware Solutions.

Due to these variations, provisioning of a server, adding network storage, and configuring network connectivity and interfaces differ for each. The process to order a VMware setup for manual configuration is similar to [Provisioning your Bare Metal server](/docs/sap?topic=sap-bm-set-up-infrastructure#bm-provision-server). Therefore, the sections to follow will not cover this option. Rather, the focus is on {{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated to using the vCenter Server (VCS) solution type.


## Before you begin
{: #vmware-sddc-before-you-set-up-infrastructure}

The {{site.data.keyword.cloud}} console requires a unique log-in ID, which is an {{site.data.keyword.IBM_notm}}id.

Because ordering {{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated creates an Overlay Network that is managed by VMware NSX, there are several prerequisites for a VMware SDDC.

Complete the following tasks before your initial provisioning. Additional information and detailed steps for these tasks can be found in [Setting up your environment for your first order of IBM Cloud for VMware](/docs/vmwaresolutions?topic=vmwaresolutions-completing_checklist)
.

1. [Create an IBM Cloud Classic Infrastructure API key](/docs/account?topic=account-classic_keys#create-classic-infrastructure-key). This task coordinates the infrastructure setup for the VMware SDDC (such as VLAN, Subnet, Bare Metal). If you request administrator access is requested, use the steps in [Locating an IAM account administrator](/docs/vmwaresolutions?topic=vmwaresolutions-iam_verify_permissions) and [verify/update your user permissions](/docs/vmwaresolutions?topic=vmwaresolutions-cloud-infra-acct-req).

1. Convert your {{site.data.keyword.cloud_notm}} account to use [Virtual routing and forwarding (VRF) on IBM Cloud](/docs/direct-link?topic=direct-link-overview-of-virtual-routing-and-forwarding-vrf-on-ibm-cloud), using the steps described in [Converting to virtual routing and forwarding](/docs/direct-link?topic=direct-link-what-happens-during-the-account-conversion-process).

    Enabling VRF permanently alters networking for your IBM Cloud account. Be sure that you have understood both the benefits and minor tradeoffs, which impact your account and resources. After you enable VRF, it cannot be disabled.
    {: important}

1. Enable the Cloud Service Endpoints (CSE) to provide a secure and unmetered connection to services on IBM Cloud from the IBM Cloud Classic Infrastructure private network. Follow the steps in [enabling service endpoints](/docs/account?topic=account-vrf-service-endpoint#service-endpoint).

    This is not the same as Virtual Private Endpoints (VPE), which is the upgraded equivalent functionality for IBM Cloud VPC Infrastructure environment; this has more benefits that include IAM access control, endpoint with IP in the Subnet Range of the VPC, and does not require Virtual routing and forwarding (VRF).
    {: note}



## Provisioning your VMware Software-Defined Datacenter
{: #vmware-sddc-provision}

The {{site.data.keyword.cloud}} console requires a unique log-in ID, which is an {{site.data.keyword.IBM_notm}}id. Use the following steps to order your {{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated. Additional information can be found under [Ordering IBM Cloud for VMware Dedicated, vCenter Server instance and vSphere clusters](/docs/vmwaresolutions?topic=vmwaresolutions-vc_orderinginstance-req).


1. Log in to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com){: external} with your unique credentials.
1. Open the independent [IBM Cloud for VMware Solutions console](https://cloud.ibm.com/vmware){: external} using either the shortcut in the Navigation menu or by using search to access using the IBM Cloud catalog.
1. The [IBM Cloud for VMware Solutions console](https://cloud.ibm.com/vmware){: external} provides significant additional information and a wide range of options. To start provisioning for SAP, select **IBM Cloud for VMware Solutions Dedicated** which opens the order page.
1. The default solution type is **vCenter Server**.
1. The default deployment topology is **Deployment in single-zone region**. This option applies to SAP certification. Do not select **Stretched cluster**.
1. The default instance configuration is set to **(New configuration)**. This option applies to SAP certification. Do not select on any of the other configurations.
1. Enter the instance name of the IBM Cloud for VMware Solutions Dedicated deployment (which shows in the IBM Cloud console)
1. Select a resource group within the IBM Cloud account
1. Select the VMware vSphere version (e.g. VMware vSphere 6.7u3). This option does impact which SAP-certified infrastructure is available.
1. Select **NSX-T** as the VMware NSX networking solution. This choice can be NSX-V, see note below about the differences in ordering when selecting NSX-V instead.
1. Select the instance type:
   * Click **Primary instance** to deploy a single instance in the environment.
   * Click **Secondary instance** if you have previously deployed a VMware SDDC within IBM Cloud, and are connecting to an existing (primary) instance that uses vCenter Platform Services Controller (PSC). This option requires the existing vCenter Server Administrator credentials._
1. Complete the license settings (supplied by IBM Cloud or BYOL) for the VMware software components. See more details on the detailed documentation referred in the previous section.
1. `For NSX-T only:` **Management cluster**
   - Specify a name for the separated cluster
   - Specify a data center
   - Specify the server (CPU and RAM), which does not need to use SAP certified host servers because this cluster hosts only the VMware components and tools (such as vCenter Server, vRealize).
   - Specify the storage type. See more details on the detailed documention in the previous section.
   - Specify the Network Interfaces and networks to be provisioned (such as Private network only)
   - Specify the VLANs. This option allows you to reuse any existing VLANs within the IBM Cloud Classic Infrastructure environment
1. `For NSX-T only:` **Workload cluster:**
   - Specify a name for the cluster
   - Specify a data center
   - Click **SAP-certified** in the **CPU Generation** selection
   - Click either **SAP NetWeaver** or **SAP HANA** in the SAP-certified Intel servers selection
   - Specify the server profile. These profiles match those shown in VMware SDDC certified [profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-vmware) and [profiles for SAP NetWeaver](/docs/sap?topic=sap-nw-iaas-offerings-profiles-vmware)
   - Specify the amount of RAM for the profile
   - Specify the storage type by using the appropriate IBM Cloud File Storage for NFS mount points. For SAP HANA, the ocal disk option is a permitted configuration with RAID10.
   - Specify the network interfaces and networks to be provisioned, either **Public and private network** or **Private network only**
   - Specify the VLANs. This option allows reuse of any existing VLANs within the IBM Cloud Classic Infrastructure environment.

   For NSX-T: If the management cluster and the workload clusters are in the same location, you cannot use existing VLANs. Instead, the workload clusters reuse the VLANs from the management cluster.
   {: note}

1. If you want to order an edge services cluster with Juniper vSRX included, select the **Edge services cluster** checkbox and configure the appropriate settings: the cluster name, the RAM selection, and the private NICs enablement.
1. Complete the [network interface settings](/docs/vmwaresolutions?topic=vmwaresolutions-vc_orderinginstance#vc_orderinginstance-network-interface-settings).
   - Enter the hostname prefix for the instance that you are provisioning, the subdomain label, and the root domain name. For a secondary instance, the domain name is automatically completed.
   - Specify the DNS configuration, that uses a single or high-availability Windows Active Directory
1. _Optional:_ There are multiple services available from IBM Cloud for VMware, these can be provided during provisioning or afterward. There is detailed documentation on all of these services in addition.
1. After all selections are made, review the **Summary** pane including the configuration and estimated price.
   * To save the settings as a new configuration template without placing an order, click **Save configuration**, enter a name for the configuration, and click **Continue**.
   * To save the updates to a saved configuration, click **Save configuration**, select **Modify current configuration**, and click **Continue**.
1. To place the order, ensure that the account to be charged is the correct account, review and accept the terms, and then click **Create**.
   - The deployment of the instance starts automatically and you receive confirmation that the order is being processed. You can check the deployment status, including any issues that might require your attention, by viewing the **Deployment history** section of the instance details.
   - When the instance is ready to use, the status of the instance is changed to **Ready to use** and you receive a notification by email.

If you change VMware components outside of the {{site.data.keyword.vmwaresolutions_short}} console, the changes are not synchronized with the console or can cause instability. Read the detailed IBM Cloud for VMware Solutions documentation for more details. Also, see [FAQ - Considerations about changing vCenter Server artifacts](/docs/vmwaresolutions?topic=vmwaresolutions-vcenter_chg_impact)
{: important}


### Provisioning with VMware NSX-V selection
{: #vmware-sddc-provision-nsxv}

Selecting VMware-NSX during provisioning can change the ordering process.
- It can impact which SAP-certified infrastructurea is available, causing different profiles to be used.
- Requires separate ordering of the Workload Cluster for SAP, as NSX-V shows only ordering for the Management Cluster and you need to repeat the ordering process to order the **secondary instance** cluster by using SAP-certified profiles for VMware.


### Provisioning with VMware vSAN
{: #vmware-sddc-provision-vsan}

VMware vSAN for SAP NetWeaver and SAP AnyDB is not permitted. The description in [SAP Note 2161991 - VMware vSphere configuration guidelines](https://me.sap.com/notes/2161991){: external} is for SAP-certified Infrastructure in traditional on-premises data centers.

VMware vSAN for SAP HANA is not permitted. The description in [SAP Note 2718982 - SAP HANA on VMware vSphere and vSAN](https://me.sap.com/notes/2718982){: external} is for SAP-certified Infrastructure that uses SAP HANA hyper-converged infrastructure (HCI) certified hardware configurations in traditional on-premises data centers.


## Adding Network Storage
{: #vmware-sddc-adding-network-storage}

{{site.data.keyword.filestorage_full_notm}} is ordered during your provisioning, and you can add more storage after.
{: shortdesc}



More information on NFS for VMware is shown on the following documents:
- [Attached storage performance - Attached storage infrastructure design of IBM Cloud for VMware Solutions Dedicated](/docs/vmwaresolutions?topic=vmwaresolutions-storage-infra-design#storage-infra-design-perf)

To expand and contract the storage capacity and performanc of individual mount points, see the following documents:
- [Expanding File Share Capacity - IBM Cloud Classic Infrastructure File Storage](/docs/FileStorage?topic=FileStorage-expandCapacity)
- [Adjusting IOPS - IBM Cloud Classic Infrastructure File Storage](/docs/FileStorage?topic=FileStorage-adjustingIOPS)


## Preparing your Operating System
{: #vmware-sddc-os}

Deploy VMware virtual machines by importing an OS Image file or OVA and creating a new virtual machine Template, similar to any other VMware SDDC. {{site.data.keyword.cloud_notm}} is providing root-control access to your VMware SDDC, putting you in full control and therefore does not provide deployments of VMware VM's with Guest OS.

The installation of the operating system is based on the vendor's instructions, and uses your own operating system license.

The operating system that you choose must be certified for SAP and have access to the necessary operating system packages for SAP.
