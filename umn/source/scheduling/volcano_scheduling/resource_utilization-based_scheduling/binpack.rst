:original_name: cce_10_0773.html

.. _cce_10_0773:

Binpack
=======

Binpack is a pod scheduling add-on that enables the scheduler to preferentially schedule pods to nodes with high resource allocation. This reduces resource fragments on each node and improves cluster resource utilization.

Prerequisites
-------------

-  A cluster of v1.19 or later is available. For details, see :ref:`Creating a CCE Cluster <cce_10_0028>`.
-  The Volcano add-on has been installed. For details, see :ref:`Volcano Scheduler <cce_10_0193>`.

Features
--------

Binpack aims to fill as many existing nodes as possible (try not to allocate blank nodes). In the concrete implementation, Binpack scheduling algorithm scores the nodes that can be delivered, and a higher score indicates a higher resource utilization rate of nodes. Binpack intends to centrally schedule application workloads onto some nodes in a cluster, which facilitates auto scaling of cluster nodes.

The Binpack add-on works with other scheduling add-ons of the scheduler to score nodes. You can customize the overall weight of the add-on and the weight of each resource to modify the influence in the node score. When using Binpack to calculate node scores, the scheduler considers extended resources such as CPUs, memory, and GPUs requested by pods, and calculates the scores based on the weights configured for each resource.

Algorithm Implementation
------------------------

A node is scored based on the overall weight of the Binpack add-on and the weight of each resource. Each type of resource requested by the to-be-scheduled pods is scored. Take CPUs as an example, the CPU score is calculated using the following formula:

**CPU.weight x (Requested + Used)/Allocatable**

A larger CPU weight leads to a higher score. A higher resource usage of a node leads to a higher node score. The same rule applies to memory and GPU resources. The parameters in the formula for scoring a resource are as follows:

-  **CPU.weight**: customized CPU weight
-  **Requested**: CPU resources requested by the pods to be scheduled
-  **Used**: CPU resources that have been used on the current node
-  **Allocatable**: total CPU resources available on the current node

The Binpack add-on calculates the score of a node using the following formula:

**Binpack.weight x (CPU.score + Memory.score + GPU.score)/(CPU.weight + Memory.weight + GPU.weight) x 100**

A larger Binpack weight leads to a higher score. A larger resource weight leads to a greater influence in the node score. The parameters in the formula for scoring a node are as follows:

-  **Binpack.weight**: Binpack weight
-  **CPU.score**: calculated CPU score; **CPU.weight**: customized CPU weight
-  **Memory.score**: calculated memory score; **Memory.weight**: customized memory weight
-  **GPU.score**: calculated GPU score; **GPU.weight**: customized GPU weight


.. figure:: /_static/images/en-us_image_0000001797871357.png
   :alt: **Figure 1** Binpack example

   **Figure 1** Binpack example

As shown in the figure, there are two nodes in the cluster. When pods need to be scheduled, the Binpack policy scores the two nodes separately.

#. The scoring for node 1 is as follows:

   Each resource is scored using the following formula: CPU.weight x (Requested + Used)/Allocatable

   -  CPU score: 1 x (2 + 4)/8 = 0.75
   -  Memory score: 1 x (4 + 8)/16 = 0.75
   -  GPU score: 2 x (4 + 4)/8 = 1

   The total score of each node is calculated using the following formula: Binpack.weight x (CPU.score + Memory.score + GPU.score)/(CPU.weight + Memory.weight + GPU.weight) x 100

   Score of node 1: 5 x (0.75 + 0.75 + 1)/(1 + 1 + 2) x 100 = 312.5

#. The scoring for node 2 is as follows:

   -  CPU score: 1 x (2 + 6)/8 = 1
   -  Memory score: 1 x (4 + 8)/16 = 0.75
   -  GPU score: 2 x (4 + 4)/8 = 1

   Score of node 2: 5 x (1 + 0.75 + 1)/(1 + 1 + 2) x 100 = 343.75

The calculation results show that the score of node 2 is greater than that of node 1. According to the Binpack policy, new pods will be preferentially scheduled to node 2.

Procedure
---------

After Volcano is installed, the Binpack policy takes effect by default. If the default configuration cannot meet your requirements, you can customize the weight of the Binpack add-on and the weight of each resource on the **Scheduling** page. To do so, perform the following operations:

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Settings** in the navigation pane and click the **Scheduling** tab.
#. In the **Resource utilization optimization scheduling** area, modify the Binpack settings.

   .. table:: **Table 1** Binpack weight

      +------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
      | Item                               | Description                                                                                                                                                | Default Value |
      +====================================+============================================================================================================================================================+===============+
      | Binpack Scheduling Strategy Weight | A larger value indicates a higher weight of the Binpack policy in overall scheduling.                                                                      | 10            |
      +------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
      | CPU Weight                         | A larger value indicates a higher cluster CPU usage.                                                                                                       | 1             |
      +------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
      | Memory Weight                      | A larger value indicates a higher cluster memory usage.                                                                                                    | 1             |
      +------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
      | Custom Resource Type               | Other custom resource types requested by pods, for example, **nvidia.com/gpu**. A larger value indicates a higher usage of the specified cluster resource. | None          |
      +------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+

#. Click **Confirm configuration**.
