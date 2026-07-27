---

copyright:
  years: 2026
lastupdated: "2026-07-20"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, {{site.data.keyword.powerSys_notm}}, instance metadata, metadata service, trusted profile
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Configuring and managing the metadata service for SAP on IBM {{site.data.keyword.powerSys_notm}}
{: #powervs-instance-metadata-service}

The {{site.data.keyword.powerSysFull}} metadata service provides information about your virtual server instances (VSIs). When the metadata service is enabled for a VSI, you can retrieve information about that VSI by sending REST API requests from the VSI. Access to the metadata service API endpoint is available only from within the {{site.data.keyword.powerSys_notm}} environment.
{: shortdesc}

{{../../../power-iaas/instance_metadata_pvs.md#metadata-service-concepts}}
{{../../../power-iaas/instance_metadata_pvs.md#metadata-auditing}}

## Configuration requirements
{: #configuration}

The metadata service uses an internal network configured with the link-local IP address `169.254.169.253` to provide secure, instance-level authentication.

| Scenario | Action |
| ---------| -------|
| Creating a VSI with metadata service access enabled        | The metadata service interface is automatically configured with IP address `169.254.169.253`. No additional configuration is required.                                                                                                                                                                                                                                                                                         |
| Enabling access to the metadata service on an existing VSI | You can enable access only when the VSI is in the `Shutoff` state. After you enable metadata service access and start the VSI, the metadata service interface is automatically configured.                                                                                                                                                                                                                                     |
| Disabling access to the metadata service                   | When you use the `force-disable` option, you must remove the network interface configuration. Specific commands depend on your Linux distribution's network management tools. For more information, see [Cleaning up network interfaces after disabling the metadata service on Linux](/docs/power-iaas?topic=power-iaas-troubleshoot-metadata-service#metadata-troubleshooting-linux-cleanup).                                |
| Considerations for boot disk overwrite operations          | Before you copy the disk image, you must remove network persistence rules to prevent connectivity issues when the image is restored. This involves backing up and clearing udev rules files with MAC address bindings. For more information, see [Reconfiguring the metadata service interface after image capture on Linux](/docs/power-iaas?topic=power-iaas-troubleshoot-metadata-service#reconfigure-linux-after-capture). |
{: caption="Metadata service configuration requirements by operating system" caption-side="bottom"}


{{../../../power-iaas/instance_metadata_pvs.md#metadata-enable-new-instance-ui}}
{{../../../power-iaas/instance_metadata_pvs.md#metadata-enable-existing-instance-ui}}
{{../../../power-iaas/instance_metadata_pvs.md#metadata-disable-instance-ui}}
{{../../../power-iaas/instance_metadata_pvs.md#metadata-service-enable-cli}}
{{../../../power-iaas/instance_metadata_pvs.md#metadata-identity-operations}}
{{../../../power-iaas/instance_metadata_pvs.md#metadata-retrieve-instance-metadata}}
