---
copyright:
  years: 2026
lastupdated: "2026-10-12"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn, Live Partition Mobility, Live Partition Mobility, HA cluster, Live Partition Mobility maintenance
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# IBM {{site.data.keyword.powerSys_notm}} maintenance operations in high availability clusters
{: #powervs-ha-lpm-overview}

During maintenance operations, an {{site.data.keyword.cloud_notm}} Operations team uses Live Partition Mobility to migrate logical partitions (LPARs) between physical servers without interrupting workloads. This document provides information about how to prepare and manage high availability (HA) clusters during Live Partition Mobility operations.
{: shortdesc}

When you configure virtual server instances (VSIs) for the HA cluster configuration, set a pinning policy to `hard` to help avoid unpredictable VSI behavior during {{site.data.keyword.cloud_notm}} maintenance operations. For more information about how the pinning policy affects VSI availability, see [What does VSI pinning do?](/docs/power-iaas?topic=power-iaas-powervs-faqs#pinning) and [Virtual server pinning and its impacts on VSI availability](/docs/power-iaas?topic=power-iaas-creating-power-virtual-server#vmpinning). The {{site.data.keyword.cloud_notm}} Operations team notifies you of an upcoming Live Partition Mobility operation only when the pinning policy is set to `hard`.

If the pinning policy is set to the `none` or `soft`, the {{site.data.keyword.cloud_notm}} Operations team does not notify you about the Live Partition Mobility.
{: note}

## Preparing the cluster for Live Partition Mobility operation
{: #lpm-preparing-cluster-before-mainenance}

After the {{site.data.keyword.cloud_notm}} Operations team notifies you of an upcoming Live Partition Mobility operation, ensure that the cluster and all associated resources are ready.

Verify the status of the HA cluster and its resources by running the following command:

 ```sh
pcs status --full
```
{: pre}

If the output shows no issues and both cluster nodes are online, complete the following preparation steps:

1. Verify that the virtual server instance is the **secondary** in an SAP HANA system replication solution. The resource must show the `DEMOTED` attribute.

1. Set the HA cluster to maintenance mode by running the following command:

   ```sh
   pcs property set maintenance-mode=true
   ```
   {: pre}

1. Check the cluster resources by running the following command:

   ```sh
   pcs status
   ```
   {: pre}

1. An `SAPHanaController` resource agent monitors and manages the SAP HANA HA environment. For example, the `sap-hana-ha` package provides a new generation of resource agents. The `resource-agents-sap-hana` package for scale-up deployments and the `resource-agents-sap-hana-scaleout` package for scale-out HA configurations contain the classic resource agents and tools. The `SAPHanaController` resource agent replaces the scale-up `SAPHana` resource agent. For more information, see [Introduction to the new resource agents](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html/upgrading_sap_hana_ha_setup_to_the_new_generation_of_resource_agents/asmb_overview_v9-upgrading-ha-resource-agent){: external}.

   The Advanced Next Generation Interface (angi) SAP HANA resource agents are included in the package `sap-hana-ha`. The `SAPHanaController` resource agent name uses the format `rsc_SAPHanaCon_<SAP_SID>_HDB<SAP_INSTANCE_ID>` and contain the string `rsc_SAPHanaCon`.
   For more information about the "angi" resource agents, see [Deploying SAP HANA Scale-Up System Replication High Availability](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html-single/deploying_sap_hana_scale-up_system_replication_high_availability/index#asmb_maintenance_proc_v9-deploying-scale-up-system-replication){: external} in the Red Hat Enterprise Linux documentation.

   Set the `SAPHanaController` resource agent to `unmanaged` status before you start Live Partition Mobility. Run the following command:

   ```sh
   pcs resource unmanage rsc_SAPHanaCon_<SAP_SID>_HDB<SAP_INSTANCE_ID>
   ```
   {: pre}

   The SAP HANA resource agents that are not "angi" have different names. For the resource agent name that you must set to `unmanaged`, see [Updating the SAP HANA instances](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html-single/automating_sap_hana_scale-up_system_replication_using_the_rhel_ha_add-on/index#asmb_maintenance_proc_v9-automating-sap-hana-scale-up-system-replication){: external} in the Red Hat Enterprise Linux for SAP Solutions documentation.

1. Stop the cluster service by running the following command:

   ```sh
   pcs cluster stop
   ```
   {: pre}

1. Change the pinning policy of the virtual server instance (VSI) to `none`. Log in to the {{site.data.keyword.cloud_notm}} user interface and follow the instructions in [Modifying a VSI by using the Power Virtual Server user interface](/docs/power-iaas?topic=power-iaas-modifying-instance#edit-vsi-pinning-placement).


## Restoring the cluster after Live Partition Mobility maintenance
{: #lpm-restoring-cluster-after-mainenance}

After the {{site.data.keyword.cloud_notm}} Operations team completes the Live Partition Mobility maintenance, they notify you that the operation is finished. Then, restore the cluster node to normal operation by completing the following steps:

1. Set the pinning policy of the VSI to `hard`.

1. Start the cluster.

   ```sh
   pcs cluster start
   ```
   {: pre}

1. Refresh the resource status. For example, to refresh the `rsc_SAPHanaCon_<SAP_SID>_HDB<SAP_INSTANCE_ID>` resource, run the following command:

   ```sh
   pcs resource refresh rsc_SAPHanaCon_<SAP_SID>_HDB<SAP_INSTANCE_ID>
   ```
   {: pre}

1. Check the cluster status. Ensure that it is up and running. Then, set the `rsc_SAPHanaCon_<SAP_SID>_HDB<SAP_INSTANCE_ID>` resource to managed mode. For more information, see [Explaining cluster information](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html/deploying_sap_hana_scale-up_system_replication_high_availability/asmb_appendixb_v9-deploying-scale-up-system-replication#con_cluster_info_v9-deploying-scale-up-system-replication){: external}:

   ```sh
   pcs resource manage rsc_SAPHanaCon_<SAP_SID>_HDB<SAP_INSTANCE_ID>
   ```
   {: pre}

1. Remove the cluster from maintenance mode by running the following command:

   ```sh
   pcs property set maintenance-mode=false
   ```
   {: pre}

1. The `SAPHanaSR-showAttr` tool provides an overview of the cluster attributes, the SAP HANA system replication status, and the HANA topology. To check the high availability status, run the following command:

   ```sh
   SAPHanaSR-showAttr
   ```
   {: pre}

   Sample output:

   ```sh
   ...

   Site    lpt        lss mns      opMode    srHook srMode  srPoll srr
   --------------------------------------------------------------------
   ... 30         4   <cluster-node>-2 logreplay SOK    syncmem SOK    S
   ... 1784044304 4   <cluster-node>-1 logreplay PRIM   syncmem PRIM   P

   Host     clone_state roles                        score site    sra srah version     vhost
   ----------------------------------------------------------------------------------------------
   <cluster-node>-1 PROMOTED    master1:master:worker:master 150   ... -   -    2.00.089.01 ...
   <cluster-node>-2 DEMOTED     master1:master:worker:master 100   ... -   -    2.00.089.01 ...
   ```
   {: screen}

   The output contains two cluster nodes where the `<cluster-node>-1` is the primary node and `<cluster-node>-2` is the secondary node. The output also shows that the "srHook" and "srPoll" resource status is "SOK" for the secondary node and the "PRIM" status for the primary node. In addition, the "PROMOTED" and "DEMOTED" identify the primary and secondary cluster nodes.
