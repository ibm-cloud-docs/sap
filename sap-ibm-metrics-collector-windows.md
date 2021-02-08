---

copyright:
  years: 2020
lastupdated: "2020-12-17"

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

# Using {{site.data.keyword.IBM_notm}} Metrics Collector for SAP (IMCS) on Windows
{: #ibm-metrics-collector-for-sap-windows}

The {{site.data.keyword.IBM}} Metrics Collector for SAP (IMCS) on Windows is a requirement by SAP Support for {{site.data.keyword.vpc_full}} Infrastructure only when SAP workloads are running on the virtual server instance (VSI).
{: note}


## Getting an {{site.data.keyword.cloud_notm}} API key
{: #get-api-key}

You need an {{site.data.keyword.cloud_notm}} API key for IMCS to successfully collect all required metrics. The API key grants view access to {{site.data.keyword.cloud_notm}} infrastructure services. You can install IMCS without an API key. However, some metrics are missing and the virtual server is not supported by SAP.

For a list of missing metrics, see [Additional Information](#addl-info).

You need to create only one Service ID and one API Key per Account. You can use the same Service ID and API Key for all the Metric Collectors that are installed in the virtual server that is associated to the Account.
{: note}


### Creating a Service ID
{: #service-id}

You need to first create a Service ID and then the related API key. Use the following steps to create a Service ID.

1. Sign in to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com){: external} and click **Manage** > **Access (IAM)**.
1. Click **Services IDs** > **Create**.
1. Enter a **Name** and **Description** for the Service ID and click **Create**. You can assign the Access Policy after your Service ID is created.
1. Click **Access Policies** > **Assign Access**.
1. Click **IAM Services** for **Assign Service ID additional access**.
1. Select **VPC Infrastructure service** for **What type of access do you want to assign?**
1. Select **All resource groups** for **in**.
1. Leave the default **Account** for **in**
1. Leave **All resource types** for **Resource type** and click **Viewer** for **Platform Access**.
1. Click **Add** > **Assign**. The VPC Infrastructure Service policy is assigned to your Service ID.

### Creating an API Key for the Service ID.
{: #api-key}

Use the following steps to create an API Key for the new Service ID.

1. Select **Service IDs** and your newly created Service ID
1. Click the tab **Access Policy** and verify that the **VPC Infrastructure Service** is listed as an Access Policy. If not repeat steps 4-9.
1. Click the next tab **API keys** > **Create**.
1. Enter a **Name** and **Description** for the key and click **Create**.
1. Click **Copy** or **Download** your API key to save it.

Now is the only opportunity to access the API Key data. You cannot view this API key again, so you cannot retrieve it later.
{: important}

## Installing the {{site.data.keyword.IBM_notm}} Metrics Collector for SAP on Windows
{: #install-imcs-windows}

The IMCS is a service that automatically starts after the installation is and requires an API key. It collects metrics from the metadata of the virtual server, {{site.data.keyword.cloud_notm}} infrastructure services, runtime data about resources, such as CPU, memory, network, and disk. The metrics are aggregated and displayed through the web server for SAP customers. SAPOSCOL uses the XML output of this web server.

The IMCS uses port 18181 to show the metrics. Make sure that port `18181` is not used by any other application. For more information on how to check port availability, see [Troubleshooting](#troubleshooting-win).
{:important}

The commands that are listed in this section were run in Windows PowerShell 5.1.
{:note}

Use the following steps to download the IMCS.

1. [Download the IMCS](ftp://public.dhe.ibm.com/cloud/bluemix/sap/windows){: external}.
2. Select the appropriate .zip. In most cases, use the current version. Connect as **guest**.
3. Save the file to your internal Downloads folder and click **OK**.
4. Move or copy the IMCS .zip file to your VPC virtual server instance.
5. Extract the file and open the extracted folder.
6. Run the `install-metric-collector.ps1` file. Right-click on the file and select 'Run with Powershell', or run the following command on the power shell:

   ```
   .\install-metric-collector.ps1
   ```
   {: pre}
7. Paste your [API key](#api-key) when prompted. If you don't have an API key, see [Getting an {{site.data.keyword.cloud_notm}} API key](#get-api-key-win).
8. Check to make sure that the IMCS is running after the installation is complete. The service status displays `Running` when it is ready.

   ```
   Get-Service Telegraf
   Status   Name               DisplayName
   ------   ----               -----------
   Running  Telegraf           Telegraf Data Collector Service
   ```
   {: pre}

## Verifying data collection
{: #verify-data-collection-win}

After the installation completes and the service is started, it can take time for the IMCS begins collecting metrics. Wait at least 2 minutes after the installation before you expect full and accurate metrics.

1. Open the browser of your choice.
1. Open the following link: http://localhost:18181/sap/metrics

   ```
    <metrics>
      <metric category="config" context="vm" device-id="" last-refresh="1607451781" refresh-interval="0" type="string" unit="none">
        <name>Data Provider Version</name>
        <value>1.3</value>
      </metric>
      <metric category="config" context="host" device-id="" last-refresh="1607451781" refresh-interval="0" type="string" unit="none">
        <name>Cloud Provider</name>
         <value>IBM Cloud</value>
      </metric>
      <metric category="config" context="vm" device-id="" last-refresh="1607451781" refresh-interval="0" type="string" unit="none">
        <name>Instance Type</name>
        <value>bx2-8x32</value>
      </metric>
      <metric category="config" context="host" device-id="" last-refresh="1607451781" refresh-interval="0" type="string" unit="none">
        <name>Virtualization Solution</name>
        <value>KVM</value>
      </metric>
      .
      .
      .
    </metrics>
   ```
     {: screen}   

You might experience a delay before your data is available.
{:note}

## Troubleshooting
{: #troubleshooting-win}

Use the following troubleshooting tips for IMCS.

### Uninstalling the Metrics Collector
{: #uninstall-imcs-win}

1. Run the following command to uninstall IMCS if you have any issues during the installation process. Then, reinstall it.

   ```
   .\uninstall-metric-collector.ps1
   ```
   {: pre}

   ```
   Are you sure you want to uninstall Metric Collector for SAP? (Default is No)
   ( y / n ) : y
   Continuing uninstalling metric collector...
   Removed scheduled task: IBM Metric Collector for SAP Updator
   Successfully Uninstalled Metric Collector
   ```
   {: screen}

### No metrics reported when you open the link
{: #no-metrics-win}

No reported metrics is often due to the port not assigned to SAP Metrics Collector. It needs port `18181` available for `localhost`. If you have any other applications using the port, you must close the applications/owning process.

1. Use the following command to see whether the port is assigned to another application.

   ```
   Get-NetTCPConnection -State listen
   ```
   {: pre}

  ![Figure 1. Ports that are used by applications](/images/quickstudy-intel-vs-gen2-win-PS-Listen.png "Ports that are used by applications."){: caption="Figure 1. Ports that are used by applications" caption-side="bottom"}


## Additional information
{: #addl-info}

If you don't have an {{site.data.keyword.cloud_notm}} API key, the IMCS can't collect all of the metrics that are required by SAP, which include

  * Network Adapter Mapping - replaced with local MAC ID.
  * Network Adapter Bandwidth - Port Speed - defaults to 0.
  * Disk Volume Mapping - replaced with Volume Attachment ID.
  * Disk Guaranteed IOPS - defaults to 0.

You must provide an API key so that all metrics can be collected. Otherwise, this virtual server is not fully supported by SAP.
{: important}
