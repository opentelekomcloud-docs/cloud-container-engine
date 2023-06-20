:original_name: cce_10_0154.html

.. _cce_10_0154:

autoscaler
==========

Introduction
------------

Autoscaler is an important Kubernetes controller. It supports microservice scaling and is key to serverless design.

When the CPU or memory usage of a microservice is too high, horizontal pod autoscaling is triggered to add pods to reduce the load. These pods can be automatically reduced when the load is low, allowing the microservice to run as efficiently as possible.

CCE simplifies the creation, upgrade, and manual scaling of Kubernetes clusters, in which traffic loads change over time. To balance resource usage and workload performance of nodes, Kubernetes introduces the autoscaler add-on to automatically resize a cluster based on the resource usage required for workloads deployed in the cluster. For details, see :ref:`Creating a Node Scaling Policy <cce_10_0209>`.

Open source community: https://github.com/kubernetes/autoscaler

How the Add-on Works
--------------------

autoscaler controls auto scale-out and scale-in.

-  **Auto scale-out**

   You can choose either of the following methods:

   -  If pods in a cluster cannot be scheduled due to insufficient worker nodes, cluster scaling is triggered to add nodes. The nodes to be added have the same specification as configured for the node pool to which the nodes belong.

      Auto scale-out will be performed when:

      -  Node resources are insufficient.
      -  No node affinity policy is set in the pod scheduling configuration. That is, if a node has been configured as an affinity node for pods, no node will not be automatically added when pods cannot be scheduled. For details about how to configure the node affinity policy, see :ref:`Scheduling Policy (Affinity/Anti-affinity) <cce_10_0232>`.

   -  When the cluster meets the node scaling policy, cluster scale-out is also triggered. For details, see :ref:`Creating a Node Scaling Policy <cce_10_0209>`.

   .. note::

      The add-on follows the "No Less, No More" policy. For example, if three cores are required for creating a pod and the system supports four-core and eight-core nodes, autoscaler will preferentially create a four-core node.

-  **Auto scale-in**

   When a cluster node is idle for a period of time (10 minutes by default), cluster scale-in is triggered, and the node is automatically deleted. However, a node cannot be deleted from a cluster if the following pods exist:

   -  Pods that do not meet specific requirements set in PodDisruptionBudget
   -  Pods that cannot be scheduled to other nodes due to constraints such as affinity and anti-affinity policies
   -  Pods that have the **cluster-autoscaler.kubernetes.io/safe-to-evict: 'false'** annotation
   -  Pods (except those created by kube-system DaemonSet) that exist in the kube-system namespace on the node
   -  Pods that are not created by the controller (Deployment/ReplicaSet/job/StatefulSet)

Notes and Constraints
---------------------

-  Only clusters of v1.9.10-r2 and later support autoscaler.
-  Ensure that there are sufficient resources for installing the add-on.
-  The default node pool does not support auto scaling. For details, see :ref:`Description of DefaultPool <cce_10_0081__section16928123042115>`.

Installing the Add-on
---------------------

#. Log in to the CCE console, click the cluster name, and access the cluster console. Choose **Add-ons** in the navigation pane, locate **autoscaler** on the right, and click **Install**.
#. Configure add-on installation parameters.

   .. table:: **Table 1** Specifications configuration

      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                            |
      +===================================+========================================================================================================================================================================================================================================================================================================================================================+
      | Add-on Specifications             | The add-on can be deployed in the following specifications:                                                                                                                                                                                                                                                                                            |
      |                                   |                                                                                                                                                                                                                                                                                                                                                        |
      |                                   | .. note::                                                                                                                                                                                                                                                                                                                                              |
      |                                   |                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    When the autoscaler add-on is deployed in HA or customized mode, anti-affinity policies exist between add-on instances and the add-on instances are deployed on different nodes. Therefore, the number of available nodes in the cluster must be greater than or equal to the number of add-on instances to ensure high availability of the add-on. |
      |                                   |                                                                                                                                                                                                                                                                                                                                                        |
      |                                   | -  **Single**: The add-on is deployed with only one pod.                                                                                                                                                                                                                                                                                               |
      |                                   | -  **HA50**: The add-on is deployed with two pods, serving a cluster with 50 nodes and ensuring high availability.                                                                                                                                                                                                                                     |
      |                                   | -  **HA200**: The add-on is deployed with two pods, serving a cluster with 50 nodes and ensuring high availability. Each pod uses more resources than those of the **HA50** specification.                                                                                                                                                             |
      |                                   | -  **Custom**: You can customize the number of pods and specifications as required.                                                                                                                                                                                                                                                                    |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Multi AZ                          | -  **Preferred**: Deployment pods of the add-on are preferentially scheduled to nodes in different AZs. If the nodes in the cluster do not meet the requirements of multiple AZs, the pods are scheduled to a single AZ.                                                                                                                               |
      |                                   | -  **Required**: Deployment pods of the add-on are forcibly scheduled to nodes in different AZs. If the nodes in the cluster do not meet the requirements of multiple AZs, not all pods can run.                                                                                                                                                       |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. table:: **Table 2** Parameter configuration

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
      |                                   |          If both auto scale-out and scale-in exist in a cluster, you are advised to set **How long after a scale-out that a scale-in evaluation resumes** to 0 minutes. This can prevent the node scale-in from being blocked due to continuous scale-out of some node pools or retries upon a scale-out failure, resulting in unexpected waste of node resources.     |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |       How long after the node deletion that a scale-in evaluation resumes. Default value: 10 minutes.                                                                                                                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |       How long after a scale-in failure that a scale-in evaluation resumes. Default value: 3 minutes. For details about the impact and relationship between the scale-in cooling intervals configured in the node pool and autoscaler, see :ref:`Description of the Scale-In Cool-Down Period <cce_10_0154__section59676731017>`.                                      |
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

#. When the configuration is complete, click **Install**.

.. _cce_10_0154__section59676731017:

Description of the Scale-In Cool-Down Period
--------------------------------------------

Scale-in cooling intervals can be configured in the node pool settings and the autoscaler add-on settings.

**Scale-in cooling interval configured in a node pool**

This interval indicates the period during which nodes added to the current node pool after a scale-out operation cannot be deleted. This interval takes effect at the node pool level.

**Scale-in cooling interval configured in the autoscaler add-on**

The interval after a scale-out indicates the period during which the entire cluster cannot be scaled in after the autoscaler add-on triggers scale-out (due to the unschedulable pods, metrics, and scaling policies). This interval takes effect at the cluster level.

The interval after a node is deleted indicates the period during which the cluster cannot be scaled in after the autoscaler add-on triggers scale-in. This interval takes effect at the cluster level.

The interval after a failed scale-in indicates the period during which the cluster cannot be scaled in after the autoscaler add-on triggers scale-in. This interval takes effect at the cluster level.
