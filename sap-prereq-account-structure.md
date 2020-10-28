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
{:important: .important}

# Setting up your {{site.data.keyword.cloud_notm}} account structure
{: #account-structure}

The {{site.data.keyword.cloud_notm}} account requires a unique log-in ID, which is an {{site.data.keyword.IBM_notm}}id.

An {{site.data.keyword.cloud_notm}} account is set up by using the [{{site.data.keyword.cloud_notm}} console](/docs/sap?topic=overview-ui) where:
- Identity and Access Management (IAM) is used for adding new users to the account and controlling the access privileges (API Keys, Access Groups, Roles, and Authorizations) for those users, for example which users can provision resources
- Billing and Usage is used to monitor and forecast consumption costs, review billing invoices, and control payments (including any discounts or promotions)
- Account resources structure, which is used in tandem with IAM to control how your workloads are organized and run (for example, Resource Groups, custom Dashboards). This includes advanced features that determine how your IBM Cloud account functions such as EU Support, HIPAA Support, private network Cloud services endpoints, and routing (that is, VRF) and other items

Use the steps within the following sections to set up your {{site.data.keyword.cloud_notm}} account structure to establish the best functions for your SAP landscape.
{: shortdesc}


## Resource Groups
{: #resource-groups}

Other than {{site.data.keyword.cloud_notm}} Classic Infrastructure resources, most {{site.data.keyword.cloud_notm}} resources require Resource Groups.

Resource groups are used to organize your account resources for access control, affecting how your workloads are organized and run. {{site.data.keyword.cloud}} provides all users with a default resource group.

Before you consider creating new resource groups, become familiar with the contents of this topic by reading [Best practices for organizing resources in a resource group](/docs/account?topic=account-account_setup) and [What makes a good resource group strategy?](/docs/account?topic=account-account_setup#resource-group-strategy).

For more information about resource groups, see [Managing resource groups](/docs/account?topic=account-rgs).


### Creating Resource Groups
{: #resource-groups-create}

To create new Resource Groups requires few steps:

1. Log in to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com){: external} with your unique credentials.
1. Click **Manage** > **Account** > **Account Resources > Resource Groups**.
1. Click **Create**, enter a unique **Name** for the new Resource Group, and click **Create**.


## Define the Cloud Identity and Access Management (IAM)
{: #iam}

Optional setup of IAM controls.
{: note}

Significant granularity of access control is available for all {{site.data.keyword.cloud_notm}} services for specified Resource Groups through IAM Access policies.

For more information about IAM roles and actions, see [IAM access](/docs/account?topic=account-userroles) with full details on how IAM policies are structured.

An account owner or administrator is assumed to have performed IAM setup for the account before the creation of an SAP landscape, including assignment of all authorizations required to provision infrastructure.

If any actions described in this documentation are being performed by another user who is not the account owner/creator, then you need to check the current IAM authorizations to complete the tasks for deploying and running SAP within {{site.data.keyword.cloud_notm}}.
{: note}


### Checking current IAM authorizations
{: #iam-check}

Managing your {{site.data.keyword.cloud_notm}} environment and preparing it to run SAP workloads includes provisioning compute, storage and network, securing the server, downloading and installing your SAP software and applications, and testing connectivity to your environment by using resources from {{site.data.keyword.ibm_cloud_sap}}.

Confirm with an administrator that you are assigned the permissions that are required to check current all authorizations. For more information about the required roles and permissions, see [Access management in IBM Cloud](/docs/account?topic=account-cloudaccess).


**The following example for VPC Infrastructure, shows how to check the current authorizations:**

1. Select **Manage** > **Access (IAM)**.
2. Click **Users** and select your username. Names are in given name alphabetical order.
3. Click **Access Policies** and click the number next to the **Role** to see the currently assigned roles and the allowed actions.
4. Click **Roles**.
5. Click **'View the roles for'** and select **{{site.data.keyword.vsi_is_short}}**. This selection shows the available Roles and allowed actions for a {{site.data.keyword.vsi_is_short}}. An Administrator can apply these roles to you or alter them to provide authorization for the necessary actions.
