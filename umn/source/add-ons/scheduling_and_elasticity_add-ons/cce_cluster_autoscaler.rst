:original_name: cce_10_0154.html

.. _cce_10_0154:

CCE Cluster Autoscaler
======================

Introduction
------------

Autoscaler is an important Kubernetes controller. It supports microservice scaling and is key to serverless design.

When the CPU or memory usage of a microservice is too high, horizontal pod autoscaling is triggered to add pods to reduce the load. These pods can be automatically reduced when the load is low, allowing the microservice to run as efficiently as possible.

CCE simplifies the creation, upgrade, and manual scaling of Kubernetes clusters, in which traffic loads change over time. To balance resource usage and workload performance of nodes, Kubernetes introduces the Autoscaler add-on to automatically adjust the number of nodes a cluster based on the resource usage required for workloads deployed in the cluster. For details, see :ref:`Creating a Node Scaling Policy <cce_10_0209>`.

Open source community: https://github.com/kubernetes/autoscaler

How the Add-on Works
--------------------

Autoscaler controls auto scale-out and scale-in.

-  **Auto scale-out**

   You can choose either of the following methods:

   -  If pods in a cluster cannot be scheduled due to insufficient worker nodes, cluster scaling is triggered to add nodes. The nodes to be added have the same specification as configured for the node pool to which the nodes belong.

      Auto scale-out will be performed when:

      -  Node resources are insufficient.
      -  No node affinity policy is set in the pod scheduling configuration. If a node has been configured as an affinity node for pods, no node will not be automatically added when pods cannot be scheduled. For details about how to configure the node affinity policy, see :ref:`Scheduling Policies (Affinity/Anti-affinity) <cce_10_0232>`.

   -  When the cluster meets the node scaling policy, cluster scale-out is also triggered. For details, see :ref:`Creating a Node Scaling Policy <cce_10_0209>`.

   .. note::

      The add-on follows the "No Less, No More" policy. For example, if three cores are required for creating a pod and the system supports four-core and eight-core nodes, Autoscaler will preferentially create a four-core node.

-  **Auto scale-in**

   When a cluster node is idle for a period of time (10 minutes by default), cluster scale-in is triggered, and the node is automatically deleted. However, a node cannot be deleted from a cluster if the following pods exist:

   -  Pods that do not meet specific requirements set in Pod Disruption Budgets (`PodDisruptionBudget <https://kubernetes.io/docs/tasks/run-application/configure-pdb/>`__)
   -  Pods that cannot be scheduled to other nodes due to constraints such as affinity and anti-affinity policies
   -  Pods that have the **cluster-autoscaler.kubernetes.io/safe-to-evict: 'false'** annotation
   -  Pods (except those created by DaemonSets in the kube-system namespace) that exist in the kube-system namespace on the node
   -  Pods that are not created by the controller (Deployment/ReplicaSet/job/StatefulSet)

   .. note::

      When a node meets the scale-in conditions, Autoscaler adds the **DeletionCandidateOfClusterAutoscaler** taint to the node in advance to prevent pods from being scheduled to the node. After the Autoscaler add-on is uninstalled, if the taint still exists on the node, manually delete it.

Notes and Constraints
---------------------

-  Ensure that there are sufficient resources for installing the add-on.
-  The default node pool does not support auto scaling. For details, see :ref:`Description of DefaultPool <cce_10_0081__section16928123042115>`.
-  Node scale-in will cause PVC/PV data loss for the :ref:`local PVs <cce_10_0391>` associated with the node. These PVCs and PVs cannot be restored or used again. In a node scale-in, a pod that uses the local PV will be evicted from the node. A new pod will be created, but it remains in a pending state because the label of the PVC bound to it conflicts with the node label.
-  When Autoscaler is used, some taints or annotations may affect auto scaling. Therefore, do not use the following taints or annotations in clusters:

   -  **ignore-taint.cluster-autoscaler.kubernetes.io**: The taint works on nodes. Kubernetes-native Autoscaler supports protection against abnormal scale outs and periodically evaluates the proportion of available nodes in the cluster. When the proportion of non-ready nodes exceeds 45%, protection will be triggered. In this case, all nodes with the **ignore-taint.cluster-autoscaler.kubernetes.io** taint in the cluster are filtered out from the Autoscaler template and recorded as non-ready nodes, which affect cluster scaling.
   -  **cluster-autoscaler.kubernetes.io/enable-ds-eviction**: The annotation works on pods, which determines whether DaemonSet pods can be evicted by Autoscaler. For details, see `Well-Known Labels, Annotations and Taints <https://kubernetes.io/docs/reference/labels-annotations-taints/#enable-ds-eviction>`__.

