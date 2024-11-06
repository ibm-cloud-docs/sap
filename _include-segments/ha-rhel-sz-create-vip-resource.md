
Review the information in [Reserving virtual IP addresses](/docs/sap?topic=sap-ha-vsi#ha-vsi-reserve-virtual-ip-addresses) and reserve a virtual IP address for the SAP HANA System Replication cluster.

Use the reserved IP address to create a virtual IP address cluster resource.
This virtual IP address is used to reach the SAP HANA System Replication primary instance.

On NODE1, assign the reserved IP address to the `VIP` environment variable.

```sh
export VIP=<reserved IP address>
```
{: screen}

```sh
echo $VIP
```
{: pre}

Create the virtual IP address cluster resource with the following command.

```sh
pcs resource create vip_${SID}_${INSTNO} IPaddr2 ip=$VIP
```
{: pre}

Check the configured virtual IP address cluster resource and the cluster status.

```sh
pcs resource config vip_${SID}_${INSTNO}
```
{: pre}

```sh
pcs status --full
```
{: pre}
