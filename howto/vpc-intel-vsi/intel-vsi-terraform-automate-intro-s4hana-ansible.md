---
copyright:
  years: 2023, 2025
lastupdated: "2025-02-27"
keywords: S4HANA, automation, ansible, VPC
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Introduction to SAP S/4HANA workload deployment on {{site.data.keyword.cloud_notm}} VPC
{: #vpc-vsi-intro-s4hana}

You can use Terraform to automate {{site.data.keyword.cloud}} VPC provisioning. The VPC provisioned includes virtual server instances with high network performance. The VPC infrastructure contains a number of Infrastructure-as-a-Service (IaaS) offerings, including Virtual Servers. After the VPC is provisioned, the scripts use the Ansible Playbook to install the SAP system.

## {{site.data.keyword.cloud_notm}} VPC introduction
{: #vpc-intro-s4hana}

A VPC is a public cloud offering that an enterprise uses to establish its own private cloud-like computing environment on shared [public cloud](https://www.ibm.com/cloud){: external} infrastructure. VPCs give an enterprise the ability to define and control a virtual network that is logically isolated from all other public cloud tenants, creating a private, secure place on the public cloud.

Imagine that a cloud provider’s infrastructure is a residential apartment building and multiple families live inside. Being a public cloud tenant is akin to sharing an apartment with a few roommates. In contrast, having a VPC is like having your own private condominium; no one else has the key, and no one can enter the space without your permission.

A VPC’s logical isolation is implemented by using virtual network functions and security features that give an enterprise customer granular control over which IP addresses or applications can access particular resources. It is analogous to the “friends-only” or “public/private” controls on social media accounts used to restrict who can or can’t see your otherwise public posts.

With {{site.data.keyword.cloud_notm}} VPC, you can use the UI, CLI, and API to manually provision virtual server instances for VPC with high network performance. VPC infrastructure contains a number of Infrastructure-as-a-Service (IaaS) offerings, including Virtual Servers for VPC. Use the following information to understand a simple use case for planning, creating, and configuring resources for your VPC, and learn about more VPC overviews and VPC tutorials. For more information about VPC, see [Getting started with Virtual Private Cloud (VPC)](/docs/vpc?topic=vpc-getting-started).

## SAP S/4HANA on {{site.data.keyword.cloud_notm}}
{: #vpc-vsi-intro-s4hana-sap}

SAP S/4HANA is an ERP system from SAP's ERP software product line. The software is based on the innovative SAP HANA database technology and was started as the fourth product generation in 2015. Users can choose between the SAP S/4HANA Cloud and on-premises solution.

An ERP system is used for demand-oriented business resource planning. It is used to control processes and to link departments and functional areas in a meaningful way. Individual modules include applications for accounting, sales, production, and marketing. More complex tasks in customer or supply chain management can also be done by ERP software. As the successor to the core product SAP ECC, SAP S/4HANA was presented as the intelligent ERP system of the new generation. With the help of modern technologies, the Software as Service (SaaS) version is designed to help companies standardize processes and make the leap to digitalization.

While previous SAP ERP solutions support the most common databases, SAP S/4HANA uses exclusively the SAP HANA in-memory database developed by SAP. This in-memory database offers users the greatest technical benefit and they benefit from increased performance. The "S" in S/4HANA stands for "simple", while the "4" refers to the generation sequence. Compared to the SAP core product SAP ECC, which is still used in most companies, SAP S/4HANA offers many innovative functions that revolutionize the system landscape from the ground up. As SAP plans to discontinue the mainstream maintenance of its existing ERP solutions by 2027, many SAP ECC users are already considering a migration to SAP HA SZ or MZ S/4HANA.

## Available automated S/4HANA versions on {{site.data.keyword.cloud_notm}} VPC
{: #intro-s4hana-versions}

|**SAP Product Version**|**Leading Software Component Version**|**Support Package Name**|
|-----|-----|-----|
|SAP S/4HANA 2020|S4CORE 105|SAPK-105{xx}INS4CORE.|
|SAP S/4HANA 2021|S4CORE 106|SAPK-106{xx}INS4CORE.|
|SAP S/4HANA 2022|S4CORE 107|SAPK-107{xx}INS4CORE.|
|SAP S/4HANA 2023|S4CORE 108|SAPK-108{xx}INS4CORE.|
{: caption="Automated S/4HANA versions" caption-side="bottom"}

## Ansible for SAP installation
{: #vpc-vsi-intro-s4hana-ansible}

Ansible is an automation tool for the deployment several IT tasks. Ansible is used for automating the installation of an SAP Netweaver with Db2. For more information about Ansible, see the [Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html){: external}.

The deployment is done by using the Ansible core, which provides CLI tools for automation. More information about Ansible core can be found on the [Ansible core page](https://docs.ansible.com/ansible-core/devel/index.html){: external}.

The Ansible playbook is called directly by the Terraform script. The script starts with Terraform specific steps for creating the VPC, and continues automatically with the Ansible specific steps to install the SAP system.

## Where to run the scripts
{: #vpc-vsi-intro-s4hana-where-scripts}

The recommended way to run the scripts is from your Deployment Server because the Deployment Server has Terraform and Ansible already installed. If you want to run the scripts from your local workstation, you need to install Terraform and Ansible locally.

For both the Deployment Server and local workstation, you must download the SAP Kits to the temporary storage assigned to you on the Deployment Server. Ansible installs the kits for you. You specify the location of the Kits in the configuration files.

## Prerequisite, where to run the scripts
{: #vpc-vsi-intro-s4hana-prerequisite-scripts}

Before you deploy any of the SAP automated solutions on IBM Cloud VPC, you create a bastion server VPC in your chosen region. The bastion server is used for downloading and storing specific SAP solution media that are needed for later automation deployment. The Bastion server is used for both CLI deployment scenarios, as well for Schematics UI deployments. For more information about how to create the Bastion server and its corresponding VPC, see [Automate SAP bastion server – SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server).

After bastion VPC deployment is complete, you must download the SAP Kits to the temporary storage assigned to you on the Bastion Server. Ansible installs the kits for you. You specify the location of the Kits in the configuration files.

This automation is offered at no cost; however, the provisioned infrastructure comes at cost.
{: note}

To save costs the bastion server, with its SAP media dedicated storage, can be decommissioned after the SAP solutions are successfully implemented on IBM Cloud VPC cloud. Or, you can keep the bastion server and use it as a jump host for that specific region.
{: note}
