---

copyright:
  years: 2020, 2021, 2022
lastupdated: "2022-01-20"

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

# FAQ of Profile List with Benchmarks and Specifications
{: #faq-profile-specs}

## What are the certifications, highest benchmarks, and highest specifications available for different offerings?
{: #faq-profile-specs-maximums}

Below is a summary table of the certifications, highest benchmark scores, and maximum RAM configurations for each offering are shown in this table:

| IaaS Type | SAPS benchmark | OLTP scale-up memory (GB) | OLAP scale-up memory (GB) | OLAP scale-out memory (GB) |
| -- | -- | -- | -- | -- |
| IBM Cloud Intel Bare Metal \n with local disk storage | 550,670  \n _(world record of Intel-based IaaS)_ | 18,432 \n _(6,144 GB RAM plus 12,288 GB PMEM)_ | 18,432 \n _(6,144 GB RAM plus 12,288 GB PMEM)_ | 184,320 from 15 nodes (excl. 1 standby)  \n _(world record of all IaaS)_  |
| IBM Cloud Intel Virtual Server | 215,570 | 5,600 | 2,464 | — |
| IBM Power Virtual Server  \n _(complementary offering from {{site.data.keyword.IBM_notm}} Power Systems)_ | 840,000  \n _(world record of all IaaS)_ | 14,400 | 14,000 | — |
| IBM Cloud for VMware | 495,603 | 6,144 | 3,072 | 30,720 from 15 nodes (excl. 1 standby) |
{: caption="Table 1. Highest specifications of the SAP-certified IaaS" caption-side="top"}


## Do you have a full list of Profile specifications, including SAP sizing for different workloads?
{: #faq-profile-specs-table}

Each list of Infrastructure profiles for SAP HANA database server:
- [Intel Bare Metal server certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-bm)
- [Intel Virtual Server certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc)
- [IBM Power Virtual Server certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-power-vs) _This is a complementary offering from {{site.data.keyword.IBM_notm}} Power Systems_
- [VMware SDDC certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-vmware)

Each list of Infrastructure profiles for SAP NetWeaver Application Server:
- [Intel Bare Metal server certified profiles for SAP NetWeaver](/docs/sap?topic=sap-nw-iaas-offerings-profiles-intel-bm)
- [Intel Virtual Server certified profiles for SAP NetWeaver](/docs/sap?topic=sap-nw-iaas-offerings-profiles-intel-vs-vpc)
- [IBM Power Virtual Server certified profiles for SAP NetWeaver](/docs/sap?topic=sap-nw-iaas-offerings-profiles-power-vs) _This is a complementary offering from {{site.data.keyword.IBM_notm}} Power Systems_
- [VMware SDDC certified profiles for SAP NetWeaver](/docs/sap?topic=sap-nw-iaas-offerings-profiles-vmware)


All SAP-certified infrastructure for SAP HANA also includes certification for SAP NetWeaver and SAP AnyDB. However this is not vice-versa, any SAP-certified infratructure for SAP NetWeaver only includes SAP AnyDB.

**For a full list of all Infrastructure-as-a-Service Profile specifications in the {{site.data.keyword.ibm_cloud_sap}} portfolio _(including complementary offerings from {{site.data.keyword.IBM_notm}} Power Systems)_, see the table below:**

The following table can be copied into spreadsheet software. All profiles- without exception - are supported for SAP NetWeaver.
{: note}


| Profile | IaaS Type | CPU Cores | CPU Threads (aka. vCPU) | Memory (DRAM GB) | SD Two-Tier benchmark (SAPS) | CPU Architecture | DRAM per CPU Thread | SAP HANA Deployment Method | SAP HANA Processing Type |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | 
| BI.S3.H2.192 | IBM Cloud Intel Bare Metal \n with local disk storage | 36 | 72 | 192 | 78,850 | Intel Skylake SP | 2.67 | Appliance | OLAP/OLTP and SAP Business One for HANA |
| BI.S3.H2.384 | IBM Cloud Intel Bare Metal \n with local disk storage | 36 | 72 |  384 | 79,430 | Intel Skylake SP | 5.33 | Appliance | OLAP/OLTP and SAP Business One for HANA|
| BI.S3.H2.768 | IBM Cloud Intel Bare Metal \n with local disk storage | 36 | 72 | 768 | 79,630 | Intel Skylake SP | 10.67 | Appliance | OLAP/OLTP and SAP Business One for HANA|
| BI.S4.H2.192 | IBM Cloud Intel Bare Metal \n with local disk storage | 32 | 64 | 192 | 82,470 | Intel Cascade Lake SP | 3.00 | Appliance | OLAP/OLTP and SAP Business One for HANA|
| BI.S4.H2.384 | IBM Cloud Intel Bare Metal \n with local disk storage | 32 | 64 | 384 | 85,130 | Intel Cascade Lake SP | 6.00 | Appliance | OLAP/OLTP and SAP Business One for HANA|
| BI.S4.H2.768 | IBM Cloud Intel Bare Metal \n with local disk storage | 40 | 80 | 768 | 112,830 | Intel Cascade Lake SP | 9.60 | Appliance | OLAP/OLTP and SAP Business One for HANA|
| BI.S4.H2.1500 | IBM Cloud Intel Bare Metal \n with local disk storage | 56 | 112 | 1,536 | 147,220 | Intel Cascade Lake SP | 13.71 | Appliance | OLAP/OLTP|
| BI.S4.H2.3000 | IBM Cloud Intel Bare Metal \n with local disk storage | 56 | 112 | 3,072 | 135,127 | Intel Cascade Lake SP | 27.43 | Appliance | OLAP/OLTP|
| BI.S4.H4.3000 | IBM Cloud Intel Bare Metal \n with local disk storage | 112 | 224 | 3,072 | 285,970 | Intel Cascade Lake SP | 13.71 | Appliance | OLAP/OLTP|
| BI.S4.H4.6000 | IBM Cloud Intel Bare Metal with Local Disk Bare Metal \n with local disk storage | 112 | 224 | 6,144 | 285,970 | Intel Cascade Lake SP | 27.43 | Appliance | OLAP/OLTP|
| BI.S4.H8.6000 | IBM Cloud Intel Bare Metal \n with local disk storage | 224 | 448 | 6,144 | 550,670 | Intel Cascade Lake SP | 13.71 | Appliance | OLAP/OLTP|
| BI.S4.H8.12000 | IBM Cloud Intel Bare Metal \n with local disk storage | 224 | 448 | 12,288 | 550,670 | Intel Cascade Lake SP | 27.43 | Appliance | OLAP/OLTP|
| mx2-8x64 | IBM Cloud Intel Virtual Server | — | 8 | 64 | 10,283 | Intel Cascade Lake SP | 8.00 | TDI | SAP Business One for HANA |
| mx2-16x128 | IBM Cloud Intel Virtual Server | — | 16 | 128 | 20,565 | Intel Cascade Lake SP | 8.00 | TDI | OLTP and SAP Business One for HANA |
| mx2-32x256 | IBM Cloud Intel Virtual Server | — | 32 | 256 | 41,130 | Intel Cascade Lake SP | 8.00 | TDI | OLTP and SAP Business One for HANA |
| mx2-48x384 | IBM Cloud Intel Virtual Server | — | 48 | 384 | 56,970 | Intel Cascade Lake SP | 8.00 | TDI | OLTP and SAP Business One for HANA |
| vx2d-16x224 | IBM Cloud Intel Virtual Server | — | 16 | 224 | 17,046 | Intel Cascade Lake SP | 14.00 | TDI | OLTP |
| vx2d-44x616 | IBM Cloud Intel Virtual Server | — | 44 | 616 | 46,875 | Intel Cascade Lake SP | 14.00 | TDI | OLAP/OLTP |
| vx2d-88x1232 | IBM Cloud Intel Virtual Server | — | 88 | 1,232 | 93,750 | Intel Cascade Lake SP | 14.00 | TDI | OLAP/OLTP |
| vx2d-144x2016 | IBM Cloud Intel Virtual Server | — | 144 | 2,016 | 153,410 | Intel Cascade Lake SP | 14.00 | TDI | OLAP/OLTP |
| vx2d-176x2464 | IBM Cloud Intel Virtual Server | — | 176 | 2,464 | 187,500 | Intel Cascade Lake SP | 14.00 | TDI | OLAP/OLTP |
| ux2d-8x224 | IBM Cloud Intel Virtual Server | — | 8 | 224 | 8,623 | Intel Cascade Lake SP | 28.00 | TDI | OLTP |
| ux2d-16x448 | IBM Cloud Intel Virtual Server | — | 16 | 448 | 17,246 | Intel Cascade Lake SP | 28.00 | TDI | OLTP |
| ux2d-36x1008 | IBM Cloud Intel Virtual Server | — | 36 | 1,008 | 38,803 | Intel Cascade Lake SP | 28.00 | TDI | OLTP |
| ux2d-48x1344 | IBM Cloud Intel Virtual Server | — | 48 | 1,344 | 51,737 | Intel Cascade Lake SP | 28.00 | TDI | OLTP |
| ux2d-72x2016 | IBM Cloud Intel Virtual Server | — | 72 | 2,016 | 77,606 | Intel Cascade Lake SP | 28.00 | TDI | OLTP |
| ux2d-100x2800 | IBM Cloud Intel Virtual Server | — | 100 | 2,800 | 107,785 | Intel Cascade Lake SP | 28.00 | TDI | OLTP |
| ux2d-200x5600 | IBM Cloud Intel Virtual Server | — | 200 | 5,600 | 215,570 | Intel Cascade Lake SP | 28.00 | TDI | OLTP |
| bh1-16x1600 | IBM Power Virtual Server | 16 | 128 | 1,600 | 96,000 | IBM POWER9 | 12.50 | Appliance | OLAP |
| bh1-20x2000 | IBM Power Virtual Server | 20 | 160 | 2,000 | 120,000 | IBM POWER9 | 12.50 | Appliance | OLAP * |
| bh1-22x2200 | IBM Power Virtual Server | 22 | 176 | 2,200 | 132,000 | IBM POWER9 | 12.50 | Appliance | OLAP * |
| bh1-25x2500 | IBM Power Virtual Server | 25 | 200 | 2,500 | 150,000 | IBM POWER9 | 12.50 | Appliance | OLAP * |
| bh1-30x3000 | IBM Power Virtual Server | 30 | 240 | 3,000 | 180,000 | IBM POWER9 | 12.50 | Appliance | OLAP * |
| bh1-35x3500 | IBM Power Virtual Server | 35 | 280 | 3,500 | 210,000 | IBM POWER9 | 12.50 | Appliance | OLAP * |
| bh1-40x4000 | IBM Power Virtual Server | 40 | 320 | 4,000 | 240,000 | IBM POWER9 | 12.50 | Appliance | OLAP * |
| bh1-50x5000 | IBM Power Virtual Server | 50 | 400 | 5,000 | 300,000 | IBM POWER9 | 12.50 | Appliance | OLAP * |
| bh1-60x6000 | IBM Power Virtual Server | 60 | 480 | 6,000 | 360,000 | IBM POWER9 | 12.50 | Appliance | OLAP/OLTP |
| bh1-70x7000 | IBM Power Virtual Server | 70 | 560 | 7,000 | 420,000 | IBM POWER9 | 12.50 | Appliance | OLAP * |
| bh1-80x8000 | IBM Power Virtual Server | 80 | 640 | 8,000 | 480,000 | IBM POWER9 | 12.50 | Appliance | OLAP * |
| bh1-100x10000 | IBM Power Virtual Server | 100 | 800 | 10,000 | 600,000 | IBM POWER9 | 12.50 | Appliance | OLAP * |
| bh1-120x12000 | IBM Power Virtual Server | 120 | 960 | 12,000 | 720,000 | IBM POWER9 | 12.50 | Appliance | OLAP * |
| bh1-140x14000 | IBM Power Virtual Server | 140 | 1,120 | 14,000 | 840,000 | IBM POWER9 | 12.50 | Appliance | OLAP |
| ch1-60x3000 | IBM Power Virtual Server | 60 | 480 | 3,000 | 360,000 | IBM POWER9 | 6.25 | Appliance | OLAP |
| ch1-70x3500 | IBM Power Virtual Server | 70 | 560 | 3,500 | 420,000 | IBM POWER9 | 6.25 | Appliance | OLAP * |
| ch1-80x4000 | IBM Power Virtual Server | 80 | 640 | 4,000 | 480,000 | IBM POWER9 |  6.25 | Appliance | OLAP * |
| ch1-100x5000 | IBM Power Virtual Server | 100 | 800 | 5,000 | 600,000 | IBM POWER9 | 6.25 | Appliance | OLAP * |
| ch1-120x6000 | IBM Power Virtual Server | 120 | 960 | 6,000 | 720,000 | IBM POWER9 | 6.25 | Appliance | OLAP * |
| ch1-140x7000 | IBM Power Virtual Server | 140 | 1,120 | 7,000 | 840,000 | IBM POWER9 | 6.25 | Appliance | OLAP |
| umh1-4x960 | IBM Power Virtual Server | 4 | 32 | 960 | 24,000 | IBM POWER9 | 30.00 | Appliance | OLTP |
| umh1-6x1440 | IBM Power Virtual Server | 6 | 48 | 1,440 | 36,000 | IBM POWER9 | 30.00 | Appliance | OLTP * |
| umh1-8x1920 | IBM Power Virtual Server | 8 | 64 | 1,920 | 48,000 | IBM POWER9 | 30.00 | Appliance | OLTP * |
| umh1-10x2400 | IBM Power Virtual Server | 10 | 80 | 2,400 | 60,000 | IBM POWER9 | 30.00 | Appliance | OLTP * |
| umh1-12x2880 | IBM Power Virtual Server | 12 | 96 | 2,880 | 72,000 | IBM POWER9 | 30.00 | Appliance | OLTP * |
| umh1-16x3840 | IBM Power Virtual Server | 16 | 128 | 3,840 | 96,000 | IBM POWER9 | 30.00 | Appliance | OLTP |
| umh1-20x4800 | IBM Power Virtual Server | 20 | 160 | 4,800 | 120,000 | IBM POWER9 | 30.00 | Appliance | OLTP * |
| umh1-22x5280 | IBM Power Virtual Server | 22 | 176 | 5,280 | 132,000 | IBM POWER9 | 30.00 | Appliance | OLTP * |
| umh1-25x6000 | IBM Power Virtual Server | 25 | 200 | 6,000 | 150,000 | IBM POWER9 | 30.00 | Appliance | OLTP * |
| umh1-30x7200 | IBM Power Virtual Server | 30 | 240 | 7,200 | 180,000 | IBM POWER9 | 30.00 | Appliance | OLTP * |
| umh1-35x8400 | IBM Power Virtual Server | 35 | 280 | 8,400 | 210,000 | IBM POWER9 | 30.00 | Appliance | OLTP * |
| umh1-40x9600 | IBM Power Virtual Server | 40 | 320 | 9,600 | 240,000 | IBM POWER9 | 30.00 | Appliance | OLTP * |
| umh1-50x12000 | IBM Power Virtual Server | 50 | 400 | 12,000 | 300,000 | IBM POWER9 | 30.00 | Appliance | OLTP * |
| umh1-60x14400 | IBM Power Virtual Server | 60 | 480 | 14,400 | 360,000 | IBM POWER9 | 30.00 | Appliance | OLTP |
| BI.S3.H2.192 (VMware) | IBM Cloud for VMware | 36 | 72 | 192 | 70,965 | Intel Skylake SP | 2.67 | TDI | OLAP/OLTP and SAP Business One for HANA |
| BI.S3.H2.384 (VMware) | IBM Cloud for VMware | 36 | 72 | 384 | 71,487 | Intel Skylake SP | 5.33 | TDI | OLAP/OLTP and SAP Business One for HANA |
| BI.S3.H2.768 (VMware) | IBM Cloud for VMware | 36 | 72 | 768 | 71,667 | Intel Skylake SP | 10.67 | TDI | OLAP/OLTP and SAP Business One for HANA |
| BI.S4.H2.192 (VMware) | IBM Cloud for VMware | 32 | 64 | 192 | 74,223 | Intel Cascade Lake SP | 3.00 | TDI | OLAP/OLTP and SAP Business One for HANA |
| BI.S4.H2.384 (VMware) | IBM Cloud for VMware | 32 | 64 | 384 | 76,617 | Intel Cascade Lake SP | 6.00 | TDI | OLAP/OLTP and SAP Business One for HANA |
| BI.S4.H2.768 (VMware) | IBM Cloud for VMware | 40 | 80 | 768 | 101,547 | Intel Cascade Lake SP | 9.60 | TDI | OLAP/OLTP and SAP Business One for HANA |
| BI.S4.H2.1500 (VMware) | IBM Cloud for VMware | 56 | 112 | 1,536 | 132,498 | Intel Cascade Lake SP | 13.71 | TDI | OLAP/OLTP |
| BI.S4.H2.3000 (VMware) | IBM Cloud for VMware | 56 | 112 | 3,072 | 121,614 | Intel Cascade Lake SP | 27.43 | TDI | OLAP/OLTP |
| BI.S4.H4.3000 (VMware) | IBM Cloud for VMware | 112 | 224 | 3,072 | 257,373 | Intel Cascade Lake SP | 13.71 | TDI | OLAP/OLTP |
| BI.S4.H4.6000 (VMware) | IBM Cloud for VMware | 112 | 224 | 6,144 | 257,373 | Intel Cascade Lake SP | 27.43 | TDI | OLAP/OLTP |
| BI.S4.H8.6000 (VMware) | IBM Cloud for VMware | 224 | 448 | 6,144 | 495,603 | Intel Cascade Lake SP | 13.71 | TDI | OLAP/OLTP |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | 
|<td class="foot" colspan="11">Starting from here, these profiles are supported for NetWeaver only - NOT for HANA </td>|
| BI.S3.NW32 | IBM Cloud Intel Bare Metal \n with local disk storage | 4 | 8 | 32 | 11,970 | Intel Kaby Lake | 4.00 |   |   |
| BI.S3.NW64 | IBM Cloud Intel Bare Metal \n with local disk storage | 4 | 8 | 64 | 12,750 | Intel Kaby Lake | 8.00 |   |   |
| BI.S3.NW192 | IBM Cloud Intel Bare Metal \n with local disk storage | 36 | 72 | 192 | 78,850 | Intel Skylake SP | 2.67 |   |   |
| BI.S3.NW384 | IBM Cloud Intel Bare Metal \n with local disk storage | 36 | 72 | 384 | 79,430 | Intel Skylake SP | 5.33 |  |   |
| BI.S3.NW768 | IBM Cloud Intel Bare Metal \n with local disk storage | 36 | 72 | 768 | 79,630 | Intel Skylake SP | 10.67 |   |   |
| BI.S4.NW192 | IBM Cloud Intel Bare Metal \n with local disk storage | 32 | 64 | 192 | 82,470 | Intel Cascade Lake SP | 3.00 |   |   |
| BI.S4.NW384 | IBM Cloud Intel Bare Metal \n with local disk storage | 32 | 64 | 384 | 85,130 | Intel Cascade Lake SP | 6.00 |   |   |
| BI.S4.NW768 | IBM Cloud Intel Bare Metal \n with local disk storage | 40 | 80 | 768 | 112,830 | Intel Cascade Lake SP | 9.60 |   |   |
| bx2-2x8 | IBM Cloud Intel Virtual Server | — | 2 | 8 | 2,306 | Intel Cascade Lake SP | 4.00 |   |   |
| bx2-4x16 | IBM Cloud Intel Virtual Server | – | 4 | 16 | 4,613 | Intel Cascade Lake SP | 4.00 |   |   |
| bx2-8x32 | IBM Cloud Intel Virtual Server | – | 8 | 32 | 9,225 | Intel Cascade Lake SP | 4.00 |   |   |
| bx2-16x64 | IBM Cloud Intel Virtual Server | – | 16 | 64 | 18,450 | Intel Cascade Lake SP | 4.00 |   |   |
| bx2-32x128 | IBM Cloud Intel Virtual Server | – | 32 | 28 | 36,900 | Intel Cascade Lake SP | 4.00 |   |   |
| bx2-48x192 | IBM Cloud Intel Virtual Server | – | 48 | 192 | 55,350 | Intel Cascade Lake SP |4.00 |    |   |
| mx2-2x16 | IBM Cloud Intel Virtual Server | – | 2 | 16 | 2,571 | Intel Cascade Lake SP | 8.00 |   |   |
| mx2-4x32 | IBM Cloud Intel Virtual Server | – | 4 | 32 | 5,141 | Intel Cascade Lake SP | 8.00 |   |   |
| mx2-64x512 | IBM Cloud Intel Virtual Server | — | 64 | 512 | 81,015 | Intel Cascade Lake SP | 8.00 |   |   |
| mx2-96x768 | IBM Cloud Intel Virtual Server | — | 96 | 768 | 121,523 | Intel Cascade Lake SP | 8.00 |   |   |
| mx2-128x1024 | IBM Cloud Intel Virtual Server | — | 128 | 1024 | 162,030 | Intel Cascade Lake SP | 8.00 |   |   |
| vx2d-16x224 | IBM Cloud Intel Virtual Server | — | 16 | 224 | 17,046 | Intel Cascade Lake SP | 14.00 |   |   |
| vx2d-44x616 | IBM Cloud Intel Virtual Server | — | 44 | 616 | 46,875 | Intel Cascade Lake SP | 14.00 |   |   |
| vx2d-88x1232 | IBM Cloud Intel Virtual Server | — | 88 | 1,232 | 93,750 | Intel Cascade Lake SP | 14.00 |   |   |
| vx2d-144x2016 | IBM Cloud Intel Virtual Server | — | 144 | 2,016 | 153,410 | Intel Cascade Lake SP | 14.00 |   |   |
| vx2d-176x2464 | IBM Cloud Intel Virtual Server | — | 176 | 2,464 | 187,500 | Intel Cascade Lake SP | 14.00 |   |   |
| ux2d-8x224 | IBM Cloud Intel Virtual Server | — | 8 | 224 | 8,623 | Intel Cascade Lake SP | 28.00 |   |   |
| ux2d-16x448 | IBM Cloud Intel Virtual Server | — | 16 | 448 | 17,246 | Intel Cascade Lake SP | 28.00 |   |   |
| ux2d-36x1008 | IBM Cloud Intel Virtual Server | — | 36 | 1,008 | 38,803 | Intel Cascade Lake SP | 28.00 |   |   |
| ux2d-48x1344 | IBM Cloud Intel Virtual Server | — | 48 | 1,344 | 51,737 | Intel Cascade Lake SP | 28.00 |   |   |
| ux2d-72x2016 | IBM Cloud Intel Virtual Server | — | 72 | 2,016 | 77,606 | Intel Cascade Lake SP | 28.00 |   |   |
| ux2d-100x2800 | IBM Cloud Intel Virtual Server | — | 100 | 2,800 | 107,785 | Intel Cascade Lake SP | 28.00 |   |   |
| ux2d-200x5600 | IBM Cloud Intel Virtual Server | — | 200 | 5,600 | 215,570 | Intel Cascade Lake SP | 28.00 |   |   |
| BI.S3.NW192 (VMware) | IBM Cloud for VMware | 36 | 72 | 192 | 70,965 | Intel Skylake SP | 2.67 |   |   |
| BI.S3.NW384 (VMware) | IBM Cloud for VMware | 36 | 72 | 384 | 71,487 | Intel Skylake SP | 5.33 |  |   |
| BI.S3.NW768 (VMware) | IBM Cloud for VMware | 36 | 72 | 768 | 71,667 | Intel Skylake SP | 10.67 |   |   |
| BI.S4.NW192 (VMware) | IBM Cloud for VMware | 32 | 64 | 192 | 74,223 | Intel Cascade Lake SP | 3.00 |   |   |
| BI.S4.NW384 (VMware) | IBM Cloud for VMware | 32 | 64 | 384 | 76,617 | Intel Cascade Lake SP | 6.00 |  |   |
| BI.S4.NW768 (VMware) | IBM Cloud for VMware | 40 | 80 | 68 | 101,547 | Intel Cascade Lake SP | 9.60 |   |   |
| BI.S4.NW1500 (VMware) | IBM Cloud for VMware | 56 | 112 | 1,536 | 132,498 | Intel Cascade Lake SP | 13.71 |   |   |
| BI.S4.NW3000 (VMware) | IBM Cloud for VMware | 56 | 112 | 3,072 | 121,614 | Intel Cascade Lake SP | 27.43 |  |   |
{: caption="Table 2. Full list of SAP-certified IaaS" caption-side="top"}

<!--- The following two vx2d-profiles have not been entered in the SAP Note yet
| vx2d-2x28 | IBM Cloud Intel Virtual Server | — | 2 | 28 | 2,131 | Intel Cascade Lake SP | 14.00 |   |   |
| vx2d-4x56 | IBM Cloud Intel Virtual Server | — | 4 | 56 | 4,262 | Intel Cascade Lake SP | 14.00 |   |   |
--->
<!--- The following two ux2d profiles have not been entered in the SAP Note yet
| ux2d-2x56 | IBM Cloud Intel Virtual Server | — | 2 | 56 | 2,156 | Intel Cascade Lake SP | 28.00 |   |   |
| ux2d-4x112 | IBM Cloud Intel Virtual Server | — | 4 | 112 | 4,312 | Intel Cascade Lake SP | 28.00 |   |   |
--->

Those marked with * asterisk, are _not_ listed on the SAP HANA Hardware Directory by SAP but **are** certified for running SAP HANA production systems. The directory lists the smallest, median and largest within each profile family. This action has been taken by SAP to avoid too many records, as the scalability of IBM POWER hardware enables significantly more granular sizing. See [SAP Note 2947579 - SAP HANA on {{site.data.keyword.IBM_notm}} Power Virtual Servers](https://launchpad.support.sap.com/#/notes/2947579){: external} for additional detail.
{: note}


## Do you have a full list of Profile specifications for SAP HANA scale-out?
{: #faq-profile-specs-hana-scale-out-table}

**For a full list of all SAP HANA scale-out certified Infrastructure-as-a-Service Profile specifications in the {{site.data.keyword.ibm_cloud_sap}} portfolio _(including complementary offerings from {{site.data.keyword.IBM_notm}} Power Systems)_, see the table below:**

This can be copied into spreadsheet software
{: note}


| Profile | IaaS Type | SAP HANA Deployment Method | SAP HANA Processing Type | Scale-out Configuration | Total CPU Cores | Total CPU Threads (aka. vCPU) | Total Memory (DRAM GB) | SAP HANA Directory | SAP Benchmark Directory | SAP Certification Document
| --- | --- | --- | --- |--- | --- | --- | --- | --- | --- | --- |
| BI.S4.H2.1500 | IBM Cloud Intel Bare Metal | TDI | OLAP | Up to 16 nodes:  \n15 active nodes _(1 parent, 14 worker nodes)_ and 1 standby node | 840 | 1,680 | 23,040 | [OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2191){: external} | [Scale-Out Record](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/bwh?id=bm:d3ea63df-52e4-4745-9c82-89bd2b91937b){: external} | [Certification #2020048](https://www.sap.com/dmc/benchmark/2020/Cert20048.pdf){: external} |
| BI.S4.H2.3000 | IBM Cloud Intel Bare Metal | TDI | OLAP | Up to 16 nodes:  \n15 active nodes _(1 parent, 14 worker nodes)_ and 1 standby node | 840 | 1,680 | 46,080 | [OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2425){: external} | [Scale-Out Record](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/bwh?id=bm:d4391cab-ea23-41bd-a020-549945faec93){: external} | [Certification #2021015](https://www.sap.com/dmc/benchmark/2021/Cert21015.pdf){: external} |
| BI.S4.H4.3000 | IBM Cloud Intel Bare Metal | TDI | OLAP | Up to 16 nodes:  \n15 active nodes _(1 parent, 14 worker nodes)_ and 1 standby node | 1,680 | 3,360 | 46,080 | [OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2212){: external} | [Scale-Out Record](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/bwh?id=bm:69bb07e8-0dab-476a-8682-f3af9c6fdd53){: external} | [Certification #2020027](https://www.sap.com/dmc/benchmark/2021/Cert21015.pdf){: external} |
| BI.S4.H4.6000 | IBM Cloud Intel Bare Metal | TDI | OLAP | Up to 16 nodes:  \n 15 active nodes _(1 parent, 14 worker nodes)_ and 1 standby node | 1,680 | 3,360 | 92,160 | [OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2213){: external} | [Scale-Out Record](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/bwh?id=bm:44417c84-5010-4fae-9245-18e2cbd971d2){: external} | [Certification #2020032](https://www.sap.com/dmc/benchmark/2020/Cert20032.pdf){: external} |
| BI.S4.H8.6000 | IBM Cloud Intel Bare Metal | TDI | OLAP | Up to 16 nodes:  \n15 active nodes _(1 parent, 14 worker nodes)_ and 1 standby node | 3,360 | 6,720 | 92,160 | [OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2305){: external} | [Scale-Out Record](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/bwh?id=bm:e9958ccc-60c6-480f-82f0-59c00ac5f3ce){: external} |[Certification #2021014](https://www.sap.com/dmc/benchmark/2021/Cert21014.pdf){: external} |
| BI.S4.H8.12000 | IBM Cloud Intel Bare Metal | TDI | OLAP | Up to 16 nodes:  \n15 active nodes _(1 parent, 14 worker nodes)_ and 1 standby node | 3,360 | 6,720 | 184,320 | [OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2302){: external} | [Scale-Out Record](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/bwh?id=bm:f050d27b-f541-4807-9df1-78aae4095661){: external} |[Certification #2021065](https://www.sap.com/dmc/benchmark/2021/Cert21065.pdf){: external} |
| BI.S4.H8.12000 | IBM Cloud Intel Bare Metal | TDI | OLTP | Up to 4 nodes:  \n 3 active nodes _(1 parent, 2 worker nodes)_ and 1 standby node | 672 | 1,344 | 36,864 | [OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2304){: external} | n/a | n/a |
{: caption="Table 3. Full list of SAP-certified IaaS in scale-out deployment" caption-side="top"}


## Do you have a full list of Profiles on the SAP HANA Hardware Directory?
{: #faq-profile-hana-hardware-directory}

**For a full list of all SAP HANA Directory records for each certified Infrastructure-as-a-Service Profile specifications in the {{site.data.keyword.ibm_cloud_sap}} portfolio _(including complementary offerings from {{site.data.keyword.IBM_notm}} Power Systems)_, see the table below:**

| Profile | IaaS Type | SAP HANA Directory |
| -- | -- | -- |
| BI.S3.H2.192 | IBM Cloud Intel Bare Metal \n with local disk storage | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2256){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2019){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2201){: external}</li></ul> |
| BI.S3.H2.384 | IBM Cloud Intel Bare Metal \n with local disk storage | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2255){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2020){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2202){: external})</li></ul> |
| BI.S3.H2.768 | IBM Cloud Intel Bare Metal \n with local disk storage | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2254){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2021){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2205){: external}</li></ul> |
| BI.S4.H2.192 | IBM Cloud Intel Bare Metal \n with local disk storage | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2252){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2190){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2200){: external}</li></ul> |
| BI.S4.H2.384 | IBM Cloud Intel Bare Metal \n with local disk storage | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2250){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2189){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2203){: external}</li></ul> |
| BI.S4.H2.768 | IBM Cloud Intel Bare Metal \n with local disk storage | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2249){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2188){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2204){: external}</li></ul> |
| BI.S4.H2.1500 | IBM Cloud Intel Bare Metal \n with local disk storage | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2253){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2191){: external}</li></ul> |
| BI.S4.H2.3000 | IBM Cloud Intel Bare Metal \n with local disk storage | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2251){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2192){: external}</li></ul> |
| BI.S4.H4.3000 | IBM Cloud Intel Bare Metal \n with local disk storage | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2248){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2212){: external}</li></ul> |
| BI.S4.H4.6000 | IBM Cloud Intel Bare Metal \n with local disk storage | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2247){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2213){: external}</li></ul> |
| BI.S4.H8.6000 | IBM Cloud Intel Bare Metal \n with local disk storage | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2303){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2305){: external}</li></ul> |
| BI.S4.H8.12000 | IBM Cloud Intel Bare Metal \n with local disk storage | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2304){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2302){: external}</li></ul> |
| mx2-8x64 | IBM Cloud Intel Virtual Server | <ul><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2563){: external}</li></ul> |
| mx2-16x128 | IBM Cloud Intel Virtual Server | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2369){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2418){: external}</li></ul> |
| mx2-32x256 | IBM Cloud Intel Virtual Server | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2370){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2419){: external}</li></ul> |
| mx2-48x384 | IBM Cloud Intel Virtual Server | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2371){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2420){: external}</li></ul> |
| vx2d-16x224 | IBM Cloud Intel Virtual Server | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;&id=s:2624){: external}</li></ul> |
| vx2d-44x616 | IBM Cloud Intel Virtual Server | <ul><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;&id=s:2603){: external}</li><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;&id=s:2623){: external}</li></ul> |
| vx2d-88x1232 | IBM Cloud Intel Virtual Server | <ul><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;&id=s:2604){: external}</li><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;&id=s:2622){: external}</li></ul> |
| vx2d-144x2016 | IBM Cloud Intel Virtual Server | <ul><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;&id=s:2605){: external}</li><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;&id=s:2621){: external}</li></ul> |
| vx2d-176x2464 | IBM Cloud Intel Virtual Server | <ul><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;&id=s:2606){: external}</li><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;&id=s:2620){: external}</li></ul> |
| ux2d-8x224 | IBM Cloud Intel Virtual Server | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;&id=s:2613){: external}</li></ul> |
| ux2d-16x448 | IBM Cloud Intel Virtual Server | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;&id=s:2614){: external}</li></ul> |
| ux2d-36x1008 | IBM Cloud Intel Virtual Server | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;&id=s:2615){: external}</li></ul> |
| ux2d-48x1344 | IBM Cloud Intel Virtual Server | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;&id=s:2616){: external}</li></ul> |
| ux2d-72x2016 | IBM Cloud Intel Virtual Server | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;&id=s:2617){: external}</li></ul> |
| ux2d-100x2800 | IBM Cloud Intel Virtual Server | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;&id=s:2618){: external}</li></ul> |
| ux2d-200x5600 | IBM Cloud Intel Virtual Server | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;&id=s:2619){: external}</li></ul> |
| bh1-16x1600 | IBM Power Virtual Server | <ul><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2358){: external}</li></ul> |
| bh1-60x6000 | IBM Power Virtual Server | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2372){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2359){: external}</li></ul> |
| bh1-140x14000 | IBM Power Virtual Server | <ul><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2387){: external}</li></ul> |
| ch1-60x3000 | IBM Power Virtual Server | <ul><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2357){: external}</li></ul> |
| ch1-140x7000 | IBM Power Virtual Server | <ul><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2373){: external}</li></ul> |
| umh1-4x960 | IBM Power Virtual Server | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2385){: external}</li></ul> |
| umh1-16x3840 | IBM Power Virtual Server | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2360){: external}</li></ul> |
| umh1-60x14400 | IBM Power Virtual Server | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2386){: external}</li></ul> |
| BI.S3.H2.192 (VMware) | IBM Cloud for VMware | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2256){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2019){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2201){: external}</li></ul> |
| BI.S3.H2.384 (VMware) | IBM Cloud for VMware | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2255){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2020){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2202){: external}</li></ul> |
| BI.S3.H2.768 (VMware) | IBM Cloud for VMware | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2254){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2021){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2205){: external}</li></ul> |
| BI.S4.H2.192 (VMware) | IBM Cloud for VMware | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2252){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2190){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2200){: external}</li></ul> |
| BI.S4.H2.384 (VMware) | IBM Cloud for VMware | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2250){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2189){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2203){: external}</li></ul> |
| BI.S4.H2.768 (VMware) | IBM Cloud for VMware | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2249){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2188){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2204){: external}</li></ul> |
| BI.S4.H2.1500 (VMware) | IBM Cloud for VMware | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2253){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2191){: external}</li></ul> |
| BI.S4.H2.3000 (VMware) | IBM Cloud for VMware | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2251){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2192){: external}</li></ul> |
| BI.S4.H4.3000 (VMware) | IBM Cloud for VMware | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2248){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2212){: external}</li></ul> |
| BI.S4.H4.6000 (VMware) | IBM Cloud for VMware | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2247){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2213){: external}</li></ul> |
| BI.S4.H8.6000 (VMware) | IBM Cloud for VMware | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2303){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas&id=s:2305){: external}</li></ul> |
{: caption="Table 4. Full list of profiles on SAP HANA Hardware Directory" caption-side="top"}
