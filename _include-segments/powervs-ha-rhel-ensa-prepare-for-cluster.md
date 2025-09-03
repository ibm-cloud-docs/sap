Use the following steps to prepare the SAP instances for cluster integration.

### Disabling the automatic start of the SAP instance agents for ASCS and ERS
{: #ha-rhel-ensa-disable-agents}

Disable the automatic start of the `sapstartsrv` instance agents for both *ASCS* and *ERS* instances after a system reboot.

#### Verifying the SAP instance agent integration type
{: #ha-rhel-ensa-verify-sap-services}

Recent versions of the SAP instance agent `sapstartsrv` provide native `systemd` support on Linux.
For more information, refer to the the SAP notes that are listed at [SAP Notes](/docs/sap?topic=sap-ha-rhel-refs#ha-rhel-refs-sap-notes).

On both nodes, check the content of the `/usr/sap/sapservices` file.
```sh
cat /usr/sap/sapservices
```
{: pre}

In the `systemd` format, entries begin with `systemctl` commands.

Example:

```sh
systemctl --no-ask-password start SAPS01_01 # sapstartsrv pf=/usr/sap/S01/SYS/profile/S01_ASCS01_cl-sap-scs
```
{: screen}

If the ASCS and ERS entries use `systemd` format, continue with the steps in [Registering the ASCS and the ERS instances](#ha-rhel-ensa-register-instances).
In the `classic` format, entries begin with `LD_LIBRARY_PATH` definitions.

Example:

```sh
LD_LIBRARY_PATH=/usr/sap/S01/ASCS01/exe:$LD_LIBRARY_PATH;export LD_LIBRARY_PATH;/usr/sap/S01/ASCS01/exe/sapstartsrv pf=/usr/sap/S01/SYS/profile/S01_ASCS01_cl-sap-scs -D -u s01adm
```
{: screen}

If the entries for ASCS and ERS are in `classic` format, modify the `/usr/sap/sapservices` file to prevent the automatic start of the `sapstartsrv` instance agents for both *ASCS* and *ERS* instances after a system reboot.

On both nodes, remove or comment out the `sapstartsrv` entries for *ASCS* and *ERS* in the *SAP services* file.

```sh
sed -i -e 's/^LD_LIBRARY_PATH=/#LD_LIBRARY_PATH=/' /usr/sap/sapservices
```
{: pre}

Example:

```sh
#LD_LIBRARY_PATH=/usr/sap/S01/ASCS01/exe:$LD_LIBRARY_PATH;export LD_LIBRARY_PATH;/usr/sap/S01/ASCS01/exe/sapstartsrv pf=/usr/sap/S01/SYS/profile/S01_ASCS01_cl-sap-scs -D -u s01adm
```
{: screen}

Proceed to [Installing permanent SAP license keys](#ha-rhel-ensa-install-lic-key).

#### Registering the ASCS and the ERS instances
{: #ha-rhel-ensa-register-instances}

Register the SAP instances on both nodes.

1. Login as the `root` user on both nodes.

1. Set the `LD_LIBRARY_PATH` environment variable to include the ASCS instance executable directory, and register the ASCS instance.

   ```shell
   export LD_LIBRARY_PATH=/usr/sap/${SID}/ASCS${ASCS_INSTNO}/exe && \
   /usr/sap/${SID}/ASCS${ASCS_INSTNO}/exe/sapstartsrv \
      pf=/usr/sap/${SID}/SYS/profile/${SID}_ASCS${ASCS_INSTNO}_${ASCS_VH} -reg
   ```

1. Repeat the registration step for the ERS instance by using the ERS profile.

   ```shell
   export LD_LIBRARY_PATH=/usr/sap/${SID}/ERS${ERS_INSTNO}/exe && \
   /usr/sap/${SID}/ERS${ERS_INSTNO}/exe/sapstartsrv \
      pf=/usr/sap/${SID}/SYS/profile/${SID}_ERS${ERS_INSTNO}_${ERS_VH} -reg
   ```

#### Disabling systemd services of the ASCS and the ERS instances
{: #ha-rhel-ensa-disable-systemd-services}

On both nodes, disable the systemd service for the ASCS instance agent.

```sh
systemctl disable --now SAP${SID}_${ASCS_INSTNO}.service
```
{: pre}

Then, disable the systemd service for the ERS instance agent.

```sh
systemctl disable --now SAP${SID}_${ERS_INSTNO}.service
```
{: pre}

#### Disabling `systemd` restart of a crashed ASCS or ERS instance
{: #ha-rhel-ensa-disable-crash-restart}

`Systemd` includes built-in mechanisms for restarting crashed services.
In a high availability setup, only the HA cluster should manage the SAP *ASCS* and *ERS* instances.
To prevent `systemd` from automatically restarting these instances, create drop-in configuration files on both cluster nodes.

On both nodes, create the directories for the drop-in files.

```sh
mkdir /etc/systemd/system/SAP${SID}_${ASCS_INSTNO}.service.d
```
{: pre}

```sh
mkdir /etc/systemd/system/SAP${SID}_${ERS_INSTNO}.service.d
```
{: pre}

On both nodes, create the drop-in files for *ASCS* and *ERS*.

```sh
cat >> /etc/systemd/system/SAP${SID}_${ASCS_INSTNO}.service.d/HA.conf << EOT
[Service]
Restart=no
EOT
```
{: pre}

```sh
cat >> /etc/systemd/system/SAP${SID}_${ERS_INSTNO}.service.d/HA.conf << EOT
[Service]
Restart=no
EOT
```
{: pre}

`Restart=no` must be in the `[Service]` section, and the drop-in files must be available on all cluster nodes.
{: note}

On both nodes, reload the `systemd` unit files.

```sh
systemctl daemon-reload
```
{: pre}

### Installing permanent SAP license keys
{: #ha-rhel-ensa-install-lic-key}

When the SAP *ASCS* instance runs on a {{site.data.keyword.powerSys_notm}} instance, the SAP license mechanism uses the partition UUID to generate the hardware key.
For details, see [SAP note 2879336 - Hardware key based on unique ID](https://me.sap.com/notes/2879336){: external}.

On both nodes, run the following command as the *sidadm* user to retrieve the hardware key.

```sh
sudo -i -u ${sid}adm -- sh -c 'saplikey -get'
```
{: pre}

Sample output:

```sh
$ sudo -i -u ${sid}adm -- sh -c 'saplikey -get'

saplikey: HARDWARE KEY = H1428224519
```
{: screen}

Record the `HARDWARE KEY` from each node.

You need the hardware keys from both nodes to request separate SAP license keys.
For guidance on requesting license keys for failover systems, refer to the following SAP Notes:
- [2879336 - Hardware key based on unique ID](https://me.sap.com/notes/2879336){: external}
- [2662880 - How to request SAP license keys for failover systems](https://me.sap.com/notes/2662880){: external}

### Installing SAP resource agents
{: #ha-rhel-ensa-install-sap-resource-agents}

Install the required software packages.
The `resource-agents-sap` package provides the *SAPInstance cluster resource agent* that is used to manage SAP instances.

If `sap_cluster_connector` is not configured for the SAP instance, the RHEL HA Add-On cluster treats any state change as a potential issue.
When external SAP tools such as `sapcontrol` are used to manage the instance, `sap_cluster_connector` enables safe interaction with SAP instances running inside the cluster.
If SAP instances are managed exclusively by cluster tools, `sap_cluster_connector` is not required.

Install the packages for the cluster resource agent and the *SAP Cluster Connector* library.
For details, see [How to enable the SAP HA Interface for SAP ABAP application server instances managed by the RHEL HA Add-On](https://access.redhat.com/solutions/3606101){: external}

On both nodes, run the following commands.

If necessary, use `subscription-manager` to enable the SAP NetWeaver repository.
For instructions, refer to the [RHEL for SAP Subscriptions and Repositories](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/8/html/rhel_for_sap_subscriptions_and_repositories/asmb_enable_repo_rhel-for-sap-subscriptions-and-repositories-8#con_hana_rhel-for-sap-subscriptions-and-repositories-8){: external} documentation.

```sh
subscription-manager repos --enable="rhel-8-for-ppc64le-sap-netweaver-e4s-rpms"
```
{: pre}

Install the required packages.

```sh
dnf install -y resource-agents-sap sap-cluster-connector
```
{: pre}

### Configuring SAP Cluster Connector
{: #ha-rhel-ensa-configure-sap-cluster-connector}

Add the *sidadm* user to the `haclient` group on both nodes.

```sh
usermod -a -G haclient ${sid}adm
```
{: pre}

### Adapting the SAP instance profiles
{: #ha-rhel-ensa-modify-sap-profiles}

Modify the start profiles of SAP instances that are managed by *SAP tools* outside the cluster.
The RHEL HA Add-On cluster and its resource agents can control both *ASCS* and *ERS* instances.
To prevent automatic restarts of instance processes, adjust the SAP instance profiles accordingly.

On NODE1, change to the SAP profile directory.

```sh
cd /sapmnt/${SID}/profile
```
{: pre}

Replace all `Restart_Program` entries with `Start_Program` in the *ASCS* and *ERS* instance profiles.

```sh
sed -i -e 's/Restart_Program_\([0-9][0-9]\)/Start_Program_\1/' ${SID}_ASCS${ASCS_INSTNO}_${ASCS_VH}
```
{: pre}

```sh
sed -i -e 's/Restart_Program_\([0-9][0-9]\)/Start_Program_\1/' ${SID}_ERS${ERS_INSTNO}_${ERS_VH}
```
{: pre}

Append the following lines to the end of the *ASCS* and *ERS* instance profiles to enable `sap_cluster_connector` integration:

```sh
service/halib = $(DIR_EXECUTABLE)/saphascriptco.so
service/halib_cluster_connector = /usr/bin/sap_cluster_connector
```
{: screen}
