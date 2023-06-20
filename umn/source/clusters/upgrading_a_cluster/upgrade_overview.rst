:original_name: cce_10_0197.html

.. _cce_10_0197:

Upgrade Overview
================

To enable interoperability from one Kubernetes installation to the next, you must upgrade your Kubernetes clusters before the maintenance period ends.

After the latest Kubernetes version is available in CCE, CCE will describe the changes in this version.

You can use the CCE console to upgrade the Kubernetes version of a cluster.

An upgrade flag will be displayed on the cluster card view if there is a new version for the cluster to upgrade.

**How to check:**

Log in to the CCE console and check whether the message "New version available" is displayed in the lower left corner of the cluster. If yes, the cluster can be upgraded. If no, the cluster cannot be upgraded.


.. figure:: /_static/images/en-us_image_0000001568902653.png
   :alt: **Figure 1** Cluster with the upgrade flag

   **Figure 1** Cluster with the upgrade flag

.. _cce_10_0197__section19981121648:

Cluster Upgrade
---------------

The following table describes the target version to which each cluster version can be upgraded, the supported upgrade modes, and upgrade impacts.

.. table:: **Table 1** Cluster upgrade paths and impacts

   +-----------------------+-----------------------+-----------------------+
   | Source Version        | Target Version        | Upgrade Modes         |
   +=======================+=======================+=======================+
   | v1.23                 | v1.25                 | In-place upgrade      |
   +-----------------------+-----------------------+-----------------------+
   | v1.21                 | v1.25                 | In-place upgrade      |
   |                       |                       |                       |
   |                       | v1.23                 |                       |
   +-----------------------+-----------------------+-----------------------+
   | v1.19                 | v1.23                 | In-place upgrade      |
   |                       |                       |                       |
   |                       | v1.21                 |                       |
   +-----------------------+-----------------------+-----------------------+
   | v1.17                 | v1.19                 | In-place upgrade      |
   |                       |                       |                       |
   | v1.15                 |                       |                       |
   +-----------------------+-----------------------+-----------------------+
   | v1.13                 | v1.15                 | Rolling upgrade       |
   |                       |                       |                       |
   |                       |                       | Replace upgrade       |
   +-----------------------+-----------------------+-----------------------+

Upgrade Modes
-------------

The upgrade processes are the same for master nodes. The differences between the upgrade modes of worker nodes are described as follows:

.. table:: **Table 2** Differences between upgrade modes and their advantages and disadvantages

   +----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Upgrade Mode         | Method                                                                                                                                                                                                                                                                                                       | Advantage                                                               | Disadvantage                                                                                                                                                                                                                                |
   +======================+==============================================================================================================================================================================================================================================================================================================+=========================================================================+=============================================================================================================================================================================================================================================+
   | **In-place upgrade** | Kubernetes components, network components, and CCE management components are upgraded on the node. During the upgrade, service pods and networks are not affected. The **SchedulingDisabled** label will be added to all existing nodes. After the upgrade is complete, you can properly use existing nodes. | You do not need to migrate services, ensuring service continuity.       | In-place upgrade does not upgrade the OS of a node. If you want to upgrade the OS, clear the corresponding node data after the node upgrade is complete and reset the node to upgrade the OS to a new version.                              |
   +----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | **Rolling upgrade**  | Only the Kubernetes components and certain network components are upgraded on the node. The **SchedulingDisabled** label will be added to all existing nodes to ensure that the running applications are not affected.                                                                                       | Services are not interrupted.                                           | -  **After the upgrade is complete, you need to manually create nodes and gradually release the old nodes.** The new nodes are billed additionally. After services are migrated to the new nodes, the old nodes can be deleted.             |
   |                      |                                                                                                                                                                                                                                                                                                              |                                                                         |                                                                                                                                                                                                                                             |
   |                      | .. important::                                                                                                                                                                                                                                                                                               |                                                                         | -  After the rolling upgrade is complete, if you want to continue the upgrade to a later version, you need to reset the old nodes first. Otherwise, the pre-upgrade check cannot be passed. Services may be interrupted during the upgrade. |
   |                      |                                                                                                                                                                                                                                                                                                              |                                                                         |                                                                                                                                                                                                                                             |
   |                      |    NOTICE:                                                                                                                                                                                                                                                                                                   |                                                                         |                                                                                                                                                                                                                                             |
   |                      |                                                                                                                                                                                                                                                                                                              |                                                                         |                                                                                                                                                                                                                                             |
   |                      |    -  **After the upgrade is complete, you need to manually create nodes and gradually release the old nodes**, thereby migrating your applications to the new nodes. In this mode, you can control the upgrade process.                                                                                     |                                                                         |                                                                                                                                                                                                                                             |
   +----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | **Replace upgrade**  | The latest worker node image is used to reset the node OS.                                                                                                                                                                                                                                                   | This is the fastest upgrade mode and requires few manual interventions. | Data or configurations on the node will be lost, and services will be interrupted for a period of time.                                                                                                                                     |
   +----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
