
Decide which resource agent to use for managing virtual IP resources in the cluster.
For details, see [SAP HANA high availability solution in a multizone region environment - Network considerations](/docs/sap?topic=sap-ha-overview#ha-overview-hana-mzr-network).

Complete all steps that are described in [Preparing a multi-zone RHEL HA Add-On cluster for a virtual IP address resource](/docs/sap?topic=sap-ha-rhel-mz#ha-rhel-mz-create-vip).

Use the `pcs resource describe` command to view detailed parameter information for the `powervs-move-ip` or `powervs-subnet` resource agents.
{: note}

If you use the `powervs-move-ip` resource agent, run the following command on NODE1 to create a cluster resource for the ASCS virtual IP address.

```sh
pcs resource create ${sid}_vip_ascs${ASCS_INSTNO} powervs-move-ip \
    api_key=${APIKEY} \
    api_type=${API_TYPE} \
    ip=${ASCS_IP} \
    route_host_map="${NODE1}:${ASCS_ROUTE_CRN1};${NODE2}:${ASCS_ROUTE_CRN2}" \
    region=${CLOUD_REGION} \
    monitor_api=${MON_API}
    op start timeout=60 \
    op stop timeout=60 \
    op monitor interval=60 timeout=60 \
    --group ${sid}_ascs${ASCS_INSTNO}_group
```
{: pre}

Otherwise, run the following command on NODE1 to create a `powervs-subnet` cluster resource for the ASCS virtual IP address.

```sh
pcs resource create ${sid}_vip_ascs${ASCS_INSTNO} powervs-subnet \
    api_key=${APIKEY} \
    api_type=${API_TYPE} \
    cidr=${ASCS_CIDR} \
    ip=${ASCS_IP} \
    crn_host_map="${NODE1}:${IBMCLOUD_CRN_1};${NODE2}:${IBMCLOUD_CRN_2}" \
    vsi_host_map="${NODE1}:${POWERVSI_1};${NODE2}:${POWERVSI_2}" \
    jumbo=${JUMBO} \
    region=${CLOUD_REGION} \
    subnet_name=${ASCS_NET} \
    route_table=5${ASCS_INSTNO} \
    op start timeout=720 \
    op stop timeout=300 \
    op monitor interval=60 timeout=30 \
    --group ${sid}_ascs${ASCS_INSTNO}_group
```
{: pre}
