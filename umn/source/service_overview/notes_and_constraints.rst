:original_name: cce_productdesc_0005.html

.. _cce_productdesc_0005:

Notes and Constraints
=====================

This section describes the notes and constraints on using CCE.

Clusters and Nodes
------------------

-  After a cluster is created, the following items cannot be changed:

   -  Number of master nodes: For example, a non-HA cluster (with one master node) cannot be changed to an HA cluster (with three master nodes).
   -  AZ where a master node is deployed
   -  Network configuration of the cluster, such as the VPC, subnet, container CIDR block, Service CIDR block, IPv6 settings, and kube-proxy (forwarding) settings
   -  Network model: For example, a container tunnel network cannot be changed to a VPC network.

-  CCE underlying resources such as ECS nodes are limited by quota and their inventory. It is possible that only some nodes are created during cluster creation, cluster scaling, or auto scaling.
-  ECS node specifications: CPU >= 2 cores, memory >= 4 GiB
-  To access a CCE cluster through a VPN, ensure that the VPN CIDR block does not conflict with the VPC CIDR block where the cluster resides and the container CIDR block.
-  Ubuntu 22.04 does not support the tunnel network model.

Networks
--------

-  By default, a NodePort Service is accessed within a VPC. To access a NodePort Service through the Internet, bind an EIP to the node in the cluster beforehand.
-  Constraints on load balancers associated with Services:

   -  The load balancer automatically created based on the Service configuration cannot be associated with other resources. If you associate the load balancer with other resources, the load balancer cannot be automatically deleted when the Service is deleted, resulting in residual resources.
   -  The names of load balancer listeners for clusters of v1.15 or earlier cannot be changed. If you change the names, access to the load balancer may be abnormal.

-  The table below shows the relationships between network policies and cluster types.

   +--------------------------------------------------------------+----------------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
   | Cluster Type                                                 | CCE Standard Cluster | CCE Standard Cluster                                                                        | CCE Turbo Cluster                                                                           |
   +==============================================================+======================+=============================================================================================+=============================================================================================+
   | Network Model                                                | Tunnel               | VPC                                                                                         | Cloud Native Network 2.0                                                                    |
   +--------------------------------------------------------------+----------------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
   | NetworkPolicy                                                | Enabled by default   | Disabled by default (To use network policies, enable DataPlane V2 when creating a cluster.) | Disabled by default (To use network policies, enable DataPlane V2 when creating a cluster.) |
   +--------------------------------------------------------------+----------------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
   | Data plane implementation                                    | OpenvSwitch          | eBPF                                                                                        | eBPF                                                                                        |
   +--------------------------------------------------------------+----------------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
   | Cluster version for ingress rules                            | All versions         | Cluster version: v1.27.16-r30, v1.28.15-r20, v1.29.13-r0, v1.30.10-r0, v1.31.6-r0, or later | Cluster version: v1.27.16-r10, v1.28.15-r0, v1.29.10-r0, v1.30.6-r0, or later               |
   +--------------------------------------------------------------+----------------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
   | Cluster version for egress rules                             | v1.23 and later      |                                                                                             |                                                                                             |
   +--------------------------------------------------------------+----------------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
   | Selector for ingress rules                                   | namespaceSelector    | namespaceSelector                                                                           | namespaceSelector                                                                           |
   |                                                              |                      |                                                                                             |                                                                                             |
   |                                                              | podSelector          | podSelector                                                                                 | podSelector                                                                                 |
   |                                                              |                      |                                                                                             |                                                                                             |
   |                                                              |                      | IPBlock                                                                                     | IPBlock                                                                                     |
   +--------------------------------------------------------------+----------------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
   | Selector for egress rules                                    | namespaceSelector    |                                                                                             |                                                                                             |
   |                                                              |                      |                                                                                             |                                                                                             |
   |                                                              | podSelector          |                                                                                             |                                                                                             |
   |                                                              |                      |                                                                                             |                                                                                             |
   |                                                              | IPBlock              |                                                                                             |                                                                                             |
   +--------------------------------------------------------------+----------------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
   | Supported OS                                                 | EulerOS              | HCE OS 2.0                                                                                  | HCE OS 2.0                                                                                  |
   |                                                              |                      |                                                                                             |                                                                                             |
   |                                                              | HCE OS 2.0           |                                                                                             |                                                                                             |
   +--------------------------------------------------------------+----------------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
   | IPv6 network policies                                        | Not supported        | Not supported                                                                               | Supported                                                                                   |
   +--------------------------------------------------------------+----------------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
   | Secure containers                                            | Not supported        | Not supported                                                                               | Not supported                                                                               |
   +--------------------------------------------------------------+----------------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
   | IPBlock scope                                                | Not limited          | Subnets within the pod CIDR block, Service CIDR block, and node IP addresses                | Subnets within the pod CIDR block, Service CIDR block, and node IP addresses                |
   +--------------------------------------------------------------+----------------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
   | Limit ClusterIP access through workload labels               | Not supported        | Supported                                                                                   | Supported                                                                                   |
   +--------------------------------------------------------------+----------------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
   | Limit the internal cloud server CIDR block of 100.125.0.0/16 | Supported            | Supported                                                                                   | Not supported                                                                               |
   +--------------------------------------------------------------+----------------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
   | SCTP                                                         | Not supported        | Supported                                                                                   | Not supported                                                                               |
   +--------------------------------------------------------------+----------------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
   | Always allow access to pods on a node from other nodes       | Supported            | Supported                                                                                   | Supported                                                                                   |
   +--------------------------------------------------------------+----------------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
   | Configure EndPort in network policies                        | Not supported        | Supported                                                                                   | Not supported                                                                               |
   +--------------------------------------------------------------+----------------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+

   .. note::

      -  CCE DataPlane V2 is released with restrictions. To use this feature, submit a service ticket to CCE.
      -  Secure containers (such as Kata as the container runtime) are not supported by network policies.
      -  If you upgrade a CCE standard cluster with a tunnel network to a version that supports egress rules in in-place mode, the rules will not work because the node OS is not upgraded. In this case, reset the node.
      -  When a network policy is enabled for a cluster with a tunnel network, a pod's source IP address is embedded in the optional field of packets it sends to any Service CIDR block. This enables the configuration of network policy rules on the destination pod, taking into account the source IP address of the pod.

