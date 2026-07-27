---

copyright:
  years: 2026
lastupdated: "2026-07-23"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, {{site.data.keyword.powerSys_notm}}, trusted profile, compute resource identity, backup, HA, resource agent, instance metadata
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Using trusted profiles for SAP workloads on IBM {{site.data.keyword.powerSys_notm}}
{: #powervs-iam-trusted-profiles}

Trusted profiles enable {{site.data.keyword.cloud_notm}} IAM access for {{site.data.keyword.powerSys_notm}} virtual server instances (VSIs) without requiring the management of static service ID API keys. Instead, a VSI retrieves a short-lived IAM token from the instance metadata service, and uses it to obtain an IAM access token associated with the trusted profile. The permissions granted by this access token are defined exclusively by the IAM policies assigned to the trusted profile.
{: shortdesc}

This approach is the recommended authorization mechanism for SAP workloads that require programmatic access to {{site.data.keyword.cloud_notm}} services. Typical use cases include backup operations and high availability (HA) resource agents, where secure, credential-free access to cloud resources is required.

For information about enabling the instance metadata service on a VSI, see [Configuring and managing the metadata service for SAP on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-powervs-instance-metadata-service).

## How trusted profiles work with compute resource identities
{: #how-it-works}

The following authentication flow is used when the instance metadata service is enabled on a VSI:

1. The VSI requests an instance identity token from the metadata service endpoint (`169.254.169.253`).
2. The identity token is exchanged with IAM for an IAM access token associated with the trusted profile assigned to the VSI.
3. The VSI uses the IAM access token to authenticate API requests to {{site.data.keyword.cloud_notm}} services.
4. IAM evaluates and enforces the policies attached to the trusted profile for each request.

The token is short-lived and automatically rotated. No API keys are stored on the VSI.

## Deciding how many trusted profiles to create
{: #one-or-two}

Backup operations run on every VSI, whereas HA resource agents run only on HA cluster nodes.
In addition, these workloads typically perform different actions and require access to different {{site.data.keyword.cloud_notm}} services.

When designing your authorization model, you must decide whether to use a single trusted profile that combines the permissions required for both backup and HA operations, or to create separate trusted profiles for each workload.
Both approaches are supported and can be implemented successfully.

The appropriate choice depends on your security and compliance requirements, the desired level of permission separation, and the operational effort you are willing to invest in managing multiple trusted profiles and their associated IAM policies.

The table compares the two options:

|                                        | One combined profile                                          | Two separate profiles                                                                                   |
| -------------------------------------- | ------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| **Number of profiles to manage**       | One                                                           | Two                                                                                                     |
| **Access granted to backup-only VSIs** | COS actions + {{site.data.keyword.powerSys_notm}} permissions | COS actions only                                                                                        |
| **Access granted to HA cluster VSIs**  | COS actions + {{site.data.keyword.powerSys_notm}} permissions | COS actions (profile `sap-backup`) + {{site.data.keyword.powerSys_notm}} permissions (profile `sap-ha`) |
| **Principle of least privilege**       | Not fully satisfied for backup-only VSIs                      | Satisfied                                                                                               |
| **Audit and update scope**             | All changes affect all VSIs                                   | Changes are scoped to the affected workload                                                             |
| **Best suited for**                    | Small environments, simple setups                             | Production landscapes with a mix of HA and non-HA VSIs                                                  |
{: caption="Comparison of one combined profile versus two separate profiles" caption-side="bottom"}

You can grant the required permissions directly in the trusted profile, or by [Creating custom roles](/docs/iam?topic=iam-custom-roles). Create a custom role in IAM that includes the permissions that are required, and assign the role in the profile.
{: note}

### Option 1: One combined trusted profile
{: #combined}

Create a single trusted profile whose compute resource identity condition matches all VSIs in your landscape. Attach both the COS backup actions and the {{site.data.keyword.powerSys_notm}} permissions to this profile.

Choose this option if:
- Your environment is small or all VSIs are part of an HA cluster.
- You want to keep the number of profiles and policies to a minimum.
- Granting {{site.data.keyword.powerSys_notm}} permissions for all your VSIs is acceptable in your security policy.

### Option 2: Two separate trusted profiles
{: #separate}

Create two trusted profiles with distinct compute resource identity conditions:

| Profile | Assigned VSIs | Access |
| ------- | ------------- | ------ |
| `sap-backup` | All VSIs that run backup agents (standalone and HA nodes) | COS bucket actions only |
| `sap-ha` | HA cluster VSIs only (primary and secondary nodes) | {{site.data.keyword.powerSys_notm}} permissions (workspace level) |
{: caption="Trusted profile split for backup and HA workloads" caption-side="bottom"}

HA cluster VSIs are associated with both trusted profiles.
This configuration is supported by IAM, as a VSI can satisfy the conditions of multiple trusted profiles and obtain access tokens for each profile independently.
In this model, the backup agent requests an IAM access token associated with the `sap-backup` trusted profile, while the HA resource agent requests a separate token associated with the `sap-ha` trusted profile.
Each token is scoped to its respective trusted profile and grants only the permissions defined by the IAM policies attached to that profile.
This approach enables strict separation of duties by ensuring that backup and HA components use distinct credentials and permissions, even when they run on the same VSI.

Choose this option if:
- Your security policy requires least-privilege access.
- Your HA cluster VSIs are a subset of your total VSI landscape.
- You want to scope changes and audits to individual workloads — for example, a COS bucket change affects only `sap-backup` and does not require updating `sap-ha`.

## Trusted profile usage patterns for SAP workloads
{: #usage-patterns}

### Backup operations
{: #usage-pattern-backup}

SAP HANA backup agents that write to {{site.data.keyword.cos_full_notm}} (COS) require write access to a specific COS bucket. When you use a trusted profile instead of a service ID API key, the backup agent running on the VSI retrieves an IAM token at runtime and uses it to authenticate COS API requests.

Assign the following {{site.data.keyword.cos_full_notm}} actions to the trusted profile for backup and restore operations. These actions map to a custom IAM role scoped to the specific bucket used for SAP HANA backups.

| Action                                                   | Purpose                                         |
| -------------------------------------------------------- | ------------------------------------------------|
| `cloud-object-storage.bucket.head`                       | Verify bucket existence and retrieve metadata   |
| `cloud-object-storage.bucket.get`                        | List objects in the bucket                      |
| `cloud-object-storage.bucket.get_lifecycle`              | Read the bucket lifecycle configuration         |
| `cloud-object-storage.bucket.get_versioning`             | Read the bucket versioning configuration        |
| `cloud-object-storage.bucket.get_versions`               | List all versions of objects in the bucket      |
| `cloud-object-storage.object.head`                       | Retrieve object metadata                        |
| `cloud-object-storage.object.get`                        | Download objects (restore)                      |
| `cloud-object-storage.object.get_version`                | Download a specific version of an object        |
| `cloud-object-storage.object.head_version`               | Retrieve metadata for a specific object version |
| `cloud-object-storage.object.put`                        | Upload objects (backup)                         |
| `cloud-object-storage.object.put_part`                   | Upload a part in a multipart upload             |
| `cloud-object-storage.object.post_initiate_upload`       | Initiate a multipart upload                     |
| `cloud-object-storage.object.post_complete_upload`       | Complete a multipart upload                     |
| `cloud-object-storage.object.put_object_lock_retention`  | Set object lock retention policy                |
| `cloud-object-storage.object.put_object_lock_legal_hold` | Set object lock legal hold                      |
{: caption="Required COS actions for SAP HANA backup trusted profile" caption-side="bottom"}

**To configure a trusted profile for backup:**

1. Create a trusted profile in IAM and add a compute resource identity condition that matches the VSI running the backup agent. For more information, see [Managing access for SAP workloads in Power Virtual Server](/docs/iam?topic=iam-trustedprofile-powervs-tutorial){: external}.
2. Attach an access policy granting Writer access to the COS bucket used for SAP HANA backups.
3. Enable the metadata service on the backup VSI. For more information, see [Configuring and managing the metadata service for SAP on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-powervs-instance-metadata-service).
4. Configure the backup agent to use the IAM token endpoint rather than a static API key.

### HA resource agents
{: #usage-pattern-ha}

High availability resource agents, such as `powervs-move-ip`, require access to the {{site.data.keyword.powerSys_notm}} API to perform operations like moving overlay IP addresses during a failover event. Trusted profiles replace the previously required service ID authorization for these agents.

Assign the following minimum IAM access to the trusted profile for HA resource agents:

For fence agent `fence_ibm_powervs`:

| Action                          | Purpose                                                                                          |
| ------------------------------- | ------------------------------------------------------------------------------------------------ |
| `power-iaas.pvm-instance.action`| trigger state-changing operation on a {{site.data.keyword.powerSys_notm}} instances              |
| `power-iaas.pvm-instance.list`  | lists all {{site.data.keyword.powerSys_notm}} instances within your workspace                    |
| `power-iaas.pvm-instance.read`  | retrieve configuration and status information for a {{site.data.keyword.powerSys_notm}} instance |
{: caption="Minimum IAM access for fence agent fence_ibm_powervs trusted profile" caption-side="bottom"}

For resource agent `powervs-move-ip`:

| Action                   | Purpose                                                                                               |
| -------------------------| ----------------------------------------------------------------------------------------------------- |
| `power-iaas.route.read`  | view routing configurations within an IBM {{site.data.keyword.powerSys_notm}} workspace               |
| `power-iaas.route.update`| modify or update routing configurations within an IBM {{site.data.keyword.powerSys_notm}} workspace   |
{: caption="Minimum IAM access for resource agent powervs-move-ip trusted profile" caption-side="bottom"}

**To configure a trusted profile for HA resource agents:**

1. Create a trusted profile in IAM and add a compute resource identity condition that matches both VSIs in the HA cluster (primary and secondary nodes).
2. Attach an access policy granting access to the {{site.data.keyword.powerSys_notm}} workspaces used by the cluster.
3. Enable the metadata service on both cluster VSIs.
4. Configure the HA resource agent (for example, `powervs-move-ip`) to retrieve its authorization token from the instance metadata service.

For guidance on migrating an existing `powervs-move-ip` setup from service ID authorization to trusted profile authorization, see [Migrating `powervs-move-ip` from service ID to trusted profile authorization](/docs/sap?topic=sap-powervs-ha-rhel-tp-migrate).

## IAM access policies and session limits
{: #access-policies}

IAM access policies attached to a trusted profile determine what actions the VSI can perform. Apply the principle of least privilege: grant only the permissions required for the specific workload running on the VSI.

| Workload | Required {{site.data.keyword.cloud_notm}} service | Access |
| -------- | ------------------------------------------------- | ------ |
| SAP HANA backup and restore to COS | {{site.data.keyword.cos_full_notm}} | Custom role with required COS actions (bucket level) |
| HA overlay IP move | {{site.data.keyword.powerSys_notm}} | Editor (workspace level) |
| Monitoring agent | {{site.data.keyword.mon_full_notm}} | Writer |
{: caption="IAM access policies by SAP workload type" caption-side="bottom"}

You can also set session limits on a trusted profile to restrict how long an IAM access token remains valid. Shorter session limits reduce the window of exposure if a token is compromised.

## Limit trusted profiles for compute resource identities
{: #metadata-limit-trusted-profiles}

Limit the trusted profiles that you create for compute resource identities in IAM. If a VSI does not need a compute resource identity, do not assign a compute resource identity.

You can also update existing trusted profiles by redefining the trust relationship, assigning access policies, and updating session limits. For more information, see [Updating trusted profiles](/docs/iam?topic=iam-trusted-profile-update&interface=ui).

## Related resources
{: #related}

- [Managing access for SAP workloads in Power Virtual Server](/docs/iam?topic=iam-trustedprofile-powervs-tutorial)
- [Managing trusted profiles](/docs/iam?topic=iam-trusted-profile-update&interface=ui)
- [Configuring and managing the metadata service for SAP on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-powervs-instance-metadata-service)
- [Managing identity and access for SAP on IBM {{site.data.keyword.powerSys_notm}}](/docs/sap?topic=sap-powervs-iam)
