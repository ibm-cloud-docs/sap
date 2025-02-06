---
copyright:
  years: 2024
lastupdated: "2025-02-06"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, NFS Server, Linux

subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}

# Implementing High Availability for SAP Applications on IBM {{site.data.keyword.powerSys_notm}} References
{: #ha-rhel-refs}

The following is a comprehensive list of product documentation, Red Hat Knowledge Base articles, and SAP notes that you need to review before you implement high availability for SAP solutions.
A Red Hat Customer Portal ID is required to access Knowledge Base articles and an SAP User ID is required to access SAP Notes.

## General requirements
{: #ha-rhel-refs-requirements}

- An [IBM Cloud](/docs/account?topic=account-account-getting-started){: external} account
- An [SAP for Me](https://me.sap.com/home/product){: external} account
- A [Red Hat Customer Portal](https://access.redhat.com/){: external} account

A valid *RHEL for SAP Applications* or *RHEL for SAP Solutions* subscription is required to enable the repositories that you need to install SAP HANA and the resource agents for HA configurations.

## Red Hat Enterprise Linux Cluster product documentation
{: #ha-rhel-refs-rhel-ha-product-docs}

| Product Documentation |
| --- |
| [Configuring and managing high availability clusters](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html/configuring_and_managing_high_availability_clusters/index){: external} |
{: class="simple-tab-table"}
{: caption="RHEL 8 cluster documentation" caption-side="bottom"}
{: #rhel-ha-docs-table-rhel8}
{: tab-title="RHEL 8"}
{: tab-group="rhel-ha-docs"}

| Product Documentation |
| --- |
| [Configuring and managing high availability clusters](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/configuring_and_managing_high_availability_clusters/index){: external} |
{: class="simple-tab-table"}
{: caption="RHEL 9 cluster documentation" caption-side="bottom"}
{: #rhel-ha-docs-table-rhel9}
{: tab-title="RHEL 9"}
{: tab-group="rhel-ha-docs"}

## Red Hat Enterprise Linux for SAP Solutions product documentation
{: #ha-rhel-refs-rhel-sap-product-docs}

| Product Documentation |
| --- |
| [Configuring RHEL 8 for SAP HANA2 installation](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/8/html/configuring_rhel_8_for_sap_hana2_installation/index){: external} |
| [Red Hat HA Solutions for SAP HANA, S/4HANA, and NetWeaver based SAP Applications](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/8/html/red_hat_ha_solutions_for_sap_hana_s4hana_and_netweaver_based_sap_applications/index){: external} |
| [Automating SAP HANA Scale-Up System Replication by using the RHEL HA Add-On](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/8/html/automating_sap_hana_scale-up_system_replication_using_the_rhel_ha_add-on/index){: external} |
| [Configuring a Cost-Optimized SAP S/4HANA HA cluster (HANA System Replication + ENSA2) by using the RHEL HA Add-On](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/8/html/configuring_a_cost-optimized_sap_s4hana_ha_cluster_hana_system_replication_ensa2_using_the_rhel_ha_add-on/index){: external} |
| [Configuring SAP HANA Scale-Up Multitarget System Replication for disaster recovery](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/8/html/configuring_sap_hana_scale-up_multitarget_system_replication_for_disaster_recovery/index){: external} |
| [Configuring an Active-Passive NFS server in a Red Hat High Availability cluster](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html/configuring_and_managing_high_availability_clusters/assembly_configuring-active-passive-nfs-server-in-a-cluster-configuring-and-managing-high-availability-clusters#doc-wrapper){: external} |
{: class="simple-tab-table"}
{: caption="RHEL 8 for SAP documentation" caption-side="bottom"}
{: #rhel-sap-8-docs-table}
{: tab-title="RHEL 8"}
{: tab-group="rhel-sap-docs"}

| Product Documentation |
| --- |
| [Installing RHEL 9 for SAP Solutions](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html/installing_rhel_9_for_sap_solutions/index){: external} |
| [Red Hat HA Solutions for SAP HANA, S/4HANA, and NetWeaver based SAP Applications](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html/red_hat_ha_solutions_for_sap_hana_s4hana_and_netweaver_based_sap_applications/index){: external} |
| [Automating SAP HANA Scale-Up System Replication by using the RHEL HA Add-On](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html/automating_sap_hana_scale-up_system_replication_using_the_rhel_ha_add-on/index){: external} |
| [Configuring SAP HANA Scale-Up Multitarget System Replication for disaster recovery](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html/configuring_sap_hana_scale-up_multitarget_system_replication_for_disaster_recovery/index){: external} |
| [Configuring HA clusters to manage SAP NetWeaver or SAP S/4HANA Application server instances by using the RHEL HA Add-On](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html/configuring_ha_clusters_to_manage_sap_netweaver_or_sap_s4hana_application_server_instances_using_the_rhel_ha_add-on/index){: external} |
{: class="simple-tab-table"}
{: caption="RHEL 9 for SAP documentation" caption-side="bottom"}
{: #rhel-sap-9-docs-table}
{: tab-title="RHEL 9"}
{: tab-group="rhel-sap-docs"}

## Red Hat Enterprise Linux general cluster knowledge base articles
{: #ha-rhel-refs-cluster-articles}

- [Support Policies for RHEL High Availability Clusters](https://access.redhat.com/articles/2912891){: external}
- [Support Policies for RHEL High Availability Clusters - General Requirements for Fencing/STONITH](https://access.redhat.com/articles/2881341){: external}
- [Support Policies for RHEL High Availability Clusters - IBM Power Systems Virtual Server (PowerVS) Virtual Machines as Cluster Members](https://access.redhat.com/articles/5651561){: external}
- [Available Fencing Types and Fencing Agents for a Red Hat High-Availability Cluster](https://access.redhat.com/articles/2295961){: external}
- [Configuring a RHEL HA Cluster Fence Agent for an IBM {{site.data.keyword.powerSys_notm}}](https://access.redhat.com/articles/6966644){: external}
- [How to configure HA-LVM Cluster by using system_id in RHEL 8 and above](https://access.redhat.com/solutions/3796221){: external}


## Red Hat Enterprise Linux for SAP cluster knowledge base articles
{: #ha-rhel-refs-sap-cluster-articles}

- [Support Policies for RHEL High Availability Clusters - Management of SAP HANA in a Cluster](https://access.redhat.com/articles/3397471){: external}
- [Automating SAP HANA Scale-Up System Replication by using the RHEL HA Add-On](https://access.redhat.com/articles/3004101){: external}
- [Support Policies for RHEL High Availability Clusters - Management of SAP S/4HANA in a cluster](https://access.redhat.com/articles/4016901){: external}
- [Configuring SAP S/4HANA ASCS/ERS with Standalone Enqueue Server 2 (ENSA2) in Pacemaker](https://access.redhat.com/articles/3974941){: external}
- [The Systemd-Based SAP Startup Framework](https://access.redhat.com/articles/6884531){: external}
- [Pacemaker cluster does not trigger a takeover of HANA System Replication when the `hdbindexserver` process of the primary HANA instance hangs/crashes](https://access.redhat.com/solutions/5472351){: external}

## SAP HANA product documentation
{: #ha-rhel-refs-sap-hana-product-docs}

- [SAP HANA Server Installation and Update Guide](https://help.sap.com/docs/SAP_HANA_PLATFORM/2c1988d620e04368aa4103bf26f17727/7eb0167eb35e4e2885415205b8383584.html){: external}
- [SAP HANA Administration Guide](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/330e5550b09d4f0f8b6cceb14a64cd22.html){: external}
- [SAP HANA System Replication](https://help.sap.com/docs/SAP_HANA_PLATFORM/4e9b18c116aa42fc84c7dbfd02111aba/afac7100bc6d47729ae8eae32da5fdec.html){: external}
- [SAP HANA System Replication - Active/Active (Read Enabled)](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/fe5fc53706a34048bf4a3a93a5d7c866.html){: external}
- [SAP HANA SQL and System Views Reference Guide](https://help.sap.com/docs/SAP_HANA_PLATFORM/4fe29514fd584807ac9f2a04f6754767/b4b0eec1968f41a099c828a4a6c8ca0f.html){: external}
- [Implementing a HA/DR Provider](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/1367c8fdefaa4808a7485b09815ae0f3.html){: external}

## SAP Notes
{: #ha-rhel-refs-sap-notes}

| SAP Notes |
| --- |
| [SAP Note 2772999 - Red Hat Enterprise Linux 8.x: Installation and Configuration](https://me.sap.com/notes/2772999){: external} |
| [SAP Note 2235581 - SAP HANA: Supported Operating Systems](https://me.sap.com/notes/2235581){: external} |
| [SAP Note 2777782 - SAP HANA DB: Recommended OS Settings for RHEL 8](https://me.sap.com/notes/2777782){: external} |
| [SAP Note 2369981 - Required configuration steps for authentication with HANA System Replication](https://me.sap.com/notes/2369981){: external} |
| [SAP note 3115048 - sapstartsrv with native Linux systemd support](https://me.sap.com/notes/3115048){: external} |
| [SAP note 3139184 - Linux: systemd integration for sapstartsrv and SAP Host Agent](https://me.sap.com/notes/3139184){: external} |
{: class="simple-tab-table"}
{: caption="SAP Notes for RHEL 8" caption-side="bottom"}
{: #rhel-sap-notes-table-rhel8}
{: tab-title="RHEL 8"}
{: tab-group="rhel-sap-notes"}

| SAP Notes |
| --- |
| [SAP Note 3108316 - Red Hat Enterprise Linux 9.x: Installation and Configuration](https://me.sap.com/notes/3108316){: external} |
| [SAP Note 2235581 - SAP HANA: Supported Operating Systems](https://me.sap.com/notes/2235581){: external} |
| [SAP Note 3108302 - SAP HANA DB: Recommended OS Settings for RHEL 9](https://me.sap.com/notes/3108302){: external} |
| [SAP Note 2369981 - Required configuration steps for authentication with HANA System Replication](https://me.sap.com/notes/2369981){: external} |
| [SAP note 3115048 - sapstartsrv with native Linux systemd support](https://me.sap.com/notes/3115048){: external} |
| [SAP note 3139184 - Linux: systemd integration for sapstartsrv and SAP Host Agent](https://me.sap.com/notes/3139184){: external} |
{: class="simple-tab-table"}
{: caption="SAP Notes for RHEL 9" caption-side="bottom"}
{: #rhel-sap-notes-table-rhel9}
{: tab-title="RHEL 9"}
{: tab-group="rhel-sap-notes"}
