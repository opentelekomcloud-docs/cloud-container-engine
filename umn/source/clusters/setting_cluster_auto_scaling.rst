:original_name: cce_01_0157.html

.. _cce_01_0157:

Setting Cluster Auto Scaling
============================

Scenario
--------

The Cluster Auto Scaling feature allows CCE to automatically scale out a cluster (adding worker nodes to a cluster) according to custom policies when workloads cannot be scheduled into the cluster due to insufficient cluster resources.

Notes and Constraints
---------------------

-  Currently, master nodes cannot be automatically added to or removed from clusters.
-  If both auto scale-in and auto scale-out are required, use the autoscaler add-on. For details, see :ref:`autoscaler <cce_01_0154>`.
-  Clusters of v1.17 do not support auto scaling using AOM. You can use node pools for auto scaling. For details, see :ref:`Node Pool Overview <cce_01_0081>`.

Automatic Cluster Scale-out
---------------------------

#. Log in to the CCE console. Choose **Resource Management** > **Clusters** in the navigation pane. In the card view of the cluster to be scaled, choose **More** > **Auto Scaling**.

#. Click the **Scale-out Settings** tab and then **Edit**. Set the maximum number of nodes, minimum number of nodes, cooldown period, and node configuration.

   .. table:: **Table 1** Scale-out settings

      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                      |
      +===================================+==================================================================================================================================================================================================================================+
      | Cooldown Period                   | Interval between consecutive scale-out operations, in the unit of second. The cooldown period ensures that a scale-out operation is initiated only when previous scaling operation is finished and the system is running stably. |
      |                                   |                                                                                                                                                                                                                                  |
      |                                   | The value ranges from 60 to 3600, in seconds. The default value is 900. If the cooling interval is less than 900 seconds (15 minutes), the auto scaling may not work well, because creating a node may take 2 to 10 minutes.     |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Maximum Nodes                     | Maximum number of nodes to which the cluster can scale out.                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                  |
      |                                   | 1 <= Maximum Nodes < cluster node quota                                                                                                                                                                                          |
      |                                   |                                                                                                                                                                                                                                  |
      |                                   | .. note::                                                                                                                                                                                                                        |
      |                                   |                                                                                                                                                                                                                                  |
      |                                   |    The cluster node quota depends on the cluster size (maximum number of nodes that can be managed by a cluster) and the node quota of the account. The cluster node quota used here is the smaller of the two.                  |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Node Configuration                | If scale-out is required after the scale-out policy is executed, the system creates a node.                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                  |
      |                                   | a. Click **Set** and set the node parameters. For details about how to set the node parameters, see :ref:`Creating a Node <cce_01_0033>`.                                                                                        |
      |                                   | b. After the parameters are configured, click **Submit**.                                                                                                                                                                        |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. After confirming the scale-out configuration and node parameters, click **OK**.

#. Set the scale-out policy for the cluster. Click the **Scale-out Policies** tab and click **Add Policy**.

   -  **Policy Name**: Enter a policy name, for example, **policy01**.
   -  **Policy Type**: Currently, the following types of auto scale-out policies are supported:

      -  :ref:`Metric-based policy <cce_01_0157__table23209107191540>`: Scale-out is performed based on the CPU or memory settings.

         .. _cce_01_0157__table23209107191540:

         .. table:: **Table 2** Parameters for adding a metric-based policy

            +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
            | Parameter                         | Description                                                                                                                                                                                                                                                                                  |
            +===================================+==============================================================================================================================================================================================================================================================================================+
            | \*Metric                          | Select **Allocated CPU** or **Allocated Memory**.                                                                                                                                                                                                                                            |
            +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
            | \*Trigger Condition               | Set a condition for triggering a scale-out policy, that is, when the average CPU or memory allocation value is greater than or less than a specified percentage.                                                                                                                             |
            +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
            | \*Monitoring Window               | Size of the data aggregation window. Select a value from the drop-down list.                                                                                                                                                                                                                 |
            |                                   |                                                                                                                                                                                                                                                                                              |
            |                                   | If you select **15min**, the selected metric is measured every 15 minutes.                                                                                                                                                                                                                   |
            +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
            | \*Threshold Crossings             | Number of consecutive times that the threshold is reached within the monitoring window. The calculation cycle is fixed at one minute. If you set this parameter to **3**, the configured action will be triggered when the metrics meet the specified threshold for three consecutive times. |
            +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
            | \*Action                          | Action executed after a policy is triggered.                                                                                                                                                                                                                                                 |
            +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      -  :ref:`Scheduled policy <cce_01_0157__table62540231191540>`: Scale-out is performed at a specified time.

         .. _cce_01_0157__table62540231191540:

         .. table:: **Table 3** Parameters for adding a scheduled policy

            ============== ============================================
            Parameter      Description
            ============== ============================================
            \*Policy Type  Set this parameter to **Scheduled policy**.
            \*Trigger Time Time at which the policy is triggered.
            \*Action       Action executed after a policy is triggered.
            ============== ============================================

      -  :ref:`Periodic policy <cce_01_0157__table60088509191540>`: Scale-out can be performed by day, week, or month.

         .. _cce_01_0157__table60088509191540:

         .. table:: **Table 4** Parameters for adding a periodic policy

            ============= ============================================
            Parameter     Description
            ============= ============================================
            \*Policy Type Set the parameter to **Periodic policy**.
            \*Time Range  Specify the time for triggering the policy.
            \*Action      Action executed after a policy is triggered.
            ============= ============================================

#. Click **OK**.

   After the auto scale-out is completed, choose **Resource Management** > **Nodes** in the navigation pane. On the node list, you can view the worker nodes added during cluster auto scaling.
