---

copyright:
  years: 2020
lastupdated: "2020-09-21"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP Data Hub, {{site.data.keyword.cos_full_notm}}, {{site.data.keyword.cos_short}}, {{site.data.keyword.openshiftlong_notm}}, {{site.data.keyword.openshiftshort}}, Red Hat Enterprise Linux, SAP Data Hub on {{site.data.keyword.cloud_notm}}, data orchestration, data governance, data integration

subcollection: sap

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:note: .note}
{:tip: .tip}

# Planning your deployment
{: #rhos-planning-items}

SAP Data Hub is available for Red Hat OpenShift Container Platform 3.11. {{site.data.keyword.cloud_notm}} manages the platform for you; you chose the size and number of worker nodes and the geographic location of the data center.
{: shortdesc}

When Planning your deployment, you set the flavor of your worker nodes within your {{site.data.keyword.openshiftshort}} cluster. Flavor  describes the compute resources, such as CPU or vCPU, memory, and disk capacity that you order when you provision your worker node. Worker nodes of the same flavor are grouped in worker node pools. The total number of worker nodes in a cluster determines the compute capacity that is available to your apps in the cluster. For more information, see [Planning your worker node setup](/docs/openshift?topic=openshift-planning_worker_nodes){: external}.

## Red Hat OpenShift Cluster Network Connectivity
{: #rhos-network-connectivity}

SAP Data Hub allows you to connect a wide range of systems and components, as long as the required network connectivity is in place.

To be sure the connections between systems and components meet requirements, we recommend that you combine planning the SAP Data Hub network layout with planning the systems you want to connect. Planning everything together helps ensure that network communication is possible for the required protocols (such as firewall rules, DMZ setups, and so on).

For security, we recommend using:
*  Properly encrypted communication for all channels, in particular, leveraging transport layer security (TLS). 
*  A common certificate authority (CA) that can be imported as a trusted source in the various components. 

For more information, see the [Administration Guide for SAP Data Hub](https://help.sap.com/doc/70ce58a2ed23404fac27ae36c8f76900/2.7.latest/en-US/loio70ce58a2ed23404fac27ae36c8f76900.pdf){: external}.

For productive setups, we recommend preparing the required SSL certificates using the preferred tools. For non-productive setups, note that the SAP Data Hub installer can generate self-signed certificates. 

##  Red Hat OpenShift Cluster Storage
{: #rhos-storage}

SAP Data Hub requires access to persistent storage that is attached to the OpenShift cluster. For importing, exporting, and checkpointing SAP Vora database tables, SAP Data Hub requires access to a persistent shared file system or object store.  In the {{site.data.keyword.cloud_notm}} environment you will use {{site.data.keyword.cloud_notm}} Object Storage,
