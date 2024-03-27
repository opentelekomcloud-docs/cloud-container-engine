:original_name: cce_10_0066.html

.. _cce_10_0066:

CCE Container Storage (Everest)
===============================

Introduction
------------

everest is a cloud native container storage system, which enables clusters of Kubernetes v1.15.6 or later to access cloud storage services through the Container Storage Interface.

**everest is a system resource add-on. It is installed by default when a cluster of Kubernetes v1.15 or later is created.**

Constraints
-----------

-  If your cluster is upgraded from v1.13 to v1.15, :ref:`storage-driver <cce_10_0127>` is replaced by everest (v1.1.6 or later) for container storage. The takeover does not affect the original storage functions.
-  In version 1.2.0 of the everest add-on, **key authentication** is optimized when OBS is used. After the everest add-on is upgraded from a version earlier than 1.2.0, restart all workloads that use OBS in the cluster. Otherwise, workloads may not be able to use OBS.
-  By default, this add-on is installed in **clusters of v1.15 and later**. For clusters of v1.13 and earlier, the :ref:`storage-driver <cce_10_0127>` add-on is installed by default.

Installing the Add-on
---------------------

This add-on has been installed by default. If it is uninstalled due to some reasons, you can reinstall it by performing the following steps:

#. Log in to the CCE console and click the cluster name to access the cluster console. Choose **Add-ons** in the navigation pane, locate **CCE Container Storage (Everest)** on the right, and click **Install**.

#. On the **Install Add-on** page, configure the specifications.

   .. table:: **Table 1** Add-on configuration

      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                     |
      +===================================+=================================================================================================================================================================================================================+
      | Pods                              | Number of pods for the add-on.                                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                 |
      |                                   | High availability is not possible with a single pod. If an error occurs on the node where the add-on instance runs, the add-on will fail.                                                                       |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Multi-AZ                          | -  **Preferred**: Deployment pods of the add-on will be preferentially scheduled to nodes in different AZs. If all the nodes in the cluster are deployed in the same AZ, the pods will be scheduled to that AZ. |
      |                                   | -  **Required**: Deployment pods of the add-on will be forcibly scheduled to nodes in different AZs. If there are fewer AZs than pods, the extra pods will fail to run.                                         |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Containers                        | The everest add-on contains the everest-csi-controller and everest-csi-driver components. For details, see :ref:`Components <cce_10_0066__section0377457163618>`.                                               |
      |                                   |                                                                                                                                                                                                                 |
      |                                   | After you select the add-on specification, the requested CPU and memory of the add-on component are displayed. For details, see :ref:`Table 2 <cce_10_0066__table10463555206>`.                                 |
      |                                   |                                                                                                                                                                                                                 |
      |                                   | In non-typical scenarios, the formulas for estimating the limit values are as follows:                                                                                                                          |
      |                                   |                                                                                                                                                                                                                 |
      |                                   | -  everest-csi-controller                                                                                                                                                                                       |
      |                                   |                                                                                                                                                                                                                 |
      |                                   |    -  CPU limit: 250m for 200 or fewer nodes, 350m for 1000 nodes, and 500m for 2000 nodes                                                                                                                      |
      |                                   |    -  Memory limit = (200 MiB + Number of nodes x 1 MiB + Number of PVCs x 0.2 MiB) x 1.2                                                                                                                       |
      |                                   |                                                                                                                                                                                                                 |
      |                                   | -  everest-csi-driver                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                 |
      |                                   |    -  CPU limit: 300 m for 200 or fewer nodes, 500 m for 1000 nodes, and 800 m for 2000 nodes                                                                                                                   |
      |                                   |    -  Memory limit: 300 MiB for 200 or fewer nodes, 600 MiB for 1000 nodes, and 900 MiB for 2000 nodes                                                                                                          |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0066__table10463555206:

   .. table:: **Table 2** Recommended configuration limits in typical scenarios

      +------------------------+----------+------------------+-----------------------------------------------------------+--------------------------------------------------------------+-----------------------------------------------------------+--------------------------------------------------------------+
      | Configuration Scenario |          |                  | everest-csi-controller                                    |                                                              | everest-csi-driver                                        |                                                              |
      +========================+==========+==================+===========================================================+==============================================================+===========================================================+==============================================================+
      | Nodes                  | PVs/PVCs | Add-on Instances | CPU (The limit value is the same as the requested value.) | Memory (The limit value is the same as the requested value.) | CPU (The limit value is the same as the requested value.) | Memory (The limit value is the same as the requested value.) |
      +------------------------+----------+------------------+-----------------------------------------------------------+--------------------------------------------------------------+-----------------------------------------------------------+--------------------------------------------------------------+
      | 50                     | 1000     | 2                | 250m                                                      | 600Mi                                                        | 300m                                                      | 300Mi                                                        |
      +------------------------+----------+------------------+-----------------------------------------------------------+--------------------------------------------------------------+-----------------------------------------------------------+--------------------------------------------------------------+
      | 200                    | 1000     | 2                | 250m                                                      | 1Gi                                                          | 300m                                                      | 300Mi                                                        |
      +------------------------+----------+------------------+-----------------------------------------------------------+--------------------------------------------------------------+-----------------------------------------------------------+--------------------------------------------------------------+
      | 1000                   | 1000     | 2                | 350m                                                      | 2Gi                                                          | 500m                                                      | 600Mi                                                        |
      +------------------------+----------+------------------+-----------------------------------------------------------+--------------------------------------------------------------+-----------------------------------------------------------+--------------------------------------------------------------+
      | 1000                   | 5000     | 2                | 450m                                                      | 3Gi                                                          | 500m                                                      | 600Mi                                                        |
      +------------------------+----------+------------------+-----------------------------------------------------------+--------------------------------------------------------------+-----------------------------------------------------------+--------------------------------------------------------------+
      | 2000                   | 5000     | 2                | 550m                                                      | 4Gi                                                          | 800m                                                      | 900Mi                                                        |
      +------------------------+----------+------------------+-----------------------------------------------------------+--------------------------------------------------------------+-----------------------------------------------------------+--------------------------------------------------------------+
      | 2000                   | 10,000   | 2                | 650m                                                      | 5Gi                                                          | 800m                                                      | 900Mi                                                        |
      +------------------------+----------+------------------+-----------------------------------------------------------+--------------------------------------------------------------+-----------------------------------------------------------+--------------------------------------------------------------+

