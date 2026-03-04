:original_name: cce_10_1088.html

.. _cce_10_1088:

Modifying the Node Scale-In Concurrency Settings
================================================

After a node auto scaling policy is configured, scale-in latency may become excessive or scale-in speed may fall short of expectations, particularly in large-scale clusters. To address these issues, the CCE Cluster Autoscaler add-on provides configurable parameters that allow you to set the maximum garbage collection (GC) waiting time for individual nodes and define the number of nodes that can be scaled in concurrently. These settings can be flexibly adjusted based on specific service requirements to improve scaling efficiency.

By setting **maxGracefulTerminationFlag**, you can specify a graceful termination period of up to 60 minutes. This ensures that service pods have enough time to complete ongoing tasks before being terminated. Additionally, **maxScaleDownParallelism**, **maxDrainParallelism**, and **parallelDrain** allow you to control the number of concurrent node scale-in operations. Fine-tuning these settings helps speed up the scale-in process and improve overall efficiency.

Prerequisites
-------------

The CCE Cluster Autoscaler add-on of 1.27.205, 1.28.172, 1.29.134, 1.30.100, 1.31.62, 1.32.38, 1.33.31, or later has been installed in the cluster.

Procedure
---------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Add-ons**. Locate **CCE Cluster Autoscaler** on the right and click **Edit**. If the add-on is not installed in the cluster, install it first.

#. In the sliding window, click **Edit YAML** above the add-on name.

#. Find **spec.values.custom** and modify the parameters below.

   +----------------------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------+
   | Add-on Parameter           | Community Parameter          | Description                                                                                                                                                                                                                                                                                                       | Example Value   |
   +============================+==============================+===================================================================================================================================================================================================================================================================================================================+=================+
   | parallelDrain              | parallel-drain               | -  Whether to enable parallel drainage of nodes.                                                                                                                                                                                                                                                                  | true            |
   |                            |                              | -  If it is set to **true**, the add-on will drain multiple nodes in parallel to speed up the scale-in process. It defaults to **false**. This specifies that parallel drainage is disabled.                                                                                                                      |                 |
   +----------------------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------+
   | maxScaleDownParallelism    | max-scale-down-parallelism   | -  The maximum number of concurrent scale-in tasks for both empty and non-empty nodes.                                                                                                                                                                                                                            | 20              |
   |                            |                              | -  It controls the maximum number of concurrent scale-in tasks for both empty and non-empty nodes with **--parallel-drain** set to **true**. If it is set to **20**, it indicates that up to 20 nodes will be moved in parallel at a time. It defaults to **10**.                                                 |                 |
   +----------------------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------+
   | maxDrainParallelism        | max-drain-parallelism        | -  The maximum number of nodes that can be drained in parallel.                                                                                                                                                                                                                                                   | 20              |
   |                            |                              | -  It controls the maximum number of nodes for parallel drainage with **--parallel-drain** set to **true**. If it is set to **20**, it indicates that up to 20 nodes will be drained in parallel at a time. It defaults to **2**, and the upper limit is the same as the value of **max-scale-down-parallelism**. |                 |
   +----------------------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------+
   | maxGracefulTerminationFlag | max-graceful-termination-sec | -  The maximum waiting time (in seconds) for the graceful termination of a node.                                                                                                                                                                                                                                  | 3600            |
   |                            |                              | -  If a node is marked as terminated, the add-on will wait for a period so that pods on the node can be gracefully terminated. This parameter controls the maximum waiting time. If it is set to **3600**, the add-on allows up to 1 hour for termination. It defaults to **600** (10 minutes).                   |                 |
   +----------------------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------+

   |image1|

#. Click **Submit**.

Verifying the Modification
--------------------------

You can view the logs of the add-on pods to ensure that the new startup parameters (**--parallel-drain**, **--max-scale-down-parallelism**, **--max-drain-parallelism**, and **--max-graceful-termination-sec**) have been added.

.. code-block::

   kubectl logs -f -n kube-system -l app=cluster-autoscaler

.. |image1| image:: /_static/images/en-us_image_0000002484119358.png
