---
copyright:
  years: 2023, 2026
lastupdated: "2026-02-13"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn, SLES HAE
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Creating instances for a high availability cluster
{: #ha-vsi}

Use the following information and procedures to create the {{site.data.keyword.powerSys_notm}} instances that are required for a high availability cluster implementation.
{: shortdesc}

## Before you begin
{: #ha-vsi-begin}

Review the general requirements, product documentation, support articles, and SAP notes for your operating system:
- Red Hat Enterprise Linux (RHEL) [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs)
- SUSE Linux Enterprise Server (SLES) [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} SLES References](/docs/sap?topic=sap-ha-sles-refs)

## Creating a workspace
{: #ha-vsi-create-workspace}

A workspace is an environment that acts as a folder for all {{site.data.keyword.powerSys_notm}} resources in a geographical region.
These resources include compute, network, and storage volumes.
Resources cannot be moved or shared between different workspaces.
Each workspace is bound to a single data center.

To create a workspace, follow the steps that are described in [Creating a {{site.data.keyword.powerSys_notm}} workspace](/docs/power-iaas?topic=power-iaas-creating-power-virtual-server#creating-service).

The created workspaces are listed under **Workspaces** on the left navigation menu of the {{site.data.keyword.powerSys_notm}} user interface.

### Creating private network subnets
{: #ha-vsi-create-subnets}

A virtual server instance is connected to the network and an IP address is assigned from the defined range of IP addresses.
It is recommended that you connect the cluster nodes to a private network rather than a public network.

Follow the steps in [Configuring a private network subnet](/docs/power-iaas?topic=power-iaas-configuring-subnet) to create a subnet.

You need at least one private subnet in the workspace.
{: note}

### Reserving virtual IP addresses
{: #ha-vsi-reserve-virtual-ip-addresses}

A high availability cluster requires *virtual IP addresses* that can migrate between nodes during a failover event.
The procedure for reserving these addresses depends on whether the cluster is deployed in a single workspace or across multiple workspaces.

Single workspace deployment
:   For clusters deployed within a single workspace, reserve the required *virtual IP addresses* by following the instructions in [Reserving IP addresses](/docs/power-iaas?topic=power-iaas-configuring-subnet#reserv-ip). This reservation ensures that {{site.data.keyword.powerSys_notm}} does not automatically allocate those addresses to other virtual server instances.

    The reserved IP address must fall within the subnet’s CIDR range and the previously restricted IP range.
    {: note}

Multizone deployment across two workspaces
:   Clusters spanning two workspaces require extra IP address planning:

    - *Virtual IP addresses* must be allocated outside the CIDR ranges of all subnets present in both workspaces.
    - If the `powervs-move-ip` resource agent is used, reserve a single IP address outside all subnet CIDR ranges with a `/32` netmask.
    - If the `powervs-move-subnet` resource agent is used, reserve a subnet outside all subnet CIDR ranges with a `/30` netmask.

    {{site.data.keyword.powerSys_notm}} does not manage these addresses. You are responsible for maintaining accurate records of the reserved allocations.
    {: note}

### Exploring more network architecture options
{: #ha-vsi-explore-additional-network-options}

If your {{site.data.keyword.powerSys_notm}} *workspace* is enabled for *Power Edge Router* (PER), you already have network communication with parts of the IBM Cloud network.
The PER solution creates a direct connection to the IBM Cloud backbone by using Multi-Protocol Label Switching (MPLS).
This configuration enables more efficient communication between different parts of the IBM network.
For more information, see [Getting started with the Power Edge Router](/docs/power-iaas?topic=power-iaas-per).

Otherwise, create {{site.data.keyword.cloud}} connections to connect your {{site.data.keyword.powerSys_notm}} instances to other {{site.data.keyword.cloud_notm}} resources within your account.
{{site.data.keyword.cloud_notm}} connections are not required to configure a high availability cluster in {{site.data.keyword.powerSys_notm}}.
They might be required for integration scenarios with the {{site.data.keyword.cloud_notm}} Classic network and Virtual Private Cloud (VPC) infrastructures.
For more information, see [IBM {{site.data.keyword.powerSys_notm}} Cloud Connections](/docs/power-iaas?topic=power-iaas-cloud-connections).

Use IBM Transit Gateway to connect your {{site.data.keyword.powerSys_notm}} to {{site.data.keyword.cloud_notm}} classic and Virtual Private Cloud (VPC) infrastructures outside your account or region.
For more information about integrating the on-premises network and {{site.data.keyword.powerSys_notm}}, see [Network architecture diagrams](/docs/power-iaas?topic=power-iaas-network-architecture-diagrams).

### Creating an SSH key
{: #ha-vsi-create-ssh-key}

Use the following steps to create one or more SSH keys for root login.

Create a keypair and load the public key to the SSH keys store in {{site.data.keyword.powerSys_notm}}.
During deployment of the virtual server instance, specify one or more keys from the keystore.
These keys are added to the `authorized_keys` file of the root user, and allow you to securely log in to the virtual server instance by using your private key.


For more information, see [Generating an SSH key](/docs/power-iaas?topic=power-iaas-create-vm#ssh-setup).

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
For more information, see [Full Linux® subscription for IBM {{site.data.keyword.powerSys_notm}} (Off-premises)](/docs/power-iaas?topic=power-iaas-set-full-Linux).

If you want to import a custom Linux image, you must first upload the image to the {{site.data.keyword.cloud_notm}} Object Storage in OVA format.

- [Importing a boot image](/docs/power-iaas?topic=power-iaas-importing-boot-image)
- [Deploying a custom image within IBM {{site.data.keyword.powerSys_notm}}](/docs/power-iaas?topic=power-iaas-deploy-custom-image)

Before you begin, make sure that the OVA image is loaded in the storage bucket.

## Creating virtual server instances for the cluster
{: #ha-vsi-create-virtual-server-instances}

Follow these steps to create the virtual server instances that function as nodes in your high availability cluster.

1. Log in to [Workspaces](https://cloud.ibm.com/power/workspaces){: external}.
1. Select the **workspace**, then click **View virtual servers**.
1. Click **Virtual server instances** > **Create Instance**.
   - You are guided through the following configuration steps: **General**, **Boot Image**, **Profile**, **Storage Volume**, and **Network Interfaces**.
1. In the **General** section:
   - Enter the **Instance name**.
   - Set the **Virtual server pinning** policy to **soft**.
     Use the **soft** pinning policy to keep the virtual server on the same host.
     The **soft** pinning policy ensures that the virtual server remains on the same host, preventing automatic relocation during cloud maintenance.

   Red Hat and SUSE do not support issues that occur when a cluster node is moved to a different server that uses Live Partition Mobility (LPM).
   Customers are advised to avoid using LPM for active cluster nodes, as this operation might lead to unpredictable behavior or unsupported configurations.
   {: important}

1. For a single zone implementation, click **+** to increase the **Number of instances** to 2.
   Select **Numerical postfix** as *Instance naming convention*, and select **Different server** as *Placement group colocation policy*.
   A placement group with colocation policy *Different server* is automatically created as part of the virtual server instances deployment.
1. Select an **SSH key** and click **Continue**.
1. In the **Boot image** section, select the **Operating system** according to your subscription model.
   Use one of the Linux selections either from the *IBM-provided subscription* or through your *Client-provided subscription*.
   In the **Tier* section, select the storage tier.
   Keep **Auto-select pool** for selecting the *Storage Pool*.
   Click **Continue**.
1. In **Profile**, select **Machine type**, **Core type**, and the virtual server instance **profile** to match your workload requirements.
   Click **Continue**.
1. In **Storage volumes**, click **Continue**.

   When you deploy multiple instances, the storage volumes that are created are shared by all instances.
   Certain high availability cluster scenarios require shared volumes.
   In these cases, create the shared volumes later.
   For SAP HANA, see [Storage configuration for SAP HANA](/docs/sap?topic=sap-storage-design-considerations#storage-config-hana).
   These volumes must be created later for the individual server instances after their deployment is complete.
   {: important}

1. In the **Network Interfaces** section, it is preferable that the cluster nodes are not directly accessible from a public network.
   Keep the *Public networks* configuration set to **Off**.
1. Click **Attach** to attach the virtual server instances to an existing subnet.
1. In the *Attach an existing network* screen, select one of the *existing networks*.
   You can either select **Automatically assign IP address from IP range**, or **Manually specify an IP address from IP range** to specify an available IP address.
1. Click **Attach.**
1. Click **Finish**, check the *I agree to the Terms and Conditions* flag, and click **Create**.

The deployment of the virtual server instances starts.

For a multizone region deployment, repeat the same steps to create the second virtual server instance in a different workspace.

## Preparing the operating system for installing an SAP solution
{: #ha-vsi-prepare-sap-install}

If you deployed a virtual server instance from a stock image, you need to perform extra configuration tasks before you can install SAP software.
For more information, see [Configuring a {{site.data.keyword.powerSys_notm}} instance](/docs/sap?topic=sap-powervs-set-up-power-instances#powervs-set-up-powervs-manually-os-config).

## Configuring Identity and Access Management for cluster agents on {{site.data.keyword.powerSys_notm}}
{: #ha-vsi-configure-iam}


In a high-availability cluster, resource agents manage the start, stop, and monitoring of cluster resources.
Fencing agents perform node-level power actions, such as stopping or restarting a node, to maintain cluster integrity and prevent data corruption.

When the cluster runs on {{site.data.keyword.powerSys_notm}} instances, the agents interact with the IBM Power Cloud API to retrieve status information and trigger required actions.
Configure Identity and Access Management (IAM) to authenticate and authorize the IBM Power Cloud API calls.

Create a custom role that restricts the actions that the agents can trigger to the minimum required set.
A *Service ID* in {{site.data.keyword.cloud_notm}} identifies a service or application, similar to how a user ID identifies a user.
Create a *service ID* for the agents to allow access to IBM Power Cloud actions such as monitoring or controlling virtual server instances.

### Log in to IBM Cloud Identity and Access Management
{: #ha-vsi-iam-logon}

Verify that you have administrative access to manage IAM roles and policies for the {{site.data.keyword.powerSys_notm}} workspace.

Go to the IBM Cloud Identity and Access Management (IAM) console.
1. Log on to [{{site.data.keyword.cloud_notm}}](https://cloud.ibm.com/){: external}.
1. On the menu bar, click **Manage** and select **Access (IAM)**.

### Creating custom IAM roles
{: #ha-vsi-create-custom-roles}

Create custom roles in IAM that include all permissions that are required by the resource and fencing agents.
These permissions must cover all operational actions that are performed by the agents within the {{site.data.keyword.powerSys_notm}} workspace.

The action set of a custom role must be unique within the account.
Multiple custom roles cannot share the same action set.
{: note}

#### Creating a custom role for the fencing agent
{: #ha-vsi-create-custom-role-fencing}

Create a *custom role* in IAM and assign the set of actions that are required for a fencing operation to the role.
You must grant access for the following actions.
- Reading objects in the *cloud_instance* or *workspace*
- Listing virtual server instances
- Getting information about a virtual server instance
- Performing an action on a virtual server instance

The action set of a custom role must be unique within the account.
You cannot create multiple custom roles with the same action set.
{: note}

Create a **custom role** in IAM.
1. Click **Roles** > **Create**.
1. Enter the **Name**, **ID**, and **Description** for the custom role.
1. Select *{{site.data.keyword.powerSys_notm}} Workspace* from the **Service** list.
1. Select *Manager* from the **View the actions for** list.
1. In the **Actions** list, locate the following actions.
   Click **Add** for each of them.
   - power-iaas.pvm-instance.list
   - power-iaas.pvm-instance.read
   - power-iaas.pvm-instance.action

2. Click **Create** to save the role.

#### Creating a custom role for the `powervs-subnet` resource agent
{: #ha-vsi-create-custom-role-subnet}

This step is only required if you are implementing a cluster in a multizone region environment with the `powervs-subnet` resource agent.
{: note}

Create a *custom role* in IAM and assign the set of actions that are required for a `subnet move` operation in the role.
You must grant access for the following actions.
- Reading objects in the *cloud_instance* or *workspace*
- Listing and getting information for subnets in the *workspace*
- Creating and deleting subnets in the *workspace*
- Attaching and detaching subnets to or from a virtual server instance
- Deleting network ports

Create a **custom role** in IAM.
1. Click **Roles** > **Create**.
1. Enter the **Name**, **ID**, and **Description** for the custom role.
1. Select *{{site.data.keyword.powerSys_notm}} Workspace* in the **Service** list.
1. Select *Manager* from the **View the actions for** list.
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

#### Creating a custom role for the `powervs-move-ip` resource agent
{: #ha-vsi-create-custom-role-move-ip}

This step is only required if you are implementing a cluster in a multizone region environment with the `powervs-move-ip` resource agent.
{: note}

Create a *custom role* in IAM and assign the set of actions that are required for static route operations.
You must grant access for the following actions.
- Reading objects in the *cloud_instance* or *workspace*
- Modifying objects in the *cloud_instance* or *workspace*

Create a **custom role** in IAM.
1. Click **Roles** > **Create**.
1. Enter the **Name**, **ID**, and **Description** for the custom role.
1. Select *{{site.data.keyword.powerSys_notm}} Workspace* in the **Service** list.
1. Select *Manager* from the **View the actions for** list.
1. In the **Actions** list, locate the following actions.
   Click **Add** for each of them.
   - power-iaas.cloud-instance.read
   - power-iaas.cloud-instance.modify
1. Click **Create** to save the role.

### Creating a Service ID and an API key
{: #ha-vsi-create-service-id-api-key}

Create a `Service ID` and an `API key` for the agents.

#### Creating a Service ID
{: #ha-vsi-create-service-id}

Create a *Service ID* for the agent and assign one or more custom roles to it.


If you configure the `fence_ibm_powervs` fence agent and either the `powervs-move-ip` or `powervs-subnet` resource agent in the cluster, you can create separate *Service IDs* for each agent or use a single shared *Service ID*.
If you are using a common *Service ID*, assign both the custom role for fencing and the custom role for the `powervs-move-ip` or `powervs-subnet` resource agent.

Create a **Service ID** in IAM.
1. Click **Service IDs** > **Create**.
1. Enter a **Name** and **Description** for the service ID.
1. Click **Create**.
1. In the *Access policies* section, click **Assign access**.
1. In the *Service* section, select **Workspace for {{site.data.keyword.powerSys_notm}}** and click **Next**.
1. In the *Resource* section, select **Specific Resources** > **Service Instance** > **string equals** > *name of the first workspace that you created earlier*.
   Click **Next**.
1. In the *Roles and actions* section, select one or more of the custom roles that you created earlier in **Custom access** and click **next**.
1. You can skip the *Conditions (Optional)* section.
1. Click **Add** and then **Assign** to create the *Service ID*.

If you create a *Service ID* for the `powervs-move-ip` or `powervs-subnet` resource agent in a multizone region implementation, you must grant access to both workspace resources.
In the *Access policies* section, click **Assign access** and repeat the steps to assign access also for the second workspace.
{: important}

#### Creating an API key for the Service ID
{: #ha-vsi-create-service-api-key}

When you configure an agent in a high availability cluster as a fencing device or cluster resource, you must specify an *API key*.
The *API key* authorizes the fencing or resource agent to use the IBM Power Cloud API to perform the actions that are defined in the *Service ID*.

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


Configuring a high availability environment requires a defined set of parameters.
Collect the following parameters before proceeding.
Uppercase variable names in the following section indicate parameters that are used as environment variables to simplify cluster setup.
These values are required later in the setup instructions for a specific high availability scenario.

The following parameters are required for all scenarios.
   - `CLOUD_REGION`
   - `APIKEY`

If you use the `fence_ibm_powervs` fence agent or the `powervs-subnet` resource agent, also collect the following information:
   - `IBMCLOUD_CRN_1`, `IBMCLOUD_CRN_2`.
     The *Cloud Resource Name (CRN)* of the {{site.data.keyword.powerSys_notm}} workspace or workspaces.
   - `POWERVSI_1` and `POWERVSI_2`.
     The virtual server *instance IDs*.
   - `GUID_1`, `GUID_2`.
     The *GUID* values derived from the corresponding CRNs (required by `fence_ibm_powervs` only).

If you use the `powervs-move-ip` resource agent, also collect the CRN for each of the routes.
   - `ROUTE_CRN_1`, `ROUTE_CRN_2`

### Gathering parameters for cluster configuration
{: #ha-rhel-gather-parameters-for-cluster-config}

Gather the parameters, and note them for the cluster configuration.

1. `CLOUD_REGION` defines the geographical region of the virtual server instance and is used to determine the correct [Power Cloud API endpoint](https://cloud.ibm.com/apidocs/power-cloud#endpoint){: external}.

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

For the `powervs-move-ip` resource agent only:
1. Click *Routes* in the workspace menu.
   Select the static route name that matches the destination of the virtual IP address.
   Click *View CRN* and record the **CRN** as `ROUTE_CRN_1` or `ROUTE_CRN_2`.
   In a multizone deployment, use the second workspace to obtain the CRN for the second route.

1. `APIKEY` contains the API key for the fencing agent.
   Use the value of the `apikey` entry from the JSON file that was downloaded in the [Creating an API key for the Service ID](/docs/sap?topic=sap-ha-vsi#ha-vsi-create-service-api-key) section.

   In a multizone region deployment, an API key is also required for the `powervs-move-ip` or `powervs-subnet` cluster resource agent.
   As with the fencing agent, you can use the value of the `apikey` entry for the `APIKEY`.
   However, the preferred approach is to place a copy of the downloaded JSON file on both nodes and set `APIKEY` to a value that starts with an `@` symbol followed by the full path to the key file.
