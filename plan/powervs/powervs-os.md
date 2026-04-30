---
copyright:
  years: 2025, 2026
lastupdated: "2026-04-29"
keywords: SAP, Power Virtual Server, PowerVS, operating system, OS, Linux, RHEL, Red Hat, SLES, SUSE, boot images, stock images, custom images, image import, Full Linux Subscription, FLS, BYOL, cloud-init, OS planning, Linux
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Operating system planning and image management for SAP on {{site.data.keyword.powerSys_notm}}
{: #plan-os-powervs}

This document provides guidance on operating system selection, planning, and image management strategies for SAP HANA&reg; and {{site.data.keyword.sap_app_server}} workloads running on {{site.data.keyword.powerSysFull}}. Understanding the available Linux distributions, their support levels, and deployment options is essential for a successful SAP system implementation.
{: shortdesc}

## Overview
{: #os-overview}

SAP HANA database and {{site.data.keyword.sap_app_server}} deployments on {{site.data.keyword.powerSysFull}} require Linux operating systems. The supported distributions are:
- Red Hat&reg; Enterprise Linux&reg; (RHEL)
- SUSE&reg; Linux Enterprise Server (SLES)

Selecting the appropriate distribution depends on SAP solution requirements, existing infrastructure, in‑house expertise, and preferred support model.

### Key considerations for OS selection
{: #os-considerations}

When defining an operating system strategy for SAP workloads on running {{site.data.keyword.powerSys_notm}}, evaluate the following factors:

SAP application compatibility
:   Verify that the selected Linux distribution and version are certified for your SAP workload (for example SAP HANA&reg;, {{site.data.keyword.sap_app_server}}, SAP S/4HANA&reg;, or SAP BW/4HANA&reg;).

Hardware compatibility
:   Make sure that the selected compute profile on {{site.data.keyword.powerSys_notm}} supports your required Linux distribution, version, and technology level.

Licensing model
:   Determine whether to use full Linux&reg; subscription that is provided by IBM or bring your own license (BYOL) option based on your procurement and cost‑management strategy.

Support lifecycle
:   Use Linux versions that align with SAP maintenance timelines to ensure long‑term support.

### Image deployment options
{: #os-options}

{{site.data.keyword.powerSys_notm}} supports two deployment approaches:

1. **IBM-provided stock images**: Preconfigured, SAP‑certified Linux OS images that {{site.data.keyword.BluSoftlayer_notm}} prepares and maintains for deployment on {{site.data.keyword.powerSys_notm}}. These images include all required configurations, packages, and cloud‑init settings that are needed to run SAP HANA or {{site.data.keyword.sap_app_server}} workloads.
1. **Custom images**: Customer-created or modified images that are imported from {{site.data.keyword.cos_full}}, supporting standardized configurations and custom requirements.

## Supported operating systems
{: #os-supported}

{{site.data.keyword.powerSysFull}} offers compute profiles for different server types in the IBM&reg; Power&reg; system family. Verify that the Linux distribution, version, and technology levels you select are compatible with both your IBM Power system generation and your SAP application requirements.

IBM {{site.data.keyword.powerSys_notm}} supports only the following OS distributions for SAP HANA and {{site.data.keyword.sap_app_server}}:

- Red Hat Enterprise Linux (RHEL)
- SUSE Linux Enterprise Server (SLES)


**Supported versions by machine type:**

|Machine type                         |RHEL version     |SLES version        |
|-------------------------------------|-----------------|--------------------|
|E1080 (9080-HEX)                     |RHEL 8.4 or later|SLES 15 SP3 or later|
|S1022 (9105-22A)                     |RHEL 8.4 or later|SLES 15 SP3 or later|
|S1122 (9824-22A)                     |RHEL 8.6 or later|SLES 15 SP4 or later|
{: caption="Supported Linux versions by machine type" caption-side="bottom"}


**Linux deployment requirements:**
- Configure kernel parameters according to the SAP guidelines and the recommendations that are provided by your Linux distribution.
- Install all the required packages, libraries, and dependencies needed to support the SAP applications you plan to deploy.


## IBM provided stock images
{: #os-stock-images}

{{site.data.keyword.BluSoftlayer_notm}} provides preconfigured stock Linux images optimized for {{site.data.keyword.powerSys_notm}}. These images are recommended for SAP HANA and {{site.data.keyword.sap_app_server}}.

### Benefits of stock images
{: #os-stock-image-benefits}

- Preconfigured for cloud with cloud‑init integration for automated configuration.
- Tested and validated for compatibility with {{site.data.keyword.powerSys_notm}} infrastructure.
- Updated regularly with security patches and improvements.
- Certified by SAP for SAP HANA and {{site.data.keyword.sap_app_server}} deployments.
- Simplified licensing through full Linux&reg; Subscription with integrated licensing and update mechanisms.
- Optimized for SAP workloads with SAP‑recommended kernel parameters and system configurations.

### Stock image categories
{: #os-stock-image-categories}

**OS images for SAP HANA systems**:
- Configured and certified for SAP HANA workloads.
- Include OS configurations, kernel parameters, and optimizations for SAP HANA workloads.
- Preconfigured with transparent huge pages, I/O scheduler settings, and memory management.
- Recommended for all SAP HANA production environments.
- Image names include "SAP-HANA" designation.

**OS images for {{site.data.keyword.sap_app_server}} instances**:
- Configured and certified for {{site.data.keyword.sap_app_server}} deployments.
- Include required packages and configurations for {{site.data.keyword.sap_app_server}} workloads.
- Image names typically include "SAP-NETWEAVER" designation.

**Generic SLES and RHEL images**:
- General-purpose Linux images for non-SAP workloads.
- Can be used for infrastructure components (proxies, jump servers, monitoring systems).
- May require additional configuration and certification for SAP workloads.
- Not recommended for SAP production environments.

### Full Linux subscription
{: #os-stock-image-fls}

The full Linux&reg; subscription feature provides comprehensive Linux support through {{site.data.keyword.BluSoftlayer_notm}} for RHEL and SLES on {{site.data.keyword.powerSys_notm}}.

**Key features**:
- Access to OS interim fixes and updates via {{site.data.keyword.BluSoftlayer_notm}} satellite servers (RHEL) or RMT servers (SLES).
- Integrated activation key management for automated registration.
- Proxy-based connectivity to update repositories.
- Support for both SAP and non-SAP workloads.
- Cloud-init scripts for automated registration during VM provisioning.
- Extra charges apply and are included in hourly billing rates.

**Licensing considerations**:
- Full Linux subscription charges are included in hourly billing rates
- BYOL (Bring your own license) images are available for customers with existing Red Hat or SUSE subscriptions
- OS license costs cannot be transferred from private cloud to {{site.data.keyword.powerSys_notm}}
- BYOL images are listed under "Client-supplied subscription" section during provisioning

### Image selection guidelines
{: #os-stock-image-guidelines}

When selecting {{site.data.keyword.BluSoftlayer_notm}} stock images for SAP workloads:
1. **Identify SAP requirements**: Use SAP-certified images (SAP-HANA or SAP-NETWEAVER) for SAP workloads.
1. **Choose appropriate version**: Select Linux version compatible with your Power hardware and SAP applications.
1. **Determine licensing model**: Decide between FLS or BYOL based on your licensing strategy and existing subscriptions.
1. **Plan for updates**: Establish update and patching strategy aligned with SAP maintenance windows.
1. **Verify SAP certification**: Confirm the image is listed in the SAP Product Availability Matrix (PAM).

### Image naming conventions
{: #os-stock-image-naming-convention}

{{site.data.keyword.BluSoftlayer_notm}} stock images follow consistent naming patterns:
- **RHEL SAP HANA images**: Start with "RHEL" followed by version and the suffix "SAP-HANA" (for example, "RHEL9-SP6-SAP-HANA").
- **RHEL SAP NetWeaver images**: Start with "RHEL" followed by version and the suffix "SAP-NETWEAVER" or "SAP" (for example, "RHEL9-SP6-SAP").
- **SLES SAP HANA images**: Start with "SLES" followed by version and the suffix "SAP-HANA" (for example, "SLES15-SP4-SAP-HANA").
- **SLES SAP NetWeaver images**: Start with "SLES" followed by version and the suffix "SAP" (for example, "SLES15-SP4-SAP").
- **BYOL images**: Include "-BYOL" suffix for bring your own license versions.
- **FLS images**: Standard naming without BYOL suffix indicates full Linux subscription.

## Bring your own images
{: #os-byo-image}

Custom image deployment allows organizations to use standardized, preconfigured Linux images tailored to their specific SAP requirements. This approach is valuable for maintaining consistency across environments and implementing organization-specific configurations.

### Custom image use cases
{: #os-byo-image-use-cases}

**When to use custom images**:
- Standardized Linux configurations across multiple SAP environments.
- Specific security hardening or compliance requirements beyond standard images.
- Migration from existing SAP environments with established image standards.
- Custom kernel parameters or OS tuning specific to your SAP workloads.

**When to use stock images**:
- Initial SAP deployments or proof-of-concept environments.
- Standard SAP configurations without special requirements.
- Environments requiring latest {{site.data.keyword.BluSoftlayer_notm}}-tested and SAP-certified configurations.
- Simplified management and update processes.
- Faster deployment timelines.

### Custom image requirements
{: #os-byo-image-requirements}

**Technical prerequisites**:
- Images must be in supported formats: .ova, .ova.gz, .tar, .tar.gz, or .tgz.
- Maximum uncompressed image size: 10 TB.
- Cloud-init must be properly configured for {{site.data.keyword.powerSys_notm}} integration.
- Images must be compatible with target Power hardware machine type.
- Linux version must meet minimum requirements for SAP applications.
- SAP-required kernel parameters and configurations must be included.

**Infrastructure prerequisites**:
- {{site.data.keyword.cos_full_notm}} account and bucket.
- HMAC credentials (access key and secret key) for {{site.data.keyword.cos_short}} authentication.


### Custom Image creation strategies
{: #os-byo-image-creation}

**Capturing from existing SAP environments**:
- Capture running {{site.data.keyword.powerSys_notm}} instances using CLI or API after SAP installation.
- Remove SAP instance-specific data before creating reusable images.
- Validate captured images in test environment before production use.

**Building custom SAP images**:
- Start with {{site.data.keyword.BluSoftlayer_notm}} SAP-certified stock image as base.
- Apply organization-specific configurations and hardening.
- Install required monitoring agents, backup software, or management tools.
- Configure cloud-init for {{site.data.keyword.powerSys_notm}} integration and SAP-specific automation.
- Document all customizations for maintenance and troubleshooting.
- Test thoroughly with SAP applications before production use.

**Cloud-init configuration for Linux**:
- Install and configure cloud-init packages for RHEL or SLES.
- For RHEL: Follow IBM PowerVC documentation for cloud-init on RHEL.
- For SLES: Follow IBM PowerVC documentation for cloud-init on SLES.
- Test cloud-init functionality during image validation.

### Custom Image import process
{: #os-byo-image-import}

**Preparation steps**:
1. Create {{site.data.keyword.cos_full_notm}} bucket in the appropriate region.
1. Generate HMAC credentials with proper permissions (Writer access required).
1. Upload Linux image file to {{site.data.keyword.cos_short}} bucket (use private endpoint domain).


**Import considerations**:
- Large images may take significant time to import (plan accordingly).
- Import operations are tracked as jobs in {{site.data.keyword.powerSys_notm}}.
- Only one import operation per {{site.data.keyword.powerSys_notm}} workspace at a time.
- Imported images become available in boot image catalog after successful import.
- For SAP images, specify import details with vendor="SAP" and product type.

**Storage pool strategies**:
- **Auto-select**: System automatically selects pool with sufficient capacity.
- **Affinity**: Place image in same pool as an existing SAP VM or volume for performance.
- **Anti-affinity**: Exclude specific pools to distribute SAP workloads across storage.

**Importing SAP-customized images**:
- When importing customized SAP HANA or {{site.data.keyword.sap_app_server}} images, select the **self-certification** checkbox.
- Specify import details: `licenseType: "byol"`, `product: "Hana"`, or `"Netweaver"`, `vendor: "SAP"`
