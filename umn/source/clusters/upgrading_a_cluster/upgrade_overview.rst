:original_name: cce_10_0197.html

.. _cce_10_0197:

Upgrade Overview
================

To enable interoperability from one Kubernetes installation to the next, you must upgrade your Kubernetes clusters before the maintenance period ends.

After the latest Kubernetes version is available in CCE, CCE will describe the changes in this version.

You can use the CCE console to upgrade the Kubernetes version of a cluster.

An upgrade tag will be displayed on the cluster card view if there is a new version for the cluster to upgrade.

**How to check:**

Log in to the CCE console and check whether the message "New version available" is displayed for the cluster. If yes, the cluster can be upgraded. View the release notes for the latest version. For details, see :ref:`Release Notes for CCE Cluster Versions <cce_10_0405>`. If no such a message is displayed, the cluster is of the latest version.

Cluster Upgrade Process
-----------------------

The cluster upgrade process involves pre-upgrade check, backup, upgrade, and post-upgrade verification.


.. figure:: /_static/images/en-us_image_0000001750950508.png
   :alt: **Figure 1** Process of upgrading a cluster

   **Figure 1** Process of upgrading a cluster

After determining the target version of the cluster, read the :ref:`precautions <cce_10_0302__section16520163082115>` carefully and prevent function incompatibility during the upgrade.

#. **Pre-upgrade check**

   Before a cluster upgrade, CCE checks the compatibility of nodes, add-ons, and workloads in the cluster to reduce the probability of upgrade failures to the best extend. If any exception is detected, rectify the fault as prompted on the console.

#. **Backup**

   Cluster data is backed up before an upgrade by default. You can also back up the entire master nodes as needed.

#. **Upgrade**

   During the upgrade, configure upgrade parameters, such as the step for add-on upgrade or node rolling upgrade. After the upgrade parameters are configured, the add-ons and nodes will be upgraded one by one.

#. **Post-upgrade verification**

   After the upgrade, manually check services and ensure that services are not interrupted by the upgrade.

Cluster Upgrade
---------------

The following table describes the target version to which each cluster version can be upgraded and the supported upgrade modes.

.. table:: **Table 1** Cluster upgrade

   +-----------------------+-----------------------+-----------------------+
   | Source Version        | Target Version        | Upgrade Mode          |
   +=======================+=======================+=======================+
   | 1.25                  | v1.27                 | In-place upgrade      |
   +-----------------------+-----------------------+-----------------------+
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
   +-----------------------+-----------------------+-----------------------+
   | v1.15                 | v1.19                 | In-place upgrade      |
   +-----------------------+-----------------------+-----------------------+

Upgrade Mode
------------

The following table lists the advantages and disadvantages.

.. table:: **Table 2** Advantages and disadvantages

   +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Upgrade Mode     | Method                                                                                                                                                                                                                                                                                                       | Advantage                                                         | Disadvantage                                                                                                                                                                                                   |
   +==================+==============================================================================================================================================================================================================================================================================================================+===================================================================+================================================================================================================================================================================================================+
   | In-place upgrade | Kubernetes components, network components, and CCE management components are upgraded on the node. During the upgrade, service pods and networks are not affected. The **SchedulingDisabled** label will be added to all existing nodes. After the upgrade is complete, you can properly use existing nodes. | You do not need to migrate services, ensuring service continuity. | In-place upgrade does not upgrade the OS of a node. If you want to upgrade the OS, clear the corresponding node data after the node upgrade is complete and reset the node to upgrade the OS to a new version. |
   +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
