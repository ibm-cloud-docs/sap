---

copyright:
  years: 2020, 2025
lastupdated: "2025-02-05"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, support, support case, premium support, advanced support, basic support, support page, help, SAP ONE Support

subcollection: sap

---

{{site.data.keyword.attribute-definition-list}}


# Getting help and support from IBM Cloud or SAP
{: #help-support}

If you experience problems with {{site.data.keyword.cloud_notm}}, you have several options to get help with determining the cause of the problem and finding a solution.

Which support option depends on the level of support (and urgency), and whether the problem is with the Offering or running SAP Workloads using the Offering.

Options include:
- {{site.data.keyword.cloud_notm}} Support Case, using the [{{site.data.keyword.cloud_notm}} Support Center](https://cloud.ibm.com/unifiedsupport/supportcenter){: external}
- SAP support incident, using the [SAP for Me](https://me.sap.com){: external}
- {{site.data.keyword.cloud_notm}} Docs

For previous users of {{site.data.keyword.cloud_notm}} Classic Infrastructure (formerly Softlayer), please be aware these Support Cases were previously termed Support Tickets.
{: note}


## {{site.data.keyword.cloud_notm}} Support
{: #ibm-cloud}

{{site.data.keyword.cloud_notm}} Support handles any support questions and issues that might arise - available through live web chat, phone, and case-based support.

Each {{site.data.keyword.cloud_notm}} account automatically comes with customer support at no cost and covers most cases which are placed each day; this is the Basic level of support.

The types of available and response time of support, depends on the support level of the account. Your support plan also determines the severity level that you can assign to support cases. For more information, see [Basic, Advanced, and Premium Support plans](/docs/account?topic=account-support-plans).

You can change your current support plan at any time by contacting {{site.data.keyword.cloud_notm}} sales expert.

For full information about opening an {{site.data.keyword.cloud_notm}} Support case, or about support levels and ticket severities, see [{{site.data.keyword.cloud_notm}} Support documentation](/docs/account?topic=account-gettinghelp).

If you need support but are unable to log in to your account, start a chat by going to the [{{site.data.keyword.cloud_notm}} Support](https://www.ibm.com/cloud/support){: external} page and clicking **Let's talk**.
{: tip}


### New support case with {{site.data.keyword.cloud_notm}} Support
{: #ibm-cloud-support-case}

If you need to open a support case, collect as much information as possible to help the {{site.data.keyword.cloud_notm}} Support team to analyze, triage and diagnose your problem as quickly as possible.

### Requesting support for SAP-certified IBM Power Virtual Servers
{: #ibm-power-support-case}

All performance-related issues need to be checked by {{site.data.keyword.IBM_notm}} Power Systems and IBM Cloud support first, to establish whether any infrastructure-related issues exist, before a case of the software stack (SAP Workloads) can be opened.

If the issue is operating system (OS) related, go the support portal of the distribution (AIX or Linux&reg;) to open a case.

You can check whether the infrastructure is set up correctly by running a python script on Linux&reg;: `python chk_numa_lpm.py`. For more information, see [SAP Note 2923962 -  - Check SAP HANA NUMA Layout on {{site.data.keyword.IBM_notm}} Power Systems Virtual Servers](https://me.sap.com/notes/2923962){: external}.
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

Enabling the EU Support setting for your account applies to all future cases that you open for issues on any service or data center that is hosted in the EU region. However, if you add resources outside of an EU location, issues for those resources are not necessarily handled by a support team in Europe. Any cases that are opened before you enable the EU Supported setting aren not affected.


## SAP ONE Support
{: #sap-one-support}

You can also continue to create tickets through SAP Support that are related to your {{site.data.keyword.cloud_notm}} IaaS and SAP products. For more information, see [SAP Support](https://support.sap.com/en/index.html){: external} and [SAP Note 2414820 - SAP on IBM Cloud: Support prerequisites](https://me.sap.com/notes/2414820){: external}.

The [SAP for Me](https://me.sap.com){: external} provides access to task-driven support resources from SAP, available with live web chat or incidnt tickets, and the following features:
- Knowledge Base for SAP Notes
- Incidents for connection with SAP Product Support teams
- Software Downloads
- Systems, Installations and License Keys

[Full information on SAP Support](https://support.sap.com/en/my-support.html){: external} provides additional details, including guidance on how to use the SAP ONE Support Launchpad.


### SAP ONE Support process for IBM Power
{: #sap-one-support-ibm-power}

If the issue is related to IBM Power and SAP, open a case by going to [support.sap.com](https://support.sap.com/en/index.html){: external} and click **Report an Incident**.

All performance-related issues must be checked by [{{site.data.keyword.cloud_notm}} Customer Support](/docs/account?topic=account-gettinghelp) first to rule out any infrastructure-related issues before a case on the software stack is opened.

Provide details and run the following commands to attach the output to the case:

* For AIX:
   * `perfsap` on [SAP Note 1170252](https://me.sap.com/notes/1170252){: external}
* For Linux:
   * `sapsysinfo` on [SAP Note 618104](https://me.sap.com/notes/618104){: external}
   * `supportconfig` on [SAP Note 1642802](https://me.sap.com/notes/1642802){: external}
   * and for SAP HANA also use `full-system-info-dump` on [SAP Note 1732157](https://me.sap.com/notes/1732157){: external}


## Stack Overflow
{: #ibm-cloud-stack-overflow}

The Stack Overflow forum provides a wide variety of searchable answers for your {{site.data.keyword.cloud_notm}} questions. If you don't find an existing answer, ask a new question. Go to [Stack Overflow](https://stackoverflow.com/questions/tagged/ibm-cloud){: external}.

{{site.data.keyword.cloud_notm}} development and support teams actively monitor Stack Overflow and follow the questions that are tagged with **ibm-cloud**. When you create a question, add the **ibm-cloud** tag to your question to ensure that it's seen by the {{site.data.keyword.cloud_notm}} development and support teams.
