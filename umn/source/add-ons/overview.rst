:original_name: cce_10_0277.html

.. _cce_10_0277:

Overview
========

CCE provides multiple types of add-ons to extend cluster functions and meet feature requirements. You can install add-ons as required.

.. important::

   CCE uses Helm charts to deploy add-ons. To modify or upgrade an add-on, perform operations on the **Add-ons** page or use open add-on management APIs. Do not directly modify resources related to add-ons in the background. Otherwise, add-on exceptions or other unexpected problems may occur.

Scheduling and Elasticity Add-ons
---------------------------------

+---------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Add-on Name                                 | Description                                                                                                                                                                                                                                                                                 |
+=============================================+=============================================================================================================================================================================================================================================================================================+
| :ref:`Volcano Scheduler <cce_10_0193>`      | This add-on is a scheduler for general-purpose, high-performance computing such as job scheduling, heterogeneous chip management, and job running management, serving end users through computing frameworks for different industries such as AI, big data, gene sequencing, and rendering. |
+---------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| :ref:`CCE Cluster Autoscaler <cce_10_0154>` | This add-on resizes a cluster based on pod scheduling status and resource usage.                                                                                                                                                                                                            |
+---------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| :ref:`CCE Advanced HPA <cce_10_0240>`       | This add-on is developed by CCE. It can be used to flexibly scale in or out Deployments based on metrics such as CPU usage and memory usage.                                                                                                                                                |
+---------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Cloud Native Observability Add-ons
----------------------------------

+------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Add-on Name                                          | Description                                                                                                                                                                                                                                                             |
+======================================================+=========================================================================================================================================================================================================================================================================+
| :ref:`Cloud Native Cluster Monitoring <cce_10_0406>` | This add-on uses Prometheus-operator and Prometheus to provide easy-to-use, end-to-end Kubernetes cluster monitoring.                                                                                                                                                   |
+------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| :ref:`CCE Node Problem Detector <cce_10_0132>`       | This add-on monitors abnormal events of cluster nodes and connects to a third-party monitoring platform. It is a daemon running on each node. It collects node issues from different daemons and reports them to the API server. It can run as a DaemonSet or a daemon. |
+------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| :ref:`Kubernetes Metrics Server <cce_10_0205>`       | This add-on is an aggregator for monitoring data of core cluster resources.                                                                                                                                                                                             |
+------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Cloud Native Heterogeneous Computing Add-ons
--------------------------------------------

+------------------------------------------------+-------------------------------------------------------------------------------------------------------------+
| Add-on Name                                    | Description                                                                                                 |
+================================================+=============================================================================================================+
| :ref:`CCE AI Suite (NVIDIA GPU) <cce_10_0141>` | NVIDIA GPU is a device management add-on that supports GPUs in containers. It supports only NVIDIA drivers. |
+------------------------------------------------+-------------------------------------------------------------------------------------------------------------+

Container Network Add-ons
-------------------------

+------------------------------+--------------------------------------------------------------------------------------------------------------+
| Add-on Name                  | Description                                                                                                  |
+==============================+==============================================================================================================+
| :ref:`CoreDNS <cce_10_0129>` | CoreDNS is a DNS server that provides domain name resolution for Kubernetes clusters through a chain add-on. |
+------------------------------+--------------------------------------------------------------------------------------------------------------+

Container Storage Add-on
------------------------

+------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Add-on Name                                          | Description                                                                                                                                                                       |
+======================================================+===================================================================================================================================================================================+
| :ref:`CCE Container Storage (Everest) <cce_10_0066>` | This add-on is a cloud native container storage system, which enables clusters of Kubernetes v1.15.6 or later to use cloud storage through the Container Storage Interface (CSI). |
+------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Add-on Lifecycle
----------------

An add-on lifecycle involves all the statuses of the add-on from installation to uninstallation.

.. table:: **Table 1** Add-on statuses

   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Status                | Attribute             | Description                                                                                                                                                                  |
   +=======================+=======================+==============================================================================================================================================================================+
   | Running               | Stable state          | The add-on is running properly, all add-on instances are deployed properly, and the add-on can be used properly.                                                             |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Partially ready       | Stable state          | The add-on is running properly, but some add-on instances are not properly deployed. In this state, the add-on functions may be unavailable.                                 |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Unavailable           | Stable state          | The add-on malfunctions, and all add-on instances are not properly deployed.                                                                                                 |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Installing            | Intermediate state    | The add-on is being deployed.                                                                                                                                                |
   |                       |                       |                                                                                                                                                                              |
   |                       |                       | If all instances cannot be scheduled due to incorrect add-on configuration or insufficient resources, the system sets the add-on status to **Unavailable** 10 minutes later. |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Installation failed   | Stable state          | Install add-on failed. Uninstall it and try again.                                                                                                                           |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Upgrading             | Intermediate state    | The add-on is being upgraded.                                                                                                                                                |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Upgrade failed        | Stable state          | Upgrade add-on failed. Upgrade it again, or uninstall it and try again.                                                                                                      |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Rolling back          | Intermediate state    | The add-on is rolling back.                                                                                                                                                  |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Rollback failed       | Stable state          | The add-on rollback failed. Retry the rollback, or uninstall it and try again.                                                                                               |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Deleting              | Intermediate state    | The add-on is being deleted.                                                                                                                                                 |
   |                       |                       |                                                                                                                                                                              |
   |                       |                       | If this state stays for a long time, an exception occurred.                                                                                                                  |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Deletion failed       | Stable state          | Delete add-on failed. Try again.                                                                                                                                             |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Unknown               | Stable state          | No add-on chart found.                                                                                                                                                       |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. note::

   When an add-on is in an intermediate state such as **Installing** or **Deleting**, you are not allowed to edit or uninstall the add-on.

   If the add-on status is unknown and the returned **status.Reason** is "don't install the addon in this cluster", the secret associated with the Helm release of the add-on in the cluster is typically deleted by mistake. In this case, uninstall the add-on and reinstall it with the same configurations.

