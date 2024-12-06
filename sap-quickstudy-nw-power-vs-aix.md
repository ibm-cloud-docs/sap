---

copyright:
  years: 2020
lastupdated: "2020-09-21"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, Quick Study Tutorial, network connectivity, jump server, routes, AIX, Linux, NTP, time server, SSH tunnelling

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
{:step: data-tutorial-type='step'}

# SAP NetWeaver deployment to IBM Power Virtual Server using AIX
{: #quickstudy-nw-power-vs-aix}
{: toc-content-type="tutorial"}
{: toc-completion-time="90m"}

**_INCOMPLETE: This page only appears in Test/Staging, it is not complete or ready to be published_**
{: important}

*A Quick Study, someone who is able to learn new things quickly.*
{: note}

These Quick Study Tutorials provide a single sample configuration, with less detailed instructions, as an introduction for customers who prefer hands-on tasks to increase their pace of learning.
{: shortdesc}

The following information provides an introduction for customers who are new to IBM Power Infrastructure environment.


## Getting started with the jump server
{: #jump_server-aix}

If you are using {{site.data.keyword.dl_full}}, you access your {{site.data.keyword.powerSys_notm}} instances from Linux&reg; or Windows servers that are located in {{site.data.keyword.cloud}} or on-premises networks. These systems are known as access system jump servers.

You can also use these jump servers as a software installation repository. The jump server has private and public IP addresses for accessing [SAP Software Center](https://launchpad.support.sap.com/#/softwarecenter){: external} or third-party vendor websites to download fixes or updates that can be stored on the jump server.

If you are using the Windows platform, you can install useful tools like WinSCP to transfer the software from the jump server to your AIX or Linux {{site.data.keyword.powerSys_notm}}. The following table lists tools for jump servers on Windows:

| Tool          | Purpose                               | Link                           |
|---------------|---------------------------------------|--------------------------------|
| WinSCP        | Upload and download third-party files | [WinSCP Download and Install](https://winscp.net/eng/docs/guide_install){: external}    |
| PuTTY         | SSH client                            | [Download PuTTY: latest release](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html){: external} |
| VNC Viewer    | Virtual session connections           | [Download VNC Viewer](https://www.realvnc.com/en/connect/download/viewer/windows/){: external}           |
| WinRAR        | File decompression and compression    | [WinRAR Demo Edition](https://www.rarlab.com/download.htm){: external}            |
| Java 8        | Prerequisite for SAP GUI for Windows  | [Java SE Runtime Environment 8 Downloads](https://www.oracle.com/java/technologies/javase-jre8-downloads.html){: external}                |
| SAP GUI       | SAP GUI for Windows                   | [SAP GUI 7.6 Core Download](https://launchpad.support.sap.com/#/softwarecenter/template/products/_APP=00200682500000001943&_EVENT=DISPHIER&HEADER=N&FUNCTIONBAR=Y&EVENT=TREE&TMPL=INTRO_SWDC_IU_FC&V=INST&REFERER=CATALOG-INSTALLATIONS&ROUTENAME=products/By%20Category%20-%20SAP%20Frontend%20Components){: external}      |
| Google Chrome | Internet browser                      | [Download Chrome](https://www.google.com/chrome/?brand=CHBD&gclid=EAIaIQobChMImqvg5Zb36gIVKoBQBh0QTwsyEAAYASABEgIR_vD_BwE&gclsrc=aw.ds){: external}         |
{: caption="Tools for jump servers on Windows" caption-side="top"}

If you are using Windows as a jump server, you can use Windows PowerShell which includes a built-in SSH server.

For jump servers on Linux, the list of tools is nearly identical. For file uploads and downloads, you can use FileZilla, native SFTP, or SCP functionality.

Download SAP's SAPCAR utility, so when you download SAP Installation Media that is often bundled into `.SAR` files, you can extract the files immediately on the target AIX or Linux {{site.data.keyword.powerSys_notm}}:

1. Go to the [SAP Software Center](https://launchpad.support.sap.com/#/softwarecenter){: external}.
2. In **Downloads**, search for SAPCAR.
3. Select **SAPCAR 7.21**.
4. For the operating system, select **AIX 64BIT** or **LINUX ON POWER LE 64BIT**.
5. Click the latest entry in the list and download to your jump server.
6. Transfer SAPCAR to your AIX or Linux {{site.data.keyword.powerSys_notm}}.

### Adding routes on your instance for the jump server
{: #adding_routes-aix}

After you configure {{site.data.keyword.dl_full}} and your jump server is provisioned with a private and public IP address, update the network route on your virtual server instance so it can connect to your jump server. The following example shows how to connect a jump server on Windows to the AIX virtual server instance.

#### AIX instance
{: #aix_instance-aix}

1. Go to the [Resource list](https://cloud.ibm.com/resources){: external} to find your jump server.
2. In the devices list, click your jump server to display the Devices Overview.
3. In the Network Details, note the private interface information. Click the information icon ("**i**") next to the private IP address to display the Gateway and Subnet Mask information. This information is required when you add the route to your AIX virtual server instance.
4. Log on to your AIX virtual server instance and add the route.

    For example, given the following information:

    **Private IP address:** 10.123.111.78

    **Subnet:** 255.255.255.192

    **Default Gateway:** 10.123.111.1

    You can run the following command:

    `route add -net 10.123.111.0 255.255.255.192 10.123.111.1`

    Note that the route is added temporarily; it disappears when you reboot.

    To add the route permanently across reboots, run the following command:

    ```
    chdev -l inet0  -a route=net,-hopcount,0,-netmask,255.255.255.192,-if,en0,,,,-static,10.123.111.0,10.123.111.1
    ```

1. Run `netstat -rn` and check that the entry appears in the list. Ping the jump server network, log on to the jump server, and use Windows Powershell to connect to the AIX instance from the jump server.

#### Linux
{: #linux_instance-aix}

To update the persistent route in Linux, add details to the `/etc/sysconfig/network/ifroute-XX` file, where `XX` is the network adapter. In the file, add details in the following format:

```
[Destination Address]  [Default Gateway]  [Subnet Mask]  [Network Adapter]
```

For example:

```
cat  /etc/sysconfig/network/ifroute-eth0

10.123.456.0  10.123.456.1 255.255.255.192 eth0
```

### SSH tunneling
{: #ssh_tunneling-aix}

This section describes SSH tunneling for specific SAP methods such as SAP GUI and Software Provisioning Manager (formerly known as SAPINST).

#### SSH connection
{: #ssh_connection-aix}

For a stable SSH connection, use the `ServerAliveInterval` and `ServerAliveCountMax` SSH options when you connect to {{site.data.keyword.powerSys_notm}} by using a public network interface.

```
ssh -o ServerAliveInterval=60 -o ServerAliveCountMax=600 <user>@<Public IP Address>
```

#### SSH tunneling for SAP GUI
{: #sap_gui-aix}

Establish an SSH tunnel from your client machine to the cloud server. For example, if your client machine is running on a Windows operating system, run the following command:

```
ssh -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -4 -L 3200:localhost:3200 -N -f -l root <Public IP Address> -p 22
```

In this command, 3200 is the port number that is used to connect to an application server with instance number 00. You might have to change it to connect to a different application server, for example, 3202 for instance number 02.

#### SSH tunneling for SAP Software Provisioning Manager
{: #swpm-aix}

Establish an SSH tunnel from your client machine to the cloud server. For example, if your client machine is running on a Windows operating system, run the following command:

```
ssh -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -4 -L 4237:localhost:4237 -N -f -l root 149.81.129.28 -p 22
```

4237 is the default port that is used by Software Provisioning Manager. You might have to change it if the Software Provisioning Manager execution suggests a different port.

##### Generic Command
{: #swpm-ssh}

```
ssh -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -4 -L $localport:localhost:$remoteport -N -f -l root $host -p 22
```

For more information about SSH port forwarding and SAP ports, see [SSH Port Forwarding with PuTTY](https://documentation.help/PuTTY/using-port-forwarding.html){: external}.

#### SSH configuration issues such as missing host keys
{: #ssh-config-issues_hana-aix}

For SSH tunnelling, missing SSH host keys must be generated. For more information, see [Setting up an SSH client](https://www.ibm.com/support/knowledgecenter/STSLR9_8.3.1/com.ibm.fs9200_831.doc/svc_ssh_215ubh_copy.html){: external}.



## Configuring the NTP client
{: #ntp_time_server_netweaver}

A newly provisioned {{site.data.keyword.powerSys_notm}} might not show the correct time when you run the `date` command. The initial time setting on the server often differs by 10 - 15 minutes from the correct time. This time difference can cause problems when you install and run your SAP system on the server, or when you connect the SAP system to your on-premises landscape.

You can keep the time current by configuring an NTP daemon that receives the correct time from an NTP time server. To set up NTP on AIX, see
[How to configure NTP in your Environment and common issues](https://www.ibm.com/support/pages/how-configure-ntp-your-enviornment-and-common-issues){: external}.

To enable the AIX virtual server to synchronize with the NTP server, add the network IP address of your company’s NTP server or a public NTP server to the NTP configuration file `/etc/ntpd.conf`.

Be sure to configure NTP to use the SLEWING option `-x` as recommended in the following SAP Notes:

* [1972803 - SAP on AIX: Recommendations](https://launchpad.support.sap.com/#/notes/1972803){: external}
* [2456565 - Set NTP with SLEWING option in AIX](https://launchpad.support.sap.com/#/notes/2456565){: external}

After you complete the configuration, check that the NTP daemon runs with the `-x` argument as follows:

```
root@ibmdemnw01:/home/root> ps -ef | grep -i xntpd
    root 32309530        1   0 00:13:24      -  0:00 /usr/sbin/xntpd -x
```

## Using the NIM service handler
{: #nim_service_handler}

If you plan to run a NIM Service Handler (NIMSH) on your AIX virtual server to connect to a NIM server, make sure that you avoid the following port conflict.

The NIM client daemon (NIMSH) uses reserved ports 3901 and 3902 (see [Using the NIM service handler for client communication](https://www.ibm.com/support/knowledgecenter/en/ssw_aix_72/install/adv_config_nimsh.html){: external}). If you install an SAP Central Services Instance with instance number 01 or 02 on the same virtual server, a conflict is generated. By default, the SAP Central Services Instance installation configures port number 39XX for SAP Message Server internal communication, where XX is the two-digit SAP instance number.

An SAP installation places the port number into the SAP profile file `DEFAULT.PFL`. For example, the following entry in `DEFAULT.PFL` is for an SAP Central Services instance number 01:

```
rdisp/msserv_internal = 3901
```
To avoid port conflicts, do one of the following actions:

* Use instance number 00 for your SAP Central Services instance or another instance number that is not 01 or 02.
* Change the value of the `rdisp/msserv_internal` profile parameter to a port value that does not conflict with the reserved ports of NIMSH or any other program on your server.

You can check the status of the NIM components on the AIX server as follows:

```
# lssrc -a |grep -i nim

 nimsh            nimclient                     inoperative

 nimhttp                                        inoperative
```

## Adding storage for the rootvg during AIX server provisioning
{: #adding_storage}

Configure additional space in the `rootvg` for third-party vendor prerequisite checks. After provisioning the AIX server, the `rootvg` has approximately 8 GB of free space. To prevent issues with prerequisite checks when you install database software, add another disk with a partition size of 30 GB.

1. During virtual server instance provisioning, create a new storage volume that is 30 GB.
2. After the AIX server is provisioned, run the `lspv` command for an overview of `hdisks`. The output shows one disk that belongs to the rootvg and another disk that does not have a physical volume identifier and a volume group assigned:
    ```
    # lspv
    hdisk0          none                                None
    hdisk1          00f6db0af58e9775                    rootvg          active
    ```
1. To extend the `rootvg` volume group, run the following command:

    ```
    extendvg rootvg <new hdisk>
    ```

## Extending `/tmp`
{: #extending_tmp}

SAP installations as well as Db2 and Oracle Database software installations use PREINSTALLER checks to make sure that your system is database-ready. One of the checks is the amount of free space in `/tmp`. For example, at least 5 GB of available space is expected for Oracle installations. For Db2, the free space check expects 512 MB.

To extend `/tmp`:

1. Check the space available in the `rootvg`. Run the following command:
    ```
    lsvg rootvg | grep "FREE PPs"
    ```
    If more storage is required, you should have sufficient space to extend `/tmp` because you already added an extra disk.

1. Run the following command:
    ```
    chfs -a size=5G /tmp
    ```
    The size of the logical volume on which `/tmp` resides (typically `hd3`) and the file system capacity is increased to 5 GB.

You can run the same command with `+5G /tmp` to append the size of the file system and add 5 GB to the existing size.

You can enter a higher value to extend the file system further, for example, if you need more capacity, or if there are issues with free space in either “`/`” or `/tmp` during the PREINSTALLER check.

## Extending or adding paging space
{: #extending_paging_space}

Prechecks can include a paging space check, which analyzes your system and determines whether your server has sufficient paging space for the database or application installation.

Some databases require at least 32 GB. Instead of increasing the size of the current paging space `hd6`, create a second paging volume when you define additional storage for the database or application volume groups.

After you create volume groups for applications and databases, you can create the new paging space to reside on the volumes within the volume groups.

The `lsps -s` command shows the total amount of paging space on your system, for example:

```
root@ibmdemnw01:/swrepo/AIX> lsps -s
Total Paging Space   Percent Used
      25760MB	             0%
```

The `lsps -a` command shows	the distribution of the paging space that is defined on your system, for example:

```
root@ibmdemnw01:/swrepo/AIX> lsps -a
Page Space  Physical Volume   Volume Group   Size %Used 	Active	Auto	Type Ch
hd6         hdisk4            rootvg	       160MB   20   yes     yes     lv
paginglv	  hdisk2            fr02swreposvg 25600MB	  0   yes     yes     lv
root@ibmdemnw01:/swrepo/AIX> I
```
This example shows that a paging space was defined in the volume group `fr02swreposvg` on `hdisk2` called `paginglv` in addition to the standard paging space `hd6` in the `rootvg`.

If your new AIX server instance doesn't have sufficient paging space when you begin the implementation phase of your database or application, it can lead to multiple errors, for example, PGSPACE_KILL where the VMM starts to prune system processes. Be sure to follow third-party software recommendations for their products.

## AIX Toolbox for Linux Applications
{: #aix_toolbox}

When you download SAP packages from the [SAP Marketplace](https://support.sap.com/en/my-support/software-downloads.html){: external}, sometimes the files are packed into multispanning archives. Tools are required to decompress the files and rebuild the packages.

For more information, see [SAP Note 886535 - Downloading multispanning archives (RAR archive)](https://launchpad.support.sap.com/#/notes/886535){: external} and [SAP Note 960755 - Unpacking ZIP archives in Unix](https://launchpad.support.sap.com/#/notes/960755){: external}.

The required tools, unrar and unzip, can be downloaded from the [AIX Toolbox For Linux Applications](https://www.ibm.com/support/pages/aix-toolbox-linux-applications-downloads-alpha#U){: external}. Download the tools and use RPM to install them on your AIX system. You might need to install additional filesets as a prerequisite. These files might already be in the limited AIX repository that is supplied when the AIX server is provisioned.

Additional installation files can be found on the `/usr/sys/inst.images` mountpoint.

The `vnc` package is another useful tool in the AIX Toolbox for Linux Applications. After you install the `vnc` package into your AIX server, you will be able to start a VNC server on your AIX server. Then you can use a VNC viewer such as TightVNC on the jump server or another desktop to connect to your AIX server to start an SAP installation by using Software Provisioning Manager (formerly known as SAPINST) with the web front end. For more information, see [Software Provisioning Manager](https://support.sap.com/en/tools/software-logistics-tools/software-provisioning-manager.html){: external} and [Installing by using a jump server](#installing_jump_server).

## Required filesets for Oracle, IBM Db2, and SAP MaxDB
{: #filesets_for_db}

### Oracle 12 on AIX {{site.data.keyword.IBM_notm}} Power Systems
{: #oracle_12_filesets}

Oracle 12 is supported on AIX 7.1 and 7.2. For a list of required operating system filesets and APARs, see the [system requirements](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/axdbi/operating-system-requirements-for-ibm-aix-on-power-systems-64-bit.html#GUID-265A2566-3693-4285-A514-5AB81DF91A85){: external}.

Run the following command to check whether the filesets are available:

```
lslpp -l |egrep -w 'bos.adt.base|bos.perf.libperfstat|bos.adt.lib| bos.adt.libm|bos.perf.perfstat|bos.perf.proctools|xlC.aix61.rte|xlC.rte'
```

### Oracle 19 on AIX {{site.data.keyword.IBM_notm}} Power Systems
{: #oracle_19_filesets}

Oracle 19 is supported on AIX 7.1 and 7.2.

For a list of operating system filesets and APARs, see the [system requirements](https://docs.oracle.com/en/database/oracle/oracle-database/19/axdbi/operating-system-requirements-for-ibm-aix-on-power-systems-64-bit.html#GUID-265A2566-3693-4285-A514-5AB81DF91A85){: external}.

### IBM Db2
{: #requirements_for_db2}

Before you install IBM Db2, make sure that the system requirements are met.

For the software requirements for Db2 and SAP, see [Installation of SAP Systems Based on the Application Server ABAP and DB2](https://help.sap.com/doc/4f95f7ac741a1014956dd879c2537334/CURRENT_VERSION/en-US/db6_inst_71x_unix_abap.pdf){: external}.

For dependencies and download details for Db2, see [System requirements for IBM Db2 for Linux, UNIX, and Windows](https://www.ibm.com/support/pages/node/612045){: external}.

The following additional filesets must be installed for Db2 installations:

* bos.adt.* - Base Application Development
* bos.perf.* - Performance and diagnostics tools
* perfagent.tools - Performance monitoring tools

You can run the following command to check whether the filesets are available:

```
lslpp -l |egrep -w 'bos.adt.base|bos.perf.libperfstat|bos.adt.lib| bos.adt.libm|bos.perf.perfstat|bos.perf.proctools'
```
### SAP MaxDB
{: #requirements_for_sap_maxdb}

Before you install SAP MaxDB, make sure that the required AIX filesets are installed. Run the following command:

```
lslpp -l | egrep -w 'bos.mp64|bos.rte.libc*|xlC.aix61.rte|xlC.sup.aix50.rte'
```
* bos.mp64 - Base Operating System 64-bit
* bos.rte.libc - libc Library
* xlC.aix61.rte	- IBM XL C++ Runtime for AIX 6.1
* xlC.sup.aix50.rte	- XL C/C++ Runtime for AIX 5.2
* bos.mp64 - Base Operating System 64-bit
* bos.rte.libc - libc Library

For more information about the required AIX filesets, see [SAP Note 720261 - System prerequisite AIX - liveCache/MaxDB 7.4-7.9](https://launchpad.support.sap.com/#/notes/720261){: external}.

I/O usage is intensive during the Software Provisioning Manager installation phase. If there isn't enough paging space to complete the Software Provisioning Manager MaxDB tasks, the stability of the system can be impacted. Before you install third-party tools, make sure that sufficient paging space is provisioned according to the third-party software manufacturer's space recommendations.
{: important}

## Storage and the AIX Logical Volume Manager
{: #storage_aix_lvm}

Follow these recommendations on creating storage and the AIX Logical Volume Manager (LVM) for the database and application layer.

### Storage and volume affinity
{: #storage_volume_affinity}

When you create storage by using the {{site.data.keyword.cloud}} console, specify volume affinity to prevent issues with new volume discovery on existing virtual server instances. Keep the following points in mind:

* You can't mix Tier 1 and Tier 3 storage.
* After you provision storage with volume affinity, you can't go back and change it. Carefully plan your storage layer and make sure that your configuration is correct.
* After you provision new volume, you can toggle bootable and sharing switches. Sharing is especially useful for an Oracle RAC implementation.

For more information about volume affinity, see [Adding network block storage](/docs/sap?topic=sap-power-vs-set-up-infrastructure#power-vs-adding-block-storage).

### Logical volumes and mount points
{: #storage_aix_lvm_mount_points}

Be sure to follow the recommendations of third-party software vendors about the configuration of logical volumes and mount points for the file systems. For more information, see the following links:

[Installation of SAP Systems Based on the Application Server ABAP of SAP NetWeaver with IBM DB2](https://help.sap.com/doc/4f95f7ac741a1014956dd879c2537334/CURRENT_VERSION/en-US/db6_inst_71x_unix_abap.pdf){: external}

[SAP Note 2172935 - Installation - SAP Systems based on SAP NetWeaver : Oracle Database](https://launchpad.support.sap.com/#/notes/2172935){: external}

[Directory and Filesystem Structure for MaxDB](https://maxdb.sap.com/doc/7_6/44/118d969c471a75e10000000a1553f6/content.htm){: external}

#### More recommendations
{: #storage_aix_lvm_mount_points-more-recommend}

* Create separate volume groups for database and applications. For example, for an Oracle installation, create the `oraclevg`. For SAP products, create the `appsvg` or `sapvg`, depending on the products that you choose to install.
* Allocate sufficient storage to each of the volume groups and follow a standard naming convention when you create logical volumes. Add your preferred SID to the logical volume name.

### Creating separate storage for an installation repository
{: #storage_aix_swpm}

SAP Software Provisioning Manager requires additional files that will be used for the installation of SAP products such as exported images, SAP kernel files, and database clients.
The disk space requirements depend on the SAP product and database that you plan to install.

Calculate the sum of SAP packages on the [SAP Marketplace](https://support.sap.com/en/my-support/software-downloads.html){: external} that are required for your installation. Before you install SAP products by using Software Provisioning Manager, provide the required storage.
Create a dedicated volume group and logical volume and mount it on `/swrepo/SAP`.

## Installing the system
{: #installation_overview}

Before you install the system by using Software Provisioning Manager (SWPM), follow these preliminary steps:

1. Create a user in the `smitty user` menu called `swpmuser` so you can avoid running the installation as the `root` user. If you didn't create a separate `rootgrp`, keep the primary group as `system`. Also include this user in the system and root groups so it is basically an administrative user. You will use this user when you start the Software Provisioning Manager tool as a Remote Access Tool user.

1. Create the following directory structure under your software repository mount point `/swrepo/SAP` for Software Provisioning Manager and other required software as recommended here: [Creating separate storage for an installation repository](#storage_aix_swpm).

| Directory             | Purpose                                                      |
|-------------------------|--------------------------------------------------------------|
| `/swrepo/SAP/SWPM/`       | Location to unpack the Software Provisioning Manager SAR file                         |
| `/swrepo/SAP/SWPM/tmp`    | Temporary storage and cache for Software Provisioning Manager installation            |
| `/swrepo/SAP/kernel`      | Location for the SAP kernel files                            |
| `/swrepo/SAP/export`      | Location for the export files needed                         |
| `/swrepo/SAP/DB`         | Location for the database product and client software        |
| `/swrepo/SAP/others`      | Location for miscellaneous software such as saphostagent |
| `/swrepo/SAP/prereqcheck` | Location for the prerequisite check output                        |
{: caption="Software repository directory structure" caption-side="top"}

## Installing by using a client machine
{: #installing_client_machine}

Follow these steps to install the system by using a client machine.

1. Set up SSH tunneling on the client machine. For more information, see [SSH tunneling](#ssh_tunneling-aix).
1. Set a variable on the server for `TMPDIR` on the AIX server:
    ```
    export TMPDIR=/swrepo/SAP/SWPM/tmp
    ```
    Otherwise, Software Provisioning Manager uses the system-wide `/tmp` directory for `sapinstdir` logging and quickly runs out of space.

1. Unpack the Software Provisioning Manager SAR file on the server.
1. Unpack the installation into the respective folders.
1. Run the Prerequisites Check to make sure that all requirements are met for the installation.
    1. Go to the `/swrepo/SAP/prereqcheck` directory.
    2. Run the Software Provisioning Manager `sapinst` executable as follows:
        ```
        /swrepo/SAP/SWPM/sapinst SAPINST_REMOTE_ACCESS_USER=swpmuser SAPINST_HTTPS_PORT=443
        ```
1. Open a browser on your client machine and go to this URL:
    ```
    https://<Public IP address of your AIX server>:443/sapinst/docs/index.html
    ```
1. In login box, enter the swpmuser and the password that you defined.
1. Open the Prerequisites Checker. For example, for Db2: On the Welcome page, select **SAP NetWeaver 7.5 > IBM Db2 for Linux, UNIX, and Windows > Preparations > Prerequisites Check**.
1. On the Prerequisite Checker Options page, select the first three options.
2. Click **Next** to proceed through the wizard until the Prerequisites Checker Results are displayed.

If you see a swap size MEDIUM result, check that you have sufficient swap space on your system.
{: note}

After you complete the prerequisites check, you can proceed with the SAP NetWeaver installation for the application server, central services, and database installation.

## Installing by using a jump server
{: #installing_jump_server}

Follow these steps to install the system by using the jump server and tools that are installed on your AIX virtual server.

### On the AIX server
{: #on_aix_server}

1. Set a password. The VNC executable can be started multiple times and open multiple channels. Set a password to prevent other people from working on your channel.
    1. Go to the `/home/root/.vnc` directory.
    2. Run the `vncpasswd` command.
    3. Set the password for your connection.
1. In the `root` directory, run the `vncserver` command to start a vncserver session.

### On your laptop or jump server
{: #on_laptop_or_jump_server}

#### Opening a terminal
{: #open_terminal}

1. Connect the VNC Viewer software to the AIX server. In the VNC Viewer window, enter the address that was displayed when you started the vncserver on AIX. To use a hostname, make sure to update the `/etc/hosts` file so that the hostname resolves to IP.

    Your new server must be resolvable in the Windows or operating system of your laptop (for direct connections) and in your jump server. If an encryption warning is displayed, click **Continue**. When prompted, enter your password and click **OK**.
    {: important}

1. Open a terminal. Enter `xterm` or `aixterm` on the command line.

#### Setting variables
{: #set_variables}

When you run SAPINST, set variables to ensure that there are enough resources to start the executable and that it exports the correct details about you and your system.

1. Echo the `$DISPLAY` variable. The output should show the same details as the connection.
2. Run the `xhost +` command to disable access control.
3. Run the `xauth` command to list the currently assigned keys that are needed to connect.

    Make a copy of the entries. If you need to switch to an SAP ACCOUNT for any reason, you can add the keys, access the session, and export the $DISPLAY again.
    {: tip}

1. Set the following variables for the installation session that are needed for SAPINST:

    ```
    export SAPINST_REMOTE_ACCESS_USER=<Your User ID here, root or swpmuser>
    export SAPINST_USE_HOSTNAME=<Hostname or Virtual Address>
    export LIBPATH=/sapmnt/<SID>/exe
    export CPIC_MAX_CONV=500
    export JAVA_HOME=/usr/java8_64
    export PATH=$JAVA_HOME/bin:$PATH
    export TMP=/swrepo/SAP/SWPM/tmp
    export TEMP=/swrepo/SAP/SWPM/tmp
    export TMPDIR=/swrepo/SAP/SWPM/tmp
    umask 022
    ulimit -n 32000
    ulimit -d unlimited
    ulimit -s unlimited
    ulimit -f unlimited
    ulimit -m unlimited
    ```
    * The `$LIBPATH` variable should point to your `/sapmnt/<SID>/exe` directory, which should already be on your system.
    * `JAVA_HOME` should point to the `/usr/javaX_64` version installed on your server.
    * The exports for the `TEMP;`, `TEMPDIR`, and `TMP` directories ensure that SAPINST doesn't use the `/tmp` directory on AIX for the installation.
    * Make sure that the temp exports are set to a dedicated filesystem where you have unpacked SAPINST.
    * The `umask` and `ulimit` settings are recommended.

#### Testing SAPINST and installing
{: #test_sapinst}

1. In the SAPINST directory, run the following command:
    ```
    ./sapinst SAPINST_SLP_MODE=true
    ```
1. When prompted to confirm, enter `y`.
1. In your browser, go to the following URL:

    https://<AIX Server IP Address>:4237/sapinst/docs/index.html

    For example:

    `https://ibmdemnw01.local.demo:4237/sapinst/docs/index.html`

1. When prompted, log in with the Software Provisioning Manager user or root user.
2. Select your preferred product and proceed to install.

#### Port forwarding
{: #port_forwarding}

If you're using a Windows operating system and the VNC port isn't working or it's closed, you need to tunnel to make the port for VNC 5901 usable. If you're using a recent version of Windows PowerShell, an SSH server is included so you can use SSH commands on the command line. For more information, see [SSH tunneling](#ssh_tunneling-aix).

#### Common problems and solutions
{: #common_issues}  
Here are some common issues that occur with Software Provisioning Manager:
* Capacity of the `/tmp` directory during the pre-flight checks. The recommendation is at least 5 GB.
* Make sure that there's at least 32 GB of paging space. This can be adjusted after the AIX server is installed with the required database and applications. The recommendation is to run the required workload and tune the I/O resources.
* Extend `hd4` or “`/`” to 150 MB. Otherwise, you get a WARNING during the prerequisite tests for Db2 and Oracle.
* Check `/etc/security/login.cfg` during installation. For more information, see [SAP Note 2360008 - 3004-703 Check "/etc/security/login.cfg" during installation](https://launchpad.support.sap.com/#/notes/2360008){: external}.
* `Required EXPORT750 (Folder EXP1) missing` - If you want to install the system in another language, download the EXP1 compressed file and associated LANG .zip files. Download them to the jump server or `swrepos` filesystem and specify them when the Software Provisioning Manager installer requests them.
* R3load TestConnect fails - To resolve this issue, refer to the following SAP Notes:

    [SAP Note 1875902 - R3load -testconnect fails during using SWPM in step testDatabaseConnection](https://launchpad.support.sap.com/#/notes/1875902){: external}

    [SAP Note 2805859 - A1EEGEN 000 (DBS) DbSlErrorMsg rc = 28 'no connection info in DBCON found'](https://launchpad.support.sap.com/#/notes/2805859){: external}

## Information resources for SAP NetWeaver
{: #information_resources_netweaver}

The following links will assist you in the installation and configuration of your {{site.data.keyword.powerSys_notm}} instances and databases with SAP products. Links with numbers in the title point to the SAP Support Portal.

### Cloud-related resources - {{site.data.keyword.IBM_notm}} Power Virtual Servers
{: #cloud_resources}

| Link                                                                          | Description                               |
|-------------------------------------------------------------------------------|-------------------------------------------|
| [2855850 - SAP Applications on IBM Power Virtual Servers](https://launchpad.support.sap.com/#/notes/2855850){: external}               | Supported SAP applications on {{site.data.keyword.IBM_notm}} Power Virtual Servers        |
| [1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654){: external}                                    | IaaS environments                         |
| [2923984 - SAP on IBM Power Virtual Servers: Support prerequisites](https://launchpad.support.sap.com/#/notes/2923984){: external}     | Support prerequisites                    |
| [2947579 - SAP HANA on IBM Power Virtual Servers](https://launchpad.support.sap.com/#/notes/2947579){: external}                       | SAP HANA and virtual server instances                         |
| [2923962 - Check SAP HANA NUMA Layout on {{site.data.keyword.IBM_notm}} Power Systems Virtual Servers](https://launchpad.support.sap.com/#/notes/2923962){: external}     | Checking the NUMA layout                  |
| [2932766 - SAP on IBM Power Virtual Servers: Key Monitoring Metrics](https://launchpad.support.sap.com/#/notes/2932766){: external} | Key Monitoring Metrics                    |
{: caption="Cloud-related resources - {{site.data.keyword.IBM_notm}} Power Virtual Servers" caption-side="top"}

### Operating systems - AIX
{: #os_aix}

| Link                                                                                  | Description                                                |
|---------------------------------------------------------------------------------------|------------------------------------------------------------|
| [1780629 - AIX: Minimal OS Requirements for SAP Kernel](https://launchpad.support.sap.com/#/notes/1780629){: external}                                | Minimum operating system requirements for AIX                                |
| [2267287 - Using SAP systems with AIX 7.2](https://launchpad.support.sap.com/#/notes/2267287){: external}                                             | SAP Systems on AIX 7.2                                     |
| [1541935 - Using SAP systems with AIX 7.1](https://launchpad.support.sap.com/#/notes/1541935/E){: external}                                              | SAP Systems on AIX 7.1                                     |
| [1972803 - SAP on AIX: Recommendations](https://launchpad.support.sap.com/#/notes/1972803){: external}                                                 | Guidance on how to optimally configure AIX for SAP     |
| [2630086 - Avoid signal 33, out of memory on AIX](https://launchpad.support.sap.com/#/notes/2630086){: external}                                       | Signals paging and memory situations - recommended      |
| [AIX Service Strategy and Best Practices](https://www.ibm.com/support/pages/node/3464613){: external}                                               | The AIX journey and strategy                               |
| [IBM Knowledge Center AIX](https://www.ibm.com/support/knowledgecenter/ssw_aix){: external}                                                             | A central link for AIX information                                |
| [Troubleshooting AIX 7.2](https://www.ibm.com/support/knowledgecenter/ssw_aix_72/navigation/troubleshooting.html){: external}                                                               | Useful problem solving                               |
| [IBM Fix Central](https://www.ibm.com/support/fixcentral/){: external}                                                                       | Fix Central for your AIX filesets and operating system upgrade source    |
| [Services and Support Best Practices POWER9](https://www.ibm.com/support/pages/service-and-support-best-practices-power-systems){: external}                                            | Download the PDF and see the command reference        |
| [Fix Level Recommendation Tool - FLRT](https://www14.software.ibm.com/support/customercare/flrt/){: external}                                                  | Cross-compatibility and fix recommendations tool           |
| [IBM AIX Developer](https://developer.ibm.com/components/aix/){: external}                                                                     | AIX developer website                                      |
| [IBM AIX Enhancements and Modernization](https://www.redbooks.ibm.com/abstracts/sg248453.html?Open){: external}                                                | The latest updated IBM Redbook for AIX                     |
{: caption="Operating systems - AIX" caption-side="top"}

### Operating systems - Linux
{: #os_linux-aix}

| Link | Description |
|---|---|
| [2378874 - Install SAP Solutions on Linux on {{site.data.keyword.IBM_notm}} Power Systems (little endian)](https://launchpad.support.sap.com/#/notes/2378874){: external}                                        | Installing SAP Solutions on {{site.data.keyword.IBM_notm}} Power Systems    |
| [2369910 - SAP Software on Linux: General information](https://launchpad.support.sap.com/#/notes/2369910){: external}                                                                 | General information for SAP software on Linux    |
| [765424 - Linux: Released IBM Hardware - POWER based servers](https://launchpad.support.sap.com/#/notes/765424){: external}                                                           | Power-based servers                              |
| [1002461 - Support of IBM Dynamic LPAR and Micropartitioning](https://launchpad.support.sap.com/#/notes/1002461){: external}                                                          | LPAR and micro partitioning                      |
| [1122387 - Linux: SAP Support in virtualized environments](https://launchpad.support.sap.com/#/notes/1122387){: external}                                                             | SAP Support in virtualized environments          |
| [1400911 - Linux: SAP NetWeaver on Red Hat KVM - Kernel-based Virtual Machine](https://launchpad.support.sap.com/#/notes/1400911){: external}                                         | Red Hat KVM - Kernel-based virtual machine        |
| [2526952 - Red Hat Enterprise Linux for SAP Solutions](https://launchpad.support.sap.com/#/notes/2526952){: external}                                                                 | RHEL for SAP Solutions *** Central Note for RHEL |
| [1631106 - Red Hat Enterprise Linux for SAP Applications](https://launchpad.support.sap.com/#/notes/0001631106){: external}                                                           | RHEL for SAP applications                        |
| [2002167 - Red Hat Enterprise Linux 7.x: Installation and Upgrade](https://launchpad.support.sap.com/#/notes/2002167){: external}                                                     | RHEL 7x installation and upgrading               |
| [936887 - End of maintenance for Linux distributions](https://launchpad.support.sap.com/#/notes/936887){: external}                                                                   | Maintenance calendar and product maturity        |
| [2679703 - Linux on {{site.data.keyword.IBM_notm}} Power Systems -- SAP monitoring recommendations](https://launchpad.support.sap.com/#/notes/2679703){: external}                                               | SAP monitoring recommendations                   |
| [2578899 - SUSE Linux Enterprise Server 15: Installation Note](https://launchpad.support.sap.com/#/notes/2578899){: external}                                                         | SLES 15 installation note                        |
| [1275776 - Linux: Preparing SLES for SAP environments](https://launchpad.support.sap.com/#/notes/1275776){: external}                                                                 | Preparing SLES for SAP environments              |
| [SUSE Best Practices Library](https://documentation.suse.com/sbp/all/?context=sles-sap){: external}                                                                                   | A useful collection of SUSE documentation        |
| [187864 - Linux: Locale Support on Linux](https://launchpad.support.sap.com/#/notes/187864){: external}                                                                               | Locale support for Linux                         |
| [SAP on {{site.data.keyword.IBM_notm}} Power Systems running Linux](https://community.sap.com/topics/linux/sap-on-ibm-power-systems-on-linux){: external}                                       | News about SAP on {{site.data.keyword.IBM_notm}} Power Systems running Linux                 |
| [Technical Resource for SAP Business Applications on Red Hat](https://blogs.sap.com/2013/02/28/technical-resources-for-sap-business-applications-on-red-hat/){: external}  | A useful collection of links for SAP and Redhat  |
| [SUSE Enterprise Server for IBM POWER](https://www.suse.com/products/power/){: external}                                                                                              | IBM and SUSE                                     |
{: caption="Operating systems - Linux" caption-side="top"}


### Databases - Db2
{: #db_db2}

| Link                                                                                                                                                                                       | Description                         |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------|
| [Installation of SAP Systems Based on the Application Server ABAP and IBM DB2](https://help.sap.com/doc/4f95f7ac741a1014956dd879c2537334/CURRENT_VERSION/en-US/db6_inst_71x_unix_abap.pdf){: external} | Downloadable as a PDF File          |
| [System requirements for IBM Db2 for Linux, UNIX, and Windows](https://www.ibm.com/support/pages/node/612045){: external}                                                                              | System requirements DB2             |
| [IBM POWER9 and SMT performance for IBM Db2](https://developer.ibm.com/articles/power9-and-smt-perf-for-db2/){: external}                                                                              | IBM POWER9 and Db2                  |
| [Currently supported DB2 software levels and Fixpacks](https://launchpad.support.sap.com/#/notes/101809){: external}                                                                                   | Db2 software levels + Fixes         |
| [Currently supported database features DB2](https://launchpad.support.sap.com/#/notes/1555903){: external}                                                                                             | Db2 feature support                 |
| [Required File systems for DB2 and SAP Netweaver](https://help.sap.com/viewer/4fbd902c7c76410bb82c6311dd4dc94b/CURRENT_VERSION/en-US/713eb64f45c6448c8dbe8a51b85680ee.html){: external}                | Filesystem overview and mountpoints |
{: caption="Databases - Db2" caption-side="top"}

### Databases - Oracle
{: #db_oracle}

| Link                                                                                                                                                                                                                                                                                          | Description                                                     |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| [2172935 - Installation - SAP Systems based on SAP NetWeaver : Oracle Database](https://launchpad.support.sap.com/#/notes/2172935){: external}                                                                                                                                                            | Also includes the Filesystems that should exist and mountpoints |
| [2799900 - Central Technical Note for Oracle Database 19c](https://launchpad.support.sap.com/#/notes/2799900){: external}                                                                                                                                                                                 | Oracle 19c                                                      |
| [2660020 - Central Technical Note for Oracle Database 18c](https://launchpad.support.sap.com/#/notes/2660020){: external}                                                                                                                                                                                 | Oracle 18c                                                      |
| [1496927 - Protection of SAP instances through Oracle Clusterware](https://launchpad.support.sap.com/#/notes/1496927){: external}                                                                                                                                                                         | Also, pre-reqs for Oracle 18c                                   |
| [527843 - Oracle RAC support in the SAP environment](https://launchpad.support.sap.com/#/notes/527843){: external}                                                                                                                                                                                        | Oracle RAC support                                              |
| [2470660 - Central Technical Note for Oracle Database 12c Release 2 (12.2)](https://launchpad.support.sap.com/#/notes/2470660){: external}                                                                                                                                                                | Oracle 12.2c                                                    |
| [Oracle Community on SAP](https://community.sap.com/topics/oracle){: external}                                                                                                                                                                                                                            | Oracle Community link                                           |
| [Managing the Stability and Performance of current Oracle Database versions running AIX on Power Systems including POWER9](https://www.ibm.com/support/pages/managing-stability-and-performance-current-oracle-database-versions-running-aix-power-systems-including-power9){: external}                                                                                              | Oracle stability and performance                                |
| [Operating System Checklist for Oracle Database on IBM AIX on POWER Systems (64-Bit)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/axdbi/operating-system-checklist-for-oracle-database-on-ibm-aix-on-power-systems-64-bit.html#GUID-0A0691E3-7BC8-48F0-A189-7D978DDB7E45){: external} | Oracle, AIX and POWER systems                                   |
| [2540847 - SAP Guides for Oracle Database Upgrade](https://launchpad.support.sap.com/#/notes/2540847){: external}                                                                                                                                                                                         | Oracle guide collection                                         |
| [2086029 - Corrections for SAP Database Upgrade Guides for Oracle](https://launchpad.support.sap.com/#/notes/2086029){: external}                                                                                                                                                                         | Corrections to the above SAP Note as reference                  |
| [Oracle Database 11g and 12c on {{site.data.keyword.IBM_notm}} Power Systems S924, S922 and S914 with POWER9 processors](http://www-03.ibm.com/support/techdocs/atsmastr.nsf/WebIndex/WP102750){: external}                                                                                                                          | Further details on POWER9 and Oracle                            |
| [Supported AIX versions with Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/axdbi/supported-ibm-aix-7-2-versions.html#GUID-7BDD5193-1943-4103-9795-CF9CCEC283B1){: external}                                                                                                     | Fully supported setups with AIX and Oracle                      |
| [Server Configuration Checklist with Oracle Installations and AIX](https://docs.oracle.com/en/database/oracle/oracle-database/19/axdbi/server-configuration-checklist-for-oracle-database-installation.html#GUID-A29CAD13-BF30-4347-A3D8-324C37B9C350){: external}                                        | Useful for setting up Oracle on AIX                             |
| [Checking Asynchronous Input Output Processes](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/axdbi/checking-asynchronous-input-output-processes.html#GUID-2F121FA1-0A7F-48BB-95F2-1661D8046BDC){: external}                                                                             | Useful for Oracle tuning on AIX OS                              |
| [Configuring Shell Limits and System Configuration Parameters for IBM AIX](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/axdbi/configuring-shell-limits-and-system-configuration-parameters-for-ibm-aix.html#GUID-639E7025-B3BD-409F-AC59-C0A947406662){: external}                     | Explanation on User Limits and System configuration             |
| [Central Landing page for Oracle Versions and Products](https://docs.oracle.com/en/database/oracle/oracle-database/index.html){: external}                                                                                                                                                                | Useful to bookmark                                              |
| [Oracle Client Installation](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/axcli/index.html){: external}                                                                                                                                                                                | Oracle Client Installation                                      |
| [Oracle 18c Installation and Upgrade repository](https://docs.oracle.com/en/database/oracle/oracle-database/18/install-and-upgrade.html){: external}                                                                                                                                                      | Useful hints and guidance for 18c                               |
{: caption="Databases - Oracle" caption-side="top"}

### Databases - MaxDB
{: #db_maxdb}

| Link                                                                                                                                                                                                                                          | Description                           |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------|
| [2365014 - Installation of SAP Systems Based on SAP NetWeaver: SAP MaxDB](https://launchpad.support.sap.com/#/notes/2365014){: external}                                                                                                                  | MaxDB installation guide              |
| [1706928 - Inst. SAP Sys. Based on NW 7.1 and higher: SAP Max DB, UNIX](https://launchpad.support.sap.com/#/notes/1706928){: external}                                                                                                                    | SAP installation note with MaxDB      |
| [1020175 - FAQ: SAP MaxDB installation, upgrade or applying a patch](https://launchpad.support.sap.com/#/notes/1020175){: external}                                                                                                                       | MaxDB FAQ                             |
| [SAP and MaxDB Community Wiki](https://wiki.scn.sap.com/wiki/display/MaxDB/SAP+MaxDB+Installation+and+Upgrade+Guides){: external}                                                                                                                         | MaxDB Community Wiki                  |
| [Installing MaxDB with SL Tools](https://support.sap.com/en/tools/software-logistics-tools.html){: external}                                                                                                                                              | Software Logistics Tools landing page |
| [SAP MaxDB Community page](https://community.sap.com/topics/maxdb){: external}                                                                                                                                                                            | MaxDB Community page                  |
| [Problems during installation, upgrade or migration of MaxDB](https://wiki.scn.sap.com/wiki/display/MaxDB/Problems+during+installation%2C+upgrade+or+migration#Problemsduringinstallation,upgradeormigration-ProblemswithSAPInstandSAPUp/SUM) | Another Wiki page (very useful){: external}       |
| [The Complete SAP MaxDB Documentation](https://maxdb.sap.com/documentation/){: external}                                                                                                                                                                  | Collection of Guides for MaxDB        |
| [820824 - FAQ: SAP MaxDB/liveCache technology](https://launchpad.support.sap.com/#/notes/820824){: external}                                                                                                                                              | MaxDB and LiveCache                   |
| [767598 - Available SAP MaxDB documentation](https://launchpad.support.sap.com/#/notes/767598){: external}                                                                                                                                                | Available MaxDB documentation         |
| [819641 - FAQ: SAP MaxDB performance](https://launchpad.support.sap.com/#/notes/819641){: external}                                                                                                                                                       | MaxDB performance                     |
| [720261 - System prerequisite AIX - liveCache/MaxDB 7.4-7.9](https://launchpad.support.sap.com/#/notes/720261){: external}                                                                                                                                | Important SAP Note                    |
| [725489 - SAP MaxDB performance analysis tools](https://launchpad.support.sap.com/#/notes/725489){: external}                                                                                                                                             | Performance tracking                  |
{: caption="Databases - MaxDB" caption-side="top"}

### Applications - SAP
{: #applications_sap}

| Link                                                                                                                                                                                                             | Description                                       |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------|
| [SAP Guide Finder](https://help.sap.com/viewer/nwguidefinder){: external}                                                                                                              | Recommended for all installations                 |
| [1704753 - Inst.Systems Based on NetWeaver on UNIX - Using Software Provisioning Manager 1.0](https://launchpad.support.sap.com/#/notes/1704753){: external}                                                                 | Software Provisioning Manager information for version 1.0                  |
| [Installation Guides - Application Server Systems - Software Provisioning Manager 1.0](https://help.sap.com/viewer/30839dda13b2485889466316ce5b39e9/CURRENT_VERSION/en-US/c8ed609927fa4e45988200b153ac63d1.html){: external} | Additional content                                |
| [{{site.data.keyword.IBM_notm}} Power Systems Planning and Monitoring Best Practices for SAP Applications](https://www.redbooks.ibm.com/redpieces/abstracts/redp5580.html?Open){: external}                                                              | IBM Redbook for planning and monitoring SAP       |
| [{{site.data.keyword.IBM_notm}} Power Systems Infrastructure I/O for SAP Applications](https://www.redbooks.ibm.com/abstracts/redp5581.html?Open){: external}                                                                                            | IBM Redbook for infrastructure planning and SAP   |
| [2158828 - Minimal DB system platform requirements for SAP NetWeaver 7.5](https://launchpad.support.sap.com/#/notes/2158828){: external}                                                                                     | Platform requirements for NW7.5                   |
| [2329005 - Minimal DB system platform requirements for NW AS ABAP 7.51 INNOVATION PKG](https://launchpad.support.sap.com/#/notes/2329005){: external}                                                                        | Platform requirements for innovation package 7.51 |
{: caption="Applications - SAP" caption-side="top"}
