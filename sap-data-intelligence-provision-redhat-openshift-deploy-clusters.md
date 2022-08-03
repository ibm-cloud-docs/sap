---

copyright:
  years: 2021
lastupdated: "2021-04-09"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP Data Intelligence, {{site.data.keyword.cos_full_notm}}, {{site.data.keyword.cos_short}}, {{site.data.keyword.openshiftlong_notm}}, {{site.data.keyword.openshiftshort}}, Red Hat Enterprise Linux, data orchestration, data governance, data integration

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
{:important: .important}

# Deploying your OpenShift cluster and jump host
{: #rhos-di-set-up-cluster}

This topic takes you through the steps for creating the {{site.data.keyword.openshiftlong}} cluster and the associated jump host.
{: shortdesc}

## Before you begin
{: #rhos-di-before-you-begin}

When you create the {{site.data.keyword.openshiftshort}} cluster, it is important that the flavor meets SAP's sizing recommendations and your expected workload characteristics, as well as the expected data volumes. The following implementation scenario is based on [Minimum Sizing for SAP Data Intelligence](https://help.sap.com/viewer/835f1e8d0dde4954ba0f451a9d4b5f10/latest/en-US/d771891d749d425ba92603ec9b0084a8.html){: external} and therefore is appropriate for a very basic test environment, only. When planning to setup and deploy a production cluster you must carefully consult the [Sizing Guide for SAP Data Intelligence]( https://help.sap.com/viewer/835f1e8d0dde4954ba0f451a9d4b5f10/latest/en-US/633d429ff69441ae81fe57d912397903.html){: external} and adapt the flavors that will meet your specific needs. Before creating your cluster, please refer to the most recent SAP documentation, like [Installation](https://help.sap.com/viewer/a8d90a56d61a49718ebcb5f65014bbe7/latest/en-US/9f866d8ef9a94c30947f12e73eaf0dd9.html){: external} and [Administration Guide](https://help.sap.com/viewer/b13b5722c8ff4bf9bb097251310031d0/latest/en-US/884ffcd587784ed2a311b2c19feb8410.html){: external}. You may find more related information in [SAP's Community page](https://community.sap.com/topics/data-intelligence){: external}.

Please check that your {{site.data.keyword.cloud_notm}} account is eligible to create clusters. For more information and the steps to set up your account, see [Prepare to create clusters at the account level](/docs/openshift?topic=openshift-clusters#cluster_prepare){: external}.

## Provisioning your OpenShift cluster
{: #rhos-di-provision-clusters}

Use the following steps to create your {{site.data.keyword.openshiftshort}} cluster. Again, the values used in this scenario are suggestions for a test environment. Please, select additional optional values that will fit your specific needs.

Currently, {{site.data.keyword.cloud_notm}} offers several {{site.data.keyword.openshiftshort}} versions, starting from 4.6.x up to 4.10.x.

**Please consider that SAP only supports {{site.data.keyword.openshiftshort}} version 4.8.x for SAP Data Intelligence 3.2**
{: important}

The Information button provides field-specific information on how to use a field.
{: tip}

1. Log in to [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com/kubernetes/clusters?platformType=openshift){: external} and click **Create cluster +**.
1. Select your setup - Leave the value set to **Manual Setup**.
1. Select *Classic* for **Infrastructure**.
1. Leave the value *Default* for **Resource group**.
1. Select the **Geography** and the **Worker zone** based on your geographic location and select *Single zone* for **Availability**.
1. Leave the default **Private VLAN** and **Public VLAN** settings that are based on your previous location selections.

    You can change these values if necessary, but make sure that your cluster, VLAN, and jump host are in the same worker zone, i.e. data center.
    {: note}

1. Check you **Worker pool** and select at least *8 vCPUs, 32 GB RAM* for **Flavor** and *3 Worker nodes per data center* for the test environment.
1. Leave the values set to **Worker pool name**, **Encrypt local disk** and the **Master service endpoint**
1. Review the **Orchestration service**  and {{site.data.keyword.openshiftshort}} version details and select **{{site.data.keyword.openshiftshort}} 4.8.x**.

    Please remember that SAP supports {{site.data.keyword.openshiftshort}} version 4.8.x only!
    {: note}

1. Check your **OCP entitlement** - Leave the value set to *Purchase additional licenses for this worker pool*.
1. Enter a **Cluster name**, for example *sap-di32-cluster*, and optionally **Tags**.
1. Check if you want to connect none, one or more of the available integrations, like **Activity tracking**, **Logging** and **Monitoring**
1. In the **Summary** pane on the left, review the order summary and then click **Create**.

For many more additional information on the fields, see [Creating a classic standard cluster in the console](/docs/openshift?topic=openshift-clusters&interface=ui#clusters_standard){: external}.

## Provisioning your jump host
{: #rhos-di-provision-jump-host}

The jump host's hardware and software requirements are 4 cores, 16 GB RAM, 20 GB disk space, OS: Red Hat 8.x. You can either use a suitable jump server that is connected to the {{site.data.keyword.openshiftshort}} cluster in {{site.data.keyword.cloud_notm}}, or you can use the following steps to create an {{site.data.keyword.virtualmachineslong}} instance to serve as your jump host.

1. Log in to [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com/gen1/infrastructure/devices){: external} and click **Order +**.
1. Select **Virtual Servers for Classic**.
1. Leave the default value *Public* for **Type of virtual server**.
1. Enter a name, for example *jump4sdi*, for **Hostname**. Click **Information** for formatting specifics.
1. Enter a name, for example, *ocpsditest.org* for **Domain**. Domain is the identification string that defines administrative control within the internet. Click **Information** for formatting specifics.
1. Leave the default values *1*, *hourly* and *None* for **Quantity**, **Billing** and **Placement group**.
1. Select the **Location** of the same data center (zone) where you set up your {{site.data.keyword.openshiftshort}} cluster.
1. Click **View all profiles** and select *B1.4x16*, which is the minimum requirement.
1. Provide your SSH key for **SSH Keys** or leave the default *None*. For more information on SSH keys, see [About SSH keys](/docs/ssh-keys?topic=ssh-keys-about-ssh-keys){: external}.
1. Check your **Operating System** - Select *Red Hat* for **Vendor** and *8.x - Minimal (64 bit) - HVM* for **Version**.
1. Leave the default values for all the other entry fields.
1. In the **Summary** pane on the left, check the **Third-Party Service Agreements** and click **Create**.

Your {{site.data.keyword.openshiftshort}} cluster and your virtual server should be available in about 15 minutes.


## Preparing the jump host
{: #rhos-di-prepare-jump-host}

Before setting up the {{site.data.keyword.openshiftlong}} cluster on which SAP Data Intelligence will be deployed, you need to prepare your jump host.
{: shortdesc}

If you have created the jump server following the steps listed above, you first should update the operating system to the latest level, and then restart the virtual server instance.

1. upade the OS

    ```
    sudo dnf update       # press 'y' when prompted
    ```
    {: pre}

1. reboot

    ```
    sudo reboot
    ```
    {: pre}

### Before you begin
{: #rhos-di-prepare-jump-host-before}

It's recommended you become familiar with the [Managing your {{site.data.keyword.cloud_notm}}  accounts](/docs/account?topic=account-accounts){: external}. Your {{site.data.keyword.cloud_notm}} user ID must at least have the cluster Administrator role.

### Install and verify the Command Line Interfaces (CLIs) for both, {{site.data.keyword.cloud_notm}} and {{site.data.keyword.openshiftshort}}
{: #rhos-di-jump-host-clis}

Use the following commands to download and install the CLIs you use to configure the connection between your {{site.data.keyword.openshiftshort}} cluster and your jump host.

1. Login to the jump host and install the {{site.data.keyword.cloud_notm}} CLI `ibmcloud`.

    ```
    curl -fsSL https://clis.cloud.ibm.com/install/linux | sudo sh
    ```
    {: pre}

    For more information on {{site.data.keyword.cloud_notm}} CLI, see [Getting started with the {{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cli-getting-started){: external}.

1. Download and install the {{site.data.keyword.openshiftshort}} 4.8.x CLI oc and the related Kubernetes command line tool kubectl.

    If not yet available, install wget.

    ```
    sudo dnf install wget
    ```
    {: pre}

 
    Download the stable version {{site.data.keyword.openshiftshort}} 4.8.x CLI.

    ```
    sudo wget https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/stable-4.8/openshift-client-linux.tar.gz
    ```
    {: pre}

    Unpack the tar file that contains the {{site.data.keyword.openshiftshort}} client (oc) and the Kubernetes command line tool (kubectl).

    ```
    tar -zxf openshift-client-linux.tar.gz
    ```
    {: pre}

    Set the executable attribute - if necessary.

    ```
    $ chmod +x oc
    $ chmod +x kubectl
    ```

    Move both files to the local executables directory.

    ```
    $ sudo mv oc /usr/local/bin
    $ sudo mv kubectl /usr/local/bin
    ```

1. Verify the installation of the {{site.data.keyword.cloud_notm}} CLI.

    ```
    ibmcloud dev -v
    ```
    {: pre}

    Example output:

    ```
    ibmcloud dev version 2.12.0
    ```
    {: screen}

1. Verify oc.

    ```
    oc version --client
    ```
    {: pre}

    Example output:

    ```
    Client Version: 4.8.39
    ```
    {: screen}

1. Verify kubectl.

    ```
    kubectl version --client
    ```
    {: pre}

    Example output:

    ```
    Client Version: version.Info{Major:"1", Minor:"21", GitVersion:"v0.21.0-beta.1", ...
    ```
    {: screen}

### Install and verify Docker
{: #rhos-di-jump-host-docker}

1. Install Docker CLI emulator powered by podman.

    ```
    sudo dnf install podman-docker       # press 'y' when prompted
    ```
    {: pre}
    
    Docker daemon is no longer available on Red Hat 8. 
    {: note}

1. Verify the Docker CLI emulator.

    ```
    podman version
    ```
    {: pre}

    Example output:

    ```
    Client:       Podman Engine
    Version:      4.0.2
    API Version:  4.0.2
    Go Version:   go1.17.7

    Built:      Tue Apr 19 05:16:32 2022
    OS/Arch:    linux/amd64
    ```
    {: screen}


### Store your login credentials and create a working directory on your jump host
{: #rhos-di-jump-host-login}

1. Log in to {{site.data.keyword.cloud_notm}} for the first time.

    Refer to [*Configure your environment*](/docs/cli?topic=cli-getting-started#step4-configure-idt-env){: external} to learn more about the login command `ibmcloud` and the `--sso` parameter.
    {: note}

    ```
    ibmcloud login [--sso]        # select the appropriate region, e.g. us-south
    ```
    
1. Create the API key file.

    Create an [{{site.data.keyword.cloud_notm}} API key file](/docs/cli?topic=cli-ibmcloud_commands_iam#ibmcloud_iam_api_keys){: external}. The command is:
    
    ```
    ibmcloud iam api-key-create <APIKeyName> -d <description> --file <APIKeyFilename>
    ```
    {: screen}

    The API key file <APIKeyFilename> will be stored in your current directory.
    ```
    $ ibmcloud iam api-key-create sdi-apikey-admin -d "API Key for managing the SAP Data Intelligence cluster" --file APIKey4sdi_file
    ```

1. Logout again after this initial step.

    ```
    ibmcloud logout
    ```
    {: pre}

1. Create a working directory

    Choose a meaningful file path where you will copy the SAP installation files to and where you will launch the installer that will create log files.

    ```
    mkdir -p ~/sap/install
    ```
    {: pre}

## Creating a new {{site.data.keyword.registryshort_notm}} namespace.
{: #rhos-di-create-cr-ns}


1. Log in to {{site.data.keyword.cloud_notm}} using the API key file that you have created during the previous step.

    The command is `ibmcloud login --apikey @<APIKeyFilename>`.

    ```
    ibmcloud login --apikey @~/APIKey4sdi_file
    ```
    {: pre}

1. Install the {{site.data.keyword.openshiftlong}} Container Registry and the container services plug-ins.

    ```
    $ ibmcloud plugin install container-registry
    $ ibmcloud plugin install container-service
    ```

1. For this example, the namespace name is `sap_di_cr`, which is an acronym for SAP Data Intelligence - Container Registry - OpenShift.

    ```
    $ ibmcloud cr login
    
    $ ibmcloud cr namespace-add sap_di_cr
    ```

1. Verify that the namespace is created.

    ```
    ibmcloud cr namespace-list
    ```
    {: pre}


## Accessing the {{site.data.keyword.openshiftshort}} cluster
{: #rhos-di-access-cluster}


After logging in to the jump host you **must complete the following steps** before you can work with your cluster.

1. Determine the cluster ID on which you want to install SAP Data Intelligence.

    ```
    ibmcloud oc cluster ls
    ```
    {: pre}

    ```
    Name                ID                     State    Created        Workers   ...
    sap-di32-cluster    bm****************kg   normal   1 days ago     3         ...
    ...
    ```
    {: screen}

1. Download the Kubernetes configuration files and certificates to connect the jump host to your cluster - copy the cluster name from the previous step.

    ```
    ibmcloud oc cluster config --admin --cluster sap-di32-cluster
    ```
    {: pre}
    
1. Logout of {{site.data.keyword.cloud_notm}}.

    ```
    ibmcloud logout
    ```
    {: pre}



You have created a {{site.data.keyword.openshiftshort}} cluster, prepared the jump host and are now ready to prepare and install SAP Data Intelligence.

### Next Step
{: #rhos-di-next-step}

* Continue with [Preparing and Installing SAP Data Intelligence](/docs/sap?topic=sap-rhos-di-dataintelligence).
