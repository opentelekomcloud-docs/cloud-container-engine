:original_name: cce_10_0036.html

.. _cce_10_0036:

Stopping a Node
===============

Scenario
--------

When a node in the cluster is stopped, all services on that node will also be stopped, and the node will no longer be available for scheduling. Check if your services will be affected before stopping a node.

Precautions
-----------

-  Stopping a node will lead to pod migration, which may affect services. Perform this operation during off-peak hours.
-  Unexpected risks may occur during the operation. Back up data beforehand.

Procedure
---------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Nodes**. On the displayed page, click the **Nodes** tab.
#. Locate the target node and click its name.
#. In the upper right corner of the ECS details page, click **Stop**. In the displayed dialog box, click **OK**.
