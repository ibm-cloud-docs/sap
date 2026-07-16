---
copyright:
  years: 2020, 2026
lastupdated: "2026-07-10"
keywords: Powervs, Certified Profile, {{site.data.keyword.sap_app_server}}, {{site.data.keyword.powerSys_notm}},
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# {{site.data.keyword.sap_app_server}} certified instances on IBM {{site.data.keyword.powerSys_notm}}
{: #nw-iaas-offerings-profiles-power-vs}

{{site.data.keyword.sap_app_server}} certified instances on {{site.data.keyword.powerSysFull}}s are available with fully adjustable CPU Cores and Memory (RAM GiB). It is permitted to define a custom size of the IBM {{site.data.keyword.powerSys_notm}} instance to use for {{site.data.keyword.sap_app_servers}}, in accordance with existing {{site.data.keyword.sap_app_server}} or SAP AnyDB for IBM best practices and SAP guidance.

## Hardware specifications
{: #supported-hardware}

- For SAP applications the following virtual server configurations are supported. Flexible {{site.data.keyword.sap_app_server}} certified instances on IBM operate in **SMT8 mode**.
- Simultaneous multithreading (SMT) on IBM Power enables multiple independent threads to execute within a single physical processor core. In SMT8 mode, eight parallel threads can run on a single physical processor core. The operating system sees eight logical processors per physical processor core.
- Given {{site.data.keyword.sap_app_server}} instance sizing is flexible, it must follow the standard SAP sizing guidelines by using the following SAPS benchmark calculation:

| **Power System type**                                                                                           | **aSAPS per CPU core (SMT-8)** | **SAPS per CPU Core (SMT-8)** | **SAPS per CPU Core (SMT-4)** |
| ----------------------------------------------------------------------------------------------------------------| -------------------------------| ------------------------------| ----------------------------- |
| [IBM Power System S1122 (9824-22A)](https://www-api.ibm.com/adobe/assets/urn:aaid:aem:80e3064d-99fb-4fad-a967-0e1813a99e8e/original/as/ibm-power-s1122-data-sheet-ph.pdf){: external} |   1,390                        | 7,915                         |  N/A                          |
| [IBM Power System E1150 (9043‑MRU)](https://www-api.ibm.com/adobe/assets/urn:aaid:aem:81c04ae4-9377-44bf-9ab4-19a1f55b9fc6/original/as/ibm-power-e1150-data-sheet-ph.pdf){: external} |   1,336                        | 8,232                         |  N/A                          |
| [IBM Power System E1180 (9080-HEU)](https://www-api.ibm.com/adobe/assets/urn:aaid:aem:5bcdfec0-e2c1-403d-acbb-b7b8a28defae/original/as/ibm-power-e1180-data-sheet-ph.pdf){: external} |   1,340                        | N/A                           |  N/A                          |
| [IBM Power System S1022 (9105-22A)](https://www-api.ibm.com/adobe/assets/urn:aaid:aem:40641b36-9af4-41c5-aba7-351df3900b4e/original/as/IBM_Power_S1022s.pdf){: external} |   N/A                          | 7,600                         |  6,000                        |
| [IBM Power System E1050 (9043‑MRX)](https://www-api.ibm.com/adobe/assets/urn:aaid:aem:54cb9345-c204-466a-ba1c-a0402e4e51e7/original/as/IBM_Power_E1050_Datasheet.pdf){: external} |   1,271                        | 7,600                         |  N/A                          |
| [IBM Power System E1080 (9080‑HEX)](https://www-api.ibm.com/adobe/assets/urn:aaid:aem:54cb9345-c204-466a-ba1c-a0402e4e51e7/original/as/IBM_Power_E1050_Datasheet.pdf){: external} |   1,316                        | 7,600                         |  6,000                        |
| [IBM Power System S922 (9009-22A)](https://www-api.ibm.com/adobe/assets/urn:aaid:aem:25fc68b8-fa7b-4528-b93a-628a8b2653db/original/as/33013033.pdf){: external}  |   N/A                          | 5,570                         |  N/A                          |
| [IBM Power System E980 (9080-M9S)](https://www-api.ibm.com/adobe/assets/urn:aaid:aem:ee9de2eb-755a-4c65-9093-1fe66d45f33a/original/as/09019709.pdf){: external}  |   N/A                          | 6,000                         |  N/A                          |
{: caption="{{site.data.keyword.sap_app_server}} certified system types" caption-side="top"}


For more information, see [SAP Note 2855850 - SAP Applications on IBM {{site.data.keyword.powerSys_notm}}s](https://launchpad.support.sap.com/#/notes/2855850){: external}

## Predefined {{site.data.keyword.sap_app_server}} instances
{: #predefined-profiles}

{{site.data.keyword.sap_app_server}} certified instances on IBM {{site.data.keyword.powerSys_notm}}s are also available as predefined configurations. A predefined {{site.data.keyword.sap_app_server}} certified instance or profile on IBM {{site.data.keyword.powerSys_notm}} defines attributes, such as physical CPU cores and RAM, which determine the size and performance capabilities of the virtual server instance.

{{site.data.keyword.sap_app_server}} certified instances on E1050 are currently not supported.
{: note}

### Profile naming convention
{: #naming-convention}

{{site.data.keyword.sap_app_server}} profile names follow a contextual and sequential naming convention. The following table illustrates an example of an SAP application certified instance profile:

| Profile name | Naming convention component | Description           |
| ------------ | --------------------------- | --------------------- |
| sr3-2x32     | sr3                         | Profile prefix        |
|              | *-*                         | *separator*           |
|              | 2                           | 2 physical CPU Cores  |
|              | x                           | *separator*           |
|              | 32                          | 32 GiB RAM            |
{: caption="Profile naming scheme for {{site.data.keyword.sap_app_servers}}" caption-side="bottom"}

### sr3 – Predefined {{site.data.keyword.sap_app_server}} profiles
{: #sr3-nw-profiles}

The following predefined {{site.data.keyword.sap_app_server}} profiles with prefix **sr3** on IBM {{site.data.keyword.powerSys_notm}} are available.

| **Profile name**    | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** | **SMT Mode** |**SAPS**  |**aSAPS**  |
| ------------------- | ------------- | ---------------- | ---------------- | ------------ |--------- | --------- |
| sr3-1x8             | 1             |      8           |  8               | SMT 8        |  7,900   | 1,300     |
| sr3-1x16            | 1             |      8           |  16              | SMT 8        |  7,900   | 1,300     |
| sr3-2x32            | 2             |      16          |  32              | SMT 8        | 15,800   | 2,600     |
| sr3-3x64            | 3             |      24          |  64              | SMT 8        | 23,700   | 3,900     |
| sr3-4x64            | 4             |      32          |  64              | SMT 8        | 31,600   | 5,200     |
| sr3-6x128           | 6             |      48          |  128             | SMT 8        | 47,400   | 7,800     |
| sr3-8x128           | 8             |      64          |  128             | SMT 8        | 63,200   | 10,400    |
| sr3-12x256          | 12            |      96          |  256             | SMT 8        | 94,800   | 15,600    |
| sr3-16x256          | 16            |      128         |  256             | SMT 8        | 126,400  | 20,800    |
{: class="simple-tab-table"}
{: tab-group="sr3-nw-table"}
{: caption="P11 predefined Instance profiles with sr3 prefix for {{site.data.keyword.sap_app_servers}}" caption-side="bottom"}
{: #sr3-nw-profiles-instance}
{: tab-title="Certified Profiles"}

| Profile\nname          | Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained|
|----------------------- |------------------------ |----------------------|---------------|
|   sr3-1x8              |  1 x 50GB               | Tier 0               |    1,250      |
|   sr3-1x16             |  1 x 50GB               | Tier 0               |    1,250      |
|   sr3-2x32             |  1 x 50GB               | Tier 0               |    1,250      |
|   sr3-3x64             |  1 x 50GB               | Tier 0               |    1,250      |
|   sr3-4x64             |  1 x 50GB               | Tier 0               |    1,250      |
|   sr3-6x128            |  1 x 50GB               | Tier 0               |    1,250      |
|   sr3-8x128            |  1 x 50GB               | Tier 0               |    1,250      |
|   sr3-12x256           |  1 x 50GB               | Tier 0               |    1,250      |
|   sr3-16x256           |  1 x 50GB               | Tier 0               |    1,250      |
{: class="simple-tab-table"}
{: tab-group="sr3-nw-table"}
{: caption="Sample file system configurations for {{site.data.keyword.sap_app_server}} profiles with sr3 prefix" caption-side="bottom"}
{: #sr3-nw-profiles-storage}
{: tab-title="Storage"}

### sr2 – Predefined {{site.data.keyword.sap_app_server}} profiles
{: #sr2-nw-profiles}

The following predefined {{site.data.keyword.sap_app_server}} profiles with prefix **sr2** on IBM {{site.data.keyword.powerSys_notm}} are available.

| **Profile name**    | **CPU cores** | **Virtual CPUs** | **Memory (GiB)** | **SMT Mode** |**SAPS**  |
| ------------------- | ------------- | ---------------- | ---------------- | ------------ |--------- |
| sr2-1x8             | 1             |      8           |  8               | SMT 8        |  7,600   |
| sr2-1x16            | 1             |      8           |  16              | SMT 8        |  7,600   |
| sr2-2x32            | 2             |      16          |  32              | SMT 8        | 15,200   |
| sr2-3x64            | 3             |      24          |  64              | SMT 8        | 22,800   |
| sr2-4x64            | 4             |      32          |  64              | SMT 8        | 30,400   |
| sr2-6x128           | 6             |      48          |  128             | SMT 8        | 45,600   |
| sr2-8x128           | 8             |      64          |  128             | SMT 8        | 60,800   |
| sr2-12x256          | 12            |      96          |  256             | SMT 8        | 91,200   |
| sr2-16x256          | 16            |      128         |  256             | SMT 8        | 121,600  |
{: class="simple-tab-table"}
{: tab-group="sr2-nw-table"}
{: caption="P10 predefined Instance profiles with sr2 prefix for {{site.data.keyword.sap_app_servers}}" caption-side="bottom"}
{: #sr2-nw-profiles-instance}
{: tab-title="Certified Profiles"}

| Profile\nname          | Sample\nstorage config  | Sample\nstorage tier | IOPs\nobtained|
|----------------------- |------------------------ |----------------------|---------------|
|   sr2-1x8              |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-1x16             |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-2x32             |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-3x64             |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-4x64             |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-6x128            |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-8x128            |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-12x256           |  1 x 50GB               | Tier 0               |    1,250      |
|   sr2-16x256           |  1 x 50GB               | Tier 0               |    1,250      |
{: class="simple-tab-table"}
{: tab-group="sr2-nw-table"}
{: caption="Sample file system configurations for {{site.data.keyword.sap_app_server}} profiles with sr2 prefix" caption-side="bottom"}
{: #sr2-nw-profiles-storage}
{: tab-title="Storage"}