Storage Volumes
---------------

-  Constraints on EVS volumes:

   -  EVS disks cannot be attached across AZs and cannot be used by multiple workloads, multiple pods of the same workload, or multiple tasks. Data sharing of a shared disk is not supported between nodes in a CCE cluster. If an EVS disk is attached to multiple nodes, I/O conflicts and data cache conflicts may occur. Therefore, select only one pod when creating a Deployment that uses EVS disks.

   -  For clusters earlier than v1.19.10, if an HPA policy is used to scale out a workload with EVS volumes mounted, the existing pods cannot be read or written when a new pod is scheduled to another node.

      For clusters of v1.19.10 and later, if an HPA policy is used to scale out a workload with EVS volumes mounted, a new pod cannot be started because EVS disks cannot be attached.

-  Constraints on SFS volumes:

   -  Multiple PVs can use the same SFS or SFS Turbo file system with the following restrictions:

      -  If a pod mounts an SFS or SFS Turbo volume used by multiple PVCs/PVs, and the PVs have identical **volumeHandle** values, the pod may fail to start. To avoid this issue, do not mount the same SFS or SFS Turbo file system to the same pod.
      -  The **persistentVolumeReclaimPolicy** parameter in the PVs must be set to **Retain**. Otherwise, when a PV is deleted, the associated underlying volume may be deleted. In this case, other PVs associated with the underlying volume malfunction.
      -  When the underlying volume is repeatedly used, enable isolation and protection for ReadWriteMany at the application layer to prevent data overwriting and loss.

   -  SFS volumes are available only in certain regions.

-  Constraints on OBS volumes:

   -  If OBS volumes are used, the owner group and permission of the mount point cannot be modified.
   -  Every time an OBS volume is mounted to a workload through a PVC, a resident process is created in the backend. When a workload uses too many OBS volumes or reads and writes a large number of object storage files, resident processes will consume a significant amount of memory. To ensure stable running of the workload, make sure that the number of OBS volumes used does not exceed the requested memory. For example, if the workload requests for 4 GiB of memory, the number of OBS volumes should be **no more than** 4.
   -  Secure containers do not support OBS volumes.
   -  Hard links are not supported when common buckets are mounted.
   -  Read-only is not supported when an OBS PVC is mounted to a workload.

-  Constraints on local PVs:

   -  Local PVs are supported only when the cluster version is v1.21.2-r0 or later and the Everest add-on version is 2.1.23 or later. Version 2.1.23 or later is recommended.
   -  Removing, deleting, resetting, or scaling in a node will cause the PVC/PV data of the local PV associated with the node to be lost, which cannot be restored or used again. In these scenarios, the pod that uses the local PV is evicted from the node. A new pod will be created and stays in the pending state. This is because the PVC used by the pod has a node label, due to which the pod cannot be scheduled. After the node is reset, the pod may be scheduled to the reset node. In this case, the pod remains in the creating state because the underlying logical volume corresponding to the PVC does not exist.
   -  Do not manually delete the corresponding storage pool or detach data disks from the node. Otherwise, exceptions such as data loss may occur.
   -  A local PV cannot be mounted to multiple workloads or jobs at the same time.

