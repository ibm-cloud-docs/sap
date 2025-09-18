
To simplify the setup process, define the following environment variables for the `root` user on both cluster nodes.
These variables are used in subsequent operating system commands.

On both nodes, set the following environment variables.

```sh
# General settings
export CLUSTERNAME="SAP_S01"        # Cluster name
export NODE1=<HOSTNAME_1>           # Virtual server instance 1 hostname
export NODE2=<HOSTNAME_2>           # Virtual server instance 2 hostname

export SID=<SID>                    # SAP System ID (uppercase)
export sid=<sid>                    # SAP System ID (lowercase)

# ASCS instance
export ASCS_INSTNO=<INSTNO>         # ASCS instance number
export ASCS_NET=<Subnet name>       # Name for the ASCS subnet in IBM Cloud
export ASCS_CIDR=<CIDR of subnet>   # CIDR of the ASCS subnet containing the service IP address
export ASCS_VH=<virtual hostname>   # ASCS virtual hostname
export ASCS_IP=<IP address>         # ASCS virtual IP address

# ERS instance
export ERS_INSTNO=<INSTNO>          # ERS instance number
export ERS_NET=<Subnet name>        # Name for the ERS subnet in IBM Cloud
export ERS_CIDR=<CIDR of subnet>    # CIDR of the ERS subnet containing the service IP address
export ERS_VH=<virtual hostname>    # ERS virtual hostname
export ERS_IP=<IP address>          # ERS virtual IP address

# Other multizone region settings
export CLOUD_REGION=<CLOUD_REGION>       # Multizone region name
export APIKEY="APIKEY or path to file"   # API key of the ServiceID for the resource agent
export API_TYPE="private or public"      # Use private or public API endpoints
export IBMCLOUD_CRN_1=<IBMCLOUD_CRN_1>   # Workspace 1 CRN
export IBMCLOUD_CRN_2=<IBMCLOUD_CRN_2>   # Workspace 2 CRN
export POWERVSI_1=<POWERVSI_1>           # Virtual server 1 instance id
export POWERVSI_2=<POWERVSI_2>           # Virtual server 2 instance id
export JUMBO="true or false"             # Enable Jumbo frames

# NFS settings
export NFS_SERVER="NFS server"           # Hostname or IP address of the highly available NFS server
export NFS_SHARE="NFS server directory"  # Exported file system directory on the NFS server
export NFS_OPTIONS="rw,sec=sys"          # Sample NFS client mount options
```
{: codeblock}

The following export commands are an example of how to set the extra environment variables that are required for a multizone region implementation.

```sh
# General settings
export CLUSTERNAME="SAP_S01"         # Cluster name
export NODE1="cl-s01-1"              # Virtual service instance 1 hostname
export NODE2="cl-s01-2"              # Virtual server instance 2 hostname

export SID="S01"                     # SAP System ID (uppercase)
export sid="s01"                     # SAP System ID (lowercase)

# ASCS instance
export ASCS_INSTNO="21"              # ASCS instance number
export ASCS_NET="s01-ascs-net"       # Name for the ASCS subnet in IBM Cloud
export ASCS_CIDR="10.40.21.100/30"   # CIDR of the ASCS subnet containing the service IP address
export ASCS_VH="s01ascs"             # ASCS virtual hostname
export ASCS_IP="10.40.21.102"        # ASCS virtual IP address

# ERS instance
export ERS_INSTNO="22"               # ERS instance number
export ERS_NET="s01-ers-net"         # Name for the ERS subnet in IBM Cloud
export ERS_CIDR="10.40.22.100/30"    # CIDR of the ERS subnet containing the service IP address
export ERS_VH="s01ers"               # ERS virtual hostname
export ERS_IP="10.40.22.102"         # ERS virtual IP address

# Other multizone region settings
export CLOUD_REGION="eu-de"
export IBMCLOUD_CRN_1="crn:v1:bluemix:public:power-iaas:eu-de-2:a/a1b2c3d4e5f60123456789a1b2c3d4e5:a1b2c3d4-0123-4567-89ab-a1b2c3d4e5f6::"
export IBMCLOUD_CRN_2="crn:v1:bluemix:public:power-iaas:eu-de-1:a/a1b2c3d4e5f60123456789a1b2c3d4e5:e5f6a1b2-cdef-0123-4567-a1b2c3d4e5f6::"
export POWERVSI_1="a1b2c3d4-0123-890a-f012-0123456789ab"
export POWERVSI_2="e5f6a1b2-4567-bcde-3456-cdef01234567"
export APIKEY="@/root/.apikey.json"
export API_TYPE="private"
export JUMBO="true"

# NFS settings
export NFS_SERVER="cl-nfs"           # Hostname or IP address of the highly available NFS server
export NFS_SHARE="/sapS01"           # Exported file system directory on the NFS server
export NFS_OPTIONS="rw,sec=sys"      # Sample NFS client mount options
```
{: screen}

