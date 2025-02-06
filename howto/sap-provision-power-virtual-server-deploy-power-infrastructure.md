---

copyright:
   years: 2020, 2022

lastupdated: "2022-11-30"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads

subcollection: sap

---

{{site.data.keyword.attribute-definition-list}}

# Deploying SAP {{site.data.keyword.powerSys_notm}} workloads
{: #power-vs-set-up-power-infrastructure}

For SAP workloads that run on {{site.data.keyword.powerSysFull}}, one workspace for {{site.data.keyword.powerSys_notm}} is deployed. When the workspace is deployed, you see the following results.

* One workspace for {{site.data.keyword.powerSys_notm}}
* Public SSH key is imported
* Two private networks are created and connected through redundant IBM Cloud Connections that pair with all your VPC services.

The following diagram shows deployed components.

![{{site.data.keyword.powerSys_notm}} infrastructure for SAP](../images/step2_manual_PowerVS-with-VPC.svg){: caption="{{site.data.keyword.powerSys_notm}} infrastructure for SAP" caption-side="bottom"}

## Deploying a {{site.data.keyword.powerSys_notm}}
{: #power-vs-set-up-power-service}

Workspaces for {{site.data.keyword.powerSys_notm}} host all the SAP instances that don't require direct internet access. All SAP NetWeaver and SAP HANA instances are deployed in the workspace for {{site.data.keyword.powerSys_notm}}. To create and configure this service, use the following steps.

1. Deploy a new workspace for {{site.data.keyword.powerSys_notm}} in the same region where your IBM Cloud edge VPC is. For more information, see [Creating a Power Systems Virtual Server workspace](/docs/power-iaas?topic=power-iaas-creating-power-virtual-server#creating-service).
1. Optionally, you can upload your SSH public key to the workspace. SSH keys are distributed across all the workspaces that are in your account, so you generate the SSH key one time.
   - Go to **SSH keys**, click `Create SSH key`, provide a unique name, and press **Add SSH key**.
1. In the workspace, create a management and backup private networks. For more information, see [£Configuring and adding a private network subnet](/docs/power-iaas?topic=power-iaas-configuring-subnet).
1. Create two IBM Cloud Connections as described [here](/docs/power-iaas?topic=power-iaas-cloud-connections#create-cloud-connections). Make sure that you select to enable IBM Transit Gateway. Attach the management and backup networks to both IBM Cloud Connections.
1. Connect Direct Links (named exactly as IBM Cloud Connections that you created) to the transit gateway as described [here](/docs/transit-gateway?topic=transit-gateway-adding-connections&interface=ui). You can now ping gateway IBM {{site.data.keyword.powerSys_notm}} private networks IP addresses from the access host.
