---

copyright:
  years: 2023
lastupdated: "2023-05-19"

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

# Introduction to {{site.data.keyword.cloud_notm}} VPC and HANA db backup automation on COS
{: #sap-automate-intro-hana-db-backup-cos}

You can use Terraform to automate {{site.data.keyword.cloud}} VPC provisioning. The VPC provisioned includes virtual server instances with high network performance. The VPC infrastructure contains a number of Infrastructure-as-a-Service (IaaS) offerings, including Virtual Servers. After the VPC is provisioned, the scripts use the Ansible Playbook to install the SAP system. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware that uses Intel Xeon CPUs and additional Intel technologies.

## {{site.data.keyword.cloud_notm}} VPC introduction
{: #vpc-intro}

A VPC is a public cloud offering that an enterprise uses to establish its own private cloud-like computing environment on shared [public cloud]( https://www.ibm.com/cloud/public) infrastructure. VPCs give an enterprise the ability to define and control a virtual network that is logically isolated from all other public cloud tenants, creating a private, secure place on the public cloud.

Imagine that a cloud provider’s infrastructure is a residential apartment building and multiple families live inside. Being a public cloud tenant is akin to sharing an apartment with a few roommates. In contrast, having a VPC is like having your own private condominium; no one else has the key, and no one can enter the space without your permission.
A VPC’s logical isolation is implemented by using virtual network functions and security features that give an enterprise customer granular control over which IP addresses or applications can access particular resources. It is analogous to the “friends-only” or “public/private” controls on social media accounts used to restrict who can or can’t see your otherwise public posts.

With {{site.data.keyword.cloud_notm}} VPC, you can use the UI, CLI, and API to manually provision virtual server instances for VPC with high network performance. VPC infrastructure contains a number of Infrastructure-as-a-Service (IaaS) offerings, including Virtual Servers for VPC. Use the following information to understand a simple use case for planning, creating, and configuring resources for your VPC, and learn about more VPC overviews and VPC tutorials. For more information about VPC, see [Getting started with Virtual Private Cloud (VPC)]( https://cloud.ibm.com/docs/vpc?topic=vpc-getting-started).

## SAP on {{site.data.keyword.cloud_notm}}
{: #sap-hana-db-backup-on-cloud}

SAP NetWeaver is the core foundation of the SAP technology stacks and is the platform that is used for ABAP and Java applications. The SAP system can be installed and configured in {{site.data.keyword.cloud_notm}} for various system and database types.

For more information about SAP system architectures on {{site.data.keyword.cloud_notm}} VPC, see the infrastructure reference architectures for SAP for each supported database type. For example, [SAP NetWeaver 7.x on UNIX with HANA on {{site.data.keyword.cloud_notm}} VPC]( https://cloud.ibm.com/docs/sap?topic=sap-sap-refarch-nw-hana&interface=terraform) is the dedicated reference architecture for this SAP solution.

## HANA db backup to {{site.data.keyword.cloud_notm}} Object Storage (COS)
{: #sap-hana-db-backkup-cos}

{{site.data.keyword.cloud_notm}} Object Storage stores encrypted and dispersed data across multiple geographic locations. This getting started tutorial walks through the steps that are needed to use {{site.data.keyword.cloud_notm}} Object Storage to create buckets, upload objects, and set up access policies to allow other users to work with your data.

Manually deploying a VPC and configure a backup/restore on iCOS with a backint hana agent on a cloud platform can be time-consuming. The Terraform automation assures not only a much quicker implementation, but also a standardized and less prone to error deployment. Terraform and Ansible are used for automating the deployment processes.

## Ansible for SAP installation
{: #sap-hana-db-backup-ansible-install}

Ansible is an automation tool for the deployment of several IT tasks. Ansible is used for automating the installation of an Hana db backup to iCOS automation module. For more information about Ansible, see the [Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html).

The deployment is done by using the Ansible core, which provides CLI tools for automation. More information about Ansible core can be found on the [Ansible core page]( https://docs.ansible.com/ansible-core/devel/index.html).
The Ansible playbook is called directly by the Terraform script. The script starts with Terraform specific steps for creating the VPC, and continues automatically with the Ansible specific steps to install the Hana db backup to iCOS automation module system.

## Where to run the scripts
{: #sap-hana-db-backup-where-scripts}

The recommended way to run the scripts is from your Deployment Server because the Deployment Server has Terraform and Ansible already installed. If you want to run the scripts from your local workstation, you need to install Terraform and Ansible locally.
For both the Deployment Server and local workstation, you must download the SAP Kits to the temporary storage assigned to you on the Deployment Server. Ansible installs the kits for you. You specify the location of the Kits in the configuration files.

## Prerequisites, create a Bastion server to run the scripts
{: #sap-hana-db-backup-prereq-bastion} 

Before you deploy any of the SAP automated solutions on {{site.data.keyword.cloud_notm}} VPC, you create a Bastion server VPC in your chosen region. The Bastion server is used for downloading and storing specific SAP solution media that are needed for later automation deployment. The Bastion server is used for both CLI deployment scenarios, as well for Schematics UI deployments. For more information about how to create the Bastion server and its corresponding VPC, see Automate SAP Bastion server – SAP media storage repository.
*	A pair of SSH keys to be used to run the automation for the backup configuration should be available. The public SSH key should be manually added on SAP HANA VSI, in /root/.ssh/authorized_keys and uploaded in {{site.data.keyword.cloud_notm}}.
*	The {{site.data.keyword.cloud_notm}} user running the automation for SAP HANA backup should have the role of Manager on {{site.data.keyword.cloud_notm}} Object Storage. To view/use the credential, the user must have the IAM level access action resource-controller.credential.retrieve_all. This action is given with the Administrator role, and overrides any credential level access enabling the user to view the credential. For more information on granting the necessary authorizations to your IBM cloud user, see https://cloud.ibm.com/iam/users and select the USER_ID wich will run the automation and then check/grant the required roles. More info on Readme file.
*	The kit of the backup agent for IBM COS aws-s3-backint-.*-linuxx86_64.tar.gz should be manually uploaded on the bastion server. This kit is part of SAP HANA kit file and can be found in */DATA_UNITS/HDB_SERVER_LINUX_X86_64/server path. Either the entire SAP HANA kit or only the kit of the backup agent for IBM COS can be provided, but the minimum backint agent kit version to be used is aws-s3-backint-1.2.17-linuxx86_64 
*	The Python script create_hdbbackint.py provided by SAP (SAP note 2935898 - Install and Configure SAP HANA Backint Agent for Amazon S3) to modify the "hdbbackint" script so that it points to the Python 3 libraries should be manually uploaded on the bastion server.

You need to have already deployed an SAP HANA system (built on one of the following OS: SUSE Linux Enterprise Server 15 SP 4 for SAP, SUSE Linux Enterprise Server 15 SP 3 for SAP, Red Hat Enterprise Linux 8.6 for SAP or Red Hat Enterprise Linux 8.4 for SAP) in an {{site.data.keyword.cloud_notm}} Gen2 VPC, on a single host (with or without HA) with the same type and version running on a compatible DB version.

This HANA backup solution was implemented and tested on the following OS images available in {{site.data.keyword.cloud_notm}}: ibm-sles-15-4-amd64-sap-hana-3, ibm-sles-15-3-amd64-sap-hana-3, ibm-redhat-8-6-amd64-sap-hana-2, ibm-redhat-8-4-amd64-sap-hana-2.
{: note}

Ansible installs the kits for you. You specify the location of the Kits in the configuration files.

To save costs the Bastion server, with its SAP media dedicated storage, can be decommissioned after the SAP solutions are successfully implemented on {{site.data.keyword.cloud_notm}} VPC cloud. Or, you can keep the Bastion server and use it as a jump host for that specific region.

