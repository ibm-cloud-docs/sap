---

copyright:
  years: 2020
lastupdated: "2020-09-21"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, VPC, SAP NetWeaver, SAP HANA, {{site.data.keyword.IBM}} Metrics Collector for SAP, IMCS

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
{:important: .important}

# Using {{site.data.keyword.IBM_notm}} Metrics Collector for SAP (IMCS) on Linux
{: #ibm-metrics-collector-for-sap-linux}

The {{site.data.keyword.IBM}} Metrics Collector for SAP (IMCS) on Linux is only a requirement by SAP Support for {{site.data.keyword.vpc_full}} Infrastructure
{: note}



{{site.data.keyword.IBM}} Metrics Collector for SAP (IMCS) collects performance-related data from {{site.data.keyword.vsi_is_full}} for SAP. The SAP Support Team uses the collected metric data to monitor, troubleshoot, and improve performance of business transactions. Use the following information to help with installing, configuring, and troubleshooting the {{site.data.keyword.IBM_notm}} Metrics Collector for SAP on Linux&reg;.
{: shortdesc}

## Before you begin
{: #before-you-begin-imcs}

You need to successfully create an {{site.data.keyword.vpc_full}} and {{site.data.keyword.vsi_is_short}} by using the appropriate catalog image for SAP. Check [SAP Note 2927211](https://launchpad.support.sap.com/#/notes/2927211) to make sure that the selected operating system is supported by SAP. The Metrics Collector runs specifically on {{site.data.keyword.vsi_is_short}} to gather required SAP metrics. Figure 1 outlines the data sources that are used by {{site.data.keyword.IBM_notm}} Metrics Collector for SAP.

![Figure 1. Data sources for {{site.data.keyword.IBM_notm}} Metrics Collector for SAP](/images/imcs-data-sources.svg "Data sources for IBM Metrics Collector for SAP"){: caption="Figure 1. Data sources for {{site.data.keyword.IBM_notm}} Metrics Collector for SAP" caption-side="bottom"}


## Getting an {{site.data.keyword.cloud_notm}} API key
{: #get-api-key}

You need an {{site.data.keyword.cloud_notm}} API key for IMCS to successfully collect all required metrics. The API key grants view access to {{site.data.keyword.cloud_notm}} infrastructure services. You can install IMCS without an API key. However, some metrics are missing and the virtual server is not supported by SAP.

You need to create only one API Key per Account. You can use the same API Key for all the Metric Collectors that are installed in the virtual server associated to the Account.
{: note}

For a list of missing metrics, see [Additional Information](#additional-information).

### Creating a Service ID
{: #service-id}

You need to first create a Service ID to get an API key. Use the following steps to create a Service ID.

You need only one service ID per account.
{: important}

1. Sign in to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com){: external} and click **Manage** > **Access (IAM)**.

   Figure 2. Open the Access (IAM page)

   ![Figure 2. Open the Access (IAM) page](/images/imcs-ibmcloud-access-iam.png "Open the Access (IAM) page"){: caption="Figure 2. Open the Access (IAM) page" caption-side="bottom"}

1. Click **Services IDs** > **Create**.
1. Enter a **Name** and **Description** for the Service ID and click **Create**. You can assign the Access Policy after your Service ID is created.
1. Click **Access Policy** > **Assign Access**.
1. Click **IAM Services** for **Assign Service ID additional access**.
1. Select **Infrastructure service** for **What type of access do you want to assign?**
1. Select **All resource groups** for **in**
1. Click **Viewer** for **Platform Access**.
1. Click **Add** > **Assign**. The Infrastructure Service policy is assigned to your Service ID.

### Creating an API Key for the Service ID.
{: #api-key}

Use the following steps to create an API Key for the new Service ID.

1. Select **Service IDs** and verify that the Infrastructure Service is listed as an Access Policy. If not repeat steps 4-9.
1. Click **API keys** > **Create**.
1. Enter a **Name** and **Description** for the key and click **Create**.
1. Click **Copy** or **Download** your key.

## Installing the {{site.data.keyword.IBM_notm}} Metrics Collector for SAP on Linux
{: #install-imcs-linux}

The {{site.data.keyword.IBM_notm}} Metrics Collector for SAP is a daemon or service that automatically starts as soon as it is installed and requires an API key. It collects metrics from a virtual server's metadata, {{site.data.keyword.cloud_notm}} infrastructure services, runtime data about resources, such as CPU, memory, network, and disk. The metrics are aggregated and displayed through the web server for SAP customers. SAPOSCOL uses the XML output of this web server.

The {{site.data.keyword.IBM_notm}} Metrics Collector for SAP uses port 18181 to display the metrics. Make sure that `port 18181` is not used by any other application. For more information about checking port availability, see [Troubleshooting](#troubleshooting).
{: important}

The commands listed below were run on a Red Hat VSI. If you have a SUSE VSI you may follow the same steps but will probably see slightly different UI.
{:note}

Use the following steps to download the IMCS.

1. [Download the IMCS](ftp://public.dhe.ibm.com/cloud/bluemix/sap/linux/){: external}.
1. Select the appropriate `tar.gz` file. In most cases, use the current version. Connect as **guest**.
1. Save the file to your internal Downloads folder and click **OK**.
1. Move or copy the IMCS `tar.gz` file to your VPC virtual server instance.
1. Extract the file and open the extracted folder.

   ```
   tar -xvf sap-metrics-collector-v1.2.tar.gz
   cd sap
   ```
   {: pre}

1. Run the `install-linux.sh` file.

   ```
   ./install-linux.sh
   ```
   {: pre}
1. Paste your [API key](#api-key) when prompted. If you don't have an API key, see [Getting an {{site.data.keyword.cloud_notm}} API key](#get-api-key).
1. Check to make sure that the `sap-metric-service` is running after the installation is complete. The service status displays `active` when it is ready.

   ```
   sudo systemctl is-active sap-metrics-collector
   ```
   {: pre}

## Verifying data collection
{: #verify-data-collection-linux}

After the installation completes and the service is started, it can take time for the IMCS begins collecting metrics. It will be at least 2 minutes after the installation before you see full and accurate metrics.

1. Run the following `curl` command for your localhost address to see your metrics:

   ```
   [root@(where you installed ICMS)-sap ~]# curl http://localhost:18181/sap/metrics
   ```
   {: pre}

   ```
   <metrics>
       <metric category="config" context="vm" device-id="" last-refresh="refresh time" refresh-interval="0" type="string" unit="none">
           <name>Data Provider Version</name>
           <value>1.0_beta1</value>
       </metric>
       <metric category="config" context="vm" device-id="" last-refresh="refresh time" refresh-interval="0" type="string" unit="none">
           <name>Cloud Provider</name>
           <value>{{site.data.keyword.cloud_notm}}</value>
       </metric>
   ```
   {: screen}   

   There might be a delay before your data is available.
   {: note}

## Troubleshooting
{: #troubleshooting-linux}

Use the following troubleshooting tips for IMCS.

### Uninstalling the Metrics Collector
{: #uninstall-icmc-linux}

1. Run the following command to uninstall IMCS if you have any issues during the installation process. Then, reinstall it.

   ```
   [root@sap-mc-instl sap]# ./uninstall-linux.sh
   ```
   {: pre}

   ```
   Removing IBM Metric Collector for SAP...
   Successfully removed IBM Metric Collector for SAP.
   ```
   {: screen}

### No metrics reported when running `curl` command
{: #no-metrics-linux}

No reported metrics message is often due to the port not assigned to SAP Metrics Collector. It needs port `18181` available for `localhost`. If you have any other applications that use the port, you must close the applications.

1. Use the following command to see whether the port is assigned to another application.

   ```
   [root@sap-mc-redhat]# nmap -sT -O localhost
   ```
   {: pre}

   ```
   Starting Nmap 6.40 (http://nmap.org) at (date and time)
   Nmap scan report for localhost (your localhost address)
   Host is up (0.0s latency).
   Other addresses for localhost (not scanned): (localhost addresses)
   rDNS record for (localhost): sap-mc-redhat
   Not shown: (number of) closed ports
   PORT   STATE SERVICE
   (port)/tcp open  ssh
   (port)/tcp open  smtp
   Device type: general purpose
   Running: Linux 3.X
   OS CPE: cpe:/o:linux:linux_kernel:3
   OS details: Linux 3.7 3.9
   Network Distance: 0 hops
   ```
   {: screen}

### nmap not found
{: #nmap-not-found}

You can install `nmap` on your system by using the appropriate package manager like `yum` or `apt-get`.

  * Command for Red Hat: `yum install nmap`
  * Command for SUSE: `zypper install nmap`


## Additional information
{: #addl-info}

If you don't have an {{site.data.keyword.cloud_notm}} API key, the {{site.data.keyword.IBM_notm}} Metrics Collector for SAP can't collect all of the metrics that are required by SAP, which include

  * Network Adapter Mapping - replaced with local MAC ID.
  * Network Adapter Bandwidth - Port Speed - defaults to 0.
  * Disk Volume Mapping - replaced with Volume Attachment ID.
  * Disk Guaranteed IOPS - defaults to 0.

You must provide an API key so that all metrics can be collected. Otherwise, this virtual server is not fully supported by SAP.
{: important}
