---
copyright:
  years: 2026
lastupdated: "2026-08-12"
keywords:
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Deploying SAP NetWeaver 7.5 with ASE
{: #deploy-sap-ase}

The deployment for SAP NetWeaver 7.5 with ASE Sybase on Red Hat&reg; Enterprise Linux&reg; (RHEL) 9.4 is performed in phases:

1. [Cloud infrastructure planning](/docs/sap?topic=sap-deploy-sap-ase#cloud-infra-plan-ase)
2. [Cloud resource deployment](/docs/sap?topic=sap-deploy-sap-ase#cloud-resource-deploy-ase)
3. [File system preparation](/docs/sap?topic=sap-deploy-sap-ase#file-system-prep-ase)
4. [Operating system preparation](/docs/sap?topic=sap-deploy-sap-ase#os-prep-ase)
5. [Installation of SAP NetWeaver 7.5 with ASE with Software Provisioning Manager (SWPM)](/docs/sap?topic=sap-deploy-sap-ase#install-sapnw-ase)

## Cloud infrastructure planning
{: #cloud-infra-plan-ase}

Before performing any deployment, it is important to plan the cloud infrastructure requirements and can ensure that all the required components are deployed on cloud VPC level. Learn about the cloud components that are required for deploying the SAP NetWeaver 7.5 with ASE solution:

### Virtual Private Cloud (VPC) deployment
{: #vpc-deploy-ase}

A VPC is the most basic component necessary for any deployment with the {{site.data.keyword.vpc_short}}. For more information, see [VPC for SAP provisioning](/docs/sap?topic=sap-intro-sap-cloud-vpc#vpc-provision-sap).

### VPC subnet
{: #vpc-subnet-ase}

A VPC subnet is created as part of the network space in which SAP and database deployment take place. For more information, see [New Subnet for VPC](/docs/sap?topic=sap-intro-sap-cloud-vpc#new-subnet-sap-vpc).

### VPC security group
{: #vpc-security-ase}

A VPC security group is created to meet the security demands and standards. By specifying the specific inbound and outbound rules, the VSIs assigned to this security group is protected against threats. For more information, see [Security Group for VPC](/docs/sap?topic=sap-intro-sap-cloud-vpc#security-group-sap-vpc).

### VPC virtual server instance (VSI)
{: #vpc-vsi-ase}

VSIs are created and assigned to proper disks to meet the compute and storage requirements for SAP and database deployment.

For compute component, based on the estimated user load for the SAP system, it is important to choose the correct sizing in terms of CPU and RAM memory resource. For more information, see [Virtual Server profile names](/docs/sap?topic=sap-intro-sap-cloud-vpc#virtual-profile-intel-vsi-sap-vpc).

Also, for any deployment it is important to choose the right OS image for your VSI. Multiple options are available to choose from a selection of SAP certified RHEL, SUSE, or Windows images or custom images. For more information, see the [Catalog images on VPC](/docs/sap?topic=sap-intro-sap-cloud-vpc#catalog-images-sap-nw-db2-rhel).

The storage component is also an important part of any VSI deployment process and choosing the right block storage volume or deciding to use the NFS-based file storage is an important decision for any SAP deployment. For more information, see [Block Storage for Virtual Server Instances on VPC Infrastructure](/docs/sap?topic=sap-intro-sap-cloud-vpc#block-storage-vpc-sap).

## Cloud resource deployment
{: #cloud-resource-deploy-ase}

Before any deployment of SAP and database applications, you should prepare and deploy the necessary cloud resources in the {{site.data.keyword.vpc_short}}. It is important to know the business and geographical location of the VPC placement. For more information, see [SAP NetWeaver deployment to Intel Virtual Server on VPC Infrastructure that uses RHEL](/docs/sap?topic=sap-quickstudy-vs-gen2-netweaver-rhel).

### VPC details
{: #vpc-details}

For instance, the following example data is of a VPC created in Germany with data centers spanning in the Frankfurt area:

| VPC name | VPC region | VPC zones |
| ---------------: | ---------: | ---------: |
| “sap-vpc” | “eu-de” | “eu-de-1”, “eu-de-2” and “eu-de-3” |
{: caption="VPC details" caption-side="top"}

Once the VPC is created, the IBM Cloud console shows a details page confirming all the configuration properties. The VPC is assigned a unique ID `r010-22415c67-d724-4fc9-8d8c-e048f963d0d9` along with a Cloud Resource Name (CRN) prefixed with `crn:v1:bluemix:public:is:eu-de:`. The resource is placed under the resource group `wes-automation`, which was set up beforehand to organize all related cloud components for this deployment. The location is confirmed as Frankfurt (`eu-de`) and the VPC is created.

The following three subnets are defined and available within this VPC, each placed in one of the three Frankfurt zones to ensure zone-level redundancy:

| VPC subnet | Location |
| ---------------: | ---------: |
|“sap-bastion-fra-subnet-01” | “eu-de-1” |
|“sap-bastion-fra-subnet-02” | “eu-de-2” |
|“sap-bastion-fra-subnet-03” | “eu-de-3” |
{: caption="VPC details" caption-side="top"}

This ensures that each VSI or workload can be placed in the appropriate zone with its own dedicated subnet.

Security group `bastion-sg-sap-bast-de` is used for this example. During the planning phase, it is important to use the {{site.data.keyword.vpc_short}} security resources and services (Security groups) by using inbound and outbound rules governing traffic to and from specific VSIs. The user can easily manage access and align with any security standards the business requires.

Before you begin the deployment of the required VSI, note the following {{site.data.keyword.vpc_short}} resource parameters:

* VPC name - “sap-vpc”
* Region - “eu-de”
* Zone - “eu-de-1”
* Subnet - “sap-bastion-fra-subnet-01”
* Security Group - “bastion-sg-sap-bast-de"
* Resource Group - "wes-automation"

When creating the VSI through the IBM Cloud console under **Virtual Server for VPC**, the resource group is set to `wes-automation` to keep the instance alongside all other related components. The location is set to Frankfurt (`eu-de`) with the specific zone `eu-de-1` selected to align with the first subnet. The VSI is attached to the existing VPC `sap-vpc`, placed inside the subnet `sap-bastion-fra-subnet-01`, and assigned to the security group `bastion-sg-sap-bast-de` which controls the inbound and outbound traffic rules for this instance.

Once the VSI is provisioned, the IBM Cloud console shows the instance detail page confirming that the Geography is set to Europe, the Region is Frankfurt (`eu-de`), and the Zone is `eu-de-1`. The instance is running inside the VPC `sap-vpc`, connected to the subnet `sap-bastion-fra-subnet-01`, protected by the security group `bastion-sg-sap-bast-de`, and organized under the resource group `wes-automation`. All of these values mirror the VPC configuration, which confirms the VSI has been correctly created inside the intended VPC environment.

### VSI creation - Image
{: #vsi-image}

Selecting the image and profile is an important step during the VSI creation process. In the **Image** section of the creation form, the OS image catalog is browsed and `Red Hat Enterprise Linux for SAP 9.4` is selected. This image belongs to the Red Hat Enterprise Linux family and targets the x86-64 architecture. It comes pre-configured with the packages and settings required by SAP workloads, making it the appropriate choice for running SAP NetWeaver with ASE on IBM Cloud VPC.

### VSI creation - Profile
{: #vsi-profile}

In the **Profile** section, the profile `bx2-2x8` is selected. This is a balanced compute profile from the `bx2` family, providing 2 vCPUs and 8 GB of memory. For this example deployment the profile is sufficient to demonstrate the installation process, though production workloads should be sized according to SAP's sizing guidelines and expected system load.

### Data Volumes
{: #data-volumes}

After all five volumes are created and attached, the IBM Cloud console lists them in the Storage section of the VSI detail page. The 32 GB SWAP volume is attached as device `vdb`, the 32 GB log volume as `vdc`, the 64 GB Sybase software volume as `vdd`, the 128 GB SAP application volume as `vde`, and the 256 GB data file volume as `vdf`. The device names assigned here correspond directly to the physical volumes used in the LVM setup during the file system preparation steps that follow.

For more information on NFS-based file storage for VSI on VPC infrastructure, see [About File Storage for VPC](/docs/vpc?topic=vpc-file-storage-vpc-about&interface=ui).

For successfully mounting the file storage share through NFS, you need to note down the “Mount path”.
{: note}

For demonstration purposes, the file system `/usr/sap/trans` is used in two different scenarios:
* as a local file system
* as a NFS-based file system, by using the File storage share service available in the {{site.data.keyword.vpc_short}}.

For more information and procedures regarding {{site.data.keyword.vpc_short}} file share resource, see [Creating file shares and mount targets](/docs/vpc?topic=vpc-file-storage-create&interface=ui).

## File system preparation
{: #file-system-prep-ase}

The installation of the SAP NetWeaver 7.5 with ASE on RHEL 9.4 is performed based on the file system layout:

| Disk name| Disk size (GB) | Physical volume | Volume group | Logical volume name | Logical volume size (GB) | File system | Mount point |
| - | - | - | - | - | - | - | - |
| vdb | 32 | /dev/vdb | ts_swap_vg | ts_swap_lv | 30 | /dev/ts_swap_vg/ts_swap_lv | [SWAP] |
| vdc | 32 | /dev/vdc | ts_log_vg | ts_saplog1_lv \n ts_saplog2_lv \n ts_saplog3_lv \n ts_saplog4_lv | 7 \n 7 \n 7 \n 7 | /dev/ts_log_vg/ts_saplog1_lv \n /dev/ts_log_vg/ts_saplog2_lv \n /dev/ts_log_vg/ts_saplog3_lv \n /dev/ts_log_vg/ts_saplog4_lv | /sybase/TS/saplog_1 \n /sybase/TS/saplog_2 \n /sybase/TS/saplog_3 \n /sybase/TS/saplog_4 |
| vdd | 64 | /dev/vdd | ts_syb_vg | ts_sybsoftware_lv \n ts_sybsystem_lv \n ts_sybtemp_lv \n ts_saptemp_lv \n ts_sapdiag_lv \n ts_sybsecurity_lv | 8 \n 10 \n 8 \n 12 \n 8 \n 8 | /dev/ts_syb_vg/ts_sybsoftware_lv \n /dev/ts_syb_vg/ts_sybsystem_lv \n /dev/ts_syb_vg/ts_sybtemp_lv \n /dev/ts_syb_vg/ts_saptemp_lv \n /dev/ts_syb_vg/ts_sapdiag_lv \n /dev/ts_syb_vg/ts_sybsecurity_lv | /sybase/TS \n /sybase/TS/sybsystem \n /sybase/TS/sybtemp \n /sybase/TS/saptemp \n /sybase/TS/sapdiag \n /sybase/TS/sybsecurity |
| vde | 128 | /dev/vde | ts_app_vg | ts_usrsap_lv \n ts_sap_lv \n ts_sapmnt_lv \n ts_saptrans_lv | 5 \n 20 \n 20 \n 20 | /dev/ts_app_vg/ts_usrsap_lv \n /dev/ts_app_vg/ts_sap_lv \n /dev/ts_app_vg/ts_sapmnt_lv \n /dev/ts_app_vg/ts_saptrans_lv | /usr/sap \n /usr/sap/TS \n /sapmnt/TS \n /usr/sap/trans |
| vdf | 256 | /dev/vdf | ts_data_vg | ts_sapdata1_lv \n ts_sapdata2_lv \n ts_sapdata3_lv \n ts_sapdata4_lv | 30 \n 30 \n 30 \n 30 | /dev/ts_data_vg/ts_sapdata1_lv \n /dev/ts_data_vg/ts_sapdata2_lv \n /dev/ts_data_vg/ts_sapdata3_lv \n /dev/ts_data_vg/ts_sapdata4_lv | /sybase/TS/sapdata_1 \n /sybase/TS/sapdata_2 \n /sybase/TS/sapdata_3 \n /sybase/TS/sapdata_4 |
{: row-headers}
{: caption="File system preparation" caption-side="bottom"}

### Creating file system at operating system level
{: #create-file-system-ase}

A proper file system layout is necessary for a correct installation of any SAP solution within the {{site.data.keyword.vpc_short}}.

For the presented example, the SAP SID is `TST`, thus the naming for the volume groups, logical volumes, and file systems are considered as this SID.
{: note}

At the operating system level, physical volumes, volume groups, logical volumes, and file systems are created in a specific sequence.

1. List the available disk by using the command:

    ```bash
    # lsblk
    ```

2. To use LVM, the Linux package `lvm2` must be installed by using the command:

    ```bash
    # dnf install lvm2
    ```

3. Create the required physical volumes by using the following commands:

    ```bash
    # pvcreate /dev/vdb
    # pvcreate /dev/vdc
    # pvcreate /dev/vdd
    # pvcreate /dev/vde
    # pvcreate /dev/vdf
    ```

4. Create the required volume groups by using the following commands:

    ```bash
    # lvcreate -L 8G -n ts_sybsoftware_lv ts_syb_vg
    # lvcreate -L 10G -n ts_sybsystem_lv ts_syb_vg
    # lvcreate -L 8G -n ts_sybtemp_lv ts_syb_vg
    # lvcreate -L 12G -n ts_saptemp_lv ts_syb_vg
    # lvcreate -L 8G -n ts_sapdiag_lv ts_syb_vg
    # lvcreate -L 8G -n ts_sybsecurity_lv ts_syb_vg
    # lvcreate -L 30G -n ts_sapdata1_lv ts_data_vg
    # lvcreate -L 30G -n ts_sapdata2_lv ts_data_vg
    # lvcreate -L 30G -n ts_sapdata3_lv ts_data_vg
    # lvcreate -L 30G -n ts_sapdata4_lv ts_data_vg
    # lvcreate -L 7G -n ts_saplog1_lv ts_log_vg
    # lvcreate -L 7G -n ts_saplog2_lv ts_log_vg
    # lvcreate -L 7G -n ts_saplog3_lv ts_log_vg
    # lvcreate -L 7G -n ts_saplog4_lv ts_log_vg
    ```

5. Create the ASE logical volumes by using the following commands:

    ```bash
    # lvcreate -L 5G -n ts_usrsap_lv ts_app_vg
    # lvcreate -L 20G -n ts_sap_lv ts_app_vg
    # lvcreate -L 20G -n ts_sapmnt_lv ts_app_vg
    # lvcreate -L 20G -n ts_saptrans_lv ts_app_vg
    ```

6. Create the SAP application logical volumes by using the following commands:

    ```bash
    # lvcreate -L 5G -n ts_usrsap_lv ts_app_vg
    # lvcreate -L 20G -n ts_sap_lv ts_app_vg
    # lvcreate -L 20G -n ts_sapmnt_lv ts_app_vg
    # lvcreate -L 20G -n ts_saptrans_lv ts_app_vg
    ```

7. Create the logical volume for the OS SWAP:

    ```bash
    # lvcreate -L 32G -n ts_swap_lv ts_swap_vg
    ```

8. Create the ASE file systems by using the following commands:

    ```bash
    # mkfs.ext4 /dev/ts_syb_vg/ts_sybsoftware_lv
    # mkfs.ext4 /dev/ts_syb_vg/ts_sybsystem_lv
    # mkfs.ext4 /dev/ts_syb_vg/ts_sybtemp_lv
    # mkfs.ext4 /dev/ts_syb_vg/ts_saptemp_lv
    # mkfs.ext4 /dev/ts_syb_vg/ts_sapdiag_lv
    # mkfs.ext4 /dev/ts_syb_vg/ts_sybsecurity_lv
    # mkfs.ext4 /dev/ts_data_vg/ts_sapdata1_lv
    # mkfs.ext4 /dev/ts_data_vg/ts_sapdata2_lv
    # mkfs.ext4 /dev/ts_data_vg/ts_sapdata3_lv
    # mkfs.ext4 /dev/ts_data_vg/ts_sapdata4_lv
    # mkfs.ext4 /dev/ts_log_vg/ts_saplog1_lv
    # mkfs.ext4 /dev/ts_log_vg/ts_saplog2_lv
    # mkfs.ext4 /dev/ts_log_vg/ts_saplog3_lv
    # mkfs.ext4 /dev/ts_log_vg/ts_saplog4_lv
    ```


9. Create the SAP application file systems by using the following commands:

    ```bash
    # mkfs.ext4 /dev/ts_app_vg/ts_usrsap_lv
    # mkfs.ext4 /dev/ts_app_vg/ts_sap_lv
    # mkfs.ext4 /dev/ts_app_vg/ts_sapmnt_lv
    # mkfs.ext4 /dev/ts_app_vg/ts_saptrans_lv
    ```

10. Create and enable the OS SWAP by using the following commands:

    ```bash
    # mkswap /dev/ts_swap_vg/ts_swap_lv
    # swapon /dev/ts_swap_vg/ts_swap_lv
    ```

11. After creating all the required file systems, create the necessary mount points by using the following commands:

    ```bash
    # mkdir -p /sybase/ts
    # mount /dev/ts_syb_vg/ts_sybsoftware_lv /sybase/ts
    # mkdir -p /sybase/ts/sybsystem
    # mount /dev/ts_syb_vg/ts_sybsystem_lv /sybase/ts/sybsystem
    # mkdir -p /sybase/ts/sybtemp
    # mount /dev/ts_syb_vg/ts_sybtemp_lv /sybase/ts/sybtemp
    # mkdir -p /sybase/ts/saptemp
    # mount /dev/ts_syb_vg/ts_saptemp_lv /sybase/ts/saptemp
    # mkdir -p /sybase/ts/sapdiag
    # mount /dev/ts_syb_vg/ts_sapdiag_lv /sybase/ts/sapdiag
    # mkdir -p /sybase/ts/sybsecurity
    # mount /dev/ts_syb_vg/ts_sybsecurity_lv /sybase/ts/sybsecurity
    # mkdir -p /sybase/ts/sapdata_1
    # mount /dev/ts_data_vg/ts_sapdata1_lv /sybase/ts/sapdata_1
    # mkdir -p /sybase/ts/sapdata_2
    # mount /dev/ts_data_vg/ts_sapdata2_lv /sybase/ts/sapdata_2
    # mkdir -p /sybase/ts/sapdata_3
    # mount /dev/ts_data_vg/ts_sapdata3_lv /sybase/ts/sapdata_3
    # mkdir -p /sybase/ts/sapdata_4
    # mount /dev/ts_data_vg/ts_sapdata4_lv /sybase/ts/sapdata_4
    # mkdir -p /sybase/ts/saplog_1
    # mount /dev/ts_log_vg/ts_saplog1_lv /sybase/ts/saplog_1
    # mkdir -p /sybase/ts/saplog_2
    # mount /dev/ts_log_vg/ts_saplog2_lv /sybase/ts/saplog_2
    # mkdir -p /sybase/ts/saplog_3
    # mount /dev/ts_log_vg/ts_saplog3_lv /sybase/ts/saplog_3
    # mkdir -p /sybase/ts/saplog_4
    # mount /dev/ts_log_vg/ts_saplog4_lv /sybase/ts/saplog_4
    # mkdir -p /usr/sap
    # mount /dev/ts_app_vg/ts_usrsap_lv /usr/sap
    # mkdir -p /usr/sap/ts
    # mount /dev/ts_app_vg/ts_sap_lv /usr/sap/ts
    # mkdir -p /sapmnt/ts
    # mount /dev/ts_app_vg/ts_sapmnt_lv /sapmnt/ts
    # mkdir -p /usr/sap/trans
    # mount /dev/ts_app_vg/ts_saptrans_lv /usr/sap/trans
    ```

12. Resize the TMPFS according to [SAP Note 941735 - SAP memory management system for 64-bit Linux systems](https://launchpad.support.sap.com/#/notes/941735) (file system `/dev/shm`) using the following command:

    ```bash
    # mount -o remount,size=47G /dev/shm
    ```

13. Add the following entries in the `/etc/fstab` file to create all the permanent mounts:

    ```bash
    /dev/ts_syb_vg/ts_sybsoftware_lv /sybase/ts ext4 defaults 0 0
    /dev/ts_syb_vg/ts_sybsystem_lv /sybase/ts/sybsystem ext4 defaults 0 0
    /dev/ts_syb_vg/ts_sybtemp_lv /sybase/ts/sybtemp ext4 defaults 0 0
    /dev/ts_syb_vg/ts_saptemp_lv /sybase/ts/saptemp ext4 defaults 0 0
    /dev/ts_syb_vg/ts_sapdiag_lv /sybase/ts/sapdiag ext4 defaults 0 0
    /dev/ts_syb_vg/ts_sybsecurity_lv /sybase/ts/sybsecurity ext4 defaults 0 0
    /dev/ts_data_vg/ts_sapdata1_lv /sybase/ts/sapdata_1 ext4 defaults 0 0
    /dev/ts_data_vg/ts_sapdata2_lv /sybase/ts/sapdata_2 ext4 defaults 0 0
    /dev/ts_data_vg/ts_sapdata3_lv /sybase/ts/sapdata_3 ext4 defaults 0 0
    /dev/ts_data_vg/ts_sapdata4_lv /sybase/ts/sapdata_4 ext4 defaults 0 0
    /dev/ts_log_vg/ts_saplog1_lv /sybase/ts/saplog_1 ext4 defaults 0 0
    /dev/ts_log_vg/ts_saplog2_lv /sybase/ts/saplog_2 ext4 defaults 0 0
    /dev/ts_log_vg/ts_saplog3_lv /sybase/ts/saplog_3 ext4 defaults 0 0
    /dev/ts_log_vg/ts_saplog4_lv /sybase/ts/saplog_4 ext4 defaults 0 0
    /dev/ts_app_vg/ts_usrsap_lv /usr/sap ext4 defaults 0 0
    /dev/ts_app_vg/ts_sap_lv /usr/sap/ts ext4 defaults 0 0
    /dev/ts_app_vg/ts_sapmnt_lv /sapmnt/ts ext4 defaults 0 0
    /dev/ts_app_vg/ts_saptrans_lv /usr/sap/trans ext4 defaults 0 0
    /dev/ts_swap_vg/ts_swap_lv swap swap defaults 0 0
    tmpfs /dev/shm tmpfs size=47G,rw,nosuid,nodev 0 0
    ```

### NFS file system by using {{site.data.keyword.vpc_short}} File Shares
{: #nfs-application-vpc-ase}

[IBM Cloud File Storage for VPC](/docs/vpc?topic=vpc-file-storage-vpc-about) is used to make SAP-specific directories accessible to the SAP system, by using technologies like NFS, shared disks, and cluster file system. When using an HA solution for your SAP system, ensure that you address the HA requirements for SAP file systems properly.

For SAP HA multi-zone applications, file shares are mounted as permanent NFS file systems on both cluster nodes.

* `/usr/sap/[SAPSID]/SYS`

* `/sapmnt/[SAPSID]`

* `/usr/sap/trans`

In some HA or distributed environment, by using NFS to store the application or database data may be necessary. In such case, you can create NFS file systems as file-shares by using them as mount points for SAP application data. For more information, see [Creating file shares and mount targets](/docs/vpc?topic=vpc-file-storage-create&interface=ui).

The following is an example where `/usr/sap/trans` file system is defined as an {{site.data.keyword.vpc_short}} file share and mounted to the SAP system VSI:

* **File storage share for VPC**

A new file storage share is created through the IBM Cloud VPC console by navigating to **Storage > File Shares** and selecting **Create**. During creation, the share is given a descriptive name, assigned to the resource group `wes-automation` so it is grouped with the other VPC resources for this deployment, and placed in the Frankfurt (`eu-de`) region with the zone set to `eu-de-1`. The intended use is set to **VPC**, which means the share will be accessible to VSIs within the same VPC via a mount target.

* **File storage for VPC details**

Once the file share is provisioned, the IBM Cloud console shows its detail page. The share is identified by the unique share ID `41677eaf-e50c-4478-8d5c-6158ff397eb5`, which also forms part of the NFS mount path later used on the VSI. The status is shown as **Stable**, confirming the share is fully available and ready for a mount target to be attached. The resource group is `wes-automation`, the region is Frankfurt (`eu-de`), and the zone is `eu-de-1`, all consistent with the rest of the deployment.

* **File storage for VPC profile**

During file share creation, a performance profile is selected to define the storage characteristics. The `dp2` profile is chosen, which provides a capacity of 100 GB with a maximum of 3000 IOPS and a throughput of approximately 393 MB/s. This profile is suitable for the `/usr/sap/trans` transport directory, which handles SAP transport requests and requires reliable read/write performance across multiple application servers.

* **Mount target access**

When creating the mount target for the file share, the access mode is set to **Security group**. This mode restricts NFS access to only those VSIs that are members of the specified security group, rather than granting access to the entire VPC or subnet. The mount target is associated with the VPC `sap-vpc`, placed on the subnet `sap-bastion-fra-subnet-01`, and controlled by the security group `bastion-sg-sap-bast-de`. This approach ensures that only authorized VSIs within the security group can mount and access the file share.

* **Mount path**

After the file share is created, check the path by selecting **View Path** on the mount target. The IBM Cloud console exposes the NFS mount path composed of the private IP address assigned to the mount target within the subnet, followed by the share UUID used as the export path. For this example, the mount path is `10.243.0.20:/41677eaf_e50c_4478_8d5c_6158ff397eb5`, where `10.243.0.20` is the private IP address of the mount target inside the subnet `sap-bastion-fra-subnet-01`, and `41677eaf_e50c_4478_8d5c_6158ff397eb5` is the unique identifier of the file share. This path must be noted down as it is required for the `fstab` entry and any manual mount operations on the target VSI.

### Shared application file systems by using classic NFS
{: #shared-fs-classic-ase}

If the installation scenario requires, then export one or more file systems. For example, `/usr/sap/trans` between various VSIs, thus between various SAP Applications.

1. Install the NFS package on the source VSI using the command: `dnf list nfs-utils`

2. Check the status by using the command: `systemctl status rpcbind`

3. Enable the NFS package by using the command: `systemctl enable –now nfs-server`

4. For example, export the `/usr/sap/trans` directory, by adding the following line in the `/etc/exports` file: `/usr/sap/trans 10.243.0.180(rw,sync,no_root_squash)`

5. Apply configuration and activate exports by using the command: `exportfs -arv`

6. After completion, see the export list (and options) by using one of the following commands: `exportfs -s` or `showmount -e`

7. On the target VSI, mount the “/usr/sap/trans” through NFS using the command: `mount --types nfs4 10.243.64.10:/usr/sap/trans /usr/sap/trans`

8. Verify the mount file system by using the command: `df -hT /usr/sap/trans`

## Operating system preparation
{: #os-prep-ase}

[RHEL]{: tag-red}

The operating system is prepared according to [SAP note 3108316](https://me.sap.com/notes/3108316/E){: external}. The OS packages are installed by using the command: `# dnf install uuidd libnsl tcsh nfs-utils`

1. After successful installation, check if the `uuidd` daemon is running by using: `# systemctl status uuidd`

2. Next, `SELinux` needs to be set to “permissive”. Open the configuration file “/etc/selinux/config” and set the following: `SELinux=permissive`

3. Once the changes are saved, restart the VSI and verify the above settings by using the command: `# getenforce`

4. Verify the short hostname and FQDN by using the OS level commands:

    * `# hostname`
    * `# hostname -s`
    * `# hostname -f`

5. Verify whether the Linux service `chronyd` is running correctly (enabled by default in RHEL9.4) using the command: `# systemctl status chronyd`

6. Next, check whether the firewall is disabled.

    If the customer-specific security rules are required, then it can be enabled and configured to allow the communication ports of the SAP System.
    {: note}

7. Check the status of the firewall by using the command: `# systemctl status firewall.d`

8. Configure the required Linux Kernel parameters by creating a file named “sap.conf” in the directory “/etc/sysctl.d”. The file content is:

    * `vm.max_map_count=2147483647`

    * `kernel.pid_max=4194304`

9. No process limits are configured as you run “systemd” higher than 239 and SAP kernel 7.54. Check the “systemd” version by running the command: `# systemctl –version`

    Regarding “temp files” you need to ensure that a file “sap.conf” is created under the directory `/etc/tmpfiles.d`, and add the following lines, inside the file:

    ```pre
    # systemd.tmpfiles exclude file for SAP
    # SAP software stores some important files in /tmp which should not be deleted automatically
    # Exclude SAP socket and lock files
    x /tmp/.sap*
    # Exclude TREX lock file
    x /tmp/.trex*lock
    ```

10. Next, check whether the following Linux kernel parameters have the values:

    ```pre
    # sysctl kernel.shmmax kernel.shmall
    kernel.shmmax = 18446744073692774399
    kernel.shmall = 18446744073692774399
       ```

11. Finally, check the OS SWAP space by using the command: `# free -m`

14. Once all the OS preconfigures activities (according to [SAP Note 3108316 - Red Hat Enterprise Linux 9.x: Installation and Configuration](https://me.sap.com/notes/3108316){: external}) and the ASE prerequisites checks are successfully completed, then the operating system needs to be tuned for running SAP NetWeaver Applications. This is done by installing and activating the `tuned-profiles-sap` using the following commands:

    * `# dnf install tuned-profiles-sap`

    * `# tuned-adm profile sap-netweaver`

[SLES]{: tag-green}

The operating system is prepared according to [SAP note 1275776](https://me.sap.com/notes/1275776/E){: external}

* Run the following commands to check or install the package “saptune”:

    `# zypper info saptune`

    `# saptune service enablestart`

* The “saptune” service is enabled and by using the command: `# saptune solution list`

As you are running both SAP NetWeaver and SAP ASE on the same VSI, you need to enable the solution "NetWeaver" and additionally the SAP note **1680803** (which is the only difference between the two solutions).

`# saptune solution apply NETWEAVER`

* SAP note “1680803” - `# saptune note apply 1680803`

After successful installation of the “saptune”, check and configure the operating system installation according to SAP Note 2578899. The commands are:

* Check input/output scheduler - `# grep . /sys/block/*/queue/scheduler`

* Check user TasksMax - `# systemctl --version`

    No changes are done for version 254.24
    {: note}

* Setting of “kernel.pid_max” - `# sysctl -a | grep kernel.pid_max`. Set according to SAP note “2578899”.

* Check the status for “sysstat” - `# systemctl status sysstat`

* Check the status for “sysctl” monitoring - `# systemctl status sysctl-logger.service`

* Check the status for the “UUID daemon” - `# systemctl status uuidd.socket`

* Check that “Polkit” is installed - `# zypper info polkit`

* Check values for “vm.dirty_background_bytes/vm.dirty_bytes”

    `# sysctl -a | grep vm.dirty_background_bytes`

    `# sysctl -a | grep vm.dirty_bytes`

This is already taken care by “saptune”

* Install package “insserv-compat”

    `# zypper in insserv-compat`

    `# zypper info insserv-compat`

* If the VSI is part of a domain, verify that the short hostname and FQDN are reported correctly by the following OS level commands:

    `# hostname`

    `# hostname -s`

    `# hostname -f`

* Check the status of “chronyd” - `# systemctl status chronyd`

* Check the version for package “glibc” - `# zypper info glibc`

According to SAP note 3425215, there is no issue by using SAP ASE 16.0 SP04 PL06.

* Initialize **TMPFS.**

* Resize TMPFS according to SAP Note 941735 (file system /dev/shm): - `# mount -o remount,size=43G /dev/shm`

43 GB represents 70% of RAM + SWAP
{: note}

To make the new size for the “TMPFS” permanent, add the following entry in the “/etc/fstab” file:
Check the new size of “TMPFS” with the following command: - `# df -h /dev/shm`

## Installation of SAP NetWeaver 7.5 with ASE with SWPM
{: #install-sapnw-ase}

After you download the installation media, follow the standard SAP installation procedure that is documented in the SAP installation guides for your SAP version and components. Also, review the corresponding SAP notes.

## Related information
{: #related-info-ase}

* [Release notes for {{site.data.keyword.vpc_short}}](/docs/vpc?topic=vpc-release-notes&interface=ui)
* [IBM Cloud® File Storage for VPC](/docs/vpc?topic=vpc-file-storage-vpc-about)
* [SAP Note 84555 - Windows Server, Linux&reg;, and UNIX: Certified hardware](https://me.sap.com/notes/84855){: external}
* [SAP Note 2927211 - SAP Applications on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}
* [SAP Note 2414820 - SAP on IBM Cloud: Support prerequisites](https://me.sap.com/notes/2414820/E){: external}
* [SAP Note 2923773 - Linux&reg; on {{site.data.keyword.cloud_notm}} (IaaS): Adaption of your SAP License](https://me.sap.com/notes/2923773){: external}
* [SAP Note 2414097 - SAP Applications on {{site.data.keyword.cloud_notm}} Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}
* [SAP Note 2369910 - SAP Software on Linux&reg;: General information](https://me.sap.com/notes/2369910){: external}
* [SAP Note 171380 - Released IBM hardware (Intel processors) and IBM cloud services offers](https://me.sap.com/notes/171380){: external}
* [SAP Note 1380654 - SAP support in IaaS environments](https://me.sap.com/notes/1380654){: external}
* [SAP Note 2987324 - SAP ASE 16.0 SP04 Supported Operating Systems and Versions](https://me.sap.com/notes/2987324/E){: external}
* [SAP Note 3386206 - Which is the current latest ASE version? - SAP ASE](https://me.sap.com/notes/3386206/E){: external}
* [SAP Note 1941500 - Certification information for Linux and other Operating Systems - SAP ASE](https://me.sap.com/notes/1941500/E){: external}
* [SAP Note 1554717 - SYB: Planning Information for SAP on SAP ASE](https://me.sap.com/notes/1554717/E){: external}
* [SAP Note 2526952 - Red Hat Enterprise Linux for SAP Solutions](https://me.sap.com/notes/2526952/E){: external}
* [SAP Note 3108316 - Red Hat Enterprise Linux 9.x: Installation and Configuration](https://me.sap.com/notes/3108316){: external}
* [SAP Note 1597355 - Swap-space recommendation for Linux](https://me.sap.com/notes/1597355){: external}
* [SAP Note 1391070 - Linux UUID solutions](https://me.sap.com/notes/1391070/E){: external}
* [SAP Note 611361 - Hostnames of SAP ABAP Platform servers](https://me.sap.com/notes/611361/E){: external}
* [SAP Note 900929 - Linux: STORAGE_PARAMETERS_WRONG_SET and "mmap() failed"](https://me.sap.com/notes/900929/E){: external}
* [SAP Note 2620175 - Reason for RHEL nproc to be set to unlimited for SAP Systems](https://me.sap.com/notes/2620175/E){: external}
* [SAP Note 1771258 - Linux: User and system resource limits](https://me.sap.com/notes/1771258/E){: external}
* [SAP Note 3139184 - Linux: systemd integration for sapstartsrv and SAP Host Agent](https://me.sap.com/notes/3139184/E){: external}
* [SAP Product Availability Matrix](https://support.sap.com/en/release-upgrade-maintenance.html?anchorId=section_1969201630){: external}
* [SAP Note 405827 - Linux: Recommended file systems](https://me.sap.com/notes/405827/E){: external}
* [SAP Note 941735 - SAP memory management system for 64-bit Linux systems](https://me.sap.com/notes/941735/E){: external}
* [SAP Note 1748888 - SYB: Inst. Systems based on NW 7.3 and Higher: SAP ASE](https://me.sap.com/notes/1748888/E){: external}
* [SAP Note 1680045 - Release Note for Software Provisioning Manager 1.0 (recommended: SWPM 1.0 SP43)](https://me.sap.com/notes/1680045/E){: external}
* [Installation guide: Installation of SAP Systems based on the Application Server ABAP of SAP NetWeaver 7.3 EHP1 to 7.52 on UNIX: SAP Adaptive Server Enterprise](https://help.sap.com/doc/4f95c9e3741a1014955595407d8604de/CURRENT_VERSION/en-US/Inst_nw7x_unix_ase_abap.pdf){: external}
* [SAP Adaptive Server Enterprise 16.0 SP04 - System Administration Guide: Volume 1](https://help.sap.com/doc/a611eab9bc2b1014a4eaf1a160376bec/16.0.4.6/en-US/SAP_ASE_System_Administration_Guide_Volume_1_en.pdf){: external}
* [SAP Adaptive Server Enterprise 16.0 SP04 - System Administration Guide: Volume 2](https://help.sap.com/doc/a6121aa4bc2b1014973595b879fce7fd/16.0.4.6/en-US/SAP_ASE_System_Administration_Guide_Volume_2_en.pdf){: external}
* [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}.
* [SAP Note 2588225 - SAP on IBM Cloud: Protect against speculative execution vulnerabilities](https://me.sap.com/notes/2588225){: external}
* [SAP Note 1380654 - SAP support in IaaS environments](https://me.sap.com/notes/1380654){: external}
* [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}
