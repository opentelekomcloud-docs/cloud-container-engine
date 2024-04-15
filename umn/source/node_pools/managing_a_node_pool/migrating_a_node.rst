:original_name: cce_10_0656.html

.. _cce_10_0656:

Migrating a Node
================

Nodes in a node pool can be migrated to the default node pool. Nodes in the default node pool or a custom node pool cannot be migrated to other custom node pools.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Nodes** and click the **Node Pools** tab.
#. Click **View Node** in the **Operation** column of the node pool to be migrated.
#. Click **More** > **Migrate** in the **Operation** column of the target node to migrate the node.
#. In the displayed **Migrate Node** dialog box, confirm the information.

   .. note::

      -  The migration does not affect custom resource tags, Kubernetes labels, and taints of the node.
      -  After the migration, system labels **cce.cloud.com** and **cce-nodepool** on the node will be deleted. If an existing workload uses these labels for affinity or anti-affinity scheduling, the existing pods on the node will be stopped and rescheduled when kubelet is restarted.
