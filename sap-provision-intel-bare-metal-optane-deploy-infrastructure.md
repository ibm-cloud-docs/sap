---

copyright:
  years: 2020
lastupdated: "2021-05-05"

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
{: #bm-optane-set-up-infrastructure}

## Before you begin
{: #bm-optane-before-you-set-up-infrastructure}

Determine the amount of memory that you need for your application. 

## Step 1: Provision your server
{: #bm-optane-order-server}

Order your Bare Metal SAP HANA server using the steps in [Deploying your infrastructure](/docs/sap?topic=sap-bm-set-up-infrastructure). Select the profile for the amount of PMem that you need for your application. 

## Step 2: Post-Provisioning Intel Optane PMem
{: #bm-optane-post-provisioning}

After you order your Bare Metal server and the server is provisioned, you complete the provisioning by creating namespaces for your system.

PMem on Bare Metal supports the App Dir mode. In App Dir mode, PMem and DRAM DIMMs act as independent memory resources that are directly accessed by the applications. 

The App Dir mode uses regions, and namespaces to represent persistent memory devices in an interleaved set. A region represents the physical persistent memory devices. A region is made up of one or more namespaces. A namespace represents a unit of storage that can be used for input/output (I/O).

Regions are created as part of the provisioning process. You must create the namespaces, create mount points, and mount the PMem devices.

For more information about regions and namespaces, see [Intel Optane Persistent Memory and SAP HANA Platform Configuration](https://www.sap.com/documents/2019/05/7a278c36-507d-0010-87a3-c30de2ffd8ff.html).

1. Log in to the Bare Metal server as **root**. 

1. Download the current version of the zypper (for SLES) or yum (for RHEL).

1.  Install the ndctl software for your system:
    *	For SLES:

        $ **zypper in ndctl**

            Refreshing service 
            'SMT-https_susesapsmtamr_service_networklayer_com'.
            Loading repository data...
            Reading installed packages...
            Resolving package dependencies...
            The following 2 NEW packages are going to be installed:
            libndctl6 ndctl

    *	For RHEL:

        $ **yum install ndctl**

            Refreshing service 
            'SMT-https_susesapsmtamr_service_networklayer_com'.
            Loading repository data...
            Reading installed packages...
            Resolving package dependencies...

1.  List the available regions for your system:
    
      $ **ndctl list -R -v**
      
          [
          {
              "dev":"region1",
              "size":1623497637888,
              "available_size":1623497637888,
              "max_available_extent":1623497637888,
              "type":"pmem",
              "numa_node":1,
              "target_node":3,
              "iset_id":-7847382400914477876,
              "persistence_domain":"memory_controller"
          },
          {
              "dev":"region0",
              "size":1623497637888,
              "available_size":1623497637888,
              "max_available_extent":1623497637888,
              "type":"pmem",
              "numa_node":0,
              "target_node":2,
              "iset_id":9195364489516887244,
              "persistence_domain":"memory_controller"
          }
      

1. Create a namespace for each of your regions.  
    
      $ **ndctl create-namespace -r region0**

          {
          "dev":"namespace0.0",
          "mode":"fsdax",
          "map":"dev",
          "size":"1488.37 GiB (1598.13 GB)",
          "uuid":"75af4e28-1b99-4d34-a39a-a342a370232a",
          "sector_size":512,
          "align":2097152,
          "blockdev":"pmem0"
          }
    
    Repeat this command for region1 

1. Create an xfs on-top of the PMem devices:

    $ **mkfs -t xfs -f /dev/pmem0**

        meta-data=/dev/pmem0             isize=512    agcount=4, agsize=97542016 blks
                  =                       sectsz=4096  attr=2, projid32bit=1
                  =                       crc=1        finobt=1, sparse=0, rmapbt=0
                  =                       reflink=0
          data    =                       bsize=4096  blocks=390168064, imaxpct=5
                  =                       sunit=0      swidth=0 blks
        naming   =version 2              bsize=4096  ascii-ci=0, ftype=1
          log      =internal log           bsize=4096 blocks=190511, version=2
                  =                       sectsz=4096  sunit=1 blks, lazy-count=1
        realtime =none                   extsz=4096   blocks=0, rtextents=0

    Repeat this command for pmem1.

1. Mount the file system that you created by adding the following lines to ``/etc/fstab``:

   /dev/pmem0 /hana/pmem/nvmem0 xfs dax 0 0
   
   /dev/pmem1 /hana/pmem/nvmem1 xfs dax 0 0
  

1. Create the path to the device.

    $ **mkdir -p /hana/pmem/nvmem0**

    $ **mkdir -p /hana/pmem/nvmem1**

    $ **mount -a**
  

1. Verify the devices.

   $ **df -h | grep pmem**

        /dev/pmem0      1.5T  1.6G  1.5T   1% /hana/pmem/nvmem0
        /dev/pmem1      1.5T  1.6G  1.5T   1% /hana/pmem/nvmem1
  
