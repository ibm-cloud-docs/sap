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

# SAP HANA deployment to IBM Power Virtual Server using SLES
{: #quickstudy-hana-power-vs-sles}
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
{: #jump_server-sles}

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
{: #adding_routes-sles}

After you configure {{site.data.keyword.dl_full}} and your jump server is provisioned with a private and public IP address, update the network route on your virtual server instance so it can connect to your jump server. The following example shows how to connect a jump server on Windows to the AIX virtual server instance.

#### AIX instance
{: #aix_instance-sles}

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
{: #linux_instance-sles}

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
{: #ssh_tunneling-sles}

This section describes SSH tunneling for specific SAP methods such as SAP GUI and Software Provisioning Manager (formerly known as SAPINST).

#### SSH connection
{: #ssh_connection-sles}

For a stable SSH connection, use the `ServerAliveInterval` and `ServerAliveCountMax` SSH options when you connect to {{site.data.keyword.powerSys_notm}} by using a public network interface.

```
ssh -o ServerAliveInterval=60 -o ServerAliveCountMax=600 <user>@<Public IP Address>
```

#### SSH tunneling for SAP GUI
{: #sap_gui-sles}

Establish an SSH tunnel from your client machine to the cloud server. For example, if your client machine is running on a Windows operating system, run the following command:

```
ssh -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -4 -L 3200:localhost:3200 -N -f -l root <Public IP Address> -p 22
```

In this command, 3200 is the port number that is used to connect to an application server with instance number 00. You might have to change it to connect to a different application server, for example, 3202 for instance number 02.

#### SSH tunneling for SAP Software Provisioning Manager
{: #swpm-sles}

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
{: #ssh-config-issues_hana-sles}

For SSH tunnelling, missing SSH host keys must be generated. For more information, see [Setting up an SSH client](https://www.ibm.com/support/knowledgecenter/STSLR9_8.3.1/com.ibm.fs9200_831.doc/svc_ssh_215ubh_copy.html){: external}.



## Configuring the NTP client
{: #ntp_time_server_hana}

A newly provisioned {{site.data.keyword.powerSys_notm}} might not show the correct time when you run the `date` command. The initial time setting on the server often differs by 10 - 15 minutes from the correct time. This time difference can cause problems when you install and run your SAP system on the server, or when you connect the SAP system to your on-premises landscape.

Ensure that time is synchronized for all {{site.data.keyword.powerSys_notm}}s in your SAP system by using the same time server. For information about setting up NTP on Linux, see [Time Synchronization with NTP](https://documentation.suse.com/sles/12-SP5/html/SLES-all/cha-netz-xntp.html){: external}.

## Disk provisioning and layout
{: #disk_provisioning_and_layout}

When you provision a new Linux server, the default size of the boot logical volume is 100 GB. To prepare your {{site.data.keyword.powerSys_notm}} instance, see the following resources.

| Link                                                                          | Description                               |
|-------------------------------------------------------------------------------|-------------------------------------------|
| [SAP Note 2055470 - HANA on POWER Planning and Installation Specifics - Central Note](https://launchpad.support.sap.com/#/notes/2055470)               | Server and storage setup and configuration        |
| [SAP Note 1943937 - Hardware Configuration Check Tool - Central Note](https://launchpad.support.sap.com/#/notes/1943937/E)                                   | Required checks to run with the HWCCT (Hardware Configuration Check Tool)                         |
| [Best Practices TDI Certified for IBM Storage](http://www-03.ibm.com/support/techdocs/atsmastr.nsf/WebIndex/WP102347)     |  How to configure SAP HANA TDI certified IBM storage                    |
{: caption="Information resources" caption-side="top"}

For test systems, follow these guidelines for storage allocation:

* 52 GB of free disk space for the partition `/usr/sap`.
* Space for three partitions for SAP HANA file storage locations as shown in Table 2:

| Directory    | Purpose                                    |
|--------------|--------------------------------------------|
| /usr/sap     | 52 GB required in the test system          |
| /hana/data   | Same size as RAM                           |
| /hana/log    | Same size as RAM up to a maximum of 512 GB |
| /hana/shared | Same size as RAM up to a maximum of 1 TB   |
| /export      | Local storage for exported images **       |
| /backup      | A preliminary backup on disk **           |
{: caption="SAP HANA file storage locations" caption-side="top"}

** Optional directory for the Linux server

If all disks that you want to use to construct the storage layer for SAP HANA are equal in size, it can be complicated to determine which disks to include in which volume groups.

Create an Excel spreadsheet with an overview of the naming convention that you want to use, the size of the disks, and LVM information.
{: tip}

For example, multiple disks aren't needed for the SAPVG using the `/usr/sap` directory. The amount of I/O workload is negligible and not intensive compared to `/hana/data` and `/hana/log` (`/hana/log` depends on your log archive mode).

### Example
{: #disk_provisioning_example}
Consider a system that requires 400 GB storage. For `/usr/sap` and `hdb_usrsap_vg`, you create one 52 GB disk.

For `/hana/data` and `hdb_data_vg`, the data is accessed randomly, depending on the disk action or request at the time. For performance purposes, you create eight physical volumes. When you provision your new virtual server instance, specify the following settings on the storage provisioning page:

**Add storage volumes:** New Storage Volume

**Provide the name of the new storage volume(s):** datavolumes1-8

**Size of volumes to be created**: 60G

**Shareable:** Off

**Quantity**: 8

Disk type is automatically set to **Tier 1**.

**Create and Attach**

480 GB of space is allocated for the `datavg`.

For the `/hana/log` and the `hdb_log_vg`, logs are created every 5 minutes, so the I/O activity is intense. Eight physical volumes are created for performance purposes.

### Another example
{: #disk_provisioning_example2}

If `log_mode=normal` and you are using a backup method such as Spectrum Protect that backs up log files and removes them after saving them, provision more storage space. If the process is interrupted, the space in `/hana/log` will be reduced quickly, which can result in a database crash and possible loss of data. See the following example of provisioning storage for `/hana/log` when more space is added.

**Add Storage Volumes** New Storage Volume

**Provide the name of the new storage volume(s)** logvolumes1-8

**Size of Volumes to be created** 70G

**Shareable** Off

**Quantity** 8

Disk type is automatically set to **Tier 1**

560 GB of space is allocated for the `hdb_log_vg`.

For `/hana/shared`, provision one physical volume by using the following formula:

Size = installation(single - node) = MIN(1x RAM ; 1 TB)

For example, a test HANA server is a scale up sized at 400 GB, thus provisioning a single PD of 400 GB.

Currently, scale out is not supported on {{site.data.keyword.powerSys_notm}}s.

When custom sizes are assigned to each of the physical volumes, you can use a simple script to create each of the required file systems.

### Disk discovery and storage setup
{: #disk_discovery_and_storage_setup}

On the Linux operating system, scan for the new storage that was provisioned. Run the following command for storage and disk discovery:

```
/usr/bin/rescan-scsi-bus.sh -a -c -v
```

Newly discovered disks are listed at the end of the report.

It's easier to check the system when the physical disks have different sizes. If the volumes are the same size, you can use the worldwide name (WWN) of the volume that is shown in the {{site.data.keyword.cloud}} UI. The WWN corresponds to the ID in the output of the `multipath -ll` command.

For example, a storage volume with the name sapdata_vol has a worldwide name 3600507681081814CE80000000000054D. In the operating system output of the `multipath -ll` command, the corresponding device name for this WWN is dm-4. The device name is required to create the logical volume and the volume group.

```
ibmdmhan01:~ # multipath -ll
3600507681081814ce80000000000054d dm-4 IBM,2145
size=100G features='0' hwhandler='1 alua' wp=rwv
```

The WWN in the {{site.data.keyword.cloud}} UI contains uppercase letters. In the operating system, the same ID contains lowercase letters.
{: note}

The following example shows how to identify disks by using disk sizes. The eight data volumes are 60 GB each, the eight log volumes are 70 GB each, the single volume for `sapdata` is 52 GB, and the single volume for the `/hana/shared` directory is 400 GB.

Run the following command to check how many volumes were created based on size:

```
ibmdmhan01:/usr/sap/DM2/SYS # /sbin/multipath -ll | grep -i 70G

size=70G features='0' hwhandler='1 alua' wp=rw
size=70G features='0' hwhandler='1 alua' wp=rw
size=70G features='0' hwhandler='1 alua' wp=rw
size=70G features='0' hwhandler='1 alua' wp=rw
size=70G features='0' hwhandler='1 alua' wp=rw
size=70G features='0' hwhandler='1 alua' wp=rw
size=70G features='0' hwhandler='1 alua' wp=rw
size=70G features='0' hwhandler='1 alua' wp=rw
```

Eight 70 GB volumes were created for the log volumes.

Run the following command to see the physical volumes that were created for the data volumes:

```
ibmdmhan01:/usr/sap/DM2/SYS # /sbin/multipath -ll | grep -i 60G

size=60G features='0' hwhandler='1 alua' wp=rw
size=60G features='0' hwhandler='1 alua' wp=rw
size=60G features='0' hwhandler='1 alua' wp=rw
size=60G features='0' hwhandler='1 alua' wp=rw
size=60G features='0' hwhandler='1 alua' wp=rw
size=60G features='0' hwhandler='1 alua' wp=rw
size=60G features='0' hwhandler='1 alua' wp=rw
size=60G features='0' hwhandler='1 alua' wp=rw
```

To create the volume groups, logical volumes, striping, and the file systems, you can use a simple script as follows. Alternatively, you can use your own standard of LVM configuration.

```
# Sample script for mkfs.sh
export pv_size=60G
export lv_name=hdb_data_lv
export vg_name=hdb_data_vg
export mount=/hana/data
devices=$(multipath -ll | grep -B 1 $pv_size | grep dm- | awk '{print "/dev/"$2}' | tr '\n' ' ')
stripes=$(multipath -ll | grep -B 1 $pv_size | grep dm- | awk '{print "/dev/"$2}' | wc | awk '{print $1}')
pvcreate $devices
vgcreate ${vg_name} ${devices}
lvcreate -i${stripes} -I64 -l100%VG -n ${lv_name} ${vg_name}
mkfs.xfs /dev/mapper/${vg_name}-${lv_name}
mkdir -p ${mount}
mount /dev/mapper/$vg_name-$lv_name ${mount}
echo "/dev/mapper/$vg_name-$lv_name ${mount} xfs defaults 1 2 " >> /etc/fstab
```

The script creates the `/hana/data` mount point and puts it in `/etc/fstab`.

If HANA scale-out or HA node failover is used, do not add the DATA and LOG file system to `/etc/fstab`. Mounting is done by SAP HANA. Always add the `/hana/shared` file system to `fstab`.
{: important}

Repeat the same process and update the `pv_size` variable to the next LVM entry that you want to create, update the `lv_name`, and so on.

When you create entries for `/usr/sap` and `/hana/shared`, you might get a system message that says striping can be done on multiple volumes and not a single volume. Striping isn't necessary if you use one volume only.

## Checking the multipathd
{: #checking_the_multipathd}

Check the status of the multipath daemon after the LVM actions finish. To enable the multipathd at boot time, run the following command as a root user:

```
systemctl enable multipathd
```

To check the status of the multipathd, run the following command:

```
systemctl status multipathd
```

To stop the service, run the following command. This isn't advisable because it can cause the partition to no longer boot.

```
systemctl stop multipathd
```

Whenever you enable or disable the multipathd, you must rebuild the `initrd`. After enabling the multipath services, run the following command to force a rebuild of the `initrd`:

```
dracut --force --add multipath
```

When you disable the multipath services, run the following command to force a rebuild of the `initrd`:

```
dracut --force -o multipath
```
## Verifying network and adapter configurations
{: #network_and_adapter_configurations}

Review the following considerations when you set the MTU for the adapter that is used for your private network.

Local connections in the same data center (for example, within SAP HANA scale-out nodes or with a local system replication scenario) can often take advantage of jumbo frames (MTU = 9000).

Remote connections to other data centers (for example, with smart data access or system replication) have an increased risk that network components can't efficiently handle jumbo frames. You might need to set MTU = 1500.

If you want to check whether a connection uses jumbo frames, run the following ping command:

```
ping -M do -c 4 -s 8972 <remote_host_or_ip>
```

The ping command sends 9 KB requests to the remote host. If one component can't handle jumbo frames, the package is broken into smaller pieces. This fragmentation is not allowed due to the `-M do` option. A `100% packet loss` error is generated if all components aren't able to handle jumbo frames.

To change the network adapter for the private network, follow the steps in [Configuring SUSE for the SAP HANA or SAP NetWeaver workload](/docs/sap?topic=sap-power-vs-set-up-infrastructure#power-vs-configure_system).

## Hostname verification
{: #hostname_verification}

Make sure that your {{site.data.keyword.powerSys_notm}} hostname resolves correctly. Check that the DNS server is entered correctly in `/etc/resolv.conf` and that instance hostname resolution is possible (in the simplest case, through an entry in `/etc/hosts`).

If you are using virtual hostnames for the SAP HANA database server, make sure that the IP addresses, short name, and fully qualified domain names are includes in the hosts file on the SAP HANA server and on any application servers that are connected to the database instance. Issues with hostname or virtual hostname resolution cause problems when you connect the application server to the database backend and configure the hdbuserstore.

## Registering the system
{: #register_system}

You can register your {{site.data.keyword.powerSys_notm}}s with SUSE for operating system updates in one of two ways.

* Register your system by using a public SUSE repository server. This method isn't recommended for SAP HANA systems because connectivity to the internet is required through a public network adapter.

    To register by using a public SUSE repository server, run the following command:
    ```
    SUSEConnect -r <REGISTRATION_KEY> -e <EMAIL_ADDRESS>
    ```
* Mirror operating system repositories with the SUSE Repository Mirroring Tool (RMT), and register your systems by using the SUSE RMT server. Because the mirror is running in the private network, connectivity to the internet through the public network isn't required. For more information, see [Configuring Clients to Use SUSE RMT (versions 12 and 15)](https://documentation.suse.com/sles/15-SP1/html/SLES-all/cha-rmt-client.html){: external}.

   To register by using the SUSE RMT server, run the following commands:
    ```
    curl http://<RMT_SERVER_IP>/tools/rmt-client-setup --output rmt-client-setup
    chmod +x rmt-client-setup
    ./rmt-client-setup https://<RMT_SERVER_IP>
    ```

## Using saptune
{: #using_saptune}

Use the `saptune` tool to apply the HANA solution to your server. The HANA solution applies recommended operating system settings for SAP HANA on SUSE Linux Enterprise Server.

The following sample workflow shows how you can use the `saptune` tool to apply the HANA solution to your server. For more information about `saptune`, see [SAP Note 1275776 - Linux: Preparing SLES for SAP environments](https://launchpad.support.sap.com/#/notes/1275776){: external}.

1. Verify that the package status is current.
    ```
    zypper info saptune
    ```
1. Verify that the saptune version is at least 2.
    ```
    saptune version
    ```
1. List all available solutions. Numbered entries represent integrated SAP Notes for each of the solutions.
    ```
    saptune solution list
    ```
1. Get an overview of `saptune` options.
    ```
    saptune --help
    ```
1. List SUSE OS defaults for instance network tunables and THP. Most of the defaults aren't set correctly for SAP HANA.
    ```
    sysctl -a| grep -E
    ```
1. Optional: Simulate the changes that will be applied.
    ```
    saptune solution simulate HANA
    ```
    `saptune` autotunes parameters based on fixed or calculated values. `saptune` indicates which parameters can't be changed automatically, and provides links to related SAP or IBM documentation to assist with manual steps.
    {: note}
1. Apply the HANA saptune solution.
    ```
    saptune solution apply HANA
    ```
1. If you want to automatically activate the solution's tuning options after a reboot, run the following command:
    ```
    saptune daemon start
    ```

To diagnose startup issues, see [SAP Note 401162 - Linux: Avoiding TCP/IP port conflicts and start problems](https://launchpad.support.sap.com/#/notes/401162){: external}.
{: tip}

## NUMA layout
{: #numa_layout}

Check that the balance of CPU and memory placement is optimized for SAP HANA by running the `chk_numa_lpm.py` script. The `chk_numa_lpm.py` script does the following actions:

* Checks the non-uniform memory access (NUMA) layout according to SAP HANA rules. The script verifies that there are no cores without memory and that the memory distribution among the cores does not exceed a 50% margin. In the first case, the script generates an error; in the latter case, the script generates a warning.
* Checks whether a Live Partition Mobility (LPM) operation occurred. After LPM, the NUMA layout might differ from the configuration at boot time. The script scans the system log for the last LPM. A warning is generated if an LPM operation occurred since the last system boot.

Download the `chk_numa_lpm.py` script from the following SAP Note: [SAP Note 2923962 - Check SAP HANA NUMA Layout on {{site.data.keyword.IBM_notm}} Power Systems Virtual Servers](https://launchpad.support.sap.com/#/notes/2923962){: external}

Then, run the `chk_numa_lpm.py` script on your newly provisioned {{site.data.keyword.cloud}} {{site.data.keyword.powerSys_notm}}.

Run the script as follows:

```
ibmdmhan01:/backup/software/numa # ./chk_numa_lpm.py
WARNING: LPM may have occurred

ibmdmhan01:/backup/software/numa # ls
chk_numa_lpm.log  chk_numa_lpm.py
ibmdmhan01:/backup/software/numa # cat chk_numa_lpm.log

###  Check run on  : 2020-09-10 09:37:21  #####

Hostname           : ibmdmhan01
Partition UUID     : 2e2fb3e5-ef18-48c6-819a-bd85bfefa953

WARNING: A possible Live Partition Migration (LPM) might have happened after boot!

Date of lastest started LPM : 2020-07-16 06:08:38
Date of lastest boot        : 2020-07-02 10:01:00

Numa Node :  0   Number of virt. CPUs :  32   Amount of memory :   255667 MB
Numa Node :  1   Number of virt. CPUs :  32   Amount of memory :   255738 MB
```

In this example, a warning was generated. There are two NUMA nodes with an equal amount of CPU and memory. For more information, see [SAP Note 2923962](https://launchpad.support.sap.com/#/notes/2923962){: external}.

## Installing SAP HANA
{: #installing_sap_hana}

Instead of a Windows jump server, you can provision a Linux jump server and a server with a public and private network. You can use this server as a software repository.

When you provision the jump server, remember to request more disk space so you can create the LVM setup and directory structure. Because the jump server has public access, it can access SAP Market Place and download the software on to the locations you specify. Then, you can enable the file system to be shared with NFS across your new cloud landscape.

There are several benefits to this approach:

* Your software is centrally located; there's no need to provision more storage on your {{site.data.keyword.powerSys_notm}} instances. Mount the jump server on each new server and install the software.
* The jump server has a public IP interface. You can create a shadow of the SUSE software repositories.
* It's easier for SAP Basis Administrators to manage one location instead of several.

When you use this setup, you can mount remotely through NFS. Remember to activate `rpcbind` on your {{site.data.keyword.powerSys_notm}}:

1. Run the following command:

    ```
    systemctl start rpcbind
    ```
1. Check that the service is active:

    ```
    systemctl status rpcbind
    ```
1. Start the NFS client service:

    ```
    systemctl start nfs
    ```

## Information resources for SAP HANA
{: #information_resources_hana}

The following links will assist you with the installation and configuration of your {{site.data.keyword.powerSys_notm}} instances with SAP HANA on Linux. Links with numbers in the title point to the SAP Support Portal.

### Operating systems – General Linux
{: #os_linux-sles}

| Link                                                                                                                               | Description                                                         |
|------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------|
| [2378874 - Install SAP Solutions on Linux on {{site.data.keyword.IBM_notm}} Power Systems (little endian)](https://launchpad.support.sap.com/#/notes/2378874) | Installing SAP solutions on {{site.data.keyword.IBM_notm}} Power Systems                       |
| [2235581 - SAP HANA: Supported Operating Systems](https://launchpad.support.sap.com/#/notes/2235581)                               | Supported operating systems for SAP HANA                            |
| [2369910 - SAP Software on Linux: General information](https://launchpad.support.sap.com/#/notes/2369910)                          | General information for SAP software on Linux                       |
| [765424 - Linux: Released IBM Hardware - POWER based servers](https://launchpad.support.sap.com/#/notes/765424)                    | IBM Power-based servers                                             |
| [1122387 - Linux: SAP Support in virtualized environments](https://launchpad.support.sap.com/#/notes/1122387)                      | SAP support in virtualized environments                             |
| [SAP on {{site.data.keyword.IBM_notm}} Power Systems running Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+IBM+Power+Systems+running+Linux)     | Useful information about running Linux on Power                     |
| [936887 - End of maintenance for Linux distributions](https://launchpad.support.sap.com/#/notes/936887)                            | Maintenance calendar and product maturity                           |
| [2679703 - Linux on {{site.data.keyword.IBM_notm}} Power Systems -- SAP monitoring recommendations](https://launchpad.support.sap.com/#/notes/2679703)        | SAP monitoring recommendations                                      |
| [187864 - Linux: Locale Support on Linux](https://launchpad.support.sap.com/#/notes/187864)                                        | Locale support for Linux                                            |
| [SAP on {{site.data.keyword.IBM_notm}} Power Systems running Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+IBM+Power+Systems+running+Linux)     | SAP on {{site.data.keyword.IBM_notm}} Power Systems library                                    |
| [2382421 - Optimizing the Network Configuration on HANA- and OS-Level](https://launchpad.support.sap.com/#/notes/2382421)          | Increasing efficiency on network for operating systems and SAP HANA |
| [401162 - Linux: Avoiding TCP/IP port conflicts and start problems](https://launchpad.support.sap.com/#/notes/401162)              | Preventive guidance to avoid network-related start issues         |
{: caption="Operating systems – general Linux" caption-side="top"}

### Operating systems – SUSE Linux
{: #suse_linux}

| Link                                                                                                                                                      | Description                                   |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------|
| [2205917 - SAP HANA DB: Recommended OS settings for SLES 12 / SLES for SAP Applications 12](https://launchpad.support.sap.com/#/notes/2205917)            | SLES 12 recommended operating system settings |
| [1984787 - SUSE LINUX Enterprise Server 12: Installation notes](https://launchpad.support.sap.com/#/notes/1984787)                                        | SLES 12 installation note                     |
| [2578899 - SUSE Linux Enterprise Server 15: Installation Note](https://launchpad.support.sap.com/#/notes/2578899)                                         | SLES 15 installation note                     |
| [2684254 - SAP HANA DB: Recommended OS settings for SLES 15 / SLES for SAP Applications 15](https://launchpad.support.sap.com/#/notes/2684254)            | SLES 15 recommended operating system settings |
| [2790462 - HANA Server connection is not available or timed out after upgrade to SUSE 15 from SUSE 12](https://launchpad.support.sap.com/#/notes/2790462) | Known issue when upgrading from 12 to 15      |
| [1275776 - Linux: Preparing SLES for SAP environments](https://launchpad.support.sap.com/#/notes/1275776)                                                 | Preparing SLES for SAP environments           |
| [SUSE Best Practices Library](https://documentation.suse.com/sbp/all/?context=sles-sap)                                                                   | A useful collection of SUSE documentation     |
| [SUSE Enterprise Server for IBM POWER](https://www.suse.com/products/power/)                                                                              | IBM and SUSE                                  |
{: caption="Operating systems – SUSE Linux" caption-side="top"}


### SAP HANA-related information
{: #hana_info}

| Link                                                                                                                    | Description                                        |
|-------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------|
| [2000003 - FAQ: SAP HANA](https://launchpad.support.sap.com/#/notes/2000003)                                            | Extensive overview of SAP HANA                     |
| [1999880 - FAQ: SAP HANA System Replication](https://launchpad.support.sap.com/#/notes/1999880)                         | HSR central note                                   |
| [2000002 - FAQ: SAP HANA SQL Optimization](https://launchpad.support.sap.com/#/notes/2000002)                           | Useful tips to improve SQL processing times        |
| [SAP HANA Platform Landing page](https://help.sap.com/viewer/product/SAP_HANA_PLATFORM/2.0.05/en-US?task=discover_task) | Useful for installation guides and upgrade guides |
| [SAP Guide Finder](https://help.sap.com/viewer/nwguidefinder/576f5c1808de4d1abecbd6e503c9ba42.html)                     | Useful to locate user guides and information on updates     |
| [2380291 - SAP HANA 2.0 Cockpit Central Release Note](https://launchpad.support.sap.com/#/notes/2380291)                | SAP HANA Cockpit central note                      |
{: caption="SAP HANA-related information" caption-side="top"}
