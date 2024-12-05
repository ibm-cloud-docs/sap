---

copyright:
  years: 2020, 2024
lastupdated: "2024-12-05"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads

subcollection: sap

---

{{site.data.keyword.attribute-definition-list}}

# Planning your deployment
{: #power-vs-planning-items}

SAP for Power Virtual Servers is a complementary offering from {{site.data.keyword.powerSysFull}} with low latency access to {{site.data.keyword.cloud}} services.
{: note}

Make sure that you're familiar with the fundamental components and options that are provided by {{site.data.keyword.powerSysShort}} infrastructure offerings for SAP. Before you deploy servers, make sure that you read the {{site.data.keyword.Bluemix}} for SAP information in [Getting started](/docs/sap?topic=sap-get-started).
{: shortdesc}

## Before you begin
{: #power-vs-before-you-begin}

To help make sure that your first deployment is a success, read [Fast path of IBM Power Virtual Servers](/docs/sap?topic=sap-fast-path-site-map-power-vs) including the following sections:
* [IBM Power Systems Infrastructure environment introduction](/docs/sap?topic=sap-power-env-introduction) for the basic understanding about the environment.
* [Connectivity options within the IBM Power Virtual Server network, connection through IBM Cloud](/docs/sap?topic=sap-determine-access#determine-access-connectivity-options-power)
* [Sample storage configurations on IBM Power Infrastructure](/docs/sap?topic=sap-storage-design-considerations#sample-power)

## Prerequisites
{: #power-vs-prereqs}

Review the following prerequisites.

1. You need sufficient {{site.data.keyword.cloud_notm}} IAM permissions to create all required elements.

   * Create services from {{site.data.keyword.cloud_notm}} catalog.
   * Create and modify {{site.data.keyword.vpc_short}} services, virtual server instances, networks, network prefixes, storage volumes, ssh keys, and security groups of this VPC.
   * Create and modify {{site.data.keyword.powerSysShort}} services, virtual server instances, networks, storage volumes, ssh keys of this {{site.data.keyword.powerSysShort}}.
   * Create and modify {{site.data.keyword.cloud_notm}} direct links and IBM transit gateways.
   * Access existing {site{.data.keyword.cos_short}} services.
2. Make sure that you have an SSH key that you can use for authentication. This key is used to log in to all virtual server instances that you create. For more information about creating SSH keys, see [SSH keys](/docs/vpc?topic=vpc-ssh-keys).
3. Upload SAP software installation packages and SAP maintenance planer XML files to an existing {site{.data.keyword.cos_short}} bucket. To configure access to these software packages from a running virtual server instance, you need following information:
   * {site{.data.keyword.cos_short}} endpoint
   * {{site.data.keyword.cos_short}} bucket name
   * {{site.data.keyword.cos_short}} HMAC access key
   * {{site.data.keyword.cos_short}} HMAC secret access key

   For more information about configuring API access in IBM Cloud Object Storage [Getting started with IBM Cloud Object Storage](/docs/cloud-object-storage?topic=cloud-object-storage-getting-started-cloud-object-storage) and [Service credentials](/docs/cloud-object-storage?topic=cloud-object-storage-service-credentials).

4. Choose a Linux operating system for your deployments. We recommend that you use the same OS release for all the hosts in the environment. When you use the same operating system version and release, it simplifies the operations management of the whole landscape. In this tutorial, Linux is used on all management components that run in your landscape. SAP solutions can run on Linux or on AIX.
5. Read [OS for IBM Power Virtual Servers](/docs/sap?topic=sap-compute-os-design-considerations#os-power) to learn about supported operating systems for SAP workloads on {{site.data.keyword.powerSysShort}}.
6. Design the network layout for your landscape. Specify the IP address ranges in the subnets to prevent IP address conflicts between all subnets in the landscape, including external subnets (subnets in your on-premises environment), and any other subnets.

   All IP address ranges in the subnets must be unique.
   {: note}

    The following private networks are created. You need to specify IP address ranges for them.

    | Service | Private network | IP address ranges |
    | --- | --- | --- |
    | Management VPC | Address prefix for VPC management network \n VPC management network for virtual server instances  | 172.10.0.0/16 \n 172.10.1.0/24 |
    | Workload VPC | Address prefix for VPC workload network \n VPC management network for virtual server instances | 172.11.0.0/16  \n 172.11.2.0/24 |
    | Edge VPC | Address prefix for VPC edge network \n VPC edge network for virtual server instances | 172.12.0.0/16  \n 172.11.1.0/24 |
    | Power VS workspace | Power VS management network  \n PowerVS backup network  \n  \n Separate network for each SAP system on Power VS and each SAP system or SAP component group on VPC \n SAP S4/HANA PRD \n SAP S4/HANA QA1 \n SAP Web Dispatchers on edge VPC | 10.10.10.0/24 \n 10.10.11.0/24 \n  \n  \n 10.10.20.0/24  \n 10.10.21.0/24 |
    {: caption="Private networks IP address ranges" caption-side="bottom"}

7. Define hostnames for all the virtual server instances and services that you deploy in the landscape. Map the hostnames to the IP addresses. Also, it's possible to deploy the virtual server instances and assign the IP address dynamically. Make sure that you clear DNS/IP picture before you start. You can use a DNS service of your choice. The only requirement is that this service must be reachable from the Virtual Private Cloud. In the example, IBM Cloud DNS service is used with the following hostname / IP address mappings.

   | Deployed virtual server instance and services | Hostname| IP address |
   | --- | --- | --- |
   | Access host | `access.example.com` | `172.10.1.4` |
   | Basic management services | `basic.service.example.com` | `172.11.1.4` |
   | Critical management services | `critical.service.example.com` | `172.11.1.5` |
   | Power VS shared file system | `power.service.mgmt.example.com` | `10.10.10.4` |
   | Example virtual server instance host for SAP HANA DB | `db.mgmt.prd.example.com` | `10.10.10.5` |
   | Example SAP HANA DB host | `db.prd.example.com` | `10.10.20.5` |
   | Example virtual server instance host for SAP NetWeaver | `app1.mgmt.prd.example.com` | `10.10.10.6` |
   | Example SAP NetWeaver application server host | `app1.prd.example.com` | `10.10.20.6` |
   {: caption="Example host name and IP address mappings" caption-side="bottom"}

8. Each Unix OS user has a UID and GUID. These IDs are used to specify file system ownership and become a part of file system metadata. In the management scenarios, where file system is backed up on one host and restored on another, both hosts must use the same UID and GUID for the same users. For the best experience, make sure that you use central user management for SAP users. In the example deployment, LDAP client setup was used on the virtual server instances that are running on {{site.data.keyword.powerSysShort}}.
9. You need an overview about the ports that must be opened for communication between SAP workloads that run on IBM Power Systems Virtual Servers (such as SAP NetWeaver or SAP HANA), in VPC (such as SAP Web Dispatcher, SAP Solution Manager) and outside of the environment (such as clients in public internet or in on-premises environment).
10. Determine the size parameters for each SAP workload that you plan to deploy. The most important parameters are memory size (especially for SAP HANA) and the number of SAPs. Most of the other configurations can be derived from these two key metrics.

For more information, see [Sizing process for SAP Systems](/docs/sap?topic=sap-sizing) and see [SAP Sizing](https://service.sap.com/quicksizer).

For deployment of each Power VS instance, you must be aware of following sizing parameters:

* Memory size
* Number of CPUs
* Sharing type of CPUs (on {{site.data.keyword.powerSysShort}}, for certain SAP workloads such as SAP NetWeaver)
* Certified SAP t-shirt size as combination of memory and number of CPUs (for certain SAP workloads such as SAP HANA)
* Needed file systems, each with file system size
* Swap space size (for certain SAP workloads, such as SAP NetWeaver)
