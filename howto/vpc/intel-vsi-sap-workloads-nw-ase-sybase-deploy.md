---
copyright:
  years: 2026
lastupdated: "2026-10-15"
keywords: SAP, {{site.data.keyword.cloud_notm}}, {{site.data.keyword.ibm_cloud_sap}}, NetWeaver, ASE
subcollection: sap
content-type: tutorial
services: vpc, workloads, ase
account-plan: lite
completion-time: 1h
---

{{site.data.keyword.attribute-definition-list}}


# Deploying SAP NetWeaver 7.5 with ASE
{: #deploy-sap-ase-intro}
{: toc-content-type="tutorial"}
{: toc-services="vpc, workload, netweaver, ase"}
{: toc-completion-time="1h"}

This tutorial describes the deployment of SAP NetWeaver 7.5 with ASE Sybase on Red Hat&reg; Enterprise Linux&reg; (RHEL) 9.4 performed in phases:

1. [Cloud infrastructure planning](/docs/sap?topic=sap-deploy-sap-ase-intro#cloud-infra-plan-ase)
2. [Cloud resource deployment](/docs/sap?topic=sap-deploy-sap-ase-intro#cloud-resource-deploy-ase)
3. [File system preparation](/docs/sap?topic=sap-deploy-sap-ase-intro#file-system-prep-ase)
4. [Operating system preparation](/docs/sap?topic=sap-deploy-sap-ase-intro#os-prep-ase)
5. [Installation of SAP NetWeaver 7.5 with ASE with Software Provisioning Manager (SWPM)](/docs/sap?topic=sap-deploy-sap-ase-intro#install-sapnw-ase)

## Cloud infrastructure planning
{: #cloud-infra-plan-ase}

Before performing any deployment, it is important to plan the cloud infrastructure requirements and  ensure that all the required components are deployed on Cloud VPC level.

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

In the **Storage** section, additional data volumes are created to align with the required file system layout. Create and attach the following volumes individually based on the SAP and ASE Sybase requirements:

- 32 GB SWAP disk
- 64 GB log volume disk
- 64 GB Sybase software disk
- 128 GB SAP application disk
- 256 GB data file disk

After all the five volumes are created and attached, they appear in the Storage section of the VSI details page in the IBM Cloud console. The volumes are assigned to the following devices:

- vdb - 32 GB SWAP volume
- vdc - 64 GB log volume
- vdd - 64 GB Sybase software volume
- vde - 128 GB SAP application volume
- vdf - 256 GB data file volume

The device names assigned here correspond directly to the physical volumes used in the LVM configuration during the subsequent file system preparation steps.

## File system preparation
{: #file-system-prep-ase}

The installation of SAP NetWeaver 7.5 with ASE is performed based on the following file system layout. The SAP SID is **SFD** and all volume group, logical volume, and directory names follow this SID convention.

| Disk name| Disk size (GB) | Physical volume | Volume group | Logical volume name | Logical volume size (GB) | File system | Mount point |
| - | - | - | - | - | - | - | - |
| vdb | 32 | /dev/vdb | sfd_swap_vg | sfd_swap_lv | 30 | /dev/sfd_swap_vg/sfd_swap_lv | [SWAP] |
| vdc | 64 | /dev/vdc | sfd_log_vg | sfd_saplog1_lv \n sfd_saplog2_lv \n sfd_saplog3_lv \n sfd_saplog4_lv | 12 \n 12 \n 12 \n 12 | /dev/sfd_log_vg/sfd_saplog1_lv \n /dev/sfd_log_vg/sfd_saplog2_lv \n /dev/sfd_log_vg/sfd_saplog3_lv \n /dev/sfd_log_vg/sfd_saplog4_lv | /sybase/SFD/saplog_1 \n /sybase/SFD/saplog_2 \n /sybase/SFD/saplog_3 \n /sybase/SFD/saplog_4 |
| vdd | 64 | /dev/vdd | sfd_syb_vg | sfd_sybsoftware_lv \n sfd_sybsystem_lv \n sfd_sybtemp_lv \n sfd_saptemp_lv \n sfd_sapdiag_lv \n sfd_sybsecurity_lv | 8 \n 10 \n 8 \n 12 \n 8 \n 8 | /dev/sfd_syb_vg/sfd_sybsoftware_lv \n /dev/sfd_syb_vg/sfd_sybsystem_lv \n /dev/sfd_syb_vg/sfd_sybtemp_lv \n /dev/sfd_syb_vg/sfd_saptemp_lv \n /dev/sfd_syb_vg/sfd_sapdiag_lv \n /dev/sfd_syb_vg/sfd_sybsecurity_lv | //sybase/SFD \n /sybase/SFD/sybsystem \n /sybase/SFD/sybtemp \n /sybase/SFD/saptemp \n /sybase/SFD/sapdiag \n /sybase/SFD/sybsecurity |
| vde | 128 | /dev/vde | sfd_app_vg | sfd_usrsap_lv \n sfd_sap_lv \n sfd_sapmnt_lv \n sfd_saptrans_lv | 5 \n 20 \n 20 \n 20 | /dev/sfd_app_vg/sfd_usrsap_lv \n /dev/sfd_app_vg/sfd_sap_lv \n /dev/sfd_app_vg/sfd_sapmnt_lv \n /dev/sfd_app_vg/sfd_saptrans_lv | /usr/sap \n /usr/sap/SFD \n /sapmnt/SFD \n /usr/sap/trans |
| vdf | 256 | /dev/vdf | sfd_data_vg | sfd_sapdata1_lv \n sfd_sapdata2_lv \n sfd_sapdata3_lv \n sfd_sapdata4_lv | 45 \n 45 \n 45 \n 45 | /dev/sfd_data_vg/sfd_sapdata1_lv \n /dev/sfd_data_vg/sfd_sapdata2_lv \n /dev/sfd_data_vg/sfd_sapdata3_lv \n /dev/sfd_data_vg/sfd_sapdata4_lv | /sybase/SFD/sapdata_1 \n /sybase/SFD/sapdata_2 \n /sybase/SFD/sapdata_3 \n /sybase/SFD/sapdata_4 |
{: row-headers}
{: caption="File system preparation" caption-side="bottom"}

### Creating file system at operating system level
{: #create-file-system-ase}

A proper file system layout is necessary for a correct installation of any SAP solution within the {{site.data.keyword.vpc_short}}.

For the presented example, the SAP SID is `SFD`, thus the naming for the volume groups, logical volumes, and file system directories use this SID as their naming prefix
{: note}

At the operating system level, physical volumes, volume groups, logical volumes, and file systems are created in a specific sequence.

1. List the available disk by using the command:

    ```bash
    # lsblk
    ```

    **Verification**: Confirm that devices vdb, vdc, vdd, vde, and vdf appear in the output and are not yet partitioned or formatted. Expected output:

    | NAME | MAJ:MIN RM | SIZE | RO | TYPE | MOUNTPOINT |
    | - | - | - | - | - | - |
    | vda |  252:0  | 0 | 25G  | 0 | disk |
    | vdb |  252:16 | 0 | 32G  | 0 | disk |
    | vdc |  252:32 | 0 | 64G  | 0 | disk |
    | vdd |  252:48 | 0 | 64G  | 0 | disk |
    | vde |  252:64 | 0 | 128G | 0 | disk |
    | vdf |  252:80 | 0 | 256G | 0 | disk |
    {: caption="Verification" caption-side="bottom"}
    

2. To use LVM, the Linux package `lvm2` must be installed by using the command:

    [RHEL]{: tag-red}

    ```bash
    # dnf install lvm2
    ```

    [SLES]{: tag-green}

    ```bash
    # zypper install lvm2
    ```

3. Create the required physical volumes by using the following commands:

    ```bash
    # pvcreate /dev/vdb
    # pvcreate /dev/vdc
    # pvcreate /dev/vdd
    # pvcreate /dev/vde
    # pvcreate /dev/vdf
    ```

    **Verification**: List all physical volumes:

    ```bash
    # pvs
    # pvdisplay
    ```

    Each device should appear with a PV label and a PVSize matching the disk size.

4. Create the required volume groups by using the following commands:

    ```bash
    # vgcreate sfd_swap_vg /dev/vdb
    # vgcreate sfd_log_vg /dev/vdc
    # vgcreate sfd_syb_vg /dev/vdd
    # vgcreate sfd_app_vg /dev/vde
    # vgcreate sfd_data_vg /dev/vdf
    ```

    **Verification**: List all volume groups:
    ```bash
    # vgs
    # vgdisplay
    ```
    
    All five volume groups (`sfd_swap_vg`, `sfd_log_vg`, `sfd_syb_vg`, `sfd_app_vg`, `sfd_data_vg`) should appear with VG status: resizable and their respective sizes.
    
5. Create the SAP ASE logical volumes by using the following commands:

    ```bash
    # lvcreate -L 8G  -n sfd_sybsoftware_lv sfd_syb_vg
    # lvcreate -L 10G -n sfd_sybsystem_lv   sfd_syb_vg
    # lvcreate -L 8G  -n sfd_sybtemp_lv     sfd_syb_vg
    # lvcreate -L 12G -n sfd_saptemp_lv     sfd_syb_vg
    # lvcreate -L 8G  -n sfd_sapdiag_lv     sfd_syb_vg
    # lvcreate -L 8G  -n sfd_sybsecurity_lv sfd_syb_vg
    # lvcreate -L 45G -n sfd_sapdata1_lv    sfd_data_vg
    # lvcreate -L 45G -n sfd_sapdata2_lv    sfd_data_vg
    # lvcreate -L 45G -n sfd_sapdata3_lv    sfd_data_vg
    # lvcreate -L 45G -n sfd_sapdata4_lv    sfd_data_vg
    # lvcreate -L 12G -n sfd_saplog1_lv     sfd_log_vg
    # lvcreate -L 12G -n sfd_saplog2_lv     sfd_log_vg
    # lvcreate -L 12G -n sfd_saplog3_lv     sfd_log_vg
    # lvcreate -L 12G -n sfd_saplog4_lv     sfd_log_vg
    ```

6. Create the SAP application logical volumes by using the following commands:

    ```bash
    # lvcreate -L 5G  -n sfd_usrsap_lv   sfd_app_vg
    # lvcreate -L 20G -n sfd_sap_lv      sfd_app_vg
    # lvcreate -L 20G -n sfd_sapmnt_lv   sfd_app_vg
    # lvcreate -L 20G -n sfd_saptrans_lv sfd_app_vg
    ```

    **Verification**: List all logical volumes to confirm all ASE and SAP application LVs were created:

    ```bash
    # lvs
    # lvdisplay
    ```
    Eighteen logical volumes should appear in total across all volume groups.

7. Create the logical volume for the OS SWAP:

    ```bash
    # lvcreate -L 30G -n sfd_swap_lv sfd_swap_vg
    ```

8. Create the ASE file systems by using the following commands:

    ```bash
    # mkfs.ext4 /dev/sfd_syb_vg/sfd_sybsoftware_lv
    # mkfs.ext4 /dev/sfd_syb_vg/sfd_sybsystem_lv
    # mkfs.ext4 /dev/sfd_syb_vg/sfd_sybtemp_lv
    # mkfs.ext4 /dev/sfd_syb_vg/sfd_saptemp_lv
    # mkfs.ext4 /dev/sfd_syb_vg/sfd_sapdiag_lv
    # mkfs.ext4 /dev/sfd_syb_vg/sfd_sybsecurity_lv
    # mkfs.ext4 /dev/sfd_data_vg/sfd_sapdata1_lv
    # mkfs.ext4 /dev/sfd_data_vg/sfd_sapdata2_lv
    # mkfs.ext4 /dev/sfd_data_vg/sfd_sapdata3_lv
    # mkfs.ext4 /dev/sfd_data_vg/sfd_sapdata4_lv
    # mkfs.ext4 /dev/sfd_log_vg/sfd_saplog1_lv
    # mkfs.ext4 /dev/sfd_log_vg/sfd_saplog2_lv
    # mkfs.ext4 /dev/sfd_log_vg/sfd_saplog3_lv
    # mkfs.ext4 /dev/sfd_log_vg/sfd_saplog4_lv
    ```

9. Create the SAP application file systems by using the following commands:

    ```bash
    # mkfs.ext4 /dev/sfd_app_vg/sfd_usrsap_lv
    # mkfs.ext4 /dev/sfd_app_vg/sfd_sap_lv
    # mkfs.ext4 /dev/sfd_app_vg/sfd_sapmnt_lv
    # mkfs.ext4 /dev/sfd_app_vg/sfd_saptrans_lv
    ```

10. Create and enable the OS SWAP by using the following commands:

    ```bash
    # mkswap /dev/sfd_swap_vg/sfd_swap_lv
    # swapon /dev/sfd_swap_vg/sfd_swap_lv
    ```

    **Verification**: Confirm SWAP is active:

    ```bash
    # swapon --show
    # free -m
    ```
    
    The swapon `--show` output should list the SWAP device with the correct size. `free -m` should show swap total ≥ 30 GB.

11. After creating all the required file systems, create the necessary mount points by using the following commands:

    ```bash
    # mkdir -p /sybase/SFD
    # mount /dev/sfd_syb_vg/sfd_sybsoftware_lv /sybase/SFD
    # mkdir -p /sybase/SFD/sybsystem
    # mount /dev/sfd_syb_vg/sfd_sybsystem_lv /sybase/SFD/sybsystem
    # mkdir -p /sybase/SFD/sybtemp
    # mount /dev/sfd_syb_vg/sfd_sybtemp_lv /sybase/SFD/sybtemp
    # mkdir -p /sybase/SFD/saptemp
    # mount /dev/sfd_syb_vg/sfd_saptemp_lv /sybase/SFD/saptemp
    # mkdir -p /sybase/SFD/sapdiag
    # mount /dev/sfd_syb_vg/sfd_sapdiag_lv /sybase/SFD/sapdiag
    # mkdir -p /sybase/SFD/sybsecurity
    # mount /dev/sfd_syb_vg/sfd_sybsecurity_lv /sybase/SFD/sybsecurity
    # mkdir -p /sybase/SFD/sapdata_1
    # mount /dev/sfd_data_vg/sfd_sapdata1_lv /sybase/SFD/sapdata_1
    # mkdir -p /sybase/SFD/sapdata_2
    # mount /dev/sfd_data_vg/sfd_sapdata2_lv /sybase/SFD/sapdata_2
    # mkdir -p /sybase/SFD/sapdata_3
    # mount /dev/sfd_data_vg/sfd_sapdata3_lv /sybase/SFD/sapdata_3
    # mkdir -p /sybase/SFD/sapdata_4
    # mount /dev/sfd_data_vg/sfd_sapdata4_lv /sybase/SFD/sapdata_4
    # mkdir -p /sybase/SFD/saplog_1
    # mount /dev/sfd_log_vg/sfd_saplog1_lv /sybase/SFD/saplog_1
    # mkdir -p /sybase/SFD/saplog_2
    # mount /dev/sfd_log_vg/sfd_saplog2_lv /sybase/SFD/saplog_2
    # mkdir -p /sybase/SFD/saplog_3
    # mount /dev/sfd_log_vg/sfd_saplog3_lv /sybase/SFD/saplog_3
    # mkdir -p /sybase/SFD/saplog_4
    # mount /dev/sfd_log_vg/sfd_saplog4_lv /sybase/SFD/saplog_4
    # mkdir -p /usr/sap
    # mount /dev/sfd_app_vg/sfd_usrsap_lv /usr/sap
    # mkdir -p /usr/sap/SFD
    # mount /dev/sfd_app_vg/sfd_sap_lv /usr/sap/SFD
    # mkdir -p /sapmnt/SFD
    # mount /dev/sfd_app_vg/sfd_sapmnt_lv /sapmnt/SFD
    # mkdir -p /usr/sap/trans
    # mount /dev/sfd_app_vg/sfd_saptrans_lv /usr/sap/trans
    ```

    **Verification**: Confirm all file systems are mounted correctly:

    ```bash
    # df -hT | grep -E "sybase|usr/sap|sapmnt"
    ```

    All 18 mount points should appear with their ext4 type and the correct device mapper paths.
    
12. Resize the TMPFS according to [SAP Note 941735 - SAP memory management system for 64-bit Linux systems](https://launchpad.support.sap.com/#/notes/941735){: external} (file system `/dev/shm`). For the bx2-2x8 profile (8 GB RAM, 30 GB SWAP = 38 GB total), the recommended size is 75% of total memory = 28 GB:

    ```bash
    # mount -o remount,size=28G /dev/shm
    ```

    **Verification**: Confirm the new TMPFS size:

    ```bash
    # df -h /dev/shm
    ```

    The Size column should report 28G.

13. Add the following entries in the `/etc/fstab` file to create all the permanent mounts:

    ```bash
    /dev/sfd_syb_vg/sfd_sybsoftware_lv /sybase/SFD          ext4 defaults 0 0
    /dev/sfd_syb_vg/sfd_sybsystem_lv   /sybase/SFD/sybsystem ext4 defaults 0 0
    /dev/sfd_syb_vg/sfd_sybtemp_lv     /sybase/SFD/sybtemp   ext4 defaults 0 0
    /dev/sfd_syb_vg/sfd_saptemp_lv     /sybase/SFD/saptemp   ext4 defaults 0 0
    /dev/sfd_syb_vg/sfd_sapdiag_lv     /sybase/SFD/sapdiag   ext4 defaults 0 0
    /dev/sfd_syb_vg/sfd_sybsecurity_lv /sybase/SFD/sybsecurity ext4 defaults 0 0
    /dev/sfd_data_vg/sfd_sapdata1_lv   /sybase/SFD/sapdata_1 ext4 defaults 0 0
    /dev/sfd_data_vg/sfd_sapdata2_lv   /sybase/SFD/sapdata_2 ext4 defaults 0 0
    /dev/sfd_data_vg/sfd_sapdata3_lv   /sybase/SFD/sapdata_3 ext4 defaults 0 0
    /dev/sfd_data_vg/sfd_sapdata4_lv   /sybase/SFD/sapdata_4 ext4 defaults 0 0
    /dev/sfd_log_vg/sfd_saplog1_lv     /sybase/SFD/saplog_1  ext4 defaults 0 0
    /dev/sfd_log_vg/sfd_saplog2_lv     /sybase/SFD/saplog_2  ext4 defaults 0 0
    /dev/sfd_log_vg/sfd_saplog3_lv     /sybase/SFD/saplog_3  ext4 defaults 0 0
    /dev/sfd_log_vg/sfd_saplog4_lv     /sybase/SFD/saplog_4  ext4 defaults 0 0
    /dev/sfd_app_vg/sfd_usrsap_lv      /usr/sap              ext4 defaults 0 0
    /dev/sfd_app_vg/sfd_sap_lv         /usr/sap/SFD          ext4 defaults 0 0
    /dev/sfd_app_vg/sfd_sapmnt_lv      /sapmnt/SFD           ext4 defaults 0 0
    /dev/sfd_app_vg/sfd_saptrans_lv    /usr/sap/trans        ext4 defaults 0 0
    /dev/sfd_swap_vg/sfd_swap_lv       swap                  swap defaults 0 0
    tmpfs /dev/shm tmpfs size=28G,rw,nosuid,nodev 0 0
    ```

    **Verification**: Validate the fstab syntax and test all entries without rebooting:

    ```bash
    # mount -a
    # echo $?
    ```

    A return code of 0 confirms all entries are valid and all file systems mounted successfully.
    
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

The operating system must be configured according to the SAP prerequisite requirements before any SAP software is installed. The operating system is prepared according to [SAP Note 3108316](https://me.sap.com/notes/3108316/E){: external} (RHEL) and [SAP Note 1275776](https://me.sap.com/notes/1275776/E){: external} for SLES.

### Step 1 – Install required OS packages
{: #step1}

[RHEL]{: tag-red}

```bash
dnf install -y uuidd libnsl tcsh nfs-utils libstdc++ compat-openssl11 java-11-openjdk xorg-x11-fonts-Type1 csh
```

[SLES]{: tag-green}

```bash
zypper install -y uuidd libnsl1 tcsh nfs-utils libstdc++6 libopenssl1_1  java-11-openjdk xorg-x11-fonts
```

After installation, start and enable the uuidd daemon, which is used by SAP to generate universally unique identifiers for enqueue table entries.

```bash
systemctl enable --now uuidd
systemctl status uuidd
```

Confirm that `uuidd` is shown as active (running) in the output of the status command.

### Step 2 – Configuring SELinux
{: #step2}

[RHEL]{: tag-red}

SELinux must be set to permissive mode for SAP. Open the configuration file /etc/selinux/config and set:

```bash
SELINUX=permissive
```

Restart the VSI and verify:

```bash
# reboot
# getenforce
```

**Verification**: The output must be Permissive. 

[SLES]{: tag-green}

```bash
# systemctl stop apparmor
# systemctl disable apparmor
```

Verify AppArmor is no longer enforcing profiles:

```bash
# aa-status
```
**Verification**: The output should show 0 profiles in enforce mode. If AppArmor must remain active per your security policy, add SAP-specific AppArmor profiles instead.

There is no SELINUX=permissive equivalent on SLES — do not create /etc/selinux/config. **AppArmor** is the security module used on SLES.
{: note}

### Step 3 – Configuring hostname and DNS
{: #step3}

Verify the short hostname and FQDN:

```bash
# hostname
# hostname -s
# hostname -f
```

**Verification**: `hostname -s` must return only the short hostname (no dots). `hostname -f` must return the fully qualified domain name. Both must match the /etc/hosts entry:

```bash
# grep $(hostname -s) /etc/hosts
```

The entry should resolve to the VSI’s primary private IP.

### Step 4 – Configuring Linux kernel parameters for SAP
{: #step4}

Create the file `/etc/sysctl.d/sap.conf` with the following content:

vm.max_map_count=2147483647
kernel.pid_max=4194304

Apply immediately without reboot:

```bash
# sysctl -p /etc/sysctl.d/sap.conf
```

**Verification**: Confirm the kernel parameters are applied:

```bash
# sysctl vm.max_map_count kernel.pid_max kernel.shmmax kernel.shmall
```

**Expected output:**

vm.max_map_count = 2147483647
kernel.pid_max = 4194304
kernel.shmmax = 18446744073692774399
kernel.shmall = 18446744073692774399

### Step 5 – Configuring tmpfiles for SAP
{: #step5}

Create `/etc/tmpfiles.d/sap.conf` with the following content to prevent SAP temporary files from being cleaned by `systemd-tmpfiles`:

```bash
# systemd.tmpfiles exclude file for SAP
# SAP software stores some important files in /tmp which should not be deleted automatically
# Exclude SAP socket and lock files
x /tmp/.sap*
# Exclude TREX lock file
x /tmp/.trex*lock
```

### Step  6 – Check time synchronization
{: #step6}

Verify that `chronyd` is running correctly:

```bash
# systemctl status chronyd
```

**Verification**: Status must be active (running). Check synchronization:

```bash
# chronyc tracking
```

The system time offset should be within milliseconds of the NTP source.

### Step 7 – Check firewall status
{: #step7}

Check whether the firewall is disabled:

```bash
# systemctl status firewalld
```

If customer-specific security rules are required, the firewall can be enabled and configured to allow the communication ports of the SAP system.
{: note}

### Step 8 – Apply SAP NetWeaver tuned profile
{: #step8}

[RHEL]{: tag-red}

Install and activate the SAP NetWeaver performance tuning profile:

```bash
# dnf install tuned-profiles-sap
# tuned-adm profile sap-netweaver
# tuned-adm active
```

**Verification**: The output of `tuned-adm` active must show: Current active profile: sap-netweaver

[SLES]{: tag-green}

Install saptune if it is not already present:

```bash
# zypper install -y saptune
```

Enable the service and apply the SAP NetWeaver solution:

```bash
# saptune service enable
# saptune service start
# saptune solution apply NETWEAVER
```

**Verification**: Confirm the solution is active:

```bash
# saptune solution enabled
# saptune status
```

The output of saptune solution enabled must show NETWEAVER. The output of saptune status must show `saptune.service` as enabled and active.

### Step 9 – Check SWAP space
{: #step9}

Run the following commands to check the SWAP space:

```bash
# free -m
# swapon --show
```

**Verification**: The total SWAP should show ≥ 30 GB as configured in the LVM layout.

## Installation of SAP NetWeaver 7.5 with ASE with SWPM
{: #install-sapnw-ase}

After you download the installation media, follow the standard SAP installation procedure that is documented in the SAP installation guides for your SAP version and components. Also, review the corresponding SAP notes.

### Downloading and staging installation media
{: #download-stage}

All installation media must be organised into dedicated subdirectories under `/software` on the VSI before the installer is launched. The following directory structure is required:

```bash
/software/
├ sapcar/       		# SAPCAR extraction tool (SAPCAR_xxx.EXE)
├ swpm/         		# SWPM 2.0 SAR archive, extracted sapinst binary lands here
├ kernel/       		# SAP Kernel SAR archives (SAPEXE + SAPEXEDB)
├ igs/          		# Internet Graphics Server archives (igsexe + igshelper)
├ hostagent/    	    # SAP Host Agent SAR archive
├ ase/          		# SAP ASE 16.0 SP04 ZIP archive
└- export/       		# SAP NW 7.5 ABAP Export ZIP archives
```

#### Step 1 – Download the installation media
{: #download-media}

Log on to [SAP Software Downloads Center (SWDC)](https://support.sap.com/en/my-support/software-downloads.html?isu_page=1){: external} and download the following components.

Always download the latest available patch level for your OS. Earlier releases may not pass SWPM’s operating system compatibility check. Exact minimum patch levels vary by OS release and are updated frequently — always verify current certification status in the SAP Product Availability Matrix (PAM) and the referenced SAP Notes before downloading.
{: note}

| Component | SAP SWDC name / search term | Target folder on VSI | Description |
| -- | -- | -- | -- |
| SAPCAR | SAPCAR_*.EXE | /software/sapcar/ | Archive extraction tool — download first |
| SWPM 2.0 | SWPM20SP*.SAR | /software/swpm/ | SAP Software Provisioning Manager 2.0 | 
| SAP Kernel 7.54 (UC) | SAPEXE_*.SAR | /software/kernel/ | SAP NetWeaver 7.5 kernel – Unicode | 
| SAP Kernel 7.54 DB (UC) | SAPEXEDB_*.SAR | /software/kernel/ | SAP kernel DB libraries for ASE — must match kernel patch level |
| Internet Graphics Server | igsexe_*.sar | /software/igs/ | SAP IGS executable |
| IGS Helper | igshelper_*.sar | /software/igs/ | SAP IGS helper — must match IGS executable release |
| SAP Host Agent | SAPHOSTAGENT_*.SAR | /software/hostagent/ | SAP Host Agent |
| SAP ASE 16.0 SP04 | SYBASE_ASE_16_*.zip | /software/ase/ | SAP ASE for Linux x86_64 |
| SAP NW 7.5 Export | NW_ABAP_*_Export_*.zip | /software/export/ | ABAP initial system export |
{: caption="Installation media" caption-side="bottom"}


#### Step 2 – Creating the staging directory structure on the VSI
{: #create-staging}

Create all required directories before transferring files:

```bash
# mkdir -p /software/sapcar
# mkdir -p /software/swpm
# mkdir -p /software/kernel
# mkdir -p /software/igs
# mkdir -p /software/hostagent
# mkdir -p /software/ase
# mkdir -p /software/export
```

#### Step 3 – Transfer and place files into the correct folders
{: #transfer}

Transfer the downloaded files to the VSI using scp or sftp, placing each file in its designated directory:

```bash
# scp SAPCAR_*.EXE         root@<vsi_ip>:/software/sapcar/
# scp SWPM20SP*.SAR        root@<vsi_ip>:/software/swpm/
# scp SAPEXE_*.SAR         root@<vsi_ip>:/software/kernel/
# scp SAPEXEDB_*.SAR       root@<vsi_ip>:/software/kernel/
# scp igsexe_*.sar         root@<vsi_ip>:/software/igs/
# scp igshelper_*.sar      root@<vsi_ip>:/software/igs/
# scp SAPHOSTAGENT_*.SAR   root@<vsi_ip>:/software/hostagent/
# scp SYBASE_ASE_16_*.zip  root@<vsi_ip>:/software/ase/
# scp NW_ABAP_*_Export_*.zip root@<vsi_ip>:/software/export/
```

#### Step 4 – Make SAPCAR executable and extract the SWPM archive
{: #sapcr}

SAPCAR is the SAP archive extraction tool. It must be made executable before use, and is also required to extract the SWPM SAR archive:

```bash
# chmod +x /software/sapcar/SAPCAR*.EXE
# /software/sapcar/SAPCAR*.EXE -xvf /software/swpm/SWPM20SP*.SAR -R /software/swpm/
```

**Verification**: Confirm the sapinst binary is present and executable:

```bash
# ls -lh /software/swpm/sapinst
# /software/swpm/sapinst --version
```

**Expected**: a version string beginning with SWPM followed by the SP and patch level number, for example SWPM 2.0, patch level 27.

### GUI installation with SWPM
{: #gui-install}

Use this method for a browser-based installation. SWPM provides a web GUI served over HTTPS on port 4237. No local display or X11 forwarding is required — any browser with access to the VSI’s IP on that port is sufficient.

#### Step 1 – Launch the SWPM GUI
{: #launch}

Start the SWPM installer as `root`. Because SWPM auto-detects the hostname for its TLS certificate, pass the servers primary private IP address using `SAPINST_GUI_HOSTNAME` to avoid a certificate SAN mismatch error:

```bash
# cd /software/swpm
# ./sapinst SAPINST_GUI_HOSTNAME=<vsi_private_ip>
```

SWPM prints the GUI URL to the terminal:

```bash
https://<vsi_private_ip>:4237/sapinst/docs/index.html
Logon users: [root]
```

Open that URL in a browser. Accept the self-signed certificate warning — this is expected. Log in as **root** when prompted.

#### Step 2 – Navigate the SWPM product tree
{: #navigate}

In the SWPM launcher, expand the product tree and navigate to:

SAP NetWeaver 7.5
  └── SAP ASE
        └── Installation
              └── Application Server ABAP
                    └── Standard System
                          └── Standard System

Select Standard System and click **Next**.

#### Step 3 – Follow the SWPM installation screens
{: #follow}

Follow the instructions on the Software Provisioning Manager screens and enter the required parameters. In Typical mode, SWPM prompts only for the essential inputs listed below; all other parameters are set to defaults and can be reviewed on the **Parameter Summary** screen before the installation starts.

#### Step 4 - Parameter Summary screen (Review Parameters)
{: #parameter}

**Screen title:** Parameter Summary

The wizard moves to the Review Parameters phase. SWPM displays a collapsible summary of every parameter entered across all screens. Review each section carefully:

| Section | Key values to verify |
| -- | -- |
| Parameter Settings | Mode: Typical |
| General SAP System Parameters | SAPSID: SFD, SAP Mount Directory: /sapmnt/SFD |
| DNS Domain Name | Set FQDN for SAP system: unchecked |
| Master Password | Password for All Users: (masked) |
| Software Package Browser | All 4 kernel/IGS packages: Available |
| Software Package Browser | SAPHOSTAGENT: Available |
| SAP ASE Database System Parameters | Package Path: /software/ase |
| SAP ASE Database System Parameters | Physical Memory: 7000 MB, Cores: 4, Connections: 200 |
| SAP System Database Import | Number of Parallel Jobs: 3 | 
| PAS and ASCS Instance | PAS: 00 on stg-nwsyb-vm, ASCS: 01 on stg-nwsyb-vm |
{: caption="Parameter Summary" caption-side="bottom"}

To change any value, check the checkbox next to the section name and click **Revise**. SWPM takes you back to that screen. When all parameters are correct, click **Next** to proceed to Execute Service and start the installation.

#### Step 5 – Monitor installation progress and logs
{: #monitor}

Once the **Execute Service** phase begins, SWPM runs all installation steps automatically. Monitor the progress in the SWPM GUI status bar.

If an error occurs, SWPM pauses and displays the error message with **Retry**, **Log Files**, and **Stop** options. Logs are also available at:

```bash
# tail -f /tmp/sapinst_instdir/NW750/SYB/INSTALL/STD/ABAP/sapinst.log
# tail -f /tmp/sapinst_instdir/NW750/SYB/INSTALL/STD/ABAP/sapinst_dev.log
```

Resolve the displayed error, then click **Retry** in the SWPM GUI to resume the installation from the failed step without restarting from scratch.

The full installation may take 60–120 minutes depending on the hardware profile and storage IOPS.
{: note}

#### Step 6 – Service Completed screen (Information)
{: #service}

**Screen title:** Service Completed SWPM phase indicator: Define Parameters > Review Parameters > Execute Service > Service Completed

When all installation steps succeed, SWPM advances to the Service Completed phase and shows a progress bar at 100% with a green checkmark.

Click **Ok** to acknowledge the completion message. SWPM moves to the final confirmation screen.

SAP Note 1923064 describes the ABAP task manager for lifecycle management automation (LMA). It provides a framework for automating post-installation configuration tasks such as client settings, transport configuration, and system parameters. Review this note after the installation is complete.
{: note}

#### Step 7 – Installation successfully finished (Exit)
{: #exit}

**Screen title:** Service Completed SWPM phase indicator: Define Parameters > Review Parameters > Execute Service > Service Completed.

After clicking **Ok** on the Information screen, SWPM displays the final confirmation:

The process execution is finished successfully.

The progress bar remains at 100% with a green checkmark. An Exit button is displayed at the bottom of the screen.

Click **Exit** to close the SWPM web UI. The sapinst process on the server terminates automatically.

The SAP NetWeaver 7.5 with SAP ASE installation is now complete. Proceed to the Post-installation verification and validation section to confirm all services are running correctly before using the system.

### Post-installation verification and validation
{: #post-install}

1. Verify SAP ASE processes are running

    Switch to the Sybase OS user and check running servers:

    ```bash
    # su - sybsfd
    $ showserver
    ```

    Expected output shows dataserver, and backupserver entries.
    From the OS level:

    ```bash
    # ps -ef | grep -E "dataserver|backupserver" | grep -v grep
    ```

    All three processes should be running under the sybsfd user.

    **Test database connectivity using isql:**

    ```bash
    **# su - sybsfd
    $ isql -S SFD -U sapsa -X
    Password: ********
    1> select @@version
    2> go
    ```

    Expected: SAP ASE version string is returned, for example:
    Adaptive Server Enterprise/16.0 SP04 PL06/...

    **Check the ASE error log for any startup errors:

    **$ cat $SYBASE/$SYBASE_ASE/install/SFD.log | tail -100
    Look for the line Recovery complete. which indicates a clean startup.

    2. Verify SAP NetWeaver instances are running

        Switch to the SAP admin user and check instance status:

        ```bash
        # su - sfdadm
        $ sapcontrol -nr 01 -function GetProcessList
        $ sapcontrol -nr 00 -function GetProcessList
        ```

        All listed processes should show GREEN or Running status.

        List all instances registered with the system:

        ```bash
        $ sapcontrol -nr 00 -function GetSystemInstanceList
        ```

        All listed processes should show GREEN or Running status.
        Start all instances if they are not yet running:

        ```bash
        $ startsap ALL
        ```

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
