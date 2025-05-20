---
copyright:
  years: 2023, 2025
lastupdated: "2025-05-20"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, {{site.data.keyword.powerSys_notm}} Instance, SAP HANA DB, SAP Netweaver, Storage, Tune, Tuning OS, saptune, Ansible Roles, RHEL System Roles, RHEL SAP Roles, Ansible galaxy, Power linux sap
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Deploying SAP applications on {{site.data.keyword.powerSys_notm}}
{: #powervs-set-up-power-instances}

The following information explains how to deploy {{site.data.keyword.powerSys_notm}} instances for SAP HANA database and SAP application server (NetWeaver) on Red Hat Enterprise Linux (RHEL) and SUSE Linux Enterprise Server (SLES).

An infrastructure, that was deployed on {{site.data.keyword.cloud}} and runs on {{site.data.keyword.powerSysFull}} consists of the following components:

* A separate private network for SAP system.
* A {{site.data.keyword.powerSys_notm}} instance for shared file systems.
* A {{site.data.keyword.powerSys_notm}} instance for SAP HANA.
* A {{site.data.keyword.powerSys_notm}} instance for SAP NetWeaver (on Linux).
* An operating system, such as RHEL or SLES, that is configured to use management services that are configured on {{site.data.keyword.vpc_full}} (SQUID proxy, NFS, NTP, DNS).
* A registered RHEL or SLES operating systems (OS) with IBM provided subscription, which includes SAP-specific network performance tuning, file system setup, and packages.

The following diagram shows SAP applications already deployed on {{site.data.keyword.powerSys_notm}} infrastructure.

![Figure 1. SAP applications on {{site.data.keyword.powerSys_notm}} infrastructure](../../images/powervs-provision-deploy-arch-ibm-pvs-sap-s4hana-bw4hana.svg){: caption="SAP applications on {{site.data.keyword.powerSys_notm}} infrastructure" caption-side="bottom"}
{: external download="deploy-arch-ibm-pvs-sap-s4hana-bw4hana.svg"}

## Deploying a separate private network for SAP systems
{: #powervs-set-up-power-sap-network}

For each SAP system that you deploy, create a separate private network for communication between the virtual server instances.
Follow [Configuring a private network subnet](/docs/power-iaas?topic=power-iaas-configuring-subnet) to configure a separate private network for the SAP system.

## Deploying {{site.data.keyword.powerSys_notm}} instances for SAP on {{site.data.keyword.cloud}}
{: #powervs-memory-sizing-and-subscription-concepts}

It is recommended to perform a memory sizing of SAP applications before deploying them on {{site.data.keyword.powerSys_notm}} instances.
See the [Sizing process for SAP Systems](/docs/sap?topic=sap-sizing) and [IBM {{site.data.keyword.powerSys_notm}} certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-power-vs).

Before deploying of {{site.data.keyword.powerSys_notm}} instances, you should be aware of the various options for choosing an operating system (OS) image, that is based on a subscription.
The available subscription alternatives are:

* **IBM provided subscription**, where {{site.data.keyword.cloud}} provides a full subscription to IBM stock OS images, such as RHEL and SLES, Linux&reg; for SAP applications (RHEL and SLES for SAP workloads), AIX, and IBM i.

* **Client supplied subscription**, where you use your own subscription with IBM stock OS images or custom images.
   This feature is called "Bring Your Own License" (BYOL).
   Custom images are imported by users as boot images into {{site.data.keyword.powerSys_notm}}.
   Therefore, if you plan to use your own subscription, select the OS image that has a suffix of -BYOL under the "Client supplied subscription" when deploying {{site.data.keyword.powerSys_notm}} instances in the step [Deploying an {{site.data.keyword.powerSys_notm}} instance for SAP HANA](#powervs-set-up-power-hana-instance).

For deploying and setting up the {{site.data.keyword.powerSys_notm}} instances for SAP applications, we focus on the **IBM provided subscription** for Red Hat Enterprise Linux (RHEL) and SUSE Linux Enterprise Server (SLES) images.
{: note}

More details about the subscription for RHEL and SLES could be found in [Using RHEL within the {{site.data.keyword.powerSys_notm}}](/docs/power-iaas?topic=power-iaas-linux-with-powervs) and [Using SLES within IBM {{site.data.keyword.powerSys_notm}}](/docs/power-iaas?topic=power-iaas-using-linux).

{{site.data.keyword.ibm_cloud_sap}} provides SAP-certified infrastructure to run SAP workloads, which includes the following operating systems with OS images under **IBM provided subscription**:

* Linux&reg; for SAP HANA: Red Hat Enterprise Linux for SAP HANA and SUSE Linux Enterprise Server for SAP HANA. The images have a `SAP` suffix.

* Linux&reg; for SAP application server (NetWeaver): Red Hat Enterprise Linux for SAP NetWeaver and SUSE Linux Enterprise Server for SAP NetWeaver. The images have a `NETWEAVER` suffix.

Pay attention to the choice of operating system.
Linux&reg; OS is not the same as Linux&reg; for SAP applications.
Linux&reg; OS does not have specific pre-configured settings for SAP workloads.
{: important}

See more details about Linux&reg; versions for SAP applications in [OS for IBM {{site.data.keyword.powerSys_notm}}s](/docs/sap?topic=sap-compute-os-design-considerations#os-power).

It is recommended to perform a memory sizing of SAP applications before deploying them on {{site.data.keyword.powerSys_notm}} instances.
See the [Sizing process for SAP Systems](/docs/sap?topic=sap-sizing) and [IBM {{site.data.keyword.powerSys_notm}} certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-power-vs).

### Deploying an {{site.data.keyword.powerSys_notm}} instance for SAP HANA
{: #powervs-set-up-power-hana-instance}

1. To deploy an {{site.data.keyword.powerSys_notm}} instance for SAP HANA database, select a previously created workspace from the list of [Workspaces](https://cloud.ibm.com/power/workspaces){: external} on the left navigation page.
1. Click **Virtual server instances** on the left page.
1. Create a new instance, click **Create instance** on the right side.
1. In the **General** section, make the following selections:

   | Field | Details |
   | ----- | ------- |
   | Instance name | Enter a unique name for the instance. |
   | Number of instances | Enter '1'. |
   | Add to server placement group | Optional and can be skipped. |
   | Add to a shared processor pool | Optional and can be skipped. |
   | Virtual server pinning  | Optional, can be set to None as default selection. |
   | SSH key | Choose an existing SSH key that was created previously. |
   {: caption="Table 1. SAP HANA general selections" caption-side="top"}

1. In the **Boot image** section, make the following selections:

   | Field | Details |
   | ----- | ------- |
   | Operating system | Select the IBM provided subscription 'Linux for SAP (HANA)'. See the explanation of differences in the section [Deploying {{site.data.keyword.powerSys_notm}} instances for SAP on {{site.data.keyword.cloud}}](#powervs-memory-sizing-and-subscription-concepts) |
   | Image | Select an operating system and a version. Make sure that you use the same operating system and version for all deployments. |
   | Tier | Choose a tier that best meets your needs, for more information, see [Storage tiers](/docs/power-iaas?topic=power-iaas-on-cloud-architecture#storage-tiers). |
   | Storage pool | Select the storage pool that you need. |
   | Advanced configurations | Enable toggle buttons to support more settings. |
   {: caption="Table 2. SAP HANA boot image selections" caption-side="top"}

1. In the **Profile** section, make the following selection:
    * Select a profile that meets your needs. For more information, see [SAP HANA profiles](/docs/sap?topic=sap-hana-iaas-offerings-profiles-power-vs).
1. In the **Storage volumes** section, make the following selection:
    * For SAP HANA, the attached volumes are on different storage tiers 'Tier 1' and 'Tier 3'. You can't mix storage tiers during the instance creation process, so you need to attach storage volumes later. Leave this list empty.
1. In the **Networking** section, make the following selections:
    * Leave **Public networks** deactivated.
    * Attach both private networks (management and backup) and any separate private networks. Enter the IP addresses as entered in the DNS configuration for the corresponding host names. If the IP addresses are assigned dynamically, you need to adapt the DNS entries for the host names of this system.

It takes some time for the {{site.data.keyword.powerSys_notm}} instance for SAP HANA to become available. When the deployment is completed, you can log in to the instance via the VPC access host. Use the SSH command below to login as `root` user to the virtual server instance:

```sh
ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand="ssh -W %h:%p root@<ACCESS_HOST_FLOATING_IP>" root@<HANA_PVS_IP>
```
{: pre}

`ACCESS_HOST_FLOATING_IP` is a public IP address of a jump host, `HANA_PVS_IP` is the virtual server instance IP address in the management subnet.

An alternative solution for connecting to the VPC instance is to use a VPN server.
See more details in the tutorial [Connect by using a client-to-site VPN](/docs/sap-powervs?topic=sap-powervs-solution-connect-client-vpn).

### Deploying an {{site.data.keyword.powerSys_notm}} instance for SAP NetWeaver
{: #powervs-set-up-power-netweaver-instance}

To deploy an {{site.data.keyword.powerSys_notm}} instance for SAP NetWeaver, go to your [workspace for {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-powervs-set-up-power-infrastructure) and create an {{site.data.keyword.powerSys_notm}} instance as described in [Configuring a {{site.data.keyword.powerSys_notm}} instance](/docs/power-iaas?topic=power-iaas-creating-power-virtual-server#configuring-instance).
Use the information in [Deploying SAP HANA on {{site.data.keyword.powerSys_notm}}](#powervs-set-up-power-hana-instance) to complete the configuration for **General**, **Profile**, **Storage volumes**, **Networking** sections.
For the **Boot image** section, specify the IBM provided subscription 'Linux for SAP (NetWeaver)' selection.

After creating an {{site.data.keyword.powerSys_notm}} instance for SAP NetWeaver, wait for the instance to become active.
Then log on to the SAP NetWeaver instance by using the following SSH command:

```sh
ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand="ssh -W %h:%p root@<ACCESS_HOST_FLOATING_IP>" root@<NETWEAVER_PVS_IP>
```
{: pre}

`ACCESS_HOST_FLOATING_IP` is a public IP address of a jump host, `NETWEAVER_PVS_IP` is the virtual server instance IP address in the management subnet.

Alternatively, connect by using a VPN client, as described in the tutorial [Connect by using a client-to-site VPN](/docs/sap-powervs?topic=sap-powervs-solution-connect-client-vpn).

### Deploying an optional {{site.data.keyword.powerSys_notm}} instance for SAP shared file systems
{: #powervs-set-up-sap-shared-file-system}

Each deployment of an SAP NetWeaver-based application server, contains file systems that are shared between multiple application server instances.
It is a good practice to set up a separate virtual service instance for the shared file systems.
A single {{site.data.keyword.powerSys_notm}} shared file systems instance can be used by multiple SAP systems.
Your security requirements determine how many {{site.data.keyword.powerSys_notm}} shared file systems instances you might need.

To deploy an {{site.data.keyword.powerSys_notm}} instance for shared file systems, use the Workspace where the SAP HANA and SAP NetWeaver instances were created.
Create a shared file system as described in [Configuring a {{site.data.keyword.powerSys_notm}} instance](/docs/power-iaas?topic=power-iaas-creating-power-virtual-server#configuring-instance).

After deploying the {{site.data.keyword.powerSys_notm}} instance for shared file systems, you can use it as Network File System (NFS) storage, where an NFS server will run.
How to set up the NFS server is described in the [Deploying an NFS server](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/configuring_and_using_network_file_services/deploying-an-nfs-server_configuring-and-using-network-file-services#services-required-on-an-nfs-server_deploying-an-nfs-server){: external} for [RHEL]{: tag-red} and [Sharing file systems with NFS](https://documentation.suse.com/sles/15-SP6/html/SLES-all/cha-nfs.html){: external} for [SLES]{: tag-green}.

### Creating extra storage volumes for SAP HANA {{site.data.keyword.powerSys_notm}}
{: #powervs-set-up-power-hana-volumes}

Modify {{site.data.keyword.powerSys_notm}} SAP HANA instance and attach extra storage volumes as described in [Managing your storage volumes](/docs/power-iaas?topic=power-iaas-modifying-instance#modifying-volume-network).

In accordance with the SAP sizing guidelines, you should add the following volumes:

* A storage volume for SAP HANA shared file system with a size of MIN (1 x RAM; 1 TB). Storage tier "Tier 3" is sufficient. The "Shareable" switch should remain "off".

* Four or more equal-size storage volumes for SAP HANA log file system with size MIN (1/2xRAM; 512 GB). Divide the file system size by the number of volumes to determine the size for each storage volume. Select "Tier 1", "Tier 0", or "Fixed IOPS Tier"  as the storage tier. Ensure that your configuration provides a total of at least 12,000 IOPS (input/output operations per second). The "Shareable" flag should remain "off".

* Four or more equal-sized storage volumes for the SAP HANA data file system with a size of 1.5 times the RAM. Divide the file system size by the number of volumes to determine the size for each storage volume. Select "Tier 1" or "Tier 0" as the storage tier. Ensure that your configuration provides a total of at least 8,000 IOPS (input/output operations per second). The "Shareable" flag should remain "off".

* An extra storage volume for other data (such as "/usr/sap" file system). The Storage tier "Tier 3" is sufficient. The "Shareable" flag should remain "off".

* You can add more volumes for backup or export.

Continue with the configuration of {{site.data.keyword.powerSys_notm}} instances for SAP applications, which can be done by manual or automated setup.
You can choose one of the following setup sections:

* [Configuring {{site.data.keyword.powerSys_notm}} instances manually](#powervs-set-up-powervs-manually-os-config).

* [Configuring {{site.data.keyword.powerSys_notm}} instances by using Ansible automation playbooks](#powervs-configure-with-ansible-automation).


## Configuring {{site.data.keyword.powerSys_notm}} instances manually
{: #powervs-set-up-powervs-manually-os-config}

Complete the following steps on your {{site.data.keyword.powerSys_notm}} instances.

### Checking IBM subscription for an operating system image
{: #powervs-register-os}

To ensure that the OS subscription is set up correctly, check for a subscription by running:

[RHEL]{: tag-red}:

```sh
subscription-manager release
```
{: pre}

```sh
subscription-manager list
```
{: pre}

```sh
yum repolist
```
{: pre}

Optional:

```sh
subscription-manager status
```
{: pre}

[SLES]{: tag-green}:

```sh
SUSEConnect --status
```
{: pre}

[RHEL]{: tag-red} and [SLES]{: tag-green}:

Enable the IBM Power Tools repository using:

```sh
/opt/ibm/lop/configure
```
{: pre}

check if repo is enabled:

[RHEL]{: tag-red}:

```sh
yum repolist enabled
```
{: pre}

[SLES]{: tag-green}:

```sh
zypper lr
```
{: pre}

Then run a system update. This will also update the installed Power Tools.

[RHEL]{: tag-red}:

```sh
yum -y update
```
{: pre}

[SLES]{: tag-green}:

```sh
zypper update -y
```
{: pre}


### Configuring a proxy endpoint
{: #powervs-configure-proxy}

To use proxy and cache services for HTTP, FTP, and other popular network protocols, you must to export a proxy server endpoint.
Run the following commands to export a `SQUID_PROXY_SERVER` proxy endpoint:

```sh
export http_proxy=http://<SQUID_PROXY_SERVER>:3128
export https_proxy=http://<SQUID_PROXY_SERVER>:3128
export HTTP_PROXY=http://<SQUID_PROXY_SERVER>:3128
export HTTPS_PROXY=http://<SQUID_PROXY_SERVER>:3128
```
{: pre}

To keep these exported variables persistent across multiple sessions, these entries must be added to `/etc/bash.bashrc` (on [SLES]{: tag-green}) or `/etc/bashrc` (on [RHEL]{: tag-red}).
These files ensure that new sessions use the exported variables as environment variables.

### Configuring a NTP client
{: #powervs-configure-ntp-client-manually}

If an NTP server is already configured, you must install the chrony package by using the following command.

[RHEL]{: tag-red}:

```sh
yum install -y chrony
```
{: pre}

[SLES]{: tag-green}:

```sh
zypper install -y chrony
```
{: pre}

Check the status of chrony service:

```sh
systemctl status chronyd
```
{: pre}

Adapt the `chrony.conf` configuration according to the description in the section [Setting up chrony for a system in an isolated network](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/configuring_basic_system_settings/configuring-time-synchronization_configuring-basic-system-settings#setting-up-chrony-for-a-system-in-an-isolated-network_using-chrony){: external}.

The following is a sample configuration for `/etc/chrony.conf`:

```sh

server <NTP_SERVER_IP> iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony

```
{: screen}

Then, restart the `chronyd` service on the node.

```sh
systemctl restart chronyd.service
```
{: pre}

### Configuring a DNS client
{: #powervs-configure-dns-client-manual}

The public Domain Name Service (DNS) servers of IBM Cloud are configured as default, there is no need to modify the DNS configuration for them.
If you create a private DNS server for your environment, you must configure it after the {{site.data.keyword.powerSys_notm}} instance is created.

### Configuring an NFS client
{: #powervs-configure-nfs-client-manual}

Use the following steps to manually configure an NFS client.

1. Install the `nfs-client` package and enable the NFS client by using the systemctl command.

   * [RHEL]{: tag-red}:

   ```sh
   yum install -y nfs-utils
   ```
   {: pre}

   * [SLES]{: tag-green}:

   ```sh
   zypper install -y nfs-utils
   ```
   {: pre}

1. To enable NFS client service, use the following command.

   ```sh
   systemctl start nfs-client
   ```
   {: pre}

1. After the NFS client service starts, you can mount the shared NFS directory by using the mount command.

   ```sh
   mount -t nfs4 -o sec=sys <NFS_SERVER_IP>:<NFS_DIRECTORY_PATH>
   ```
   {: pre}

   `NFS_SERVER_IP` is an IP address of a loadbalancer, `NFS_DIRECTORY_PATH` is the path of the NFS file storage share.

### Creating file systems manually
{: #powervs-create-file-systems-manual}

For shared SAP file systems, you must create a file system to store SAP data and distribute them to all SAP instances.
You can use extra file systems for other purposes.

For an SAP NetWeaver instance, you must create a file system to store instance-specific data.

Three file systems are required to install SAP HANA: data, log, and shared.
According to the default installation catalog, these file systems are `/hana/data`, `/hana/log` and `/hana/shared`, but you can customize the file system names.
You might also need file systems for other purposes (`/usr/sap` directory).
The `/hana/data` and `/hana/log` file systems are striped across four or eight disks, depending on the number of volumes that you created.
`/hana/shared` and all other file systems are non-striped 1-disk file systems.

Perform a disk discovery by running the following script:

```sh
/usr/bin/rescan-scsi-bus.sh -a -c -v
```
{: pre}

Newly discovered disks are listed with their details.

To identify the Word Wide Names (WWNs) that will be used to set up storage volumes, run:

```sh
multipath -ll
```
{: pre}

The output of the `multipath -ll` command corresponds to the World Wide Names that are also listed on the {{site.data.keyword.cloud}} console.
Log on to the {{site.data.keyword.cloud}} console and go to the [Storage volumes](https://cloud.ibm.com/power/workspaces){: external}, select a workspace and a Virtual server instance where storage volumes are defined.
You might notice that the WWNs on the {{site.data.keyword.cloud}} are in uppercase, while they are in lowercase in the operating system.

To create a file system, use the `/hana/data` example that is described below.
The same procedure is repeated for `/hana/log` and `/hana/shared` and all other file systems.
For example, a storage volume named `dm-6` has a WWN of `6005076813810214200000000000a7a8` and a size of 60G.
The `3` at the beginning is ignored.
The device name is needed to create a logical volume and a volume group.

```sh
multipath -ll
36005076813810214200000000000a72d dm-0 IBM,2145
size=100G features='1 queue_if_no_path' hwhandler='1 alua' wp=rw
|-+- policy='service-time 0' prio=50 status=active
| |- 1:0:1:0 sdj  8:144  active ready running
| |- 2:0:1:0 sdab 65:176 active ready running
| |- 3:0:1:0 sdat 66:208 active ready running
| `- 4:0:1:0 sdbl 67:240 active ready running
`-+- policy='service-time 0' prio=10 status=enabled
  |- 1:0:0:0 sda  8:0    active ready running
  |- 2:0:0:0 sds  65:32  active ready running
  |- 3:0:0:0 sdak 66:64  active ready running
  `- 4:0:0:0 sdbc 67:96  active ready running
36005076813810214200000000000a7a8 dm-6 IBM,2145
size=60G features='1 queue_if_no_path' hwhandler='1 alua' wp=rw
|-+- policy='service-time 0' prio=50 status=active
| |- 1:0:1:4 sdn  8:208  active ready running
| |- 2:0:1:4 sdaf 65:240 active ready running
| |- 3:0:1:4 sdax 67:16  active ready running
| `- 4:0:1:4 sdbp 68:48  active ready running
`-+- policy='service-time 0' prio=10 status=enabled
  |- 1:0:0:4 sde  8:64   active ready running
  |- 2:0:0:4 sdw  65:96  active ready running
  |- 3:0:0:4 sdao 66:128 active ready running
  `- 4:0:0:4 sdbg 67:160 active ready running
...
```
{: screen}

Multipath aliases are used for this setup.
Run the following commands to create the `/hana/data` file system.

Export the following variables:

```sh
export pv_size=60G
```
{: pre}

```sh
export lv_name=hana_data_lv
```
{: pre}

```sh
export vg_name=hana_data_vg
```
{: pre}

```sh
export mount=/hana/data
```
{: pre}

If you are using multipath aliases, use the following commands:

```sh
devices=$(multipath -ll | grep -B 1 $pv_size | grep dm- | awk '{print "/dev/"$2}' | tr '\n' ' ')
```
{: pre}

```sh
stripes=$(multipath -ll | grep -B 1 $pv_size | grep dm- | awk '{print "/dev/"$2}' | wc | awk '{print $1}')
```
{: pre}

```sh
pvcreate $devices
```
{: pre}

```sh
vgcreate ${vg_name} ${devices}
```
{: pre}

```sh
lvcreate -i${stripes} -I64 -l100%VG -n ${lv_name} ${vg_name}
```
{: pre}

```sh
mkfs.xfs /dev/mapper/${vg_name}-${lv_name}
```
{: pre}

```sh
mkdir -p ${mount}
```
{: pre}

```sh
mount /dev/mapper/${vg_name}-${lv_name} ${mount}
```
{: pre}

Run the same commands to create storage volumes for `/hana/log` and `/hana/shared`.
Change `lv_name` to `hana_log_lv`, `vg_name` to `hana_log_vg` and `mount` to `/hana/log`.

Export these variables:

```sh
export lv_name=hana_log_lv
```
{: pre}

```sh
export vg_name=hana_log_vg
```
{: pre}

```sh
export mount=/hana/log
```
{: pre}

Use the same approach for `/hana/shared`.

Make sure that the variable `pv_size` has different values for `/hana/data`, `/hana/log` and `/hana/shared`, before running the `export` command, otherwise the whole setup will not work.
{: important}

If you don't use multipath aliases, replace the line starting with `devices=$()` that is used to identify devices.
Instead, use following line: `devices=$(multipath -ll | grep -B 1 $pv_size | grep dm- | awk '{print "/dev/"$2}' | tr '\n' ' ')`

### Checking the storage volumes
{: #powervs-checking-of-storage-volumes}

After you create the storage volumes, verify that they were created correctly by running the following commands:

```sh
lvscan
```
{: pre}

The command output shows the status of the created logical volumes (LV). The status should be active.

To check if the file system is mounted:

```sh
mount | grep hana
```
{: pre}

The following is a sample output of this command:

```sh
/dev/mapper/hana_shared_vg-hana_shared_lv on /hana/shared type xfs (rw,relatime,seclabel,attr2,inode64,logbufs=8,logbsize=32k,noquota)
/dev/mapper/hana_log_vg-hana_log_lv on /hana/log type xfs (rw,relatime,seclabel,attr2,inode64,logbufs=8,logbsize=64k,sunit=128,swidth=256,noquota)
/dev/mapper/hana_data_vg-hana_data_lv on /hana/data type xfs (rw,relatime,seclabel,attr2,inode64,logbufs=8,logbsize=64k,sunit=128,swidth=512,noquota)
```
{: screen}

Add the file systems `/hana/data`, `/hana/log` and `/hana/shared` to the file systems table `/etc/fstab`, unless there is already an entry for each of them.

```sh
cat /etc/fstab
```
{: pre}

Sample output:

```sh
...
/dev/mapper/datavg-datalv /hana/data xfs defaults,nofail 0 0
/dev/mapper/logvg-loglv /hana/log xfs defaults,nofail 0 0
/dev/mapper/sharedvg-sharedlv /hana/shared xfs defaults,nofail 0 0
```
{: screen}

On RHEL 8.8 and RHEL 8.10 please run the following command as a workaround to prevent the logical volumes from becoming inactive after reboot:

```sh
dracut --force --verbose
```

It might be necessary to reboot the operating system.
Then check that the LV setup is still correct and proceed to the [Preparing for SAP Installation](#powervs-prepare-sap-installation) section.

## Configuring {{site.data.keyword.powerSys_notm}} instances by using Ansible automation playbooks
{: #powervs-configure-with-ansible-automation}

To configure {{site.data.keyword.powerSys_notm}} instances for SAP applications, use Ansible automation playbooks on both [RHEL]{: tag-red} and [SLES]{: tag-green}.
Download and install the `ibm.power_linux_sap` Ansible Galaxy collection:

```sh
ansible-galaxy collection install ibm.power_linux_sap
```
{: pre}

This [Ansible Galaxy collection](https://galaxy.ansible.com/ui/repo/published/ibm/power_linux_sap/docs/) is used on both operating systems, RHEL and SLES without changing any specific OS settings.

### Configuring network management services
{: #powervs-configure-network-services-client-ansible}

The `configure_network_management_services` Ansible role installs and configures a proxy endpoint, NTP, DNS, and NFS network services in a virtual server instance.
For more details, see the [full role description](https://galaxy.ansible.com/ui/repo/published/ibm/power_linux_sap/content/role/configure_network_management_services/) on Ansible Galaxy.

Before executing an Ansible playbook where the `configure_network_management_services`role is defined, configure the variable file in `playbooks/vars/sample-variables-configure-network-services-client.yml` by updating the downloaded sample with your own values, see an example how it might look like:

```yaml
client_config:
  squid:
    enable: true
    squid_server_ip_port: "SQUID_PROXY_SERVER_IP:3128"
    no_proxy_hosts: "161.0.0.0/8"
  ntp:
    enable: true
    ntp_server_ip: "NTP_SERVER_IP"
  nfs:
    enable: true
    nfs_server_path: "NFS_SERVER_IP:/nfs"
    nfs_client_path: "/nfs"
    opts: sec=sys,nfsvers=4.1,nofail
    fstype: nfs4
  dns:
    enable: true
    dns_server_ip: "DNS_SERVER_IP"
```
{: pre}

A detailed description of the parameters `SQUID_PROXY_SERVER_IP`, `NTP_SERVER_IP`, `NFS_SERVER_IP`, `DNS_SERVER_IP`, that are used for the above example, can be found in the section [Edit parameters in the configuration file](https://github.com/IBM/ansible-power-linux-sap/tree/main/roles/configure_network_management_services#3-edit-parameters-in-the-configuration-file).

After updating the variable file, run the following command:

```sh
ansible-playbook --connection=local -i "localhost," playbooks/sample-configure-network-services-client.yml
```
{: pre}

### Creating the file systems
{: #powervs-create-files-system-ansible}

The Ansible role, `powervs_storage_and_swap_setup` is used to create file systems for SAP HANA, SAP NetWeaver, or for a SAP shared file systems instance.
This role performs tasks such as creating file systems for `/hana/data`, `/hana/log` and `/hana/shared`, mounting these file systems on provided mount points, adding an entry to `/etc/fstab` for automount on reboot, and other tasks.
For more details, see the description of [`powervs_storage_and_swap_setup` on Ansible Galaxy](https://galaxy.ansible.com/ui/repo/published/ibm/power_linux_sap/content/role/powervs_storage_and_swap_setup/).

Before running a playbook for a storage setup, identify the WWNs as described in the [Manually creating file systems](#powervs-create-file-systems-manual) section.
Add the WWNs to the `sample-variables-powervs-storage-setup.yml` variable file.
For an explanation of the variable file, see the [Edit parameters in the configuration file](https://galaxy.ansible.com/ui/repo/published/ibm/power_linux_sap/content/role/powervs_storage_and_swap_setup/) section.

Next, run an Ansible playbook to create the file systems as:

```sh
ansible-playbook --connection=local -i "localhost," playbooks/sample-powervs-storage-setup.yml
```
{: pre}

To see if all file systems have been created and mounted correctly, use the [Checking of storage volumes](#powervs-checking-of-storage-volumes) section.
Continue to [Preparing for SAP installation](#powervs-prepare-sap-installation), if the `/hana/data`, `/hana/log`, and `/hana/shared` are mounted correctly.

## Preparing for SAP software installation
{: #powervs-prepare-sap-installation}

Preparation for SAP software installation is required for SAP HANA and SAP NetWeaver, but is not needed on the {{site.data.keyword.powerSys_notm}} instance for SAP shared file systems.

### Configuring SLES for SAP applications
{: #powervs-using-saptune-sles-sap}

[SLES]{: tag-green}

Use the saptune tool to apply recommended operating system settings for SAP HANA or SAP NetWeaver on SUSE Linux® Enterprise Server.
On IBM Power Systems Virtual Servers, the same SUSE Linux® Enterprise Server image is used for SAP NetWeaver and SAP HANA.

The following workflow shows how you can use the saptune tool to apply the SAP solution to your server.
For more information about saptune, see [SAP Note 1275776 - Linux: Preparing SLES for SAP environments](https://me.sap.com/notes/1275776){: external}.

1. Verify that the package status is current.

    ```sh
    zypper info saptune
    ```
    {: pre}

1. Verify that the saptune version is at least 3.

    ```sh
    saptune version
    ```
    {: pre}

1. List all available solutions. Numbered entries represent integrated SAP Notes for each of the solutions.

    ```sh
    saptune solution list
    ```
    {: pre}

1. Get an overview of saptune options.

    ```sh
    saptune --help
    ```
    {: pre}

1. Enable and start the saptune.service. This command also disables sapconf and tuned, which isn't used since saptune version 3.

    ```sh
    saptune service takeover
    ```
    {: pre}

1. Simulate the changes before applying them (optional).

    For SAP HANA:

    ```sh
    saptune solution simulate HANA
    ```
    {: pre}

    For SAP NetWeaver:

    ```sh
    saptune solution simulate NETWEAVER
    ```
    {: pre}

1. Apply the saptune solution.

    For SAP HANA:

    ```sh
    saptune solution apply HANA
    ```
    {: pre}

    For SAP NetWeaver:

    ```sh
    saptune solution apply NETWEAVER
    ```
    {: pre}

1. Check the saptune status.

    ```sh
    saptune status
    ```
    {: pre}

1. Verify if the saptune is set up correctly.

    ```sh
    saptune check
    ```
    {: pre}

### Configuring RHEL for SAP applications
{: #powervs-configure-RHEL-system-roles-sap}

[RHEL]{: tag-red}

RHEL System Roles for SAP are a collection of Ansible roles that help you configure a RHEL system for installing SAP HANA or SAP NetWeaver.
Ansible roles for SAP configuration are distributed and updated directly by Red Hat, so the task performed and parameters required might vary depending on the version of the `rhel-system-roles-sap` package.
The RHEL image that is provided by IBM includes the Ansible execution engine, SAP-related system roles, and the Ansible execution files.

Starting with `rhel-system-roles-sap-3.2.0-1.el8_4`, the role names have changed.
The `/root/sap-preconfigure.yml`, `/root/sap-netweaver.yml`, and `/root/sap-hana.yml` files in the OS images for RHEL 8.1 or RHEL 8.4 must be adapted.
For more information, see [following Red Hat article](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/8/html-single/red_hat_enterprise_linux_system_roles_for_sap/index){: external}.
{: note}

|Previous role name         |New role name             |
|---------------------------|--------------------------|
|sap-preconfigure           |sap_general_preconfigure  |
|sap-netweaver-preconfigure |sap_netweaver_preconfigure|
|sap-hana-preconfigure      |sap_hana_preconfigure     |
{: caption="Table 3. RHEL System Roles" caption-side="bottom"}

The RHEL system roles for setting up SAP application are available in the root directory.

Use the following command to prepare the operating system for an SAP HANA workload.

* For RHEL 8.4 and previous versions:

```sh
ansible-playbook /root/sap-hana.yml
```
{: pre}

* For RHEL versions RHEL 8.6 and greater:

```sh
ansible-playbook -i /root/inventory /root/sap-hana.yml
```
{: pre}

Use the following command to prepare the operating system for an SAP NetWeaver workload.

* For RHEL 8.4 and previous versions:

```sh
ansible-playbook /root/sap-netweaver.yml
```
{: pre}

* For RHEL versions RHEL 8.6 and greater:

```sh
ansible-playbook -i /root/inventory /root/sap-netweaver.yml
```
{: pre}

For more information about customizing the operating system, see the following documentation.
* [SAP Note 2772999 "Red Hat Enterprise Linux 8.x: Installation and Configuration"](https://me.sap.com/notes/2772999){: external}
* [SAP Note 2777782 "SAP HANA DB: Recommended OS Settings for RHEL8"](https://me.sap.com/notes/2777782){: external}
* [SAP Note 2382421 "Optimizing the Network Configuration on HANA- and OS-Level"](https://me.sap.com/notes/2382421){: external}
* [Red Hat Enterprise Linux System Roles for SAP](https://access.redhat.com/sites/default/files/attachments/rhel_system_roles_for_sap_1.pdf){: external}

### Configuring jumbo frames
{: #powervs-fnetwork-configurations-sles-rhel}

Jumbo frames should be enabled by setting `MTU='9000'`.

[RHEL]{: tag-red}

In the `/etc/sysconfig/network-scripts` directory, check that the `ifcfg-env0`, `ifcfg-env2`, `ifcfg-env(...)` files contain the `MTU='9000' parameter`.

[SLES]{: tag-green}

In the `/etc/sysconfig/network` directory, check the content of the files such as `ifcfg-eth0`, `ifcfg-eth1`, etc.

### Checking the NUMA layout
{: #powervs-numa-layout}

Check that the CPU and memory placement is optimized for SAP HANA by running the `chk_numa_lpm.py` script.
The `chk_numa_lpm.py` script performs the following actions.

* Checks the nonuniform memory access (NUMA) layout according to SAP HANA rules.
   The script verifies that there are no cores without memory and that the memory distribution between the cores doesn't exceed a margin of 50%.
   In the first case, the script generates an error; in the second case, the script generates a warning.
* Checks if a Live Partition Mobility (LPM) operation has been performed.
   After LPM, the NUMA layout might be different from the configuration at boot time.
   The script searches the system log for the last LPM operation.
   A warning is generated if there has been an LPM operation since the last system boot.

1. Check the information in [SAP Note 2923962](https://me.sap.com/notes/2923962){: external}.
1. Download the `chk_numa_lpm.py` script that is attached to this SAP Note and copy it to your {{site.data.keyword.powerSys_notm}} instance.
1. Set executable permissions for the script:

    ```sh
    chmod +x ./chk_numa_lpm.py
    ```
    {: pre}

1. Run the script:


    ```sh
    ./chk_numa_lpm.py
    ```
    {: pre}

## Next Steps
{: #powervs-set-up-power-next_steps}

Your infrastructure is now ready to install the SAP software.
