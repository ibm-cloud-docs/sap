---

copyright:
  years: 2020, 2024
lastupdated: "2024-12-03"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, Quick Study Tutorial

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
{: important .important}
{:step: data-tutorial-type='step'}

# SAP NetWeaver deployment to Bare Metal on Classic Infrastructure, using RHEL
{: #quickstudy-bm-netweaver-rhel}
{: toc-content-type="tutorial"}
{: toc-completion-time="90m"}

*A Quick Study, someone who is able to learn new things quickly.*
{: note}

These Quick Study Tutorials provide a single sample configuration, with less detailed instructions, as an introduction for customers who prefer hands-on tasks to increase their pace of learning.
{: shortdesc}

The following information provides an introduction for customers who are new to the Classic Infrastructure environment. Two sample configurations are provided to help you through the ordering process to the start of the SAP installation.

The first configuration sample is a simple, single node 32 GB RAM server with Red Hat Enterprise Linux&reg; (RHEL). The second is an advanced two node configuration that adds a second virtual server of 192 GB RAM with Red Hat Enterprise Linux (RHEL) to the landscape.

An example of how to set up external storage, which can be applied to either sample configuration, is also provided.

The sample layouts might not be your preferred layout. The purpose of this guidance is to show two possibilities. Your installation should follow your business requirements and SAP installation documentation.


## Provisioning a 32 GB server for a single-host environment
{: #provisioning_single_host}
{: step}

1. Log in to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com){: external} with your unique credentials.
2. Click **Create** > **Compute** > **Infrastructure** > **Bare Metal Server**.
3. Click **Continue**. If you can't click **Continue**, you don't have the correct permissions to create a server. Check with your system administrator about your permissions.
4. Leave *1* in the **Quantity** field.
5. Enter *e2e1270* in the **Hostname** field. Hostname is a permanent or temporary name for your servers. Click **Information** for formatting specifics.
6. Enter *mycloud.com* in the **Domain** field. Domain is the identification string that defines administrative control within the internet. Click **Information** for formatting specifics.
7. **Billing** defaults to *Monthly*. Currently, 1-year contract and 3-year contract are not available for SAP-certified servers.
8. The data centers displayed under **Location** depend on product availability within a particular data center. Select *NA East TOR01-Toronto*.
9. Click **All servers** > **SAP certified**.

### Configuring your server
{: #configure_server-32GB-rhel}
{: step}

1. Select *CPU Model BI.S3.NW32 (OS Options)*. For more information about how to decipher the server names, see [Provisioning your {{site.data.keyword.baremetal_short}} using the {{site.data.keyword.cloud_notm}} console](/docs/sap?topic=sap-nw-iaas-offerings-profiles-intel-bm).
2. **RAM** defaults to a predefined value based on your server selection and cannot be changed.
3. Enter an optional public key for your **SSH key**, which you can use to log in to your server after the server is provisioned. The default is *None*.
4. Select *Red Hat* as your **Image** (OS). The default is *7.x (64 bit)*.

   If you're bringing your own license (BYOL) for your OS, select *No OS*. For more information, see [Bring your own license](#byol-32GB-rhel).
   {: note}

### Adding storage disks
{: #adding-storage-disks-32GB-rhel}
{: step}

1. Under **Type**, select *RAID 10*.
2. **Disks**, **Hot Spare**, and **Disk Media** have default values. Select a **Disk size** that covers the total amount of storage you need.
3. Click the Menu icon ![Menu icon](../../icons/action-menu-icon.svg) > **Advanced configuration** and leave **Controller** cleared. Click **OK**.

### Network interface
{: #network-interface-32GB}
{: step}

1. Select *1 Gbps Redundant Public and Private Network Uplinks* for **Uplink Port Speed**.
2. Select the values in Table 1 for the following fields:

|              Field               |      Value              |
| -------------------------------- | ------------------------|
| Private VLAN                     | tor01.bcr01a.1241     |
| Public VLAN                      | tor01.fcr01a.851     |
| Private Subnet                   | 10.114.63.64/26      |
| Public Subnet                    | 158.85.65.224/28      |
{: caption="32 GB network interface values" caption-side="top"}

3. Leave the default values for all other fields.
4. Review your Order Summary.
5. Click **I read and agree to the following Third-Party Service Agreements**.

   You can create your server, save the order as a quote to provision later, or add the order to an estimate, which might include multiple services.
   {: note}

6. Click **Create** to be redirected to the Checkout page after your order is verified.

You are redirected to a page with your order number. The page is your order receipt; print a copy for your records. You also receive a confirmation email with the subject *Your {{site.data.keyword.cloud_notm}} Order ## has been approved*. The ## is your order number.

Depending on your order, server is available for use within one to four hours after your order is submitted. You can check the Device Details from the {{site.data.keyword.cloud_notm}} console (Menu icon ![Menu icon](../../icons/icon_hamburger.svg) > Resource List > Devices) for the status of the provisioning steps. Click the **Device Name** that matches your device's Hostname and Domain to see its status.

### Bring your own license
{: #byol-32GB-rhel}
{: step}

If you have your own operating system license, you install it on your {{site.data.keyword.baremetal_short}} based on the vendor's instructions. For more information, see [The no OS option](/docs/bare-metal?topic=bare-metal-bm-no-os#bm-no-os).

### Access your server
{: #access_32GB}
{: step}

A public IP is used for remote access, so you can connect to your server through an `ssh` client (for example, PuTTY on Microsoft Windows). Use the public IP address displayed in the Device List (under the **Devices** menu) for your device. The root password for your server is also displayed. Click **Show Password** to see it.

### Partitioning and file systems
{: #partition_32GB-rhel}
{: step}

For the single-node example, you ordered a server with one logical disk (on RAID 1). The server has one mirrored disk with the operating system (OS) and one large root file system equal to the total size of the disk (with some space used for `/boot`). The file system layout in this example is just one possible approach. For production use, follow the sizing information for your system as other layouts might better meet your needs or SAP requirements.

1. Create the three directories required for the SAP installation, `/sapmnt`, `/usr/sap`, and `/db2`.
```
[root@e2e1270 ~]# mkdir /sapmnt
[root@e2e1270 ~]# mkdir /usr/sap
[root@e2e1270 ~]# mkdir /db2
```
Your {{site.data.keyword.baremetal_long}} is now ready for external storage and the installation of your SAP applications and software.



## Provisioning 192 GB and 32 GB servers for a three-tier environment
{: #provisioning_three_tier-rhel}
{: step}

A three-tier environment is a more complex scenario using a 192 GB server as the database server and a 32 GB server as the SAP NetWeaver application server.

### Ordering your SAP NetWeaver Application Server
{: #order-app-server-rhel}
{: step}

Follow the same steps in [Provisioning a 32 GB server for a single-host environment](#provisioning_single_host) to order the SAP NetWeaver Application Server.

### Ordering your Database Server
{: #order-db-server-rhel}
{: step}

Use the following steps to order an SAP-certified server as your database server.

1. Log in to the [{{site.data.keyword.cloud}} console)](https://cloud.ibm.com){: external} with your unique credentials.
2. Click **Create resource** > **Compute** > **{{site.data.keyword.baremetal_short_sing}}**.
3. Click **Continue.** If you can't click **Continue**, you don't have the correct permissions to create a server. Check with your system administrator about your permissions.
4. Leave *1* in the **Quantity** field.
5. Enter *sdb192* in the **Hostname** field. Hostname is a permanent or temporary name for your servers. Click **Information** for formatting specifics.
6. Enter *mycloud.com* in the **Domain** field. Domain is the identification string that defines administrative control within the internet. Click **Information** for formatting specifics.
7. **Billing** defaults to *Monthly*. Currently, 1-year contract and 3-year contract are not available for SAP-certified servers.
8. The data centers displayed under **Location** depend on product availability within a particular data center. Select *NA East, TOR01-Toronto*.
9. Click **All servers** > **SAP certified**.

### Configuring your Database Server
{: #options_192GB}
{: step}

Use the following steps to configure your database server and OS.

1. Select *CPU Model BI.S3.NW192 (OS Options)*. For more information about how to decipher the server names, see [Provisioning your {{site.data.keyword.baremetal_short}} using the {{site.data.keyword.cloud_notm}} console](/docs/sap?topic=sap-nw-iaas-offerings-profiles-intel-bm).
2. **RAM** defaults to a predefined value based on your server selection and cannot be changed.
3. Enter an optional public key for **SSH key**, which you can use to log in to your server after the server is provisioned. The default is *None*.
4. Select *Red Hat* as your **Image** (OS). The default is *7.x (64 bit)*.

    If you're bringing your own license (BYOL) for your OS, select *No OS* as your image. For more information, see [Bring your own license](#byol).
    {: note}

### Adding storage disks
{: #adding-storage-disks}
{: step}

Use the following steps to add a second 2 TB SATA drive to your database server.

1. For **Disk 1**, click the Menu icon ![Menu icon](../../icons/action-menu-icon.svg) > **Advanced configuration** > and verify that Primary disk partition** is set to the default of *Windows Basic*. Click **OK**.
2. Click **Add new**.
3. **Disks**, **Hot Spare**, and **Disk Media** have default values. Select a **Disk Size** that covers the total amount of storage you need.

### Setting up the network interface
{: #network-options}
{: step}

Use the following steps to set up the network interface for your database server.

1. Select *1 Gbps Redundant Public & Private Network Uplinks* for **Uplink Port Speed**.
2. Select the values in Table 1 for the following fields:

    Make sure the network interface values for your database server match the values of your application server.
    {: note}

|              Field               |      Value              |
| -------------------------------- | ------------------------|
| Private VLAN                     | tor01.bcr01a.1241     |
| Public VLAN                      | tor01.fcr01a.851      |
| Private Subnet                   | 10.114.63.64/26       |
| Public Subnet                    | 158.85.65.224/28      |
{: caption="192 GB network interface values" caption-side="top"}

3. Leave the default values for all other fields.
4. Review your Order Summary.
5. Select **I read and agree to the following Third-Party Service Agreements**.

    You can create your server, save the order as a quote to provision later, or add the order to an estimate, which might include multiple services.
    {: note}

6. Click **Create** to be redirected to the Checkout page after your order is verified.

You are redirected to a page with your order number. The page is your receipt; print the page for your records. You also receive a confirmation email with the subject _Your {{site.data.keyword.cloud_notm}} Order ## has been approved_. The ## is your order number.

 Depending on your order, the server is available for use within one to four hours after the order is submitted. You can check Device Details from the {{site.data.keyword.cloud_notm}} console (Menu icon ![Menu icon](../../icons/icon-hamburger.svg) > Resource List > Devices) for the status of the provisioning steps. Click the **Device Name** that matches your given Hostname and Domain to see its status.

### Bring your own license
{: #byol}
{: step}

If you have your own operating system license, you install it on your {{site.data.keyword.baremetal_short}} based on the vendor's instructions. For more information, see [The no OS option](/docs/bare-metal?topic=bare-metal-bm-no-os#bm-no-os).

### Access your server
{: #access-db-server-rhel}
{: step}

A public IP is used for remote access, so you can connect to your server through an `ssh` client (for example, PuTTY on Microsoft Windows). Use the public IP address displayed in the Device List (**Classic Infrastructure** > **Devices**) for your device. The root password for your server is also displayed. Click **Show Password** to see it.

### Partitioning and file systems
{: #partitioning-and-file-systems-rhel}
{: step}

For the three-tier example, a 192 GB database server with one logical disk (on RAID10) and a 32 GB application server with one logical disk (on RAID 1) were ordered. Both servers come with one large root file system that is equal to the total size of disk (with some space that is used for `/boot`).

For the 32 GB server, create the `/usr/sap/` file system. The file systems `sapmnt1` and `/usr/sap/trans` are created on the database server. The Network File System (NFS) is exported from the database server, which also hosts the Advanced Business Application Programming (ABAP) SAP Central Service [(A)SCS] instance.

The following file system layout is one possible approach. For production use, you might follow the sizing information for your system as other layouts might better meet your needs or SAP requirements, or you might use quotas.

Use the following commands to create the required directories for installing the SAP software, `/sapmnt`, `/usr/sap`, and `/db2`:
```
[root@ sdb192 ~]# mkdir /sapmnt
[root@ sdb192 ~]# mkdir /usr/sap
[root@ sdb192 ~]# mkdir /db2
```

### Preparing your network for a three-tier setup
{: #network-rhel}
{: step}

If you are planning to install a three-tier setup, the network needs to be set up correctly. In the example, a 192 GB database server (named "sdb192") and a 32 GB application server (named "e2e1270") are deployed. The database server also hosts the (A)SCS instance. Adding the IP addresses on the private network to your `/etc/hosts` helps with the upcoming steps and ensures that SAP internal network traffic goes through the right network.

![Figure 1. Sample of three-tier setup](/images/quickstudy-network_2019-07-23.png "Sample of three-tier setup"){: caption="Sample of three-tier setup" caption-side="bottom"}

Use the following steps to establish your network.

1. Log in to the servers and find their private network configuration.

```
[root@sdb192 ~]# ifconfig bond0
    bond0	  Link encap:Ethernet  HWaddr 0C:C4:7A:66:2D:A8
            inet addr:10.17.139.35  Bcast:10.17.139.63 Mask:255.255.255.192
            inet6 addr: fe80::ec4:7aff:fe66:2da8/64 Scope:Link
            UP BROADCAST RUNNING MASTER MULTICAST  MTU:1500  Metric:1
            RX packets:128080 errors:0 dropped:0 overruns:0 frame:0
            TX packets:25491 errors:0 dropped:0 overruns:0 carrier:0
            collisions:0 txqueuelen:0
            RX bytes:19137850 (18.2 MiB)  TX bytes:3426015 (3.2 MiB)
```

```
[root@sdb192 ~]# ifconfig bond1
    bond1	  Link encap:Ethernet  HWaddr 0C:C4:7A:66:2D:A9
            inet addr:208.43.211.118  Bcast:208.43.211.127 Mask:255.255.255.224
            inet6 addr: fe80::ec4:7aff:fe66:2da9/64 Scope:Link
            UP BROADCAST RUNNING MASTER MULTICAST  MTU:1500  Metric:1
            RX packets:289610 errors:0 dropped:0 overruns:0 frame:0
            TX packets:109371 errors:0 dropped:0 overruns:0 carrier:0
            collisions:0 txqueuelen:0
            RX bytes:31216160 (29.7 MiB)  TX bytes:18591947 (17.7 MiB)
```

In the three-tier example, 10.17.139.35 is the private IP address of the database server; it is from one of the IP address ranges from RFC 1597. You can determine the IP address of the application server, too, and add both IP addresses to both servers’ `/etc/hosts`.

```
[root@sdb192 ~]# cat /etc/hosts

127.0.0.1 localhost.localdomain localhost
208.43.211.118 e2e2690.saptest.com e2e2690

10.17.139.35    sdb192-priv
10.17.139.46    e2e1270-priv
```

Add the last two lines on `e2e1270`, too.

### Installing NFS software
{: #install-nfs}
{: step}

1. Install NFS software `nfs-utils` **on both servers**.
```
[root@sdb192 ~]# yum install nfs-utils
```
Make sure you start and register the `rpcbind` and NFS service on the database server.

```
[root@sdb192 ~]# service rpcbind start
[root@sdb192 ~]# chkconfig nfs on
[root@sdb192 ~]# service nfs start
```

### Using NFS to export
{: #use-nfs}
{: step}

1. Use NFS to export `/sapmnt` and `/usr/sap/trans` from the database server to the application server by adding the required entry to `/etc/exports` of the database server.

```
/sapmnt/C10		10.17.139.46(rw,no_root_squash,sync,no_subtree_check)
/usr/sap/trans	10.17.139.46(rw,no_root_squash,sync,no_subtree_check)
```

The sample value `C10` needs to be replaced with the SAP System ID for your SAP system. You must create the directory before you export it. Run the following commands from the command line of the database server:

```
[root@sdb192 ~]# mkdir /sapmnt/C10
[root@sdb192 ~]# mkdir -p /usr/sap/trans
[root@sdb192 ~]# exportfs -a
```

### Mounting the NFS share
{: #mount-nfs}
{: step}

1. Mount the NFS share on the application server by adding the following entry to its `/etc/fstab` and mount it from the command line.

```
sdb192-priv:/sapmnt/C10 /sapmnt/C10 nfs defaults 0 0
sdb192-priv:/usr/sap/trans /usr/sap/trans nfs defaults 0 0
```

2. Create the target directories on the application server and mount them.

```
[root@e2e1270 ~]# mkdir -p /sapmnt/C10
[root@e2e1270 ~]# mkdir /usr/sap/trans
[root@e2e1270 ~]# mount /sapmnt/C10
[root@e2e1270 ~]# mount /usr/sap/trans
```

Your servers are now prepared to host the components of a distributed SAP installation.



## Adding external storage to your server
{: #storage-rhel}
{: step}

External storage can be added to your provisioned server or servers. You can use the external storage as a backup device, or use as a snapshot to quickly restore your database in a test environment. For the three-tier example, block storage is used for both archiving database log files and online and offline database backups. The fastest block storage (10 IOPS per GB) was selected to help assure a minimum backup time. Slower block storage might be sufficient for your needs. For more information about {{site.data.keyword.blockstoragefull}}, see [Getting started with Block Storage](/docs/BlockStorage?topic=BlockStorage-getting-started#getting-started).

{{site.data.keyword.cloud_notm}} storage LUNS can be provisioned with two options - Endurance and Performance. Endurance tiers feature pre-defined performance levels and other features, such as [snapshot](/docs/BlockStorage?topic=BlockStorage-snapshots) and replication. A custom Performance environment is built with allocated input/output operations per second (IOPS) in the range 100 - 1,000.

### Setting up external storage
{: #set_up_storage}
{: step}

1. Log in to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com/){: external} with your unique credentials.
2. Expand the Menu icon ![Menu icon](../../icons/icon.hamburger.svg) and select *Classic Infrastructure*.
3. Select *Storage* > *Block Storage* > *Order Block Storage*.
4. Select the specifics for your storage needs. Table 1 contains recommended values, including 10 IOPS/GB for a demanding database workload.

|              Field               |      Value                                        |
| -------------------------------- | ------------------------------------------------- |
|Location                          | US South, DAL10                                   |
|Billing Method                    | Monthly (default)                                 |
|Size                              | 1000 GB                                           |
|Endurance (IOPS tiers)            | 10 IOPS/GB                                        |
|Snapshot space                    | 0 GB                                              |
|OS Type                           | Linux (default)                                   |
{: caption="Recommended values for block storage" caption-side="top"}

5. Review the Order Summary.
6. Select **I have read and agree to the terms and conditions listed below**.
7. Click **Create**.

### Authorizing host
{: #authorizing-hosts-console-rhel}
{: step}

1. Select **Storage** > **Block Storage**.
2. Highlight your LUN and expand the Action menu ![Action menu](../../icons/action-menu-icon.svg) and select **Authorize Host**.
3. Select *Bare Metal Server* for **Device Type**.
4. Click **Hardware** to load available devices and select the hostname of your database server.
5. Click **Save**.
6. Check the status of your provisioned storage under **Devices** > (select your device) > **Storage** tab.
7. Note the **Target Address** and iSCSI Qualified Name (**IQN**) for your server (iSCSI initiator), and the **username** and **password** for authorization with the iSCSI server. You need that information in the following steps.

    More provisioning information can be found under [Ordering Block Storage through the Console](/docs/BlockStorage?topic=BlockStorage-orderingthroughConsole).
    {: tip}

Follow the steps in [Connecting to MPIO iSCSCI LUNS on Microsoft Windows](/docs/BlockStorage?topic=BlockStorage-mountingWindows#mountingWindows) to make your storage accessible from your provisioned server.

### Making storage multipath
{: #multipath}
{: step}

In the sample deployment, you retrieved the following data from the **Storage** tab:
    * Target IP: 10.2.62.78
    * IQN: iqn.2005-05.com.softlayer:SL01SU276540-H896345
    * User: SL01SU276540-H896345
    * Password: EtJ79F4RA33dXm2q

1. Enter the following based on the retrieved information:
```
[root@sdb192 ~]# cat /etc/iscsi/initiatorname.iscsi
InitiatorName=iqn.2005-05.come.softlayer:SL01SU276540-H986345
```
   An existing entry might have to be replaced in `/etc/iscsi/initiatorname.iscsi`.

2. Add the following lines to the end of `/etc/iscsi/iscsid.conf`:
```
[root@sdb192 ~]# tail /etc/iscsi/iscsid.conf
# it continue to respond to R2Ts. To enable this, uncomment this line
# node.session.iscsi.FastAbort = No
node.session.auth.authmethod = CHAP
node.session.auth.username = SL01SU276540-H896345
node.session.auth.password = EtJ79F4RA33dXm2q
discovery.sendtargets.auth.authmethod = CHAP
discovery.sendtargets.auth.username = SL01SU276540-H896345
discovery.sendtargets.auth.password = EtJ79F4RA33dXm2q
```

3. Replace the `username` and `password` values in step 2 with th values that you noted during step 5 of *Authorizing hosts*.

4. Discover the iSCSI target by entering the following lines.
```
[root@sdb192 ~]# iscsiadm -m discovery -t sendtargets -p "10.2.62.78"
10.2.62.78:3260,1031 iqn.1992-08.com.netapp:tor0101
10.2.62.87:3260,1032 iqn.1992-08.com.netapp:tor0101
```

5. Set the host to automatically log in to the iSCSI array.

```
[root@sdb192 ~]# iscsiadm -m node -L automatic
```

6. Install and start the multipath daemon.
```
[root@sdb192 ~]# yum install device-mapper-multipath
…
[root@sdb192 ~]# chkconfig multipathd on
[root@sdb192 ~]# service multipathd start
```

7. Complete all the commands in [Connecting to iSCSI LUNS on Linux](/docs/BlockStorage?topic=BlockStorage-mountingLinux) so another LUN appears in the multipath output.
```
[root@sdb192 ~]# multipath -ll
…
3600a098038303452543f464142755a42 dm-9 NETAPP,LUN C-Mode
size=500G features='3 queue_if_no_path pg_init_retries 50' hwhandler='1 alua' wp=rw
|-+- policy='round-robin 0' prio=50 status=active
| `- 10:0:0:169 sde 8:64 active ready running
`-+- policy='round-robin 0' prio=10 status=enabled
`- 9:0:0:169  sdf 8:80 active ready running
…`
```

You can now use the multipath device as you would use any disk device. A device path appears under `/dev/mapper/3600a098038303452543f464142755a42`.

Take the sample `/etc/multipath.conf` from the [example `multipath.conf`](/docs/sap?topic=sap-quickstudy-bm-netweaver-rhel#sample) and create it on your server. Any copied special characters, DOS-like carriage returns, line-feed entries might lead to unexpected behavior. Make sure that you have an ASCII Unix file after you copy the contents.

Adapt the multipath block from `/etc/multipath.conf` to create an alias of the path to access the device under `1/dev/mapper/mpath1`.

```
multipaths {
    multipath {
        wwid 3600a098038303452543f464142755a42
        alias mpath1
    }
}
```

1. Restart `multipathd`. You can now create the `/backup` file system and mount on the block device.

```
[root@sdb192 ~]# service multipathd restart
[root@sdb192 ~]# mkfs.ext4 /dev/mapper/mpath1
[root@sdb192 ~]# mkdir  /backup
```

1. Check the file systems on both servers. Your output should be similar to the following output.

```
[root@e2e1270 ~]# df -h
Filesystem		    Size  Used Avail Use% Mounted on
/dev/sda3             879G  1,5G  833G   1% /
tmpfs                  16G     0   16G   0% /dev/shm
/dev/sda1             248M   63M  173M  27% /boot
/dev/sdb2             849G  201M  805G   1% /usr/sap
db192-priv:/usr/sap/trans
              165G   59M  157G   1% /usr/sap/trans
              db192-priv:/sapmnt/C10
              165G   59M  157G   1% /sapmnt/C10
```

```
[root@sdb192 ~]# df -h
Filesystem      	    Size  Used Avail Use% Mounted on
/dev/sda3             549G  2,3G  519G   1% /
tmpfs                 127G     0  127G   0% /dev/shm
/dev/sda1             248M   63M  173M  27% /boot
/dev/mapper/mpath1    493G   70M  468G   1% /backup
/dev/mapper/datavg-datalv
              1,2T   71M  1,1T   1% /db2
/dev/mapper/datavg-saplv
              165G   60M  157G   1% /usr/sap
/dev/mapper/datavg-sapmntlv
              165G   60M  157G   1% /sapmnt
```

If you install an SAP NetWeaver-based SAP application on IBM Db2, you must create subdirectories under `/backup` owned by the database admin user (db2SID) for full backups and archived log files. To automatically archive the log files, set `LOGMETH1` in your {{site.data.keyword.Db2_on_Cloud_short}} database. Refer to the [{{site.data.keyword.Db2_on_Cloud_short}} documentation)](http://www.ibm.com/support/knowledgecenter/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0051344.html){: external} for details.

## Installing your SAP landscape
{: #install_landscape-rhel}
{: step}

### Prerequisite: Installing RPM packages
{: #prereq-RPM}
{: step}

An SAP installation requires certain prerequisites for the packages that are installed on the OS and the OS daemons that are running. Refer to the latest [installation guides)](https://support.sap.com/en/my-support/software-downloads.html){: external}. Click **Access downloads** under **Installations & Upgrades** (requires an [SAP S-user ID](/docs/sap?topic=sap-necessary-credentials). Also, refer to the latest [support notes)](https://support.sap.com/en/my-support/knowledge-base.html){: external} (requires an SAP S-user ID) from SAP for an up-to-date list of these prerequisites.

Two more packages need to be installed:
* compat-sap-c++: Generally achieves compatibility of the C++ runtime with the compilers that are used by SAP. Because Red Hat Enterprise Linux for SAP Business Application 7.X was selected as the OS for both the 32 GB application server and the 192 GB database server, you use compat-sap-c++-7.
* uuidd: Maintains OS support for the creation of UUIDs.

### Checking if uuidd is installed
{: #prereq-uuidd}
{: step}

1. Check whether uuid daemon (uuidd) is installed. If it is not, install and start it.
```
[root@sdb192 ~]# rpm -qa | grep uuidd
[root@sdb192 ~]# yum install uuidd
[root@sdb192 ~]# chkconfig uuidd on
[root@sdb192 ~]# service uuidd start
```

#### Installing package compat-sap-c++-7
{: #prereq-compat-sap-c++}
{: step}

1. Follow [SAP Note 2195019)](https://me.sap.com/notes/2195019){: external} and install package compat-sap-c++-7 and create a specific soft-link, which is required by the SAP binaries. Check the release-specific SAP Notes for the product you are installing to determine whether the library is required.
```
[root@sdb192 ~]# yum install compat-sap-c++-7-7.2.1-2.e17_4.x86_64.rpm
....
[root@sdb192 ~]# mkdir -p /usr/sap/lib
[root@sdb192 ~]# ln -s /opt/rh/SAP/lib64/compat-sap-c++.so /usr/sap/lib/libstdc++.so.6
```

### Downloading your SAP software
{: #download_software-rhel}
{: step}

Log in to the [SAP Support Portal)](https://support.sap.com/en/index.html){: external}, click **Download Software**, and download the required DVDs to a local share drive. Transfer the files to your provisioned server. Another option is to download the [SAP Software Download Manager)](https://support.sap.com/en/my-support/software-downloads.html#section_995042677){: external}, install it on your target server and directly download the DVD images to the server.

### Preparing for SAP's SWPM GUI
{: #prepare_for_GUI}
{: step}

Depending on your network bandwidth and latency, you might want to run the SAP Software Provisioning Manager (SWPM) graphical user interface (GUI) remotely in a virtual network computing (VNC) session. Another option is to have the GUI running locally and connect to SWPM on the target machine. Use the [SWPM documentation)](https://wiki.scn.sap.com/wiki/display/SL/Software+Provisioning+Manager+1.0+and+2.0){: external} if you decide to run the GUI locally.

The following steps outline running the SWPM GUI remotely in a virtual network computing (VNC) session. This option installs a VNC server, which might not be inline with hardening your operating system; ensure that you are meeting your security measures. Refer to [VNC documentation](http://searchnetworking.techtarget.com/definition/virtual-network-computing){: external} for an overview on its functions if you are not familiar with it.

1. Use the following commands to install a VNC server.
```
[root@sdb192 ~]# yum install tigervnc-server
```

2. Use the following command to install the X11 window manager, `twm`, which is included in the Linux distribution.

```
[root@sdb192 ~]# yum install twm
```

3. Install a terminal emulator, for example, `xterm`.

```
[root@sdb192 ~]# yum install xterm
```

4. Start the VNC server from the command line.

```
[root@sdb192 ~]# vncserver
```

You now require a VNC client program. Multiple implementations are available for all operating systems at no cost. Typically, you need port 590X (where X is the number of the servers that are running, starting at 1) to be accessible from your client.

You might have to start an xterm from the background menu of twm. You can start SWPM (sapinst) from the xterm.

### Installing SAP software
{: #install_software-rhel}
{: step}

After you download the installation media, follow the standard SAP installation procedure that is documented in the SAP installation guide for your SAP version and components, and the corresponding SAP Notes.

You can start SAP SWPM from the xterm, and run the installation steps.

#### Installing the SAP software in a three-tier environment
{: #install_software_three_tier}
{: step}

Follow the steps in SAP's SWPM for a three-tier setup.

1. Select **Distributed System** and install the ASCS and the database on the database server.
2. Install the Application Server ABAP on the application server. Be sure to use the private addresses for the ASCS and the database hostnames during installation of the application server.

The use of the private addresses and hostnames assures that network traffic between the application server and ASCS, or database, passes through the private network and not through the public network.


## Sample multipath.conf
{: #sample}
{: step}

The following sample multipath.conf is for Red Hat 7.X and NetApp-based iSCSI LUNs.
```
defaults {
        user_friendly_names no
        max_fds max
        flush_on_last_del yes
        queue_without_daemon no
        dev_loss_tmo infinity
        fast_io_fail_tmo 5
}
```

All data under blacklist must be specific to your system.
```
blacklist {
        wwid "SAdaptec*"
        devnode "^hd[a-z]"
        devnode "^(ram|raw|loop|fd|md|dm-|sr|scd|st)[0-9]*"
        devnode "^cciss.*"
}
devices {
        device {
                vendor "NETAPP"
                product "LUN"
                path_grouping_policy group_by_prio
                features "3 queue_if_no_path pg_init_retries 50"
                prio "alua"
                path_checker tur
                failback immediate
                path_selector "round-robin 0"
                hardware_handler "1 alua"
                rr_weight uniform
                rr_min_io 128
        }
}
```

Sample multipath.conf multipaths extension for ‘human readable’ device paths:
```
multipaths {
	multipath {
		wwid XXXXYYYZZZ
		alias pathname
	}
}
```
