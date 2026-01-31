---
copyright:
  years: 2020, 2026
lastupdated: "2026-01-31"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# SAP HANA certified instances on Intel-powered virtual servers on VPC Infrastructure
{: #hana-iaas-offerings-profiles-vpc-intel-vsi}

A SAP HANA certified server or profile on Intel-powered virtual servers on VPC Infrastructure defines attributes, such as physical CPU cores and RAM, which determine the size and performance capabilities of the server.

## Profile naming convention
{: #naming-convention}

With {{site.data.keyword.vsi_is_full}}, the profile families that are certified for SAP are: *Memory Optimized*, *Very High* and *Ultra High Memory Optimized*.

All the Memory family profiles cater to memory intensive workloads, such as demanding database applications and in-memory analytics workloads, and are especially designed for SAP HANA workloads.

For more information, see [x86-64 instance profiles](/docs/vpc?topic=vpc-profiles).

The first letter of the profile name indicates the profile family that is mentioned the profile list, where the ratio depends on the Intel generation, Sapphire Rapids (SPR) or Cascade Lake (CL):

| **First letter** | **Characteristics of the related profile family**                                     |
| ---------------- | ------------------------------------------------------------------------------------- |
| b                | *Balanced* family, higher vCPU to memory ratio SPR 1:5                                |
| m                | *Memory Optimized* family, higher vCPU to memory ratio SPR 1:10, CL 1:8               |
| v                | *Very High Memory Optimized* family, very high vCPU to memory ratio SPR 1:16, CL 1:14 |
| u                | *Ultra High Memory Optimized* family, ultra high vCPU to memory ratio CL 1:28         |
{: caption="{{site.data.keyword.vsi_is_full}} Profile Families" caption-side="top"}

The virtual server profile names are contextual and sequential. The following table illustrates an example of an SAP HANA certified server profile:

| Profile name | Naming convention component | Description                                                                                                         |
| ------------ | --------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| mx2-16x128   | m                           | *Memory Optimized* family                                                                                           |
|              | x                           | Intel x86_64 CPU Architecture                                                                                       |
|              | ? \n   2 \n   3             | The Intel generation for the underlying hardware \n   Cascade Lake \n   Sapphire Rapids                             |
|              | d                           | the optional 'd' in the name indicates that the server is equipped with one or more internal SSD storage devices (*)|
|              | —                           | *separator*                                                                                                         |
|              | 16                          | 16 vCPU                                                                                                             |
|              | x                           | *separator*                                                                                                         |
|              | 128                         | 128 GiB RAM                                                                                                         |
{: caption="Profile naming scheme for SAP HANA" caption-side="top"}

Refer to [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external} for up-to-date information on certified SAP HANA profiles.
{: note}

(\*) For virtual server instances (VSI) using instance storage on SSD, you must not place any SAP workload related data on such instance storage, because data loss may occur in certain situations. For more information, see [About instance storage](/docs/vpc?topic=vpc-instance-storage).
{: note}

## Intel Sapphire Rapids certified servers for SAP HANA
{: #sr-profiles}

The following table lists available profile families for Intel Sapphire Rapids certified servers for SAP HANA:

| **Profile name** | **CPU cores** | **Memory (GiB)** |
| -----------------| ------------- | ---------------- |
| bx3d-176x880     | 176           | 880              |
| mx3d-176x1760    | 176           | 1,760            |
| vx3d-88x1408-spl | 88            | 1,408            |
| vx3d-176x2816    | 176           | 2,816            |
{: class="simple-tab-table"}
{: tab-group="sr-profiles"}
{: caption="Intel Sapphire Rapids profiles for SAP HANA" caption-side="bottom"}
{: #hana-iaas-intel-sr-profiles}
{: tab-title="Certified Profiles"}

| **Profile name** | **Workload type** | **SAPS** |
| -----------------| ----------------- | -------- |
| bx3d-176x880     | OLAP/OLTP         | 230,170  |
| mx3d-176x1760    | OLAP/OLTP         | 227,880  |
| vx3d-88x1408-spl | OLAP/OLTP         | 110,560  |
| vx3d-176x2816    | OLAP/OLTP         | 221,120  |
{: class="simple-tab-table"}
{: tab-group="sr-profiles"}
{: caption="Intel Sapphire Rapids profiles for SAP HANA" caption-side="bottom"}
{: #hana-iaas-intel-sr-sizing}
{: tab-title="Sizing"}


| **Certified profile**      | **IOPS\nrequired** | **Log file system\nstorage config (GB)** | **Log file system\nstorage tier** | **Log file system\nsize(GB)** | **IOPs\nobtained** |
|--------------------------- | ------------------ | ---------------------------------------- | --------------------------------- | ----------------------------- | -------------------|
| bx3d-176x880               |    10 IOPS/GB      |    2 x 256 GB                            |    10iops-tier                    |    512 GB                     |    5,120           |
| mx3d-176x1760              |    10 IOPS/GB      |    2 x 256 GB                            |    10iops-tier                    |    512 GB                     |    5,120           |
| vx3d-88x1408-spl           |    10 IOPS/GB      |    2 x 256 GB                            |    10iops-tier                    |    512 GB                     |    5,120           |
| vx3d-176x2816              |    10 IOPS/GB      |    2 x 256 GB                            |    10iops-tier                    |    512 GB                     |    5,120           |
{: class="simple-tab-table"}
{: tab-group="sr-profiles"}
{: caption="Intel Sapphire Rapids profiles for SAP HANA" caption-side="bottom"}
{: #hana-iaas-intel-sr-log}
{: tab-title="Log File System"}


| **Certified profile**      | **IOPS\nrequired** | **Data file system\nstorage config (GB)** | **Data file system\nstorage tier** | **Data file system\nsize(GB)** | **IOPs\nobtained** |
|--------------------------- | ------------------ | ----------------------------------------- | ---------------------------------- |------------------------------- | ------------------ |
| bx3d-176x880               |    10 IOPS/GB      |    2 x 544 GB                             |    10iops-tier                     |    1,088 GB                    |    10,880          |
| mx3d-176x1760              |    10 IOPS/GB      |    2 x 1,056 GB                           |    10iops-tier                     |    2,112 GB                    |    21,120          |
| vx3d-88x1408-spl           |    10 IOPS/GB      |    2 x 848 GB                             |    10iops-tier                     |    1,696 GB                    |    16,960          |
| vx3d-176x2816              |    10 IOPS/GB      |    2 x 1,696 GB                           |    10iops-tier                     |    3,392 GB                    |    33,920          |
{: class="simple-tab-table"}
{: tab-group="sr-profiles"}
{: caption="Intel Sapphire Rapids profiles for SAP HANA" caption-side="bottom"}
{: #hana-iaas-intel-sr-data}
{: tab-title="Data File System"}


| **Certified profile**      | **IOPS\nrequired** | **Shared file system\nstorage config (GB)** | **Shared file system\nstorage tier** | **Shared file system\nsize(GB)** | **IOPs\nobtained** |
|--------------------------- | ------------------ | ------------------------------------------- | ------------------------------------ | -------------------------------- | ------------------ |
| bx3d-176x880               |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
| mx3d-176x1760              |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
| vx3d-88x1408-spl           |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
| vx3d-176x2816              |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
{: class="simple-tab-table"}
{: tab-group="sr-profiles"}
{: caption="Intel Sapphire Rapids profiles for SAP HANA" caption-side="bottom"}
{: #hana-iaas-intel-sr-shared}
{: tab-title="Shared File System"}


## Intel Cascade Lake certified servers for SAP HANA
{: #intel-vpc-vsi-sap-hana-profile-cl-family}

The following table lists available profile families for Intel Cascade Lake certified servers for SAP HANA:

### mx2 – Certified profiles
{: #mx2-profiles}

The following SAP HANA profiles with prefix mx2 on Intel Cascade Lake Servers are supported:

| **Profile name**                         | **CPU cores** | **Memory (GiB)** |
|------------------------------------------|---------------|------------------|
| mx2-8x64\nmx2d-8x64                      | 8             | 64               |
| mx2-16x128\nmx2d-16x128                  | 16            | 128              |
| mx2-32x256\nmx2d-32x256                  | 32            | 256              |
| mx2-48x384\nmx2d-48x384                  | 48            | 384              |
{: class="simple-tab-table"}
{: tab-group="mx2-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA-(mx2)" caption-side="bottom"}
{: #hana-iaas-intel-mx2-profiles}
{: tab-title="Certified Profiles"}

| **Profile name**           | **Workload type** | **SAPS** |
| ---------------------------| ----------------- | -------- |
| mx2-8x64 \n mx2d-8x64 | SAP Business One | 10,283 |
| mx2-16x128 \n mx2d-16x128 | OLTP \nSAP Business One | 20,565 |
| mx2-32x256 \n mx2d-32x256 | OLTP \nSAP Business One | 41,130 |
| mx2-48x384 \n mx2d-48x384 | OLTP \nSAP Business One | 56,970 |
{: class="simple-tab-table"}
{: tab-group="mx2-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA-(mx2)" caption-side="bottom"}
{: #hana-iaas-intel-mx2-sizing}
{: tab-title="Sizing"}


| **Certified profile**      | **IOPS\nrequired** | **Log file system\nstorage config (GB)** | **Log file system\nstorage tier** | **Log file system\nsize(GB)** | **IOPs\nobtained** |
|--------------------------- | ------------------ | ---------------------------------------- | --------------------------------- | ----------------------------- | ------------------ |
| mx2-8x64\nmx2d-8x64        |    10 IOPS/GB      |    1 x  64 GB                            |    10iops-tier                    |     64 GB                     |      640           |
| mx2-16x128\nmx2d-16x128    |    10 IOPS/GB      |    1 x  64 GB                            |    10iops-tier                    |     64 GB                     |      640           |
| mx2-32x256\nmx2d-32x256    |    10 IOPS/GB      |    1 x 128 GB                            |    10iops-tier                    |    128 GB                     |    1,280           |
| mx2-48x384\nmx2d-48x384    |    10 IOPS/GB      |    4 x  64 GB                            |    10iops-tier                    |    256 GB                     |    2,560           |
{: class="simple-tab-table"}
{: tab-group="mx2-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA-(mx2)" caption-side="bottom"}
{: #hana-iaas-intel-mx2-log}
{: tab-title="Log File System"}


| **Certified profile**      | **IOPS\nrequired** | **Data file system\nstorage config (GB)** | **Data file system\nstorage tier** | **Data file system\nsize(GB)** | **IOPs\nobtained** |
|--------------------------- | ------------------ | ----------------------------------------- | ---------------------------------- |------------------------------- | ------------------ |
| mx2-8x64\nmx2d-8x64        |    10 IOPS/GB      |    2 x  48 GB                             |    10iops-tier                     |    96 GB                       |      960           |
| mx2-16x128\nmx2d-16x128    |    10 IOPS/GB      |    2 x  96 GB                             |    10iops-tier                     |   192 GB                       |    1,920           |
| mx2-32x256\nmx2d-32x256    |    10 IOPS/GB      |    4 x  96 GB                             |    10iops-tier                     |   384 GB                       |    3,840           |
| mx2-48x384\nmx2d-48x384    |    10 IOPS/GB      |    4 x 144 GB                             |    10iops-tier                     |   576 GB                       |    5,760           |
{: class="simple-tab-table"}
{: tab-group="mx2-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA-(mx2)" caption-side="bottom"}
{: #hana-iaas-intel-mx2-data}
{: tab-title="Data File System"}


| **Certified profile**      | **IOPS\nrequired** | **Shared file system\nstorage config (GB)** | **Shared file system\nstorage tier** | **Shared file system\nsize(GB)** | **IOPs\nobtained** |
|--------------------------- | ------------------ | ------------------------------------------- | ------------------------------------ | -------------------------------- | ------------------ |
| mx2-8x64\nmx2d-8x64        |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
| mx2-16x128\nmx2d-16x128    |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
| mx2-32x256\nmx2d-32x256    |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
| mx2-48x384\nmx2d-48x384    |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
{: class="simple-tab-table"}
{: tab-group="mx2-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA-(mx2)" caption-side="bottom"}
{: #hana-iaas-intel-mx2-shared}
{: tab-title="Shared File System"}

### vx2 – Certified profiles
{: #vx2-profiles}

The following SAP HANA profiles with prefix vx2 on Intel Cascade Lake servers are supported:

| **Profile name**                 | **CPU cores** | **Memory (GiB)** |
|----------------------------------|---------------|------------------|
| vx2d-16x224                      | 16            | 224              |
| vx2d-44x616                      | 44            | 616              |
| vx2d-88x1232                     | 88            | 1,232            |
| vx2d-144x2016                    | 144           | 2,016            |
| vx2d-176x2464                    | 176           | 2,464            |
{: class="simple-tab-table"}
{: tab-group="vx2-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA-(vx2)" caption-side="bottom"}
{: #hana-iaas-intel-vx2-profiles}
{: tab-title="Certified Profiles"}

| **Profile name**  | **Workload type** | **SAPS**  |
|-------------------|-------------------|-----------|
| vx2d-16x224       | OLTP              | 17,046    |
| vx2d-44x616       | OLAP/OLTP         | 46,875    |
| vx2d-88x1232      | OLAP/OLTP         | 93,750    |
| vx2d-144x2016     | OLAP/OLTP         | 153,410   |
| vx2d-176x2464     | OLAP/OLTP         | 187,500   |
{: class="simple-tab-table"}
{: tab-group="vx2-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA-(vx2)" caption-side="bottom"}
{: #hana-iaas-intel-vx2-sizing}
{: tab-title="Sizing"}


| **Certified profile** | **IOPS\nrequired** | **Log file system\nstorage config (GB)** | **Log file system\nstorage tier** | **Log file system\nsize(GB)** | **IOPs\nobtained** |
|---------------------- | ------------------ | ---------------------------------------- | --------------------------------- | ----------------------------- | ------------------ |
| vx2d-16x224           |    10 IOPS/GB      |    2 x  64 GB                            |    10iops-tier                    |    128 GB                     |    1,280           |
| vx2d-44x616           |    10 IOPS/GB      |    2 x 160 GB                            |    10iops-tier                    |    320 GB                     |    3,200           |
| vx2d-88x1232          |    10 IOPS/GB      |    4 x 128 GB                            |    10iops-tier                    |    512 GB                     |    5,120           |
| vx2d-144x2016         |    10 IOPS/GB      |    4 x 128 GB                            |    10iops-tier                    |    512 GB                     |    5,120           |
| vx2d-176x2464         |    10 IOPS/GB      |    4 x 128 GB                            |    10iops-tier                    |    512 GB                     |    5,120           |
{: class="simple-tab-table"}
{: tab-group="vx2-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA-(vx2)" caption-side="bottom"}
{: #hana-iaas-intel-vx2-log}
{: tab-title="Log File System"}


| **Certified profile** | **IOPS\nrequired** | **Data file system\nstorage config (GB)** | **Data file system\nstorage tier** | **Data file system\nsize(GB)** | **IOPs\nobtained** |
|---------------------- | ------------------ | ----------------------------------------- | ---------------------------------- |------------------------------- | ------------------ |
| vx2d-16x224           |    10 IOPS/GB      |    2 x 160 GB                             |    10iops-tier                     |     320 GB                       |    3,200         |
| vx2d-44x616           |    10 IOPS/GB      |    4 x 224 GB                             |    10iops-tier                     |     896 GB                       |    8,960         |
| vx2d-88x1232          |    10 IOPS/GB      |    4 x 448 GB                             |    10iops-tier                     |   1,792 GB                       |   17,920         |
| vx2d-144x2016         |    10 IOPS/GB      |    4 x 768 GB                             |    10iops-tier                     |   3,072 GB                       |   30,720         |
| vx2d-176x2464         |    10 IOPS/GB      |    4 x 896 GB                             |    10iops-tier                     |   3,584 GB                       |   35,840         |
{: class="simple-tab-table"}
{: tab-group="vx2-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA-(vx2)" caption-side="bottom"}
{: #hana-iaas-intel-vx2-data}
{: tab-title="Data File System"}


| **Certified profile** | **IOPS\nrequired** | **Shared file system\nstorage config (GB)** | **Shared file system\nstorage tier** | **Shared file system\nsize(GB)** | **IOPs\nobtained** |
|---------------------- | ------------------ | ------------------------------------------- | ------------------------------------ | -------------------------------- | ------------------ |
| vx2d-16x224           |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
| vx2d-44x616           |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
| vx2d-88x1232          |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
| vx2d-144x2016         |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
| vx2d-176x2464         |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
{: class="simple-tab-table"}
{: tab-group="vx2-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA-(vx2)" caption-side="bottom"}
{: #hana-iaas-intel-vx2-shared}
{: tab-title="Shared File System"}

### ux2 – Certified profiles
{: #ux2-profiles}

The following SAP HANA profiles with prefix ux2 on Intel Cascade Lake servers are supported:

| **Profile name**  | **CPU cores** | **Memory (GiB)** |
|-------------------|---------------|-----------------|
| ux2d-8x224        | 8             | 224             |
| ux2d-16x448       | 16            | 448             |
| ux2d-36x1008      | 36            | 1,008           |
| ux2d-48x1344      | 48            | 1,344           |
| ux2d-72x2016      | 72            | 2,016           |
| ux2d-100x2800     | 100           | 2,800           |
| ux2d-200x5600     | 200           | 5,600           |
{: class="simple-tab-table"}
{: tab-group="ux2-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA-(ux2)" caption-side="bottom"}
{: #hana-iaas-intel-ux2-profiles}
{: tab-title="Certified Profiles"}

| **Profile name**   | **Workload type** | **SAPS**  |
|--------------------|-------------------|-----------|
| ux2d-8x224         | OLTP              | 8,623     |
| ux2d-16x448        | OLTP              | 17,246    |
| ux2d-36x1008       | OLTP              | 38,803    |
| ux2d-48x1344       | OLTP              | 51,737    |
| ux2d-72x2016       | OLTP              | 77,606    |
| ux2d-100x2800      | OLTP              | 107,785   |
| ux2d-200x5600      | OLTP              | 215,570   |
{: class="simple-tab-table"}
{: tab-group="ux2-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA-(ux2)" caption-side="bottom"}
{: #hana-iaas-intel-ux2-sizing}
{: tab-title="Sizing"}

| **Certified profile** | **IOPS\nrequired** | **Log file system\nstorage config (GB)** | **Log file system\nstorage tier** | **Log file system\nsize(GB)** | **IOPs\nobtained** |
|---------------------- | ------------------ | ---------------------------------------- | --------------------------------- | ----------------------------- | ------------------ |
| ux2d-8x224            |    10 IOPS/GB      |    2 x  64 GB                            |    10iops-tier                    |    128 GB                     |    1,280           |
| ux2d-16x448           |    10 IOPS/GB      |    2 x 128 GB                            |    10iops-tier                    |    256 GB                     |    2,560           |
| ux2d-36x1008          |    10 IOPS/GB      |    4 x 128 GB                            |    10iops-tier                    |    512 GB                     |    5,120           |
| ux2d-48x1344          |    10 IOPS/GB      |    4 x 128 GB                            |    10iops-tier                    |    512 GB                     |    5,120           |
| ux2d-72x2016          |    10 IOPS/GB      |    4 x 128 GB                            |    10iops-tier                    |    512 GB                     |    5,120           |
| ux2d-100x2800         |    10 IOPS/GB      |    4 x 128 GB                            |    10iops-tier                    |    512 GB                     |    5,120           |
| ux2d-200x5600         |    10 IOPS/GB      |    4 x 128 GB                            |    10iops-tier                    |    512 GB                     |    5,120           |
{: class="simple-tab-table"}
{: tab-group="ux2-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA-(ux2)" caption-side="bottom"}
{: #hana-iaas-intel-ux2-log}
{: tab-title="Log File System"}


| **Certified profile** | **IOPS\nrequired** | **Data file system\nstorage config (GB)** | **Data file system\nstorage tier** | **Data file system\nsize(GB)** | **IOPs\nobtained** |
|---------------------- | ------------------ | ----------------------------------------- | ---------------------------------- |------------------------------- | ------------------ |
| ux2d-8x224            |    10 IOPS/GB      |    2 x   160 GB                             |    10iops-tier                   |     320 GB                     |    3,200           |
| ux2d-16x448           |    10 IOPS/GB      |    4 x   160 GB                             |    10iops-tier                   |     640 GB                     |    6,400           |
| ux2d-36x1008          |    10 IOPS/GB      |    4 x   364 GB                             |    10iops-tier                   |   1,536 GB                     |   15,360           |
| ux2d-48x1344          |    10 IOPS/GB      |    4 x   504 GB                             |    10iops-tier                   |   2,016 GB                     |   20,160           |
| ux2d-72x2016          |    10 IOPS/GB      |    4 x   768 GB                             |    10iops-tier                   |   3,072 GB                     |   30,720           |
| ux2d-100x2800         |    10 IOPS/GB      |    4 x 1,024 GB                             |    10iops-tier                   |   4,096 GB                     |   40,960           |
| ux2d-200x5600         |    10 IOPS/GB      |    4 x 2,048 GB                             |    10iops-tier                   |   8,192 GB                     |   81,920           |
{: class="simple-tab-table"}
{: tab-group="ux2-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA-(ux2)" caption-side="bottom"}
{: #hana-iaas-intel-ux2-data}
{: tab-title="Data File System"}


| **Certified profile** | **IOPS\nrequired** | **Shared file system\nstorage config (GB)** | **Shared file system\nstorage tier** | **Shared file system\nsize(GB)** | **IOPs\nobtained** |
|---------------------- | ------------------ | ------------------------------------------- | ------------------------------------ | -------------------------------- | ------------------ |
| ux2d-8x224            |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
| ux2d-16x448           |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
| ux2d-36x1008          |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
| ux2d-48x1344          |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
| ux2d-72x2016          |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
| ux2d-100x2800         |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
| ux2d-200x5600         |    5 IOPS/GB       |    1 x 256 GB                               |    5iops-tier                        |    256 GB                        |    1,280           |
{: class="simple-tab-table"}
{: tab-group="ux2-profiles"}
{: caption="Intel Cascade Lake profiles for SAP HANA-(ux2)" caption-side="bottom"}
{: #hana-iaas-intel-ux2-shared}
{: tab-title="Shared File System"}
