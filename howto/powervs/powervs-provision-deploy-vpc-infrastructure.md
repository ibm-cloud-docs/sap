---
copyright:
  years: 2023, 2025
lastupdated: "2025-03-21"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, VPC landing zone, NTP, NFS, DNS, Proxy, Squid, Terraform, Deployable architecture, Edge VPC, SSH
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Deploying {{site.data.keyword.vpc_short}} infrastructure for {{site.data.keyword.powerSys_notm}} SAP workloads
{: #powervs-set-up-vpc-infrastructure}

As a best practice for SAP that runs on {{site.data.keyword.powerSysFull}}, one {{site.data.keyword.vpc_short}} is created and two virtual server instances are deployed and configured.

* **Access host that is deployed in edge VPC** is used for the management access to the landscape.
* **Host for network management services that are deployed in edge VPC** provides certain network services to the {{site.data.keyword.powerSys_notm}} instances, like proxy forwarding, NFS, DNS, NTP and monitoring services.

![VPC landing zone for SAP on {{site.data.keyword.powerSys_notm}}](../../images/powervs-provision-deploy-arch-ibm-pvs-inf-standard.svg){: caption="VPC landing zone for SAP on {{site.data.keyword.powerSys_notm}}" caption-side="bottom"}

## Deploying {{site.data.keyword.vpc_short}} for management services
{: #powervs-set-up-edge-vpc}

{{site.data.keyword.vpc_short}} for management services is a **mandatory** component in the SAP on {{site.data.keyword.powerSys_notm}} best practices. This service hosts the access hosts to the environment.

* Set up Virtual Private Cloud for management services. For more information, see [Creating a VPC and subnet](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#creating-a-vpc-and-subnet).
* Take care about the default network prefix. We recommend reducing the default network prefix, so it does not overlap between all your {{site.data.keyword.vpc_short}} services.
* Optionally, attach a public gateway to the subnet. With this option, every Virtual Server Instance in the subnet can communicate with the internet. An alternative is to use a floating IP address on virtual system instance instead and enable internet for the instances. The [table](/docs/powervs-vpc?topic=powervs-vpc-landing-zone-preset#landing-zone-private-networks) gives an overview on segregation of networks used.

### Deploying access host in edge VPC
{: #deploy-access-host}

Access host is a **mandatory** component in the SAP on {{site.data.keyword.powerSys_notm}} best practices. By this virtual server instance, the floating IP address is activated. You can then log in to the environment by using SSH. You need to set up a VPN access as a separate step and disable the floating IP address for a more secure environment. Use the following steps to deploy the access host.

1. Create ACL rules for Edge VPC as described [here](/docs/powervs-vpc?topic=powervs-vpc-landing-zone-preset#landing-zone-acl)
1. Create security groups for management services, network services and IBM Cloud services as described [here](/docs/powervs-vpc?topic=powervs-vpc-landing-zone-preset#landing-zone-sg). For more information, see [Configuring the security group for the instance](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#configuring-the-security-group-step). Specify following rules.
1. Create a virtual server instance with a Linux operating system. For more information, see [Creating a virtual server instance](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#creating-a-vsi).
    * You can select any available Linux image, but you need to use the same operating system release for all of the virtual server instances in the landscape. We verify the setup with newest versions of Red Hat Enterprise Linux (starting with RHEL 8.4) and of Suse Linux Enterprise Server (starting with SLES 15 SP3).
    * You don't have limitations as to which Compute profile, SAP certification, or storage and network performance is used. You can use the smallest profile with 2 virtual CPUs and with 4 GB memory.
    {: tip}

1. Attach the right security group (management-sg security group) to the virtual server instance that you created in the previous step. Detach the default security group.
1. Enable the floating IP address on the access host. For more information, see [Reserving a floating IP address](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#reserving-a-floating-ip-address).
1. After instance status changes to `Running`, verify that you can successfully log in on the access host.

We recommend that you use two extra SSH client parameters to get a more reliable SSH connection: `ServerAliveInterval=60` and `ServerAliveCountMax=600`. If you use a nondefault path to your SSH key, you must specify it by following SSH client parameter: `-i \<path to your SSH private key\>`.
{: tip}

SSH command example:

```bash
ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 root@\<access_host_floating_ip\>
```
{: pre}

### Deploying network services host in edge VPC
{: #deploy-network-service-host}

Host for internet services is a mandatory component in the SAP on {{site.data.keyword.powerSys_notm}} best practices. This virtual server instance hosts mandatory proxy server, NTP and DNS services. It is also configured as central Ansible Node. Use the following steps to deploy a host for basic network management services.

The following example setup uses open ports for standard SQUID proxy server that is provided by Linux distributions. If you use another proxy software or custom configurations, setup might vary.
{: note}

1. Provision a virtual service instance with Linux. For more information, see [Creating a virtual server instance](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console#creating-a-vsi).
    * You can choose any of available Linux distributions. We recommend that you use the same OS release for all virtual server instances in the landscape. We verify the setup with the newest versions of Red Hat Enterprise Linux (starting with RHEL 8.4) and of Suse Linux Enterprise Server (starting with SLES 15 SP3).
    * One of the profiles with 2 or 4 virtual CPUs and with 4 GB or 8 GB of memory would be sufficient in general. If you have stronger performance requirements, by using SQUID cache, you can choose a profile with more virtual CPUs or memory.
    * We recommend that you attach extra storage to the instance, locate SAP installation files on this separate disk, and export them over NFS. The disk size must be large enough to host all the data from IBM Cloud Object Storage that is relevant for all the installations and setups.
1. Attach the right security group to the virtual server instance (network-services-sg security group) that you created in the previous step. Detach the default security group.
1. After some time, instance becomes status 'running'.

Verify that you can successfully log in to the **host for network services over the access host**. The floating IP address of the access host is specified as `ProxyCommand` parameter of your ssh command.

We recommend that you use two extra SSH client parameters for a more reliable SSH connection: `ServerAliveInterval=60` and `ServerAliveCountMax=600`. If you use a nondefault path to your SSH key, you must specify it by following SSH client parameter: `-i \<path_to_your_SSH_private_key\>`.
{: tip}

SSH command example (host for network services over the access host):

```bash
ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand=\"ssh -W %h:%p root@\<access_host_floating_ip\>\" root@\<network_services_host_private_ip\>
```
{: pre}

## Configure the network management services on the Intel virtual server instances in {{site.data.keyword.vpc_short}}
{: #powervs-set-up-management-services}

The following setup example demonstrates usage of SQUID proxy server, NFS server, NTP forwarder, IBM Cloud DNS service, or DNS forwarder for your private DNS server. Setup of central user management (LDAP) is not covered here.

### Setup SQUID proxy server
{: #powervs-set-up-proxy-server}

1. Ensure all required ports in the security group in {{site.data.keyword.vpc_short}} for edge services that are used by host for internet services are open. The needed ports are configured for internet services. Required ports for SQUID proxy are used by {{site.data.keyword.powerSys_notm}} services. For more information, see [Creating a proxy](/docs/power-iaas?topic=power-iaas-set-full-Linux#create-proxy).
1. Log in to the network services instance. SSH command example:

   ```bash
   ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand=\"ssh -W %h:%p root@\<access_host_floating_ip\>\" root@\<network_services_host_private_ip\>
   ```
   {: pre}

1. Ensure that the squid software is available. On SUSE: `zypper update -y; zypper install -y squid`. On RHEL: `yum update -y; yum install epel-release; yum install -y squid`.
1. Modify the SQUID configuration and add the rules relevant for OS update registration of virtual instances that run in {{site.data.keyword.powerSys_notm}}. For more information, see [proxy configuration documentation](/docs/power-iaas?topic=power-iaas-set-full-Linux#configure-proxy).
1. Enable and restart SQUID service: `systemctl stop squid; systemctl start squid; systemctl enable squid`.

### Setup SQUID proxy server -- Ansible
{: #powervs-set-up-proxy-server-ansible}

To perform the previous steps through ansible automation, download ansible-galaxy collection `ibm.power_linux_sap`.

```yaml
ansible-galaxy collection install ibm.power_linux_sap
```
{: pre}

After the ansible collection is installed, to set up proxy, update the variable file `playbook/vars/sample-variables-configure-network-services-host.yml`

```yaml
server_config: {
  squid: { enable: true }
}
```
{: pre}

After the file is updated, run the following ansible-playbook command.

```yaml
ansible-playbook \--connection=local -i \"localhost,\" playbooks/sample-configure-network-services-host.yml
```
{: pre}

This ansible execution ensures that the squid proxy server is configured on host for network services.

### Setting up NFS server for SAP installation files
{: #powervs-set-up-nfs-server}

1. Ensure all required ports in the security group in {{site.data.keyword.vpc_short}} for network services that are used by host for private services are open. The needed ports are configured for private services. Required ports for NFS server are: 111 (TCP and UDP) and 2049 (TCP and UDP).
1. Log in to the network services instance. SSH command example:

   ```bash
   ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand=\"ssh -W %h:%p root@\<access_host_floating_ip\>\" root@\<network_services_host_private_ip\>
   ```
   {: pre}

1. Make sure that the required NFS software is available. On SUSE: `zypper update -y; zypper install -y nfs-utils`. On RHEL: `yum update -y; yum install epel-release; yum install -y nfs-utils`.
1. Create a directory where NFS is mounted and export it.
1. Start NFS service and verify that the directory is exported. Use command `showmount -e`.
1. Make sure that the awscli software is available. This software is used later to download the software from S3 storage (IBM Cloud Object Storage). On SUSE: `zypper update -y; zypper install -y aws-cli`. On RHEL: `yum update -y; yum install epel-release; yum install -y awscli`.

### Setting up NFS server for SAP installation files -- Ansible
{: #powervs-set-up-nfs-server-ansible}

To perform the previous steps through ansible automation, download ansible-galaxy collection `ibm.power_linux_sap`.

```yaml
ansible-galaxy collection install ibm.power_linux_sap
```
{: pre}

After the ansible collection is installed, update the variable file `playbook/vars/sample-variables-configure-network-services-host.yml` to set up NFS.

```yaml
server_config: {
  nfs: { enable: true, nfs_directory: "/NFS;/hana/software" }
}
```
{: pre}

After file is updated, run the following ansible-playbook command.

```yaml
ansible-playbook \--connection=local -i \"localhost,\" playbooks/sample-configure-network-services-host.yml
```
{: pre}

This ansible execution makes sure that the host for network services acts as an NFS server.

### Setting up NTP proxy and forwarder
{: #powervs-set-up-ntp-server}

1. Ensure all required ports in the security group in {{site.data.keyword.vpc_short}} for network services that are used by host for private services are open. The needed ports are configured for private services. Required port for NTP forwarder is: 123 (TCP).
1. Log in to the network services instance. SSH command example:

   ```bash
   ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand=\"ssh -W %h:%p root@\<access_host_floating_ip\>\" root@\<network_services_host_private_ip\>
   ```
   {: pre}

1. Make sure that the required NTP software is available. On SUSE: `zypper update -y; zypper install -y chrony`. On RHEL: `yum update -y; yum install epel-release; yum install -y chrony`.
1. Modify file '/etc/chrony.conf' and add following lines. Replace `\<pvs_mgmt_cidr\>` with {{site.data.keyword.powerSys_notm}} management CIDR block (network segment)

   ```yaml
   local stratum 10
   manual
   allow \<pvs_mgmt_cidr \>
   ```
   {: pre}

5. Enable and start chrony service: `systemctl stop chronyd; systemctl start chronyd; systemctl enable chronyd;`.


### Setup NTP proxy or forwarder -- Ansible
{: #powervs-set-up-ntp-server-ansible}

To perform previous steps through ansible automation, download ansible-galaxy collection `ibm.power_linux_sap`.

```yaml
ansible-galaxy collection install ibm.power_linux_sap
```
{: pre}

After the ansible collection is installed, to set up NTP, update the variable file `playbook/vars/sample-variables-configure-network-services-host.yml`.

```yaml
server_config: {
  ntp: { enable: true }
}
```
{: pre}

After the file is updated, run the following ansible-playbook command.

```yaml
ansible-playbook \--connection=local -i \"localhost,\" playbooks/sample-configure-network-services-host.yml
```
{: pre}

This ansible execution ensures that the NTP proxy is configured on the host for network services.

### Setting up IBM Cloud-native DNS service (DNS option 1)
{: #powervs-set-up-dns-service}

You can use IBM Cloud DNS service that is directly reachable from IBM {{site.data.keyword.powerSys_notm}}s over custom resolver. For more information, see the following links.

* [Setting up an instance](/docs/dns-svcs?topic=dns-svcs-setting-up-your-dns-instance)
* [Managing DNS zones](/docs/dns-svcs?topic=dns-svcs-managing-dns-zones&interface=ui)
* [Managing permitted networks](/docs/dns-svcs?topic=dns-svcs-managing-permitted-networks&interface=ui)
* [Managing DNS records](/docs/dns-svcs?topic=dns-svcs-managing-dns-records)
* [Configuring custom resolver](/docs/dns-svcs?topic=dns-svcs-ui-create-cr).

Specify VPE subnet of {{site.data.keyword.vpc_short}} for network services as location in the resolver.

Make sure that all required ports in the security group in {{site.data.keyword.vpc_short}} for network services that are used by host for private services are open. The needed ports are configured for private services. Required port for DNS is: 53 (TCP and UDP). As result, you get a private IP address that you can specify in {{site.data.keyword.powerSysShort}} instances as DNS endpoints by private subnet configuration. These IP addresses are entered into `/etc/resolv.conf` in the operating system.

### Setting up DNS forwarder (DNS option 2)
{: #powervs-set-up-dns-forwarder}

If you use your own DNS service, its IP must be reachable from VPC for network services. If the IP is not directly reachable from IBM Power Systems Virtual Servers, a DNS forwarder on the host for critical management services is required. Use the following steps to complete the configuration:

1. Make sure that all required ports in the security group in {{site.data.keyword.vpc_short}} for network services that are used by host for private services are open. The needed ports are configured for private services. Required port for DNS is: 53 (TCP and UDP).
1. Log in to the private services instance. You can use the following SSH command example:

   ```bash
   ssh -A -o ServerAliveInterval=60 -o ServerAliveCountMax=600 -o ProxyCommand=\"ssh -W %h:%p root@\<access_host_floating_ip\>\" root@\<network_services_host_private_ip\>
   ```
   {: pre}

1. Make sure that the required DNS software is available. On SUSE: `zypper update -y; zypper install -y bind`. On RHEL: `yum update -y; yum install epel-release; yum install -y bind`.
1. Modify file `/etc/named.conf`:
   * Add the following lines at the beginning of the file direct after the starting comment (before the `options` section starts). Replace `\<pvs_mgmt_cidr\>` with {{site.data.keyword.powerSys_notm}} management CIDR block (network segment)

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
{: #powervs-set-up-dns-forwarder-ansible}

To perform the previous steps through ansible automation, download ansible-galaxy collection `ibm.power_linux_sap`.

```yaml
ansible-galaxy collection install ibm.power_linux_sap
```
{: pre}

After the ansible collection is installed, to set up proxy, update the variable file `playbook/vars/sample-variables-configure-network-services-host.yml`

```yaml
server_config: {
  dns: { enable: false, dns_servers: "161.26.0.7; 161.26.0.8; 9.9.9.9;" }
}
```
{: pre}

After the file is updated, run the following ansible-playbook command.

```yaml
ansible-playbook \--connection=local -i \"localhost,\" playbooks/sample-configure-network-services-host.yml
```
{: pre}

This ansible execution ensures that the DNS forwarder services are configured on {{site.data.keyword.powerSys_notm}}.

### Optional steps on {{site.data.keyword.vpc_short}} for other services
{: #vpc-management-services}

To create other VPC resources, such as load balancer, and other resources, see [Using the IBM Cloud console to create VPC resources](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console).

## Terraform automation - Deploying the VPC landing zone for {{site.data.keyword.powerSys_notm}}
{: #powervs-vpc-landing-zone-automation}

All the above steps are automated using terraform and ansible and is available as a Deployable Architecture Solution in IBM Cloud Catalog. To configure and deploy check the [link](/docs/powervs-vpc?topic=powervs-vpc-deploy-arch-ibm-pvs-inf-standard) for further details.
