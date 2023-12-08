:original_name: cce_10_0431.html

.. _cce_10_0431:

Node Restrictions
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

-  **Scenario 1: The node status is abnormal. Rectify the fault first.**

   Log in to the CCE console and access the cluster console. Choose **Nodes** in the navigation pane and check the node status. Ensure that the node is in the **Running** status. A node in the **Installing** or **Deleting** status cannot be upgraded.

   If the node status is abnormal, restore the node and retry the check task.

-  **Scenario 2: The node OS does not support the upgrade. Contact technical support.**

   The following table lists the node OSs that support the upgrade. You can reset the node OS to an available OS in the list.

   .. table:: **Table 1** OSs that support the upgrade

      +-------------+-----------------------------------------------------------------------------------------------------------------------+
      | OS          | Constraint                                                                                                            |
      +=============+=======================================================================================================================+
      | EulerOS 2.x | None.                                                                                                                 |
      +-------------+-----------------------------------------------------------------------------------------------------------------------+
      | Ubuntu      | Some sites cannot perform upgrade. If the check result shows the upgrade is not supported, contact technical support. |
      +-------------+-----------------------------------------------------------------------------------------------------------------------+

-  **Scenario 3: The node belongs to the default node pool but contains a common node pool label, which affects the upgrade process.**

   If a node is migrated from a node pool to the default node pool, the node pool label **cce.cloud.com/cce-nodepool** is retained, affecting cluster upgrade. Check whether the load scheduling on the node depends on the label.

   -  If there is no dependency, delete the tag.
   -  If yes, modify the load balancing policy, remove the dependency, and then delete the tag.

-  **Scenario 4: CNIProblem taints are detected on the node. Remove the taints first.**

   The node contains a taint whose key is **node.cloudprovider.kubernetes.io/cni-problem**, and the effect is **NoSchedule**. The taint is added by the npd add-on. You are advised to upgrade the npd add-on to the latest version and check again. If the problem persists, contact technical support.

-  **Scenario 5: The Kubernetes node corresponding to the node does not exist. The node may be being deleted. Check again later.**

   Check again later.
