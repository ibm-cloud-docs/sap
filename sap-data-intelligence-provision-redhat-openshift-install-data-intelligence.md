---

copyright:
  years: 2021
lastupdated: "2021-04-09"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP Data intelligence, {{site.data.keyword.cos_full_notm}}, {{site.data.keyword.cos_short}}, {{site.data.keyword.openshiftlong_notm}}, {{site.data.keyword.openshiftshort}}, Red Hat Enterprise Linux, data orchestration, data governance, data integration

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

# Preparing and Installing SAP Data Intelligence
{: #rhos-di-dataintelligence}

In the previous chapter we have created a {{site.data.keyword.openshiftshort}} cluster and prepared the jump host. The next steps cater to the preparation of the  cluster to be ready for the SAP Data Intelligence installation.

The instructions for implementing SAP Data Intelligence on the {{site.data.keyword.openshiftshort}} cluster follow the Red hat article (RHA) [SAP Data Intelligence 3 on OpenShift Container Platform 4](https://access.redhat.com/articles/5100521){: external} starting from [**3.3. OCP Post Installation Steps**](https://access.redhat.com/articles/5100521#ocp-post-installation){: external}, because in this scenario {{site.data.keyword.openshiftshort}} 4.8.x already has been provisioned.


## Setting the `sdi` role for the worker nodes for SAP Data Intelligence
{: #rhos-di-label-nodes}

According to section [3.3.4.1. Label the compute nodes for SAP Data Intelligence](https://access.redhat.com/articles/5100521#ocp-post-label-nodes){: external} in the RHA, set the `sdi` role for the worker nodes that will be considered by Red Hat's SDI observer. See below the parameter `SDI_NODE_SELECTOR` in the SDI observers's installation script.

1. Determine the nodes' names.

    ```
    oc get nodes
    ```
    {: pre}

    Example output:

    ```
    NAME                STATUS   ROLES           AGE   VERSION
    10.**********.177   Ready    master,worker   1d    v1.21.8+ed4d8fd
    10.**********.182   Ready    master,worker   1d    v1.21.8+ed4d8fd
    10.**********.185   Ready    master,worker   1d    v1.21.8+ed4d8fd
    ```
    {: screen}

1. Set the nodes' role.

    ```
    $ oc label node/10.**********.177 node-role.kubernetes.io/sdi=""
    $ oc label node/10.**********.182 node-role.kubernetes.io/sdi=""
    $ oc label node/10.**********.185 node-role.kubernetes.io/sdi=""
    ```

1. Check the nodes.

    ```
    oc get nodes
    ```
    {: pre}

    Example output:

    ```
    NAME                STATUS   ROLES               AGE   VERSION
    10.**********.177   Ready    master,sdi,worker   1d    v1.21.8+ed4d8fd
    10.**********.182   Ready    master,sdi,worker   1d    v1.21.8+ed4d8fd
    10.**********.185   Ready    master,sdi,worker   1d    v1.21.8+ed4d8fd
    ```
    {: screen}

    Note that the `sdi`role has been added.


## Creating the related projects
{: #rhos-di-create-sdi-projects}

For deployment of SAP applications in cloud-based environments, SAP provides the Software Lifecycle Container Bridge 1.0 tool with the Maintenance Planner. In the following, we use the abbreviated tool name “SLC Bridge”. [Find more information on SAP's SLC Bridge here](https://help.sap.com/viewer/a8d90a56d61a49718ebcb5f65014bbe7/3.2.latest/en-US/0986e8da1d8f43379be9c7999f9ff280.html){: external}. Red Hat's SAP Data Intelligence (SDI) Observer, SLC Bridge and SDI runtime components require separate projects/namespaces for the related pods that will be deployed.
{: shortdesc}

In the RHA, the sample project names (i.e. namespaces) are `sdi`, `sdi-observer` and `sap-slcbridge`. Following these naming conventions of the Red Hat    article, create the related projects as follows.

1. create the projects
    
    ```
    $ oc new-project sdi-observer
    $ oc new-project sap-slcbridge
    $ oc new-project sdi
    ```

## Deploying Red Hat's SAP Data Intelligence (SDI) Observer
{: #rhos-di-deploy-sdi-observer}

*SAP Data Intelligence (SDI) Observer* monitors SDI and SLC Bridge namespaces and applies changes to SDI deployments to allow SDI to run on OpenShift.
{: shortdesc}

The projects' namespaces and {{site.data.keyword.registryshort_notm}} names used in the following steps are the same as those used in previous steps.
{: note}

    
1. SDI Observer needs a secret with credentials for registry.redhat.io

   Follow the section [4.2.1. Prerequisites for Connected OpenShift Cluster](https://access.redhat.com/articles/5100521#sdi-observer-prereq-online){: external} in the RHA and save your `rht-registry-secret.yaml` in the ~/sap/install directory. This yaml file will be required to automatically set the respective parameters below.

1. Get information about Red Hat's SDI Observer installation script

   Review the section [4.2.3. Instantiation of Observer's Template](https://access.redhat.com/articles/5100521#sdi-observer-instantiate){: external} in the RHA to confirm the deployment instructions and the source URL are valid.

1. Download the installation script
    ```
    curl -O https://raw.githubusercontent.com/redhat-sap/sap-data-intelligence/master/observer/run-observer-template.sh
    ```
    {: pre}
    
1. Edit the downloaded script file in your favorite editor; especially, mind the following parameters:
    ```
    FLAVOUR=ubi-build
    REDHAT_REGISTRY_SECRET_PATH="$HOME/sap/install/rht-registry-secret.yaml"
    NAMESPACE=sdi-observer
    SDI_NAMESPACE=sdi
    SLCB_NAMESPACE=sap-slcbridge
    SDI_NODE_SELECTOR="node-role.kubernetes.io/sdi="
    ```
    
1. Save the script.
    
1. Deploy the SAP Data Intelligence Observer in the `sdi` namespace - i.e. run the script using bash.

    ```
    bash ./run-observer-template.sh
    ```
    {: pre}
    
    In chapter [4.3 Managing SDI Observer](https://access.redhat.com/articles/5100521#sdi-observer-manage){: external} of the RHA you will learn how you can review and update SDI Observer's current configuration.

1. Get information about Red Hat's SDI node-configurator

The worker nodes need be configured to grant proper execution of SAP Data Intelligence - see also the documentation at [Red Hat's SDI Node Configurator](https://github.com/redhat-sap/sap-data-intelligence/tree/master/node-configurator){: external} on GitHub.
 
1. First set security context constraints to the sdi-node-configurator service account:
    ```
    oc adm policy add-scc-to-user -n sdi-observer privileged -z sdi-node-configurator
    ```
    {: pre}
    
 1. Copy the template from GitHub:
    ```
    curl -O https://raw.githubusercontent.com/redhat-sap/sap-data-intelligence/master/node-configurator/ocp-template.json
    ```
    {: pre}

1. Create the objects directly from the downloaded template
    ```
    oc process NAMESPACE=sdi-observer -f ./ocp-template.json | oc create -f -
    ```
    {: pre}

## Preparing {{site.data.keyword.cos_full_notm}} for SAP Data Intelligence

For production environments, storage for backup&restore as well as Semantic Data Lake (SDL) Connections must be set up. Follow these instructions to set up {{site.data.keyword.cos_full_notm}} and to define the parameters that will be handed over to the installation dialog.
{: shortdesc}

For ad hoc small term test and evaluation environments, you may skip this topic and go directly to the [Installing the Software Lifecycle Container Bridge (SLCB)](/docs/sap?topic=sap-rhos-di-dataintelligence#rhos-di-install-slcb) section.


### Before you begin
{: #rhos-di-before-you-begin-set-up-checkpoint-storage}

Review [Getting started with {{site.data.keyword.cloud_notm}} {{site.data.keyword.cos_short}}](/docs/cloud-object-storage){: external} for general information on {{site.data.keyword.cloud_notm}} {{site.data.keyword.cos_short}}.

### Provisioning {{site.data.keyword.cos_short}}
{: #rhos-di-provision-storage}

Use the steps in [Provision storage](/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-provision){: external} to provision your {{site.data.keyword.cos_short}}.

The service instance name for the following example is `sdi_cos_k8`. Choose a name that fits your needs when creating your service instance.
{: note}

### Creating the bucket and the directory
{: #rhos-di-create-bucket}

1. Use the steps under [Creating some buckets to store your data](/docs/cloud-object-storage?topic=cloud-object-storage-getting-started-cloud-object-storage#gs-create-buckets){: external}.

1. Choose *Regional* as your **level of resiliency** and select the same **Location** where your {{site.data.keyword.openshiftshort}} cluster is deployed. For this example, the location is `eu-de`.

1. Select the **Storage Class** of **Standard** that will meet your performance needs.

    The bucket name in this example is `sdi-cos-bucket`.
    {: note}

1. Create the directory by uploading an empty folder from your desktop to the bucket. In the console, navigate to your bucket. Select **Resource List** > **Storage** > **sdi_cos_k8** > **sdi-cos-bucket** and click **Upload**.
1. Select the empty folder and name it `checkpoints`.

    The first time you upload, you may be required to install the free tool, Aspera Connect. Another option is to create the directory by using an `S3 bucket`compatible tool. Using such tool is not covered in this topic.
    {: note}

#### Creating the service instance and credentials for accessing the bucket
{: #rhos-di-create-serv-inst-cred}

1. Follow the steps under [Service credentials](/docs/services/cloud-object-storage/iam?topic=cloud-object-storage-service-credentials){: external} to create the service credentials for accessing the bucket that is handed over to the SAP Data Intelligence installation script. Make sure that you select the **Writer** role and click **Include HMAC Credential**.

    The credential name in this example is `sdiOScred`. Choose a name that fits your needs when creating your service credentials.
    {: note}

1. After the service credentials have been created, click **View credentials** and note the values of `access_key_id` and `secret_access_key`. You will need them during the SDI installation dialog later. See below for an example.

    ```
    ...
    "cos_hmac_keys": {
      "access_key_id": "383**************************cf3",
      "secret_access_key": "a24******************************************0f9"
    },
    ```

1. Use [Endpoints and storage locations](/docs/cloud-object-storage?topic=cloud-object-storage-endpoints){: external} to find the **S3 Host** that matches the location where your bucket's created. In the example, it's `s3.eu-de.cloud-object-storage.appdomain.cloud`.

    In a production environment, you should use the private endpoint.
    {: note}


## Installing the Software Lifecycle Container Bridge (SLCB)
{: #rhos-di-install-slcb}

Go directly to the [(RHA) 5.1 Install Software Lifecycle Container Bridge (SLCB)](https://access.redhat.com/articles/5100521#sdi-slcb){: external} instructions, and see the next section to learn the specific parameters for IBM Cloud.

You need to copy the downloaded SLCB exectuable (`SLCB01_##-70003322.EXE`) to your working directory `$HOME/sap/install` and rename it to `slcb`. Note that `##` will be the current version number.

### Locating the Installation Dialog parameters for SLCB
{: #rhos-di-find-parameters-slcb}

In order to run SLCB for your SAP DI installation, several parameters need to be supplied. One of these parameters is the *address of the Container Image Repository*. You can take advantage of the {{site.data.keyword.openshiftlong}} {{site.data.keyword.registryshort_notm}}. The address consists of the endpoint URL for the registry and the namespace that you have created during the setup of the jump host, `sap_di_cr` - see: [Creating a new {{site.data.keyword.registryshort_notm}} namespace](/docs/sap?topic=sap-rhos-di-set-up-cluster#rhos-di-create-cr-ns){: external}.
{: note}

1. To find the endpoint URL of the registry that you are currently targeting, run the `ibmcloud cr api` command.

    ```
    ibmcloud cr api
    ```
    {: pre}

    Example output:

    ```
    Registry API endpoint   https://de.icr.io/api
    ```
    {: screen}

1. In this example the domain is `icr.io`.

1. The region code precedes the domain - here it is `de`.

1. Finally, the *address of the Container Image Repository* here is `de.icr.io/sap_di_cr`.

1. Run the SLCB installation process:

    ```
    ./slcb init
    ```
    {: pre}

During installation of the SLCB, you're prompted to enter following parameters:

| Parameter | Value |
| -- | -- |
| Address of the Container Image Repo: | de.icr.io/sap_di_cr |
| S-User Name: | Sxxxxxxxxxx |
| S-User Password: | xxxxxxxx |
| New Technical User - xxxxx-#custnr#: | tUser |
| Path to the "kubeconfig" file: | ~/.kube/config |
| Expert mode: | 2 |
| Kubernetes namespace for the SLC Bridge: | sap-slcbridge |
| name of the admin user for the SLCB Base: | admin |
| password of the administrator user admin: | xxxxxxx **IMPORTANT**: regard the **password constraints** below|
| NodePort: | 2 |
| Proxy Settings: | no |
| noFeedback: | 3 |

**Password constraints**:
* It must at least consist of 8 characters
* **It must contain at least one lower case, one upper case, one numerical and one special character**
* The allowed special characters are . @ # $ % * + _ ? ! -
{: important}

You need to note the hostname and port of the SLCB service by following commands:
* get the fully qualified hostname <HOSTNAME> that you will need later
    ```
    ibmcloud oc cluster get -c rv-sap-di-cl | grep -i subdomain
    ```
    {: pre}

* get the port number >PORT> that you will need later
    ```
    oc get svc -n sap-slcbridge slcbridgebase-service -o jsonpath=$'{.spec.ports[0].nodePort}\n'
    ```
    {: pre}

This URL will be required later to launch the SDI installation - https://HOSTNAME:PORT/docs/index.html.

## SAP Data Intelligence installation via running the SAP Maintenance Planner
{: #rhos-di-run-install-di}

Review SAP documentation about the installation setup - [Install SAP Data Intelligence with SLC Bridge](https://help.sap.com/viewer/a8d90a56d61a49718ebcb5f65014bbe7/3.2.latest/en-US/7e4847e241c340b3a3c50a5db11b46e2.html){: external}.

Before installing SAP Data Intelligence you need to review the Red Hat article and do these steps:
* [(RHA) 5.3. Project setup](https://access.redhat.com/articles/5100521#project-setup){: external}

As described there execute all `oc adm policy add-scc-to-*` commands and then start the SAP Maintenance Planner.

Launch [SAP Maintenance Planner (MP)](https://apps.support.sap.com/sap/support/mp){: external} in a browser and click **Plan a New System**.

1. Click **Plan**
1. Select **CONTAINER BASED**
1. Select **SAP DATA INTELLIGENCE**
1. Select **SAP DATA INTELLIGENCE 3**
1. Select **3.2 11/2021** from the drop-down box
1. Check **DI - Platform full**
1. Click **Confirm Selection** then **Next**
1. Select **Linux on x86_64 64bit**
1. Click **Confirm Selection** then **Next**
1. Click **Execute Plan**
1. Fill HOSTNAME that you found above into entry field **Fully Qualified Domain Name**
1. Fill PORT that you found above into entry field **Port**
1. Click **Next**

Before you click **Deploy** you must enter your previously found URL https://HOSTNAME:PORT/docs/index.html into a browser and login with your SLCB admin user credentials that you have provided during the *./slcb init* installation process.
{: important}

1. After successfull login, click **Deploy** then **Next**

Now, select the link in the tools column which will launch SAP Intelligence installer in a separate browser window. During installation of SAP Data Intelligence, you're prompted to provide the following parameters:

| Parameter | Value |
| -- | -- |
| When the installer is called for the first time it will download SLCB images from SAP and therefore prompt you for your credentials |
| S-User Name: | Sxxxxxxxx |
| S-User Password: | xxxxxxxx |
| Then you will select **Install** and **DI Platform Full** and click **Next** two times |
| Kubernetes Namespace: | sdi |
| Installation Type: | advanced |
| Restore from Backup: | no |
| Address of the Container Image Repository: | de.icr.io/sdi_cr_os |
| Eventually you will need to enter your S-User credentials again |
| S-User Name: | Sxxxxxxxx |
| S-User Password: | xxxxxxxx |
| SAP DI System Tenant Administrator Password: | xxxxxxxx (you have specified this password before)|
| SAP DI Initial Tenant Name: | default |
| SAP DI Initial Tenant Administrator Username: | default-adm |
| Specify a password for "default" user of "default" tenant: | xxxxxxx |
| proxy | no |
| backup | confirm that SAP Note 2918288 is read |
| backup, restore and vora storage: | s3 compatible |
| Access Key: | xxxxxxxxxxxxxxxxxxxxxx (see above)|
| Secret Access Key: | xxxxxxxxxxxxxxxxxxxxxxx (see above)|
| Endpoint: | https://s3.direct.eu-de.cloud-object-storage.appdomain.cloud |
| S3 bucket and directory: | sdi-cos-bucket/checkpoints |
| timeout for checkpoint store: | 180 |
| checkpoint store validation: | yes |
| disable checksum | yes |
| disable certificate validation | yes |
| Backup Schedule (Cron Expression): | 0 0 \* \* \*	(daily at midnight) |
| want to configure storage classes for ReadWriteOnce PersistentVolumes | yes |
| define default storage class | ibmc-block-bronze |
| Default Storage Class: | ibmc-block-bronze |
| System Management Storage Class: | ibmc-block-bronze |
| Dlog Storage Class: | ibmc-block-bronze |
| Disk Storage Class: | ibmc-block-bronze |
| SAP HANA Storage Class: | ibmc-block-bronze |
| SAP Data Intelligence Diagnostics Storage Class: | ibmc-block-bronze |
| different log path: | no |
| Kaniko: | yes |
| different registry: | no |
| load NFS: | no |
| network policies: | no |
| timeout: | 3600 |
| Additional parameters: | -e hana.memoryRequest=7Gi -e storageGateway.replicas=2 -e vsystem.vRep.exportsMask=true |
| Cluster Name:	| sap-di32-cluster |
| S-User Name: | Sxxxxxxxx |
| S-User Password: | xxxxxxxx |
 

The installation and the initialization of all pods may take a while to complete.
{: note}

1. You may want to watch how the pods get initialized and start running

    ```
    watch oc get -n sdi pods -o wide
    ```
    {: pre}


### Final Step
{: #rhos-di-final-step}

[Testing your installation](https://help.sap.com/viewer/a8d90a56d61a49718ebcb5f65014bbe7/3.1.latest/en-US/1551785f3d7e4d37af7fe99185f7acb6.html){: external}.
