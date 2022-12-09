:original_name: cce_01_0057.html

.. _cce_01_0057:

Scaling a Workload
==================

-  Auto scaling: You can set metric-based, scheduled, and periodic policies. After configuration, pods can be automatically added or deleted based on resource changes or the specified schedule.
-  Manual scaling: Pods are immediately added or deleted after the configuration is complete.

.. note::

   **Scaling policy priority**: If you do not manually adjust the number of pods, auto scaling policies will take effect for resource scheduling. If :ref:`manual scaling <cce_01_0057__section1050418516503>` is triggered, auto scaling policies will be temporarily invalid.

Auto Scaling - HPA
------------------

HPA policies can be used for auto scaling. **You can view all policies or perform more operations in** :ref:`Auto Scaling <cce_01_0208>`.

Auto Scaling - AOM
------------------

You can define auto scaling policies as required, which can intelligently adjust resources in response to service changes and data traffic spikes.

Auto scaling can be backed by Application Operations Management (AOM), but not for clusters of v1.17 and later.

Currently, CCE supports the following types of auto scaling policies:

:ref:`Metric-based policy <cce_01_0057__li16804196913>`: After a workload is created, pods will be automatically scaled when the workload's CPU or memory usage exceeds or falls below a preset limit.

:ref:`Scheduled policy <cce_01_0057__li1595211281895>`: scaling at a specified time. Scheduled auto scaling is applicable flash sales, premier shopping events, and other regular events that bring a high burst of traffic load.

:ref:`Periodic policy <cce_01_0057__li35861531491>`: scaling at a specified time on a daily, weekly, or monthly basis. Periodic scheduling is applicable to scenarios where traffic changes periodically.

-  .. _cce_01_0057__li16804196913:

   **Metric-based policy**: Supports auto scaling of a workload based on the CPU/memory usage.

   #. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Deployments** or **StatefulSets**. In the same row as the target workload, choose **More** > **Scaling**.

   #. In the **Auto Scaling** area, click **Add Scaling Policy**.

   #. Set the policy parameters as listed in :ref:`Table 1 <cce_01_0057__table297454613303>`.

      .. _cce_01_0057__table297454613303:

      .. table:: **Table 1** Parameters for adding a metric-based policy

         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                        |
         +===================================+====================================================================================================================================================================================================================================================================================================================+
         | Policy Name                       | Enter the name of the scaling policy.                                                                                                                                                                                                                                                                              |
         |                                   |                                                                                                                                                                                                                                                                                                                    |
         |                                   | The policy name must be 1 to 64 characters in length and start with a letter. Only letters, digits, underscores (_), and hyphens (-) are allowed.                                                                                                                                                                  |
         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Policy Type                       | Set this parameter to **Metric-based policy**.                                                                                                                                                                                                                                                                     |
         |                                   |                                                                                                                                                                                                                                                                                                                    |
         |                                   | The alarm policy is triggered based on historical data. The system checks whether the indicators set by the user in the monitoring window meet the triggering conditions **every minute**. If the triggering conditions are met for N consecutive periods, the system performs the action specified by the policy. |
         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Metric                            | Set the metrics that describe the resource performance data or status.                                                                                                                                                                                                                                             |
         |                                   |                                                                                                                                                                                                                                                                                                                    |
         |                                   | -  **CPU Usage**: CPU usage of the measured object. The value is the percentage of the used CPU cores to the total CPU cores.                                                                                                                                                                                      |
         |                                   | -  **Physical Memory Usage**: percentage of the physical memory size used by the measured object to the physical memory size that the measured object has applied for.                                                                                                                                             |
         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Trigger Condition                 | The value can be higher (>) or lower (<) than a threshold. When the usage of the preceding metrics reaches the specified value, the scaling policy is triggered.                                                                                                                                                   |
         |                                   |                                                                                                                                                                                                                                                                                                                    |
         |                                   | For example, if **Metric** is set to **CPU Usage** and this parameter is set to **> 70%**, the scaling policy is triggered when the CPU usage exceeds 70%.                                                                                                                                                         |
         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Monitoring window                 | Size of the data aggregation window.                                                                                                                                                                                                                                                                               |
         |                                   |                                                                                                                                                                                                                                                                                                                    |
         |                                   | If the value is set to **60**, metric statistics are collected every 60 seconds.                                                                                                                                                                                                                                   |
         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Threshold Crossings               | Number of consecutive times that the threshold is reached within the monitoring window. The calculation cycle is fixed at one minute.                                                                                                                                                                              |
         |                                   |                                                                                                                                                                                                                                                                                                                    |
         |                                   | If the parameter is set to **3**, the action is triggered if threshold is reached for three consecutive measurement periods.                                                                                                                                                                                       |
         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Action                            | Action executed after a policy is triggered. Two actions are available: add or reduce pods.                                                                                                                                                                                                                        |
         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   #. Click **OK**.

   #. In the **Auto Scaling** area, check that the policy has been started.

      When the trigger condition is met, the auto scaling policy starts automatically.

