---
copyright:
   years: 2020, 2025
lastupdated: "2025-03-21"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, {{site.data.keyword.powerSys_notm}} Workspace, SSH Key, Transit Gateway, {{site.data.keyword.powerSys_notm}} Subnets, Networks
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Deploying {{site.data.keyword.powerSys_notm}} infrastructure
{: #powervs-set-up-power-infrastructure}

For SAP workloads that run on {{site.data.keyword.powerSysFull}}, one workspace for {{site.data.keyword.powerSys_notm}} is deployed.

The following diagram shows deployed components.

![{{site.data.keyword.powerSys_notm}} infrastructure for SAP](../../images/powervs-provision-deploy-arch-ibm-pvs-inf-standard.svg){: caption="{{site.data.keyword.powerSys_notm}} infrastructure for SAP" caption-side="bottom"}

## Deploying a {{site.data.keyword.powerSys_notm}}
{: #powervs-set-up-power-service}

Workspaces for {{site.data.keyword.powerSys_notm}} host all the SAP instances that don't require direct internet access. All SAP NetWeaver and SAP HANA instances are deployed in the workspace for {{site.data.keyword.powerSys_notm}}. To create and configure this service, use the following steps.

1. Deploy a new workspace for {{site.data.keyword.powerSys_notm}} in the same region where your IBM Cloud edge VPC is. For more information, see [Creating a Power Systems Virtual Server workspace](/docs/power-iaas?topic=power-iaas-creating-power-virtual-server#creating-service).
1. Upload your SSH public key to the workspace. SSH keys are distributed across all the workspaces that are in your account, so you generate the SSH key one time.
   - Go to **SSH keys**, click `Create SSH key`, provide a unique name, and press **Add SSH key**.
1. In the workspace, create a management and backup private networks. For more information, see [Configuring and adding a private network subnet](/docs/power-iaas?topic=power-iaas-configuring-subnet).
1. Connect the {{site.data.keyword.powerSys_notm}} workspace to the transit gateway as described [here](/docs/transit-gateway?topic=transit-gateway-adding-connections&interface=ui). You can now ping gateway IBM {{site.data.keyword.powerSys_notm}} private networks IP addresses from the access host deployed in VPC.

## Terraform automation - Deploying the VPC landing zone for {{site.data.keyword.powerSys_notm}}
{: #powervs-vpc-landing-zone-automation}

All the above steps are automated using terraform and ansible and is available as a Deployable Architecture Solution in IBM Cloud Catalog. To configure and deploy check the [link](/docs/powervs-vpc?topic=powervs-vpc-deploy-arch-ibm-pvs-inf-standard) for further details.
