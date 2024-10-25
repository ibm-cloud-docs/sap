---
copyright:
  years: 2024
lastupdated: "2024-10-25"

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Implementing a RHEL HA Add-On cluster on IBM {{site.data.keyword.powerSys_notm}} in a Multizone Region Environment
{: #ha-rhel-mz}

Use the following information and procedures to implement a Red Hat Enterprise Linux (RHEL) High Availability (HA) cluster in a multizone region environment.
The cluster uses instances in [{{site.data.keyword.powerSysFull}}](https://www.ibm.com/products/power-virtual-server){: external} as cluster nodes.
The virtual server instances run in different zones in a multizone region.
The setup uses the *powervs-subnet* cluster resource agent to manage the service IP address of an application in a multizone region implementation.
The resource agent supports only the use of different zones in the same multizone region.
Deployment across multiple regions is not supported.
See [Multizone regions (MZR)](https://cloud.ibm.com/docs/overview?topic=overview-locations#table-mzr){: external} and [IBM Cloud regions](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-ibm-cloud-reg){: external} for more information about multizone regions and available locations.

The information describes how to transform the individual virtual server instances into a cluster.
{: shortdesc}

These procedures include installing the high availability packages and agents on each cluster node and configuring the fencing devices.

This information is intended for architects and specialists who are planning a high availability deployment of SAP applications on {{site.data.keyword.powerSys_notm}}. It is not intended to replace existing SAP or Red Hat documentation.
{: note}

## Before you begin
{: #ha-rhel-mz-begin}

Review the general requirements, product documentation, support articles, and SAP notes listed in [Implementing High Availability for SAP Applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

## Creating virtual server instances for the cluster
{: #ha-rhel-mz-create-virtual-server-instances}

Use the instructions in [Creating Instances for a High Availability Cluster on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-ha-vsi) to create the virtual server instances that you want to use as cluster nodes.

Create two workspaces in two zones of a multizone region.
Create a [Transit Gateway](/docs/transit-gateway) and add both workspaces to the connections.
Create two virtual server instances, one in each workspace.
{: overview}

## Gathering parameters for the cluster configuration
{: #ha-rhel-mz-gather-parameters-for-cluster-config}

The *Cloud Resource Name (CRN)* of the {{site.data.keyword.powerSys_notm}} workspaces in the different zones and the *instance IDs* of the virtual server instances are required to configure the fencing agent.
Some extra parameters are derived from the *CRN*.
The fencing agent also uses the *API Key of the Service ID* to authenticate with the {{site.data.keyword.powerSys_notm}} API.

The uppercase variables in the following section indicate that these parameters must be set as environment variables on the virtual server instances to simplify the cluster setup.

Use the following steps to gather the parameters to configure a cluster.

1. Log in to [Workspaces - {{site.data.keyword.powerSys_notm}}](https://cloud.ibm.com/power/workspaces){: external}.
1. The list contains the name and CRN of the workspaces.

   Locate both workspaces. Click **Copy** next to the CRN and paste it into a temporary document.

   A CRN has multiple sections that are divided by a colon.
   The following example is the base format of a CRN:

   `crn:version:cname:ctype:service-name:location:scope:service-instance:resource-type:resource`

   service-name
   :   The fifth field of the CRN of the workspace is always *power-iaas*, the **service name**.

   location
   :   The sixth field is the **location** that needs to be mapped to the region.

   scope
   :   The seventh field is the **Tenant ID**.

   service_instance
   :   The eighth field is the **Cloud Instance ID** or **GUID**.

1. Set `IBMCLOUD_CRN_1` to the full CRN and `GUID_1` to the contents of the *service_instance* field of the first workspace.
1. Set `IBMCLOUD_CRN_2` and `GUID_2` to the same fields from the second workspace.
1. Set `CLOUD_REGION` to the prefix that represents the geographic area of your service instance to target the correct [Power Cloud API endpoint](https://cloud.ibm.com/apidocs/power-cloud#endpoint){: external}.

   CLOUD_REGION when you use a public network
   :   For a public network, map the location to its respective geographic area (*us-east, us-south, eu-de, lon, tor, syd, or tok*).

   CLOUD_REGION when you use a private network
   :   For a private network, map the location to its respective geographic area (*us-east, us-south, eu-de, eu-gb, ca-tor, au-syd, jp-tok, jp-osa, br-sao, or ca-mon*).

1. On the tile for the workspace in the first zone, click **View Instances**.
1. In the list of the virtual server instances, click the cluster node and take a note of the **ID**.
1. Repeat the previous two steps for the workspace in the second zone.
1. Set these IDs as `POWERVSI_1` and `POWERVSI_2`.
1. For information on how to obtain the *Service ID API key*, see [Creating a Custom Role, Service ID, and API key in IBM Cloud](/docs/sap?topic=sap-ha-vsi#ha-vsi-create-service-id).
   The *apikey* object in the downloaded API key file provides the API key that is required by the fencing agent.

## Preparing the nodes for RHEL HA Add-On installation
{: #ha-rhel-mz-prepare-nodes-for-rhel-ha-installation}

The following section describes basic preparation steps on the cluster nodes.
Make sure that you follow the steps on both nodes.

Log in as the root user to each of the cluster nodes.

### Populating entries for each node in the hosts file
{: #ha-rhel-mz-update-hosts-file}

On both nodes, use the following information to populate entries.

Add the IP addresses and hostnames of both nodes to the hosts file `/etc/hosts`.

For more information, see [Setting up `/etc/hosts` files on RHEL cluster nodes](https://access.redhat.com/solutions/81123){: external}.

### Preparing environment variables
{: #ha-rhel-mz-prepare-environment-variables}

To simplify the setup process, prepare the following environment variables for the root user on both nodes.

On both nodes, create a file with the following environment variables and update to your environment.

```sh
export CLUSTERNAME=SAP_CLUSTER              # Cluster Name
export NODE1=<HOSTNAME_1>                   # Hostname of first virtual server instance
export NODE2=<HOSTNAME_2>                   # Hostname of second virtual server instance

export APIKEY=<APIKEY>                      # API Key of the ServiceID
export CLOUD_REGION=<CLOUD_REGION>          # Multizone region of workspaces
export PROXY_IP=<IP_ADDRESS>                # IP address of proxy server

export IBMCLOUD_CRN_1=<IBMCLOUD_CRN_1>      # CRN of first workspace
export IBMCLOUD_CRN_2=<IBMCLOUD_CRN_2>      # CRN of second workspace
export GUID_1=<GUID_1>                      # GUID of first workspace
export GUID_2=<GUID_2>                      # GUID of second workspace
export POWERVSI_1=<POWERVSI_1>              # ID of first virtual server instance
export POWERVSI_2=<POWERVSI_2>              # ID of second virtual server instance
```
{: codeblock}

## Installing and configuring a RHEL HA Add-On cluster
{: #ha-rhel-mz-install-and-configure-rhel-ha-cluster}

Use the following steps to set up a two-node cluster for an IBM {{site.data.keyword.powerSys_notm}}.

The instructions are based on the Red Hat product documentation and articles that are listed in [Implementing High Availability for SAP Applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

You need to complete some steps on both nodes and some steps on either NODE1 or on NODE2.
{: tip}

### Installing RHEL HA Add-On software
{: #ha-rhel-mz-install-rhel-ha-software}

Install the required software packages.
The minimum operating system version required to use the *powervs-subnet* resource agent is RHEL 9.2.

The *@server* group must be installed on the operating system.
This installation is a standard requirement for SAP applications.
{: attention}

#### Checking the RHEL HA repository
{: #ha-rhel-mz-check-rhel-ha-repository}

Check that the appropriate repository is enabled on both nodes by using the following command.

```sh
dnf repolist
```
{: pre}

Use the following commands to enable the HA repository if it is missing.

```sh
subscription-manager repos \
    --enable="rhel-9-for-ppc64le-highavailability-e4s-rpms"
```
{: pre}

```sh
dnf clean all
```
{: pre}

```sh
dnf repolist
```
{: pre}

#### Installing the RHEL HA Add-On software packages
{: #ha-rhel-mz-install-ha-software-packages}

Install the required software packages on both nodes by running the following command.

```sh
dnf install -y pcs pacemaker fence-agents-ibm-powervs
```
{: pre}

Make sure that you install the minimal version of the *fence-agents-ibm-powervs* package dependent on your Red Hat Enterprise Linux release:

RHEL 9
:   fence-agents-ibm-powervs-4.10.0-43.el9

### Configuring a RHEL HA Add-On cluster
{: #ha-rhel-mz-configure-rhel-ha-cluster}

Use the following steps to configure a RHEL HA Add-On cluster.

#### Configuring firewall services
{: #ha-rhel-mz-configure-firewall-services}

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
{: #ha-rhel-mz-start-pcsd-daemon}

Start the PCS daemon that is used for controlling and configuring RHEL HA Add-On clusters through PCS.

On both nodes, run the following commands.

```sh
systemctl enable --now pcsd.service
```
{: pre}

Make sure that the PCS service is running.

```sh
systemctl status pcsd.service
```
{: pre}

#### Setting a password for the hacluster user ID
{: #ha-rhel-mz-set-hacluster-password}

Set the password for the hacluster user ID.

On both nodes, run the following command.

```sh
passwd hacluster
```
{: pre}

#### Authenticating the cluster nodes
{: #ha-rhel-mz-authenticate-cluster-nodes}

Use the following command to authenticate the user hacluster to the PCS daemon on the nodes in the cluster.
The command prompts you for the password that you set in the previous step.

On NODE1, run the following command.

```sh
pcs host auth ${NODE1} ${NODE2} -u hacluster
```
{: pre}

#### Configuring and starting the cluster nodes
{: #ha-rhel-mz-configure-and-start-cluster-nodes}

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
{: #ha-rhel-mz-create-fencing-device}

STONITH is an acronym for "Shoot The Other Node In The Head" and protects your data from corruption in a split-brain situation.

You must enable STONITH (fencing) for a RHEL HA Add-On production cluster.
{: important}

Fence agent *fence_ibm_powervs* is the only supported agent for a STONITH device on {{site.data.keyword.powerSys_notm}} clusters.

You must configure a fencing device for each of the two workspaces in the multizone region.
The fence agent connects to the [Power Cloud API](https://cloud.ibm.com/apidocs/power-cloud){: external} by using the common `APIKEY` and `CLOUD_REGION` parameters.
The parameters `IBMCLOUD_CRN_<n>`, `GUID_<n>`, and the instance ID `POWERVSI_<n>` are specific to the workspace.
You can test the agent invocation by using the parameters that you gathered in the [Gathering required parameters for the cluster configuration](#ha-rhel-mz-gather-parameters-for-cluster-config) section.

#### Identifying the virtual server instances for fencing
{: #ha-rhel-mz-identify-virtual-instances-for-fencing}

Use the *list* option of *fence_ibm_powervs* to identify and or verify the instance IDs of the two cluster nodes.

On any node, run the following commands.

```sh
fence_ibm_powervs \
    --token=${APIKEY} \
    --crn=${IBMCLOUD_CRN_1} \
    --instance=${GUID_1} \
    --region=${CLOUD_REGION} \
    --api-type=public \
    -o list
```
{: pre}

```sh
fence_ibm_powervs \
    --token=${APIKEY} \
    --crn=${IBMCLOUD_CRN_2} \
    --instance=${GUID_2} \
    --region=${CLOUD_REGION} \
    --api-type=public \
    -o list
```
{: pre}

If the virtual server instances have access to only a private network, you must use the `--api-type=private` option, which also requires an extra `--proxy` option.

Example:

```sh
fence_ibm_powervs \
    --token=${APIKEY} \
    --crn=${IBMCLOUD_CRN_1} \
    --instance=${GUID_1} \
    --region=${CLOUD_REGION} \
    --api-type=private \
    --proxy=http://${PROXY_IP}:3128 \
    -o list
```
{: pre}

The following examples use the `--api-type=private` option.

#### Checking the status of both virtual server instances
{: #ha-rhel-mz-check-virtual-instances-status}

On both nodes, run the following commands.

```sh
time fence_ibm_powervs \
    --token=${APIKEY} \
    --crn=${IBMCLOUD_CRN_1} \
    --instance=${GUID_1} \
    --region=${CLOUD_REGION} \
    --plug=${POWERVSI_1} \
    --api-type=private \
    --proxy=http://${PROXY_IP}:3128 \
    -o status
```
{: pre}

```sh
time fence_ibm_powervs \
    --token=${APIKEY} \
    --crn=${IBMCLOUD_CRN_2} \
    --instance=${GUID_2} \
    --region=${CLOUD_REGION} \
    --plug=${POWERVSI_2} \
    --api-type=private \
    --proxy=http://${PROXY_IP}:3128 \
    -o status
```
{: pre}

The `status` action of the fence agent against a virtual server instance `--plug=<POWERVSI_n>` displays its power status.

On both nodes, the two commands must report `Status: ON`.

The output of the `time` command might be useful later when you choose timeouts for the STONITH device.

You can add the `-v` flag for verbose output, which shows more information about connecting to the Power Cloud API and querying virtual server power status.

#### Creating the stonith devices
{: #ha-rhel-mz-create-stonith-device}

The following command shows the device-specific options for the *fence_ibm_powervs* fencing agent.

```sh
pcs stonith describe fence_ibm_powervs
```
{: pre}

Create the stonith device for both virtual server instances.

On NODE1, run the following commands.

```sh
pcs stonith create fence_node1 fence_ibm_powervs \
    token=${APIKEY} \
    crn=${IBMCLOUD_CRN_1} \
    instance=${GUID_1} \
    region=${CLOUD_REGION} \
    api_type=private \
    proxy=http://${PROXY_IP}:3128 \
    pcmk_host_map="${NODE1}:${POWERVSI_1}" \
    pcmk_reboot_timeout=600 \
    pcmk_monitor_timeout=600 \
    pcmk_status_timeout=60
```
{: pre}

```sh
pcs stonith create fence_node2 fence_ibm_powervs \
    token=${APIKEY} \
    crn=${IBMCLOUD_CRN_2} \
    instance=${GUID_2} \
    region=${CLOUD_REGION} \
    api_type=private \
    proxy=http://${PROXY_IP}:3128 \
    pcmk_host_map="${NODE2}:${POWERVSI_2}" \
    pcmk_reboot_timeout=600 \
    pcmk_monitor_timeout=600 \
    pcmk_status_timeout=60
```
{: pre}

Although the `fence_ibm_powervs` agent uses `api-type` as an option when started from the command line, the stonith resource needs to be created by using `api_type`.
{: important}

Verify the configuration with the following commands.

```sh
pcs config
```
{: pre}

```sh
pcs status
```
{: pre}

```sh
pcs stonith config
```
{: pre}

```sh
pcs stonith status
```
{: pre}

#### Setting the stonith-action cluster property
{: #ha-rhel-mz-set-stonith-action-cluster-property}

For the *powervs-subnet* resource agent to work, you must set the *stonith-action* cluster property to *off*.
When the cluster performs a fencing action, it triggers a *power-off* operation instead of a *reboot* for the fenced instance.
{: attention}

After this change, you always need to log in to the {{site.data.keyword.cloud_notm}} Console, and manually start an instance that was fenced by the cluster.

```sh
pcs property set stonith-action=off
```
{: pre}

Verify the change.

```sh
pcs config
```
{: pre}

#### Testing fencing operations
{: #ha-rhel-mz-test-fencing-operations}

To test the STONITH configuration, manually fence the nodes.

When fencing is manually triggered by `pcs stonith fence`, the `stonith-action` cluster attribute is not used and the node restarts.
{: note}

On NODE1, run the following commands.

```sh
pcs stonith fence ${NODE2}
```
{: pre}

```sh
pcs status
```
{: pre}

As a result, NODE2 restarts.

After NODE2 is running, start the cluster on NODE2 and try to fence NODE1.

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

After the node is running, start the cluster on NODE1 again.

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

### Disabling the automatic startup of cluster services when the server boots
{: #ha-rhel-mz-disable-cl-auto-start}

After a virtual server instance restarts, it takes some time for its *STATUS* to become *ACTIVE* and its *Health Status* to become *OK*.
The *powervs-subnet* resource agent requires these states to function properly.
Therefore, you must disable automatic cluster startup and start the cluster manually after the instance reaches the required states.

On any node, disable the automatic startup of cluster services at boot time.

```sh
pcs cluster disable --all
```
{: pre}

When you restart an instance, check the instance status in the {{site.data.keyword.cloud_notm}} Console and wait until the *Status* field shows *Active* with a green checkmark.
Then, use the following command to manually start the cluster.

```sh
pcs cluster start
```
{: pre}

## Preparing a multizone RHEL HA Add-On cluster for a virtual IP address resource
{: #ha-rhel-mz-create-vip}

Use the following steps to prepare a multizone RHEL HA Add-on cluster for a virtual IP address resource.

### Verifying operating system requirements
{: #ha-rhel-mz-verify-os-requirements}

Verify that the `NetworkManager-config-server` package is installed.

On both nodes, run the following command.

```sh
dnf list NetworkManager-config-server
```
{: pre}

Sample output:

```sh
# dnf list NetworkManager-config-server
Installed Packages
NetworkManager-config-server.noarch                                    1:1.42.2-16.el9_2                                     @rhel-9-for-ppc64le-baseos-e4s-rpms
```
{: screen}

Make sure that the NetworkManager `no-auto-default` configuration variable is set to `*`.

```sh
NetworkManager --print-config | grep "no-auto-default="
```
{: pre}

Sample output:

```sh
# NetworkManager --print-config | grep "no-auto-default="
no-auto-default=*
```
{: screen}

If the `no-auto-default` shows a value other than `*`, edit the `/etc/NetworkManager/conf.d/00-server.conf` file and change the variable as needed.

### Installing the powervs-subnet resource agent
{: #ha-rhel-mz-install-powervs-subnet-resource-agent}

Currently, the *powervs-subnet* resource agent is available in the ClusterLabs GitHub resource agent repository.

Download the resource agent from https://github.com/ClusterLabs/resource-agents/blob/main/heartbeat/powervs-subnet.in and place a copy in the `/tmp` directory on both nodes.

On both nodes, install the script in the *OCF Resource Agents* heartbeat directory and set its permissions.

```sh
sed -e 's|#!@PYTHON@|#!/usr/bin/python3|' /tmp/powervs-subnet.in \
     > /usr/lib/ocf/resource.d/heartbeat/powervs-subnet
```
{: pre}

```sh
chmod 755 /usr/lib/ocf/resource.d/heartbeat/powervs-subnet
```
{: pre}

Use the following command to verify the installation and display a brief description of the resource agent.

```sh
pcs resource describe powervs-subnet
```
{: pre}

### Creating a service ID for the `powervs-subnet` resource agent
{: #ha-rhel-mz-iam-custom-role}

Follow the steps in [Creating a Custom Role, Service ID, and API key in {{site.data.keyword.cloud_notm}}](/docs/sap?topic=sap-ha-vsi#ha-vsi-create-service-id) to create a `Service ID` and an `API key` for the `powervs-subnet` resource agent.

This completes the basic cluster implementation.
You can now proceed with the specific instructions for your planned high availability scenario.
{: note}


Do not perform the following steps now.
You must create a virtual IP address resource later during the cluster configuration of the specific multizone region high availability scenario.
Then the instructions refer to the following section.
{: attention}

## Creating a virtual IP address resource in the multizone region setup
{: #ha-rhel-mz-define-subnet-resource}

To simplify the setup of the virtual IP address set the following environment variables on NODE1.
These variables are required in addition to the environment variables used to create the cluster and stonith devices, and are used with the next command.

```sh
export SUBNET_NAME="vip-${sid}-net"            # Name which is used to define the subnet in IBM Cloud
export CIDR="CIDR of subnet"                   # CIDR of the subnet containing the service IP address
export VIP="Service IP address"                # IP address in the subnet
export JUMBO="true or false"                   # Enable Jumbo frames
export APIKEY="APIKEY or path to file"         # API Key of the IBM Cloud IAM ServiceID for the resource agent
export API_TYPE="private or public"            # Use private or public API endpoints
```
{: screen}

The variable `SUBNET_NAME` is set to the name for the subnet.
The variable `CIDR` represents the Classless Inter-Domain Routing (CIDR) notation for the subnet.
Specify `CIDR` in the format `<IPv4_address>/number`.
The variable `VIP` is the IP address for the virtual IP address resource, and must belong to the `CIDR` of the subnet.
Set variable `JUMBO` to `true` if you like to enable the subnet for a large MTU size.
Set variable `API_TYPE` to `private` to communicate to IBM Cloud IAM and IBM Power Cloud API across private endpoints.

When you are [Creating a service ID for the powervs-subnet resource agent](/docs/sap?topic=sap-ha-rhel-mz#ha-rhel-mz-iam-custom-role), you can copy its APIKEY and set the `APIKEY` environment variable to this value.
Alternatively, you can download the key as a JSON file, and place a copy of this file on both cluster nodes.
Then set the `APIKEY` environment variable to a string that starts with the `@` character, followed by the full path to the key file.

The second option is recommended.
{: note}

The following shows an example of the environment variables.

```sh
export SUBNET_NAME="vip-mha-net"
export CIDR="10.40.11.100/30"
export VIP="10.40.11.102"
export JUMBO="true"
export APIKEY="@/root/.apikey.json"
export API_TYPE="private"
```
{: screen}

Run the command `pcs resource describe powervs-subnet` on one of the cluster nodes to get information about the parameters for the resource agent.
{: note}

On NODE1, create a `powervs-subnet` resource by running the following command.

```sh
pcs resource create vip_${SID}_${INSTNO} powervs-subnet \
    api_key=${APIKEY} \
    api_type=${API_TYPE} \
    cidr=${CIDR} \
    ip=${VIP} \
    crn_host_map="${NODE1}:${IBMCLOUD_CRN_1};${NODE2}:${IBMCLOUD_CRN_2}" \
    vsi_host_map="${NODE1}:${POWERVSI_1};${NODE2}:${POWERVSI_2}" \
    jumbo=${JUMBO} \
    region=${CLOUD_REGION} \
    subnet_name=${SUBNET_NAME} \
    op start timeout=720 \
    op stop timeout=300 \
    op monitor interval=60 timeout=30
```
{: pre}

If you set `API_TYPE` to `public` then you need to specify a `proxy` parameter in addition.
{: note}



Check the configured virtual IP address resource and the cluster status.

```sh
pcs resource config vip_${SID}_${INSTNO}
```
{: pre}

Sample output:

```sh
# pcs resource config vip_MHA_00
Resource: vip_MHA_00 (class=ocf provider=heartbeat type=powervs-subnet)
  Attributes: vip_MHA_00-instance_attributes
    api_key=@/root/.apikey.json
    api_type=private
    cidr=10.40.11.100/30
    crn_host_map=cl-mha-1:crn:v1:bluemix:public:power-iaas:eu-de-2:**********************************:************************************::;cl-mha-2:crn:v1:bluemix:public:power-iaas:eu-
        de-1:**********************************:************************************::
    ip=10.40.11.102
    jumbo=true
    proxy=http://10.30.40.4:3128
    region=eu-de
    subnet_name=vip-mha-net
    vsi_host_map=cl-mha-1:************************************;cl-mha-2:************************************
  Operations:
    monitor: res_vip_MHA_00-monitor-interval-60
      interval=60
      timeout=60
    start: res_vip_MHA_00-start-interval-0s
      interval=0s
      timeout=720
    stop: res_vip_MHA_00-stop-interval-0s
      interval=0s
      timeout=300
```
{: screen}


```sh
pcs status --full
```
{: pre}

The following example is a sample output of an SAP HANA System Replication cluster in a multizone region setup.

```sh
# pcs status --full
Cluster name: SAP_MHA
Status of pacemakerd: 'Pacemaker is running' (last updated 2024-07-31 11:37:49 +02:00)
Cluster Summary:
  * Stack: corosync
  * Current DC: cl-mha-2 (2) (version 2.1.5-9.el9_2.4-a3f44794f94) - partition with quorum
  * Last updated: Wed Jul 31 11:37:50 2024
  * Last change:  Wed Jul 31 11:37:31 2024 by root via crm_attribute on cl-mha-1
  * 2 nodes configured
  * 7 resource instances configured

Node List:
  * Node cl-mha-1 (1): online, feature set 3.16.2
  * Node cl-mha-2 (2): online, feature set 3.16.2

Full List of Resources:
  * fence_node1	(stonith:fence_ibm_powervs):	 Started cl-mha-1
  * fence_node2	(stonith:fence_ibm_powervs):	 Started cl-mha-2
  * Clone Set: SAPHanaTopology_MHA_00-clone [SAPHanaTopology_MHA_00]:
    * SAPHanaTopology_MHA_00	(ocf:heartbeat:SAPHanaTopology):	 Started cl-mha-2
    * SAPHanaTopology_MHA_00	(ocf:heartbeat:SAPHanaTopology):	 Started cl-mha-1
  * Clone Set: SAPHana_MHA_00-clone [SAPHana_MHA_00] (promotable):
    * SAPHana_MHA_00	(ocf:heartbeat:SAPHana):	 Unpromoted cl-mha-2
    * SAPHana_MHA_00	(ocf:heartbeat:SAPHana):	 Promoted cl-mha-1
  * vip_MHA_00	(ocf:heartbeat:powervs-subnet):	 Started cl-mha-1

Node Attributes:
  * Node: cl-mha-1 (1):
    * hana_mha_clone_state            	: PROMOTED
    * hana_mha_op_mode                	: logreplay
    * hana_mha_remoteHost             	: cl-mha-2
    * hana_mha_roles                  	: 4:P:master1:master:worker:master
    * hana_mha_site                   	: SiteA
    * hana_mha_sra                    	: -
    * hana_mha_srah                   	: -
    * hana_mha_srmode                 	: syncmem
    * hana_mha_sync_state             	: PRIM
    * hana_mha_version                	: 2.00.075.00
    * hana_mha_vhost                  	: cl-mha-1
    * lpa_mha_lpt                     	: 1722418651
    * master-SAPHana_MHA_00           	: 150
  * Node: cl-mha-2 (2):
    * hana_mha_clone_state            	: DEMOTED
    * hana_mha_op_mode                	: logreplay
    * hana_mha_remoteHost             	: cl-mha-1
    * hana_mha_roles                  	: 4:S:master1:master:worker:master
    * hana_mha_site                   	: SiteB
    * hana_mha_sra                    	: -
    * hana_mha_srah                   	: -
    * hana_mha_srmode                 	: syncmem
    * hana_mha_sync_state             	: SOK
    * hana_mha_version                	: 2.00.075.00
    * hana_mha_vhost                  	: cl-mha-2
    * lpa_mha_lpt                     	: 30
    * master-SAPHana_MHA_00           	: 100

Migration Summary:

Tickets:

PCSD Status:
  cl-mha-1: Online
  cl-mha-2: Online

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```
{: screen}
