:original_name: cce_10_0656.html

.. _cce_10_0656:

Migrating a Node
================

Nodes in a node pool can be migrated. Currently, nodes in a node pool can be migrated only to the default node pool (defaultpool) in the same cluster.

#. Log in to the CCE console and access the cluster console.
#. In the navigation pane, choose **Nodes** and switch to the **Node Pools** tab page.
#. Click **View Node** in the **Operation** column of the node pool to be migrated.
#. Click **More** > **Migrate** in the **Operation** column of the target node to migrate the node.
#. In the displayed **Migrate Node** window, confirm the information.

   .. note::

      The migration has no impacts on the original resource tags, Kubernetes labels, and taints of the node.
