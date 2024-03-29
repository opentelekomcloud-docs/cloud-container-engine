:original_name: cce_10_0154.html

.. _cce_10_0154:

CCE Cluster Autoscaler
======================

Introduction
------------

CCE Cluster Autoscaler is an important Kubernetes controller. It supports microservice scaling and is key to serverless design.

When the CPU or memory usage of a microservice is too high, horizontal pod autoscaling is triggered to add pods to reduce the load. These pods can be automatically reduced when the load is low, allowing the microservice to run as efficiently as possible.

CCE simplifies the creation, upgrade, and manual scaling of Kubernetes clusters, in which traffic loads change over time. To balance resource usage and workload performance of nodes, Kubernetes introduces the autoscaler add-on to automatically adjust the number of nodes in a cluster based on the resource usage required for workloads deployed in the cluster. For details, see :ref:`Creating a Node Scaling Policy <cce_10_0209>`.

Open source community: https://github.com/kubernetes/autoscaler

How the Add-on Works
--------------------

autoscaler controls auto scale-out and scale-in.

-  **Auto scale-out**

   You can choose either of the following methods:

   -  If pods in a cluster cannot be scheduled due to insufficient worker nodes, cluster scaling is triggered to add nodes. The nodes to be added have the same specification as configured for the node pool to which the nodes belong.

      Auto scale-out will be performed when:

      -  Node resources are insufficient.
      -  No node affinity policy is set in the pod scheduling configuration. If a node has been configured as an affinity node for pods, no node will not be automatically added when pods cannot be scheduled. For details about how to configure the node affinity policy, see :ref:`Scheduling Policy (Affinity/Anti-affinity) <cce_10_0232>`.

   -  When the cluster meets the node scaling policy, cluster scale-out is also triggered. For details, see :ref:`Creating a Node Scaling Policy <cce_10_0209>`.

   .. note::

      The add-on follows the "No Less, No More" policy. For example, if three cores are required for creating a pod and the system supports four-core and eight-core nodes, autoscaler will preferentially create a four-core node.

-  **Auto scale-in**

   When a cluster node is idle for a period of time (10 minutes by default), cluster scale-in is triggered, and the node is automatically deleted. However, a node cannot be deleted from a cluster if the following pods exist:

   -  Pods that do not meet specific requirements set in Pod Disruption Budgets (`PodDisruptionBudget <https://kubernetes.io/docs/tasks/run-application/configure-pdb/>`__)
   -  Pods that cannot be scheduled to other nodes due to constraints such as affinity and anti-affinity policies
   -  Pods that have the **cluster-autoscaler.kubernetes.io/safe-to-evict: 'false'** annotation
   -  Pods (except those created by DaemonSets in the kube-system namespace) that exist in the kube-system namespace on the node
   -  Pods that are not created by the controller (Deployment/ReplicaSet/job/StatefulSet)

   .. note::

      When a node meets the scale-in conditions, autoscaler adds the **DeletionCandidateOfClusterAutoscaler** taint to the node in advance to prevent pods from being scheduled to the node. After the autoscaler add-on is uninstalled, if the taint still exists on the node, manually delete it.

Constraints
-----------

-  Ensure that there are sufficient resources for installing the add-on.
-  The default node pool does not support auto scaling. For details, see :ref:`Description of DefaultPool <cce_10_0081__section16928123042115>`.
-  When autoscaler is used, some taints or annotations may affect auto scaling. Therefore, do not use the following taints or annotations in clusters:

   -  **ignore-taint.cluster-autoscaler.kubernetes.io**: The taint works on nodes. Kubernetes-native autoscaler supports protection against abnormal scale outs and periodically evaluates the proportion of available nodes in the cluster. When the proportion of non-ready nodes exceeds 45%, protection will be triggered. In this case, all nodes with the **ignore-taint.cluster-autoscaler.kubernetes.io** taint in the cluster are filtered out from the autoscaler template and recorded as non-ready nodes, which affects cluster scaling.
   -  **cluster-autoscaler.kubernetes.io/enable-ds-eviction**: The annotation works on pods, which determines whether DaemonSet pods can be evicted by autoscaler. For details, see `Well-Known Labels, Annotations and Taints <https://kubernetes.io/docs/reference/labels-annotations-taints/#enable-ds-eviction>`__.

Installing the Add-on
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console. Choose **Add-ons** in the navigation pane, locate **CCE Cluster Autoscaler** on the right, and click **Install**.

#. On the **Install Add-on** page, configure the specifications.

   .. table:: **Table 1** Add-on configuration

      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                     |
      +===================================+=================================================================================================================================================================================================================+
      | Pods                              | Number of pods for the add-on.                                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                 |
      |                                   | High availability is not possible with a single pod. If an error occurs on the node where the add-on instance runs, the add-on will fail.                                                                       |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Multi-AZ                          | -  **Preferred**: Deployment pods of the add-on will be preferentially scheduled to nodes in different AZs. If all the nodes in the cluster are deployed in the same AZ, the pods will be scheduled to that AZ. |
      |                                   | -  **Required**: Deployment pods of the add-on will be forcibly scheduled to nodes in different AZs. If there are fewer AZs than pods, the extra pods will fail to run.                                         |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Containers                        | Adjust the number of the Autoscaler pods and their CPU and memory quotas based on the cluster scale. For details, see :ref:`Table 2 <cce_10_0154__table874811472078>`.                                          |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0154__table874811472078:

   .. table:: **Table 2** Recommended quotas for the add-on

      ===== ==== =============== ========== ================ ============
      Nodes Pods Requested vCPUs vCPU Limit Requested Memory Memory Limit
      ===== ==== =============== ========== ================ ============
      50    2    1000m           1000m      1000Mi           1000Mi
      200   2    4000m           4000m      2000Mi           2000Mi
      1000  2    8000m           8000m      8000Mi           8000Mi
      2000  2    8000m           8000m      8000Mi           8000Mi
      ===== ==== =============== ========== ================ ============

