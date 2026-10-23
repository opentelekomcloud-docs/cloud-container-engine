:original_name: cce_10_0658.html

.. _cce_10_0658:

Scaling a Node Pool
===================

When you create a node pool, it contains no nodes. Add nodes to ensure sufficient resources for your workloads. When resources are underutilized, scale in nodes to reduce costs.

.. important::

   The default node pool does not support autoscaling. To adjust the number of nodes, see :ref:`Creating a Node <cce_10_0363>` (scale-out) or :ref:`Deleting a Node <cce_10_0186>` (scale-in).

Node Pool Resizing
------------------

**Scaling out nodes**

This operation adds nodes to a node pool. You specify the flavor and quantity of nodes to add. If node creation fails (for example, due to resource exhaustion), CCE retries automatically until the actual node count matches the expected count. If a node pool contains multiple flavors, nodes are billed according to the flavor you select for each scaling operation.

**Scaling in nodes**

This operation removes nodes from a node pool. You specify the flavor and quantity of nodes to remove. You cannot designate specific nodes for deletion first. To delete a specific node, remove it directly from the node list. For details, see :ref:`Deleting a Node <cce_10_0186>`.

A node's system disk and data disks share its lifecycle. When a node is deleted, its EVS disks are also deleted, and all data is permanently lost and cannot be restored. For data requiring persistent storage, use PVs to decouple storage from the node lifecycle and ensure data durability.

Procedure
---------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane. In the right pane, click the **Node Pools** tab.
#. Click **Resize** next to the target node pool.
#. In the **Resize Node Pool** dialog box, configure scaling parameters.

   -  **Resize**: Add or reduce nodes for scaling.
   -  Use the selected flavor to increase or decrease the number of nodes.
   -  Configure the number of nodes to be added or deleted.

      -  When scaling out a node pool, make sure that the total number of nodes, both existing and new, does not exceed the management scale of the current cluster.

      -  When scaling in a node pool, make sure that the number of nodes to be removed does not exceed the number of nodes currently in the pool.

         Scaling in can result in the unavailability of resources associated with a node, such as local storage and workloads that were scheduled to that node. Exercise caution when performing this operation to avoid impact on running services.

#. Click **OK**. If the node pool status changes from **Scaling** to **Normal**, the node pool has been scaled in or out.

Non-Standard Operations and Remediation
---------------------------------------

Performing non-standard operations may prevent a node pool from scaling as expected and cause unexpected issues. Do not perform any non-standard operations. The following table lists common non-standard operations, their impacts, and remediation steps.

+-------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------+
| Non-standard Operation                                | Impact                                                                                                                 | Remediation                                          |
+=======================================================+========================================================================================================================+======================================================+
| Remove a node directly through *kubectl delete node*. | The cluster API server loses access to the node, and the node status in the node pool is displayed as **Unavailable**. | Perform all node operations through the CCE console. |
|                                                       |                                                                                                                        |                                                      |
|                                                       | However, the ECS is not released, so the node pool's node count remains unchanged.                                     | To restore the node, reset it.                       |
+-------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------+
| Delete a node through the ECS console.                | CCE locks in-use nodes on the ECS console to prevent this operation.                                                   | Delete the node through the CCE console.             |
+-------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------+
