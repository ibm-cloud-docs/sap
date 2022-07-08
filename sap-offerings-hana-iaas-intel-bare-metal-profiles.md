---

copyright:
  years: 2020
lastupdated: "2020-09-21"

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

# Intel Bare Metal server certified profiles on Classic infrastructure for SAP HANA
{: #hana-iaas-offerings-profiles-intel-bm}

## Profiles list
{: #hana-iaas-intel-bm-list}

The published names are subject to change.
{: note}

This table provides an overview of the SAP-certified profiles with Intel Bare Metal:

| **Profile** | **CPU Cores** | **CPU Threads (aka. vCPU)** | **Memory (RAM GB)** | **SAPS** | **SAP HANA Processing Type** |
| --- | --- | --- | --- | --- | --- |
| [BI.S3.H2.192 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13475&packageId=1045&presetId=823){: external} | 36 | 72 | 192 GB | 78,850 | OLAP/OLTP |
| [BI.S3.H2.384 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13475&packageId=1045&presetId=825){: external} | 36 | 72 | 384 GB | 79,430 | OLAP/OLTP |
| [BI.S3.H2.768 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13475&packageId=1045&presetId=827){: external} | 36 | 72 | 768 GB | 79,630 | OLAP/OLTP |
| [BI.S4.H2.192 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13475&packageId=1109&presetId=1087){: external} | 32 | 64 | 192 GB | 82,470 | OLAP/OLTP |
| [BI.S4.H2.384 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13475&packageId=1109&presetId=1091){: external} | 32 | 64 | 384 GB | 85,130 | OLAP/OLTP |
| [BI.S4.H2.768 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13475&packageId=1109&presetId=1095){: external} | 40 | 80 | 768 GB | 112,830 | OLAP/OLTP |
| [BI.S4.H2.1500 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13467&packageId=1109&presetId=1099){: external} | 56 | 112 | 1536 GB | 147,220 | OLAP/OLTP |
| [BI.S4.H2.3000 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13467&packageId=1109&presetId=1103){: external} | 56 | 112 | 3072 GB | 135,127 | OLAP/OLTP |
| [BI.S4.H4.3000 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13467&packageId=1117&presetId=1117){: external} | 112 | 224 | 3072 GB | 285,970 | OLAP/OLTP |
| [BI.S4.H4.6000 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13467&packageId=1117&presetId=1113){: external} | 112 | 224 | 6144 GB | 285,970 | OLAP/OLTP |
| [BI.S4.H8.6000 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13467&packageId=1119&presetId=1121){: external} | 224 | 448 | 6144 GB | 550,670 | OLAP/OLTP | 
| [BI.S4.H8.12000 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13467&packageId=1119&presetId=1129){: external} | 224 | 448 | 12288 GB | 550,670 | OLAP/OLTP |
{: caption="Table 1. SAP HANA servers" caption-side="top"}

For more information see [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097){: external}.

A number of these Profiles are also available as "Boot-only" which have the same configuration but only the boot drives are inserted. This configuration lets you customize the Local SSD Storage and the attachment of either Network Block or File storage options.

## Understanding Bare Metal profile names
{: #hana-iaas-intel-bm-names}

The Bare Metal profile names are contextual and sequential.  This example uses an SAP HANA certified server to list and describe the naming conventions:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| BI.S3.H8401 | BI | Bluemix Infrastructure (former name of {{site.data.keyword.cloud_notm}}) |
| | S2 | Series 2 (processor generation)<br/><ul><li>S2 is Intel Broadwell</li><li>S3 is Intel Skylake or Kaby Lake</li><li>S4 is Intel Cascade Lake</li></ul> |
| | H | HANA-certified server |
| | 8 | 8-socket server |
| | 4 | 4 TB RAM |
| | 01 | Revision number (00 is launch, 01 is first revision, and other revisions) |
| | A or B | Available as appliances with preconfigured built-in disks (A) or as boot servers only (B) |
{: caption="Table 2. Profile naming for SAP HANA" caption-side="top"}


## Profiles available on Hourly Consumption Billing
{: #hana-iaas-intel-bm-hourly}

The following Bare Metal servers are available on **Hourly** Consumption Billing:
- BI.S3.H2.192
- BI.S3.H2.384
- BI.S4.H2.192 Appliance
- BI.S4.H2.384 Appliance
- BI.S4.H2.768 Appliance
- BI.S4.H2.1500 Appliance
- BI.S4.H2.3000 Appliance
- BI.S4.H2.192 (boot only)
- BI.S4.H2.384 (boot only)
- BI.S4.H2.768 (boot only)
- BI.S4.H2.1500 (boot only)
- BI.S4.H2.3000 (boot only)


## Storage specifications
{: #hana-iaas-intel-bm-storage-specs}

The following dual-socket servers are available in the Appliance delivery model for SAP HANA, with preconfigured disk storage.

These Profiles do not use any Network Block or File storage options that are mounted to the Bare Metal server. These Profiles use only Local SSD Storage devices that are physically attached through a RAID Controller inside the server.
{: note}


### BI.S3.H2.192 Appliance
{: #hana-iaas-intel-bm-s3-h2-192gb}

Link to Profile: [BI.S3.H2.192 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13475&packageId=1045&presetId=823){: external}

#### Physical Disk and RAID Configuration
{: #hana-iaas-intel-bm-s3-h2-192gb-physical}

| RAID | Components | Drives | Array | Total Capacity |
| --- | --- | --- | --- | --- |
| RAID 1 | 2x 960 GB 5100 | `hdd0, hdd1` | RAID1-A | 960 GB |
| RAID 1 | 2x 960 GB 5100 | `hdd2, hdd3` | RAID1-B | 960 GB |
| Global hot spare | 1x 960 GB 5100 | `hdd4` |  |  |

#### Disk mount points and Partitions
{: #hana-iaas-intel-bm-s3-h2-192gb-logical}

| Array | Partition | Name | Size (GB) |
| --- | --- | --- | --- |
| RAID1-A | `/dev/sda` |   |  |
|   | `/dev/sda1` | `/boot` | 50 |
|   | `/dev/sda2` | `/` | 150 |
|   | `/dev/sda3` | `/usr/sap` | 150 |
|   | `/dev/sda4` | `/hana/log` |  |
| RAID1-B | `/dev/sdb` |   |  |
|   | `/dev/sdb1` | `/hana/shared` | 250 |
|   | `/dev/sdb2` | `/hana/data` | _remaining capacity_ |


### BI.S3.H2.384 Appliance
{: #hana-iaas-intel-bm-s3-h2-382gb}

Link to Profile: [BI.S3.H2.384 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13475&packageId=1045&presetId=825){: external}

#### Physical Disk and RAID Configuration
{: #hana-iaas-intel-bm-s3-h2-382gb-physical}

| RAID | Components | Drives | Array | Total Capacity |
| --- | --- | --- | --- | --- |
| RAID 1 | 2x 960 GB 5100 |`hdd0, hdd1` | RAID1-A | 960 GB |
| RAID 10 | 4x 960 GB 5100 | `hdd2, hdd3, hdd4, hdd5` | RAID1-B | 1920 GB |
| Global hot spare | 1x 960 GB 5100 | `hdd6` |  |  |

#### Disk mount points and Partitions
{: #hana-iaas-intel-bm-s3-h2-382gb-logical}

| Array | Partition | Name | Size (GB) |
| --- | --- | --- | --- |
| RAID1-A | `/dev/sda` |   |  |
|   | `/dev/sda1` | `/boot` | 50 |
|   | `/dev/sda2` | `/` | 150 |
|   | `/dev/sda3` | `/usr/sap` | 150 |
|   | `/dev/sda4` | `/hana/log` | _remaining capacity_ |
| RAID1-B | `/dev/sdb` |   |  |
|   | `/dev/sdb1` | `/hana/shared` | 500 |
|   | `/dev/sdb2` | `/hana/data` | _remaining capacity_ |


### BI.S3.H2.768 Appliance
{: #hana-iaas-intel-bm-s3-h2-768gb}

Link to Profile: [BI.S3.H2.768 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13475&packageId=1045&presetId=827){: external}

#### Physical Disk and RAID Configuration
{: #hana-iaas-intel-bm-s3-h2-768gb-physical}

| RAID | Components | Drives | Array | Total Capacity |
| --- | --- | --- | --- | --- |
| RAID 1 | 2x 960 GB 5100 |`hdd0, hdd1` | RAID1-A | 960 GB |
| RAID 10 | 4x 960 GB 5100 | `hdd2, hdd3, hdd4, hdd5` | RAID1-B | 1920 GB |
| Global hot spare | 1x 960 GB 5100 | `hdd6` |  |  |

#### Disk mount points and Partitions
{: #hana-iaas-intel-bm-s3-h2-768gb-logical}

| Array | Partition | Name | Size (GB) |
| --- | --- | --- | --- |
| RAID1-A | `/dev/sda` |   |  |
|   | `/dev/sda1` | `/boot` | 50 |
|   | `/dev/sda2` | `/` | 150 |
|   | `/dev/sda3` | `/usr/sap` | 150 |
|   | `/dev/sda4` | `/hana/log` | _remaining capacity_ |
| RAID1-B | `/dev/sdb` |   |  |
|   | `/dev/sdb1` | `/hana/shared` | 800 |
|   | `/dev/sdb2` | `/hana/data` | _remaining capacity_ |


### BI.S4.H2.192 Appliance
{: #hana-iaas-intel-bm-s4-h2-192gb}

Link to Profile: [BI.S4.H2.192 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13475&packageId=1109&presetId=1087){: external}

#### Physical Disk and RAID Configuration
{: #hana-iaas-intel-bm-s4-h2-192gb-physical}

| RAID | Components | Drives | Array | Total Capacity |
| --- | --- | --- | --- | --- |
| RAID 1 | 2x 960 GB 5100 | `hdd0, hdd1` | RAID1-A | 960 GB |
| RAID 1 | 2x 960 GB 5100 | `hdd2, hdd3` | RAID1-B | 960 GB |
| Global hot spare | 1x 960 GB 5100 | `hdd4` |  |  |

#### Disk mount points and Partitions
{: #hana-iaas-intel-bm-s4-h2-192gb-logical}

| Array | Partition | Name | Size (GB) |
| --- | --- | --- | --- |
| RAID1-A | `/dev/sda` |   |  |
|   | `/dev/sda1` | `/boot` | 50 |
|   | `/dev/sda2` | `/` | 150 |
|   | `/dev/sda3` | `/usr/sap` | 150 |
|   | `/dev/sda4` | `/hana/log` | _remaining capacity_ |
| RAID1-B | `/dev/sdb` |   |  |
|   | `/dev/sdb1` | `/hana/shared` | 250 |
|   | `/dev/sdb2` | `/hana/data` | _remaining capacity_ |


### BI.S4.H2.384 Appliance
{: #hana-iaas-intel-bm-s4-h2-384gb}

Link to Profile: [BI.S4.H2.384 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13475&packageId=1109&presetId=1091){: external}

#### Physical Disk and RAID Configuration
{: #hana-iaas-intel-bm-s4-h2-384gb-physical}

| RAID | Components | Drives | Array | Total Capacity |
| --- | --- | --- | --- | --- |
| RAID 1 | 2x 960 GB 5100 |`hdd0, hdd1` | RAID1-A | 960 GB |
| RAID 10 | 4x 960 GB 5100 | `hdd2, hdd3, hdd4, hdd5` | RAID1-B | 1920 GB |
| Global hot spare | 1x 960 GB 5100 | `hdd6` |  |  |

#### Disk mount points and Partitions
{: #hana-iaas-intel-bm-s4-h2-384gb-logical}

| Array | Partition | Name | Size (GB) |
| --- | --- | --- | --- |
| RAID1-A | `/dev/sda` |   |  |
|   | `/dev/sda1` | `/boot` | 50 |
|   | `/dev/sda2` | `/` | 150 |
|   | `/dev/sda3` | `/usr/sap` | 150 |
|   | `/dev/sda4` | `/hana/log` | _remaining capacity_ |
| RAID1-B | `/dev/sdb` |   |  |
|   | `/dev/sdb1` | `/hana/shared` | 500 |
|   | `/dev/sdb2` | `/hana/data` | _remaining capacity_ |


### BI.S4.H2.768 Appliance
{: #hana-iaas-intel-bm-s4-h2-768gb}

Link to Profile: [BI.S4.H2.768 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13475&packageId=1109&presetId=1095){: external}

#### Physical Disk and RAID Configuration
{: #hana-iaas-intel-bm-s4-h2-768gb-physical}

| RAID | Components | Drives | Array | Total Capacity |
| --- | --- | --- | --- | --- |
| RAID 1 | 2x 960 GB 5100 |`hdd0, hdd1` | RAID1-A | 960 GB |
| RAID 10 | 4x 960 GB 5100 | `hdd2, hdd3, hdd4, hdd5` | RAID1-B | 1920 GB |
| Global hot spare | 1x 960 GB 5100 | `hdd6` |  |  |

#### Disk mount points and Partitions
{: #hana-iaas-intel-bm-s4-h2-768gb-logical}

| Array | Partition | Name | Size (GB) |
| --- | --- | --- | --- |
| RAID1-A | `/dev/sda` |   |  |
|   | `/dev/sda1` | `/boot` | 50 |
|   | `/dev/sda2` | `/` | 150 |
|   | `/dev/sda3` | `/usr/sap` | 150 |
|   | `/dev/sda4` | `/hana/log` | _remaining capacity_ |
| RAID1-B | `/dev/sdb` |   |  |
|   | `/dev/sdb1` | `/hana/shared` | 800 |
|   | `/dev/sdb2` | `/hana/data` | _remaining capacity_ |


### BI.S4.H2.1500 Appliance
{: #hana-iaas-intel-bm-s4-h2-1500gb}

Link to Profile: [BI.S4.H2.1500 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13467&packageId=1109&presetId=1099){: external}

#### Physical Disk and RAID Configuration
{: #hana-iaas-intel-bm-s4-h2-1500gb-physical}

| RAID | Components | Drives | Array | Total Capacity |
| --- | --- | --- | --- | --- |
| RAID 1 | 2x 960 GB SSD SED |`hdd0, hdd1` | RAID1-A | 960 GB |
| RAID 1 | 2x 3.8 TB SSD SED | `hdd2, hdd3` | RAID1-B | 3.8 TB |
| Global hot spare | 1x 3.8 TB SSD SED | `hdd4` |  |  |

#### Disk mount points and Partitions
{: #hana-iaas-intel-bm-s4-h2-1500gb-logical}

| Array | Partition | Name | Size (GB) |
| --- | --- | --- | --- |
| RAID1-A | `/dev/sda` |   |  |
|   | `/dev/sda1` | `/boot` | 50 |
|   | `/dev/sda2` | `/` | 150 |
|   | `/dev/sda3` | `/usr/sap` | 150 |
|   | `/dev/sda4` | `/hana/log` | _remaining capacity_ |
| RAID1-B | `/dev/sdb` |   |  |
|   | `/dev/sdb1` | `/hana/shared` | 1024 |
|   | `/dev/sdb2` | `/hana/data` | _remaining capacity_ |


### BI.S4.H2.3000 Appliance
{: #hana-iaas-intel-bm-s4-h2-3000gb}

Link to Profile: [BI.S4.H2.3000 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13467&packageId=1109&presetId=1103){: external}

#### Physical Disk and RAID Configuration
{: #hana-iaas-intel-bm-s4-h2-3000gb-physical}

| RAID | Components | Drives | Array | Total Capacity |
| --- | --- | --- | --- | --- |
| RAID 1 | 2x 960 GB SSD SED |`hdd0, hdd1` | RAID1-A | 960 GB |
| RAID 10 | 4x 3.8 TB SSD SED | `hdd2, hdd3, hdd4, hdd5` | RAID1-B | 7.6 TB |
| Global hot spare | 1x 3.8 TB SSD SED | `hdd6` |  |  |

#### Disk mount points and Partitions
{: #hana-iaas-intel-bm-s4-h2-3000gb-logical}

| Array | Partition | Name | Size (GB) |
| --- | --- | --- | --- |
| RAID1-A | `/dev/sda` |   |  |
|   | `/dev/sda1` | `/boot` | 50 |
|   | `/dev/sda2` | `/` | 150 |
|   | `/dev/sda3` | `/usr/sap` | 150 |
|   | `/dev/sda4` | `/hana/log` | _remaining capacity_ |
| RAID1-B | `/dev/sdb` |   |  |
|   | `/dev/sdb1` | `/hana/shared` | 1024 |
|   | `/dev/sdb2` | `/hana/data` | _remaining capacity_ |


### BI.S4.H4.3000 Appliance
{: #hana-iaas-intel-bm-s4-h4-192gb}

Link to Profile: [BI.S4.H4.3000 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13467&packageId=1117&presetId=1117){: external}

#### Physical Disk and RAID Configuration
{: #hana-iaas-intel-bm-s4-h4-192gb-physical}

| RAID | Components | Drives | Array | Total Capacity |
| --- | --- | --- | --- | --- |
| RAID 1 | 2x 960 GB SSD SED |`hdd0, hdd1` | RAID1-A | 960 GB |
| RAID 10 | 4x 3.8 TB SSD SED | `hdd2, hdd3, hdd4, hdd5` | RAID1-B | 7.6 TB |
| Global hot spare | 1x 3.8 TB SSD SED | `hdd6` |  |  |

#### Disk mount points and Partitions
{: #hana-iaas-intel-bm-s4-h4-192gb-logical}

| Array | Partition | Name | Size (GB) |
| --- | --- | --- | --- |
| RAID1-A | `/dev/sda` |   |  |
|   | `/dev/sda1` | `/boot` | 50 |
|   | `/dev/sda2` | `/` | 150 |
|   | `/dev/sda3` | `/usr/sap` | 150 |
|   | `/dev/sda4` | `/hana/log` | _remaining capacity_ |
| RAID1-B | `/dev/sdb` |   |  |
|   | `/dev/sdb1` | `/hana/shared` | 1024 |
|   | `/dev/sdb2` | `/hana/data` | _remaining capacity_ |


### BI.S4.H4.6000 Appliance
{: #hana-iaas-intel-bm-s4-h4-6000gb}


Link to Profile: [BI.S4.H4.6000 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13467&packageId=1117&presetId=1113){: external}

#### Physical Disk and RAID Configuration
{: #hana-iaas-intel-bm-s4-h4-6000gb-physical}

| RAID | Components | Drives | Array | Total Capacity |
| --- | --- | --- | --- | --- |
| RAID 1 | 2x 960 GB SSD SED |`hdd0, hdd1` | RAID1-A | 960 GB |
| RAID 10 | 4x 3.8 TB SSD SED | `hdd2, hdd3, hdd4, hdd5` | RAID1-B | 7.6 TB |
| Global hot spare | 1x 3.8 TB SSD SED | `hdd6` |  |  |

#### Disk mount points and Partitions
{: #hana-iaas-intel-bm-s4-h4-6000gb-logical}

| Array | Partition | Name | Size (GB) |
| --- | --- | --- | --- |
| RAID1-A | `/dev/sda` |   |  |
|   | `/dev/sda1` | `/boot` | 50 |
|   | `/dev/sda2` | `/` | 150 |
|   | `/dev/sda3` | `/usr/sap` | 150 |
|   | `/dev/sda4` | `/hana/log` | _remaining capacity_ |
| RAID1-B | `/dev/sdb` |   |  |
|   | `/dev/sdb1` | `/hana/shared` | 1024 |
|   | `/dev/sdb2` | `/hana/data` | _remaining capacity_ |


### BI.S4.H8.6000 Appliance
{: #hana-iaas-intel-bm-s4-h8-6000gb}

Link to Profile: [BI.S4.H8.6000 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13467&packageId=1119&presetId=1121){: external}

#### Physical Disk and RAID Configuration
{: #hana-iaas-intel-bm-s4-h8-6000gb-physical}

| RAID | Components | Drives | Array | Total Capacity |
| --- | --- | --- | --- | --- |
| RAID 1 | 2x 960 GB SSD SED |`hdd0, hdd1` | RAID1-A | 960 GB |
| RAID 10 | 4x 3.8 TB SSD SED | `hdd2, hdd3, hdd4, hdd5` | RAID1-B | 7.6 TB |
| Global hot spare | 1x 3.8 TB SSD SED | `hdd6` |  |  |

#### Disk mount points and Partitions
{: #hana-iaas-intel-bm-s4-h8-6000gb-logical}

| Array | Partition | Name | Size (GB) |
| --- | --- | --- | --- |
| RAID1-A | `/dev/sda` |   |  |
|   | `/dev/sda1` | `/boot` | 50 |
|   | `/dev/sda2` | `/` | 150 |
|   | `/dev/sda3` | `/usr/sap` | 150 |
|   | `/dev/sda4` | `/hana/log` | _remaining capacity_ |
| RAID1-B | `/dev/sdb` |   |  |
|   | `/dev/sdb1` | `/hana/shared` | 1024 |
|   | `/dev/sdb2` | `/hana/data` | _remaining capacity_ |


### BI.S4.H8.12000 Appliance
{: #hana-iaas-intel-bm-s4-h8-12000gb}

Link to Profile: [BI.S4.H8.12000 Appliance](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13467&packageId=1119&presetId=1129){: external}

#### Physical Disk and RAID Configuration
{: #hana-iaas-intel-bm-s4-h8-12000gb-physical}

| RAID | Components | Drives | Array | Total Capacity |
| --- | --- | --- | --- | --- |
| RAID 1 | 2x 960 GB SSD SED |`hdd0, hdd1` | RAID1-A | 960 GB |
| RAID 10 | 8x 3.8 TB SSD SED | `hdd2, hdd3, hdd4, hdd5, hdd6, hdd7, hdd8, hdd9` | RAID2-A | 15.2 TB |
| Global hot spare | 1x 3.8 TB SSD SED | `hdd10` |  |  |

#### Disk mount points and Partitions
{: #hana-iaas-intel-bm-s4-h8-12000gb-logical}

| Array | Partition | Name | Size (GB) |
| --- | --- | --- | --- |
| RAID1-A | `/dev/sda` |   |  |
|   | `/dev/sda1` | `/boot` | 50 |
|   | `/dev/sda2` | `/` | 150 |
|   | `/dev/sda3` | `/usr/sap` | 150 |
|   | `/dev/sda4` | `/hana/log` | _remaining capacity_ |
| RAID1-B | `/dev/sdb` |   |  |
|   | `/dev/sdb1` | `/hana/shared` | 1024 |
|   | `/dev/sdb2` | `/hana/data` | _remaining capacity_ |


## Boot-only servers
{: #hana-iaas-intel-bm-boot-only}

By default, {{site.data.keyword.cloud_notm}} {{site.data.keyword.baremetal_short}} come with high-performance and highly reliable internal storage based on solid-state disks and high-performance RAID adapters.

For projects with different requirements, such as storage snapshots, that don't have a very high through-put requirement, {{site.data.keyword.cloud_notm}} offers "boot-only servers."

Boot-only servers have the same configuration as standard {{site.data.keyword.baremetal_short}}, however, only the boot disks for the operating system are configured. The storage required for the SAP file systems - `/usr/sap`, `/hana/shared`, `/hana/data`, and `/hana/log` - is not provided, significantly reducing the server cost.

To run SAP HANA on a boot-only server, you must add {{site.data.keyword.cloud_notm}} storage to the server.

SAP HANA Tailored Data Center Integration (TDI) requirements must be met to run SAP HANA as Production for the SAP System/s, or in a production-like environment. To fulfill SAP HANA TDI key performance indicators (TDIs), storage performance must meet minimum values.

### {{site.data.keyword.filestorage_full_notm}} with Boot-only services
{: #hana-iaas-intel-bm-boot-only-file-storage}

To run SAP HANA in production and production-like setups that use {{site.data.keyword.filestorage_full_notm}}, based on the Network File System (NFS) protocol, requires:
- Minimum of 8 K IOPS for `/hana/log`
- Minimum of 7 K IOPS for `/hana/data`

You can share storage areas for `/hana/shared` with `/hana/log` or `/hana/data` if these areas are too large for your particular use case.

For non-production use, you can use one storage area for `/hana/shared`, `/hana/log`, and `/hana/data`.

These minimums are for either _Endurance_ or _Performance_ options available with {{site.data.keyword.filestorage_full_notm}}: 
- Endurance storage is a predefined IOPS per GB of storage, for example, 0.25 up to 10 IOPS per GB.
- Performance storage has different ranges of IOPS per GB, depending on the total size of the storage device. This option is more customizable.

This table compares the file storage (using NFS protocol) and the performance IOPS range for the capacity:

| File Storage capacity (GB) | Performance custom IOPS |
| --- | --- |
| 20-39 | 100-1,000 |
| 40-79 | 100-2,000 |
| 80-99 | 100-4,000 |
| 100-499 | 100-6,000 |
| 500-999 | 100-10,000 |
| 1,000-1,999 | 100-20,000 |
| 2,000-2,999 | 200-40,000 |
| 3,000-3,999 | 200-48,000 |
| 4,000-7,999 | 300-48,000 |
| 8,000-9,999 | 500-48,000 |
| 10,000-12,000 | 1,000-48,000 |
| Up to 24,000 | Up to 96,000 [by special request](/docs/FileStorage?topic=FileStorage-expandCapacity#increasecapacityover12TB) |
| Up to 18,000 | Up to 180,000 [by special request](/docs/FileStorage?topic=FileStorage-expandCapacity#increasecapacityover12TB) |
{: caption="Table 3. {{site.data.keyword.filestorage_full_notm}} Performance storage GB and IOPS" caption-side="top"}


### BI.S4.H2.192 (boot only)
{: #hana-iaas-intel-bm-s4-h2-192gb-boot-only}

Link to Profile: [BI.S4.H2.192 (boot only)](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13467&packageId=1109&presetId=1089){: external}

#### Required configuration of disk mount points and Partitions
{: #hana-iaas-intel-bm-s4-h2-192gb-boot-only-logical}

| Array | Partition | Name | Size (GB) |
| --- | --- | --- | --- |
| RAID1-A | `/dev/sda` |   |  |
|   | `/dev/sda1` | `/boot` | 50 |
|   | `/dev/sda2` | `/` | 150 |
|   | `/dev/sda3` | `/usr/sap` | 150 |
|   | `/dev/sda4` | `/hana/log` | _remaining capacity_ |
| RAID1-B | `/dev/sdb` |   |  |
|   | `/dev/sdb1` | `/hana/shared` | 250 |
|   | `/dev/sdb2` | `/hana/data` | _remaining capacity_ |


### BI.S4.H2.384 (boot only)
{: #hana-iaas-intel-bm-s4-h2-384gb-boot-only}

Link to Profile: [BI.S4.H2.384 (boot only)](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13467&packageId=1109&presetId=1093){: external}

#### Required configuration of disk mount points and Partitions
{: #hana-iaas-intel-bm-s4-h2-384gb-boot-only-logical}

| Array | Partition | Name | Size (GB) |
| --- | --- | --- | --- |
| RAID1-A | `/dev/sda` |   |  |
|   | `/dev/sda1` | `/boot` | 50 |
|   | `/dev/sda2` | `/` | 150 |
|   | `/dev/sda3` | `/usr/sap` | 150 |
|   | `/dev/sda4` | `/hana/log` | _remaining capacity_ |
| RAID1-B | `/dev/sdb` |   |  |
|   | `/dev/sdb1` | `/hana/shared` | 500 |
|   | `/dev/sdb2` | `/hana/data` | _remaining capacity_ |


### BI.S4.H2.768 (boot only)
{: #hana-iaas-intel-bm-s4-h2-768gb-boot-only}

Link to Profile: [BI.S4.H2.768 (boot only)](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13467&packageId=1109&presetId=1097){: external}

#### Required configuration of disk mount points and Partitions
{: #hana-iaas-intel-bm-s4-h2-768gb-boot-only-logical}

| Array | Partition | Name | Size (GB) |
| --- | --- | --- | --- |
| RAID1-A | `/dev/sda` |   |  |
|   | `/dev/sda1` | `/boot` | 50 |
|   | `/dev/sda2` | `/` | 150 |
|   | `/dev/sda3` | `/usr/sap` | 150 |
|   | `/dev/sda4` | `/hana/log` | _remaining capacity_ |
| RAID1-B | `/dev/sdb` |   |  |
|   | `/dev/sdb1` | `/hana/shared` | 800 |
|   | `/dev/sdb2` | `/hana/data` | _remaining capacity_ |


### BI.S4.H2.1500 (boot only)
{: #hana-iaas-intel-bm-s4-h2-1500gb-boot-only}

Link to Profile: [BI.S4.H2.1500 (boot only)](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13467&packageId=1109&presetId=1101){: external}

#### Required configuration of disk mount points and Partitions
{: #hana-iaas-intel-bm-s4-h2-1500gb-boot-only-logical}

| Array | Partition | Name | Size (GB) |
| --- | --- | --- | --- |
| RAID1-A | `/dev/sda` |   |  |
|   | `/dev/sda1` | `/boot` | 50 |
|   | `/dev/sda2` | `/` | 150 |
|   | `/dev/sda3` | `/usr/sap` | 150 |
|   | `/dev/sda4` | `/hana/log` | _remaining capacity_ |
| RAID1-B | `/dev/sdb` |   |  |
|   | `/dev/sdb1` | `/hana/shared` | 1024 |
|   | `/dev/sdb2` | `/hana/data` | _remaining capacity_ |


### BI.S4.H2.3000 (boot only)
{: #hana-iaas-intel-bm-s4-h2-3000gb-boot-only}

Link to Profile: [BI.S4.H2.3000 (boot only)](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=13467&packageId=1109&presetId=1105){: external}

#### Required configuration of disk mount points and Partitions
{: #hana-iaas-intel-bm-s4-h2-3000gb-boot-only-logical}

| Array | Partition | Name | Size (GB) |
| --- | --- | --- | --- |
| RAID1-A | `/dev/sda` |   |  |
|   | `/dev/sda1` | `/boot` | 50 |
|   | `/dev/sda2` | `/` | 150 |
|   | `/dev/sda3` | `/usr/sap` | 150 |
|   | `/dev/sda4` | `/hana/log` | _remaining capacity_ |
| RAID1-B | `/dev/sdb` |   |  |
|   | `/dev/sdb1` | `/hana/shared` | 1024 |
|   | `/dev/sdb2` | `/hana/data` | _remaining capacity_ |