Installing the Add-on
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console. Choose **Add-ons** in the navigation pane, locate **CCE Cluster Autoscaler** on the right, and click **Install**.

#. On the **Install Add-on** page, configure the specifications.

   .. table:: **Table 1** Specifications configuration

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                            |
      +===================================+========================================================================================================================================================================+
      | Pods                              | Number of pods for the add-on.                                                                                                                                         |
      |                                   |                                                                                                                                                                        |
      |                                   | High availability is not possible with a single pod. If an error occurs on the node where the add-on instance runs, the add-on will fail.                              |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Containers                        | Adjust the number of the Autoscaler pods and their CPU and memory quotas based on the cluster scale. For details, see :ref:`Table 2 <cce_10_0154__table874811472078>`. |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0154__table874811472078:

   .. table:: **Table 2** Recommended Autoscaler quotas

      ===== ==== =============== ========== ================ ============
      Nodes Pods Requested vCPUs vCPU Limit Requested Memory Memory Limit
      ===== ==== =============== ========== ================ ============
      50    2    1000m           1000m      1000 MiB         1000 MiB
      200   2    4000m           4000m      2000 MiB         2000 MiB
      1000  2    8000m           8000m      8000 MiB         8000 MiB
      2000  2    8000m           8000m      8000 MiB         8000 MiB
      ===== ==== =============== ========== ================ ============

#. Configure the add-on parameters.

   .. table:: **Table 3** Parameters

      +--------------------+----------------------------------------------------------------------------------------------------------+
      | Parameter          | Description                                                                                              |
      +====================+==========================================================================================================+
      | Total Nodes        | Maximum number of nodes that can be managed by the cluster, within which cluster scale-out is performed. |
      +--------------------+----------------------------------------------------------------------------------------------------------+
      | Total CPUs         | Maximum sum of CPU cores of all nodes in a cluster, within which cluster scale-out is performed.         |
      +--------------------+----------------------------------------------------------------------------------------------------------+
      | Total Memory (GiB) | Maximum sum of memory of all nodes in a cluster, within which cluster scale-out is performed.            |
      +--------------------+----------------------------------------------------------------------------------------------------------+

#. Configure scheduling policies for the add-on.

   .. note::

      -  Scheduling policies do not take effect on add-on instances of the DaemonSet type.
      -  When configuring multi-AZ deployment or node affinity, ensure that there are nodes meeting the scheduling policy and that resources are sufficient in the cluster. Otherwise, the add-on cannot run.

   .. table:: **Table 4** Configurations for add-on scheduling

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                    |
      +===================================+================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | Multi AZ                          | -  **Preferred**: Deployment pods of the add-on will be preferentially scheduled to nodes in different AZs. If all the nodes in the cluster are deployed in the same AZ, the pods will be scheduled to that AZ.                                                                                                                                                                                                                                |
      |                                   | -  **Equivalent mode**: Deployment pods of the add-on are evenly scheduled to the nodes in the cluster in each AZ. If a new AZ is added, you are advised to increase add-on pods for cross-AZ HA deployment. With the Equivalent multi-AZ deployment, the difference between the number of add-on pods in different AZs will be less than or equal to 1. If resources in one of the AZs are insufficient, pods cannot be scheduled to that AZ. |
      |                                   | -  **Required**: Deployment pods of the add-on will be forcibly scheduled to nodes in different AZs. If there are fewer AZs than pods, the extra pods will fail to run.                                                                                                                                                                                                                                                                        |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Node Affinity                     | -  **Not configured**: Node affinity is disabled for the add-on.                                                                                                                                                                                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Node Affinity**: Specify the nodes where the add-on is deployed. If you do not specify the nodes, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Specified Node Pool Scheduling**: Specify the node pool where the add-on is deployed. If you do not specify the node pool, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                                                          |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Custom Policies**: Enter the labels of the nodes where the add-on is to be deployed for more flexible scheduling policies. If you do not specify node labels, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                       |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   |    If multiple custom affinity policies are configured, ensure that there are nodes that meet all the affinity policies in the cluster. Otherwise, the add-on cannot run.                                                                                                                                                                                                                                                                      |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Toleration                        | Using both taints and tolerations allows (not forcibly) the add-on Deployment to be scheduled to a node with the matching taints, and controls the Deployment eviction policies after the node where the Deployment is located is tainted.                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | The add-on adds the default tolerance policy for the **node.kubernetes.io/not-ready** and **node.kubernetes.io/unreachable** taints, respectively. The tolerance time window is 60s.                                                                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | For details, see :ref:`Configuring Tolerance Policies <cce_10_0728>`.                                                                                                                                                                                                                                                                                                                                                                          |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. After the configuration is complete, click **Install**.

