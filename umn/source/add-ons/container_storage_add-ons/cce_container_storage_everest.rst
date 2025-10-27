:original_name: cce_10_0066.html

.. _cce_10_0066:

CCE Container Storage (Everest)
===============================

Introduction
------------

Container Storage Interface (CSI) is a storage add-on standard recommended by the Kubernetes community. It is used for unified interconnection between the container orchestration platform and various storage systems. Based on the CSI standard, CCE provides CCE Container Storage (Everest), a self-developed storage add-on. This add-on can seamlessly interconnect with multiple IaaS storage services, including EVS (block storage), SFS, OBS, and SFS Turbo, to provide multiple types of persistent storage capabilities for containers.

CCE Container Storage (Everest) provides diverse persistent storage capabilities for clusters. It can adapt to storage requirements in various service scenarios, such as databases, high-performance computing, shared file access, and large file archiving. This add-on provides a unified storage interface, supports multiple types of storage media, and works closely with CCE scheduling policies, auto scaling strategies, and security systems. It can significantly improve the storage performance and service continuity of cloud native workloads.

**Everest is a system resource add-on. It is installed by default when a cluster of Kubernetes v1.15 or later is created.**

Notes and Constraints
---------------------

-  In version 1.2.0 of the Everest add-on, **key authentication** is optimized when OBS is used. After the Everest add-on is upgraded from a version earlier than 1.2.0, restart all workloads that use OBS in the cluster. Otherwise, workloads may not be able to use OBS.

Installing the Add-on
---------------------

This add-on has been installed by default. If it is uninstalled due to some reasons, you can reinstall it by performing the following steps:

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Add-ons**. Locate **CCE Container Storage (Everest)** and click **Edit**.
#. On the **Install Add-on** page, configure the specifications as needed.

   -  If you selected **Preset**, you can choose between **Small**, **Medium**, or **Large** as needed. The system will automatically set the number of add-on pods and resource quotas according to the preset specifications. You can see the configurations on the console.

      The small specification is best for clusters with up to 50 nodes and 500 PVCs. The medium specification works well for clusters with up to 200 nodes and 2000 PVCs. The large specification is perfect for clusters with up to 1000 nodes and 10,000 PVCs.

   -  If you selected **Custom**, you can adjust the number of pods and resource quotas as needed. The requested CPUs and memory can be adjusted based on the number of nodes and PVCs. For details, see :ref:`Table 1 <cce_10_0066__table189158231597>`.

      In non-typical scenarios, the formulas for estimating the limits are as follows:

      -  everest-csi-controller

         -  CPU limit: 250m for 200 or fewer nodes, 350m for 1000 nodes, and 500m for 2000 nodes
         -  Memory limit = (200 MiB + Number of nodes x 1 MiB + Number of PVCs x 0.2 MiB) x 1.2

      -  everest-csi-driver

         -  CPU limit: 300m for 200 or fewer nodes, 500m for 1000 nodes, and 800m for 2000 nodes
         -  Memory limit: 300 MiB for 200 or fewer nodes, 600 MiB for 1000 nodes, and 900 MiB for 2000 nodes

      .. _cce_10_0066__table189158231597:

      .. table:: **Table 1** Recommended configuration limits in typical scenarios

         +------------------------+----------+-------------+-----------------------------+--------------------------+-----------------------------+--------------------------+
         | Configuration Scenario |          |             | everest-csi-controller      |                          | everest-csi-driver          |                          |
         +========================+==========+=============+=============================+==========================+=============================+==========================+
         | Nodes                  | PVs/PVCs | Add-on Pods | CPU Cores (Limit = Request) | Memory (Limit = Request) | CPU Cores (Limit = Request) | Memory (Limit = Request) |
         +------------------------+----------+-------------+-----------------------------+--------------------------+-----------------------------+--------------------------+
         | 50                     | 1000     | 2           | 250m                        | 600 MiB                  | 300m                        | 300 MiB                  |
         +------------------------+----------+-------------+-----------------------------+--------------------------+-----------------------------+--------------------------+
         | 200                    | 1000     | 2           | 250m                        | 1 GiB                    | 300m                        | 300 MiB                  |
         +------------------------+----------+-------------+-----------------------------+--------------------------+-----------------------------+--------------------------+
         | 1000                   | 1000     | 2           | 350m                        | 2 GiB                    | 500m                        | 600 MiB                  |
         +------------------------+----------+-------------+-----------------------------+--------------------------+-----------------------------+--------------------------+
         | 1000                   | 5000     | 2           | 450m                        | 3 GiB                    | 500m                        | 600 MiB                  |
         +------------------------+----------+-------------+-----------------------------+--------------------------+-----------------------------+--------------------------+
         | 2000                   | 5000     | 2           | 550m                        | 4 GiB                    | 800m                        | 900 MiB                  |
         +------------------------+----------+-------------+-----------------------------+--------------------------+-----------------------------+--------------------------+
         | 2000                   | 10000    | 2           | 650m                        | 5 GiB                    | 800m                        | 900 MiB                  |
         +------------------------+----------+-------------+-----------------------------+--------------------------+-----------------------------+--------------------------+

