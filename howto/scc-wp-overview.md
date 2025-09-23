---
copyright:
  years: 2025
lastupdated: "2025-09-22"
keywords: SAP, SAP SCC Workload Protection, {{site.data.keyword.sysdigsecure_full_notm}}, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Getting started with {{site.data.keyword.sysdigsecure_full}} for SAP systems
{: #scc-wp-getting-started}

[{{site.data.keyword.sysdigsecure_full_notm}}](/docs/workload-protection?topic=workload-protection-getting-started) can be used to find and prioritize software vulnerabilities, detect and respond to threats, manage configurations, permissions, and compliance from source to run.
{: shortdesc}

This section describes how to set up {{site.data.keyword.sysdigsecure_short}} with {{site.data.keyword.powerSys_notm}} instances and with {{site.data.keyword.vsi_is_full}} instances for SAP.

All virtual server instances in the example are using RHEL or SLES Linux operating systems. However, links to instructions for how to setup the {{site.data.keyword.sysdigsecure_short}} agent on other operating systems are provided in the agent setup section.

## Features
{: #scc-wp-features}

{{site.data.keyword.sysdigsecure_full_notm}} provides a multitude of features. The following points describes a selection that's relevant for SAP environments. For a full list, refer to [{{site.data.keyword.sysdigsecure_short}} Key features](/docs/workload-protection?topic=workload-protection-key-features)

### Cloud Security Posture Management (CSPM)
{: #scc-wp-feature_1}

- Provides a unified and centralized platform to manage the security and compliance of applications, workloads and infrastructure that run on {{site.data.keyword.cloud_notm}}, in other clouds, and on-prem, covering managed services, hosts or VMs and containers, OpenShift or Kubernetes.

- Dozens of out-of-the-box frameworks such as Financial Services, PCI, DORA, CIS or NIST allow to implement and validate the controls that are required to meet industry standards and laws.

- Cloud Security Posture Management (CSPM) to help you identify misconfigurations and validate compliance on the cloud across {{site.data.keyword.cloud_notm}}, AWS, Azure and GCP or inside hosts, VMs and Kubernetes, including VPC, VMware or PowerVS and IBM Z with Linux.

- Assisted remediation instructions to remediate the failing controls or ability to accept known risks.

- Offers an inventory of all your Cloud assets (compute resources, managed services, identities and entitlements) and hosts, VMs and clusters, whether they are in the Cloud or on-prem.

- Advanced risk prioritization correlating misconfigurations, public exposure, in-use context (vulnerabilities, active permissions) and high confidence threat detection. Visualize risks through a correlation vector map providing security teams full situational awareness beyond just static posture evaluation.

- Gain visibility into cloud identities and manage permissions through the Cloud Infrastructure Entitlement Management (CIEM) capabilities: identify inactive users or with excessive permissions. Optimize access policies to grant just enough privileges across users, groups, roles and machine identities.

- Cloud Detection and Response (CDR) to investigate suspicious activity across overly privileged users accessing sensitive data. Gain real-time visibility by monitoring cloud security controls, detecting configuration changes and preventing drift across cloud accounts.

- Analyze infrastructure as code (IaC) security posture including Terraform, CloudFormation, Helm charts or YAML manifests.


### Vulnerability Management
{: #scc-wp-feature_2}

- Scanning for vulnerabilities on OS packages and 3rd-party libraries such as Java, Python, Golang, Javascript, or Ruby.

- Implement scanning across your application lifecycle, from your CI/CD pipeline, container image registry or during runtime in hosts, VMs or Kubernetes/OpenShift clusters.

- Adds in-use runtime context to filter out vulnerabilities exposed on running applications, reducing vulnerabilities to immediately fix by 85% on average.

- Additional filters for prioritization such as exploit available, fix available, etc.

- Vulnerability management overview dashboard, advanced and customizable reporting, alerting of new vulnerabilities or unscanned images and routing to ticketing systems.

- Advanced policies to customize vulnerability criteria and blocking per environment or any scope.


### Server Endpoint Detection and Response (EDR)
{: #scc-wp-feature_3}

- Instruments hosts, VMs and Kubernetes/OpenShift clusters through eBPF to inspect all system activity through system calls with minimal performance footprint.

- Multiple thousands of OOTB policies updated weekly by our Threat Research team. Rules can be customized or you can create new ones using the Falco language. Falco is the open source Cloud-native standard for runtime security.

- In addition to rules, behavioral analysis allows detection of common threats and malware such as crypto mining activities and workload profiling to automatically define expected behavior can extend detection capabilities.

- Preemptive blocking, preventing blacklisted or malicious binaries from execution, including identified malware or drifted binaries in a container image.

- Advanced remediation, allowing to automatically execute corrective actions including killing processes, killing or pausing containers, etc.

- FIM (File Integrity Monitoring) detection rules that can be defined per scope (path, filename, cmd, user, etc) and detect at source all possible activities (read, write a file, write a directory, etc).


### Incident response and forensics
{: #scc-wp-feature_5}

- Automates the collection and correlation of events, posture, and vulnerabilities to identities across hosts, VMs, containers and Cloud activity.

- Attack Path Analysis provides attack chain visualization and empowers security analysts to rapidly understand the relationships between resources, and their implications for the attack chain across any cloud environment.

- Understand suspicious identity behaviors such as unusual logins, impossible travel scenarios, and malicious IP addresses. With this context, teams can rapidly understand the who, what, where, and how of threat actors in their infrastructure.

- Record for audit and investigation all executed commands (and the process tree), file changes or network connections, across hosts and containers.

- Trigger the capture of all system activity (SCAP) with any policy violation, to perform in-depth investigation and reconstruction of attacker activity, even if the container is long gone.

- Forward security events to your SIEM or other security response and alerting tools.


## Architecture
{: #scc-wp-architecture}

To use {{site.data.keyword.sysdigsecure_short}}, all that's needed is a {{site.data.keyword.sysdigsecure_short}} instance and the installation of the {{site.data.keyword.sysdigsecure_short}} agent on the host OS. Below is an example setup with one x86_64 Virtual Server Instance in VPC and one {{site.data.keyword.powerSys_notm}}.

![Figure 1. {{site.data.keyword.sysdigsecure_full_notm}} for SAP environments](../images/scc-wp-architecture.svg "{{site.data.keyword.sysdigsecure_full_notm}} for SAP environments"){: caption="{{site.data.keyword.sysdigsecure_full_notm}} for SAP environments" caption-side="bottom"}
{: external download="scc-wp-architecture.svg"}


## Understanding your responsibilities when using {{site.data.keyword.sysdigsecure_full_notm}}
{: #scc-wp-responsibilities}

It's important that you understand the responsibilities you have when using {{site.data.keyword.sysdigsecure_full_notm}} with SAP systems in {{site.data.keyword.cloud_notm}}. Therefore, make sure to familiarize yourself with the following page: [Understanding your responsibilities when using {{site.data.keyword.sysdigsecure_full_notm}}](/docs/workload-protection?topic=workload-protection-shared-responsibilities).


## Next steps
{: #scc-wp-next-steps}

To set up {{site.data.keyword.sysdigsecure_short}} with your SAP systems, you must perform the following tasks.

* [Creating an {{site.data.keyword.sysdigsecure_full}} instance](/docs/sap?topic=sap-scc-wp-create-instance)
* [Set up the {{site.data.keyword.sysdigsecure_short}} agent](/docs/sap?topic=sap-scc-wp-agent-setup)
* [{{site.data.keyword.sysdigsecure_short}} Dashboard Overview](/docs/sap?topic=sap-scc-wp-dashboard-overview)
