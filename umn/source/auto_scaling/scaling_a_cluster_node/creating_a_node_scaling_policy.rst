:original_name: cce_01_0209.html

.. _cce_01_0209:

Creating a Node Scaling Policy
==============================

CCE provides auto scaling through the :ref:`autoscaler <cce_01_0154>` add-on. Nodes with different specifications can be automatically added across AZs on demand.

If a node scaling policy and the configuration in the autoscaler add-on take effect at the same time, for example, there are pods that cannot be scheduled and the value of a metric reaches the threshold at the same time, scale-out is performed first for the unschedulable pods.

-  If the scale-out succeeds for the unschedulable pods, the system skips the metric-based rule logic and enters the next loop.
-  If the scale-out fails for the unschedulable pods, the metric-based rule is executed.

Prerequisites
-------------

Before using the node scaling function, you must install the :ref:`autoscaler <cce_01_0154>` add-on of v1.13.8 or later.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Auto Scaling**. On the **Node Scaling** tab page, click **Create Node Scaling Policy**.
#. In the **Check Add-ons** step:

   -  If |image1| is displayed next to the add-on name, click **Install**, set add-on parameters as required, and click **Install** to install the add-on.
   -  If |image2| is displayed next to the add-on name, the add-on has been installed.

#. After the required add-ons have been installed, click **Next: Policy configuration**.

   .. note::

      If the add-ons have been installed, after you click **Create Node Scaling Policy**, you will directly land on the second step to configure the policy. The first step (checking the add-ons) has been completed almost instantly.

#. On the **Create Node Scaling Policy** page, set the following policy parameters.

   -  **Policy Name**: name of the policy to be created, which can be customized.

   -  **Associated Node Pool**: Click **Add Node Pool** and select the node pool to be associated. You can associate multiple node pools to use the same scaling policy.

      .. note::

         Priority is now supported for node pools. CCE will select a node pool for auto scaling based on the following policies:

         a. CCE uses algorithms to determine whether a node pool meets the conditions to allow scheduling of a pod in pending state, including whether the node resources are greater than requested by the pod, and whether the nodeSelect, nodeAffinity, and taints meet the conditions. In addition, the node pools that fail to be scaled (due to insufficient resources or other reasons) and are still in the 15-minute cool-down interval are filtered.
         b. If multiple node pools meet the scaling requirements, the system checks the priority of each node pool and selects the node pool with the highest priority for scaling. The value ranges from 0 to 100 and the default priority is 0. The value 100 indicates the highest priority, and the value 0 indicates the lowest priority.
         c. If multiple node pools have the same priority or no priority is configured for them, the system selects the node pool that will consume the least resources based on the configured VM specification.
         d. If the VM specifications of multiple node pools are the same but the node pools are deployed in different AZs, the system randomly selects a node pool to trigger scaling.
         e. If the resources of the preferred node pool are insufficient, the system automatically selects next node pool based on the priority.

         For details about the node pool priority, see :ref:`Autoscaler <cce_01_0012__li153215432027>`.

   -  **Execution Rules**: Click **Add Rule**. In the dialog box displayed, set the following parameters:

      **Name**: Enter a rule name.

      **Type**: You can select **Metric-based** or **Periodic**. The differences between the two types are as follows:

      -  **Metric-based**:

         -  **Condition**: Select **CPU allocation** or **Memory allocation** and enter a value. The value must be greater than the scale-in percentage configured in the autoscaler add-on.

            .. note::

               -  Resource allocation (%) = Resources requested by pods in the node pool/Resources allocatable to pods in the node pool

               -  **If multiple rules meet the conditions, the rules are executed in either of the following modes:**

                  If rules based on the **CPU allocation rate** and **memory allocation rate** are configured and two or more rules meet the scale-out conditions, the rule that will add the most nodes will be executed.

                  If a rule based on the **CPU allocation rate** and **a periodic rule** are configured and they both meet the scale-out conditions, one of them will be executed randomly. The rule executed first (rule A) changes the node pool to the scaling state. As a result, the other rule (rule B) cannot be executed. After rule A is executed and the node pool status becomes normal, rule B will not be executed.

               -  If rules based on the **CPU allocation rate** and **memory allocation rate** are configured, the policy detection period varies with the processing logic of each loop of the autoscaler add-on. Scale-out is triggered once the conditions are met, but it is constrained by other factors such as the cool-down interval and node pool status.

         -  **Action**: Set an action to be performed when the trigger condition is met.

      -  **Periodic**:

         -  **Triggered At**: You can select a specific time point every day, every week, every month, or every year.
         -  **Action**: Set an action to be performed when the **Triggered At** value is reached.

      You can click **Add Rule** again to add more node scaling policies. You can add a maximum of one CPU usage-based rule and one memory usage-based rule. The total number of rules cannot exceed 10.

#. After the configuration is complete, click **Create**. If the system displays a message indicating that the request to create a node scaling policy is submitted successfully, click **Back to Node Scaling Policy List**.
#. On the **Node Scaling** tab page, you can view the created node scaling policy.

Constraints on Scale-in
-----------------------

CCE cannot trigger scale-in by using node scaling policies. You can set a scale-in policy when installing the :ref:`autoscaler add-on <cce_01_0154>`.

Node scale-in can be triggered only by the resource allocation rate. When CPU and memory allocation rates in a cluster are lower than the specified thresholds (set when the autoscaler add-on is installed or modified), scale-in is triggered for nodes in the node pool (this function can be disabled).

Example YAML File
-----------------

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

.. _cce_01_0209__table18763092201:

.. table:: **Table 1** Key parameters

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

.. |image1| image:: /_static/images/en-us_image_0259814716.png
.. |image2| image:: /_static/images/en-us_image_0259814717.png
