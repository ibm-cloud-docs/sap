---
copyright:
  years: 2025
lastupdated: "2025-05-19"
keywords: SAP, {{site.data.keyword.cloud_notm}} SAP-Certified Infrastructure, {{site.data.keyword.ibm_cloud_sap}}, SAP Workloads, SAP HANA, Linux, RHEL, RedHat, Custom Image
subcollection: sap
---

{{site.data.keyword.attribute-definition-list}}


# Custom OS image build process on RHEL for SAP solutions on {{site.data.keyword.powerSysFull}}
{: #custom-os-image-build-rhel-overview}

The following information describes how to build a custom operating system (OS) image on Red Hat Enterprise Linux (RHEL) for SAP software on {{site.data.keyword.powerSysFull}}.
{: shortdesc}

## Custom OS image build requirements
{: #custom-os-image-build-rhel-requirements}

Complete the following requirements before you start the image build process:

- Create an [{{site.data.keyword.cloud_notm}} account](https://cloud.ibm.com) and obtain the necessary credentials to deploy an image build server on {{site.data.keyword.powerSys_notm}}. Details about identity and access management (IAM) are described in [Managing identity and access management (IAM) for {{site.data.keyword.powerSysFull}}](/docs/power-iaas?topic=power-iaas-managing-resources-and-users).

- Ensure that you have access to {{site.data.keyword.cos_full_notm}} so that you can upload a base image.
 See [Getting started with IBM Cloud Object Storage](/docs/cloud-object-storage?topic=cloud-object-storage-getting-started-cloud-object-storage) for information on how to get started.

- Download the Red Hat Enterprise Linux 9 KVM Guest Image `rhel-9.4-ppc64le-kvm.qcow2` from [Red Hat Enterprise Linux for Power little endian](https://access.redhat.com/downloads/content/279/ver=/rhel---9/9.4/ppc64le/product-software){: external}.

   The following guidance for building a custom OS image is based on RHEL 9.4 OS image.
   {: note}

- Create an image build server, where you configure an image build environment and build an OS image. It consists of an {{site.data.keyword.powerSys_notm}} instance running RHEL 9.2 (RHEL9-SP2-SAP-NETWEAVER) or higher RHEL version, with the additional storage volume of 200 GByte. There are two methods how to set up the image build server:

   - For a manual approach, refer to the detailed information in [Getting started with {{site.data.keyword.powerSys_notm}}](/docs/power-iaas?topic=power-iaas-getting-started) and [Creating an IBM Power Virtual Server](/docs/power-iaas?topic=power-iaas-creating-power-virtual-server#creating-service).

   - For an automated approach, refer to the [Power Virtual Server with VPC Landing Zone](/docs/powervs-vpc)deployment guide.

## Setup a build image environment
{: #custom-os-image-build-server-env}

The following steps describe how to set up a build server environment for image creation.

1. Log in to your build server and check its registration status by executing the following command.

   ```sh
   subscription-manager status
   ```
   {: pre}

1. Update RHEL OS packages.

1. Check and adjust proxy settings for a build environment. Configure temporary HTTPS proxy settings by using your Proxy IP address or URL. If you deployed the automated {{site.data.keyword.powerSys_notm}} with VPC landing zone from {{site.data.keyword.cloud_notm}} catalog, use this proxy address `http://10.30.40.7:3128/` for your configuration.

    ```sh
    export https_proxy="http://10.30.40.7:3128/"  || die "Set HTTPS proxy failed (rc $?)"
    ```
    {: pre}

1. Create a '/data' directory and export an environment variable that points to it.

    ```sh
    mkdir -p /data
    ```
    {: pre}

    ```sh
    export MOUNT_PATH=/data
    ```
    {: pre}

1. Attach an additional storage volume with a capacity of 200 GB and create the `/data` file system.

   - Run the multipath command to identify the available storage volumes and their World Wide Names (WWNs). Ensure that the command output only shows one 200 GB storage volume. Otherwise, creating the file system will not work properly..

      ```sh
      multipath -ll
      ```
      {: pre}

   - Export the following variables for physical and logical volumes, a volume group, and a mount directory.

      ```sh
      export PV_SIZE=200G
      ```
      {: pre}

      ```sh
      export LV_NAME=image_lv
      ```
      {: pre}

      ```sh
      export VG_NAME=image_vg
      ```
      {: pre}

   - Create the physical and logical volumes.

      ```sh
      devices=$(multipath -ll | grep -B 1 $PV_SIZE | grep dm- | awk '{print "/dev/"$2}' | tr '\n' ' ')
      ```
      {: pre}

      ```sh
      stripes=$(multipath -ll | grep -B 1 $PV_SIZE | grep dm- | awk '{print "/dev/"$2}' | wc | awk '{print $1}')
      ```
      {: pre}

      ```sh
      pvcreate $devices
      ```
      {: pre}

      ```sh
      vgcreate ${VG_NAME} ${devices}
      ```
      {: pre}

      ```sh
      lvcreate -i${stripes} -I64 -l100%VG -n ${LV_NAME} ${VG_NAME}
      ```
      {: pre}

      ```sh
      mkfs.xfs /dev/mapper/${VG_NAME}-${LV_NAME}
      ```
      {: pre}

   - Check if the created logical volume is active.

      ```sh
      lvscan
      ```
      {: pre}

   - Mount the file system.

      ```sh
      mount -t xfs -o defaults,nofail --source /dev/mapper/${VG_NAME}-${LV_NAME} --target ${MOUNT_PATH}
      ```
      {: pre}

   - Add the file system to the `/etc/fstab` file.

      ```sh
      echo "/dev/mapper/${VG_NAME}-${LV_NAME}  ${MOUNT_PATH} xfs defaults,nofail 0 0" >> /etc/fstab
      ```
      {: pre}

   - Ensure that the file system is present in the list of all mounted file systems.

      ```sh
      df -h
      ```
      {: pre}

1. Create the directory `/data/image` where the base image is saved.

1. Copy the image `rhel-9.4-ppc64le-kvm.qcow2` to `/data/image`.

1. Create the `/data/tmp` directory, in which a temporary image is saved during the build process.

1. Complete the following tasks to configure the build server. Use the `dnf` RHEL package manager.

   - Install the `xfsprogs` and `lvm2` system packages.
   - Install the  `make`, `git`, `libgcrypt-devel` and `go-toolset` development packages.
   - Install the `qemu-img`  and `cloud-utils-growpart` packages.
   - Configure a proxy by adding your proxy host in the file `~root/.bashrc`.

       ```sh
       export https_proxy='<your_https_proxy>'
       ```
       {: codeblock}

1. Configure the NBD module to load at boot time by adding the following lines to the file `/etc/modules-load.d/ndb.conf`.

    ```sh
    nbd
    options nbd max_part=8
    ```
    {: codeblock}

1. Download the `pvsadm` tool from the [latest release of `pvsadm-linux-ppc64le`](https://api.github.com/repos/ppc64le-cloud/pvsadm/releases/latest){: external} GitHub repository. Save it in the file named `/usr/local/bin/pvsadm` and change the mode to `0755`. For more details about the tool, refer to [`pvsadm` tool](https://github.com/ppc64le-cloud/pvsadm/tree/main){: external}.

## Building a custom OS image
{: #custom-os-image-build-process}

The build process of a custom OS image for IBM Cloud PowerVS consists of the following tasks.

1. Check that the Red Hat Satellite subscription credentials are valid.

1. Create an empty file named `image-prep.template` and use it as the image preparation template .

1. The following tasks contain the shell commands that are needed for building a customized image. Add these commands to the template `image-prep.template`. The commands update the base OS image during image creation with the `pvsadm` tool to create the final SAP-ready OS image. The command order is important and should not be changed.

   - Copy the following lines, including error handling, to be used as the template header.

      ```sh
      #!/usr/bin/env bash
      set -o errexit
      set -o nounset
      set -o pipefail

      die() {
        echo -e "\n${1}"
        set +o errexit
        set +o nounset
        exit 1
      }
      ```
      {: codeblock}

   - Add a temporary name server.

      ```sh
      echo "nameserver 9.9.9.9" >> /etc/resolv.conf \
          || die "Add nameserver failed (rc $?)"
      ```
      {: pre}

   - Create a work directory `/tmp/work` and export it as an environmental variable.

      ```sh
      mkdir -p /tmp/work || die "Create work directory failed (rc $?)"
      ```
      {: pre}

      ```sh
      export WORK_DIR=/tmp/work
      ```
      {: pre}

   - Create the 25GB swap file, add permissions and check that it has been created correctly.

      ```sh
      fallocate -l 25G /swapfile || die "allocate swapfile failed (rc $?)"
      ```
      {: pre}

      ```sh
      chmod 600 /swapfile || die "chmod swapfile failed (rc $?)"
      ```
      {: pre}

      ```sh
      mkswap /swapfile || die "mkswap swapfile failed (rc $?)"
      ```
      {: pre}

      ```sh
      swapon /swapfile || die "swapon swapfile failed (rc $?)"
      ```
      {: pre}

      ```sh
      swapon --show
      ```
      {: pre}

      ```sh
      echo "/swapfile swap swap defaults 0 0" >> /etc/fstab || die "update fstab with swapfile failed (rc $?)"
      ```
      {: pre}

      ```sh
      echo "swapon -s"
      ```
      {: pre}

   - Subscribe to Red Hat Satellite and enable the SAP repositories. Use your own values for the subscription user name, password and release version.

      ```sh
      subscription-manager register \
          --force --auto-attach \
          --username=<SUBSCRIPTION_USER>  \
          --password=<SUBSCRIPTION_PASSWORD> \
          --release=<OS_VERSION> \
         || die "Register subscription failed (rc $?)"
      ```
      {: pre}

      ```sh
      subscription-manager repos \
          --disable="*" \
          --enable="rhel-9-for-$(uname -m)-baseos-e4s-rpms" \
          --enable="rhel-9-for-$(uname -m)-appstream-e4s-rpms" \
          --enable="rhel-9-for-$(uname -m)-sap-solutions-e4s-rpms" \
          --enable="rhel-9-for-$(uname -m)-sap-netweaver-e4s-rpms" \
          --enable="rhel-9-for-$(uname -m)-highavailability-e4s-rpms"\
          --enable="codeready-builder-for-rhel-9-$(uname -m)-rpms" \
          || die "Repository configuration failed (rc $?)"
      ```
      {: pre}

   - Install a package group server.

      ```sh
      dnf -y group install server \
          || die "Installing server group packages failed (rc $?)"
      ```
      {: pre}

   - Install the system packages.

      ```sh
      dnf -y install \
          cloud-init \
          device-mapper-multipath \
          libxcrypt-compat \
          glibc-langpack-en \
          || die "Install system packages failed (rc $?)"
      ```
      {: pre}

   - Update `grub2`.

      ```sh
      dnf -y reinstall grub2-common \
          || die "Reinstall system package failed (rc $?)"
      ```
      {: pre}

      ```sh
      dnf -y update grub2 \
          || die "Update grub2 package failed (rc $?)"
      ```
      {: pre}

   - Install Ansible and the Red Hat system roles for SAP.

      ```sh
      dnf -y install ansible-core rhel-system-roles rhel-system-roles-sap \
          || die "Install Ansible and RH System Roles failed (rc $?)"
      ```
      {: pre}

   - Enable the EPEL repository.

      ```sh
      dnf -y install "https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm" \
          || die "Installation EPEL configuration failed (rc $?)"
      ```
      {: pre}

   - Configure the IBM Power [tools repository](https://public.dhe.ibm.com/software/server/POWER/Linux/yum/download/ibm-power-repo-latest.noarch.rpm){: external}.

      ```sh
      dnf -y install "https://public.dhe.ibm.com/software/server/POWER/Linux/yum/download/ibm-power-repo-latest.noarch.rpm" \
          || die "Installation IBM Power Tools configuration failed (rc $?)"
       ```
      {: pre}

      ```sh
      echo 'y' | (/opt/ibm/lop/configure \
          || die "Power Tools configuration failed (rc $?)")
      ```
      {: pre}

   - Install the IBM Power tools and disable the repository.

      ```sh
      dnf config-manager --set-disabled Advance_Toolchain \
          || die "Disable Advance Toolchain repository failed (rc $?)"
      ```
      {: pre}

       ```sh
       dnf -y install ibm-power-managed-rhel9 \
          || die "Install IBM Power Tools failed (rc $?)"
       ```
      {: pre}

      ```sh
      dnf config-manager --set-disabled IBM_Power_Tools \
          || die "Disable IBM Power Tools repository failed (rc $?)"
      ```
      {: pre}

   - Updating OS.

      ```sh
      dnf -y update || die "OS update failed (rc $?)"
      ```
      {: pre}

   - Remove the EPEL repository.

      ```sh
      dnf -y remove epel-release \
          || die "Uninstall EPEL configuration failed (rc $?)"
      ```
      {: pre}

   - Unregister from the Red Hat Satellite server.

      ```sh
      subscription-manager unregister \
          || die "Unregister subscription failed (rc $?)"
      ```
      {: pre}

      ```sh
      subscription-manager clean \
          || die "Clean subscription failed (rc $?)"
      ```
      {: pre}

   - Change the cryptographic policy (disable SHA1/CBC).

       ```sh
      echo -e "cipher@ssh = -*-CBC\n" \
          > /etc/crypto-policies/policies/modules/NO-CBC.pmod \
          || die "Create of CBC disabling file failed (rc $?)"
       ```
       {: pre}

      ```sh
      update-crypto-policies --set DEFAULT:NO-SHA1:NO-CBC \
          || die "Update of cryptografic policy failed (rc $?)"
      ```
      {: pre}

   - Create a basic multipath configuration.

      ```sh
      echo -e "defaults {\n \
          user_friendly_names no\n \
          find_multipaths smart\n}\n" \
          > /etc/multipath.conf \
          || die "Create multipath configuration failed (rc $?)"
       ```
       {: pre}

   - Define an Ansible inventory.

      ```sh
      echo -e "localhost ansible_connection=local\n" > /root/inventory \
          || die "Create Ansible inventory file failed (rc $?)"
      ```
      {: pre}

   - Create Ansible yml-files for SAP HANA and NetWeaver.

      ```sh
      cat <<EOF > /root/sap-hana.yml \
        || die "Create SAP HANA Ansible file failed (rc $?)"
      - hosts: localhost
        vars:
         sap_hana_preconfigure_min_rhel_release_check: false
         sap_hana_preconfigure_install_ibm_power_tools: false
         sap_hana_preconfigure_add_ibm_power_repo: false
        connection: local
        roles:
          - redhat.sap_install.sap_general_preconfigure
          - redhat.sap_install.sap_hana_preconfigure
      EOF
      ```
      {: codeblock}

       ```sh
       cat <<EOF > /root/sap-netweaver.yml \
         || die "Create SAP NetWeaver Ansible file failed (rc $?)"
       - hosts: localhost
         connection: local
         roles:
           - redhat.sap_install.sap_general_preconfigure
           - redhat.sap_install.sap_netweaver_preconfigure
       EOF
       ```
      {: codeblock}

      ```sh
      cat <<EOF > /root/sap-preconfigure.yml \
        || die "Create SAP preconfiguration Ansible file failed (rc $?)"
      - hosts: localhost
        connection: local
        roles:
          - redhat.sap_install.sap_general_preconfigure
      EOF
      ```
      {: codeblock}

   - Change the default kernel parameter in the file `/etc/sysctl.conf`.

      ```sh
      cat <<EOF >> /etc/sysctl.conf \
       || die "Change of kernel parameter failed (rc $?)"
      net.core.rmem_max = 56623104
      net.core.wmem_max = 56623104
      net.ipv4.tcp_rmem = 65536 262088 56623104
      net.ipv4.tcp_wmem = 65536 262088 56623104
      net.ipv4.tcp_mem = 56623104 56623104 56623104
      EOF
      ```
      {: codeblock}

   - Enable Receive Flow Steering (RFS) on ibmveth devices, adjust the file `/etc/udev/rules.d/70-ibmveth-rfs.rules`.

      ```sh
      cat <<EOF >> /etc/udev/rules.d/70-ibmveth-rfs.rules \
       || die "Create udev rule for RFS failed (rc $?)"
      # Enable Receive Flow Steering (RFS) on ibmveth devices
      SUBSYSTEM=="net",ACTION=="add",DRIVERS=="ibmveth",RUN{program}+="/bin/bash -c 'echo 32768 > /sys/\$DEVPATH/queues/rx-0/rps_flow_cnt';"
      EOF
      ```
      {: codeblock}

      Enable the Receive Flow Steering (RFS) in the file `/etc/sysctl.d/95-enable-rfs.conf`.

      ```sh
      cat <<EOF >> /etc/sysctl.d/95-enable-rfs.conf \
       || die "Create kernel parameter file for RFS failed (rc $?)"
      # Enable Receive Flow Steering (RFS)
      net.core.rps_sock_flow_entries=32768
      EOF
      ```
      {: codeblock}

   - Configure the SSH daemon.

      ```sh
      sed -i \
          -e 's/^\(#\)\?PermitRootLogin .*/PermitRootLogin yes/g' \
          -e 's/^\(#\)\?PasswordAuthentication .*/PasswordAuthentication no/g' \
          -e 's/^\(#\)\?MaxStartups .*/MaxStartups 10:30:60/g' \
          /etc/ssh/sshd_config \
          || die "Failed SSH daemon configuration (rc $?)"
      ```
      {: pre}

   - Adapt GRUB.

      - Check that the PReP partition exists and update it.

        ```sh
        prep_partition=$(fdisk -l | grep -i ppc | grep -i loop | awk '{print $1}')

        # Check if a PReP partition was found
        if [ -z "$prep_partition" ]; then
          echo "No PReP partition found with /dev/loop."
          exit 1
        else
          echo "PReP partition found: $prep_partition"
        fi
        ```
        {: codeblock}

      - Install `grub2`on the PReP partition.

        ```sh
        grub2-install "$prep_partition" \
            || die "Install GRUB update failed (rc $?)"
        ```
        {: pre}

      - Execute the bootlist command.

        ```sh
        bootlist -m normal -o || die "Setting boot list failed (rc $?)"
        ```
        {: pre}

      - Change the GRUB default timeout option.

        ```sh
        sed -i \
            -e 's/GRUB_TIMEOUT=.*/GRUB_TIMEOUT=20/' \
            /etc/default/grub || die "Fail to change default GRUB options (rc $?)"
        ```
        {: pre}

      - Update the GRUB configuration.

        ```sh
        grubby --update-kernel=ALL \
            --remove-args="net.ifnames=0" \
            --args="console=tty0 console=hvc0,115200n8 crashkernel=2G-4G:384M,4G-16G:512M,16G-64G:1G,64G-128G:2G,128G-:4G rd.shell rd.debug rd.driver.pre=dm_multipath log_buf_len=1M elevator=none" \
            || die "GRUB cmdline configuration update failed (rc $?)"
        ```
        {: pre}

      - Enable multipath for all kernels.

        ```sh
        echo -e 'force_drivers+=" dm-multipath "\n' >/etc/dracut.conf.d/10-mp.conf\
            || die "Create of multipath include file for dracut failed (rc $?)"
        ```
        {: pre}

        ```sh
        dracut --regenerate-all --force \
         || die "Regenerate of initramfs images failed (rc $?)"
        for kernel in $(rpm -q kernel | sort -V | sed 's/kernel-//'); do
          dracut --kver ${kernel} --force --add multipath --include /etc/multipath\
          /etc/multipath --include /etc/multipath.conf /etc/multipath.conf \
             || die "Generate initramfs of ${kernel} failed (rc $?)"
        done
        ```
        {: codeblock}

      - Generate the GRUB configuration.

        ```sh
        grub2-mkconfig -o /boot/grub2/grub.cfg \
            || die "Generate GRUB configuration failed (rc $?)"
        ```
        {: pre}

   - Remove the temporary name server.

     ```sh
     sed -i '/nameserver 9.9.9.9/d' /etc/resolv.conf \
         || die "Remove nameserver failed (rc $?)"
     ```
     {: pre}

   - Enable SELinux relabeling on the next boot.

     ```sh
     touch /.autorelabel || die "Create relabel file failed (rc $?)"
     ```
     {: pre}

   - Delete the root user password.

     ```sh
     usermod -p '!' root || die "Delete root password failed (rc $?)"
     ```
     {: pre}

   - Clean up the file system.

     ```sh
     rm -rf /etc/sysconfig/network-scripts/ifcfg-eth*
     ```
     {: pre}

     ```sh
     rm -rf  /tmp/work
     ```
     {: pre}

     ```sh
     rm -rf /root/.ssh
     ```
     {: pre}

     ```sh
     rm -rf /etc/pki/entitlement/
     ```
     {: pre}

     ```sh
     rm -rf /setup.sh
     ```
     {: pre}

   - Deleting command history.

     ```sh
     history -c
     ```
     {: pre}

1. Before running the `pvsadm` tool, make sure that local swapping on a build server is disabled. Otherwise, the error `mkswap: error: /swapfile is mounted; will not make swapspace` occurs while executing `pvsadm`. To disable swapping, run the following command.

   ```sh
   swapoff -a
   ```
   {: pre}

1. Use the `pvsadm` image build tool with the `qcow2ova` command to convert the qcow2 image to the OVA format. Set your own customized values for the command flags according to their input formats. See the description of the flags below.

   ```sh
   --image-name <string>     Name of the resultant OVA image
   --image-url  <string>     URL or absolute local file path to the qcow2 image
   --image-dist <string>     Image Distribution(supported: rhel, centos, coreos)
   --target-disk-size <int>  Size (in GB) of the target disk volume where OVA will be copied (default 120)
   --rhn-user <string>       RedHat Subscription username. Required when Image distribution is rhel
   --rhn-password <string>   RedHat Subscription password. Required when Image distribution is rhel
   --os-password  <string>   Root user password, will auto-generate the 12 bits password(applicable only for redhat and cento distro)
   --temp-dir <string>       Scratch space to use for OVA generation
   --prep-template <string>  Image preparation script template
   ```
   {: screen}

   For instance, use the `rhel-9.4-ppc64le-image` as a new OS image name, `rhel-9.4-ppc64le-kvm.qcow2` is the base OS image located in `/data/image`, so the absolute local path for the qcow2 image is `/data/image/rhel-9.4-ppc64le-kvm.qcow2`, and the image distribution is `rhel`. Adjust the image and target disk sizes according to your requirements, 120 GB is set as the default value for the target disk volume. An image preparation script template is `image-prep.template`, and the name of the script is customizable as well. To simplify running the `pvsadm` image build tool, you can set up environment variables as well.

   ```sh
   export IMAGE_NAME=rhel-9.4-ppc64le-image
   export QCOW2_IMAGE_PATH=/data/image/rhel-9.4-ppc64le-kvm.qcow2
   export IMAGE_SIZE=100
   export TARGET_DISK_SIZE=100
   export SUBSCRIPTION_USER=<RedHat subscription username>
   export SUBSCRIPTION_PASSWORD=<RedHat Subscription password>
   export IMAGE_BUILD_DIRECTORY=/data/tmp
   export IMAGE_PREP_SCRIPT=image-prep.template
   ```
   {: codeblock}

   ```sh
   pvsadm image qcow2ova --image-name ${IMAGE_NAME} \
       --image-url ${QCOW2_IMAGE_PATH} \
       --image-dist rhel \
       --image-size ${IMAGE_SIZE} --target-disk-size ${TARGET_DISK_SIZE} \
       --rhn-user ${SUBSCRIPTION_USER} \
       --rhn-password ${SUBSCRIPTION_PASSWORD}  \
       --temp-dir ${IMAGE_BUILD_DIRECTORY} \
       --prep-template ${IMAGE_PREP_SCRIPT}
   ```
   {: pre}

   The `pvsadm` tool has a help menu with detailed descriptions of usage, commands and flags. Run the following command to access the help menu.

   ```sh
   pvsadm --help
   ```
   {: pre}

1. Upload the custom-created OS image to Cloud Object Storage (COS).

## Importing OS image in the {{site.data.keyword.powerSys_notm}} workspace
{: #custom-os-image-import-to-powervs-workspace}

To import your custom boot image, see the guidance for [Importing a boot image](/docs/power-iaas?topic=power-iaas-importing-boot-image).

## Creating an {{site.data.keyword.powerSys_notm}} instance from the new imported OS image
{: #os-image-creating-powervs-instance-from-custom}

To provision a new {{site.data.keyword.powerSys_notm}} instance follow the steps that are described in [Using a custom boot image to provision a new instance](/docs/power-iaas?topic=power-iaas-deploy-custom-image#upload-custom-boot).