Components
----------

.. table:: **Table 5** Add-on components

   ========== ==================================== =============
   Component  Description                          Resource Type
   ========== ==================================== =============
   Autoscaler Auto scaling for Kubernetes clusters Deployment
   ========== ==================================== =============

Scale-In Cool-Down Period
-------------------------

Scale-in cooling intervals can be configured in the node pool settings and the Autoscaler add-on settings.

**Scale-in cooling interval configured in a node pool**

This interval indicates the period during which nodes added to the current node pool after a scale-out cannot be deleted. This setting takes effect in the entire node pool.

**Scale-in cooling interval configured in the Autoscaler add-on**

The interval after a scale-out indicates the period during which the entire cluster cannot be scaled in after the Autoscaler add-on triggers a scale-out (due to the unschedulable pods, metrics, and scaling policies). This setting takes effect in the entire cluster.

The interval after a node is deleted indicates the period during which the cluster cannot be scaled in after the auto scaling add-on triggers a scale-in. This setting takes effect in the entire cluster.

The interval after a failed scale-in indicates the period during which the cluster cannot be scaled in after the Autoscaler add-on triggers scale-in. This interval takes effect at the cluster level.

Change History
--------------

.. table:: **Table 6** Updates of the add-on adapted to clusters 1.29

   +----------------+---------------------------+------------------------------+----------------------------------------------------------------------------------------------+
   | Add-on Version | Supported Cluster Version | New Feature                  | Community Version                                                                            |
   +================+===========================+==============================+==============================================================================================+
   | 1.29.17        | v1.29                     | Optimized events.            | `1.29.1 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.29.1>`__ |
   +----------------+---------------------------+------------------------------+----------------------------------------------------------------------------------------------+
   | 1.29.13        | v1.29                     | Clusters 1.29 are supported. | `1.29.1 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.29.1>`__ |
   +----------------+---------------------------+------------------------------+----------------------------------------------------------------------------------------------+

.. table:: **Table 7** Updates of the add-on adapted to clusters 1.28

   +----------------+---------------------------+-------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | Add-on Version | Supported Cluster Version | New Feature                                                                                           | Community Version                                                                            |
   +================+===========================+=======================================================================================================+==============================================================================================+
   | 1.28.55        | v1.28                     | Optimized events.                                                                                     | `1.28.1 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.28.1>`__ |
   +----------------+---------------------------+-------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.28.22        | v1.28                     | Fixed some issues.                                                                                    | `1.28.1 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.28.1>`__ |
   +----------------+---------------------------+-------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.28.20        | v1.28                     | Fixed some issues.                                                                                    | `1.28.1 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.28.1>`__ |
   +----------------+---------------------------+-------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.28.17        | v1.28                     | Fixed the issue that scale-in cannot be performed when there are custom pod controllers in a cluster. | `1.28.1 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.28.1>`__ |
   +----------------+---------------------------+-------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+

.. table:: **Table 8** Updates of the add-on adapted to clusters 1.27

   +----------------+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | Add-on Version | Supported Cluster Version | New Feature                                                                                                                             | Community Version                                                                            |
   +================+===========================+=========================================================================================================================================+==============================================================================================+
   | 1.27.55        | v1.27                     | Fixed some issues.                                                                                                                      | `1.27.1 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.27.1>`__ |
   +----------------+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.27.53        | v1.27                     | Fixed some issues.                                                                                                                      | `1.27.1 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.27.1>`__ |
   +----------------+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.27.51        | v1.27                     | Fixed some issues.                                                                                                                      | `1.27.1 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.27.1>`__ |
   +----------------+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.27.14        | v1.27                     | Fixed the scale-in failure of nodes of different specifications in the same node pool and unexpected **PreferNoSchedule** taint issues. | `1.27.1 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.27.1>`__ |
   +----------------+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+

