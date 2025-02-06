---

copyright:
  years: 2020, 2022

lastupdated: "2022-12-12"


keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads

subcollection: sap

---

{{site.data.keyword.attribute-definition-list}}

# Deploying {{site.data.keyword.vpc_short}} infrastructure for {{site.data.keyword.powerSys_notm}} SAP workloads
{: #power-vs-set-up-vpc-infrastructure}

As a best practice for SAP that runs on {{site.data.keyword.powerSysFull}}, three {{site.data.keyword.vpc_short}}s are created and three virtual server instances are deployed and configured.

* **Access host that is deployed in management VPC** is used for the management access to the landscape.
* **Host for internet services that are deployed in edge VPC** is used to configure the access from {{site.data.keyword.powerSys_notm}} instances to IBM Cloud services and to the public internet.
* **Host for private services that are deployed in workload VPC** provides certain management services to the {{site.data.keyword.powerSys_notm}} instances, like NFS, DNS, NTP.

![VPC landing zone for SAP on {{site.data.keyword.powerSys_notm}}](../images/step1_manual_VPC-for-{{site.data.keyword.powerSys_notm}}.svg){: caption="VPC landing zone for SAP on {{site.data.keyword.powerSys_notm}}" caption-side="bottom"}

## Deploying {{site.data.keyword.vpc_short}} for management services (management VPC)
{: #power-vs-set-up-management-vpc}

{{site.data.keyword.vpc_short}} for management services (management VPC) is a **mandatory** component in the SAP on {{site.data.keyword.powerSys_notm}} best practices. This service hosts the access hosts to the environment.

* Set up Virtual Private Cloud for management services. For more information, see [Creating a VPC and subnet](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#creating-a-vpc-and-subnet).
* Take care about the default network prefix. We recommend reducing the default network prefix, so it does not overlap between all your {{site.data.keyword.vpc_short}} services.
* Optionally, attach a public gateway to the subnet. With this option, every VPC in the subnet can communicate with the internet. An alternative is to use a floating IP address on virtual system instance instead and enable internet for the instances.

### Deploying access host in management VPC
{: #deploy-access-host}

Access host is a **mandatory** component in the SAP on {{site.data.keyword.powerSys_notm}} best practices. By this virtual server instance, the floating IP address is activated. You can then log in to the environment by using SSH. You need to set up a VPN access as a separate step and disable the floating IP address for a more secure environment. Use the following steps to deploy the access host.

1. Create a security group for management services. For more information, see [Configuring the security group for the instance](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#configuring-the-security-group-step). Specify following rules.

    |**Protocol**       |**Source type**|**Source**   |**Value** |**Rule's purpose**                                     |
    |-------------------|---------------|-------------|----------|-------------------------------------------------------|
    |ALL                |CIDR Block     |10.0.0.0/8   |n/a       |Address block of {{site.data.keyword.vpc_short}} and {{site.data.keyword.powerSys_notm}} management subnets    |
    |ALL                |CIDR Block     |161.26.0.0/16|n/a       |Address block of OS registration servers               |
    |TCP                |CIDR Block     |0.0.0.0/0    |Port 22-22|Address block allowed for host administration over SSH |
    {: class="simple-tab-table"}
    {: caption="Overview of the inbound security group rules for the access host" caption-side="bottom"}
    {: #inboundsecurityrulesaccesshost}
    {: tab-title="Inbound security group rules"}
    {: tab-group="Access-host-security-group-rules"}

    |**Protocol**       |**Source type**|**Source**   |**Value** |**Rule's purpose**                                     |
    |-------------------|---------------|-------------|----------|-------------------------------------------------------|
    |ALL                |CIDR Block     |0.0.0.0/0    |n/a       |Allow all connections initiated from the hosts         |
    {: class="simple-tab-table"}
    {: caption="Overview of the outbound security group rules for the access host" caption-side="bottom"}
    {: #outboundsecuritygrouprulesaccesshost}
    {: tab-title="Outbound security group rules"}
    {: tab-group="Access-host-security-group-rules"}

2. Create an ACL to restrict the access to the management VPC's subnets.

    |**Rule priority**|**Allow or deny**|**Protocol**|**Source**               |**Destination**          |**Value**|**Rule's purpose**                                          |
    |-----------------|--------------|------------|-------------------------|-------------------------|---------|------------------------------------------------------------|
    |1                |Allow         |ALL         |161.0.0.0/8              |10.0.0.0/8               |n/a      |address block of OS registration servers                    |
    |2                |Allow         |TCP         |Any IP, any port         |10.10.10./24, ports 22-22|n/a      |address block for host administration over SSH              |
    |3                |Allow         |ALL         |10.0.0.0/8               |10.0.0.0/8               |n/a      |address block that is allowed for {{site.data.keyword.vpc_short}} and {{site.data.keyword.powerSys_notm}} management subnets|
    |4                |Deny          |ALL         |Any IP                   |Any IP                   |n/a      |Deny all other inbound traffic                              |
    {: class="simple-tab-table"}
    {: caption="Overview of the inbound ACL rules for the access host" caption-side="bottom"}
    {: #aclinboundrulesaccesshost}
    {: tab-title="Inbound ACL rules"}
    {: tab-group="Access-host-ACL-rules"}

    |**Rule priority**|**Allow or deny**|**Protocol**|**Source**               |**Destination**          |**Value**|**Rule's purpose**                                          |
    |-----------------|--------------|------------|-------------------------|-------------------------|---------|------------------------------------------------------------|
    |1                |Allow         |ALL         |10.0.0.0/8               |161.0.0.0/8              |n/a      |address block of OS registration servers                    |
    |2                |Allow         |TCP         |10.10.10./24, ports 22-22|Any IP, any port         |n/a      |address block for host administration over SSH              |
    |3                |Allow         |ALL         |10.0.0.0/8               |10.0.0.0/8               |n/a      |address block that is allowed for {{site.data.keyword.vpc_short}} and {{site.data.keyword.powerSys_notm}} management subnets|
    |4                |Deny          |ALL         |Any IP                   |Any IP                   |n/a      |Deny all other outbound traffic                             |
    {: class="simple-tab-table"}
    {: caption="Overview of the outbound ACL rules for the access host" caption-side="bottom"}
    {: #acloutboundrulesaccesshost}
    {: tab-title="Outbound ACL rules"}
    {: tab-group="Access-host-ACL-rules"}

3. Create a virtual server instance with a Linux operating system. For more information, see [Creating a virtual server instance](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#creating-a-vsi).

    * You can select any available Linux image, but you need to use the same operating system release for all of the virtual server instances in the landscape. We verify the setup with newest versions of Red Hat Enterprise Linux (starting with RHEL 8.4) and of Suse Linux Enterprise Server (starting with SLES 15 SP3).
    * You don't have limitations as to which Compute profile, SAP certification, or storage and network performance is used. You can use the smallest profile with 2 virtual CPUs and with 4 GB memory.
    {: tip}

4. Attach the security group to the virtual server instance that you created in the previous step. Detach the default security group.
5. Enable the floating IP address on the access host. For more information, see [Reserving a floating IP address](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#reserving-a-floating-ip-address).
6. After instance status changes to `Running`, verify that you can successfully log in on the access host.

We recommend that you use two extra SSH client parameters to get a more reliable SSH connection: `ServerAliveInterval=60` and `ServerAliveCountMax=600`. If you use a nondefault path to your SSH key, you must specify it by following SSH client parameter: `-i \<path to your SSH private key\>`.
{: tip}

SSH command example:

```bash
ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 root@\<access_host_floating_ip\>
```
{: pre}

### Optional steps on {{site.data.keyword.vpc_short}} for management services
{: #vpc-management-services}

To create other VPC resources, such as ACL, load balancer, and other resources, see [Using the IBM Cloud console to create VPC resources](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console).

## Deploying {{site.data.keyword.vpc_short}} for edge services (edge VPC)
{: #power-vs-set-up-edge-vpc}

{{site.data.keyword.vpc_short}} for edge services (edge VPC) is a **mandatory** component in the SAP on IBM Power Virtual Services best practices. This service hosts the internet-facing SAP workloads.

* Set up {{site.data.keyword.vpc_short}} for management services. For more information, see [Creating a VPC and subnet](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#creating-a-vpc-and-subnet).
* Take care about the default network prefix. We recommend reducing the default network prefix, so it does not overlap between all your {{site.data.keyword.vpc_short}} services.
* Attach a public gateway to the subnet. Every VPC in the subnet can communicate with the internet. An alternative is to use a floating IP address on the virtual system instance instead and enable internet for the instances.

### Deploying host for internet services in edge VPC
{: #deploy-host-internet-services}

Host for internet services is a mandatory component in the SAP on {{site.data.keyword.powerSys_notm}} best practices. This virtual server instance hosts mandatory proxy server. Use the following steps to deploy a host for basic management services.

The following example setup uses open ports for standard SQUID proxy server that is provided by Linux distributions. If you use another proxy software or custom configurations, setup might vary.
{: note}

1. Create a security group for basic management services as described [Configuring the security group for the instance](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#configuring-the-security-group-step). Specify following rules:


    |**Protocol**       |**Source type**|**Source**   |**Value**     |**Rule's purpose**                                     |
    |-------------------|---------------|-------------|--------------|-------------------------------------------------------|
    |ALL                |CIDR Block     |10.0.0.0/8   |n/a           |address block of {{site.data.keyword.vpc_short}} and {{site.data.keyword.powerSys_notm}} management subnets    |
    |TCP                |CIDR Block     |0.0.0.0/8    |Port 80-80    |HTTP                                                   |
    |TCP                |CIDR Block     |0.0.0.0/8    |Port 443-443  |HTTPS                                                  |
    |TCP                |CIDR Block     |0.0.0.0/8    |Port 8443-8443|HTTPS (used by OS registration)                        |
    |ALL                |CIDR Block     |161.26.0.0/16|n/a           |address block of OS registration servers               |
    |UDP                |CIDR Block     |0.0.0.0/0    |Port 53-53    |DNS                                                    |
    {: class="simple-tab-table"}
    {: caption="Overview of inbound security group rules for the internet services host" caption-side="bottom"}
    {: #inboundsecuritygroup-internetserviceshost}
    {: tab-title="Inbound security group rules"}
    {: tab-group="Internet-services-host-security-group-rules"}

    |**Protocol**       |**Source type**|**Source**   |**Value**     |**Rule's purpose**                                     |
    |-------------------|---------------|-------------|--------------|-------------------------------------------------------|
    |ALL                |CIDR Block     |0.0.0.0/0    |n/a           |Allow all connections initiated from the hosts         |
    {: class="simple-tab-table"}
    {: caption="Overview of outbound security group rules for the internet services host" caption-side="bottom"}
    {: #outboundsecuritygroup-internetserviceshost}
    {: tab-title="Outbound security group rules"}
    {: tab-group="Internet-services-host-security-group-rules"}

2. Create an ACL to restrict the access to the edge {{site.data.keyword.vpc_short}}'s subnets.

    |**Rule Priority**|**Allow or deny**|**Protocol**|**Source**               |**Destination**          |**Value**|**Rule's purpose**                                          |
    |-----------------|--------------|------------|-------------------------|-------------------------|---------|------------------------------------------------------------|
    |1                |Allow         |ALL         |161.0.0.0/8              |10.0.0.0/8               |n/a      |address block of OS registration servers                    |
    |2                |Allow         |UDP         |Any IP, ports 53-53      |10.0.0.0/8, any port     |n/a      |DNS                                                         |
    |3                |Allow         |ALL         |10.0.0.0/8               |10.0.0.0/8               |n/a      |address block that is allowed for {{site.data.keyword.vpc_short}} and {{site.data.keyword.powerSys_notm}} management subnets|
    |4                |Allow         |TCP         |Any IP, ports 8443-8443  |10.0.0.0/8, any port     |n/a      |HTTPS for OS registration                                   |
    |5                |Allow         |TCP         |Any IP, ports 443-443    |10.0.0.0/8, any port     |n/a      |HTTPS                                                       |
    |6                |Allow         |TCP         |Any IP, ports 80-80      |10.0.0.0/8, any port     |n/a      |HTTP                                                        |
    |7                |Deny          |ALL         |Any IP                   |Any IP                   |n/a      |Deny all other inbound traffic                              |
    {: class="simple-tab-table"}
    {: caption="Overview of ACL inbound rules for the internet services host" caption-side="bottom"}
    {: #inboundacl-internetserviceshost}
    {: tab-title="Inbound ACL rules"}
    {: tab-group="Internet-services-host-ACL-rules"}

    |**Rule priority**|**Allow or deny**|**Protocol**|**Source**               |**Destination**          |**value**|**Rule's purpose**                                          |
    |-----------------|--------------|------------|-------------------------|-------------------------|---------|------------------------------------------------------------|
    |1                |Allow         |ALL         |10.0.0.0/8               |161.0.0.0/8, any port    |n/a      |address block of OS registration servers                    |
    |2                |Allow         |UDP         |10.0.0.0/8, any port     |Any IP, ports 53-53      |n/a      |DNS                                                         |
    |3                |Allow         |ALL         |10.0.0.0/8               |10.0.0.0/8               |n/a      |address block that is allowed for {{site.data.keyword.vpc_short}} and {{site.data.keyword.powerSys_notm}} management subnets|
    |4                |Allow         |TCP         |10.0.0.0/8, any port     |Any IP, ports 8443-8443  |n/a      |HTTPS for OS registration                                   |
    |5                |Allow         |TCP         |10.0.0.0/8, any port     |Any IP, ports 443-443    |n/a      |HTTPS                                                       |
    |6                |Allow         |TCP         |10.0.0.0/8, any port     |Any IP, ports 80-80      |n/a      |HTTP                                                        |
    |7                |Deny          |ALL         |Any IP                   |Any IP                   |n/a      |Deny all other outbound traffic                             |
    {: class="simple-tab-table"}
    {: caption="Overview of ACL outbound rules for the internet services host" caption-side="bottom"}
    {: #outboundacl-internetserviceshost}
    {: tab-title="Outbound ACL rules"}
    {: tab-group="Internet-services-host-ACL-rules"}

3. Provision a virtual service instance with Linux. For more information, see [Creating a virtual server instance](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#creating-a-vsi).
    * You can choose any of available Linux distributions. We recommend that you use the same OS release for all virtual server instances in the landscape. We verify the setup with the newest versions of Red Hat Enterprise Linux (starting with RHEL 8.4) and of Suse Linux Enterprise Server (starting with SLES 15 SP3).
    * One of the profiles with 2 or 4 virtual CPUs and with 4 GB or 8 GB of memory would be sufficient in general. If you have stronger performance requirements, by using SQUID cache, you can choose a profile with more virtual CPUs or memory.
    * We recommend that you attach extra storage to the instance, locate SAP installation files on this separate disk, and export them over NFS. The disk size must be large enough to host all the data from IBM Cloud Object Storage that is relevant for all the installations and setups.
4. Attach the security group to the virtual server instance that you created in the previous step. Detach the default security group.
5. After some time, instance becomes status 'running'.

## Deploying {{site.data.keyword.vpc_short}} for workload services (workload VPC)
{: #power-vs-set-up-workload-vpc}

{{site.data.keyword.vpc_short}} for workload services (workload VPC) is a **mandatory** component in the SAP on IBM Power Virtual Services best practices. This service hosts the instances with management components that are used by operating systems and workloads that are running in {{site.data.keyword.powerSysShort}}s.

* Set up Virtual Private Cloud for management services. For more information, see [Creating a VPC and subnet](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#creating-a-vpc-and-subnet).
* Take care about the default network prefix. We recommend reducing the default network prefix, so it does not overlap between all your {{site.data.keyword.vpc_short}} services.
* Do not attach a public gateway to the subnet and do not use floating IP addresses. The hosts are communication with the internet over SQUID proxy.

### Deploying host for workload services in workload VPC
{: #deploy-host-workload-services}

Host for workload services is a **mandatory** component in the SAP on IBM Power Virtual Services best practices. This virtual server instance hosts optional NFS server, NTP forwarder, DNS forwarder, and LDAP server. If you have already some of these services in your environment that are accessible from {{site.data.keyword.powerSys_notm}} networks, then you can use them and do not need to install here. Execute following steps to deploy host for private services.

By the using the following setup, we open ports for standard widely used management components: NFS service provided by Linux distributions, NTP service over 'chrony', DNS service over 'bind' and open LDAP. If you use another software solutions or custom configurations, set up might vary.
{: note}

1. Create a security group for private services. For more information, see [Configuring the security group for the instance](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#configuring-the-security-group-step). Specify following rules:

    |**Protocol**       |**Source type**|**Source**   |**Value**     |**Rule's purpose**                                     |
    |-------------------|---------------|-------------|--------------|-------------------------------------------------------|
    |ALL                |CIDR Block     |10.0.0.0/8   |n/a           |address block of {{site.data.keyword.vpc_short}} and {{site.data.keyword.powerSys_notm}} management subnets    |
    |ALL                |CIDR Block     |161.26.0.0/16|n/a           |address block of OS registration servers               |
    |UDP                |CIDR Block     |0.0.0.0/0    |Port 53-53    |DNS                                                    |
    {: class="simple-tab-table"}
    {: caption="Overview of the inbound security group rules for the workload services host" caption-side="bottom"}
    {: #inboundsecuritygroupworkloadserviceshost}
    {: tab-title="Inbound security group rules"}
    {: tab-group="Security-Group-Workload-services-host-rules"}

    |**Protocol**       |**Source type**|**Source**   |**Value**     |**Rule's purpose**                                     |
    |-------------------|---------------|-------------|--------------|-------------------------------------------------------|
    |ALL                |CIDR Block     |0.0.0.0/0    |n/a           |Allow all connections initiated from the hosts         |
    {: class="simple-tab-table"}
    {: caption="Overview of the outbound security group rules for the workload services host" caption-side="bottom"}
    {: #outboundsecuritygroupworkloadserviceshost}
    {: tab-title="Outbound security group rules"}
    {: tab-group="Security-Group-Workload-services-host-rules"}

2. Create an ACL to restrict the access to the Workload VPC's subnets.

    |**Rule priority**|**Allow or deny**|**Protocol**|**Source**               |**Destination**          |**Value**|**Rule's purpose**                                          |
    |-----------------|--------------|------------|-------------------------|-------------------------|---------|------------------------------------------------------------|
    |1                |Allow         |ALL         |161.0.0.0/8              |10.0.0.0/8               |n/a      |address block of OS registration servers                    |
    |2                |Allow         |UDP         |Any IP, ports 53-53      |10.0.0.0/8, any port     |n/a      |DNS                                                         |
    |3                |Allow         |ALL         |10.0.0.0/8               |10.0.0.0/8               |n/a      |address block that is allowed for {{site.data.keyword.vpc_short}} and {{site.data.keyword.powerSys_notm}} management subnets|
    |4                |Deny          |ALL         |Any IP                   |Any IP                   |n/a      |Deny all other inbound traffic                              |
    {: class="simple-tab-table"}
    {: caption="Overview of the inbound ACL rules for the workload services host" caption-side="bottom"}
    {: #inboundaclworkloadserviceshost}
    {: tab-title="Inbound ACL rules"}
    {: tab-group="ACL-Workload-services-host-rules"}

    |**Rule priority**|**Allow or deny**|**Protocol**|**Source**               |**Destination**          |**Value**|**Rule's purpose**                                          |
    |-----------------|--------------|------------|-------------------------|-------------------------|---------|------------------------------------------------------------|
    |1                |Allow         |ALL         |10.0.0.0/8               |161.0.0.0/8, any port    |n/a      |address block of OS registration servers                    |
    |2                |Allow         |UDP         |10.0.0.0/8, any port     |Any IP, ports 53-53      |n/a      |DNS                                                         |
    |3                |Allow         |ALL         |10.0.0.0/8               |10.0.0.0/8               |n/a      |address block that is allowed for {{site.data.keyword.vpc_short}} and {{site.data.keyword.powerSys_notm}} management subnets|
    |4                |Deny          |ALL         |Any IP                   |Any IP                   |n/a      |Deny all other outbound traffic                             |
    {: class="simple-tab-table"}
    {: caption="Overview of the outbound ACL rules for the workload services host" caption-side="bottom"}
    {: #outboundaclworkloadserviceshost}
    {: tab-title="Outbound ACL rules"}
    {: tab-group="ACL-Workload-services-host-rules"}

3. Create a virtual server instance by using a Linux image. For more information, see [Creating a virtual server instance](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#creating-a-vsi).
    * You can choose any of available Linux distributions. We recommend that you use the same OS release for all virtual server instances in the landscape. We verify the setup with newest versions of Red Hat Enterprise Linux (starting with RHEL 8.4) and of Suse Linux Enterprise Server (starting with SLES 15 SP3).
    * Analyze your DNS and LDAP performance requirements and ensure you choose an appropriate profile with enough virtual CPUs and memory.
    * We recommend attaching an extra storage disk to the instance, locate SAP installation files on this separate disk and export them over NFS. The disk size must be large enough to host all the data from IBM Cloud Object Storage that is relevant for all the installations and setups in this landscape.
4. Attach the security group to the virtual server instance that you created previously. Detach the default security group.
5. After some time, instance becomes status 'running'.

## Connecting {{site.data.keyword.vpc_short}} services over transit gateway
{: #power-vs-set-up-transit-gateway}

To establish communication between virtual server instances that run in different {{site.data.keyword.vpc_short}} services, you must connect the private networks in the {{site.data.keyword.vpc_short}} services with each other.
1. Create transit gateway. For more information, see [Creating a transit gateway by using the UI](/docs/transit-gateway?topic=transit-gateway-ordering-transit-gateway&interface=ui).
2. Connect all {{site.data.keyword.vpc_short}}s (management, edge, workload) to the transit gateway. For more information, see [Adding a connection](/docs/transit-gateway?topic=transit-gateway-adding-connections&interface=ui). Now you can connect with SSH over the access host with internet and private services hosts.
3. Verify that you can successfully log in to the **host for internet services over the access host** and to the **host for workload services over the access host**. The floating IP address of the
access host is specified as `ProxyCommand` parameter of your ssh command.

We recommend that you use two extra SSH client parameters for a more reliable SSH connection: `ServerAliveInterval=60` and `ServerAliveCountMax=600`. If you use a nondefault path to your SSH key, you must specify it by following SSH client parameter: `-i \<path_to_your_SSH_private_key\>`.
{: tip}

SSH command example (host for internet services over the access host):

```bash
ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand=\"ssh -W %h:%p root@\<access_host_floating_ip\>\" root@\<internet_services_host_private_ip\>
```
{: pre}

SSH command example (host for workload services over the access host):

```bash
ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand=\"ssh -W %h:%p root@\<access_host_floating_ip\>\" root@\<workload_services_host_private_ip\>
```
{: pre}

## Configure the management services on the virtual server instances in {{site.data.keyword.vpc_short}}
{: #power-vs-set-up-management-services}

The following setup example demonstrates usage of SQUID proxy server, NFS server, NTP forwarder, IBM Cloud DNS service, or DNS forwarder for your private DNS server. Setup of central user management (LDAP) is not covered here.

### Setup SQUID proxy server
{: #power-vs-set-up-proxy-server}

1. Ensure all required ports in the security group in {{site.data.keyword.vpc_short}} for edge services that are used by host for internet services are open. The needed ports are configured for internet services. Required ports for SQUID proxy are used by {{site.data.keyword.powerSys_notm}} services. For more information, see [Creating a proxy](/docs/power-iaas?topic=power-iaas-set-full-Linux#create-proxy).
2. Log in to the internet services instance. SSH command example:

   ```bash
   ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand=\"ssh -W %h:%p root@\<access_host_floating_ip\>\" root@\<internet_services_host_private_ip\>
   ```
   {: pre}

3. Ensure that the squid software is available. On SUSE: `zypper update -y; zypper install -y squid`. On RHEL: `yum update -y; yum install epel-release; yum install -y squid`.
4. Modify the SQUID configuration and add the rules relevant for OS update registration of virtual instances that run in {{site.data.keyword.powerSys_notm}}. For more information, see [proxy configuration documentation](/docs/power-iaas?topic=power-iaas-set-full-Linux#configure-proxy).
5. Enable and restart SQUID service: `systemctl stop squid; systemctl start squid; systemctl enable squid`.

### Setup SQUID proxy server -- Ansible
{: #power-vs-set-up-proxy-server-ansible}

To perform the previous steps through ansible automation, download ansible-galaxy collection `ibm.power_linux_sap`.

```yaml
ansible-galaxy collection install ibm.power_linux_sap
```
{: pre}

After the ansible collection is installed, to set up proxy, update the variable file `playbook/vars/sample_services_variable_file.yml`

```yaml
server_config: {
  squid: { enable: true }
}
```
{: pre}

After the file is updated, run the following ansible-playbook command.

```yaml
ansible-playbook \--connection=local -i \"localhost,\" powervs-services.yml -e \@vars/sample_services_variable_file.yml
```
{: pre}

This ansible execution ensures that the squid proxy server is configured on host for workload services.

### Setting up NFS server for SAP installation files
{: #power-vs-set-up-nfs-server}

1. Ensure all required ports in the security group in {{site.data.keyword.vpc_short}} for workload services that are used by host for private services are open. The needed ports are configured for private services. Required ports for NFS server are: 111 (TCP and UDP) and 2049 (TCP and UDP).
2. Log in to the workload services instance. SSH command example:

   ```bash
   ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand=\"ssh -W %h:%p root@\<access_host_floating_ip\>\" root@\<workload_services_host_private_ip\>
   ```
   {: pre}

3. Make sure that the required NFS software is available. On SUSE: `zypper update -y; zypper install -y nfs-utils`. On RHEL: `yum update -y; yum install epel-release; yum install -y nfs-utils`.
4. Create a directory where NFS is mounted and export it.
5. Start NFS service and verify that the directory is exported. Use command `showmount -e`.
6. Make sure that the awscli software is available. This software is used later to download the software from S3 storage (IBM Cloud Object Storage). On SUSE: `zypper update -y; zypper install -y aws-cli`. On RHEL: `yum update -y; yum install epel-release; yum install -y awscli`.

### Setting up NFS server for SAP installation files -- Ansible
{: #power-vs-set-up-nfs-server-ansible}

To perform the previous steps through ansible automation, download ansible-galaxy collection `ibm.power_linux_sap`.

```yaml
ansible-galaxy collection install ibm.power_linux_sap
```
{: pre}

After the ansible collection is installed, update the variable file `playbook/vars/sample_services_variable_file.yml` to set up proxy.

```yaml
server_config: {
  nfs: { enable: true, nfs_directory: "/NFS;/hana/software" }
}
```
If you also want to install awscli, by using ansible, add update variable file `playbook/vars/sample_services_variable_file.yml` as

```yaml
server_config: {
  nfs: { enable: true, nfs_directory: "/NFS;/hana/software" },
  awscli: { enable: true }
}
```
{: pre}

After file is updated, run the following ansible-playbook command.

```yaml
ansible-playbook \--connection=local -i \"localhost,\" powervs-services.yml -e \@vars/sample_services_variable_file.yml
```
{: pre}

This ansible execution makes sure that the host for workload services acts as an NFS server and awscli package is installed.

### Setting up NTP proxy and forwarder
{: #power-vs-set-up-ntp-server}

1. Ensure all required ports in the security group in {{site.data.keyword.vpc_short}} for workload services that are used by host for private services are open. The needed ports are configured for private services. Required port for NTP forwarder is: 123 (TCP).
2. Log in to the workload services instance. SSH command example:

   ```bash
   ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand=\"ssh -W %h:%p root@\<access_host_floating_ip\>\" root@\<workload_services_host_private_ip\>
   ```
   {: pre}

3. Make sure that the required NTP software is available. On SUSE: `zypper update -y; zypper install -y chrony`. On RHEL: `yum update -y; yum install epel-release; yum install -y chrony`.
4. Modify file '/etc/chrony.conf' and add following lines. Replace `\<pvs_mgmt_cidr\>` with {{site.data.keyword.powerSys_notm}} management CIDR block (network segment)

   ```yaml
   local stratum 10
   manual
   allow \<pvs_mgmt_cidr \>
   ```
   {: pre}

5. Enable and start chrony service: `systemctl stop chronyd; systemctl start chronyd; systemctl enable chronyd;`.


### Setup NTP proxy or forwarder -- Ansible
{: #power-vs-set-up-ntp-server-ansible}

To perform previous steps through ansible automation, download ansible-galaxy collection `ibm.power_linux_sap`.

```yaml
ansible-galaxy collection install ibm.power_linux_sap
```
{: pre}

After the ansible collection is installed, to set up proxy, update the variable file `playbook/vars/sample_services_variable_file.yml`.

```yaml
server_config: {
  ntp: { enable: true }
}
```
{: pre}

After the file is updated, run the following ansible-playbook command.

```yaml
ansible-playbook \--connection=local -i \"localhost,\" powervs-services.yml -e \@vars/sample_services_variable_file.yml
```
{: pre}

This ansible execution ensures that the NTP proxy is configured on the host for workload services.

### Setting up IBM Cloud-native DNS service (DNS option 1)
{: #power-vs-set-up-dns-service}

You can use IBM Cloud DNS service that is directly reachable from IBM {{site.data.keyword.powerSys_notm}}s over custom resolver. For more information, see the following links.

* [Setting up an instance](/docs/dns-svcs?topic=dns-svcs-setting-up-your-dns-instance)
* [Managing DNS zones](/docs/dns-svcs?topic=dns-svcs-managing-dns-zones&interface=ui)
* [Managing permitted networks](/docs/dns-svcs?topic=dns-svcs-managing-permitted-networks&interface=ui)
* [Managing DNS records](/docs/dns-svcs?topic=dns-svcs-managing-dns-records)
* [Configuring custom resolver](/docs/dns-svcs?topic=dns-svcs-ui-create-cr). Specify VPE subnet of {{site.data.keyword.vpc_short}} for workload services as location in the resolver.

Make sure that all required ports in the security group in {{site.data.keyword.vpc_short}} for workload services that are used by host for private services are open. The needed ports are configured for private services. Required port for DNS is: 53 (TCP and UDP). As result, you get a private IP address that you can specify in {{site.data.keyword.powerSysShort}} instances as DNS endpoints by private subnet configuration. These IP addresses are entered into `/etc/resolf.conf` in the operating system.

### Setting up DNS forwarder (DNS option 2)
{: #power-vs-set-up-dns-forwarder}

If you use your own DNS service, its IP must be reachable from VPC for workload services. If the IP is not directly reachable from IBM Power Systems Virtual Servers, a DNS forwarder on the host for critical management services is required. Use the following steps to complete the configuration:

1. Make sure that all required ports in the security group in {{site.data.keyword.vpc_short}} for workload services that are used by host for private services are open. The needed ports are configured for private services. Required port for DNS is: 53 (TCP and UDP).
2. Log in to the private services instance. You can use the following SSH command example:

   ```bash
   ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand=\"ssh -W %h:%p root@\<access_host_floating_ip\>\" root@\<workload_services_host_private_ip\>
   ```
   {: pre}

3. Make sure that the required DNS software is available. On SUSE: `zypper update -y; zypper install -y bind`. On RHEL: `yum update -y; yum install epel-release; yum install -y bind`.
4. Modify file `/etc/named.conf`:
   * Add the following lines at the beginning of the file direct after the starting comment (before the `options` section starts). Replace \<pvs_mgmt_cidr\> with {{site.data.keyword.powerSys_notm}} management CIDR block (network segment)

      ```yaml
      acl allowed_clients {
      localhost;
      10.10.0.0/16;
      };
      ```
      {: pre}

   * Add the following lines at the beginning of the `options` section. Both 161.26.0.x IPs are the IBM Cloud DNS servers. 9.9.9.9 IP is the IBM public DNS server IP. Replace or extend this IP list with DNS servers of your choice.

      ```yaml
      forwarders {
      161.26.0.7;
      161.26.0.8;
      9.9.9.9;
      };
      recursion yes;
      allow-query { allowed_clients; };
      forward only;
      ```
      {: pre}

5. Enable and start DNS service: `systemctl stop named; systemctl start named; systemctl enable named;`.

### Setup DNS forwarder (DNS option 2) -- Ansible
{: #power-vs-set-up-dns-forwarder-ansible}

To perform the previous steps through ansible automation, download ansible-galaxy collection `ibm.power_linux_sap`.

```yaml
ansible-galaxy collection install ibm.power_linux_sap
```
{: pre}

After the ansible collection is installed, to set up proxy, update the variable file `playbook/vars/sample_services_variable_file.yml`

```yaml
server_config: {
  dns: { enable: false, dns_servers: "161.26.0.7; 161.26.0.8; 9.9.9.9;" }
}
```
{: pre}

After the file is updated, run the following ansible-playbook command.

```yaml
ansible-playbook \--connection=local -i \"localhost,\" powervs-services.yml -e \@vars/sample_services_variable_file.yml
```
{: pre}

This ansible execution ensures that the DNS forwarder services are configured on {{site.data.keyword.powerSys_notm}}.
