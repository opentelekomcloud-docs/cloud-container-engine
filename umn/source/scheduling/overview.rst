:original_name: cce_10_0702.html

.. _cce_10_0702:

Overview
========

CCE supports different types of resource scheduling and task scheduling, improving application performance and overall cluster resource utilization. This section describes the main functions of CPU resource scheduling, GPU/NPU heterogeneous resource scheduling, and Volcano scheduling.

CPU Scheduling
--------------

CCE provides CPU policies to allocate complete physical CPU cores to applications, improving application performance and reducing application scheduling latency.

+---------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------+
| Function            | Description                                                                                                                                                                                                                                                                                                                                                                                                         | Documentation                            |
+=====================+=====================================================================================================================================================================================================================================================================================================================================================================================================================+==========================================+
| CPU policy          | When many CPU-intensive pods are running on a node, workloads may be migrated to different CPU cores. Many workloads are not sensitive to this migration and thus work fine without any intervention. For CPU-sensitive applications, you can use the CPU policy provided by Kubernetes to allocate dedicated cores to applications, improving application performance and reducing application scheduling latency. | :ref:`CPU Policy <cce_10_0351>`          |
+---------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------+
| Enhanced CPU policy | Based on the Kubernetes static core binding policy, the enhanced CPU policy (enhanced-static) supports burstable pods (whose CPU requests and limits must be positive integers) and allows them to preferentially use certain CPUs to ensure application stability.                                                                                                                                                 | :ref:`Enhanced CPU Policy <cce_10_0552>` |
+---------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------+

GPU Scheduling
--------------

CCE schedules heterogeneous GPU resources in clusters and allows GPUs to be used in containers.

+--------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------+
| Function                             | Description                                                                                                                                     | Documentation                                             |
+======================================+=================================================================================================================================================+===========================================================+
| Default GPU scheduling in Kubernetes | This function allows you to specify the number of GPUs that a pod requests. The value can be less than 1 so that multiple pods can share a GPU. | :ref:`Default GPU Scheduling in Kubernetes <cce_10_0345>` |
+--------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------+

Volcano Scheduling
------------------

Volcano is a Kubernetes-based batch processing platform that supports machine learning, deep learning, bioinformatics, genomics, and other big data applications. It provides general-purpose, high-performance computing capabilities, such as job scheduling, heterogeneous chip management, and job running management.

+---------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------+
| Function                              | Description                                                                                                                                                           | Documentation                                        |
+=======================================+=======================================================================================================================================================================+======================================================+
| Scheduling workloads                  | Kubernetes typically uses its default scheduler to schedule workloads. To use Volcano, specify Volcano for your workloads.                                            | :ref:`Scheduling Workloads <cce_10_0722>`            |
+---------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------+
| Resource utilization-based scheduling | Scheduling policies are optimized for computing resources to effectively reduce resource fragments on each node and maximize computing resource utilization.          | :ref:`Resource Usage-based Scheduling <cce_10_0768>` |
+---------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------+
| Priority-based scheduling             | Scheduling policies are customized based on service importance and priorities to guarantee the resources of key services.                                             | :ref:`Priority-based Scheduling <cce_10_0774>`       |
+---------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------+
| AI performance-based scheduling       | Scheduling policies are configured based on the nature and resource usage of AI tasks to increase the throughput of cluster services and improve service performance. | :ref:`AI Performance-based Scheduling <cce_10_0776>` |
+---------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------+
| NUMA affinity scheduling              | Volcano targets to lift the limitation to make scheduler NUMA topology aware so that:                                                                                 | :ref:`NUMA Affinity Scheduling <cce_10_0425>`        |
|                                       |                                                                                                                                                                       |                                                      |
|                                       | -  Pods are not scheduled to the nodes that NUMA topology does not match.                                                                                             |                                                      |
|                                       | -  Pods are scheduled to the best node for NUMA topology.                                                                                                             |                                                      |
+---------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------+

Cloud Native Hybrid Deployment
------------------------------

The cloud native hybrid deployment solution focuses on the Volcano and Kubernetes ecosystems to help users improve resource utilization and efficiency and reduce costs.

+-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------+
| Function                          | Description                                                                                                                                                                                                                                                                                                                                | Documentation                                          |
+===================================+============================================================================================================================================================================================================================================================================================================================================+========================================================+
| Dynamic resource oversubscription | Based on the types of online and offline jobs, Volcano scheduling is used to utilize the resources that are requested but not used in the cluster (the difference between the number of requested resources and the number of used resources) for resource oversubscription and hybrid deployment to improve cluster resource utilization. | :ref:`Dynamic Resource Oversubscription <cce_10_0384>` |
+-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------+
