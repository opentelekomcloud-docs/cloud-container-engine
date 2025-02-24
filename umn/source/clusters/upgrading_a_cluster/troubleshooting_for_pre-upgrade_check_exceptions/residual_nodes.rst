:original_name: cce_10_0439.html

.. _cce_10_0439:

Residual Nodes
==============

Check Items
-----------

Check whether nodes need to be migrated.

Solution
--------

This issue is caused by either an error in the node's package pull component or the absence of key system components on the node, which could be due to an upgrade from an earlier version.

**Solution 1**

Log in to the CCE console and click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane. Locate the row containing the target node and choose **More** > **Reset Node** in the **Operation** column. For details, see :ref:`Resetting a Node <cce_10_0003>`. After the node is reset, retry the check task.

.. note::

   Resetting a node will reset all node labels, which may affect workload scheduling. Before resetting a node, check and retain the labels that you have manually added to the node.

**Solution 2**

After creating a node, delete the faulty node.
