---
copyright:
  years: 2023, 2024
lastupdated: "2024-07-16"

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Creating instances for a high availability cluster on IBM {{site.data.keyword.powerSys_notm}}
{: #ha-vsi}

Use the following information and procedures to create the {{site.data.keyword.powerSys_notm}} instances that are required for a high availability cluster implementation.
{: shortdesc}

The following information is provided in the following sections.

- [Creating the {{site.data.keyword.powerSys_notm}} workspace in {{site.data.keyword.cloud}}](#ha-vsi-create-workspace)

    After the workspace is created, you can create and configure virtual server instances, network resources, and storage volumes.

- [Creating a Service ID API Key in {{site.data.keyword.cloud}}](#ha-vsi-create-service-id)

    For monitoring and management, the fencing agent authenticates to the {{site.data.keyword.powerSys_notm}} API by using the Service API key.


## Before you begin
{: #ha-vsi-begin}

Review the general requirements, product documentation, support articles, and SAP notes listed in [Implementing High Availability for SAP Applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

## Creating the workspace
{: #ha-vsi-create-workspace}

A workspace is the environment that acts as a folder for all the {{site.data.keyword.powerSys_notm}} resources in a specific geographic region.
These resources include computing, network, and storage volumes.
Resources cannot be moved or shared between different workspaces.
Each workspace is tied to a single data center.

Log in into [{{site.data.keyword.powerSys_notm}}](https://cloud.ibm.com/power/overview){: external} in {{site.data.keyword.cloud}}.

1. Select **Workspaces** in the navigation bar and press **Create** to open the **Create workspace** menu.
1. Enter the **Workspace name** and select a **Resource group** according to the intended assignment of the cluster resources.
1. Select the **Region** where you want the resources to deploy.
1. Add any **User tags** and **Access management tags** according to your policies.
1. Click **Create** to initiate the workspace.

## Creating subnets
{: #ha-vsi-create-subnets}

The virtual server instance is connected to the network and gets an IP address from the assigned IP range.

It is recommended that you connect the cluster nodes to a private network, not to a public network.

An extra bastion node can access both public and private networks and can be used to tunnel SSH connections to the cluster nodes.

Private subnets are created in the context of the {{site.data.keyword.powerSys_notm}} workspace. When you connect different networks, you can use GRE Tunneling if a subnet range conflicts with existing classical infrastructure.

You need at least one private subnet in the workspace.
{: tip}

Use the following steps to create a subnet.

1. Go to [Subnets in {{site.data.keyword.powerSys_notm}}](https://cloud.ibm.com/power/subnets){: external}.
1. Select the **workspace** that you created.
1. Click **Create subnet** and enter the following information for the new subnet.
   - **Name** for the new subnet.
   - Classless Inter-Domain Routing (**CIDR**) in form of an address prefix.
   - Number of bits reserved for the netmask separated by a slash.

   By entering a specific **IP range**, you can restrict the IP address range to a subset of the full CIDR range.
   Restricting the IP address range prevents IP addresses from being automatically assigned to another virtual server instance during a provisioning request.
1. Click **Create subnet**.

## Reserving virtual IP addresses
{: #ha-vsi-reserve-virtual-ip-addresses}

A high availability cluster typically needs *virtual IP addresses* that must move with the application in a failover scenario.

Reserve an IP address in the subnet to prevent {{site.data.keyword.powerSys_notm}} from assigning a specific IP address to a virtual server instance.
See [Reserving IP addresses](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-configuring-subnet#reserv-ip){: external}.

- Open [Subnets](https://cloud.ibm.com/power/subnets) in IBM Cloud for your {{site.data.keyword.powerSys_notm}} *workspace*.
- From the list of subnets, click the subnet for which you want to reserve the *IP address*.
- Click *Reserve IP*.
- Enter your IP address in the *IP address* field.

Make sure that the IP address you want to reserve is within the CIDR range of the subnet and within the *IP range* that you previously restricted.
{: tip}

- Provide a description of your reserved IP in the *Reserved IP description (optional)* field.

## Exploring more network architecture options
{: #ha-vsi-explore-additional-network-options}

If your {{site.data.keyword.powerSys_notm}} *workspace* is enabled for *Power Edge Router* (PER), you already have network communication with parts of the IBM network.
The PER solution creates a direct connection to the IBM Cloud MPLS Multi Protocol Label Switching (MPLS) backbone, making it easy for different parts of the IBM network to communicate with each other.
For more information, see [Getting started with the Power Edge Router](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-per).

Otherwise, use an {{site.data.keyword.cloud}} connection to connect your {{site.data.keyword.powerSys_notm}} instances to other {{site.data.keyword.cloud}} resources within your account.
{{site.data.keyword.cloud}} connections are not required to configure a Red Hat High Availability cluster on {{site.data.keyword.powerSys_notm}}, but might be required for integration scenarios when you use the {{site.data.keyword.cloud}} Classic network and Virtual Private Cloud (VPC) infrastructures.
For more information, see [Managing {{site.data.keyword.cloud}} connections](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-cloud-connections){: external}.

Use IBM Transit Gateway to connect your {{site.data.keyword.powerSys_notm}} to {{site.data.keyword.cloud}} classic and Virtual Private Cloud (VPC) infrastructures outside your account or region.
For more information about integrating the on-premises network and {{site.data.keyword.powerSys_notm}}, see [Network architecture diagrams](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-network-architecture-diagrams){: external}.

## Creating an SSH key
{: #ha-vsi-create-ssh-key}

Use the following steps to create one or more root login SSH keys.

First, you need to create a keypair and load the public key to the SSH keys store in {{site.data.keyword.powerSys_notm}}.
For deployment of the virtual server instance, specify one or more keys out of the keystore.
These keys are added to the authorized key file of the root user, and allow you to securely log in to the virtual server instance by using your private key.

For more information, see [Generating an SSH key](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-create-vm#ssh-setup){: external}.

The recommendation is to use key type *Ed25519* because this key type is fast and secure.
{: tip}

1. Log in to [SSH keys in {{site.data.keyword.powerSys_notm}}](https://cloud.ibm.com/power/ssh-keys){: external}
1. Select the **workspace** that you created.
1. Click **Create SSH key**.
1. Enter a **Key name**, then copy and paste the **Public key** that you generated before into the field.
1. Click **Add SSH key**.

## Selecting a boot image
{: #ha-vsi-select-boot-image}

You have different options for obtaining operating system images for your cluster.
Use the following steps to select a boot image.

You have access to several types of stock images that are already prepared for {{site.data.keyword.powerSys_notm}}. Images are sorted into "IBM Provided Subscription" and "Client Provided Subscription" sections on the {{site.data.keyword.powerSys_notm}} provisioning page.
For more information, see [Full Linux® subscription for {{site.data.keyword.powerSys_notm}} instances](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-set-full-Linux){: external}.

If you want to import a custom Linux image, you need to first upload the image to {{site.data.keyword.cloud}} Object Storage in OVA format.

- [Deploying a custom image within a {{site.data.keyword.powerSys_notm}} workspace](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-deploy-custom-image){: external}, which outlines the necessary steps for
- [Creating an {{site.data.keyword.cloud}} Storage bucket](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-deploy-custom-image#cloud-storage-bucket){: external}
- [Generating secret and access keys](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-deploy-custom-image#access-keys){: external}

Before you begin, make sure that the OVA image is loaded in the storage bucket.

1. Log in to [Boot images in {{site.data.keyword.powerSys_notm}}](https://cloud.ibm.com/power/boot-images){: external}.
1. Select the **workspace** that you created.
1. Click **Import image**.
1. Enter a **Custom image name** for the image name in your catalog.
1. Select **Storage type** either as *Tier 1* or *Tier 3*. A virtual server instance can use volumes from one storage type only, and the custom image need to be prepared for this storage type.
1. Select the **Region** for your deployment.
1. Enter the file name of the image as **Image file name**.
1. Enter the **Bucket name** of your Cloud Object Storage.
1. Enter **Cloud Object Storage access key** and **Cloud Object Storage secret key**.
1. Click **Import image**.

## Creating virtual server instances for the cluster
{: #ha-vsi-create-virtual-server-instances}

Complete the following steps to create the virtual server instances that you want to use as high availability cluster nodes.

1. Log in to [Workspaces - {{site.data.keyword.powerSys_notm}}](https://cloud.ibm.com/power/workspaces){: external}.
1. Select the **Workspace** that you created.
1. Click **View virtual server instances** > **Create Instance**.
   You need to step through the subsections General, Boot Image, Profile, Storage Volume, Network Interfaces.
1. In subsection **General**, enter the **Instance name** and click **+** to increase the **Number of instances** to 2.
1. Select **Numerical postfix** as Instance naming convention, and select **Different server** as Placement group colocation policy.
   A placement group with colocation policy *Different server* is automatically created as part of the virtual server instances deployment.
1. Select the **SSH key** that you created and click **Continue**.
1. In **Boot image**, select the **Operating system** according to your subscription model. \
   Use one of the Linux selections either from the *IBM-provided subscription* or through your *Client-provided subscription*. \
   Keep **Auto-select pool** for selecting the Storage Pool. \
   Click **Continue**.
1. In **Profile**, select **Machine type**, **Core type**, and the virtual server instance **profile** according to your workload requirements.
   Click **Continue**.
1. In **Storage volumes**, click **Continue**.

   When you deploy multiple instances, the storage volumes that are created are shared by both instances.
   Certain high availability cluster scenarios require shared volumes.
   In these cases, create the shared volumes later.
   For SAP HANA, see [Storage configuration for SAP HANA](https://cloud.ibm.com/docs/sap?topic=sap-storage-design-considerations#storage-config-hana){: external}.
   Those volumes must be created later for the individual server instances after their provisioning completes.
   {: important}

1. In subsection **Network Interfaces**, it is recommended that the cluster nodes are not accessible directly from a public network, so keep the configuration for *Public networks* as **Off**.
1. Click **Attach existing** to attach the virtual server instances to a subnet.
1. In the *Attach an existing network* screen, select one of the *Existing networks*. You can either select **Automatically assign IP address from IP range**, or **Manually specify an IP address from IP range** to specify an available IP address.
1. Click **Attach.**
1. Click **Finish**, check the *I agree to the Terms and Conditions* flag, and click **Create**.

The deployment of the virtual server instances starts.

## Preparing remote login for virtual server instances
{: #ha-vsi-prepare-remote-login}

Set up SSH forwarding on the bastion host, and prepare or test SSH remote login from your workstation by using your private SSH key.

## Preparing operating system for SAP installation
{: #ha-vsi-prepare-sap-install}

If you deployed a virtual server instance from a stock image, you need to perform extra configuration tasks before you can install SAP software.
For more information, see [Configuring a Power Virtual Server instance](https://cloud.ibm.com/docs/sap?topic=sap-power-vs-set-up-power-instances#power-vs-set-up-power-basic-os-config).
{: important}


## Creating a Service ID and API key in {{site.data.keyword.cloud}}
{: #ha-vsi-create-service-id}

A Service ID in {{site.data.keyword.cloud}} identifies a service or an application in a similar way as a user ID identifies a user in {{site.data.keyword.cloud}}.
The service ID is used by the cluster fencing agent to monitor the status of and control the virtual server instances in the cluster.

1. Log in to [{{site.data.keyword.cloud}}](https://cloud.ibm.com/){: external}.
1. In the toolbar, click **Manage** to expand the drop-down menu, then select **Access (IAM)**.
1. Click **Service IDs** > **Create**.
1. Enter **Name** and **Description** for the service ID.
1. Click **Create**.
1. In the Access policies section, click **Assign access**.
1. As Service, click **Workspace for {{site.data.keyword.powerSys_notm}}** > **Next**.
1. Click **Specific Resources** > *Attribute Type* **Service Instance** > name of the workspace that you created earlier > **Next**.
1. In **Service access**, select **Manager** > **Add** > **Assign**.
1. Click **API Keys** to toggle the screen to manage API keys for the service ID.
1. Click **Create**.
1. Enter the **Name** and **Description** for the key.
1. Click **Create**.

Copy the API key or download it to save it.

The key is available for 300 seconds.
After the 300 seconds, you won't be able to display or retrieve the key.
{: important}


