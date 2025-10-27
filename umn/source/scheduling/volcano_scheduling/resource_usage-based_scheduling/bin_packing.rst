:original_name: cce_10_0773.html

.. _cce_10_0773:

Bin Packing
===========

Bin packing is an optimization algorithm that aims to properly allocate resources to each job and get the jobs done using the minimum number of resources. After bin packing is enabled for cluster workloads, the scheduler preferentially schedules pods to nodes with high resource allocation. This reduces resource fragments on each node and improves cluster resource utilization.

Prerequisites
-------------

-  A cluster of v1.19 or later is available. For details, see :ref:`Creating a CCE Standard/Turbo Cluster <cce_10_0028>`.
-  The Volcano add-on has been installed. For details, see :ref:`Volcano Scheduler <cce_10_0193>`.

Features
--------

The bin packing algorithm maximizes resource utilization per node and minimizes idle nodes. It scores nodes based on resource usage. Higher usage results in higher scores and priority. This centralizes application workloads on selected cluster nodes, aiding in auto-scaling.

As a Volcano scheduler add-on, bin packing collaborates with other add-ons to set node scores. You can customize its global weight and configure weights for resources like CPU, memory, and GPU to influence scheduling. The scheduler calculates bin packing scores by considering all pod-requested resources, performing a weighted average based on each resource's weight to finalize the node score.

How It Works
------------

When scoring nodes, the scheduler calculates bin packing scores by considering the global weight of the add-on and the weight of each resource type such as CPU, memory, and GPU. The scoring process is as follows:

Each type of resource requested by the to-be-scheduled pods is scored. Take CPUs as an example, the CPU score is calculated using the following formula:

**CPU.weight x (Requested + Used)/Allocatable**

A larger CPU weight leads to a higher score. A higher resource usage of a node leads to a higher node score. The same rule applies to memory and GPU resources. The parameters in the formula for scoring a resource are as follows:

-  **CPU.weight**: customized CPU weight
-  **Requested**: CPU resources requested by the pods to be scheduled
-  **Used**: CPU resources that have been used on the current node
-  **Allocatable**: total CPU resources available on the current node

The bin packing add-on calculates the score of a node using the following formula:

**Binpack.weight x (CPU.score + Memory.score + GPU.score)/(CPU.weight + Memory.weight + GPU.weight) x 100**

A larger bin packing weight leads to a higher score. A larger resource weight leads to a greater influence in the node score. The parameters in the formula for scoring a node are as follows:

-  **Binpack.weight**: bin packing weight
-  **CPU.score**: calculated CPU score; **CPU.weight**: customized CPU weight
-  **Memory.score**: calculated memory score; **Memory.weight**: customized memory weight
-  **GPU.score**: calculated GPU score; **GPU.weight**: customized GPU weight

.. _cce_10_0773__fig565130165812:

.. figure:: /_static/images/en-us_image_0000002434240896.png
   :alt: **Figure 1** Bin packing example

   **Figure 1** Bin packing example

In :ref:`Figure 1 <cce_10_0773__fig565130165812>`, there are two nodes in the cluster, node 1 and node 2. A pod requests CPU, memory, and GPU resources. Before scheduling the pod, CCE uses the bin packing policy to score the two nodes.

For example, **CPU.weight** is set to **1**, **Memory.weight** is set to **1**, **GPU.weight** is set to **2**, and **binpack.weight** is set to **5** in a cluster.

#. Scoring for node 1

   The score of each resource type is calculated using the following formulas:

   -  CPU: CPU.weight x (Requested + Used)/Allocatable = 1 x (2 + 4)/8 = 0.75
   -  Memory: Memory.weight x (Requested + Used)/Allocatable = 1 x (4 + 8)/16 = 0.75
   -  GPU: GPU.weight x (Requested + Used)/Allocatable = 2 x (4 + 4)/8 = 2

   The total score of each node is calculated using the following formula: Binpack.weight x (CPU.score + Memory.score + GPU.score)/(CPU.weight + Memory.weight + GPU.weight) x 100

   If **binpack.weight** is set to **5**, the score of node 1 is calculated as follows: 5 x (0.75 + 0.75 + 2)/(1 + 1 + 2) x 100 = 437.5

#. Scoring for node 2

   -  CPU: CPU.weight x (Requested + Used)/Allocatable = 1 x (2 + 6)/8 = 1
   -  Memory: Memory.weight x (Requested + Used)/Allocatable = 1 x (4 + 8)/16 = 0.75
   -  GPU: GPU.weight x (Requested + Used)/Allocatable = 2 x (4 + 4)/8 = 2

   Score of node 2: 5 x (1 + 0.75 + 2)/(1 + 1 + 2) x 100 = 468.75

The calculation results show that the score of node 2 is greater than that of node 1. According to the bin packing policy, new pods will be preferentially scheduled to node 2.

Configuring Bin Packing
-----------------------

After Volcano is installed, bin packing takes effect by default. If the default configuration cannot meet your requirements, you can customize the weight of bin packing and the weight of each resource on the **Scheduling** page. To do so, perform the following operations:

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Settings** in the navigation pane and click the **Scheduling** tab.
#. In the **Resource utilization optimization scheduling** configuration, enable bin packing.

   .. table:: **Table 1** Bin packing weights

      +------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
      | Item                               | Description                                                                                                                                                | Default Value |
      +====================================+============================================================================================================================================================+===============+
      | Binpack Scheduling Strategy Weight | A larger value indicates a higher weight of the bin packing policy in overall scheduling.                                                                  | 10            |
      +------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
      | CPU Weight                         | A larger value indicates a higher cluster CPU usage.                                                                                                       | 1             |
      +------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
      | Memory Weight                      | A larger value indicates a higher cluster memory usage.                                                                                                    | 1             |
      +------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
      | Custom Resource Type               | Other custom resource types requested by pods, for example, **nvidia.com/gpu**. A larger value indicates a higher usage of the specified cluster resource. | None          |
      +------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+

#. Click **Confirm**.
