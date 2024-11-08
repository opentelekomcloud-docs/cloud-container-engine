:original_name: cce_10_0657.html

.. _cce_10_0657:

Deleting a Node Pool
====================

Deleting a node pool will delete nodes in the pool. Pods on these nodes will be automatically migrated to available nodes in other node pools.

Precautions
-----------

-  Deleting a node pool will delete all nodes in the node pool. Back up data in a timely manner to prevent data loss.
-  Deleting a node will lead to pod migration, which may affect services. Perform this operation during off-peak hours. If pods in the node pool have a specific node selector and none of the other nodes in the cluster satisfies the node selector, the pods will become unschedulable.
-  When deleting a node pool, the system sets all nodes in the current node pool to the unschedulable state.

Procedure
---------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane. In the right pane, click the **Node Pools** tab.
#. Choose **More > Delete** in the **Operation** column of the target node pool.
#. Read the precautions in the **Delete Node Pool** dialog box.
#. In the text box, enter **DELETE** and click **Yes** to confirm that you want to continue the deletion.
