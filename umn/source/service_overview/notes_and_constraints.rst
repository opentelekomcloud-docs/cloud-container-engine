:original_name: cce_productdesc_0005.html

.. _cce_productdesc_0005:

Notes and Constraints
=====================

This section describes the notes and constraints on using CCE.

Clusters and Nodes
------------------

-  After a cluster is created, the following items cannot be changed:

   -  Number of master nodes. For example, you cannot change a non-HA cluster (with one master node) to an HA cluster (with three master nodes).
   -  AZ of a master node.
   -  Network configuration of the cluster, such as the VPC, subnet, container CIDR block, Service CIDR block, IPv6 settings, and kube-proxy (forwarding) settings.
   -  Network model. For example, change the **tunnel network** to the **VPC network**.

-  Applications cannot be migrated between different namespaces.
-  Underlying resources, such as ECSs (nodes), are limited by quotas and their inventory. Therefore, only some nodes may be successfully created during cluster creation, cluster scaling, or auto scaling.
-  The ECS (node) specifications must be higher than 2 cores and 4 GB memory.
-  To access a CCE cluster through a VPN, ensure that the VPN CIDR block does not conflict with the VPC CIDR block where the cluster resides and the container CIDR block.
-  Ubuntu 22.04 does not support the tunnel network model.

Networking
----------

-  By default, a NodePort Service is accessed within a VPC. If you need to use an EIP to access a NodePort Service through public networks, bind an EIP to the node in the cluster in advance.
-  LoadBalancer Services allow workloads to be accessed from public networks through **ELB**. This access mode has the following restrictions:

   -  It is recommended that automatically created load balancers not be used by other resources. Otherwise, these load balancers cannot be completely deleted, causing residual resources.
   -  Do not change the listener name for the load balancer in clusters of v1.15 and earlier. Otherwise, the load balancer cannot be accessed.

-  Constraints on network policies:

   -  Only clusters that use the tunnel network model support network policies.

   -  Network isolation is not supported for IPv6 addresses.

   -  Network policies do not support egress rules except for clusters of v1.23 or later.

      Egress rules are supported only in the following operating systems:

      +-----------------------------------+-------------------------------------------+
      | OS                                | Kernel Version                            |
      +===================================+===========================================+
      | CentOS                            | 3.10.0-1062.18.1.el7.x86_64               |
      |                                   |                                           |
      |                                   | 3.10.0-1127.19.1.el7.x86_64               |
      |                                   |                                           |
      |                                   | 3.10.0-1160.25.1.el7.x86_64               |
      +-----------------------------------+-------------------------------------------+
      | EulerOS 2.5                       | 3.10.0-862.14.1.5.h591.eulerosv2r7.x86_64 |
      +-----------------------------------+-------------------------------------------+
      | EulerOS 2.9                       | 4.18.0-147.5.1.6.h541.eulerosv2r9.x86_64  |
      +-----------------------------------+-------------------------------------------+

   -  If a cluster is upgraded to v1.23 in in-place mode, you cannot use egress rules because the node OS is not upgraded. In this case, reset the node.

Volumes
-------

-  Constraints on EVS volumes:

   -  EVS disks cannot be attached across AZs and cannot be used by multiple workloads, multiple pods of the same workload, or multiple jobs.

   -  Data in a shared disk cannot be shared between nodes in a CCE cluster. If the same EVS disk is attached to multiple nodes, read and write conflicts and data cache conflicts may occur. When creating a Deployment, you are advised to create only one pod if you want to use EVS disks.

   -  For clusters earlier than v1.19.10, if an HPA policy is used to scale out a workload with EVS volumes mounted, the existing pods cannot be read or written when a new pod is scheduled to another node.

      For clusters of v1.19.10 and later, if an HPA policy is used to scale out a workload with EVS volume mounted, a new pod cannot be started because EVS disks cannot be attached.

   -  When you create a StatefulSet and add a cloud storage volume, existing EVS volumes cannot be used.

   -  EVS disks that have partitions or have non-ext4 file systems cannot be imported.

   -  Container storage in CCE clusters of Kubernetes 1.13 or later version supports encryption. Currently, E2E encryption is supported only in certain regions.

   -  EVS volumes cannot be created in specified enterprise projects. Only the default enterprise project is supported.

-  Constraints on SFS volumes:

   -  SFS volumes are available only in certain regions.
   -  Container storage in CCE clusters of Kubernetes 1.13 or later version supports encryption. Currently, E2E encryption is supported only in certain regions.
   -  Volumes cannot be created in specified enterprise projects. Only the default enterprise project is supported.

-  Constraints on OBS volumes:

   -  CCE clusters of v1.7.3-r8 and earlier do not support OBS volumes. You need to upgrade these clusters or create clusters of a later version that supports OBS.
   -  Volumes cannot be created in specified enterprise projects. Only the default enterprise project is supported.

-  Constraints on snapshots and backups:

   -  The snapshot function is available **only for clusters of v1.15 or later** and requires the CSI-based everest add-on.
   -  The subtype (common I/O, high I/O, or ultra-high I/O), disk mode (SCSI or VBD), data encryption, sharing status, and capacity of an EVS disk created from a snapshot must be the same as those of the disk associated with the snapshot. These attributes cannot be modified after being queried or set.

Services
--------

A Service is a Kubernetes resource object that defines a logical set of pods and a policy by which to access them.

A maximum of 6,000 Services can be created in each namespace.

CCE Cluster Resources
---------------------

There are resource quotas for your CCE clusters in each region.

+--------------------------------------------------------------+------------------------------------------------------------------------+
| Item                                                         | Constraints on Common Users                                            |
+==============================================================+========================================================================+
| Total number of clusters in a region                         | 50                                                                     |
+--------------------------------------------------------------+------------------------------------------------------------------------+
| Number of nodes in a cluster (cluster management scale)      | You can select 50, 200, 1,000, or 2,000 nodes.                         |
+--------------------------------------------------------------+------------------------------------------------------------------------+
| Maximum number of container pods created on each worker node | This number can be set on the console when you are creating a cluster. |
|                                                              |                                                                        |
|                                                              | In the VPC network model, a maximum of 256 pods can be created.        |
+--------------------------------------------------------------+------------------------------------------------------------------------+

Dependent Underlying Cloud Resources
------------------------------------

+----------------+-----------------------------------------+-----------------------------+
| Category       | Item                                    | Constraints on Common Users |
+================+=========================================+=============================+
| Compute        | Pods                                    | 1,000                       |
+----------------+-----------------------------------------+-----------------------------+
|                | Cores                                   | 8,000                       |
+----------------+-----------------------------------------+-----------------------------+
|                | RAM capacity (MB)                       | 16384000                    |
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
