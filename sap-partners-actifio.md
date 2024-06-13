---

copyright:
  years: 2020
lastupdated: "2020-09-21"

keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, Veeam Backup & Replication, Actifio GO, Actifio Sky, SAP HANA Backup, SAP NetWeaver Backup, Backups, {{site.data.keyword.cloud_notm}} storage, {{site.data.keyword.blockstorageshort}}, RTO, RPO

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
{:bash: .ph data-hd-programlang='bash'}
{:powershell: .ph data-hd-programlang='PowerShell'}
{:important: .important}


# Actifio for SAP HANA backups
{: #actifio}

**_INCOMPLETE: This page only appears in Test/Staging, it is not complete or ready to be published_**
{: important}

Actifio GO can be provisioned and deployed from the {{site.data.keyword.cloud_notm}} catalog to provide protection for SAP HANA workloads on certified {{site.data.keyword.ibm_cloud_sap}} infrastructure. Actifio performs SAP HANA database snapshot combined with block change tracking to backup incremental block changes of disk mount points used by the database, and is lisited on the [SAP Certified Solutions Directory](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/solutions).
{: shortdesc}

## Actifio GO overview and considerations
{: #actifio-go-overview}

Actifio GO on {{site.data.keyword.cloud_notm}} is the next generation multi-cloud Copy Data Management SaaS solution that allows customers to perform backups to directly to {{site.data.keyword.cloud}} Object Storage (COS); while being able to instantly access the backup images within their on-premises data center.

Actifio GO SaaS is built on Actifio Sky, and uses the patented core Virtual Data Pipeline technology - Actifio VDP. It enables users to provision multiple clones instantly in the cloud, so tests can be done simultaneously on multiple instances and reduce testing cycles.

The Actifio Sky appliances run on a hardened Linux software stack and user accounts and direct access to the operating system are not used for normal operations and support.

Actifio GO SaaS uses Actifio Sky to support multiple on-premises workloads for backup to Cloud:
- File systems
- VMware
- SAP HANA
- IBM Db2
- Microsoft SQL Server
- PostgreSQL
- MySQL
- MongoDB
- Progress

Depending on the backup target, a different method of backup will be performed with Actifio technology:
- For VMware, Actifio GO leverages the patented OnVault technology to incrementally backup VMware data to {{site.data.keyword.cloud}} Object Storage (COS) while still maintaining instant access
- For SAP HANA database server, Actifio GO leverages the native SAP HANA Database Snapshot API backup interfaces combined with block change tracking to backup incremental block changes of disk mount points used by the database; for SAP's own cutting-edge in-memory database, Actifio leverages `backint` interface of the SAP HANA Database Server
- For SAP AnyDB databases such as IBM Db2, Actifio uses the native database API backup interfaces to perform snapshots combined with block change tracking to backup incremental block changes of disk mount points used by the database

Actifio GO is able to provision for all {{site.data.keyword.cloud_notm}} infrastructure. However, at time of writing, the regular pricing plan is only available to use with {{site.data.keyword.cloud}} for VMware Solutions Dedicated. For pricing related to SAP, including for databases (e.g. SAP HANA, IBM Db2, MS SQL, etc.) and other compute types (e.g. physical servers) `please contact info@actifio.com`.
{: note}

When using Actifo GO with {{site.data.keyword.cloud}} for VMware Solutions Dedicated, the Virtual machines associated with a VMware host are discovered through the Application Manager on the Actifio Global Manager (AGM) and can be backed up using either VMware snapshots or the Actifio Connector. However for SAP Workloads, this is performed at the database-level and there is not an auto-detect for the databases which could be backed up via Actifio GO to the defined {{site.data.keyword.cloud}} Object Storage.
{: note}


## Actifio GO benefits for SAP Workloads
{: #actifio-go-benefits}

Benefits include:

- **Incremental Forever Backup** - Efficient incremental forever backup which reduces backup window (RPO/RTO), optimizes bandwidth and cloud storage utilization
- **Direct to Cloud Object Storage** - Incremental forever backups go directly to {{site.data.keyword.cloud}} Object Storage, eliminating any on-premises data center storage needs and the operational burden for capacity management (and zero network bandwidth charges using {{site.data.keyword.cloud_notm}}'s private network backbone)
- **Instant testing from Cloud** - Instantly begin clone deployments from {{site.data.keyword.cloud}} Object Storage to begin isolated testing quickly or point-in-time data science analysis
- **Data Encryption** - Data is encrypted in transit and at rest in {{site.data.keyword.cloud}} Object Storage

Actifio can be used in various SAP project implementations:
- for SAP Systems Sandbox and Regression Testing
- testing SAP workloads move from on-premises to Cloud environment
- testing validation of SAP workload performance in a Cloud environment
- running conversion trial projects to the newest SAP software releases (e.g. SAP ECC > SAP S/4HANA)


## Solution overview
{: #actifio-go-solution-overview}

The diagram below shows the solution overview of Actifio GO:

![Overview diagram](actifiogo-overview.svg)

The solution consists of the following components:

- **Source host** - the on-premises host running an SAP workload, to backup and restore on Cloud
- **Actifio GO SaaS Cloud** - provides the registration, deployment, reporting and enables Enterprises to set up and monitor SLAs
- **Actifio Global Manager (AGM)** - the manager is hosted in the Actifio GO SaaS Cloud and provides centralized management capabilities, via web-based UI, to manage multiple Actifio Sky appliances. The AGM can manage up to 40 Actifio Sky appliances and communicates with each Actifio appliance over the Internet via a bi-directional link set up by the appliance. This requires firewall rule changes and a outbound internet access e.g. via SNAT or Public Internet Gateway; for example, with {{site.data.keyword.cloud}} for VMware Solutions Dedicated vCenter Server instances this would be achieved using a firewall rule and a SNAT entry on the customer-ESG in a vCenter Server instance
- **Windows Server host** - a Windows server is required to download and run the Actifio GO installer, which is only available as a Windows executable. When the installer is executed, additional files are downloaded and the Actifio Sky appliance (separate host) is installed. This requires firewall rule changes and a outbound internet access, including specific ports opened to allow licensing validation, installation and post-install configuration
- **Actifio Sky host** - the data mover appliance is downloaded and installed via the Actifio GO intaller on Windows Server. The Actifio Sky host is on both the source (on-premises hosts) and the target (Cloud hosts) environments
- **DNS Server** - the DNS server must be able to resolve external FQDN such as [actifiogo.com](http://actifiogo.com) and resolve internal FQDN within the landscape on {{site.data.keyword.cloud_notm}}
- **Optional: NTP Server** - the Actifio Sky appliance can be configured to use a specific NTP server

## Process overview
{: #actifio-go-process-overview}

The process is as follows, it assumes you have an on-premises VMware environment and an {{site.data.keyword.cloud}} for VMware Solutions Dedicated vCenter Server instance:

### Initial setup
{: #actifio-go-initial-setup}

1. Purchase Actifio GO from the {{site.data.keyword.cloud_notm}} catalog. An account is automatically created for the provisioning user. The Actifio GO service dashboard on the {{site.data.keyword.cloud_notm}} Catalog will present the user with temporary credentials to access the service. The temporary credentials can be accessed at any time from the service dashboard of the {{site.data.keyword.cloud_notm}} resource listing page.
2. Purchase {{site.data.keyword.cloud}} Object Storage (COS) from the {{site.data.keyword.cloud_notm}} catalog.

### On-premises setup
{: #actifio-go-on-premises-setup}

1. Configure the networks to allow the required traffic flow.
2. Provision or use an existing Windows virtual machine as a jump server.
3. Enable the Windows Jump server to allow incoming connections on port 5420.
4. Browse to [Actifio GO](https://www.actifiogo.com) and using the temporary credentials generated as part of the provisioning process, log into the service.
5. Change credentials.
6. Navigate to the quickstart page.
7. Click on the Get Ahead tile to be presented with a page to download the installer.
8. The installer guides the user through the provisioning steps:
   - The Actifio ova is downloaded.
   - Provide the on-premises vCenter credentials to deploy the Actifio Sky appliance.
   - Provide the required on premises network information.
   - Provide the IBM Cloud Object Storage credentials, and enter the public endpoint URL.
   - The installer will provision the Actifio Sky appliance.
9. Using the Actifio Global Manager, select the virtual machines you want to backup and restore to Cloud, which will use IBM Cloud Object Storage to store the backups. The Actifio GO service includes a default OnVault Policy and Resource Profiles. These can be adjusted as required. The OnVault policy will run according to its schedule and the selected virtual machines will be written to the OnVault Pool, IBM Cloud Object Storage bucket, specified in the Resource Profile.

### vCenter Server instance setup
{: #actifio-go-vcenter-server-instance-setup}

1. Configure the networks to allow the required traffic flow.
2. Provision or use an existing Windows virtual machine as a jump server.
3. Enable the Windows Jump server to allow incoming connections on port 5420.
4. Browse to [Actifio GO](https://www.actifiogo.com), log into the service.
5. Navigate to the quickstart page.
6. Click on the Get Ahead tile to be presented with a page to download the installer.
7. The installer guides the user through the provisioning steps:
   - The Actifio ova is downloaded.
   - Provide the on-premises vCenter credentials to deploy the Actifio Sky appliance.
   - Provide the required on premises network information.
   - Provide the IBM Cloud Object Storage credentials, and enter the private endpoint URL.
   - The installer will provision the Actifio Sky appliance.

### Backup and Restore
{: #actifio-go-backup-restore}

1. After the first on-premises capture operation has completed, the backup of the virtual machines to backup and restore will be stored as images in the OnVault Pool's object storage location ready to be accessed by the Actifio Sky appliance in the vCenter Server instance.
2. Using AGM, users can import OnVault images from one managed Actifio appliance to another. An image that has been imported to another Actifio appliance can be mounted to that appliance's application hosts.
3. Images can be imported from the AGM Domain Manager's Storage Pool page or the AGM Application Manager's Applications page. Importing images from the Domain Manager's Storage Pool page has the advantage of allowing you to select multiple application images. To import images from the Domain Manager's Storage Pool page:
   - Right click on an OnVault Storage Pool and from the drop down menu select Import Images and the Import OnVault Images page is displayed.
   - Select the Actifio Sky appliance to which the images will be imported.
   - Select the images to import.
   - Click Import and the import operation will begin. A message will be displayed when the operation completes.
4. Actifio GO provides the following options;
   - Restore - Recovers the image to the existing virtual machine. This option is not suitable for backup and restore to {{site.data.keyword.cloud_notm}}.
   - Mount - The image can be mounted to an existing host or a new VM. When mounting as new VM, the VM version, Guest Id, number of CPUs, memory, hardware details are preserved. The images are presented as either NFS, vRDM or pRDM disks to the new virtual machines. The Actifio Sky appliance acts as a gateway to the image. The image is not copied to the datastore. This can be useful for testing the move to Cloud but not for production workload migration to {{site.data.keyword.cloud_notm}}.
   - Clone - The image can be used to clone to a source or target VMware environment. Select the vCenter in {{site.data.keyword.cloud_notm}}, and a clone of the on-premises VM, the VM version, Guest Id, number of CPUs, memory and Hardware details are cloned and the network left disconnected and the virtual machine is powered off.
   - Recover System - This recovers the virtual machine to either AWS, Azure or GCP. This option is not suitable for {{site.data.keyword.cloud_notm}}.
5. Using the Clone option, clone a virtual machine in the vCenter Server instance, connect to the required network and power on.
6. Carry out the required testing.
7. Upon successful testing, power off the on-premises virtual machine and make the virtual machine in {{site.data.keyword.cloud_notm}} accessible.

Note, this is an overview of the backup and restore process. To use this process for production workload migration, then use of the native Database Snapshot API backup interfaces is required instead and a full migration plan with detailed tasks is required, with required testing and SAP cut-over process (including post-copy processing).

As we are using a clone of a copy of data for testing use case only, multiple tests can be carried out with no impact to production. the cloned virtual machine can be placed in an isolated network to not impact the production instance of SAP.


## Actifio Sky Appliance
{: #actifio-sky-appliance}

Actifio Sky performs only one full backup of the source device. All subsequent backups rely on incremental changes only due to the CBT feature. Actifio Sky synthesizes a point-in-time virtual full backup from the incremental backups. An OnVault policy defines when data is captured, the frequency it will be captured, and how long it will be retained and are created via the AGM. Resource Profiles define which Actifio pools will be used to retain application data and are created via the AGM.

### Actifio Connector
{: #actifio-connector}

In this use case an Actifio Connector was not used. The Actifio connector is a lightweight connector that incorporates change-block tracking (CBT). The connector automatically provides a list of changed blocks when backups are initiated, minimizing the amount of CPU, storage, and memory required. The Actifio Connector is deployed on the source device and invokes the appropriate APIs to ensure that all backups are application consistent. A connector is required to discover applications on the host or VM and to interact with the applications API. VMware virtual machines can be backed up without an Actifio Connector. This provide crash consistent backups or some level of application consistency with VM Tools integration. Additionally, vSphere ESXI hosts do not need to be be enabled for iSCSI. Actifio Connector based protection, provides better application consistency and application aware restore functionality, again if the application is hosted on VMware virtual machine iSCSI is not required.

The Actifio appliance comes with different connector installer files. Each is of a file type appropriate to its intended host type. You can download these with a web browser from the Actifio Resource Center; just open a browser to the IP address of the appliance. Traffic between the Actifio Sky appliance and the connector on the SAP host is encrypted and communicated via SSL. The Actifio Connector uses port 5106 by default for bidirectional communication from the Actifio SKY appliance. The flow is as follows:

- The Actifio connector uses the SAP HANA HDB SQL API to create a snapshot to flush data and memory to disk.
- The connector creates a snapshot of the SAP HANA data area disk and deletes the native SAP HANA snapshot, freeing up the HANA database for new reads and writes.
- Actifio Sky mounts a volume to the SAP HANA server and the connector leverages its changed block tracking bitmap to back up the changed blocks from the data area disk snapshot to the mounted volume.
- When the incremental backup is done, Actifio Sky unmounts the volume and deletes the data area disk snapshot.
- Actifio Sky issues an internal software snapshot and synthesizes a point in time virtual full backup copy.

Now that we have a replicated copy, the admin invokes the cloning process. To clone an SAP HANA database, the user triggers an Actifio workflow, that will automatically present a clone on-demand, which can be presented to the target server for instant access. Once the SAP HANA clone is presented by the connector to the target server, the connector will mount the disc volume containing the clone. The connector will then bring the database online using native SAP HANA functionality, and the server will have a complete read/write enabled virtual full SAP HANA database ready for testing.

### Actifio GO installer requirements
{: #actifio-go-installer-requirements}

During the install you are presented with the following requirements:

- You have access to a VMware vCenter wherein you are about to deploy an Actifio virtual appliance.
- Your VMware vCenter Server User ID has sufficient privileges to perform these required operations.
- Your VMware vCenter Server has access to a datastore with at least 250GB of free space.
- Your ESXi systems are configured with iSCSI technology for the Actifio appliance to access remote storage using the IP network - This requirement is only required if using the Actifio Connector and certain uses cases. It is NOT required for this use case.
- On this machine where the Actifio Appliance Installer is running, Port 5420 is open for SSL communication with your VMware vCenter Server.
- The network you choose to deploy Actifio Sky on allows outbound communication via TCP port 1194 which is required to ensure that the vaulting Sky can be securely managed via Actifio cloud.
- Based on Windows compatibility, install Microsoft Net Framework of versions 3.5 and above.
- You have at least 6 GB of free space on the local disk where this Actifio Appliance Installer application is running.

### Network Ports
{: #actifio-go-network-ports}

The following network traffic is required between the various components:

![Overview diagram](actifiogo-ports.svg)

| Destination Port Protocol(s) | Source IP Address | Destination IP Address | Description |
| --- | --- | --- | --- |
| 3389 (TCP) MS RDP | User device | Jump-server | MS RDP access from the user's device to the jump-server. |
| 80 (TCP) HTTP | Jump-server | Actifio Appliance | Installation post deployment. |
| 443 (TCP) HTTPS | Jump-server | Actifio Appliance | Download of the Actifio Desktop and Connector software. |
| 53 (UDP) DNS | Jump-server | DNS server | Resolve IP addresses. |
| 443 (TCP) HTTPS | Jump-server | vCenter Server IP | To install the appliance ova. |
| 443 (TCP) HTTPS | Jump-server | AGM server | Web browser access to AGM. |
| 53 (UDP) DNS | Actifio Appliance | DNS server | Resolve IP addresses. |
| 123 (UDP) NTP | Actifio Appliance | NTP server | NTP. |
| 443 (TCP) HTTPS | Actifio Appliance | vCenter Server IP | Snapshot and image management |
| 443 (TCP) HTTPS | Actifio Appliance | IBM Cloud Object Storage Endpoint | Actifio OnVault cloud data transfer |
| 902 (TCP) | Actifio Appliance | ESX Server VMKernel IPs | Encrypted connectivity to VMware ESXi hosts for data movement operations. |
| 5420 (TCP) | Actifio Appliance | vCenter Server IP | Licence server details from the installer. |
| 1194 (TCP) | Actifio Appliance | AGM Server | Secure communications between Actifio Sky appliance and the AGM. |



If Actifio Connectors are used the following network traffic is required between the various components:

| Destination Port Protocol(s) | Source IP Address | Destination IP Address | Description |
| --- | --- | --- | --- |
| 5106 (TCP) Actifio API | Actifio Appliance | Host Servers | Encrypted control channel for hosts with Actifio connector. |



If iSCSI ise used the following network traffic is required between the various components:

| Destination Port Protocol(s) | Source IP Address | Destination IP Address | Description |
| --- | --- | --- | --- |
| 3205 and 3260 (TCP) iSCSI | Host servers | Actifio Appliance | iSCSI target |



### On-premises information
{: #actifio-go-on-premises-information}

| Parameter | Notes |
| --- | --- |
| Cluster\resource pool | As required, e.g. cluster1. |
| Datastore | As required, e.g. vsanDatastore. |
| Network | As required. |
| Sky Appliance name | As required, e.g. actifiosrc. |
| Sky Appliance IP | Select a free IP address. |
| Gateway | The customer edge private interface IP address for the portable subnet for customer workloads. |
| DNS | The IP address iof the on-premises DNS. |
| Mask | The network mask of the subnet hSky Appliance IP e.g. 255.255.255.0. |
| NTP | Optional. |
| Time Zone | As required. |
| Bucket Name | The bucket name used in the IBM Cloud Object Storage order e.g. actifio01 |
| Access Key ID | Available from the IBM Cloud Object Storage credentials web page. |
| Secret Access Key | Available from the IBM Cloud Object Storage credentials web page. |
| Public Region Endpoint | Available from the IBM Cloud Object Storage service page. |
| vCenter Credentials |  |
| vCenter IP/FQDN |  |


### vCenter Server instance information
{: #actifio-go-vcenter-server-instance-information}

The following information will need to be collected for use by, the installation of the jump server, Actifio GO installer or for use in configuring NAT and firewall rules in the customer-esg. It assumes that you will use the default customer VXLAN, DLR and ESG configured by the automation:

| Parameter | Notes |
| --- | --- |
| Jump server IP | Select a free IP address from the workload BYOIP range. e.g. 192.168.10.168 |
| Jump server IP Public | This uses the existing secondary IP address configured from the public portable subnet for customer workload edge IP range. |
| Jump server IP Private | Select a free IP address from the private portable subnet for customer workload edge IP range. e.g. 10.134.217.198 |
| Private subnets | The {{site.data.keyword.cloud_notm}} private network subnets that you want to allow to connect the jump-server. |
| Cluster\resource pool | As required, e.g. cluster1. |
| Datastore | As required, e.g. vsanDatastore. |
| Network | The network should be; vxw-dvs-22-virtualwire-3-sid-6002-Workload; if the workload BYOIP subnet is being used. |
| Sky Appliance name | As required, e.g. actifiotgt. |
| Sky Appliance IP | Select a free IP address from the workload BYOIP range, e.g. 192.168.10.169 |
| Gateway | The customer edge private interface IP address for the portable subnet for customer workloads, e.g. 192.168.10.1 |
| DNS | The IP address is published in the {{site.data.keyword.cloud}} for VMware Solutions Dedicated console. |
| Mask | The private portable subnet for customer workloads subnet has a network mask of 255.255.255.0. |
| NTP | The {{site.data.keyword.cloud_notm}} time server; 10.0.77.54 |
| Time Zone | As required. |
| Bucket Name | The bucket name used in the IBM Cloud Object Storage order e.g. actifio01 |
| Access Key ID | Available from the IBM Cloud Object Storage credentials web page. |
| Secret Access Key | Available from the IBM Cloud Object Storage credentials web page. |
| Private Region Endpoint | Available from the IBM Cloud Object Storage service page. |
| vCenter credentials | Available from the {{site.data.keyword.cloud}} for VMware Solutions Dedicated console. |
| vCenter IP/FQDN | Available from the {{site.data.keyword.cloud}} for VMware Solutions Dedicated console. |


The diagram below shows a network overview for the vCenter Server instance, the Network Address Translation (NAT) and Firewalls are described in the sections below.

![Network overview diagram](actifiogo-natfw.svg)

### Network address translation
{: #actifio-go-network-address-translation}

NAT needs to be configured to allow the following:

- The automation configures a SNAT rule, SNAT for customer VMs, that translates customer workload IP addresses to a secondary public IP address on the customer-esg. This rule may need to be enabled. This NAT will allow the jump server and the Actifio Sky appliance access to Internet resources such as Actifio GO SaaS.
- To make the jump server accessible from the {{site.data.keyword.cloud_notm}} private network, a DNAT is required to translate a secondary private IP address on the customer-esg to the BYOIP address used on the jump server. e.g. DNAT, applied on Private Uplink destination IP 10.134.217.198, translated IP 192.168.10.168.
- To alow the jump server access to DNS and vCenter, and the Actifio Sky appliance access to DNS, NTP, IBM Cloud Object Storage private endpoint, vCenter and the vSphere ESXi hosts, a SNAT is required to translate the BYOIP address used on the appliance to a secondary private IP address on the customer-esg. e.g. SNAT, applied on Private Uplink, source IP 192.168.0.0/16 translated IP 10.134.217.197.


### Firewall rules
{: #actifio-go-firewall-rules}

Firewall rules are required to allow traffic into and out of the subnets protected by the customer-esg. The following rules are required:

- The vCenter Server automation configures the, All outgoing customer VMs rule, with a source 192.168.0.0/16, destination any, and action accept. This will allow the jump server and Actifio Sky appliance access to resources on the public and private networks.
- A rule to allow access to the jump server is required. e.g source 10.0.0.0/8, destination 10.134.217.198, and action accept.


### Jump-server
{: #actifio-go-jump-server}

Install Microsoft Net Framework of versions 3.5 and above using Windows Server Manager, add roles and features. Using the following PowerShell command to open the Windows firewall to allow the Actifio Sky appliance to get a licence:

```powershell
netsh advfirewall firewall add rule name="Open Port 5420 In” dir=in action=allow protocol=TCP localport=5420
```
{: codeblock}


### Related links
{: #related-links}

- [Actifio](https://www.actifio.com/)
- [About Actifio GO](/docs/Actifio-GO)
- [Actifio Global Manager (AGM)](http://docs.actifio.com/8.0/AGM/Introduction.htm#AGM_UG/AGM_Preface.htm#agm_preface_1570682960_1155428%3FTocPath%3DPreface%7C_____0)
- [Installing an Actifio Sky Appliance on a VMware Server](http://docs.actifio.com/9.0/PDFs/SkyInstallVMware.pdf)
- [Network Administrator's Guide to Actifio Copy Data Management](http://docs.actifio.com/9.0/PDFs/NetworkConfiguration.pdf)
- [Actifio Global Manager getting started](http://docs.actifio.com/9.0/AGM/Introduction.htm#AGM_UG/Introduction.htm#intro_935409780_1119600%3FTocPath%3DGetting%2520Started%7C_____0)
- [Connecting Hosts to Actifio Appliances](http://docs.actifio.com/9.0/PDFs/Hosts.pdf)
- [Configuring Actifio OnVault](http://docs.actifio.com/9.0/PDFs/ActifioOnVaultConfiguration.pdf)
- [Accessing and Recovering Copy Data with the Application Manager](http://docs.actifio.com/9.0/PDFs/AppManagerAccess.pdf)
- [Actifio data security](http://docs.actifio.com/9.0/PDFs/DataSecurity.pdf)
