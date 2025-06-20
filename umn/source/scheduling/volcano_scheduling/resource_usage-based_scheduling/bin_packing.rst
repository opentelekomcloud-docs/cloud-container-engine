:original_name: cce_10_0773.html

.. _cce_10_0773:

Bin Packing
===========

Bin packing is an optimization algorithm that aims to properly allocate resources to each job and get the jobs done using the minimum amount of resources. After bin packing is enabled for cluster workloads, the scheduler preferentially schedules pods to nodes with high resource allocation. This reduces resource fragments on each node and improves cluster resource utilization.

Prerequisites
-------------

-  A cluster of v1.19 or later is available. For details, see :ref:`Creating a CCE Standard/Turbo Cluster <cce_10_0028>`.
-  The Volcano add-on has been installed. For details, see :ref:`Volcano Scheduler <cce_10_0193>`.

Features
--------

Bin packing aims to fill as many existing nodes as possible (try not to allocate blank nodes). In the concrete implementation, the bin packing algorithm scores the nodes that can be delivered, and a higher score indicates a higher resource utilization rate of nodes. Bin packing intends to centrally schedule application workloads onto some nodes in a cluster, which facilitates auto scaling of cluster nodes.

The bin packing add-on works with other scheduling add-ons of the scheduler to score nodes. You can customize the overall weight of the add-on and the weight of each resource to modify the influence in the node score. When using bin packing to calculate node scores, the scheduler considers extended resources such as CPUs, memory, and GPUs requested by pods, and calculates the scores based on the weights configured for each resource.

How It Works
------------

A node is scored based on the overall weight of the bin packing add-on and the weight of each resource. Each type of resource requested by the to-be-scheduled pods is scored. Take CPUs as an example, the CPU score is calculated using the following formula:

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


.. figure:: /_static/images/en-us_image_0000002253780457.png
   :alt: **Figure 1** Bin packing example

   **Figure 1** Bin packing example

In this figure, there are two nodes in the cluster. When pods need to be scheduled, the bin packing policy scores the two nodes separately.

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

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Settings** in the navigation pane. In the right pane, click the **Scheduling** tab.
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
