---

copyright:
  years: 2020
lastupdated: "2020-09-21"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, Veeam Backup & Replication, Veeam Plug-in, SAP HANA Backup, SAP HANA Backint, SAP NetWeaver Backup, Backups, {{site.data.keyword.cloud_notm}} storage, {{site.data.keyword.blockstorageshort}}

subcollection: sap

content-type: tutorial
completion-time: 90m

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
{:deprecated: .deprecated}
{:step: data-tutorial-type='step'}


# Veeam Backup & Replication for SAP HANA backups
{: #tutorial-veeam-provisioning-infrastructure}
{: toc-content-type="tutorial"}
{: toc-completion-time="90m"}

This document is out of date, it is being updated for Veeam 10.x and replaced in November-2020 with new content.
{: deprecated}

You need to provision an {{site.data.keyword.IBM}} Virtual Server and {{site.data.keyword.blockstorageshort}} to host Veeam Backup & Replication.
{: shortdesc}

## Provisioning a Virtual Server to host your Veeam instance
{: #veeam-provisioning-vsi}
{: step}

Use the following steps to provision the virtual server to host your Veeam Backup & Replication instance. For more information on provisioning public virtual server instances, see [Provisioning public instances](/docs/virtual-servers?topic=virtual-servers-ordering-vs-public).

1. Log in to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com/){: external} with your unique credentials.
2. Click **Create resource** > **Compute** > **Infrastructure** > **Virtual Server**.
3. The default is **Public Virtual Server**. Change the type to **Starting from $25.00 monthly** and click **Continue**.
4. Leave the defaults for **Type of virtual server**, **Quantity**, and **Billing**.
5. **Hostname** is a permanent or temporary name for your servers, for example `virtualserver01`. Hover over **Information** for formatting specifics.
6. **Domain** is the identification string that defines administrative control within the internet, for example, `Customer-145840.cloud`. Hover over **Information** for formatting specifics.
7. Leave **Placement group** set to **None**.
8. Select the **Location** where your current SAP-certified infrastructure is located.
9. Click **All profiles** and select **B1.4x8**. You can select a larger configuration if your needs require it.
10. Click **Microsoft Image** (OS) and select **Windows 2016 Standard (64 bit)-HVM**.
11. Click **Add-ons** (under Image) and select **Veeam**, and choose your Veeam licensing based on the number of servers or SAP HANA nodes to be backed up.
12. Leave **Attached storage disks** as is.
13. Change **Uplink port speeds** to **1 Gbps Public & Private Network Uplinks**.
14. Leave the default values for all other fields.
15. Review your Order Summary.
16. Click _I have read and agree to Third-Party Service Agreements_.
17. Click **Create** to be redirected to the Checkout page after your order has been verified.

You are redirected to a page with your order number. You can print the page because it's your receipt. In addition, you receive a confirmation email with the subject *Your {{site.data.keyword.cloud_notm}} Order ## has been approved* with ## being your order number.

After the order is submitted, the server, depending on your order, is available for use within one to four hours. You can check Device Details from the {{site.data.keyword.cloud_notm}} console (Menu icon ![Menu icon](../../icons/icon_hamburger.svg) > Resource List > Devices) for a status of the provisioning steps. Click the **Device Name** that matches your given hostname and domain to see its status.

## Provisioning {{site.data.keyword.blockstorageshort}} to host your Veeam backup repository
{: #veeam-provisioning-block-storage}
{: step}

1. Expand the Menu icon ![Menu icon](../../icons/icon_hamburger.svg) and select *Classic Infrastructure*.
2. Select **Storage** > **Block Storage** > **Order Block Storage**.
3. Select the specifics for your storage needs. Table 1 contains the recommended values for your backup repository.

| Field  | Value |
| --- | --- |
| Location | Same as where you provisioned your virtual server instance |
| Billing Method | Monthly (default) |
| Size | 1,000 GB |
| Endurance (IOPS tiers) | 0.25 IOPS/GB |
| Snapshot space | 0 GB  |
| OS Type | Windows 2008+  |
{: caption="Table 1. Recommended values for block storage" caption-side="top"}

4. Click **Create**.

### Authorizing host
{: #veeam-authorizing-hosts-console-veeam}
{: step}

1. Select **Storage** > **Block Storage**.
2. Highlight your LUN and expand the Action menu ![Action menu](../../icons/action-menu-icon.svg) and select **Authorize Host**.
3. Select a **Device Type** of **Virtual Server**.
4. Click **Virtual Guest** and select the name of the server you provisioned for Veeam Backup & Replication.
5. Click **Save**.

    Additional provisioning information can be found under [Ordering Block Storage through the Console](/docs/BlockStorage?topic=BlockStorage-orderingthroughConsole).
    {: tip}  

## Configuring the server for Veeam Backup & Replication
{: #veeam-configuring-server}
{: step}

### Before you begin
{: #veeam-hana-configure-before-you-begin}
{: step}

Before you configure your server to host Veeam Backup & Replication, make sure the following prerequisites have been met.

* {{site.data.keyword.baremetal_long}} or public {{site.data.keyword.virtualmachineslong}} have been provisioned
* {{site.data.keyword.cloud_notm}} {{site.data.keyword.blockstorageshort}} has been allocated to the Veeam backup repository
* You have credentials to access your hypervisor environment
{: shortdesc}

### Attaching {{site.data.keyword.blockstorageshort}} to your provisioned servers
{: #veeam-attaching-block-storage-for-veeam}
{: step}

Use the following steps to enable {{site.data.keyword.cloud_notm}} {{site.data.keyword.blockstorageshort}} multipath I/O (MPIO) on your provisioned server.

1. Log in to {{site.data.keyword.cloud_notm}} console and access your server through the Device List. If you need help accessing the Device List, see [Navigating to devices](/docs/virtual-servers?topic=virtual-servers-navigating-devices).
2. Open Windows Server Manager and select **Local Server**.
3. Under **Manage**, choose **Tasks** > **Add Roles and Features**. Click **Next** four times.
4. Click **Multipath I/O** under Features > **Next**. Select **Restart the destination server automatically** > **Install**.
5. A feature installation confirmation should appear to validate the installation of MPIO.

#### Configure Windows iSCSI Initiator
{: #veeam-configure-windows-iscsi-initiator}
{: step}

You are going between the {{site.data.keyword.cloud_notm}} console and the Windows Server Manager dashboard to configure the Windows iSCSI Initiator.
{: note}

1. Click the Menu icon ![Menu icon](../../icons/icon_hamburger.svg) > **Classic Infrastructure** > **Storage** > **Block Storage** and select your {{site.data.keyword.blockstorageshort}} LUN.
2. Under **Authorized Hosts**, highlight and copy the LUN's **HOST IQN**.
3. Go back to the Windows Server Manager dashboard and select **Tools** > **iSCSI Initiator**.
4. Click the **Configuration** tab in the iSCSI Initiator Properties window, click **Change**, paste the Host IQN into **New initiator name**, and click **OK**.
5. Restore the {{site.data.keyword.cloud_notm}} console and copy a **Target Address**. Either address works.
6. Restore the iSCSI Initiator Properties, click **Discovery** > **Discover Portal**, and paste the Target Address into **IP address or DNS name**. Click **Advanced**.
7. Go back to the {{site.data.keyword.cloud_notm}} console and copy **Username** under Authorized Hosts.
8. Restore the iSCSI Initiator Properties, click **Enable CHAP log on**, and paste the Username in **Name** under Advanced Settings.
9. Go back to the {{site.data.keyword.cloud_notm}} console and copy **Password** under Authorized Hosts.
10. Restore the iSCSI Initiator Properties, paste the Username in **Target secret** under Advanced Settings.
11. Click **OK** twice.
12. Select **Targets** and click the inactive Discovered Storage IQN. Click **Connect** and click **OK**.
13. Click **Advanced** and repeat steps 7 to 11.
14. Minimize the {{site.data.keyword.cloud_notm}} console. You'll need it when you establish a connection between the Veeam backup repository and infrastructure.

    The storage target is now in a _Connected_ status if all settings were correctly entered.

#### Enabling operating system visibility to the attached storage
{: #veeam-enabling-os-visibility}
{: step}

1. Go back to the Windows Server Manager dashboard and select **Tools** > **Computer Management**, and click **OK** to initialize the new disk.
2. Select **Storage** > **Disk Management**. The new storage disk will be offline.
3. Right-click on the disk and click **Online**. You will also need to right-click and initialize any storage not previously in use.
4. Right-click in the volume field and click **New Simple Volume**. The New Simple Volume Wizard will display. Click **Next**.
5. Select the following, click **Next**, and click **Finish** when you're done.

    64K block size is selected because the sample virtual server is running Windows 2016 as its OS. The selected settings help ensure optimal performance and reliability with your Veeam deployment.

    The volume is now online.


| Field | Value |
| --- | --- |
| Simple volume size in MB                      | Select the maximum amount                          |
| Assign the following drive letter             | Accept the default value                           |
| Format the volume with the following settings | Select                                             |
| File system                                   | ReFS                                               |
| Allocation unit size                          | 64K                                                |
| Volume name                                   | Enter a name for the volume, for example `VBRrepo` |
{: caption="Table 2. Summary of input" caption-side="top"}


## Creating the Veeam backup repository
{: #veeam-creating-repository}
{: step}

The next step in implementing Veeam Backup & Replication is to create repository and establishing connections to your infrastructure.
{: shortdesc}

### Setting up the backup repository
{: #veeam-setting-up-repo}
{: step}

Use the following steps to set up the Veeam repository.

1. Log in to the Veeam Backup & Replication console.
2. Select **Backup Infrastructure** > **Backup Repository** > **Add Repository**.
3. Click **Direct Attached Storage** > **Microsoft Windows server**.
4. Enter your repository's **Name**, for example, `RSL025IOPS`, **Descirption**, and click **Next**.
5. Click **Populate** and select the drive that corresponds to the attached {{site.data.keyword.blockstoragefull}}. For the example, the corresponding drive is `V:\`. Click **Next**.
6. Click **Advanced**, select **User per-VM backup files**, and click **OK**.
7. Click **Next** twice and click **Apply** to apply the settings.
8. Click **Finish** to create the repository extent, then click **No** to relocate the Veeam configuration backup location to the new repository.

To be able to store backups on a Veeam backup repository, accounts, including administrative accounts, _must_ have access permissions on the backup repository. For instructions on setting up permissions, see [Granting Permissions on Repositories](https://helpcenter.veeam.com/docs/backup/plugins/repository_permissions.html?ver=95u4)
{: important}

### Creating a backup repository
{: #veeam-creating-backup-repo}
{: step}

You can now begin writing backups to the new repository. However, for maximum economy, flexibility, and scalability, it is strongly advised to create an optional scale-out backup repository. Why should you create an optional scale-out backup repository? For economy, it enables the purchase of only the {{site.data.keyword.blockstorageshort}} you need for your current deployment. As your {{site.data.keyword.cloud_notm}} infrastructure grows, additional {{site.data.keyword.blockstorageshort}} `extents` can be purchased and added to the scale-out backup repository. You lessen your up-front costs for the Veeam solution without incurring the overhead of reconfiguring backup jobs to point to specific `extents` within the repository.

Use the following steps to create a scale-out backup repository.

1. From the Veeam Backup & Replication console, select **Scale-out Repositories** > **Add Repository**.
2. Enter a **Name** and **Description** for the scale-out repository, and click **Next**.
3. Click **Add** and select the new backup repository. Click **Apply**.

### Establishing a connection to the {{site.data.keyword.cloud_notm}} Infrastructure
{: #veeam-establishing-connection-to-infrastructure}
{: step}

Now that you've created your backup repository, you need to connect the repository to the {{site.data.keyword.cloud_notm}} infrastructure. Use the following steps to establish the connection.

1. From the Veeam Backup & Replication console, select **Backup Infrastructure** > **Managed Servers** > **Add Server** > **MICROSOFT SERVER**.
2. Enter a **DNS name or IP address** and **Description** for your host VMware vSphere (ESXi) or VMware vCenter. Click **Next**.
3. Restore the {{site.data.keyword.cloud_notm}} console and go to [Menu icon ![Menu icon](../../icons/icon_hamburger.svg) > **Resource List** > **Devices** and find the server you set up for Veeam Backup & Replication.
4. Copy the Username and go back to the Veeam Backup & Replication console. Paste the server username in **Credentials Username**.
5. Go back to the {{site.data.keyword.cloud_notm}} console and copy the server's Password.
6. Restore the Veeam Backup & Replication console and paste the server password in **Credentials Password**. Enter a **Description** and click **OK**.
7. Click **Connect** to the Security Warning, and click **Next**.

The server should now appear under Managed Servers.


## Submitting the initial backup job
{: #veeam-submitting-backup-job}
{: step}

Once the infrastructure and repository has been set up, you're ready to create and submit your initial backup job.
{: shortdesc}

### Before you begin
{: #veeam-initial-backup-before-you-begin}
{: step}

Be sure to have set up the correct permissions on the backup repository. For instructions on setting up permissions, see [Granting Permissions on Repositories](https://helpcenter.veeam.com/docs/backup/plugins/repository_permissions.html?ver=95u4)
{: important}

### Creating the initial backup job
{: #veeam-creating-initial-backup-job}
{: step}

1. From the Veeam Backup & Replication console, select **Backup & Replication** > **Jobs** > **Backup Job**. The **New Backup Job** wizard displays.
2. Under Add Objects, select the IP address of the server you provisioned for Veeam Backup & Replication. Click **Add**,

   Add Objects will list options for the VMs to include in the backup job. Individual VMs, VM folders, VM resource pools, or entire hosts and clusters can be added to the backup job. Adding VM groups simplifies ongoing job maintenance as the Veeam backup job doesn't need to be modified in the event VMs are added or removed from the grouping entity.
   {: note}

3. Under Storage in the New Backup Job wizard, **Backup proxy** defaults to **Automatic selection**. For **Backup repository**, select the name of the scale-out proxy and click **Next**.

   You're selecting the scale-out backup repository for the job's backup storage target because by default 14 restore points (days) are maintained. These default restore points mean that if a job runs daily, two weeks worth of backups are maintained. The backup chain is constructed using _forward incremental backups_. The initial full backup is stored and only changed blocks are stored for the subsequent restore points. For more information, see [Backup Chain](https://helpcenter.veeam.com/archive/backup/95/vsphere/backup_files.html).
   {: note}

4. Click **Next** under Guest Processing.
5. Select **Daily at this time** at **8:00 AM** for your schedule.
6. Click **Apply**.
7. Click **Run the job when I click Finish** to run the initial backup job immediately. Click **Finish**.
8. Double-click the running job and select **Show Details** to see granular status information on the running backup job.

For information on Veeam's application-aware processing for advanced application-specific item level recovery for SQL, Oracle, Exchange, SharePoint, and others, see [Application-Aware Processing](https://helpcenter.veeam.com/archive/backup/95/vsphere/application_aware_processing.html).   
