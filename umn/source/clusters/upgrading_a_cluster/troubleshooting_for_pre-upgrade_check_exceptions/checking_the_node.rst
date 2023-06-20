:original_name: cce_10_0431.html

.. _cce_10_0431:

Checking the Node
=================

Check Item
----------

Check the following aspects:

-  Check whether the node is available.
-  Check whether the node OS supports the upgrade.
-  Check whether there are unexpected node pool tags in the node.
-  Check whether the Kubernetes node name is consistent with the ECS name.

Solution
--------

-  **Scenario 1: The node is unavailable.**

   Log in to the CCE console and access the cluster console. Choose **Nodes** in the navigation pane and check the node status. Ensure that the node is in the **Running** status. A node in the **Installing** or **Deleting** status cannot be upgraded.

   If the node status is abnormal, restore the node by referring to and retry the check task.

-  **Scenario 2: The node OS does not support the upgrade.**

   The following table lists the node OSs that support the upgrade. You can reset the node OS to an available OS in the list.

   .. table:: **Table 1** OSs that support the upgrade

      +-----------------+-----------------------------------------------------------------------------------------------------------------------+
      | OS              | Restriction                                                                                                           |
      +=================+=======================================================================================================================+
      | EulerOS 2.5/2.9 | None                                                                                                                  |
      +-----------------+-----------------------------------------------------------------------------------------------------------------------+
      | CentOS 7.7      | None                                                                                                                  |
      +-----------------+-----------------------------------------------------------------------------------------------------------------------+
      | Ubuntu 22.04    | Some sites cannot perform upgrade. If the check result shows the upgrade is not supported, contact technical support. |
      +-----------------+-----------------------------------------------------------------------------------------------------------------------+

-  **Scenario 3: There are unexpected node pool tags in the node.**

   If a node is migrated from a node pool to the default node pool, the node pool label **cce.cloud.com/cce-nodepool** is retained, affecting cluster upgrade. Check whether the load scheduling on the node depends on the label.

   -  If there is no dependency, delete the tag.
   -  If yes, modify the load balancing policy, remove the dependency, and then delete the tag.

-  **Scenario 4: The Kubernetes node name is consistent with the ECS name.**

   Kubernetes node name, which defaults to the node's private IP. If you select a cloud server name as the node name, the cluster cannot be upgraded.

   Log in to the CCE console and access the cluster console. Choose **Nodes** in the navigation pane, view the node label, and check whether the value of **kubernetes.io/hostname** is consistent with the ECS name. If they are the same, remove the node before the cluster upgrade.

   |image1|

.. |image1| image:: /_static/images/en-us_image_0000001517903020.png
