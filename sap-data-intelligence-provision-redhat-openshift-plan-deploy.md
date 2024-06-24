---

copyright:
  years: 2021
lastupdated: "2021-04-09"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP Data Intelligence, {{site.data.keyword.cos_full_notm}}, {{site.data.keyword.cos_short}}, {{site.data.keyword.openshiftlong_notm}}, {{site.data.keyword.openshiftshort}}, Red Hat Enterprise Linux, data orchestration, data governance, data integration

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

# Planning your SAP Data Intelligence deployment
{: #rhos-di-planning-items}

Currently, SAP Data Intelligence 3.2 is available for Red Hat OpenShift Container Platform 4.8 in {{site.data.keyword.cloud_notm}}. {{site.data.keyword.cloud_notm}} manages the platform for you; you select the size and number of worker nodes and the geographic locations of one or more data centers.
{: shortdesc}

When planning your deployment, you set the _flavor_ of your worker nodes within your {{site.data.keyword.openshiftshort}} on the {{site.data.keyword.cloud_notm}} cluster. _Flavor_ describes the compute resources, such as number of CPUs or vCPUs, amount of memory, and disk capacity that you order when provisioning your worker nodes. The total number of worker nodes in a cluster determines the compute capacity that is available to your apps in the cluster. For more information, see [Planning your worker node setup](/docs/openshift?topic=openshift-planning_worker_nodes){: external}.

## Red Hat OpenShift Cluster Network Connectivity
{: #rhos-di-network-connectivity}

SAP Data Intelligence allows you to connect a wide range of systems and components, as long as the required network connectivity is in place.

To be sure the connections between systems and components meet the requirements, we strongly recommend to combine planning of the SAP Data Intelligence network layout with planning the systems you want to connect. Planning both aspects jointly ensures that network communication is possible for the required protocols (i.e. required firewall rules, DMZ setups, and so on).

For an enhanced security, we recommend using:
*  Properly encrypted communication for all channels, in particular leveraging transport layer security (TLS). 
*  A common certificate authority (CA) that can be imported as a trusted source in the various components. 

For more information, see the [Administration Guide for SAP Data Intelligence](https://help.sap.com/viewer/b13b5722c8ff4bf9bb097251310031d0/latest/en-US/884ffcd587784ed2a311b2c19feb8410.html){: external}.

For productive setups, we recommend preparing the required SSL certificates using the preferred tools. For non-productive setups, note that the SAP Data Intelligence installer can generate self-signed certificates. 

## Red Hat OpenShift Cluster Storage
{: #rhos-di-storage}

SAP Data Intelligence requires access to persistent storage that is attached to the OpenShift cluster. For importing, exporting, and check-pointing SAP Vora database tables, SAP Data Intelligence requires access to a persistent shared file system or an object store.  In the {{site.data.keyword.cloud_notm}} environment, {{site.data.keyword.cloud_notm}} Object Storage is utilized for that purpose.

## Prerequisites
{: #rhos-di-prereq}

To be able to access the required installation binaries and authorizations to install SAP Data Intelligence
* You need an SAP S-user ID and Download Authorization for login to the [SAP ONE Support Launchpad](https://launchpad.support.sap.com){: external}.
* You need a Red Hat account for login to the [Red Hat Registry Service Accounts](https://access.redhat.com/terms-based-registry/){: external}.

### Next Step
{: #rhos-di-next}

* Continue with [Deploying your OpenShift cluster and jump host](/docs/sap?topic=sap-rhos-di-set-up-cluster).
