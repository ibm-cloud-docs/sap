---
copyright:
  years: 2020, 2026
lastupdated: "2026-04-29"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, baremetal, Netweaver, profiles, amd, vmware, sapphire rapids, Skylake, Cascade lake
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# {{site.data.keyword.sap_app_server}} certified instances on Intel-powered bare metal servers in Classic Infrastructure
{: #nw-iaas-offerings-profiles-classic-bm}

## Profile naming convention
{: #naming-convention}

{{site.data.keyword.sap_app_server}} profile names follow a contextual and sequential naming convention. The following table illustrates an example of an SAP application certified profile:


| Profile name | Naming convention component | Description                                                                                          |
|--------------|-----------------------------|------------------------------------------------------------------------------------------------------ |
| BI.S3.NW384  | BI                          | IBM Cloud Infrastructure                                                                              |
|              | S3                          | Series 3 (CPU generation)\n• S3: Intel Skylake\n• S4: Intel Cascade Lake\n• S5: Intel Sapphire Rapids |
|              | NW                          | {{site.data.keyword.sap_app_server}} (NetWeaver) certified server                                                   |
|              | H2                          | HANA-(*) and {{site.data.keyword.sap_app_server}} certified server, 2 sockets                                       |
|              | 384                         | 384 GB RAM                                                                                            |
{: caption="Profile naming scheme for {{site.data.keyword.sap_app_server}}" caption-side="top"}

For more information, see [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}.


## Profiles hosted on Intel Sapphire Rapids CPU
{: #nw-sr-profiles}

| **Profile**  | **CPU Cores** | **Virtual CPUs**  | **Memory (GiB)**  | **SAPS**  | **aSAPS** |
|------------- |---------------|-------------------|-------------------|-----------|-----------|
| BI.S5.NW256  | 36            | 72                | 256               | 132,630   |  25,300   |
| BI.S5.NW512  | 36            | 72                | 512               | 128,750   |  24,600   |
| BI.S5.NW1024 | 36            | 72                | 1,024             | 125,920   |  24,600   |
{: caption="Intel Sapphire Rapids servers certified for {{site.data.keyword.sap_app_server}}" caption-side="top"}



## Profiles hosted on Intel Cascade Lake CPU
{: #nw-cl-profiles}

| **Profile**     | **CPU Cores** | **Virtual CPUs**  | **Memory (GiB)**  | **SAPS**  |
|---------------- |---------------|-------------------|-------------------|-----------|
| BI.S4.NW192     | 32            | 64                | 192               | 82,470    |
| BI.S4.NW384     | 32            | 64                | 384               | 85,130    |
| BI.S4.NW384_v3  | 16            | 32                | 384               | 60,420    |
| BI.S4.NW768     | 40            | 80                | 768               | 112,830   |
| BI.S4.NW768_v2  | 48            | 96                | 768               | 124,620   |
| BI.S4.NW768_v3  | 16            | 32                | 768               | 60,420    |
| BI.S4.NW1500    | 56            | 112               | 1,536             | 147,220   |
| BI.S4.NW3000    | 56            | 112               | 3,072             | 135,127   |
{: caption="Intel Cascade Lake servers certified for {{site.data.keyword.sap_app_server}}" caption-side="top"}



## Profiles hosted on Intel Skylake CPU
{: #nw-sl-profiles}

| **Profile**  | **CPU Cores** | **Virtual CPUs**  | **Memory (GiB)**  | **SAPS**  |
|--------------|---------------|-------------------|-------------------|-----------|
| BI.S3.NW64   | 4             | 8                 | 64                | 12,750    |
| BI.S3.NW192  | 36            | 72                | 192               | 78,850    |
| BI.S3.NW384  | 36            | 72                | 384               | 79,430    |
| BI.S3.NW768  | 36            | 72                | 768               | 79,630    |
{: caption="Intel Skylake servers certified for {{site.data.keyword.sap_app_server}}" caption-side="top"}


## Profiles hosted on AMD servers
{: #nw-iaas-amd-bm-list}

The following is an overview of the SAP-certified profiles with AMD Bare Metal:

| **Profile**   | **CPU Cores** | **Virtual CPUs**  | **Memory (GiB)**  | **SAPS**  |
|---------------|---------------|-------------------|-------------------|-----------|
| BI.S4A.NW2000 | 96            | 192               | 2048              | 265,650   |
| BI.S4A.NW4000 | 96            | 192               | 4096              | 267,450   |
{: caption="AMD servers certified for {{site.data.keyword.sap_app_server}}" caption-side="top"}


## Profiles available on hourly consumption billing
{: #nw-iaas-intel-bm-hourly}

The following {{site.data.keyword.baremetal_short}} are available on hourly consumption billing:
- BI.S3.NW32
- BI.S4.NW192
- BI.S4.NW384
- BI.S4.NW768
