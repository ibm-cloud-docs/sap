---

copyright:
  years: 2020, 2025
lastupdated: "2025-01-21"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads

subcollection: sap

---

{{site.data.keyword.attribute-definition-list}}

# SAP HANA certified instances on IBM Power Virtual Server
{: #sap-hana-iaas-offerings-profiles-power-vs}

Provision SAP HANA on IBM Power Virtual Servers by selecting one of the certified profiles. A profile defines instance attributes, such as physical CPU cores and RAM, which determine the size and performance capabilities of the virtual server instance.

SAP HANA profile names follow a contextual and sequential naming convention.
The following table illustrates an example of an SAP HANA certified instance profile:

| Profile name | Naming convention component | description |
| --- | --- | --- |
| sh2-33x1900 | sh2 | Profiles with the prefix *sh2* support HANA database sizes up to 1900 GiB. |
| | - | _spacer_ |
| | 33 | 33 physical CPU Cores |
| | x | _spacer_ |
| | 1900 | 1900 GiB RAM |
{: caption="Profile naming scheme for SAP HANA" caption-side="top"}

## Profile families for Power10 server generation
{: #sap-hana-iaas-offerings-profiles-power10-families}

SAP HANA profiles for Power10 servers operate in SMT4 or SMT8 mode. Simultaneous Multithreading (SMT) on IBM Power enables multiple independent threads to execute within a single physical CPU core.
1. In SMT4 mode, four parallel threads run per physical CPU core, resulting in four logical CPUs per core.
2. In SMT8 mode, eight parallel threads run per physical CPU core, resulting in eight logical CPUs per core.

The following table lists available profile families for IBM Power10 processor-based servers:

| Families | Description |
| -------- | ----------- |
| [Profiles with sr2 prefix](#sr2profiles) | Profiles with the prefix *sr2* are custom profiles that support selection of any combination of physical CPU cores and memory. Combinations that are certified by SAP for productive usage are documented in [SAP Note 2947579 - SAP HANA on {{site.data.keyword.IBM_notm}} Power Virtual Servers](https://launchpad.support.sap.com/#/notes/2947579){: external} and in [SAP HANA hardware directory](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=v:deCertified;v:60ed2297-5cdd-4387-89c2-b0d3651d1206&sort=Latest%20Certification&sortDesc=true&id=s:2837). Custom profiles may be deployed through CLI or API only. |
| [Profiles with sh2 prefix](#sh2profiles) | Profiles with the prefix *sh2* support HANA database sizes up to 1900 GiB. |
{: caption="Table 1. Profile families for Power10 server generation" caption-side="top"}

## Certified profiles with the prefix sr2
(: #sr2profiles)

Refer to [SAP Note 2947579 - SAP HANA on {{site.data.keyword.IBM_notm}} Power Virtual Servers](https://launchpad.support.sap.com/#/notes/2947579){: external} for up-to-date information on certified SAP HANA profiles.
{: note}

The following SAP HANA profiles with the prefix *sr2* are supported in SMT4 mode:

| **Profile name(s)** | **CPU Cores** | **Virtual CPUs** | **Memory (GiB)** | **SAPS** | **Workload Type** |
| -- | -- | -- | -- | -- | --|
sr2-12x950 | 12 | 48 | 950 | 72,000 | OLTP scale-up, OLAP scale-up |
sr2-12x1450 | 12 | 48 | 1,450 | 72,000 | OLTP scale-up, OLAP scale-up |
sr2-14x512 | 14 | 56 | 512 | 84,000 | OLTP scale-up |
sr2-14x740 | 14 | 56 | 740 | 84,000 | OLTP scale-up, OLAP scale-up |
sr2-22x2950 | 22 | 88 | 2,950 | 132,000 | OLTP scale-up, OLAP scale-up |
sr2-24x1024 | 24 | 96 | 1,024 | 144,000 | OLTP scale-up, OLAP scale-up |
sr2-24x1536 | 24 | 96 | 1,536 | 144,000 | OLTP scale-up, OLAP scale-up |
sr2-35x4450 | 35 | 140 | 4,450 | 210,000 | OLTP scale-up, OLAP scale-up |
sr2-40x3072 | 40 | 160 | 3,072 | 240,000 | OLTP scale-up, OLAP scale-up |
sr2-64x6144 | 64 | 256 | 6,144 | 384,000 | OLTP scale-up, OLAP scale-up |
sr2-80x9216 | 80 | 320 | 9,216 | 480,000 | OLTP scale-up, OLAP scale-up |
sr2-80x12288 | 80 | 320 | 12,288 | 480,000 | OLTP scale-up, OLAP scale-up |
sr2-80x14400 | 80 | 320 | 14,400 | 480,000 | OLTP scale-up, OLAP scale-up |
{: caption="Certified profiles with sh2 prefix in SMT4 mode" caption-side="top"}

The following SAP HANA profiles with the prefix *sr2* on IBM Cloud PowerVS are supported in SMT8 mode:

| **Profile name(s)** | **CPU Cores** | **Virtual CPUs** | **Memory (GiB)** | **SAPS** | **Workload Type** |
| -- | -- |  -- | -- | -- | --|
sr2-35x3000 | 35 | 280 | 3,000 | 266,000 | OLTP scale-up, OLAP scale-up |
sr2-35x3900 | 35 | 280 | 3,900 | 266,000 | OLTP scale-up, OLAP scale-up |
sr2-80x6144 | 80 | 640 | 6,144 | 608,000 | OLTP scale-up, OLAP scale-up |
sr2-87x6000 | 87 | 696 | 6,000 | 661,200 | OLTP scale-up, OLAP scale-up |
sr2-87x7000 | 87 | 696 | 7,000 | 661,200 | OLTP scale-up, OLAP scale-up |
sr2-87x7600 | 87 | 696 | 7,600 | 661,200 | OLTP scale-up, OLAP scale-up |
sr2-165x30500 | 165 | 1,320 | 30,500 | 1,254,000 | OLTP scale-up, OLTP scale-out (up to 3 nodes), OLAP scale-up |
{: caption="Certified profiles with sh2 prefix in SMT8 mode" caption-side="top"}

## Certified profiles with the prefix sh2
(: #sh2profiles)

Refer to [SAP Note 2947579 - SAP HANA on {{site.data.keyword.IBM_notm}} Power Virtual Servers](https://launchpad.support.sap.com/#/notes/2947579){: external} for up-to-date information on certified SAP HANA profiles.{: note}

The following SAP HANA profiles with the prefix *sh2* are supported in SMT4 mode:

| **Profile name(s)** | **CPU Cores** | **Virtual CPUs** | **Memory (GiB)** | **SAPS** | **Workload Type** |
| -- | -- | -- | -- | -- | --|
sh2-4x256 | 4 | 16 | 256 | 24,000 | OLTP scale-up |
sh2-4x384 | 4 | 16 | 384 | 24,000 | OLTP scale-up |
sh2-4x512 | 4 | 16 | 512 | 24,000 | OLTP scale-up |
sh2-4x768  | 4 | 16 | 768 | 24,000 | OLTP scale-up |
sh2-4x1000 | 4 | 16 | 1,000 | 24,000 | OLTP scale-up |
sh2-4x1500 | 4 | 16 | 1,500 | 24,000 | OLTP scale-up |
sh2-7x256 | 7 | 28 | 256 | 42,000 | OLTP scale-up |
sh2-7x384 | 7 | 28 | 384 | 42,000 | OLTP scale-up |
sh2-7x512 | 7 | 28 | 512 | 42,000 | OLTP scale-up |
sh2-7x768 | 7 | 28 | 768 | 42,000 | OLTP scale-up |
sh2-7x1000 | 7 | 28 | 1,000 | 42,000 | OLTP scale-up |
sh2-7x1500 | 7 | 28 | 1,500 | 42,000 | OLTP scale-up |
sh2-12x256 | 12 | 48 | 256 | 42,000 | OLTP scale-up |
sh2-12x384 | 12 | 48 | 384 | 42,000 | OLTP scale-up |
sh2-12x512 | 12 | 48 | 512 | 42,000 | OLTP scale-up |
sh2-12x768 | 12 | 48 | 768 | 42,000 | OLTP scale-up, OLAP scale-up |
sh2-12x1000 | 12 | 48 | 1,000 | 42,000 | OLTP scale-up, OLAP scale-up |
sh2-12x1500 | 12 | 48 | 1,500 | 42,000 | OLTP scale-up, OLAP scale-up |
sh2-16x1000 | 16 | 64 | 1,000 | 96,000 | OLTP scale-up, OLAP scale-up |
sh2-16x1500 | 16 | 64 | 1,500 | 96,000 | OLTP scale-up, OLAP scale-up |
sh2-25x1000 | 25 | 100 | 1,000 | 150,000 | OLTP scale-up, OLAP scale-up |
sh2-25x1500 | 25 | 100 | 1,500 | 150,000 | OLTP scale-up, OLAP scale-up |
{: caption="Certified profiles with sh2 prefix in SMT4 mode" caption-side="top"}

The following SAP HANA profiles with the prefix *sh2* on IBM Cloud PowerVS are supported in SMT8 mode:

| **Profile name(s)** | **CPU Cores** | **Virtual CPUs** | **Memory (GiB)** | **SAPS** | **Workload Type** |
| -- | -- |  -- | -- | -- | --|
sh2-8x1900 | 8 | 64 | 1900 | 60,800 | OLTP scale-up |
sh2-16x1900 | 16 | 128 | 1,900 | 121,600 | OLTP scale-up, OLAP scale-up |
sh2-25x1900 | 25 | 200 | 1,900 | 190,000 | OLTP scale-up, OLAP scale-up |
sh2-25x1900 | 33 | 200 | 1,900 | 190,000 | OLTP scale-up, OLAP scale-up |
{: caption="Certified profiles with sh2 prefix in SMT8 mode" caption-side="top"}

## Profile families for Power9 server generation
{: #sap-hana-iaas-offerings-profiles-power9-families}

SAP HANA profiles for the Power9 server generation support SMT8 mode. Simultaneous multithreading (SMT) in IBM Power allows multiple independent threads to run on a single physical CPU core. In SMT8 mode, each physical CPU core runs eight parallel threads, enabling the operating system to operate with eight logical CPUs per physical CPU core.

The following profile families are available for IBM Power9 processor-based servers.

| Families | Description |
| -------- | ----------- |
| [Profiles with cnp prefix](#cnpprofiles) | Profiles with the prefix *cnp* are custom profiles for test or development use only. These profiles are not intended for production use and are not supported or certified for SAP production. Each profile must have minimum of two physical CPU cores. |
| [Profiles with ush1 prefix](#ush1profiles) | Profiles with the prefix *ush* are small profiles and are best suited for balanced workloads that require less CPU and storage consumption. |
| [Profiles with bh1 prefix](#bh1profiles) | Profiles with the prefix *bh1* are balanced profiles and are best suited for midsize databases and common cloud applications with moderate traffic. |
| [Profiles with ch1 prefix](#ch1profiles)  | Profiles with the prefix *ch1* are compute intensive profiles and are best suited for moderate to high web traffic workloads. Compute profiles are best suited for cpu-intensive workloads, such as heavy web traffic, production batch processing, and front-end web servers. |
| [Profiles with mh1 prefix](#mh1profiles) | Profiles with the prefix *mh1* are very high memory profiles and are best suited for server OLAP databases, such as SAP NetWeaver. |
| [Profiles with umh prefix](#umhprofiles) | Profiles with the prefix *umh* are ultra memory profiles that provide the highest vCPU-to-memory ratios for serving in-memory OLTP databases, such as SAP HANA. |

## Certified profiles with the prefix cnp
{: #cnpprofiles}

{{site.data.keyword.IBM_notm}} IBM Power Virtual Servers provide fully customizable CPU cores and memory (RAM in GiB). Configure a virtual server instance by specifying a custom size for the IBM Power Virtual Server profile, following SAP HANA for IBM Power Systems best practices and SAP’s guidance for non-production SAP HANA instances.

Custom profiles are designed exclusively for non-production development or testing purposes. These profiles are not intended, supported, or certified for SAP production environments and cannot be used to move from a non-production environment to a production environment.

Each profile requires a minimum of two dedicated cores. For storage performance requirements, refer to the [Storage Guidelines for SAP HANA](/docs/sap?topic=sap-storage-design-considerations#sap-fiops-config). For additional details on storage, refer to the [storage tiers section](/docs/power-iaas?topic=power-iaas-about-virtual-server#storage-tiers) section in the **What is a Power Systems Virtual Server?** documentation.

## Certified profiles with the prefix ush1
{: #ush1profiles}

| **Profile name(s)** | **CPU Cores** | **Virtual CPUs** | **Memory (GiB)** | **SAPS** | **Workload Type** |
| -- | -- | -- | -- | -- | --|
| ush1-4x128 | 4 | 32 | 128 | 24,000 | OLAP/OLTP |
| ush1-4x256 | 4 | 32 | 256 | 24,000 | OLAP/OLTP |
| ush1-4x384 | 4 | 32 | 384 | 24,000 | OLAP/OLTP |
| ush1-4x512 | 4 | 32 | 512 | 24,000 | OLAP/OLTP |
| ush1-4x768 | 4 | 32 | 768 | 24,000 | OLAP/OLTP |
{: caption="Certified profiles with ush1 prefix" caption-side="top"}

## Certified profiles with the prefix bh1
{: #bh1profiles}

| **Profile name(s)** | **CPU Cores** | **Virtual CPUs** | **Memory (GiB)** | **SAPS** | **Workload Type** |
| -- | -- | -- | -- | -- | -- |
| bh1-16x1600 | 16 | 128 | 1,600 | 96,000 | OLAP/OLTP |
| bh1-20x2000 | 20 | 160 | 2,000 | 120,000 | OLAP * |
| bh1-22x2200 | 22 | 178 | 2,200 | 132,000 | OLAP * |
| bh1-25x2500 | 25 | 200 | 2,500 | 150,000 | OLAP * |
| bh1-30x3000 | 30 | 240 | 3,000 | 180,000 | OLAP * |
| bh1-35x3500 | 35 | 280 | 3,500 | 210,000 | OLAP * |
| bh1-40x4000 | 40 | 320 | 4,000 | 240,000 | OLAP * |
| bh1-50x5000 | 50 | 400 | 5,000 | 300,000 | OLAP * |
| bh1-60x6000 | 60 | 480 | 6,000 | 360,000 | OLAP/OLTP |
| bh1-70x7000 | 70 | 560 | 7,000 | 420,000 | OLAP * |
| bh1-80x8000 | 80 | 640 | 8,000 | 480,000 | OLAP * |
| bh1-100x10000 | 100 | 800 | 10,000 | 600,000 | OLAP * |
| bh1-120x12000 | 120 | 900 | 12,000 | 720,000 | OLAP * |
| bh1-140x14000 | 140 | 1,120 | 14,000 | 840,000 | OLAP |
{: caption="Certified profiles with bh1 prefix" caption-side="top"}


## Certified profiles with the prefix ch1
{: #ch1profiles}

| **Profile name(s)** | **CPU Cores** | **Virtual CPUs** | **Memory (GiB)** | **SAPS** | **Workload Type** |
| -- | -- | -- | -- | -- | -- |
| ch1-60x3000 | 60 | 480 | 3,000 | 360,000 | OLAP |
| ch1-70x3500 | 70 | 560 | 3,500 | 420,000 | OLAP * |
| ch1-80x4000 | 80 | 640 | 4,000 | 480,000 | OLAP * |
| ch1-100x5000 | 100 | 800 | 5,000 | 600,000 | OLAP * |
| ch1-120x6000 | 120 | 900 | 6,000 | 720,000 | OLAP * |
| ch1-140x7000 | 140 | 1,120 | 7,000 | 840,000 | OLAP |
{: caption="Certified profiles with ch1 prefix" caption-side="top"}

## Certified profiles with the prefix mh1
{: #mh1profiles}

| **Profile name(s)** | **CPU Cores** | **Virtual CPUs** | **Memory (GiB)** | **SAPS** | **Workload Type** |
| -- | -- | -- | -- | -- | -- |
| mh1-8x1440 | 8 | 64 | 1,440 | 48,000 | OLAP |
| mh1-10x1800 | 10 | 80 | 1,800 | 60,000 | OLAP |
| mh1-12x2160 | 12 | 96 | 2,160 | 72,000 | OLAP * |
| mh1-16x2880 | 16 | 128 | 2,880 | 96,000 | OLAP * |
| mh1-20x3600 | 20 | 160 | 3,600 | 120,000 | OLAP * |
| mh1-22x3960 | 22 | 176 | 3,960 | 132,000 | OLAP * |
| mh1-25x4500 | 25 | 200 | 4,500 | 150,000 | OLAP * |
| mh1-30x5400 | 30 | 240 | 5,400 | 180,000 | OLAP * |
| mh1-35x6300 | 35 | 280 | 6,300 | 210,000 | OLAP * |
| mh1-40x7200 | 40 | 320 | 7,200 | 240,000 | OLAP * |
| mh1-50x9000 | 50 | 400 | 9,000 | 300,000 | OLAP * |
| mh1-60x10800 | 60 | 460 | 10,800 | 360,000 | OLAP * |
| mh1-70x12600 | 70 | 560 | 12,600 | 420,000 | OLAP * |
| mh1-80x14400 | 80 | 640 | 14,400 | 480,000 | OLAP |
| mh1-90x16200 | 90 | 720| 16,200 | 540,000| OLAP |
| mh1-100x18000 | 100 | 800 | 18,000 | 600,000 | OLAP |
| mh1-125x22500 | 125 | 1,000  | 22,500 | 750,000 | OLAP |
{: caption="Certified profiles with mh1 prefix" caption-side="top"}

## Certified profiles with the prefix umh
{: #umhprofiles}

| **Profile name(s)** | **CPU Cores** | **Virtual CPUs** | **Memory (GiB)** | **SAPS** | **Workload Type** |
| -- | -- | -- | -- | -- | -- |
| umh-4x960 | 4 | 32 | 960 | 24,000 | OLTP |
| umh-6x1440 | 6 | 48 | 1,440 | 36,000 | OLTP * |
| umh-8x1920 | 8 | 64 | 1,920 | 48,000 | OLTP * |
| umh-10x2400 | 10 | 80 | 2,400 | 60,000 | OLTP * |
| umh-12x2880 | 12 | 96 | 2,880 | 72,000 | OLTP * |
| umh-16x3840 | 16 | 128 | 3,840 | 96,000 | OLTP |
| umh-20x4800 | 20 | 160 | 4,800 | 120,000 | OLTP * |
| umh-22x5280 | 22 | 176 | 5,280 | 132,000 | OLTP * |
| umh-25x6000 | 25 | 200 | 6,000 | 150,000 | OLTP * |
| umh-30x7200 | 30 | 240 | 7,200 | 180,000 | OLTP * |
| umh-35x8400 | 35 | 280 | 8,400 | 210,000 | OLTP * |
| umh-40x9600 | 40 | 320 | 9,600 | 240,000 | OLTP * |
| umh-50x12000 | 50 | 400 | 12,000 | 300,000 | OLTP * |
| umh-60x14400 | 60 | 480 | 14,400 | 360,000 | OLTP |
{: caption="Certified profiles with umh prefix" caption-side="top"}

Profile types marked with an asterisk `*` are not listed in the SAP HANA Hardware Directory by SAP but are certified for SAP HANA production systems. The directory includes only the smallest, median, and largest sizes within each profile family. SAP implemented this approach to reduce the number of entries, as IBM Power hardware supports highly granular sizing. For more details, refer to [SAP Note 2947579 - SAP HANA on {{site.data.keyword.IBM_notm}} Power Virtual Servers](https://launchpad.support.sap.com/#/notes/2947579){: external}.{: note}
