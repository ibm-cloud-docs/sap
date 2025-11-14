---
copyright:
  years: 2025
lastupdated: "2025-11-11"
keywords: IBM Cloud for SAP, SAP on {{site.data.keyword.powerSysShort}}
subcollection: sap
content-type: conref
---

{{site.data.keyword.attribute-definition-list}}

# Content references for SAP subcollection
{: #conref-sap}



CONTENT:

- [Create a VPC](/docs/vpc?topic=vpc-getting-started&interface=ui) or use an existing one.
- Create a subnet in the VPC for your preferred zone.
- Create an IBM {{site.data.keyword.powerSys_notm}} workspace in the IBM Cloud region.
- Create a Transit Gateway and attach the VPC subnet and the IBM {{site.data.keyword.powerSys_notm}} workspace to the Transit Gateway.
- Check the user permissions.
   Make sure that your user account has permissions to create and manage VPC resources.
   See [Granting user permissions for VPC resources](/docs/vpc?topic=vpc-managing-user-permissions-for-vpc-resources).
- Use or create an SSH key to connect to the virtual server instances.
   If you don't have an SSH key, see [Getting started with SSH keys](/docs/vpc?topic=vpc-ssh-keys).
{: #nfs-zonal-prereqs}






CONTENT:

Create a security group and configure inbound rules for port 2049.
1. Go to [Security groups for VPC](/infrastructure/network/securityGroups) and click **Create**.
1. Verify or set the **Geography** and **Region** fields.
1. Enter `nfs-server-sg` for the **Name**.
1. Select the same **Resource group** as the VPC resource group.
1. Select your VPC in the **Virtual private cloud** list.
1. Create an inbound rule for each virtual server instance (NFS client).
   1. In the **Inbound rules** section, click **Create**.
   1. Configure the rule as follows:
      - **Protocol**: Select **TCP**.
      - **Port**: Select **Port range**, and enter `2049` for both **Port min** and **Port max**.
      - **Source type**: Select **IP or CIDR**.
         - Enter the IP address of each virtual server instance.
         - If your instances are in a subnet and you want to allow access from all members of the subnet, enter the subnet's CIDR block.
      - **Destination type**: Select **Any**.
   1. Configure the rule as follows in addition to above when provisioning a mount target with in-transit encryption [EIT]{: tag-green}:
      - **Protocol**: Select **TCP**.
      - **Port**: Select **Port range**, and enter `20049` for both **Port min** and **Port max**.
      - **Source type**: Select **IP or CIDR**.
         - Enter the IP address of each virtual server instance.
         - If your instances are in a subnet and you want to allow access from all members of the subnet, enter the subnet's CIDR block.
      - **Destination type**: Select **Any**.
   1. Click **Create** to save the rule.
1. Create a common outbound rule.
   1. In the **Outbound rules** section, click **Create**.
   1. Configure the rule as follows:
      - **Protocol**: Select **TCP**.
      - **Port**: Select **Any**.
      - **Destination type**: Select **Any**.
      - **Source type**: Select **Any**.
   1. Click **Create** to save the rule.
1. Finalize the security group.
   - Click **Create security group** to apply the configuration.
{: #nfs-create-sg}






CONTENT:

1. Go to [File storage shares for VPC](/infrastructure/storage/fileShares).
1. Click **Create** > **Create file share**.
1. In the **Availability** section, choose `Single Zone` availability.
1. In the **Location** section, select the same **Geography**, **Region**, and **Zone** as the virtual private cloud.
1. Enter `nfs-server` in the **Name** field.
   Select the same **Resource group** as the VPC resource group.
1. In the **Size** section, enter the `Storage size` in GB.
1. Enter a `Max IOPS` value.
1. In the **Mount target access mode** section, select **Security groups**.
1. In the **Mount targets (optional)** section, click **Create**.
   - Enter `nfs-server-mount-target`in the **Mount target name** field.
   - Select your **VPC**.
   - In the **Network interfaces** section, click the pencil icon on the new interface.
   - Verify the selected subnet and click **Next**.
   - In the **Security groups** section, check the `nfs-server-sg` security group, clear the **VPC default** security group, and click **Next**.
   - Click **Next** to get to the **Review** section, then click **Save**.
   - Back on the **Create mount target screen**, click **Next**.
   - **Encryption in transit** is disabled by default. **If required** click the toggle to change the preset value. For more information about this feature, see Encryption in transit - [Securing mount connections between file share and host](/docs/vpc?topic=vpc-file-storage-vpc-eit).
   - In the **Review** step, click **Create**.
1. Click **Create file share** to provision the file storage and the mount target.
{: #nfs-create-zonal-share}






CONTENT:

1. Go to [File storage shares for VPC](/infrastructure/storage/fileShares).
1. Click **Create** > **Create file share**.
1. In the **Availability** section, choose `Regional` availability.
1. In the **Location** section, select the **Geography** and the multizone **Region** (MZR).
1. In the **Details** section, enter `nfs-server` in the **Name** field.
   Select the same **Resource group** as the VPC resource group.
   - The profile is chosen automatically based on your data availability selection.
      If you chose regional availability, your file share uses the **rfs** profile.
   - Select the **Size** and **Max throughput** for your file share.
      You can increase the capacity later, and you can also adjust the bandwidth as needed.
   The **Mount target access mode** is always **Security groups**.
   The previous access mode `Virtual private cloud` is not available for file storage with regional availability.
1. In the **Mount targets (optional)** section, click **Create**.
   - In the **Mount target details** section, enter `nfs-server-mount-target` in the **Mount target name** field.
   - Select your **VPC**.
   - In the **Network interfaces** section, click the pencil icon on the new interface.
      Verify the selected subnet and click **Next**.
   - In the **Security groups** section, check the `nfs-server-sg` security group, clear the **VPC default** security group, and click **Next**.
   - Click **Next** to get to the **Review** section, then click **Save**.
1. Click **Next** when you got back to the **Create mount target screen**.
   - **Encryption in transit** is disabled by default. **If required** click the toggle to change the preset value. For more information about this feature, see Encryption in transit - [Securing mount connections between file share and host](/docs/vpc?topic=vpc-file-storage-vpc-eit).
   - In the **Review** step, click **Create**.
1. Click **Create file share** on the right to provision the file storage and the mount target.
{: #nfs-create-regional-share}






CONTENT:

To support routing mode, you must first create a *service-to-service* authentication policy for your NLB.
1. Log in to [IBM Access Management](/iam/authorizations/grant).
1. Click **Authorizations**, then click **Create**.
1. Select **This account** for **Source account** and click **Next**.
1. Select **VPC Infrastructure Services** for **Service** and click **Next**.
1. Select **Specific resources** > **Resource Type** > **Load Balancer for VPC** for the scope access and click **Next**.
1. Select **VPC Infrastructure Services** for the target service and click **Next**.
1. Select **Specific resources** > **Resource Type** > **Virtual Private Cloud** for the scope access and click **Next**.
1. Select the **Editor** checkbox to grant the Editor access role.
1. Click **Authorize**.
{: #nfs-create-auth-policy}






CONTENT:

Customize the **Ingress routes** to route incoming traffic from external sources such as the IBM Cloud Transit Gateway.
Only one custom routing table is associated with an ingress source.
If an ingress routing table exists for the IBM Cloud Transit Gateway source, add the **route** to that table.
1. Go to [Routing tables for VPC](/infrastructure/network/routingTables).
1. Click **Create**.
1. In the **Location** section, select the same **Geography** and **Region** that is used for the virtual private cloud.
1. Enter `nfs-server-routing` in the **Name** field.
1. Select your VPC in the **Virtual private cloud** list.
1. Enable the **Transit gateway** flag in the **Traffic source (optional)** section.
1. Click **Create routing table** to provision the routing table.
{: #nfs-create-routing-table}






CONTENT:

To propagate routes outside the VPC address prefix range, enable **Advertise to** for the transit gateway.
1. Go to [Routing tables for VPC](/infrastructure/network/routingTables).
1. Click on the name of the ingress route that you created earlier.
1. In the **Traffic** section, click **Edit** to open the **Edit traffic** panel.
1. Under **Traffic source**, expand the **Transit Gateway** section.
1. Toggle the **Advertise to** switch to **On**.
1. Click **Save** to apply the changes to the routing table.
{: #nfs-update-routing-table}






CONTENT:

1. Go to [File storage shares for VPC](/infrastructure/storage/fileShares).
1. Click the **Name** `nfs-server`.
1. In the **Mount targets** section, click the **Name** of the mount target in the VPC to view the mount target details.
1. Make a note of the **Mount path**.
      In the example, the mount path is `10.20.30.40:/73a1ff96_4861_4463_aa09_8c8128b8e277fsf`.
      The first part of the mount path is the **Primary IP** of the mount target: `10.20.30.40` .
{: #nfs-get-mount-path}






**End of Marketing**: As of 31 October 2025, new deployments of VMware Solutions offerings are no longer available for new customers. Existing customers can still use and expand their active VMware® workloads on {{site.data.keyword.cloud}}. For more information, see [End of Marketing for VMware on {{site.data.keyword.cloud_notm}}](/docs/vmwaresolutions?topic=vmwaresolutions-eos-vms).
{: note}
{: #vms-deprecated-note}
