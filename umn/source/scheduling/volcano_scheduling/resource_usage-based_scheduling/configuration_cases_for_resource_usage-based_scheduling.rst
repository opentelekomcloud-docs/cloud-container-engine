:original_name: cce_10_0813.html

.. _cce_10_0813:

Configuration Cases for Resource Usage-based Scheduling
=======================================================

Overview
--------

Volcano scheduling involves node filtering and scoring, which is used to filter the nodes meeting scheduling conditions and score the filtered nodes to find the one with the highest score for scheduling. Volcano provides multiple scheduling policies for node scoring. The weight of each scheduling policy can be adjusted based on service scenarios to enhance or reduce the impact of the policy on node scoring.

Scheduling Policies for Node Scoring
------------------------------------

The following table lists the scheduling policies supported by Volcano for node scoring.

+--------------------------------------------------+--------------------------+-----------------------------------------------------------------------------------------------------+-----------------------------------------------+
| Scheduling Policy                                | Parameter                | Description                                                                                         | Reference                                     |
+==================================================+==========================+=====================================================================================================+===============================================+
| Bin packing                                      | binpack.weight           | After this function is enabled, the parameter defaults to **10**.                                   | :ref:`Bin Packing <cce_10_0773>`              |
+--------------------------------------------------+--------------------------+-----------------------------------------------------------------------------------------------------+-----------------------------------------------+
| kube-scheduler node sorting (nodeorder)          | nodeaffinity.weight      | Pods are scheduled based on node affinity. The parameter defaults to **2**.                         | By default, this function is enabled.         |
+--------------------------------------------------+--------------------------+-----------------------------------------------------------------------------------------------------+-----------------------------------------------+
|                                                  | podaffinity.weight       | Pods are scheduled based on pod affinity. The parameter defaults to **2**.                          |                                               |
+--------------------------------------------------+--------------------------+-----------------------------------------------------------------------------------------------------+-----------------------------------------------+
|                                                  | leastrequested.weight    | Pods are scheduled to the node with the least requested resources. The parameter defaults to **1**. |                                               |
+--------------------------------------------------+--------------------------+-----------------------------------------------------------------------------------------------------+-----------------------------------------------+
|                                                  | balancedresource.weight  | Pods are scheduled to the node with balanced resource allocation. The parameter defaults to **1**.  |                                               |
+--------------------------------------------------+--------------------------+-----------------------------------------------------------------------------------------------------+-----------------------------------------------+
|                                                  | mostrequested.weight     | Pods are scheduled to the node with the most requested resources. The parameter defaults to **0**.  |                                               |
+--------------------------------------------------+--------------------------+-----------------------------------------------------------------------------------------------------+-----------------------------------------------+
|                                                  | tainttoleration.weight   | Pods are scheduled to the node with a high taint tolerance. The parameter defaults to **3**.        |                                               |
+--------------------------------------------------+--------------------------+-----------------------------------------------------------------------------------------------------+-----------------------------------------------+
|                                                  | imagelocality.weight     | Pods are scheduled to the node where the required images exist. The parameter defaults to **1**.    |                                               |
+--------------------------------------------------+--------------------------+-----------------------------------------------------------------------------------------------------+-----------------------------------------------+
|                                                  | selectorspread.weight    | Pods are evenly scheduled to different nodes. The parameter defaults to **0**.                      |                                               |
+--------------------------------------------------+--------------------------+-----------------------------------------------------------------------------------------------------+-----------------------------------------------+
|                                                  | podtopologyspread.weight | Pods are scheduled based on the pod topology. The parameter defaults to **2**.                      |                                               |
+--------------------------------------------------+--------------------------+-----------------------------------------------------------------------------------------------------+-----------------------------------------------+
| NUMA affinity scheduling (numa-aware)            | weight                   | After this function is enabled, the parameter defaults to **1**.                                    | :ref:`NUMA Affinity Scheduling <cce_10_0425>` |
+--------------------------------------------------+--------------------------+-----------------------------------------------------------------------------------------------------+-----------------------------------------------+
| Load-aware scheduling (usage)                    | weight                   | After this function is enabled, the parameter defaults to **5**.                                    | :ref:`Load-aware Scheduling <cce_10_0789>`    |
+--------------------------------------------------+--------------------------+-----------------------------------------------------------------------------------------------------+-----------------------------------------------+
| Node pool affinity scheduling (nodepoolaffinity) | nodepoolaffinity.weight  | After this function is enabled, the parameter defaults to **10000**.                                | :ref:`Node Pool Affinity <cce_10_0767>`       |
+--------------------------------------------------+--------------------------+-----------------------------------------------------------------------------------------------------+-----------------------------------------------+

