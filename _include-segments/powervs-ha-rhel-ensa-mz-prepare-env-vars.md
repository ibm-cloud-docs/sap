

To simplify the setup process, define the following environment variables for the `root` user on both cluster nodes.
These variables are used in subsequent operating system commands.

On both nodes, set the following environment variables. Some of the variables are marked, and need to be set only either for resource agent `powervs-move-ip` or resource agent `powervs-subnet`.

```sh
# General settings
export CLUSTERNAME="SAP_S01"        # Cluster name
export NODE1=<HOSTNAME_1>           # Virtual server instance 1 hostname (in Workspace_1)
export NODE2=<HOSTNAME_2>           # Virtual server instance 2 hostname (in Workspace_2)

export SID=<SID>                    # SAP System ID (uppercase)
export sid=<sid>                    # SAP System ID (lowercase)

# ASCS instance
export ASCS_INSTNO=<INSTNO>         # ASCS instance number
export ASCS_VH=<virtual hostname>   # ASCS virtual hostname
export ASCS_IP=<IP address>         # ASCS virtual IP address
# resource agent powervs-move-ip only
export ASCS_ROUTE_CRN1=<Route_CRN1> # CRN of the static route in Workspace_1 with destination ASCS_IP (powervs-move-ip only)
export ASCS_ROUTE_CRN2=<Route_CRN2> # CRN of the static route in Workspace_2 with destination ASCS_IP (powervs-move-ip only)
# resource agent powervs-subnet only
export ASCS_NET=<Subnet name>       # Name for the ASCS subnet in IBM Cloud (powervs-subnet only)
export ASCS_CIDR=<CIDR of subnet>   # CIDR of the ASCS subnet containing the service IP address (powervs-subnet only)

# ERS instance
export ERS_INSTNO=<INSTNO>          # ERS instance number
export ERS_VH=<virtual hostname>    # ERS virtual hostname
export ERS_IP=<IP address>          # ERS virtual IP address
# resource agent powervs-move-ip only
export ERS_ROUTE_CRN1=<Route_CRN1>  # CRN of the static route in Workspace_1 with destination ERS_IP (powervs-move-ip only)
export ERS_ROUTE_CRN2=<Route_CRN2>  # CRN of the static route in Workspace_2 with destination ERS_IP (powervs-move-ip only)
# resource agent powervs-subnet only
export ERS_NET=<Subnet name>        # Name for the ERS subnet in IBM Cloud (powervs-subnet only)
export ERS_CIDR=<CIDR of subnet>    # CIDR of the ERS subnet containing the service IP address (powervs-subnet only)

# Other multizone region settings
export CLOUD_REGION=<CLOUD_REGION>       # Multizone region name
export APIKEY="APIKEY or path to file"   # API key of the ServiceID for the resource agent
export API_TYPE="private or public"      # Use private or public API endpoints
# resource agent powervs-move-ip only
export MON_API="false or true"           # Use cloud api in monitor command (powervs-move-ip only)
# resource agent powervs-subnet only
export IBMCLOUD_CRN_1=<IBMCLOUD_CRN_1>   # Workspace 1 CRN (powervs-subnet only)
export IBMCLOUD_CRN_2=<IBMCLOUD_CRN_2>   # Workspace 2 CRN (powervs-subnet only)
export POWERVSI_1=<POWERVSI_1>           # Virtual server 1 instance id (powervs-subnet only)
export POWERVSI_2=<POWERVSI_2>           # Virtual server 2 instance id (powervs-subnet only)
export JUMBO="true or false"             # Enable Jumbo frames (powervs-subnet only)

# NFS settings
export NFS_SERVER="NFS server"           # Hostname or IP address of the highly available NFS server
export NFS_SHARE="NFS server directory"  # Exported file system directory on the NFS server
export NFS_OPTIONS="rw,sec=sys"          # Sample NFS client mount options
```
{: codeblock}

The following export commands are an example of how to set the extra environment variables that are required for a multizone region implementation when using the resource agent `powervs-move-ip`.

```sh
# General settings
export CLUSTERNAME="SAP_S01"         # Cluster name
export NODE1="cl-s01-1"              # Virtual service instance 1 hostname
export NODE2="cl-s01-2"              # Virtual server instance 2 hostname

export SID="S01"                     # SAP System ID (uppercase)
export sid="s01"                     # SAP System ID (lowercase)

# ASCS instance
export ASCS_INSTNO="21"              # ASCS instance number
export ASCS_VH="s01ascs"             # ASCS virtual hostname
export ASCS_IP="10.40.21.102"        # ASCS virtual IP address
export ASCS_ROUTE_CRN1="crn:v1:bluemix:public:power-iaas:eu-de-2:a/a1b2c3d4e5f60123456789a1b2c3d4e5:a1b2c3d4-0123-4567-89ab-a1b2c3d4e5f6:route:a1b2c3d4-1234-5678-9abc-a1b2c3"
export ASCS_ROUTE_CRN2="crn:v1:bluemix:public:power-iaas:eu-de-1:a/a1b2c3d4e5f60123456789a1b2c3d4e5:e5f6a1b2-cdef-0123-4567-a1b2c3d4e5f6:route:1a2b3c4d-cba9-8765-4321-c3b2a1"

# ERS instance
export ERS_INSTNO="22"               # ERS instance number
export ERS_VH="s01ers"               # ERS virtual hostname
export ERS_IP="10.40.22.102"         # ERS virtual IP address
export ERS_ROUTE_CRN1="crn:v1:bluemix:public:power-iaas:eu-de-2:a/a1b2c3d4e5f60123456789a1b2c3d4e5:a1b2c3d4-0123-4567-89ab-a1b2c3d4e5f6:route:cba98765-5678-1234-9abc-a1b2c3"
export ERS_ROUTE_CRN2="crn:v1:bluemix:public:power-iaas:eu-de-1:a/a1b2c3d4e5f60123456789a1b2c3d4e5:e5f6a1b2-cdef-0123-4567-a1b2c3d4e5f6:route:9abca1b2-4321-8765-4321-b2a1c3"

# Other multizone region settings
export CLOUD_REGION="eu-de"
export APIKEY="@/root/.apikey.json"
export API_TYPE="private"
export MON_API="false"

# NFS settings
export NFS_SERVER="cl-nfs"           # Hostname or IP address of the highly available NFS server
export NFS_SHARE="/sapS01"           # Exported file system directory on the NFS server
export NFS_OPTIONS="rw,sec=sys"      # Sample NFS client mount options
```
{: screen}
