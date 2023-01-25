:original_name: cce_01_0127.html

.. _cce_01_0127:

storage-driver (System Resource Add-on, Mandatory)
==================================================

Introduction
------------

storage-driver functions as a standard Kubernetes FlexVolume plug-in to allow containers to use IaaS storage resources. By installing and upgrading storage-driver, you can quickly install and update cloud storage capabilities.

**storage-driver is a system resource add-on. It is installed by default when a cluster of Kubernetes v1.13 or earlier is created.**

Notes and Constraints
---------------------

-  For clusters created in CCE, Kubernetes v1.15.11 is a transitional version in which the FlexVolume plug-in (storage-driver) is compatible with the CSI plug-in (:ref:`everest <cce_01_0066>`). Clusters of v1.17 and later versions do not support FlexVolume any more. You need to use the everest add-on. For details about CSI and FlexVolume, see :ref:`Differences Between CSI and FlexVolume Plug-ins <cce_01_0306__section86752053123513>`.
-  The FlexVolume plug-in will be maintained by Kubernetes developers, but new functionality will only be added to CSI. You are advised not to create storage that connects to the FlexVolume plug-in (storage-driver) in CCE any more. Otherwise, the storage resources may not function normally.
-  This add-on can be installed only in **clusters of v1.13 or earlier**. By default, the :ref:`everest <cce_01_0066>` add-on is installed when clusters of v1.15 or later are created.

   .. note::

      **In a cluster of v1.13 or earlier**, when an upgrade or bug fix is available for storage functionalities, you only need to install or upgrade the storage-driver add-on. Upgrading the cluster or creating a cluster is not required.

Installing the Add-on
---------------------

This add-on has been installed by default. If it is uninstalled due to some reasons, you can reinstall it by performing the following steps:

If storage-driver is not installed in a cluster, perform the following steps to install it:

#. Log in to the CCE console. In the navigation pane, choose **Add-ons**. On the **Add-on Marketplace** tab page, click **Install Add-on** under **storage-driver**.

#. On the **Install Add-on** page, select the cluster and the add-on version, and click **Next: Configuration**.

#. Click **Install** to install the add-on. Note that the storage-driver has no configurable parameters and can be directly installed.

   After the add-on is installed, click **Go Back to Previous Page**. On the **Add-on Instance** tab page, select the corresponding cluster to view the running instance. This indicates that the add-on has been installed on each node in the cluster.

Upgrading the Add-on
--------------------

#. Log in to the CCE console. In the navigation pane, choose **Add-ons**. On the **Add-on Instance** tab page, select the target cluster and click **Upgrade** under **storage-driver**.

   .. note::

      -  If the **Upgrade** button is unavailable, the current add-on is already up-to-date and no upgrade is required.
      -  When the upgrade is complete, the original storage-driver version on cluster nodes will be replaced by the latest version.

#. On the **Basic Information** page, select the add-on version and click **Next**.
#. Click **Upgrade** to upgrade the storage-driver add-on. Note that the storage-driver has no configurable parameters and can be directly upgraded.

Uninstalling the Add-on
-----------------------

#. Log in to the CCE console. In the navigation pane, choose **Add-ons**. On the **Add-on Instance** tab page, select the target cluster and click **Uninstall** under **storage-driver**.
#. In the dialog box displayed, click **Yes** to uninstall the add-on.
