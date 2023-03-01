---
copyright:
  years: 2023
lastupdated: "2023-03-01"

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Implement RHEL HA Add-On cluster on {{site.data.keyword.powerSysFull}}
{: #ha-rhel}

Running SAP on IBM {{site.data.keyword.powerSys_notm}} offers a consistent platform for HANA-based and traditional applications, best-in-class performance, resilience for critical workloads, and a flexible infrastructure.

Use the following information and procedures to help you implement a Red Hat High Availability (HA) cluster by using {{site.data.keyword.powerSys_notm}} instances as cluster nodes.

This information doesn't replace existing SAP or Red Hat documentation.

## Overview
{: #ha-rhel-overview}

You can find the following information in the proceeding sections.

- [Creating the {{site.data.keyword.powerSys_notm}} workspace in IBM Cloud](#create-workspace)
    After the workspace is created, you can create and configure virtual server instances, network resources, and storage volumes.
- [Creating a Service ID API Key in IBM Cloud](#create-service-id))
    For monitoring and management, the fencing agent authenticates to the {{site.data.keyword.powerSys_notm}} API by using the Service API key.
- [Setting up and configuring HA clusters](#install-and-configure-rhel-ha-cluster) transforms the individual virtual server instances in {{site.data.keyword.powerSys_notm}} into a cluster.
    These procedures include installing the high availability packages and agents on each cluster node and configuring the fencing devices.

Keep in mind that this information is intended for architects and specialists that are planning a high-availability deployment of SAP applications on {{site.data.keyword.powerSys_notm}}.

## Prerequisites
{: #ha-rhel-prereqs}

- A [Red Hat Customer Portal](https://access.redhat.com/){: external} account.
- An [IBM Cloud account](https://cloud.ibm.com/docs/account?topic=account-account-getting-started){: external}.
- [Setting up an enterprise account](https://cloud.ibm.com/docs/account?topic=account-enterprise-tutorial){: external} guides you creating an enterprise and managing your account.

## References
{: #ha-rhel-references}

Check the following Red Hat documentation and Knowledge Base articles (a Red Hat Customer portal ID is required to access Knowledge Base articles):

- [Support Policies for RHEL High Availability Clusters](https://access.redhat.com/articles/2912891){: external}
- [Support Policies for RHEL High Availability Clusters - General Requirements for Fencing/STONITH](https://access.redhat.com/articles/2881341){: external}
- [Support Policies for RHEL High Availability Clusters - IBM {{site.data.keyword.powerSys_notm}} (PowerVS) Virtual Machines as Cluster Members](https://access.redhat.com/articles/5651561){: external}
- [Red Hat Enterprise Linux 8 - Configuring and managing the Red Hat High Availability Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_and_managing_high_availability_clusters/index){: external}
- [Configuring a RHEL HA Cluster Fence Agent for an IBM {{site.data.keyword.powerSys_notm}}](https://access.redhat.com/articles/6966644){: external}

## Setting up a {{site.data.keyword.powerSys_notm}}
{: #ha-rhel-powervs-set-up}

Use the following information to set up a {{site.data.keyword.powerSys_notm}}.

### Creating the workspace
{: #ha-rhel-create-workspace}

A workspace is the environment that acts as folder for all the {{site.data.keyword.powerSys_notm}} resources in a specific geographic region.
These resources include computing, network, and storage volumes.
Resources cannot be moved or shared between different workspaces.
Each workspace is tied to a single data center.

The nodes of a Red Hat HA Add-On cluster are deployed in a single workspace.

Log in into [{{site.data.keyword.powerSys_notm}}](https://cloud.ibm.com/power/overview){: external} in IBM Cloud.

1. Select **Workspaces** in the navigation bar and press **Create** to open the **Create workspace** menu.
1. Enter the **Workspace name** and select a **Resource group** according to the intended assignment of the cluster resources.
1. Select the **Region** where you want the resources to deploy.
1. Add any **User tags** and **Access management tags** according to your policies.
1. Click **Create** to initiate the workspace.

### Creating subnets
{: #ha-rhel-create-subnets}

The virtual server instance is connected to the network and gets an IP address from the assigned IP range.

It is recommended that you connect the cluster nodes to a private network, not to a public network.

An extra bastion node can access both public and private networks and can be used to tunnel SSH connections to the cluster nodes.

Private subnets are created in context of the {{site.data.keyword.powerSys_notm}} workspace. When you connect different networks, you can use GRE Tunneling if a subnet range conflicts with existing classical infrastructure.

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

### Reserving virtual IP addresses
{: #ha-rhel-reserve-virtual-ip-addresses}

High availability cluster typically needs *virtual IP addresses* that must move with the application in a failover scenario.

Currently, {{site.data.keyword.powerSys_notm}} does not support reserving a floating IP address.
Follow this procedure to avoid that virtual IP addresses are erroneously used during other virtual server deployments in the same workspace:
- Go to the specific [Subnets](https://cloud.ibm.com/power/subnets) of your {{site.data.keyword.powerSys_notm}} *workspace*, and define a subset of the full CIDR as allowed *IP range*.
- Select one unused IP address within the CIDR range of the subnet, but outside of the *IP range* that you previously restricted.
- You need to manage the usage of these addresses on your own.
   Usage of *IBM Cloud DNS Services* might help for administration.

### Exploring more network architecture options
{: #ha-rhel-explore-additional-network-options}

You use an {{site.data.keyword.cloud}} connection to connect your {{site.data.keyword.powerSys_notm}} instances to other {{site.data.keyword.cloud}} resources within your account.
{{site.data.keyword.cloud}} connections are not required to configure a Red Hat High Availability cluster on {{site.data.keyword.powerSys_notm}}, but might be required for integration scenarios when you use {{site.data.keyword.cloud}} classic network and Virtual Private Cloud (VPC) infrastructures.
For more information, see [Managing IBM Cloud connections](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-cloud-connections){: external}.

Use IBM Transit Gateway to interconnect your {{site.data.keyword.powerSys_notm}} to {{site.data.keyword.cloud}} classic and Virtual Private Cloud (VPC) infrastructures outside of your account or region.
For more information about integrating on-premises network and {{site.data.keyword.powerSys_notm}}, see [Network architecture diagrams](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-network-architecture-diagrams){: external}.

### Creating an SSH key
{: #ha-rhel-create-ssh-key}

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

### Selecting a boot image
{: #decide-on-boot-image}

You have different options for obtaining RHEL images for your cluster.
Use the following steps to select a boot image.

With the *Full Linux Subscription*, you have access to stock images that are already prepared for {{site.data.keyword.powerSys_notm}}.
For more information, see [Full Linux® subscription for {{site.data.keyword.powerSys_notm}} instances](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-set-full-Linux){: external}.

If you want to import a custom Red Hat Enterprise Linux image, you need to first upload the image to IBM Cloud Object Storage in OVA format.

- [Deploying a custom image within a {{site.data.keyword.powerSys_notm}} workspace](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-deploy-custom-image){: external}, which outlines the necessary steps for
- [Creating an IBM Cloud Storage bucket](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-deploy-custom-image#cloud-storage-bucket){: external}
- [Generating secret and access keys](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-deploy-custom-image#access-keys){: external}

Before you begin, make sure that the OVA image is loaded in the storage bucket.

1. Log in to [Boot images in {{site.data.keyword.powerSys_notm}}](https://cloud.ibm.com/power/boot-images){: external}.
1. Select the **workspace** that you created.
1. Click **Import image**.
1. Enter **Custom image name** as image name in your catalog.
1. Select **Storage type** either as *Tier 1* or *Tier 3*. A virtual server instance can use volumes from one storage type only, and the custom image need to be prepared for this storage type.
1. Select the **Region** for your deployment.
1. Enter the file name of the image as **Image file name**.
1. Enter the **Bucket name** of your Cloud Object Storage.
1. Enter **Cloud Object Storage access key** and **Cloud Object Storage secret key**.
1. Click **Import image**.

### Creating a Service ID and API key in IBM Cloud
{: #ha-rhel-create-service-id}

A Service ID in IBM Cloud identifies a service or an application in a similar way as a user ID identifies a user in IBM Cloud.
The service ID is used by the cluster fencing agent to monitor the status of and control the virtual server instances in the cluster.

1. Log in to [IBM Cloud](https://cloud.ibm.com/){: external}.
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

Copy the API key or download to save it.

The key is available for 300 seconds.
After the 300 seconds, you won't be able to display or retrieve the key.
{: important}

## Prepare a RHEL HA Add-On cluster on {{site.data.keyword.powerSys_notm}}
{: #ha-rhel-prepare-rhel-ha-cluster}

## Creating a virtual server instances for the cluster
{: #ha-rhel-create-virtual-server-instances}

Complete the following steps to create the virtual server instances that you want to use as HA cluster nodes.

Before you begin, make sure that you complete all the steps in the [Setting up {{site.data.keyword.powerSys_notm}}](#powervs-setup) section.

1. Log in to [Workspaces - {{site.data.keyword.powerSys_notm}}](https://cloud.ibm.com/power/workspaces){: external}.
1. Select the **Workspace** that you created.
1. Click **View virtual server instances** > **Create Instance**.
   You need to step through the subsections General, Boot Image, Profile, Storage Volume, Network Interfaces.
1. In subsection **General**, enter the **Instance name** and click **+** to increase the **Number of instances** to 2.
1. Select **Numerical postfix** as Instance naming convention, and select **Different server** as Placement group colocation policy.
   A placement group with colocation policy *Different server* is automatically created as part of the virtual server instances deployment.
1. Select the **SSH key** that you created and click **Continue**.
1. In **Boot image**, select the **Operating system** according to your subscription model. \
   Use one of the Linux selections either from the *IBM-provided subscription* or through your *Client-provided subscription*, and select **RHEL8** as the **Image**. \
   Keep **Auto-select pool** for selecting the Storage Pool. \
   Click **Continue**.
1. In **Profile**, select **Machine type**, **Core type**, and the virtual server instance **profile** according to your workload requirements.
   Click **Continue**.
1. In **Storage volumes**, click **Continue**.

   When you deploy multiple instances, storage volumes that are created are shared by both instances.
   The Red Hat High Availability cluster does not require a shared volume.
   For SAP HANA, see [Storage configuration for SAP HANA](https://cloud.ibm.com/docs/sap?topic=sap-storage-design-considerations#storage-config-hana){: external}.
   Those volumes must be created later for the individual server instances after their provisioning completes.
   {: important}

1. In subsection **Network Interfaces**, it is recommended that the cluster nodes are not accessible directly from a public network, so keep the configuration for *Public networks* as **Off**.
1. Click **Attach existing** to attach the virtual server instances to a subnet.
1. In the *Attach an existing network* screen, select one of the *Existing networks*. You can either select **Automatically assign IP address from IP range**, or **Manually specify an IP address from IP range** to specify a free IP address.
1. Click **Attach.**
1. Click **Finish**, check the *I agree to the Terms and Conditions* flag, and click **Create**.

The deployment of the virtual server instances starts.

## Gathering parameters for the cluster configuration
{: #ha-rhel-gather-parameters-for-cluster-config}

Parameters that are required for fencing agent configuration include the *Cloud Resource Name (CRN)* of the {{site.data.keyword.powerSys_notm}} workspace and the *instance IDs* of the virtual server instances.
Some extra parameters need to be derived from the *CRN*.
The fencing agent also uses the *API Key of the Service ID {APIKEY}* to authenticate with the {{site.data.keyword.powerSys_notm}} API.

The uppercase variables in the following section indicate that these parameters need to be set as environment variables on the virtual server instances to simplify the setup of the cluster.

1. Log in to [Workspaces - {{site.data.keyword.powerSys_notm}}](https://cloud.ibm.com/power/workspaces){: external} and search for your **Workspace**.
1. The tile for the workspace contains the CRN, click **Copy** to copy the CRN and paste it to a temporary document.

   The CRN has multiple sections that are divided by a colon.
   The base format of a CRN is:

   `crn:version:cname:ctype:service-name:location:scope:service-instance:resource-type:resource`

   service-name
   :   The fifth field of the CRN of the workspace is always *power-iaas*, the **service name**.

   location
   :   The sixth field is the **location**, which needs to be mapped to the region.

   scope
   :   The seventh field is the **Tenant ID**.

   service_instance
   :   The eighth field is the **Cloud Instance ID** or **GUID**.

1. Set IBMCLOUD_CRN to the full *CRN* and GUID to the content of the *service_instance* field.
1. Set the CLOUD_REGION to the prefix that represents the geographic area of your service instance to target the correct [Power Cloud API endpoint](https://cloud.ibm.com/apidocs/power-cloud#endpoint){: external}.

   CLOUD_REGION when you use a public network
   :   For a public network, map the location to its respective geographic area (*us-east, us-south, eu-de, lon, tor, syd, or tok*).

   CLOUD_REGION when you use a private network
   :   For a private network, map the location to its respective geographic area (*us-east, us-south, eu-de, eu-gb, ca-tor, au-syd, jp-tok, jp-osa, br-sao, or ca-mon*).

1. On the tile for the workspace, click **View Instances**.
1. In the list of the virtual server instances, click each of the cluster nodes, and take a note of each **ID**.
1. Set these IDs as *POWERVSI_01* and *POWERVSI_02*.

### Preparing remote login for virtual server instances
{: #ha-rhel-prepare-remote-login}

Set up SSH forwarding on the bastion host, and prepare or test SSH remote login from your workstation by using your private SSH key.

## Preparing the nodes for RHEL HA Add-On installation
{: #ha-rhel-prepare-nodes-for-rhel-ha-installation}

The following section describes basic preparation steps on the cluster nodes.
Make sure that you follow the steps on both nodes.

Log in as root to each of the cluster nodes.

## Populating entries for each node in the hosts file
{: #ha-rhel-update-hosts-file}

On both nodes, use the following information to populate entries.

Add the IP addresses and hostnames of both nodes to the hosts file `/etc/hosts`.

For more information, see [Setting up /etc/hosts files on RHEL cluster nodes](https://access.redhat.com/solutions/81123){: external}.

## Preparing environment variables
{: #ha-rhel-prepare-environment-variables}

To simplify the setup process, prepare the following environment variables for user root on both nodes.

On both nodes, create a file with the following environment variables and update to your environment.

```sh
export CLUSTERNAME=SAP_CLUSTER         # Cluster Name

export APIKEY=<APIKEY>                 # API Key of the ServiceID
export IBMCLOUD_CRN=<IBMCLOUD_CRN>     # CRN of workspace
export GUID=<GUID>                     # GUID of workspace
export CLOUD_REGION=<CLOUD_REGION>     # Region of workspace
export PROXY_IP=<IP_ADDRESS>           # IP address of proxy server

export NODE1=<HOSTNAME_01>             # <Hostname of virtual server instance 1
export NODE2=<HOSTNAME_02>             # <Hostname of virtual server instance 2

export POWERVSI_01=<POWERVSI_01>       # ID virtual server instance 1
export POWERVSI_02=<POWERVSI_02>       # ID virtual server instance 2
```
{: codeblock}

## Installing and configuring a RHEL HA Add-On cluster
{: #ha-rhel-install-and-configure-rhel-ha-cluster}

Use the following steps to set up a two-node cluster for an IBM {{site.data.keyword.powerSys_notm}}.

The instructions are extracted from Red Hat product documentation and articles:
- [Configuring and managing high availability clusters](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_and_managing_high_availability_clusters/index){: external}
- [Support Policies for RHEL High Availability Clusters - IBM {{site.data.keyword.powerSys_notm}} (PowerVS) Virtual Machines as Cluster Member](https://access.redhat.com/articles/5651561){: external}
- [Configuring a RHEL HA Cluster Fence Agent for an IBM {{site.data.keyword.powerSys_notm}}](https://access.redhat.com/articles/6966644){: external}.

Some steps need to be performed on both nodes, some only on NODE1 or on NODE2.

### Setting up the RHEL HA Add-On cluster
{: #ha-rhel-set-up-cluster}

Use the following information to set up a RHEL HA Add-On cluster.

#### Installing RHEL HA Add-On software
{: #ha-rhel-install-rhel-ha-software}

Install the required software packages.

##### Checking the RHEL HA repository
{: #ha-rhel-check-rhel-ha-repository}

Check that the RHEL High Availability repository is enabled.

On both nodes, use the followings command.

```sh
yum repolist
```
{: pre}

If the HA repository is missing, use the following commands to enable it:

```sh
subscription-manager repos \
    --enable="rhel-8-for-ppc64le-highavailability-e4s-rpms"
```
{: pre}

```sh
yum clean all
```
{: pre}

```sh
yum repolist
```
{: pre}

##### Installing the RHEL HA Add-On software packages
{: #ha-rhel-install-ha-software-packages}

Install the required software packages.

On both nodes, run the following command.

```sh
yum install -y pcs pacemaker fence-agents-ibm-powervs
```
{: pre}

Make sure that you install the minimal version of the *fence-agents-ibm-powervs* package dependent on your Red Hat Enterprise Linux release:

RHEL 8.4
:   fence-agents-ibm-powervs-4.2.1-65.el8_4.11.noarch.rpm (https://access.redhat.com/errata/RHBA-2023:0503){: external}

RHEL 8.6
:   fence-agents-ibm-powervs-4.2.1-89.el8_6.8.noarch.rpm (https://access.redhat.com/errata/RHBA-2023:0428){: external}

### Configuring a RHEL HA Add-On cluster
{: #ha-rhel-configure-rhel-ha-cluster}

#### Configuring firewall services
{: #ha-rhel-configure-firewall-services}

Add the high availability service to the RHEL firewall if *firewalld.service* is installed and enabled.

On both nodes, run the following commands.

```sh
firewall-cmd --permanent --add-service=high-availability
```
{: pre}

```sh
firewall-cmd --reload
```
{: pre}

#### Starting the PCS daemon
{: #ha-rhel-start-pcsd-daemon}

Start the PCS daemon, which is used for controlling and configuration of RHEL HA Add-On clusters through PCS.

On both nodes, run the following commands.

```sh
systemctl enable --now pcsd.service
```
{: pre}

Make sure that the PCS service is running:

```sh
systemctl status pcsd.service
```
{: pre}

#### Setting a password for hacluster user ID
{: #ha-rhel-set-hacluster-password}

Set the password for the hacluster user ID.

On both nodes, run the following command.

```sh
passwd hacluster
```
{: pre}

#### Authenticating the cluster nodes
{: #ha-rhel-authenticate-cluster-nodes}

Use the following command to authenticate user hacluster to the PCS daemon on the nodes in the cluster.
The command prompts you for the password that you set in the previous step.

On NODE1, run the following command.

```sh
pcs host auth ${NODE1} ${NODE2} -u hacluster
```
{: pre}

If you get an error message similar to `Error: Unable to communicate with {NODE2}`, check whether you have any proxy variables set in your environment (`env | grep -i proxy`). You need to unset these variables or define a *no_proxy* variable to exclude the cluster nodes: `export no_proxy=${NODE1},${NODE2},$no_proxy`
{: tip}

#### Configuring and starting the cluster nodes
{: #ha-rhel-configure-and-start-cluster-nodes}

Configure the cluster configuration file and synchronize the configuration to the specified nodes.

The `--start` option also starts the cluster service on the nodes.

On NODE1, run the following command.

```sh
pcs cluster setup ${CLUSTERNAME} --start ${NODE1} ${NODE2}
```
{: pre}

```sh
pcs status
```
{: pre}

#### Creating the fencing device
{: #ha-rhel-create-fencing-device}

STONITH is an acronym for "Shoot The Other Node In The Head" and protects your data from corruption in a split brain situation.

STONITH (fencing) must be enabled for a RHEL HA Add-On production cluster.
{: important}

Fence agent *fence_ibm_powervs* is the only supported agent for a STONITH device on {{site.data.keyword.powerSys_notm}} clusters.

The fence agent connects to the [Power Cloud API](https://cloud.ibm.com/apidocs/power-cloud){: external} by using parameters *APIKEY*, *IBMCLOUD_CRN*, *CLOUD_REGION*, *GUID*, and the instance IDs *POWERVSI_01* and *POWERVSI_02*.
You can test the agent invocation by using the parameters that were gathered in the [Gather required parameters for the cluster configuration](#gather-parameters-for-cluster-config) section.

#### Identifying the virtual server instances for fencing
{: #ha-rhel-identify-virtual-instances-for-fencing}

Use the *list* option of *fence_ibm_powervs* to identify and or verify the instance IDs of the two cluster nodes:

On any node, run the following command.

```sh
fence_ibm_powervs \
    --token=${APIKEY} \
    --crn=${IBMCLOUD_CRN} \
    --instance=${GUID} \
    --region=${CLOUD_REGION} \
    --api-type=public \
    -o list
```
{: pre}

If the virtual server instances have access to only a private network, you must use the *--api-type=private* option, which also requires an extra *--proxy* option.

Example:

```sh
fence_ibm_powervs \
    --token=${APIKEY} \
    --crn=${IBMCLOUD_CRN} \
    --instance=${GUID} \
    --region=${CLOUD_REGION} \
    --api-type=private \
    --proxy=http://${PROXY_IP}:3128 \
    -o list
```
{: pre}

Continue by using *--api-type=private* in the following examples.

#### Checking the status of both virtual server instances
{: #ha-rhel-check-virtual-instances-status}

On both nodes, run the following commands.

```sh
time fence_ibm_powervs \
    --token=${APIKEY} \
    --crn=${IBMCLOUD_CRN} \
    --instance=${GUID} \
    --region=${CLOUD_REGION} \
    --plug=${POWERVSI_01} \
    --api-type=private \
    --proxy=http://${PROXY_IP}:3128 \
    -o status
```
{: pre}

```sh
time fence_ibm_powervs \
    --token=${APIKEY} \
    --crn=${IBMCLOUD_CRN} \
    --instance=${GUID} \
    --region=${CLOUD_REGION} \
    --plug=${POWERVSI_02} \
    --api-type=private \
    --proxy=http://${PROXY_IP}:3128 \
    -o status
```
{: pre}

The `status` action of the fence agent against a virtual server instance {pvm_instance_id} displays its power status.

On both nodes, the two commands must report `Status: ON`.

The output of the `time` command might be useful later when you choose timeouts for the STONITH device.

You can add the `-v` flag for verbose output, which shows more details in connecting to the Power Cloud API and querying virtual server power status.

#### Creating a stonith device
{: #ha-rhel-create-stonith-device}

The following command shows the device-specific options for the *fence_ibm_powervs* fencing agent.

```sh
pcs stonith describe fence_ibm_powervs
```
{: pre}

Create the stonith device for both virtual server instances.

On NODE1, run the following command.

```sh
pcs stonith create fence_device fence_ibm_powervs \
    token=${APIKEY} \
    crn=${IBMCLOUD_CRN} \
    instance=${GUID} \
    region=${CLOUD_REGION} \
    api-type=private \
    proxy=http://${PROXY_IP}:3128 \
    pcmk_host_map="${NODE1}:${POWERVSI_01};${NODE2}:${POWERVSI_02}" \
    pcmk_reboot_timeout=600 \
    pcmk_monitor_timeout=600 \
    pcmk_status_timeout=60
```
{: pre}

Verify the configuration with the following commands.

```sh
pcs config
```
{: pre}

```sh
pcs status
```

```sh
pcs stonith config
```
{: pre}

```sh
pcs stonith status
```
{: pre}

#### Setting the stonith-action cluster property
{: #ha-rhel-set-stonith-action-cluster-property}

To speed up failover times in an IBM Power Virtual Server cluster, you can change the cluster property *stonith-action* to *off*.
When the cluster performs a fencing action, it triggers a *power off* operation instead of a *reboot* for the instance that is fenced.

After this change, you always need to log in to the IBM Cloud Console, and manually start an instance that was fenced by the cluster.

```sh
pcs property set stonith-action=off
```
{: pre}

Verify the change:

```sh
pcs config
```
{: pre}


#### Testing fencing operations
{: #ha-rhel-test-fencing-operations}

To test the STONITH configuration, you need to fence the nodes manually.

When fencing is triggered manually through `pcs stonith fence`, the `stonith-action` cluster attribute is not used, and the node is restarted.
{: note}

On NODE1, run the following commands:

```sh
pcs stonith fence ${NODE2}
```
{: pre}

```sh
pcs status
```
{: pre}

As a result, NODE2 restarts.

After NODE2 is running again, start the cluster on NODE2 and try to fence NODE1.

On NODE2, run the following commands.

```sh
pcs cluster start
```
{: pre}

```sh
pcs status
```
{: pre}

```sh
pcs stonith status
```
{: pre}

```sh
pcs stonith fence ${NODE1}
```
{: pre}

NODE1 restarts.

After it is running, start the cluster on NODE1 again.

On NODE1, run the following command.

```sh
pcs cluster start
```
{: pre}

```sh
pcs status
```
{: pre}

```sh
pcs stonith status
```
{: pre}
