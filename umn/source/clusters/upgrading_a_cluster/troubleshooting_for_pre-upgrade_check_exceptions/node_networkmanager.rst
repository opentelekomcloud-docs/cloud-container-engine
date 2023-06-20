:original_name: cce_10_0489.html

.. _cce_10_0489:

Node NetworkManager
===================

Check Item
----------

Check whether NetworkManager of a node is normal.

Solution
--------

Log in to the node and run the **systemctl is-active NetworkManager** command to query the running status of NetworkManager. If the command output is abnormal, run the **systemctl restart NetworkManager** command and query the status again.

If the problem persists after NetworkManager is restarted, contact technical support.