#. Configure the add-on parameters.

   .. table:: **Table 2** Add-on parameters

      +-----------------------------------------------------+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Item                                                | Configuration Method         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                         |
      +=====================================================+==============================+=====================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | Reserved EVS Disks for Non-Container Workloads      | Visualized GUI configuration | Number of disks on the node reserved for custom use. This parameter is supported when the add-on version is 2.3.11 or later.                                                                                                                                                                                                                                                                                                                        |
      |                                                     |                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                                     |                              | Assume that a maximum of 20 EVS disks can be attached to a node, and the value of this parameter is set to **6**. Then 14 (20-6) disks can be attached to this node when the system schedules the EVS disk attachment workloads. The reserved six disks include one system disk and one data disk that are already attached to the node. You can attach four EVS disks to this node as additional data disks or raw disks for a local storage pool. |
      +-----------------------------------------------------+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Prohibit Global Secret from Mounting Object Storage | Visualized GUI configuration | Whether the default AK/SK can be used when an object bucket or parallel file system is mounted.                                                                                                                                                                                                                                                                                                                                                     |
      |                                                     |                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                                     |                              | -  **is**: If you do not specify a custom secret for mounting OBS storage, the global secret you uploaded will be used.                                                                                                                                                                                                                                                                                                                             |
      |                                                     |                              | -  **No**: If you do not specify a custom secret for mounting OBS storage, only the global secret you uploaded can be used. Otherwise, the mounting will fail.                                                                                                                                                                                                                                                                                      |
      +-----------------------------------------------------+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Concurrent EVS Disk Attaching Tasks                 | Visualized GUI configuration | Number of workers that can be concurrently processed by Everest for attaching EVS volumes. The default value is **60**.                                                                                                                                                                                                                                                                                                                             |
      +-----------------------------------------------------+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Concurrent EVS Disk Detaching Tasks                 | Visualized GUI configuration | Number of workers that can be concurrently processed by Everest for detaching EVS volumes. The default value is **60**.                                                                                                                                                                                                                                                                                                                             |
      +-----------------------------------------------------+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Distributed Volume Mounting                         | Visualized GUI configuration | When enabled, the everest-csi-driver component on each node is responsible for attaching or detaching EVS disks.                                                                                                                                                                                                                                                                                                                                    |
      |                                                     |                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                                     |                              | If disabled, the everest-csi-controller component takes on this responsibility.                                                                                                                                                                                                                                                                                                                                                                     |
      +-----------------------------------------------------+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | flow_control                                        | Extended parameter settings  | The default configuration is used. After installing the CCE Container Storage (Everest) add-on, you can refer to ConfigMap everest-driver-th-config in the kube-system namespace for more details.                                                                                                                                                                                                                                                  |
      +-----------------------------------------------------+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | over_subscription                                   | Extended parameter settings  | Overcommitment ratio of the local storage pool (**local_storage**). The default value for the local storage pool size is set to **80**. If the pool size is 100 GiB, it can be overcommitted up to 180 GiB. (Total capacity after overcommitment = (1 + Overcommitment ratio) x Actual capacity)                                                                                                                                                    |
      +-----------------------------------------------------+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | enable_local_autoexpander                           | Extended parameter settings  | Whether to enable automatic scale-out for the local storage pool of thin volumes. If this function is enabled, automatic scale-out is triggered based on the scale-out threshold and scale-out step of the local storage pool.                                                                                                                                                                                                                      |
      +-----------------------------------------------------+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | expansion_threshold                                 | Extended parameter settings  | Capacity expansion threshold of the local storage pool of the thin volume. When the usage of the local storage pool of the thin volume exceeds the threshold, the local storage pool is automatically expanded.                                                                                                                                                                                                                                     |
      +-----------------------------------------------------+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | expansion_step                                      | Extended parameter settings  | Capacity expansion step of a single EVS disk in the local storage pool of thin provisioning volumes (unit: Gi)                                                                                                                                                                                                                                                                                                                                      |
      +-----------------------------------------------------+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | expansion_max_evs_size                              | Extended parameter settings  | Maximum capacity of a single EVS disk in the local storage pool of thin provisioning volumes (unit: Gi)                                                                                                                                                                                                                                                                                                                                             |
      +-----------------------------------------------------+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | volume_attaching_flow_ctrl                          | Extended parameter settings  | Maximum number of EVS volumes that can be attached by the Everest add-on within 1 minute. The default value is **0**, indicating that the performance of attaching EVS volumes is determined by the underlying storage resources.                                                                                                                                                                                                                   |
      +-----------------------------------------------------+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | enable_aksk_refresh                                 | Extended parameter settings  | Whether to enable the function of dynamically updating custom access keys (AK/SK) using parallel file systems. This parameter is supported when the add-on version is 2.4.150 or later. For details, see :ref:`Automatically Applying Updated Access Keys (AK/SK) for an OBS Volume <cce_10_0966>`. The default value is **false**, indicating that the function is disabled. The value **true** indicates that the function is enabled.            |
      |                                                     |                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                                     |                              | Whether to enable the function of dynamically updating custom access keys (AK/SK) using object storage. This parameter is supported when the add-on version is 2.4.150 or later. For details, see :ref:`Automatically Applying Updated Access Keys (AK/SK) for an OBS Volume <cce_10_0966>`. The default value is **false**, indicating that the function is disabled. The value **true** indicates that the function is enabled.                   |
      |                                                     |                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                                     |                              | If the add-on version is between 2.4.150 (inclusive) and 2.4.165 (exclusive), only parallel file systems are supported. Starting from version 2.4.165 and onward, both parallel file systems and OBS buckets are supported.                                                                                                                                                                                                                         |
      +-----------------------------------------------------+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. note::

      In the extended parameter settings, you can customize the advanced configurations that are not displayed on the GUI. If the settings in the extended parameters conflict with those on the GUI, the settings in the extended parameters will work.

