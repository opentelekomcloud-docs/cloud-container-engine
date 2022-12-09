:original_name: cce_01_0182.html

.. _cce_01_0182:

Monitoring Overview
===================

CCE works with AOM to comprehensively monitor clusters. When a node is created, the ICAgent (the DaemonSet named **icagent** in the kube-system namespace of the cluster) of AOM is installed by default. The ICAgent collects monitoring data of underlying resources and workloads running on the cluster. It also collects monitoring data of custom metrics of the workload.

-  Resource metrics

   Basic resource monitoring includes CPU, memory, and disk monitoring. For details, see :ref:`Resource Metrics <cce_01_0182__section205486212251>`. You can view these metrics of clusters, nodes, and workloads on the CCE or AOM console.

.. important::

   AOM is available only in certain regions.

.. _cce_01_0182__section205486212251:

Resource Metrics
----------------

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

Viewing Cluster Monitoring Data
-------------------------------

In the navigation pane of the CCE console, choose **Resource Management** > **Clusters**. Click |image1| on the cluster card to access the cluster monitoring page.

|image2|

The cluster monitoring page displays the monitoring status of cluster resources, CPU, memory, and disk usage of all nodes in a cluster, and CPU and memory allocation rates.

**Explanation of monitoring metrics:**

-  CPU allocation rate = Sum of CPU quotas requested by pods in the cluster/Sum of CPU quotas that can be allocated of all nodes (excluding master nodes) in the cluster
-  Memory allocation rate = Sum of memory quotas requested by pods in the cluster/Sum of memory quotas that can be allocated of all nodes (excluding master nodes) in the cluster
-  CPU usage: Average CPU usage of all nodes (excluding master nodes) in a cluster
-  Memory usage: Average memory usage of all nodes (excluding master nodes) in a cluster

.. note::

   Allocatable node resources (CPU or memory) = Total amount - Reserved amount - Eviction thresholds. For details, see :ref:`Formula for Calculating the Reserved Resources of a Node <cce_01_0178>`.

On the cluster monitoring page, you can also view monitoring data of nodes, workloads, and pods. You can click |image3| to view the detailed data.

|image4|

Viewing Monitoring Data of Master Nodes
---------------------------------------

CCE allows you to view monitoring data of master nodes. You can view the monitoring data of a master node in the upper right corner of the cluster details page. Clicking **More** will direct you to the AOM console.

Viewing Monitoring Data of Worker Nodes
---------------------------------------

In addition to the cluster monitoring page, you can also view node monitoring data on the node console by clicking **Monitoring** in the row where the node resides.

The node list page also displays the data about the allocable resources of the node. **Allocatable resources** indicate the upper limit of resources that can be requested by pods on a node, and are calculated based on the requests. Allocatable resources do not indicate the actual available resources of the node.

The calculation formulas are as follows:

-  Allocatable CPU = Total CPU - Requested CPU of all pods - Reserved CPU for other resources
-  Allocatable memory = Total memory - Requested memory of all pods - Reserved memory for other resources

Viewing Workload Monitoring Data
--------------------------------

You can view monitoring data of a workload on the **Monitoring** tab page of the workload details page.

You can also click **AOM** to go to the AOM console to view monitoring data of the workload.

Viewing Pod Monitoring Data
---------------------------

You can view monitoring data of a pod on the **Pods** tab page of the workload details page.

.. |image1| image:: /_static/images/en-us_image_0000001222591781.png
.. |image2| image:: /_static/images/en-us_image_0000001221007635.png
.. |image3| image:: /_static/images/en-us_image_0000001221376671.png
.. |image4| image:: /_static/images/en-us_image_0000001176255102.png
