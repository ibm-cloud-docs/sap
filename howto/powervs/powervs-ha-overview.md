---
copyright:
  years: 2023, 2026
lastupdated: "2026-01-23"
keywords: SAP, {{site.data.keyword.cloud_notm}}, SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, SAP HANA System Replication, High Availability, HA, Linux, Pacemaker, RHEL HA AddOn
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Overview: High availability for SAP applications on IBM {{site.data.keyword.powerSys_notm}}
{: #ha-overview}

Running SAP on {{site.data.keyword.powerSysFull}} offers a consistent platform for SAP HANA and traditional applications, delivering world-class performance, resiliency for critical workloads, and flexible infrastructure options.
{: shortdesc}

Use the following information to learn how to implement high availability solutions for SAP systems on {{site.data.keyword.powerSys_notm}} instances.

## SAP system architecture
{: #ha-overview-architecture}

The main components of an SAP system are as follows.

SAP HANA system
:   The SAP HANA system hosts the tenant database that stores data for SAP applications.

SAP application server
:   SAP application servers provide the functional components of an SAP S/4HANA or other SAP application solution.
    All customization and application data is stored in the tenant database of the SAP HANA system.

    An SAP application system is installed and configured as a single unit.
    It consists of the following application instances.

    - One ABAP System Central Services instance (ASCS instance)
      - Each SAP application system includes one ASCS instance, which contains a message server and an enqueue server.

    - One or more application server instances (AS instances)
      - The primary application server (PAS) is the first AS instance that is installed for an ABAP system.
      - Additional AS instances are called additional application servers (AAS).

    Both the application server instances and the ASCS instance require read and write access to a shared file system.

Shared file system
:   The shared file system is typically exported from a Network File System (NFS) server and mounted on all AS instances.

Figure 1 shows the technical components of an SAP system.

![Figure 1. Technical components for SAP systems](../../images/powervs-ha-technical-components.svg "Technical components for SAP systems"){: caption="Technical components for SAP systems" caption-side="bottom"}

## SAP high availability solution considerations
{: #ha-overview-ha-considerations}

To ensure high availability, install application servers redundantly.
Deploy at least two application servers (PAS and AAS) and configure logon groups for load balancing.
If an application server fails, all active user sessions on that instance are ended.
Users must log in again, and load balancing redirects them to another running application server.

Other technical components, including the SAP HANA database, ASCS instance, and shared file system, are single points of failure and require protection.

### SAP HANA system protection
{: #ha-overview-ha-hana}

To protect an SAP HANA system, deploy a second SAP HANA instance on a separate virtual server, enable SAP HANA system replication, and use high availability (HA) cluster software for automated failover.
The solution depends on the recovery time objective (RTO).


| Scenario                      | Typical RTO           | Comment        |
| ----------------------------- | --------------------- | -------------- |
| Performance Optimized         | A few minutes         | This scenario is used by default unless specific requirements apply. |
| Active/Active (read enabled)  | A few minutes         | In an Active/Active (read enabled) configuration, SAP HANA system replication enables read access to database content on the secondary system. |
| Cost Optimized                | A few dozen minutes   | In a cost-optimized configuration, a nonproduction SAP HANA system runs on the secondary node during normal operation. Hardware resources are shared between the nonproduction system and the replication secondary. Memory consumption is reduced by disabling the column table data preload. During the failover process, the nonproduction instance is stopped before the node takes over the production workload. This approach increases takeover time compared to a performance-optimized configuration.|
{: caption="Variations for high availability solutions for SAP HANA" caption-side="bottom"}



### SAP ASCS instance protection
{: #ha-overview-ha-ascs}

To protect the ASCS instance, deploy an Enqueue Replication Server (ERS) instance on a separate virtual server and use HA cluster software for automated failover.

Install the ASCS and ERS instances on a shared disk that is attached to both servers or on an NFS file system.

The enqueue server in the ASCS instance manages the lock table, while the ERS maintains a replicated copy in its memory.
If the enqueue server is restarted, the lock table is rebuilt from the ERS copy, preserving all locks.

A restart of the message server is sufficient because no data needs to be retained.

### Shared file system protection
{: #ha-overview-ha-shared}

To protect the shared file system, create [File storage shares for VPC](/docs/vpc?topic=vpc-file-storage-vpc-about).
File Storage for {{site.data.keyword.filestorage_vpc_full}} is designed for high availability and offers profiles for zonal and regional deployments.
Mount the file system on your {{site.data.keyword.powerSys_notm}} instances by following the steps in [Accessing file storage shares for VPC](/docs/sap?group=file-storage-shares-for-vpc){: external}.

## Overview of high availability clusters
{: #ha-overview-ha-cluster}

An HA cluster uses virtual server instances to provide high availability for the SAP system.
Critical SAP components run on multiple instances within the cluster.
Although HA cluster software does not completely eliminate downtime, it manages and automates failover processes to help maintain service continuity and minimize disruption when a failover occurs.

In a nonclustered deployment, a failure of a critical component or its underlying virtual server instance causes a complete SAP system outage.
The administrator must diagnose the issue, fix it, and restart the failed component.

In an HA cluster, the system automatically detects hardware or software faults.
When a fault occurs, HA cluster software restarts the affected component on a healthy virtual server instance.
This process, which is known as failover, occurs automatically without administrator intervention, reducing recovery time compared to manual recovery procedures.
This setup helps maintain high availability for the SAP system when an application component or virtual server instance fails.

### Key components in an HA cluster
{: #ha-overview-ha-cluster-key-components}

To run critical application components on multiple cluster nodes, the following elements typically play a role in enabling high availability:

Shared storage
: Cluster nodes require access to the same storage.
   During failover, another node can start the failed component because it can access the required files and data.

Service IP address
: Client nodes communicate with the application component through a fixed service IP address, which is assigned to the node where the component is running.

Resource agents
: A resource agent abstracts cluster management for the application component.
   It defines the logic for starting, stopping, and monitoring the component.

### Cluster monitoring and fault detection
{: #ha-overview-ha-cluster-fault-detection}

An HA cluster uses a heartbeat mechanism to monitor node health and status.

The cluster software must prevent a split-brain scenario, which occurs when all heartbeat communication fails while nodes remain active.
In this situation, multiple nodes might incorrectly assume that they are active and start duplicate resources, causing data corruption.

### Quorum and node isolation
{: #ha-overview-ha-cluster-quorum}

Quorum determines whether nodes can safely continue operating and managing resources.
Nodes that do not have quorum must be stopped or isolated to prevent a split-brain scenario.

### Fencing mechanisms
{: #ha-overview-ha-cluster-fencing}

Fencing isolates nodes without quorum or when a resource cannot be stopped normally, ensuring the resource can start safely on another node.
Two common fencing methods include:

Power fencing
: A *fence_agent* controls power devices, hardware management consoles, or hypervisor APIs to forcibly power off or restart a node.
   Powering off the node ensures it cannot access shared resources.

   Power fencing provides reliable isolation and ensures that the node is powered off.
   Control occurs at the hardware level, independent of the operating system or cluster software state.
   However, power cycling takes time, and configuring fence agents requires a more complex setup.

SBD (STONITH Block Device) poison-pill fencing
: A shared storage device is accessible to all nodes.
   Nodes write a poison pill to the disk to signal another node to shut down.

   The `sbd` daemon on the target node reads the poison pill and triggers a restart or shutdown.
   A watchdog is required for this method.
   If the `sbd` daemon hangs and fails to read the poison pill, it also fails to reset the watchdog timer.
   When the timer expires, the watchdog restarts the node.

## Overview of Linux high availability cluster on {{site.data.keyword.powerSys_notm}}
{: #ha-overview-ha-powervs}

Cluster nodes in {{site.data.keyword.powerSys_notm}} can run in a single workspace within one zone or across two workspaces in different zones.

The network architecture depends on the deployment model.
In a single workspace, one subnet can span all cluster nodes.
However, {{site.data.keyword.powerSys_notm}} does not allow a subnet to span multiple workspaces.
As a result, the traditional `IPaddr2` resource agent cannot be used to assign a fixed service IP across workspaces.

The resource agents `powervs-move-ip` and `powervs-subnet` manage additional subnets or network routes when cluster nodes are distributed across multiple workspaces.

Table 2 compares high availability cluster configurations for single-workspace and multizone region deployments.

|         Configuration          | Single workspace                                                   | Multizone region                                    |
| ------------------------------ | ------------------------------------------------------------------ | --------------------------------------------------- |
| Shared block storage           | {{site.data.keyword.powerSys_notm}} volumes                        | n/a                                                 |
| Shared file storage            | File storage shares for VPC (zonal)                                | File storage shares for VPC (regional)              |
| Fencing agent                  | `fence_ibm_powervs` (one agent)                                    | `fence_ibm_powervs` (one agent per workspace)       |
| Shared storage for SBD fencing | {{site.data.keyword.powerSys_notm}} volumes                        | iSCSI volumes on VPC VSIs                           |
| Resource Agent ServiceIP       | `ipaddr2` (recommended), `powervs_move_ip`                         | `powervs_move_ip` (recommended), `powervs_subnet`   |
{: caption="Comparison of HA cluster configurations for single-workspace and multizone region deployments" caption-side="bottom"}

The following figures illustrate the architecture of a highly available SAP system.

SAP Web Dispatcher serves as a reverse proxy for HTTP(S) requests to the SAP system.
Two SAP Web Dispatchers run in parallel in {{site.data.keyword.vpc_full}}s behind a {{site.data.keyword.loadbalancer_full}}.
{{site.data.keyword.tg_full}} interconnects the {{site.data.keyword.vpc_short}} with resources in {{site.data.keyword.powerSys_notm}} workspaces.
Business users access the SAP system through the {{site.data.keyword.loadbalancer_short}} and SAP Web Dispatchers.

Figure 2 shows a high availability setup with the SAP system that is installed in a single {{site.data.keyword.powerSys_notm}} workspace.

At least two application servers run on virtual server instances within the {{site.data.keyword.powerSys_notm}} workspace.
Instance directories for the application servers are stored on {{site.data.keyword.powerSys_notm}} volumes or a zonal file storage share.

Two {{site.data.keyword.powerSys_notm}} instances form a cluster for ASCS and ERS.
ASCS and ERS instance directories are stored on a zonal file storage share.
The `ipaddr2` resource agent manages virtual IP addresses for ASCS and ERS.

Another two {{site.data.keyword.powerSys_notm}} instances form a cluster for SAP HANA.
SAP HANA runs on both instances with system replication configured.
SAP HANA storage is stored on {{site.data.keyword.powerSys_notm}} volumes.
The `ipaddr2` resource agent manages the virtual IP for the SAP HANA primary.

![Figure 2. SAP on {{site.data.keyword.powerSys_notm}} HA architecture overview](../../images/powervs-ha-architecture-overview-sz.svg "SAP on {{site.data.keyword.powerSys_notm}} HA architecture overview"){: caption="SAP on {{site.data.keyword.powerSys_notm}} HA architecture overview" caption-side="bottom"}

Figure 3 shows a high availability setup with the SAP system that is deployed across two zones in a multizone region.

Redundant application servers run on virtual server instances in both {{site.data.keyword.powerSys_notm}} workspaces.
Application server instance directories are stored on {{site.data.keyword.powerSys_notm}} volumes or a regional file storage share.

Two {{site.data.keyword.powerSys_notm}} instances across the workspaces form a cluster for ASCS and ERS.
Their instance directories are stored on a regional file storage share.
The `powervs-move-ip` resource agent manages static routes and virtual IP addresses for ASCS and ERS.

Another two {{site.data.keyword.powerSys_notm}} instances form a cluster for SAP HANA.
SAP HANA runs on both instances with system replication configured.
SAP HANA uses {{site.data.keyword.powerSys_notm}} volumes for storage.
The `powervs-move-ip` resource agent manages static routes and the virtual IP for the SAP HANA primary.

![Figure 3. SAP on {{site.data.keyword.powerSys_notm}} HA architecture overview](../../images/powervs-ha-architecture-overview-mz.svg "SAP on {{site.data.keyword.powerSys_notm}} HA architecture overview"){: caption="SAP on {{site.data.keyword.powerSys_notm}} HA architecture overview" caption-side="bottom"}

## High availability network considerations
{: #ha-overview-hana-network-considerations}

High availability strategies for SAP solutions on IBM {{site.data.keyword.powerSys_notm}} differ between single-zone and multizone implementations.

A single-zone setup typically offers better latency and faster failover times because all components are located within the same zone.
However, it is more vulnerable to zone-level infrastructure failures.

In contrast, a multizone setup distributes resources across separate zones, providing stronger resilience against zone-specific outages and improving overall fault tolerance.
Multizone deployments introduce more networking considerations, which are addressed by specialized resource agents that are described in the following.

When you deploy SAP application servers in a multizone environment, running them in both zones can introduce latency between the application servers and the active database server.
To minimize this impact, it is recommended to colocate application servers with the active SAP HANA database instance.

### IP failover in multizone environments
{: #ha-overview-hana-network-ip-failover-multizone}

In IBM {{site.data.keyword.powerSys_notm}}, subnets are associated with a single workspace and do not span multiple workspaces.
Therefore, standard Linux resource agents such as *ipaddr2* cannot provide service IP failover across workspaces.

Maintaining connectivity from {{site.data.keyword.vpc_short}} or another {{site.data.keyword.powerSys_notm}} workspace in a multizone regions setup requires custom handling.
To maintain connectivity between {{site.data.keyword.vpc_short}} or another {{site.data.keyword.powerSys_notm}} workspace in a multizone region, specific configuration steps are needed.
IBM provides two custom resource agents to enable IP failover in these scenarios.

### Resource agent: powervs-move-ip
{: #ha-overview-hana-network-ra-move-ip}

This resource agent manages failover by assigning an overlay IP address as an alias to the virtual server instance (VSI) and updating static routes during takeover.

During a takeover event, the `powervs-move-ip` resource agent updates predefined static routes in IBM {{site.data.keyword.powerSys_notm}} and assigns an overlay IP address as an IP alias to the virtual server instance.

The following figures illustrate this scenario in an SAP HANA System Replication cluster.
- Two virtual server instances (VSIs) are deployed in separate workspaces.
- A private subnet is created in each workspace.
- The instances form a two-node RHEL High Availability Add-On cluster.
- SAP HANA is installed on both instances, and SAP HANA system replication is configured.
- A cluster resource for the overlay IP address is configured with the `powervs-move-ip` resource agent.

#### Overlay IP characteristics
{: #ha-overview-hana-network-ovl-ip-characteristics}

- The overlay IP is outside all CIDR subnet ranges in the environment.
- It is not assigned to a network interface during deployment.
- At run time, the resource agent configures the overlay IP address as an IP alias on a VSI network adapter.
- A static route is configured in each workspace:
   - The destination of each static route is the overlay IP address.
   - The next hop for each route is the primary IP address of its respective VSI.
   - The resource agent enables the route to the VSI hosting the SAP HANA primary and disables the route to the VSI hosting the SAP HANA secondary.

#### Overlay IP normal operation
{: #ha-overview-hana-network-ovl-ip-normal-op}

- The overlay IP address is configured as an alias on VSI-1 in workspace 1.
- In workspace 1, the static route with the overlay IP address as the destination and the next hop set to VSI-1's primary IP address is enabled.
- In workspace 2, the static route with the same destination and the next hop set to VSI-2's primary IP address is disabled.
- The SAP HANA primary instance runs on VSI-1.
- The SAP HANA secondary instance runs on VSI-2.

![Figure 4. SAP HANA on {{site.data.keyword.powerSys_notm}} in multizone region HA overview](../../images/powervs-ha-architecture-ovlip-mzr.svg "SAP HANA on {{site.data.keyword.powerSys_notm}} in a multizone region HA overview"){: caption="SAP HANA on {{site.data.keyword.powerSys_notm}} in multizone region HA overview" caption-side="bottom"}

#### Overlay IP after a takeover
{: #ha-overview-hana-network-ovl-after-takeover}

- The overlay IP address is configured as an alias on VSI-2 in workspace 2.
- In workspace 2, the static route with the overlay IP address as the destination and the next hop set to VSI-2's primary IP address is enabled.
- In workspace 1, the static route with the same destination and the next hop set to VSI-1's primary IP address is disabled.
- The SAP HANA primary instance runs on VSI-2.
- The SAP HANA secondary instance runs on VSI-1.

![Figure 5. SAP HANA on {{site.data.keyword.powerSys_notm}} in multizone region HA takeover](../../images/powervs-ha-architecture-ovlip-mzr-to.svg "SAP HANA on {{site.data.keyword.powerSys_notm}} in a multizone region HA takeover"){: caption="SAP HANA on {{site.data.keyword.powerSys_notm}} in multizone region HA takeover" caption-side="bottom"}

### Resource agent: powervs-subnet
{: #ha-overview-hana-network-ra-move-subnet}

During a takeover event, the `powervs-subnet` resource agent moves the entire subnet, including the virtual IP address, from one workspace to another.

The following figures illustrate this scenario in an SAP HANA System Replication cluster.

Two virtual server instances are deployed in separate workspaces, each with its own subnet:
- SAP HANA is installed on both VSIs, and SAP HANA System Replication is configured.
- The two virtual server instances form a two-node high availability cluster, each using its own subnet.
- A cluster resource that uses the `powervs-subnet` resource agent is configured for `Subnet 3` and `IP address 3`.
   Use a small CIDR range.
   Only `IP address 3` and the gateway IP are allocated in `Subnet 3`.
- SAP HANA database clients use `IP address 3` to connect to the database.

#### Move subnet characteristics
{: #ha-overview-hana-network-move-subnet-characteristics}

- The CIDR range does not overlap with any other subnet in the environment.
- The subnet is not preconfigured in either workspace.
- The resource agent creates the subnet in the workspace that hosts the SAP HANA primary instance.

#### Move subnet normal operation
{: #ha-overview-hana-network-move-subnet-normal-op}

- Subnet 3 is created in workspace 1.
- Subnet 3 is attached to VSI-1.
- IP address 3 is configured on VSI-1.
- The SAP HANA primary instance is active on VSI-1.
- The SAP HANA secondary instance is active on VSI-2.

![Figure 6. SAP HANA on {{site.data.keyword.powerSys_notm}} in multizone region HA overview](../../images/powervs-ha-architecture-mzr.svg "SAP HANA on {{site.data.keyword.powerSys_notm}} in a multizone region HA overview"){: caption="SAP HANA on {{site.data.keyword.powerSys_notm}} in multizone region HA overview" caption-side="bottom"}

#### Move subnet after a takeover
{: #ha-overview-hana-network-move-subnet-after-takeover}

- Subnet 3 is created in workspace 2.
- Subnet 3 is attached to VSI-2.
- IP address 3 is configured on VSI-2.
- The SAP HANA primary instance is active on VSI-2.
- The SAP HANA secondary instance is active on VSI-1.

![Figure 7. SAP HANA on {{site.data.keyword.powerSys_notm}} in multizone region HA takeover](../../images/powervs-ha-architecture-mzr-to.svg "SAP HANA on {{site.data.keyword.powerSys_notm}} in a multizone region HA takeover"){: caption="SAP HANA on {{site.data.keyword.powerSys_notm}} in multizone region HA takeover" caption-side="bottom"}

### SAP application server considerations in a multizone region
{: #ha-overview-hana-mzr-app-server}

Active-Active deployment
:  SAP application servers are distributed across two workspaces in different zones.
   At least two application servers run in separate workspaces.
   Redundant virtual server instances for SAP Central Services (CS) and the SAP HANA database system are deployed in both workspaces.
   Only one CS instance and one SAP HANA primary node are active at a time.
   This deployment provides redundancy at the application layer.

Active-Passive deployment
:  SAP application servers are active in only one of the two workspaces at a time.
   Application servers in the passive workspace are pre-provisioned but remain powered off until failover.
   As in the active-active architecture, CS and SAP HANA are deployed in both workspaces, but only one CS instance and one primary node are active at a time.
   This deployment provides better performance but increases the recovery time objective because the application servers must start during failover.

## SAP HANA disaster recovery scenarios
{: #ha-overview-hana-dr-scenarios}

To provide extra protection for the database, replicate the SAP HANA system to a third system in a different region by using SAP HANA system replication.
Select one of the following topologies based on your requirements.

- SAP HANA multitier system replication

   Multitier system replication chains multiple systems together to provide a higher level of availability.

- SAP HANA multitarget system replication

   Multitarget system replication enables primary and secondary systems to replicate changes to multiple targets.

## High availability cluster setup references
{: #ha-overview-hana-mzr-howto}

The following table provides links to documentation for configuring high availability clusters.

| Operating system | Cluster environment | Documentation                                                                                                                                |
| ---------------- | ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| RHEL             | One workspace       | [Implementing a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel)                                      |
| RHEL             | One workspace       | [Configuring an active-passive NFS Server in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-nfs)   |
| RHEL             | Multizone region    | [Implementing a Red Hat Enterprise Linux High Availability Add-On cluster in a multizone region environment](/docs/sap?topic=sap-ha-rhel-mz) |
| RHEL             | Multizone region    | [Implementing SBD poison-pill fencing in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-sbd)            |
| SLES             | One workspace       | [Implementing a SUSE Linux Enterprise Server high availability cluster](/docs/sap?topic=sap-ha-sles)                                         |
{: caption="Documentation for implementing a basic cluster" caption-side="bottom"}
{: tab-title="Implementing a basic cluster"}
{: tab-group="doc-link-group"}
{: class="simple-tab-table"}
{: #doc-link-table1}


| Operating system | Deployment scenario                                | Documentation |
| ---------------- | --------------------------------------------       | ------------- |
| RHEL             | SAP HANA single-host, performance-optimized        | [Configuring SAP HANA scale-up system replication in a Red Hat Enterprise Linux High Availability Add-On cluster with the sap-hana-ha resource agent](/docs/sap?topic=sap-ha-rhel-hana-ng-sh) |
| RHEL             | SAP HANA single-host, performance-optimized        | [Configuring SAP HANA scale-up system replication in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-hana-sr) |
| RHEL             | SAP HANA single-host, cost-optimized               | [Configuring SAP HANA cost-optimized scale-up system replication in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-hana-sr-cost-optimized) |
| RHEL             | SAP HANA single-host, active/active (read enabled) | [Configuring SAP HANA active/active (read enabled) system replication in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-hana-sr-aa) |
| RHEL             | SAP HANA single-host, multitier system replication | [Configuring SAP HANA multitier system replication in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-hana-sr-multitier) |
| RHEL             | SAP HANA multiple-hosts                            | [Configuring SAP HANA multiple-host system replication in a Red Hat Enterprise Linux High Availability Add-On cluster with the sap-hana-ha resource agent](/docs/sap?topic=sap-ha-rhel-hana-ng-mh) |
| SLES             | SAP HANA single-host, performance-optimized        | [Configuring SAP HANA scale-up system replication in a SUSE Linux Enterprise High Availability Extension cluster](/docs/sap?topic=sap-ha-sles-hana-sr) |
{: caption="Documentation for configuring high availability for SAP HANA" caption-side="bottom"}
{: tab-title="High availability for SAP HANA"}
{: tab-group="doc-link-group"}
{: class="simple-tab-table"}
{: #doc-link-table2}

| Operating system | Deployment scenario         | Documentation                                  |
| ---------------- | --------------------------- |----------------------------------------------- |
| RHEL             | ENSA2 in one workspace      | [Configuring high availability for SAP S/4HANA (ASCS and ERS) in a Red Hat Enterprise Linux High Availability Add-On cluster](/docs/sap?topic=sap-ha-rhel-ensa) |
| RHEL             | ENSA2 in a multizone region | [Configuring high availability for SAP S/4HANA (ASCS and ERS) on Red Hat Enterprise Linux HA Add-On clusters in a multizone region with simple mount](/docs/sap?topic=sap-ha-rhel-ensa-sm-mz) \n\n [Configuring high availability for SAP S/4HANA (ASCS and ERS) in a Red Hat Enterprise Linux High Availability Add-On cluster in a multizone region environment](/docs/sap?topic=sap-ha-rhel-ensa-mz) |
{: caption="Documentation for configuring high availability for ASCS" caption-side="bottom"}
{: tab-title="High availability for ASCS"}
{: tab-group="doc-link-group"}
{: class="simple-tab-table"}
{: #doc-link-table3}
