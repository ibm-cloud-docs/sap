Use the following steps to prepare the SAP instances for the cluster integration.

### Disabling the automatic start of the SAP instance agents for ASCS and ERS
{: #ha-rhel-ensa-disable-agents}

You must disable the automatic start of the `sapstartsrv` instance agents for both *ASCS* and *ERS* instances after a reboot.

#### Verifying the SAP instance agent integration type
{: #ha-rhel-ensa-verify-sap-services}

Recent versions of the SAP instance agent `sapstartsrv` provide native `systemd` support on Linux.
For more information, refer to the the SAP notes that are listed at [SAP Notes](/docs/sap?topic=sap-ha-rhel-refs#ha-rhel-refs-sap-notes).

On both nodes, check the content of the `/usr/sap/sapservices` file.
```sh
cat /usr/sap/sapservices
```
{: pre}

In the `systemd` format, the lines start with `systemctl` entries.

Example:

```sh
systemctl --no-ask-password start SAPS01_01 # sapstartsrv pf=/usr/sap/S01/SYS/profile/S01_ASCS01_cl-sap-scs
```
{: screen}

If the entries for ASCS and ERS are in `systemd` format, continue with the steps in [Disabling systemd services of the ASCS and the ERS SAP instance](#ha-rhel-ensa-disable-systemd-services).

In the `classic` format, the lines start with `LD_LIBRARY_PATH` entries.

Example:

```sh
LD_LIBRARY_PATH=/usr/sap/S01/ASCS01/exe:$LD_LIBRARY_PATH;export LD_LIBRARY_PATH;/usr/sap/S01/ASCS01/exe/sapstartsrv pf=/usr/sap/S01/SYS/profile/S01_ASCS01_cl-sap-scs -D -u s01adm
```
{: screen}

If the entries for ASCS and ERS are in `classic` format, then modify the `/usr/sap/sapservices` file to prevent the automatic start of the `sapstartsrv` instance agent for both *ASCS* and *ERS* instances after a reboot.

On both nodes, remove or comment out the `sapstartsrv` entries for both *ASCS* and *ERS* in the *SAP services* file.

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


#### Disabling systemd services of the ASCS and the ERS instances
{: #ha-rhel-ensa-disable-systemd-services}

On both nodes, disable the instance agent for the ASCS.

```sh
systemctl disable --now SAP${SID}_${ASCS_INSTNO}.service
```
{: pre}

On both nodes, disable the instance agent for the ERS.

```sh
systemctl disable --now SAP${SID}_${ERS_INSTNO}.service
```
{: pre}

#### Disabling `systemd` restart of a crashed ASCS or ERS instance
{: #ha-rhel-ensa-disable-crash-restart}

`Systemd` has its own mechanisms for restarting a crashed service.
In a high availability setup, only the HA cluster is responsible for managing the SAP ASCS and ERS instances.
Create `systemd drop-in files` on both cluster nodes to prevent `systemd` from restarting a crashed SAP instance.

On both nodes, create the directories for the drop-in files.

```sh
mkdir /etc/systemd/system/SAP${SID}_${ASCS_INSTNO}.service.d
```
{: pre}

```sh
mkdir /etc/systemd/system/SAP${SID}_${ERS_INSTNO}.service.d
```
{: pre}

On both nodes, create the drop-in files for ASCS and ERS.

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

When the SAP *ASCS* instance is installed on a {{site.data.keyword.powerSys_notm}} instance, the SAP license mechanism relies on the partition UUID.
For more information, see [SAP note 2879336 - Hardware key based on unique ID](https://me.sap.com/notes/2879336){: external}.

On both nodes, run the following command as user `<sid>adm` to identify the `HARDWARE KEY` of the node.

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

Note the `HARDWARE KEY` of each node.

You need both hardware keys to request two different SAP license keys.
Check the following SAP notes for more information about requesting SAP license keys:
- [2879336 - Hardware key based on unique ID](https://me.sap.com/notes/2879336){: external}
- [2662880 - How to request SAP license keys for failover systems](https://me.sap.com/notes/2662880){: external}

### Installing SAP resource agents
{: #ha-rhel-ensa-install-sap-resource-agents}

Install the required software packages.
The `resource-agents-sap` includes the *SAPInstance cluster resource agent* for managing the SAP instances.

Unless `sap_cluster_connector` is configured for the SAP instance, the RHEL HA Add-On cluster considers any state change of the instance as an issue.
If other SAP tools such as `sapcontrol` are used to manage the instance, then `sap_cluster_connector` grants permission to control SAP instances that are running inside the cluster.
If the SAP instances are managed by only cluster tools, the implementation of `sap_cluster_connector`
is not necessary.

Install the packages for the resource agent and the *SAP Cluster Connector* library.
For more information, see [How to enable the SAP HA Interface for SAP ABAP application server instances managed by the RHEL HA Add-On](https://access.redhat.com/solutions/3606101){: external}

On both nodes, run the following commands.

If needed, use `subscription-manager` to enable the SAP NetWeaver repository.
The [RHEL for SAP Subscriptions and Repositories](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_sap_solutions/8/html/rhel_for_sap_subscriptions_and_repositories/asmb_enable_repo_rhel-for-sap-subscriptions-and-repositories-8#con_hana_rhel-for-sap-subscriptions-and-repositories-8){: external} documentation describes how to enable the required repositories.

```sh
subscription-manager repos --enable="rhel-8-for-ppc64le-sap-netweaver-e4s-rpms"
```
{: pre}

Install the required packages.

```sh
dnf install -y resource-agents-sap  sap-cluster-connector
```
{: pre}

### Configuring SAP Cluster Connector
{: #ha-rhel-ensa-configure-sap-cluster-connector}

Add user `${sid}adm` to the `haclient` group.

On both nodes, run the following command.

```sh
usermod -a -G haclient ${sid}adm
```
{: pre}

### Adapting the SAP instance profiles
{: #ha-rhel-ensa-modify-sap-profiles}

Modify the start profiles of all SAP instances that are managed by *SAP tools* outside the cluster.
Both *ASCS* and *ERS* instances can be controlled by the RHEL HA Add-On cluster and its resource agents.
Adjust the SAP instance profiles to prevent an automatic restart of instance processes.

On NODE1, navigate to the SAP profile directory.

```sh
cd /sapmnt/${SID}/profile
```
{: pre}

Change all occurrences of `Restart_Program` to `Start_Program` in the instance profile of both *ASCS* and *ERS*.

```sh
sed -i -e 's/Restart_Program_\([0-9][0-9]\)/Start_Program_\1/' ${SID}_ASCS${ASCS_INSTNO}_${ASCS_VH}
```
{: pre}

```sh
sed -i -e 's/Restart_Program_\([0-9][0-9]\)/Start_Program_\1/' ${SID}_ERS${ERS_INSTNO}_${ERS_VH}
```
{: pre}

Add the following two lines at the end of the SAP instance profile to configure `sap_cluster_connector` for the *ASCS* and *ERS* instances.

```sh
service/halib = $(DIR_EXECUTABLE)/saphascriptco.so
service/halib_cluster_connector = /usr/bin/sap_cluster_connector
```
{: screen}