-  Constraints on local EVs:

   -  Local EVs are supported only when the cluster version is v1.21.2-r0 or later and the Everest add-on version is 1.2.29 or later.
   -  Do not manually delete the corresponding storage pool or detach data disks from the node. Otherwise, exceptions such as data loss may occur.
   -  Ensure that the **/var/lib/kubelet/pods/** directory is not mounted to the pod on the node. Otherwise, the pod, mounted with such volumes, may fail to be deleted.

-  Constraints on snapshots and backups:

   -  The snapshot function is available **only for clusters of v1.15 or later** and requires the CSI-based Everest add-on.
   -  The subtype (common I/O, high I/O, or ultra-high I/O), disk mode (VBD or SCSI), data encryption, sharing status, and capacity of an EVS disk created from a snapshot must be the same as those of the disk associated with the snapshot. These attributes cannot be modified after being checked or configured.
   -  Snapshots can be created only for EVS disks that are available or in use, and a maximum of seven snapshots can be created for a single EVS disk.
   -  Snapshots can be created only for PVCs created using the storage class (whose name starts with csi) provided by the Everest add-on. Snapshots cannot be created for PVCs created using the FlexVolume storage class whose name is ssd, sas, or sata.
   -  Snapshot data of encrypted disks is stored encrypted, and that of non-encrypted disks is stored non-encrypted.
   -  A PVC of the xfs file system type can generate snapshots. The file system of the disk associated with the PVC created using these snapshots remains xfs.

-  Constraints on LVM:

   The default backup configuration that is stored in the **/etc/lvm/lvm.conf** path for the node LVM has been changed. Once the CCE Container Storage (Everest) add-on (version >= 2.4.98) is installed, archive logs will only be kept for one day to avoid filling up disk space with historical metadata from numerous LVM operations.

Add-ons
-------

CCE uses Helm charts to deploy add-ons. To modify or upgrade an add-on, perform operations on the **Add-ons** page or use open add-on management APIs. Avoid making changes to add-on resources in the backend, as this may lead to abnormal add-on behavior or unexpected issues. For example, parameter settings could be overwritten after an upgrade.

CCE Cluster Resources
---------------------

There are resource quotas for your CCE clusters in each region.

+---------------------------------------------------------+------------------------------------------------------------+
| Item                                                    | Constraints on Common Users                                |
+=========================================================+============================================================+
| Total number of clusters in a region                    | 50                                                         |
+---------------------------------------------------------+------------------------------------------------------------+
| Number of nodes in a cluster (cluster management scale) | A maximum of 50, 200, 1000, or 2000 nodes can be selected. |
+---------------------------------------------------------+------------------------------------------------------------+
| Maximum number of pods on a node                        | 256                                                        |
+---------------------------------------------------------+------------------------------------------------------------+
| Maximum number of pods managed by a cluster             | 100,000 pods                                               |
+---------------------------------------------------------+------------------------------------------------------------+

Cluster Capacity Limit
----------------------

The capacity of a cluster is made up of various resource types, including container groups (pods), cloud storage instances (persistent volumes), and Services. Additionally, the size of these resource objects can also impact the cluster capacity.

For example:

-  If there are too many pods, the maximum number of pods will decrease within a certain performance range.
-  As the number of pods approaches the upper limit, the upper limits of other resource types in the cluster will also decrease accordingly.

Since clusters in actual application environments contain multiple resource types, it is possible that the number of resources for a single type may not reach its upper limit. It is important to monitor cluster resource usage regularly and plan and manage the resources effectively to ensure the best performance of all resources. If the current specifications do not meet your requirements, you can scale out the cluster to ensure stability.

Dependent Underlying Cloud Resources
------------------------------------

+----------------+-----------------------------------------+-----------------------------+
| Category       | Item                                    | Constraints on Common Users |
+================+=========================================+=============================+
| Compute        | Pods                                    | 1000                        |
+----------------+-----------------------------------------+-----------------------------+
|                | Cores                                   | 8000                        |
+----------------+-----------------------------------------+-----------------------------+
|                | RAM capacity (MB)                       | 16,384,000                  |
+----------------+-----------------------------------------+-----------------------------+
| Networking     | VPCs per account                        | 5                           |
+----------------+-----------------------------------------+-----------------------------+
|                | Subnets per account                     | 100                         |
+----------------+-----------------------------------------+-----------------------------+
|                | Security groups per account             | 100                         |
+----------------+-----------------------------------------+-----------------------------+
|                | Security group rules per account        | 5000                        |
+----------------+-----------------------------------------+-----------------------------+
|                | Routes per route table                  | 100                         |
+----------------+-----------------------------------------+-----------------------------+
|                | Routes per VPC                          | 100                         |
+----------------+-----------------------------------------+-----------------------------+
|                | VPC peering connections per region      | 50                          |
+----------------+-----------------------------------------+-----------------------------+
|                | Network ACLs per account                | 200                         |
+----------------+-----------------------------------------+-----------------------------+
|                | Layer 2 connection gateways per account | 5                           |
+----------------+-----------------------------------------+-----------------------------+
| Load balancing | Elastic load balancers                  | 50                          |
+----------------+-----------------------------------------+-----------------------------+
|                | Load balancer listeners                 | 100                         |
+----------------+-----------------------------------------+-----------------------------+
|                | Load balancer certificates              | 120                         |
+----------------+-----------------------------------------+-----------------------------+
|                | Load balancer forwarding policies       | 500                         |
+----------------+-----------------------------------------+-----------------------------+
|                | Load balancer backend host group        | 500                         |
+----------------+-----------------------------------------+-----------------------------+
|                | Load balancer backend server            | 500                         |
+----------------+-----------------------------------------+-----------------------------+