#. Configure the add-on parameters.

   .. table:: **Table 3** Add-on parameters

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                            |
      +===================================+========================================================================================================================================================================================================================================================================================================================================================================+
      | Scaling                           | You can select the following options as required:                                                                                                                                                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   | -  **Nodes are automatically added (from the node pool) when pods in the cluster cannot be scheduled.**                                                                                                                                                                                                                                                                |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    That is, when a pod is in **Pending** state, automatic scale-out is performed. If a node has been configured as an affinity node for pods, no node will not be automatically added when pods cannot be scheduled. Generally, an HPA policy works with such scaling. For details, see :ref:`Using HPA and CA for Auto Scaling of Workloads and Nodes <cce_10_0300>`. |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    If this parameter is not selected, scaling can be performed only through :ref:`node scaling policies <cce_10_0209>`.                                                                                                                                                                                                                                                |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   | -  Auto node scale-in                                                                                                                                                                                                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    -  **Node Idle Time (min)**: Time for which a node should be unneeded before it is eligible for scale-down. Default value: 10 minutes.                                                                                                                                                                                                                              |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    -  **Scale-in Threshold**: If the percentage of both requested CPU and memory on a node is below this threshold, auto scale-down will be triggered to delete the node from the cluster. The default value is 0.5, which means 50%.                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    -  **Stabilization Window (s)**                                                                                                                                                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |       How long after a scale-out that a scale-in evaluation resumes. Default value: 10 minutes.                                                                                                                                                                                                                                                                        |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |       .. note::                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |          If both auto scale-out and scale-in exist in a cluster, set **How long after a scale-out that a scale-in evaluation resumes** to 0 minutes. This can prevent the node scale-in from being blocked due to continuous scale-out of some node pools or retries upon a scale-out failure, resulting in unexpected waste of node resources.                        |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |       How long after the node deletion that a scale-in evaluation resumes. Default value: 10 minutes.                                                                                                                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |       How long after a scale-in failure that a scale-in evaluation resumes. Default value: 3 minutes. For details about the impact and relationship between the scale-in cooling intervals configured in the node pool and autoscaler, see :ref:`Scale-In Cool-Down Period <cce_10_0154__section59676731017>`.                                                         |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    -  **Max. Nodes for Batch Deletion**: Maximum number of empty nodes that can be deleted at the same time. Default value: 10.                                                                                                                                                                                                                                        |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |       This feature applies only to idle nodes. Idle nodes can be concurrently scaled in. Nodes that are not idle can only be scaled in one by one.                                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |       .. note::                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |          During node scale-in, if the pod on the node does not need to be evicted (such as the pods of DaemonSet), the node is idle. Otherwise, the node is not idle.                                                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    -  **Check Interval**: Interval for checking again a node that could not be removed before. Default value: 5 minutes.                                                                                                                                                                                                                                               |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Total Nodes                       | Maximum number of nodes that can be managed by the cluster, within which cluster scale-out is performed.                                                                                                                                                                                                                                                               |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Total CPUs                        | Maximum sum of CPU cores of all nodes in a cluster, within which cluster scale-out is performed.                                                                                                                                                                                                                                                                       |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Total Memory (GB)                 | Maximum sum of memory of all nodes in a cluster, within which cluster scale-out is performed.                                                                                                                                                                                                                                                                          |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. After the configuration is complete, click **Install**.

Components
----------

.. table:: **Table 4** autoscaler

   =================== ==================================== =============
   Container Component Description                          Resource Type
   =================== ==================================== =============
   autoscaler          Auto scaling for Kubernetes clusters Deployment
   =================== ==================================== =============

.. _cce_10_0154__section59676731017:

Scale-In Cool-Down Period
-------------------------

Scale-in cooling intervals can be configured in the node pool settings and the autoscaler add-on settings.

**Scale-in cooling interval configured in a node pool**

This interval indicates the period during which nodes added to the current node pool after a scale-out operation cannot be deleted. This interval takes effect at the node pool level.

**Scale-in cooling interval configured in the autoscaler add-on**

The interval after a scale-out indicates the period during which the entire cluster cannot be scaled in after the autoscaler add-on triggers scale-out (due to the unschedulable pods, metrics, and scaling policies). This interval takes effect at the cluster level.

The interval after a node is deleted indicates the period during which the cluster cannot be scaled in after the autoscaler add-on triggers scale-in. This interval takes effect at the cluster level.

The interval after a failed scale-in indicates the period during which the cluster cannot be scaled in after the autoscaler add-on triggers scale-in. This interval takes effect at the cluster level.
