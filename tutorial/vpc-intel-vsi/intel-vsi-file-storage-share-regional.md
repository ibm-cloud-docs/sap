---
copyright:
   years: 2025
lastupdated: "2025-10-08"
keywords: SAP, {{site.data.keyword.cloud_notm}}, {{site.data.keyword.ibm_cloud_sap}}, NFS, File Storage Share, Network Load Balancer
subcollection: sap
content-type: tutorial
services: vpc, sap
account-plan: paid
completion-time: 1h
---

{{site.data.keyword.attribute-definition-list}}


# Accessing VPC regional file storage shares from Virtual Server Instances in VPC
{: #nfs-regional-vpc-intro}
{: toc-content-type="tutorial"}
{: toc-services="vpc, sap"}
{: toc-completion-time="1h"}

This tutorial might incur costs.
Use the [Cost Estimator](/estimator) to generate a cost estimate based on your projected usage.
{: tip}


In this tutorial, you learn how to mount a file share with regional availability on Virtual Server Instances in VPC.

The following architecture overview diagram illustrates this scenario.

![Architecture overview diagram](../../images/nfs-regional-vpc.svg){: figure caption="A diagram that shows the architecture for accessing File Storage for VPC with regional availability."}

## Before you begin
{: #nfs-regional-vpc-prereqs}

- [Create a VPC](/docs/vpc?topic=vpc-getting-started&interface=ui) spanning at least two availability zones, or use an existing one.

- Create the address prefixes and the subnets for the multizone region VPC.

- Create two virtual server instances in VPC
    Deploy one virtual server instance in each zone.
    Note the IP addresses of the virtual server instances.

- Check the user permissions.
   Make sure that your user account has permissions to create and manage VPC resources.
   See [Granting user permissions for VPC resources](/docs/vpc?topic=vpc-managing-user-permissions-for-vpc-resources).

- Use or create an SSH key to connect to the virtual server instances.
   If you don't have an SSH key, see [Getting started with SSH keys](/docs/vpc?topic=vpc-ssh-keys).




## Creating a security group to allow NFS V4 traffic
{: #nfs-regional-vpc-create-sg}
{: step}

{{site.data.content.nfs-create-sg}}

## Provisioning file storage with regional availability
{: #nfs-regional-vpc-create-fs-share}
{: step}

The availability cannot be modified after provisioning.{: important}

The encryption setting of a mount target cannot be modified after it has been created. To change the encryption, you must delete the existing mount target and recreate it. This process does not affect the underlying data, and no data will be lost.{: important}

{{site.data.content.nfs-create-regional-share}}

## Gathering the file storage IP address and mount path information
{: #nfs-regional-vpc-get-mount-path}
{: step}

{{site.data.content.nfs-get-mount-path}}

Use the **Mount path** parameter as an argument for the `mount` command on the Virtual Server instance in VPC.

## Mounting the file share on the VPC Virtual Server instance
{: #nfs-regional-vpc-mount-file-share}
{: step}

Log on as the `root` user to the server instance in VPC where you want to mount the file share.

{{../../_include-segments/tutorial-file-storage-share-vpc-mount.md}}
{{../../_include-segments/tutorial-file-storage-share-vpc-mount-eit.md}}
