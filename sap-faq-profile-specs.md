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
{:important: .important}

# FAQ of Profile List with Benchmarks and Specifications
{: #faq-profile-specs}

## What are the certifications, highest benchmarks, and highest specifications available for different offerings?
{: #faq-profile-specs-maximums}

Below is a summary table of the certifications, highest benchmark scores, and maximum RAM configurations for each offering are shown in this table:

| IaaS Type | SAPS benchmark | OLTP scale-up memory (GB) | OLAP scale-up memory (GB) | OLAP scale-out memory (GB) |
| -- | -- | -- | -- | -- |
| IBM Cloud Intel Bare Metal with Local Disk | 550,670<br />_(world record of Intel-based IaaS)_ | 12,288 | 6,144 | 92,160 from 15 nodes (excl. 1 standby)<br />_(world record of all IaaS)_  |
| IBM Cloud Intel Virtual Server (Gen2) | 56,970 | 384 | - | - |
| IBM Power Virtual Server<br />_(complementary offering from IBM Systems)_ | 840,000<br />_(world record of all IaaS)_ | 14,400 | 14,000 | - |
| IBM Cloud for VMware | 495,603 | 6,144 | 3,072 | 30,720 from 15 nodes (excl. 1 standby) |
{: caption="Table 1. Highest specifications of the SAP-certified IaaS" caption-side="top"}


## Do you have a full list of Profile specifications, including SAP sizing for different workloads?
{: #faq-profile-specs-table}

Each list of Infrastructure profiles for SAP HANA database server:
- [Intel Bare Metal server certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-bm)
- [Intel Virtual Server (Gen2) certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-vs-vpc)
- [IBM Power Virtual Server certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-power-vs) _This is a complementary offering from IBM Systems_
- [VMware SDDC certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-vmware)

Each list of Infrastructure profiles for SAP NetWeaver Application Server:
- [Intel Bare Metal server certified profiles for SAP NetWeaver](/docs/sap?topic=sap-nw-iaas-offerings-profiles-intel-bm)
- [Intel Virtual Server (Gen2) certified profiles for SAP NetWeaver](/docs/sap?topic=sap-nw-iaas-offerings-profiles-intel-vs-vpc)
- [IBM Power Virtual Server certified profiles for SAP NetWeaver](/docs/sap?topic=sap-nw-iaas-offerings-profiles-power-vs) _This is a complementary offering from IBM Systems_
- [VMware SDDC certified profiles for SAP NetWeaver](/docs/sap?topic=sap-nw-iaas-offerings-profiles-vmware)


All SAP-certified infrastructure for SAP HANA also includes certification for SAP NetWeaver and SAP AnyDB. However this is not vice-versa, any SAP-certified infratructure for SAP NetWeaver only includes SAP AnyDB.

**For a full list of all Infrastructure-as-a-Service Profile specifications in the {{site.data.keyword.ibm_cloud_sap}} portfolio _(including complementary offerings from IBM Systems)_ see the table below:**

This can be copied into spreadsheet software
{: note}


| Profile | IaaS Type | CPU Cores | CPU Threads (aka. vCPU) | CPU Architecture | Memory (DRAM GB) | DRAM per CPU Thread | SD Two-Tier benchmark (SAPS) | SAP NetWeaver support | SAP HANA Deployment Method | SAP HANA Processing Type | SAP HANA Directory |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| BI.S3.H2.192 | IBM Cloud Intel Bare Metal with Local Disk | 36 | 72 | Intel Skylake SP | 192 | 2.67 | 78,850 | Yes | Appliance | OLAP/OLTP and SAP Business One for HANA | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2256){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2019){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2201){: external}</li></ul> |
| BI.S3.H2.384 | IBM Cloud Intel Bare Metal with Local Disk | 36 | 72 | Intel Skylake SP | 384 | 5.33 | 79,430 | Yes | Appliance | OLAP/OLTP and SAP Business One for HANA| <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2255){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2020){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2202{: external})</li></ul> |
| BI.S3.H2.768 | IBM Cloud Intel Bare Metal with Local Disk | 36 | 72 | Intel Skylake SP | 768 | 10.67 | 79,630 | Yes | Appliance | OLAP/OLTP and SAP Business One for HANA| <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2254){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2021){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2205){: external}</li></ul> |
| BI.S4.H2.192 | IBM Cloud Intel Bare Metal with Local Disk | 32 | 64 | Intel Cascade Lake SP | 192 | 3.00 | 60,984 | Yes | Appliance | OLAP/OLTP and SAP Business One for HANA| <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2252){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2190){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2200){: external}</li></ul> |
| BI.S4.H2.384 | IBM Cloud Intel Bare Metal with Local Disk | 32 | 64 | Intel Cascade Lake SP | 384 | 6.00 | 85,130 | Yes | Appliance | OLAP/OLTP and SAP Business One for HANA| <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2250){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2189){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2203){: external}</li></ul> |
| BI.S4.H2.768 | IBM Cloud Intel Bare Metal with Local Disk | 40 | 80 | Intel Cascade Lake SP | 768 | 9.60 | 112,830 | Yes | Appliance | OLAP/OLTP and SAP Business One for HANA| <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2249){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2188){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2204){: external}</li></ul> |
| BI.S4.H2.1500 | IBM Cloud Intel Bare Metal with Local Disk | 56 | 112 | Intel Cascade Lake SP | 1,536 | 13.71 | 147,220 | Yes | Appliance | OLAP/OLTP| <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2253){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2191){: external}</li></ul> |
| BI.S4.H2.3000 | IBM Cloud Intel Bare Metal with Local Disk | 56 | 112 | Intel Cascade Lake SP | 3,072 | 27.43 | 135,127 | Yes | Appliance | OLAP/OLTP| <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2251){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2192){: external}</li></ul> |
| BI.S4.H4.3000 | IBM Cloud Intel Bare Metal with Local Disk | 112 | 224 | Intel Cascade Lake SP | 3,072 | 13.71 | 285,970 | Yes | Appliance | OLAP/OLTP| <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2248){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2212){: external}</li></ul> |
| BI.S4.H4.6000 | IBM Cloud Intel Bare Metal with Local Disk | 112 | 224 | Intel Cascade Lake SP | 6,144 | 27.43 | 285,970 | Yes | Appliance | OLAP/OLTP| <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2247){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2213){: external}</li></ul> |
| BI.S4.H8.6000 | IBM Cloud Intel Bare Metal with Local Disk | 224 | 448 | Intel Cascade Lake SP | 6,144 | 13.71 | 550,670 | Yes | Appliance | OLAP/OLTP| <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2303){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2305){: external}</li></ul> |
| BI.S4.H8.12000 | IBM Cloud Intel Bare Metal with Local Disk | 224 | 448 | Intel Cascade Lake SP | 12,288 | 27.43 | 550,670 | Yes | Appliance | OLAP/OLTP| <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2304){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2302){: external}</li></ul> |
| mx2-16x128 | IBM Cloud Intel Virtual Server (Gen2) | - | 16 | Intel Cascade Lake SP | 128 | 8.00 | 20,560 | Yes | Appliance | OLTP | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2369){: external}</li></ul> |
| mx2-32x256 | IBM Cloud Intel Virtual Server (Gen2) | - | 32 | Intel Cascade Lake SP | 256 | 8.00 | 41,130 | Yes | Appliance | OLTP | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2370){: external}</li></ul> |
| mx2-48x384 | IBM Cloud Intel Virtual Server (Gen2) | - | 48 | Intel Cascade Lake SP | 384 | 8.00 | 56,970 | Yes | Appliance | OLTP | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2371){: external}</li></ul> |
| bh1-16x1600 | IBM Power Virtual Server | 16 | 128 | IBM POWER9 | 1,600 | 12.50 | 96,000 | Yes | Appliance | OLAP | <ul><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2358){: external}</li></ul> |
| bh1-20x2000 | IBM Power Virtual Server | 20 | 160 | IBM POWER9 | 2,000 | 12.50 | 120,000 | Yes | Appliance | OLAP * | |
| bh1-22x2200 | IBM Power Virtual Server | 22 | 178 | IBM POWER9 | 2,200 | 12.36 | 132,000 | Yes | Appliance | OLAP * | |
| bh1-25x2500 | IBM Power Virtual Server | 25 | 200 | IBM POWER9 | 2,500 | 12.50 | 150,000 | Yes | Appliance | OLAP * | |
| bh1-30x3000 | IBM Power Virtual Server | 30 | 240 | IBM POWER9 | 3,000 | 12.50 | 180,000 | Yes | Appliance | OLAP * | |
| bh1-35x3500 | IBM Power Virtual Server | 35 | 280 | IBM POWER9 | 3,500 | 12.50 | 210,000 | Yes | Appliance | OLAP * | |
| bh1-40x4000 | IBM Power Virtual Server | 40 | 320 | IBM POWER9 | 4,000 | 12.50 | 240,000 | Yes | Appliance | OLAP * | |
| bh1-50x5000 | IBM Power Virtual Server | 50 | 400 | IBM POWER9 | 5,000 | 12.50 | 300,000 | Yes | Appliance | OLAP * | |
| bh1-60x6000 | IBM Power Virtual Server | 60 | 480 | IBM POWER9 | 6,000 | 12.50 | 360,000 | Yes | Appliance | OLAP/OLTP | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2372){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2359){: external}</li></ul> |
| bh1-70x7000 | IBM Power Virtual Server | 70 | 560 | IBM POWER9 | 7,000 | 12.50 | 420,000 | Yes | Appliance | OLAP * | |
| bh1-80x8000 | IBM Power Virtual Server | 80 | 640 | IBM POWER9 | 8,000 | 12.50 | 480,000 | Yes | Appliance | OLAP * | |
| bh1-100x10000 | IBM Power Virtual Server | 100 | 800 | IBM POWER9 | 10,000 | 12.50 | 600,000 | Yes | Appliance | OLAP * | |
| bh1-120x12000 | IBM Power Virtual Server | 120 | 900 | IBM POWER9 | 12,000 | 13.33 | 720,000 | Yes | Appliance | OLAP * | |
| bh1-140x14000 | IBM Power Virtual Server | 140 | 1,120 | IBM POWER9 | 14,000 | 12.50 | 840,000 | Yes | Appliance | OLAP | <ul><li>[OLAP Record]</li></ul> |
| ch1-60x3000 | IBM Power Virtual Server | 60 | 480 | IBM POWER9 | 3,000 | 6.25 | 360,000 | Yes | Appliance | OLAP | <ul><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2357){: external}</li></ul> |
| ch1-70x3500 | IBM Power Virtual Server | 70 | 560 | IBM POWER9 | 3,500 | 6.25 | 420,000 | Yes | Appliance | OLAP * | |
| ch1-80x4000 | IBM Power Virtual Server | 80 | 640 | IBM POWER9 | 4,000 | 6.25 | 480,000 | Yes | Appliance | OLAP * | |
| ch1-100x5000 | IBM Power Virtual Server | 100 | 800 | IBM POWER9 | 5,000 | 6.25 | 600,000 | Yes | Appliance | OLAP * | |
| ch1-120x6000 | IBM Power Virtual Server | 120 | 900 | IBM POWER9 | 6,000 | 6.66 | 720,000 | Yes | Appliance | OLAP * | |
| ch1-140x7000 | IBM Power Virtual Server | 140 | 1,120 | IBM POWER9 | 7,000 | 6.25 | 840,000 | Yes | Appliance | OLAP | <ul><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2373){: external}</li></ul> |
| umh1-4x960 | IBM Power Virtual Server | 4 | 32 | IBM POWER9 | 960 | 30 | 24,000 | Yes | Appliance | OLTP | <ul><li>[OLTP Record]</li></ul> |
| umh1-6x1440 | IBM Power Virtual Server | 6 | 48 | IBM POWER9 | 1,440 | 30 | 36,000 | Yes | Appliance | OLTP * | |
| umh1-8x1920 | IBM Power Virtual Server | 8 | 64 | IBM POWER9 | 1,920 | 30 | 48,000 | Yes | Appliance | OLTP * | |
| umh1-10x2400 | IBM Power Virtual Server | 10 | 80 | IBM POWER9 | 2,400 | 30 | 60,000 | Yes | Appliance | OLTP * | |
| umh1-12x2880 | IBM Power Virtual Server | 12 | 96 | IBM POWER9 | 2,880 | 30 | 72,000 | Yes | Appliance | OLTP * | |
| umh1-16x3840 | IBM Power Virtual Server | 16 | 128 | IBM POWER9 | 3,840 | 30 | 96,000 | Yes | Appliance | OLTP | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2360){: external}</li></ul> |
| umh1-20x4800 | IBM Power Virtual Server | 20 | 160 | IBM POWER9 | 4,800 | 30 | 120,000 | Yes | Appliance | OLTP * | |
| umh1-22x5280 | IBM Power Virtual Server | 22 | 176 | IBM POWER9 | 5,280 | 30 | 132,000 | Yes | Appliance | OLTP * | |
| umh1-25x6000 | IBM Power Virtual Server | 25 | 200 | IBM POWER9 | 6,000 | 30 | 150,000 | Yes | Appliance | OLTP * | |
| umh1-30x7200 | IBM Power Virtual Server | 30 | 240 | IBM POWER9 | 7,200 | 30 | 180,000 | Yes | Appliance | OLTP * | |
| umh1-35x8400 | IBM Power Virtual Server | 35 | 280 | IBM POWER9 | 8,400 | 30 | 210,000 | Yes | Appliance | OLTP * | |
| umh1-40x9600 | IBM Power Virtual Server | 40 | 320 | IBM POWER9 | 9,600 | 30 | 240,000 | Yes | Appliance | OLTP * | |
| umh1-50x12000 | IBM Power Virtual Server | 50 | 400 | IBM POWER9 | 12,000 | 30 | 300,000 | Yes | Appliance | OLTP * | |
| umh1-60x14400 | IBM Power Virtual Server | 60 | 480 | IBM POWER9 | 14,400 | 30 | 360,000 | Yes | Appliance | OLTP | <ul><li>[OLTP Record]</li></ul> |
| BI.S3.H2.192 (VMware) | IBM Cloud for VMware | 36 | 72 | Intel Skylake SP | 192 | 2.67 | 70,965 | Yes | TDI | OLAP/OLTP and SAP Business One for HANA | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2256){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2019){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2201){: external}</li></ul> |
| BI.S3.H2.384 (VMware) | IBM Cloud for VMware | 36 | 72 | Intel Skylake SP | 384 | 5.33 | 71,487 | Yes | TDI | OLAP/OLTP and SAP Business One for HANA | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2255){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2020){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2202){: external}</li></ul> |
| BI.S3.H2.768 (VMware) | IBM Cloud for VMware | 36 | 72 | Intel Skylake SP | 768 | 10.67 | 71,667 | Yes | TDI | OLAP/OLTP and SAP Business One for HANA | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2254){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2021){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2205){: external}</li></ul> |
| BI.S4.H2.192 (VMware) | IBM Cloud for VMware | 32 | 64 | Intel Cascade Lake SP | 192 | 3.00 | 54,886 | Yes | TDI | OLAP/OLTP and SAP Business One for HANA | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2252){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2190){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2200){: external}</li></ul> |
| BI.S4.H2.384 (VMware) | IBM Cloud for VMware | 32 | 64 | Intel Cascade Lake SP | 384 | 6.00 | 76,617 | Yes | TDI | OLAP/OLTP and SAP Business One for HANA | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2250){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2189){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2203){: external}</li></ul> |
| BI.S4.H2.768 (VMware) | IBM Cloud for VMware | 40 | 80 | Intel Cascade Lake SP | 768 | 9.60 | 101,547 | Yes | TDI | OLAP/OLTP and SAP Business One for HANA | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2249){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2188){: external}</li><li>[Business One Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2204){: external}</li></ul> |
| BI.S4.H2.1500 (VMware) | IBM Cloud for VMware | 56 | 112 | Intel Cascade Lake SP | 1,536 | 13.71 | 132,498 | Yes | TDI | OLAP/OLTP | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2253){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2191){: external}</li></ul> |
| BI.S4.H2.3000 (VMware) | IBM Cloud for VMware | 56 | 112 | Intel Cascade Lake SP | 3,072 | 27.43 | 121,614 | Yes | TDI | OLAP/OLTP | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2251){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2192){: external}</li></ul> |
| BI.S4.H4.3000 (VMware) | IBM Cloud for VMware | 112 | 224 | Intel Cascade Lake SP | 3,072 | 13.71 | 257,373 | Yes | TDI | OLAP/OLTP | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2248){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2212){: external}</li></ul> |
| BI.S4.H4.6000 (VMware) | IBM Cloud for VMware | 112 | 224 | Intel Cascade Lake SP | 6,144 | 27.43 | 257,373 | Yes | TDI | OLAP/OLTP | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2247){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2213){: external}</li></ul> |
| BI.S4.H8.6000 (VMware) | IBM Cloud for VMware | 224 | 448 | Intel Cascade Lake SP | 6,144 | 13.71 | 495,603 | Yes | TDI | OLAP/OLTP | <ul><li>[OLTP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2303){: external}</li><li>[OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2305){: external}</li></ul> |
| BI.S3.NW32 | IBM Cloud Intel Bare Metal with Local Disk | 4 | 8 | Intel Kaby Lake | 32 | 4.00 | 11,970 | Yes |   |   |   |
| BI.S3.NW64 | IBM Cloud Intel Bare Metal with Local Disk | 4 | 8 | Intel Kaby Lake | 64 | 8.00 | 12,750 | Yes |   |   |   |
| BI.S3.NW192 | IBM Cloud Intel Bare Metal with Local Disk | 36 | 72 | Intel Skylake SP | 192 | 2.67 | 78,850 | Yes |   |   |   |
| BI.S3.NW384 | IBM Cloud Intel Bare Metal with Local Disk | 36 | 72 | Intel Skylake SP | 384 | 5.33 | 79,430 | Yes |   |   |   |
| BI.S3.NW768 | IBM Cloud Intel Bare Metal with Local Disk | 36 | 72 | Intel Skylake SP | 768 | 10.67 | 79,630 | Yes |   |   |   |
| BI.S4.NW192 | IBM Cloud Intel Bare Metal with Local Disk | 32 | 64 | Intel Cascade Lake SP | 192 | 3.00 | 60,984 | Yes |   |   |   |
| BI.S4.NW384 | IBM Cloud Intel Bare Metal with Local Disk | 32 | 64 | Intel Cascade Lake SP | 384 | 6.00 | 85,130 | Yes |   |   |   |
| BI.S4.NW768 | IBM Cloud Intel Bare Metal with Local Disk | 40 | 80 | Intel Cascade Lake SP | 768 | 9.60 | 112,830 | Yes |   |   |   |
| bx2-2x8 | IBM Cloud Intel Virtual Server (Gen2) | - | 2 | Intel Cascade Lake SP | 8 | 4.00 | 2,300 | Yes |   |   |   |
| bx2-4x16 | IBM Cloud Intel Virtual Server (Gen2) | - | 4 | Intel Cascade Lake SP | 16 | 4.00 | 4,600 | Yes |   |   |   |
| bx2-8x32 | IBM Cloud Intel Virtual Server (Gen2) | - | 8 | Intel Cascade Lake SP | 32 | 4.00 | 9,200 | Yes |   |   |   |
| bx2-16x64 | IBM Cloud Intel Virtual Server (Gen2) | - | 16 | Intel Cascade Lake SP | 64 | 4.00 | 18,450 | Yes |   |   |   |
| bx2-32x128 | IBM Cloud Intel Virtual Server (Gen2) | - | 32 | Intel Cascade Lake SP | 128 | 4.00 | 36,900 | Yes |   |   |   |
| bx2-48x192 | IBM Cloud Intel Virtual Server (Gen2) | - | 48 | Intel Cascade Lake SP | 192 | 4.00 | 55,350 | Yes |   |   |   |
| mx2-2x16 | IBM Cloud Intel Virtual Server (Gen2) | - | 2 | Intel Cascade Lake SP | 16 | 8.00 | 2,570 | Yes |   |   |   |
| mx2-4x32 | IBM Cloud Intel Virtual Server (Gen2) | - | 4 | Intel Cascade Lake SP | 32 | 8.00 | 5,140 | Yes |   |   |   |
| mx2-8x64 | IBM Cloud Intel Virtual Server (Gen2) | - | 8 | Intel Cascade Lake SP | 64 | 8.00 | 10,280 | Yes |   |   |   |
| mx2-16x128 | IBM Cloud Intel Virtual Server (Gen2) | - | 16 | Intel Cascade Lake SP | 128 | 8.00 | 20,560 | Yes |   |   |   |
| mx2-32x256 | IBM Cloud Intel Virtual Server (Gen2) | - | 32 | Intel Cascade Lake SP | 256 | 8.00 | 41,130 | Yes |   |   |   |
| mx2-48x384 | IBM Cloud Intel Virtual Server (Gen2) | - | 48 | Intel Cascade Lake SP | 384 | 8.00 | 56,970 | Yes |   |   |   |
| BI.S3.NW192 (VMware) | IBM Cloud for VMware | 36 | 72 | Intel Skylake SP | 192 | 2.67 | 70,965 | Yes |   |   |   |
| BI.S3.NW384 (VMware) | IBM Cloud for VMware | 36 | 72 | Intel Skylake SP | 384 | 5.33 | 71,487 | Yes |   |   |   |
| BI.S3.NW768 (VMware) | IBM Cloud for VMware | 36 | 72 | Intel Skylake SP | 768 | 10.67 | 71,667 | Yes |   |   |   |
| BI.S4.NW192 (VMware) | IBM Cloud for VMware | 32 | 64 | Intel Cascade Lake SP | 192 | 3.00 | 54,886 | Yes |   |   |   |
| BI.S4.NW384 (VMware) | IBM Cloud for VMware | 32 | 64 | Intel Cascade Lake SP | 384 | 6.00 | 76,617 | Yes |   |   |   |
| BI.S4.NW768 (VMware) | IBM Cloud for VMware | 40 | 80 | Intel Cascade Lake SP | 768 | 9.60 | 101,547 | Yes |   |   |   |
| BI.S4.NW1500 (VMware) | IBM Cloud for VMware | 56 | 112 | Intel Cascade Lake SP | 1,536 | 13.71 | 132,498 | Yes |   |   |   |
| BI.S4.NW3000 (VMware) | IBM Cloud for VMware | 56 | 112 | Intel Cascade Lake SP | 3,072 | 27.43 | 121,614 | Yes |   |   |   |
{: caption="Table 2. Full list of SAP-certified IaaS" caption-side="top"}

