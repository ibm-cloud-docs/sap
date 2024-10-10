---

copyright:
  years: 2020, 2024
lastupdated: "2024-10-10"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads

subcollection: sap

---

{{site.data.keyword.attribute-definition-list}}

# Deploying SAP {{site.data.keyword.powerSys_notm}}
{: #power-vs-set-up-power-instances}

When architecture for SAP that runs on {{site.data.keyword.powerSysFull}} is deployed, you get the following features.

* Separate private network for SAP system.
* A {{site.data.keyword.powerSys_notm}} instance for shared file systems.
* A {{site.data.keyword.powerSys_notm}} instance for SAP HANA.
* A {{site.data.keyword.powerSys_notm}} instance for SAP NetWeaver (on Linux).
* Operating system that is configured to use management services that are configured on {{site.data.keyword.vpc_full}} (SQUID proxy, NFS, NTP, DNS).
* Operating system that is registered to use IBM-provided OS subscription.
* Operating systems on {{site.data.keyword.powerSys_notm}} instances are prepared for SAP installation, which includes SAP-specific network performance tuning, file system setup, and OS tuning.

The following diagram shows deployed SAP on PowerVS infrastructure components.

![SAP on PowerVS infrastructure](images/step3_manual_SAP-on-PowerVS.svg){: caption="SAP on PowerVS infrastructure" caption-side="bottom"}

## Deploying a separate private network for SAP systems
{: #power-vs-set-up-power-sap-network}

For each SAP system that you create, a separate private network that is used to communicate between virtual server instances is needed. This network is tuned for maximum performance. To create separate private network for the SAP system, use the following steps.

1. In the workspace for {{site.data.keyword.powerSys_notm}}, create a private network for SAP system as described [here](/docs/power-iaas?topic=power-iaas-configuring-subnet).
2. Attach created network to both IBM Cloud Connections.

## Deploying optional {{site.data.keyword.powerSys_notm}} instance for SAP shared file systems
{: #power-vs-set-up-power-sharedfs-instance}

Each SAP system deployment based on SAP NetWeaver as application server contains file systems that are shared between multiple application server instances. It is a good practice to set up a separate virtual service instance for the shared file systems. One single {{site.data.keyword.powerSys_notm}} instance for shared file systems can be used by multiple SAP systems. Your security requirements determine how many {{site.data.keyword.powerSys_notm}} instances for shared file systems that you need.

To deploy {{site.data.keyword.powerSys_notm}} instance for shared file systems, go to your [workspace for {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-power-vs-set-up-power-infrastructure) and create {{site.data.keyword.powerSys_notm}} instance as described [here](/docs/power-iaas?topic=power-iaas-creating-power-virtual-server#configuring-instance). Specify following selections:

1. In the **General** section, make the following selections:
   | Field | Details |
   | ----- | ------- |
   | Instance name | Enter a unique name for the instance. |
   | Number of instances | Enter '1'. |
   | Server placement group (optional) | You can skip this selection if you don't use placement groups. |
   | VM pinning (optional) | Keeping this selection is recommended. |
   | SSH key | Select the key that you created previously. |
   {: caption="General shared file systems selections" caption-side="top"}

2. In the **Boot image** section, make the following selections:
   | Field | Details |
   | ----- | ------- |
   | Operating system | Select Linux for SAP (NetWeaver). |
   | Image | Select operating system and version. Make sure that you use the same operating system and version for all deployments. |
   | Tier | Select the tier that best fits your needs. |
   | Storage pool variations | Select the variation that best fits your needs. |
   {: caption="Shared file systems boot image selections" caption-side="top"}

3. In the **Profile** section, make the following selections:
   | Field | Details |
   | ----- | ------- |
   | Machine type | Select the type that best fits your needs. |
   | Core type | Select the core type that best fits your needs. |
   | Cores and Memory (GiB) | Select based on your performance requirements. |
   {: caption="Share file system profile selections" caption-side="top"}

4. In the **Storage volumes** section, make the following selection:
   * Attach one or more storage volumes to make sure that you have enough capacity for your shared file system.
5. In the **Networking** section, make the following selections:
   * Keep 'Public networks' deactivated

   * Attach both your private networks (management and backup). Make sure that you specify the IP addresses as entered in the DNS configuration for the corresponding hostnames. If IP addresses are assigned dynamically, you need to adapt the DNS entries for the system hostnames.


It takes some time until the {{site.data.keyword.powerSys_notm}} instance for shared file systems becomes available. The deployment is finished when you can log in to the instance over the VPC access host by using the SSH command:

```bash
>ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand="ssh -W %h:%p root@\<access_host_floating_ip\>" root@\<shared_fs_pvs_mgmt_ip\>*'
```
{: pre}

 Where `shared_fs_pvs_mgmt_ip` is the virtual server instance IP address in the management subnet.

## Deploying an SAP NetWeaver {{site.data.keyword.powerSys_notm}} on Linux
{: #power-vs-set-up-power-netweaver-instance}

To deploy {{site.data.keyword.powerSys_notm}} instance for SAP NetWeaver, go to your [workspace for {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-power-vs-set-up-power-infrastructure) and create {{site.data.keyword.powerSys_notm}} instance as described [here](/docs/power-iaas?topic=power-iaas-creating-power-virtual-server#configuring-instance). Specify the following selections:

1. In the **General** section, make the following selections:
   | Field | Details |
   | ----- | ------- |
   | Instance name | Enter a unique name for the instance. |
   | Number of instances | Enter '1'. |
   | Server placement group (optional) | You can skip this selection if you don't use placement groups. |
   | VM pinning (optional) | Keeping this selection is recommended. |
   | SSH key | Select the key that you created previously. |
   {: caption="SAP NetWeaver Linux general selections" caption-side="top"}

2. In the **Boot image** section, make the following selections:
   | Field | Details |
   | ----- | ------- |
   | Operating system | Select 'Linux for SAP (NetWeaver)'. |
   | Image | Select operating system and version. Make sure that you use the same operating system and version for all deployments. |
   | Tier | Select the tier that best fits your needs. |
   | Storage pool variations | Select the variation that best fits your needs. |
   {: caption="Shared file systems boot image selections" caption-side="top"}

3. In the **Profile** section, make the following selections:
   | Field | Details |
   | ----- | ------- |
   | Machine type | Select the type that best fits your needs. |
   | Core type | Select the core type that best fits your needs. |
   | Cores and Memory (GiB) | Select based on your performance requirements. For more information, see [Power virtual server profiles](/docs/sap?topic=sap-nw-iaas-offerings-profiles-power-vs). |
   {: caption="Shared file systems profile selections" caption-side="top"}

4. In the **Storage volumes** section, make the following selection:
    * Attach one or more volumes to make sure that you have enough capacity for the file system for SAP NetWeaver (/usr/sap)
5. In the **Networking** section, make the following selections:
    * Keep 'Public networks' deactivated
    * Attach both private networks (management and backup) and any separate private networks for the SAP system. Enter the IP addresses as entered in the DNS configuration for the corresponding hostnames. If the IP addresses are assigned dynamically, you need to adapt the DNS entries for the hostnames of this system.

It takes some time until the {{site.data.keyword.powerSys_notm}} instance for SAP NetWeaver becomes available. The deployment is finished when you can log in to the instance over the VPC access host by using the SSH command:

```bash
ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand="ssh -W %h:%p root@\<access_host_floating_ip\>" root@\<netweaver_pvs_mgmt_ip\>*'
```
{: pre}

Where `netweaver_pvs_mgmt_ip` is the virtual server instance IP address in the management subnet.

## Deploying an SAP HANA {{site.data.keyword.powerSys_notm}}
{: #power-vs-set-up-power-hana-instance}

To deploy {{site.data.keyword.powerSys_notm}} instance for SAP HANA, go to your [workspace for {{site.data.keyword.powerSys_notm}}](/docs/sap?1).

1. In the **General** section, make the following selections:
   | Field | Details |
   | ----- | ------- |
   | Instance name | Enter a unique name for the instance. |
   | Number of instances | Enter '1'. |
   | Server placement group (optional) | You can skip this selection if you don't use placement groups. |
   | VM pinning (optional) | "Soft" is the recommended selection. |
   | SSH key | Select the key that you created previously. |
   {: caption="SAP HANA general selections" caption-side="top"}

2. In the **Boot image** section, make the following selections:
   | Field | Details |
   | ----- | ------- |
   | Operating system | Select 'Linux for SAP (NetWeaver)'. |
   | Image | Select operating system and version. Make sure that you use the same operating system and version for all deployments. |
   | Tier | Select the tier that best fits your needs. |
   | Storage pool | Select the tier that best fits your needs. |
   {: caption="SAP HANA boot image selections" caption-side="top"}
  
3. In the **Profile** section, make the following selection:
    * Select a profile that fits your needs. For more information, see [SAP HANA profiles](/docs/sap?topic=sap-hana-iaas-offerings-profiles-power-vs).
4. In the **Storage volumes** section, make the following selection:
    * For SAP HANA the attached volumes are on different storage tiers 'Tier 1' and 'Tier 3'. You can't mix storage tiers in the instance creation process, so you need to attach storage volumes later. Keep this list empty.
5. In the **Networking** section, make the following selections:
    * Keep **Public networks** deactivated.
    * Attach both private networks (management and backup) and any separate private networks. Enter the IP addresses as entered in the DNS configuration for the corresponding host names. If the IP addresses are assigned dynamically, you need to adapt the DNS entries for the host names of this system.

It takes some time until the {{site.data.keyword.powerSys_notm}} instance for SAP HANA becomes available. The deployment is finished when you can log in
to the instance over the VPC access host by using the SSH command:

```bash
ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand="ssh -W %h:%p root@\<access_host_floating_ip\>" root@\<hana_pvs_mgmt_ip\>*'
```
{: pre}

 where `hana_pvs_mgmt_ip` the virtual server instance IP address is in the management subnet.

## Creating an SAP HANA {{site.data.keyword.powerSys_notm}} storage volume
{: #power-vs-set-up-power-hana-volumes}

Modify the created {{site.data.keyword.powerSys_notm}} instance for SAP HANA and attach extra storage volumes as described in [Modifying volume network](/docs/power-iaas?topic=power-iaas-modifying-server#modifying-volume-network).
Attach the following volumes:

* A storage volume for SAP HANA shared file system. Volume size must be MIN (1 x RAM; 1 TB). Storage tier "Tier 3" is sufficient. 'Shareable' switch can stay 'off'.
* Four or eight storage volumes of the same size for SAP HANA log file system. File system size must be MIN (1/2xRAM; 512 GB). Divide file system size through Four or eight to determine the size for each storage volume. Select "Tier 1" as the storage tier. 'Shareable' switch can stay 'off'.
* Four or eight storage volumes of the same size for SAP HANA data file system. File system size must be at minimum equal to the amount of RAM. Divide file system size through Four or eight to determine the size for each storage volume. Select "Tier 1" as the storage tier. 'Shareable' switch can stay 'off'.
* An extra storage volume of chosen size for extra data (such as '/usr/sap' file system). Storage tier "Tier 3" is sufficient. 'Shareable' switch can stay 'off'.
* You can attach extra volumes for backup or export.

## Configuring a {{site.data.keyword.powerSys_notm}} instance 
{: #power-vs-set-up-power-basic-os-config}

Complete following configurations on your {{site.data.keyword.powerSys_notm}} instances.

### Configuring a proxy endpoint
{: #power-vs-configure-proxy}

To provide proxy and cache services for HTTP, FTP, and other popular network protocols, you need to export a proxy server endpoint. Run the following commands to export a proxy endpoint.

```bash
export http_proxy=http://\<SQUID_PROXY_SERVER\>:3128
export https_proxy= http://\<SQUID_PROXY_SERVER\>:3128
export HTTP_proxy= http://\<SQUID_PROXY_SERVER\>:3128
export HTTPS_proxy =http://\<SQUID_PROXY_SERVER\>:3128
```
{: pre}

To keep these exported variables persistent across multiple sessions, `/etc/bash.bashrc` (on SLES) or `/etc/bashrc` (on RHEL) files must be updated with these entries. These files help make sure that new sessions use these exported variables as environment variables.

### Configuring the proxy by using ansible automation`
{: #power-vs-configure-proxy-ansible}

To configure the proxy by using ansible automation, download the `ibm.power_linux_sap` ansible-galaxy collection.

```yaml
ansible-galaxy collection install ibm.power_linux_sap
```
{: pre}

After the ansible collection is installed, you need to configure the proxy by updating the variable file `playbook/vars/sample_client_services_variable_file.yml`.

```yaml
client_config: {
  proxy: { enable: true, squid_server_ip_port: \"SQUID_PROXY_SERVER\", no_proxy_hosts: '161.0.0.0/8' }
}
```
{: pre}

After you update the file, run the following ansible-playbook command.

```yaml
ansible-playbook \--connection=local -i \"localhost,\" powervs-services.yml -e \@vars/sample_client_services_variable_file.yml
```
{: pre}

This ansible execution helps make sure that the proxy client service is configured.

### Registering the operating system
{: #power-vs-register-os}

To register the operating system with IBM Full Linux Subscription, follow the steps in [Set full Linux](/docs/power-iaas?topic=power-iaas-set-full-Linux#virtual-server).

After registration, perform a system update

On SUSE:

```bash
zypper update
```
{: pre}

On RHEL:

```yaml
yum -y update
```
{: pre}

### Registering the operating system by using ansible automation
{: #power-vs-register-os-ansible}

To register the operating system by using ansible automation, download the `ibm.power_linux_sap` ansible-galaxy collection.

```yaml
ansible-galaxy collection install ibm.power_linux_sap
```
{: pre}

After the ansible collection installs, register the operating system by running the following ansible-playbook command.

For RHEL:
```yaml
ansible-playbook \--connection=local -i \"localhost,\" powervs-rhel.yml
```
{: pre}

For SLES:
```yaml
ansible-playbook \--connection=local -i \"localhost,\" powervs-sles.yml
```
{: pre}

This ansible execution helps make sure that the operating system is registered on {{site.data.keyword.powerSys_notm}} instance. For more information, see [Set full Linux](/docs/power-iaas?topic=power-iaas-set-full-Linux#virtual-server).

### Manually configuring the NTP client
{: #power-vs-configure-ntp-client-manually}

If NTP server is already configured, you need to install the chrony package by using the following command.

On SLES:

```yaml
zypper install -y chrony
```
{: pre}

On RHEL:

```yaml
yum install -y chrony
```
{: pre}

After the package is installed, the file `/etc/chrony.conf` is available. You need to update this file with the NTP server details. Comment out `! pool pool.ntp.org iburst` and add `server \<NTP_SERVER_IP\> iburst`. Then, start the chrony service with `systemctl start chronyd`. To check the status of chrony service, run `systemctl status chronyd`.

### Configuring NTP client by using ansible automation
{: #power-vs-configure-ntp-client-ansible}

Configuring an NTP client by using ansible automation, download the `ibm.power_linux_sap` ansible-galaxy collection.

```yaml
ansible-galaxy collection install ibm.power_linux_sap
```
{: pre}

After the ansible collection is installed, configure NTP by updating the variable file `playbook/vars/sample_client_services_variable_file.yml`.

```yaml
client_config: {
  ntp: { enable: true, ntp_server_ip: \"NTP_SERVER_IP\" }
}
```
{: pre}

After the file is updated, run the following ansible-playbook command.

```yaml
ansible-playbook \--connection=local -i \"localhost,\" powervs-services.yml -e \@vars/sample_client_services_variable_file.yml
```
{: pre}

This ansible execution helps make sure that the NTP service is configured.

### Manually configuring the DNS client
{: #power-vs-configure-dns-client-manual}

To configure DNS server, you need to create the `/etc/resolv.conf` file, and enter the DNS server detail as `nameserver \<DNS_SERVER_NAME_or_IP\>`

### Configuring the DNS client by using ansible automation
{: #power-vs-configure-dns-client-ansible}

1. To enable DNS service through ansible automation, download the `ibm.power_linux_sap` ansible-galaxy collection.

    ```yaml
    ansible-galaxy collection install ibm.power_linux_sap*
    ```
    {: pre}

2. After the ansible collection is installed, configure DNS by updating the variable file `playbook/vars/sample_client_services_variable_file.yml`.

    ```yaml
    client_config: {
      dns: { enable: true, dns_server_ip: \"DNS_SERVER_IP\" }
    }
    ```
    {: pre}

3. After the file is updated, run the following ansible-playbook command.

    ```yaml
    ansible-playbook \--connection=local -i \"localhost,\" powervs-services.yml -e \@vars/sample_client_services_variable_file.yml
    ```
    {: pre}

This ansible execution helps make sure that the DNS service is configured.

### Manually configuring an NFS client
{: #power-vs-configure-nfs-client-manual}

Use to following steps to manually create an NFS client.

1. Export shared NFS directories. To make these exported NFS directories available, mount them by using NFS protocol. Then, install the `nfs-client` package and enable the NFS client by using the systemctl command.

   * On SLES:

   ```yaml
   zypper install -y nfs-utils
   ```
   {: pre}

   * On RHEL:

   ```yaml
   yum install -y nfs-utils
   ```
{: pre}

2. To enable NFS client service, use the following command.

   ```yaml
   systemctl start nfs-client
   ```
   {: pre}

3. After the NFS client service starts, you can mount the shared NFS directory by using the mount command.

   ```yaml
   mount \<NFS_SERVER\>:\<REMOTE_NFS_DIRECTORY\> \<LOCAL_MOUNT_DIRICTORY\>
   ```
   {: pre}

### Configuring an NFS client by using ansible automation
{: #power-vs-configure-nfs-client-ansible}

1. This NFS enablement and mounting of shared NFS directories can also be performed by using ansible automation. Download the `ibm.power_linux_sap` ansible-galaxy collection.

   ```yaml
   ansible-galaxy collection install ibm.power_linux_sap
   ```
   {: pre}

2. After the ansible collection is installed, configure DNS by updating the variable file `playbook/vars/sample_client_services_variable_file.yml`.

   ```yaml
   client_config: {
     nfs: { enable: true, nfs_server_path: \"172.23.0.12:/NFS;172.23.0.12:/hana/software\", nfs_client_path: \"/mnt;/hana\" }
   }
   ```
   {: pre}

3. After the file is updated, run the following ansible-playbook command.

   ```yaml
   ansible-playbook \--connection=local -i \"localhost,\" powervs-services.yml -e \@vars/sample_client_services_variable_file.yml
   ```
   {: pre}

This ansible execution helps make sure that the NFS service is configured and that the proper directories are mounted. To check a mounted directory, run the mount command.

### Manually creating file systems
{: #power-vs-create-file-systems-manual}

For shared SAP file systems, you need to create a file system to store SAP data and distribute them to all SAP instances. You can use extra file systems for other purposes.

For an SAP NetWeaver instance, you need to create a file system to store instance-specific data. You can use extra file systems for other purposes.

To install SAP HANA, three file systems are created: data, log, and shared. According to the default installation catalog, these file systems are `/hana/data`, `/hana/log` and `/hana/shared`, but you can customize these file system names. You might also need file systems for other purposes (`/usr/sap` directory). `/hana/data` and `/hana/log` file systems are striped across four or eight disks according to the number of volumes that you created. `/hana/shared` and all other file systems are nonstripped 1-disk file systems.

To create a file system, use the following steps. As example, `/hana/data` is used. The same procedure is repeated for `/hana/log` and `/hana/shared` and all other file systems.

1. Scan for the new storage that was provisioned. Run the following command for storage and disk discovery. Newly discovered disks are listed at the end of the report.

```bash
/usr/bin/rescan-scsi-bus.sh -a -c -v
```
{: pre}

It is easier to check the system when the physical disks have different sizes. If the volumes are the same size, you can use the worldwide name (WWN) of the volume that is shown in the IBM Cloud® UI. The WWN corresponds to the ID in the output of the `multipath -ll` command.
{: tip}

For example, a storage volume with the name `sapdata_vol` has a WWN `3600507681081814CE80000000000054D`. In the operating system output of the `multipath -ll` command, the corresponding device name for this WWN is `dm-4`. The device name is needed to create the logical volume and the volume group.

   ```bash
   multipath -ll
   3600507681081814ce80000000000054d dm-4 IBM,2145
   size=100G features=\'0\' hwhandler=\'1 alua\' wp=rwv
   ```
   {: pre}

The WWN in the IBM Cloud® UI contains uppercase letters. In the operating system, the same ID contains lowercase letters.
{: note}

2. Use the following to identify disks by using disk sizes. The four storage volumes for SAP HANA data are 60 GB each. Multipath aliases are used. Run the following command to create file system for `/hana/data`.

```bash
export pv_size=60G
export lv_name=hana_data_lv
export vg_name=hana_data_vg
export mount=/hana/data
# use following command if you USE multipath aliases 
devices=$(multipath -ll | grep -B 1 $pv_size | grep dm- | awk '{print "/dev/"$3}' | tr '\n' ' ')
stripes=$(multipath -ll | grep -B 1 $pv_size | grep dm- | awk '{print "/dev/"$2}' | wc | awk '{print $1}')
pvcreate $devices
vgcreate ${vg_name} ${devices}
lvcreate -i${stripes} -I64 -l100%VG -n ${lv_name} ${vg_name}
mkfs.xfs /dev/mapper/${vg_name}-${lv_name}
mkdir -p ${mount}
```
{: pre}

3. The four storage volumes for SAP HANA log are 110 GB each. Multipath aliases are used. Run the following command to create filesystem for `/hana/log`.

```bash
export pv_size=110G
export lv_name=hana_log_lv
export vg_name=hana_log_vg
export mount=/hana/log
# use following command if you USE multipath aliases 
devices=$(multipath -ll | grep -B 1 $pv_size | grep dm- | awk '{print "/dev/"$3}' | tr '\n' ' ')
stripes=$(multipath -ll | grep -B 1 $pv_size | grep dm- | awk '{print "/dev/"$2}' | wc | awk '{print $1}')
pvcreate $devices
vgcreate ${vg_name} ${devices}
lvcreate -i${stripes} -I64 -l100%VG -n ${lv_name} ${vg_name}
mkfs.xfs /dev/mapper/${vg_name}-${lv_name}
mkdir -p ${mount}
```
{: pre}

4. One storage volume for SAP HANA shared is 400 GB. Multipath aliases are used. Run the following command to create file system for `/hana/shared`.

```bash
export pv_size=400G
export lv_name=hana_shared_lv
export vg_name=hana_shared_vg
export mount=/hana/shared
# use following command if you USE multipath aliases 
devices=$(multipath -ll | grep -B 1 $pv_size | grep dm- | awk '{print "/dev/"$3}' | tr '\n' ' ')
stripes=$(multipath -ll | grep -B 1 $pv_size | grep dm- | awk '{print "/dev/"$2}' | wc | awk '{print $1}')
pvcreate $devices
vgcreate ${vg_name} ${devices}
lvcreate -i${stripes} -I64 -l100%VG -n ${lv_name} ${vg_name}
mkfs.xfs /dev/mapper/${vg_name}-${lv_name}
mkdir -p ${mount}
```
{: pre}

If you don't use multipath aliases, replace the line (starting with `devices=$(`) that is used to identify devices. Instead, use following line: `devices=$(multipath -ll | grep -B 1 $pv_size | grep dm- | awk '{print "/dev/"$2}' | tr '\n' ' ')`

### Creating file systems by using ansible automation
{: #power-vs-create-files-system-ansible}

1. To create file systems by using ansible automation, download the `ibm.power_linux_sap` ansible-galaxy collection.

```yaml
ansible-galaxy collection install ibm.power_linux_sap
```
{: pre}

The ansible role, `powervs_fs_creation` is used to create filesystem for SAP HANA, SAP NetWeaver or for SAP shared file systems instance. This role performs the following tasks:

* Creates file systems with user-defined stripe size by using ansible built-in LVM logical volumes modules.
* Mounts the file systems on provided mount points
* Adds an entry to `/etc/fstab` for automount on reboot.

2. To run this role, you can pass two types of variables. The first as a dictionary and the other as a list. See the following example.

   * Example A. The following example is the data structure for `disks_configuration` as the dictionary value:

        ```yaml
        disks_configuration:
        {
          counts: [2,2,1],
          names: [data,log,shared],
          paths: [/hana/data,/hana/log,/hana/shared],
          wwns: [600507681082018bc8000000000057e4,600507681082018bc8000000000057e8,600507681082018bc8000000000057e5,600507681082018bc8000000000057e6, 600507681082018bc8000000000057e7]
        }
        ```
        {: pre}

    * Example B. Data structure for `disks_configuration` as list value example:

        ```yaml
        disks_configuration: [
        {
          name: data,
          path: /hana/data,
          wwns: 600507681082018bc8000000000057e4,600507681082018bc8000000000057e8
        },
        {
          name: log,
          path: /hana/log,
          wwns: 600507681082018bc8000000000057d9,600507681082018bc8000000000057ed7
        },
        {
          name: shared,
          path: /hana/shared,
          wwns: 600507681082018bc8000000000057f1
        }]
        ```
        {: pre}

WWNs associated with disks that are passed as input parameters for running the ansible role.

3. To run this ansible playbook, run the following command after you update one of the variable examples and store them in the`filesystem_creation_variables` file.

    * For Red Hat

        ```yaml
        ansible-playbook \--connection=local -i \"localhost,\" powervs-rhel.yml -e @ filesystem_creation_variables
        ```
        {: pre}

    * For SLES

        ```yaml
        ansible-playbook \--connection=local -i \"localhost,\" powervs-sles.yml -e @ filesystem_creation_variables
        ```
        {: pre}

After successful execution, file systems that are required for HANA installations are available.

### Preparing for SAP installation
{: #power-vs-prepare-sap-installation}

Preparation for SAP installation is needed for SAP HANA and SAP NetWeaver and not required on {{site.data.keyword.powerSys_notm}} instance for SAP shared file systems.

#### Required additional Software Packages for SLES 15
{: #power-vs-addtl-sw-sles-sap}

Please install the `insserv` package before the SAP HANA installation:

```bash
zypper install insserv
```
{: pre}

#### Using saptune on SLES for SAP
{: #power-vs-using-saptune-sles-sap}

Use the saptune tool to apply recommended operating system settings for SAP HANA or SAP NetWeaver on SUSE Linux® Enterprise Server. On IBM Power Systems Virtual Servers, the same SUSE Linux® Enterprise Server image is used for SAP NetWeaver and SAP HANA.

The following workflow shows how you can use the saptune tool to apply the SAP solution to your server. For more information about saptune, see [SAP Note 1275776 - Linux: Preparing SLES for SAP
environments](https://launchpad.support.sap.com/#/notes/1275776).

1. Verify that the package status is current.

    ```bash
    zypper info saptune
    ```
    {: pre}

2. Verify that the saptune version is at least 3.

    ```bash
    saptune version
    ```
    {: pre}

3. List all available solutions. Numbered entries represent integrated SAP Notes for each of the solutions.

    ```bash
    saptune solution list
    ```
    {: pre}

4. Get an overview of saptune options.

    ```bash
    saptune --help
    ```
    {: pre}

5. Enable and start the saptune.service. This command also disables sapconf and tunes, which isn't used since saptune version 3.

    ```bash
    saptune service takeover
    ```
    {: pre}

6. Simulate the changes before you apply them (optional).

    For SAP HANA:

    ```bash
    saptune solution simulate HANA
    ```
    {: pre}

    For SAP NetWeaver:

    ```bash
    saptune solution simulate NETWEAVER
    ```
    {: pre}

7. Apply the saptune solution.

    For SAP HANA:

    ```bash
    saptune solution apply HANA
    ```
    {: pre}

    For SAP NetWeaver:

    ```bash
    saptune solution apply NETWEAVER
    ```
    {: pre}

8. Check the status of saptune with the following command.

    ```bash
    saptune status
    ```
    {: pre}

#### Configurig Red Hat Enterprise Linux with RHEL System Roles for SAP
{: #power-vs-configure-RHEL-system-roles-sap}

RHEL System Roles for SAP are a collection of roles that are run by ansible to help you configure a RHEL system for the installation of SAP HANA or SAP NetWeaver. Ansible files for SAP configuration are distributed and updated directly by Red Hat, so the executed tasks and required parameters might vary depending on the version of `rhel-system-roles-sap` package. The RHEL image that is provided by IBM includes the ansible execution engine, SAP-related system roles, and the ansible execution files. If you have the most recent updates, you might get updated SAP-related system roles.

Beginning with `rhel-system-roles-sap-3.2.0-1.el8_4`, the role names changed. And files `/root/sap-preconfigure.yml`, `/root/sap-netweaver.yml` and `/root/sap-hana.yml` in the OS images with RHEL 8.1 and RHEL 8.4 must be adapted. For more information, see [following Red Hat article](https://access.redhat.com/articles/4488731).
{: note}

|Previous role name         |New role name             |
|---------------------------|--------------------------|
|sap-preconfigure           |sap_general_preconfigure  | 
|sap-netweaver-preconfigure |sap_netweaver_preconfigure|
|sap-hana-preconfigure      |sap_hana_preconfigure     |
{: caption="RHEL System Roles" caption-side="bottom"}

The execution files are in the root directory.

```yaml
ls -ltr /root/
total 28
-rw\-\-\-\-\-\--. 1 root root 6777 Oct 29 2019 original-ks.cfg
-rw\-\-\-\-\-\--. 1 root root 7030 Oct 29 2019 anaconda-ks.cfg
-rw-r\--r\--. 1 root root 104 Jan 30 03:38 sap-netweaver.yml
-rw-r\--r\--. 1 root root 99 Jan 30 03:38 sap-hana.yml
-rw-r\--r\--. 1 root root 71 Jan 30 03:38 sap-preconfigure.yml
```
{: pre}

Use the following command to tune the operating system for the SAP HANA workload.


* For RHEL 8.4 and previous versions, use this command:

```yaml
ansible-playbook /root/sap-hana.yml
```
{: pre}

* For RHEL versions RHEL 8.6 and greater, use this command:

```yaml
ansible-playbook -i /root/inventory /root/sap-hana.yml 
```
{: pre}

Use the following command to tune the operating system for the SAP NetWeaver workload.

* For RHEL 8.4 and previous versions, use this command:

```yaml
ansible-playbook /root/sap-netweaver.yml
```
{: pre}

* For RHEL versions RHEL 8.6 and greater, use this command:

```yaml
ansible-playbook -i /root/inventory /root/sap-netweaver.yml
```
{: pre}


For more information about running tasks, see the following documentation.
* [SAP Note 2772999 "Red Hat Enterprise Linux 8.x: Installation and Configuration"](https://launchpad.support.sap.com/#/notes/2772999)
* [SAP Note 2777782 "SAP HANA DB: Recommended OS Settings for RHEL8"](https://launchpad.support.sap.com/#/notes/2777782)
* [SAP Note 2382421 "Optimizing the Network Configuration on HANA- and OS-Level"](https://launchpad.support.sap.com/#/notes/2382421)
* [Red Hat Enterprise Linux System Roles for SAP](https://access.redhat.com/sites/default/files/attachments/rhel_system_roles_for_sap_1.pdf)

#### Further Network Configurations on SLES
{: #power-vs-network-configurations-sles}

You must set jumbo frames(`MTU='9000'`) and enable TSO on private networks that are used for communication between multiple instances in the SAP three-tier system.

```bash
cd /etc/sysconfig/network
vi ifcfg-envX # choose correct name of tje ethernet device that is used for communication between SAP instances (instead of envX)

BROADCAST=''
ETHTOOL_OPTS='-K ethX tso on ' # choose correct name of ethernet device used for communication between SAP instances (instead of ethX)
BOOTPROTO='static'
IPADDR='xx.xx.xx.xx/xx'
NAME='Virtual Ethernet card 0' NETWORK=''
REMOTE_IPADDR=''
STARTMODE='auto'
USERCONTROL='no'
MTU='9000'
```
{: pre}

To activate the changes, restart your network (`ifdown ethX; ifup ethX`), or set the MTU for the current configuration by using `ip link set dev <ethX> mtu 9000`.

#### Further Network Configurations on RHEL
{: #power-vs-network-configurations-rhel}

You must set jumbo frames(`MTU='9000'`) and enable TSO on private networks that are used for communication between multiple instances in the SAP three-tier system.

```bash
cd /etc/sysconfig/network-scripts
vi ifcfg-envX # choose correct name of the ethernet device that is used for communication between SAP instances (instead of envX)

BROADCAST=''
ETHTOOL_OPTS='-K envX tso on ' # choose correct name of ethernet device used for communication between SAP instances (instead of envX)
config fileBOOTPROTO='static'
IPADDR='xx.xx.xx.xx/xx'
NAME='Virtual Ethernet card 0' NETWORK=''
REMOTE_IPADDR=''
STARTMODE='auto'
USERCONTROL='no'
MTU='9000'
```
{: pre}

To activate the changes, restart your network (`ifdown envX; nmcli con down 'System envX'; nmcli con up 'System envX'`). Or, set the MTU for
the current configuration (with `ip link set dev <envX> mtu 9000` and `ethtool -K <envX> tso on`).

#### NUMA layout
{: #power-vs-numa-layout}

Check that the balance of CPU and memory placement is optimized for SAP HANA by running the `chk_numa_lpm.py` script. The `chk_numa_lpm.py` script does the following actions.

* Checks the nonuniform memory access (NUMA) layout according to SAP HANA rules. The script verifies that no cores without memory exist and that the memory distribution among the cores doesn't exceed a 50% margin. In the first case, the script generates an error; in the latter case, the script generates a warning.
* Checks whether a Live Partition Mobility (LPM) operation occurred. After LPM, the NUMA layout might differ from the configuration at boot time. The script scans the system log for the last LPM. A warning is generated if an LPM operation occurred since the last system boot.

1. Download the `chk_numa_lpm.py` script from the [SAP Note 2923962](https://launchpad.support.sap.com/#/notes/2923962). Then, run the `chk_numa_lpm.py` script on your newly provisioned {{site.data.keyword.powerSys_notm}} instance.
2. Run the script.

    ```python
    ./chk_numa_lpm.py
    WARNING: LPM might have occurred

    ls
    chk_numa_lpm.log chk_numa_lpm.py

    cat chk_numa_lpm.log
    ### Check run on : 2020-09-10 09:37:21 #####
    Hostname : ibmdmhan01
    Partition UUID : 2e2fb3e5-ef18-48c6-819a-bd85bfefa953
    WARNING: A possible Live Partition Migration (LPM) might have happened
    after boot!
    Date of latest started LPM : 2020-07-16 06:08:38
    Date of latest boot : 2020-07-02 10:01:00
    Numa Node : 0 Number of virt. CPUs : 32 Amount of memory : 255667 MB
    Numa Node : 1 Number of virt. CPUs : 32 Amount of memory : 255738 MB
    ```
    {: pre}

In this example, a warning is generated because two NUMA nodes with an equal amount of CPU and memory were created. For more information, see [SAP Note 2923962](https://launchpad.support.sap.com/#/notes/2923962).

## Next Steps
{: #power-vs-set-up-power-next_steps}

After the previous steps are completed, your infrastructure is ready to install the SAP software. Complete the following steps to install the SAP software.

1. Install a Windows server in management or in workload VPCs and use it for SAP administration.
2. Move SAP installation binary files to the NFS server in the virtual server instance that is running in workload VPC.
3. Log in through SSH and perform SAP installation by using the CLI on {{site.data.keyword.powerSys_notm}} instances.
4. Log in with Windows server and perform SAP installation by using the SAP GUI on {{site.data.keyword.powerSys_notm}} instances.
5. Create extra instances and extend the environment.
6. Create more instances on edge VPC and install internet-facing SAP applications such as SAP routed or SAP Web Dispatcher.
7. Install IBM Spectrum Protect as a backup solution in workload VPC and configure IBM Spectrum Protect client on {{site.data.keyword.powerSys_notm}} instances.
8. Configure VPN in management VPC and deactivate floating IP address on the access host.
