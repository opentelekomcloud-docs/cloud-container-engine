:original_name: cce_10_0431.html

.. _cce_10_0431:

Node Restrictions
=================

Check Items
-----------

Check the following items:

-  Check whether the node is available.
-  Check whether the node OS supports the upgrade.
-  Check whether the node is marked with unexpected node pool labels.
-  Check whether the Kubernetes node name is the same as the ECS name.

Solutions
---------

#. **The node is unavailable. Preferentially recover the node.**

   If a node is unavailable, log in to the CCE console and click the cluster name to access the cluster console. Then, choose **Nodes** in the navigation pane. In the right pane, click the **Nodes** tab. Ensure that the node is in the **Running** state. A node in the **Installing** or **Deleting** state cannot be upgraded.

   If a node is unavailable, recover the node and retry the check task.

#. **The node OS does not support the upgrade.**

   The following table lists the node OSs that support the upgrade. You can reset the node OS to an available OS in the list.

   .. table:: **Table 1** OSs that support the upgrade

      +-----------------------------------+-------------------------------------------------------------------------------------------------+
      | OS                                | Constraint                                                                                      |
      +===================================+=================================================================================================+
      | EulerOS 2.x                       | If the target version is earlier than v1.27, there are no constraints.                          |
      |                                   |                                                                                                 |
      |                                   | If the target version is v1.27 or later, only EulerOS 2.9 and EulerOS 2.10 support the upgrade. |
      +-----------------------------------+-------------------------------------------------------------------------------------------------+
      | Ubuntu                            | If the target version is v1.27 or later, only Ubuntu 22.04 supports the upgrade.                |
      +-----------------------------------+-------------------------------------------------------------------------------------------------+
      | HCE OS 2.0                        | None                                                                                            |
      +-----------------------------------+-------------------------------------------------------------------------------------------------+

#. **The affected node belongs to the default node pool but it is configured with a non-default node pool label, which will affect the upgrade.**

   If a node is migrated from a common node pool to the default node pool, the **cce.cloud.com/cce-nodepool** label will affect the cluster upgrade. Check whether load scheduling on the node depends on the label.

   -  If no, delete the label.
   -  If yes, modify the load balancing policy, remove the dependency, and then delete the label.

#. **The node is marked with a CNIProblem taint. Preferentially recover the node.**

   The node contains a taint whose key is **node.cloudprovider.kubernetes.io/cni-problem**, and the effect is **NoSchedule**. The taint is added by the NPD add-on. Upgrade the NPD add-on to the latest version and check again. If the problem persists, contact technical support.

#. **The resources for the affected node in Kubernetes are not available. It is possible that the node is being deleted. Try again later.**

   Recheck after the node is deleted.

#. **The OS running on the master node is EulerOS 2.5, which does not support the cluster to be upgraded to v1.27.5-r0.**

   You can upgrade the version to 1.25 or 1.28. If you choose to upgrade the version to 1.28, EulerOS 2.5 will be rolling upgraded to HCE OS 2.0 during the upgrade. If you have other requirements, submit a service ticket.
