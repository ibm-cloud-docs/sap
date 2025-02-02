---

copyright:
  years: 2020
lastupdated: "2020-12-17"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP Data Hub, {{site.data.keyword.cos_full_notm}}, {{site.data.keyword.cos_short}}, {{site.data.keyword.openshiftlong_notm}}, {{site.data.keyword.openshiftshort}}, Red Hat Enterprise Linux, SAP Data Hub on {{site.data.keyword.cloud_notm}}, data orchestration, data governance, data integration

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

# Deploying your OpenShift cluster and jump host
{: #rhos-set-up-cluster}

This topic takes you through the steps of creating the {{site.data.keyword.openshiftlong}} cluster and your jump host.
{: shortdesc}

## Before you begin
{: #rhos-before-you-begin}

When you create the {{site.data.keyword.openshiftshort}} cluster, it is important that the flavor meets SAP's sizing recommendations and your expected workload characeterisitcs, as well as the expected data volumes. The flavor used in this provisioning scenario is appropriate for a test environment. Use this scenario to create your own test environment or adapt the scenario for your needs. Refer to the most recent SAP documentation when creating your cluster. The following implementation scenario is based on [Minimum Sizing for SAP Data Hub 2.7](https://help.sap.com/docs/SAP_DATA_HUB/1f833eab23244ef2ad66fe982dd14873/d771891d749d425ba92603ec9b0084a8.html){: external}. When planning to setup and deploy a production cluster you must consult [Sizing Guide for SAP Data Hub 2.7](https://help.sap.com/docs/SAP_DATA_HUB/1f833eab23244ef2ad66fe982dd14873/d771891d749d425ba92603ec9b0084a8.html){: external}.

Please check that your {{site.data.keyword.cloud_notm}} account is set up to create clusters. For more information and the steps to set up your account, see [Prepare to create clusters at the account level](/docs/openshift?topic=openshift-clusters#cluster_prepare).

## Provisioning your OpenShift cluster
{: #rhos-provision-clusters}

Use the following steps to create your {{site.data.keyword.openshiftshort}} cluster. The values used in this scenario are suggestions for a test environment. Select the optional values that will fit your needs.

The Information button provides field-specific information on how to use a field.
{: tip}

1. Log in to [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com/kubernetes/clusters?platformType=openshift){: external} and click **Create +**.
1. Review the {{site.data.keyword.openshiftshort}} version details, **{{site.data.keyword.openshiftshort}} 3.11.x (stable)**
1. Check your OCP entitlement - Leave the value set to **Purchase additional licenses for this worker pool**
1. Select *Classic* for **Infrastructure**.
1. Select *Single zone* for **Availability**.
1. Select the **Geography** and the **Worker zone** based on your geographic location.
1. Private VLAN and Public VLAN default based on your zone selections. You can change these values if necessary.

    Your clusters, VLAN, and jump host must be in the same worker zone, or data center.
    {: note}

1. Select *8 vCPUs, 32 GB RAM* for **Flavor** and at least *3 Worker nodes per data center* for the test environment.
1. Enter a **Cluster name** and optionally **Tags**.
1. In the Summary pane, review the order summary and then click **Create**.

For additional information on the fields, see [Creating a classic standard cluster in the console](/docs/openshift?topic=openshift-clusters#clusters_ui){: external}.

## Provisioning your jump host
{: #rhos-provision-jump-host}

The instructions for implementing SAP Data Hub on the {{site.data.keyword.openshiftshort}} cluster follow [SAP Data Hub 2 on OpenShift Container Platform 3](https://access.redhat.com/articles/3630111){: external}, which lays out the [jump host's requirements](https://access.redhat.com/articles/3630111#jump-server-requirements){: external}. You can either use a suitable jump server that is connected to the {{site.data.keyword.openshiftshort}} cluster in {{site.data.keyword.cloud_notm}}, or you can use the following steps to create an {{site.data.keyword.virtualmachineslong}} instance to serve as your jump host.

1. Once you've confirmed that the {{site.data.keyword.openshiftshort}} cluster is building, select the **Menu icon ![Menu icon](../icons/icon_hamburger.svg)** > **Classic Infrastructure** > **Device List**.
1. Click **Order devices +** -
1. Select **Virtual Servers for Classic**.
1. Leave the default values for **Type of virtual server**, **Quantity**, and **Billing**.
1. Enter a name, for example *jump4sdh*, for **Hostname**. Click **Information** for formatting specifics.
1. Enter a name, for example, *sdh.com* for **Domain**. Domain is the identification string that defines administrative control within the internet. Click **Information** for formatting specifics.
1. Select your **Placement group**, default is *None*.
1. Select the **Location** of the data center (zone) where you set up your {{site.data.keyword.openshiftshort}} cluster.
1. Click **All profiles** and select *B1.4x16*, which is the minimum requirement.
1. Provide your SSH key for **SSH Keys** or leave the default *None*. For more information on SSH keys, see [About SSH keys](/docs/ssh-keys?topic=ssh-keys-about-ssh-keys){: external}.
1. Select *Red Hat 7xMinimal (64 bit) - HVM* for **Image**.
1. Leave the default values for **Attached storage disks** and **Network interface**.
1. Click **Third-Party Service Agreements** and click **Create**.

Your {{site.data.keyword.openshiftshort}} cluster and your virtual server should be available in about 15 minutes.

## Preparing the jump host
{: #rhos-prepare-jump-host}

Prepare your jump host to connect to the {{site.data.keyword.openshiftlong}} cluster that you created to host SAP Data Hub.
{: shortdesc}

### Before you begin
{: #rhos-prepare-jump-host-before}

It is recommended you become familiar with the [Managing your {{site.data.keyword.cloud_notm}}  accounts](/docs/account?topic=account-accounts){: external}. Your {{site.data.keyword.cloud_notm}} user ID must have the cluster Administrator role.

### Install the {{site.data.keyword.cloud_notm}} and {{site.data.keyword.openshiftshort}} client Command Line Interfaces (CLI)
{: #rhos-jump-host}

Use the following commands to download and install the CLIs you use to configure the connection between your {{site.data.keyword.openshiftshort}} cluster and your jump host.

1. Login to the jump host as `root` and install the {{site.data.keyword.cloud_notm}} CLI `ibmcloud`.

    ```
      curl -fsSL https://clis.cloud.ibm.com/install/linux | sudo sh
    ```
    {: pre}

    For more information on {{site.data.keyword.cloud_notm}} CLI, see [Getting started with the {{site.data.keyword.cloud_notm}} CLI and Developer Tools](/docs/cli?topic=cli-getting-started){: external}.

1. Download and install the {{site.data.keyword.openshiftshort}} 3.11 CLI oc.

    Click [here](https://mirror.openshift.com/pub/openshift-v3/clients/){: external} for the repository of all oc versions. If you are not sure which version to select, navigate to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com/kubernetes/clusters?platformType=openshift){: external} and look at the **Version** details for the {{site.data.keyword.openshiftshort}} cluster you created. For example, 3.11.232, which matches the current version.

    Download the appropriate version.

    ```
    wget https://mirror.openshift.com/pub/openshift-v3/clients/3.11.232/linux/oc.tar.gz
    ```
    {: pre}

    Unpack the {{site.data.keyword.openshiftshort}} client.

    ```
    tar -xf oc.tar.gz
    ```
    {: pre}

    Move it to the local executables directory.

    ```
    mv oc /usr/local/bin
    ```
    {: pre}

1. Download and install kubectl. For {{site.data.keyword.openshiftshort}} 3.11, release v1.11 of kubectl is required.

    Download the appropriate version.

    ```
    curl -LO https://dl.k8s.io/release/v1.11.10/bin/linux/amd64/kubectl
    ```
    {: pre}

    Set the executable attribute.

    ```
    chmod +x ./kubectl
    ```
    {: pre}

    Move it to the local executables directory.

    ```
    mv kubectl /usr/local/bin
    ```
    {: pre}

1. Download a script from [The Kubernetes Package Manager](https://github.com/helm/helm){: external} and execute it with the latest release of helm for your SAP Data Hub (SDH) release. The helm version is v2.11.0 for SAP Data Hub 2.4, 2.5, 2.6 and 2.7.

    ```
    curl --silent https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get | \
    DESIRED_VERSION=v2.11.0 bash
    ```
    {: pre}

1. Download and install docker through the standard Red Hat repositories.

    Enable the appropriate Red Hat repositories.

    ```
    subscription-manager repos --enable=rhel-7-server-rpms \
     --enable=rhel-7-server-extras-rpms \
     --enable=rhel-7-server-optional-rpms
    ```
    {: pre}

    Install docker and some dependent packages.

    ```
    yum install -y docker device-mapper-libs device-mapper-event-libs
    ```
    {: pre}

    Start the docker process.
    ```
    systemctl enable --now docker.service
    ```
    {: pre}

1. Verify the installation of the {{site.data.keyword.cloud_notm}} CLI.

    ```
    ibmcloud dev -v
    ```
    {: pre}

    Example output:

    ```
    ibmcloud dev version 2.4.0
    ```
    {: screen}

1. Verify kubectl.

    ```
    kubectl version
    ```
    {: pre}

    Example output:

    ```
    Client Version: version.Info{Major:"1", Minor:"11", GitVersion:"v1.11.10", GitCommit: ...
    ```
    {: screen}

1. Verify docker.

    ```
    systemctl status docker
    ```
    {: pre}

    Example output:

    ```
    ● docker.service - Docker Application Container Engine
    Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
    Active: active (running) since ...
    ```
    {: screen}

1. Log in to {{site.data.keyword.cloud_notm}}.

    ```
    ibmcloud login [--sso]
    ```
    {: pre}

    a. If you're logging in to {{site.data.keyword.cloud_notm}} for the first time, create an [{{site.data.keyword.cloud_notm}} API key file](/docs/account?topic=account-userapikey&interface=ui#create_user_key).

    The command is `ibmcloud iam api-key-create <APIKeyName> -d <description> --file <APIKeyFilename>`.
    The API key file <APIKeyFilename> will be stored in your current directory.

    ```
    ibmcloud iam api-key-create myAK4sdh -d "This is your API Key for SAP Data Hub" \
    --file myAK4sdh_file
    ```
    {: pre}

    b. For all subsequent logins use the API Key file that you created earlier during step a.
    The command is `ibmcloud login --apikey @<APIKeyFilename>`.

    ```
    ibmcloud login --apikey @myAK4sdh_file
    ```
    {: pre}


### Access the {{site.data.keyword.openshiftshort}} cluster
{: #rhos-access-cluster}

After logging in to the jump host you **must complete the following steps** before you can work with your cluster.

1. Determine the cluster ID on which you want to install SAP Data Hub.

    ```
    ibmcloud oc cluster ls
    ```
    {: pre}

    ```
    Name                     ID                     State    Created        Workers   ...
    RH_OS_DataHub_SAP_Test   bm****************kg   normal   3 months ago   3         ...
    ```
    {: screen}

1. Download the Kubernetes configuration files and certificates to connect to your cluster.

    The command is `ibmcloud oc cluster config --cluster <clusterID> --admin`. Copy the cluster ID from the previous step.

    You can copy the export command from the response of the `ibmcloud oc` cluster config command.
    {: note}

    ```
    ibmcloud oc cluster config --cluster bm****************kg --admin
    ```
    {: pre}

    ```
    export KUBECONFIG=/root/.bluemix/plugins/container-service/clusters/......-RH_OS_DataHub_SAP_Test.yml
    ```
    {: pre}

You have created an {{site.data.keyword.openshiftshort}} cluster and prepared the jump host.

## Choosing the Dynamic Storage Provisioner
{: #rhos-configure-dynamic-storage-provider}

Dynamic volume provisioning allows storage volumes to be created on-demand. {{site.data.keyword.cloud_notm}} provides several [storage classes](/docs/openshift?topic=openshift-storage-plan) that can be used as storage providers.
{: shortdesc}

Use the steps below to define the default {{site.data.keyword.cloud_notm}} storage class.

1. Select the appropriate storage class that will fit your needs.

    For SAP Data Hub the `ibmc-block-bronze` storage class is recommended.
    {: note}

1. Remove the current default storage class:

    ```
    oc annotate sc --all storageclass.beta.kubernetes.io/is-default-class- \
    storageclass.kubernetes.io/is-default-class-
    ```
    {: pre}

1. Run the following command to define `ibmc-block-bronze` as the default storage class.

    ```
    oc annotate storageclass ibmc-block-bronze storageclass.kubernetes.io/is-default-class=true
    ```
    {: pre}

The SAP Data Hub installation command lets you provide different storage classes for the different SAP Data Hub storage types.
{: note}

## Setting up the {{site.data.keyword.cloud_notm}} Container Registry
{: #rhos-set-up-external-image-registry}

In [Install the {{site.data.keyword.cloud_notm}} and OpenShift client Command Line Interfaces (CLI)](/docs/sap?topic=sap-rhos-set-up-cluster#rhos-jump-host), you installed the {{site.data.keyword.cloud_notm}} CLI, which installed the CLI plug-ins for {{site.data.keyword.containerlong}} and {{site.data.keyword.registryfull}}. Next, you'll set up the external image registry.
{: shortdesc}

Use the following commands to set up the external image registry.

1. Log in to {{site.data.keyword.cloud_notm}} with your API key file that you have generated in step 7a in the section [Install the {{site.data.keyword.cloud_notm}} and OpenShift client Command Line Interfaces (CLI)](/docs/sap?topic=sap-rhos-set-up-cluster#rhos-jump-host).

    ```
    ibmcloud login --apikey @myAK4sdh_file
    ```
    {: pre}

1. Create a new {{site.data.keyword.registryshort_notm}} namespace, `ibmcloud cr namespace-add <my_namespace>`. For this example, the namespace name is `sdh_cr_os`, which is an acronym for SAP Data Hub - Container Registry - OpenShift.

    ```
    ibmcloud cr namespace-add sdh_cr_os
    ```
    {: pre}

1. Verify that the namespace is created.

    ```
    ibmcloud cr namespace-list
    ```
    {: pre}

### Next Steps
{: #rhos-next-steps-external-image-registry}

Complete the setup following Red Hat instructions and then continue to deployment:

* Go to the Red Hat article and do these steps:
    * [RHA 3.4.3.2.4. Allow administrator to manage SDH resources](https://access.redhat.com/articles/3630111#admin-can-manage-sdh){: external}
    * [RHA 3.4.3.2.5. Create privileged tiller service account](https://access.redhat.com/articles/3630111#create-privileged-tiller){: external}
    * [RHA 3.4.3.2.6. Initialize helm](https://access.redhat.com/articles/3630111#helm-init){: external}
    * [RHA 3.4.3.2.7. Create sdh project](https://access.redhat.com/articles/3630111#create-sdh-project){: external}

    * **Skip** step [RHA 3.4.3.2.8. Granting privileges to sdh admin](https://access.redhat.com/articles/3630111#sdhadmin-privileged){: external}.

* Continue with [Deploying Red Hat's SAP Data Hub (SDH) Observer](/docs/sap?topic=sap-rhos-datahub#rhos-deploy-sdh-observer).
