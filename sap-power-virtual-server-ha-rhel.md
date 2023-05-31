---
copyright:
  years: 2023
lastupdated: "2023-05-31"

keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Implementing a RHEL HA Add-On cluster on {{site.data.keyword.powerSysFull}}
{: #ha-rhel}

Running SAP on IBM {{site.data.keyword.powerSys_notm}} offers a consistent platform for HANA-based and traditional applications, best-in-class performance, resilience for critical workloads, and a flexible infrastructure.
{: shortdesc}

Use the following information and procedures to help you implement a Red Hat High Availability (HA) cluster by using {{site.data.keyword.powerSys_notm}} instances as cluster nodes.

This information doesn't replace existing SAP or Red Hat documentation.
{: note}

## Overview
{: #ha-rhel-overview}

You can find the following information in the proceeding sections.

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

## Creating virtual server instances for the cluster
{: #ha-rhel-create-virtual-server-instances}

Use the instructions in [Creating instances for a high availability cluster on {{site.data.keyword.powerSysFull}}](#ha-vsi) to create the virtual server instances that you want to use as cluster nodes.

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

## Preparing the nodes for RHEL HA Add-On installation
{: #ha-rhel-prepare-nodes-for-rhel-ha-installation}

The following section describes basic preparation steps on the cluster nodes.
Make sure that you follow the steps on both nodes.

Log in as root to each of the cluster nodes.

### Populating entries for each node in the hosts file
{: #ha-rhel-update-hosts-file}

On both nodes, use the following information to populate entries.

Add the IP addresses and hostnames of both nodes to the hosts file `/etc/hosts`.

For more information, see [Setting up /etc/hosts files on RHEL cluster nodes](https://access.redhat.com/solutions/81123){: external}.

### Preparing environment variables
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

### Installing RHEL HA Add-On software
{: #ha-rhel-install-rhel-ha-software}

Install the required software packages.

#### Checking the RHEL HA repository
{: #ha-rhel-check-rhel-ha-repository}

Check that the RHEL High Availability repository is enabled.

On both nodes, use the followings command.

```sh
dnf repolist
```
{: pre}

If the HA repository is missing, use the following commands to enable it:

```sh
subscription-manager repos \
    --enable="rhel-8-for-ppc64le-highavailability-e4s-rpms"
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
pcs stonith create res_fence_ibm_powervs res_fence_ibm_powervs \
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
