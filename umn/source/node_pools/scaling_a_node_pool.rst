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
#. In the displayed **Node Pool Scaling** window, configure scaling parameters.

   -  Add or reduce nodes for scaling.
   -  Use the selected flavor to increase or decrease the number of nodes.
   -  Configure the number of nodes to be added or deleted.

      -  When scaling out a node pool, make sure that the total number of nodes, both existing and new, does not exceed the management scale of the current cluster.

      -  When scaling in a node pool, make sure that the number of nodes to be removed does not exceed the number of nodes currently in the pool.

         Scaling in can result in the unavailability of resources associated with a node, such as local storage and workloads that were scheduled to that node. Exercise caution when performing this operation to avoid impact on running services.

#. Click **OK**.
