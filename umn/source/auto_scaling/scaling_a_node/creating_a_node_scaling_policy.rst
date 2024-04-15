:original_name: cce_10_0209.html

.. _cce_10_0209:

Creating a Node Scaling Policy
==============================

CCE provides auto scaling through the :ref:`CCE Cluster Autoscaler <cce_10_0154>` add-on. Nodes with different flavors can be automatically added across AZs on demand.

If both a node scaling policy and the configuration in the auto scaling add-on take effect, for example, there are pods that cannot be scheduled and the value of a metric reaches the threshold, scale-out is performed first for the unschedulable pods.

-  If the scale-out succeeds for the unschedulable pods, the system skips the metric-based rule logic and enters the next loop.
-  If the scale-out fails for the unschedulable pods, the metric-based rule is executed.

Prerequisites
-------------

Before using the node scaling function, you must install the :ref:`CCE Cluster Autoscaler <cce_10_0154>` add-on of v1.13.8 or later in the cluster.

Constraints
-----------

-  Auto scaling policies apply to node pools. When the number of nodes in a node pool is 0 and the scaling policy is based on CPU or memory usage, node scaling is not triggered.
-  Node scale-in will cause PVC/PV data loss for the :ref:`local PVs <cce_10_0391>` associated with the node. These PVCs and PVs cannot be restored or used again. In a node scale-in, the pod that uses the local PV is evicted from the node. A new pod is created and stays in the pending state. This is because the PVC used by the pod has a node label, due to which the pod cannot be scheduled.
-  When Autoscaler is used, some taints or annotations may affect auto scaling. Therefore, do not use the following taints or annotations in clusters:

   -  **ignore-taint.cluster-autoscaler.kubernetes.io**: The taint works on nodes. Kubernetes-native Autoscaler supports protection against abnormal scale outs and periodically evaluates the proportion of available nodes in the cluster. When the proportion of non-ready nodes exceeds 45%, protection will be triggered. In this case, all nodes with the **ignore-taint.cluster-autoscaler.kubernetes.io** taint in the cluster are filtered out from the Autoscaler template and recorded as non-ready nodes, which affects cluster scaling.
   -  **cluster-autoscaler.kubernetes.io/enable-ds-eviction**: The annotation works on pods, which determines whether DaemonSet pods can be evicted by Autoscaler. For details, see `Well-Known Labels, Annotations and Taints <https://kubernetes.io/docs/reference/labels-annotations-taints/#enable-ds-eviction>`__.

Procedure
---------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Nodes**. On the **Node Pools** tab, locate the row containing the target node pool and click **Auto Scaling**.

   -  If the auto scaling add-on has not been installed, configure add-on parameters based on service requirements, click **Install**, and wait until the add-on is installed. For details about add-on configurations, see :ref:`CCE Cluster Autoscaler <cce_10_0154>`.
   -  If the auto scaling add-on has been installed, directly configure auto scaling policies.

#. Configure auto scaling policies.

   **AS Configuration**

   -  .. _cce_10_0209__li1193994013156:

      **Customize scale-out rules.**: Click **Add Rule**. In the dialog box displayed, configure parameters. You can add multiple node scaling rules, a maximum of one CPU usage-based rule, and one memory usage-based rule. The total number of rules cannot exceed 10.

      The following table lists custom rules.

      .. table:: **Table 1** Custom rules

         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Rule Type                         | Configuration                                                                                                                                                                                                                                                                                                                                                                                                           |
         +===================================+=========================================================================================================================================================================================================================================================================================================================================================================================================================+
         | Metric-based                      | -  **Trigger**: Select **CPU allocation rate** or **Memory allocation rate** and enter a value. The value must be greater than the scale-in percentage configured in the auto scaling add-on.                                                                                                                                                                                                                           |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                         |
         |                                   |    .. note::                                                                                                                                                                                                                                                                                                                                                                                                            |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                         |
         |                                   |       -  Resource allocation (%) = Resources requested by pods in the node pool/Resources allocatable to pods in the node pool                                                                                                                                                                                                                                                                                          |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                         |
         |                                   |       -  **If multiple rules meet the conditions, the rules are executed in either of the following modes:**                                                                                                                                                                                                                                                                                                            |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                         |
         |                                   |          If rules based on the **CPU allocation rate** and **memory allocation rate** are configured and two or more rules meet the scale-out conditions, the rule that will add the most nodes will be executed.                                                                                                                                                                                                       |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                         |
         |                                   |          If a rule based on the **CPU allocation rate** and **a periodic rule** are configured and they both meet the scale-out conditions, one of them will be executed randomly. The rule executed first (rule A) changes the node pool to the scaling state. As a result, the other rule (rule B) cannot be executed. After rule A is executed and the node pool status becomes normal, rule B will not be executed. |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                         |
         |                                   |       -  If rules based on the **CPU allocation rate** and **memory allocation rate** are configured, the policy detection period varies with the processing logic of each loop of the Autoscaler add-on. A scale-out is triggered once the conditions are met, but it is constrained by other factors such as the cooldown period and node pool status.                                                                |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                         |
         |                                   |       -  When the number of nodes in the cluster reaches the upper limit, or the CPU or memory usage reaches the upper limit of the autoscaler add-on, node scale-out will not be triggered.                                                                                                                                                                                                                            |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                         |
         |                                   | -  **Action**: Configure an action to be performed when the triggering condition is met.                                                                                                                                                                                                                                                                                                                                |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                         |
         |                                   |    -  **Custom**: Add a specified number of nodes to a node pool.                                                                                                                                                                                                                                                                                                                                                       |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                         |
         |                                   |    -  **Auto calculation**: When the trigger condition is met, nodes are automatically added and the allocation rate is restored to a value lower than the threshold. The formula is as follows:                                                                                                                                                                                                                        |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                         |
         |                                   |       Number of nodes to be added = [Resource request of pods in the node pool/(Available resources of a single node x Target allocation rate)] - Number of current nodes + 1                                                                                                                                                                                                                                           |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Periodic                          | -  **Trigger Time**: You can select a specific time every day, every week, every month, or every year.                                                                                                                                                                                                                                                                                                                  |
         |                                   | -  **Action**: specifies an action to be carried out when the trigger time is reached. A specified number of nodes will be added to the node pool.                                                                                                                                                                                                                                                                      |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  **Nodes**: The number of nodes in a node pool will always be within the range during auto scaling.

   -  **Cooldown Period**: a period during which the nodes added in the current node pool cannot be scaled in.

   **AS Object**

   **Specifications**: Configure whether to enable auto scaling for node flavors in a node pool.

