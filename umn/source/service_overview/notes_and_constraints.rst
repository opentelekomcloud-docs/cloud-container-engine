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
-  LoadBalancer Services allow workloads to be accessed from public networks through ELB. This access mode has the following restrictions:

   -  Automatically created load balancers should not be used by other resources. Otherwise, these load balancers cannot be completely deleted.
   -  Do not change the listener name for the load balancer in clusters of v1.15 and earlier. Otherwise, the load balancer cannot be accessed.

-  Constraints on network policies:

   -  Only clusters that use the tunnel network model support network policies. Network policies are classified into the following types:

      -  Ingress: All versions support this type.
      -  Egress: Only the following OSs and cluster versions support egress rules.

         +-----------------------+-----------------------+------------------------------------------+
         | OS                    | Cluster Version       | Verified Kernel Version                  |
         +=======================+=======================+==========================================+
         | EulerOS 2.9           | v1.23 or later        | 4.18.0-147.5.1.6.h541.eulerosv2r9.x86_64 |
         |                       |                       |                                          |
         |                       |                       | 4.18.0-147.5.1.6.h766.eulerosv2r9.x86_64 |
         |                       |                       |                                          |
         |                       |                       | 4.18.0-147.5.1.6.h998.eulerosv2r9.x86_64 |
         |                       |                       |                                          |
         |                       |                       | 4.18.0-147.5.1.6.h1305.eulerosv2r9.x86_64|
         +-----------------------+-----------------------+------------------------------------------+
         | HCE OS 2.0            | v1.25 or later        | 5.10.0-60.18.0.50.r865_35.hce2.x86_64    |
         |                       |                       |                                          |
         |                       |                       | 5.10.0-182.0.0.95.r1941_123.hce2.x86_64  |
         +-----------------------+-----------------------+------------------------------------------+

   -  Network isolation is not supported for IPv6 addresses.
   -  If upgrade to a cluster version that supports egress rules is performed in in-place mode, you cannot use egress rules because the node OS is not upgraded. In this case, reset the node.

Storage Volumes
---------------

-  Constraints on EVS volumes:

   -  EVS disks cannot be attached across AZs and cannot be used by multiple workloads, multiple pods of the same workload, or multiple tasks. Data sharing of a shared disk is not supported between nodes in a CCE cluster. If an EVS disk is attacked to multiple nodes, I/O conflicts and data cache conflicts may occur. Therefore, create only one pod when creating a Deployment that uses EVS disks.

   -  For clusters earlier than v1.19.10, if an HPA policy is used to scale out a workload with EVS volumes mounted, the existing pods cannot be read or written when a new pod is scheduled to another node.

      For clusters of v1.19.10 and later, if an HPA policy is used to scale out a workload with EVS volumes mounted, a new pod cannot be started because EVS disks cannot be attached.

-  Constraints on SFS volumes:

   -  Multiple PVs can use the same SFS or SFS Turbo file system with the following restrictions:

      -  Do not mount all PVCs/PVs that use the same underlying SFS or SFS Turbo file system to a pod. This leads to a pod startup failure because not all PVCs can be mounted to the pod due to the same **volumeHandle** values of these PVs.
      -  The **persistentVolumeReclaimPolicy** parameter in the PVs is suggested to be set to **Retain**. Otherwise, when a PV is deleted, the associated underlying volume may be deleted. In this case, other PVs associated with the underlying volume malfunction.
      -  When the underlying volume is repeatedly used, enable isolation and protection for ReadWriteMany at the application layer to prevent data overwriting and loss.

   -  SFS volumes are available only in certain regions.

-  Constraints on OBS volumes:

   -  If OBS volumes are used, the owner group and permission of the mount point cannot be modified.

   -  CCE allows parallel file systems to be mounted using OBS SDKs or PVCs. If PVC mounting is used, the obsfs tool provided by OBS must be used. An obsfs resident process is generated each time an object storage volume generated from the parallel file system is mounted to a node, as shown in the following figure.


      .. figure:: /_static/images/en-us_image_0000001897906197.png
         :alt: **Figure 1** obsfs resident process

         **Figure 1** obsfs resident process

      Reserve 1 GiB of memory for each obsfs process. For example, for a node with 4 vCPUs and 8 GiB of memory, an obsfs parallel file system should be mounted to **no more than** eight pods.

      .. note::

         -  An obsfs resident process runs on a node. If the consumed memory exceeds the upper limit of the node, the node malfunctions. On a node with 4 vCPUs and 8 GiB of memory, if more than 100 pods are mounted to a parallel file system, the node will be unavailable. Control the number of pods mounted to a parallel file system on a single node.

   -  Kata containers do not support OBS volumes.

-  Restrictions on using local PVs:

   -  Local PVs are supported only when the cluster version is v1.21.2-r0 or later and the Everest add-on version is 2.1.23 or later. Version 2.1.23 or later is recommended.
   -  Removing, deleting, resetting, or scaling in a node will cause the PVC/PV data of the local PV associated with the node to be lost, which cannot be restored or used again. In these scenarios, the pod that uses the local PV is evicted from the node. A new pod will be created and stays in the pending state. This is because the PVC used by the pod has a node label, due to which the pod cannot be scheduled. After the node is reset, the pod may be scheduled to the reset node. In this case, the pod remains in the creating state because the underlying logical volume corresponding to the PVC does not exist.
   -  Do not manually delete the corresponding storage pool or detach data disks from the node. Otherwise, exceptions such as data loss may occur.
   -  A local PV cannot be mounted to multiple workloads or jobs at the same time.

-  Restrictions on using local EVs:

   -  Local EVs are supported only when the cluster version is v1.21.2-r0 or later and the Everest add-on version is 1.2.29 or later.
   -  Do not manually delete the corresponding storage pool or detach data disks from the node. Otherwise, exceptions such as data loss may occur.
   -  Ensure that the **/var/lib/kubelet/pods/** directory is not mounted to the pod on the node. Otherwise, the pod, mounted with such volumes, may fail to be deleted.

-  Constraints on snapshots and backups:

   -  The snapshot function is available **only for clusters of v1.15 or later** and requires the CSI-based Everest add-on.
   -  The subtype (common I/O, high I/O, or ultra-high I/O), disk mode (SCSI or VBD), data encryption, sharing status, and capacity of an EVS disk created from a snapshot must be the same as those of the disk associated with the snapshot. These attributes cannot be modified after being queried or set.
   -  Snapshots can be created only for EVS disks that are available or in use, and a maximum of seven snapshots can be created for a single EVS disk.
   -  Snapshots can be created only for PVCs created using the storage class (whose name starts with csi) provided by the Everest add-on. Snapshots cannot be created for PVCs created using the Flexvolume storage class whose name is ssd, sas, or sata.
   -  Snapshot data of encrypted disks is stored encrypted, and that of non-encrypted disks is stored non-encrypted.
   -  A PVC of the xfs file system type can generate snapshots. The file system of the disk associated with the PVC created using these snapshots remains xfs.

Add-ons
-------

CCE uses Helm charts to deploy add-ons. To modify or upgrade an add-on, perform operations on the **Add-ons** page or use open add-on management APIs. Do not directly modify add-on resources on the backend. Otherwise, add-on exceptions or other unexpected problems may occur.

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
-  As the number of pods approaches the upper limit, the upper limits of other resource types in the cluster will decrease accordingly.

Since clusters in actual application environments contain multiple resource types, it is possible that the number of resources for a single type may not reach its upper limit. It is important to monitor cluster usage regularly and plan and manage the cluster effectively to ensure the best performance of all resources. If the current specifications do not meet your requirements, you can scale out the cluster to ensure stability.

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
