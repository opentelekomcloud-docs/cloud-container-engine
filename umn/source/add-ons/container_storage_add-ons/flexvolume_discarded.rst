:original_name: cce_10_0127.html

.. _cce_10_0127:

FlexVolume (Discarded)
======================

Introduction
------------

CCE Container Storage (FlexVolume), also called storage-driver, functions as a standard Kubernetes FlexVolume plugin to allow containers to use EVS, SFS, OBS, and SFS Turbo storage resources. By installing and upgrading storage-driver, you can quickly install and update cloud storage capabilities.

**FlexVolume is a system resource add-on. It is installed by default when a cluster of Kubernetes v1.13 or earlier is created.**

Notes and Constraints
---------------------

-  For clusters created in CCE, Kubernetes v1.15.11 is a transitional version in which the FlexVolume add-on is compatible with the CSI add-on (:ref:`Everest <cce_10_0066>`). Clusters of v1.17 and later versions do not support FlexVolume anymore. Use the Everest add-on.
-  The FlexVolume add-on will be maintained by Kubernetes developers, but new functionality will only be added to :ref:`Everest <cce_10_0066>`. Do not create CCE storage that uses the FlexVolume add-on (storage-driver) anymore. Otherwise, storage may malfunction.
-  This add-on can be installed only in **clusters of v1.13 or earlier**. By default, the :ref:`Everest <cce_10_0066>` add-on is installed when clusters of v1.15 or later are created.

   .. note::

      **In a cluster of v1.13 or earlier**, when an upgrade or bug fix is available for storage functionalities, you only need to install or upgrade the storage-driver add-on. Upgrading the cluster or creating a cluster is not required.

Installing the Add-on
---------------------

This add-on has been installed by default. If it is uninstalled due to some reasons, you can reinstall it by performing the following steps:

If storage-driver is not installed in a cluster, perform the following steps to install it:

#. Log in to the CCE console and click the cluster name to access the cluster console. Choose **Add-ons** in the navigation pane, locate **CCE Container Storage (FlexVolume)** on the right, and click **Install**.
#. Click **Install** to install the add-on. Note that the storage-driver has no configurable parameters and can be directly installed.
