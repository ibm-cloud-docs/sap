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

# Preparing and Installing SAP Data Hub
{: #rhos-datahub}

## Deploying Red Hat's SAP Data Hub (SDH) Observer
{: #rhos-deploy-sdh-observer}

Deploying the SAP Data Hub (SDH) Observer lets the SAP Data Hub installer and runtime components access images in the {{site.data.keyword.registryfull}} to provide the image pull secret for the project. In the Red Hat article, the project name is 'sdh'.
{: shortdesc}

The project's namespace and {{site.data.keyword.registryshort_notm}} names used in the following steps are the same as those used in previous steps.
{: note}

1. Copy the local pull image secret from the `default` area to the `sdh` project. For more information, see [Copying an existing image pull secret](/docs/openshift?topic=openshift-registry#copy_imagePullSecret){: external}.

    The source secret should match your region, for example, 'de' for Central Europe. In this example, the source secret `default-de-icr-io` is copied to `sdh-de-icr-io`.
    {: note}

    Find your region.

    ```
    ibmcloud cr api
    ```
    {: pre}

    The region code precedes the domain - in this example the domain is `icr.io` and the region code is `de`.    
    ```
    Registry API endpoint   https://de.icr.io/api
    ```
    {: screen}

    The command line is: `oc patch secret --dry-run -o yaml -n default <local_secret>  -p '{"metadata":{"namespace": "<project_name>"}}' | oc -n sdh create -f -`
    ```
    oc patch secret --dry-run -o yaml -n default default-de-icr-io -p '{"metadata": {"namespace": "sdh", "name": "sdh-de-icr-io"}}' | oc -n sdh create -f -
    ```
    {: pre}

1. Link the secret `sdh-de-icr-io` to the default service account’s pods.

    ```
    oc secrets link -n sdh --for=pull default sdh-de-icr-io
    ```
    {: pre}

1. Deploy the SAP Data Hub Observer in the `sdh` namespace.

    Review the [Required Input Parameters](https://access.redhat.com/articles/3630111#deploy-sdh-observer){: external} to confirm the deployment instructions and the source URL are valid.
    {: note}

    ```
    oc process -f https://raw.githubusercontent.com/redhat-sap/sap-datahub/master/sdh-observer.yaml \
    BASE_IMAGE_TAG=v3.11 SDH_NAMESPACE=sdh MAKE_VSYSTEM_IPTABLES_PODS_PRIVILEGED=true NAMESPACE=sdh \
    REGISTRY=de.icr.io/sdh_cr_os | oc create -f -
    ```
    {: pre}

## Preparing {{site.data.keyword.cloud_notm}} {{site.data.keyword.cos_full_notm}} for SAP Vora Checkpoint
{: #rhos-set-up-checkpoint-storage}

For production environments, storage for the SAP Vora Checkpoint must be set up. Follow these instructions to set up {{site.data.keyword.cloud_notm}} {{site.data.keyword.cos_full_notm}} and to define the parameters that will be handed over to the installation dialog.

For test and evaluation environments, you may skip this topic and go directly to the [Manual Installation using an installation script (manual)](https://access.redhat.com/articles/3630111#manual){: external} instructions, and the instructions for [running the installation script](/docs/sap?topic=sap-rhos-datahub) for the {{site.data.keyword.cloud_notm}} specific parameters.

### Before you begin
{: #rhos-before-you-begin-set-up-checkpoint-storage}

Review [Getting started with {{site.data.keyword.cloud_notm}} {{site.data.keyword.cos_short}}](/docs/cloud-object-storage) for general information on {{site.data.keyword.cloud_notm}} {{site.data.keyword.cos_short}}.

### Provisioning {{site.data.keyword.cos_short}}
{: #rhos-provision-storage}

Use the steps in [Provision storage](/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-provision) to provision your {{site.data.keyword.cos_short}}.

The service instance name for the following example is `sdh_cos_k8`. Choose a name that fits your needs when creating your service instance.
{: note}

### Creating the bucket and the directory
{: #rhos-create-bucket}

1. Use the steps under [Creating some buckets to store your data](/docs/cloud-object-storage?topic=cloud-object-storage-getting-started-cloud-object-storage#gs-create-buckets).
1. Choose *Regional* as your **level of resiliency** and select the same **Location** where your {{site.data.keyword.openshiftshort}} cluster is deployed. For this example, the location is `eu-de`.
1. Select the **Storage Class** of **Standard** that will meet your performance needs.

    The bucket name in this example is `sdh-cos-vora-bucket`.
    {: note}

1. Create the directory by uploading an empty folder from your desktop to the bucket. In the console, navigate to your bucket. Select **Resource List** > **Storage** > **sdh_cos_k8** > **sdh-cos-vora-bucket** and click **Upload**.
1. Select the empty folder and name it `checkpoints`.

    The first time you upload, you may be required to install the free tool, Aspera Connect. Another option is to create the directory [by using the `aws` command line tool](https://aws.amazon.com/cli/){: external}. Using this tool is not covered in this topic.
    {: note}

#### Creating the service instance and credentials for accessing the bucket
{: #rhos-create-serv-inst-cred}

1. Follow the steps under [Service credentials](/docs/services/cloud-object-storage/iam?topic=cloud-object-storage-service-credentials) to create the service credentials for accessing the bucket that is handed over to the SAP Data Hub installation script. Make sure that you select the **Writer** role and click **Include HMAC Credential**.

    The credential name in this example is `sdhOScred`. Choose a name that fits your needs when creating your service credentials.
    {: note}

1. After the service credentials have been created, click **View credentials** and note the values of `access_key_id` and `secret_access_key`. See below for an example.

    ```
    ...
    "cos_hmac_keys": {
      "access_key_id": "383**************************cf3",
      "secret_access_key": "a24******************************************0f9"
    },
    ```

### Locating the Installation Dialog parameters
{: #rhos-find-parameters}

During installation of SAP Data Hub, you're prompted to:

    ```
    Enable Vora checkpoint store? (yes/no)
    ```

If you're setting up a test environment, you can select **yes** or **no**. However, for a production environment, select **yes**. You're next asked for the following parameters:

    ```
    Please provide the following parameters for Vora's checkpoint store
    Please enter type of shared storage (oss/s3/wasb/gcs/webhdfs):
    Please enter S3 access key:
    Please enter S3 secret access key:
    Please enter S3 host (empty for default 'https://s3.amazonaws.com'):
    Please enter S3 region you want to connect to (empty for default 'us-east-1'):
    Please enter connection timeout in seconds (empty for default 180):
    Please enter S3 bucket and directory (in the form my-bucket/directory):
    Do you want to validate the checkpoint store? (yes/no)
    ```

1. Select **s3** for **type of shared storage**. You can take advantage of {{site.data.keyword.cos_short}}'s compatibility with the S3 API.
1. Enter the **access_key_id** from the generation of the service credentials for the **S3 access key**.
1. Enter the **secret_access_key** from the generation of the service credentials for the **S3 secret access key**.
1. Use [Endpoints and storage locations](/docs/cloud-object-storage?topic=cloud-object-storage-endpoints) to find the **S3 Host** that matches the location where your bucket's created. In the example, it's `s3.eu-de.cloud-object-storage.appdomain.cloud`. Leave **S3 region** blank. It's taken from the endpoint's URL, for example, `eu-de`.

In a production environment, you should use the private endpoint.
{: note}

1. Leave **connection timeout in second** blank. It will default to 180 seconds.
1. Enter the bucket and directory names you entered in [Creating the bucket and the directory](#rhos-create-bucket) for **S3 bucket and directory**. In the example, it's `sdh-cos-vora-bucket/checkpoints`.
1. Enter **yes** to **validate the checkpoint store**.

## Running the installation shell script
{: #rhos-run-install-sh}

We recommend manually installing the SAP Data Hub.

### Before you begin
{: #rhos-run-install-sh-before}

Follow the steps in the Red Hat article, section [4.5 Manual Installation using an installation script (manual)](https://access.redhat.com/articles/3630111#manual){: external}, to get the SAP Data Hub Foundation image.   Before you can unzip the SAP Data Hub Foundation image you must install `unzip`.

```
yum install unzip
```
{: pre}

The command line parameters provide {{site.data.keyword.cloud_notm}} specific information for the SAP installation script. For the installation script, you need this information from the previous tasks, the:
* default storage provider
* image registry, including the registry namespace and pull secret
* domain to be used for the auto-generated certificates and the enablement of the [Kaniko image builder](https://access.redhat.com/articles/3630111#kaniko){: external}.

 The default domain for {{site.data.keyword.openshiftlong}} clusters on {{site.data.keyword.cloud_notm}} is `<region>.containers.cloud.ibm.com`. You can see your region with the command `oc project`.
 {: note}

For more information about command-line parameters, see [Command-Line Parameters of the SAP Data Hub Installer](https://help.sap.com/viewer/e66c399612e84a83a8abe97c0eeb443a/2.7.latest/en-US/fe5f71216f0e465c929e93ecd48e5a14.html){. external}.

### SAP Data Hub installation
{: #rhos-run-install-sh-manually}

1. Log in to the {{site.data.keyword.cloud}} Container Registry.

    ```
    ibmcloud cr login
    ```
    {: pre}

1. Use the following command using parameters from previous steps. You will be entering your S-UserID and password.

    ```
    ./install.sh --namespace sdh --registry de.icr.io/sdh_cr_os --pv-storage-class ibmc-block-bronze \
    --cert-domain eu-de.containers.cloud.ibm.com --image-pull-secret sdh-de-icr-io --enable-kaniko yes
    ```
    {: pre}

The installation and the initialization of all pods may take a while to complete.
{: note}

## Provide the Modeler's Access Credentials for the {{site.data.keyword.cloud_notm}} Container Registry
{: #rhos-modeler-provide-credentials-for-registry}

After installation has finished and the SAP Data Hub Launchpad is available, log in to SAP Data Hub, and  provide the proper credentials for the Modeler in System Management.

Use the following commands to provide the modeler's access credentials to the {{site.data.keyword.registryfull}}.
{: shortdesc}

1. Retrieve your user's API key that you have created during step 7a in [Install the {{site.data.keyword.cloud_notm}} and OpenShift client Command Line Interfaces (CLI)](/docs/sap?topic=sap-rhos-set-up-cluster#rhos-jump-host).

    ```
    cat myAK4sdh_file
    {
	    "name": "myAK4sdh",
	    "description": "This is your API Key for SAP Data Hub",
	    "apikey": "********************************************",
	    "createdAt": "2019-12-18T10:40+0000",
	    "locked": false,
	    "uuid": "ApiKey-********-****-****-****-************"
    }
    # copy the API key shown after "apikey".
    ```
    {: pre}

2. Follow the instructions in [Provide Access Credentials for a Password Protected Container Registry](https://help.sap.com/viewer/e66c399612e84a83a8abe97c0eeb443a/2.7.latest/en-US/a1cbbc0acc834c0cbbe443f2e0d63ab9.html){: external}. Be sure to use the following parameters, including pasting the copied API key into the **password** field.

    ```
    cat >/tmp/vsystem-registry-secret.txt <<EOF
    username: "iamapikey"
    password: "********************************************"
    EOF
    ```
    {: pre}

### Final Step
{: #rhos-next-steps-provide-credentials}

[Testing your installation](https://help.sap.com/viewer/e66c399612e84a83a8abe97c0eeb443a/2.7.latest/en-US/1551785f3d7e4d37af7fe99185f7acb6.html){: external}.
