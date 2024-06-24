---

copyright:
  years: 2020
lastupdated: "2020-12-17"

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

# ABAP SDK for IBM Watson
{: #abap-sdk-watson}

The ABAP SDK for IBM Watson, enables developers to easily call and use IBM Watson Developer Services directly from ABAP to create an Intelligent Enterprise.
{: shortdesc}

ABAP SDK is a community-driven SDK for the ABAP open source community that is written by ABAPers from IBM Cloud, IBM Services, and IBM Systems. The SDK includes tight collaboration with the SAP Cloud Platform ABAP Environment development team to provide optimal usage of IBM Watson for side-by-side extensibility development.

The ABAP SDK includes over 40,000 lines of ABAP code so that any ABAP developer needs to use only 10 - 20 lines of ABAP code to call IBM Watson APIs.


## Summary of ABAP SDK capabilities
{: #abap-sdk-watson-summary}

The ABAP SDK for IBM Watson includes:
- Pre-built Classes for each IBM Watson API; use only 10 - 20 lines to call IBM Watson APIs
- Pre-built Methods and data types
- API responses that are provided in ABAP Structures or Tables
- Packaged using [abapGit (Git client for ABAP)](https://docs.abapgit.org){: external}, for easy use with [APACK (package and dependency manager)](https://docs.abapgit.org/ref-apack.html){: external}
- SAP-standard-generated documentation (abap-docs)


This IBM first-of-a-kind release of ABAP code to open source (licensed under the [Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0){: external}), is designed for and released with variants for _both_:
- SAP NetWeaver Application Server 7.50+
- SAP Cloud Platform ABAP Environment 2002+

The ABAP SDK for IBM Watson is available in two variants:
- [**ABAP SDK for IBM Watson, using SAP NetWeaver**](https://github.com/watson-developer-cloud/abap-sdk-nwas){: external}
- [**ABAP SDK for IBM Watson, using SAP Cloud Platform ABAP Environment**](https://github.com/watson-developer-cloud/abap-sdk-scp){: external}

 

It is tested with:
- **SAP Business Applications**
    - SAP S/4HANA
    - SAP ECC
- **SAP Development Applications**
    - SAP Cloud Platform ABAP Environment
- **SAP Development methods, frameworks, and tools**
    - ABAP RESTful Application Programming Model
    - SAP Fiori UX with SAPUI5 side-by-side extensions


And it adheres to SAP coding standards for application development, such as:
- HTTP(S) / REST calls
- Authentication
- IAM token handling
- JSON <-> DDIC type conversion
- Error handling


## Simple Business Example Use Case
{: #abap-sdk-watson-business-example-use-case}

This example is an end-to-end simplified business example use case for using the ABAP SDK for IBM Watson in multiple ways.

1. **ABAP with SAP Fiori + ABAP SDK for IBM Watson on the SAP Cloud Platform, Mobile Services, and ABAP Environment**
   - Action: Customer uses mobile app to take photo of broken item, identified material by using Visual Recognition
   - For example, send request to business to fulfill:
     - Submitted photo of asset
     - Lookup of material in ERP
     - Enact RFQ or PO

2. **ABAP with SAP Fiori + ABAP SDK for IBM Watson**
   - Action: Visual Recognition to check uploaded file before import of document into ERP
   - For example, check that uploaded image is:
     - A contract document
     - The correct contract required
     - Has a signature in the required position of the form

3. **ABAP with SAP GUI + ABAP SDK for IBM Watson**
   - Action: Visual Recognition and Language Translation of document that is imported into ERP
    - For example, upload contract document in a different language:
     - Convert to English
     - Extract specific data points
     - Triggers integrated business process (Finance, Planning, and other processes) or RPA flow

 
**Resulting in a vendor comparison by global Category Managers which:**
- Reduces low-quality data entry
- Compares Payment Term Objectives
- Investigates payment punctuality of Invoice Payment Days above Term
- Starts execution of automated Next Best Action workflow routing


## Available IBM Watson APIs with the ABAP SDK
{: #abap-sdk-watson-available-apis}

Full [documentation for IBM Watson Developer Services](https://cloud.ibm.com/developer/watson/documentation){: external} is available.

The ABAP SDK for IBM Watson provides for the following IBM Watson Developer Services:
- Compare and Comply
- Discovery
- Language Translator
- Natural Language Classifier
- Natural Language Understanding
- Personality Insights
- Speech to Text
- Text to Speech
- Tone Analyzer
- Visual Recognition
- Watson Assistant


## Getting started with the ABAP SDK for IBM Watson
{: #abap-sdk-watson-getting-started}

The four main steps to getting started are:

1. **Import the ABAP SDK by using abapGit**. Use Online Mode with clone of GitHub Repository or Offline Mode with .zip download from GitHub, and import into your ABAP runtime
2. **Create IBM Cloud account**. Start with the free “Lite” IBM Watson Developer Services on cloud.ibm.com, obtain service instance credentials for use in ABAP SDK
3. **Use Sample Code examples**. To begin innovation initiatives combined with any SAP Application, while you are following SAP’s ABAP coding standards
4. **Begin developing business-led value**. Create your Intelligent Enterprise with SAP, combining SAP business application data with insights from IBM Watson

Full documentation on using the ABAP SDK for IBM Watson is available in the first page (the README.md file) of the Git repositories on GitHub.com:
- [**ABAP SDK for IBM Watson, using SAP NetWeaver**](https://github.com/watson-developer-cloud/abap-sdk-nwas){: external}
- [**ABAP SDK for IBM Watson, using SAP Cloud Platform ABAP Environment**](https://github.com/watson-developer-cloud/abap-sdk-scp){: external}


### Step-by-Step Tutorial
{: #abap-sdk-watson-step-by-step-tutorial}

The following step-by-step tutorial is written by one of the open source ABAP SDK core developers for IBM Watson. The tutorial details how to go from zero to running your own ABAP Program by using the ABAP SDK for IBM Watson:
- [ABAP SDK for IBM Watson: Step-by-Step Tutorial](https://blogs.sap.com/2019/11/08/abap-sdk-for-ibm-watson-step-by-step-tutorial){: external}


### Example code snippets
{: #abap-sdk-watson-example-code-snippets}

Several example code snippets are included with the ABAP SDK for IBM Watson, including:
- Text to Speech Example
- Natural Language Understanding Example
- Personality Insights Example
- Language Translator Example

Each of these example code snippets is included in the Git repository under the #Usage section of the documentation:
- [#Usage section with sample code for the ABAP SDK for IBM Watson, using SAP NetWeaver](https://github.com/watson-developer-cloud/abap-sdk-nwas#usage){: external}
- [#Usage section with sample code for the ABAP SDK for IBM Watson, using SAP Cloud Platform ABAP Environment](https://github.com/watson-developer-cloud/abap-sdk-scp#usage){: external}


### API Reference
{: #abap-sdk-watson-api-reference}

The API Reference is built into the open source Git repository, and is therefore hosted by GitHub Pages:
- [ABAP Client Library for Watson API Reference](https://watson-developer-cloud.github.io/abap-sdk-nwas/){: external}.


## Release and Support
{: #abap-sdk-watson-release-support}

As the ABAP SDK is a community release, it is not updated with the same schedule as IBM-supported SDKs. It is the choice and responsibility of application developers how this Community SDK is used.

The ABAP SDK is a Community SDK for IBM Watson, created by the IBM Watson development community and SAP's ABAP development community - written by ABAPers from IBM Cloud, IBM Services, and IBM Systems.

Therefore, as a community release it is not updated with the same schedule as IBM-supported SDKs, and does not include support by IBM. For more information about IBM-supported SDKs and the update policy, see [Watson SDKs Reference information](/docs/watson?topic=watson-using-sdks).

If you have questions about the IBM Watson services or are having difficulties with using the APIs, ask a question on [Stack Overflow under tag ibm-watson-cognitive](http://stackoverflow.com/questions/ask?tags=ibm-watson-cognitive){: external}.
