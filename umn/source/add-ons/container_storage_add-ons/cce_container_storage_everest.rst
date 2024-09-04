:original_name: cce_10_0066.html

.. _cce_10_0066:

CCE Container Storage (Everest)
===============================

Introduction
------------

Everest is a cloud native container storage system, which enables clusters of Kubernetes v1.15.6 or later to access cloud storage services through the CSI.

**Everest is a system resource add-on. It is installed by default when a cluster of Kubernetes v1.15 or later is created.**

Notes and Constraints
---------------------

-  If your cluster is upgraded from v1.13 to v1.15, :ref:`storage-driver <cce_10_0127>` will be replaced by Everest (v1.1.6 or later) for container storage. The takeover does not affect the original storage functions.
-  In version 1.2.0 of the Everest add-on, **key authentication** is optimized when OBS is used. After the Everest add-on is upgraded from a version earlier than 1.2.0, restart all workloads that use OBS in the cluster. Otherwise, workloads may not be able to use OBS.
-  By default, this add-on is installed in **clusters of v1.15 and later**. For clusters of v1.13 and earlier, the :ref:`storage-driver <cce_10_0127>` add-on is installed by default.

Installing the Add-on
---------------------

This add-on has been installed by default. If it is uninstalled due to some reasons, you can reinstall it by performing the following steps:

#. Log in to the CCE console and click the cluster name to access the cluster console. Click **Add-ons** in the navigation pane, locate **CCE Container Storage (Everest)** on the right, and click **Install**.

#. On the **Install Add-on** page, configure the specifications.

   .. table:: **Table 1** Add-on configuration

      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                         |
      +===================================+=====================================================================================================================================================================================================================================================================================================================================================+
      | Pods                              | Number of pods for the add-on.                                                                                                                                                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                                                                                                                                     |
      |                                   | High availability is not possible with a single pod. If an error occurs on the node where the add-on instance runs, the add-on will fail.                                                                                                                                                                                                           |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Containers                        | The Everest add-on contains the Everest-csi-controller and everest-csi-driver components. For details, see :ref:`Components <cce_10_0066__section0377457163618>`.                                                                                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                                                                                     |
      |                                   | The add-on component specifications can be customized based on your requirements. Retain the default requested CPU and memory values of the add-on components. The limit values can be adjusted based on the number of cluster nodes and PVCs. For details about the configuration suggestions, see :ref:`Table 2 <cce_10_0066__table10463555206>`. |
      |                                   |                                                                                                                                                                                                                                                                                                                                                     |
      |                                   | In non-typical scenarios, the formulas for estimating the limit values are as follows:                                                                                                                                                                                                                                                              |
      |                                   |                                                                                                                                                                                                                                                                                                                                                     |
      |                                   | -  everest-csi-controller                                                                                                                                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                                                                                                     |
      |                                   |    -  CPU limit: 250m for 200 or fewer nodes, 350m for 1000 nodes, and 500m for 2000 nodes                                                                                                                                                                                                                                                          |
      |                                   |    -  Memory limit = (200 Mi + Number of nodes x 1 Mi + Number of PVCs x 0.2 Mi) x 1.2                                                                                                                                                                                                                                                              |
      |                                   |                                                                                                                                                                                                                                                                                                                                                     |
      |                                   | -  everest-csi-driver                                                                                                                                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                                                                                                     |
      |                                   |    -  CPU limit: 300m for 200 or fewer nodes, 500m for 1000 nodes, and 800m for 2000 nodes                                                                                                                                                                                                                                                          |
      |                                   |    -  Memory limit: 300 Mi for 200 or fewer nodes, 600 Mi for 1000 nodes, and 900 Mi for 2000 nodes                                                                                                                                                                                                                                                 |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0066__table10463555206:

   .. table:: **Table 2** Recommended configuration limits in typical scenarios

      +------------------------+----------+------------------+---------------------------+----------------------------+---------------------------+----------------------------+
      | Configuration Scenario |          |                  | everest-csi-controller    |                            | everest-csi-driver        |                            |
      +========================+==========+==================+===========================+============================+===========================+============================+
      | Nodes                  | PVs/PVCs | Add-on Instances | vCPUs (Limit = Requested) | Memory (Limit = Requested) | vCPUs (Limit = Requested) | Memory (Limit = Requested) |
      +------------------------+----------+------------------+---------------------------+----------------------------+---------------------------+----------------------------+
      | 50                     | 1000     | 2                | 250m                      | 600 MiB                    | 300m                      | 300 MiB                    |
      +------------------------+----------+------------------+---------------------------+----------------------------+---------------------------+----------------------------+
      | 200                    | 1000     | 2                | 250m                      | 1 GiB                      | 300m                      | 300 MiB                    |
      +------------------------+----------+------------------+---------------------------+----------------------------+---------------------------+----------------------------+
      | 1000                   | 1000     | 2                | 350m                      | 2 GiB                      | 500m                      | 600 MiB                    |
      +------------------------+----------+------------------+---------------------------+----------------------------+---------------------------+----------------------------+
      | 1000                   | 5000     | 2                | 450m                      | 3 GiB                      | 500m                      | 600 MiB                    |
      +------------------------+----------+------------------+---------------------------+----------------------------+---------------------------+----------------------------+
      | 2000                   | 5000     | 2                | 550m                      | 4 GiB                      | 800m                      | 900 MiB                    |
      +------------------------+----------+------------------+---------------------------+----------------------------+---------------------------+----------------------------+
      | 2000                   | 10000    | 2                | 650m                      | 5 GiB                      | 800m                      | 900 MiB                    |
      +------------------------+----------+------------------+---------------------------+----------------------------+---------------------------+----------------------------+

