---
copyright:
  years: 2020, 2026
lastupdated: "2026-02-27"
keywords: SAP, {{site.data.keyword.ibm_cloud_sap}}, support, support case, premium support, advanced support, basic support, support page, help, SAP ONE Support, AI-based support
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}



# Getting help and support from IBM Cloud or SAP
{: #help-support}

If you experience problems with {{site.data.keyword.cloud_notm}}, there are several ways to identify the cause and resolve the issue.
{: shortdesc}

The support option you choose depends on:
- Your {{site.data.keyword.cloud_notm}} support plan level (Basic, Advanced, and Premium)
- The urgency and severity of the issue.
- Whether the issue is related to the {{site.data.keyword.cloud_notm}} offering or to SAP workloads running on that offering.

Support options include:
- Review the [FAQs](/docs/sap?topic=sap-faq-ibm-cloud-for-sap) in the solution guide documentation
- Ask a question in the [AI assistant](/docs/overview?topic=overview-ask-ai-assistant) from the console or the {{site.data.keyword.cloud_notm}} CLI
- If the issue is related to {{site.data.keyword.cloud_notm}} infrastructure, create an IBM Cloud Support case by using the [{{site.data.keyword.cloud_notm}} Support Center](https://cloud.ibm.com/unifiedsupport/supportcenter){: external} (available only for Advanced and Premium support plans). Issues on infrastructure or performance topics shall be analyzed first by the IBM Cloud support team before you create a case for the operating system or SAP component.
- If the issue is related to a SAP software component, open an SAP support incident, by using the [SAP for Me Get Support](https://me.sap.com/getsupport){: external} portal.

## {{site.data.keyword.cloud_notm}} Support
{: #ibm-cloud}

{{site.data.keyword.cloud_notm}} Support provides assistance for issues and questions related to {{site.data.keyword.cloud_notm}} services. Each IBM Cloud account includes a Basic support plan, with optional upgrades to Advanced or Premium support for faster response times and higher-severity coverage. For more details, see the [Basic, Advanced, and Premium Support plans](/docs/account?topic=account-support-plans).

The Basic plan now provides self-service and AI-driven support only. Basic users cannot open technical support cases or contact support via phone or chat.

You can change your current support plan at any time by contacting a {{site.data.keyword.cloud_notm}} sales expert.

For more information about opening an {{site.data.keyword.cloud_notm}} Support cases, or about support levels and ticket severities, see [{{site.data.keyword.cloud_notm}} Support documentation](/docs/account?topic=account-gettinghelp).

If you need support but cannot sign in to your account, start a chat by visiting the [{{site.data.keyword.cloud_notm}} Support](https://www.ibm.com/products/cloud/support){: external} page and clicking **Let's talk**.
{: tip}

### New support case with {{site.data.keyword.cloud_notm}} Support
{: #ibm-cloud-support-case}

If you need to open a support case, gather all relevant information first so the {{site.data.keyword.IBM_notm}} Support team can analyze your issue quickly.

1. Log in to [IBM Cloud](https://cloud.ibm.com/catalog){: external} and open the **Support Center** from the Help menu.
1. Click **Create a case**.
1. Select the appropriate **Topic** and **Subtopic** for your issue.
1. Enter a clear subject and a detailed description of the problem.
1. (Optional) Attach supporting files.
1. Review your case information and submit it.
1. Check your email for the confirmation and follow the instructions for further communication.


### Requesting support for SAP-certified IBM {{site.data.keyword.powerSys_notm}}s
{: #ibm-power-support-case}

All performance-related issues need to be checked by {{site.data.keyword.IBM_notm}} Power Systems and IBM Cloud support first, to establish whether any infrastructure-related issues exist, before a case of the software stack (SAP Workloads) can be opened.

If the issue is operating system (OS) related, go to the support portal of the distribution (AIX or Linux&reg;) to open a case.

You can check whether the infrastructure is set up correctly by running a python script on Linux&reg;: `python chk_numa_lpm.py`. For more information, see [SAP Note 2923962 - Check SAP HANA NUMA Layout on {{site.data.keyword.IBM_notm}} Power Systems Virtual Servers](https://me.sap.com/notes/2923962){: external}.
{: tip}

### Requesting support for resources in the European Union
{: #ibm-cloud-support-case-eu}

European Union (EU) support is available to customers who choose to enable the EU supported setting. EU Support is provided 24 hours a day and 7 days a week by engineers that are located in Europe.

Global teams provide support at the discretion of the EU support team. Global teams might be contacted, for example, when issues are not resolved by the Advanced Customer Support (ACS) team in the EU, and more expertise is needed.

You can specify that you want EU support for your account if the following criteria are true:

* The EU Supported setting is enabled for your account by the primary user or account owner. For more information, see [Enabling the EU Supported setting](/docs/account?topic=account-eu-supported).
* Your resources are in the appropriate European data center. For more information, see [Data centers](/docs/overview?topic=overview-locations#data-centers).
* You select the EU supported case level when you open the case.

{{site.data.keyword.cloud_notm}} offerings hosted in the Frankfurt location must be supported by a team that is physically located in Europe.
{: note}

Enabling the EU Support setting for your account applies to all future cases that you open for issues on any service or data center that is hosted in the EU region. However, if you add resources outside of an EU location, issues for those resources are not necessarily handled by a support team in Europe. Any cases that are opened before you enable the EU Supported setting are not affected.


## SAP ONE Support
{: #sap-one-support}

You can also continue to create tickets through SAP Support that are related to your {{site.data.keyword.cloud_notm}} IaaS and SAP products. For more information, see [SAP Support](https://support.sap.com/en/index.html){: external} and [SAP Note 2414820 - SAP on IBM Cloud: Support prerequisites](https://me.sap.com/notes/2414820){: external}.

The [SAP for Me](https://me.sap.com){: external} provides access to task-driven support resources from SAP, available with live web chat or incident tickets, and the following features:
- Knowledge Base for SAP Notes
- Incidents for connection with SAP Product Support teams
- Software Downloads
- Systems, Installations and License Keys

[Full information on SAP Support](https://support.sap.com/en/my-support.html){: external} provides additional details, including guidance on how to use the SAP ONE Support Launchpad.


### SAP ONE Support process for IBM Power
{: #sap-one-support-ibm-power}

If the issue is related to IBM Power and SAP, open a case by going to [support.sap.com](https://support.sap.com/en/index.html){: external} and clicking **Report an Incident**.

All performance-related issues must be checked by [{{site.data.keyword.cloud_notm}} Customer Support](/docs/account?topic=account-gettinghelp) first to rule out any infrastructure-related issues before a case on the software stack is opened.

Provide details and run the following commands to attach the output to the case:

* For AIX:
   * `perfsap` on [SAP Note 1170252](https://me.sap.com/notes/1170252){: external}
* For Linux:
   * `sapsysinfo` on [SAP Note 618104](https://me.sap.com/notes/618104){: external}
   * `supportconfig` on [SAP Note 1642802](https://me.sap.com/notes/1642802){: external}
   * and for SAP HANA also use `full-system-info-dump` on [SAP Note 1732157](https://me.sap.com/notes/1732157){: external}
