:original_name: cce_01_0066.html

.. _cce_01_0066:

everest (System Resource Add-on, Mandatory)
===========================================

Introduction
------------

Everest is a cloud-native container storage system. Based on Container Storage Interface (CSI), clusters of Kubernetes v1.15 or later can interconnect with cloud storage services such as EVS, OBS, SFS, and SFS Turbo.

**everest is a system resource add-on. It is installed by default when a cluster of Kubernetes v1.15 or later is created.**

Notes and Constraints
---------------------

-  If your cluster is upgraded from v1.13 to v1.15, :ref:`storage-driver <cce_01_0127>` is replaced by everest (v1.1.6 or later) for container storage. The takeover does not affect the original storage functions. For details about CSI and FlexVolume, see :ref:`Differences Between CSI and FlexVolume Plug-ins <cce_01_0306__section86752053123513>`.
-  In version 1.2.0 of the everest add-on, **key authentication** is optimized when OBS is used. After the everest add-on is upgraded from a version earlier than 1.2.0, you need to restart all workloads that use OBS in the cluster. Otherwise, workloads may not be able to use OBS.
-  By default, this add-on is installed in **clusters of v1.15 and later**. For clusters of v1.13 and earlier, the :ref:`storage-driver <cce_01_0127>` add-on is installed by default.

Installing the Add-on
---------------------

This add-on has been installed by default. If it is uninstalled due to some reasons, you can reinstall it by performing the following steps:

#. Log in to the CCE console. In the navigation pane, choose **Add-ons**. On the **Add-on Marketplace** tab page, click **Install Add-on** under **everest**.

#. On the **Install Add-on** page, select the cluster and the add-on version, and click **Next: Configuration**.

#. Select **Single** or **HA** for **Add-on Specifications**, and click **Install**.

   After the add-on is installed, click **Go Back to Previous Page**. On the **Add-on Instance** tab page, select the corresponding cluster to view the running instance. This indicates that the add-on has been installed on each node in the cluster.

Upgrading the Add-on
--------------------

#. Log in to the CCE console. In the navigation pane, choose **Add-ons**. On the **Add-on Instance** tab page, click **Upgrade** under **everest**.

   .. note::

      -  If the **Upgrade** button is unavailable, the current add-on is already up-to-date and no upgrade is required.
      -  When the upgrade is complete, the original everest version on cluster nodes will be replaced by the latest version.

#. On the **Basic Information** page, select the add-on version and click **Next**.
#. Select **Single** or **HA** for **Add-on Specifications**, and click **Upgrade**.

Uninstalling the Add-on
-----------------------

#. Log in to the CCE console. In the navigation pane, choose **Add-ons**. On the **Add-on Instance** tab page, click **Uninstall** under **everest**.
#. In the dialog box displayed, click **Yes** to uninstall the add-on.
