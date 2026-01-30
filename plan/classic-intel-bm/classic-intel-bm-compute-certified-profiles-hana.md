---
copyright:
  years: 2020, 2026
lastupdated: "2026-01-29"
keywords: SAP-Certified HANA profiles, Classic, log, data, shared, sapphire rapids, Skylake, Cascade lake, boot only, bare metal, nvme, ssd
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# SAP HANA certified instances on Intel-powered bare metal servers in Classic Infrastructure
{: #hana-iaas-offerings-profiles-classic-intel-bm}

A SAP HANA certified server or profile on Intel-powered bare metal server in Classic Infrastructure defines attributes, such as physical CPU cores and RAM, which determine the size and performance capabilities of the server.


## Profile naming convention
{: #naming-convention}

Provision SAP HANA on Intel-powered bare metal server in Classic Infrastructure by selecting one of the certified profiles.

SAP HANA profile names follow a contextual and sequential naming convention.
The following table illustrates an example of an SAP HANA certified server profile:


| Profile name | Naming convention component | Description                                                                                                        |
| ------------ | --------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| BI.S4.H2.1500| BI                          | {{site.data.keyword.cloud_notm}} Infrastructure                                                                    |
|              | S4                          | Series 4 (CPU generation)\n* S3: Intel Skylake/Kaby Lake\n* S4: Intel Cascade Lake\n* S5: Intel Sapphire Rapids    |
|              | H                           | HANA-certified server                                                                                              |
|              | 2                           | 2-socket server                                                                                                    |
|              | 1500                        | 1500 GiB RAM                                                                                                       |
{: caption="Profile naming scheme for SAP HANA" caption-side="bottom"}

Refer to [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external} for up-to-date information on certified SAP HANA profiles.
{: note}


## Profiles hosted on Intel Sapphire Rapids CPU
{: #sr-profiles}

The following table lists available profile families for Intel Sapphire Rapids processor-based servers:

These profiles do not use any network block or file storage options that are mounted to the bare metal server. These profiles use only local NVMe SSD storage devices that are physically attached through a RAID controller inside the server.
{: note}

| **Profile name**           | **CPU cores** | **Virtual CPUs** | **Memory (GiB)**  |
| ---------------------------| ------------- | ---------------- | ---------------- |
| BI.S5.H2.1000 Appliance    | 96            | 192              | 1,024            |
| BI.S5.H2.2001 Appliance    | 96            | 192              | 2,048            |
| BI.S5.H2.2000 Appliance    | 120           | 240              | 2,048            |
| BI.S5.H2.3000 Appliance    | 120           | 240              | 3,072            |
| BI.S5.H2.4000 Appliance    | 120           | 240              | 4,096            |
{: class="simple-tab-table"}
{: tab-group="s5-profiles"}
{: caption="Intel Sapphire Rapids profiles for SAP HANA" caption-side="bottom"}
{: #sr-profiles-table}
{: tab-title="Certified Profiles"}

| **Profile name**           | **Workload type** | **SAPS** |
| ---------------------------| ----------------- | -------- |
| BI.S5.H2.1000 Appliance    | OLTP/OLAP         | 297,370  |
| BI.S5.H2.2001 Appliance    | OLTP/OLAP         | 297,370  |
| BI.S5.H2.2000 Appliance    | OLTP/OLAP         | 322,550  |
| BI.S5.H2.3000 Appliance    | OLTP/OLAP         | 327,295  |
| BI.S5.H2.4000 Appliance    | OLTP/OLAP         | 329,350  |
{: class="simple-tab-table"}
{: tab-group="s5-profiles"}
{: caption="Intel Sapphire Rapids profiles for SAP HANA" caption-side="bottom"}
{: #sr-sizing}
{: tab-title="Sizing"}

| **Profile name**           | **Storage Array RAID 1\n(Boot + Log)** | **Storage Array RAID 10\n(Shared + Data)**  |
| ---------------------------|----------------------------------------|---------------------------------------------|
| BI.S5.H2.1000 Appliance    | 2 x 480 GB                             | 5 x 3.2 TB                                  |
| BI.S5.H2.2001 Appliance    | 2 x 480 GB                             | 7 x 3.2 TB                                  |
| BI.S5.H2.2000 Appliance    | 2 x 480 GB                             | 7 x 3.2 TB                                  |
| BI.S5.H2.3000 Appliance    | 2 x 480 GB                             | 5 x 6.4 TB                                  |
| BI.S5.H2.4000 Appliance    | 2 x 480 GB                             | 7 x 6.4 TB                                  |
{: class="simple-tab-table"}
{: tab-group="s5-profiles"}
{: caption="Intel Sapphire Rapids profiles for SAP HANA" caption-side="bottom"}
{: #sr-storage}
{: tab-title="Storage"}


## Profiles hosted on Intel Cascade Lake CPU
{: #cl-profiles}

The following table lists available profile families for Intel Cascade Lake processor-based servers:

These profiles do not use any network block or file storage options that are mounted to the bare metal server. These profiles use only local SSD storage devices that are physically attached through a RAID controller inside the server.
{: note}

| **Profile name**           | **CPU cores** | **Virtual CPUs** | **Memory (GiB)**  |
| ---------------------------| ------------- | ---------------- | ---------------- |
| BI.S4.H2.192 Appliance     | 32            | 64               | 192              |
| BI.S4.H2.384 Appliance     | 32            | 64               | 384              |
| BI.S4.H2.384_v2 Appliance  | 16            | 32               | 384              |
| BI.S4.H2.768 Appliance     | 40            | 80               | 768              |
| BI.S4.H2.768_v2 Appliance  | 48            | 96               | 768              |
| BI.S4.H2.1500 Appliance    | 56            | 112              | 1,536            |
| BI.S4.H2.3000 Appliance    | 56            | 112              | 3,072            |
| BI.S4.H4.3000 Appliance    | 112           | 224              | 3,072            |
| BI.S4.H4.6000 Appliance    | 112           | 224              | 6,144            |
| BI.S4.H8.6000 Appliance    | 224           | 448              | 6,144            |
| BI.S4.H8.12000 Appliance   | 224           | 448              | 12,288           |
{: class="simple-tab-table"}
{: tab-group="s4-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA" caption-side="bottom"}
{: #cl-profiles-table}
{: tab-title="Certified Profiles"}

| **Profile name**           | **Workload type**          | **SAPS** |
| ---------------------------| -------------------------- | -------- |
| BI.S4.H2.192 Appliance     | OLTP/OLAP/SAP Business One | 82,470   |
| BI.S4.H2.384 Appliance     | OLTP/OLAP/SAP Business One | 85,130   |
| BI.S4.H2.384_v2 Appliance  | OLTP/OLAP/SAP Business One | 60,420   |
| BI.S4.H2.768 Appliance     | OLTP/OLAP/SAP Business One | 112,830  |
| BI.S4.H2.768_v2 Appliance  | OLTP/OLAP/SAP Business One | 124,620  |
| BI.S4.H2.1500 Appliance    | OLTP/OLAP                  | 147,220  |
| BI.S4.H2.3000 Appliance    | OLTP/OLAP                  | 135,127  |
| BI.S4.H4.3000 Appliance    | OLTP/OLAP                  | 285,970  |
| BI.S4.H4.6000 Appliance    | OLTP/OLAP                  | 285,970  |
| BI.S4.H8.6000 Appliance    | OLTP/OLAP                  | 550,670  |
| BI.S4.H8.12000 Appliance   | OLTP/OLAP                  | 550,670  |
{: class="simple-tab-table"}
{: tab-group="s4-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA" caption-side="bottom"}
{: #cl-sizing}
{: tab-title="Sizing"}

| **Profile name**           | **Storage Array RAID 1\n(Boot + Log)** | **Storage Array RAID 10\n(Shared + Data)**  |**Global hot spare** |
| ---------------------------|----------------------------------------|---------------------------------------------|---------------------|
| BI.S4.H2.192 Appliance     |  2 x 960 GB                            | 2 x 960 GB                                  | 1 x 960 GB          |
| BI.S4.H2.384 Appliance     |  2 x 960 GB                            | 4 x 960 GB                                  | 1 x 960 GB          |
| BI.S4.H2.384_v2 Appliance  |  2 x 960 GB                            | 4 x 960 GB                                  | 1 x 960 GB          |
| BI.S4.H2.768 Appliance     |  2 x 960 GB                            | 4 x 960 GB                                  | 1 x 960 GB          |
| BI.S4.H2.768_v2 Appliance  |  2 x 960 GB                            | 4 x 960 GB                                  | 1 x 960 GB          |
| BI.S4.H2.1500 Appliance    |  2 x 960 GB                            | 2 x 3.8 TB                                  | 1 x 3.8 TB          |
| BI.S4.H2.3000 Appliance    |  2 x 960 GB                            | 4 x 3.8 TB                                  | 1 x 3.8 TB          |
| BI.S4.H4.3000 Appliance    |  2 x 960 GB                            | 4 x 3.8 TB                                  | 1 x 3.8 TB          |
| BI.S4.H4.6000 Appliance    |  2 x 960 GB                            | 4 x 3.8 TB                                  | 1 x 3.8 TB          |
| BI.S4.H8.6000 Appliance    |  2 x 960 GB                            | 4 x 3.8 TB                                  | 1 x 3.8 TB          |
| BI.S4.H8.12000 Appliance   |  2 x 960 GB                            | 8 x 3.8 TB                                  | 1 x 3.8 TB          |
{: class="simple-tab-table"}
{: tab-group="s4-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA" caption-side="bottom"}
{: #cl-storage}
{: tab-title="Storage"}


## Profiles hosted on Intel Cascade Lake CPU with Intel Optane PMem
{: #cl-optane-profiles}

The following table lists available profile families for Intel Cascade Lake processor-based servers **with Intel Optane Persistent Memory (PMem)PMem**:

These profiles do not use any network block or file storage options that are mounted to the bare metal server. These profiles use only local SED SSD storage devices that are physically attached through a RAID controller inside the server.
{: note}

| **Profile name**                             | **CPU cores** | **Virtual CPUs** | **Memory (GiB)**  | **Non-Volatile Memory (GiB)**  |
| ---------------------------------------------| ------------- | ---------------- | ---------------- | ------------------------------|
| BI.S4.H2.384GB RAM + 1.5TB Persistent Memory | 56            | 112              | 384              | 1,536                         |
| BI.S4.H2.768GB RAM + 1.5TB Persistent Memory | 56            | 112              | 768              | 1,536                         |
| BI.S4.H2.768GB RAM + 3TB Persistent Memory   | 56            | 112              | 768              | 3,072                         |
| BI.S4.H4.768GB RAM + 3TB Persistent Memory   | 56            | 112              | 768              | 3,072                         |
| BI.S4.H2.1.5TB RAM + 1.5TB Persistent Memory | 56            | 112              | 1,536            | 1,536                         |
| BI.S4.H2.1.5TB RAM + 3TB Persistent Memory   | 56            | 112              | 1,536            | 3,072                         |
| BI.S4.H4.1.5TB RAM + 3TB Persistent Memory   | 112           | 224              | 1,536            | 3,072                         |
| BI.S4.H4.1.5TB RAM + 6TB Persistent Memory   | 112           | 224              | 1,536            | 6,144                         |
| BI.S4.H8.1.5TB RAM + 6TB Persistent Memory   | 224           | 448              | 1,536            | 6,144                         |
| BI.S4.H4.3TB RAM + 3TB Persistent Memory     | 112           | 224              | 3,072            | 3,072                         |
| BI.S4.H4.3TB RAM + 6TB Persistent Memory     | 112           | 224              | 3,072            | 6,144                         |
| BI.S4.H8.3TB RAM + 6TB Persistent Memory     | 224           | 448              | 3,072            | 6,144                         |
| BI.S4.H8.3TB RAM + 12TB Persistent Memory    | 224           | 448              | 3,072            | 12,288                        |
| BI.S4.H8.6TB RAM + 6TB Persistent Memory     | 224           | 448              | 6,144            | 6,144                         |
| BI.S4.H8.6TB RAM + 12TB Persistent Memory    | 224           | 448              | 6,144            | 12,288                        |
{: class="simple-tab-table"}
{: tab-group="s4-optane-profiles"}
{: caption="Intel Cascade Lake profiles with Optane PMem for SAP HANA" caption-side="bottom"}
{: #cl-optane-profiles-table}
{: tab-title="Certified Profiles"}

| **Profile name**                             | **Workload type** | **SAPS** |
| ---------------------------------------------| ----------------- | -------- |
| BI.S4.H2.384GB RAM + 1.5TB Persistent Memory | OLTP              | 135,127  |
| BI.S4.H2.768GB RAM + 1.5TB Persistent Memory | OLTP              | 135,127  |
| BI.S4.H2.768GB RAM + 3TB Persistent Memory   | OLTP              | 135,127  |
| BI.S4.H4.768GB RAM + 3TB Persistent Memory   | OLTP              | 285,970  |
| BI.S4.H2.1.5TB RAM + 1.5TB Persistent Memory | OLTP/OLAP         | 135,127  |
| BI.S4.H2.1.5TB RAM + 3TB Persistent Memory   | OLTP/OLAP         | 135,127  |
| BI.S4.H4.1.5TB RAM + 3TB Persistent Memory   | OLTP/OLAP         | 285,970  |
| BI.S4.H4.1.5TB RAM + 6TB Persistent Memory   | OLTP              | 285,970  |
| BI.S4.H8.1.5TB RAM + 6TB Persistent Memory   | OLTP              | 550,670  |
| BI.S4.H4.3TB RAM + 3TB Persistent Memory     | OLTP/OLAP         | 285,970  |
| BI.S4.H4.3TB RAM + 6TB Persistent Memory     | OLTP/OLAP         | 285,970  |
| BI.S4.H8.3TB RAM + 6TB Persistent Memory     | OLTP/OLAP         | 550,670  |
| BI.S4.H8.3TB RAM + 12TB Persistent Memory    | OLTP              | 550,670  |
| BI.S4.H8.6TB RAM + 6TB Persistent Memory     | OLTP/OLAP         | 550,670  |
| BI.S4.H8.6TB RAM + 12TB Persistent Memory    | OLTP/OLAP         | 550,670  |
{: class="simple-tab-table"}
{: tab-group="s4-optane-profiles"}
{: caption="Intel Cascade Lake profiles with Optane PMem for SAP HANA" caption-side="bottom"}
{: #cl-optane-sizing}
{: tab-title="Sizing"}



| **Profile name**                             |  **Storage Array RAID 1\n(Boot + Log)** | **Storage Array RAID 10\n(Shared + Data)**  |**Global hot spare**     |
| ---------------------------------------------|-----------------------------------------|---------------------------------------------|-------------------------|
| BI.S4.H2.384GB RAM + 1.5TB Persistent Memory | 2 x 960 GB                              | 6 x 1.9 TB                                  | 1 x 960 GB, 1 x 1.9 TB  |
| BI.S4.H2.768GB RAM + 1.5TB Persistent Memory | 2 x 960 GB                              | 6 x 1.9 TB                                  | 1 x 960 GB, 1 x 1.9 TB  |
| BI.S4.H2.768GB RAM + 3TB Persistent Memory   | 2 x 960 GB                              | 10 x 1.9 TB                                 |                         |
| BI.S4.H4.768GB RAM + 3TB Persistent Memory   | 2 x 960 GB                              | 10 x 1.9 TB                                 | 1 x 960 GB, 1 x 1.9 TB  |
| BI.S4.H2.1.5TB RAM + 1.5TB Persistent Memory | 2 x 960 GB                              | 8 x 1.9 TB                                  | 1 x 960 GB, 1 x 1.9 TB  |
| BI.S4.H2.1.5TB RAM + 3TB Persistent Memory   | 2 x 960 GB                              | 6 x 3.8 TB                                  | 1 x 960 GB, 1 x 3.8 TB  |
| BI.S4.H4.1.5TB RAM + 3TB Persistent Memory   | 2 x 960 GB                              | 6 x 3.8 TB                                  | 1 x 960 GB, 1 x 3.8 TB  |
| BI.S4.H4.1.5TB RAM + 6TB Persistent Memory   | 2 x 960 GB                              | 10 x 3.8 TB                                 | 1 x 960 GB, 1 x 3.8 TB  |
| BI.S4.H8.1.5TB RAM + 6TB Persistent Memory   | 2 x 960 GB                              | 10 x 3.8 TB                                 | 1 x 960 GB, 1 x 3.8 TB  |
| BI.S4.H4.3TB RAM + 3TB Persistent Memory     | 2 x 960 GB                              | 8 x 3.8 TB                                  | 1 x 960 GB, 1 x 3.8 TB  |
| BI.S4.H4.3TB RAM + 6TB Persistent Memory     | 2 x 960 GB                              | 10 x 3.8 TB                                 | 1 x 960 GB, 1 x 3.8 TB  |
| BI.S4.H8.3TB RAM + 6TB Persistent Memory     | 2 x 960 GB                              | 10 x 3.8 TB                                 | 1 x 960 GB, 1 x 3.8 TB  |
| BI.S4.H8.3TB RAM + 12TB Persistent Memory    | 2 x 960 GB                              | 8 x 7.68 TB                                 | 1 x 960 GB, 1 x 7.68 TB |
| BI.S4.H8.6TB RAM + 6TB Persistent Memory     | 2 x 960 GB                              | 8 x 7.68 TB                                 | 1 x 960 GB, 1 x 7.68 TB |
| BI.S4.H8.6TB RAM + 12TB Persistent Memory    | 2 x 960 GB                              | 10 x 7.68 TB                                | 1 x 960 GB, 1 x 7.68 TB |
{: class="simple-tab-table"}
{: tab-group="s4-optane-profiles"}
{: caption="Intel Cascade Lake profiles with Optane PMem for SAP HANA" caption-side="bottom"}
{: #cl-optane-storage}
{: tab-title="Storage"}


## Profiles hosted on Intel Skylake CPU
{: #sl-profiles}

The following table lists available profile families for Intel Skylake processor-based servers:

These profiles do not use any network block or file storage options that are mounted to the bare metal server. These profiles use only local SSD storage devices that are physically attached through a RAID controller inside the server.
{: note}

| **Profile name**           | **CPU cores** | **Virtual CPUs** | **Memory (GiB)**  |
| ---------------------------| ------------- | ---------------- | ---------------- |
| BI.S3.H2.768 Appliance     | 36            | 72               | 768              |
{: class="simple-tab-table"}
{: tab-group="s3-profiles"}
{: caption="Intel Skylake profiles for SAP HANA" caption-side="bottom"}
{: #sl-profiles-table}
{: tab-title="Certified Profiles"}

| **Profile name**           | **Workload type** | **SAPS** |
| ---------------------------| ----------------- | -------- |
| BI.S3.H2.768 Appliance     | OLTP/OLAP         | 79,630   |
{: class="simple-tab-table"}
{: tab-group="s3-profiles"}
{: caption="Intel Skylake profiles for SAP HANA" caption-side="bottom"}
{: #sl-sizing}
{: tab-title="Sizing"}

| **Profile name**           | **Storage Array RAID 1\n(Boot + Log)** | **Storage Array RAID 10\n(Shared + Data)**  |**Global hot spare** |
| ---------------------------|----------------------------------------|---------------------------------------------|---------------------|
| BI.S3.H2.768 Appliance     |  2 x 960 GB                            | 4 x 960 GB                                  | 1 x 960 GB          |
{: class="simple-tab-table"}
{: tab-group="s3-profiles"}
{: caption="Intel Skylake profiles for SAP HANA" caption-side="bottom"}
{: #sl-storage}
{: tab-title="Storage"}



## Boot-only servers
{: #boot-only-servers}

* By default, {{site.data.keyword.baremetal_long}} come with high-performance and highly reliable internal storage based on solid-state disks and high-performance RAID adapters.
* For projects with different requirements, such as storage snapshots, that don't have a very high through-put requirement, {{site.data.keyword.cloud_notm}} offers `boot-only servers.`
* Boot-only servers have the same configuration as standard {{site.data.keyword.baremetal_short}}, however, only the boot disks for the operating system are configured. The storage required for the SAP file systems - `/usr/sap`, `/hana/shared`, `/hana/data`, and `/hana/log` - is not provided, significantly reducing the server cost.
* To run SAP HANA on a boot-only server, you must add {{site.data.keyword.cloud_notm}} storage to the server.
* SAP HANA Tailored Data Center Integration (TDI) requirements must be met to run SAP HANA as Production for the SAP System/s, or in a production-like environment. To fulfill SAP HANA TDI key performance indicators (TDIs), storage performance must meet minimum values.

### Boot-only servers profiles
{: #boot-only-profiles}

| **Profile name**           | **CPU cores** | **Virtual CPUs** | **Memory (GiB)**  |
| ---------------------------| ------------- | ---------------- | ---------------- |
| BI.S4.H2.192 (boot only)   | 32            | 32               | 192              |
| BI.S4.H2.384 (boot only)   | 32            | 64               | 384              |
| BI.S4.H2.768 (boot only)   | 40            | 80               | 768              |
| BI.S4.H2.1500 (boot only)  | 56            | 112              | 1,536            |
| BI.S4.H2.3000 (boot only)  | 56            | 112              | 3,072            |
{: tab-group="boot-only-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA" caption-side="bottom"}
{: #boot-only-profiles-table}
{: tab-title="Certified Profiles"}

| **Profile name**           | **Workload type** | **SAPS** |
| ---------------------------| ----------------- | -------- |
| BI.S4.H2.192 (boot only)   | OLTP/OLAP         | 82,470   |
| BI.S4.H2.384 (boot only)   | OLTP/OLAP         | 85,130   |
| BI.S4.H2.768 (boot only)   | OLTP/OLAP         | 112,830  |
| BI.S4.H2.1500 (boot only)  | OLTP/OLAP         | 147,220  |
| BI.S4.H2.3000 (boot only)  | OLTP/OLAP         | 135,127  |
{: class="simple-tab-table"}
{: tab-group="boot-only-profiles"}
{: caption="Boot only profiles for SAP HANA" caption-side="bottom"}
{: #boot-only-sizing}
{: tab-title="Sizing"}

| **Profile name**           | **Storage Array RAID 1\n(Boot + Log)** |
| ---------------------------|----------------------------------------|
| BI.S4.H2.192 (boot only)   |  2 x 960 GB                            |
| BI.S4.H2.384 (boot only)   |  4 x 960 GB                            |
| BI.S4.H2.768 (boot only)   |  4 x 960 GB                            |
| BI.S4.H2.1500 (boot only)  |  4 x 960 GB                            |
| BI.S4.H2.3000 (boot only)  |  4 x 960 GB                            |
{: class="simple-tab-table"}
{: tab-group="boot-only-profiles"}
{: caption="Boot only profiles for SAP HANA" caption-side="bottom"}
{: #boot-only-storage}
{: tab-title="Storage"}


* To run SAP HANA in production and production-like setups that use {{site.data.keyword.filestorage_full_notm}}, based on the Network File System (NFS) protocol, requires:
   - Minimum of 8 K IOPS for `/hana/log`
   - Minimum of 7 K IOPS for `/hana/data`

* You can share storage areas for `/hana/shared` with `/hana/log` or `/hana/data` if these areas are too large for your particular use case.
* For non-production use, you can use one storage area for `/hana/shared`, `/hana/log`, and `/hana/data`.
* These minimums are for either *Endurance* or *Performance* options available with {{site.data.keyword.filestorage_full_notm}}:
   - Endurance storage is a predefined IOPS per GB of storage, for example, 0.25 up to 10 IOPS per GB.
   - Performance storage has different ranges of IOPS per GB, depending on the total size of the storage device. This option is more customizable.

This table compares the file storage (using NFS protocol) and the performance IOPS range for the capacity:

| **File Storage capacity (GB)** | **Performance custom IOPS**                                                                                    |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------------|
| 20-39                          | 100-1,000                                                                                                      |
| 40-79                          | 100-2,000                                                                                                      |
| 80-99                          | 100-4,000                                                                                                      |
| 100-499                        | 100-6,000                                                                                                      |
| 500-999                        | 100-10,000                                                                                                     |
| 1,000-1,999                    | 100-20,000                                                                                                     |
| 2,000-2,999                    | 200-40,000                                                                                                     |
| 3,000-3,999                    | 200-48,000                                                                                                     |
| 4,000-7,999                    | 300-48,000                                                                                                     |
| 8,000-9,999                    | 500-48,000                                                                                                     |
| 10,000-12,000                  | 1,000-48,000                                                                                                   |
| Up to 24,000                   | Up to 96,000 [by special request](/docs/FileStorage?topic=FileStorage-expandCapacity#increasecapacityover12TB) |
| Up to 18,000                   | Up to 180,000 [by special request](/docs/FileStorage?topic=FileStorage-expandCapacity#increasecapacityover12TB)|
{: caption="{{site.data.keyword.filestorage_full_notm}} Performance storage GB and IOPS" caption-side="top"}


## Profiles hosted on VMware SDDC
{: #vmware-profiles}

{{site.data.content.vms-deprecated-note}}

The following table is an overview of the SAP-certified profiles with either:
- Intel Bare Metal and VMware vSphere (ESXi), manual VMware setup and configuration
- {{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated, automated VMware SDDC setup and configuration


| **Profile**            | **CPU Cores** | **Virtual CPUs** | **Memory (GiB)** |
| ---------------------- |---------------| ---------------- | ---------------- |
| BI.S3.H2.384 (VMware)  | 36            | 72               | 384              |
| BI.S3.H2.768 (VMware)  | 36            | 72               | 768              |
| BI.S4.H2.192 (VMware)  | 32            | 64               | 192              |
| BI.S4.H2.384 (VMware)  | 32            | 64               | 384              |
| BI.S4.H2.768 (VMware)  | 40            | 80               | 768              |
| BI.S4.H2.1500 (VMware) | 56            | 112              | 1536             |
| BI.S4.H2.3000 (VMware) | 56            | 112              | 3072             |
| BI.S4.H4.3000 (VMware) | 112           | 224              | 3072             |
| BI.S4.H4.6000 (VMware) | 112           | 224              | 6144             |
| BI.S4.H8.6000 (VMware) | 224           | 448              | 6144             |
{: class="simple-tab-table"}
{: tab-group="vmware-profiles"}
{: caption="VMware profiles for SAP HANA" caption-side="bottom"}
{: #vmware-profiles-table}
{: tab-title="Certified Profiles"}

| **Profile**            | **SAPS (after VMware hypervisor 10%)** | **Workload type** |
| ---------------------- | -------------------------------------- | ----------------- |
| BI.S3.H2.384 (VMware)  | 71,487                                 | OLAP, OLTP        |
| BI.S3.H2.768 (VMware)  | 71,667                                 | OLAP, OLTP        |
| BI.S4.H2.192 (VMware)  | 74,223                                 | OLAP, OLTP        |
| BI.S4.H2.384 (VMware)  | 76,617                                 | OLAP, OLTP        |
| BI.S4.H2.768 (VMware)  | 101,547                                | OLAP, OLTP        |
| BI.S4.H2.1500 (VMware) | 132,498                                | OLAP, OLTP        |
| BI.S4.H2.3000 (VMware) | 121,614                                | OLAP, OLTP        |
| BI.S4.H4.3000 (VMware) | 257,373                                | OLAP, OLTP        |
| BI.S4.H4.6000 (VMware) | 257,373                                | OLAP, OLTP        |
| BI.S4.H8.6000 (VMware) | 495,603                                | OLAP, OLTP        |
{: class="simple-tab-table"}
{: tab-group="vmware-profiles"}
{: caption="VMware profiles for SAP HANA" caption-side="bottom"}
{: #vmware-sizing}
{: tab-title="Sizing"}