How Can I Improve Cluster Resource Utilization by Reducing Node Resource Fragments?
-----------------------------------------------------------------------------------

There are both heavy- and low-resource jobs running in a cluster. It is hoped that the low-resource job preferentially uses resource fragments on each node so that idle nodes can be allocated to the high-resource job. This prevents job scheduling failures caused by insufficient node resources.

To resolve the preceding issue, enable bin packing and use the default policy weight **10**. For details, see :ref:`Bin Packing <cce_10_0773>`.

Recommended configurations:

-  To preferentially reduce CPU fragments in the cluster, increase the CPU weight to **5** and retain the memory weight to **1** in the bin packing policy.
-  To preferentially reduce memory fragments in the cluster, increase the memory weight to **5** and retain the CPU weight to **1** in the bin packing policy.
-  To preferentially reduce GPU fragments in the cluster, customize the GPU resource type, set the GPU weight to **10**, and retain both the CPU weight and memory weight to **1** in the bin packing policy.

How Can I Balance the Actual CPU and Memory Loads on Nodes?
-----------------------------------------------------------

When a workload is running, the CPU and memory resources used may differ greatly from what was initially requested. To avoid any issues caused by overloading a single node, it is hoped that the scheduler preferentially schedules pods to the nodes with lighter loads based on nodes' CPU and memory usage in the cluster. This balances loads between nodes and ensures the stability of both applications and nodes.

**Configuration case 1**

#. Enable load-aware scheduling and use the default policy weight **5**. For details, see :ref:`Load-aware Scheduling <cce_10_0789>`.
#. Disable bin packing. For details, see :ref:`Bin Packing <cce_10_0773>`.

Recommended configurations:

-  To preferentially balance the CPU load of each node, increase the CPU weight of the policy to **5** and retain the memory weight to **1**.
-  To preferentially balance the memory load of each node, increase the memory weight of the policy to **5** and retain the CPU weight to **1**.
-  To use bot the CPU and memory usage and the CPU and memory thresholds, do as follows:

   -  Hard constraints:

      -  After the CPU usage of a node exceeds its CPU threshold, do not schedule new loads to the node.
      -  After the memory usage of a node exceeds its memory threshold, do not schedule new loads to the node.

   -  Soft constraints:

      -  After the CPU usage of a node exceeds its CPU threshold, do not schedule new loads to the node as far as possible.
      -  After the memory usage of a node exceeds its memory threshold, do not schedule new loads to the node as far as possible.

   -  To balance the load of each node in a cluster while maximizing the cluster resource utilization, enable soft constraints for the CPU and memory thresholds and use the default value **80** for both the CPU and memory thresholds.
   -  To ensure workload stability and reduce the CPU and memory usage of heavy-load nodes, enable hard constraints for the CPU and memory thresholds and set the CPU and memory thresholds a value ranging from 60 to 80.

**Configuration case 2**

The status, workload traffic, and requests of a cluster change dynamically, and the resource usage of nodes changes in real time. Node imbalancing may recur after pod scheduling. Use both load-aware scheduling and descheduling for the optimal load balancing of cluster nodes. For details about hotspot descheduling, see :ref:`Descheduling <cce_10_0766>`.

#. Enable load-aware scheduling and use the default policy weight **5**. For details, see :ref:`Load-aware Scheduling <cce_10_0789>`.
#. Enable descheduling and configure the load-aware descheduling policy. For details, see :ref:`Descheduling <cce_10_0766>`.
#. Disable bin packing. For details, see :ref:`Bin Packing <cce_10_0773>`.

Recommended configurations:

-  Configure the load-aware descheduling policy as follows:

   -  **targetThreshold** for evicting pods from heavy-load nodes: Set the CPU threshold to **75** and memory threshold to **70**.
   -  **thresholds** for accepting pods on light-load nodes: Set both the CPU and memory thresholds to **30**.

-  Ensure the actual CPU or memory threshold is between the CPU or memory threshold of the heaviest-load node and that of the lightest-load node.

   -  Actual CPU threshold: **65**
   -  Actual memory threshold: **60**