#. Configure deployment policies for the add-on pods.

   .. note::

      -  Scheduling policies do not take effect on add-on pods of the DaemonSet type.
      -  When configuring multi-AZ deployment or node affinity, ensure that there are nodes meeting the scheduling policy and that resources are sufficient in the cluster. Otherwise, the add-on cannot run.

   .. table:: **Table 3** Configurations for add-on scheduling

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                    |
      +===================================+================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | Multi-AZ Deployment               | -  **Preferred**: Deployment pods of the add-on will be preferentially scheduled to nodes in different AZs. If all the nodes in the cluster are deployed in the same AZ, the pods will be scheduled to different nodes in that AZ.                                                                                                                                                                                                             |
      |                                   | -  **Equivalent mode**: Deployment pods of the add-on are evenly scheduled to the nodes in the cluster in each AZ. If a new AZ is added, you are advised to increase add-on pods for cross-AZ HA deployment. With the Equivalent multi-AZ deployment, the difference between the number of add-on pods in different AZs will be less than or equal to 1. If resources in one of the AZs are insufficient, pods cannot be scheduled to that AZ. |
      |                                   | -  **Forcible**: Deployment pods of the add-on are forcibly scheduled to nodes in different AZs. There can be at most one pod in each AZ. If nodes in a cluster are not in different AZs, some add-on pods cannot run properly. If a node is faulty, add-on pods on it may fail to be migrated.                                                                                                                                                |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Node Affinity                     | -  **Not configured**: Node affinity is disabled for the add-on.                                                                                                                                                                                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Specify node**: Specify the nodes where the add-on is deployed. If you do not specify the nodes, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Specify node pool**: Specify the node pool where the add-on is deployed. If you do not specify the node pools, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Customize affinity**: Enter the labels of the nodes where the add-on is to be deployed for more flexible scheduling policies. If you do not specify node labels, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   |    If multiple custom affinity policies are configured, ensure that there are nodes that meet all the affinity policies in the cluster. Otherwise, the add-on cannot run.                                                                                                                                                                                                                                                                      |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Toleration                        | Using both taints and tolerations allows (not forcibly) the add-on Deployment to be scheduled to a node with the matching taints, and controls the Deployment eviction policies after the node where the Deployment is located is tainted.                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | The add-on adds the default tolerance policy for the **node.kubernetes.io/not-ready** and **node.kubernetes.io/unreachable** taints, respectively. The tolerance time window is 60s.                                                                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | For details, see :ref:`Configuring Tolerance Policies <cce_10_0728>`.                                                                                                                                                                                                                                                                                                                                                                          |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Install**.

