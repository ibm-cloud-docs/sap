
-  **Mounting when not using in-transit encryption**

   1. Install the NFS client packages on the instance.

      ```sh
      dnf install nfs-utils
      ```
      {: pre}

   1. Create a directory for the mount point.

      ```sh
      mkdir <mount_point>
      ```
      {: pre}

   1. Mount the remote file share.

      ```sh
      mount -t nfs4 -o <options> <host:/mount_target> <mount_point>
      ```
      {: pre}

      See the following example.

      ```sh
      mkdir /mnt/test
      mount -t nfs4 -o rw,sec=sys 10.20.30.40:/ea90ea14_0a1b_4f36_85c0_1cf83a2c8065 /mnt/test
      ```
      {: codeblock}
