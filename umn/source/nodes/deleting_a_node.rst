:original_name: cce_01_0186.html

.. _cce_01_0186:

Deleting a Node
===============

Scenario
--------

When a node in a CCE cluster is deleted, services running on the node will also be deleted. Exercise caution when performing this operation.

Notes and Constraints
---------------------

-  After a CCE cluster is deleted, the ECS nodes in the cluster are also deleted.

Notes
-----

-  Deleting a node will lead to pod migration, which may affect services. Perform this operation during off-peak hours.
-  Unexpected risks may occur during the operation. Back up related data in advance.
-  During the operation, the backend will set the node to the unschedulable state.
-  Only worker nodes can be deleted.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Nodes**. In the same row as the node you will delete, choose **More** > **Delete**.
#. In the **Delete Node** dialog box, enter **DELETE** and click **Yes**.

   .. note::

      -  After the node is deleted, pods on it are automatically migrated to other available nodes.
      -  If the disks and EIPs bound to the node are important resources, unbind them first. Otherwise, they will be deleted with the node.
