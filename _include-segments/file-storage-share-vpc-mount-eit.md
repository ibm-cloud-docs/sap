
-  **Mounting when using in-transit encryption** [EIT]{: tag-green}

   The utility installs stunnel on the compute host. Stunnel needs a pem file. Because stunnel is used in client mode, you can use the pem that that comes with the distribution.

   1. Install the NFS client packages on the instance.

      ```sh
      dnf install nfs-utils
      ```
      {: pre}

   1. Download the Mount Helper package from GitHub.

      ```sh
      curl -LO https://github.com/IBM/vpc-file-storage-mount-helper/releases/download/latest/mount.ibmshare-latest.tar.gz
      ```
      {: pre}

   1. Extract the compressed file.

      ```sh
      tar -xvf mount.ibmshare-latest.tar.gz
      ```
      {: pre}

   1. Install the Mount Helper and all the dependencies.

      ```sh
      ./install.sh --stunnel
      ```
      {: pre}

   1. Create a directory for the mount point.

      ```sh
      mkdir <mount_point>
      ```
      {: pre}

   1. Mount the remote file share.

      ```sh
      mount -t ibmshare -o stunnel <host:/mount_target> <mount_point>
      ```
      {: pre}

      See the following example.

      ```sh
      mkdir /mnt/test
      mount -t ibmshare -o stunnel,rw,sec=sys 10.20.30.40:/ea90ea14_0a1b_4f36_85c0_1cf83a2c8065 /mnt/test
      ```
      {: codeblock}