Related Operations
------------------

You can perform the operations listed in :ref:`Table 2 <cce_10_0277__table1619535674020>` on the **Add-ons** page.

.. _cce_10_0277__table1619535674020:

.. table:: **Table 2** Related operations

   +-----------------------+----------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
   | Operation             | Description                                                                                                                      | Procedure                                                                                                                          |
   +=======================+==================================================================================================================================+====================================================================================================================================+
   | Install               | Install a specified add-on.                                                                                                      | #. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Add-ons**. |
   |                       |                                                                                                                                  |                                                                                                                                    |
   |                       |                                                                                                                                  | #. Click **Install** under the target add-on.                                                                                      |
   |                       |                                                                                                                                  |                                                                                                                                    |
   |                       |                                                                                                                                  |    Each add-on has different configuration parameters. For details, see the corresponding chapter.                                 |
   |                       |                                                                                                                                  |                                                                                                                                    |
   |                       |                                                                                                                                  | #. Click **OK**.                                                                                                                   |
   +-----------------------+----------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
   | Upgrade               | Upgrade an add-on to the new version.                                                                                            | #. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Add-ons**. |
   |                       |                                                                                                                                  |                                                                                                                                    |
   |                       |                                                                                                                                  | #. If an add-on can be upgraded, the **Upgrade** button is displayed under it.                                                     |
   |                       |                                                                                                                                  |                                                                                                                                    |
   |                       |                                                                                                                                  |    Click **Upgrade**. Each add-on has different configuration parameters. For details, see the corresponding chapter.              |
   |                       |                                                                                                                                  |                                                                                                                                    |
   |                       |                                                                                                                                  | #. Click **OK**.                                                                                                                   |
   +-----------------------+----------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
   | Edit                  | Edit add-on parameters.                                                                                                          | #. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Add-ons**. |
   |                       |                                                                                                                                  |                                                                                                                                    |
   |                       |                                                                                                                                  | #. Click **Edit** under the target add-on.                                                                                         |
   |                       |                                                                                                                                  |                                                                                                                                    |
   |                       |                                                                                                                                  |    Each add-on has different configuration parameters. For details, see the corresponding chapter.                                 |
   |                       |                                                                                                                                  |                                                                                                                                    |
   |                       |                                                                                                                                  | #. Click **OK**.                                                                                                                   |
   +-----------------------+----------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
   | Uninstall             | Uninstall an add-on from the cluster.                                                                                            | #. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Add-ons**. |
   |                       |                                                                                                                                  |                                                                                                                                    |
   |                       |                                                                                                                                  | #. Click **Uninstall** under the target add-on.                                                                                    |
   |                       |                                                                                                                                  |                                                                                                                                    |
   |                       |                                                                                                                                  | #. In the displayed dialog box, click **Yes**.                                                                                     |
   |                       |                                                                                                                                  |                                                                                                                                    |
   |                       |                                                                                                                                  |    This operation cannot be undone.                                                                                                |
   +-----------------------+----------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
   | Roll back             | Roll back an add-on to the source version.                                                                                       | #. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Add-ons**. |
   |                       |                                                                                                                                  |                                                                                                                                    |
   |                       | .. note::                                                                                                                        | #. If an add-on can be rolled back, the **Roll Back** button is displayed under it.                                                |
   |                       |                                                                                                                                  |                                                                                                                                    |
   |                       |    -  This function is used to roll back an upgraded add-on to the source version, not to undo the editing of add-on parameters. |    Click **Roll Back**.                                                                                                            |
   |                       |    -  An add-on cannot be rolled back repeatedly.                                                                                |                                                                                                                                    |
   |                       |                                                                                                                                  | #. In the displayed dialog box, click **Yes**.                                                                                     |
   +-----------------------+----------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+

.. note::

   Add-on rollback is supported in certain add-on versions.

   -  CoreDNS: 1.25.11 and later versions
   -  Everest: 2.1.19 and later versions
   -  Autoscaler:

      -  v1.21 clusters: v1.21.22 and later versions
      -  v1.23 clusters: v1.23.24 and later versions
      -  v1.25 clusters: v1.25.14 and later versions

   -  kube-prometheus-stack: v3.7.2 and later versions
   -  Volcano: 1.11.4 and later versions
   -  NPD: 1.18.22 and later versions
