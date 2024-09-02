---
copyright:
  years: 2023, 2024
lastupdated: "2024-09-02"

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Creating Instances for a High Availability Cluster on IBM {{site.data.keyword.powerSys_notm}}
{: #ha-vsi}

Use the following information and procedures to create the {{site.data.keyword.powerSys_notm}} instances that are required for a high availability cluster implementation.
{: shortdesc}

The following information is provided in the following sections.

- [Creating the {{site.data.keyword.powerSys_notm}} workspace in {{site.data.keyword.cloud}}](#ha-vsi-create-workspace)

    After the workspace is created, you can create and configure virtual server instances, network resources, and storage volumes.
- [Creating a Custom Role, Service ID, and API key in {{site.data.keyword.cloud}}](#ha-vsi-create-service-id)

    For monitoring and management, the fencing agent authenticates to the {{site.data.keyword.powerSys_notm}} API by using the Service API key.
    A custom role allows only the actions required by the fencing agent.

## Before you begin
{: #ha-vsi-begin}

Review the general requirements, product documentation, support articles, and SAP notes listed in [Implementing High Availability for SAP Applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

## Creating the workspace
{: #ha-vsi-create-workspace}

A workspace is an environment that acts as a folder for all the {{site.data.keyword.powerSys_notm}} resources in a geographic region.
These resources include compute, network, and storage volumes.
Resources cannot be moved or shared between different workspaces.
Each workspace is bound to a single data center.

To create a workspace, follow the steps described in [Creating a Power Virtual Server workspace](/docs/power-iaas?topic=power-iaas-creating-power-virtual-server#creating-service){: external}.

The created workspaces are listed under **Workspaces** on the left navigation pane of the Power Virtual Server user interface.
Select the workspace and follow the instructions below.

### Creating private network subnets
{: #ha-vsi-create-subnets}

The virtual server instance is connected to the network and is assigned an IP address from the defined range of IP addresses.
It is recommended that you connect the cluster nodes to a private network rather than a public one.

Follow the steps in [Configuring a private network subnet](/docs/power-iaas?topic=power-iaas-configuring-subnet){: external} to create a subnet.

You need at least one private subnet in the workspace.
{: note}

### Reserving virtual IP addresses
{: #ha-vsi-reserve-virtual-ip-addresses}

A high availability cluster typically requires *virtual IP addresses* that must move with the application in a failover scenario.

Reserve the required IP addresses in the subnet to prevent {{site.data.keyword.powerSys_notm}} from assigning a specific IP address to another virtual server instance.
See [Reserving IP addresses](/docs/power-iaas?topic=power-iaas-configuring-subnet#reserv-ip){: external}.

Make sure that the IP address you want to reserve is within the CIDR range of the subnet and within the *IP range* that you previously restricted.
{: note}

### Exploring more network architecture options
{: #ha-vsi-explore-additional-network-options}

If your {{site.data.keyword.powerSys_notm}} *workspace* is enabled for *Power Edge Router* (PER), you already have network communication with parts of the IBM Cloud network.
The PER solution creates a direct connection to the IBM Cloud Multi Protocol Label Switching (MPLS) backbone, making it easy for different parts of the IBM network to communicate with each other.
For more information, see [Getting started with the Power Edge Router](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-per).

Otherwise, use an {{site.data.keyword.cloud}} connection to connect your {{site.data.keyword.powerSys_notm}} instances to other {{site.data.keyword.cloud}} resources within your account.
{{site.data.keyword.cloud}} connections are not required to configure a Red Hat High Availability cluster on {{site.data.keyword.powerSys_notm}}, but might be required for integration scenarios when you use the {{site.data.keyword.cloud}} Classic network and Virtual Private Cloud (VPC) infrastructures.
For more information, see [Managing {{site.data.keyword.cloud}} connections](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-cloud-connections){: external}.

Use IBM Transit Gateway to connect your {{site.data.keyword.powerSys_notm}} to {{site.data.keyword.cloud}} classic and Virtual Private Cloud (VPC) infrastructures outside your account or region.
For more information about integrating the on-premises network and {{site.data.keyword.powerSys_notm}}, see [Network architecture diagrams](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-network-architecture-diagrams){: external}.

### Creating an SSH key
{: #ha-vsi-create-ssh-key}

Use the following steps to create one or more SSH keys for root login.

Create a keypair and load the public key to the SSH keys store in {{site.data.keyword.powerSys_notm}}.
During deployment of the virtual server instance, specify one or more keys from the keystore.
These keys are added to the `authorized_keys` file of the root user, and allow you to securely log in to the virtual server instance by using your private key.


For more information, see [Generating an SSH key](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-create-vm#ssh-setup){: external}.

The preferred choice is the *ed25519* key type.
It offers both security and performance advantages.
{: tip}

1. Log in to [Workspaces](https://cloud.ibm.com/power/ssh-keys){: external}.
1. Select the **workspace** that you created.
1. Click [SSH keys](https://cloud.ibm.com/power/ssh-keys){: external}.
1. Click **Create SSH key**.
1. Enter a **Key name**. Then copy and paste the **public key** that you generated earlier into the field.
1. Click **Add SSH key**.

### Selecting a boot image
{: #ha-vsi-select-boot-image}

There are several options to obtain operating system images for the cluster nodes.
Use the following steps to select a boot image.

You can choose from several types of stock images that are already prepared for {{site.data.keyword.powerSys_notm}}.
Images are available in the *IBM Provided Subscription* and *Client Provided Subscription* sections of the {{site.data.keyword.powerSys_notm}} provisioning page.
For more information, see [Full Linux® subscription for {{site.data.keyword.powerSys_notm}} instances](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-set-full-Linux){: external}.

If you want to import a custom Linux image, you must first upload the image to the {{site.data.keyword.cloud}} Object Storage in OVA format.

- [Importing a boot image](/docs/power-iaas?topic=power-iaas-importing-boot-image){: external}
- [Deploying a custom image within IBM Power Virtual Server](/docs/power-iaas?topic=power-iaas-deploy-custom-image){: external}

Before you begin, make sure that the OVA image is loaded in the storage bucket.

## Creating virtual server instances for the cluster
{: #ha-vsi-create-virtual-server-instances}

Complete the following steps to create the virtual server instances that you want to use as high availability cluster nodes.

1. Log in to [Workspaces - {{site.data.keyword.powerSys_notm}}](https://cloud.ibm.com/power/workspaces){: external}.
1. Select the **Workspace** that you created.
1. Click **View virtual server instances** > **Create Instance**.
   You need to step through the subsections **General**, **Boot Image**, **Profile**, **Storage Volume**, **Network Interfaces**.
1. In subsection **General**, enter the **Instance name** and click **+** to increase the **Number of instances** to 2.
1. Select **Numerical postfix** as *Instance naming convention*, and select **Different server** as *Placement group colocation policy*.
   A placement group with colocation policy *Different server* is automatically created as part of the virtual server instances deployment.
1. Select the **SSH key** that you created and click **Continue**.
1. In **Boot image**, select the **Operating system** according to your subscription model. \
   Use one of the Linux selections either from the *IBM-provided subscription* or through your *Client-provided subscription*. \
   Keep **Auto-select pool** for selecting the *Storage Pool*. \
   Click **Continue**.
1. In **Profile**, select **Machine type**, **Core type**, and the virtual server instance **profile** to match your workload requirements.
   Click **Continue**.
1. In **Storage volumes**, click **Continue**.

   When you deploy multiple instances, the storage volumes that are created are shared by all instances.
   Certain high availability cluster scenarios require shared volumes.
   In these cases, create the shared volumes later.
   For SAP HANA, see [Storage configuration for SAP HANA](https://cloud.ibm.com/docs/sap?topic=sap-storage-design-considerations#storage-config-hana){: external}.
   These volumes must be created later for the individual server instances after their deployment is complete.
   {: important}

1. In the **Network Interfaces** subsection, it is recommended that the cluster nodes are not directly accessible from a public network, so leave the *Public networks* configuration as **Off**.
1. Click **Attach** to attach the virtual server instances to an existing subnet.
1. In the *Attach an existing network* screen, select one of the *Existing networks*. You can either select **Automatically assign IP address from IP range**, or **Manually specify an IP address from IP range** to specify an available IP address.
1. Click **Attach.**
1. Click **Finish**, check the *I agree to the Terms and Conditions* flag, and click **Create**.

The deployment of the virtual server instances starts.

## Preparing operating system for SAP installation
{: #ha-vsi-prepare-sap-install}

If you deployed a virtual server instance from a stock image, you need to perform extra configuration tasks before you can install SAP software.
For more information, see [Configuring a Power Virtual Server instance](https://cloud.ibm.com/docs/sap?topic=sap-power-vs-set-up-power-instances#power-vs-set-up-power-basic-os-config).
{: important}

## Creating a Custom Role, Service ID, and API key in {{site.data.keyword.cloud}}
{: #ha-vsi-create-service-id}

A *Service ID* in {{site.data.keyword.cloud}} identifies a service or an application in a similar way as a user ID identifies a user.
Create a *service ID* for the fencing agent to allow access to IBM Power Cloud actions such as monitoring or controlling the virtual server instances.
Create a custom role in advance to limit the allowed IBM Power Cloud API actions to only those required for fencing.

Managing *Custom Roles*, *Service IDs*, and *API keys* is part of IBM Cloud Identity and Access Management (IAM).
Navigate to the IAM for the following steps.

### Login to IBM Cloud Identity and Access Management
{: #ha-vsi-create-service-id-logon}

Access IBM Cloud Identity and Access Management (IAM).
1. Log on to [{{site.data.keyword.cloud}}](https://cloud.ibm.com/){: external}.
1. On the toolbar, click **Manage** to expand the drop-down menu, then select **Access (IAM)**.

### Creating a custom role for the fencing agent
{: #ha-vsi-create-service-id-fencing-role}

Create a *custom role* in IAM and assign the set of actions required for a fencing operation in the role.
You must grant access for the following actions.
- reading objects in the *cloud_instance* or *workspace*
- listing virtual server instances
- getting information about a virtual server instance
- performing an action on a virtual server instance

Create a **custom role** in IAM.
1. Click **Roles** > **Create**.
1. Enter the **Name**, **ID** and **Description** for the custom role.
1. Select *Workspace for Power Virtual Server* from the **Service** drop-down list.
1. Select *Manager* from the **View the actions for** drop-down list.
1. In the **Actions** list, locate the following actions.
   Click **Add** for each of them.
   - power-iaas.pvm-instance.list
   - power-iaas.pvm-instance.read
   - power-iaas.pvm-instance.action

2. Click **Create** to save the role.

### Creating a custom role for the `powervs-subnet` resource agent
{: #ha-vsi-create-service-id-subnet-role}

This step is only required if you are implementing a cluster in a multizone region environment with the `powervs-subnet` resource agent.
{: note}

Create a *custom role* in IAM and assign the set of actions required for a `subnet move` operation in the role.
You must grant access for the following actions.
- reading objects in the *cloud_instance* or *workspace*
- listing and getting information for subnets in the *workspace*
- creating and deleting subnets in the *workspace*
- attaching and detaching subnets to or from a virtual server instance
- deleting network ports

Create a **custom role** in IAM.
1. Click **Roles** > **Create**.
1. Enter the **Name**, **ID** and **Description** for the custom role.
1. Select *Workspace for Power Virtual Server* in the **Service** drop-down list.
1. Select *Manager* from the **View the actions for** drop-down list.
1. In the **Actions** list, locate the following actions.
   Click **Add** for each of them.
   - power-iaas.cloud-instance.read
   - power-iaas.pvm-instance-network.list
   - power-iaas.pvm-instance-network.read
   - power-iaas.network.list
   - power-iaas.network.create
   - power-iaas.network.delete
   - power-iaas.network-port.delete
   - power-iaas.pvm-instance-network.create
   - power-iaas.pvm-instance-network.delete
1. Click **Create** to save the role.

If you want to share a common `Service ID` and `API key` for both the fencing agent and the `powervs-subnet` resource agent, add the action `power-iaas.pvm-instance.action` to the custom role.
{: note}

### Creating a Service ID
{: #ha-vsi-create-service-id-id}

Create a *Service ID* for the fencing agent and assign the custom role to it.
In a multizone region implementation, you can create a second *Service ID* for the `powervs-subnet` resource agent.
It is also possible to use a common Service ID for both agents (see the note in the previous section).

Create a **Service ID** in IAM.
1. Click **Service IDs** > **Create**.
1. Enter a **Name** and **Description** for the service ID.
1. Click **Create**.
1. In the *Access policies* section, click **Assign access**.
1. In the  *Service* section, select **Workspace for {{site.data.keyword.powerSys_notm}}** and click **Next**.
1. In the *Resource* section, select **Specific Resources** > **Service Instance** > **string equals** > *name of the workspace that you created earlier*.
   Click **Next**.
1. In the *Roles and actions* section, select the custom role you created earlier from **Custom access** and click **next**.
1. You can skip the *Conditions (Optional)* section.
1. Click **Add** and then **Assign** to create the *Service ID*.

If you create a *Service ID* for the `powervs-subnet` resource agent in a multizone region implementation, you must click **Assign access** in the *Access policies* section again and follow the steps to assign access for the second workspace.
{: important}

### Creating an API key for the Service ID
{: #ha-vsi-create-service-api-key}

When you configure the *fencing agent* in the *Red Hat HA cluster* or the *powervs-subnet* resource agent in a multizone region implementation, you must specify an *API key*.
The *API key* authorizes the fencing agent or resource agent to use the IBM Power Cloud API to perform the actions that are defined in the *Service ID*.

Create the **API Key** for the **Service ID** in IAM.

1. Click **Service IDs** and select the *Service ID* that you created earlier.
2. Click **API Keys** to switch to the *Create and manage API keys for this service ID* tab.
3. Click **Create**.
4. Enter a **Name** and a **Description** for the key.
5. Click **Create**.

Click download to save the API key.
Keep the key in a safe place.

The key is available for 300 seconds.
After 300 seconds, you won't be able to view or retrieve the key.
{: important}
