
Verify the completion of all steps in the [Preparing a multi-zone RHEL HA Add-On cluster for a virtual IP address resource](/docs/sap?topic=sap-ha-rhel-mz#ha-rhel-mz-create-vip) section.

To simplify the virtual IP address setup, set the following environment variables on NODE1.
These variables are required in addition to the environment variables used to create the cluster and stonith devices, and are used with the `pcs resource create` command.

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

Ensure that both virtual server instances in the cluster have the status `Active` and the health status `OK` before running the `pcs resource config` command.
{: important}

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
