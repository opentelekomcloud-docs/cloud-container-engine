:original_name: cce_10_0489.html

.. _cce_10_0489:

NetworkManager
==============

Check Items
-----------

Check whether NetworkManager of a node is normal.

Solution
--------

Log in to the node and run the **systemctl is-active NetworkManager** command to obtain the running status of NetworkManager. If the command output is abnormal, run the **systemctl restart NetworkManager** command and obtain the status again.

If the fault persists, reset the node. For details, see :ref:`Resetting a Node <cce_10_0003>`. Alternatively, contact technical support to restore the file and then perform the upgrade.
