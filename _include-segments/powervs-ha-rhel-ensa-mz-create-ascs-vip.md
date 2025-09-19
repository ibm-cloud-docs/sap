

On NODE1, run the following command to create a `powervs-subnet` cluster resource for the ASCS virtual IP address.

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
