:original_name: cce_01_0197.html

.. _cce_01_0197:

Overview
========

To enable interoperability from one Kubernetes installation to the next, you must upgrade your Kubernetes clusters before the maintenance period ends.

After the latest Kubernetes version is available in CCE, CCE will describe the changes in this version.

You can use the CCE console to upgrade the Kubernetes version of a cluster.

An upgrade flag will be displayed on the cluster card view if there is a new version for the cluster to upgrade.

**How to check:**

Choose **Resource Management** > **Clusters** and check whether there is an upgrade flag in the upper right corner of the cluster card view. If yes, the cluster can be upgraded.


.. figure:: /_static/images/en-us_image_0000001190048341.png
   :alt: **Figure 1** Cluster with the upgrade flag

   **Figure 1** Cluster with the upgrade flag

Cluster Upgrade
---------------

The following table describes the target version to which each cluster version can be upgraded, the supported upgrade modes, and upgrade impacts.

.. table:: **Table 1** Cluster upgrade paths and impacts

   +-----------------+---------------------------------------------------+------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Source Version  | Target Version                                    | Upgrade Modes    | Impacts                                                                                                                                                                                                                                                    |
   +=================+===================================================+==================+============================================================================================================================================================================================================================================================+
   | v1.21           | v1.23                                             | In-place upgrade | You need to identify the differences between versions.                                                                                                                                                                                                     |
   +-----------------+---------------------------------------------------+------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | v1.19           | v1.21                                             | In-place upgrade | You need to identify the differences between versions.                                                                                                                                                                                                     |
   +-----------------+---------------------------------------------------+------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | v1.17           | v1.19                                             | In-place upgrade | You need to identify the differences between versions.                                                                                                                                                                                                     |
   |                 |                                                   |                  |                                                                                                                                                                                                                                                            |
   | v1.15           |                                                   |                  |                                                                                                                                                                                                                                                            |
   +-----------------+---------------------------------------------------+------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | v1.13           | v1.15                                             | Rolling upgrade  | -  The **proxy** configuration item in the coredns add-on configuration is not supported and needs to be replaced with **forward**.                                                                                                                        |
   |                 |                                                   |                  | -  The storage add-on is changed from storage-driver to everest.                                                                                                                                                                                           |
   |                 |                                                   | Replace upgrade  |                                                                                                                                                                                                                                                            |
   +-----------------+---------------------------------------------------+------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | v1.11           | 1.15                                              | Replace upgrade  | -  The cluster signature certificate mechanism is changed. As a result, the original cluster certificate becomes invalid. You need to obtain the certificate or kubeconfig file again after the cluster is upgraded.                                       |
   |                 |                                                   |                  | -  RBAC is enabled for clusters of Kubernetes v1.13 by default. Applications need to adapt to RBAC.                                                                                                                                                        |
   | v1.9            |                                                   |                  | -  After the cluster is upgraded from v1.9 to v1.15, kube-dns in the cluster will be replaced with CoreDNS. Before the upgrade, you need to back up the kube-dns configuration. After the upgrade, you need to reconfigure kube-dns in the coredns add-on. |
   +-----------------+---------------------------------------------------+------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | v1.9            | Latest version that can be created on the console | Migration        | You need to identify the differences between versions.                                                                                                                                                                                                     |
   |                 |                                                   |                  |                                                                                                                                                                                                                                                            |
   | v1.7            |                                                   |                  |                                                                                                                                                                                                                                                            |
   +-----------------+---------------------------------------------------+------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Upgrade Modes
-------------

CCE provides the following upgrade modes based on the cluster version and deployment site. The upgrade processes are the same for master nodes. The differences between the upgrade modes of worker nodes are described as follows:

.. table:: **Table 2** Differences between upgrade modes and their advantages and disadvantages

   +----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Upgrade Mode         | Method                                                                                                                                                                                                                                                                                                                                                                                                                                    | Advantage                                                               | Disadvantage                                                                                                                                                                                              |
   +======================+===========================================================================================================================================================================================================================================================================================================================================================================================================================================+=========================================================================+===========================================================================================================================================================================================================+
   | **In-place upgrade** | Kubernetes components, network components, and CCE management components are upgraded on the node. During the upgrade, service pods and networks are not affected. The **SchedulingDisabled** label will be added to all existing nodes. After the upgrade is complete, you can properly use existing nodes.                                                                                                                              | You do not need to migrate services, ensuring service continuity.       | In-place upgrade does not upgrade the OS of a node. If you want to upgrade the OS, clear the corresponding node after the node upgrade is complete and reset the node to upgrade the OS to a new version. |
   +----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | **Rolling upgrade**  | Only the Kubernetes components and certain network components are upgraded on the node. The **SchedulingDisabled** label will be added to all existing nodes to ensure that the running applications are not affected. **After the upgrade is complete, you need to manually create nodes and gradually release the old nodes**, thereby migrating your applications to the new nodes. In this mode, you can control the upgrade process. | Services are not interrupted.                                           | -                                                                                                                                                                                                         |
   +----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | **Replace upgrade**  | The latest worker node image is used to reset the node OS.                                                                                                                                                                                                                                                                                                                                                                                | This is the fastest upgrade mode and requires few manual interventions. | Data or configurations on the node will be lost, and services will be interrupted for a period of time.                                                                                                   |
   +----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_01_0197__section16738338445:

Cluster Upgrade Between Major Versions
--------------------------------------

.. table:: **Table 3** Changelog between minor versions

   +-----------------------+-----------------------+-------------------------------------------------------------------------------------+
   | Source Version        | Target Version        | Description                                                                         |
   +=======================+=======================+=====================================================================================+
   | v1.21                 | v1.23                 | -  Changelog from v1.21 to v1.23                                                    |
   |                       |                       |                                                                                     |
   |                       |                       |    https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.23.md |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------+
   | v1.19                 | v1.21                 | -  Changelog from v1.19 to v1.21                                                    |
   |                       |                       |                                                                                     |
   |                       |                       |    https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.21.md |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------+
   | v1.17                 | v1.19                 | -  Changelog from v1.17 to v1.19                                                    |
   |                       |                       |                                                                                     |
   |                       |                       |    https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.19.md |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------+
   | v1.15                 | v1.17                 | -  Changelog from v1.15 to v1.17                                                    |
   |                       |                       |                                                                                     |
   |                       |                       |    https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.17.md |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------+
   | v1.13                 | v1.15                 | -  Changelog from v1.9 to v1.15                                                     |
   |                       |                       |                                                                                     |
   |                       |                       |    Changelog from v1.13 to v1.15:                                                   |
   |                       |                       |                                                                                     |
   |                       |                       |    https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.15.md |
   |                       |                       |                                                                                     |
   |                       |                       |    Changelog from v1.11 to v1.13:                                                   |
   |                       |                       |                                                                                     |
   |                       |                       |    https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.13.md |
   |                       |                       |                                                                                     |
   |                       |                       |    Changelog from v1.10 to v1.11:                                                   |
   |                       |                       |                                                                                     |
   |                       |                       |    https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.11.md |
   |                       |                       |                                                                                     |
   |                       |                       |    Changelog from v1.9 to v1.10:                                                    |
   |                       |                       |                                                                                     |
   |                       |                       |    https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.10.md |
   |                       |                       |                                                                                     |
   |                       |                       | -  Replacement of cluster kube-dns by core-dns                                      |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------+
   | v1.11                 |                       |                                                                                     |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------+
   | v1.9                  |                       |                                                                                     |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------+