Components
----------

.. table:: **Table 4** Add-on components

   +------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
   | Component              | Description                                                                                                                                                                                                                                                                                                                                                                    | Resource Type |
   +========================+================================================================================================================================================================================================================================================================================================================================================================================+===============+
   | everest-csi-controller | Used to create, delete, snapshot, expand, attach, and detach storage volumes. If the cluster version is 1.19 or later and the add-on version is 1.2.\ *x*, the pod of the everest-csi-controller component also has an everest-localvolume-manager container by default. This container manages the creation of LVM storage pools and local PVs on the node.                   | Deployment    |
   +------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
   | everest-csi-driver     | Used to mount and unmount PVs and resize file systems. If the add-on version is 1.2.\ *x* and the region where the cluster is located supports node-attacher, the pod of the everest-csi-driver component also contains an everest-node-attacher container. This container is responsible for distributed EVS attaching. This configuration item is available in some regions. | DaemonSet     |
   +------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+

Collecting Prometheus Metrics
-----------------------------

everest-csi-controller exposes Prometheus metrics over port 3225. You can create an on-premises Prometheus collector to identify and obtain everest-csi-controller metrics from **http://{**\ *{everest-csi-controller pod IP address}*\ **}:3225/metrics**.

.. note::

   Prometheus metrics can be exposed only when the Everest add-on version is 2.4.4 or later.

.. table:: **Table 5** Key metrics

   +------------------------------------------+-------------+-------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------+
   | Metric                                   | Type        | Description                                                             | Label                                                                                                          | Example                                                                                            |
   +==========================================+=============+=========================================================================+================================================================================================================+====================================================================================================+
   | everest_action_result_total              | Counter     | Invoking of different functions                                         | **action**: indicates different functions. For details, see :ref:`Table 6 <cce_10_0066__table195218413174>`.   | everest_action_result_total{action="create_snapshot:disk.csi.everest.io",result="success"} 2       |
   |                                          |             |                                                                         |                                                                                                                |                                                                                                    |
   |                                          |             |                                                                         | **result**: indicates that the invoking is successful or fails.                                                |                                                                                                    |
   +------------------------------------------+-------------+-------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------+
   | everest_function_duration_seconds_bucket | Histogram   | Number of times that different functions are executed at different time | **function**: indicates different functions. For details, see :ref:`Table 6 <cce_10_0066__table195218413174>`. | everest_function_duration_seconds_bucket{function="create_snapshot:disk.csi.everest.io",le="10"} 2 |
   +------------------------------------------+-------------+-------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------+
   | everest_function_duration_seconds_sum    | Histogram   | Total invoking time of different functions                              | **function**: indicates different functions. For details, see :ref:`Table 6 <cce_10_0066__table195218413174>`. | everest_function_duration_seconds_sum{function="create:disk.csi.everest.io"} 24.381399053          |
   +------------------------------------------+-------------+-------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------+
   | everest_function_duration_seconds_count  | Histogram   | Number of invoking times of different functions                         | **function**: indicates different functions. For details, see :ref:`Table 6 <cce_10_0066__table195218413174>`. | everest_function_duration_seconds_count{function="attach:disk.csi.everest.io"} 4                   |
   +------------------------------------------+-------------+-------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------+

**action** and **function** specify different CSI drivers and their functions, and are in the format of *{Function}*\ **:**\ *{CSI driver}*. For example, **create:disk.csi.everest.io** specifies that the function is to create a volume and the volume type is EVS disk.

.. _cce_10_0066__table195218413174:

.. table:: **Table 6** Functions

   =============== =================================
   Operation       Description
   =============== =================================
   create          Creates a volume.
   delete          Deletes a volume.
   attach          Mounts a volume.
   detach          Detaches a volume.
   expand          Expands the capacity of a volume.
   create_snapshot Creates a volume snapshot.
   delete_snapshot Deletes a volume snapshot
   =============== =================================

Release History
---------------

