:original_name: cce_10_0564.html

.. _cce_10_0564:

Node Label and Taint Check
==========================

Check Item
----------

-  Check whether the label is lost.
-  Check whether there are unexpected taints.

Procedure
---------

Go to the CCE console, access the cluster console, and choose **Nodes** in the navigation pane. On the displayed page, click the **Nodes** tab, select all nodes, and click **Manage Labels and Taints** to view the labels and taints of the current node.

Solution
--------

User labels are not changed during the cluster upgrade. If you find that labels are lost or added abnormally, contact technical support.

If you find a new taint (**node.kubernetes.io/upgrade**) on a node, the node may be skipped during the upgrade. For details, see :ref:`Node Skipping Check for Reset <cce_10_0567>`.

If you find that other taints are added to the node, contact technical support.
