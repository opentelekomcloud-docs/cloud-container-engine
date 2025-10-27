:original_name: cce_10_0654.html

.. _cce_10_0654:

Synchronizing Node Pools
========================

After the configuration of a node pool is updated, some configurations cannot be automatically synchronized for existing nodes. You can manually synchronize configurations for these nodes.

.. important::

   -  Do not delete or reset nodes during batch synchronization. Otherwise, the synchronization of node pool configuration may fail.
   -  This operation involves resetting nodes. **Workloads running on a node may be interrupted due to standalone deployment or insufficient schedulable resources.** Evaluate the upgrade risks and perform the upgrade during off-peak hours. Alternatively, `specify a disruption budget for your key applications <https://kubernetes.io/docs/tasks/run-application/configure-pdb/>`__ to ensure the availability of these applications during the upgrade.
   -  During configuration synchronization for existing nodes, the nodes will be reset, and the system disks and data disks will be cleared. Back up important data before the synchronization.
   -  Only some node pool parameters can be synchronized by resetting nodes. The constraints are as follows:

      -  Changes to the container engine, OS, security hardening, or pre-/post-installation script in a node pool take effect only on new nodes. To synchronize the modification onto existing nodes, manually reset these nodes.
      -  The modification of the system disk or data disk size and data disk space allocation rule of a node pool applies only to new nodes. The settings cannot be synchronized for existing nodes even if they are reset.
      -  Changes to resource tags and Kubernetes labels/taints in a node pool will be automatically synchronized to existing nodes. You do not need to reset these nodes.

      -  If a new OS patch exists in the environment, you can synchronize the node pool to update the OS.

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
#. In the **Batch synchronization** window, configure parameters.

   -  **OS**: shows the image of the target version. You do not need to configure this parameter.
   -  **Synchronization Type**: **Reset node** is supported.
   -  **Max. Nodes for Batch Synchronization**: maximum number of nodes that will be unavailable during node synchronization. Nodes will be unavailable during synchronization by resetting the nodes. Properly configure this parameter to prevent pod scheduling failures caused by too many unavailable nodes in the cluster.
   -  In the node list, select the nodes requiring the synchronization of node pool configurations.

#. Click **OK**.
