---
copyright:
  years: 2026
lastupdated: "2026-09-16"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, capture and export, SAP workloads, SAP HANA, migration of hardware profile, relocate SAP workload on IBM {{site.data.keyword.powerSys_notm}}
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Resizing and migrating hardware profiles for IBM {{site.data.keyword.powerSys_notm}} instance in the same workspace
{: #sapmig-resizing-migrating-hardware-profiles}

IBM Cloud provides multiple ways to resize an {{site.data.keyword.powerSysFull}} instance in the same workspace.
Depending on the workload requirements, you can adjust CPU and memory resources, increase storage capacity, or migrate the workload to a new virtual server with a different hardware profile.
Each method has different operational effects, so you can choose the approach that fits your maintenance, capacity planning, and application requirements.
{: shortdesc}

The following options are available for resizing an IBM {{site.data.keyword.powerSys_notm}} instance in the same workspace:

- **Resize CPU and memory resources**: You can resize CPU and memory resources by changing the SAP hardware profile of an existing IBM {{site.data.keyword.powerSys_notm}} instance without relocating the workload.
   The ability to resize the SAP hardware profile depends on the available resources on the IBM Power Systems server.
   The storage volumes remain unchanged.
   This method does not help if you also need to resize the storage volumes, for example, to increase SAP volumes because of a growing SAP HANA database.
   Applications such as SAP HANA databases and SAP application servers can benefit from additional processor and memory resources.
   SAP application servers typically do not require storage volume resizing. In most cases, changing the SAP hardware profile is sufficient, and you do not need to create a new IBM {{site.data.keyword.powerSys_notm}} instance.
- **Migrate the workload to a new hardware profile**: You can relocate an SAP workload from one hardware profile to a new IBM {{site.data.keyword.powerSys_notm}} instance in the same workspace. The new hardware profile can be larger or smaller, and you can increase the storage volumes as needed.

   The **Capture and export** virtual server action creates an IBM boot image that includes the boot volume and all attached storage volumes.
   You can then deploy a new IBM {{site.data.keyword.powerSys_notm}} instance in the same workspace from the exported catalog image.

   When you deploy the new instance, you can do the following tasks:
   - Increase or decrease CPU and memory resources by selecting a different SAP hardware profile.
   - Resize storage volumes, including SAP HANA volumes, to user-defined capacities.
   - Keep the original IBM Power Virtual Server instance as a backup because it remains unchanged throughout the process.


Do not update the operating system.
Keep the host in its current healthy state.
Do not trigger more than one **Capture and export** process in the same workspace at the same time.
Wait until the new IBM {{site.data.keyword.powerSys_notm}} instance is fully created completely from the exported image.
{: note}

## Resizing without relocating an IBM {{site.data.keyword.powerSys_notm}} instance
{: #sapmig-profile-hana-resizing}

SAP profiles are defined sizes of CPU and memory.
Resizing requires enough resources on the same IBM Power System.
No changes to applications or the operating system are necessary.
In most cases, this approach is sufficient for an SAP application server because it typically uses a small hardware profile.

To change the SAP hardware profile of an IBM {{site.data.keyword.powerSys_notm}} instance without relocating the workload, complete the following steps:

1. Shut down the IBM {{site.data.keyword.powerSys_notm}} instance (You can change the SAP hardware profile only when the instance is shut down.)
1. In the IBM Cloud console, adjust the CPU and memory settings:
   1. List the resources
   1. Choose your workspace
   1. Choose the IBM {{site.data.keyword.powerSys_notm}} instance
   1. Click tab **Capacity** in the instance details
   1. Edit **Capacity**
   1. Choose another **SAP profile**
      (This action changes the values on CPU cores and memory.)
   1. Click **Save** to confirm the changes
1. Wait until the new values are applied
1. Restart the {{site.data.keyword.powerSys_notm}} instance

## Relocating an SAP workload to a new IBM {{site.data.keyword.powerSys_notm}} instance and resizing the hardware profile
{: #sapmig-profile-relocation-hana}

Relocate the SAP workload from one hardware profile on an IBM {{site.data.keyword.powerSys_notm}} instance to a new IBM {{site.data.keyword.powerSys_notm}} instance in the same workspace and resize the SAP hardware profile.
Then, resize the SAP storage volumes.

### Optional: Using the same IP address for the new IBM {{site.data.keyword.powerSys_notm}} instance
{: #sapmig-profile-using-same-IP}

IBM Cloud console prevents you from assigning existing IP addresses to a new deployment in the same workspace.
If you want to assign the same IP address,
add a new IP address to the original IBM {{site.data.keyword.powerSys_notm}} instance, and then remove the original IP address.

1. In the IBM Cloud console, attach an existing network as a second network device.
   Attach the same subnet. Assign a different IP address by using DHCP or a manual assignment.
   Only this method makes the IP address visible correctly in the IBM Cloud console.

1. Assign the IP address on OS level by using `nmcli` or configuring the network scripts.

1. Activate the IP address.

   ```sh
   systemctl restart NetworkManager
   ```
   {: pre}

1. Restart the host and verify SSH login to the second network interface.

1. Remove the first original interface in IBM cloud UI.
   Only the added second interface remains in the IBM Cloud console.

### Steps on the current SAP HANA host
{: #sapmig-profile-old-host}

Complete the following steps on the current SAP HANA host:

1. In the IBM Cloud console, list the resources and choose the SAP host. In the server details, select the **storage** tab.
   List and note the names and sizes of the SAP storage volumes.

   ```sh
   *hana-data-* (usually 4 volumes *1-4)
   *hana-log-*   (usually 4 volumes *1-4)
   *hana-shared*  (usually 1 volume *-1)
   *hana-usrsap* (usually 1 volume *-1)
   ```
   {: codeblock}

   The new host that you create has different volume names. Identify the SAP volumes by their storage volume sizes.

1. Log in to the SAP host by using SSH.

1. Check for the status of two packages,
   the packages `cloud-init` and `ibm-power-managed-rhel<8/9/10>` packages are required to make deployment of the cloned image cloud compatible.

   ```sh
   dnf install -y cloud-init ibm-power-managed-rhel<8/9/10>
   ```
   {: pre}

1. Display and note information about the logical volumes.
   Logical Volume Manager (LVM) aggregates several storage volumes into one logical volume.

   ```sh
   lvs
   ```
   {: pre}

   Example output:

   ```sh
   LV       VG       Attr       LSize    Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
   datalv   datavg   -wi-ao----  319.98g
   loglv    logvg    -wi-ao----  511.98g
   sharedlv sharedvg -wi-ao---- <200.00g
   usrsaplv usrsapvg -wi-ao----  <50.00g
   ```
   {: screen}

1. Note the size of the mounted volumes.

   ```sh
   df -h | egrep 'hana|sap'
   ```
   {: pre}

1. Show and note the SAP mounts in `/etc/fstab`.

   ```sh
   egrep 'sap|hana' /etc/fstab
   ```
   {: pre}

   Example output:

   ```sh
   /dev/mapper/datavg-datalv /hana/data xfs defaults,nofail 0 0
   /dev/mapper/logvg-loglv /hana/log xfs defaults,nofail 0 0
   /dev/mapper/sharedvg-sharedlv /hana/shared xfs defaults,nofail 0 0
   /dev/mapper/usrsapvg-usrsaplv /usr/sap xfs defaults,nofail 0 0
   ```
   {: screen}

1. Shut down the SAP host. Do not delete the host until the new SAP host is running.

### Capturing and exporting all storage volumes of the IBM {{site.data.keyword.powerSys_notm}} instance
{: #sapmig-profile-capture-and-export}

1. Shut down the IBM {{site.data.keyword.powerSys_notm}} instance before you run the **Capture and export** virtual machine (VM) action.

   Any changes to the data during the export process can compromise data integrity.
   {: note}

1. In the IBM cloud console, select the IBM {{site.data.keyword.powerSys_notm}} instance that hosts SAP HANA.
1. Click the tab **Storage**, and note all storage volumes and their sizes.
1. Create a mapping of the storage volumes to their corresponding mount points.

1. Click **VM actions**, and then select **Capture and export**.
1. Select all storage volumes to be included to the image.
1. Confirm the terms and conditions, and then click **Next**.
1. Select **Image catalog** as the image destination.
   The image is available as a boot image for deployments only in the same workspace.

### Creating an IBM {{site.data.keyword.powerSys_notm}} instance with the migrated SAP HANA DB
{: #sapmig-profile-hana-create-virtual-server}

To create the new IBM {{site.data.keyword.powerSys_notm}} instance,
follow the instructions in [Preparing {{site.data.keyword.powerSys_notm}} instances for SAP system installation](/docs/sap?topic=sap-powervs-set-up-power-instances), and use the following parameters:

1. Deploy the instance in the same IBM {{site.data.keyword.powerSys_notm}} workspace as the current instance
1. Use the boot image that you exported to the **image catalog**

   In the **Boot image** section, use the following options:
   - **Operating system**: Go to **IBM provided subscription/Linux for SAP (HANA)**
   - **Image**: Select the boot image name that you exported before
1. In the **Profile properties** section, use the following options:
   - Select the **Machine type**, the Power System server type might differ
   - Choose an SAP hardware **Profile** that fits your future needs
1. In the **Storage Volumes** section, do not modify the storage volumes because the image includes all previously attached volumes.
1. In the **Network interfaces** section, select the private network:
   - Click **Add existing network**
   - Click **Attach (an existing) subnet**
   - Select the existing private network that you want to use
   - Select a new IP manually or set to DHCP
1. Click **Create** to deploy the defined IBM {{site.data.keyword.powerSys_notm}} instance

### Resizing the storage volumes on the new SAP host
{: #sapmig-profile-hana-resize-storage-volumes}

To increase the size of the SAP volumes, proceed with these steps:

1. Log in to the newly created SAP host by using SSH.

1. In `/etc/fstab`, comment out the SAP volume lines. It prevents the mounting of the SAP volumes in the next restart.

1. Shut down the newly created SAP host.

   You can resize the storage volumes in the IBM Cloud console while the host is running.
   However, the Logical Volume Manager (LVM) volumes at the operating system level are recognized only after a restart.
   To avoid data corruption, do not resize physical storage volumes on a running host.
   {: note}

1. In the IBM Cloud console, select each SAP volume separately of the {{site.data.keyword.powerSys_notm}} instance, and then select **edit** on the corresponding three-dot option.

1. Increase each SAP volume to the new self-defined size.
   As the volume names have different volume names than the old host, recognize the volumes by their size.
   Optionally, modify storage tiers.
   It helps to rename the SAP volumes corresponding to their logical volume name:

   ```sh
   *hana-data-* (usually 4 volumes *1-4)
   *hana-log-*   (usually 4 volumes *1-4)
   *hana-shared*  (usually 1 volume *-1)
   *hana-usrsap* (usually 1 volume *-1)
   ```
   {: screen}

   The size of each logical volume is the combined size of the corresponding storage volumes.

1. Start the host after resizing the storage volumes.

### Resizing the LVM volumes on the new host
{: #sapmig-profile-hana-resize-LVM}

The attached store volumes must now be scanned by the operating system, added to the LVM and mounted to the file system.
For resizing procedure, see [Extending a logical volume and file system online in RHEL after disk resize](https://access.redhat.com/solutions/1265413){: external}.

To resize the volumes at the operating system level, complete the following steps:

1. Log in to the host by using SSH

1. Scan the SCSI bus to discover new devices

   ```sh
   /usr/bin/rescan-scsi-bus.sh -a -c
   ```
   {: pre}

1. List all physical volumes and their current recognized, but former size

   ```sh
   pvscan
   ```
   {: pre}

1. List the overview of the physical volumes (PV), volume group (VG), and the former size

   ```sh
   pvs
   ```
   {: pre}

1. Resize each of the LVM physical volumes (PV) that were listed with pvs

   ```sh
   pvresize <PV-name>
   ```
   {: pre}

   Example output:

   ```sh
   pvresize /dev/mapper/36005076813810264e80000000000a287

   Physical volume "/dev/mapper/36005076813810264e80000000000a287" changed
     1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```
   {: screen}

1. Check the new PV size with pvs
   ```sh
   pvs
   ```
   {: pre}

1. List the overview of the volume groups (VG) and the current size

   ```sh
   lvs
   ```
   {: pre}

1. Extend each Volume Group (VG) that was listed with lvs

   ```sh
   lvextend -l +100%FREE <VG-name>
   ```
   {: pre}

   Example output:
   ```sh
   lvextend -l +100%FREE datavg

   Using stripesize of last segment 64.00 KiB
   Size of logical volume datavg/datalv changed from 319.98 GiB (81916 extents) to <31.25 TiB (8191996 extents).
   Logical volume datavg/datalv successfully resized.
   ```
   {: screen}

1. Check the new VG-size with lvs

   ```sh
   lvs
   ```
   {: pre}

1. Activate the volume group

   ```sh
   vgchange -ay <VG-name>
   ```
   {: pre}

   Example output:
   ```sh
   vgchange -ay datavg
   ```
   {: screen}

1. Extend the physical volumes and logical volumes for all SAP volumes, and activate all logical volumes as described in the steps 4-10.

   Repeat the steps 4-10 for all SAP volumes
   {: note}

1. Uncomment all SAP file systems in `/etc/fstab`

1. Activate the changes on systemd

   ```sh
   systemctl daemon-reload
   ```
   {: pre}

1. Mount all file systems listed in `/etc/fstab`

   ```sh
   mount -a
   ```
   {: pre}

1. Resize the XFS file systems, retrieve the mount points from `/etc/fstab`

   ```sh
   xfs_growfs <mountpoint>
   ```
   {: pre}

   Example output:
   ```sh
   xfs_growfs /hana/data
   ```
   {: screen}

   Repeat this step for all LVM mount points
   {: note}

1. Check the mounted SAP file systems
   ```sh
   df -h | egrep 'hana|sap'
   ```
   {: pre}

### Starting the SAP services on the new host
{: #sapmig-profile-start-sap-services}

To start the SAP services on the new host, complete the following steps:

1. Adjust the file `/etc/hosts`.
   Add entries for all SAP hosts with the current IP addresses.

1. Start and check the SAP services.

   ```sh
   systemctl start SAP.slice
   ```
   {: pre}

   ```sh
   systemctl start SAP${SID}_${instance_number}.service
   ```
   {: pre}

1. Show all SAP processes.
   Verify that the following services are listed: `saphostagent.service` and `SAP${SID}_${instance_number}.service`.

   ```sh
   systemd-cgls --unit SAP.slice
   ```
   {: pre}

1. Start the SAP HANA database.

   ```sh
   sudo -i -u ${sid}adm -- HDB start
   ```
   {: pre}

1. Check all SAP HANA processes.

   ```sh
   sudo -i -u ${sid}adm -- HDB info
   ```
   {: pre}

1. Wait for the dispstatus status of the SAP instance to become `GREEN`.

   ```sh
   su - ${sid}adm -c "sapcontrol -nr ${instance_number} -function GetProcessList"
   ```
   {: pre}

### Checking the health status of the SAP HANA database
{: #sapmig-profile-sap-health-status}

Check that the SAP license is valid.
Verify SAP HANA data consistency by activating SAP HANA backup and checking the SAP HANA backup as described in [Checking Individual Backups (hdbbackupcheck)](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/0ab1ddd1d95e4b46ad32b7963e7b7e4d.html){: external}.

### Deleting or preserving the previous IBM {{site.data.keyword.powerSys_notm}} instance
{: #sapmig-profile-delete-old-power-VS}

You can preserve the original IBM {{site.data.keyword.powerSys_notm}} instance in its original state
or delete it after SAP HANA DB on the new host is confirmed to be healthy.
