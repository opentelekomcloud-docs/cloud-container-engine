:original_name: cce_10_0127.html

.. _cce_10_0127:

storage-driver (System Resource Add-On, Discarded)
==================================================

Introduction
------------

storage-driver functions as a standard Kubernetes FlexVolume plug-in to allow containers to use EVS, SFS, OBS, and SFS Turbo storage resources. By installing and upgrading storage-driver, you can quickly install and update cloud storage capabilities.

**storage-driver is a system resource add-on. It is installed by default when a cluster of Kubernetes v1.13 or earlier is created.**

Notes and Constraints
---------------------

-  For clusters created in CCE, Kubernetes v1.15.11 is a transitional version in which the FlexVolume plug-in (storage-driver) is compatible with the CSI plug-in (:ref:`everest <cce_10_0066>`). Clusters of v1.17 and later versions do not support FlexVolume anymore. You need to use the everest add-on.
-  The FlexVolume plug-in will be maintained by Kubernetes developers, but new functionality will only be added to CSI. You are advised not to create storage that connects to the FlexVolume plug-in (storage-driver) in CCE anymore. Otherwise, the storage resources may not function normally.
-  This add-on can be installed only in **clusters of v1.13 or earlier**. By default, the :ref:`everest <cce_10_0066>` add-on is installed when clusters of v1.15 or later are created.

   .. note::

      **In a cluster of v1.13 or earlier**, when an upgrade or bug fix is available for storage functionalities, you only need to install or upgrade the storage-driver add-on. Upgrading the cluster or creating a cluster is not required.

Installing the Add-on
---------------------

This add-on has been installed by default. If it is uninstalled due to some reasons, you can reinstall it by performing the following steps:

If storage-driver is not installed in a cluster, perform the following steps to install it:

#. Log in to the CCE console, click the cluster name, and access the cluster console. Choose **Add-ons** in the navigation pane, locate **storage-driver** on the right, and click **Install**.
#. Click **Install** to install the add-on. Note that the storage-driver has no configurable parameters and can be directly installed.
