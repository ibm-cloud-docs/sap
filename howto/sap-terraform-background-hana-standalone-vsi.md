---

copyright:
  years: 2023, 2024
lastupdated: "2024-06-17"

subcollection: sap

---

{{site.data.keyword.attribute-definition-list}}

# Background for automating SAP HANA stand-alone virtual server instance on {{site.data.keyword.cloud}} VPC
{: #sap-hana-vpc-background}

## {{site.data.keyword.cloud}} Virtual Private Cloud (VPC) introduction
{: #sap-hana-standalone-vsi-vpc-intro}

{{site.data.keyword.cloud}} VPC offers the possibility to quickly provision virtual server instances for VPC with high network performance. VPC infrastructure contains a number of Infrastructure-as-a-Service (IaaS) offerings, including Virtual Servers for VPC. A VPC is a public cloud offering that an enterprise uses to establish its own private cloud-like computing environment on shared [public cloud](https://www.ibm.com/cloud){: external} infrastructure. A VPC gives an enterprise the ability to define and control a virtual network that is logically isolated from all other public cloud tenants, creating a private, secure place on the public cloud.
{: shortdesc}

Imagine that a cloud provider’s infrastructure is a residential apartment building with multiple families living inside. Being a public cloud tenant is akin to sharing an apartment with a few roommates. In contrast, having a VPC is like having your own private condominium. No one else has the key, and no one can enter the space without your permission.

A VPC’s logical isolation is implemented by using virtual network functions and security features that give an enterprise customer granular control over which IP addresses or applications can access particular resources. It is analogous to the “friends-only” or “public/private” controls on social media accounts used to restrict who can or can’t see your otherwise public posts.

With {{site.data.keyword.cloud_notm}} VPC, you can use the UI, CLI, and API to quickly provision virtual server instances for VPC with high network performance. VPC infrastructure contains a number of Infrastructure-as-a-Service (IaaS) offerings, including Virtual Servers for VPC. Use the following information to understand a simple use case for planning, creating, and configuring resources for your VPC, and learn about more VPC overviews and VPC tutorials. For more information, see [Getting started with Virtual Private Cloud (VPC)](/docs/vpc?topic=vpc-getting-started).

## SAP HANA in {{site.data.keyword.cloud_notm}}
{: #sap-hana-in-cloud}

The IBM public cloud is an open, security-rich, and enterprise-ready public cloud for business. This design makes it easier for global enterprises to modernize and build new business applications in the cloud to meet the evolving needs of the business and its customers. {{site.data.keyword.cloud_notm}} offerings include a broader portfolio of SAP-certified infrastructure, ranging from bare metal, VMware, VPC, and IBM Power® Systems Virtual Server products.

## SAP HANA defined
{: #sap-hana-defined}

SAP HANA (High-performance Analytic Appliance) is a multi-model database that stores data in its memory instead of keeping it on a disk. This arrangement results in data processing that magnitudes faster than processing of disk-based data systems, allowing for advanced, real-time [analytics](https://www.ibm.com/think/topics/business-analytics){: external}.

Serving as a platform for enterprise resource planning (ERP) software and other business applications, SAP HANA can be placed on premises, in the cloud, or both, in a [hybrid cloud](https://www.research.ibm.com/hybrid-cloud/?lnk=leadspace) system. For more information, see [SAP HANA on {{site.data.keyword.cloud_notm}}](https://www.ibm.com/think/topics/sap-hana){: external}.

The number of hosts in an SAP HANA system landscape determines the SAP HANA system type.

An SAP HANA system can be configured as either:

*	A single-host system - One SAP HANA instance on one host
*	A distributed system (multiple-host system) - Multiple SAP HANA instances distributed over multiple hosts

## Single-host SAP HANA system
{: #sap-hana-single-host}

A single-host system is the simplest system installation type that runs an SAP HANA system entirely on one host. You can scale the system up as needed. The single-host system has these components:

 ![Figure 1. SAP NetWeaver 7.x SAP HANA single-host installation on bare metal and x86 VSIs](../images/refarch-sap-hana-single-host-only.svg "SAP NetWeaver 7.x SAP HANA single-host installation on bare metal and x86 VSIs"){: caption="SAP NetWeaver 7.x SAP HANA single-host installation on bare metal and x86 VSIs" caption-side="bottom"}

For more information about SAP systems architectures in {{site.data.keyword.cloud_notm}} VPC, see [reference architectures](/docs/sap?topic=sap-sap-refarch-nw-hana) for each supported database type.

Manually deploying a VPC and installing an SAP system can be time-consuming. The automation assures not only a much quicker implementation, but also a standardized and less prone to error deployment. Terraform and Ansible are used for automating the deployment processes.

The solution that is presented in this how-to guide is the automated deployment of a single host with SAP HANA stand-alone VSI on Red Hat Enterprise Linux or SUSE Linux Enterprise Server (SLES)for SAP applications.

Database instance (DB) - To assist your projects planning phase, more design considerations are provided at SAP HANA database with {{site.data.keyword.cloud_notm}} for SAP. For more information, see [SAP HANA database design considerations](/docs/sap?topic=sap-anydb-hana-db) and [Infrastructure certified for SAP](/docs/sap?topic=sap-iaas-offerings).

A dedicated reference architecture about this SAP HANA on {{site.data.keyword.cloud_notm}} VPC cloud can be found on [SAP HANA database design considerations](/docs/sap?topic=sap-anydb-hana-db).

SAP HANA installation media that are used for this deployment is the default one for SAP HANA, platform edition 2.0 SPS05. The media is available at SAP Support Portal in the Installation and Upgrade area and it must be provided manually in the input parameter file.

## Terraform for infrastructure deployment
{: #sap-hana-terraform-for-infra-deploy}

Terraform on {{site.data.keyword.cloud_notm}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} solutions. For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Getting started with Terraform on {{site.data.keyword.cloud_notm}}](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

Terraform is used to provision of the infrastructure components in {{site.data.keyword.cloud_notm}}. For the automating this process, the current solution uses a Terraform script for deploying a VPC and a VSI with SAP certified storage and network configuration. The VPC contains one subnet and one security group that has three rules:

* Allow all outbound traffic from the VSI
* Allow inbound DNS traffic (UDP port 53)
* Allow inbound SSH traffic (TCP port 22)

After the successful deployment of the infrastructure, the Terraform script calls the Ansible Playbook, which automatically installs the SAP application.
Access creating single-tier VPC for SAP by using Terraform to get the detailed steps about using Terraform only for the creation of a VPC for SAP.

This automation is offered at no cost; however, the provisioned infrastructure comes at cost.
{: note}

## Ansible for SAP installation
{: #sap-hana-ansible-for-install}

Ansible is an automation tool for the deployment of several IT tasks. This solution performs the automated deployment of SAP HANA 2.0 DB on several validated OS platforms:
* SUSE Linux Enterprise Server 15 SP 4 for SAP
* SUSE Linux Enterprise Server 15 SP 3 for SAP
* Red Hat Enterprise Linux 8.6 for SAP
* Red Hat Enterprise Linux 8.4 for SAP in an existing {{site.data.keyword.cloud_notm}} Gen2 VPC on stand-alone SAP HANA box VSI.

For more information about Ansible, see the documentation available on the Ansible page.

The deployment is done by the Ansible core, which provides CLI tools for automation. More information about Ansible core can be found on [the Ansible core page](https://docs.ansible.com/ansible-core/devel/index.html){: external}.

The Ansible playbook is called directly by the Terraform script. The Terraform script is run in one run. During the run, the first steps are Terraform specific for creating the VPC, and it continues automatically with the second, Ansible, steps for the installation of the SAP system.
