---
copyright:
  years: 2020, 2025
lastupdated: "2025-11-12"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# VMware SDDC certified profiles for SAP HANA
{: #hana-iaas-offerings-profiles-vmware}

{{site.data.content.vms-deprecated-note}}

## SAP HANA profile naming convention
{: #hana-iaas-vmware-names}

SAP HANA profile names follow a contextual and sequential naming convention. The following table illustrates an example of an SAP HANA certified instance profile:

| Profile name  | Naming convention component | Description                                                                                      |
|---------------|-----------------------------|--------------------------------------------------------------------------------------------------|
| BI.S4.H4.3000 | BI                          | {{site.data.keyword.cloud_notm}} Infrastructure                                                  |
|               | S3                          | Series 3 (processor generation)  \n * S3 is Intel Skylake/Kaby Lake\n * S4 is Intel Cascade Lake |
|               | H                           | HANA-certified server                                                                            |
|               | 4                           | socket server 4                                                                                  |
|               | 3000                        | 3000 GiB RAM                                                                                     |
{: caption="Profile naming for SAP HANA" caption-side="top"}

## Profiles list
{: #hana-iaas-vmware-list}

The following table is an overview of the SAP-certified profiles with either:
- Intel Bare Metal and VMware vSphere (ESXi), manual VMware setup and configuration
- {{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated, automated VMware SDDC setup and configuration


| **Profile**            | **CPU Cores** | **Virtual CPUs** | **Memory (GiB)** | **SAPS (after VMware hypervisor 10%)** | **Workload Type** |
| ---------------------- |---------------| ---------------- | ---------------- | -------------------------------------- | ----------------- |
| BI.S3.H2.192 (VMware)  | 36            | 72               | 192               | 70,965                                | OLAP, OLTP        |
| BI.S3.H2.384 (VMware)  | 36            | 72               | 384               | 71,487                                | OLAP, OLTP        |
| BI.S3.H2.768 (VMware)  | 36            | 72               | 768               | 71,667                                | OLAP, OLTP        |
| BI.S4.H2.192 (VMware)  | 32            | 64               | 192               | 74,223                                | OLAP, OLTP        |
| BI.S4.H2.384 (VMware)  | 32            | 64               | 384               | 76,617                                | OLAP, OLTP        |
| BI.S4.H2.768 (VMware)  | 40            | 80               | 768               | 101,547                               | OLAP, OLTP        |
| BI.S4.H2.1500 (VMware) | 56            | 112              | 1536              | 132,498                               | OLAP, OLTP        |
| BI.S4.H2.3000 (VMware) | 56            | 112              | 3072              | 121,614                               | OLAP, OLTP        |
| BI.S4.H4.3000 (VMware) | 112           | 224              | 3072              | 257,373                               | OLAP, OLTP        |
| BI.S4.H4.6000 (VMware) | 112           | 224              | 6144              | 257,373                               | OLAP, OLTP        |
| BI.S4.H8.6000 (VMware) | 224           | 448              | 6144              | 495,603                               | OLAP, OLTP        |
{: caption="SAP HANA servers" caption-side="top"}
