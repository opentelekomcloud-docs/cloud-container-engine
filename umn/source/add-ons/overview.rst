:original_name: cce_10_0277.html

.. _cce_10_0277:

Overview
========

CCE provides multiple types of add-ons to extend cluster functions and meet feature requirements. You can install add-ons as required.

.. important::

   CCE uses Helm charts to deploy add-ons. To modify or upgrade an add-on, perform operations on the **Add-ons** page or use open add-on management APIs. Do not directly modify resources related to add-ons in the background. Otherwise, add-on exceptions or other unexpected problems may occur.

.. table:: **Table 1** Add-on list

   +---------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Add-on Name                                             | Introduction                                                                                                                                                                                                                                                                                                                    |
   +=========================================================+=================================================================================================================================================================================================================================================================================================================================+
   | :ref:`CoreDNS <cce_10_0129>`                            | CoreDNS is a DNS server that provides domain name resolution for Kubernetes clusters through chain plug-ins.                                                                                                                                                                                                                    |
   +---------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`CCE Container Storage (FlexVolume) <cce_10_0127>` | storage-driver is a FlexVolume driver used to support IaaS storage services such as EVS, SFS, and OBS.                                                                                                                                                                                                                          |
   +---------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`CCE Container Storage (Everest) <cce_10_0066>`    | everest is a cloud native container storage system, which enables clusters of Kubernetes v1.15.6 or later to use cloud storage through the Container Storage Interface (CSI).                                                                                                                                                   |
   +---------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`CCE Node Problem Detector <cce_10_0132>`          | node-problem-detector (npd for short) is an add-on that monitors abnormal events of cluster nodes and connects to a third-party monitoring platform. It is a daemon running on each node. It collects node issues from different daemons and reports them to the API server. The npd add-on can run as a DaemonSet or a daemon. |
   +---------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`CCE Cluster Autoscaler <cce_10_0154>`             | The autoscaler add-on resizes a cluster based on pod scheduling status and resource usage.                                                                                                                                                                                                                                      |
   +---------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Kubernetes Metrics Server <cce_10_0205>`          | metrics-server is an aggregator for monitoring data of core cluster resources.                                                                                                                                                                                                                                                  |
   +---------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`CCE AI Suite (NVIDIA GPU) <cce_10_0141>`          | gpu-device-plugin is a device management add-on that supports GPUs in containers. It supports only NVIDIA drivers.                                                                                                                                                                                                              |
   +---------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Volcano Scheduler <cce_10_0193>`                  | Volcano provides general-purpose, high-performance computing capabilities, such as job scheduling, heterogeneous chip management, and job running management, serving end users through computing frameworks for different industries, such as AI, big data, gene sequencing, and rendering.                                    |
   +---------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Add-on Lifecycle
----------------

An add-on lifecycle involves all the statuses of the add-on from installation to uninstallation.

.. table:: **Table 2** Add-on statuses

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

Related Operations
------------------

You can perform the operations described in :ref:`Table 3 <cce_10_0277__table1619535674020>` on the **Add-ons** page.

.. _cce_10_0277__table1619535674020:

.. table:: **Table 3** Related operations

   +-----------------------+---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
   | Operation             | Description                           | Procedure                                                                                                                          |
   +=======================+=======================================+====================================================================================================================================+
   | Install               | Install a specified add-on.           | #. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Add-ons**. |
   |                       |                                       |                                                                                                                                    |
   |                       |                                       | #. Click **Install** under the target add-on.                                                                                      |
   |                       |                                       |                                                                                                                                    |
   |                       |                                       |    Each add-on has different configuration parameters. For details, see the corresponding chapter.                                 |
   |                       |                                       |                                                                                                                                    |
   |                       |                                       | #. Click **OK**.                                                                                                                   |
   +-----------------------+---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
   | Upgrade               | Upgrade an add-on to the new version. | #. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Add-ons**. |
   |                       |                                       |                                                                                                                                    |
   |                       |                                       | #. If an add-on can be upgraded, the **Upgrade** button is displayed under it.                                                     |
   |                       |                                       |                                                                                                                                    |
   |                       |                                       |    Click **Upgrade**. Each add-on has different configuration parameters. For details, see the corresponding chapter.              |
   |                       |                                       |                                                                                                                                    |
   |                       |                                       | #. Click **OK**.                                                                                                                   |
   +-----------------------+---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
   | Edit                  | Edit add-on parameters.               | #. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Add-ons**. |
   |                       |                                       |                                                                                                                                    |
   |                       |                                       | #. Click **Edit** under the target add-on.                                                                                         |
   |                       |                                       |                                                                                                                                    |
   |                       |                                       |    Each add-on has different configuration parameters. For details, see the corresponding chapter.                                 |
   |                       |                                       |                                                                                                                                    |
   |                       |                                       | #. Click **OK**.                                                                                                                   |
   +-----------------------+---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
   | Uninstall             | Uninstall an add-on from the cluster. | #. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Add-ons**. |
   |                       |                                       |                                                                                                                                    |
   |                       |                                       | #. Click **Uninstall** under the target add-on.                                                                                    |
   |                       |                                       |                                                                                                                                    |
   |                       |                                       | #. In the displayed dialog box, click **Yes**.                                                                                     |
   |                       |                                       |                                                                                                                                    |
   |                       |                                       |    This operation cannot be undone.                                                                                                |
   +-----------------------+---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
