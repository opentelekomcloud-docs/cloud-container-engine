:original_name: cce_10_0725.html

.. _cce_10_0725:

Importing an EV to a Storage Pool
=================================

CCE allows you to use LVM to combine data volumes on nodes into a storage pool (VolumeGroup) and create LVs for containers to mount. Before creating a local EV, import the data disk of the node to the storage pool.

Notes and Constraints
---------------------

-  Local EVs are supported only when the cluster version is v1.21.2-r0 or later and the Everest add-on version is 1.2.29 or later.

-  The first data disk (used by container runtime and the kubelet component) on a node cannot be imported as a storage pool.
-  Storage pools in striped mode do not support scale-out. After scale-out, fragmented space may be generated and the storage pool cannot be used.
-  Storage pools cannot be scaled in or deleted.
-  If disks in a storage pool on a node are deleted, the storage pool will malfunction.

Importing a Storage Pool
------------------------

**Imported during node creation**

When creating a node, you can add a data disk to the node in **Storage Settings** and import the data disk to the storage pool as an EV. For details, see :ref:`Creating a Node <cce_10_0363>`.

**Imported manually**

If no EV is imported during node creation, or the capacity of the current storage volume is insufficient, you can manually import an EV.

#. Go to the ECS console and add a SCSI disk to the node.
#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Storage** in the navigation pane. In the right pane, click the **Storage Pool** tab.
#. View the node to which the disk has been added and select **Import as EV**. You can select a write mode during the import.

   .. note::

      If the manually attached disk is not displayed in the storage pool, wait for 1 minute and refresh the list.

   -  **Linear**: A linear logical volume integrates one or more physical volumes. Data is written to the next physical volume when the previous one is used up.
   -  **Striped**: A striped logical volume stripes data into blocks of the same size and stores them in multiple physical volumes in sequence, allowing data to be concurrently read and written. Select this option only when there are multiple volumes.
