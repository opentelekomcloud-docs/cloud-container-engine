:original_name: cce_10_0860.html

.. _cce_10_0860:

Expanding the Capacity of an EVS Disk
=====================================

If the EVS disk attached to a workload does not have enough space, you can increase its capacity by expanding it. This section describes how to expand the capacity of an EVS disk through the console.

Prerequisites
-------------

You have created a cluster and installed the :ref:`CCE Container Storage (Everest) <cce_10_0066>` add-on in the cluster.

Procedure for an EVS Disk
-------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Storage** in the navigation pane and click the **PersistentVolumeClaims (PVCs)** tab. Click **More** in the **Operation** column of the target PVC and select **Scale-out**.
#. Enter the capacity to be added and click **OK**.

   .. note::

      The disk size can only be increased, not decreased.
