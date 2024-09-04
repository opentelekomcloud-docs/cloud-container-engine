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

      -  Changes to the container engine, OS, or pre-/post-installation script in a node pool take effect only on new nodes. To synchronize the modification onto existing nodes, manually reset these nodes.
      -  Changes to resource tags and Kubernetes labels/taints in a node pool will be automatically synchronized to existing nodes. You do not need to reset these nodes.

Synchronizing a Single Node
---------------------------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane. In the right pane, click the **Nodes** tab.
#. Find **upgrade** in the **Node Pool** column of the existing nodes in the node pool.
#. Click **update**. In the dialog box that is displayed, confirm whether to reset the node immediately.

Batch Synchronization
---------------------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane. In the right pane, click the **Node Pools** tab.
#. Choose **More > Synchronize** in the **Operation** column of the target node pool.
#. In the **Batch synchronization** window, configure parameters.

   -  **OS**: shows the image of the target version. You do not need to configure this parameter.
   -  **Synchronization Policy**: **Node Reset** is supported.
   -  **Max. Nodes for Batch Synchronize**: maximum number of nodes that will be unavailable during node synchronization. Nodes will be unavailable during synchronization by resetting the nodes. Properly configure this parameter to prevent pod scheduling failures caused by too many unavailable nodes in the cluster.
   -  **Node List**: Select the nodes requiring the synchronization of node pool configurations.

#. Click **OK**.
