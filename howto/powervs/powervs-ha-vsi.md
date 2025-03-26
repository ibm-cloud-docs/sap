---
copyright:
  years: 2023, 2025
lastupdated: "2025-03-25"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Creating instances for a high availability cluster
{: #ha-vsi}

Use the following information and procedures to create the {{site.data.keyword.powerSys_notm}} instances that are required for a high availability cluster implementation.
{: shortdesc}

## Before you begin
{: #ha-vsi-begin}

Review the general requirements, product documentation, support articles, and SAP notes listed in [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

## Creating a workspace
{: #ha-vsi-create-workspace}

A workspace is an environment that acts as a folder for all {{site.data.keyword.powerSys_notm}} resources in a geographical region.
These resources include compute, network, and storage volumes.
Resources cannot be moved or shared between different workspaces.
Each workspace is bound to a single data center.

To create a workspace, follow the steps that are described in [Creating a {{site.data.keyword.powerSys_notm}} workspace](/docs/power-iaas?topic=power-iaas-creating-power-virtual-server#creating-service){: external}.

The created workspaces are listed under **Workspaces** on the left navigation pane of the {{site.data.keyword.powerSys_notm}} user interface.

### Creating private network subnets
{: #ha-vsi-create-subnets}

A virtual server instance is connected to the network and is assigned an IP address from the defined range of IP addresses.
It is recommended that you connect the cluster nodes to a private network rather than a public network.

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
The PER solution creates a direct connection to the IBM Cloud Multi Protocol Label Switching (MPLS) backbone, making it easier for different parts of the IBM network to communicate with each other.
For more information, see [Getting started with the Power Edge Router](/docs/power-iaas?topic=power-iaas-per).

Otherwise, create {{site.data.keyword.cloud}} connections to connect your {{site.data.keyword.powerSys_notm}} instances to other {{site.data.keyword.cloud_notm}} resources within your account.
{{site.data.keyword.cloud_notm}} connections are not required to configure a high availability cluster in {{site.data.keyword.powerSys_notm}}.
They might be required for integration scenarios with the {{site.data.keyword.cloud_notm}} Classic network and Virtual Private Cloud (VPC) infrastructures.
For more information, see [IBM {{site.data.keyword.powerSys_notm}} Cloud Connections](/docs/power-iaas?topic=power-iaas-cloud-connections){: external}.

Use IBM Transit Gateway to connect your {{site.data.keyword.powerSys_notm}} to {{site.data.keyword.cloud_notm}} classic and Virtual Private Cloud (VPC) infrastructures outside your account or region.
For more information about integrating the on-premises network and {{site.data.keyword.powerSys_notm}}, see [Network architecture diagrams](/docs/power-iaas?topic=power-iaas-network-architecture-diagrams){: external}.

### Creating an SSH key
{: #ha-vsi-create-ssh-key}

Use the following steps to create one or more SSH keys for root login.

Create a keypair and load the public key to the SSH keys store in {{site.data.keyword.powerSys_notm}}.
During deployment of the virtual server instance, specify one or more keys from the keystore.
These keys are added to the `authorized_keys` file of the root user, and allow you to securely log in to the virtual server instance by using your private key.


For more information, see [Generating an SSH key](/docs/power-iaas?topic=power-iaas-create-vm#ssh-setup){: external}.

The preferred choice is the *ed25519* key type.
It offers both security and performance advantages.
{: tip}

1. Log in to [Workspaces](https://cloud.ibm.com/power/ssh-keys){: external}.
1. Click the **workspace** name and **View virtual servers**.
1. Click [SSH keys](https://cloud.ibm.com/power/ssh-keys){: external}.
1. Click **Create SSH key**.
1. Enter a **Key name**. Then, copy and paste the **public key** that you generated earlier into the field.
1. Click **Add SSH key**.

### Selecting a boot image
{: #ha-vsi-select-boot-image}

You have several options to obtain operating system images for the cluster nodes.
Use the following steps to select a boot image.

You can choose from several types of stock images that are already prepared for {{site.data.keyword.powerSys_notm}}.
Images are available in the *IBM Provided Subscription* and *Client Provided Subscription* sections of the {{site.data.keyword.powerSys_notm}} provisioning page.
For more information, see [Full Linux® subscription for IBM {{site.data.keyword.powerSys_notm}} (Off-premises)](/docs/power-iaas?topic=power-iaas-set-full-Linux){: external}.

If you want to import a custom Linux image, you must first upload the image to the {{site.data.keyword.cloud_notm}} Object Storage in OVA format.

- [Importing a boot image](/docs/power-iaas?topic=power-iaas-importing-boot-image){: external}
- [Deploying a custom image within IBM {{site.data.keyword.powerSys_notm}}](/docs/power-iaas?topic=power-iaas-deploy-custom-image){: external}

Before you begin, make sure that the OVA image is loaded in the storage bucket.

## Creating virtual server instances for the cluster
{: #ha-vsi-create-virtual-server-instances}

Complete the following steps to create the virtual server instances that you want to use as high availability cluster nodes.

1. Log in to [Workspaces](https://cloud.ibm.com/power/workspaces){: external}.
1. Click the **workspace** name and **View virtual servers**.
1. Click **Virtual server instances** > **Create Instance**.
   You need to step through the subsections **General**, **Boot Image**, **Profile**, **Storage Volume**, **Network Interfaces**.
1. In subsection **General**, enter the **Instance name**.
1. For a singlezone implementation, click **+** to increase the **Number of instances** to 2.
   Select **Numerical postfix** as *Instance naming convention*, and select **Different server** as *Placement group colocation policy*.
   A placement group with colocation policy *Different server* is automatically created as part of the virtual server instances deployment.
1. Select an **SSH key** and click **Continue**.
1. In the **Boot image** section, select the **Operating system** according to your subscription model.
   Use one of the Linux selections either from the *IBM-provided subscription* or through your *Client-provided subscription*.
   In the **Tier* section, select the desired storage tier.
   Keep **Auto-select pool** for selecting the *Storage Pool*.
   Click **Continue**.
1. In **Profile**, select **Machine type**, **Core type**, and the virtual server instance **profile** to match your workload requirements.
   Click **Continue**.
1. In **Storage volumes**, click **Continue**.

   When you deploy multiple instances, the storage volumes that are created are shared by all instances.
   Certain high availability cluster scenarios require shared volumes.
   In these cases, create the shared volumes later.
   For SAP HANA, see [Storage configuration for SAP HANA](/docs/sap?topic=sap-storage-design-considerations#storage-config-hana){: external}.
   These volumes must be created later for the individual server instances after their deployment is complete.
   {: important}

8. In the **Network Interfaces** subsection, it is preferable that the cluster nodes are not directly accessible from a public network, so leave the *Public networks* configuration as **Off**.
9. Click **Attach** to attach the virtual server instances to an existing subnet.
10. In the *Attach an existing network* screen, select one of the *Existing networks*. You can either select **Automatically assign IP address from IP range**, or **Manually specify an IP address from IP range** to specify an available IP address.
11. Click **Attach.**
12. Click **Finish**, check the *I agree to the Terms and Conditions* flag, and click **Create**.

The deployment of the virtual server instances starts.

For a multizone region deployment, repeat the same steps to create the second virtual server instance in a different workspace.

## Preparing the operating system for installating an SAP solution
{: #ha-vsi-prepare-sap-install}

If you deployed a virtual server instance from a stock image, you need to perform extra configuration tasks before you can install SAP software.
For more information, see [Configuring a {{site.data.keyword.powerSys_notm}} instance](/docs/sap?topic=sap-powervs-set-up-power-instances#powervs-set-up-powervs-manually-os-config).

## Creating a Custom Role, Service ID, and API key in {{site.data.keyword.cloud_notm}}
{: #ha-vsi-create-role-and-service-id}

A *Service ID* in {{site.data.keyword.cloud_notm}} identifies a service or an application in a similar way as a user ID identifies a user.
Create a *service ID* for the fencing agent to allow access to IBM Power Cloud actions such as monitoring or controlling the virtual server instances.
Create a custom role in advance to limit the allowed IBM Power Cloud API actions to only those actions that are required for fencing.

Managing *Custom Roles*, *Service IDs*, and *API keys* are part of IBM Cloud Identity and Access Management (IAM).
Navigate to the IAM for the following steps.

### Log in to IBM Cloud Identity and Access Management
{: #ha-vsi-create-service-id-logon}

Go to the IBM Cloud Identity and Access Management (IAM) console.
1. Log on to [{{site.data.keyword.cloud_notm}}](https://cloud.ibm.com/){: external}.
1. On the menu bar, click **Manage** and select **Access (IAM)**.

### Creating a custom role for the fencing agent
{: #ha-vsi-create-service-id-fencing-role}

Create a *custom role* in IAM and assign the set of actions that are required for a fencing operation to the role.
You must grant access for the following actions.
- reading objects in the *cloud_instance* or *workspace*
- listing virtual server instances
- getting information about a virtual server instance
- performing an action on a virtual server instance

The action set of a custom role must be unique within the account.
You cannot create multiple custom roles with the same action set.
{: note}

Create a **custom role** in IAM.
1. Click **Roles** > **Create**.
1. Enter the **Name**, **ID**, and **Description** for the custom role.
1. Select *Workspace for {{site.data.keyword.powerSys_notm}}* from the **Service** drop-down list.
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

Create a *custom role* in IAM and assign the set of actions that are required for a `subnet move` operation in the role.
You must grant access for the following actions.
- reading objects in the *cloud_instance* or *workspace*
- listing and getting information for subnets in the *workspace*
- creating and deleting subnets in the *workspace*
- attaching and detaching subnets to or from a virtual server instance
- deleting network ports

Create a **custom role** in IAM.
1. Click **Roles** > **Create**.
1. Enter the **Name**, **ID**, and **Description** for the custom role.
1. Select *Workspace for {{site.data.keyword.powerSys_notm}}* in the **Service** drop-down list.
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

### Creating a Service ID
{: #ha-vsi-create-service-id}

Create a *Service ID* for the fencing agent and assign one or more custom roles to it.
In a multizone region implementation, you can create a second *Service ID* for the `powervs-subnet` resource agent.
It is also possible to use a common Service ID for both agents (see the note in the previous section).
If you are using a common Service ID, assign both the custom role for fencing and the custom role for the `powervs-subnet` resource agent.

Create a **Service ID** in IAM.
1. Click **Service IDs** > **Create**.
1. Enter a **Name** and **Description** for the service ID.
1. Click **Create**.
1. In the *Access policies* section, click **Assign access**.
1. In the  *Service* section, select **Workspace for {{site.data.keyword.powerSys_notm}}** and click **Next**.
1. In the *Resource* section, select **Specific Resources** > **Service Instance** > **string equals** > *name of the workspace that you created earlier*.
   Click **Next**.
1. In the *Roles and actions* section, select one or more of the custom roles that you created earlier in **Custom access** and click **next**.
1. You can skip the *Conditions (Optional)* section.
1. Click **Add** and then **Assign** to create the *Service ID*.

If you create a *Service ID* for the `powervs-subnet` resource agent in a multizone region implementation, you must grant access to both workspace resources.
In the *Access policies* section, click **Assign access**  again and follow the steps to assign access for the second workspace.
{: important}

### Creating an API key for the Service ID
{: #ha-vsi-create-service-api-key}

When you configure the *fencing agent* in a high availability cluster or the *powervs-subnet* resource agent in a multizone region implementation, you must specify an *API key*.
The *API key* authorizes the fencing agent or resource agent to use the IBM Power Cloud API to perform the actions that are defined in the *Service ID*.

Create the **API Key** for the **Service ID** in the IAM.

1. Click **Service IDs** and select the *Service ID* that you created earlier.
2. Click **API Keys** to switch to the *Create and manage API keys for this service ID* tab.
3. Click **Create**.
4. Enter a **Name** and a **Description** for the key.
5. Click **Create**.

Click download to save the API key to a JSON file.
Keep the downloaded file in a safe place.

The key is available for 300 seconds.
After 300 seconds, you won't be able to view or retrieve the key.
{: important}

## Collecting parameters for configuring a high availability cluster
{: #ha-rhel-collect-parameters-for-cluster-config}

Several parameters are required to set up a specific high availability scenario.
These include the following parameters, which can be collected now.

- *Cloud Resource Name (CRN)* of the {{site.data.keyword.powerSys_notm}} workspace
- Virtual server *instance IDs*
- Extra parameters that must be derived from the *CRN*
- API key for the *fencing agent*
- API key for the *powervs-subnet* resource agent if you are implementing a multizone region environment

The uppercase variables in the following section indicate that these parameters are used as environment variables to simplify the cluster setup.
Make a note of their contents now, as they will be needed in the setup instructions for a specific high availability scenario.

1. `CLOUD_REGION` contains the geographical area of your virtual server instance and is used to target the correct [Power Cloud API endpoint](https://cloud.ibm.com/apidocs/power-cloud#endpoint){: external}.

   `CLOUD_REGION` if you are using public endpoints
   :   Public endpoint URLs match the pattern `https://<CLOUD_REGION>.power-iaas.cloud.ibm.com`.
       For `CLOUD_REGION`, note the first word in the hostname in the public endpoint URL of the specific location.
       For example, sites *syd04* and *syd05* map to *syd*.

   `CLOUD_REGION` if you are using private endpoints
   :   Private endpoint URLs match the pattern `https://private.<CLOUD_REGION>.power-iaas.cloud.ibm.com`.
       For `CLOUD_REGION`, note the second word in the hostname in the private endpoint URL of the specific location.
       For example, sites *syd04* and *syd05* map to *au-syd*.

1. Log in to [Workspaces - {{site.data.keyword.powerSys_notm}}](https://cloud.ibm.com/power/workspaces){: external}.
   The list contains the name and CRN of the workspaces.

   Locate your **Workspace**, or both workspaces for a multizone region deployment.
   Click **Copy** next to the CRN and paste it into a temporary document.

   A CRN has multiple sections that are divided by a colon.
   The base format of a CRN is:

   `crn:version:cname:ctype:service-name:location:scope:service-instance:resource-type:resource`

   service-name
   :   The fifth field of the CRN of the workspace is always *power-iaas*, the **service-name**.

   location
   :   The sixth field is the **location** that needs to be mapped to a region.

   scope
   :   The seventh field is the **Tenant ID**.

   service-instance
   :   The eighth field is the **Cloud Instance ID** or **GUID**.

1. `IBMCLOUD_CRN_1` contains the full *CRN*.
1. `GUID_1` refers to the contents of the *service-instance* field in the *CRN*.
1. In a multizone region deployment, use the *CRN* of the second workspace and note the contents for `IBM_CLOUD_CRN_2` and `GUID_2`.
1. Click the workspace name and then **View virtual servers**.
   Click the virtual server instance names and find their **ID**.
1. Note these IDs for `POWERVSI_1` and `POWERVSI_2`.
   In a multizone deployment, use the second workspace to find the ID of the second instance.
1. `APIKEY` contains the API key for the fencing agent. Use the value of the `apikey` entry in the JSON file that was downloaded in the [Creating an API key for the Service ID](/docs/sap?topic=sap-ha-vsi#ha-vsi-create-service-api-key) section.

   In a multizone region deployment, an API key is also required for the `powervs-subnet` cluster resource agent.
   As before, you can use the value of the apikey entry for the `APIKEY` variable.
   However, the preferred option is to place a copy of the downloaded JSON file on both nodes and set `APIKEY` to a string that starts with a `@` sign followed by the full path to the key file.
