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


# Accessing VPC zonal file storage shares from Virtual Server Instances in VPC
{: #nfs-zonal-vpc-intro}
{: toc-content-type="tutorial"}
{: toc-services="vpc, sap"}
{: toc-completion-time="1h"}

This tutorial might incur costs.
Use the [Cost Estimator](/estimator) to generate a cost estimate based on your projected usage.
{: tip}

In this tutorial, you learn how to mount a file share on Virtual server instance in VPC.


The following architecture overview diagram illustrates this scenario.

![Architecture overview diagram](../../images/nfs-zonal-vpc.svg){: figure caption="A diagram that shows the architecture for accessing File Storage for VPC."}

## Before you begin
{: #nfs-zonal-vpc-prereqs}

{{site.data.content.nfs-zonal-prereqs}}

## Creating a security group to allow NFS V4 traffic
{: #nfs-zonal-vpc-create-sg}
{: step}

{{site.data.content.nfs-create-sg}}

## Provisioning a file storage share
{: #nfs-zonal-vpc-create-fs-share}
{: step}

The availability cannot be modified after provisioning.{: important}

The encryption setting of a mount target cannot be modified after it has been created. To change the encryption, you must delete the existing mount target and recreate it. This process does not affect the underlying data, and no data will be lost.{: important}

{{site.data.content.nfs-create-zonal-share}}

## Gathering the file storage IP address and mount path information
{: #nfs-zonal-vpc-get-mount-path}
{: step}

{{site.data.content.nfs-get-mount-path}}

The **Mount path** parameter is used as an argument to the `mount` command on the Virtual server instance in VPC.


## Mounting the file share on the VPC Virtual server instance
{: #nfs-zonal-vpc-mount-file-share}
{: step}

Log on as the `root` user to the server instance in VPC where you want to mount the file share.

{{../../_include-segments/tutorial-file-storage-share-vpc-mount.md}}
