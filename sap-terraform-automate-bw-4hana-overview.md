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

# Automating SAP workload deployment on {{site.data.keyword.cloud}} Virtual Private Cloud (VPC) with Terraform and Ansible
{: #sap-automate-bw-4hana-overview}

Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) infrastructure resources so that you can rapidly build complex, cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware that uses Intel&reg; Xeon CPUs and other Intel&reg; technologies.
{: shortdesc}

You can use Terraform scripts to create a single-tier VPC and create the SAP and SAP HANA in a distributed architecture on the bastion server that you create. Creating the bastion server is a prerequisite for all IBM SAP VPC automated solutions. The Terraform scripts use the VPC information that you provide and then call the Ansible playbook to create the SAP architecture on the specified VPC.

## {{site.data.keyword.cloud}} VPC introduction
{: #sap-automate-bw-4hana-vpc-intro}

A VPC is a public cloud offering that an enterprise uses to establish its own private cloud-like computing environment on shared [public cloud](https://www.ibm.com/cloud/public){: external} infrastructure. VPCs give an enterprise the ability to define and control a virtual network that is logically isolated from all other public cloud tenants, creating a private, secure place on the public cloud.

Imagine that a cloud provider’s infrastructure is a residential apartment building and multiple families live inside. Being a public cloud tenant is akin to sharing an apartment with a few roommates. In contrast, having a VPC is like having your own private condominium; no one else has the key, and no one can enter the space without your permission.

A VPC’s logical isolation is implemented by using virtual network functions and security features that give an enterprise customer granular control over which IP addresses or applications can access particular resources. It is analogous to the “friends-only” or “public/private” controls on social media accounts used to restrict who can or can’t see your otherwise public posts.

With {{site.data.keyword.cloud_notm}} VPC, you can use the UI, CLI, and API to manually provision virtual server instances for VPC with high network performance. VPC infrastructure contains a number of Infrastructure-as-a-Service (IaaS) offerings, including virtual servers for VPC. Use the following information to understand a simple use case for planning, creating, and configuring resources for your VPC, and learn about more VPC overviews and VPC tutorials. For more information about VPC, see [Getting started with Virtual Private Cloud (VPC)](/docs/vpc?topic=vpc-getting-started).

## SAP solution implemented 
{: #sap-bw4hana-automation-solution}

SAP BW/4HANA is a packaged data warehouse based on SAP HANA. As the on-premises data warehouse layer of SAP’s Business Technology Platform, SAP BW/4HANA can be used to consolidate data across the enterprise to get a consistent, agreed-upon view of your data.

SAP BW/4HANA is a next-generation data warehouse SAP product that, like SAP S/4HANA&reg;, is optimized for the SAP HANA platform, including inheriting the high performance, simplicity, and agility of SAP HANA. SAP BW/4HANA delivers real-time, enterprise-wide analytics that minimize the movement of data and can connect all the data in an organization into a single, logical view, including new data types and sources. Organizations now have a data warehousing solution that can meet their current and future business needs. A solution that handles more data, from more sources to deliver the solutions that increase an organization’s success in the next generation of business.

SAP BW/4HANA, combined with SAP S/4HANA and the SAP HANA platform, combines traditional data warehousing, such as operational reporting and historical analysis, with the future  - logical data warehouse, Internet of Things, and data lakes.

SAP BW/4HANA combines with SAP S/4HANA to meet your operational and historical analytics needs by using the same data as your applications, so you are always using fresh data.

With a logical data warehouse approach that uses intelligent data integration, SAP BW/4HANA eliminates duplication and expensive data movement to connect the data silos in your organization. All data sources can be connected, including SAP and non-SAP data sources.

## Ansible for SAP installation
{: #ansible-sap-bw-4hana-installation}

Ansible is an automation tool for the deployment of several IT tasks. For more information about Ansible, see the [Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html){: external}.

The deployment is done by using the Ansible core, which provides CLI tools for automation. More information about Ansible core can be found on the [Ansible core page](https://docs.ansible.com/ansible-core/devel/index.html){: external}.

The Ansible playbook is called directly by the Terraform script. The script starts with Terraform specific steps for creating the VPC, and continues automatically with the Ansible-specific steps to install the SAP system.

## Where to run the scripts
{: #sap-bw-4hana-run-scripts}

The recommended way to run the scripts is from your deployment (bastion) server because the deployment (bastion) server has Terraform and Ansible already installed. If you want to run the scripts from your local workstation, you need to install Terraform and Ansible locally. 

For both the deployment (bastion) server and local workstation, you must download the SAP kits to the temporary storage assigned to you on the deployment (bastion) server. Ansible installs the kits for you. You specify the location of the kits in the configuration files.

## Prerequisite, where to run the scripts
{: #sap-bw4hana-prereqs}

Before you deploy any of the SAP automated solutions on {{site.data.keyword.cloud_notm}} VPC, you create a bastion server VPC in your chosen region. The bastion server is used for downloading and storing specific SAP solution media that are needed for later automation deployment. The bastion server is used for both CLI deployment scenarios, as well for {{site.data.keyword.bpshort}} UI deployments. For more information about how to create the bastion server and its corresponding VPC, see [Automate SAP bastion server – SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server).

After bastion VPC deployment is complete, you must download the SAP kits to the temporary storage assigned to you on the bastion server. Ansible installs the kits for you. You specify the location of the kits in the configuration files.

To save costs, the bastion server, with its SAP media dedicated storage, can be decommissioned after the SAP solutions are successfully implemented on {{site.data.keyword.cloud_notm}} VPC. Or, you can keep the bastion server and use it as a jump host for that specific region. 
{: note}

This automation is offered free of charge however, the provisioned infrastructure comes at cost. {: note}