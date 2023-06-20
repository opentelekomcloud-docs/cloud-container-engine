:original_name: cce_10_0567.html

.. _cce_10_0567:

Node Skipping Check for Reset
=============================

Check Item
----------

After the cluster is upgraded, you need to reset the nodes that fail to be upgraded.

Procedure
---------

Go back to the previous step or view the upgrade details on the upgrade history page to view the nodes that are skipped during the upgrade.

The skipped nodes are displayed on the upgrade details page. Reset the skipped nodes after the upgrade is complete. For details about how to reset a node, see :ref:`Resetting a Node <cce_10_0003>`.

.. note::

   Resetting a node will reset all node labels, which may affect workload scheduling. Before resetting a node, check and retain the labels that you have manually added to the node.