.. table:: **Table 9** Updates of the add-on adapted to clusters 1.25

   +-----------------+---------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | Add-on Version  | Supported Cluster Version | New Feature                                                                                                                                       | Community Version                                                                            |
   +=================+===========================+===================================================================================================================================================+==============================================================================================+
   | 1.25.88         | v1.25                     | Fixed some issues.                                                                                                                                | `1.25.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.25.0>`__ |
   +-----------------+---------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.25.86         | v1.25                     | Fixed some issues.                                                                                                                                | `1.25.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.25.0>`__ |
   +-----------------+---------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.25.84         | v1.25                     | Fixed some issues.                                                                                                                                | `1.25.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.25.0>`__ |
   +-----------------+---------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.25.46         | v1.25                     | Fixed the scale-in failure of nodes of different specifications in the same node pool and unexpected **PreferNoSchedule** taint issues.           | `1.25.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.25.0>`__ |
   +-----------------+---------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.25.21         | v1.25                     | -  Fixed the issue that the autoscaler's least-waste is disabled by default.                                                                      | `1.25.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.25.0>`__ |
   |                 |                           | -  Fixed the issue that the node pool cannot be switched to another pool for scaling out after a scale-out failure and the add-on has to restart. |                                                                                              |
   |                 |                           | -  The default taint tolerance duration is changed to 60s.                                                                                        |                                                                                              |
   |                 |                           | -  Fixed the issue that scale-out is still triggered after the scale-out rule is disabled.                                                        |                                                                                              |
   +-----------------+---------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.25.7          | v1.25                     | -  CCE clusters 1.25 are supported.                                                                                                               | `1.25.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.25.0>`__ |
   |                 |                           | -  Modified the memory request and limit of a customized flavor.                                                                                  |                                                                                              |
   |                 |                           | -  Enabled to report an event indicating that scaling cannot be performed in a node pool with auto scaling disabled.                              |                                                                                              |
   +-----------------+---------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+

.. table:: **Table 10** Updates of the add-on adapted to clusters 1.23

   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | Add-on Version  | Supported Cluster Version | New Feature                                                                                                                                                 | Community Version                                                                            |
   +=================+===========================+=============================================================================================================================================================+==============================================================================================+
   | 1.23.95         | v1.23                     | Fixed some issues.                                                                                                                                          | `1.23.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.23.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.23.93         | v1.23                     | Fixed some issues.                                                                                                                                          | `1.23.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.23.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.23.91         | v1.23                     | Fixed some issues.                                                                                                                                          | `1.23.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.23.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.23.54         | v1.23                     | Fixed the scale-in failure of nodes of different specifications in the same node pool and unexpected **PreferNoSchedule** taint issues.                     | `1.23.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.23.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.23.31         | v1.23                     | -  Fixed the issue that the autoscaler's least-waste is disabled by default.                                                                                | `1.23.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.23.0>`__ |
   |                 |                           | -  Fixed the issue that the node pool cannot be switched to another pool for scaling out after a scale-out failure and the add-on has to restart.           |                                                                                              |
   |                 |                           | -  The default taint tolerance duration is changed to 60s.                                                                                                  |                                                                                              |
   |                 |                           | -  Fixed the issue that scale-out is still triggered after the scale-out rule is disabled.                                                                  |                                                                                              |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.23.17         | v1.23                     | -  Supported node scaling policies without a step.                                                                                                          | `1.23.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.23.0>`__ |
   |                 |                           | -  Fixed a bug so that deleted node pools are automatically removed.                                                                                        |                                                                                              |
   |                 |                           | -  Supported scheduling by priority.                                                                                                                        |                                                                                              |
   |                 |                           | -  Supported the emptyDir scheduling policy.                                                                                                                |                                                                                              |
   |                 |                           | -  Fixed a bug so that scale-in can be triggered on the nodes whose capacity is lower than the scale-in threshold when the node scaling policy is disabled. |                                                                                              |
   |                 |                           | -  Modified the memory request and limit of a customized flavor.                                                                                            |                                                                                              |
   |                 |                           | -  Enabled to report an event indicating that scaling cannot be performed in a node pool with auto scaling disabled.                                        |                                                                                              |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.23.10         | v1.23                     | -  Optimized logging.                                                                                                                                       | `1.23.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.23.0>`__ |
   |                 |                           | -  Supported scale-in waiting so that operations such as data dump can be performed before a node is deleted.                                               |                                                                                              |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+

