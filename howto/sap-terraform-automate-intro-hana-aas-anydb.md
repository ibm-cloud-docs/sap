---

copyright:
  years: 2023
lastupdated: "2023-12-06"

subcollection: sap

---

{{site.data.keyword.attribute-definition-list}}

# Introduction to {{site.data.keyword.cloud_notm}} VPC and Additional Application Server (AAS) to HANA and AnyDB
{: #intro-automate-aas-hana-anydb-terraform-ansible}

You can use Terraform to automate {{site.data.keyword.cloud}} VPC provisioning. The VPC provisioned includes virtual server instances with high network performance. The VPC infrastructure contains a number of Infrastructure-as-a-Service (IaaS) offerings, including Virtual Servers. After the VPC is provisioned, the scripts use the Ansible Playbook to install the SAP system. IBM Cloud VPC infrastructure consists of SAP certified hardware that uses Intel&reg; Xeon CPUs and additional Intel&reg; technologies.

## {{site.data.keyword.cloud_notm}} VPC introduction
{: #intro-aas-hana-anydb-cloud-vpc}

A VPC is a public cloud offering that an enterprise uses to establish its own private cloud-like computing environment on shared [public cloud](https://www.ibm.com/cloud){: external} infrastructure. VPCs give an enterprise the ability to define and control a virtual network that is logically isolated from all other public cloud tenants, creating a private, secure place on the public cloud.

Imagine that a cloud provider’s infrastructure is a residential apartment building and multiple families live inside. Being a public cloud tenant is akin to sharing an apartment with a few roommates. In contrast, having a VPC is like having your own private condominium; no one else has the key, and no one can enter the space without your permission.

A VPC’s logical isolation is implemented by using virtual network functions and security features that give an enterprise customer granular control over which IP addresses or applications can access particular resources. It is analogous to the “friends-only” or “public/private” controls on social media accounts used to restrict who can or can’t see your otherwise public posts.

With {{site.data.keyword.cloud_notm}} VPC, you can use the UI, CLI, and API to manually provision virtual server instances for VPC with high network performance. VPC infrastructure contains a number of Infrastructure-as-a-Service (IaaS) offerings, including Virtual Servers for VPC. Use the following information to understand a simple use case for planning, creating, and configuring resources for your VPC, and learn about more VPC overviews and VPC tutorials. For more information about VPC, see [Getting started with Virtual Private Cloud (VPC)](/docs/vpc?topic=vpc-getting-started).

## SAP on {{site.data.keyword.cloud_notm}}
{: #intro-aas-hana-anydb-cloud-sap}

SAP NetWeaver is the core foundation of the SAP technology stack and is the platform that is used for ABAP and Java applications. The SAP system can be installed and configured in {{site.data.keyword.cloud_notm}} for various system and database types.



## SAP Project Value Guide – Additional Application Server (AAS) to HANA
{: #intro-aas-hana-anydb-cloud-value-guide}

SAP projects vary widely in scope and budget, but none are considered trivial. Whether you are delivering a new SAP system or implementing changes to an existing one, the requirements for error-free execution and reducing the project time to realized benefits are always present.

In many SAP project scenarios, the deployment of an SAP system is often a key and repeated task. This project value guide covers the automated deployment of {{site.data.keyword.cloud_notm}} VPC and Additional Application Server (AAS) to HANA. We will also discuss on [SAP NetWeaver of HANA database](/docs/sap?topic=sap-intro-automate-deploy-vpc-terraform-ansible&interface=ui) and [Backing up the HANA database IBM Cloud Object Storage](/docs/sap?topic=sap-sap-automate-intro-hana-db-backup-cos&interface=ui) in their respective sections.

After your HANA DB and SAP NetWeaver application server are up and running, you may want additional SAP application servers deployed to increase workload capacity. With this automation, deploying infrastructure, performing SAP software installation, and joining the existing SAP system in less than twenty minutes is possible. For more information, go to [VPC with Additional Application Server ABAP on Linux for SAP HANA](https://cloud.ibm.com/catalog/architecture/deploy-arch-ibm-sap-vpc-automation-hana-nw-abap-aas-51f5ccbc-23fe-42d0-a17c-7a90b73da835-global){: external}.

 ![Figure 1. Additional Application Server (AAS) to SAP instance and HANA instance](../images/sap_value_guide_aas.svg "Additional Application Server (AAS) to SAP instance and HANA instance"){: caption="Additional Application Server (AAS) to SAP instance and HANA instance" caption-side="bottom"}

## SAP Additional Application Server Instance
{: #intro-aas-hana-anydb-sap-refarch}

You can install one or more additional application server instances for an existing SAP system. Additional application server instances are optional and can be installed on separate hosts.

An additional application server instance can run on:

*   The host of any instance of the existing SAP system
*   On a dedicated host - distributed on additional IBM VPC (VSI instance)

![Figure 1. Standard installation](../images/refarch-sap-hana-single-host-all-q2-23.svg "SAP NetWeaver 7.x with SAP HANA database single-host installation with AAS"){: caption="SAP NetWeaver 7.x with SAP HANA database single-host installation with AAS" caption-side="bottom"}

For example, SAP NetWeaver (ABAP or Java stack) has a dedicated reference architecture that is running on SAP HANA or AnyDB databases that are published on IBM cloud [SAP NetWeaver on UNIX architecture diagram](/docs/sap?topic=sap-sap-refarch-nw-sybase#sap-netweaver-arch-diag-syb).

Manually deploying a VPC and installing an SAP system can be time-consuming. The Terraform automation assures not only a much quicker implementation, but also a standardized and less prone to error deployment. Terraform and Ansible are used for automating the deployment processes.

The Terraform scripts solution provides the automated deployment of a single host with SAP NetWeaver with either ASE SYB or SAP HANA db on the Red Hat Enterprise Linux® 8.4 and SUSE 15.x for SAP Applications.

The SAP installation media that are used for this deployment are the default media for SAP AAS of NetWeaver 7.x (ABAP stack) on SUSE/RHEL with ASE SYB 16 or HANA 2.0 db as distributed instance available at the SAP Support Portal under INSTALLATION AND UPGRADE area. You provide the installation media as an input parameter for Terraform for your SAP AAS solution chosen on dedicated kits storage from bastion server.

## Ansible for SAP installation
{: #intro-aas-hana-anydb-sap-ansible}

Ansible is an automation tool for the deployment of several IT tasks. Ansible is used for automating the installation of an SAP NetWeaver with HANA or AnyDB. For more information about Ansible, see the [Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html){: external}.

The deployment is done by using the Ansible core, which provides CLI tools for automation. More information about Ansible core can be found on the [Ansible core page](https://docs.ansible.com/ansible-core/devel/index.html){: external}.

The Ansible playbook is called directly by the Terraform script. The script starts with Terraform specific steps for creating the VPC, and continues automatically with the Ansible specific steps to install the SAP system.

## Where to run the scripts
{: #intro-aas-hana-anydb-scripts-deployment}

The recommended way to run the scripts is from your Deployment Server because the Deployment Server has Terraform and Ansible already installed. If you want to run the scripts from your local workstation, you need to install Terraform and Ansible locally.

For both the Deployment Server and local workstation, you must download the SAP Kits to the temporary storage assigned to you on the Deployment Server. Ansible installs the kits for you. You specify the location of the Kits in the configuration files.

## Prerequisites
{: #intro-aas-hana-anydbb-create-bastion}

Before you deploy any of the SAP automated solutions on {{site.data.keyword.cloud_notm}} VPC, you create a Bastion server VPC in your chosen region. The Bastion server is used for downloading and storing specific SAP solution media that are needed for later automation deployment. The Bastion server is used for both CLI deployment scenarios, as well for Schematics UI deployments. For more information about how to create the Bastion server and its corresponding VPC, see [Automate SAP Bastion server – SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server).

After bastion VPC deployment is complete, you must download the SAP Kits for the SAP AAS type and DB you want to deploy to the temporary storage assigned to you on the Bastion Server.

You need to have already deployed an SAP NetWeaver (ABAP) 7.X -standard or distributed installation on SUSE 15.3 /RHEL 8.4 either ASE SYB or HANA 2.0 db before you deploy SAP AAS of NetWeaver 7.x (ABAP stack) on SUSE/RHEL with ASE SYB 16 or HANA 2.0 as distributed instance. SAP AAS has to be connected to an already running SAP NW 7.X with the same type and version running on a compatible DB version.
{: note}

Ansible installs the kits for you. You specify the location of the Kits in the configuration files.

To save costs the Bastion server, with its SAP media dedicated storage, can be decommissioned after the SAP solutions are successfully implemented on {{site.data.keyword.cloud_notm}} VPC cloud. Or, you can keep the Bastion server and use it as a jump host for that specific region.

This automation is offered at no cost; however, the provisioned infrastructure comes at cost.
{: note}