Those marked with * asterisk, are _not_ listed on the SAP HANA Hardware Directory by SAP but **are** certified for running SAP HANA production systems. The directory lists the smallest, median and largest within each profile family. This action has been taken by SAP to avoid too many records, as the scalability of IBM POWER hardware enables significantly more granular sizing. See [SAP Note 2947579 - SAP HANA on IBM Power Systems Virtual Servers](https://launchpad.support.sap.com/#/notes/2947579){: external} for additional detail.
{: note}


## Do you have a full list of Profile specifications for SAP HANA scale-out?
{: #faq-profile-specs-hana-scale-out-table}

**For a full list of all SAP HANA scale-out certified Infrastructure-as-a-Service Profile specifications in the {{site.data.keyword.ibm_cloud_sap}} portfolio _(including complementary offerings from IBM Systems)_ see the table below:**

This can be copied into spreadsheet software
{: note}


| Profile | IaaS Type | SAP HANA Deployment Method | SAP HANA System Type | SAP HANA Processing Type | Scale-out Configuration | Total CPU Cores | Total CPU Threads (aka. vCPU) | Total Memory (DRAM GB) | SAP HANA Directory | SAP Benchmark Directory |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| BI.S4.H4.3000 | IBM Cloud Intel Bare Metal with Local Disk | Appliance | Scale-out | OLAP | Up to 16 nodes:<br/>15 active nodes _(1 parent, 14 worker nodes)_ and 1 standby node | 1,680 | 3,360 | 46,080 | [OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2212){: external} | [Certification 2020027](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/bwh?filters=4a9e824336e2837bf9081e423d57e5c9&id=69bb07e8-0dab-476a-8682-f3af9c6fdd53){: external} |
| BI.S4.H4.6000 | IBM Cloud Intel Bare Metal with Local Disk | TDI | Scale-out | OLAP | Up to 16 nodes:<br/>15 active nodes _(1 parent, 14 worker nodes)_ and 1 standby node | 1,680 | 3,360 | 92,160 | [OLAP Record](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#recordid=2213){: external} | [Certification 2020032](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/bwh?filters=4a9e824336e2837bf9081e423d57e5c9&id=44417c84-5010-4fae-9245-18e2cbd971d2){: external} |
{: caption="Table 3. Full list of SAP-certified IaaS in scale-out deployment" caption-side="top"}
