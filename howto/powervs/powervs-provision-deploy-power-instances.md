---
copyright:
  years: 2023, 2026
lastupdated: "2026-01-29"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, {{site.data.keyword.powerSys_notm}} Instance, SAP HANA DB, SAP Netweaver, Storage, Tune, Tuning OS, saptune, Ansible Roles, RHEL System Roles, RHEL SAP Roles, Ansible galaxy, Power linux sap
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Preparing {{site.data.keyword.powerSys_notm}} Instances for SAP System Installation
{: #powervs-set-up-power-instances}

Follow the instructions to deploy the {{site.data.keyword.powerSys_notm}} instances that are required for SAP system installation.
The guidance includes configuring virtual server instances for the SAP HANA system, and the ABAP application server on either Red Hat&; Enterprise Linux&; (RHEL) or SUSE Linux Enterprise Server (SLES).

A {{site.data.keyword.vpc_full}} landing zone, a {{site.data.keyword.tg_full}}, and one {{site.data.keyword.powerSys_notm}} workspace are required as prerequisites.
To deploy these prerequisites, use the automation {{site.data.keyword.powerSys_notm}} with VPC landing zone{: external}, available in the {{site.data.keyword.cloud_notm}} catalog.

Figure 1 illustrates the infrastructure architecture and highlights the commonly used cloud services.
Within the {{site.data.keyword.vpc_short}} environment, the cloud services include a SQUID proxy, an Ansible automation node, virtual private endpoints, file storage, and a Domain Name System (DNS) forwarder.
Also, the architecture incorporates the {{site.data.keyword.tg_full}} and the {{site.data.keyword.powerSys_notm}} workspace.
![Figure 1. Infrastructure components for SAP system installation and their roles](../../images/powervs-provision-deploy-arch-ibm-pvs-sap-s4hana-bw4hana.svg){: caption="Infrastructure components for SAP system installation and their roles" caption-side="bottom"}
{: external download="deploy-arch-ibm-pvs-sap-s4hana-bw4hana.svg"}

## Deploying infrastructure components for the SAP system in the {{site.data.keyword.powerSys_notm}} workspace
{: #powervs-set-up-power-infrastructure}

Deploy the following resources for the SAP system in the {{site.data.keyword.powerSys_notm}} workspace:
- A private subnet to enable communication within the SAP system.
- A dedicated {{site.data.keyword.powerSys_notm}} instance for the SAP HANA system.
- The storage volumes for the SAP HANA system.
- One or more {{site.data.keyword.powerSys_notm}} instances for the SAP application servers.
- The file storage and storage volumes for the SAP application servers.
- Optional, one {{site.data.keyword.powerSys_notm}} instance for the SAP shared global file system and transport directory.
- All virtual server instances run either Red Hat Enterprise Linux (RHEL) or SUSE Linux Enterprise Server (SLES) as the operating system.

### Deploying a dedicated private network subnet
{: #powervs-set-up-power-sap-network}

Create a dedicated private network subnet for each SAP system that you install in the workspace.
For detailed steps, see [Configuring a private network subnet](/docs/power-iaas?topic=power-iaas-configuring-subnet).


### Planning the {{site.data.keyword.powerSys_notm}} instances
{: #powervs-memory-sizing-and-subscription-concepts}

Refer to the [Sizing process for SAP Systems](/docs/sap?topic=sap-sizing) to validate the sizing for the SAP system.
Use the sizing results to select the appropriate compute profiles for the {{site.data.keyword.powerSys_notm}} instances.
For information about available profiles, see SAP HANA certified instances on {{site.data.keyword.powerSys_notm}} and SAP Application Server certified instances on {{site.data.keyword.powerSys_notm}}.

You can choose one of the following options for the OS image subscription:

IBM provided subscription:
{{site.data.keyword.cloud}} offers full subscriptions for boot images for AIX, IBM i, and Linux operating systems.
Choose Linux for SAP HANA, or Linux for SAP Netweaver.

Client supplied subscription ("Bring Your Own License" - BYOL)
If you use a client supplied subscription, either:
- Import your own image as a custom image for deployment, or
- Select an image from the client supplied subscription section of the IBM stock images (identified with the BYOL suffix) for {{site.data.keyword.powerSys_notm}} instance deployment.

See more details about the operating system versions in [OS for IBM {{site.data.keyword.powerSys_notm}}s](/docs/sap?topic=sap-compute-os-design-considerations#os-power).
See [Using RHEL within the {{site.data.keyword.powerSys_notm}}](/docs/power-iaas?topic=power-iaas-linux-with-powervs) and [Using SLES within IBM {{site.data.keyword.powerSys_notm}}](/docs/power-iaas?topic=power-iaas-using-linux) for more details about the subscriptions for RHEL and SLES.

The following steps describe deploying {{site.data.keyword.powerSys_notm}} instances by using an **IBM provided subscription**.
{: note}

{{site.data.keyword.ibm_cloud_sap}} provides SAP-certified infrastructure to run SAP workloads.
The **Boot image** selection offers the following operating systems with an **IBM provided subscription** for both RHEL and SLES:
- **Linux for SAP (HANA)** for SAP HANA systems.
   The image names have the suffix `-SAP`.
- **Linux for SAP (NetWeaver)** for SAP application servers.
   The image names have the suffix `-SAP-NETWEAVER`.

Pay attention to the choice of operating system.
**Linux** is different than **Linux for SAP (HANA)** and **Linux for SAP (NetWeaver)**.
Plain **Linux** images do not include the specific extensions that are required to run SAP systems for production environments.
{: important}


### Deploying an {{site.data.keyword.powerSys_notm}} instance for the SAP HANA system
{: #powervs-set-up-power-hana-instance}

Deploy an {{site.data.keyword.powerSys_notm}} instance for the SAP HANA system.

1. Select a workspace from the list of [Workspaces](https://cloud.ibm.com/power/workspaces){: external} on the left navigation page.
1. Click **View virtual servers** on the right page.
   The list of virtual server instances is shown.
1. Click **Create instance** to create a new virtual server instance.
Complete the fields in the **General** section.

   | Field | Details |
   | ----- | ------- |
   | Instance name | Enter a unique name for the instance. |
   | Number of instances | Enter '1'. |
   | Add to a server placement group | Optional and can be skipped. |
   | Add to a shared processor pool | Optional and can be skipped. |
   | Virtual server pinning  | Optional, choose the default selection "None". |
   | SSH key | Choose an existing SSH key. |
   {: caption="General parameter selection for the SAP HANA instance" caption-side="top"}

   Click **Continue**.

Complete the fields in the **Boot image** section.

   | Field | Details |
   | ----- | ------- |
   | Operating system | Select the IBM provided subscription 'Linux for SAP (HANA)'. See the explanation of differences in the section [Deploying {{site.data.keyword.powerSys_notm}} instances for SAP on {{site.data.keyword.cloud}}](#powervs-memory-sizing-and-subscription-concepts) |
   | Image | Select an operating system and a version. Make sure that you use the same operating system and version for all deployments. |
   | Tier | Choose a storage tier that best meets your needs, for more information, see [Storage tiers](/docs/power-iaas?topic=power-iaas-on-cloud-architecture#storage-tiers). |
   | Storage pool | Select the storage pool that you need. |
   | Advanced configurations | Optional. Expand, and toggle the items only if required. |
   {: caption="Boot image selection for the SAP HANA instance" caption-side="top"}

   Advanced configurations allow influencing the profile selection for subsequent steps.
   When you select **Custom SAP (HANA) Deployment**, you can modify parameters such as CPU cores, memory size, and storage tier. Custom profiles are supported only on Power9 systems, and are intended exclusively for nonproduction environments.
   When you select **SAP RISE Deployment**, you can choose from predefined profiles that are aligned with the RISE with SAP on Power Virtual Server offering.
   {: note}

   Click **Continue**.

Complete the fields in the **Profile** section.

   | Field                           | Details                                                                              |
   | ------------------------------- | ------------------------------------------------------------------------------------ |
   | Deploy to dedicated host        | Optional. Check only if you reserved a dedicated host before in your environment.    |
   | Machine type                    | Select one of the IBM Power servers offered in {{site.data.keyword.powerSys_notm}}.  |
   | Core type                       | The core type for virtual server instances for SAP HANA is always set to ‘Dedicated’.|
   | Profile                         | Select a compute profile that meets your needs.                                      |
   {: caption="Profile parameter selection for the SAP HANA instance" caption-side="top"}

   Click **Continue**.

Complete the fields in the **Storage volumes** section.

    For the SAP HANA system, you create storage volumes that belong to different storage tiers 'Tier 1' and 'Tier 3'.
    You cannot mix storage tiers during the instance creation process, so you need to create and attach the storage volumes later.
    Do not specify the storage volumes now.
    {: important}

   Click **Continue**.

Complete the fields in the **Networking** section.
    * Do not specify any **Public networks**.
    * Click **Attach existing network** to attach the subnet.
       * Select the name of the subnet from the list of existing subnets.
       * Select **Manually specify an IP address from IP range**, and enter the IP address `<HANA_PVS_IP>` that you reserved before in your DNS setup.
         If you select **Automatically assign an IP address from IP range**, then the IP address is assigned dynamically.
         In that case, you must retrieve the `<HANA_PVS_IP>` after deployment and update your DNS configuration by using this IP address for the hostname of the instance.
       * Click **Attach**

Click **Finish**, accept the terms and conditions, and then click **Create**.

It takes some time for the {{site.data.keyword.powerSys_notm}} instance for SAP HANA to become available.

When the deployment is complete, log in to the instance by using the VPC access host.
Use the following SSH command to login as `root` user to the virtual server instance:

```sh
ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand="ssh -W %h:%p root@<ACCESS_HOST_FLOATING_IP>" root@<HANA_PVS_IP>
```
{: pre}

`<ACCESS_HOST_FLOATING_IP>` is the public IP address of the jump host.
`<HANA_PVS_IP>` is the IP address of the virtual server instance.

An alternative way to connect to the VPC instance is to use a VPN server.
For more information, see the tutorial [Connect by using a client-to-site VPN](/docs/sap-powervs?topic=sap-powervs-solution-connect-client-vpn).

### Deploying a {{site.data.keyword.powerSys_notm}} instance for SAP NetWeaver
{: #powervs-set-up-power-netweaver-instance}

To deploy a {{site.data.keyword.powerSys_notm}} instance for SAP NetWeaver, go to your [Workspaces](https://cloud.ibm.com/power/workspaces){: external} and create a new instance as described in [Configuring a {{site.data.keyword.powerSys_notm}} instance](/docs/power-iaas?topic=power-iaas-creating-power-virtual-server#configuring-instance).
Use the information in [Deploying SAP HANA on {{site.data.keyword.powerSys_notm}}](#powervs-set-up-power-hana-instance) to complete the configuration for **General**, **Boot Image**, **Profile**, **Storage volumes**, and **Networking** sections.
For the **Boot image** section, specify the IBM provided subscription 'Linux for SAP (NetWeaver)' in the selection.

Wait until the instance for SAP NetWeaver becomes active.
Then log on to the instance by using the following SSH command:

```sh
ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand="ssh -W %h:%p root@<ACCESS_HOST_FLOATING_IP>" root@<NETWEAVER_PVS_IP>
```
{: pre}

`<ACCESS_HOST_FLOATING_IP>` is the public IP address of the jump host.
`<NETWEAVER_PVS_IP>` is the IP address of the virtual server instance.

Alternatively, connect by using a VPN client, as described in the tutorial [Connect by using a client-to-site VPN](/docs/sap-powervs?topic=sap-powervs-solution-connect-client-vpn).

### Deploying the shared file systems for a distributed SAP system installation
{: #powervs-set-up-sap-shared-file-system}

A distributed SAP system installation requires file systems that are shared between the multiple virtual server instances.
[File Storage for VPC](/docs/vpc?group=file-storage-for-vpc) is recommended for the shared file systems.
See [Accessing file storage shares for VPC](/docs/sap?group=file-storage-shares-for-vpc) for more details about creating, accessing, and mounting the file shares on the virtual server instances.
{: note}

### Creating storage volumes
{: #powervs-set-up-power-hana-volumes}

To plan storage volumes for SAP HANA, refer to the tables provided in [SAP HANA certified instances on IBM Power Virtual Server](/docs/sap?topic=sap-hana-iaas-offerings-profiles-power-vs).
Locate the compute profile applicable to your deployment and review the *Log File System*, *Data File System*, and *Shared File System* tabs.
These tabs specify the required quantity of storage volumes, the storage tier, and the corresponding volume sizes for the file systems `/hana/log`, `/hana/data`, and `/hana/shared`.

To plan storage volumes for the SAP application server, refer to the table in [SAP Application Server certified instances on IBM Power Virtual Server](/docs/sap?topic=sap-nw-iaas-offerings-profiles-power-vs).
Use the SAP Application Server Filesystem tab to determine the storage tier, and the size of the storage volume for the `/usr/sap` file system.

Modify the {{site.data.keyword.powerSys_notm}} virtual server instances and attach the volumes to the virtual server instance for SAP HANA or the SAP application server.
See [Managing your storage volumes](/docs/power-iaas?topic=power-iaas-modifying-instance#modifying-volume-network).

Make sure that the "Shareable" option remains "off" for all the block storage volumes.
{: note}

Continue with the configuration of {{site.data.keyword.powerSys_notm}} instances for SAP applications.
Choose one of the options for a manual or an automated setup.

* [Configuring {{site.data.keyword.powerSys_notm}} instances manually](#powervs-set-up-powervs-manually-os-config).

* [Configuring {{site.data.keyword.powerSys_notm}} instances by using Ansible automation playbooks](#powervs-configure-with-ansible-automation).

## Configuring {{site.data.keyword.powerSys_notm}} instances manually
{: #powervs-set-up-powervs-manually-os-config}

Complete the following steps on your {{site.data.keyword.powerSys_notm}} instances.

### Configuring a proxy endpoint
{: #powervs-configure-proxy}

The {{site.data.keyword.powerSys_notm}} instances are not attached to a public network, and do not have direct access to the internet.
The SQUID proxy server in IBM VPC acts as an intermediary between the virtual server instance and the internet.
In a Linux environment, environment variables such as `http_proxy`, `https_proxy`, and `no_proxy` configure the proxy settings.

Run the following commands to export a `SQUID_PROXY_SERVER` proxy endpoint:

```sh
export http_proxy=http://<SQUID_PROXY_SERVER>:3128
export https_proxy=http://<SQUID_PROXY_SERVER>:3128
export HTTP_PROXY=http://<SQUID_PROXY_SERVER>:3128
export HTTPS_PROXY=http://<SQUID_PROXY_SERVER>:3128
```
{: pre}

The commands set the environment variables temporarily, they do not persist across multiple terminal sessions, and do not survive a restart.
To keep the environment variables persistent, add these entries to file `/etc/bash.bashrc` (on [SLES]{: tag-green}) or `/etc/bashrc` (on [RHEL]{: tag-red}).

### Checking IBM subscription for an operating system image
{: #powervs-register-os}

To help ensure that the OS subscription is set up correctly, check for a subscription by running:

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

Enable the IBM Power Tools repository by using:

```sh
/opt/ibm/lop/configure
```
{: pre}

Check whether the repo is enabled:

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

Run a system update.
This process also updates the installed IBM Power Tools.

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

### Configuring a Domain Name System (DNS) client
{: #powervs-configure-dns-client-manual}

The DNS servers of IBM Cloud are configured as default, you do not need to modify the DNS configuration for them.
If you create a private DNS server for your environment, configure it after the {{site.data.keyword.powerSys_notm}} instance is created.

### Configuring a Network Time Protocol client
{: #powervs-configure-ntp-client-manually}

Install the `chrony` package by using the following command.

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

Adapt the `/etc/chrony.conf` configuration.
In {{site.data.keyword.powerSys_notm}} workspaces enabled for Power Edge Router (PER), the virtual server instances can directly access IBM Cloud NTP server `time.adn.networklayer.com`.

See the following sample configuration for `/etc/chrony.conf`:

```sh
server time.adn.networklayer.com iburst
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

### Configuring a Network File System (NFS) client
{: #powervs-configure-nfs-client-manual}

For the shared SAP file systems, create file storage for VPC and mount it on the virtual server instances.
Use the following steps to manually configure the NFS client.

1. Install the `nfs-client` package and enable the NFS client by using the `systemctl` command.

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

   `NFS_SERVER_IP` is the IP address of the NFS server.
   `NFS_DIRECTORY_PATH` is the path of the NFS file storage share.

### Creating file systems manually
{: #powervs-create-file-systems-manual}

For an ABAP application server, create a file system `/usr/sap` to store instance-specific data.

To install SAP HANA, three file systems are required:
- HANA Data
- HANA Log
- HANA Shared

By default, these file systems are named:
- `/hana/data`
- `/hana/log`
- `/hana/shared`
However, you can customize the names.
You might configure other file systems, such as for the `/usr/sap` directory.

Stripe the `/hana/data` and `/hana/log` file systems across four or eight volumes.
The `/hana/shared` file system and all other file systems are nonstriped, single-disk file systems.

Extra file systems can be created for other purposes.

```sh
/usr/bin/rescan-scsi-bus.sh -a -c -v
```
{: pre}

Newly discovered disks are listed with their details.

Identify the Word Wide Names (WWNs) of the storage volumes by running the `multipath` command.

```sh
multipath -ll
```
{: pre}

The output of the `multipath -ll` command details the World Wide Names of the volumes. The WWNs are also visible on the {{site.data.keyword.cloud_notm}} console.
Log on to the {{site.data.keyword.cloud}} console and go to the [Storage volumes](https://cloud.ibm.com/power/workspaces){: external}, select the workspace, and then select the virtual server instance for which the storage volumes are defined.

Notice that the WWNs on the {{site.data.keyword.cloud}} are in uppercase, while they are in lowercase in the output of the `multipath -ll` command.
In the output of the `multipath -ll` command, a prefix `3` is added at the beginning of each WWN.
{: note}

The example illustrates the steps to create a `/hana/data` file system.
Repeat similar steps for `/hana/log`, `/hana/shared`, and all the other file systems.

For example, a storage volume that is named `dm-6` has the WWN `6005076813810214200000000000a7a8` and a size of `60G`.

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

One or more device names need to be extracted to create the physical volume devices, the volume group, and the logical volume.

In the example, multipath aliases are used.
Run the following commands to create the `/hana/data` file system.

Export the following variables:

```sh
export PV_SIZE="60G"
```
{: pre}

```sh
export LV_NAME="hana_data_lv"
```
{: pre}

```sh
export VG_NAME="hana_data_vg"
```
{: pre}

```sh
export MOUNT="/hana/data"
```
{: pre}

If you are using multipath aliases, use the following commands:

```sh
DEVICES=$(multipath -ll | grep -B 1 ${PV_SIZE} | grep dm- | awk '{print "/dev/"$2}' | tr '\n' ' ')
```
{: pre}

```sh
STRIPES=$(multipath -ll | grep -B 1 ${PV_SIZE} | grep dm- | awk '{print "/dev/"$2}' | wc | awk '{print $1}')
```
{: pre}

```sh
pvcreate ${DEVICES}
```
{: pre}

```sh
vgcreate ${VG_NAME} ${DEVICES}
```
{: pre}

```sh
lvcreate -i${STRIPES} -I64 -l100%VG -n ${LV_NAME} ${VG_NAME}
```
{: pre}

```sh
mkfs.xfs /dev/mapper/${VG_NAME}-${LV_NAME}
```
{: pre}

```sh
mkdir -p ${MOUNT}
```
{: pre}

```sh
mount /dev/mapper/${VG_NAME}-${LV_NAME} ${MOUNT}
```
{: pre}

Run the same commands to create storage volumes for `/hana/log` and `/hana/shared`.
Change `LV_NAME` to `hana_log_lv`, `VG_NAME` to `hana_log_vg` and `MOUNT` to `/hana/log`.

Export these variables:

```sh
export LV_NAME=hana_log_lv
```
{: pre}

```sh
export VG_NAME=hana_log_vg
```
{: pre}

```sh
export MOUNT=/hana/log
```
{: pre}

Use the same approach for `/hana/shared`.

You must have different storage volume sizes and so different values for the `PV_SIZE` for `/hana/data`, `/hana/log`, and `/hana/shared`.
Otherwise, the approach fails.
{: important}

If you don't use multipath aliases, adjust the `DEVICES=$()` command that is used to identify the disk devices.
Use the following `DEVICES=$(multipath -ll | grep -B 1 ${PV_SIZE} | grep dm- | awk '{print "/dev/"$2}' | tr '\n' ' ')` instead.

### Checking the storage volumes
{: #powervs-checking-of-storage-volumes}

After you create the storage volumes, verify that they were created correctly by running the following commands:

```sh
lvscan
```
{: pre}

The command output shows the status of the created logical volumes (LV). Verify that the status is `active`.

Verify that the file system is mounted:

```sh
mount | grep hana
```
{: pre}

See a sample output of the command:

```sh
/dev/mapper/hana_shared_vg-hana_shared_lv on /hana/shared type xfs (rw,relatime,seclabel,attr2,inode64,logbufs=8,logbsize=32k,noquota)
/dev/mapper/hana_log_vg-hana_log_lv on /hana/log type xfs (rw,relatime,seclabel,attr2,inode64,logbufs=8,logbsize=64k,sunit=128,swidth=256,noquota)
/dev/mapper/hana_data_vg-hana_data_lv on /hana/data type xfs (rw,relatime,seclabel,attr2,inode64,logbufs=8,logbsize=64k,sunit=128,swidth=512,noquota)
```
{: screen}

Add the file systems `/hana/data`, `/hana/log`, and `/hana/shared` to the file systems table `/etc/fstab`, unless an entry for each of them exists.

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

On RHEL 8.8 and RHEL 8.10 run the following command as a workaround to prevent the logical volumes remain closed after a restart:

```sh
dracut --force --verbose
```

It might be necessary to restart the operating system.
After the restart, check that the LV setup is correct.

Continue with [Preparing for SAP software installation](#powervs-prepare-sap-installation).

## Configuring {{site.data.keyword.powerSys_notm}} instances by using Ansible automation playbooks
{: #powervs-configure-with-ansible-automation}

To configure {{site.data.keyword.powerSys_notm}} instances for SAP applications, use Ansible automation playbooks on both [RHEL]{: tag-red} and [SLES]{: tag-green}.

Download and install the `ibm.power_linux_sap` Ansible Galaxy collection:

```sh
ansible-galaxy collection install ibm.power_linux_sap
```
{: pre}

Additionally, download and install the required collections:

```sh
cd /root/.ansible/collections/ansible_collections/ibm/power_linux_sap
ansible-galaxy collection install -r requirements.yml
```
{: pre}

This [Ansible Galaxy collection](https://galaxy.ansible.com/ui/repo/published/ibm/power_linux_sap/docs/) is used on both operating systems, RHEL, and SLES without changing any specific OS settings.

### Configuring network management services
{: #powervs-configure-network-services-client-ansible}

The `configure_network_management_services` Ansible role installs and configures a proxy endpoint, NTP, DNS, and NFS network services in a virtual server instance.
For more details, see the [configure_network_management_services on Ansible Galaxy](https://galaxy.ansible.com/ui/repo/published/ibm/power_linux_sap/content/role/configure_network_management_services/){: external}.

Configure the variable file in `playbooks/vars/sample-variables-configure-network-services-client.yml` first to run an Ansible playbook with the `configure_network_management_services` role.
Update the sample with your own values.
See an example how it might look like:

```yaml
client_config:
  squid:
    enable: true
    squid_server_ip_port: "<SQUID_PROXY_SERVER_IP>:3128"
    no_proxy_hosts: "161.0.0.0/8"
  ntp:
    enable: true
    ntp_server_ip: "time.adn.networklayer.com"
  nfs:
    enable: true
    nfs_server_path: "<NFS_SERVER_IP>:/nfs"
    nfs_client_path: "/nfs"
    opts: sec=sys,nfsvers=4.1,nofail
    fstype: nfs4
  dns:
    enable: true
    dns_server_ip: "<DNS_SERVER_IP>"
```
{: pre}

A detailed description of the parameters `SQUID_PROXY_SERVER_IP`, `NTP_SERVER_IP`, `NFS_SERVER_IP`, `DNS_SERVER_IP`, that are used for the preceding example, can be found in the section [Edit parameters in the configuration file](https://github.com/IBM/ansible-power-linux-sap/tree/main/roles/configure_network_management_services#3-edit-parameters-in-the-configuration-file){: external}.

Then, run the following command:

```sh
ansible-playbook --connection=local -i "localhost," playbooks/sample-configure-network-services-client.yml
```
{: pre}

### Creating the file systems
{: #powervs-create-files-system-ansible}

The Ansible role, `powervs_storage_and_swap_setup` is used to create file systems for both SAP HANA, and the SAP application server.
This role runs tasks such as creating file systems for `/hana/data`, `/hana/log` and `/hana/shared`, mounting these file systems on provided mount points, adding an entry to `/etc/fstab` for automount on restart, and other tasks.
For more details, see the description of [`powervs_storage_and_swap_setup` on Ansible Galaxy](https://galaxy.ansible.com/ui/repo/published/ibm/power_linux_sap/content/role/powervs_storage_and_swap_setup/){: external}.

Identify the WWNs as described in the [Manually creating file systems](#powervs-create-file-systems-manual) section first to run a playbook for storage setup.
Add the WWNs to the `sample-variables-powervs-storage-setup.yml` variable file.
For an explanation of the variable file, see the [Edit parameters in the configuration file](https://galaxy.ansible.com/ui/repo/published/ibm/power_linux_sap/content/role/powervs_storage_and_swap_setup/){: external} section.

Next, run an Ansible playbook to create the file systems as:

```sh
ansible-playbook --connection=local -i "localhost," playbooks/sample-powervs-storage-setup.yml
```
{: pre}

To verify that all file systems are created and mounted correctly, use the [Checking of storage volumes](#powervs-checking-of-storage-volumes) section.
When `/hana/data`, `/hana/log`, and `/hana/shared` are mounted correctly then continue with [Preparing for SAP installation](#powervs-prepare-sap-installation).

## Preparing for SAP software installation
{: #powervs-prepare-sap-installation}

The steps are required only for the virtual server instances for the SAP HANA system, and the SAP application server.
You can skip these steps for the {{site.data.keyword.powerSys_notm}} instance for the SAP shared file systems.

### Configuring SLES for SAP applications
{: #powervs-using-saptune-sles-sap}

[SLES]{: tag-green}

Use the `saptune` tool to apply the recommended operating system settings for SLES to the SAP HANA system (HANA) or the application server (NETWEAVER).
For more information about `saptune`, see [SAP Note 1275776 - Linux: Preparing SLES for SAP environments](https://me.sap.com/notes/1275776){: external}.

1. Verify that the `saptune` package is installed.

    ```sh
    zypper info saptune
    ```
    {: pre}

1. Verify that the `saptune` version is at least 3.

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

1. Enable and start the `saptune.service`. The command disables `sapconf` and `tuned`, which are no longer used.

    ```sh
    saptune service takeover
    ```
    {: pre}

1. Simulate the changes first before they are applied (optional).

    On the virtual server instance for the SAP HANA system:

    ```sh
    saptune solution simulate HANA
    ```
    {: pre}

    For SAP NetWeaver:

    ```sh
    saptune solution simulate NETWEAVER
    ```
    {: pre}

1. Apply the `saptune` solution.

    On the virtual server instance for the SAP HANA system:

    ```sh
    saptune solution apply HANA
    ```
    {: pre}

    On the virtual server instance for the application server:

    ```sh
    saptune solution apply NETWEAVER
    ```
    {: pre}

1. Check the `saptune` status.

    ```sh
    saptune status
    ```
    {: pre}

1. Verify that `saptune` is set up correctly.

    ```sh
    saptune check
    ```
    {: pre}

### Configuring RHEL for SAP applications
{: #powervs-configure-RHEL-system-roles-sap}

[RHEL]{: tag-red}

RHEL System Roles for SAP are a collection of Ansible roles that help you configure a RHEL system for installing SAP HANA or SAP NetWeaver.
Red Hat distributes and maintains the Ansible roles for SAP configuration, so the tasks and required parameters can differ depending on the version of the `rhel-system-roles-sap` package.
The RHEL image that is provided by IBM includes the Ansible execution engine, SAP-related system roles, and the Ansible execution files.

The RHEL system roles for preparing the operating system settings for SAP HANA or SAP NetWeaver are available in directory `/root`.

Use the following command to prepare the virtual server instance for SAP HANA workload.

```sh
ansible-playbook -i /root/inventory /root/sap-hana.yml
```
{: pre}

Use the following command to prepare the virtual server instance for SAP NetWeaver workload.

```sh
ansible-playbook -i /root/inventory /root/sap-netweaver.yml
```
{: pre}

For more information about customizing the operating system, see the following documentation.
* [SAP Note 2772999 "Red Hat Enterprise Linux 8.x: Installation and Configuration"](https://me.sap.com/notes/2772999){: external}
* [SAP Note 2777782 "SAP HANA DB: Recommended OS Settings for RHEL8"](https://me.sap.com/notes/2777782){: external}
* [SAP Note 3108316 "Red Hat Enterprise Linux 9.x: Installation and Configuration"](https://me.sap.com/notes/3108316){: external}
* [SAP Note 3108302 "SAP HANA DB: Recommended OS Settings for RHEL9"](https://me.sap.com/notes/3108302){: external}
* [SAP Note 2382421 "Optimizing the Network Configuration on HANA- and OS-Level"](https://me.sap.com/notes/2382421){: external}
* [Red Hat Enterprise Linux System Roles for SAP](https://access.redhat.com/sites/default/files/attachments/rhel_system_roles_for_sap_1.pdf){: external}

### Configuring jumbo frames
{: #powervs-fnetwork-configurations-sles-rhel}

Enable jumbo frames by setting `MTU=9000` on both RHEL (`nmcli`) and SLES (`wicked`).

[RHEL]{: tag-red}

Identify the connection name for the network manager.
The example shows the commands for connection `System env2`.

- Verify the current MTU size for the connection

   ```sh
    nmcli con show "System env2" | grep -E "802-3-ethernet.mtu|ethtool.feature-tso"
    802-3-ethernet.mtu:                     1500
   ```
   {: screen}

- Enable jumbo frames by altering the MTU size to 9000. Activate TCP segmentation offload (TSO).

   ```sh
   nmcli connection modify "System env2" \
     802-3-ethernet.mtu 9000 \
     ethtool.feature-tso on
   ```
   {: screen}

- Verify the new settings for the connection

   ```sh
   nmcli con show "System env2" | grep -E "802-3-ethernet.mtu|ethtool.feature-tso"
   802-3-ethernet.mtu:                     9000
   ethtool.feature-tso:                    on
   ```
   {: screen}

[SLES]{: tag-green}

Identify the device name for the network adapter.
The example shows the commands for device name `env2`.

- Edit file `/etc/sysconfig/network/ifcfg-env2`, and check that it contains the parameters

   ```sh
   MTU='9000'
   ETHTOOL_OPTS='-K env2 tso on'
   ```
   {: screen}

- Afterward, restart the interface

   ```sh
   wicked ifdown env2 && wicked ifup env2
   ```
   {: screen}

### Checking the NUMA layout
{: #powervs-numa-layout}

Check that the CPU and memory placement is optimized for SAP HANA by running the `chk_numa_lpm.py` script.

The script runs the following checks:

- NUMA Layout Validation
   Verifies the nonuniform memory access (NUMA) configuration according to SAP HANA requirements.
   - Verifies that for each CPU core local memory is available.
   - Verifies that the memory distribution across the cores does not exceed a 50% variance.
   - Generates error messages if a core lacks memory, and warning messages if the memory distribution exceeds the margin.

- Live Partition Mobility (LPM) Check
   Detects whether an LPM operation occurred since the last system restart. An LPM operation can alter the NUMA layout from its boot-time configuration.
   - Searches system logs for the most recent LPM event.
   - Issues a warning if an LPM operation is detected.

Steps to run the Check

1. Review the details in [SAP Note 2923962](https://me.sap.com/notes/2923962){: external}.
1. Download the `chk_numa_lpm.py` script that is attached to the SAP Note and copy it to your {{site.data.keyword.powerSys_notm}} instance.
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
