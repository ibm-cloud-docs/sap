---

copyright:
  years: 2020
lastupdated: "2020-12-17"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, Quick Study Tutorial

subcollection: sap

content-type: tutorial
completion-time:

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
{:step: data-tutorial-type='step'}

# SAP NetWeaver deployment to Intel Virtual Server (Gen2) on VPC Infrastructure that uses Windows Server
{: #quickstudy-vs-gen2-netweaver-wins}
{: toc-content-type="tutorial"}
{: toc-completion-time="90m"}

*A Quick Study, someone who is able to learn new things quickly.*
{: note}

These Quick Study Tutorials provide a single sample configuration, with less detailed instructions, as an introduction for customers who prefer hands-on tasks to increase their pace of learning.
{: shortdesc}

The following information provides an introduction for customers who are new to {{site.data.keyword.vpc_full}} (VPC) Gen 2 environment. Two sample configurations are provided to help you through the ordering process to the start of the SAP installation.

The first configuration sample is simple, a single node 128 GB, 32 vCPU virtual server instance (VSI). The second is an advanced configuration of two nodes by adding a second VSI to the landscape. The sample layouts might not be your preferred layout. The purpose of this guidance is to show you two possibilities if you are not experienced with the Windows&reg; operating system or with VPC Gen 2.

Although we want to start quickly, you first must be able to log in to {{site.data.keyword.cloud_notm}} and make sure that you have access to important SAP resources. Read the *Pre-Requisites for SAP Workloads* topic group, under [Necessary account credentials for SAP and {{site.data.keyword.cloud_notm}}](/docs/sap?topic=sap-necessary-credentials).

  This tutorial contains instructions to complete the deployment - a detailed explanation of the navigation through the {{site.data.keyword.cloud_notm}} console and all available options that you can use, you find in the topic [Deploying your infrastructure](/docs/sap?topic=sap-vs-set-up-infrastructure).
  {: note}

## Securing Access
{: #quickstudy-vs-gen2-netweaver-wins-secure-access}
{: step}

Security is one of the biggest concerns when you run your business-critical applications in a cloud environment. To secure your connection to your {{site.data.keyword.virtualmachineslong}}, a public SSH key can be uploaded to your account, per region. These public keys are deployed to your VSIs to allow access to them.

Before you continue, create an SSH public key that you can upload later to the region of your choice when you are creating the VSI. Follow the steps that are [documented here](/docs/vpc?topic=vpc-ssh-keys). Store your public and private key on your client computer - usually, in Linux&reg; environments it is located in the ~/.ssh folder.
{: tip}

You use security groups to restrict access to and from IP ranges, protocols, and ports. The default security group that is deployed with your sample VPC can suffice. However, you might have to add extra ports for exceptions to the access restrictions, such as the SAP Software Provisioning Manager and for the ports that are being used by your SAP NetWeaver based application.


## Creating an {{site.data.keyword.vpc_short}} and subnet
{: #quickstudy-vs-gen2-netweaver-wins-vpc-subnet}
{: step}

{{site.data.keyword.cloud}} compute resources are kept in a global region within a VPC. Use the following steps to create a VPC and its subnet.

1. Log in to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com){: external} with your unique credentials.
1. Click **Menu icon** ![Menu icon](../../icons/icon_hamburger.svg) > **VPC Infrastructure** > **Network** > **VPCs**
1. Click **Create**.
1. Enter a unique **Name** for the VPC, for example, *sap-test-vpc*.
1. Select a **Resource group**. Use resource groups to organize your account resources for access control and billing purposes. **Leave the value default**.
1. _Optional: Tags_. Enter tags to help you organize and find your resources. For example, *sap quick guide*.
1. Select whether the **Default security group** allows inbound SSH and ping traffic to VSIs in this VPC. **Leave the value default**.
1. _Optional: Classic access_. Select whether you want to enable your VPC to access classic infrastructure resources. **Leave the value default**.
1. _Optional: Default address prefixes_. If you do disable this option, the **New subnet for VPC** section will be hidden, and will require manual definition after the VPC is created. **Leave the value default**.

### New subnet for VPC
{: #vs-new-subnet-for-vpc-wins}

1. Enter a unique **Name** for the VPC subnet, for example, *sap-test-net*.
1. Select a **Resource group** for the subnet. **Leave the value default**.
1. Select a **Location** for the subnet. The location consists of a region and a zone.

    The region that you select is used as the region of the VPC. All additional resources that you create in this VPC are created in the selected region.
    {: tip}

1. Enter an **Address prefix**, **Number of addresses**, and an **IP range** for the subnet. **Leave the value default**.
1. _Optional: Public gateway_. **Leave the value default**.
1. Click **Create virtual private cloud** on the right.

## Creating a Virtual Server Instance
{: #new-vrtual-server-wins}
{: step}

Use the following steps to create a virtual server instance.

1. Click **Virtual server instances** > **New instance**.
1. Enter a unique **Name** for the virtual server, for example, *sap-wdb*. The name that you enter becomes the hostname.

    SAP hostnames must consist of a maximum of 13 alpha-numeric characters. See [SAP Note 611361](https://launchpad.support.sap.com/#/notes/611361){: external} for further details.
    {: important}

1. Select the **Virtual private cloud** in which to attach the VSI, for example, *sap-test-vpc*.
1. Keep the **Resource group** default.
1. _Optional: Tags_. For example, *sap quick guide*.
1. Leave the selected **Location** in which you created your subnet
1. Select **Windows Server** > **_2016 Standard Edition_** as the _Operating System_.
1. Click **All profiles** > **Balanced** and select *bx2-32x128*.

For more information about SAP-certified profiles, see [Intel Virtual Server (Gen2) certified profiles for SAP NetWeaver](/docs/sap?topic=sap-nw-iaas-offerings-profiles-intel-vs-vpc).

### Setting an SSH key
{: #ssh-key-wins}
{: step}

If you uploaded your public key for the VPC's region, select it and skip to the next section (Attaching storage).
Otherwise, follow these steps.

1. Click **New key**.
1. Enter a unique **Name**, for example, *sap-ssh-key*.
1. Keep the default **Resource group**.
1. The **Region** in which you created your subnets is already selected.
1. _Optional: Tags_. For example, *sap quick guide*.
2. Paste the **Public key**, that you created according to [the guidelines mentioned in Securing Access](#quickstudy-vs-gen2-netweaver-wins-secure-access).
3. Click **Add SSH key**.

4. _Optional: User data_. Leave blank.

### Attaching a block storage volume
{: #attach-block-wins}
{: step}

To have file system space available beyond what is required by the operating system, you need to attach a block storage volume to your VSI. This storage volume is used by the application that you're installing. In this example, the application is the Relational Database Management System (RDBMS) required for an SAP NetWeaver stack.

1. Click **New volume**.
1. Enter *sap-db-vol* for **Name**.
1. Select *Custom* for **Profile**.
1. Enter *500* for **Size**.
1. Enter *10000* for **IOPS**. **Throughput** defaults to *156.25 MiBps*.
1. Keep the **Encryption** and **Auto Delete** defaults.
1. Click **Attach**.
1. Keep the **Networking** default.
1. Keep the **Network interfaces** default.
1. Click **Create virtual server instance**. After the Windows instance is provisioned and ready, you need to retrieve the *Administrator* password and connect to it.


## Connecting to your Windows VSI
{: #connect-to-virtual-server-wins}
{: step}

To be able to connect to the Windows VSI from your client, you need the *Administrator* password and a public IP address. The password is retrieved by the {{site.data.keyword.cloud_notm}} command-line interface (CLI) whereas the public IP address - it is called _Floating IP_ - can be created with the {{site.data.keyword.cloud_notm}} console.

### Install CLI

Before you can use the CLI to retrieve the *Administrator* password, you must [install the IBM Cloud CLI](/docs/cli?topic=cli-getting-started) and [the VPC CLI plug-in](/docs/vpc?topic=vpc-set-up-environment#cli-prerequisites-setup).

### Connect to {{site.data.keyword.cloud_notm}} with the CLI

Log in to {{site.data.keyword.cloud_notm}} with your IBMid. If you have multiple accounts, you are prompted to select which account to use.
  ```
  ibmcloud login
  ```
  {: codeblock}

  If your credentials are rejected, you might be using a federated ID. To log in with a federated ID, use the `--sso` flag. See [Logging in with a federated ID](/docs/account?topic=account-federated_id) for more details.
  {: tip}

### Set the target region (DC)

Look up your region code in the table that corresponds to the location of your VPC.

| Region | Location |
| --- | --- |
| au-syd | Sydney |
| jp-tok | Tokyo |
| eu-de | Frankfurt |
| eu-gb | London |
| us-south | Dallas |
| us-east | Washington DC |

For example:

```
ibmcloud target -r eu-de

```

### Get the instance ID

```
ibmcloud is ins

```

Find the instance ID that is assigned to your VSI *sap-wdb*.

### Retrieve the Administrator password

```
ibmcloud is instance-initialization-values <instance ID> --private-key @sap-ssh-key

```

Take note of the password.

### Set the floating IP

To quickly access the deployed instance, you can assign a _Floating IP_ to your VSI. To add this IP to your server, complete the following steps:

1. In the {{site.data.keyword.cloud_notm}} console, go to **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > VPC Infrastructure > Compute > Virtual server instances**.
1. Click the name of the Windows VSI - *sap-wdb*.
1. On the Instance details page, find the **Network interfaces** section.  
1. By default, the first interface is named *eth0*.
1. Click the pencil icon to edit the primary network interface.
1. On the **Edit network interface** page, locate the **Floating IP address** field. You can select **Reserve a new floating IP** or you can select an existing floating IP address.
1. After you make your selection, click **Save**.
1. You might note the _Floating IP address_ or back in the **virtual server instances list** you can click it and copy it into the clipboard.

You can now log in to the virtual instance and begin preparing it for the SAP NetWeaver workload installation.


## Preparing the virtual server instance for your workload
{: #prepare-virtual-server-wins}
{: step}

In this tutorial, we simplify the process and use sample VSI profiles, volume and pagefile sizes. In a production-ready environment, of course, you need to size the servers and the volumes according to the number of concurrent users and the expected amount of data and further parameters. Find more in the topic [Sizing process for SAP Systems](/docs/sap?topic=sap-sizing).
{: important}

Depending on the database vendor you should consult their specific documentation, recommendations and best practises how to setup the file systems. You may start here.
* [IBM Db2](/docs/sap?topic=sap-anydb-ibm-db2) 
* [SAP MaxDB](/docs/sap?topic=sap-anydb-ibm-maxdb) 
* [SAP ASE](/docs/sap?topic=sap-anydb-ase) 

We let `sapinst`, the SAP installation programm, care about the user management, the disk partitioning as well as folder and subfolder creations that are required for the SAP application and the RDBMS. 


### Logging in to your Windows VSI

You can access the newly created VSI with Windows Remote Desktop. Enter the *Floating IP* and the *Administrator* password that you retrieved during the steps that are described previously.

### Initializing the block storage for Windows disk usage

1. Start the Windows Server **Disk Management**
![Figure 1. Windows Server Disk Manager](/images/quickstudy-intel-vs-gen2-win-DiskMgr.png "Windows Server Disk Manager"){: caption="Figure 1. Windows Server Disk Manager" caption-side="bottom"}
1. Find the block storage - usually it is Disk 2 and shows the ordered size and the status *offline*
1. Right-click the Disk tile and select **online** from the menu
1. Again right-click the Disk tile and select **Initialize Disk** from the menu, check to make sure that the correct disk is selected, select **GPT** as the default partition style - see footnote (+) below - and click **Ok**
1. Now right-click the  related tile to the right that shows *Unallocated* and select **New Simple Volume...**
1. Click **Next** twice, which retains the default value for the disk size and then specify your preferred drive letter, or leave the default and click **Next**
1. Overwrite the **Folder Name**, for example, *SAP* and leave the other default values and click **Next** - note, that **File System** *FAT32* is NOT SUPPORTED for SAP applications
1. Check the values and click **Finish**
1. After the volume has been prepared and formatted, you can find the new disk in the Windows Explorer

(+) [About partition styles - GPT and MBR.](https://docs.microsoft.com/en-us/windows-server/storage/disk-management/initialize-new-disks#about-partition-styles---gpt-and-mbr)

### Specifying the page file

1. Start the Windows Control Panel
1. Click **System and Security** then **System**
1. Click **Advanced system settings**
1. Click the tab **Advanced** then in Section **Performance** the button **Settings...**
1. Click the tab **Advanced** then in Section **Virtual memory** the button **Change...**
1. Deselect the check mark **Automacally manage...**
1. Select drive C: and click **Custom size**
1. Enter Initial size and Maximum size **32768**, click **Set** and **Ok**


Your next step is to [download and install your SAP software and applications](#install-sap-wins) if a single virtual server sample is sufficient for your needs.


## Installing two virtual server instances in a 3-tier setup
{: #3-tier-wins}
{: step}

A more complex scenario involves installing two virtual servers. One server is the SAP NetWeaver Application Server (*sap-wapp*) and the other server (*sap-wdb*) is the database server for SAP NetWeaver. You can reuse the server *sap-wdb* that you provisioned in the previous sections and create the application server *sap-wapp* as described in sections [Creating a Virtual Server Instance](#new-vrtual-server-wins) and [Attaching a block storage volume](#attach-block-wins) previously, except that you use the **Balanced profile** *bx2-8x32* and *20* as **Size** for data volume *sap-app-vol*.  Also, follow the steps to retrieve the *Administrator* password of the new VSI.

Both VSIs have one extra attached volume and a _Floating IP_. A smaller volume is attached to *sap-wapp*, which is the SAP primary application server (PAS). *sap-wdb* has a larger volume to host the RDBMS and the SAP Central Services (ASCS) instance.


<!-- either these diagrams should be replaced or removed>
<!--![Figure 4. Virtual server instances](/images/quickstudy-intel-vs-gen2-image14.png "Virtual server instances"){: caption="Figure 4. Virtual server instances" caption-side="bottom"} -->

<!--![Figure 5. Block storage volumes for VPC](/images/quickstudy-intel-vs-gen2-image15.png "Block storage volumes for VPC"){: caption="Figure 5. Block storage volumes for VPC" caption-side="bottom"}-->


## Preparing your network
{: #prepare-network-3-tier-wins}
{: step}

To segregate network traffic, as recommended by SAP, deploy a second subnet. One network is used for client access, the other for communication between the SAP ABAP stack and the RDBMS.

Follow the steps in the section [New subnet for VPC](#vs-new-subnet-for-vpc-wins) but use the **Name** *sap-test-net2*. After the new subnet is created, it will show on the Subnets for VPC page.

The two VSIs need to connect to the new network.
1. Go to the VSIs details overview and on each click **New interface**.
1. Select *eth1* as **Interface name**.
1. Select *sap-test-net2* as **Subnet**.
1. Leave the other vaulues default and click **Create**.

![Figure 13. Data volumes](/images/quickstudy-intel-vs-gen2-image4.png "Data volumes"){: caption="Figure 13. Data volumes" caption-side="bottom"}

Maintain your `hosts` files on both servers according to the targeted setup. Usually you find it in the following path: `C:\Windows\System32\drivers\etc\hosts`.

In this tutorial that installs a prototypical SAP System, we do not specify a Windows domain. Usually, if you configure a server for your company's access, you would specify the domain in the `hosts` file. During the SAP installation, you turn off the `FQDN`option and leave the domain name blank.
{: note}

The following example is for the server instance *sap-wdb*.

```
10.243.128.9 sap-wdb
10.243.129.6 sap-wdb-priv
10.243.128.7 sap-wapp-win
10.243.129.4 sap-wapp-priv
```

Your VSIs are now prepared to host the components of a distributed SAP installation. For more information about more installation preparations, see [Downloading and installing SAP software and applications](/docs/sap?topic=sap-download-install-media).

## Installing your SAP landscape
{: #install-sap-wins}
{: step}

### Installing the {{site.data.keyword.cloud_notm}} Metrics Collector for SAP
{: #install-metrics-collector-wins}

SAP requires the installation of the {{site.data.keyword.cloud_notm}} Metrics Collector for SAP to analyze your infrastructure in the event that a support incident has been submitted. Install the collector by using the instructions in [{{site.data.keyword.cloud_notm}} Metrics Collector for SAP](/docs/sap?topic=sap-ibm-metrics-collector-for-sap-windows).


### Downloading your SAP software
{: #download-sap-software-wins}
{: step}

You need an S-User ID and the Download Software authorization when you download the DVD images from the SAP Service Marketplace. To request an S-USer ID, see the [SAP Support Portal](https://support.sap.com/en/index.html).
{: note}

Depending on your target SAP application that you are going to install you need to gather information, which SAP images you will need to download. In this tutorial, we are choosing SAP NetWeaver ABAP on Windows using IBM Db2 for the SAP Database. Therefore we find in this guide  [SAP NetWeaver Installation Guide](https://help.sap.com/viewer/77e3a615f633437e9c4549891bb6bad7/CURRENT_VERSION/en-US){: external} all the needed information. SAP recommends to always search for the most recent versions.

Log in to the [SAP Service Marketplace](https://support.sap.com/swdc) and download the required DVD images to a local share drive and then transfer the images to your provisioned server. Alternative option, download the SAP Software Download Manager, install it on your target server and directly download the DVD images to the server. You might also consider an extra volume for keeping the SAP DVD images. For more information about the SAP Software Download Manager, see [SAP Software Download Center](https://support.sap.com/en/my-support/software-downloads.html){: external}.

### Preparing for SAP’s Software Provisioning Manager (SWPM)
{: #prepare-swpm-wins}
{: step}

SWPM is the component that guides you through the steps to successfully prepare and complete an SAP installation. Together with the other required images you may store and unpack SWPM on an extra file share that you then can attach to several VSIs on which you want to install SAP workloads.


### Installing SAP software
{: #install-sap-software-wins}
{: step}

Follow the instructions in the [SAP NetWeaver Installation Guide](https://help.sap.com/viewer/77e3a615f633437e9c4549891bb6bad7/CURRENT_VERSION/en-US){: external}. Also, review the corresponding SAP notes. See more detailed information about SAP NetWeaver installation that uses Db2 as the RDBMS in [Considerations about IBM Db2](/docs/sap?topic=sap-anydb-ibm-db2).

![Figure 2. SAP Application instances](/images/quickstudy-intel-vs-gen2-win-SingleHostSAPDb2.png "SAP Application instances"){: caption="Figure 2. SAP Application instances" caption-side="bottom"}

Figure 2 illustrates the basic SAP instances that will be installed to deploy the SAP NetWeaver ABAP application server onto one host. If you now want to complete the single server instance - i.e. just using VSI `sap-wdb`, you need to launch `sapinst` only one time on that server. In this case, you will first install the ABAP Central Services Instance (ASCS), then the Database Instance DB and finally the Primary Application Server (PAS).

If you go for the more complex implementation, a distributed SAP System, you will need to launch `sapinst` on `sap-wdb`, install the ASCS and the DB and then run `sapinst` on `sap-wapp` to install the PAS. Then, it is necessary that you open specific ports to allow inter-application communication between the application server and the database server. To accomplish this, you must use Windows Firewall tool.

1. Start **Windows Firewall with Advanced Security** - enter **wf.msc** in Windows search field
1. Click **Inbound Rules** then **Action** and **New rule...**
1. Click Rule Type **Port** and **Next**
1. Enter the ports that need to be opened (see below)
1. Click Action **Allow the connection** and **Next**
1. Click Profile **Public** to unselect this option and **Next**
1. Enter a name and optionally a description and click **Finish** (see below)

Example values are e.g. depending on the instance numbers that you have chosen:

| Ports | Name|
| --- | --- |
| 3000-3999 | SAP |
| 5912-5917 | Db2 |
| 40000-40099 | IGS |
| 50000-50099 | sapstartsrv |

In a production environment you will get more granular on the port numbers. For more information about ports, see [SAP ports](https://help.sap.com/viewer/ports){: external} and the respective documentation of your database vendor for details.
{: note}

If you run SAP GUI on your desktop, remember to add the ports that are required by your SAP application (example: ports 3200-3299, depending on your SAP NetWeaver instance number) to the security group.


## Finding more information
{: #find-information-wins}

Leaving now the tutorial and finding all information that you need to install your specific SAP components and versions, visit the [SAP Help Portal](https://help.sap.com/viewer/index){: external} as a starting point.


### Relevant SAP Notes

* [SAP Note 2384179 - SAP Systems on Windows Server 2016](https://launchpad.support.sap.com/#/notes/2384179){: external}.
* [SAP Note 2979010 - Windows on IBM Cloud (IaaS): Adaption of your SAP License](https://launchpad.support.sap.com/#/notes/2979010){: external}.

#### Memory Management

* [SAP Note 88416 - Zero administration memory management for the ABAP server](https://launchpad.support.sap.com/#/notes/88416){: external}.
* [SAP Note 1518419 - Page file and virtual memory required by the SAP system](https://launchpad.support.sap.com/#/notes/1518419){: external}.
* [SAP Note 2488097 - FAQ: Memory usage for the ABAP Server on Windows](https://launchpad.support.sap.com/#/notes/2488097){: external}.

#### Troubleshooting

* [SAP Note 100972 - Windows bug check event (blue screen)](https://launchpad.support.sap.com/#/notes/100972){: external}.
* [SAP Note 1559353 - How to capture user dumps on Windows](https://launchpad.support.sap.com/#/notes/1559353){: external}.
* [SAP Note 2015747 - How to generate Windows crash dump files](https://launchpad.support.sap.com/#/notes/2015747){: external}.


