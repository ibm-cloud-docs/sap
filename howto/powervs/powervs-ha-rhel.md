---
copyright:
  years: 2023, 2025
lastupdated: "2025-02-24"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Implementing a RHEL HA Add-On Cluster
{: #ha-rhel}

Use the following information and procedures to implement a Red Hat Enterprise Linux (RHEL) High Availability (HA) cluster.
The cluster uses instances in [{{site.data.keyword.powerSysFull}}](https://www.ibm.com/products/power-virtual-server){: external} as cluster nodes.

The information describes how to transform the individual virtual server instances into a cluster.
{: shortdesc}

These procedures include installing the high availability packages and agents on each cluster node and configuring the fencing devices.

This information is intended for architects and specialists who are planning a high availability deployment of SAP applications on {{site.data.keyword.powerSys_notm}}. It is not intended to replace existing SAP or Red Hat documentation.
{: note}

## Before you begin
{: #ha-rhel-begin}

Review the general requirements, product documentation, support articles, and SAP notes listed in [Implementing High Availability for SAP Applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

## Creating virtual server instances for the cluster
{: #ha-rhel-create-virtual-server-instances}

Use the instructions in [Creating Instances for a High Availability Cluster on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-ha-vsi) to create the virtual server instances that you want to use as cluster nodes.

## Preparing the nodes for RHEL HA Add-On installation
{: #ha-rhel-prepare-nodes-for-rhel-ha-installation}

The following section describes basic preparation steps on the cluster nodes.
Make sure that you follow the steps on both nodes.

Log in as the root user to each of the cluster nodes.

### Adding cluster node entries to the hosts file
{: #ha-rhel-update-hosts-file}

On both nodes, add the IP addresses and hostnames of both nodes to the `/etc/hosts` file.

For more information, see [Setting up /etc/hosts files on RHEL cluster nodes](https://access.redhat.com/solutions/81123){: external}.

### Preparing environment variables
{: #ha-rhel-prepare-environment-variables}

To simplify the setup process, prepare some environment variables for the root user.
These environment variables are used with later operating system commands in this information.

On both nodes, create a file with the following environment variables and update to your environment.

```sh
# General settings
export CLUSTERNAME="SAP_CLUSTER"         # Cluster name

export APIKEY=<APIKEY>                   # API Key of the IBM Cloud IAM ServiceID for the fencing agent
export CLOUD_REGION=<CLOUD_REGION>       # Workspace region
export PROXY_IP=<IP_ADDRESS>             # Proxy server IP address

# Workspace 1
export IBMCLOUD_CRN_1=<IBMCLOUD_CRN_1>   # Workspace CRN
export GUID_1=<GUID_1>                   # Workspace GUID

# Virtual server instance 1
export NODE1=<HOSTNAME_1>                # Virtual server instance hostname
export POWERVSI_1=<POWERVSI_1>           # Virtual server instance id

# Virtual server instance 2
export NODE2=<HOSTNAME_2>                # Virtual server instance hostname
export POWERVSI_2=<POWERVSI_2>           # Virtual server instance id
```
{: codeblock}

To find the settings for the `APIKEY`, `IBMCLOUD_CRN_1`, `GUID_1`, and `POWERVSI_?` variables, follow the steps in [Collecting parameters for configuring a RHEL HA Add-On cluster](/docs/sap?topic=sap-ha-vsi#ha-rhel-collect-parameters-for-cluster-config).

## Installing and configuring a RHEL HA Add-On cluster
{: #ha-rhel-install-and-configure-rhel-ha-cluster}

Use the following steps to set up a two-node cluster for an IBM {{site.data.keyword.powerSys_notm}}.

The instructions are based on the Red Hat product documentation and articles that are listed in [Implementing High Availability for SAP Applications on IBM {{site.data.keyword.powerSys_notm}} References](/docs/sap?topic=sap-ha-rhel-refs).

You need to perform some steps on both nodes and some steps on either NODE1 or on NODE2.
{: tip}

### Installing RHEL HA Add-On software
{: #ha-rhel-install-rhel-ha-software}

Install the required software packages.

#### Checking the RHEL HA repository
{: #ha-rhel-check-rhel-ha-repository}

Check that the RHEL High Availability repository is enabled.

On both nodes, use the following command.

```sh
dnf repolist
```
{: pre}

Use the following command to enable the HA repository if it is missing.

```sh
subscription-manager repos \
    --enable="rhel-8-for-ppc64le-highavailability-e4s-rpms"
```
{: pre}

For RHEL 9, use this command.
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
{: #ha-rhel-install-ha-software-packages}

Install the required software packages.

On both nodes, run the following command.

```sh
dnf install -y pcs pacemaker fence-agents-ibm-powervs
```
{: pre}

Make sure that you install the minimal version of the *fence-agents-ibm-powervs* package dependent on your Red Hat Enterprise Linux release:

RHEL 8
:   fence-agents-ibm-powervs-4.2.1-121.el8

RHEL 9
:   fence-agents-ibm-powervs-4.10.0-43.el9

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

Start the PCS daemon that is used for controlling and configuring RHEL HA Add-On clusters through PCS.

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

STONITH is an acronym for "Shoot The Other Node In The Head" and protects your data from corruption in a split-brain situation.

You must enable STONITH (fencing) for a RHEL HA Add-On production cluster.
{: important}

Fence agent *fence_ibm_powervs* is the only supported agent for a STONITH device on {{site.data.keyword.powerSys_notm}} clusters.

The fence agent connects to the [Power Cloud API](https://cloud.ibm.com/apidocs/power-cloud){: external} by using parameters *APIKEY*, *IBMCLOUD_CRN_1*, *CLOUD_REGION*, *GUID*, and the instance IDs *POWERVSI_1* and *POWERVSI_2*.

You can test the agent invocation by using the parameters that you gathered in the [Collecting parameters for configuring a RHEL HA Add-On cluster](/docs/sap?topic=sap-ha-vsi#ha-rhel-collect-parameters-for-cluster-config) section.

#### Identifying the virtual server instances for fencing
{: #ha-rhel-identify-virtual-instances-for-fencing}

Use the *list* option of *fence_ibm_powervs* to identify and or verify the instance IDs of the two cluster nodes:

On any node, run the following command.

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

If the virtual server instances have access to only a private network, you must use the *--api-type=private* option, which also requires an extra *--proxy* option.

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

Continue by using *--api-type=private* in the following examples.

#### Checking the status of both virtual server instances
{: #ha-rhel-check-virtual-instances-status}

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
    --crn=${IBMCLOUD_CRN_1} \
    --instance=${GUID_1} \
    --region=${CLOUD_REGION} \
    --plug=${POWERVSI_2} \
    --api-type=private \
    --proxy=http://${PROXY_IP}:3128 \
    -o status
```
{: pre}

The `status` action of the fence agent against a virtual server instance {pvm_instance_id} displays its power status.

On both nodes, the two commands must report `Status: ON`.

The output of the `time` command might be useful later when you choose timeouts for the STONITH device.

You can add the `-v` flag for verbose output, which shows more information about connecting to the Power Cloud API and querying virtual server power status.

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
pcs stonith create res_fence_ibm_powervs fence_ibm_powervs \
    token=${APIKEY} \
    crn=${IBMCLOUD_CRN_1} \
    instance=${GUID_1} \
    region=${CLOUD_REGION} \
    api_type=private \
    proxy=http://${PROXY_IP}:3128 \
    pcmk_host_map="${NODE1}:${POWERVSI_1};${NODE2}:${POWERVSI_2}" \
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
{: #ha-rhel-set-stonith-action-cluster-property}

To speed up failover times in an IBM {{site.data.keyword.powerSys_notm}} cluster, you can change the cluster property *stonith-action* to *off*.
When the cluster performs a fencing action, it triggers a *power off* operation instead of a *reboot* for the fenced instance.

After this change, you always need to log in to the IBM Cloud Console, and manually start an instance that was fenced by the cluster.

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
{: #ha-rhel-test-fencing-operations}

To test the STONITH configuration, you need to manually fence the nodes.

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
