---

copyright:
  years: 2024
lastupdated: "2024-08-12"

subcollection: sap

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip} 
{:ui: .ph data-hd-interface="ui"}
{:terraform: .ph data-hd-interface="terraform"}

# Deploying SAP HANA db cross-regions DR automation module on existing {{site.data.keyword.at_full_notm}} VPC with automation
{: #automate-hana-db-crossregion-dr}

As of 28 March 2024, the {{site.data.keyword.at_full_notm}} service is deprecated and will no longer be supported as of 30 March 2025. Customers will need to migrate to {{site.data.keyword.logs_full_notm}} before 30 March 2025. During the migration period, customers can use {{site.data.keyword.at_full_notm}} along with {{site.data.keyword.logs_full_notm}}. Activity tracking events are the same for both services. For information about migrating from {{site.data.keyword.at_full_notm}} to {{site.data.keyword.logs_full_notm}} and running the services in parallel, see [migration planning](/docs/cloud-logs?topic=cloud-logs-migration-intro).
{: important}

Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} VPC infrastructure resources so that you can rapidly build complex cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware by using Intel&reg; Xeon CPUs and additional Intel&reg; technologies.
{: shortdesc}

You have two deployment methods to choose from:

* In CLI, by running the Terraform script on your bastion server.
* In Schematics User Interface, which is accessed from your {{site.data.keyword.cloud_notm}} dashboard menu.

You can store the SAP HANA db backup into the Cloud Object Storage.

