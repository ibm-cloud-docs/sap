---

copyright:
  years: 2020, 2022
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
{:important: .important}

# {{site.data.keyword.cloud}} Virtual Private Cloud (VPC) Infrastructure environment introduction
{: #vpc-env-introduction}

An Infrastructure-as-a-Service (IaaS) environment consists of many components - primarily compute, storage, and network from a specified region (such as the US) and a designated site location (also referred to as a zone), which is a data center site.

## Deployment and management
{: #vpc-env-deployment-and-management}

{{site.data.keyword.cloud_notm}} VPC Infrastructure offerings, such as virtual or bare metal servers, are deployed through the [{{site.data.keyword.cloud_notm}} VPC Infrastructure console](https://cloud.ibm.com/vpc-ext/overview).

Alternatively, deployments can be made and managed by using:
- {{site.data.keyword.cloud_notm}} CLI
- {{site.data.keyword.cloud_notm}} VPC Infrastructure API calls that use an {{site.data.keyword.cloud_notm}} API key
- [Terraform Provider for {{site.data.keyword.cloud_notm}}](/docs/terraform?topic=terraform-getting-started) by using an {{site.data.keyword.cloud_notm}} API key

For more information, see [Managing VPC Infrastructure (IAM)](/docs/vpc?topic=vpc-iam-getting-started).


## Locations - availability zones
{: #vpc-env-locations-availability-zones}

With availability zones across North and South America, Europe, Asia, and Australia, you can provision cloud resources where (and when) you need them. Many regions are available globally, with multiple availability zones in each region. Each availability zone is connected to the {{site.data.keyword.cloud_notm}} global private network, making data transfers faster and more efficient anywhere in the world.

For more information about {{site.data.keyword.cloud_notm}} availability zones, data centers, and Points of Presence (PoPs), see the [global regions, availability zones, and data centers map](https://www.ibm.com/cloud/data-centers/#datacentermap){: external}.


## Compute Resources
{: #vpc-env-compute}

Two types of compute resource can be deployed in {{site.data.keyword.cloud_notm}} VPC Infrastructure environment:
- Intel Virtual Server Instances (VSIs)
- Intel Bare Metal Servers

These compute resources are offered in different profiles that define CPU and RAM combinations.

For more information, see [Infrastructure certified for SAP](/docs/sap?topic=sap-iaas-offerings).


## Networking
{: #vpc-env-networking}

The {{site.data.keyword.vpc_short}} infrastructure network, is robust, secure, and flexible; powered by the latest in networking hardware, with the best networking capabilities. It allows definable isolation and creation of a network within the cloud.

| {{site.data.keyword.cloud_notm}} VPC Infrastructure network |
| -- |
| Global |
| Resource Group |
| Region |
| VPC |
| Availability zone (with address prefix) |
| Subnet |
{: caption="Table 1. Networking component layers overview" caption-side="top"}

Every {{site.data.keyword.vpc_full}} is created for a region, and spans multiple availability zones.

When you deploy a VPC in an availability zone, an [address prefix](/docs/vpc?topic=vpc-vpc-addressing-plan-design) is used for that specified zone.

Each VPC Zone (and the address prefix) contains one or more subnets. You can define each subnet manually by choosing the IP range and the subnet mask, or you can choose the number of IP addresses needed. A newly created compute resource is deployed into this subnet and can also be attached to further subnets.


### Networking connectivity
{: #vpc-env-networking-connectivity}

[{{site.data.keyword.vpc_full}} network overview](/docs/vpc?topic=vpc-about-networking-for-vpc) demonstrates the connectivity for the environment. Issues with network connectivity can cause delays for your project if you do not plan properly, regardless of how you plan to use your system.

In general, {{site.data.keyword.cloud_notm}} VPC has a highly available, high-bandwidth network that is connected to every compute resource, be it bare metal servers or physical servers, which, in the VSI case, serve a hypervisor. Each physical server (host), which serves a hypervisor, divides the network into virtual network interfaces (vNICs) that are attached to the virtual server.

Depending on the profile of your virtual server, the total available network bandwidth to the virtual server is in the range of 4 Gbps to 64 Gbps. It's important to consider that each vNIC has a maximum throughput of 16 Gbps, so to achieve maximum throughput, up to 4 additional vNICs must be attached to the virtual server (that is, a virtual server might have a maximum of 5 vNICs attached).

If you need to connect to your virtual or bare metal server through the public internet (also known as inbound to a server), you can order a **_Floating IP_** and attach to the server's vNIC, in other words: you can attach one **_Floating IP_** per server.

If you want to connect to the public internet from your server (also known as outbound from a server), you need to attach a **_Public Gateway_** to the VPC. This gateway provides access to the internet for an entire subnet.

The following inter-connectivity options are available:
* VPC zone to zone,
* VPC to VPC,
* VPC to Classic Infrastructure,
* VPC to {{site.data.keyword.IBM_notm}} Power Systems Infrastructure,
* VPC to on-premises data centers by using a VPC VPN Gateway

When a connection to the public internet is not acceptable because of security measures, you can deploy an IPsec Gateway into your VPC to connect to your server. For more information, see [Connectivity to your SAP system landscape - VPC VPN Gateway](/docs/sap?topic=sap-determine-access#determine-access-vpc-vpn-gateway). Or, you can have an even closer integration into your backbone infrastructure by an {{site.data.keyword.cloud_notm}} Direct Link. For more information, see [Connectivity to your SAP system landscape - {{site.data.keyword.cloud_notm}} Direct Link](/docs/sap?topic=sap-determine-access#determine-access-vpc-direct-link).

Server resources that are in {{site.data.keyword.cloud_notm}} Classic Infrastructure can be connected through **_Transit Gateways_**. These virtual devices are used to connect your private VLAN subnets in the _Classic Infrastructure_ to your VPC subnets.

For more information, see [About Networking for VPC](/docs/vpc?topic=vpc-about-networking-for-vpc) and [Setting up access to classic infrastructure](/docs/vpc?topic=vpc-setting-up-access-to-classic-infrastructure).


Extra requirements exist in Classic Infrastructure networking to enable the _Transit Gateway_, be sure to review documentation before you change your Classic Infrastructure or VPC Infrastructure networking topology and configuration.
{: important}

It is advised that your networking department contact [{{site.data.keyword.cloud_notm}} Support](/docs/get-support?topic=get-support-using-avatar#getting-support) after determining the layout of your landscape and the connectivity that is required on the SAP application layer.
{: note}


### Networking protection
{: #vpc-env-networking-protection}

{{site.data.keyword.cloud}} offers further protection mechanisms that can provide your {{site.data.keyword.vsi_is_short}} with a layer of security that you can configure and adapt anytime. Two key principles are:
    * **Network Access Control Lists (ACL)**: Available for use by all subnets in all zones. ACLs attach to a subnet and provide subnet-level protection by limiting a subnet's inbound and outbound traffic.
    * **Security Groups**: Available for use by all subnets on all zones that are attached to a vNIC of any server that provides instance-level protection by acting as a firewall to restrict a vNIC inbound and outbound traffic.

For more information, see [Security in your VPC](/docs/vpc?topic=vpc-security-in-your-vpc).

#### Subnets to separate traffic
{: #vpc-env-networking-multiple-subnets}

If you want to separate different network traffic types in your landscape, either because of security restrictions or because of throughput considerations, you can configure and attach multiple subnets to your VPC and make them available to your compute resources, too.

#### Network Access Control List
{: #vpc-env-networking-acl}

Network Access Control Lists (ACLs) are used to manage `allow` and `deny` rules on a subnet level. ACLs are used to manage network traffic between subnets, too. The default ACL for a subnet opens the subnet for all traffic. If you wanted more strict security measures, you would need to add rules to the ACL. When you add rules, keep in mind that required services like DNS or OS patch and packages downloads might be affected by those rules. For more information, see [Security in your VPC](/docs/vpc?topic=vpc-security-in-your-vpc).

#### Security Groups
{: #vpc-env-networking-security-groups}

A Security Group is a set of _allow_-only firewall rules. You can apply these rules to one or more bare metal servers or VSIs. You can also create a default Security Group with Secure Shell (SSH) and ICMP (ping) during VPC creation, which allows ICMP and SSH from any IP address. These rules need to restrict the IPs or IP ranges from which you are planning to access the VPC.

## Storage
{: #vpc-env-storage}

Block storage is provided with your virtual servers and uses input/output operations per second (IOPS) to determine storage needs. It is ideal for storage-intensive applications with high I/O needs, such as an OS, and database and application software. This option is the perfect companion for SAP HANA workloads.

All Block storage is selected based on capacity (GB) and performance (IOPS) measurements and is required to meet a specific SAP Workload.

IOPS values are measured based on 16 KB block size with a 50-50 read/write mix. To achieve a maximum I/O throughput, it's advisable to look at the tier and custom profiles available for storage and find the optimal combination of size and IOPS.

Storage volumes differ in performance, depending on their IOPS tier. You can select among 3, 5, and 10 IOPS/GB (see [Tiered IOPS profiles](/docs/vpc?topic=vpc-block-storage-profiles#tiers)). You can also select a [custom size](/docs/vpc?topic=vpc-block-storage-profiles#custom) (in GB and IOPS) that is based on the size of the storage.

If you need more than the initially provisioned storage in your virtual server, you can attach extra volumes to a virtual server later. Contact [{{site.data.keyword.cloud_notm}} Support](/docs/get-support?topic=get-support-using-avatar#getting-support) for extension options if the attached storage is insufficient for your workload.

### Shared Storage
{: #vpc-env-shared-storage}

Block storage can be detached and attached to other virtual servers at any time, but, only to one virtual server at the same time.

No shared storage for virtual servers is available in VPC at time of writing.
