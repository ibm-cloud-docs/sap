---

copyright:
  years: 2023
lastupdated: "2023-10-09"

keywords: SAP, VPC, SAP Workloads

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

# Understanding your responsibilities when using SAP automated workloads on IBM Cloud VPC
{: #understand-sap-automated-workloads-intro}

SAP automated workloads on {{site.data.keyword.vpc_short}} involves several management responsibilities and terms and conditions:

   * For more information about the responsibilities when using the deployable architecture, see [Understanding your responsibilities when you use deployable architectures](https://cloud.ibm.com/docs/secure-enterprise?topic=secure-enterprise-responsibilities-deployable-architectures).
   * For a high-level view of the service types in {{site.data.keyword.cloud}} and the breakdown of responsibilities between the customer and IBM for each type, see [Shared responsibilities for using IBM Cloud products](https://cloud.ibm.com/docs/overview?topic=overview-shared-responsibilities).
   * For all the terms of use, see [{{site.data.keyword.cloud}} Terms and Notices](https://cloud.ibm.com/docs/overview/terms-of-use?topic=overview-terms).

## Security and regulation compliance
{: #understand-sap-automated-workloads-compliance}

Security and regulation compliance responsibilities are divided between you and IBM:

|     |IBM Responsibilities	|Your Responsibilities|
|-----|-----|-----|
|Ensure that the operating system image does not contain any vulnerabilities.|{{site.data.keyword.cloud_notm}} provided and verified RHEL and SLES operating system images are used. After deployment, operating system images are updated to the newest state in the defined minor release. Official RHEL and SLES software repositories are used for installation of additional software components (like SQUID proxy).|Customer is responsible for keeping the operating system secure and compliant after deployment.|
|Ensure that the SAP software does not contain any vulnerabilities.|N/A	|Customer is responsible to provide the SAP software only from SAP legal repositories: https://me.sap.com/softwarecenter. Customer is responsible for keeping the SAP software secure and compliant after deployment.|
|**Updates**: Activities to maintain or enhance a system, for example: <ul><li> Installing enhancement packages</li> <li> Installing add-ons</li> <li>Applying support package stacks or support packages</li> **Upgrades**: The switch from an older software version to a new version.|IBM is not responsible in maintaining or supporting a Live System, if is not contracted IBM is not responsible for any human errors, software malfunction, or supporting operational customer phases. After the new system is deployed with IBM automations, this is validated and accepted by the customer.|Customer is responsible for keeping the SAP live/operational system secure and compliant after deployment.|
|SAP Software security recommendations, security patches, and configurations|N/A|Customer is responsible for keeping the SAP live/operational system secure and configured with SAP recommended security notes after deployment completes. The process and procedures are covered in the References section.|

Hdblcm verifies the authenticity and integrity of the software packages prior to the installation or update. For SAP HANA, follow the SAP recommendations in verifying the authenticity of the downloaded software. The SAP HANA system can be installed using the SAP HANA Database Lifecycle Manager (HDBLCM). The installation software is downloaded from outside of your network and cannot be trusted. Therefore, you should ensure that the components are authentic before starting the SAP HANA Database Lifecycle Manager (HDBLCM).
{: note}

To verify the authenticity of a HANA software SAR archive, use this command:
`/usr/sap/hostctrl/exe/SAPCAR -dVf <archive name> /usr/sap/hostctrl/exe/libsapcrypto.so`. 
To verify the signature of the additional components, run `hdblcm` with the parameter `verify_signature`. For more information, see [SAP Note 2577617](https://me.sap.com/notes/2577617).

## References
{: #understand-sap-automated-workloads-reference}

* [SAP compliance and security](https://www.sap.com/about/trust-center/certification-compliance.html)
* [SAP/My Trust Center](https://support.sap.com/en/my-support/trust-center/tools-information.html)
* [SAP Assurance and Compliance Software](https://help.sap.com/docs/acs)
* [Security Configurations for SAP Cloud Services](https://www.sap.com/documents/2022/12/7616adbb-547e-0010-bca6-c68f7e60039b.html)