## SAP Solution implemented
{: #automate-hana-db-crossregion-dr-solution-implement}

SAP HANA db cross-regions DR automation module will be developed as standalone automation module that will be integrated to any already deployed SAP solution running on HANA database.

SAP HANA Cloud offers options to replicate your SAP HANA Cloud database synchronously within the same availability zone or asynchronously to other availability zones in another region since October 2021.

With these options, you can set up a Highly Available (HA) architecture and/or a Disaster Recovery (DR) architecture in by running HANA DR cross-region automation module on top of an existing SAP product running on HANA db.

## What is created
{: #automate-hana-db-crossregion-dr-create}

The SAP HANA system is deployed on one of the following operating systems:

* SUSE Linux Enterprise Server 15 SP 4 for SAP
* SUSE Linux Enterprise Server 15 SP 3 for SAP
* Red Hat Enterprise Linux 8.6 for SAP
* Red Hat Enterprise Linux 8.4 for SAP

The [{{site.data.keyword.at_full_notm}} Activity Tracker](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-getting-started) service should be used to capture the records of your {{site.data.keyword.cloud_notm}} activities and monitor the activity of your {{site.data.keyword.cloud_notm}} account. You can use this service to investigate abnormal activity, critical actions, and comply with regulatory audit requirements. In addition, you can be alert on the actions as they occur. The events that are collected comply with the Cloud Auditing Data Federation (CADF) standard.

You can deploy an Activity Tracker instance along with the SAP system by using the SAP deployment Automation or if you already have created one, you can specify the Activity Tracker name in the deployment variables. You can set the Activity Tracker plan variable according to your chosen [Service plans](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-service_plan). By default, the Lite (free) plan is selected. For more information on how to provision an Activity Tracker instance, see [here](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-getting-started).

Important:

* Every user who accesses the {{site.data.keyword.cloud_notm}} Activity Tracker service in your account must be assigned an access policy with an IAM user role defined. The policy determines what actions the user can perform within the context of the service or instance you select. The allowable actions are customized and defined as operations that are allowed to be performed on the service. The actions are then mapped to IAM user roles. For more information, see [here](https://cloud.ibm.com/docs/services/activity-tracker?topic=activity-tracker-iam).

* You can provision only one instance of the service per {{site.data.keyword.cloud_notm}} region.

{{site.data.keyword.cloud_notm}} Activity Tracker provides a solution for administrators to capture, store, view, search, and monitor API activity in a single place. It also offers a notification feature to alert you by using any of the supported notification channels.

{{site.data.keyword.cloud_notm}} Activity Tracker collects and stores audit records for API calls made to resources that run in the {{site.data.keyword.cloud_notm}}. You can archive these events on {{site.data.keyword.cloud_notm}} for long-term storage.
{: note}

## Schematics deployment
{: #automate-hana-db-crossregion-dr-schematics}

When you are using the Schematics interface for the deployment, you need to:

* Provide the workspace information.
* Provide the GitHub repository path.
* Update the value of the parameters in the Schematics interface. The parameters are similar to the ones from `input.auto.tfvars` file, which is used in the deployment from CLI.

## Support
{: #automate-hana-db-crossregion-dr-support}

There are no warranties of any kind, and there is no service or technical support available for these materials from {{site.data.keyword.IBM}}. As a recommended practice, review carefully any materials that you download from this site before using them on a live system. 

Though the materials provided herein are not supported by the IBM Service organization, your comments are welcomed by the developers, who reserve the right to revise, readapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Virtual server instance configuration
{: #automate-hana-db-crossregion-dr-config}

This solution is applied on existing VSIs with SAP HANA deployed in an existing VPC and different region then planned DR deployment. See below for more information SAP automated HANA family solutions:

* https://github.com/IBM-Cloud/sap-netweaver-abap-hana
* https://github.com/IBM-Cloud/sap-netweaver-java-hana
* https://github.com/IBM-Cloud/sap-s4hana
* https://github.com/IBM-Cloud/sap-bw4hana
* https://github.com/IBM-Cloud/sap-netweaver-abap-hana
* https://github.com/IBM-Cloud/sap-s4hana-sz-ha

## Before you begin
{: #automate-hana-db-crossregion-dr-before}

Before you use the scripts:

* An SAP HANA productive system that is built on one of the above OS should be deployed in an {{site.data.keyword.at_full_notm}} Gen2 VPC, on a single host different from the planned region of HANA DR inatance. This HANA db DR instance solution is implemented and tested on the following OS images available in {{site.data.keyword.at_full_notm}}:
    * ibm-sles-15-4-amd64-sap-hana-3
    * ibm-sles-15-3-amd64-sap-hana-2 
    * ibm-redhat-8-6-amd64-sap-hana-2 
    * ibm-redhat-8-4-amd64-sap-hana-2
* A bastion server with secure remote SSH access must be deployed in the same region and zone of {{site.data.keyword.at_full_notm}} Gen2 VPC as the SAP HANA system.
* A pair of SSH keys used to run the automation for the backup configuration should be available. The public SSH key should be manually added on SAP HANA VSI in /root/.ssh/authorized_keys and uploaded in {{site.data.keyword.at_full_notm}}.

To provide the access for the required roles to run the "SAP HANA db backup to Cloud Object Storage", go to https://cloud.ibm.com/iam/users and select the USER_ID to run the automation.

* The HANA DB DR SYSTEM user should have the same password for SYSTEMDB and all tenant databases.

## Deploying SAP HANA db cross-regions DR automation module on VPC by using the Schematics user interface
{: #automate-hana-db-crossregion-dr-deploy}
{: ui}

Use these steps to configure the SAP HANA db cross-regions DR automation module by using the Schematics interface. The script takes one hour to complete if they run after a SAP HANA fresh install.

1. From the {{site.data.keyword.cloud_notm}} menu, select **{{site.data.keyword.bpshort}}**.
2. Click **Create workspace**.
3. On the **Specify template** page:
    *   Enter the [URL](https://github.com/IBM-Cloud/sap-hana-backup-cos) for the github repository that contains the {{site.data.keyword.bpshort}} code for this offering.
    *   Select the **Terraform version** that is listed in the [Readme](https://github.com/IBM-Cloud/sap-hana-backup-cos/blob/main/README.md) file.
    *   Click **Next**.
4. On the **Workspace details** page:
    *   Enter a name for the workspace.
    *   Select a **Resource group**.
    *   Select a **Location** for your workspace. The workspace location does not have to match the resource location.
    *   Select **Next**.
5. Select **Create** to create your workspace.
6. On the workspace settings page, in the input variables section, review the default input variables and provide values that match your solution.

    For a more detailed description of each of the parameters, check the GitHub repo [Readme](https://github.com/IBM-Cloud/sap-hana-backup-cos/blob/main/README.md) file, chapter “Input parameter file”. Also, ensure to mark the parameters that contain sensitive information like passwords, API, and ssh private keys as "sensitive". These parameters are marked as “sensitive” in the readme file, under Input parameter file.
    Save each parameter that you modify.

7. On the workspace settings page, click **Generate plan**. Wait for the plan to complete.
8. Click **View log** to review the log files of your Terraform execution plan.
9. Apply your Terraform template by clicking **Apply plan**.
10. Review the log file to ensure that no errors occurred during the provisioning, modification, or deletion process.

## Next steps
{: #automate-hana-db-crossregion-dr-next}
{: ui}

Do not use the {{site.data.keyword.cloud_notm}} dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results.

If the resources created with the SAP deployment automation is removed, the Activity Tracker instance is also removed, if it is provisioned at the same time with the SAP solution (when ATR_PROVISION parameter is set to `true` during the deployment of the SAP solution).

## Related information
{: #automate-hana-db-crossregion-dr-related}

For more information about the Terraform on {{site.data.keyword.cloud_notm}}, see [Getting started with Terraform on {{site.data.keyword.cloud_notm}}](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

For more information about using the Terraform for creating only a VPC for SAP, without the SAP architecture, see [Creating single-tier virtual private cloud for SAP by using Terraform](/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).

SAP One Support Notes that apply to this document:

*	[SAP Note 84555 - Windows Server, Linux&reg;, and UNIX: Certified hardware](https://launchpad.support.sap.com/#/notes/84855)
*	[SAP Note 2927211 - SAP Applications on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
*	[SAP Note 2923773 - Linux&reg; on {{site.data.keyword.cloud_notm}} (IaaS): Adaption of your SAP License](https://launchpad.support.sap.com/#/notes/2923773)
*	[SAP Note 2414097 - SAP Applications on {{site.data.keyword.cloud_notm}} Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)
*	[SAP Note 2369910 - SAP Software on Linux&reg;: General information](https://launchpad.support.sap.com/#/notes/2369910)
*	[SAP Note 171380 - Released IBM hardware (Intel processors) and IBM cloud services offers](https://launchpad.support.sap.com/#/notes/171380)
*	[SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)

This document is referenced by:

*	[SAP Note 2927211 - SAP Applications on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
*	[SAP Note 2588225 - SAP on {{site.data.keyword.cloud_notm}}: Protect against speculative execution vulnerabilities](https://launchpad.support.sap.com/#/notes/2588225)
*	[SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)
*	[SAP Note 2414097 - SAP Applications on {{site.data.keyword.cloud_notm}} Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)
*   [IBM Cloud Activity Tracker](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-getting-started)

This automation is offered at no cost; however, the provisioned infrastructure comes at cost.
{: note}
