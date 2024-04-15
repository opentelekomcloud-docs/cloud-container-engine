:original_name: cce_10_0455.html

.. _cce_10_0455:

Node Readiness
==============

Check Items
-----------

Check whether the nodes in the cluster are ready.

Solution
--------

-  **Scenario 1: The nodes are in the unavailable status.**

   Log in to the CCE console and click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane and filter out unavailable nodes, rectify the faulty nodes by referring to the suggestions provided by the console, and check again.

-  **Scenario 2: The displayed node status is inconsistent with the actual status.**

   The possible causes are as follows:

   #. The node status is normal on the nodes page, but the check result shows that the node is not ready. Check again.
   #. The node is not found on the nodes page, but the check result shows that the node is in the cluster. Contact technical support.
