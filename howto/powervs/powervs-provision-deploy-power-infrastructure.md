---
copyright:
   years: 2020, 2026
lastupdated: "2026-02-27"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, {{site.data.keyword.powerSys_notm}} Workspace, SSH Key, Transit Gateway, {{site.data.keyword.powerSys_notm}} Subnets, Networks
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Preparing {{site.data.keyword.powerSys_notm}} workspaces for SAP workloads
{: #powervs-set-up-power-infrastructure}

For SAP workloads that run on {{site.data.keyword.powerSysFull}}, one workspace for {{site.data.keyword.powerSys_notm}} is deployed.

The following diagram shows deployed components.

![{{site.data.keyword.powerSys_notm}} infrastructure for SAP](../../images/powervs-provision-deploy-arch-ibm-pvs-inf-standard.svg){: caption="{{site.data.keyword.powerSys_notm}} infrastructure for SAP" caption-side="bottom"}

## Deploying a {{site.data.keyword.powerSys_notm}} workspace
{: #powervs-set-up-power-service}

A {{site.data.keyword.powerSys_notm}} workspace is a regional environment that provides compute, network, and storage resources.
Resources that are created in a workspace are isolated to that workspace and cannot be moved or shared between workspaces.
Each workspace is associated with a single data center.
All SAP HANA and SAP application server instances are deployed within the designated {{site.data.keyword.powerSys_notm}} workspace.

Create the {{site.data.keyword.powerSys_notm}} workspace in the same region as your IBM Cloud edge VPC.
To create a workspace, follow the steps that are described in [Creating a {{site.data.keyword.powerSys_notm}} workspace](/docs/power-iaas?topic=power-iaas-creating-power-virtual-server#creating-service){: external}.

### Creating an SSH key
{: #powervs-set-up-power-ssh-key}

1. Open the Power Virtual Server console and select your workspace.
1. In the Compute section, click SSH keys.
1. Click Create SSH key.
1. Enter a unique name, select the access level, add a description, and provide the SSH key details.
1. Click Add SSH key.

SSH keys can exist at either the account level or the workspace level.
Traditionally, keys were stored at the account (tenant) level, which made them visible across all workspaces in the account.
Workspace-scoped keys provide isolation between environments and are restricted to a single workspace.
{: note}

### Creating network subnets
{: #powervs-set-up-power-subnets}

1. Create private networks for management and backup.
   For more information, see [Configuring and adding a private network subnet](/docs/power-iaas?topic=power-iaas-configuring-subnet).

1. Create a dedicated private network subnet for each SAP system that you install in the workspace.

1. Connect the {{site.data.keyword.powerSys_notm}} workspace to the {{site.data.keyword.tg_full}} as described in [Adding a connection](/docs/transit-gateway?topic=transit-gateway-adding-connections&interface=ui).

You can now ping the IBM {{site.data.keyword.powerSys_notm}} private network IP addresses from the access host that is deployed in VPC.

## Terraform automation - Deploying the VPC landing zone for {{site.data.keyword.powerSys_notm}}
{: #powervs-vpc-landing-zone-automation}

All the preceding steps are automated by using Terraform and Ansible and are available as a *Deployable Architecture* solution in the IBM Cloud catalog.
To configure and deploy, see [Power Virtual Server with VPC landing zone - 'Standard Landscape Variation'](/docs/powervs-vpc?topic=powervs-vpc-deploy-arch-ibm-pvs-inf-standard) for more details.
