:original_name: cce_10_0658.html

.. _cce_10_0658:

Scaling a Node Pool
===================

You can specify a specification in a node pool for scaling.

.. important::

   The default node pool does not support scaling. Use :ref:`Creating a Node <cce_10_0363>` to add a node.

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane. In the right pane, click the **Node Pools** tab.
#. Choose **Scaling** next to the target node pool.
#. In the displayed window, configure scaling parameters.

   -  **Number of Scaling Targets**: The number of target nodes cannot exceed the management scale of the current cluster.
   -  **Node Configuration**: Use the selected flavor to add nodes. If the flavor resources are insufficient, the scale-out will fail.

      .. note::

         -  If there are fewer nodes running in the node pool than the desired number of nodes, some nodes will be added. If there are more nodes than the desired number of nodes, some nodes will be deleted.
         -  During scale-in, if there are not enough nodes of the specified flavor to be deleted, nodes of other flavors will be removed.

#. Click **OK**.
