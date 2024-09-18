:original_name: cce_10_0186.html

.. _cce_10_0186:

Deleting a Node
===============

Scenario
--------

You can delete a pay-per-use node that is not needed from the node list.

Deleting or unsubscribing from a node in a CCE cluster will release the node and services running on the node. Drain the node and back up data before the deletion or unsubscription to prevent services running on the node from being affected.

Precautions
-----------

-  Deleting a node will lead to pod migration, which may affect services. Perform this operation during off-peak hours. It is a good practice to drain the node before deletion. For details, see :ref:`Draining a Node <cce_10_0605>`.
-  Unexpected risks may occur during the operation. Back up data beforehand.

-  Deleting a node will cause PVC/PV data loss for the :ref:`local PV <cce_10_0391>` associated with the node. These PVCs and PVs cannot be restored or used again. In this scenario, the pod that uses the local PV is evicted from the node. A new pod is created and stays in the pending state. This is because the PVC used by the pod has a node label, due to which the pod cannot be scheduled.


Deleting a Node
---------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Nodes**. On the displayed page, click the **Nodes** tab.
#. Locate the target node and choose **More** > **Delete** in the **Operation** column.
#. In the **Delete Node** dialog box, enter **DELETE** and click **Yes**.

   .. note::

      -  After the node is deleted, workload pods on the node are automatically migrated to other available nodes.
      -  If the disks and EIPs bound to the node are important resources, unbind them first. Otherwise, they will be deleted with the node.