#. View cluster-level auto scaling configurations, which take effect for all node pools in the cluster. On this page, you can only view cluster-level auto scaling policies. To modify these policies, go to the **Settings** page. For details, see :ref:`Configuring an Auto Scaling Policy for a Cluster <cce_10_0209__section1276410102184>`.

#. Click **OK**.

.. _cce_10_0209__section1276410102184:

Configuring an Auto Scaling Policy for a Cluster
------------------------------------------------

.. note::

   An auto scaling policy takes effect on all node pools in a cluster. After the policy is modified, the Autoscaler add-on will be restarted.

#. Log in to the CCE console and click the cluster name to access the details page.

#. In the navigation pane, choose **Settings** and click the **Auto Scaling** tab.

#. Configure for an elastic scale-out.

   -  **Auto Scale-out when the load cannot be scheduled**: When workload pods in a cluster cannot be scheduled (pods remain in pending state), CCE automatically adds nodes to the slave node pool. If a node has been configured to be affinity for pods, no node will not be automatically added when pods cannot be scheduled. Such auto scaling typically works with an HPA policy. For details, see :ref:`Using HPA and CA for Auto Scaling of Workloads and Nodes <cce_10_0300>`.

      If this function is not enabled, scaling can be performed only using :ref:`custom scaling policies <cce_10_0209__li1193994013156>`.

   -  **Upper limit of resources to be expanded**: Configure an upper limit for the total resources in the cluster. When the upper limit is reached, nodes will not be automatically added.

      .. note::

         When the total number of nodes, CPUs, and memory is collected, unavailable nodes in custom node pools are included but unavailable nodes in the default node pool are not included.

   -  **Scale-Out Priority**: You can drag and drop the node pools in a list to adjust their scale-out priorities.

#. Configure for an elastic scale-in. Elastic scale-in is disabled by default. After it is enabled, the following configurations are supported:

   **Node Scale-In Conditions**: Nodes in a cluster are automatically scaled in when the scale-in conditions are met.

   -  **Node Resource Condition**: When the requested cluster node resources (both CPU and memory) are lower than a certain percentage (50% by default) for a period of time (10 minutes by default), a cluster scale-in is triggered.
   -  **Node Status Condition**: If a node is unavailable for a specified period of time, the node will be automatically reclaimed. The default value is 20 minutes.
   -  **Scale-in Exception Scenarios**: When a node meets the following exception scenarios, CCE will not scale in the node even if the node resources or status meets scale-in conditions:

      a. Resources on other nodes in the cluster are insufficient.
      b. Scale-in protection is enabled on the node. To enable or disable node scale-in protection, choose **Nodes** in the navigation pane and then click the **Nodes** tab. Locate the target node, choose **More**, and then enable or disable node scale-in protection in the **Operation** column.
      c. There is a pod with the non-scale-in label on the node.
      d. Policies such as reliability have been configured for some pods on the node.
      e. There is a non-DaemonSet pod in the **kube-system** namespace of the node.
      f. (Optional) There is a pod created using CRD on the node, and this pod is managed by a third-party Pod Controller.

   **Node Scale-In Policy**

   -  **Number of Concurrent Scale-In Requests**: maximum number of idle nodes that can be concurrently deleted. Default value: 10.

      Only idle nodes can be concurrently scaled in. Nodes that are not idle can only be scaled in one by one.

      .. note::

         During a node scale-in, if the pods on the node do not need to be evicted (such as DaemonSet pods), the node is idle. Otherwise, the node is not idle.

   -  **Node Recheck Timeout**: interval for rechecking a node that could not be removed. Default value: 5 minutes.

   -  **Cooldown Time**

      -  **Scale-in Cooldown Time After Scale-out**: Default value: 10 minutes.

         .. note::

            If both auto scale-out and scale-in exist in a cluster, set **Scale-in Cooldown Time After Scale-out** to 0 minutes. This prevents the node scale-in from being blocked due to continuous scale-out of some node pools or retries upon a scale-out failure, which results in unexpected waste of node resources.

      -  **Scale-in Cooldown Time After Node Deletion**: Default value: 10 minutes.
      -  **Scale-in Cooldown Time After Failure**: Default value: 3 minutes. For details, see :ref:`Cooldown Period <cce_10_0209__section59676731017>`.

