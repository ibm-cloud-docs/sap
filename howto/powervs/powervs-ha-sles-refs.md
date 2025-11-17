---
copyright:
  years: 2025
lastupdated: "2025-11-17"
keywords: SAP, SAP-Certified Infrastructure, SAP Workloads, NFS Server, Linux, SUSE, SLES, SLES High Availability Extension, SLES HAE
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Implement high availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}} SLES References
{: #ha-sles-refs}

The following information is a comprehensive list of product documentation, SLES Knowledge Base articles, and SAP notes that you need to review before you implement high availability for SAP solutions.
A SUSE Customer Portal ID is required to access Knowledge Base articles and an SAP user ID is required to access SAP Notes.

## General requirements
{: #ha-sles-refs-requirements}

See the following general requirements before you begin.

- An [IBM Cloud](/docs/account?topic=account-account-getting-started){: external} account
- An [SAP for Me](https://me.sap.com/home/product){: external} account
- A [SUSE Customer Center](https://scc.suse.com/home/){: external} account
- A valid *SUSE Linux Enterprise Server for SAP applications* license is required to enable the repositories that you need to install SAP HANA and the resource agents for HA configurations.

## SUSE Enterprise Linux Cluster product documentation
{: #ha-sles-refs-sles-ha-product-docs}

For more information about SUSE Enterprise Linux Cluster, see the following documentation.

- [Supported HA solutions](https://documentation.suse.com/sles-sap/sap-ha-support/){: external}
- [Setting up an SAP HANA cluster](https://documentation.suse.com/sles-sap/15-SP6/html/SLES-SAP-guide/cha-cluster.html){: external}

## SUSE Linux Enterprise Server for SAP applications product documentation
{: #ha-sles-refs-sles-sap-product-docs}

For more information about SUSE Linux Enterprise Server for SAP applications, see the following documentation.

- [SUSE Linux Enterprise Server for SAP applications - Guide](https://documentation.suse.com/sles-sap/15-SP7/html/SLES-SAP-guide/index.html){: external}
- [Installation Quick Start](https://documentation.suse.com/sles-sap/15-SP7/html/SLES-SAP-installation/index.html){: external}
- [SAP Automation](https://documentation.suse.com/sles-sap/15-SP7/html/SLES-SAP-automation/article-sap-automation.html){: external}
- [An overview of supported High Availability Solutions by SLES for SAP applications](https://documentation.suse.com/sles-sap/sap-ha-support/html/sap-ha-support/article-sap-ha-support.html){: external}
- [SAP HANA System Replication Scale-Up - Cost Optimized Scenario](https://documentation.suse.com/sbp/sap-15/html/SLES4SAP-hana-sr-guide-costopt-15/index.html){: external}
- [SAP HANA System Replication Scale-Up - Performance Optimized Scenario](https://documentation.suse.com/sbp/sap-15/html/SLES4SAP-hana-sr-guide-PerfOpt-15/){: external}
- [SAP HANA System Replication Scale-Out - Performance Optimized Scenario](https://documentation.suse.com/sbp/sap-15/html/SLES4SAP-hana-scaleOut-PerfOpt-15/index.html){: external}
- [SAP HANA System Replication Scale-Out - Multi-Target Performance-Optimized Scenario](https://documentation.suse.com/sbp/sap-15/html/SLES4SAP-hana-scaleout-multitarget-perfopt-15/index.html){: external}

## SUSE Linux Enterprise server for SAP cluster knowledge base article
{: #ha-sles-refs-sap-cluster-articles}

For more information about SUSE Linux Enterprise server for SAP cluster, see [How to prevent pacemaker from auto-starting on boot or after a fence](https://support.scc.suse.com/s/kb/How-to-prevent-pacemaker-from-auto-starting-on-boot-or-after-a-fence?language=en_US){: external}.

## SAP HANA product documentation
{: #ha-sles-refs-sap-hana-product-docs}

For more information about SAP HANA, see the following documentation.

- [SAP HANA Server Installation and Update Guide](https://help.sap.com/docs/SAP_HANA_PLATFORM/2c1988d620e04368aa4103bf26f17727/7eb0167eb35e4e2885415205b8383584.html){: external}
- [SAP HANA Administration Guide](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/330e5550b09d4f0f8b6cceb14a64cd22.html){: external}
- [SAP HANA System Replication](https://help.sap.com/docs/SAP_HANA_PLATFORM/4e9b18c116aa42fc84c7dbfd02111aba/afac7100bc6d47729ae8eae32da5fdec.html){: external}
- [SAP HANA System Replication - Active/Active (Read Enabled)](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/fe5fc53706a34048bf4a3a93a5d7c866.html){: external}
- [SAP HANA SQL and System Views Reference Guide](https://help.sap.com/docs/SAP_HANA_PLATFORM/4fe29514fd584807ac9f2a04f6754767/b4b0eec1968f41a099c828a4a6c8ca0f.html){: external}
- [Implementing a HA/DR Provider](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/1367c8fdefaa4808a7485b09815ae0f3.html){: external}

## SAP Notes
{: #ha-sles-refs-sap-notes}

The following items are SAP Notes that you need to be aware of.

- [SAP Note 2578899 - SUSE Linux Enterprise Server 15: Installation Note](https://me.sap.com/notes/2578899){: external}
- [SAP Note 2235581 - SAP HANA: Supported Operating Systems](https://me.sap.com/notes/2235581){: external}
- [SAP Note 2684254 - Recommended OS settings for SLES 15 / SLES for SAP Applications 15](https://me.sap.com/notes/2684254){: external}
- [SAP Note 1763512 - Support details for SUSE Linux Enterprise for SAP Applications - HA Solution for SAP NetWeaver and SAP S/4HANA](https://me.sap.com/notes/1763512){: external}
- [SAP Note 2369981 - Required configuration steps for authentication with HANA System Replication](https://me.sap.com/notes/2369981){: external}
- [SAP note 3115048 - sapstartsrv with native Linux systemd support](https://me.sap.com/notes/3115048){: external}
- [SAP note 3139184 - Linux: systemd integration for sapstartsrv and SAP Host Agent](https://me.sap.com/notes/3139184){: external}
