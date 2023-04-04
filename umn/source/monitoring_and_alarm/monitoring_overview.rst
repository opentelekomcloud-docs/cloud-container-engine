:original_name: cce_10_0182.html

.. _cce_10_0182:

Monitoring Overview
===================

CCE works with AOM to comprehensively monitor clusters. When a node is created, the ICAgent (the DaemonSet named **icagent** in the kube-system namespace of the cluster) of AOM is installed by default. The ICAgent collects monitoring data of underlying resources and workloads running on the cluster. It also collects monitoring data of custom metrics of the workload.

-  Resource metrics

   Basic resource monitoring includes CPU, memory, and disk monitoring. For details, see :ref:`Resource Metrics <cce_10_0182__section205486212251>`. You can view these metrics of clusters, nodes, and workloads on the CCE or AOM console.

-  Custom metrics

   The ICAgent collects custom metrics of applications and uploads them to AOM. For details, see :ref:`Custom Monitoring <cce_10_0201>`.

.. _cce_10_0182__section205486212251:

Resource Metrics
----------------

On the CCE console, you can view the following metrics.

.. table:: **Table 1** Resource metrics

   +------------------------+------------------------------------------------------------------------------+
   | Metric                 | Description                                                                  |
   +========================+==============================================================================+
   | CPU Allocation Rate    | Indicates the percentage of CPUs allocated to workloads.                     |
   +------------------------+------------------------------------------------------------------------------+
   | Memory Allocation Rate | Indicates the percentage of memory allocated to workloads.                   |
   +------------------------+------------------------------------------------------------------------------+
   | CPU Usage              | Indicates the CPU usage.                                                     |
   +------------------------+------------------------------------------------------------------------------+
   | Memory Usage           | Indicates the memory usage.                                                  |
   +------------------------+------------------------------------------------------------------------------+
   | Disk Usage             | Indicates the disk usage.                                                    |
   +------------------------+------------------------------------------------------------------------------+
   | Down                   | Indicates the speed at which data is downloaded to a node. The unit is KB/s. |
   +------------------------+------------------------------------------------------------------------------+
   | Up                     | Indicates the speed at which data is uploaded from a node. The unit is KB/s. |
   +------------------------+------------------------------------------------------------------------------+
   | Disk Read Rate         | Indicates the data volume read from a disk per second. The unit is KB/s.     |
   +------------------------+------------------------------------------------------------------------------+
   | Disk Write Rate        | Indicates the data volume written to a disk per second. The unit is KB/s.    |
   +------------------------+------------------------------------------------------------------------------+

On the AOM console, you can view host metrics and container metrics.

Viewing Cluster Monitoring Data
-------------------------------

Click the cluster name and access the cluster console. In the navigation pane, choose **Cluster Information**. In the right pane, you can view the CPU and memory usage of all nodes (excluding master nodes) in the cluster in the last hour.

**Explanation of monitoring metrics:**

-  CPU allocation rate = Sum of CPU quotas requested by pods in the cluster/Sum of CPU quotas that can be allocated of all nodes (excluding master nodes) in the cluster
-  Memory allocation rate = Sum of memory quotas requested by pods in the cluster/Sum of memory quotas that can be allocated of all nodes (excluding master nodes) in the cluster
-  CPU usage: Average CPU usage of all nodes (excluding master nodes) in a cluster
-  Memory usage: Average memory usage of all nodes (excluding master nodes) in a cluster

.. note::

   Allocatable node resources (CPU or memory) = Total amount - Reserved amount - Eviction thresholds. For details, see :ref:`Formula for Calculating the Reserved Resources of a Node <cce_10_0178>`.

CCE provides the status, availability zone (AZ), CPU usage, and memory usage of master nodes.

Viewing Monitoring Data of Worker Nodes
---------------------------------------

In addition to viewing monitoring data of all nodes, you can also view monitoring data of a single node. Click the cluster name and access the cluster console. Choose **Nodes** in the navigation pane and click **Monitor** in the **Operation** column of the target node.

Monitoring data comes from AOM. You can view the monitoring data of a node, including the CPU, memory, disk, network, and GPU.

Viewing Workload Monitoring Data
--------------------------------

You can view monitoring data of a workload on the **Monitoring** tab page of the workload details page. Click the cluster name and access the cluster console. Choose **Workloads** in the navigation pane and click **Monitor** in the **Operation** column of the target workload.

Monitoring data comes from AOM. You can view the monitoring data of a workload, including the CPU, memory, network, and GPU, on the AOM console.

**Explanation of monitoring metrics:**

-  Workload CPU usage = Maximum CPU usage in each pod of the workload
-  Workload memory usage = Maximum memory usage in each pod of the workload

You can also click **View More** to go to the AOM console and view monitoring data of the workload.

Viewing Pod Monitoring Data
---------------------------

You can view monitoring data of a pod on the **Pods** tab page of the workload details page.

**Explanation of monitoring metrics:**

-  Pod CPU usage = The used CPU cores/The sum of all CPU limits of the pods (If not specified, all node CPU cores are used.)
-  Pod memory usage = The used physical memory/The sum of all memory limits of pods (If not specified, all node memory is used.)