#. Click **Confirm configuration**.

.. _cce_10_0209__section59676731017:

Cooldown Period
---------------

The impact and relationship between the two cooldown periods configured for a node pool are as follows:

**Cooldown Period During a Scale-out**

This interval indicates the period during which nodes added to the current node pool after a scale-out cannot be deleted. This setting takes effect in the entire node pool.

**Cooldown Period During a Scale-in**

The interval after a scale-out indicates the period during which the entire cluster cannot be scaled in after the auto scaling add-on triggers a scale-out (due to the unschedulable pods, metrics, and scaling policies). This interval takes effect in the entire cluster.

The interval after a node is deleted indicates the period during which the cluster cannot be scaled in after the auto scaling add-on triggers a scale-in. This setting takes effect in the entire cluster.

The interval after a failed scale-in indicates the period during which the cluster cannot be scaled in after the auto scaling add-on triggers a scale-in. This setting takes effect in the entire cluster.

Example YAML
------------

The following is a YAML example of a node scaling policy:

.. code-block::

   apiVersion: autoscaling.cce.io/v1alpha1
   kind: HorizontalNodeAutoscaler
   metadata:
     creationTimestamp: "2020-02-13T12:47:49Z"
     generation: 1
     name: xxxx
     namespace: kube-system
     resourceVersion: "11433270"
     selfLink: /apis/autoscaling.cce.io/v1alpha1/namespaces/kube-system/horizontalnodeautoscalers/xxxx
     uid: c2bd1e1d-60aa-47b5-938c-6bf3fadbe91f
   spec:
     disable: false
     rules:
     - action:
         type: ScaleUp
         unit: Node
         value: 1
       cronTrigger:
         schedule: 47 20 * * *
       disable: false
       ruleName: cronrule
       type: Cron
     - action:
         type: ScaleUp
         unit: Node
         value: 2
       disable: false
       metricTrigger:
         metricName: Cpu
         metricOperation: '>'
         metricValue: "40"
         unit: Percent
       ruleName: metricrule
       type: Metric
     targetNodepoolIds:
     - 7d48eca7-3419-11ea-bc29-0255ac1001a8

.. _cce_10_0209__table18763092201:

.. table:: **Table 2** Key parameters

   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
   | Parameter                                   | Type    | Description                                                                                                         |
   +=============================================+=========+=====================================================================================================================+
   | spec.disable                                | Bool    | Whether to enable the scaling policy. This parameter takes effect for all rules in the policy.                      |
   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
   | spec.rules                                  | Array   | All rules in a scaling policy.                                                                                      |
   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
   | spec.rules[x].ruleName                      | String  | Rule name.                                                                                                          |
   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
   | spec.rules[x].type                          | String  | Rule type. Currently, **Cron** and **Metric** are supported.                                                        |
   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
   | spec.rules[x].disable                       | Bool    | Rule switch. Currently, only **false** is supported.                                                                |
   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
   | spec.rules[x].action.type                   | String  | Rule action type. Currently, only **ScaleUp** is supported.                                                         |
   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
   | spec.rules[x].action.unit                   | String  | Rule action unit. Currently, only **Node** is supported.                                                            |
   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
   | spec.rules[x].action.value                  | Integer | Rule action value.                                                                                                  |
   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
   | spec.rules[x].cronTrigger                   | /       | Optional. This parameter is valid only in periodic rules.                                                           |
   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
   | spec.rules[x].cronTrigger.schedule          | String  | Cron expression of a periodic rule.                                                                                 |
   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
   | spec.rules[x].metricTrigger                 | /       | Optional. This parameter is valid only in metric-based rules.                                                       |
   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
   | spec.rules[x].metricTrigger.metricName      | String  | Metric of a metric-based rule. Currently, **Cpu** and **Memory** are supported.                                     |
   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
   | spec.rules[x].metricTrigger.metricOperation | String  | Comparison operator of a metric-based rule. Currently, only **>** is supported.                                     |
   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
   | spec.rules[x].metricTrigger.metricValue     | String  | Metric threshold of a metric-based rule. The value can be any integer from 1 to 100 and must be a character string. |
   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
   | spec.rules[x].metricTrigger.Unit            | String  | Unit of the metric-based rule threshold. Currently, only **%** is supported.                                        |
   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
   | spec.targetNodepoolIds                      | Array   | All node pools associated with the scaling policy.                                                                  |
   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
   | spec.targetNodepoolIds[x]                   | String  | ID of the node pool associated with the scaling policy.                                                             |
   +---------------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------+
