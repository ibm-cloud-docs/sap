---

copyright:
  years: 2022, 2023
lastupdated: "2023-07-05"

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

# Overview SAP workload deployment on {{site.data.keyword.cloud}} Virtual Private Cloud (VPC) (Terraform and Ansible)
{: #components-terraform-3tier-nw-db2-terraform}

You can use Terraform to automate {{site.data.keyword.cloud_notm}} VPC provisioning. The VPC provisioned includes virtual server instances with high network performance. The VPC infrastructure contains a number of Infrastructure-as-a-Service (IaaS) offerings, including virtual servers. After the VPC is provisioned, the scripts use the Ansible Playbook to install the SAP system.
{: shortdesc}

## {{site.data.keyword.cloud}} VPC introduction
{: #vpc-intro-sap-terraform-3tier-ansible}

A VPC is a public cloud offering that an enterprise uses to establish its own private cloud-like computing environment on shared [public cloud](https://www.ibm.com/cloud){: external} infrastructure. VPCs give an enterprise the ability to define and control a virtual network that is logically isolated from all other public cloud tenants, creating a private, secure place on the public cloud.

Imagine that a cloud provider’s infrastructure is a residential apartment building and multiple families live inside. Being a public cloud tenant is akin to sharing an apartment with a few roommates. In contrast, having a VPC is like having your own private condominium; no one else has the key, and no one can enter the space without your permission.

A VPC’s logical isolation is implemented by using virtual network functions and security features that give an enterprise customer granular control over which IP addresses or applications can access particular resources. It is analogous to the “friends-only” or “public/private” controls on social media accounts used to restrict who can or can’t see your otherwise public posts.

With {{site.data.keyword.cloud_notm}} VPC, you can use the UI, CLI, and API to manually provision virtual server instances for VPC with high network performance. VPC infrastructure contains a number of Infrastructure-as-a-Service (IaaS) offerings, including virtual servers for VPC. Use the following information to understand a simple use case for planning, creating, and configuring resources for your VPC, and learn about more VPC overviews and VPC tutorials. For more information about VPC, see [Getting started with Virtual Private Cloud (VPC)](/docs/vpc?topic=vpc-getting-started).

## SAP on {{site.data.keyword.cloud}}
{: #sap-ibm-cloud-overview-3tier}

SAP NetWeaver is the core foundation of the SAP technology stacks and is the platform that is used for ABAP and Java applications. SAP system can be installed and configured in {{site.data.keyword.cloud_notm}} for various system and database types.

For more information about SAP system architectures on {{site.data.keyword.cloud_notm}} VPC, see the infrastructure reference architectures for SAP for each supported database type. For example, [SAP NetWeaver 7.x on UNIX with Db2 on {{site.data.keyword.cloud}} VPC](/docs/sap?topic=sap-sap-refarch-nw-db2) is the dedicated reference architecture for this SAP solution.

Manually deploying a VPC and installing an SAP system can be time-consuming. The Terraform automation assures not only a much quicker implementation, but also a standardized and less prone to error deployment. Terraform and Ansible are used for automating the deployment processes.

The Terraform scripts solution provides the automated deployment of a single host with SAP NetWeaver with Db2 3-tier on the Red Hat Enterprise Linux&reg; and SUSE for SAP Applications (releases specified in [`README` file](https://github.com/IBM-Cloud/sap-netweaver-abap-db2-distributed/blob/main/README.md){: external}.

The SAP installation media that are used for this deployment are the default media for SAP NetWeaver 7.5 with Db2 (releases specified in [`README` file](https://github.com/IBM-Cloud/sap-netweaver-abap-db2-distributed/blob/main/README.md){: external} available at the SAP Support Portal in the **Installation and Upgrade** area. You provide the installation media as an input parameter for Terraform.

## Ansible for SAP installation
{: #ansible-sap-installation-3tier}

Ansible is an automation tool for the deployment of several IT tasks. Ansible is used for automating the installation of an SAP NetWeaver with Db2 3-tier. For more information about Ansible, see the [Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html){: external}.

The deployment is done by using the Ansible core, which provides CLI tools for automation. More information about Ansible core can be found on the [Ansible core page](https://docs.ansible.com/ansible-core/devel/index.html){: external}.

The Ansible playbook is called directly by the Terraform script. The script starts with Terraform specific steps for creating the VPC, and continues automatically with the Ansible-specific steps to install the SAP system.

## Where to run the scripts
{: #sap-terraform-3tier-nw-db2-where-run-scripts}

The recommended way to run the scripts is from your deployment (bastion) server because the deployment (bastion) server has Terraform and Ansible already installed. If you want to run the scripts from your local workstation, you need to install Terraform and Ansible locally.

For both the deployment (bastion) server and local workstation, you must download the SAP kits to the temporary storage assigned to you on the deployment (bastion) server. Ansible installs the kits for you. You specify the location of the kits in the configuration files.

## Prerequisite, where to run the scripts
{: #sap-terraform-3tier-nw-db2-prereqs}

Before you deploy any of the SAP automated solutions on {{site.data.keyword.cloud_notm}} VPC, you create a bastion server VPC in your chosen region. The bastion server is used for downloading and storing specific SAP solution media that are needed for later automation deployment. The bastion server is used for both CLI deployment scenarios, as well for {{site.data.keyword.bpshort}} UI deployments. For more information about how to create the bastion server and its corresponding VPC, see [Automate SAP bastion server – SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server).

After bastion VPC deployment is complete, you must download the SAP kits to the temporary storage assigned to you on the bastion server. Ansible installs the kits for you. You specify the location of the kits in the configuration files.

This automation is offered at no cost; however, the provisioned infrastructure comes at cost.
{: note}

To save costs, the bastion server, with its SAP media dedicated storage, can be decommissioned after the SAP solutions are successfully implemented on {{site.data.keyword.cloud_notm}} VPC. Or, you can keep the bastion server and use it as a jump host for that specific region.
{: note}