.. table:: **Table 11** Updates of the add-on adapted to clusters 1.21

   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | Add-on Version  | Supported Cluster Version | New Feature                                                                                                                                                 | Community Version                                                                            |
   +=================+===========================+=============================================================================================================================================================+==============================================================================================+
   | 1.21.89         | v1.21                     | Fixed some issues.                                                                                                                                          | `1.21.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.21.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.21.87         | v1.21                     | Fixed some issues.                                                                                                                                          | `1.21.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.21.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.21.86         | v1.21                     | Fixed the issue that the node pool auto scaling cannot meet expectations after AZ topology constraints are configured for nodes.                            | `1.21.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.21.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.21.51         | v1.21                     | Fixed the scale-in failure of nodes of different specifications in the same node pool and unexpected **PreferNoSchedule** taint issues.                     | `1.21.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.21.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.21.29         | v1.21                     | -  Supported anti-affinity scheduling of add-on pods on nodes in different AZs.                                                                             | `1.21.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.21.0>`__ |
   |                 |                           | -  Added the tolerance time during which the pods with temporary storage volumes cannot be scheduled.                                                       |                                                                                              |
   |                 |                           | -  Fixed the issue that the number of node pools cannot be restored when scaling group resources are insufficient.                                          |                                                                                              |
   |                 |                           | -  Fixed the issue that the node pool cannot be switched to another pool for scaling out after a scale-out failure and the add-on has to restart.           |                                                                                              |
   |                 |                           | -  The default taint tolerance duration is changed to 60s.                                                                                                  |                                                                                              |
   |                 |                           | -  Fixed the issue that scale-out is still triggered after the scale-out rule is disabled.                                                                  |                                                                                              |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.21.16         | v1.21                     | -  Supported node scaling policies without a step.                                                                                                          | `1.21.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.21.0>`__ |
   |                 |                           | -  Fixed a bug so that deleted node pools are automatically removed.                                                                                        |                                                                                              |
   |                 |                           | -  Supported scheduling by priority.                                                                                                                        |                                                                                              |
   |                 |                           | -  Supported the emptyDir scheduling policy.                                                                                                                |                                                                                              |
   |                 |                           | -  Fixed a bug so that scale-in can be triggered on the nodes whose capacity is lower than the scale-in threshold when the node scaling policy is disabled. |                                                                                              |
   |                 |                           | -  Modified the memory request and limit of a customized flavor.                                                                                            |                                                                                              |
   |                 |                           | -  Enabled to report an event indicating that scaling cannot be performed in a node pool with auto scaling disabled.                                        |                                                                                              |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.21.9          | v1.21                     | -  Optimized logging.                                                                                                                                       | `1.21.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.21.0>`__ |
   |                 |                           | -  Supported scale-in waiting so that operations such as data dump can be performed before a node is deleted.                                               |                                                                                              |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.21.1          | v1.21                     | Fixed the issue that the node pool modification in the existing periodic auto scaling rule does not take effect.                                            | `1.21.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.21.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+

.. table:: **Table 12** Updates of the add-on adapted to clusters 1.19

   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | Add-on Version  | Supported Cluster Version | New Feature                                                                                                                                                 | Community Version                                                                            |
   +=================+===========================+=============================================================================================================================================================+==============================================================================================+
   | 1.19.76         | v1.19                     | -  Optimized the method of identifying GPUs and NPUs.                                                                                                       | `1.19.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.19.0>`__ |
   |                 |                           | -  Used the remaining node quota of a cluster for the extra nodes that are beyond the cluster scale.                                                        |                                                                                              |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.19.56         | v1.19                     | Fixed the scale-in failure of nodes of different specifications in the same node pool and unexpected **PreferNoSchedule** taint issues.                     | `1.19.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.19.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.19.35         | v1.19                     | -  Supported anti-affinity scheduling of add-on pods on nodes in different AZs.                                                                             | `1.19.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.19.0>`__ |
   |                 |                           | -  Added the tolerance time during which the pods with temporary storage volumes cannot be scheduled.                                                       |                                                                                              |
   |                 |                           | -  Fixed the issue that the number of node pools cannot be restored when scaling group resources are insufficient.                                          |                                                                                              |
   |                 |                           | -  Fixed the issue that the node pool cannot be switched to another pool for scaling out after a scale-out failure and the add-on has to restart.           |                                                                                              |
   |                 |                           | -  The default taint tolerance duration is changed to 60s.                                                                                                  |                                                                                              |
   |                 |                           | -  Fixed the issue that scale-out is still triggered after the scale-out rule is disabled.                                                                  |                                                                                              |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.19.22         | v1.19                     | -  Supported node scaling policies without a step.                                                                                                          | `1.19.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.19.0>`__ |
   |                 |                           | -  Fixed a bug so that deleted node pools are automatically removed.                                                                                        |                                                                                              |
   |                 |                           | -  Supported scheduling by priority.                                                                                                                        |                                                                                              |
   |                 |                           | -  Supported the emptyDir scheduling policy.                                                                                                                |                                                                                              |
   |                 |                           | -  Fixed a bug so that scale-in can be triggered on the nodes whose capacity is lower than the scale-in threshold when the node scaling policy is disabled. |                                                                                              |
   |                 |                           | -  Modified the memory request and limit of a customized flavor.                                                                                            |                                                                                              |
   |                 |                           | -  Enabled to report an event indicating that scaling cannot be performed in a node pool with auto scaling disabled.                                        |                                                                                              |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.19.14         | v1.19                     | -  Optimized logging.                                                                                                                                       | `1.19.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.19.0>`__ |
   |                 |                           | -  Supported scale-in waiting so that operations such as data dump can be performed before a node is deleted.                                               |                                                                                              |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.19.11         | v1.19                     | Fixed the issue that authentication fails due to incorrect signature in the add-on request retries.                                                         | `1.19.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.19.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.19.8          | v1.19                     | Fixed the issue that the node pool modification in the existing periodic auto scaling rule does not take effect.                                            | `1.19.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.19.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.19.7          | v1.19                     | Regular upgrade of add-on dependencies                                                                                                                      | `1.19.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.19.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+

.. table:: **Table 13** Updates of the add-on adapted to clusters 1.17

   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | Add-on Version  | Supported Cluster Version | New Feature                                                                                                                                                 | Community Version                                                                            |
   +=================+===========================+=============================================================================================================================================================+==============================================================================================+
   | 1.17.27         | v1.17                     | -  Optimized logging.                                                                                                                                       | `1.17.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.17.0>`__ |
   |                 |                           | -  Fixed a bug so that deleted node pools are automatically removed.                                                                                        |                                                                                              |
   |                 |                           | -  Supported scheduling by priority.                                                                                                                        |                                                                                              |
   |                 |                           | -  Fixed the issue that taints on newly added nodes are overwritten.                                                                                        |                                                                                              |
   |                 |                           | -  Fixed a bug so that scale-in can be triggered on the nodes whose capacity is lower than the scale-in threshold when the node scaling policy is disabled. |                                                                                              |
   |                 |                           | -  Modified the memory request and limit of a customized flavor.                                                                                            |                                                                                              |
   |                 |                           | -  Enabled to report an event indicating that scaling cannot be performed in a node pool with auto scaling disabled.                                        |                                                                                              |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.17.22         | v1.17                     | Optimized logging.                                                                                                                                          | `1.17.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.17.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.17.19         | v1.17                     | Fixed the issue that authentication fails due to incorrect signature in the add-on request retries.                                                         | `1.17.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.17.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.17.16         | v1.17                     | Fixed the issue that the node pool modification in the existing periodic auto scaling rule does not take effect.                                            | `1.17.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.17.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.17.15         | v1.17                     | Unified resource specification configuration unit.                                                                                                          | `1.17.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.17.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
   | 1.17.2          | v1.17                     | Clusters 1.17 are supported.                                                                                                                                | `1.17.0 <https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.17.0>`__ |
   +-----------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------+
