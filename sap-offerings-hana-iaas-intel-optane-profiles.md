---

copyright:
  years: 2021
lastupdated: "2021-05-18"

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

# Intel Bare Metal server with Intel Optane DC Persistent Memory certified profiles for SAP HANA
{: #hana-iaas-offerings-profiles-intel-optane}


## Profiles list
{: #hana-iaas-intel-optane-list}

The following table is an overview of the SAP-certified profiles for Intel Optane PMem on Bare Metal servers:

|Profile|Cores|Speed|RAM|Storage|Features|  
|-----------|-------------|---------------|----|----|-----| 
|1:1 DRAM:PMem |
|[BI.S4.H2.1.5TB RAM + 1.5TB Persistent Memory](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=15706&packageId=2636&presetId=1174){: external}|56|2.70 GHx|1536 GB|12 Drives|SAP|
|[BI.S4.H4.3TB RAM + 3TB Persistent Memory](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=15706&packageId=2676&presetId=1250){: external}|112|2.70 GHz|3072 GB|12 Drives|SAP|
|[BI.S4.H8.6TB RAM + 6TB Persistent Memory](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=8451&packageId=1041&itemId=10831){: external}|224 |2.70 GHz|6144 GB|12 Drives|SAP|
|1:2 DRAM:PMem|
|[BI.S4.H2.768GB RAM + 1.5TB Persistent Memory](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=15706&packageId=2636&presetId=1170){: external}|56|2.70 GHx|768 GB|10|SAP|
|[BI.S4.H2.1.5TB RAM + 3TB Persistent Memory](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=15706&packageId=2636&presetId=1176){: external}|56|2.70 GHx|1536 GB|10|SAP|
|[BI.S4.H4.1.5TB RAM + 3TB Persistent Memory](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=15706&packageId=2676&presetId=1220){: external}|112|2.70 GHx|1536 GB|10|SAP|
|[BI.S4.H4.3TB RAM + 6TB Persistent Memory](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=15706&packageId=2676&presetId=1252){: external}|112|2.70 GHx|3072 GB|14|SAP|
|[BI.S4.H8.3TB RAM + 6TB Persistent Memory](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=15706&packageId=2700&presetId=1256){: external}|224|2.70 GHx|3072 GB|14|SAP|
|[BI.S4.H8.6TB RAM + 12TB Persistent Memory](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=15706&packageId=2700&presetId=1262){: external}|224|2.70 GHx|6144 GB|14|SAP|
|1:4 DRAM:PMem|
|[BI.S4.H2.384GB RAM + 1.5TB Persistent Memory](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=15706&packageId=2636&presetId=1166){: external}|56|2.70 GHx|384 GB|10|SAP|
|[BI.S4.H2.768GB RAM + 3TB Persistent Memory](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=15706&packageId=2636&presetId=1172){: external}|56|2.70 GHx|768 GB|12|SAP|
|[BI.S4.H4.768GB RAM + 3TB Persistent Memory](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=15706&packageId=2676&presetId=1246){: external}|112|2.70 GHx|768 GB|14|SAP|
|[BI.S4.H4.1.5TB RAM + 6TB Persistent Memory](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=15706&packageId=2676&presetId=1248){: external}|112|2.70 GHx|1536 GB|14|SAP|
|[BI.S4.H8.1.5TB RAM + 6TB Persistent Memory](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=15706&packageId=2700&presetId=1254){: external}|224|2.70 GHx|1536|14|SAP|
|[BI.S4.H8.3TB RAM + 12TB Persistent Memory](https://cloud.ibm.com/gen1/infrastructure/provision/bm?imageItemId=15706&packageId=2700&presetId=1258){: external}|224|2.70 GHx|3072 GB|12|SAP|

## Bare Metal profile names
{: #hana-iaas-intel-bm-names-pmem}

The Bare Metal profile names are contextual and sequential. For more information, see [Understanding Bare Metal profile names](https://test.cloud.ibm.com/docs/sap?topic=sap-hana-iaas-offerings-profiles-intel-bm#hana-iaas-intel-bm-names) 

## Storage specifications
{: #hana-iaas-intel-optane-storage-specs-optane}

|Name|HANA DB Size (TB)|Storage Needed (1TB shared plus DB size data)|Boot (Log)|Storage Array (Data + Shared)|
|---|---|---|---|---|
|BI.S4.H2.1.5TB RAM + 1.5TB Persistent Memory|3|7|2x 960GB SSD (RAID 1)|8 x 1.9TB SSD (RAID 10)|
|BI.S4.H4.3TB RAM + 3TB Persistent Memory|6|13|2x 960GB SSD (RAID 1)|8 x 3.8TB SSD (RAID 10)|
|BI.S4.H8.6TB RAM + 6TB Persistent Memory|12|25|2x 960GB SSD (RAID 1)|8 x 7.68TB SSD (RAID 10)|
|1:2 DRAM:PMem|
|BI.S4.H2.768GB RAM + 1.5TB Persistent Memory|2.25|5.5|2x 960GB SSD (RAID 1)|6 x 1.9TB SSD (RAID 10)|
|BI.S4.H2.1.5TB RAM + 3TB Persistent Memory|4.5|10|2x 960GB SSD (RAID 1)|6 x 3.8TB SSD (RAID 10)|
|BI.S4.H2.768GB RAM + 3TB Persistent Memory|3.75|8.5|2x 960GB SSD (RAID 1)|10 x 1.9TB SSD (RAID 10)|
|BI.S4.H4.1.5TB RAM + 3TB Persistent Memory|4.5|10|2x 960GB SSD (RAID 1)|6 x 3.8TB SSD (RAID 10)|
|BI.S4.H4.3TB RAM + 6TB Persistent Memory|9|19|2x 960GB SSD (RAID 1)|10 x 3.8TB SSD (RAID 10)
|BI.S4.H8.3TB RAM + 6TB Persistent Memory|9|19|2x 960GB SSD (RAID 1)|10 x 3.8TB SSD (RAID 10)|
|BI.S4.H8.6TB RAM + 12TB Persistent Memory|18|37|2x 960GB SSD (RAID 1)|10 x 7.68TB SSD (RAID 10)|
|1:4 DRAM:PMem|
|BI.S4.H8.6TB RAM + 12TB Persistent Memory|1.75|4.5|2x 960GB SSD (RAID 1)|6 x 1.9TB SSD (RAID 10)|
|BI.S4.H4.768GB RAM + 3TB Persistent Memory|3.75|8.5|2x 960GB SSD (RAID 1)|10 x 1.9TB SSD (RAID 10)|
|BI.S4.H4.1.5TB RAM + 6TB Persistent Memory|7.5|16|2x 960GB SSD (RAID 1)|10 x 3.8TB SSD (RAID 10)|
|BI.S4.H8.1.5TB RAM + 6TB Persistent Memory|7.5|16|2x 960GB SSD (RAID 1)|10 x 3.8TB SSD (RAID 10)|
|BI.S4.H8.3TB RAM + 12TB Persistent Memory|15|31|2x 960GB SSD (RAID 1)|8 x 7.68TB SSD (RAID 10)|

