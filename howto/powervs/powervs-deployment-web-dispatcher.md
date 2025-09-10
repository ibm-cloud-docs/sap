---
copyright:
  years: 2025
lastupdated: "2025-09-10"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP Web Dispatcher, {{site.data.keyword.cloud_notm}} Application Load Balancer, {{site.data.keyword.alb_full}}
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Deploying SAP Web Dispatcher and Application Load Balancer
{: #sap-web-dispatcher-and-alb-setup}

The following instructions explain how to deploy SAP Web Dispatcher and Application Load Balancer for SAP solutions on {{site.data.keyword.powerSysFull}}.
{: shortdesc}

The architecture diagram illustrates the deployment locations of the SAP Web Dispatcher and the Application Load Balancer.

It also shows the SSL connections between components:
1. Between a web browser and an IBM Cloud Application Load Balancer
1. Between the Application Load Balancer and SAP Web Dispatchers
1. Between the SAP Web Dispatchers and SAP application server

![{{site.data.keyword.powerSys_notm}} infrastructure for SAP Web Dispatcher and Application Load Balancer](../../images/powervs-deployment-swebdispatcher-and-alb.svg){: caption="{{site.data.keyword.powerSys_notm}} infrastructure for SAP Web Dispatcher and Application Load Balancer" caption-side="bottom"}

To deploy an SAP Web Dispatcher and an {{site.data.keyword.BluSoftlayer_notm}} Application Load Balancer, you need to set up an infrastructure environment, including an additional Virtual Server Instance (VSI) dedicated to SAP Web Dispatcher, download and install Software Provisioning Manager (SWPM). A detailed, step-by-step guidance is provided in the sections below.

## Preparing the environment for SAP Web Dispatcher installation
{: #sap-web-dispatcher-infrastructure-setup}

### Prerequisites
{: #sap-web-dispatcher-infra-setup-prerequisites}

1. Before you start an SAP Web Dispatcher installation, ensure that you deploy a *VPC landing zone architecture and {{site.data.keyword.powerSys_notm}} with SAP landscape*.
   The [Overview of {{site.data.keyword.powerSys_notm}} with VPC landing zone deployable architectures](/docs/powervs-vpc?topic=powervs-vpc-automation-solution-overview) describes the available infrastructure variations for SAP workloads.
   The chapter [Deploying SAP applications on {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-powervs-set-up-power-instances) explains how to set up and configure {{site.data.keyword.powerSys_notm}} instances for SAP software.
   After the SAP software installation, verify that all SAP instances are active and running.
1. Install and configure a client-to-site VPN (Virtual Private Network) server and a VPN client on your local host.
   The chapter [Connect that uses a client-to-site VPN](/docs/powervs-vpc?topic=powervs-vpc-solution-connect-client-vpn) provides step-by-step instructions on how to set it up.
   A VPN connection is required to run *Software Provisioning Manager* in a browser on your local host.
   Alternatively, you can use a remote desktop to connect to the SAP Web Dispatcher VSI.
1. Identify the HTTPS port numbers that are used by the deployed SAP applications.
   These port numbers are required for configuring SAP Web Dispatcher.

### Deploying and configuring the VSI
{: #sap-web-dispatcher-config-vsi}

Follow these steps to set up the Virtual Server Instance (VSI) for SAP Web Dispatcher.

1. Create an edge VSI to host SAP Web Dispatcher.
   Refer to the preceding architecture diagram to identify which VPC ({{site.data.keyword.vpc_full}}) is used.
   [Creating virtual server instances](/docs/vpc?topic=vpc-creating-virtual-servers&interface=ui) provides guidance on how to proceed.

   Make sure that the edge VSI hostname has no more than 13 characters.
   {: note}

1. Log in to the VSI that you created for SAP Web Dispatcher and verify that the required repositories for installation are enabled.
   Refer to [Enable the Required Repositories](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html-single/rhel_for_sap_subscriptions_and_repositories/index#asmb_enable_repo_rhel-for-sap-subscriptions-and-repositories-9){: external} for detailed instructions.

   To check the installed repositories, use the following command:

   ```sh
   dnf repolist
   ```
   {: pre}

   The following repositories are required.

   ```sh
   rhel-9-for-x86_64-baseos-e4s-rpms
   rhel-9-for-x86_64-appstream-e4s-rpms
   rhel-9-for-x86_64-sap-solutions-e4s-rpms
   rhel-9-for-x86_64-sap-netweaver-e4s-rpms
   ```
   {: screen}

   If these repositories are missing, use the following command to enable them.

   ```sh
   subscription-manager repos \
   --disable="*" \
   --enable="rhel-9-for-$(uname -m)-baseos-e4s-rpms" \
   --enable="rhel-9-for-$(uname -m)-appstream-e4s-rpms" \
   --enable="rhel-9-for-$(uname -m)-sap-solutions-e4s-rpms" \
   --enable="rhel-9-for-$(uname -m)-sap-netweaver-e4s-rpms"
   ```
   {: pre}

1. Use the following command to install the RHEL system roles.

   ```sh
   dnf install rhel-system-roles-sap
   ```
   {: pre}

1. Ensure that at least 20480 MB of swap space is configured on the local system.
   If swap space is not available, create it as described in [Building a custom OS image](/docs/sap?topic=sap-custom-os-image-build-rhel-overview#custom-os-image-build-process).

1. Create an Ansible playbook `sap-netweaver.yml` with the following content in the `/root` directory:

   ```yaml
   - hosts: localhost
     connection: local
     roles:
       - sap_general_preconfigure
       - sap_netweaver_preconfigure
   ```
   {: codeblock}

1. Configure the SAP domain hostname.
   Alternatively, you can set the domain hostname during execution of the `sap-netweaver.yml` pre-configuration playbook, which is required before you start the SAP Web Dispatcher installation.
1. Use the following command to run the `sap-netweaver.yml` Ansible playbook.

   ```sh
   ansible-playbook sap-netweaver.yml --extra-vars "sap_domain=<your_host_name>"
   ```
   {: pre}

1. Download the current *SAR* packages from the [SAP Software Download Center](https://me.sap.com/softwarecenter){: external}.
   The required components include:
   - Software Provisioning Manager
   - SAP Web Dispatcher
   - SAP host agent
   - SAPCAR (which is used to unpack the installer packages).

1. Configure a user account and a password that are used during the SAP Web Dispatcher installation.

### Deploying an {{site.data.keyword.alb_full}}
{: #sap-web-dispatcher-deploying-application-load-balancer}

Create an {{site.data.keyword.BluSoftlayer_notm}} {{site.data.keyword.alb_full}} (ALB) service.
Refer to [Creating an {{site.data.keyword.alb_full}}](/docs/vpc?topic=vpc-load-balancers&interface=ui) for detailed instructions.
The documentation [About application load balancers](/docs/vpc?topic=vpc-load-balancers-about&interface=ui#types-load-balancer) describes the available ALB types.
Choose the appropriate ALB type for your deployment, and configure the ALB subnet and security groups according to the preceding architecture diagram.

## Installing an SAP Web Dispatcher with SWPM 2.0
{: #sap-web-dispatcher-swpm-installer}

The *Software Provisioning Manager* is required to install SAP Web Dispatcher.
Refer to the [Installation Guides-SAP Web Dispatcher-Software Provisioning Manager 2.0](https://help.sap.com/docs/SOFTWARE_PROVISIONING_MANAGER/30839dda13b2485889466316ce5b39e9/5290d46f1d6646d6aba92b595921945d.html?locale=en-US){: external} on the SAP Help Portal.
For detailed information on architecture, functions, administration, configuration, and error handling, consult the
[SAP Web Dispatcher](https://help.sap.com/docs/ABAP_PLATFORM_NEW/683d6a1797a34730a6e005d1e8de6f22/488fe37933114e6fe10000000a421937.html?locale=en-US) documentation.

The following guidance provides basic information on how to install and configure SAP Web Dispatcher on {{site.data.keyword.BluSoftlayer_notm}}.
It does not include in-depth guidance on SAP software product installations.
For product-specific user guides, refer to the [SAP Help Portal](https://help.sap.com/docs/){: external} and the [SAP Software Download Center](https://me.sap.com/softwarecenter){: external}.
{: note}

Prepare the necessary SAP system parameter that is required for SAP Web Dispatcher installation, such as:

- SAP System ID and *sapmnt* directory.
- Message server host and its HTTP port.
- SAP back-end System ID.

The following steps describe how to install SAP Web Dispatcher by using *Software Provisioning Manager (SPWM) 2.0*:

1. Rename the *SAPCAR* executable file to `SAPCAR`.
1. Enable the executable flag for the `SAPCAR` file:

   ```sh
   chmod +x SAPCAR
   ```
   {: pre}

1. Use this command to unpack the *SWPM* installer in an empty, writable directory.

   ```sh
   ./SAPCAR -xvf  SWPM20SP20_4-XXXXXXX.SAR
   ```
   {: pre}

1. Run the *SWPM* installer file `sapinst` from the directory where it was extracted.

   ```sh
   ./sapinst
   ```
   {: pre}

   The Software Provisioning Manager verifies the integrity of all SAP-provided software.
   Ensure that the certificate revocation list (CRL) file `crlbag.p7s` is present in the `/root/.sapinst/` directory.
   If the CRL is not downloaded automatically, you can manually download it from this [SAP server link](https://tcs.mysap.com/crl/crlbag.p7s){: external}.
   {: note}

   To access the *SWPM* installer GUI, ensure that your local workstation is connected to the previously deployed VPN server and that the VPN client is active.
   Then, open a web browser on your local workstation and modify the URL generated by `sapinst` as follows:

   ```sh
   https://<VSI_HOST_IP>:4237/sapinst/docs/index.html
   ```
   {: screen}

   `VSI_HOST_IP>` is the IP address of the host where you started the *SWPM* installer.

    Sample URL:

   ```sh
   https://10.30.50.10:4237/sapinst/docs/index.html
   ```
   {: pre}

1. In the *SWPM* GUI, select the **SAP Web Dispatcher (Unicode)** option.
1. Continue the installation and provide the previously prepared SAP system parameters as input.
1. After the installation completes successfully, verify the setup by logging in as `webadm` at the following URL.
   Use the SAP Web Dispatcher master password, which was configured during installation.

   ```sh
   https://<VSI_HOST_IP>:44300/sap/wdisp/admin/public/default.html
   ```
   {: pre}


## Configuring the SAP Web Dispatcher
{: #sap-web-dispatcher-configuration-front-backend}

The [Parameterization of SAP Web Dispatcher](https://help.sap.com/docs/ABAP_PLATFORM_NEW/683d6a1797a34730a6e005d1e8de6f22/48957c3d94cc73eae10000000a42189b.html?locale=en-US){: external} chapter describes SAP Web Dispatcher profile parameters and how to modify them.
Detailed parameter descriptions are available in the [SAP Web Dispatcher: Parameter References](https://help.sap.com/docs/ABAP_PLATFORM_NEW/683d6a1797a34730a6e005d1e8de6f22/48877df50e3f2722e10000000a42189d.html?locale=en-US){: external} documentation.

You can use several methods to change parameters.
One method is to edit the SAP Web Dispatcher instance profile and restart the system to apply the changes.

Another method is to use the browser-based Web Administration Interface (Admin UI), which provides a graphical user interface (GUI).
For more information on using the Web Administration Interface, see [Using the Web Administration Interface in the Web Dispatcher](https://help.sap.com/docs/ABAP_PLATFORM_NEW/683d6a1797a34730a6e005d1e8de6f22/4880c48a109a1b5ae10000000a42189c.html?locale=en-US){: external}.
To change parameters, go to **Core System > Parameters > Edit Parameters**.

### Configuring the SAP Web Dispatcher for back-end systems
{: #sap-web-dispatcher-configuration-for-back-end}

The [Configuration of SAP Web Dispatcher for Back-End Systems](https://help.sap.com/docs/ABAP_PLATFORM_NEW/683d6a1797a34730a6e005d1e8de6f22/04c5c0060f54456585952fd41db449ac.html?locale=en-US&version=202310.003){: external} documentation provides instructions for configuring the SAP Web Dispatcher for back-end systems.

To enable secure HTTPS communication between SAP Web Dispatcher and the {{site.data.keyword.BluSoftlayer_notm}} Application Load Balancer, configure the SSL parameters.

### Configuring SSL for SAP Web Dispatcher
{: #sap-web-dispatcher-configuration-ssl}

The [Configure SAP Web Dispatcher to Support SSL](https://help.sap.com/docs/ABAP_PLATFORM_NEW/683d6a1797a34730a6e005d1e8de6f22/493db10a19341067e10000000a42189c.html?locale=en-US){: external} documentation describes the steps that are required to configure SSL.
To complete this configuration, obtain a trusted server certificate that is signed by a certificate authority (CA).
The certificate must be in PEM format and include the private key.

For more information, see [Set up a Valid Server Certificate in SAP Web Dispatcher](https://help.sap.com/docs/ABAP_PLATFORM_NEW/683d6a1797a34730a6e005d1e8de6f22/c6b6d472e32d4b61abfc8ec3ef9f2676.html?locale=en-US){: external}.

The [Managing PSE files at the SAP Web Dispatcher](https://help.sap.com/docs/SUPPORT_CONTENT/si/3362959621.html?locale=en-US){: external} documentation provides a step-by-step guide for using the Web Administration page.
It describes how to configure and manage Personal Security Environment (PSE) files, which are required for HTTPS connections.

### Verifying the configuration
{: #sap-web-dispatcher-configuration-verification}

After you complete the configuration for back-end systems and set up HTTPS communication for SAP Web Dispatcher, verify that the system is functioning correctly.
Use the Web Administration Interface and select options from the left side of the page.
The [Installation of SAP Web Dispatcher](https://help.sap.com/docs/ABAP_PLATFORM_NEW/683d6a1797a34730a6e005d1e8de6f22/487f579f7df935e1e10000000a42189c.html?locale=en-US){: external} documentation provides detailed information about the area menu and submenus.

To verify that HTTPS is working, access the SAP Web Dispatcher by using a URL that begins with `https://`.
The browser displays a secure connection indicator.

## Configuring the {{site.data.keyword.cloud_notm}} Application Load Balancer service
{: #sap-web-dispatcher-appl-load-balancer-service}

### Configuring the ALB front-end listeners and back-end pools
{: #sap-web-dispatcher-configure-front-end-back-end-alb}

The previously created {{site.data.keyword.BluSoftlayer_notm}} {{site.data.keyword.alb_full}} is positioned in front of the deployed SAP Web Dispatcher instances to manage incoming and outgoing traffic.

The following simplified architecture diagram shows the placement of the ALB and helps identify front-end listeners and back-end pools:

![SAP Web Dispatcher and Application Load Balancer](../../images/powervs-deployment-alb-swebdisp-simple-diagram.svg){: caption="SAP Web Dispatcher and Application Load Balancer" caption-side="bottom"}

To configure the ALB, set up front-end listeners and back-end pools.
See Front-end listeners and back-end pools for detailed configuration guidelines.

See [Front-end listeners and back-end pools](/docs/vpc?topic=vpc-load-balancers-about&interface=ui#front-end-listeners-and-back-end-pools) for detailed configuration guidelines.

The [Creating an application load balancer](/docs/vpc?topic=vpc-load-balancers&interface=ui) documentation provides a step-by-step guide for creating and configuring a back-end pool and a front-end listener.


The following table shows a sample configuration:

| ALB Setting                        | Value/Parameter |
| ---------------------------------- | --------------- |
| ALB type                           | Public |
| Listener Protocol                  | TCP |
| Port                               | 44300 |
| Attached subnets                   | Edge VPC with IP range of 10.30.50.0/24 |
| Back-end pool servers              | VSIs where SAP Web Dispatcher is deployed and running |
| Back-end pool protocol             | TCP |
| Back-end pool session stickiness   | Source IP |
| Back-end pool method               | The method depends on the number of deployed SAP Web Dispatcher instances. See [Load-balancing methods](/docs/vpc?topic=vpc-load-balancers-about&interface=ui#load-balancing-methods) for details. |
| Front-end listener protocol        | TCP |
| Front-end listener port            | 44300 |
| Front-end back-end pool            | VSIs where SAP Web Dispatcher is deployed and running |
| Front-end Timeout (sec) (optional) | 50 |
{: caption="Application Load Balancer sample configuration" caption-side="bottom"}

### Configuring end-to-end SSL encryption for ALB
{: #sap-web-dispatcher-configure-ssl-for-alb}

To configure end-to-end SSL encryption, follow the steps in [End-to-end SSL encryption](/docs/vpc?topic=vpc-load-balancers-about&interface=ui#end-to-end-ssl-encryption).

To verify that the {{site.data.keyword.alb_full}} configuration is working correctly, check the health status of the ALB service.
A green status indicates that the service is operating as expected.
For details on monitoring health checks, see [Working with health checks](/docs/vpc?topic=vpc-alb-health-checks&interface=ui).

### Validating SAP Web Dispatcher and {{site.data.keyword.BluSoftlayer_notm}} Application Load Balancer
{: #sap-web-dispatcher-end-to-end-validation}

To validate the integration of SAP Web Dispatcher and the {{site.data.keyword.BluSoftlayer_notm}} Application Load Balancer, open the ALB URL in a web browser as follows:

```sh
https://<alb-public-host-name-or-ip>:44300/sap/wdisp/admin/public/default.html
```
{: screen}

Log in using your username (for example, `webadm`) and the password that you specified during the SAP Web Dispatcher installation.
If the login is successful, the Web Administration Interface of the SAP Web Dispatcher opens.
The dispatcher is deployed behind the {{site.data.keyword.BluSoftlayer_notm}} Application Load Balancer.
