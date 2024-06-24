---

copyright:
  years: 2022, 2023
lastupdated: "2023-07-06"

subcollection: sap

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip} 
{:ui: .ph data-hd-interface="ui"}
{:terraform: .ph data-hd-interface="terraform"}

# Introduction to SAP NetWeaver and ASE SYB DB 2-tier and 3-tier on {{site.data.keyword.cloud_notm}} VPC 
{: #intro-automate-nw-asesyb-terraform-ansible}

You can use Terraform to automate {{site.data.keyword.cloud}} VPC provisioning. The VPC provisioned includes virtual server instances with high network performance. The VPC infrastructure contains a number of Infrastructure-as-a-Service (IaaS) offerings, including Virtual Servers. After the VPC is provisioned, the scripts use the Ansible Playbook to install the SAP system. IBM Cloud VPC infrastructure consists of SAP certified hardware that uses Intel&reg; Xeon CPUs and additional Intel&reg; technologies.

## {{site.data.keyword.cloud_notm}} VPC introduction
{: #intro-nw-asesyb-cloud-vpc}

A VPC is a public cloud offering that an enterprise uses to establish its own private cloud-like computing environment on shared [public cloud](https://www.ibm.com/cloud/public) infrastructure. VPCs give an enterprise the ability to define and control a virtual network that is logically isolated from all other public cloud tenants, creating a private, secure place on the public cloud.

Imagine that a cloud provider’s infrastructure is a residential apartment building and multiple families live inside. Being a public cloud tenant is akin to sharing an apartment with a few roommates. In contrast, having a VPC is like having your own private condominium; no one else has the key, and no one can enter the space without your permission.

A VPC’s logical isolation is implemented by using virtual network functions and security features that give an enterprise customer granular control over which IP addresses or applications can access particular resources. It is analogous to the “friends-only” or “public/private” controls on social media accounts used to restrict who can or can’t see your otherwise public posts.

With {{site.data.keyword.cloud_notm}} VPC, you can use the UI, CLI, and API to manually provision virtual server instances for VPC with high network performance. VPC infrastructure contains a number of Infrastructure-as-a-Service (IaaS) offerings, including Virtual Servers for VPC. Use the following information to understand a simple use case for planning, creating, and configuring resources for your VPC, and learn about more VPC overviews and VPC tutorials. For more information about VPC, see [Getting started with Virtual Private Cloud (VPC)](/docs/vpc?topic=vpc-getting-started).

## SAP on {{site.data.keyword.cloud_notm}}
{: #intro-nw-asesyb-cloud-sap}

SAP NetWeaver is the core foundation of the SAP technology stacks and is the platform that is used for ABAP and Java applications. The SAP system can be installed and configured in {{site.data.keyword.cloud_notm}} for various system and database types.

For more information about SAP system architectures on {{site.data.keyword.cloud_notm}} VPC, see the infrastructure reference architectures for SAP for each supported database type. For example, [SAP NetWeaver 7.x on UNIX with ASE SYB on {{site.data.keyword.cloud_notm}} VPC](/docs/sap?topic=sap-sap-refarch-nw-sybase) is the dedicated reference architecture for this SAP solution.

The SAP installation media that are used for this deployment are the default media for SAP NetWeaver 7.5 with ASE SYB v16. The media are available at the SAP Support Portal under INSTALLATION AND UPGRADE area. You provide the installation media as an input parameter for Terraform.

This automated solution can be deployed in 2 scenarios: standard deployment and distributed deployment.

### Standard deployment
{: #intro-stand-depl}

In a standard system, all main instances run on a single virtual server instance (VPC) within a private subnet. You can install a central system on a single host.

Instances available in standard deployment:

* ABAP central services instance (ASCS instance) Contains the ABAP message server and the ABAP enqueue server.
* SAP recommends installing the ASCS instance because ASCS is used to cluster the message server and enqueue server separately from the central instance. However, you can also install your SAP system without the ASCS instance. In this case, follow the instructions in Installing a Central or Distributed System Without the ASCS Instance.
* Optionally, you can install the ASCS instance with an embedded SAP Web Dispatcher. For more information, see ASCS Instance with Embedded SAP Web Dispatcher.
* Database instance (DB instance)
* Central instance

Additionally, you can install one or more dialog instances. For more information, see SAP NW with SYB reference architecture section [Architectural design on IBM Cloud VPC on Unix](/docs/sap?topic=sap-sap-refarch-nw-sybase).

Manually deploying a VPC and installing an SAP system can be time-consuming. The Terraform automation assures not only a much quicker implementation, but also a standardized and less error-prone deployment. Terraform and Ansible are used for automating the deployment processes.

### Distributed deployment 
{: #intro-dist-depl}

In a distributed system, every instance can run on a separate host (VSI).
Instances installed in a distributed hosts architecture include:

* Database instance (DB instance)
* Central instance

Additionally, you can install one or more dialog instances. For more information, see SAP NW with SYB reference architecture section [Architectural design on IBM Cloud VPC on Unix](/docs/sap?topic=sap-sap-refarch-nw-sybase).

The Terraform scripts solution provides the automated deployment of a:
* Distributed host for SAP NetWeaver App instance and a 
* Second host with an ASE SYB database instance on the Red Hat Enterprise Linux 8 and SUSE 15 for SAP Applications.

The SAP installation media that are used for this deployment are the default media for SAP NetWeaver 7.5 with ASE SYB ASE SYB v16. The media are available at the SAP Support Portal under INSTALLATION AND UPGRADE area. You provide the installation media as an input parameter for Terraform.

## Ansible for SAP installation
{: #intro-nw-asesyb-sap-ansible}

Ansible is an automation tool for the deployment of several IT tasks. Ansible is used for automating the installation of an SAP NetWeaver with ASE SYB. For more information about Ansible, see the [Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html).

The deployment is done by using the Ansible core, which provides CLI tools for automation. More information about Ansible core can be found on the [Ansible core page](https://docs.ansible.com/ansible-core/devel/index.html).

The Ansible playbook is called directly by the Terraform script. The script starts with Terraform specific steps for creating the VPC, and continues automatically with the Ansible specific steps to install the SAP system.

## Where to run the scripts
{: #intro-nw-asesyb-scripts-deployment}

The recommended way to run the scripts is from your Deployment Server because the Deployment Server has Terraform and Ansible already installed. If you want to run the scripts from your local workstation, you need to install Terraform and Ansible locally.

For both the Deployment Server and local workstation, you must download the SAP Kits to the temporary storage assigned to you on the Deployment Server. Ansible installs the kits for you. You specify the location of the Kits in the configuration files.

## Prerequisite, create a Bastion server to run the scripts
{: #intro-nw-asesyb-create-bastion}

Before you deploy any of the SAP automated solutions on {{site.data.keyword.cloud_notm}} VPC, you create a Bastion server VPC in your chosen region. The Bastion server is used for downloading and storing specific SAP solution media that are needed for later automation deployment. The Bastion server is used for both CLI deployment scenarios, as well for Schematics UI deployments. For more information about how to create the Bastion server and its corresponding VPC, see [Automate SAP Bastion server – SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server).

After Bastion VPC deployment is complete, you must download the SAP Kits to the temporary storage assigned to you on the Bastion Server. Ansible installs the kits for you. You specify the location of the Kits in the configuration files.

To save costs the Bastion server, with its SAP media dedicated storage, can be decommissioned after the SAP solutions are successfully implemented on {{site.data.keyword.cloud_notm}} VPC cloud. Or, you can keep the Bastion server and use it as a jump host for that specific region.

This automation is offered at no cost; however, the provisioned infrastructure comes at cost.
{: note}
