:original_name: cce_10_0967.html

.. _cce_10_0967:

Specifying Node Scale-in Conditions for a Node Pool
===================================================

When you use the CCE Cluster Autoscaler add-on to automatically adjust the number of nodes, you need to specify the scale-in conditions for each node pool in a cluster based on service requirements.

Prerequisites
-------------

-  A cluster of v1.25 or later is available.
-  The CCE Cluster Autoscaler add-on has been installed in the cluster, and the add-on version is 1.25.181, 1.27.152, 1.28.120, 1.29.81, 1.30.48, 1.31.10, or later.

Step 1: Create a Node Pool
--------------------------

In this example, you need to create a node pool and configure custom scale-in conditions for it.

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. In the navigation pane, choose **Nodes**. Then click the **Node Pools** tab.
#. Click **Create Node Pool** in the upper right corner and then configure the parameters as required. For details about the parameter settings, see :ref:`Creating a Node Pool <cce_10_0012>`.

Step 2: Specify Custom Scale-in Conditions for the Node Pool
------------------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Settings**. Then click the **Auto Scaling** tab.

   -  If CCE Cluster Autoscaler is not installed, configure add-on parameters based on service requirements, click **Install**, and wait until the add-on is installed. For details about how to configure the add-on, see :ref:`CCE Cluster Autoscaler <cce_10_0193>`.
   -  If CCE Cluster Autoscaler has been installed, configure scaling policies.

#. Configure auto scale-in. Auto scale-in is disabled by default. After you enable this feature, you can configure **Node Scale-In Conditions**.

   Nodes in a cluster are automatically scaled in when the scale-in conditions are met. If no custom scale-in conditions are configured, the default scale-in conditions are used for each node pool. If you configure custom scale-in conditions for a node pool, the custom scale-in conditions are preferentially used for the node pool.

   **Default Scale-In Conditions**

   In this example, if the pre-allocated CPU and memory of a node in the cluster are below 50% for 10 minutes, or the node is unavailable for more than 20 minutes, the node is selected for scale-in.

   **(Optional) Custom Scale-In Conditions**

   Click **Add**, select a node pool, enter the scale-in conditions, and click **Confirm Settings**. If auto scaling is not enabled for all specifications in a node pool, custom scale-in conditions configured for the node pool do not take effect. For details about how to enable auto scaling for a node pool, see :ref:`Configuring Auto Scaling Policies for a Node Pool <cce_10_0209>`.

   +------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | Parameter                                                              | Description                                                                                                                                                                                                                                                                                                                                                                                                                          | Example Value         |
   +========================================================================+======================================================================================================================================================================================================================================================================================================================================================================================================================================+=======================+
   | Pre-allocated CPU and memory in the **Node Scale-In Condition** column | When the ratio of the total CPU or memory of all pods running on the node to the allocatable resources on the node is lower than the threshold, the node can be scaled in.                                                                                                                                                                                                                                                           | 30%                   |
   |                                                                        |                                                                                                                                                                                                                                                                                                                                                                                                                                      |                       |
   |                                                                        | For example, if this parameter is set to **30%**, scale-in evaluation is triggered when the resource usage is lower than 30%. If this parameter is not configured, the CCE Cluster Autoscaler add-on uses the default resource usage threshold.                                                                                                                                                                                      |                       |
   +------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | Time specified in the **Node Scale-In Condition** column               | This is the duration during which a node is in the unneeded state before the scale-in operation can be performed. CCE Cluster Autoscaler removes a node only when the node is not needed for a specified period of time. This prevents frequent scale-in triggered by temporary resource fluctuation and improves system stability. If this parameter is not configured, the CCE Cluster Autoscaler add-on uses the default setting. | 10 minutes            |
   +------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+

#. In the displayed dialog box, click **Save**.

Step 3: Verify that the Custom Scale-in Conditions Take Effect
--------------------------------------------------------------

In this example, the custom scale-in threshold is set to 30% for the node pool, while the default scale-in threshold is 50%. If the node CPU and memory in the node pool remains between 30% and 50% and scale-in is not triggered, the node pool preferentially follows the custom scale-in conditions instead of the default scale-in conditions.

Related Operations
------------------

-  For details about the CCE Cluster Autoscaler add-on, see :ref:`CCE Cluster Autoscaler <cce_10_0193>`.
-  For details about the steps of how to create an auto scaling policy and the parameters, see :ref:`Creating a Node Scaling Policy <cce_10_0209>`.