#. Configure the add-on parameters.

   .. table:: **Table 3** Everest parameters

      +------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                      |
      +====================================+==================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | csi_attacher_worker_threads        | Number of worker nodes that can be concurrently processed by Everest for attaching EVS volumes. The default value is **60**.                                                                                                                                                                                                                                                                                                                     |
      +------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | csi_attacher_detach_worker_threads | Number of worker nodes that can be concurrently processed by Everest for detaching EVS volumes. The default value is **60**.                                                                                                                                                                                                                                                                                                                     |
      +------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | volume_attaching_flow_ctrl         | Maximum number of EVS volumes that can be attached by the Everest add-on within 1 minute. The default value is **0**, indicating that the performance of attaching EVS volumes is determined by the underlying storage resources.                                                                                                                                                                                                                |
      +------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | cluster_id                         | Cluster ID                                                                                                                                                                                                                                                                                                                                                                                                                                       |
      +------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | default_vpc_id                     | ID of the VPC to which the cluster belongs                                                                                                                                                                                                                                                                                                                                                                                                       |
      +------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | disable_auto_mount_secret          | Whether the default AK/SK can be used when an object bucket or parallel file system is mounted. The default value is **false**.                                                                                                                                                                                                                                                                                                                  |
      +------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | enable_node_attacher               | Whether to enable the attacher on the agent to process the `VolumeAttachment <https://kubernetes.io/docs/reference/kubernetes-api/config-and-storage-resources/volume-attachment-v1/>`__.                                                                                                                                                                                                                                                        |
      +------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | flow_control                       | This field is left blank by default. You do not need to configure this parameter.                                                                                                                                                                                                                                                                                                                                                                |
      +------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | number_of_reserved_disks           | Number of disks on the node reserved for custom use. This parameter is supported when the add-on version is 2.3.11 or later.                                                                                                                                                                                                                                                                                                                     |
      |                                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                    | Assume that a maximum of 20 EVS disks can be attached to a node, and the value of this parameter is set to **6**. Then 14 (20-6) disks can be attached to this node when the system schedules the EVS disk attachment workloads. The reserved six disks include one system disk and one data disk that has been attached to the node. You can attach four EVS disks to this node as additional data disks or raw disks for a local storage pool. |
      +------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | over_subscription                  | Overcommitment ratio of the local storage pool (**local_storage**). The default value is **80**. If the size of the local storage pool is 100 GB, it can be overcommitted to 180 GB.                                                                                                                                                                                                                                                             |
      +------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | project_id                         | ID of the project to which a cluster belongs                                                                                                                                                                                                                                                                                                                                                                                                     |
      +------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. note::

      In Everest 1.2.26 or later, the performance of attaching a large number of EVS volumes has been optimized. The following parameters can be configured:

      -  csi_attacher_worker_threads
      -  csi_attacher_detach_worker_threads
      -  volume_attaching_flow_ctrl

      The preceding parameters are associated with each other and are constrained by the underlying storage resources in the region where the cluster is located. To attach a large number of volumes (more than 500 EVS volumes per minute), contact administrator and configure the parameters under their guidance to prevent the Everest add-on from running abnormally due to improper parameter settings.