.. table:: **Table 7** CCE Container Storage (Everest) add-on

   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | Add-on Version        | Supported Cluster Version | New Feature                                                                                            |
   +=======================+===========================+========================================================================================================+
   | 2.4.161               | v1.25                     | CCE clusters v1.32 are supported.                                                                      |
   |                       |                           |                                                                                                        |
   |                       | v1.27                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.28                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.29                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.30                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.31                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.32                     |                                                                                                        |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | 2.4.134               | v1.25                     | Fixed some issues.                                                                                     |
   |                       |                           |                                                                                                        |
   |                       | v1.27                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.28                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.29                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.30                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.31                     |                                                                                                        |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | 2.4.75                | v1.23                     | On HCE OS 2.0 nodes, you can configure the EVS PVC's fstype to xfs.                                    |
   |                       |                           |                                                                                                        |
   |                       | v1.25                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.27                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.28                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.29                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.30                     |                                                                                                        |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | 2.4.28                | v1.23                     | Fixed some issues.                                                                                     |
   |                       |                           |                                                                                                        |
   |                       | v1.25                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.27                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.28                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.29                     |                                                                                                        |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | 2.3.23                | v1.21                     | Subdirectories can be created in an SFS Turbo file system.                                             |
   |                       |                           |                                                                                                        |
   |                       | v1.23                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.25                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.27                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.28                     |                                                                                                        |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | 2.3.21                | v1.21                     | Fixed some issues.                                                                                     |
   |                       |                           |                                                                                                        |
   |                       | v1.23                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.25                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.27                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.28                     |                                                                                                        |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | 2.3.14                | v1.21                     | CCE clusters v1.28 are supported.                                                                      |
   |                       |                           |                                                                                                        |
   |                       | v1.23                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.25                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.27                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.28                     |                                                                                                        |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | 2.1.51                | v1.19                     | Supported HCE OS 2.0.                                                                                  |
   |                       |                           |                                                                                                        |
   |                       | v1.21                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.23                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.25                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.27                     |                                                                                                        |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | 2.1.30                | v1.19                     | -  Supported anti-affinity scheduling of add-on pods on nodes in different AZs.                        |
   |                       |                           | -  Adapted the obsfs package to Ubuntu 22.04.                                                          |
   |                       | v1.21                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.23                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.25                     |                                                                                                        |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | 2.1.13                | v1.19                     | Optimized the performance of creating subPath PVCs in batches for SFS Turbo volumes.                   |
   |                       |                           |                                                                                                        |
   |                       | v1.21                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.23                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.25                     |                                                                                                        |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | 1.3.28                | v1.19                     | -  Enabled graceful exit.                                                                              |
   |                       |                           | -  Supported OBS process monitoring.                                                                   |
   |                       | v1.21                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.23                     |                                                                                                        |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | 1.2.78                | v1.15                     | Supported anti-affinity scheduling of add-on pods on nodes in different AZs.                           |
   |                       |                           |                                                                                                        |
   |                       | v1.17                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.19                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.21                     |                                                                                                        |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | 1.2.70                | v1.15                     | Optimized the performance of creating subPath PVCs in batches for SFS Turbo volumes.                   |
   |                       |                           |                                                                                                        |
   |                       | v1.17                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.19                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.21                     |                                                                                                        |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | 1.2.44                | v1.15                     | -  By default, the **enable_noobj_cache** parameter is no longer used for mounting OBS buckets.        |
   |                       |                           |                                                                                                        |
   |                       | v1.17                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.19                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.21                     |                                                                                                        |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | 1.2.30                | v1.15                     | Supported emptyDir.                                                                                    |
   |                       |                           |                                                                                                        |
   |                       | v1.17                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.19                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.21                     |                                                                                                        |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | 1.2.13                | v1.15                     | Supported EulerOS 2.10.                                                                                |
   |                       |                           |                                                                                                        |
   |                       | v1.17                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.19                     |                                                                                                        |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | 1.1.11                | v1.15                     | -  Supported security hardening.                                                                       |
   |                       |                           | -  Supported third-party OBS storage.                                                                  |
   |                       | v1.17                     | -  Switched to the EVS query API with better performance.                                              |
   |                       |                           | -  Disks can be created from snapshots using clone by default.                                         |
   |                       |                           | -  Optimized and enhanced disk status detection and log output for attaching and detaching operations. |
   |                       |                           | -  Improved the reliability of determining authentication expiration.                                  |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
