:original_name: cce_10_0302.html

.. _cce_10_0302:

Before You Start
================

Before the upgrade, you can check whether your cluster can be upgraded and which versions are available on the CCE console. For details, see :ref:`Upgrade Overview <cce_10_0197>`.

Precautions
-----------

-  **Upgraded clusters cannot be rolled back. Therefore, perform the upgrade during off-peak hours to minimize the impact on your services.**
-  Do not **shut down, restart, or delete nodes** during cluster upgrade. Otherwise, the upgrade fails.
-  Before upgrading a cluster, **disable auto scaling policies** to prevent node scaling during the upgrade. Otherwise, the upgrade fails.
-  If you locally modify the configuration of a cluster node, the cluster upgrade may fail or the configuration may be lost after the upgrade. Therefore, modify the configurations on the CCE console (cluster or node pool list page) so that they will be automatically inherited during the upgrade.
-  During the cluster upgrade, the running workload services will not be interrupted, but access to the API server will be temporarily interrupted.
-  Before upgrading the cluster, check whether the cluster is healthy.
-  To ensure data security, you are advised to back up data before upgrading the cluster. During the upgrade, you are not advised to perform any operations on the cluster.
-  During the cluster upgrade, the **node.kubernetes.io/upgrade** taint (the effect is **NoSchedule**) is added to the node. After the cluster upgrade is complete, the taint is removed. Do not add taints with the same key name on the node. Even if the taints have different effects, they may be deleted by the system by mistake after the upgrade.

Notes and Constraints
---------------------

-  Currently, only CCE clusters consisting of VM nodes and CCE Turbo clusters can be upgraded.

-  Currently, clusters using private images cannot be upgraded.

-  After the cluster is upgraded, if the containerd vulnerability of the container engine is fixed in :ref:`Cluster Version Release Notes <cce_10_0068>`, you need to manually restart containerd for the upgrade to take effect. The same applies to the existing pods.

-  If initContainer or Istio is used in the in-place upgrade of a cluster of v1.15, pay attention to the following restrictions:

   In kubelet 1.16 and later versions, `QoS classes <https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/>`__ are different from those in earlier versions. In kubelet 1.15 and earlier versions, only containers in **spec.containers** are counted. In kubelet 1.16 and later versions, containers in both **spec.containers** and **spec.initContainers** are counted. The QoS class of a pod will change after the upgrade. As a result, the container in the pod restarts. You are advised to modify the QoS class of the service container before the upgrade to avoid this problem. For details, see :ref:`Table 1 <cce_10_0302__table10713231143911>`.

   .. _cce_10_0302__table10713231143911:

   .. table:: **Table 1** QoS class changes before and after the upgrade

      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Init Container (Calculated Based on spec.initContainers) | Service Container (Calculated Based on spec.containers) | Pod (Calculated Based on spec.containers and spec.initContainers) | Impacted or Not |
      +==========================================================+=========================================================+===================================================================+=================+
      | Guaranteed                                               | Besteffort                                              | Burstable                                                         | Yes             |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Guaranteed                                               | Burstable                                               | Burstable                                                         | No              |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Guaranteed                                               | Guaranteed                                              | Guaranteed                                                        | No              |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Besteffort                                               | Besteffort                                              | Besteffort                                                        | No              |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Besteffort                                               | Burstable                                               | Burstable                                                         | No              |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Besteffort                                               | Guaranteed                                              | Burstable                                                         | Yes             |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Burstable                                                | Besteffort                                              | Burstable                                                         | Yes             |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Burstable                                                | Burstable                                               | Burstable                                                         | No              |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Burstable                                                | Guaranteed                                              | Burstable                                                         | Yes             |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+

Upgrade Backup
--------------

How to back up a node:

-  etcd database backup: CCE automatically backs up the etcd database during the cluster upgrade.
-  Master node backup (recommended, **manual confirmation required**): On the upgrade confirmation page, click **Backup** to back up the entire master node of the cluster. The backup process uses the Cloud Backup and Recovery (CBR) service and takes about 20 minutes. If there are many cloud backup tasks at the current site, the backup time may be prolonged.