-  .. _cce_01_0057__li1595211281895:

   **Scheduled policy:** scaling at a specified time.

   #. In the **Auto Scaling** area, click **Add Scaling Policy**. Select **Scheduled policy**.

      .. table:: **Table 2** Parameters for adding a scheduled policy

         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                       |
         +===================================+===================================================================================================================================================+
         | Policy Name                       | Enter the name of the scaling policy.                                                                                                             |
         |                                   |                                                                                                                                                   |
         |                                   | The policy name must be 1 to 64 characters in length and start with a letter. Only letters, digits, underscores (_), and hyphens (-) are allowed. |
         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
         | Policy Type                       | Set this parameter to **Scheduled policy**.                                                                                                       |
         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
         | Trigger Time                      | Time at which the policy is enforced.                                                                                                             |
         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
         | Action                            | Action executed after a policy is triggered. Three actions are available: add pods, reduce pods, and set the number of pods.                      |
         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+

   #. Click **OK**.

   #. In the **Auto Scaling** area, check that the policy has been started.

      When the trigger time is reached, you can see on the **Pods** tab page that the auto scaling policy has taken effect.

-  .. _cce_01_0057__li35861531491:

   **Periodic policy:** scaling at a specified time on a daily, weekly, or monthly basis.

   #. In the **Auto Scaling** area, click **Add Scaling Policy**. Select **Periodic policy**.

      .. table:: **Table 3** Parameters for adding a periodic policy

         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                       |
         +===================================+===================================================================================================================================================+
         | Policy Name                       | Enter the name of the scaling policy.                                                                                                             |
         |                                   |                                                                                                                                                   |
         |                                   | The policy name must be 1 to 64 characters in length and start with a letter. Only letters, digits, underscores (_), and hyphens (-) are allowed. |
         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
         | Policy Type                       | Set this parameter to **Periodic policy**.                                                                                                        |
         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
         | Time Range                        | Specify the time for triggering the policy.                                                                                                       |
         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
         | Action                            | Action executed after a policy is triggered. Three actions are available: add pods, reduce pods, and set the number of pods.                      |
         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+

   #. Click **OK**.

   #. In the **Auto Scaling** area, check that the policy has been started.

      When the trigger condition is met, the auto scaling policy starts automatically.

.. _cce_01_0057__section1050418516503:

Manual Scaling
--------------

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Deployments** or **StatefulSets**. In the same row as the target workload, choose **More** > **Scaling**.
#. In the **Manual Scaling** area, click |image1| and change the number of pods to, for example, **3**. Then, click **Save**. The scaling takes effect immediately.
#. On the **Pods** tab page, check that a new pod is being created. When the pod status becomes **Running**, pod scaling is complete.

.. |image1| image:: /_static/images/en-us_image_0144045351.png
