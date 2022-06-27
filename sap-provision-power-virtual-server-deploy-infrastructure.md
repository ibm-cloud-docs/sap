---

copyright:
  years: 2020, 2022
lastupdated: "2022-04-08"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads

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

# Deploying your infrastructure
{: #power-vs-set-up-infrastructure}

SAP HANA and SAP NetWeaver are complementary offerings from {{site.data.keyword.IBM_notm}} Power Systems, with low latency access to {{site.data.keyword.cloud_notm}} services
{: note}

Use the following information to deploy and set up SAP HANA and SAP NetWeaver on {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}. These sections include references to previous sections and covers setting up your network, security, storage, and operating system (OS).
{: shortdesc}
<!-- Friendy advice: this file can benefit from many of these sections being made into their own topics/files. Documentation best practice is to give each kind of topic type its own topic. Such as a how to, concept, and reference topic. Topic writing guidance - https://test.cloud.ibm.com/docs/writing?topic=writing-topics -->
## Before you begin
{: #power-vs-before-you-set-up-infrastructure}

Before you order your {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}, review the following steps:

 * Set up your [resource groups](/docs/account?topic=account-rgs) if you're setting up multiple servers for various purposes.
 * [Invite users](/docs/account?topic=account-access-getstarted) and determine what resources and resource groups that they can access.
 * Select a version of the {{site.data.keyword.IBM_notm}}-provided SUSE Enterprise Linux&reg; Server (SLES), Red Hat Enterprise Linux&reg; (RHEL), or IBM AIX stock images (depending on SAP HANA or SAP NetWeaver workload). The operating system version levels of the stock images are subject to change. For more information, see [SAP Note 2855850](https://launchpad.support.sap.com/#/notes/2855850){: external}.
   * You must use your own Linux&reg; license.
   * You can't use an existing AIX license for logical partitions (LPARs) in a {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} instance. For Linux images, you must use your own license.

## Creating your {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} service
{: #power-vs-create-power-service-group}

The {{site.data.keyword.cloud}} console requires a unique log-in ID, which is an {{site.data.keyword.IBM_notm}} ID. Use the following steps to create your {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} service. For more information, see [Creating a {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}](/docs/power-iaas?topic=power-iaas-creating-power-virtual-server).

1. Log in to [{{site.data.keyword.cloud_notm}}](https://cloud.ibm.com){: external} with your unique credentials.
2. Click **Create resource** > **Compute** > **Infrastructure** > **{{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}**.
3. Select your **Region** and **Pricing plan**.
4. Enter a unique **Service name** and select your **Resource group**. You can also enter a **Tag** for your service.
5. Click **Create**. You are redirected to the Resource list for your account where you can see when your service was provisioned.

### Configuring your {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} service
{: #power-vs-configure-power-service-group}

Use the following steps to configure your {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} service.

1. Create a virtual private cloud (VPC). On {{site.data.keyword.vpc_full}}, you can host virtual machines to manage your {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} Virtual Servers, such as a Windows virtual server for SAP SWPM, and extra managing services, such as a proxy server for access to Cloud Object Store. For more information, see [Getting started with Virtual Private Cloud (VPC](/docs/vpc).
1. Add an SSH key to securely connect to your {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}. For more information, see [Adding an SSH key](/docs/ssh-keys?topic=ssh-keys-adding-an-ssh-key).
1.	Configure one or more private network subnets to use for the SAP workload. For more information, see [Configuring and adding a private network subnet](/docs/power-iaas?topic=power-iaas-configuring-subnet).
1. Create two redundant Cloud connections and attach {{site.data.keyword.powerSysFull}} and {{site.data.keyword.vpc_full}} networks together by using these connections.
1. Attach the VPC networks to the Cloud connection. This connection establishes the communication from the virtual machines in VPC to the {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}.
1.	If required, create more management systems (for example, OS update server, time server, jump server) on {{site.data.keyword.vpc_full}}, or on {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} service.
1.	If required, configure Cloud connections to {{site.data.keyword.cloud}} classic or to on-premises networks.

## Provisioning your {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}
{: #power-vs-provision-server}

Use the following steps to provision a new {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}:

1. Highlight your provisioned {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} service from the Resources list and click **New Instance**.
1. Enter a **Name** for your servers and enter the **Number of instances** to be provisioned. More options appear if you enter more than one instance. See [Creating a {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}](/docs/power-iaas?topic=power-iaas-creating-power-virtual-server) for option details.
1. Specify the VM-pinning rule. You can choose to soft pin or hard pin a VM to the host where it is running. When you choose to soft pin a VM for high availability, IBM PowerVC automatically migrates the VM back to the original host when the host is back to its operating state. IBM PowerVC runs in the background. If the VM has a licensing restriction with the host, the hard pin option restricts the movement of the VM during maintenance. The default pinning policy is none.
   - For SAP HANA, the recommended choice is soft pin.
   - For SAP NetWeaver, the recommended choice is soft pin. The hard pin option isn't required because the SAP license is bound to the virtual server by a unique ID and not to the physical server's hardware ID.
1. Select your **SSH key** or define a new SSH key. For more information about adding an SSH key, see [Adding an SSH key](/docs/ssh-keys?topic=ssh-keys-adding-an-ssh-key) and an example is shown on [Creating an AIX virtual machine (VM) with SSH keys for root login](/docs/power-iaas?topic=power-iaas-create-vm).
   - When you use an AIX stock image as the boot volume, the root password isn't set. You must connect to the AIX VM and set the root password for the system. If you don't complete this step, SSH login as 'root' appears disabled.
1. Under **Boot Image**, select the **Operating System** and one of the {{site.data.keyword.IBM_notm}}-provided images of the OS that you want to deploy (usually defined by the OS version). The image might also be a custom image that is loaded to {{site.data.keyword.cloud_notm}}. However, the custom image isn't supported.
   - For SAP HANA, select **Linux for SAP (HANA) - Client supplied subscription** for the operating system and select the image.
   - For SAP NetWeaver, either select **AIX** or select **Linux for SAP (NetWeaver) - Client supplied subscription** for the operating system and select the image.
      - With AIX 7.1, only images at version 7100-05-05 or higher are supported for SAP.
      - With AIX 7.2, only images at version 7200-04-01 or higher are supported for SAP.
      - With Linux&reg; for SAP (NetWeaver) - client supplied subscription, all the listed images are supported.
      - With Linux&reg; for SAP (HANA) - client supplied subscription, all the listed images are supported.
      - The {{site.data.keyword.IBM_notm}}i operating system isn't supported.
1. Under **Profile**, either select a pre-defined profile from one of the profile families or create a customized profile. For more information about available profiles, see [IBM Power Virtual Server certified profiles for SAP HANA](/docs/sap?topic=sap-power-vs-set-up-infrastructure#power-vs-provision-server). To create a customized profile, use the following steps. 
   - Select a machine type (which determines the number of available cores and memory).
      - For SAP HANA, only machine type E980 is allowed [(link to specifications information on E980)](https://www.ibm.com/us-en/marketplace/power-system-e980){: external}.
      - For SAP NetWeaver, use either S922 (see [specifications information on S922](https://www.ibm.com/products/power-system-s922?mhsrc=ibmsearch_a&mhq=S922){: external}), or E980 (see [specifications information on E980](https://www.ibm.com/us-en/marketplace/power-system-e980){: external}).
   - Define the number of CPU **Cores** and **Memory** (RAM) that match the size of your SAP HANA database server or SAP NetWeaver application server.
   - Choose a processor type. For a description of the technical differences between *Dedicated processor*, *Shared uncapped processor*, and *Shared capped processor*, see the [FAQ](/docs/power-iaas?topic=power-iaas-power-iaas-faqs#processor) for Power Systems Virtual Servers.

      Customized profiles can't be used in production environments.
      {: note}

1. You can either create a **New storage volume** or attach existing storage volumes that are already defined in your {{site.data.keyword.cloud}} account. If you create a new storage volume, specify a unique name for the volume so that you can identify it in the list of all volumes.
    - For SAP HANA data and log volumes, file systems must be striped over eight volumes. Specify *8* for the quantity of new storage volumes.
1. Select **Private networks** for communication between SAP instances. For more information, see [Configuring a private network](/docs/power-iaas?topic=power-iaas-configuring-subnet). Optionally, you can configure an extra public network, for example, to get OS updates over the internet.
1. Click **I have read and agree to the agreements listed below** and click **Create**. The new instance becomes visible in the list of virtual servers with a status of `Creating` for a few minutes. When the status changes to `Active`, you can log in to the server (for example, by using SSH).

For SAP NetWeaver, after an IBM AIX virtual server becomes active, a console session is required to initially set the root user password. If you don't initially set the root password, SSH login as 'root' appears disabled.
{: important}

## Adding Network Block Storage
{: #power-vs-adding-block-storage}

A 20 GB boot disk is automatically created when you provision a new {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} with AIX. The size of the Linux&reg; root volume is 100 GB. If you need more space for the root volume group, create a new storage volume and attach it to the virtual server. Next, rescan for the new devices and extend the root volume group with the new disk in the operating system.

Use the following steps to add a storage volume:

1. Click **New volume**.
2. Enter the **Name**, **Type**, and **Size** of the storage volume. You can also select whether to make it **Shareable** and set an **Affinity policy**. Specify a unique name for the volume so you can identify it in the list of all volumes.
3. Select **Affinity** for the **Affinity policy** and specify the boot disk as the **Affinity volume**. Specifying an Affinity Policy makes sure that the new storage is available in the list to attach to your virtual server. By identifying the boot disk as an Affinity Volume, your volumes are on the same backbone storage.

   Mixing storage tiers isn't supported. If you have a virtual server instance that uses Tier 1, you cannot define Tier 3 disks.
    {: important}

4. Agree to the **Service Agreement** and **Terms and Conditions**, then click **Create Storage Volume**.
5. Go to the **Storage Volume overview** to see your new volume listed.
6. Attach the new volume to your virtual server by selecting your server in the list of Attached Volumes. Then, click **Manage Existing** and select the volume that you created and click **Finish**.  

   **Linux:** If you attached new volumes to your Linux&reg; OS, you must run a SCSI scan or restart the operating system (OS) to see the disks (for example: `/usr/bin/rescan-scsi-bus.sh -a -c -v`).

7. **AIX:** Run the `cfgmgr` command. Server restart isn't necessary. To get the disk overview, run `lsdev -Cc disk`.
8. Extend your existing file systems or create a new one with the OS Logic Volume Manager (LVM). For AIX, see the following links:
    * [Adding storage for the rootvg during AIX server provisioning](/docs/sap?topic=sap-power-vs-aix-nw#power-vs-aix-nw-adding_storage)
    * [Extending /tmp](/docs/sap?topic=sap-power-vs-aix-nw#power-vs-aix-nw-extending_tmp)
    * [Extending or adding paging space](/docs/sap?topic=sap-power-vs-aix-nw#power-vs-aix-nw-extending_paging_space)
    * [Storage and the AIX Logical Volume Manager](/docs/sap?topic=sap-power-vs-aix-nw#power-vs-aix-nw-storage_aix_lvm)

If you want to attach or detach a volume, click **Manage existing volumes** and select the volume. You can also change the boot status of a volume by clicking the **Bootable** toggle.

## Creating network interfaces
{: #power-vs-creating-network-interface}

You define at least one network interface (vNIC) for each Power Virtual Server that is provisioned. The network interface is defined by using either the of the following ways:
- Single Public Network for the Power Virtual Server Group
- Private Network/s defined for the Power Virtual Server Group (created with a choice of Subnet range)

A Private Network must be created before you provision the Power Virtual Server, as described in [Power IaaS - Configuring and adding a private network subnet](/docs/power-iaas?topic=power-iaas-configuring-subnet), which establishes a Private VLAN with a Private Subnet. The subnet is shown in the _Subnets_ list within the Power Virtual Server Group.

A public network can be created only when you provision a Power Virtual Server by using the on/off toggle button. After the first Power Virtual Server is deployed with a public network, the created Public VLAN with a Public Subnet is also shown in the _Subnets_ list within the Power Virtual Server Group. The VLAN is prefixed with `public-` and can't be altered.

At least one network interface that is connected to a Public Network or Private Network is required before a Power Virtual Server can be provisioned.

You can provision a Power Virtual Server public network only, but this configuration isn't recommended for security reasons and doesn't allow the public network to toggle on or off.
{: note}

Use the following steps to add a new network interface for a Private Network to your Power Virtual Server.

1. Under **Network interfaces** of the **Create a new instance for Power Systems Virtual Server** page, click **Attach existing** under subheading "Private Networks"
2. An overlay appears. Click **Select a network** to access the list that contains all of the established private networks
3. Select a private network to display the IP range of the subnet
   *Optional:* By default the IP address is automatically assigned from the IP range. However, you can specify a specific IP address
4. Click **Attach**. The network that you attached is displayed under the subheading "Attached Private Networks". The IP address remains blank if the optional step to specify an IP address was not done.
5. This task can be repeated for as many private networks as needed.

For an AIX VM, network interfaces are assigned based on the order that you specified during creation. To display information about all of the network interfaces after provisioning, open the AIX console and type `ifconfig -a`.
{: note}

For more information about AIX or IBM i, see the following links:
- [Adding a network interface to an AIX VM](/docs/power-iaas?topic=power-iaas-managing-network-interface)
- [Adding a network interface to an IBM i VM](/docs/power-iaas?topic=power-iaas-managing-network-interface-ibmi#add-nic-ibmi)

## Readying your Operating System
{: #power-vs-os}


### Bring your own License (BYOL) for OS
{: #power-vs-os-byol}

Linux&reg; OS that uses an OS Image that is provided by {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s, requires the OS License (BYOL) to be provided. This option is supported by SAP.

Unix&reg; OS that uses an OS Image that is provided {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s, can use either a monthly subscription or BYOL. This option is supported by SAP.

When you have your own operating system license, you install it based on the vendor's instructions. The OS that you choose must be certified for SAP and have access to the necessary OS Packages for SAP.

For more information, see [OS for IBM Power Virtual Servers](/docs/sap?topic=sap-compute-os-design-considerations#os-power).

### Bring your own OS
{: #power-vs-os-byos}

Linux&reg; OS that uses an OS image that is provided by / created by a customer, is **not** supported by SAP.

Unix&reg; OS that uses an OS image that is provided by / created by a customer, is supported by SAP.

For more information, see [Bring-your-own-OS (custom OS Image and BYOL License)](/docs/sap?topic=sap-compute-os-design-considerations#os-byos) and [OS for IBM Power Virtual Servers](/docs/sap?topic=sap-compute-os-design-considerations#os-power).

### Configuring SUSE for the SAP HANA or SAP NetWeaver workload
{: #power-vs-configure_system}

After you deploy {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}, do the following steps within the operating system.

1. Configure root user access. You can log in to the system as a root user by using a configured SSH key. Logging in as a root user with a password through SSH is deactivated. You can log in as a root user by using the default password `root` in a virtual console window. After you log in, change the default password for the root user.
1. Start and enable the multipath daemon. For example, by running `systemctl start multipathd` and `systemctl enable multipathd`.
1. Set jumbo frames for internal network adapters. "Jumbo frames" is another way of saying network maximum transmission units (MTU) of 9000-byte payload frames. All the network components in {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s support jumbo frames. In cases where certain network components don't support jumbo frames (for example, in communication to the external world), setting MTU=9000 can cause network issues. Therefore, set MTU=9000 only on adapters that are used internally.

   You must set jumbo frames on private networks that are used for communication between multiple instances in SAP three-tier systems as follows:
   
   ```
   # cd /etc/sysconfig/network
   # vi ifcfg-ethX <--- name of ethernet device used for communication between SAP instances
   BROADCAST=''
   ETHTOOL_OPTIONS=''
   BOOTPROTO='static'
   IPADDR='xx.xx.xx.xx/xx'
   NAME='Virtual Ethernet card 0' NETWORK=''
   REMOTE_IPADDR=''
   STARTMODE='auto'
   USERCONTROL='no'
   MTU='9000'
   ```
   {: pre}

    To activate the changes, restart your network (`ifdown ethX; ifup ethX`), or set the MTU for the current configuration (with `ip link set dev <ethX> mtu 9000`).

1. Verify hostname resolution. Check that the DNS server is correctly entered in `/etc/resolv.conf` and that instance hostname resolution is possible (in the simplest case, through an entry in `/etc/hosts`).
1. Synchronize time. Make sure that the {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s that belong to the same SAP system have their time synchronized. For example, connect your partition to the time server with `ntpdate -u <ntpserver>`.
2. Install irqbalance

   ```
   zypper install -y irqbalance; systemctl start irqbalance; systemctl enable irqbalance
   
   ```
   {: pre}
4. Tune SUSE Linux Enterprise Server for the SAP HANA or SAP NetWeaver workload. On {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s, the same SUSE Linux&reg; Enterprise Server image is used for SAP NetWeaver and SAP HANA.
   - For SAP HANA, run the following command to tune the operating system for the SAP HANA workload: `saptune solution apply HANA`. Enable the tuning daemon by running `systemctl enable tuned`.
   - For SAP NetWeaver, run the following command to tune the operating system for the SAP HANA workload: `saptune solution apply NETWEAVER`. Enable the tuning daemon by running `systemctl enable tuned`.
5. Prepare file systems on extra storage volumes. If you attached extra storage volumes to your {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}, you must create file systems by using Linux&reg; Logical Volume Manager.
   - For an example with SAP HANA, see the following section [Create file systems on SLES12 SP4 by using the command-line interface](#power-vs-create_file_systems).
6. Register your SAP virtual server by using SUSE. Register your {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s by using SUSE (for example, through SUSE RMT server as described in the [SUSE documentation](https://documentation.suse.com/sles/15-SP1/html/SLES-all/cha-rmt-client.html){: external}).

### Configuring RHEL for the SAP HANA or SAP NetWeaver workload
{: #power-vs-configure_system}

After you deploy {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}, do the following steps within the operating system.

1. Configure root user access. You can log in to the system as a root user by using a configured SSH key. Logging in as a root user with a password through SSH is deactivated. You can log in as a root user by using the default password `root` in a virtual console window. After you log in, change the default password for the root user.
1. Register the operating system by Red Hat. The following example shows registration by public Red Hat repository servers.

   ```
   subscription-manager register --username <USER> --password <PASSWORD>
   
   ```
   {: pre}

1. Attach your operating system to a repository pool that contains SAP repositories.

   ```
   subscription-manager attach --pool=<POOL_ID>
   
   ```
   {: pre}

1. Configure repositories for SAP workload for RHEL 8.1 or for RHEL 8.2

   ```
   subscription-manager release --set=8.1
   ```
   {: pre}

   Or

   ```
   subscription-manager release --set=8.2
   ```
   {: pre}

1. Install RHEL updates.

   ```
   yum clean all
   subscription-manager repos --disable="*"
   subscription-manager repos --enable="rhel-8-for-ppc64le-baseos-e4s-rpms" --enable="rhel-8-for-ppc64le-appstream-e4s-rpms"     --enable="rhel-8-for-ppc64le-sap-solutions-e4s-rpms" --enable="rhel-8-for-ppc64le-sap-netweaver-e4s-rpms" --enable="rhel-  8-for-ppc64le-highavailability-e4s-rpms"
   yum -y update

   ```
   {: codeblock}

1.  Install irqbalance

    ```
    dnf install -y irqbalance; systemctl start irqbalance; systemctl enable irqbalance
    ```
    {: pre}

3. Set jumbo frames for internal network adapters. All the network components in {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s support jumbo frames. In cases where certain network components don't support jumbo frames (for example, in communication to the external world), setting MTU=9000 can cause network issues. Therefore, set MTU=9000 only on adapters that are used internally.

   You must set TCP segmentation offload (TSO) on private networks that are used for communication between multiple instances in SAP three-tier systems as follows:

    ```
    # cd /etc/sysconfig/network-scripts
    # vi ifcfg-envX <--- name of ethernet device used for communication between SAP instances
    BROADCAST=''
    ETHTOOL_OPTS='-K envX tso on '  # <---devicename may differ in config fileBOOTPROTO='static'
    IPADDR='xx.xx.xx.xx/xx'
    NAME='Virtual Ethernet card 0' NETWORK=''
    REMOTE_IPADDR=''
    STARTMODE='auto'
    USERCONTROL='no'
    MTU='9000'
    
    ```
    {: codeblock}

    To activate the changes, restart your network (`ifdown envX; nmcli con down 'System envX'; nmcli con up 'System envX'`), or set the MTU for the current configuration (with `ip link set dev <envX> mtu 9000` and `ethtool -K <envX> tso on`).
1. Verify hostname resolution. Check that the DNS server is correctly entered in `/etc/resolv.conf` and that instance hostname resolution is possible (in the simplest case, through an entry in `/etc/hosts`).
1. Prepare the operating system for SAP by using following ansible files. Ansible files for SAP configuration are distributed and updated directly by Red Hat, so the executed tasks and required parameters may vary depending on the version of “rhel-system-roles-sap” package. For more informatin, see [Red Hat Enterprise Linux System Roles for SAP](https://access.redhat.com/articles/4488731). 
   - For SAP Hana
   
   ```
   ansible-playbook /root/sap-hana.yml
   
   ```
   {: pre}

   - For SAP NetWeaver
   
   ```
   ansible-playbook /root/sap-netweaver.yml
   
   ```
   {: pre}
   
1. Install python. This step is required to verify partition readiness for SAP HANA with “chk_numa_lpm.py” script.

   ```python
   yum -y install python36 
   
   ```
   {: pre}

   This step is required only for RHEL 8.1. On RHEL 8.2, python is already installed.
   {: note}

1. Prepare file systems on extra storage volumes. If you attached extra storage volumes to your {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}, you must create file systems by using Linux&reg; Logical Volume Manager.
   - For an example with SAP HANA, see the following section [Create file systems on SLES12 SP4 by using the command-line interface](#power-vs-create_file_systems).

### Creating file systems on SLES or RHEL by using the command-line interface
{: #power-vs-create_file_systems}

You can use the following example to create volumes for file systems that are required by SAP HANA (HANA data, HANA log, and HANA shared).

The following example shows how you can use Linux&reg; Logical Volume Manager to create a file system that is called `/hana/data` based on eight storage volumes, each with a size of 110 GB. The name of the volume group is `hana_data_vg`, and the name of the logical volume is `hana_data_lv`. The volume size must be unique so it can be used as an identifier to find a required storage volume.

   The root volume is 100 GB. Use different sizes for more volumes.
   {: note}

   ```
   export pv_size=110G
   export lv_name=hana_data_lv
   export vg_name=hana_data_vg
   export mount=/hana/data
   # use following command if you DO NOT USE multipath aliases 
   devices=$(multipath -ll | grep -B 1 $pv_size | grep dm- | awk '{print "/dev/"$2}' | tr '\n' ' ')
   # use following command if you USE multipath aliases 
   devices=$(multipath -ll | grep -B 1 $pv_size | grep dm- | awk '{print "/dev/"$3}' | tr '\n' ' ')
   stripes=$(multipath -ll | grep -B 1 $pv_size | grep dm- | awk '{print "/dev/"$2}' | wc | awk '{print $1}')
   pvcreate $devices
   vgcreate ${vg_name} ${devices}
   lvcreate -i${stripes} -I64 -l100%VG -n ${lv_name} ${vg_name}
   mkfs.xfs /dev/mapper/${vg_name}-${lv_name}
   mkdir -p ${mount}
   mount /dev/mapper/$vg_name-$lv_name ${mount}
   echo "/dev/mapper/$vg_name-$lv_name ${mount} xfs defaults 1 2 " >> /etc/fstab
   ```
   {: codeblock}

### Configuring AIX for SAP NetWeaver
{: #power-vs-config-aix}

After you deploy an {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} with AIX, make sure that you follow the configuration steps thast are in [SAP Note 323816 - AIX: User limit settings](https://launchpad.support.sap.com/#/notes/323816) and [SAP Note 1972803 - SAP on AIX: Recommendations](https://launchpad.support.sap.com/#/notes/1972803).

For more information, see [Creating an AIX virtual machine (VM) with SSH keys for root login](/docs/power-iaas?topic=power-iaas-create-vm).

Also, make sure that you implement any specific configurations that might be required for the SAP products that you are planning to install. For more information, see the respective installation guides for these products.

## Verifying system readiness for SAP HANA on IBM Power Virtual Servers
{: #power-vs-verify_system}

After you provision your new {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} for SAP HANA, make sure that the memory NUMA (non-uniform memory access) configuration is correct and supported for the SAP HANA workload.

Memory NUMA configuration isn't supported by SAP HANA when {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s are deployed on a physical server where most of the CPUs and memory are already allocated.

You can run a Python script on Linux&reg; to see whether the infrastructure is set up correctly. Download the script from [SAP Note 2923962](https://launchpad.support.sap.com/#/notes/2923962){: external} and run the script on the command line (without any parameters). First, add the execution permissions to the script file. You can download the script to the `/root` location, by using the following example:

   ```python
   chmod +x /root/chk_numa_lpm.py
   
   /root/chk_numa_lpm.py
   ```

The tool checks the NUMA layout and whether a Live Partition Mobility (LPM) operation happened. Both can impact performance.

The LPM check finds the date of the last LPM in the log file from the DLPAR operations, and compares it to the date of the last boot. If it detects that a possible LPM occurred after the last boot, a warning is generated.

The NUMA check is based on SAP rules. The script checks that all cores have memory. If cores without memory are found, the script issues an error. The script also checks that the memory distribution among the cores doesn't exceed a 50% margin. If so, the script generates a warning.

The tool writes a log file (`chk_numa_lpm.log`) in the current directory with its findings. If an error or warning is generated, open a [support case](/docs/get-support?topic=get-support-using-avatar).

### Validating SAP HCMT system
{: #power-vs-sap-hcmt}

After you configure your {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s for SAP HANA, verify your system with [SAP HANA hardware and cloud measurement tools](https://help.sap.com/viewer/02bb1e64c2ae4de7a11369f4e70a6394/latest/en-US){: external}.

For more information about downloading, installing, and configuring the HCMT tool, see [SAP Note 2493172](https://launchpad.support.sap.com/#/notes/2493172){: external}. The document also provides an upload link for loading the file that is generated by HCMT so you can get a pictorial view of the health and readiness of your system.

## Networking for SAP HANA and SAP NetWeaver on IBM Power Virtual Servers
{: #power-vs-network}

When you configure SAP systems with SAP HANA as the database, the following network types are required for the usage scenarios that are provided:
* Internal network for communication between multiple instances in an SAP Landscape with different SAP System Tiering approaches
* Network for the management and storage transfers of Database systems
* Network that is used in production

IBM Power: For security reasons, a public network is not supported for SAP. Public networks can be used for other purposes (for example, for OS updates) on the virtual instances where SAP software is installed.
{: note}

### Internal network for communication between multiple instances in SAP three-tier systems
{: #power-vs-network-internal_network_three_tier}

You must provision your {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s with at least one separate private network. Private networks are used for the connection of your virtual instances with systems outside of the {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s network and for communication between multiple instances in SAP three-tier systems. Create a private network as described in [Configuring and adding a private network subnet](/docs/power-iaas?topic=power-iaas-configuring-subnet).

You can provision your {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s with a public network interface. Carefully consider the security aspects because ports 443, 80, and 22 are open. A public interface is not required for SAP.

### Network for the management of SAP HANA-based systems
{: #power-vs-network-management-network}

The following network options are available to manage your SAP systems from outside of the network.

1. {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s that host an SAP workload accessible through a virtual server in {{site.data.keyword.cloud}}.

   This virtual server can be a Windows&reg; or Linux&reg; server. This server can be used, for example:
   * For downloading software and patches. When the software is on the server, you can use SCP or WinSCP or another SSH file transfer protocol (SFTP) application to transfer the software to the SAP virtual server for installation.
   * As a VNC server for installing SAP with the SAP Software Provisioning Manager (SWPM).
   * For management with SAP Logon and SAP GUI.
   * For forwarding SAP GUI to client endpoints.

   You can set up the virtual server with a public network interface, or restrict the connection through VPN only. For more information about virtual servers in {{site.data.keyword.cloud}} classic infrastructure, see [Getting started with virtual servers](/docs/virtual-servers?topic=virtual-servers-getting-started-tutorial).

   Direct Link connection between {{site.data.keyword.cloud}} and {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s is required in this scenario. For more information, see [Direct Link Connect for Power Systems Virtual Servers](/docs/power-iaas?topic=power-iaas-ordering-direct-link-connect).

2. **{{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s that host an SAP workload connected to on-premises networks.**

   * This setup is similar to the previous one. The difference is that in this scenario, your virtual instances are connected not only to {{site.data.keyword.cloud}} but to your on-premises or other networks.
   * Direct Link on Classic is required in this scenario. Direct Link on Classic is a separate service. For more information, see [Ordering Direct Link Connect on classic](/docs/direct-link?topic=direct-link-how-to-order-ibm-cloud-direct-link-connect-classic).

### Network for production
{: #power-vs-network-for-production}

When you use SAP systems in production, it might need to configure network connections to servers that are distributed around the world. {{site.data.keyword.cloud_notm}} classic infrastructure has several network options for various customer use cases. The connection between separated and secure {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s and the {{site.data.keyword.cloud_notm}} is realized by using {{site.data.keyword.dlc_full}}. For example, access to the SAP systems that run on {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s can be routed from the external systems by an SAP router that is installed in classic {{site.data.keyword.cloud_notm}} through {{site.data.keyword.dlc_short}} connection.

## For more information

For deployment tips and resources, see the following links:

* [Preparing AIX OS on IBM Power VS for SAP NetWeaver](/docs/sap?topic=sap-power-vs-aix-nw)
* [Preparing SLES OS on IBM Power VS for SAP HANA](/docs/sap?topic=sap-power-vs-sles-hana)

