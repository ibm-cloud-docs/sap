

On NODE1, run the following command to create a `powervs-subnet` cluster resource for the ERS virtual IP address.

```sh
pcs resource create ${sid}_vip_ers${ERS_INSTNO} powervs-subnet \
    api_key=${APIKEY} \
    api_type=${API_TYPE} \
    cidr=${ERS_CIDR} \
    ip=${ERS_IP} \
    crn_host_map="${NODE1}:${IBMCLOUD_CRN_1};${NODE2}:${IBMCLOUD_CRN_2}" \
    vsi_host_map="${NODE1}:${POWERVSI_1};${NODE2}:${POWERVSI_2}" \
    jumbo=${JUMBO} \
    region=${CLOUD_REGION} \
    subnet_name=${ERS_NET} \
    route_table=5${ERS_INSTNO} \
    op start timeout=720 \
    op stop timeout=300 \
    op monitor interval=60 timeout=30 \
    --group ${sid}_ers${ERS_INSTNO}_group
```
{: pre}

<preview>

If you use the `powervs-move-ip` resource agent, run the following command on NODE1 to create a cluster resource for the ERS virtual IP address.

```sh
pcs resource create ${sid}_vip_ers${ERS_INSTNO} powervs-move-ip \
    api_key=${APIKEY} \
    api_type=${API_TYPE} \
    ip=${ERS_IP} \
    route_host_map="${NODE1}:${ERS_ROUTE_CRN1};${NODE2}:${ERS_ROUTE_CRN2}" \
    region=${CLOUD_REGION} \
    monitor_api=${MON_API}
    op start timeout=60 \
    op stop timeout=60 \
    op monitor interval=60 timeout=60 \
    --group ${sid}_ers${ERS_INSTNO}_group
```
{: pre}

Otherwise, run the following command on NODE1 to create a `powervs-subnet` cluster resource for the ERS virtual IP address.

```sh
pcs resource create ${sid}_vip_ers${ERS_INSTNO} powervs-subnet \
    api_key=${APIKEY} \
    api_type=${API_TYPE} \
    cidr=${ERS_CIDR} \
    ip=${ERS_IP} \
    crn_host_map="${NODE1}:${IBMCLOUD_CRN_1};${NODE2}:${IBMCLOUD_CRN_2}" \
    vsi_host_map="${NODE1}:${POWERVSI_1};${NODE2}:${POWERVSI_2}" \
    jumbo=${JUMBO} \
    region=${CLOUD_REGION} \
    subnet_name=${ERS_NET} \
    route_table=5${ERS_INSTNO} \
    op start timeout=720 \
    op stop timeout=300 \
    op monitor interval=60 timeout=30 \
    --group ${sid}_ers${ERS_INSTNO}_group
```
{: pre}

</preview>
