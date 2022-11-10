:original_name: cce_01_0003.html

.. _cce_01_0003:

Resetting a Node
================

Scenario
--------

You can reset a node to modify the node configuration, such as the node OS and login mode.

Resetting a node will reinstall the node OS and the Kubernetes software on the node. If a node is unavailable because you modify the node configuration, you can reset the node to rectify the fault.

Notes and Constraints
---------------------

-  The cluster version must be v1.13 or later.

Notes
-----

-  Only worker nodes can be reset. If the node is still unavailable after the resetting, delete the node and create a new one.
-  **Resetting a node will reinstall the node OS and interrupt workload services running on the node. Therefore, perform this operation during off-peak hours.**
-  **Data in the system disk and Docker data disks will be cleared. Back up important data before resetting the node.**
-  **When an extra data disk is mounted to a node, data in this disk will be cleared if the disk has not been unmounted before the node reset. To prevent data loss, back up data in advance and mount the data disk again after the node reset is complete.**
-  The IP addresses of the workload pods on the node will change, but the container network access is not affected.
-  There is remaining EVS disk quota.
-  While the node is being deleted, the backend will set the node to the unschedulable state.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Nodes**. In the same row as the node you will reset, choose **More** > **Reset**.

#. In the dialog box displayed, enter **RESET** and reconfigure the key pair for login.


   .. figure:: /_static/images/en-us_image_0000001190302085.png
      :alt: **Figure 1** Resetting the selected node

      **Figure 1** Resetting the selected node

#. Click **Yes** and wait until the node is reset.

   After the node is reset, pods on it are automatically migrated to other available nodes.
