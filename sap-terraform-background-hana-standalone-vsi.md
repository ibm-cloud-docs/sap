---

copyright:
  years: 2021
lastupdated: "2021-10-04"

subcollection: sap

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}

# Background for automating SAP HANA stand-alone VSI 
{: #sap-hana-vpc-background}

## IBM VPC introduction
{: #sap-hana-vpc-intro}

{{site.data.keyword.cloud}} Virtual Private Cloud (VPC) offers the possibility to quickly provision virtual server instances for VPC with high network performance. VPC infrastructure contains a number of Infrastructure-as-a-Service (IaaS) offerings, including Virtual Servers for VPC. A VPC is a public cloud offering that an enterprise uses to establish its own private cloud-like computing environment on shared [public cloud](https://www.ibm.com/cloud/public) infrastructure. A VPC gives an enterprise the ability to define and control a virtual network that is logically isolated from all other public cloud tenants, creating a private, secure place on the public cloud.
{: shortdesc}

Imagine that a cloud provider’s infrastructure is a residential apartment building with multiple families living inside. Being a public cloud tenant is akin to sharing an apartment with a few roommates. In contrast, having a VPC is like having your own private condominium. No one else has the key, and no one can enter the space without your permission.

A VPC’s logical isolation is implemented by using virtual network functions and security features that give an enterprise customer granular control over which IP addresses or applications can access particular resources. It is analogous to the “friends-only” or “public/private” controls on social media accounts used to restrict who can or can’t see your otherwise public posts.

With {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC), you can use the UI, CLI, and API to quickly provision virtual server instances for VPC with high network performance. VPC infrastructure contains a number of Infrastructure-as-a-Service (IaaS) offerings, including Virtual Servers for VPC. Use the following information to understand a simple use case for planning, creating, and configuring resources for your VPC, and learn about more VPC overviews and VPC tutorials. [Getting started with Virtual Private Cloud (VPC)](/docs/vpc/getting-started.html)

## SAP HANA in {{site.data.keyword.cloud_notm}}
{: #sap-hana-in-cloud}

The IBM public cloud is an open, security-rich, and enterprise-ready public cloud for business. This design makes it easier for global enterprises to modernize and build new business applications in the cloud to meet the evolving needs of the business and its customers. {{site.data.keyword.cloud_notm}} offerings include a broader portfolio of SAP-certified infrastructure, ranging from bare metal, VMware, virtual private cloud (VPC), and IBM Power® Systems Virtual Server products.

## SAP HANA defined 
{: #sap-hana-defined}

SAP HANA (High-performance ANalytic Appliance) is a multi-model database that stores data in its memory instead of keeping it on a disk. This arrangement results in data processing that is magnitudes faster than processing of disk-based data systems, allowing for advanced, real-time [analytics](https://www.ibm.com/analytics/business-analytics).

Serving as a platform for enterprise resource planning (ERP) software and other business applications, SAP HANA can be placed on premises, in the cloud, or both, in a [hybrid cloud](https://www.research.ibm.com/hybrid-cloud/?lnk=leadspace) system. For more information, see [SAP HANA on {{site.data.keyword.cloud_notm}}](https://www.ibm.com/topics/sap-hana).

The number of hosts in an SAP HANA system landscape determines the SAP HANA system type. 

An SAP HANA system can be configured as either:

*	A single-host system - One SAP HANA instance on one host. 
*	A distributed system (multiple-host system) - Multiple SAP HANA instances distributed over multiple hosts, 

## Single-host HANA system
{: #sap-hana-single-host}

A single-host system is the simplest system installation type that runs an SAP HANA system entirely on one host. You can scale the system up as needed. The single-host system has these components: 

 ![Figure 1. SAP NetWeaver 7.x HANA single-host installation with AAS](images/refarch-sap-hana-single-host-only.svg "SAP NetWeaver 7.x HANA standard installation with AAS"){: caption="Figure 2. SAP NetWeaver 7.x HANA single-host installation with AAS" caption-side="bottom"}

For more information about SAP systems architectures in {{site.data.keyword.cloud_notm}} VPC, see [reference architectures](/docs/sap?topic=sap-sap-refarch-nw-hana) for each supported database type.

Manually deploying a VPC and installing an SAP system can be time-consuming. The automation assures not only a much quicker implementation, but also a standardized and less prone to error deployment. Terraform and Ansible are used for automating the deployment processes.

The solution that is presented in this how-to guide is the automated deployment of a single host with SAP HANA stand-alone VSI on Red Hat Enterprise Linux 7.6 for SAP Applications.

Database instance (DB) - To assist your project's planning phase, more design considerations are provided at SAP AnyDB – HANA database with {{site.data.keyword.cloud_notm}} for SAP. For more information, see [AnyDB - SAP HANA](/docs/sap?topic=sap-anydb-hana-db) and [Infrastructure certified by SAP](/docs/sap?topic=sap-iaas-offerings).
A dedicated reference architecture about this AnyDB SAP HANA on IBM VPC cloud can be found on [AnyDB - HANA Database](/docs/sap?topic=sap-anydb-hana-db).

SAP HANA installation media that are used for this deployment is the default one for SAP HANA, platform edition 2.0 SPS05. The media is available at SAP Support Portal in the INSTALLATION AND UPGRADE area and it must be provided manually in the input parameter file.

## Terraform for infrastructure deployment
{: #sap-hana-terraform-for-infra-deploy}

Terraform on {{site.data.keyword.cloud_notm}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} solutions. For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Terraform on {{site.data.keyword.cloud_notm}} getting started tutorial](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

Terraform is used to provision of the infrastructure components in {{site.data.keyword.cloud_notm}}. For the automating this process, the current solution uses a Terraform script for deploying a VPC and a VSI with SAP certified storage and network configuration. The VPC contains one subnet and one security group that has three rules:

*	Allow all outbound traffic from the VSI
*	Allow inbound DNS traffic (UDP port 53)
*	Allow inbound SSH traffic (TCP port 22)

After the successful deployment of the infrastructure, the Terraform script calls the Ansible Playbook, which automatically installs the SAP application.
Access creating single-tier virtual private cloud for SAP by using Terraform to get the detailed steps about using Terraform only for the creation of a VPC for SAP.

## Ansible for SAP installation
{: #sap-hana-ansible-for-install}

Ansible is an automation tool for the deployment several IT tasks. This solution performs the automated deployment of SAP HANA 2.0 DB on Red Hat Enterprise Linux 7.6 for SAP HANA on stand-alone HANA box VSI. For more information about Ansible, check out the documentation available on the Ansible page.

The deployment is done by the Ansible core, which provides CLI tools for automation. More information about Ansible core can be found on [the Ansible core page](https://docs.ansible.com/ansible-core/devel/index.html).

The Ansible playbook is called directly by the Terraform script. The Terraform script is run in one run. During the run, the first steps are Terraform specific for creating the VPC, and it continues automatically with the second, Ansible, steps for the installation of the SAP system.


