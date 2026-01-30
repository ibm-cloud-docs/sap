---
copyright:
  years: 2020, 2026
lastupdated: "2026-01-29"
keywords: Powervs, Certified Profile, Netweaver, {{site.data.keyword.powerSys_notm}},
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# SAP Application Server certified instances on IBM {{site.data.keyword.powerSys_notm}}
{: #nw-iaas-offerings-profiles-power-vs}

SAP Application Server certified instances on {{site.data.keyword.powerSysFull}}s are available with fully adjustable CPU Cores and Memory (RAM GiB). It is permitted to define a custom size of the IBM {{site.data.keyword.powerSys_notm}} instance to use for SAP Application Servers or SAP NetWeaver, in accordance with existing SAP Application Server, SAP NetWeaver or SAP AnyDB for IBM best practices and guidance from SAP.

## Hardware specifications
{: #supported-hardware}

- For SAP applications the following virtual server configurations are supported. Flexible SAP Application Server certified instances on IBM operate in **SMT8 mode**.
- Simultaneous multithreading (SMT) on IBM Power enables multiple independent threads to execute within a single physical processor core. In SMT8 mode, eight parallel threads can run on a single physical processor core. The operating system sees eight logical processors per physical processor core.
- Given SAP Application Server instance sizing is flexible, it must follow the standard SAP sizing guidelines by using the following SAPS benchmark calculation:

| **Power System type**                                                                                           | **SAPS per CPU Core (using SMT-8)** |
| ----------------------------------------------------------------------------------------------------------------| ----------------------------------- |
| [IBM Power System S1122 (9824-22A)](https://www.ibm.com/downloads/documents/us-en/13774247783d5fe6){: external} | 7,915                               |
| [IBM Power System E1150 (9043‑MRU)](https://www.ibm.com/downloads/documents/us-en/137a1e1e625bad7e){: external} | 8,232                               |
| [IBM Power System S1022 (9105-22A)](https://www.ibm.com/downloads/documents/us-en/107a02e95b48f639){: external} | 7,600                               |
| [IBM Power System E1080 (9080‑HEX)](https://www.ibm.com/downloads/documents/us-en/107a02e95c48f71a){: external} | 7,600                               |
| [IBM Power System S922 (9009-22A)](https://www.ibm.com/downloads/documents/us-en/107a02e959c8f4e8){: external}  | 5,570                               |
| [IBM Power System E980 (9080-M9S)](https://www.ibm.com/downloads/documents/us-en/10a99803f42fd831){: external}  | 6,000                               |
{: caption="SAP NetWeaver certified system types" caption-side="top"}

SAP NetWeaver certified instances on E1050 are currently not supported.
{: note}

For more information, see [SAP Note 2855850 - SAP Applications on IBM {{site.data.keyword.powerSys_notm}}s](https://launchpad.support.sap.com/#/notes/2855850){: external}

## Predefined SAP Application Server instances
{: #predefined-profiles}

SAP Application Server certified instances on IBM {{site.data.keyword.powerSys_notm}}s are also available as predefined configurations. A predefined SAP Application Server certified instance or profile on IBM {{site.data.keyword.powerSys_notm}} defines attributes, such as physical CPU cores and RAM, which determine the size and performance capabilities of the virtual server instance.

### Profile naming convention
{: #naming-convention}

SAP Application Server profile names follow a contextual and sequential naming convention. The following table illustrates an example of an SAP Application certified instance profile:

| Profile name | Naming convention component | Description           |
| ------------ | --------------------------- | --------------------- |
| sr2-2x32     | sr2                         | Profile prefix        |
|              | *-*                         | *separator*           |
|              | 2                           | 2 physical CPU Cores  |
|              | x                           | *separator*           |
|              | 32                          | 32 GiB RAM            |
{: caption="Profile naming scheme for SAP Application Servers" caption-side="bottom"}


### sr2 – Predefined SAP Application Server profiles
{: #sr2-nw-profiles}

The following predefined SAP Application Server profiles with prefix **sr2** on IBM {{site.data.keyword.powerSys_notm}} are available. Profiles with **sr2** prefix are custom profiles that may be deployed through **CLI or API only**.

| **Profile name**    | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** | **SMT Mode** |**SAPS**  |
| ------------------- | ------------- | ---------------- | ---------------- | ------------ |--------- |
| sr2-1x8             | 1             |      8           |  8               | SMT 8        |  7,600   |
| sr2-1x16            | 1             |      8           |  16              | SMT 8        |  7,600   |
| sr2-2x8             | 2             |      16          |  8               | SMT 8        | 15,200   |
| sr2-2x32            | 2             |      16          |  32              | SMT 8        | 15,200   |
| sr2-3x64            | 3             |      24          |  64              | SMT 8        | 22,800   |
| sr2-4x16            | 4             |      32          |  16              | SMT 8        | 30,400   |
| sr2-4x64            | 4             |      32          |  64              | SMT 8        | 30,400   |
| sr2-6x128           | 6             |      48          |  128             | SMT 8        | 45,600   |
| sr2-8x128           | 8             |      64          |  128             | SMT 8        | 60,800   |
| sr2-12x256          | 12            |      96          |  256             | SMT 8        | 91,200   |
| sr2-16x256          | 16            |      128         |  256             | SMT 8        | 121,600  |
{: class="simple-tab-table"}
{: tab-group="sr2-nw-table"}
{: caption="P10 predefined Instance profiles with sr2 prefix for SAP Application Servers" caption-side="bottom"}
{: #sr2-nw-profiles-instance}
{: tab-title="Instance Profile"}

| Profile\nname          | Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained|
|----------------------- |------------------------ |----------------------|---------------|
|   sr2-1x8              |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-1x16             |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-2x8              |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-2x32             |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-3x64             |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-4x16             |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-4x64             |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-6x128            |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-8x128            |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-12x256           |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-16x256           |  1 x 50GB               | Tier 0               |    1,250      |
{: class="simple-tab-table"}
{: tab-group="sr2-nw-table"}
{: caption="Sample file system configurations for SAP Application Server profiles with sr2 prefix" caption-side="bottom"}
{: #sr2-nw-profiles-storage}
{: tab-title="Storage"}