#. Configure the add-on parameters.

   .. table:: **Table 3** Add-on parameters

      +------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                          | Description                                                                                                                                                                                                                       |
      +====================================+===================================================================================================================================================================================================================================+
      | csi_attacher_worker_threads        | Number of worker nodes that can concurrently attach EVS volumes. The default value is **60**.                                                                                                                                     |
      +------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | csi_attacher_detach_worker_threads | Number of worker nodes that can concurrently detach EVS volumes. The default value is **60**.                                                                                                                                     |
      +------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | volume_attaching_flow_ctrl         | Maximum number of EVS volumes that can be attached by the everest add-on within 1 minute. The default value is **0**, indicating that the performance of attaching EVS volumes is determined by the underlying storage resources. |
      +------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | cluster_id                         | Cluster ID                                                                                                                                                                                                                        |
      +------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | default_vpc_id                     | ID of the VPC to which the cluster belongs                                                                                                                                                                                        |
      +------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | disable_auto_mount_secret          | Whether the default AK/SK can be used when an object bucket or parallel file system is mounted. The default value is **false**.                                                                                                   |
      +------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | enable_node_attacher               | Whether to enable the attacher on the agent to process the `VolumeAttachment <https://kubernetes.io/docs/reference/kubernetes-api/config-and-storage-resources/volume-attachment-v1/>`__.                                         |
      +------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | flow_control                       | This field is left blank by default. You do not need to configure this parameter.                                                                                                                                                 |
      +------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | over_subscription                  | Overcommitment ratio of the local storage pool (**local_storage**). The default value is **80**. If the size of the local storage pool is 100 GB, it can be overcommitted to 180 GB.                                              |
      +------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | project_id                         | ID of the project to which a cluster belongs                                                                                                                                                                                      |
      +------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. note::

      In everest 1.2.26 or later, the performance of attaching a large number of EVS volumes has been optimized. The following parameters can be configured:

      -  csi_attacher_worker_threads
      -  csi_attacher_detach_worker_threads
      -  volume_attaching_flow_ctrl

      The preceding parameters are associated with each other and are constrained by the underlying storage resources in the region where the cluster is located. To attach a large number of volumes (more than 500 EVS volumes per minute), contact customer service and configure the parameters under their guidance to prevent the everest add-on from running abnormally due to improper parameter settings.

#. Click **Install**.

.. _cce_10_0066__section0377457163618:

Components
----------

.. table:: **Table 4** everest components

   +------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
   | Container Component    | Description                                                                                                                                                                                                                                                                                                                                                                    | Resource Type |
   +========================+================================================================================================================================================================================================================================================================================================================================================================================+===============+
   | everest-csi-controller | Used to create, delete, snapshot, expand, attach, and detach storage volumes. If the cluster version is 1.19 or later and the add-on version is 1.2.\ *x*, the pod of the everest-csi-controller component also has an everest-localvolume-manager container by default. This container manages the creation of LVM storage pools and local PVs on the node.                   | Deployment    |
   +------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
   | everest-csi-driver     | Used to mount and unmount PVs and resize file systems. If the add-on version is 1.2.\ *x* and the region where the cluster is located supports node-attacher, the pod of the everest-csi-driver component also contains an everest-node-attacher container. This container is responsible for distributed EVS attaching. This configuration item is available in some regions. | DaemonSet     |
   +------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
