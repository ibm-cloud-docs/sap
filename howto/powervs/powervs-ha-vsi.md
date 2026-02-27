---
copyright:
  years: 2023, 2026
lastupdated: "2026-02-27"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn, SLES HAE
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Preparing the {{site.data.keyword.cloud_notm}} environment for a high availability cluster
{: #ha-vsi}

Use the following information to prepare the {{site.data.keyword.cloud_notm}} environment for implementing a high availability cluster in {{site.data.keyword.powerSys_notm}}.
{: shortdesc}

## Before you begin
{: #ha-vsi-begin}

Review the general requirements, product documentation, support articles, and SAP notes for your operating system:

- Red Hat Enterprise Linux (RHEL) [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).
- SUSE Linux Enterprise Server (SLES) [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} SLES References](/docs/sap?topic=sap-ha-sles-refs).

## Configuring the {{site.data.keyword.powerSys_notm}} workspace
{: #ha-vsi-workspace}

Create a workspace, define the required subnets, create an SSH key, and prepare a boot image.


1. Create a workspace.

    To create a workspace, follow the instructions in [Deploying a {{site.data.keyword.powerSys_notm}} workspace](/docs/sap?topic=sap-powervs-set-up-power-service).
    After the workspace is created, it appears under **Workspaces** in the left navigation panel of the {{site.data.keyword.powerSys_notm}} user interface.
1. Create network subnets in the workspace.

    Virtual server instances must be connected to a network, and their IP addresses are assigned from the configured subnet range.
    For cluster deployments, use a **private** subnet rather than a public subnet for all nodes.
    Follow the steps in [Creating network subnets](/docs/sap?topic=sap-powervs-set-up-power-subnets).
1. Create an SSH key.

    For details, see [Creating an SSH key](/docs/sap?topic=sap-powervs-set-up-power-ssh-key).
1. Select a boot image.

    You can choose from multiple operating system images for the cluster nodes.
    Images are available in the *IBM Provided Subscription* and *Client Provided Subscription* sections of the {{site.data.keyword.powerSys_notm}} provisioning page.
    For more information, see [Full Linux® subscription for IBM {{site.data.keyword.powerSys_notm}} (Off-premises)](/docs/power-iaas?topic=power-iaas-set-full-Linux).
    If you want to import a custom Linux image, you must first upload the image to {{site.data.keyword.cos_full}} in OVA format.
    - [Importing a boot image](/docs/power-iaas?topic=power-iaas-importing-boot-image)
    - [Deploying a custom image within IBM {{site.data.keyword.powerSys_notm}}](/docs/power-iaas?topic=power-iaas-deploy-custom-image).

    Make sure that the OVA image is available in the storage bucket before you start provisioning virtual server instances.

## Configuring {{site.data.keyword.iamlong}} for cluster agents on {{site.data.keyword.powerSys_notm}}
{: #ha-vsi-configure-iam}

In a high-availability cluster, resource agents manage the start, stop, and monitoring of cluster resources.
Fencing agents run node-level power actions, such as stopping or restarting a node, to maintain cluster integrity and prevent data corruption.

When the cluster runs on {{site.data.keyword.powerSys_notm}} instances, the agents interact with the IBM Power Cloud API to retrieve status information and trigger required actions.
Configure {{site.data.keyword.iamshort}} (IAM) to authenticate and authorize the IBM Power Cloud API calls.

Create a custom role that restricts the actions that the agents can trigger to the minimum required set.
A *service ID* in {{site.data.keyword.cloud_notm}} identifies a service or application, similar to how a user ID identifies a user.
Create a *service ID* for the agents to allow access to IBM Power Cloud actions such as monitoring or controlling virtual server instances.

### Verifying access to {{site.data.keyword.iamshort}}
{: #ha-vsi-iam-logon}

Verify that you have administrative access to manage IAM roles and policies for the {{site.data.keyword.powerSys_notm}} workspace.

1. Log in to [{{site.data.keyword.cloud_notm}}](https://cloud.ibm.com/){: external}.
1. In the menu bar, click **Manage** > **Access (IAM)** > **Users**, and then click your user name.
1. Open the **Access** tab.
    - Verify that the necessary policies are granted either through an access group or through an access policy.
    - Search for the service access policy *All Identity and Access enabled service*, and confirm that you are assigned the roles *Administrator* and *Manager*.
    - Search for the policy *All Account Management Services*, and confirm that you are assigned the *Administrator* role.

### Creating custom IAM roles
{: #ha-vsi-create-custom-roles}

Create custom roles in IAM that include all permissions that are required for running the resource and fencing agents.
These permissions must cover all operational actions of the agents within the {{site.data.keyword.powerSys_notm}} workspace.

The action set of a custom role must be unique within the account.
Multiple custom roles cannot share one common action set.
{: note}

#### Creating a custom role for the fencing agent
{: #ha-vsi-create-custom-role-fencing}

Create a *custom role* in IAM and assign the set of actions that are required for a fencing operation to the role.
Grant access for the following actions.
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
Grant access for the following actions.
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
Grant access for the following actions.
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

1. `IBMCLOUD_CRN` and `GUID`.
   The Cloud Resource Name (CRN) is a globally unique identifier for the workspace.
   The Globally Unique Identifier (GUID) is part of it, and is specific for your account or organization.

   Log in to [Workspaces](https://cloud.ibm.com/power/workspaces){: external}.
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

   `IBMCLOUD_CRN_1` contains the full *CRN*.
   `GUID_1` refers to the contents of the *service-instance* field in the *CRN*.
   In a multizone region deployment, use the *CRN* of the second workspace and note the contents for `IBM_CLOUD_CRN_2` and `GUID_2`.

1. `POWERVSI`, the IDs of the virtual server instances
   Click the workspace name and then **View virtual servers**.
   Click the virtual server instance names and find their **ID**.
   Note these IDs for `POWERVSI_1` and `POWERVSI_2`.
   In a multizone deployment, use the second workspace to find the ID of the second instance.

1. `ROUTE_CRN`, the CRNs of the static routes (`powervs-move-ip` resource agent only).

   Click *Routes* in the workspace menu.
   Select the static route name that matches the destination of the virtual IP address.
   Click *View CRN* and record the **CRN** as `ROUTE_CRN_1` or `ROUTE_CRN_2`.
   In a multizone deployment, use the second workspace to obtain the CRN for the second route.

1. `APIKEY`, the API key for the fencing agent.
   Use the value of the `apikey` entry from the JSON file that was downloaded in the [Creating an API key for the Service ID](/docs/sap?topic=sap-ha-vsi#ha-vsi-create-service-api-key) section.

   In a multizone region deployment, an API key is also required for the `powervs-move-ip` or `powervs-subnet` cluster resource agent.
   As with the fencing agent, you can use the value of the `apikey` entry for the `APIKEY`.
   However, the preferred approach is to place a copy of the downloaded JSON file on both nodes and set `APIKEY` to a value that starts with an `@` symbol followed by the full path to the key file.

## Creating virtual server instances
{: #ha-vsi-create-virtual-server-instances}

After preparing the workspace, subnets, SSH key, and boot image, proceed with creating the virtual server instances that will function as nodes in the high‑availability cluster.
Follow the steps for provisioning virtual server instances in either [Deploying an {{site.data.keyword.powerSys_notm}} instance for the SAP HANA system](/docs/sap?topic=sap-powervs-set-up-power-instances#powervs-set-up-power-hana-instance) or [Deploying an {{site.data.keyword.powerSys_notm}} instance for SAP NetWeaver](/docs/sap?topic=sap-powervs-set-up-power-instances#powervs-set-up-power-netweaver-instance).

## Reserving virtual IP addresses
{: #ha-vsi-reserve-virtual-ip-addresses}

A high availability cluster requires *virtual IP addresses* that can migrate between nodes during a failover event.
The procedure for reserving these addresses depends on whether the cluster is deployed in a single workspace or across multiple workspaces.

- Single workspace deployment

    For clusters that are deployed within a single workspace, reserve the required *virtual IP addresses* by following the instructions in [Reserving IP addresses](/docs/power-iaas?topic=power-iaas-configuring-subnet#reserv-ip).
    This prevents {{site.data.keyword.powerSys_notm}} from assigning those addresses to other virtual server instances.

    The reserved IP address must fall within the subnet’s CIDR range and the previously restricted IP range.
    {: note}

- Multizone deployment across two workspaces

    Clusters than span two workspaces require extra IP address planning:
    - *Virtual IP addresses* must be allocated outside the CIDR ranges of all subnets in both workspaces.
    - If the `powervs-move-ip` resource agent is used, reserve a single IP address outside all subnet CIDR ranges with a `/32` netmask.
    - If the `powervs-move-subnet` resource agent is used, reserve a subnet outside all subnet CIDR ranges with a `/30` netmask.

   {{site.data.keyword.powerSys_notm}} does not manage these addresses.
   You are responsible for maintaining accurate records of the reserved allocations.
   {: note}
