---
copyright:
  years: 2025
lastupdated: "2025-06-11"
keywords: SAP, SAP SCC Workload Protection, {{site.data.keyword.sysdigsecure_full_notm}}, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA
subcollection: sap
content-type: tutorial
account-plan: paid
completion-time: 10m
---

{{site.data.keyword.attribute-definition-list}}


# Creating a {{site.data.keyword.sysdigsecure_full}} instance
{: #scc-wp-create-instance}
{: toc-content-type="tutorial"}
{: toc-completion-time="10m"}

To leverage the functionality offered by {{site.data.keyword.sysdigsecure_full}}, {{site.data.keyword.sysdigsecure_short}} instance is required required. This tutorial describes how to create one.
{: shortdesc}

## Before you begin
{: #getting-started-prereqs}

- You must have a user ID that is a member or an owner of an {{site.data.keyword.cloud_notm}} account. To get an {{site.data.keyword.cloud_notm}} user ID, go to: [Registration](https://cloud.ibm.com/login){: external}.

- Check the regions where the service is available. [Learn more](/docs/workload-protection?topic=workload-protection-regions). You can complete the steps in any of the supported regions.


## Manage user access
{: #getting-started-step1}
{: step}

Every user that accesses the {{site.data.keyword.sysdigsecure_full_notm}} service in your account must be assigned an access policy with an IAM user role defined. The policy determines the actions that the user can run within the context of the service or instance you selected. The allowable actions are customized and defined as operations that are allowed to be run on the service. The actions are then mapped to IAM user roles. For more information, see [Managing user access in the {{site.data.keyword.cloud_notm}}](/docs/workload-protection?topic=workload-protection-iam).

When a user is granted permissions in the {{site.data.keyword.cloud_notm}} to work with the {{site.data.keyword.sysdigsecure_full_notm}} service, the user is automatically granted a service role. This role determines the actions that a user has permissions to run. For more information, see [Controlling access through IAM](/docs/workload-protection?topic=workload-protection-iam).

Before you can provision an instance, you need to understand:
* The account owner can create, view, and delete an instance of a service in the {{site.data.keyword.cloud_notm}}, and can grant permissions to other users to work with the {{site.data.keyword.sysdigsecure_full_notm}} service.
* You must have permissions to create resources in the *Default* resource group.
* Other {{site.data.keyword.cloud_notm}} users with `administrator` or `editor` permissions can manage the {{site.data.keyword.sysdigsecure_full_notm}} service in the {{site.data.keyword.cloud_notm}}. These users must also have platform permissions to create resources within the context of the resource group where they plan to provision the instance.

To grant a user the administrator role for the service and to manage instances within a resource group in the account, the user must have an IAM policy for the {{site.data.keyword.sysdigsecure_full_notm}} service. For more information, see [Granting permissions to work with the {{site.data.keyword.sysdigsecure_full_notm}} service](/docs/workload-protection?topic=workload-protection-iam).

By default, users are automatically added as members of the **Secure Operations** team that is predefined for each {{site.data.keyword.sysdigsecure_full_notm}} instance. Users have full permissions to see all the data in the web UI.


## Provision an instance
{: #getting-started-step2}
{: step}

To add monitoring features with {{site.data.keyword.sysdigsecure_full_notm}} in the {{site.data.keyword.cloud_notm}}, you must provision an instance of the {{site.data.keyword.sysdigsecure_full_notm}} service.

Instances are provisioned in the context of a resource group. A resource group organizes your services for access control and billing purposes. You can provision the {{site.data.keyword.sysdigsecure_full_notm}} instance in the *default* resource group or in a custom resource group.

To provision an instance through the {{site.data.keyword.cloud_notm}} UI, complete the following steps:

1. Log in to your {{site.data.keyword.cloud_notm}} account.

   Open the [{{site.data.keyword.cloud_notm}} dashboard](https://cloud.ibm.com/login){: external}.

   After you log in with your user ID and password, the {{site.data.keyword.cloud_notm}} UI opens.

1. Click **Catalog**. The list of the services that are available in {{site.data.keyword.cloud_notm}} opens.

1. To filter the list of services that is displayed, select the **Security** category.

1. Click the **{{site.data.keyword.sysdigsecure_full_notm}}** tile.

1. Select the location.

1. Select a service plan.

   For more information about the service plans, see [Service plans](/docs/workload-protection?topic=workload-protection-pricing).

1. Enter a service name.

1. Select a resource group. By default, the **Default** resource group is set.

1. Click **Create** to provision an instance.

The service UI opens.

To provision an instance through the CLI, see [Provisioning a Monitoring instance through the {{site.data.keyword.cloud_notm}} CLI](/docs/workload-protection?topic=workload-protection-provision&interface=cli#provision_cli).
{: tip}


## Access the {{site.data.keyword.sysdigsecure_short}} Dashboard
{: #scc-wp-create-instance-access-dashboard}
{: step}

The last step should have taken you to the Overview page of the {{site.data.keyword.sysdigsecure_short}} you just created. From there, you can just click on "Open dashboard". In case you closed the page and want to get back, follow these steps to access the dashboard:

1. Open the [{{site.data.keyword.cloud_notm}} dashboard](https://cloud.ibm.com/login){: external}.

1.  Click the **Navigation menu** icon ![Navigation menu icon](../../icons/icon_hamburger.svg "Menu") > **Security** > **Resources**

1. From the list, select your {{site.data.keyword.sysdigsecure_short}} instance and click on it.

1. In the instance, click on **Open Dashboard**

## Next step: Configure the agent
{: #scc-wp-create-instance-next-step}
{: step}

You've successfully created a {{site.data.keyword.sysdigsecure_short}} instance. Proceed to [{{site.data.keyword.sysdigsecure_short}} agent setup](/docs/sap?topic=sap-scc-wp-agent-setup) to learn how to install and configure the agent on your {{site.data.keyword.powerSys_notm}} instances and {{site.data.keyword.vsi_is_short}}.
