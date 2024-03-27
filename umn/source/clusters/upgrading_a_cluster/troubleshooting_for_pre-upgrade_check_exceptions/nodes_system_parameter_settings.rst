:original_name: cce_10_0504.html

.. _cce_10_0504:

Nodes' System Parameter Settings
================================

Check Items
-----------

Check whether the default system parameter settings on your nodes are modified.

Solution
--------

If the MTU value of the bond0 network on your BMS node is not the default value 1500, this check item failed.

Non-default parameter settings may lead to service packet loss. Change them back to the default values.
