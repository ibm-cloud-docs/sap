---
copyright:
  years: 2020, 2026
lastupdated: "2026-01-20"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Deploying your infrastructure
{: #bm-set-up-infrastructure}

This **Deploying your infrastructure** guidance provides how to deploy and set up SAP HANA and SAP NetWeaver on {{site.data.keyword.baremetal_long}}. This guidance refers to previous sections and includes setting up your network, security, storage, and operating system (OS).
{: shortdesc}


## Provisioning your Bare Metal server
{: #bm-provision-server}

The {{site.data.keyword.cloud}} console requires a unique log-in ID, which is an {{site.data.keyword.IBM_notm}}id. Use the following steps to order your {{site.data.keyword.baremetal_short}}. Additional information can be found under [Building a custom bare metal server](/docs/bare-metal?topic=bare-metal-ordering-baremetal-server#ordering-baremetal-server).

1. Log in to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com){: external} with your unique credentials.
1. Click **Create resource** > **Compute** > **Infrastructure** > **Bare Metal Server** > **Continue**.
1. Enter the number of servers you are ordering in the **Quantity** field.
1. Enter a **Hostname**, which is a permanent or temporary name for your servers. SAP Hostnames must consist of a maximum of 13 alpha-numeric characters. See [SAP Notes 611361](https://me.sap.com/notes/611361){: external} and [129997](https://me.sap.com//#/129997){: external} for more SAP Hostname details. Click **Information** for formatting specifics.
1. Enter a **Domain**, which is the identification string that defines administrative control within the internet. Click **Information** for formatting specifics.
1. **Billing** defaults to **Monthly**. Some select SAP-certified servers are offered with 1- or 3-year contract.
1. The data centers displayed under **Location** depend on product availability within a particular data center. Select your data center.
1. Click **All servers** > **SAP certified**. SAP HANA-certified servers are identified with an **.H** under CPU Model and SAP NetWeaver-certified are identified with an **.NW**.

1. Select the appropriate SAP HANA-certified and SAP NetWeaver-certified server for your workload from the URLs in [Intel Bare Metal server certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-classic-intel-bm) or [Intel Bare Metal server certified profiles for SAP NetWeaver](/docs/sap?topic=sap-nw-iaas-offerings-profiles-classic-bm)

.

1. **Server**, **RAM**, and your private storage option default based on your server selection and cannot be changed. {{site.data.keyword.IBM_notm}} {{site.data.keyword.blockstorageshort}} for {{site.data.keyword.cloud_notm}} or {{site.data.keyword.filestorage_full_notm}} are ordered after you have ordered your server.

1. Enter an optional public key for your SSH key, which you can use to log in to your server after it is provisioned.

1. Select your **Image** from either Red Hat or SUSE to run SAP HANA, or Red Hat, SUSE or Microsoft Windows to run SAP NetWeaver; and select the specific operating system, or VMware hypervisor for your server, if you have selected a server with the (VMware) option.


    If you are using SAP Business One, you need to select one of the [servers](/docs/sap?topic=sap-b1#b1-iaas) that supports it and SUSE 15 SP1 or higher as your Operating System.
    {: note}

{{site.data.content.vms-deprecated-note}}

If you are bringing your own license (BYOL) for your operating system, select **Other** > **No OS**. For more information, see [Bring your own license](/docs/bare-metal?topic=bare-metal-bm-no-os#bm-no-os).
{: note}


### Provisioning Bare Metal with Local SSD storage
{: #bm-local-storage}

When provisioning a Bare Metal with Local SSD storage, you can select different options for Redundant Array of Independent Disks (RAID) storage groups and partitioning layouts on-top of the RAID storage groups. See [About RAID](/docs/bare-metal?topic=bare-metal-bm-raid-levels) for more information.

You cannot change the local storage when you have selected an SAP HANA appliance.
{: note}


1. Under **Type**, select the appropriate RAID.
2. **Disks**, **Hot Spare**, and **Disk Media** have default values based on your selection. Select a **Disk Size** that covers the total amount of storage you need.


## Adding Network Storage
{: #bm-adding-network-storage}

{{site.data.keyword.blockstoragefull}} and {{site.data.keyword.filestorage_full_notm}} are ordered after you deploy your {{site.data.keyword.cloud_notm}} {{site.data.keyword.baremetal_short}}.
{: shortdesc}

{{site.data.keyword.cloud_notm}} storage LUNS ({{site.data.keyword.blockstorageshort}}) and volumes ({{site.data.keyword.filestorage_full_notm}}) can be provisioned with two options - Endurance and Performance:
- Endurance tiers feature pre-defined performance levels and other features, such as [snapshot](/docs/BlockStorage?topic=BlockStorage-snapshots) and replication.
- Custom Performance environment is built with allocated input/output/operations per second (IOPS) between 100 and 1,000

[Getting started with Block Storage](/docs/BlockStorage?topic=BlockStorage-getting-started) and [Getting started with File Storage](/docs/FileStorage?topic=FileStorage-getting-started) provides you with provisioning considerations, how to submit your order (if you didn't order when you originally provisioned your server), and connecting to and managing your new storage.

Use the following links to learn more about {{site.data.keyword.blockstorageshort}} and {{site.data.keyword.filestorage_full_notm}}:
* [Learn about {{site.data.keyword.blockstorageshort}}](https://www.ibm.com/think/topics/block-storage){: external}
* [Learn about {{site.data.keyword.filestorage_full_notm}}](/docs/FileStorage)

Provisioning steps can be found under
* [Ordering Block Storage](/docs/BlockStorage?topic=BlockStorage-orderingBlockStorage)
* [Ordering File Storage through the Console](/docs/FileStorage?topic=FileStorage-getting-started)


## Creating the Network Interface
{: #bm-creating-network-interface}

1. Select an **Uplink Port Speed**.
2. Enter values the **VLAN** and **Subnet** fields

If you have decided on a public/private setup for your environment and plan to
    * Install multiple SAP systems that need to communicate with each other *or*
    * Choose a three-tier SAP setup (database and application instances on different hardware)

Make sure that your name resolution reflects the internal and external addresses. The external address matches the hostname of the server resolved by its fully-qualified domain name (FQDN). The internal addresses will not appear in the domain name system (DNS). Since internal IPs should be used for communication between the servers, make sure you extend your `/etc/hosts` or Microsoft Windows "host" file accordingly. This information might also apply to the guest operating system of you VMware ESXi-based deployments.
{: note}

3. Leave the default values for all other fields.
4. Review your Order Summary.
5. Select **I read and agree to the following Third-Party Service Agreements**.

    You can create your server, save the order as a quote to provision at a later time, or add the order an estimate, which may include multiple services.
    {: note}

6. Click **Create** to be redirected to the Checkout page after your order has been verified.

You are redirected to a page with your order number. You can print the page, because it's your receipt. A confirmation email with the subject Your *{{site.data.keyword.cloud_notm}} Order ## has been approved* is be sent to the email address in your profile. This email is notice that your server has been approved and is in the process of being deployed. After it is deployed, another notice is sent notifying you that the server is available and can be managed through the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com/){: external}. You can check Device Details from the {{site.data.keyword.cloud_notm}} console (Menu icon ![Menu icon](../../../icons/icon_hamburger.svg) > Classic Infrastructure > Device List) for a status of the provisioning steps. Click the **Device Name** that matches your device's Hostname and Domain to see its status.


## Readying your Operating System
{: #bm-os}


### Bring your own OS product license
{: #bm-os-byol}

When you have your own operating system license, you can install it on your Bare Metal server based on the vendor's instructions. For more information about custom images, see [Bare Metal - The no OS option](/docs/bare-metal?topic=bare-metal-bm-no-os).

Please consider, the OS chosen must be certified for SAP and have access to the necessary OS Packages for SAP.

Please check in [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external} if the selected operating system and its version is supported.
