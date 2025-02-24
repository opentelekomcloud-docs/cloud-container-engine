:original_name: cce_10_0785.html

.. _cce_10_0785:

Scheduling
==========

Basic kube-scheduler configurations and Volcano-backed advanced scheduling are available. You can enable advanced scheduling functions such as bin packing, priority-based scheduling and preemption, AI task performance enhancement, and heterogeneous resource management for improved cluster resource utilization at low costs.

kube-scheduler
--------------

kube-scheduler provides the community native, standard scheduling capabilities.

Before **enabling volcano enhanced capabilities**, install Volcano Scheduler. Enabling this function will provide advanced scheduling capabilities, including optimizing resource utilization, enhancing AI job performance, and managing heterogeneous resources. This will ultimately improve cluster resource utilization and reduce costs.

Enhanced configurations of the Volcano scheduler:

-  :ref:`Priority-based Scheduling <cce_10_0785__section61971703016>`
-  :ref:`Resource Utilization Optimization Scheduling (Supported by the Volcano Scheduler) <cce_10_0785__section15912157011>`
-  :ref:`AI Task Performance Enhanced Scheduling (Supported by the Volcano Scheduler) <cce_10_0785__section56816252018>`
-  :ref:`Heterogeneous Resource Scheduling (Supported by the Volcano Scheduler) <cce_10_0785__section1697213291411>`

Scheduler Configuration
-----------------------

.. note::

   Only kube-scheduler supports this configuration.

.. table:: **Table 1** Parameters

   +---------------------------------------------+-----------------+---------------------------------------------+------------------------------------------------------------------------------------------+
   | Item                                        | Parameter       | Description                                 | Value                                                                                    |
   +=============================================+=================+=============================================+==========================================================================================+
   | QPS for communicating with kube-apiserver   | kube-api-qps    | QPS for communication with kube-apiserver   | -  If the number of nodes in a cluster is less than 1,000, the default value is **100**. |
   |                                             |                 |                                             | -  If the number of nodes in a cluster is 1,000 or more, the default value is **200**.   |
   +---------------------------------------------+-----------------+---------------------------------------------+------------------------------------------------------------------------------------------+
   | Burst for communicating with kube-apiserver | kube-api-burst  | Burst for communication with kube-apiserver | -  If the number of nodes in a cluster is less than 1,000, the default value is **100**. |
   |                                             |                 |                                             | -  If the number of nodes in a cluster is 1,000 or more, the default value is **200**.   |
   +---------------------------------------------+-----------------+---------------------------------------------+------------------------------------------------------------------------------------------+

.. _cce_10_0785__section61971703016:

Priority-based Scheduling
-------------------------

**Scheduling based on priority**

This is a basic scheduling capability and cannot be disabled. The scheduler preferentially guarantees the running of high-priority pods, and will not evict low-priority pods that are running.

.. _cce_10_0785__section15912157011:

Resource Utilization Optimization Scheduling (Supported by the Volcano Scheduler)
---------------------------------------------------------------------------------

**Bin packing**

With this option enabled, the cluster scheduler schedules pods to nodes that have the most requested resources. This reduces resource fragments on each node and improves the resource utilization of the cluster. For details, see :ref:`Bin Packing <cce_10_0773>`.

For details about the bin packing weight and weights for each resource to score nodes, see :ref:`Table 2 <cce_10_0785__table1938818529165>`.

.. _cce_10_0785__table1938818529165:

.. table:: **Table 2** Bin packing weight

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

**Enable load-aware scheduling (usage)**

This function uses the Cloud Native Cluster Monitoring (kube-prometheus-stack) add-on to obtain the actual CPU and memory load of each node, calculates the average load of each node based on the specified period, and preferentially schedules jobs to the node with the lightest load to balance load. For details, see :ref:`Load-aware Scheduling <cce_10_0789>`.

.. _cce_10_0785__section56816252018:

AI Task Performance Enhanced Scheduling (Supported by the Volcano Scheduler)
----------------------------------------------------------------------------

**Fair Scheduling Policy (DRF)**

Dominant Resource Fairness (DRF) is a scheduling algorithm based on the dominant resource of a container group. It supports fair allocation of multiple types of resources and is suitable for batch AI training and big data jobs. DRF is suitable for batch process small scale services like single AI model training and single big data computing and query, because it preferentially considers the throughput of services in clusters.

DRF helps you enhance the service throughput of clusters and improve service performance. For details, see :ref:`DRF <cce_10_0777>`.

**Workload Group Scheduling Policy (Gang)**

Gang scheduling meets the scheduling requirements of "All or nothing" in the scheduling process and avoids the waste of cluster resources caused by arbitrary scheduling of pods. It is mainly used in scenarios that require multi-process collaboration, such as AI and big data scenarios.

Gang scheduling effectively resolves pain points such as resource waiting or deadlocks in distributed training jobs, thereby significantly improving the utilization of cluster resources. For details, see :ref:`Gang <cce_10_0778>`.

.. _cce_10_0785__section1697213291411:

Heterogeneous Resource Scheduling (Supported by the Volcano Scheduler)
----------------------------------------------------------------------

**Support GPU resource scheduling**

To use this capability, the CCE AI Suite (NVIDIA GPU) add-on (:ref:`CCE AI Suite (NVIDIA GPU) <cce_10_0141>`) must be installed. With this option enabled, GPUs can be used for AI training jobs, and the scheduler provides full GPU dispatch and GPU sharing to improve resource utilization.
