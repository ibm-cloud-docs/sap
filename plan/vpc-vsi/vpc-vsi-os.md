---
copyright:
  years: 2025, 2026
lastupdated: "2026-04-29"
keywords: SAP, VPC, Virtual Server Instance, operating system, OS, Linux, RHEL, Red Hat, SLES, SUSE, Windows Server, stock images, custom images, image import, BYOL, cloud-init, OS planning
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Operating system planning and image management for SAP on virtual server instances in {{site.data.keyword.vpc_short}}
{: #plan-os-vpc-vsi}

This document provides guidance on operating system selection, planning, and image management strategies for SAP HANA&reg; and {{site.data.keyword.sap_app_server}} workloads running on {{site.data.keyword.vsi_is_full}} in {{site.data.keyword.vpc_full}}. Understanding the available operating systems, their support levels, and deployment options is essential for a successful SAP system implementation.
{: shortdesc}

## Overview
{: #os-overview}

SAP HANA and {{site.data.keyword.sap_app_server}} deployments on {{site.data.keyword.vsi_is_short}} require certified operating systems. The supported operating systems include:
- Red Hat&reg; Enterprise Linux&reg; (RHEL)
- SUSE&reg; Linux Enterprise Server (SLES)
- Microsoft Windows Server

Selecting the appropriate operating system depends on SAP solution requirements, existing infrastructure, in-house expertise, and preferred support model.

### Key considerations for OS selection
{: #os-considerations}

When defining an operating system strategy for running SAP workloads on {{site.data.keyword.vsi_is_short}}, evaluate the following factors:

SAP application compatibility
:   Verify that the selected operating system and version are certified for the SAP solution you plan to deploy (for example, SAP HANA, {{site.data.keyword.sap_app_server}}, SAP S/4HANA, SAP BW/4HANA, SAP ASE, SAP IQ, or SAP MaxDB).

Application compatibility of other vendors
:   Verify that the selected operating system and version are certified for the third-party solution you plan to deploy (for example, IBM Db2, Microsoft SQL Server).

Hardware compatibility
:   Make sure that your selected compute profile in {{site.data.keyword.vpc_short}} supports the required operating system and version.

Licensing model
:   Determine whether to use IBM-provided stock images with included licensing or bring-your-own-license (BYOL) options based on your procurement and cost-management strategy.

Support lifecycle
:   Use operating system versions that align with SAP maintenance timelines to ensure long‑term support.

Performance requirements
:   Consider operating system-specific optimizations and tuning parameters required for your SAP workload.

### Image deployment options
{: #os-options}

{{site.data.keyword.vpc_short}} supports multiple OS deployment methods:

1. **Stock images**: Preconfigured, ready-to-deploy operating system images maintained by {{site.data.keyword.IBM_notm}} with integrated support and SAP certifications.
1. **Custom images**: Customer-created or modified images imported from {{site.data.keyword.cos_full_notm}}, supporting standardized configurations and custom requirements.
1. **Catalog images**: Images from {{site.data.keyword.IBM_notm}} private catalogs that can be shared across multiple accounts within an enterprise.

## Supported operating systems
{: #os-supported}

{{site.data.keyword.vsi_is_short}} supports multiple operating systems for SAP workloads.Verify that the operating system version you select is compatible with both your virtual server instance profile and your SAP application requirements.

Minor versions or releases of the listed operating systems are still supported by SAP but no longer offered through {{site.data.keyword.cloud_notm}}.
{: note}

The following operating systems are available for SAP workloads on {{site.data.keyword.vsi_is_short}}:

- **Red Hat Enterprise Linux (RHEL)**: Version 8.6 or higher for SAP Applications
- **SUSE Linux Enterprise Server (SLES)**: Version 15 SP3 or higher for SAP Applications
- **Microsoft Windows Server**: 2016, 2019, 2022, and 2025

For the most current list of supported operating system versions and their compatibility with specific SAP products, see:
- [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}
- [SAP Note 1380654 - SAP support in IaaS environments](https://me.sap.com/notes/1380654){: external}
- [SAP Product Availability Matrix (PAM)](https://support.sap.com/en/release-upgrade-maintenance.html#section_1969201630){: external}

**Operating system deployment requirements**:
- Configure kernel parameters according to SAP guidelines and the recommendations for your selected operating system.
- Install all required packages, libraries, and dependencies needed to support the SAP applications you plan to deploy.
- Apply security hardening and compliance configurations as required by your organization.
- Configure network settings, hostname resolution, and time synchronization.

## IBM-provided stock images
{: #os-stock-images}

{{site.data.keyword.IBM_notm}} provides preconfigured stock operating system images optimized for {{site.data.keyword.vpc_short}} environments. These images are the recommended starting point for SAP HANA and {{site.data.keyword.sap_app_server}} deployments.

### Benefits of stock images
{: #os-stock-image-benefits}

- Preconfigured for cloud with cloud‑init integration for automated configuration.
- Tested and validated for compatibility with {{site.data.keyword.vpc_short}} infrastructure.
- Maintained and updated by {{site.data.keyword.IBM_notm}} with security patches and improvements.
- Certified by SAP for SAP HANA and {{site.data.keyword.sap_app_server}} workloads.
- Support faster provisioning through predefined configurations.
- Include SAP‑recommended kernel parameters and system optimizations.

### Stock image categories
{: #os-stock-image-categories}

**OS images for SAP HANA systems**:
- Configured and certified for SAP HANA workloads.
- Include operating system configurations, kernel parameters, and optimizations for SAP HANA workloads.
- Apply required OS settings according to SAP support notes.
- Recommended for all SAP HANA production environments.
- Image names typically include "sap-hana" designation.

**OS images for {{site.data.keyword.sap_app_server}} systems**:
- Configured and certified for {{site.data.keyword.sap_app_server}} deployments.
- Include required packages and configurations for {{site.data.keyword.sap_app_server}} workloads.
- Image names typically include "sap-applications" designation.
- Support both Linux and Windows Server operating systems.

**Generic operating system images**:
- General-purpose operating system images for non-SAP workloads.
- Can be used for infrastructure components (proxies, jump servers, monitoring systems).
- May require additional configuration and certification for SAP workloads.
- Not recommended for SAP production environments.

### Image selection guidelines
{: #os-stock-image-guidelines}

When selecting stock images for SAP workloads on {{site.data.keyword.vsi_is_short}}:

1. **Identify SAP requirements**: Use SAP-certified images (sap-hana or sap-applications) for SAP workloads.
1. **Choose appropriate version**: Select operating system version compatible with your virtual server profile and SAP applications.
1. **Determine licensing model**: Decide between included licensing or BYOL based on your licensing strategy and existing subscriptions.
1. **Plan for updates**: Establish update and patching strategy aligned with SAP maintenance windows.
1. **Verify SAP certification**: Confirm the image is listed in SAP Product Availability Matrix (PAM) and [SAP Note 2927211](https://me.sap.com/notes/2927211){: external}.

### Image naming conventions
{: #os-stock-image-naming-convention}

{{site.data.keyword.IBM_notm}} stock images follow consistent naming patterns:

- **RHEL SAP HANA images**: Include "rhel" followed by version and "sap-hana" (for example, `ibm-redhat-9-6-amd64-sap-hana-4`).
- **RHEL SAP Applications images**: Include "rhel" followed by version and "sap-applications" (for example, `ibm-redhat-9-6-amd64-sap-applications-4`).
- **SLES SAP HANA images**: Include "sles" followed by version and "sap-hana" (for example, `ibm-sles-15-6-amd64-sap-hana-6`).
- **SLES SAP Applications images**: Include "sles" followed by version and "sap-applications" (for example, `ibm-sles-15-6-amd64-sap-applications-6`).
- **Windows Server images**: Include "windows" followed by version (for example, `ibm-windows-server-2025-full-standard-amd64-10`).

## Bring your own images
{: #os-byo-image}

Custom image deployment allows organizations to use standardized, preconfigured operating system images tailored to their specific SAP requirements. This approach is valuable for maintaining consistency across environments and implementing organization-specific configurations.

### Custom image use cases
{: #os-byo-image-use-cases}

**When to use custom images**:
- Standardized operating system configurations across multiple SAP environments.
- Specific security hardening or compliance requirements beyond standard images.
- Migration from existing SAP environments with established image standards.
- Custom kernel parameters or operating system tuning specific to your SAP workloads.
- Integration with enterprise configuration management tools.

**When to use stock images**:
- Initial SAP deployments or proof-of-concept environments.
- Standard SAP configurations without special requirements.
- Environments requiring latest {{site.data.keyword.IBM_notm}}-tested and SAP-certified configurations.
- Simplified management and update processes.
- Faster deployment timelines.

### Custom image requirements
{: #os-byo-image-requirements}

**Technical prerequisites**:
- Images must be in supported formats for {{site.data.keyword.vpc_short}}.
- Cloud-init must be properly configured for {{site.data.keyword.vpc_short}} integration.
- Images must be compatible with target virtual server instance profiles.
- Operating system version must meet minimum requirements for SAP applications.
- SAP-required kernel parameters and configurations must be included.

**Infrastructure prerequisites**:
- {{site.data.keyword.cos_full_notm}} account and bucket.
- Appropriate identity and access management (IAM) permissions for image import operations.
- Network connectivity to {{site.data.keyword.cos_short}} endpoints.

For detailed requirements, see [Planning for custom images](/docs/vpc?topic=vpc-planning-custom-images){: external}.

### Custom image creation strategies
{: #os-byo-image-creation}

**Capturing from existing SAP environments**:
- Capture running {{site.data.keyword.vsi_is_short}} instances using image from volume feature.
- Remove SAP instance-specific data before creating reusable images.
- Validate captured images in test environment before production use.

**Building custom SAP images**:
- Start with {{site.data.keyword.IBM_notm}} SAP-certified stock image as base.
- Apply organization-specific configurations and hardening.
- Install required monitoring agents, backup software, or management tools.
- Configure cloud-init for {{site.data.keyword.vpc_short}} integration and SAP-specific automation.
- Document all customizations for maintenance and troubleshooting.
- Test thoroughly with SAP applications before production use.

**Cloud-init configuration**:
- Install and configure cloud-init packages for your operating system.
- Test cloud-init functionality during image validation.
- Ensure proper integration with {{site.data.keyword.vpc_short}} metadata service.

### Custom image import process
{: #os-byo-image-import}

**Preparation steps**:
1. Create {{site.data.keyword.cos_full_notm}} bucket in appropriate region.
1. Upload operating system image file to {{site.data.keyword.cos_short}} bucket.
1. Ensure proper IAM permissions for image import operations.

**Import process**:
1. Navigate to {{site.data.keyword.vpc_short}} console.
1. Select **Images** from the navigation menu.
1. Click **Create** and select **Import custom image**.
1. Provide image details including name, resource group, and region.
1. Specify {{site.data.keyword.cos_short}} bucket and image file location.
1. Select operating system and version.
1. Configure encryption settings if required.
1. Review and create the custom image.

**Import considerations**:
- Large images may take significant time to import (plan accordingly).
- Import operations are tracked as jobs in {{site.data.keyword.vpc_short}}.
- Imported images become available in image catalog after successful import.
- For SAP images, ensure proper operating system selection during import.

For more information, see:
- [Importing and managing custom images](/docs/vpc?topic=vpc-managing-custom-images){: external}
- [Importing and validating custom images into VPC](/docs/vpc?topic=vpc-importing-custom-images-vpc){: external}
- [Image from volume](/docs/vpc?topic=vpc-image-from-volume-vpc){: external}

## Bring your own license (BYOL)
{: #byol-sap-vpc}

When you have your own operating system license, you can install it on your virtual server instance based on the vendor's instructions. The operating system chosen must be certified for SAP and have access to the necessary operating system packages for SAP.

**BYOL use cases**:
- You have existing operating system licenses with support contracts.
- You need to maintain license compliance across hybrid cloud environments.
- You require specific operating system versions or configurations not available in stock images.
- Cost optimization through existing volume licensing agreements.

**Licensing considerations**:
- Valid operating system licenses for the number of virtual server instances.
- Active support contracts with operating system vendors.
- Compliance with vendor licensing terms for cloud deployments.

For more information, see [Bring your own license](/docs/vpc?topic=vpc-byol-vpc-about){: external}.

## Operating system update and patch management
{: #os-update-management}

**Update strategies**:
- Establish regular patching schedules aligned with SAP maintenance windows.
- Test updates in non-production environments before applying to production.
- Maintain documentation of applied patches and updates.
- Consider using automation tools for consistent patch deployment.

**SAP-specific considerations**:
- Follow SAP guidelines for operating system updates.
- Verify compatibility with SAP applications before applying updates.
- Review SAP Notes for operating system-specific requirements.
- Coordinate updates with SAP application maintenance.

## References
{: #os-references}

- [SAP Note 2927211 - SAP Applications on IBM Cloud Virtual Private Cloud (VPC) Infrastructure environment](https://me.sap.com/notes/2927211){: external}
- [Getting started with images](/docs/vpc?topic=vpc-getting-started-images-on-vpc-stock)
- [About virtual server images](/docs/vpc?topic=vpc-about-images)
- [Planning for custom images](/docs/vpc?topic=vpc-planning-custom-images)
- [Importing and managing custom images](/docs/vpc?topic=vpc-managing-custom-images)
- [Importing and validating custom images into VPC](/docs/vpc?topic=vpc-importing-custom-images-vpc)
- [Granting access to IBM Cloud Object Storage to import images](/docs/vpc?topic=vpc-object-storage-prereq&interface=ui)
- [Image from volume](/docs/vpc?topic=vpc-image-from-volume-vpc)
- [Bring your own license](/docs/vpc?topic=vpc-byol-vpc-about)
- [SAP HANA certified instances on Intel-powered virtual servers on VPC Infrastructure](/docs/sap?topic=sap-hana-iaas-offerings-profiles-vpc-intel-vsi)
- [SAP application server certified instances on Intel-powered virtual servers on VPC Infrastructure](/docs/sap?topic=sap-nw-iaas-offerings-profiles-vpc-vsi)
