---
copyright:
  years: 2020
lastupdated: "2020-09-21"
keywords:
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# VMware SDDC certified profiles for SAP NetWeaver
{: #nw-iaas-offerings-profiles-vmware}

## Profiles list
{: #nw-iaas-vmware-list}

The published names are subject to change.
{: note}

The following table is an overview of the SAP-certified profiles with either:
- Intel Bare Metal and VMware vSphere (ESXi), manual VMware setup and configuration
- {{site.data.keyword.cloud_notm}} for VMware Solutions Dedicated, automated VMware SDDC setup and configuration



| **Profile** | **CPU Cores** | **CPU Threads (also known as. vCPU)** | **Memory (RAM GB)** | **SAPS  (after VMware hypervisor 10%)** |
| --- | --- | --- | --- | --- |
| BI.S3.NW192 (VMware) | 36 | 72 | 192 GB | 70,965 |
| BI.S3.NW384 (VMware) | 36 | 72 | 384 GB | 71,487 |
| BI.S3.NW768 (VMware) | 36 | 72 | 768 GB | 71,667 |
| BI.S4.NW192 (VMware) | 32 | 64 | 192 GB | 74,223 |
| BI.S4.NW384 (VMware) | 32 | 64 | 384 GB | 76,617 |
| BI.S4.NW768 (VMware) | 40 | 80 | 768 GB | 101,547 |
| BI.S4.NW1500 (VMware) | 56 | 112 | 1536 GB | 132,498 |
| BI.S4.NW3000 (VMware) | 56 | 112 | 3072 GB | 121,614 |
{: caption="SAP NetWeaver servers" caption-side="top"}


## Understanding Bare Metal profile names
{: #nw-iaas-vmware-names}

The Bare Metal profile names are contextual and sequential, below uses an SAP HANA certified server as an example:

| Profile name | Naming convention component | What it means |
| --- | --- | --- |
| BI.S3.H8401 | BI | {{site.data.keyword.cloud_notm}} Infrastructure |
| | S3 | Series 3 (processor generation)  \n * S3 is Intel Skylake/Kaby Lake\n *S4 is Intel Cascade Lake |
| | H | HANA-certified server |
| | 8 | 8-socket server |
| | 4 | 4 TB RAM |
| | 01 | Revision number (00 is launch, 01 is first revision, and so on) |
| | A or B | Available as appliances with preconfigured built-in disks (A) or as boot servers only (B) |
{: caption="Profile naming for SAP NetWeaver" caption-side="top"}
