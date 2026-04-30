---
copyright:
  years: 2025, 2026
lastupdated: "2026-04-29"
keywords: SAP, Bare Metal, Classic, operating system, OS, Linux, RHEL, Red Hat, SLES, SUSE, Windows Server, OS images, BYOL, OS planning, Classic Infrastructure
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Operating system planning for SAP on {{site.data.keyword.baremetal_short}} in Classic Infrastructure
{: #plan-os-classic-bm}

This document provides guidance on operating system selection and planning for SAP HANA&reg; and {{site.data.keyword.sap_app_server}} workloads running on {{site.data.keyword.baremetal_long}} in {{site.data.keyword.cloud}} Classic Infrastructure. Understanding the available operating systems, their support levels, and deployment options is essential for a successful SAP system implementation on Classic Infrastructure.
{: shortdesc}

## Overview
{: #os-overview}

SAP HANA and {{site.data.keyword.sap_app_server}} deployments on {{site.data.keyword.baremetal_short}} in Classic Infrastructure require carefully selected operating systems that are certified for both the bare metal hardware and your specific SAP applications. The supported operating systems include:
- Red Hat&reg; Enterprise Linux&reg; (RHEL)
- SUSE&reg; Linux Enterprise Server (SLES)
- Microsoft Windows Server

Selecting the appropriate operating system depends on SAP solution requirements, existing infrastructure, in-house expertise, and preferred support model.

### Key considerations for OS selection
{: #os-considerations}

When defining your operating system strategy for running SAP workloads on {{site.data.keyword.baremetal_short}} in Classic Infrastructure, evaluate the following factors:

SAP application compatibility
:   Verify that the selected operating system and version are certified for the SAP solution you plan to deploy (for example, SAP HANA, {{site.data.keyword.sap_app_server}}, SAP S/4HANA, SAP BW/4HANA, SAP ASE, SAP IQ, or SAP MaxDB).

Application compatibility of other vendors
:   Verify that the selected operating system and version are certified for the third-party solution you plan to deploy (for example, IBM Db2, Microsoft SQL Server).

Hardware compatibility
:   Make sure that your selected {{site.data.keyword.baremetal_short}} profile supports the required operating system, version, and technology levels.

Licensing model
:   Determine whether to use IBM-provided operating system licenses or bring your own license (BYOL) option based on your procurement and cost-management strategy.

Support lifecycle
:   Use operating system versions that align with SAP maintenance timelines to ensure long‑term support.

Performance requirements
:   Consider operating system-specific optimizations and tuning parameters that are required for your SAP workload on bare metal infrastructure.

### Operating system deployment in Classic Infrastructure
{: #os-deployment-options}

In Classic Infrastructure, the operating system is deployed during the {{site.data.keyword.baremetal_short}} provisioning process. You select the operating system and version when you order the server. The operating system is installed on the server before the server is delivered to you.

## Supported and available operating systems
{: #os-supported}

{{site.data.keyword.baremetal_short}} in Classic Infrastructure support multiple operating systems for SAP workloads. Verify that the operating system version you select is compatible with both your bare metal server profile and your SAP application requirements.

Minor versions or releases of the listed operating systems are still supported by SAP but are no longer offered through {{site.data.keyword.cloud_notm}}.
{: note}

The following operating systems are available for SAP workloads on {{site.data.keyword.baremetal_short}} in Classic Infrastructure:

- **Red Hat Enterprise Linux (RHEL)**: Version 8.4 or higher for SAP Applications
- **SUSE Linux Enterprise Server (SLES)**: Version 15 SP3 or higher for SAP Applications
- **Microsoft Windows Server**: 2016, 2019, 2022, and 2025

For the most current list of supported operating system versions and their compatibility with specific SAP products, see:
- [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}
- [SAP Note 1380654 - SAP support in IaaS environments](https://me.sap.com/notes/1380654){: external}
- [SAP Product Availability Matrix (PAM)](https://support.sap.com/en/release-upgrade-maintenance.html#section_1969201630){: external}.

**Operating system deployment requirements**:
- Configure kernel parameters according to SAP guidelines and the recommendations for your selected operating system.
- Install required packages, libraries, and dependencies for the SAP applications being deployed.
- Apply security hardening and compliance configurations as required by your organization.
- Configure network settings, hostname resolution, and time synchronization.
- For bare metal servers, ensure proper RAID configuration and disk management for optimal performance.

## Operating system selection during provisioning
{: #os-selection}

When provisioning {{site.data.keyword.baremetal_short}} in Classic Infrastructure, you select the operating system during the server ordering process. Available operating systems depend on the server configuration and SAP certification requirements.

### SAP-certified operating systems
{: #os-sap-certified}

For SAP workloads, select operating systems that are certified for SAP HANA or {{site.data.keyword.sap_app_server}}.

**For SAP HANA systems**:
- Red Hat Enterprise Linux for SAP HANA.
- SUSE Linux Enterprise Server for SAP HANA.
- Operating systems include SAP-specific configurations and optimizations.
- Apply required OS settings according to SAP support notes.
- Recommended for all SAP HANA production environments.

**For {{site.data.keyword.sap_app_server}} systems**:
- Red Hat Enterprise Linux for SAP applications.
- SUSE Linux Enterprise Server for SAP applications.
- Microsoft Windows Server (for {{site.data.keyword.sap_app_server}} on Windows).
- Include required packages and configurations for {{site.data.keyword.sap_app_server}} workloads.

### Operating system licensing
{: #os-licensing}

{{site.data.keyword.cloud_notm}} Classic Infrastructure offers two licensing options:

**IBM-provided licenses**:
- Operating system license included in the monthly server cost.
- Automatic updates and patches are applied through IBM-managed repositories.
- License management and compliance are simplified.
- Support is included as part of the infrastructure support.

**Bring your own license (BYOL)**:
- Use your existing operating system licenses.
- Maintain license compliance across hybrid cloud environments.
- Requires active support contracts with operating system vendors.
- You are responsible for license management and software updates.

## Operating system configuration for SAP
{: #os-configuration}

After the operating system is installed on your {{site.data.keyword.baremetal_short}}, additional configuration is required to prepare the system for SAP workloads.

### Kernel parameters and system settings
{: #os-kernel-parameters}

Configure kernel parameters and system settings according to SAP requirements and always refer to the relevant SAP Notes.

Some SAP Notes reference additional SAP Notes that must also be reviewed.
{: note}

For any SAP application, review the following SAP Notes:

- [2083594 - SAP Kernel Versions and SAP Kernel Patch Levels](https://me.sap.com/notes/2083594){: external}
- [2578899 - SUSE Linux Enterprise Server 15: Installation Note](https://me.sap.com/notes/2578899){: external}
- [3108316 - Red Hat Enterprise Linux 9.x: Installation and Configuration](https://me.sap.com/notes/3108316){: external}
- [2772999 - Red Hat Enterprise Linux 8.x: Installation and Configuration](https://me.sap.com/notes/2772999){: external}
- [3579834 - SAP Systems on Windows Server 2025](https://me.sap.com/notes/3579834){: external}
- [3143497 - SAP Systems on Windows Server 2022](https://me.sap.com/notes/3143497){: external}
- [2751450 - SAP Systems on Windows Server 2019](https://me.sap.com/notes/2751450){: external}

In addition, for SAP HANA, review the following SAP Notes:

- [SAP Note 2684254 - SAP HANA DB: Recommended OS settings for SLES 15 / SLES for SAP Applications 15](https://me.sap.com/notes/2684254){: external}
- [SAP Note 3108302 - SAP HANA DB: Recommended OS Settings for RHEL 9](https://me.sap.com/notes/3108302){: external}
- [SAP Note 2777782 - SAP HANA DB: Recommended OS Settings for RHEL 8](https://me.sap.com/notes/2777782){: external}

### Required packages and dependencies
{: #os-packages}

Install all required packages and libraries to support SAP workloads.

**For Linux systems**:
- SAP Host Agent prerequisites
- Database client libraries
- System monitoring tools
- Backup and recovery utilities
- High availability cluster software (if applicable)

**For Windows systems**:
- Microsoft Visual C++ redistributables
- .NET Framework versions required by SAP
- Database client software
- SAP-specific Windows components

Many of the components will be provided by the SAP installation application **Software Provisioning Manager** (SWPM).
- [SAP Note 2568783 - Release Note for Software Provisioning Manager 2.0](https://me.sap.com/notes/2568783){: external}
- [SAP Note 1680045 - Release Note for Software Provisioning Manager 1.0](https://me.sap.com/notes/1680045){: external}


### Security hardening
{: #os-security}

Apply security configurations:

- Disable unnecessary services
- Configure firewall rules
- Implement SELinux or AppArmor policies (Linux)
- Configure audit logging
- Apply security patches and updates
- Implement access controls and user management

## Operating system update and patch management
{: #os-update-management}

**Update strategies**:
- Establish regular patching schedules aligned with SAP maintenance windows.
- Test updates in nonproduction environments before applying to production.
- Maintain documentation of applied patches and updates.
- Use automation tools for consistent patching where possible.

**SAP-specific considerations**:
- Follow SAP guidelines for operating system updates.
- Verify compatibility with SAP applications before applying updates.
- Review SAP Notes for operating system-specific requirements.
- Coordinate updates with SAP application maintenance.

**Classic Infrastructure considerations**:
- Plan for potential downtime during OS updates on bare metal servers.
- Ensure that RAID configurations remain intact after updates.
- Verify hardware driver compatibility after OS updates.
- Coordinate with IBM support for critical updates if needed.

## High availability and disaster recovery
{: #os-ha-dr}

For high availability configurations:

**Linux clustering**:
- Pacemaker and Corosync for RHEL
- Pacemaker for SLES
- Configure fencing mechanisms
- Set up cluster resources for SAP applications

**Windows clustering**:
- Windows Server Failover Clustering (WSFC)
- Configure cluster quorum
- Set up cluster resources for SAP applications

**For disaster recovery**:
- Implement OS-level replication where applicable
- Configure backup and restore procedures
- Document recovery procedures
- Test disaster recovery scenarios regularly

## References
{: #os-references}

- [SAP Note 2414097 - SAP Applications on IBM Cloud Classic Infrastructure environment](https://me.sap.com/notes/2414097){: external}
- [SAP Note 1380654 - SAP support in IaaS environments](https://me.sap.com/notes/1380654){: external}
- [SAP Note 2235581 - SAP HANA: Supported Operating Systems](https://me.sap.com/notes/2235581){: external}
- [SAP Note 2369910 - SAP Software on Linux: General information](https://me.sap.com/notes/2369910){: external}
- [SAP Product Availability Matrix (PAM)](https://support.sap.com/en/release-upgrade-maintenance.html#section_1969201630){: external}
- [Red Hat Enterprise Linux for SAP Solutions](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux/sap){: external}
- [SUSE Linux Enterprise Server for SAP applications](https://www.suse.com/products/sles-for-sap/){: external}
- [SAP HANA certified instances on Intel-powered bare metal servers in Classic Infrastructure](/docs/sap?topic=sap-hana-iaas-offerings-profiles-classic-intel-bm)
- [SAP application server certified instances on Intel-powered bare metal servers in Classic Infrastructure](/docs/sap?topic=sap-nw-iaas-offerings-profiles-classic-bm)