#. Configure scheduling policies for the add-on.

   .. note::

      -  Scheduling policies do not take effect on add-on instances of the DaemonSet type.
      -  When configuring multi-AZ deployment or node affinity, ensure that there are nodes meeting the scheduling policy and that resources are sufficient in the cluster. Otherwise, the add-on cannot run.

   .. table:: **Table 4** Configurations for add-on scheduling

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                    |
      +===================================+================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | Multi AZ                          | -  **Preferred**: Deployment pods of the add-on will be preferentially scheduled to nodes in different AZs. If all the nodes in the cluster are deployed in the same AZ, the pods will be scheduled to that AZ.                                                                                                                                                                                                                                |
      |                                   | -  **Equivalent mode**: Deployment pods of the add-on are evenly scheduled to the nodes in the cluster in each AZ. If a new AZ is added, you are advised to increase add-on pods for cross-AZ HA deployment. With the Equivalent multi-AZ deployment, the difference between the number of add-on pods in different AZs will be less than or equal to 1. If resources in one of the AZs are insufficient, pods cannot be scheduled to that AZ. |
      |                                   | -  **Required**: Deployment pods of the add-on will be forcibly scheduled to nodes in different AZs. If there are fewer AZs than pods, the extra pods will fail to run.                                                                                                                                                                                                                                                                        |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Node Affinity                     | -  **Not configured**: Node affinity is disabled for the add-on.                                                                                                                                                                                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Node Affinity**: Specify the nodes where the add-on is deployed. If you do not specify the nodes, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Specified Node Pool Scheduling**: Specify the node pool where the add-on is deployed. If you do not specify the node pool, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                                                          |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Custom Policies**: Enter the labels of the nodes where the add-on is to be deployed for more flexible scheduling policies. If you do not specify node labels, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                       |
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

.. _cce_10_0066__section0377457163618:

Components
----------

.. table:: **Table 5** Add-on components

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

.. table:: **Table 6** Key metrics

   +------------------------------------------+-------------+-------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------+
   | Metric                                   | Type        | Description                                                             | Label                                                                                                      | Example                                                                                            |
   +==========================================+=============+=========================================================================+============================================================================================================+====================================================================================================+
   | everest_action_result_total              | Counter     | Invoking of different functions                                         | action: indicates different functions. For details, see :ref:`Table 7 <cce_10_0066__table195218413174>`.   | everest_action_result_total{action="create_snapshot:disk.csi.everest.io",result="success"} 2       |
   |                                          |             |                                                                         |                                                                                                            |                                                                                                    |
   |                                          |             |                                                                         | result: indicates that the invoking is successful or fails.                                                |                                                                                                    |
   +------------------------------------------+-------------+-------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------+
   | everest_function_duration_seconds_bucket | Histogram   | Number of times that different functions are executed at different time | function: indicates different functions. For details, see :ref:`Table 7 <cce_10_0066__table195218413174>`. | everest_function_duration_seconds_bucket{function="create_snapshot:disk.csi.everest.io",le="10"} 2 |
   +------------------------------------------+-------------+-------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------+
   | everest_function_duration_seconds_sum    | Histogram   | Total invoking time of different functions                              | function: indicates different functions. For details, see :ref:`Table 7 <cce_10_0066__table195218413174>`. | everest_function_duration_seconds_sum{function="create:disk.csi.everest.io"} 24.381399053          |
   +------------------------------------------+-------------+-------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------+
   | everest_function_duration_seconds_count  | Histogram   | Number of invoking times of different functions                         | function: indicates different functions. For details, see :ref:`Table 7 <cce_10_0066__table195218413174>`. | everest_function_duration_seconds_count{function="attach:disk.csi.everest.io"} 4                   |
   +------------------------------------------+-------------+-------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------+

**action** and **function** specify different CSI drivers and their functions, and are in the format of *{Function}*\ **:**\ *{CSI driver}*. For example, **create:disk.csi.everest.io** specifies that the function is to create a volume and the volume type is EVS disk.

.. _cce_10_0066__table195218413174:

.. table:: **Table 7** Functions

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

Change History
--------------

.. table:: **Table 8** Release history

   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | Add-on Version        | Supported Cluster Version | New Feature                                                                                            |
   +=======================+===========================+========================================================================================================+
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
   | 2.3.14                | v1.21                     | CCE clusters 1.28 are supported.                                                                       |
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
   |                       |                           | -  Adapts the obsfs package to Ubuntu 22.04.                                                           |
   |                       | v1.21                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.23                     |                                                                                                        |
   |                       |                           |                                                                                                        |
   |                       | v1.25                     |                                                                                                        |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
   | 2.1.13                | v1.19                     | Optimized the performance of creating subpath PVCs in batches for SFS Turbo volumes.                   |
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
   | 1.2.70                | v1.15                     | Optimized the performance of creating subpath PVCs in batches for SFS Turbo volumes.                   |
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
   | 1.1.11                | v1.15                     | -  Supports security hardening.                                                                        |
   |                       |                           | -  Supports third-party OBS storage.                                                                   |
   |                       | v1.17                     | -  Switches to the EVS query API with better performance.                                              |
   |                       |                           | -  Uses snapshots to create disks in clone mode by default.                                            |
   |                       |                           | -  Optimizes and enhances disk status detection and log output for attaching and detaching operations. |
   |                       |                           | -  Improves the reliability of determining authentication expiration.                                  |
   +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------+
