:original_name: cce_10_0066.html

.. _cce_10_0066:

everest (System Resource Add-On, Mandatory)
===========================================

Introduction
------------

Everest is a cloud native container storage system. Based on the Container Storage Interface (CSI), clusters of Kubernetes v1.15.6 or later obtain access to cloud storage services.

**everest is a system resource add-on. It is installed by default when a cluster of Kubernetes v1.15 or later is created.**

Notes and Constraints
---------------------

-  If your cluster is upgraded from v1.13 to v1.15, :ref:`storage-driver <cce_10_0127>` is replaced by everest (v1.1.6 or later) for container storage. The takeover does not affect the original storage functions.
-  In version 1.2.0 of the everest add-on, **key authentication** is optimized when OBS is used. After the everest add-on is upgraded from a version earlier than 1.2.0, you need to restart all workloads that use OBS in the cluster. Otherwise, workloads may not be able to use OBS.
-  By default, this add-on is installed in **clusters of v1.15 and later**. For clusters of v1.13 and earlier, the :ref:`storage-driver <cce_10_0127>` add-on is installed by default.

Installing the Add-on
---------------------

This add-on has been installed by default. If it is uninstalled due to some reasons, you can reinstall it by performing the following steps:

#. Log in to the CCE console and access the cluster console. Choose **Add-ons** in the navigation pane, locate **everest** on the right, and click **Install**.

#. Select **Standalone**, **HA**, or **Custom** for **Add-on Specifications**.

   The everest add-on contains the following containers. You can adjust the specifications as required.

   -  **everest-csi-controller**: A Deployment workload. This container is responsible for creating, deleting, snapshotting, expanding, attaching, and detaching volumes. If the cluster version is 1.19 or later and the add-on version is 1.2.\ *x*, the pod of the everest-csi-driver component also has an everest-localvolume-manager container by default. This container manages the creation of LVM storage pools and local PVs on the node.

      .. note::

         If you select **Custom**, the recommended **everest-csi-controller** memory configuration is as follows:

         -  If the number of pods and PVCs is less than 2000, set the memory upper limit to 600 MiB.
         -  If the number of pods and PVCs is less than 5000, set the memory upper limit to 1 GiB.

   -  **everest-csi-driver**: A DaemonSet workload. This container is responsible for mounting and unmounting PVs and resizing file systems. If the add-on version is 1.2.\ *x* and the region where the cluster is located supports node-attacher, the pod of the everest-csi-driver component also contains an everest-node-attacher container. This container is responsible for distributed EVS attaching. This configuration item is available in some regions.

      .. note::

         If you select **Custom**, it is recommended that the **everest-csi-driver** memory limit be greater than or equal to 300 MiB. If the value is too small, the add-on container cannot be started and the add-on is unavailable.

#. Whether to deploy the add-on instance across multiple AZs.

   -  **Preferred**: Deployment pods of the add-on are preferentially scheduled to nodes in different AZs. If the nodes in the cluster do not meet the requirements of multiple AZs, the pods are scheduled to a single AZ.
   -  **Required**: Deployment pods of the add-on are forcibly scheduled to nodes in different AZs. If the nodes in the cluster do not meet the requirements of multiple AZs, not all pods can run.

#. Set related parameters.

   In everest 1.2.26 or later, the performance of attaching a large number of EVS volumes is optimized. The following three parameters are provided:

   -  **csi_attacher_worker_threads**: number of workers that can concurrently mount EVS volumes. The default value is **60**.
   -  **csi_attacher_detach_worker_threads**: number of workers that can concurrently unmount EVS volumes. The default value is **60**.
   -  **volume_attaching_flow_ctrl**: maximum number of EVS volumes that can be mounted by the everest add-on within one minute. The default value is **0**, indicating that the EVS volume mounting performance is determined by the underlying storage resources.

   The preceding three parameters are associated with each other and are constrained by the underlying storage resources in the region where the cluster is located. If you want to mount a large number of volumes (more than 500 EVS volumes per minute), you can contact the customer service personnel and configure the parameters under their guidance to prevent the everest add-on from running abnormally due to improper parameter settings.

   Other parameters

   -  **cluster_id**: cluster ID
   -  **default_vpc_id**: ID of the VPC to which the data warehouse cluster belongs
   -  **disable_auto_mount_secret**: indicates whether the default AK/SK can be used when an object bucket or parallel file system is mounted. The default value is **false**.
   -  **enable_node_attacher**: indicates whether to enable the attacher on the agent to process the `VolumeAttachment <https://kubernetes.io/docs/reference/kubernetes-api/config-and-storage-resources/volume-attachment-v1/>`__.
   -  **flow_control**: This parameter is left blank by default.
   -  **over_subscription**: overcommitment ratio of the local storage pool (**local_storage**). The default value is **80**. If the size of the local storage pool is 100 GB, you can overcommit 180 GB.
   -  **project_id**: ID of the project to which the cluster belongs.

#. Click **Install**.
