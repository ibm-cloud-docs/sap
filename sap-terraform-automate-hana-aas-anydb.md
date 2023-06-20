---

copyright:
  years: 2023
lastupdated: "2023-06-14"

subcollection: sap

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:ui: .ph data-hd-interface="ui"}
{:terraform: .ph data-hd-interface="terraform"}

# Deploying Additional Application Server (AAS) to SAP HANA and AnyDB on an existing {{site.data.keyword.cloud_notm}} VPC with automation
{: #automate-hana-aas-anydb-terraform-ansible}

You can use Terraform scripts to create a single-tier VPC and create the AAS to HANA and AnyDB infrastructure on the VPC. The Terraform scripts use the VPC information that you provide and then call the Ansible playbook to create the SAP architecture on the specified VPC. Terraform on {{site.data.keyword.cloud}} enables predictable and consistent provisioning of {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) infrastructure resources so that you can rapidly build complex, cloud environments. {{site.data.keyword.cloud_notm}} VPC infrastructure consists of SAP certified hardware that uses Intel Xeon CPUs and additional Intel technologies.
{: #shortdesc}

## What is created
{: #automate-hana-aas-anydb-what-created}

The scripts use the information that you provide for an existing VPC and deploy AAS to SAP HANA or AnyDB on different host than CI (SAP Central Instance) VSI host. For more information about this architecture, see [SAP NetWeaver 7.x on UNIX with HANA or AnyDB on IBM Cloud VPC on {{site.data.keyword.cloud_notm}} VPC](/docs/sap?topic=sap-sap-refarch-nw-sybase). You specify the information for the VPC to use in the `input.auto.tfvars` file.

The scripts call the Ansible Playbook to install the SAP architecture.

## Script files
{: #automate-hana-aas-anydb-script-files}

The configuration and script files are provided on GitHub. Each supported interface for the SAP solution installation has its own folder in the GitHub repository:

*   [Github repository for Terraform - AAS SYB](https://github.com/IBM-Cloud/sap-aas-abap-ase-syb/tree/main/cli).
*   [Github repository for Terraform – AAS HANA](https://github.com/IBM-Cloud/sap-abap-hana-aas)

## Terraform interface
{: #automate-hana-aas-anydb-terraform-interface}
{: terraform}

To run the Terraform scripts, you modify:

* The input.auto.tfvars file to specify the existing VPC resources for your solution. You specify the variables for the existing VPC:

    *   VPC name
    *   Security group
    *   Subnet
    *   HostName
    *   Profile
    *   Image
    *   Up to two SSH keys

    You can change the default SAP system configuration settings to match your solution. You also specify the location where you downloaded the SAP kits.

The {{site.data.keyword.cloud_notm}} Provider plug-in for Terraform on {{site.data.keyword.cloud_notm}} uses these configuration files to install AAS to SAP HANA and AnyDB on the specified VPC in your {{site.data.keyword.cloud_notm}} account.

## Support
{: #automate-hana-aas-anydb-support}

There are no warranties of any kind, and there is no service or technical support available for these materials from {{site.data.keyword.ibm}}. As a recommended practice, review carefully any materials that you download from this site before using them on a live system.

Though the materials provided herein are not supported by the {{site.data.keyword.IBM}} Service organization, your comments are welcomed by the developers, who reserve the right to revise, re-adapt or remove the materials at any time. To report a problem, or provide suggestions or comments, open a GitHub issue.

## Before you begin
{: #automate-hana-aas-anydb-before}

Before you use the scripts in the Bastion cli:

* Set up your account to access the VPC. Make sure that your account is [upgraded to a paid account](/docs/account?topic=account-accountfaqs#changeacct).
* If you have not already, create a Bastion server to store the SAP kits. For more information, see [Automate SAP bastion server - SAP media storage repository](/docs/sap?topic=sap-sap-bastion-server).
* Download the SAP kits from the SAP Portal to your Deployment Server. Make note of the download locations. Ansible decompresses the files. For more information, see the [readme](https://github.com/IBM-Cloud/sap-abap-hana-aas#readme) file. 
* [Create or retrieve an {{site.data.keyword.cloud_notm}} API key](/docs/account?topic=account-userapikey#create_user_key). The API key is used to authenticate with the {{site.data.keyword.cloud_notm}} platform and to determine your permissions for {{site.data.keyword.cloud_notm}} services.
* [Create or retrieve your SSH key ID](/docs/ssh-keys?topic=ssh-keys-getting-started-tutorial). You need the 40-digit UUID for the SSH key, not the SSH key name.
* Terraform should already be installed on the bastion server that you deployed. For more information, see [Bastion server for SAP deployment](/docs/sap?topic=sap-sap-bastion-server). 

## Creating the infrastructure by using Terraform with the Bastion server CLI
{: #automate-hana-aas-anydb-create-terraform}
{: terraform}

Use these steps to configure the IBM Cloud Provider plug-in and use Terraform to install SAP AAS to SAP HANA and AnyDB on your existing VPC on an already deployed SAP NetWeaver 7.X with SAP HANA 2.0 or ASE SYB as a Central Instance.

The scripts can take 1 - 2 hours to complete.

1. Access the Bastion server cli.
2. Clone the solution repository and change to the folder.

    ASE SYB 16 
    Clone the solution repository from https://github.com/IBM-Cloud/sap-aas-abap-ase-syb/cli and cd to the https://github.com/IBM-Cloud/sap-aas-abap-ase-syb/tree/main/cli folder.
    
    ```shell
    git clone https://github.com/IBM-Cloud/sap-aas-abap-ase-syb/cli.git
    cd sap-aas-abap-ase-syb/cli/

    ```

    SAP HANA 2.0: 
    Clone the solution repository from https://github.com/IBM-Cloud/sap-abap-hana-aas and cd to the https://github.com/IBM-Cloud/sap-abap-hana-aas folder.

    ```shell
    git clone https://github.com/IBM-Cloud/sap-abap-hana-aas.git
    cd sap-abap-hana-aas/
    ```

3. Modify the input.auto.tfvars file to specify the information for the existing VPC, your region, zone, networking component names, hostname for the AAS VSI,profile and image. You need your 40-digit SSH key ID for this file. The second SSH key is optional. For more options for profile, see [Instance Profiles](/docs/vpc?topic=vpc-profiles). For more options for image, see [Images](/docs/vpc?topic=vpc-about-images). For descriptions of the variables, see the [readme](https://github.com/IBM-Cloud/sap-abap-hana-aas#readme) file. 

    The VSI OS images that are supported for this solution for Netweaver Additional Application Server are:

    *   ibm-redhat-7-6-amd64-sap-applications-3
    *   ibm-redhat-8-4-amd64-sap-applications-2
    *   ibm-sles-15-3-amd64-sap-applications-2


    ```terraform
    #Infra VPC variables for ASE SYB
    REGION			= "eu-de"
    ZONE			= "eu-de-2"
    VPC				= "ic4sap"                        # EXISTING Security group name
    SECURITY_GROUP	= "ic4sap-securitygroup"      # EXISTING Security group name
    RESOURCE_GROUP  = "wes-automation"
    SUBNET			= "ic4sap-subnet"               # EXISTING Subnet name
    SSH_KEYS                = [ "r010-57bfc315-f9e5-46bf-bf61-d87a24a9ce7a" , "r010-3fcd9fe7-d4a7-41ce-8bb3-d96e936b2c7e" ]

    # SAP AAS VSI variables:
    HOSTNAME = "sapnwase-as01"
    PROFILE = "bx2-4x16"
    IMAGE = "ibm-redhat-8-4-amd64-sap-applications-2
    
    #Infra VPC variables for HANA 2.0 
    REGION			= "eu-de"
    ZONE			= "eu-de-2"
    VPC				= "ic4sap"                         EXISTING Security group name
    SECURITY_GROUP	= "ic4sap-securitygroup"      
    RESOURCE_GROUP  = "wes-automation"
    SUBNET			= "ic4sap-subnet"               
    SSH_KEYS                = [ "r010-57bfc315-f9e5-46bf-bf61-d87a24a9ce7a" , "r010-3fcd9fe7-d4a7-41ce-8bb3-d96e936b2c7e" ]

    #SAP AAS VSI variables for HANA 2.0:
    HOSTNAME = "sapnwapp"
    PROFILE = "bx2-4x16"
    IMAGE = "ibm-redhat-8-4-amd64-sap-applications-2
    ```


4. Customize your SAP system configuration. In the same file, input.auto.tfvars, edit the SAP system configuration variables that are passed to the Ansible automated deployment. For descriptions of the variables, see the [readme](https://github.com/IBM-Cloud/sap-abap-hana-aas#readme) file. 

    ```terraform

    ##SAP system configuration – for HANA 2.0 
    sap_sid	= "NWD"
    sap_ci_host = IP I.E. "10.243.132.10"
    sap_ci_hostname = "sapnwci" 
    sap_ci_instance_number = "00"
    sap_ascs_instance_number = "01"
    sap_aas_instance_number = "00"

    #Kits paths - for HANA 2.0
    kit_sapcar_file = "/storage/NW75HDB/SAPCAR_1010-70006178.EXE"
    kit_swpm_file =  "/storage/NW75HDB/SWPM10SP31_7-20009701.SAR"
    kit_saphotagent_file = "/storage/NW75HDB/SAPHOSTAGENT51_51-20009394.SAR"
    kit_hdbclient_file = "/storage/NW75HDB/IMDB_CLIENT20_009_28-80002082.SAR"
    ```

    Ansible decompresses the rest of the SAP kit files. For more information, see the [readme](https://github.com/IBM-Cloud/sap-abap-hana-aas#readme) file. 

5. Initialize the Terraform CLI.

    `terraform init`

6. Create a Terraform execution plan. The Terraform execution plan summarizes all the actions that are done to create the virtual private cloud instance in your account.

    `terraform plan plan1`

    You must enter an SAP main password and your API key.

    The SAP main password must be 10 - 14 characters long and contain at least one digit (0-9). It can contain only the following characters: a-z, A-Z, 0-9, @, #, $, _. This password cannot contain exclamation points '!'. The password must not start with a digit or an underscore (_).

7. Verify that the plan shows all of the resources that you want to create and that the names and values are correct. If the plan needs to be adjusted, edit the input.auto.tfvars file to correct resources and run terraform plan again.

8. Apply the saved plan.

    ```terraform
    terraform apply "plan1"
    ```
 
 The virtual private cloud and components are created and you see output similar to the `terraform plan` output.  

9. Add the SAP credentials and the virtual server instance IP to the SAP GUI. For more information about the SAP GUI, see [SAP GUI](https://help.sap.com/doc/7abd5470728810148a4b1a83b0e91070/1511%20000/en-US/frameset.htm).

## Next steps
{: #automate-hana-aas-anydb-next}
{: terraform}

If you need to rename your resources after they are created, modify the input.auto.tfvars file to change the names and run `terraform plan` and `terraform apply` again. Do not use the {{site.data.keyword.cloud_notm}} Dashboard and user interface to modify your VPC after it is created. The Terraform scripts create a complete solution and selectively modifying resources with the user interface might cause unexpected results.

If you need to remove the SAP Netweaver 7.X on HANA or ANYdb installation, go to your project folder and run `terraform destroy`. The `terraform destroy` command does not remove the VPC in this scenario because the VPC was created before these Terraform scripts were run.

## Related information
{: #automate-nw-asesyb-related}

For more information about Terraform on {{site.data.keyword.cloud_notm}}, see [Getting started with Terraform on {{site.data.keyword.cloud_notm}}](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).

For more information about using Terraform for creating only a VPC for SAP, without the SAP architecture, see [Creating single-tier virtual private cloud for SAP by using Terraform](/docs/sap?topic=sap-create-terraform-single-tier-vpc-sap).

SAP One Support Notes that apply to this document:

* [SAP Note 84555 - Windows Server, Linux&reg;, and UNIX: Certified hardware](https://launchpad.support.sap.com/#/notes/84855)
* [SAP Note 2927211 - SAP Applications on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
* [SAP Note 2923773 - Linux&reg; on {{site.data.keyword.cloud_notm}} (IaaS): Adaption of your SAP License](https://launchpad.support.sap.com/#/notes/2923773)
* [SAP Note 2414097 - SAP Applications on {{site.data.keyword.cloud_notm}} Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)
* [SAP Note 2369910 - SAP Software on Linux&reg;: General information](https://launchpad.support.sap.com/#/notes/2369910)
* [SAP Note 171380 - Released IBM hardware (Intel processors) and IBM cloud services offers](https://launchpad.support.sap.com/#/notes/171380)
* [SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)

This document is referenced by:

* [SAP Note 2927211 - SAP Applications on {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) Infrastructure environment](https://launchpad.support.sap.com/#/notes/2927211)
* [SAP Note 2588225 - SAP on {{site.data.keyword.cloud_notm}}: Protect against speculative execution vulnerabilities](https://launchpad.support.sap.com/#/notes/2588225)
* [SAP Note 1380654 - SAP support in IaaS environments](https://launchpad.support.sap.com/#/notes/1380654)
* [SAP Note 2414097 - SAP Applications on {{site.data.keyword.cloud_notm}} Classic Infrastructure environment](https://launchpad.support.sap.com/#/notes/2414097)
