:original_name: cce_10_0605.html

.. _cce_10_0605:

Draining a Node
===============

Scenario
--------

After you enable nodal drainage on the console, CCE configures the node to be non-schedulable and securely evicts all pods that comply with :ref:`Rules for Draining Nodes <cce_10_0605__section14957193483118>` on the node. Subsequent new pods will not be scheduled to this node.

When a node becomes faulty, nodal drainage quickly isolates the faulty node. The pods evicted from the faulty node will be scheduled by the workload controller to other nodes that are running properly.

.. important::

   To ensure service availability during drainage, `specify a disruption budget <https://kubernetes.io/docs/tasks/run-application/configure-pdb/>`__ for your application. Otherwise, the application may become unavailable during pod rescheduling.

Prerequisites
-------------

-  A cluster is available and the cluster version meets the following requirements:

   -  v1.21: v1.21.10-r0 or later
   -  v1.23: v1.23.8-r0 or later
   -  v1.25: v1.25.3-r0 or later
   -  Versions later than v1.25

-  To drain a node as an IAM user, you must have at least one of the following permissions (for details, see :ref:`Namespace Permissions (Kubernetes RBAC-based) <cce_10_0189>`):

   -  cluster-admin (administrator): read and write permissions on all resources in all namespaces.
   -  drainage-editor: drain a node.
   -  drainage-viewer: view the nodal drainage status but cannot drain a node.

.. _cce_10_0605__section14957193483118:

Rules for Draining Nodes
------------------------

When a node is drained, all pods on the node will be safely evicted. However, CCE will take specific actions for pods that meet certain filtering criteria.

+-----------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------+--------------------------+
| Filter Criterion                                                                                                                                    | Forced Drainage Enabled | Forced Drainage Disabled |
+=====================================================================================================================================================+=========================+==========================+
| The `status.phase <https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#pod-phase>`__ field of the pod is **Succeeded** or **Failed**. | Deletion                | Deletion                 |
+-----------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------+--------------------------+
| The pod is not managed by the workload controller.                                                                                                  | Deletion                | Drainage cancellation    |
+-----------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------+--------------------------+
| The pod is managed by DaemonSet.                                                                                                                    | None                    | Drainage cancellation    |
+-----------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------+--------------------------+
| A volume of the emptyDir type is mounted to the pod.                                                                                                | Eviction                | Drainage cancellation    |
+-----------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------+--------------------------+
| The pod is a `static pod <https://kubernetes.io/docs/tasks/configure-pod-container/static-pod/>`__ directly managed by kubelet                      | None                    | None                     |
+-----------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------+--------------------------+

.. note::

   The following operations may be performed on pods during node drainage:

   -  Deletion: The pod is deleted from the current node and will not be scheduled to other nodes.
   -  Eviction: The pod is deleted from the current node and rescheduled to another node.
   -  None: The pod will not be evicted or deleted.
   -  Drainage cancellation: If a pod on a node cancels drainage, the drainage process of the node is terminated and no pod is evicted or deleted.

Procedure
---------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Nodes**. On the displayed page, click the **Nodes** tab.
#. Locate the target node and choose **More** > **Nodal Drainage** in the **Operation** column.
#. In the **Nodal Drainage** window displayed, set parameters.

   -  **Timeout (s)**: The drainage task automatically fails after the preset timeout period. The value 0 indicates that the timeout period is not set.
   -  **Forced Drainage**: If this function is enabled, pods managed by DaemonSet will be ignored, and pods with emptyDir volumes and pods not managed by controllers will be deleted. For details, see :ref:`Rules for Draining Nodes <cce_10_0605__section14957193483118>`.

#. Click **OK** and wait until the node drainage is complete.

Cancelling Node Drainage
------------------------

.. note::

   In clusters of v1.23.16-r0, v1.25.11-r0, v1.27.8-r0, v1.28.6-r0, v1.29.2-r0, or later versions, node drainage can be canceled.

   This operation will abort drainage on nodes, but workloads that have been evicted from these nodes will not be automatically migrated back.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Nodes**. On the displayed page, click the **Nodes** tab.
#. Locate the node that is being drained and click **Cancel Drainage**.
#. In the displayed dialog box, click **OK**. The node status changes to **Drainage cancelled**. You can click **Enable Scheduling** to restore the node to the schedulable state.
