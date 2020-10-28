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
{:deprecated: .deprecated}

This document is out of date, it is being updated and replaced in November-2020 with new content.
{:deprecated}

# SAP HANA scale-out Reference Architecture
{: #refarch-hana-scaleout}

The {{site.data.keyword.cloud}} architecture provides superior technical capabilities, such as a software definable environment critical to a cloud infrastructure, programmable interfaces, and hundreds of hardware and network configurations. It's designed to deliver a higher level of flexibility (by mixing virtual and dedicated servers to fit a variety of workloads), automation of interfaces, and hybrid deployment options. The {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure offering for SAP HANA and SAP NetWeaver provides you with a best-fit selection. This selection includes bare metal and virtualization-based servers on which the SAP software stack is run.


## Intel Bare Metal
{: #refarch-hana-scaleout-bm}

### Reviewing the network topology and storage layout
{: #reviewing-topology}

Figure 1 shows the network topology required for the {{site.data.keyword.cloud_notm}} Infrastructure as a Service SAP HANA TDI setup.

![Figure 1. {{site.data.keyword.cloud_notm}} Infrastructure as a Service SAP HANA TDI network topology](/images/refarch-sap-bw-hana-scale-out.png "{{site.data.keyword.cloud_notm}} Infrastructure as a Service SAP HANA TDI network topology"){: caption="Figure 1. {{site.data.keyword.cloud_notm}} Infrastructure as a Service SAP HANA TDI network topology" caption-side="bottom"}
