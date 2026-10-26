:original_name: cce_10_0654.html

.. _cce_10_0654:

Synchronizing Node Pools
========================

After the settings of a node pool are updated, some settings cannot be automatically synchronized for existing nodes. You can manually synchronize the settings for these nodes.

.. caution::

   -  Do not delete or reset nodes during batch synchronization. Otherwise, the synchronization of node pool configuration may fail.
   -  This operation involves resetting nodes. **Workloads running on a node may be interrupted due to standalone deployment or insufficient schedulable resources.** Evaluate the upgrade risks and perform the upgrade during off-peak hours. Alternatively, `specify a disruption budget for your key applications <https://kubernetes.io/docs/tasks/run-application/configure-pdb/>`__ to ensure the availability of these applications during the upgrade.
   -  During configuration synchronization for existing nodes, the nodes will be reset, and the system disks and data disks will be cleared. Back up important data before the synchronization.
   -  Only some node pool parameters can be synchronized by resetting nodes. For details, see :ref:`Updating a Node Pool <cce_10_0653>`.
   -  If a new OS patch is available in the environment, synchronize the node pool to update the OS.

Synchronizing a Single Node
---------------------------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane. In the right pane, click the **Nodes** tab.
#. Find the node marked with **Node pool updated** in the node pool.
#. Move the cursor to **Node pool updated**. The **Reset** button is displayed. You can then determine whether to reset the node immediately.

Batch Synchronization
---------------------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane. In the right pane, click the **Node Pools** tab.
#. Choose **More > Synchronize** in the **Operation** column of the target node pool.
#. In the **Batch Synchronize** window, configure parameters.

   -  **OS**: shows the image of the target version. You do not need to configure this parameter.
   -  **Synchronization Type**: **Reset node** is supported.
   -  **Max. Nodes for Batch Synchronization**: the maximum number of nodes that can be synchronized simultaneously. Nodes will be unavailable during synchronization when the nodes are reset. Properly configure this parameter to prevent pod scheduling failures caused by too many unavailable nodes in the cluster.

#. Select the target nodes and click **OK** to start the synchronization.
