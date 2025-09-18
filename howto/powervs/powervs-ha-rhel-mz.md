---
copyright:
  years: 2024, 2025
lastupdated: "2025-09-18"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Implementing a Red Hat Enterprise Linux High Availability Add-On cluster in a multizone region environment
{: #ha-rhel-mz}

Use the following information and procedures to implement a Red Hat Enterprise Linux (RHEL) High Availability Add-On cluster in a multizone region environment.
The cluster uses instances in [{{site.data.keyword.powerSysFull}}](https://www.ibm.com/products/power-virtual-server){: external} as cluster nodes.
The virtual server instances run in different zones in a multizone region.

The setup uses the *powervs-subnet* cluster resource agent to manage the service IP address of an application in a multizone region implementation.

The resource agent supports only the use of different zones in the same multizone region.
Deployment across multiple regions is not supported.
See [Multizone regions (MZR)](/docs/overview?topic=overview-locations#table-mzr){: external} and [IBM Cloud regions](/docs/power-iaas?topic=power-iaas-ibm-cloud-reg){: external} for more information about multizone regions and available locations.

The information describes how to transform the individual virtual server instances into a cluster.
{: shortdesc}

These procedures include installing the high availability packages and agents on each cluster node and configuring the fencing devices.

This information is intended for architects and specialists who are planning a high availability deployment of SAP applications on {{site.data.keyword.powerSys_notm}}. It is not intended to replace existing SAP or Red Hat documentation.
{: note}

## Before you begin
{: #ha-rhel-mz-begin}

Review the general requirements, product documentation, support articles, and SAP notes listed in [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

## Creating virtual server instances for the cluster
{: #ha-rhel-mz-create-virtual-server-instances}

Use the instructions in [Creating instances for a high availability cluster](/docs/sap?topic=sap-ha-vsi) to create the virtual server instances that you want to use as cluster nodes.

Create two workspaces in two zones of a multizone region.
Create a [Transit Gateway](/docs/transit-gateway) and add both workspaces to the connections.
Create two virtual server instances, one in each workspace.
{: overview}

## Preparing the nodes for RHEL HA Add-On installation
{: #ha-rhel-mz-prepare-nodes-for-rhel-ha-installation}

The following section describes basic preparation steps on the cluster nodes.
Make sure that you follow the steps on both nodes.

Log in as the root user to each of the cluster nodes.

### Adding cluster node entries to the hosts file
{: #ha-rhel-mz-update-hosts-file}

On both nodes, add the IP addresses and hostnames of both nodes to the `/etc/hosts` file.

For more information, see [Setting up `/etc/hosts` files on RHEL cluster nodes](https://access.redhat.com/solutions/81123){: external}.

### Preparing environment variables
{: #ha-rhel-mz-prepare-environment-variables}

To simplify the setup process, prepare some environment variables for the root user.
These environment variables are used with later operating system commands in this information.

On both nodes, set the following environment variables.

```sh
# General settings
export CLUSTERNAME="SAP_CLUSTER"         # Cluster name

export APIKEY=<APIKEY>                   # API Key of the IBM Cloud IAM ServiceID for the fencing agent
export CLOUD_REGION=<CLOUD_REGION>       # Multizone region name
export PROXY_IP=<IP_ADDRESS>             # IP address of proxy server

# Workspace 1
export IBMCLOUD_CRN_1=<IBMCLOUD_CRN_1>   # Workspace CRN
export GUID_1=<GUID_1>                   # Workspace GUID

# Workspace 2
export IBMCLOUD_CRN_2=<IBMCLOUD_CRN_2>   # Workspace CRN
export GUID_2=<GUID_2>                   # Workspace GUID

# Virtual server instance 1
export NODE1=<HOSTNAME_1>                # Virtual server instance hostname
export POWERVSI_1=<POWERVSI_1>           # Virtual server instance id

# Virtual server instance 2
export NODE2=<HOSTNAME_2>                # Virtual server instance
export POWERVSI_2=<POWERVSI_2>           # Virtual server instance id
```
{: codeblock}

To find the settings for the `APIKEY`, `IBMCLOUD_CRN_?`, `GUID_?`, and `POWERVSI_?` variables, follow the steps in [Collecting parameters for configuring a high availability cluster](/docs/sap?topic=sap-ha-vsi#ha-rhel-collect-parameters-for-cluster-config).

## Installing and configuring a RHEL HA Add-On cluster
{: #ha-rhel-mz-install-and-configure-rhel-ha-cluster}

Use the following steps to set up a two-node cluster for an IBM {{site.data.keyword.powerSys_notm}}.

The instructions are based on the Red Hat product documentation and articles that are listed in [Implementing high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

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

See [Checking the RHEL HA repository](/docs/sap?topic=sap-ha-rhel#ha-rhel-check-rhel-ha-repository) for the steps to enable the RHEL HA repository.

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
You can test the agent invocation by using the parameters that you gathered in the [Collecting parameters for configuring a high availability cluster](/docs/sap?topic=sap-ha-vsi#ha-rhel-collect-parameters-for-cluster-config) section.

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
When the cluster performs a fencing action, it triggers an *off* operation instead of a *reboot* for the fenced instance.
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

On NODE1, run the following commands.

```sh
pcs stonith fence ${NODE2}
```
{: pre}

```sh
pcs status
```
{: pre}

As a result, NODE2 stops.

Activate NODE2, then start the cluster on the node and try to fence NODE1.

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

NODE1 stops.

Activate NODE2, then start the cluster on the node.

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

Follow the steps in [Creating a Custom Role, Service ID, and API key in {{site.data.keyword.cloud_notm}}](/docs/sap?topic=sap-ha-vsi#ha-vsi-create-role-and-service-id) to create a `Service ID` and an `API key` for the `powervs-subnet` resource agent.

## Conclusion
{: #ha-rhel-mz-conclusion}

This completes the basic cluster implementation and the necessary preparations for creating a `powervs-subnet` cluster resource.
The `powervs-subnet` cluster resource itself is created during the configuration of the specific high availability scenario.

You can now proceed with the specific instructions for your planned high availability scenario.
