:original_name: cce_10_0186.html

.. _cce_10_0186:

Deleting a Node
===============

Scenario
--------

When a node in a CCE cluster is deleted, services running on the node will also be deleted. Exercise caution when performing this operation.

Constraints
-----------

-  VM nodes that are being used by CCE do not support deletion on the ECS page.
-  Deleting a node will cause PVC/PV data loss for the :ref:`local PVs <cce_10_0391>` associated with the node. These PVCs and PVs cannot be restored or used again. In this scenario, the pod that uses the local PV is evicted from the node. A new pod is created and stays in the pending state. This is because the PVC used by the pod has a node label, due to which the pod cannot be scheduled.

Precautions
-----------

-  Deleting a node will lead to pod migration, which may affect services. Perform this operation during off-peak hours.
-  Unexpected risks may occur during the operation. Back up related data in advance.
-  While the node is being deleted, the backend will set the node to the unschedulable state.
-  Only worker nodes can be deleted.

Procedure
---------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Nodes**. On the displayed page, click the **Nodes** tab.
#. Locate the target node and choose **More** > **Delete** in the **Operation** column.
#. In the **Delete Node** dialog box, enter **DELETE** and click **Yes**.

   .. note::

      -  After the node is deleted, workload pods on the node are automatically migrated to other available nodes.
      -  If the disks and EIPs bound to the node are important resources, unbind them first. Otherwise, they will be deleted with the node.
