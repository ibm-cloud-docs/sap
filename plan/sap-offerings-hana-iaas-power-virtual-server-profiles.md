---
copyright:
  years: 2020, 2026
lastupdated: "2026-01-15"
keywords: SAP-Certified HANA profiles, ush1, bh1, ch1, mh1, umh, cnp, sr2, sh2, bh2, ch2, mh2, sr3, Rise, P9, P10, P11, storage, powervs, log, data, shared, iops, tiers, tier
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# SAP HANA certified instances on IBM {{site.data.keyword.powerSys_notm}}
{: #hana-iaas-offerings-profiles-power-vs}


{{site.data.keyword.IBM_notm}} Power9 certified profiles are discouraged for new deployments. Users are advised to migrate to {{site.data.keyword.IBM_notm}} Power10 certified profiles, selecting the appropriate profile based on memory and core requirements.
{:attention: .attention}

A SAP HANA certified instance or profile on IBM {{site.data.keyword.powerSys_notm}} defines attributes, such as physical CPU cores and RAM, which determine the size and performance capabilities of the virtual server instance.

## SAP HANA profile naming convention
{: #powervs-hana-profile-naming-convention}

Provision SAP HANA on IBM {{site.data.keyword.powerSys_notm}}s by selecting one of the certified profiles.

SAP HANA profile names follow a contextual and sequential naming convention.
The following table illustrates an example of an SAP HANA certified instance profile:

| Profile name | Naming convention component | Description           |
| ------------ | --------------------------- | --------------------- |
| sh2-33x1900  | sh2                         | Profile prefix        |
|              | *-*                         | *separator*           |
|              | 33                          | 33 physical CPU Cores |
|              | x                           | *separator*           |
|              | 1900                        | 1900 GiB RAM          |
{: caption="Profile naming scheme for SAP HANA" caption-side="bottom"}

Refer to [SAP Note 2947579 - SAP HANA on {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s](https://launchpad.support.sap.com/#/notes/2947579){: external} for up-to-date information on certified SAP HANA profiles.
{: note}

## Simultaneous multithreading (SMT) on IBM Power for SAP HANA
{: #smt-modes}

SAP HANA profiles for Power servers operate in **SMT4 or SMT8 mode**.
Simultaneous multithreading (SMT) on IBM Power enables multiple independent threads to execute within a single physical processor core.

1. In **SMT4 mode**, four parallel threads can run on a single physical processor core.
   The operating system sees four logical processors per physical processor core.
1. In **SMT8 mode**, eight parallel threads can run on a single physical processor core.
   The operating system sees eight logical processors per physical processor core.


## IBM Power11 certified instances for SAP HANA
{: #sap-hana-iaas-offerings-profiles-power11-families}

The following table lists available profile families for IBM Power11 processor-based servers:

| Families | Description |
| -------- | ----------- |
| [Profiles with sr3 prefix](#sr3-profiles) | Profiles with the prefix *sr3* are SAP RISE profiles.|
{: caption="Profile families for Power11 server generation" caption-side="bottom"}


### sr3 - Certified profiles
{: #sr3-profiles}

The following SAP HANA profiles with prefix **sr3** on IBM {{site.data.keyword.powerSys_notm}} are supported:

Data volume storage is sized at 120% of memory, following SAP’s recommendation to include a 20% buffer for operational overhead.

| **Profile name** | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** |  **[SMT Mode](#smt-modes)** |
| -----------------| ------------- | ---------------- | ---------------- | --------------------------- |
| sr3-6x256        | 6             | 48               | 256              | SMT8                        |
| sr3-11x512       | 11            | 88               | 512              | SMT8                        |
| sr3-19x1024      | 19            | 152              | 1024             | SMT8                        |
| sr3-76x6144      | 76            | 608              | 6144             | SMT8                        |
| sr3-229x30500    | 229           | 1832             | 30500            | SMT8                        |
{: class="simple-tab-table"}
{: tab-group="sr3-profiles"}
{: caption="P11 Certified Instance profiles with sr3 prefix" caption-side="bottom"}
{: #sr3-profiles-instance}
{: tab-title="Certified Profiles"}


| **Profile name** | **Workload type** | **aSAPS** | **SAPS** |
| -----------------| ----------------- | --------- | -------- |
| sr3-6x256        | OLTP              | 9,800     | 47,490   |
| sr3-11x512       | OLTP              | 16,200    | 87,065   |
| sr3-19x1024      | OLTP/OLAP         | 30,900    | 150,385  |
| sr3-76x6144      | OLTP/OLAP         | 103,900   | 625,632  |
| sr3-229x30500    | OLTP/OLAP         | 307,000   |    N/A   |
{: class="simple-tab-table"}
{: tab-group="sr3-profiles"}
{: caption="P11 Certified Instance profiles with sr3 prefix" caption-side="bottom"}
{: #sr3-profiles-sizing}
{: tab-title="Sizing"}


| **sr3\nCertified profile** | **IOPs\nrequired**| **Log file system\nstorage config (GB)** | **Log file system\nstorage tier**  | **Log file system\nsize(GB)**| **IOPs\nobtained** |
|--------------------------- |------------------ |----------------------------------------- |------------------------------------|------------------------------|--------------------|
| sr3-6x256                  | 12,000            |   4 x 128 GB                             | Tier 0                             |512 GB                        | 12,800             |
| sr3-11x512                 | 12,000            |   4 x 128 GB                             | Tier 0                             |512 GB                        | 12,800             |
| sr3-19x1024                | 12,000            |   4 x 128 GB                             | Tier 0                             |512 GB                        | 12,800             |
| sr3-76x6144                | 12,000            |   4 x 128 GB                             | Tier 0                             |512 GB                        | 12,800             |
| sr3-229x30500              | 12,000            |   4 x 128 GB                             | Tier 0                             |512 GB                        | 12,800             |
{: class="simple-tab-table"}
{: tab-group="sr3-profiles"}
{: caption="Log file system configurations for SAP HANA profiles with sr3 prefix" caption-side="bottom"}
{: #sr3-profiles-log}
{: tab-title="Log File System"}


| **sr3\nCertified profile** | **IOPs\nrequired**| **Data file system\nstorage config (GB)** | **Data file system\nstorage tier**  | **Data file system\nsize(GB)**| **IOPs\nobtained** |
|--------------------------- |------------------ |------------------------------------------ |-------------------------------------|-------------------------------|--------------------|
| sr3-6x256                  | 8,000             | 4 x 80 GB                                 | Tier 0                              | 320 GB                        | 8,000              |
| sr3-11x512                 | 8,000             | 4 x 154 GB                                | Tier 0                              | 616 GB                        | 15,400             |
| sr3-19x1024                | 8,000             | 4 x 307 GB                                | Tier 0                              | 1,228 GB                      | 30,700             |
| sr3-76x6144                | 8,000             | 4 x 1844 GB                               | Tier 3                              | 7,376 GB                      | 22,128             |
| sr3-229x30500              | 8,000             | 4 x 9150 GB                               | Tier 3                              | 36,600 GB                     | 109,800            |
{: class="simple-tab-table"}
{: tab-group="sr3-profiles"}
{: caption="Data file system configurations for SAP HANA profiles with sr3 prefix" caption-side="bottom"}
{: #sr3-profiles-data}
{: tab-title="Data File System"}


| **sr3\nCertified profile** | **IOPs\nrequired**| **Shared file system\nstorage config (GB)** | **Shared file system\nstorage tier**  | **Shared file system\nsize(GB)**| **IOPs\nobtained** |
|--------------------------- |------------------ |-------------------------------------------- |---------------------------------------|---------------------------------|--------------------|
|  sr3-6x256                 | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|  sr3-11x512                | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|  sr3-19x1024               | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|  sr3-76x6144               | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
{: tab-group="sr3-profiles"}
{: caption="Shared file system configurations for SAP HANA profiles with sr3 prefix" caption-side="bottom"}
{: #sr3-profiles-shared}
{: tab-title="Shared File System"}


For IBM Power11 profiles, "N/A" indicates that SAPS are not measured.
{:note: .note}



## IBM Power10 certified instances for SAP HANA
{: #sap-hana-iaas-offerings-profiles-power10-families}

The following table lists available profile families for IBM Power10 processor-based servers:

| Families | Description |
| -------- | ----------- |
| [Profiles with sr2 prefix](#sr2-profiles) | Profiles with the prefix *sr2* are SAP RISE profiles.|
| [Profiles with sh2 prefix](#sh2-profiles) | Profiles with the prefix *sh2* support HANA database sizes up to 1900 GiB. |
| [Profiles with bh2 prefix](#bh2-profiles) | Profiles with the prefix *bh2* are balanced profiles and are best suited for midsize databases and common cloud applications with moderate traffic. |
| [Profiles with ch2 prefix](#ch2-profiles)  | Profiles with the prefix *ch2* are compute intensive profiles and are best suited for moderate to high web traffic workloads. Compute profiles are best suited for cpu-intensive workloads, such as heavy web traffic, production batch processing, and front-end web servers. |
| [Profiles with mh2 prefix](#mh2-profiles) | Profiles with the prefix *mh2* are very high memory profiles and are best suited for server OLAP databases, such as SAP NetWeaver. |
{: caption="Profile families for Power10 server generation" caption-side="bottom"}

**sr2 profiles** also support custom combination of physical CPU cores and memory. **Custom sr2 profiles** must be deployed by using the **CLI or API** interface only and these custom profiles are not supported by SAP.



### sr2 - Certified profiles
{: #sr2-profiles}

The following SAP HANA profiles with prefix **sr2** on IBM {{site.data.keyword.powerSys_notm}} are supported:

Data volume storage is sized at 120% of memory, following SAP’s recommendation to include a 20% buffer for operational overhead.

| **Profile name**    | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** | **[SMT Mode](#smt-modes)** |
| --------------------| ------------- | ---------------- | -----------------|----------------------------|
| sr2-7x256           | 7             | 28               | 256              |  SMT4                      |
| sr2-7x384           | 7             | 28               | 384              |  SMT4                      |
| sr2-14x512          | 14            | 56               | 512              |  SMT4                      |
| sr2-14x740          | 14            | 56               | 740              |  SMT4                      |
| sr2-12x950          | 12            | 48               | 950              |  SMT4                      |
| sr2-24x1024         | 24            | 96               | 1,024            |  SMT4                      |
| sr2-12x1450         | 12            | 48               | 1,450            |  SMT4                      |
| sr2-24x1536         | 24            | 96               | 1,536            |  SMT4                      |
| sr2-25x1900         | 25            | 200              | 1,900            |  SMT8                      |
| sr2-35x3000         | 35            | 280              | 3,000            |  SMT8                      |
| sr2-35x3900         | 35            | 280              | 3,900            |  SMT8                      |
| sr2-80x6144         | 80            | 640              | 6,144            |  SMT8                      |
| sr2-87x7000         | 87            | 696              | 7,000            |  SMT8                      |
| sr2-87x7600         | 87            | 696              | 7,600            |  SMT8                      |
| sr2-80x9216         | 80            | 320              | 9,216            |  SMT4                      |
| sr2-80x12288        | 80            | 320              | 12,288           |  SMT4                      |
| sr2-80x14400        | 80            | 320              | 14,400           |  SMT4                      |
| sr2-128x16000       | 128           | 512              | 16,000           |  SMT4                      |
| sr2-128x18000       | 128           | 512              | 18,000           |  SMT4                      |
| sr2-128x20000       | 128           | 512              | 20,000           |  SMT4                      |
| sr2-165x22000       | 165           | 1,320            | 22,000           |  SMT8                      |
| sr2-165x24000       | 165           | 1,320            | 24,000           |  SMT8                      |
| sr2-165x26000       | 165           | 1,320            | 26,000           |  SMT8                      |
| sr2-165x28000       | 165           | 1,320            | 28,000           |  SMT8                      |
| sr2-165x30500       | 165           | 1,320            | 30,500           |  SMT8                      |
{: class="simple-tab-table"}
{: tab-group="sr2-certified-profiles"}
{: caption="P10 Certified Instance profiles with sr2 prefix" caption-side="bottom"}
{: #sr2-certified-profiles-instance}
{: tab-title="Certified Profiles"}

| **Profile name**    |**Workload type**                             | **aSAPS**| **SAPS**  |
| --------------------|----------------------------------------------| ---------| ----------|
| sr2-7x256           |     OLTP                                     |     N/A  | 42,000    |
| sr2-7x384           |     OLTP                                     |     N/A  | 42,000    |
| sr2-14x512          |     OLTP                                     |     N/A  | 84,000    |
| sr2-14x740          |     OLTP/OLAP                                |     N/A  | 84,000    |
| sr2-12x950          |     OLTP/OLAP                                |     N/A  | 72,000    |
| sr2-24x1024         |     OLTP/OLAP                                |     N/A  | 144,000   |
| sr2-12x1450         |     OLTP/OLAP                                |     N/A  | 72,000    |
| sr2-24x1536         |     OLTP/OLAP                                |     N/A  | 144,000   |
| sr2-25x1900         |     OLTP/OLAP                                |     N/A  | 190,000   |
| sr2-35x3000         |     OLTP/OLAP                                |     N/A  | 266,000   |
| sr2-35x3900         |     OLTP/OLAP                                |     N/A  | 266,000   |
| sr2-80x6144         |     OLTP/OLAP/OLAP scale-out (up to 6 nodes) |     N/A  | 608,000   |
| sr2-87x7000         |     OLTP/OLAP                                |     N/A  |661,200    |
| sr2-87x7600         |     OLTP/OLAP                                |     N/A  | 661,200   |
| sr2-80x9216         |     OLTP/OLAP                                |     N/A  | 480,000   |
| sr2-80x12288        |     OLTP/OLAP                                |     N/A  | 480,000   |
| sr2-80x14400        |     OLTP/OLAP                                |     N/A  | 480,000   |
| sr2-128x16000       |     OLTP/OLAP                                |  133,100 | 768,000   |
| sr2-128x18000       |     OLTP/OLAP                                |  133,100 | 768,000   |
| sr2-128x20000       |     OLTP/OLAP                                |  133,100 | 768,000   |
| sr2-165x22000       |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) |  217,100 | 1,254,000 |
| sr2-165x24000       |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) |  217,100 | 1,254,000 |
| sr2-165x26000       |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) |  217,100 | 1,254,000 |
| sr2-165x28000       |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) |  217,100 | 1,254,000 |
| sr2-165x30500       |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) |     N/A  | 1,254,000 |
{: class="simple-tab-table"}
{: tab-group="sr2-certified-profiles"}
{: caption="Sizing details for SAP HANA profiles with sr2 prefix" caption-side="bottom"}
{: #sr2-certified-profiles-sizing}
{: tab-title="Sizing"}



| **sr2\nCertified profile** | **IOPs\nrequired**| **Log file system\nstorage config (GB)**|**Log file system\nstorage tier**|**Log file system \nsize (GB)**|**IOPs\nobtained**|
|--------------------------- |------------------ |---------------------------------------- |---------------------------------|-------------------------------|------------------|
| sr2-7x256                  | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-7x384                  | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-14x512                 | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-14x740                 | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-12x950                 | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-24x1024                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-12x1450                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-24x1536                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-25x1900                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-35x3000                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-35x3900                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-80x6144                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-87x7000                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-87x7600                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-80x9216                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-80x12288               | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-80x14400               | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-128x16000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-128x18000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-128x20000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-165x22000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-165x24000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-165x26000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-165x28000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-165x30500              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
{: class="simple-tab-table"}
{: tab-group="sr2-certified-profiles"}
{: caption="Sample log file system configurations for SAP HANA profiles with sr2 prefix" caption-side="bottom"}
{: #sr2-certified-profiles-log}
{: tab-title="Log File System"}

|**sr2 Certified profile** |**IOPs required**|**Data file system\nstorage config (GB)**|**Data file system \nstorage tier**|**Data file system \nsize (GB)**|**IOPs obtained**|
|--------------------------|-----------------|-----------------------------------------|-----------------------------------|--------------------------------|-----------------|
|sr2-7x256                 |   8,000         |      4 x 80 GB                          | Tier 0                            |     320 GB                     | 8000            |
|sr2-7x384                 |   8,000         |      4 x 116 GB                         | Tier 0                            |     464 GB                     | 11600           |
|sr2-14x512                |   8,000         |      4 x 154 GB                         | Tier 0                            |     616 GB                     | 15400           |
|sr2-14x740                |   8,000         |      4 x 222 GB                         | Tier 0                            |     888 GB                     | 22200           |
|sr2-12x950                |   8,000         |      4 x 285 GB                         | Tier 0                            |     1,140 GB                   | 28500           |
|sr2-24x1024               |   8,000         |      4 x 307 GB                         | Tier 0                            |     1,228 GB                   | 30700           |
|sr2-12x1450               |   8,000         |      4 x 435 GB                         | Tier 0                            |     1,740 GB                   | 43500           |
|sr2-24x1536               |   8,000         |      4 x 461 GB                         | Tier 0                            |     1,844 GB                   | 46100           |
|sr2-25x1900               |   8,000         |      4 x 570 GB                         | Tier 0                            |     2,280 GB                   | 57000           |
|sr2-35x3000               |   8,000         |      4 x 900 GB                         | Tier 3                            |     3,600 GB                   | 10800           |
|sr2-35x3900               |   8,000         |      4 x 1170 GB                        | Tier 3                            |     4,680 GB                   | 14040           |
|sr2-80x6144               |   8,000         |      4 x 1844 GB                        | Tier 3                            |     7,376 GB                   | 22128           |
|sr2-87x7000               |   8,000         |      4 x 2100 GB                        | Tier 3                            |     8,400 GB                   | 25200           |
|sr2-87x7600               |   8,000         |      4 x 2280 GB                        | Tier 3                            |     9,120 GB                   | 27360           |
|sr2-80x9216               |   8,000         |      4 x 2765 GB                        | Tier 3                            |     11,060 GB                  | 33180           |
|sr2-80x12288              |   8,000         |      4 x 3687 GB                        | Tier 3                            |     14,748 GB                  | 44244           |
|sr2-80x14400              |   8,000         |      4 x 4320 GB                        | Tier 3                            |     17,280 GB                  | 51840           |
|sr2-128x16000             |   8,000         |      4 x 4800 GB                        | Tier 3                            |     19,200 GB                  | 57600           |
|sr2-128x18000             |   8,000         |      4 x 5400 GB                        | Tier 3                            |     21,600 GB                  | 64800           |
|sr2-128x20000             |   8,000         |      4 x 6000 GB                        | Tier 3                            |     24,000 GB                  | 72000           |
|sr2-165x22000             |   8,000         |      4 x 6600 GB                        | Tier 3                            |     26,400 GB                  | 79200           |
|sr2-165x24000             |   8,000         |      4 x 7200 GB                        | Tier 3                            |     28,800 GB                  | 86400           |
|sr2-165x26000             |   8,000         |      4 x 7800 GB                        | Tier 3                            |     31,200 GB                  | 93600           |
|sr2-165x28000             |   8,000         |      4 x 8400 GB                        | Tier 3                            |     33,600 GB                  | 100800          |
|sr2-165x30500             |   8,000         |      4 x 9150 GB                        | Tier 3                            |     36,600 GB                  | 109800          |
{: class="simple-tab-table"}
{: tab-group="sr2-certified-profiles"}
{: caption="Sample data file system configurations for SAP HANA profiles with sr2 prefix" caption-side="bottom"}
{: #sr2-certified-profiles-data}
{: tab-title="Data File System"}

| **sr2\nCertified profile** | **IOPs\nrequired**| **Shared file system\nstorage config (GB)** | **Shared file system\nstorage tier**  | **Shared file system\nsize(GB)**| **IOPs\nobtained** |
|--------------------------- |------------------ |-------------------------------------------- |---------------------------------------|---------------------------------|--------------------|
|    sr2-7x256               | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-7x384               | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-14x512              | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-14x740              | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-12x950              | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-24x1024             | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-12x1450             | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-24x1536             | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-25x1900             | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-35x3000             | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-35x3900             | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-80x6144             | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-87x7000             | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-87x7600             | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-80x9216             | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-80x12288            | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-80x14400            | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-128x16000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-128x18000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-128x20000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-165x22000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-165x24000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-165x26000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-165x28000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    sr2-165x30500           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
{: class="simple-tab-table"}
{: tab-group="sr2-certified-profiles"}
{: caption="Sample shared file system configurations for SAP HANA profiles with sr2 prefix" caption-side="bottom"}
{: #sr2-certified-profiles-shared}
{: tab-title="Shared File System"}


### sr2 - Discouraged profiles
{: #sr2-discouraged-profiles}

The following SAP HANA **sr2** profiles on IBM {{site.data.keyword.powerSys_notm}} are discouraged due to configuration anomalies. Alternative recommended profiles are suggested for each discouraged configuration.

Data volume storage is sized at 120% of memory, following SAP’s recommendation to include a 20% buffer for operational overhead.

|**Profile name**|**Recommended alternative** |**CPU cores**|**Virtual CPUs**|**Memory (GiB)**|**[SMT Mode](#smt-modes)**|
|----------------| -------------------------- | ----------- | -------------- | -------------- | -------------------------|
| sr2-22x2950    | sr2-35x3000                | 22          | 88             | 2,950          | SMT4                     |
| sr2-40x3072    | sr2-35x3000                | 40          | 160            | 3,072          | SMT4                     |
| sr2-35x4450    | sr2-35x3900                | 35          | 140            | 4,450          | SMT4                     |
| sr2-87x6000    | sr2-80x6144 or sr2-87x7000 | 87          | 696            | 6,000          | SMT8                     |
| sr2-64x6144    | sr2-80x6144                | 64          | 256            | 6,144          | SMT4                     |
{: class="simple-tab-table"}
{: tab-group="sr2-discouraged-profiles"}
{: caption="P10 Discouraged Instance profiles with sr2 prefix" caption-side="bottom"}
{: #sr2-discouraged-profiles-instance}
{: tab-title="Certified Profiles"}

|**Profile name**| **Workload type**|**aSAPS**| **SAPS**|
|----------------| ---------------- |---------| ------- |
| sr2-22x2950    | OLTP/OLAP        |   N/A   | 132,000 |
| sr2-40x3072    | OLTP/OLAP        |   N/A   | 240,000 |
| sr2-35x4450    | OLTP/OLAP        |   N/A   | 210,000 |
| sr2-87x6000    | OLTP/OLAP        |   N/A   | 661,200 |
| sr2-64x6144    | OLTP/OLAP        |   N/A   | 384,000 |
{: class="simple-tab-table"}
{: tab-group="sr2-discouraged-profiles"}
{: caption="Sizing details for SAP HANA profiles with sr2 prefix" caption-side="bottom"}
{: #sr2-discouraged-profiles-sizing}
{: tab-title="Sizing"}


|**sr2 Certified profile** |**IOPs required**|**Data file system\nstorage config (GB)**|**Data file system \nstorage tier**|**Data file system \nsize (GB)**|**IOPs obtained**|
|--------------------------|-----------------|-----------------------------------------|-----------------------------------|--------------------------------|-----------------|
| sr2-22x2950                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-40x3072                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-35x4450                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-87x6000                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sr2-64x6144                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
{: class="simple-tab-table"}
{: tab-group="sr2-discouraged-profiles"}
{: caption="Sample log file system configurations for SAP HANA profiles with sr2 prefix" caption-side="bottom"}
{: #sr2-discouraged-profiles-log}
{: tab-title="Log File System"}


|**sr2 Certified profile** |**IOPs required**|**Data file system\nstorage config (GB)**|**Data file system \nstorage tier**|**Data file system \nsize (GB)**|**IOPs obtained**|
|--------------------------|-----------------|-----------------------------------------|-----------------------------------|--------------------------------|-----------------|
|  sr2-22x2950             | 8,000           | 4 x 885 GB                              | Tier 3                            | 3540 GB                        | 14,256          |
|  sr2-40x3072             | 8,000           | 4 x 992 GB                              | Tier 3                            | 3688 GB                        | 14,844          |
|  sr2-35x4450             | 8,000           | 4 x 1335 GB                             | Tier 3                            | 5340 GB                        | 21,504          |
|  sr2-87x6000             | 8,000           | 4 x 1800 GB                             | Tier 3                            | 7200 GB                        | 28,998          |
|  sr2-64x6144             | 8,000           | 4 x 1843 GB                             | Tier 3                            | 7372 GB                        | 29,691          |
{: class="simple-tab-table"}
{: tab-group="sr2-discouraged-profiles"}
{: caption="Sample data file system configurations for SAP HANA profiles with sr2 prefix" caption-side="bottom"}
{: #sr2-discouraged-profiles-data}
{: tab-title="Data File System"}

| **sr2\nCertified profile** | **IOPs\nrequired**| **Shared file system\nstorage config (GB)** | **Shared file system\nstorage tier**  | **Shared file system\nsize(GB)**| **IOPs\nobtained** |
|--------------------------- |------------------ |-------------------------------------------- |---------------------------------------|---------------------------------|--------------------|
|    sr2-22x2950             | 3,000             |  1 x 200 GB                                 | Tier 0                                |  200 GB                         | 5,000              |
|    sr2-40x3072             | 3,000             |  1 x 200 GB                                 | Tier 0                                |  200 GB                         | 5,000              |
|    sr2-35x4450             | 3,000             |  1 x 200 GB                                 | Tier 0                                |  200 GB                         | 5,000              |
|    sr2-87x6000             | 3,000             |  1 x 200 GB                                 | Tier 0                                |  200 GB                         | 5,000              |
|    sr2-64x6144             | 3,000             |  1 x 200 GB                                 | Tier 0                                |  200 GB                         | 5,000              |
{: class="simple-tab-table"}
{: tab-group="sr2-discouraged-profiles"}
{: caption="Sample shared file system configurations for SAP HANA profiles with sr2 prefix" caption-side="bottom"}
{: #sr2-discouraged-profiles-shared}
{: tab-title="Shared File System"}



### sh2 - Certified profiles
{: #sh2-profiles}

The following SAP HANA profiles with prefix **sh2** on IBM {{site.data.keyword.powerSys_notm}} are supported:

Data volume storage is sized at 120% of memory, following SAP’s recommendation to include a 20% buffer for operational overhead.

| **Profile name** | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** | **[SMT Mode](#smt-modes)** |
| -----------------| ------------- | ---------------- | ---------------- | ---------------------------|
| sh2-4x256        | 4             | 16               | 256              |  SMT4                      |
| sh2-7x256        | 7             | 28               | 256              |  SMT4                      |
| sh2-12x256       | 12            | 48               | 256              |  SMT4                      |
| sh2-4x384        | 4             | 16               | 384              |  SMT4                      |
| sh2-7x384        | 7             | 28               | 384              |  SMT4                      |
| sh2-12x384       | 12            | 48               | 384              |  SMT4                      |
| sh2-4x512        | 4             | 16               | 512              |  SMT4                      |
| sh2-7x512        | 7             | 28               | 512              |  SMT4                      |
| sh2-12x512       | 12            | 48               | 512              |  SMT4                      |
| sh2-4x768        | 4             | 16               | 768              |  SMT4                      |
| sh2-7x768        | 7             | 28               | 768              |  SMT4                      |
| sh2-12x768       | 12            | 48               | 768              |  SMT4                      |
| sh2-4x1000       | 4             | 16               | 1,000            |  SMT4                      |
| sh2-7x1000       | 7             | 28               | 1,000            |  SMT4                      |
| sh2-12x1000      | 12            | 48               | 1,000            |  SMT4                      |
| sh2-16x1000      | 16            | 64               | 1,000            |  SMT4                      |
| sh2-25x1000      | 25            | 100              | 1,000            |  SMT4                      |
| sh2-4x1500       | 4             | 16               | 1,500            |  SMT4                      |
| sh2-7x1500       | 7             | 28               | 1,500            |  SMT4                      |
| sh2-12x1500      | 12            | 48               | 1,500            |  SMT4                      |
| sh2-16x1500      | 16            | 64               | 1,500            |  SMT4                      |
| sh2-25x1500      | 25            | 100              | 1,500            |  SMT4                      |
| sh2-8x1900       | 8             | 64               | 1,900            |  SMT8                      |
| sh2-16x1900      | 16            | 128              | 1,900            |  SMT8                      |
| sh2-33x1900      | 33            | 264              | 1,900            |  SMT8                      |
{: class="simple-tab-table"}
{: tab-group="sh2-certified-profiles-table"}
{: caption="P10 Certified Instance profiles with sh2 prefix" caption-side="bottom"}
{: #sh2-profiles-instance}
{: tab-title="Certified Profiles"}

| **Profile name** |**Workload type**    |**aSAPS** |**SAPS** |
| -----------------| ------------------- | -------- |-------- |
| sh2-4x256        |     OLTP            |    N/A   | 24,000  |
| sh2-7x256        |     OLTP            |    N/A   |42,000   |
| sh2-12x256       |     OLTP            |    N/A   |72,000   |
| sh2-4x384        |     OLTP            |    N/A   |24,000   |
| sh2-7x384        |     OLTP            |    N/A   | 42,000  |
| sh2-12x384       |     OLTP            |    N/A   |42,000   |
| sh2-4x512        |     OLTP            |    N/A   |24,000   |
| sh2-7x512        |     OLTP            |    N/A   |42,000   |
| sh2-12x512       |     OLTP            |    N/A   |72,000   |
| sh2-4x768        |     OLTP            |    N/A   |24,000   |
| sh2-7x768        |     OLTP            |    N/A   |42,000   |
| sh2-12x768       |     OLTP/OLAP       |    N/A   |72,000   |
| sh2-4x1000       |     OLTP            |    N/A   |24,000   |
| sh2-7x1000       |     OLTP            |    N/A   |42,000   |
| sh2-12x1000      |     OLTP/OLAP       |    N/A   |72,000   |
| sh2-16x1000      |     OLTP/OLAP       |    N/A   |96,000   |
| sh2-25x1000      |     OLTP/OLAP       |    N/A   |150,000  |
| sh2-4x1500       |     OLTP            |    N/A   |24,000   |
| sh2-7x1500       |     OLTP            |    N/A   |42,000   |
| sh2-12x1500      |     OLTP/OLAP       |    N/A   |72,000   |
| sh2-16x1500      |     OLTP/OLAP       |    N/A   |96,000   |
| sh2-25x1500      |     OLTP/OLAP       |    N/A   |150,000  |
| sh2-8x1900       |     OLTP            |    N/A   |60,800   |
| sh2-16x1900      |     OLTP/OLAP       |    N/A   |121,600  |
| sh2-33x1900      |     OLTP/OLAP       |    N/A   |250,800  |
{: class="simple-tab-table"}
{: tab-group="sh2-certified-profiles-table"}
{: caption="Sizing details for SAP HANA profiles with sh2 prefix" caption-side="bottom"}
{: #sh2-profiles-sizing}
{: tab-title="Sizing"}


| **sh2\nCertified profile** | **IOPs\nrequired** | **Log file system\nstorage config (GB)**|**Log file system\nstorage tier**|**Log file system \nsize (GB)**|**IOPs\nobtained**|
|----------------------------|--------------------|---------------------------------------- |---------------------------------|-------------------------------|------------------|
| sh2-4x256                  | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-7x256                  | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-12x256                 | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-4x384                  | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-7x384                  | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-12x384                 | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-4x512                  | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-7x512                  | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-12x512                 | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-4x768                  | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-7x768                  | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-12x768                 | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-4x1000                 | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-7x1000                 | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-12x1000                | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-16x1000                | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-25x1000                | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-4x1500                 | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-7x1500                 | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-12x1500                | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-16x1500                | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-25x1500                | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-8x1900                 | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-16x1900                | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| sh2-33x1900                | 12,000             |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
{: class="simple-tab-table"}
{: tab-group="sh2-certified-profiles-table"}
{: caption="Sample log file system configurations for SAP HANA profiles with sh2 prefix" caption-side="bottom"}
{: #sh2-profiles-log}
{: tab-title="Log File System"}

|**sh2 Certified profile** |**IOPs required**|**Data file system\nstorage config (GB)**|**Data file system \nstorage tier**|**Data file system \nsize (GB)**|**IOPs obtained**|
|--------------------------|-----------------|-----------------------------------------|-----------------------------------|--------------------------------|-----------------|
| sh2-4x256                |   8,000         |      4 x 80 GB                          | Tier 0                            |     320 GB                     | 8000            |
| sh2-7x256                |   8,000         |      4 x 80 GB                          | Tier 0                            |     320 GB                     | 8000            |
| sh2-12x256               |   8,000         |      4 x 80 GB                          | Tier 0                            |     320 GB                     | 8000            |
| sh2-4x384                |   8,000         |      4 x 116 GB                         | Tier 0                            |     464 GB                     | 11600           |
| sh2-7x384                |   8,000         |      4 x 116 GB                         | Tier 0                            |     464 GB                     | 11600           |
| sh2-12x384               |   8,000         |      4 x 116 GB                         | Tier 0                            |     464 GB                     | 11600           |
| sh2-4x512                |   8,000         |      4 x 154 GB                         | Tier 0                            |     616 GB                     | 15400           |
| sh2-7x512                |   8,000         |      4 x 154 GB                         | Tier 0                            |     616 GB                     | 15400           |
| sh2-12x512               |   8,000         |      4 x 154 GB                         | Tier 0                            |     616 GB                     | 15400           |
| sh2-4x768                |   8,000         |      4 x 230 GB                         | Tier 0                            |     920 GB                     | 23000           |
| sh2-7x768                |   8,000         |      4 x 230 GB                         | Tier 0                            |     920 GB                     | 23000           |
| sh2-12x768               |   8,000         |      4 x 230 GB                         | Tier 0                            |     920 GB                     | 23000           |
| sh2-4x1000               |   8,000         |      4 x 300 GB                         | Tier 0                            |     1,200 GB                   | 30000           |
| sh2-7x1000               |   8,000         |      4 x 300 GB                         | Tier 0                            |     1,200 GB                   | 30000           |
| sh2-12x1000              |   8,000         |      4 x 300 GB                         | Tier 0                            |     1,200 GB                   | 30000           |
| sh2-16x1000              |   8,000         |      4 x 300 GB                         | Tier 0                            |     1,200 GB                   | 30000           |
| sh2-25x1000              |   8,000         |      4 x 300 GB                         | Tier 0                            |     1,200 GB                   | 30000           |
| sh2-4x1500               |   8,000         |      4 x 450 GB                         | Tier 0                            |     1,800 GB                   | 45000           |
| sh2-7x1500               |   8,000         |      4 x 450 GB                         | Tier 0                            |     1,800 GB                   | 45000           |
| sh2-12x1500              |   8,000         |      4 x 450 GB                         | Tier 0                            |     1,800 GB                   | 45000           |
| sh2-16x1500              |   8,000         |      4 x 450 GB                         | Tier 0                            |     1,800 GB                   | 45000           |
| sh2-25x1500              |   8,000         |      4 x 450 GB                         | Tier 0                            |     1,800 GB                   | 45000           |
| sh2-8x1900               |   8,000         |      4 x 570 GB                         | Tier 0                            |     2,280 GB                   | 57000           |
| sh2-16x1900              |   8,000         |      4 x 570 GB                         | Tier 0                            |     2,280 GB                   | 57000           |
| sh2-33x1900              |   8,000         |      4 x 570 GB                         | Tier 0                            |     2,280 GB                   | 57000           |
{: class="simple-tab-table"}
{: tab-group="sh2-certified-profiles-table"}
{: caption="Sample data file system configurations for SAP HANA profiles with sh2 prefix" caption-side="bottom"}
{: #sh2-profiles-data}
{: tab-title="Data File System"}

| **sh2\nCertified profile** | **IOPs\nrequired**| **Shared file system\nstorage config (GB)** | **Shared file system\nstorage tier**  | **Shared file system\nsize(GB)**| **IOPs\nobtained** |
|--------------------------- |------------------ |-------------------------------------------- |---------------------------------------|---------------------------------|--------------------|
| sh2-4x256                  | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-7x256                  | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-12x256                 | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-4x384                  | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-7x384                  | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-12x384                 | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-4x512                  | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-7x512                  | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-12x512                 | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-4x768                  | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-7x768                  | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-12x768                 | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-4x1000                 | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-7x1000                 | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-12x1000                | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-16x1000                | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-25x1000                | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-4x1500                 | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-7x1500                 | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-12x1500                | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-16x1500                | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-25x1500                | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-8x1900                 | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-16x1900                | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
| sh2-33x1900                | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
{: class="simple-tab-table"}
{: tab-group="sh2-certified-profiles-table"}
{: caption="Sample shared file system configurations for SAP HANA profiles with sh2 prefix" caption-side="bottom"}
{: #sh2-profiles-shared}
{: tab-title="Shared File System"}


### bh2 - Certified profiles
{: #bh2-profiles}

The following SAP HANA profiles with prefix **bh2** on IBM {{site.data.keyword.powerSys_notm}} are supported:

Data volume storage is sized at 120% of memory, following SAP’s recommendation to include a 20% buffer for operational overhead.


| **Profile name** | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** | **[SMT Mode](#smt-modes)**|
| -----------------| ------------- | ---------------- | ---------------- | ------------------------- |
| bh2-35x3000      | 35            | 280              | 3000             |  SMT8                     |
| bh2-35x3900      | 35            | 280              | 3900             |  SMT8                     |
| bh2-128x16000    | 128           | 512              | 16000            |  SMT4                     |
| bh2-128x17000    | 128           | 512              | 17000            |  SMT4                     |
| bh2-165x17000    | 165           | 1320             | 17000            |  SMT8                     |
| bh2-128x18000    | 128           | 512              | 18000            |  SMT4                     |
| bh2-165x18000    | 165           | 1320             | 18000            |  SMT8                     |
| bh2-128x19000    | 128           | 512              | 19000            |  SMT4                     |
| bh2-165x19000    | 165           | 1320             | 19000            |  SMT8                     |
| bh2-128x20000    | 128           | 512              | 20000            |  SMT4                     |
| bh2-165x20000    | 165           | 1320             | 20000            |  SMT8                     |
| bh2-165x21000    | 165           | 1320             | 21000            |  SMT8                     |
| bh2-165x22000    | 165           | 1320             | 22000            |  SMT8                     |
| bh2-165x23000    | 165           | 1320             | 23000            |  SMT8                     |
| bh2-165x24000    | 165           | 1320             | 24000            |  SMT8                     |
| bh2-165x25000    | 165           | 1320             | 25000            |  SMT8                     |
| bh2-165x26000    | 165           | 1320             | 26000            |  SMT8                     |
| bh2-165x27000    | 165           | 1320             | 27000            |  SMT8                     |
| bh2-165x28000    | 165           | 1320             | 28000            |  SMT8                     |
| bh2-165x29000    | 165           | 1320             | 29000            |  SMT8                     |
| bh2-165x30500     | 165           | 1320             | 30500           |  SMT8                     |
{: class="simple-tab-table"}
{: tab-group="bh2-certified-profiles-table"}
{: caption="P10 Certified Instance profiles with bh2 prefix" caption-side="bottom"}
{: #bh2-profiles-instance}
{: tab-title="Certified Profiles"}

| **Profile name** |  **Workload type**                           |**aSAPS** | **SAPS**  |
| -----------------| ---------------------------------------------|----------| --------- |
| bh2-35x3000      |     OLTP/OLAP                                |   N/A    | 266,000   |
| bh2-35x3900      |     OLTP/OLAP                                |   N/A    | 266,000   |
| bh2-128x16000    |     OLTP/OLAP                                | 133,100  | 768,000   |
| bh2-128x17000    |     OLTP/OLAP                                | 133,100  | 768,000   |
| bh2-165x17000    |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) | 217,100  | 1,254,000 |
| bh2-128x18000    |     OLTP/OLAP                                | 133,100  | 768,000   |
| bh2-165x18000    |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) | 217,100  | 1,254,000 |
| bh2-128x19000    |     OLTP/OLAP                                |  133,100 | 768,000   |
| bh2-165x19000    |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) | 217,100  | 1,254,000 |
| bh2-128x20000    |     OLTP/OLAP                                | 133,100  | 768,000   |
| bh2-165x20000    |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) |  217,100 | 1,254,000 |
| bh2-165x21000    |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) | 217,100  | 1,254,000 |
| bh2-165x22000    |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) | 217,100  | 1,254,000 |
| bh2-165x23000    |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) | 217,100  | 1,254,000 |
| bh2-165x24000    |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) |  217,100 | 1,254,000 |
| bh2-165x25000    |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) | 217,100  | 1,254,000 |
| bh2-165x26000    |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) | 217,100  | 1,254,000 |
| bh2-165x27000    |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) | 217,100  | 1,254,000 |
| bh2-165x28000    |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) | 217,100  | 1,254,000 |
| bh2-165x29000    |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) | 217,100  | 1,254,000 |
| bh2-165x30500    |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) |   N/A    | 1,254,000 |
{: class="simple-tab-table"}
{: tab-group="bh2-certified-profiles-table"}
{: caption="Sizing details for SAP HANA profiles with bh2 prefix" caption-side="bottom"}
{: #bh2-profiles-sizing}
{: tab-title="Sizing"}


| **bh2\nCertified profile** | **IOPs\nrequired**| **Log file system\nstorage config (GB)**|**Log file system\nstorage tier**|**Log file system \nsize (GB)**|**IOPs\nobtained**|
|----------------------------|-------------------|-----------------------------------------|---------------------------------|-------------------------------|------------------|
| bh2-35x3000                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-35x3900                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-128x16000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-128x17000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-165x17000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-128x18000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-165x18000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-128x19000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-165x19000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-128x20000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-165x20000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-165x21000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-165x22000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-165x23000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-165x24000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-165x25000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-165x26000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-165x27000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-165x28000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-165x29000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| bh2-165x30500              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
{: class="simple-tab-table"}
{: tab-group="bh2-certified-profiles-table"}
{: caption="Sample log file system configurations for SAP HANA profiles with bh2 prefix" caption-side="bottom"}
{: #bh2-profiles-log}
{: tab-title="Log File System"}


| **bh2\nCertified profile** | **IOPs\nrequired**| **Data file system\nstorage config (GB)**|**Data file system\nstorage tier**|**Data file system \nsize (GB)**|**IOPs\nobtained**|
|----------------------------|-------------------|------------------------------------------|----------------------------------|--------------------------------|------------------|
|  bh2-35x3000               | 8,000             | 4 x 900 GB                               | Tier 3                           | 3600  GB                       | 10800            |
|  bh2-35x3900               | 8,000             | 4 x 1,170 GB                             | Tier 3                           | 4680  GB                       | 14040            |
|  bh2-128x16000             | 8,000             | 4 x 4,800 GB                             | Tier 3                           | 19200 GB                       | 57600            |
|  bh2-128x17000             | 8,000             | 4 x 5,100 GB                             | Tier 3                           | 20400 GB                       | 61200            |
|  bh2-165x17000             | 8,000             | 4 x 5,100 GB                             | Tier 3                           | 20400 GB                       | 61200            |
|  bh2-128x18000             | 8,000             | 4 x 5,400 GB                             | Tier 3                           | 21600 GB                       | 64800            |
|  bh2-165x18000             | 8,000             | 4 x 5,400 GB                             | Tier 3                           | 21600 GB                       | 64800            |
|  bh2-128x19000             | 8,000             | 4 x 5,700 GB                             | Tier 3                           | 22800 GB                       | 68400            |
|  bh2-165x19000             | 8,000             | 4 x 5,700 GB                             | Tier 3                           | 22800 GB                       | 68400            |
|  bh2-128x20000             | 8,000             | 4 x 6,000 GB                             | Tier 3                           | 24000 GB                       | 72000            |
|  bh2-165x20000             | 8,000             | 4 x 6,000 GB                             | Tier 3                           | 24000 GB                       | 72000            |
|  bh2-165x21000             | 8,000             | 4 x 6,300 GB                             | Tier 3                           | 25200 GB                       | 75600            |
|  bh2-165x22000             | 8,000             | 4 x 6,600 GB                             | Tier 3                           | 26400 GB                       | 79200            |
|  bh2-165x23000             | 8,000             | 4 x 6,900 GB                             | Tier 3                           | 27600 GB                       | 82800            |
|  bh2-165x24000             | 8,000             | 4 x 7,200 GB                             | Tier 3                           | 28800 GB                       | 86400            |
|  bh2-165x25000             | 8,000             | 4 x 7,500 GB                             | Tier 3                           | 30000 GB                       | 90000            |
|  bh2-165x26000             | 8,000             | 4 x 7,800 GB                             | Tier 3                           | 31200 GB                       | 93600            |
|  bh2-165x27000             | 8,000             | 4 x 8,100 GB                             | Tier 3                           | 32400 GB                       | 97200            |
|  bh2-165x28000             | 8,000             | 4 x 8,400 GB                             | Tier 3                           | 33600 GB                       | 100800           |
|  bh2-165x29000             | 8,000             | 4 x 8,700 GB                             | Tier 3                           | 34800 GB                       | 104400           |
|  bh2-165x30500             | 8,000             | 4 x 9,150 GB                             | Tier 3                           | 36600 GB                       | 109800           |
{: class="simple-tab-table"}
{: tab-group="bh2-certified-profiles-table"}
{: caption="Sample data file system configurations for SAP HANA profiles with bh2 prefix" caption-side="bottom"}
{: #bh2-profiles-data}
{: tab-title="Data File System"}


| **bh2\nCertified profile** | **IOPs\nrequired**| **Shared file system\nstorage config (GB)** | **Shared file system\nstorage tier**  | **Shared file system\nsize(GB)**| **IOPs\nobtained** |
|--------------------------- |------------------ |-------------------------------------------- |---------------------------------------|---------------------------------|--------------------|
|    bh2-35x3000             | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-35x3900             | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-128x16000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-128x17000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-165x17000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-128x18000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-165x18000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-128x19000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-165x19000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-128x20000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-165x20000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-165x21000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-165x22000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-165x23000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-165x24000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-165x25000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-165x26000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-165x27000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-165x28000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-165x29000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    bh2-165x30500           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
{: class="simple-tab-table"}
{: tab-group="bh2-certified-profiles-table"}
{: caption="Sample shared file system configurations for SAP HANA profiles with bh2 prefix" caption-side="bottom"}
{: #bh2-profiles-shared}
{: tab-title="Shared File System"}




### ch2 - Certified profiles
{: #ch2-profiles}

The following SAP HANA profiles with prefix **ch2** on IBM {{site.data.keyword.powerSys_notm}} are supported:

Data volume storage is sized at 120% of memory, following SAP’s recommendation to include a 20% buffer for operational overhead.


| **Profile name** | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** | **[SMT Mode](#smt-modes)** |
| -----------------| ------------- | ---------------- | ---------------- | ---------------------------|
| ch2-87x6000      | 87            | 696              | 6,000            |  SMT8                      |
| ch2-80x6144      | 80            | 640              | 6,144            |  SMT8                      |
| ch2-87x7000      | 87            | 696              | 7,000            |  SMT8                      |
| ch2-87x7600      | 87            | 696              | 7,600            |  SMT8                      |
| ch2-165x12000    | 165           | 1320             | 12,000           |  SMT8                      |
| ch2-165x13000    | 165           | 1320             | 13,000           |  SMT8                      |
| ch2-165x14000    | 165           | 1320             | 14,000           |  SMT8                      |
| ch2-165x15000    | 165           | 1320             | 15,000           |  SMT8                      |
| ch2-165x16000    | 165           | 1320             | 16,000           |  SMT8                      |
{: class="simple-tab-table"}
{: tab-group="ch2-certified-profiles-table"}
{: caption="P10 Certified Instance profiles with ch2 prefix" caption-side="bottom"}
{: #ch2-profiles-instance}
{: tab-title="Certified Profiles"}

| **Profile name** |    **Workload type**                         |**aSAPS**| **SAPS**   |
| -----------------|  ------------------------------------------- |---------| ---------- |
| ch2-87x6000      |     OLTP/OLAP                                |   N/A   | 661,200    |
| ch2-80x6144      |    OLTP/OLAP                                 |   N/A   | 608,000    |
| ch2-87x7000      |     OLTP/OLAP                                |   N/A   |661,200     |
| ch2-87x7600      |    OLTP/OLAP                                 |   N/A   |661,200     |
| ch2-165x12000    |     OLTP/OLAP                                | 217,100 |1,254,000   |
| ch2-165x13000    |     OLTP/OLAP                                |217,100  |1,254,000   |
| ch2-165x14000    |     OLTP/OLAP                                | 217,100 |1,254,000   |
| ch2-165x15000    |     OLTP/OLAP                                | 217,100 |1,254,000   |
| ch2-165x16000    |     OLTP/OLAP/OLTP scale-out (up to 4 nodes) | 217,100 |1,254,000   |
{: class="simple-tab-table"}
{: tab-group="ch2-certified-profiles-table"}
{: caption="Sizing details for SAP HANA profiles with ch2 prefix" caption-side="bottom"}
{: #ch2-profiles-sizing}
{: tab-title="Sizing"}


| **ch2\nCertified profile** | **IOPs\nrequired**| **Log file system\nstorage config (GB)**|**Log file system\nstorage tier**|**Log file system \nsize (GB)**|**IOPs\nobtained**|
|----------------------------|-------------------|-----------------------------------------|---------------------------------|-------------------------------|------------------|
| ch2-87x6000                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| ch2-80x6144                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| ch2-87x7000                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| ch2-87x7600                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| ch2-165x12000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| ch2-165x13000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| ch2-165x14000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| ch2-165x15000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| ch2-165x16000              | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
{: class="simple-tab-table"}
{: tab-group="ch2-certified-profiles-table"}
{: caption="Sample log file system configurations for SAP HANA profiles with ch2 prefix" caption-side="bottom"}
{: #ch2-profiles-log}
{: tab-title="Log File System"}


| **ch2\nCertified profile** | **IOPs\nrequired**| **Data file system\nstorage config (GB)**|**Data file system\nstorage tier**|**Data file system \nsize (GB)**|**IOPs\nobtained**|
|----------------------------|-------------------|------------------------------------------|----------------------------------|--------------------------------|------------------|
|  ch2-87x6000               | 8,000             | 4 x 1,800 GB                             | Tier 3                           | 7200 GB                        | 28,998           |
|  ch2-80x6144               | 8,000             | 4 x 1,843 GB                             | Tier 3                           | 7372 GB                        | 29,691           |
|  ch2-87x7000               | 8,000             | 4 x 2,100 GB                             | Tier 3                           | 8400 GB                        | 33,831           |
|  ch2-87x7600               | 8,000             | 4 x 2,280 GB                             | Tier 3                           | 9120 GB                        | 36,729           |
|  ch2-165x12000             | 8,000             | 4 x 3,600 GB                             | Tier 3                           | 14400 GB                       | 57,996           |
|  ch2-165x13000             | 8,000             | 4 x 3,900 GB                             | Tier 3                           | 15600 GB                       | 62,829           |
|  ch2-165x14000             | 8,000             | 4 x 4,200 GB                             | Tier 3                           | 16800 GB                       | 67,662           |
|  ch2-165x15000             | 8,000             | 4 x 4,500 GB                             | Tier 3                           | 18000 GB                       | 72,495           |
|  ch2-165x16000             | 8,000             | 4 x 4,800 GB                             | Tier 3                           | 19200 GB                       | 77,328           |
{: class="simple-tab-table"}
{: tab-group="ch2-certified-profiles-table"}
{: caption="Sample data file system configurations for SAP HANA profiles with ch2 prefix" caption-side="bottom"}
{: #ch2-profiles-data}
{: tab-title="Data File System"}


| **ch2\nCertified profile** | **IOPs\nrequired**| **Shared file system\nstorage config (GB)** | **Shared file system\nstorage tier**  | **Shared file system\nsize(GB)**| **IOPs\nobtained** |
|--------------------------- |------------------ |-------------------------------------------- |---------------------------------------|---------------------------------|--------------------|
|    ch2-87x6000             | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    ch2-80x6144             | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    ch2-87x7000             | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    ch2-87x7600             | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    ch2-165x12000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    ch2-165x13000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    ch2-165x14000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    ch2-165x15000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|    ch2-165x16000           | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
{: class="simple-tab-table"}
{: tab-group="ch2-certified-profiles-table"}
{: caption="Sample shared file system configurations for SAP HANA profiles with ch2 prefix" caption-side="bottom"}
{: #ch2-profiles-shared}
{: tab-title="Shared File System"}



### mh2 - Certified profiles
{: #mh2-profiles}

The following SAP HANA profiles with prefix **mh2** on IBM {{site.data.keyword.powerSys_notm}} are supported:

Data volume storage is sized at 120% of memory, following SAP’s recommendation to include a 20% buffer for operational overhead.

| **Profile name** | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** | **[SMT Mode](#smt-modes)** |
| -----------------| ------------- | ---------------- | ---------------- | ---------------------------|
| mh2-64x8000      | 64            | 256              | 8,000            |  SMT4                      |
| mh2-64x9000      | 64            | 256              | 9,000            |  SMT4                      |
| mh2-64x10000     | 64            | 256              | 10,000           |  SMT4                      |
| mh2-64x11000     | 64            | 256              | 11,000           |  SMT4                      |
| mh2-64x12000     | 64            | 256              | 12,000           |  SMT4                      |
| mh2-64x13000     | 64            | 256              | 13,000           |  SMT4                      |
| mh2-64x14000     | 64            | 256              | 14,000           |  SMT4                      |
| mh2-64x15000     | 64            | 256              | 15,000           |  SMT4                      |
{: class="simple-tab-table"}
{: tab-group="mh2-certified-profiles-table"}
{: caption="P10 Certified Instance profiles with mh2 prefix" caption-side="bottom"}
{: #mh2-profiles-instance}
{: tab-title="Certified Profiles"}


| **Profile name** |  **Workload type**    |**aSAPS**| **SAPS** |
| -----------------|---------------------- |---------| -------- |
| mh2-64x8000      |       OLTP/OLAP       | 65,400  | 384,000  |
| mh2-64x9000      |       OLTP/OLAP       | 65,400  | 384,000  |
| mh2-64x10000     |       OLTP/OLAP       | 65,400  | 384,000  |
| mh2-64x11000     |      OLTP/OLAP        | 65,400  | 384,000  |
| mh2-64x12000     |       OLTP/OLAP       | 65,400  | 384,000  |
| mh2-64x13000     |       OLTP/OLAP       | 65,400  | 384,000  |
| mh2-64x14000     |       OLTP/OLAP       | 65,400  | 384,000  |
| mh2-64x15000     |       OLTP/OLAP       |  65,400 | 384,000  |
{: class="simple-tab-table"}
{: tab-group="mh2-certified-profiles-table"}
{: caption="Sizing details for SAP HANA profiles with mh2 prefix" caption-side="bottom"}
{: #mh2-profiles-sizing}
{: tab-title="Sizing"}



| **mh2\nCertified profile** | **IOPs\nrequired**| **Log file system\nstorage config (GB)**|**Log file system\nstorage tier**|**Log file system \nsize (GB)**|**IOPs\nobtained**|
|----------------------------|-------------------|-----------------------------------------|---------------------------------|-------------------------------|------------------|
| mh2-64x8000                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| mh2-64x9000                | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| mh2-64x10000               | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| mh2-64x11000               | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| mh2-64x12000               | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| mh2-64x13000               | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| mh2-64x14000               | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
| mh2-64x15000               | 12,000            |   4 x 128 GB                            | Tier 0                          | 512 GB                        | 12,800           |
{: class="simple-tab-table"}
{: tab-group="mh2-certified-profiles-table"}
{: caption="Sample log file system configurations for SAP HANA profiles with mh2 prefix" caption-side="bottom"}
{: #mh2-profiles-log}
{: tab-title="Log File System"}


| **mh2\nCertified profile** | **IOPs\nrequired**| **Data file system\nstorage config (GB)**|**Data file system\nstorage tier**|**Data file system \nsize (GB)**|**IOPs\nobtained**|
|----------------------------|-------------------|------------------------------------------|----------------------------------|--------------------------------|------------------|
|  mh2-64x8000               | 8,000             | 4 x 2400 GB                              | Tier 3                           |     9600 GB                    |  28800           |
|  mh2-64x9000               | 8,000             | 4 x 2700 GB                              | Tier 3                           |     10800 GB                   |  32400           |
|  mh2-64x10000              | 8,000             | 4 x 3000 GB                              | Tier 3                           |     12000 GB                   |  36000           |
|  mh2-64x11000              | 8,000             | 4 x 3300 GB                              | Tier 3                           |     13200 GB                   |  39600           |
|  mh2-64x12000              | 8,000             | 4 x 3600 GB                              | Tier 3                           |     14400 GB                   |  43200           |
|  mh2-64x13000              | 8,000             | 4 x 3900 GB                              | Tier 3                           |     15600 GB                   |  46800           |
|  mh2-64x14000              | 8,000             | 4 x 4200 GB                              | Tier 3                           |     16800 GB                   |  50400           |
|  mh2-64x15000              | 8,000             | 4 x 4500 GB                              | Tier 3                           |     18000 GB                   |  54000           |
{: class="simple-tab-table"}
{: tab-group="mh2-certified-profiles-table"}
{: caption="Sample data file system configurations for SAP HANA profiles with mh2 prefix" caption-side="bottom"}
{: #mh2-profiles-data}
{: tab-title="Data File System"}


| **mh2\nCertified profile** | **IOPs\nrequired**| **Shared file system\nstorage config (GB)** | **Shared file system\nstorage tier**  | **Shared file system\nsize(GB)**| **IOPs\nobtained** |
|--------------------------- |------------------ |-------------------------------------------- |---------------------------------------|---------------------------------|--------------------|
|  mh2-64x8000               | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|  mh2-64x9000               | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|  mh2-64x10000              | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|  mh2-64x11000              | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|  mh2-64x12000              | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|  mh2-64x13000              | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|  mh2-64x14000              | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
|  mh2-64x15000              | 3,000             |  1 x 200 GB                                 | Tier 0                                | 200 GB                          | 5,000              |
{: class="simple-tab-table"}
{: tab-group="mh2-certified-profiles-table"}
{: caption="Sample shared file system configurations for SAP HANA profiles with mh2 prefix" caption-side="bottom"}
{: #mh2-profiles-shared}
{: tab-title="Shared File System"}


For IBM Power10 profiles, "N/A" indicates that SAPS are not measured.
{:note: .note}

## IBM Power9 certified instances for SAP HANA
{: #sap-hana-iaas-offerings-profiles-power9-families}


The following profile families are available for IBM Power9 processor-based servers.

| Families | Description |
| -------- | ----------- |
| [Profiles with cnp prefix](#cnpprofiles) | Profiles with the prefix *cnp* are custom profiles for test or development use only. These profiles are not intended for production use and are not supported or certified for SAP production. Each profile must have minimum of two physical CPU cores. |
| [Profiles with ush1 prefix](#ush1profiles) | Profiles with the prefix *ush* are small profiles and are best suited for balanced workloads that require less CPU and storage consumption. |
| [Profiles with bh1 prefix](#bh1profiles) | Profiles with the prefix *bh1* are balanced profiles and are best suited for midsize databases and common cloud applications with moderate traffic. |
| [Profiles with ch1 prefix](#ch1profiles)  | Profiles with the prefix *ch1* are compute intensive profiles and are best suited for moderate to high web traffic workloads. Compute profiles are best suited for cpu-intensive workloads, such as heavy web traffic, production batch processing, and front-end web servers. |
| [Profiles with mh1 prefix](#mh1profiles) | Profiles with the prefix *mh1* are very high memory profiles and are best suited for server OLAP databases, such as SAP NetWeaver. |
| [Profiles with umh prefix](#umhprofiles) | Profiles with the prefix *umh* are ultra memory profiles that provide the highest vCPU-to-memory ratios for serving in-memory OLTP databases, such as SAP HANA. |
{: caption="Profile families for Power9 server generation" caption-side="bottom"}

### ush1 - Certified profiles
{: #ush1profiles}

The following SAP HANA profiles with prefix **ush1** on IBM {{site.data.keyword.powerSys_notm}} are supported:

Data volume storage is sized at 120% of memory, following SAP’s recommendation to include a 20% buffer for operational overhead.

| **Profile name**    | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** | **SAPS** | **[SMT Mode](#smt-modes)** | **Workload type** |
| ------------------- | ------------- | ---------------- | ---------------- | -------- | -------------------------- | ----------------- |
| ush1-4x128          | 4             | 32               | 128              | 24,000   | SMT8                       | OLAP/OLTP         |
| ush1-4x256          | 4             | 32               | 256              | 24,000   | SMT8                       | OLAP/OLTP         |
| ush1-4x384          | 4             | 32               | 384              | 24,000   | SMT8                       | OLAP/OLTP         |
| ush1-4x512          | 4             | 32               | 512              | 24,000   | SMT8                       | OLAP/OLTP         |
| ush1-4x768          | 4             | 32               | 768              | 24,000   | SMT8                       | OLAP/OLTP         |
{: class="simple-tab-table"}
{: tab-group="ush1-table"}
{: caption="P9 Certified Instance profiles with ush1 prefix" caption-side="bottom"}
{: #ush1-profiles-instance}
{: tab-title="Instance Profile"}

| ush1\nCertified profile | IOPs\nrequired| Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained| Alternative configuration\n(cost effective)|
|-------------------------|---------------|-------------------------|----------------------|---------------|--------------------------------------------|
| ush1-4x128              | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        | 4 x 16 GB \nusing 'Fixed IOPs'             |
| ush1-4x256              | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        | 4 x 32 GB \nusing 'Fixed IOPs'             |
| ush1-4x384              | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        | 4 x 48 GB \nusing 'Fixed IOPs'             |
| ush1-4x512              | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        | 4 x 64 GB \nusing 'Fixed IOPs'             |
| ush1-4x768              | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        | 4 x 96 GB \nusing 'Fixed IOPs'             |
{: class="simple-tab-table"}
{: tab-group="ush1-table"}
{: caption="Sample log file system configurations for SAP HANA profiles with ush1 prefix" caption-side="bottom"}
{: #ush1-profiles-log}
{: tab-title="Log File System"}

| ush1\nCertified profile | IOPs\nrequired| Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained| Alternative configuration\n(cost effective)|
|-------------------------|---------------|-------------------------|----------------------|---------------|--------------------------------------------|
| ush1-4x128              | 8,000         | 4 x 670 GB              | Tier 3               | 8,040         | 4 x 81 GB  \nusing 'Tier 0'                |
| ush1-4x256              | 8,000         | 4 x 670 GB              | Tier 3               | 8,040         | 4 x 103 GB \nusing 'Tier 0'                |
| ush1-4x384              | 8,000         | 4 x 670 GB              | Tier 3               | 8,040         | 4 x 154 GB \nusing 'Tier 0'                |
| ush1-4x512              | 8,000         | 4 x 670 GB              | Tier 3               | 8,040         | 4 x 206 GB \nusing 'Tier 0'                |
| ush1-4x768              | 8,000         | 4 x 670 GB              | Tier 3               | 8,040         | 4 x 309 GB \nusing 'Tier 0'                |
{: class="simple-tab-table"}
{: tab-group="ush1-table"}
{: caption="Sample data file system configurations for SAP HANA profiles with ush1 prefix" caption-side="bottom"}
{: #ush1-profiles-data}
{: tab-title="Data File System"}

| ush1\nCertified profile | IOPs\nrequired| Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained| Alternative configuration    |
|-------------------------|---------------|-------------------------|----------------------|---------------|----------------------------- |
| ush1-4x128              | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| ush1-4x256              | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| ush1-4x384              | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| ush1-4x512              | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| ush1-4x768              | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
{: class="simple-tab-table"}
{: tab-group="ush1-table"}
{: caption="Sample shared file system configurations for SAP HANA profiles with ush1 prefix" caption-side="bottom"}
{: #ush1-profiles-shared}
{: tab-title="Shared File System"}


### bh1 - Certified profiles
{: #bh1profiles}

The following SAP HANA profiles with prefix **bh1** on IBM {{site.data.keyword.powerSys_notm}} are supported:

Data volume storage is sized at 120% of memory, following SAP’s recommendation to include a 20% buffer for operational overhead.

| **Profile name**    | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** | **SAPS** | **[SMT Mode](#smt-modes)**       | **Workload type**                        |
| ------------------- | ------------- | ---------------- | ---------------- | -------- | -------------------------------- | -----------------------------------------|
| bh1-16x1600         | 16            | 128              | 1,600            | 96,000   | SMT8                             | OLAP/OLTP                                |
| bh1-20x2000         | 20            | 160              | 2,000            | 120,000  | SMT8                             | OLAP                                     |
| bh1-22x2200         | 22            | 178              | 2,200            | 132,000  | SMT8                             | OLAP                                     |
| bh1-25x2500         | 25            | 200              | 2,500            | 150,000  | SMT8                             | OLAP                                     |
| bh1-30x3000         | 30            | 240              | 3,000            | 180,000  | SMT8                             | OLAP/OLAP scale-out (up to 3 nodes)      |
| bh1-35x3500         | 35            | 280              | 3,500            | 210,000  | SMT8                             | OLAP                                     |
| bh1-40x4000         | 40            | 320              | 4,000            | 240,000  | SMT8                             | OLAP                                     |
| bh1-50x5000         | 50            | 400              | 5,000            | 300,000  | SMT8                             | OLAP                                     |
| bh1-60x6000         | 60            | 480              | 6,000            | 360,000  | SMT8                             | OLAP/OLTP/OLAP scale-out (up to 3 nodes) |
| bh1-70x7000         | 70            | 560              | 7,000            | 420,000  | SMT8                             | OLAP                                     |
| bh1-80x8000         | 80            | 640              | 8,000            | 480,000  | SMT8                             | OLAP                                     |
| bh1-100x10000       | 100           | 800              | 10,000           | 600,000  | SMT8                             | OLAP                                     |
| bh1-120x12000       | 120           | 900              | 12,000           | 720,000  | SMT8                             | OLAP                                     |
| bh1-140x14000       | 140           | 1,120            | 14,000           | 840,000  | SMT8                             | OLAP                                     |
{: class="simple-tab-table"}
{: tab-group="bh1-table"}
{: caption="P9 Certified Instance profiles with bh1 prefix" caption-side="bottom"}
{: #bh1-profiles-instance}
{: tab-title="Instance Profile"}

| bh1 \nCertified profile | IOPs\nrequired| Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained|
|-------------------------|---------------|-------------------------|----------------------|---------------|
| bh1-16x1600             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| bh1-20x2000             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| bh1-22x2200             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| bh1-25x2500             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| bh1-30x3000             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| bh1-35x3500             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| bh1-40x4000             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| bh1-50x5000             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| bh1-60x6000             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| bh1-70x7000             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| bh1-80x8000             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| bh1-100x10000           | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| bh1-120x12000           | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| bh1-140x14000           | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
{: class="simple-tab-table"}
{: tab-group="bh1-table"}
{: caption="Sample log file system configurations for SAP HANA profiles with bh1 prefix" caption-side="bottom"}
{: #bh1-profiles-log}
{: tab-title="Log File System"}

| bh1\nCertified profile | IOPs\nrequired| Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained|
|-------------------------|---------------|------------------------|----------------------|---------------|
| bh1-16x1600             | 8,000         | 4 x 670 GB             | Tier 3               | 8,040         |
| bh1-20x2000             | 8,000         | 4 x 805 GB             | Tier 3               | 9,666         |
| bh1-22x2200             | 8,000         | 4 x 886 GB             | Tier 3               | 10,632        |
| bh1-25x2500             | 8,000         | 4 x 1006 GB            | Tier 3               | 12,081        |
| bh1-30x3000             | 8,000         | 4 x 1208 GB            | Tier 3               | 14,499        |
| bh1-35x3500             | 8,000         | 4 x 1409 GB            | Tier 3               | 16,914        |
| bh1-40x4000             | 8,000         | 4 x 1611 GB            | Tier 3               | 19,332        |
| bh1-50x5000             | 8,000         | 4 x 2013 GB            | Tier 3               | 24,165        |
| bh1-60x6000             | 8,000         | 4 x 2416 GB            | Tier 3               | 28,998        |
| bh1-70x7000             | 8,000         | 4 x 2819 GB            | Tier 3               | 33,831        |
| bh1-80x8000             | 8,000         | 4 x 3222 GB            | Tier 3               | 38,664        |
| bh1-100x10000           | 8,000         | 4 x 4027 GB            | Tier 3               | 48,330        |
| bh1-120x12000           | 8,000         | 4 x 4833 GB            | Tier 3               | 19,332        |
| bh1-140x14000           | 8,000         | 4 x 5638 GB            | Tier 3               | 67,662        |
{: class="simple-tab-table"}
{: tab-group="bh1-table"}
{: caption="Sample data file system configurations for SAP HANA profiles with bh1 prefix" caption-side="bottom"}
{: #bh1-profiles-data}
{: tab-title="Data File System"}


| bh1 \nCertified profile | IOPs\nrequired| Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained| Alternative configuration    |
|-------------------------|---------------|-------------------------|----------------------|---------------|----------------------------- |
| bh1-16x1600             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| bh1-20x2000             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| bh1-22x2200             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| bh1-25x2500             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| bh1-30x3000             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| bh1-35x3500             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| bh1-40x4000             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| bh1-50x5000             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| bh1-60x6000             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| bh1-70x7000             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| bh1-80x8000             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| bh1-100x10000           | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| bh1-120x12000           | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| bh1-140x14000           | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
{: class="simple-tab-table"}
{: tab-group="bh1-table"}
{: caption="Sample shared file system configurations for SAP HANA profiles with bh1 prefix" caption-side="bottom"}
{: #bh1-profiles-shared}
{: tab-title="Shared File System"}

### ch1 - Certified profiles
{: #ch1profiles}

The following SAP HANA profiles with prefix **ch1** on IBM {{site.data.keyword.powerSys_notm}} are supported:

Data volume storage is sized at 120% of memory, following SAP’s recommendation to include a 20% buffer for operational overhead.

| **Profile name**    | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** | **SAPS** | **[SMT Mode](#smt-modes)** | **Workload type**                   |
| ------------------- | ------------- | ---------------- | ---------------- | -------- | -------------------------- | ----------------------------------- |
| ch1-60x3000         | 60            | 480              | 3,000            | 360,000  | SMT8                       | OLAP/OLAP scale-out (up to 3 nodes) |
| ch1-70x3500         | 70            | 560              | 3,500            | 420,000  | SMT8                       | OLAP                                |
| ch1-80x4000         | 80            | 640              | 4,000            | 480,000  | SMT8                       | OLAP                                |
| ch1-100x5000        | 100           | 800              | 5,000            | 600,000  | SMT8                       | OLAP                                |
| ch1-120x6000        | 120           | 900              | 6,000            | 720,000  | SMT8                       | OLAP                                |
| ch1-140x7000        | 140           | 1,120            | 7,000            | 840,000  | SMT8                       | OLAP                                |
{: class="simple-tab-table"}
{: tab-group="ch1-table"}
{: caption="P9 Certified Instance profiles with ch1 prefix" caption-side="bottom"}
{: #ch1-profiles-instance}
{: tab-title="Instance Profile"}

| ch1 \nCertified profile | IOPs\nrequired| Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained|
|-------------------------|---------------|-------------------------|----------------------|---------------|
| ch1-60x3000             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| ch1-70x3500             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| ch1-80x4000             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| ch1-100x5000            | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| ch1-120x6000            | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| ch1-140x7000            | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
{: class="simple-tab-table"}
{: tab-group="ch1-table"}
{: caption="Sample log file system configurations for SAP HANA profiles with ch1 prefix" caption-side="bottom"}
{: #ch1-profiles-log}
{: tab-title="Log File System"}

| ch1\n Certified profile | IOPs\nrequired| Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained|
|-------------------------|---------------|-------------------------|----------------------|---------------|
| ch1-60x3000             | 8,000         | 4 x 1208 GB             | Tier 3               | 14,499        |
| ch1-70x3500             | 8,000         | 4 x 1409 GB             | Tier 3               | 16,914        |
| ch1-80x4000             | 8,000         | 4 x 1611 GB             | Tier 3               | 19,332        |
| ch1-100x5000            | 8,000         | 4 x 2013 GB             | Tier 3               | 24,165        |
| ch1-120x6000            | 8,000         | 4 x 2416 GB             | Tier 3               | 28,998        |
| ch1-140x7000            | 8,000         | 4 x 2819 GB             | Tier 3               | 33,831        |
{: class="simple-tab-table"}
{: tab-group="ch1-table"}
{: caption="Sample data file system configurations for SAP HANA profiles with ch1 prefix" caption-side="bottom"}
{: #ch1-profiles-data}
{: tab-title="Data File System"}

| ch1 \nCertified profile | IOPs\nrequired| Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained| Alternative configuration    |
|-------------------------|---------------|-------------------------|----------------------|---------------|----------------------------- |
| ch1-60x3000             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| ch1-70x3500             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| ch1-80x4000             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| ch1-100x5000            | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| ch1-120x6000            | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| ch1-140x7000            | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
{: class="simple-tab-table"}
{: tab-group="ch1-table"}
{: caption="Sample shared file system configurations for SAP HANA profiles with ch1 prefix" caption-side="bottom"}
{: #ch1-profiles-shared}
{: tab-title="Shared File System"}


### mh1 - Certified profiles
{: #mh1profiles}

The following SAP HANA profiles with prefix **mh1** on IBM {{site.data.keyword.powerSys_notm}} are supported:

Data volume storage is sized at 120% of memory, following SAP’s recommendation to include a 20% buffer for operational overhead.

| **Profile name**    | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** | **SAPS** | **[SMT Mode](#smt-modes)** | **Workload type** |
| ------------------- | ------------- | ---------------- | ---------------- | -------- | ---------------------------| ----------------- |
| mh1-8x1440          | 8             | 64               | 1,440            | 48,000   | SMT8                       | OLAP              |
| mh1-10x1800         | 10            | 80               | 1,800            | 60,000   | SMT8                       | OLAP              |
| mh1-12x2160         | 12            | 96               | 2,160            | 72,000   | SMT8                       | OLAP              |
| mh1-16x2880         | 16            | 128              | 2,880            | 96,000   | SMT8                       | OLAP              |
| mh1-20x3600         | 20            | 160              | 3,600            | 120,000  | SMT8                       | OLAP              |
| mh1-22x3960         | 22            | 176              | 3,960            | 132,000  | SMT8                       | OLAP              |
| mh1-25x4500         | 25            | 200              | 4,500            | 150,000  | SMT8                       | OLAP              |
| mh1-30x5400         | 30            | 240              | 5,400            | 180,000  | SMT8                       | OLAP              |
| mh1-35x6300         | 35            | 280              | 6,300            | 210,000  | SMT8                       | OLAP              |
| mh1-40x7200         | 40            | 320              | 7,200            | 240,000  | SMT8                       | OLAP              |
| mh1-50x9000         | 50            | 400              | 9,000            | 300,000  | SMT8                       | OLAP              |
| mh1-60x10800        | 60            | 460              | 10,800           | 360,000  | SMT8                       | OLAP              |
| mh1-70x12600        | 70            | 560              | 12,600           | 420,000  | SMT8                       | OLAP              |
| mh1-80x14400        | 80            | 640              | 14,400           | 480,000  | SMT8                       | OLAP              |
| mh1-90x16200        | 90            | 720              | 16,200           | 540,000  | SMT8                       | OLAP              |
| mh1-100x18000       | 100           | 800              | 18,000           | 600,000  | SMT8                       | OLAP              |
| mh1-125x22500       | 125           | 1,000            | 22,500           | 750,000  | SMT8                       | OLAP              |
{: class="simple-tab-table"}
{: tab-group="mh1-table"}
{: caption="P9 Certified Instance profiles with mh1 prefix" caption-side="bottom"}
{: #mh1-profiles-instance}
{: tab-title="Instance Profile"}


| mh1 \nCertified profile | IOPs\nrequired| Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained|
|-------------------------|---------------|-------------------------|----------------------|---------------|
| mh1-8x1440              | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| mh1-10x1800             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| mh1-12x2160             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| mh1-16x2880             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| mh1-20x3600             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| mh1-22x3960             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| mh1-25x4500             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| mh1-30x5400             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| mh1-35x6300             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| mh1-40x7200             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| mh1-50x9000             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| mh1-60x10800            | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| mh1-70x12600            | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| mh1-80x14400            | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| mh1-90x16200            | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| mh1-100x18000           | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| mh1-125x22500           | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
{: class="simple-tab-table"}
{: tab-group="mh1-table"}
{: caption="Sample log file system configurations for SAP HANA profiles with mh1 prefix" caption-side="bottom"}
{: #mh1-profiles-log}
{: tab-title="Log File System"}

| mh1 \nCertified profile | IOPs\nrequired| Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained|
|-------------------------|---------------|-------------------------|----------------------|---------------|
| mh1-8x1440              | 8,000         | 4 x 670 GB              | Tier 3               | 8,040         |
| mh1-10x1800             | 8,000         | 4 x 670 GB              | Tier 3               | 8,040         |
| mh1-12x2160             | 8,000         | 4 x 869 GB              | Tier 3               | 10,437        |
| mh1-16x2880             | 8,000         | 4 x 1159 GB             | Tier 3               | 13,917        |
| mh1-20x3600             | 8,000         | 4 x 1449 GB             | Tier 3               | 17,397        |
| mh1-22x3960             | 8,000         | 4 x 1594 GB             | Tier 3               | 19,137        |
| mh1-25x4500             | 8,000         | 4 x 1812 GB             | Tier 3               | 21,747        |
| mh1-30x5400             | 8,000         | 4 x 2174 GB             | Tier 3               | 26,097        |
| mh1-35x6300             | 8,000         | 4 x 2537 GB             | Tier 3               | 30,447        |
| mh1-40x7200             | 8,000         | 4 x 2899 GB             | Tier 3               | 34,797        |
| mh1-50x9000             | 8,000         | 4 x 3624 GB             | Tier 3               | 43,497        |
| mh1-60x10800            | 8,000         | 4 x 4349 GB             | Tier 3               | 51,194        |
| mh1-70x12600            | 8,000         | 4 x 5074 GB             | Tier 3               | 60,894        |
| mh1-80x14400            | 8,000         | 4 x 5799 GB             | Tier 3               | 69,594        |
| mh1-90x16200            | 8,000         | 4 x 6524 GB             | Tier 3               | 78,294        |
| mh1-100x18000           | 8,000         | 4 x 7249 GB             | Tier 3               | 86,994        |
| mh1-125x22500           | 8,000         | 4 x 9061 GB             | Tier 3               | 108,741       |
{: class="simple-tab-table"}
{: tab-group="mh1-table"}
{: caption="Sample data file system configurations for SAP HANA profiles with mh1 prefix" caption-side="bottom"}
{: #mh1-profiles-data}
{: tab-title="Data File System"}


| mh1 \nCertified profile | IOPs\nrequired| Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained| Alternative configuration    |
|-------------------------|---------------|-------------------------|----------------------|---------------|----------------------------- |
| mh1-8x1440              | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| mh1-10x1800             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| mh1-12x2160             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| mh1-16x2880             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| mh1-20x3600             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| mh1-22x3960             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| mh1-25x4500             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| mh1-30x5400             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| mh1-35x6300             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| mh1-40x7200             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| mh1-50x9000             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| mh1-60x10800            | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| mh1-70x12600            | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| mh1-80x14400            | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| mh1-90x16200            | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| mh1-100x18000           | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| mh1-125x22500           | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
{: class="simple-tab-table"}
{: tab-group="mh1-table"}
{: caption="Sample shared file system configurations for SAP HANA profiles with mh1 prefix" caption-side="bottom"}
{: #mh1-profiles-shared}
{: tab-title="Shared File System"}


### umh - Certified profiles
{: #umhprofiles}

The following SAP HANA profiles with prefix **umh** on IBM {{site.data.keyword.powerSys_notm}} are supported:

Data volume storage is sized at 120% of memory, following SAP’s recommendation to include a 20% buffer for operational overhead.

| **Profile name**    | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** | **SAPS** | **[SMT Mode](#smt-modes)** | **Workload type** |
| ------------------- | ------------- | ---------------- | ---------------- | -------- | -------------------------- | ----------------- |
| umh-4x960           | 4             | 32               | 960              | 24,000   | SMT8                       | OLTP              |
| umh-6x1440          | 6             | 48               | 1,440            | 36,000   | SMT8                       | OLTP              |
| umh-8x1920          | 8             | 64               | 1,920            | 48,000   | SMT8                       | OLTP              |
| umh-10x2400         | 10            | 80               | 2,400            | 60,000   | SMT8                       | OLTP              |
| umh-12x2880         | 12            | 96               | 2,880            | 72,000   | SMT8                       | OLTP              |
| umh-16x3840         | 16            | 128              | 3,840            | 96,000   | SMT8                       | OLTP              |
| umh-20x4800         | 20            | 160              | 4,800            | 120,000  | SMT8                       | OLTP              |
| umh-22x5280         | 22            | 176              | 5,280            | 132,000  | SMT8                       | OLTP              |
| umh-25x6000         | 25            | 200              | 6,000            | 150,000  | SMT8                       | OLTP              |
| umh-30x7200         | 30            | 240              | 7,200            | 180,000  | SMT8                       | OLTP              |
| umh-35x8400         | 35            | 280              | 8,400            | 210,000  | SMT8                       | OLTP              |
| umh-40x9600         | 40            | 320              | 9,600            | 240,000  | SMT8                       | OLTP              |
| umh-50x12000        | 50            | 400              | 12,000           | 300,000  | SMT8                       | OLTP              |
| umh-60x14400        | 60            | 480              | 14,400           | 360,000  | SMT8                       | OLTP              |
{: class="simple-tab-table"}
{: tab-group="umh-table"}
{: caption="P9 Certified Instance profiles with umh prefix" caption-side="bottom"}
{: #umh-profiles-instance}
{: tab-title="Instance Profile"}

| umh \nCertified profile | IOPs\nrequired| Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained|
|-------------------------|---------------|-------------------------|----------------------|---------------|
| umh-4x960               | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| umh-6x1440              | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| umh-8x1920              | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| umh-10x2400             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| umh-12x2880             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| umh-16x3840             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| umh-20x4800             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| umh-22x5280             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| umh-25x6000             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| umh-30x7200             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| umh-35x8400             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| umh-40x9600             | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| umh-50x12000            | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
| umh-60x14400            | 12,000        | 4 x 128 GB              | Tier 0               | 12,800        |
{: class="simple-tab-table"}
{: tab-group="umh-table"}
{: caption="Sample log file system configurations for SAP HANA profiles with umh prefix" caption-side="bottom"}
{: #umh-profiles-log}
{: tab-title="Log File System"}

| umh \nCertified profile | IOPs\nrequired| Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained|
|-------------------------|---------------|-------------------------|----------------------|---------------|
| umh-4x960               | 8,000         | 4 x 670 GB              | Tier 3               | 8,040         |
| umh-6x1440              | 8,000         | 4 x 670 GB              | Tier 3               | 8,040         |
| umh-8x1920              | 8,000         | 4 x 773 GB              | Tier 3               | 9,279         |
| umh-10x2400             | 8,000         | 4 x 966 GB              | Tier 3               | 11,598        |
| umh-12x2880             | 8,000         | 4 x 1159 GB             | Tier 3               | 13,917        |
| umh-16x3840             | 8,000         | 4 x 1546 GB             | Tier 3               | 18,558        |
| umh-20x4800             | 8,000         | 4 x 1933 GB             | Tier 3               | 23,196        |
| umh-22x5280             | 8,000         | 4 x 2126 GB             | Tier 3               | 25,518        |
| umh-25x6000             | 8,000         | 4 x 2416 GB             | Tier 3               | 28,998        |
| umh-30x7200             | 8,000         | 4 x 2899 GB             | Tier 3               | 34,797        |
| umh-35x8400             | 8,000         | 4 x 3383 GB             | Tier 3               | 40,596        |
| umh-40x9600             | 8,000         | 4 x 3866 GB             | Tier 3               | 46,395        |
| umh-50x12000            | 8,000         | 4 x 4833 GB             | Tier 3               | 57,996        |
| umh-60x14400            | 8,000         | 4 x 5799 GB             | Tier 3               | 69,594        |
{: class="simple-tab-table"}
{: tab-group="umh-table"}
{: caption="Sample data file system configurations for SAP HANA profiles with umh prefix" caption-side="bottom"}
{: #umh-profiles-data}
{: tab-title="Data File System"}

| umh \nCertified profile | IOPs\nrequired| Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained| Alternative configuration    |
|-------------------------|---------------|-------------------------|----------------------|---------------|----------------------------- |
| umh-4x960               | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| umh-6x1440              | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| umh-8x1920              | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| umh-10x2400             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| umh-12x2880             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| umh-16x3840             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| umh-20x4800             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| umh-22x5280             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| umh-25x6000             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| umh-30x7200             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| umh-35x8400             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| umh-40x9600             | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| umh-50x12000            | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
| umh-60x14400            | 3,000         | 1 x 200 GB              | Tier 0               | 5,000         | 1 x 1000 GB \nusing 'Tier 3' |
{: class="simple-tab-table"}
{: tab-group="umh-table"}
{: caption="Sample shared file system configurations for SAP HANA profiles with umh prefix" caption-side="bottom"}
{: #umh-profiles-shared}
{: tab-title="Shared File System"}


### cnp - Custom profiles
{: #cnpprofiles}

{{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s provide fully customizable CPU cores and memory (RAM in GiB). Configure a virtual server instance by specifying a custom size for the IBM {{site.data.keyword.powerSys_notm}} profile, following SAP HANA for IBM Power Systems best practices and SAP’s guidance for non-production SAP HANA instances.

Custom profiles are designed exclusively for non-production development or testing purposes. These profiles are not intended, supported, or certified for SAP production environments and cannot be used to move from a non-production environment to a production environment.

Each profile requires a minimum of two dedicated cores. For storage performance requirements, refer to the [Storage Guidelines for SAP HANA](/docs/sap?topic=sap-storage-design-considerations#sample-power). For additional details on storage, refer to the [storage tiers section](/docs/power-iaas?topic=power-iaas-on-cloud-architecture#storage-tiers) section in the **What is a Power Systems Virtual Server?** documentation.


For more details, refer to [SAP Note 2947579 - SAP HANA on {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}s](https://launchpad.support.sap.com/#/notes/2947579){: external}.
{: note}
{: class="simple-tab-table"}
