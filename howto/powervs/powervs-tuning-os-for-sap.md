---
copyright:
  years: 2023, 2026
lastupdated: "2026-10-30"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, {{site.data.keyword.powerSys_notm}} Instance, SAP HANA DB, SAP Netweaver, Storage, Tune, Tuning OS, saptune, Ansible Roles, RHEL System Roles, RHEL SAP Roles, Ansible galaxy, Power linux sap
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Tuning operating system for SAP software installation
{: #powervs-tuning-os-for-sap}

Complete the following steps for the virtual server instances that host the SAP HANA system and SAP application server.
You can skip these steps for the virtual server instance that hosts the SAP shared file systems.

## Configuring RHEL for SAP applications
{: #powervs-configure-rhel}

### RHEL System Roles
{: #powervs-rhel-system-roles}

Red Hat Enterprise Linux (RHEL) System Roles for SAP are a collection of Ansible roles that help you configure a RHEL system to install SAP HANA or SAP application server.
Red Hat distributes and maintains the Ansible roles for SAP configuration, so the tasks and required parameters can differ depending on the version of the `rhel-system-roles-sap` package.
The RHEL image that IBM provides includes the Ansible execution engine, SAP-related system roles, and the Ansible execution files.

The RHEL system roles for preparing the operating system settings for SAP HANA or SAP application server are available in `/root` directory.

Use the following command to prepare the virtual server instance for an SAP HANA workload:

```sh
ansible-playbook -i /root/inventory /root/sap-hana.yml
```
{: pre}

Use the following command to prepare the virtual server instance for an SAP application server workload:

```sh
ansible-playbook -i /root/inventory /root/sap-netweaver.yml
```
{: pre}

For more information about customizing the operating system, see the following documentation:
* [SAP Note 2772999 - Red Hat Enterprise Linux 8.x: Installation and Configuration](https://me.sap.com/notes/2772999){: external}
* [SAP Note 2777782 - SAP HANA DB: Recommended OS Settings for RHEL8](https://me.sap.com/notes/2777782){: external}
* [SAP Note 3108316- Red Hat Enterprise Linux 9.x: Installation and Configuration](https://me.sap.com/notes/3108316){: external}
* [SAP Note 3108302 - SAP HANA DB: Recommended OS Settings for RHEL9](https://me.sap.com/notes/3108302){: external}
* [SAP Note 2382421 - Optimizing the Network Configuration on HANA- and OS-Level](https://me.sap.com/notes/2382421){: external}
* [Red Hat Enterprise Linux System Roles for SAP](https://access.redhat.com/sites/default/files/attachments/rhel_system_roles_for_sap_1.pdf){: external}

If you use your own RHEL subscription instead of the IBM-provided RHEL image, install the Ansible Engine and RHEL System Roles for SAP before you prepare the operating system for SAP workloads. For more information, see [Red Hat Enterprise Linux for SAP Solutions](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/9/html-single/red_hat_enterprise_linux_system_roles_for_sap/index){: external} that describes in details how to configure your own RHEL for SAP applications.
{: note}

### Configuring jumbo frames
{: #powervs-config-jumbo-frames-rhel}

Enable jumbo frames by setting `MTU=9000` on RHEL by using (`nmcli`).

Identify the connection name for the network manager.
The following example shows the commands for the `System env2`connection.

1. Verify the current MTU size for the connection.

   ```sh
    nmcli con show "System env2" | grep -E "802-3-ethernet.mtu|ethtool.feature-tso"
    802-3-ethernet.mtu:                     1500
   ```
   {: screen}

1. Enable jumbo frames by setting the MTU size to 9000. Also, enable TCP segmentation offload (TSO).

   ```sh
   nmcli connection modify "System env2" \
     802-3-ethernet.mtu 9000 \
     ethtool.feature-tso on
   ```
   {: screen}

1. Verify the new settings.

   ```sh
   nmcli con show "System env2" | grep -E "802-3-ethernet.mtu|ethtool.feature-tso"
   802-3-ethernet.mtu:                     9000
   ethtool.feature-tso:                    on
   ```
   {: screen}

## Configuring SLES for SAP applications
{: #powervs-config-sles-sap}

## Configuring saptune
{: #powervs-config-saptune-sles}

Use the `saptune` tool to apply the recommended operating system settings for SLES to the SAP HANA system (HANA) or the application server (NETWEAVER).
For more information about `saptune`, see [SAP Note 1275776 - Linux: Preparing SLES for SAP environments](https://me.sap.com/notes/1275776){: external}.

1. Verify that the `saptune` package is installed.

    ```sh
    zypper info saptune
    ```
    {: pre}

1. Verify that the `saptune` version is 3 or later.

    ```sh
    saptune version
    ```
    {: pre}

1. List all available solutions. Numbered entries represent integrated SAP Notes for each of the solutions.

    ```sh
    saptune solution list
    ```
    {: pre}

1. Review the available options.

    ```sh
    saptune --help
    ```
    {: pre}

1. Enable and start the `saptune.service`. This command disables `sapconf` and `tuned`, which are no longer used.

    ```sh
    saptune service takeover
    ```
    {: pre}

1. Optionally simulate the changes first before they are applied.

    For SAP HANA:

    ```sh
    saptune solution simulate HANA
    ```
    {: pre}

    For SAP NetWeaver:

    ```sh
    saptune solution simulate NETWEAVER
    ```
    {: pre}

1. Apply the `saptune` solution.

    For SAP HANA system:

    ```sh
    saptune solution apply HANA
    ```
    {: pre}

    For application server:

    ```sh
    saptune solution apply NETWEAVER
    ```
    {: pre}

1. Check the `saptune` status.

    ```sh
    saptune status
    ```
    {: pre}

1. Verify that `saptune` is set up correctly.

    ```sh
    saptune check
    ```
    {: pre}

### Configuring jumbo frames
{: #powervs-config-jumbo-frames-sles}

Enable jumbo frames by setting `MTU=9000` on SLES by using `wicked`.

Identify the device name for the network adapter.
The following example shows the commands for device name `env2`.

- Edit the `/etc/sysconfig/network/ifcfg-env2` file, and verify that it contains the following parameters:

   ```sh
   MTU='9000'
   ETHTOOL_OPTS='-K env2 tso on'
   ```
   {: screen}

- Restart the interface.

   ```sh
   wicked ifdown env2 && wicked ifup env2
   ```
   {: screen}

## Checking the nonuniform memory access layout
{: #powervs-numa-layout}

Check that the CPU and memory placement is optimized for SAP HANA by running the `chk_numa_lpm.py`script.

The script runs the following checks:

- NUMA Layout Validation
   Verifies the nonuniform memory access (NUMA) configuration according to SAP HANA requirements.
   - Verifies that local memory is available for each CPU core.
   - Verifies that the memory distribution across the cores does not exceed a 50% variance.
   - Generates error messages if a core lacks memory, and warning messages if the memory distribution exceeds the margin.

- Live Partition Mobility (LPM) Check
   Detects whether an LPM operation occurred since the last system restart. An LPM operation can alter the NUMA layout from its boot-time configuration.
   - Searches system logs for the most recent LPM event.
   - Issues a warning if an LPM operation is detected.

Steps to run the Check

1. Review the [SAP Note 2923962](https://me.sap.com/notes/2923962){: external}.
1. Download the `chk_numa_lpm.py` script that is attached to the SAP Note and copy it to your virtual server instance.
1. Set executable permissions for the script:

    ```sh
    chmod +x ./chk_numa_lpm.py
    ```
    {: pre}

1. Run the script:

    ```sh
    ./chk_numa_lpm.py
    ```
    {: pre}

## If you use your own subscriptions
{: #powervs-config-own-image}

If you use your own subscriptions, use the following command to install the collection:

```sh
    yum install rhel-system-roles-sap
```
{: pre}

Then, create the playbook and update it to point to the required roles.

Your infrastructure is now ready to install SAP software.
