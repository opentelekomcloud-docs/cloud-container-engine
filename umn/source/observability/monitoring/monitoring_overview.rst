:original_name: cce_10_0182.html

.. _cce_10_0182:

Monitoring Overview
===================

CCE works with AOM to comprehensively monitor clusters. When a node is created, the ICAgent (the DaemonSet named **icagent** in the kube-system namespace of the cluster) of AOM is installed by default. The ICAgent collects monitoring data of underlying resources and workloads running on the cluster. It also collects monitoring data of custom metrics of the workload.

-  Resource metrics

   Basic resource monitoring includes CPU, memory, and disk monitoring. For details, see :ref:`Resource Metrics <cce_10_0182__section205486212251>`. You can view these metrics of clusters, nodes, and workloads on the CCE or AOM console.

-  Custom metrics

   The ICAgent collects custom metrics of applications and uploads them to AOM. For details, see :ref:`Monitoring Custom Metrics on AOM <cce_10_0201>`.

.. _cce_10_0182__section205486212251:

Resource Metrics
----------------

On the CCE console, you can view the following metrics.

-  :ref:`Viewing Cluster Monitoring Data <cce_10_0182__section1932383618498>`
-  :ref:`Viewing Monitoring Data of Worker Nodes <cce_10_0182__section965517431154>`
-  :ref:`Viewing Workload Monitoring Data <cce_10_0182__section2221948202013>`
-  :ref:`Viewing Pod Monitoring Data <cce_10_0182__section1799803015267>`

On the AOM console, you can view host metrics and container metrics.

.. _cce_10_0182__section1932383618498:

Viewing Cluster Monitoring Data
-------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. CCE allows you to view the monitoring data of all nodes. Choose **Clusters** from the navigation pane. Click the cluster name, and information like **CPU Metrics** and **Memory** of all nodes (excluding master nodes) in the last hour, the **Status**, **AZ** are displayed.

   .. table:: **Table 1** Cluster monitoring metrics

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Metric                            | Description                                                                                                                                                                               |
      +===================================+===========================================================================================================================================================================================+
      | CPU Allocation (%)                | A metric indicates the percentage of CPUs allocated to workloads.                                                                                                                         |
      |                                   |                                                                                                                                                                                           |
      |                                   | **CPU Allocation (%)** = Sum of CPU quotas requested by running pods in the cluster/Sum of CPU quotas that can be allocated from all nodes (excluding master nodes) to workloads          |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Memory Allocation (%)             | A metric indicates the percentage of memory allocated to workloads.                                                                                                                       |
      |                                   |                                                                                                                                                                                           |
      |                                   | **Memory Allocation (%)** = Sum of memory quotas requested by running pods in the cluster/Sum of memory quotas that can be allocated from all nodes (excluding master nodes) to workloads |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | CPU Usage (%)                     | A metric indicates the CPU usage of the cluster.                                                                                                                                          |
      |                                   |                                                                                                                                                                                           |
      |                                   | This metric is the average CPU usage of all nodes (excluding master nodes) in a cluster.                                                                                                  |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Memory Usage (%)                  | A metric indicates the memory usage of your cluster.                                                                                                                                      |
      |                                   |                                                                                                                                                                                           |
      |                                   | This metric is the average memory usage of all nodes (excluding master nodes) in a cluster.                                                                                               |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. note::

      Allocatable node resources (CPU or memory) = Total amount - Reserved amount - Eviction thresholds. For details, see :ref:`Node Resource Reservation Policy <cce_10_0178>`.

.. _cce_10_0182__section965517431154:

Viewing Monitoring Data of Worker Nodes
---------------------------------------

CCE also allows you to view monitoring data of a single node.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Nodes** in the navigation pane. On the right of the page, click **Monitor** of the target node to view the monitoring data.
#. You can select statistical **Dimension** and choose time range to view the monitoring data. The data is provided by AOM. You can view the monitoring data of a node, including the CPU, memory, disk, networking, and GPU.

   .. table:: **Table 2** Node monitoring metrics

      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Metric                            | Description                                                                                                                                                                                        |
      +===================================+====================================================================================================================================================================================================+
      | CPU Usage (%)                     | A metric indicates the CPU usage of the node.                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                    |
      |                                   | **CPU Usage (%)** = Used CPU cores/Total number of CPU cores                                                                                                                                       |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Used CPU Cores (cores)            | A metric indicates the number of used CPU cores.                                                                                                                                                   |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Physical Memory Usage (%)         | A metric indicates the physical memory usage of the node                                                                                                                                           |
      |                                   |                                                                                                                                                                                                    |
      |                                   | **Physical Memory Usage (%)** = (Physical memory capacity - Available physical memory)/Physical memory capacity                                                                                    |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Available Physical Memory (GiB)   | A metric indicates the unused physical memory of the node.                                                                                                                                         |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Disk Usage (%)                    | A metric indicates the disk usage of the file system on the data disk of the node. It is calculated based on the file partition. For details, see :ref:`Data Disk Space Allocation <cce_10_0341>`. |
      |                                   |                                                                                                                                                                                                    |
      |                                   | **Disk Usage (%)** = (Disk capacity - Available disk space)/Disk capacity                                                                                                                          |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Available Disk Space (GiB)        | A metric indicates the unused disk space.                                                                                                                                                          |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Downlink Rate (BPS) (KB/s)        | A metric indicates the speed at which data is downloaded from the Internet to the node.                                                                                                            |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Uplink Rate (BPS) (KB/s)          | A metric indicates the speed at which data is uploaded from the node to the Internet.                                                                                                              |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | GPU Usage (%)                     | A metric indicates the GPU usage of the node.                                                                                                                                                      |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | GPU Memory Usage (%)              | A metric indicates the percentage of the used GPU memory to the GPU memory capacity.                                                                                                               |
      |                                   |                                                                                                                                                                                                    |
      |                                   | **GPU Memory Usage (%)** = Used GPU memory/GPU memory capacity                                                                                                                                     |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Used GPU Memory (GiB)             | A metric indicates the used GPU memory.                                                                                                                                                            |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0182__section2221948202013:

Viewing Workload Monitoring Data
--------------------------------

CCE allows you to view monitoring data of a single workload.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Workloads** in the navigation pane. On the right of the page, click **Monitor** of the target workload. In the window that slides out from the right, the workload monitoring data is displayed.
#. You can select statistical **Dimension** and choose time range to view the monitoring data. The data is provided by AOM. You can view the monitoring data of a workload, including the CPU, memory, networking, and GPU.

   .. note::

      If there are multiple pods exist in the workload, the monitoring data may vary according to the statistical **Dimension**. For example, if you select **Maximum** or **Minimum** for **Dimension**, the value of each monitoring data is the maximum or minimum value of all pods under the workload. If **Average** is selected, the value of each monitoring data is the average value of all pods under the workload.

   .. table:: **Table 3** Workload monitoring metrics

      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Metric                            | Description                                                                                                                                                                       |
      +===================================+===================================================================================================================================================================================+
      | CPU Usage (%)                     | A metric indicates the CPU usage of the workload.                                                                                                                                 |
      |                                   |                                                                                                                                                                                   |
      |                                   | **CPU Usage (%)** = Used CPU cores/Total number of CPU cores of all running pods (If no limit is configured, the total number of the node's CPU cores is used.)                   |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Used CPU Cores (cores)            | A metric indicates the number of used CPU cores.                                                                                                                                  |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Physical Memory Usage (%)         | A metric indicates the physical memory usage of the workload.                                                                                                                     |
      |                                   |                                                                                                                                                                                   |
      |                                   | **Physical Memory Usage (%)** = Used physical memory/Total number of CPU cores of all running pods (If no limit is configured, the total number of the node's CPU cores is used.) |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Used Physical Memory (GiB)        | A metric indicates the amount of the used physical memory.                                                                                                                        |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Disk Read Rate                    | A metric indicates the data volume read from a disk per second. The unit is KB/s.                                                                                                 |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Disk Write Rate                   | A metric indicates the data volume written to a disk per second. The unit is KB/s.                                                                                                |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Downlink Rate (BPS) (KB/s)        | A metric indicates the speed at which data is downloaded from the Internet.                                                                                                       |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Uplink Rate (BPS) (KB/s)          | A metric indicates the speed at which data is uploaded from the node to the Internet                                                                                              |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | GPU Usage (%)                     | A metric indicates the GPU usage of the workload.                                                                                                                                 |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | GPU Memory Usage (%)              | A metric indicates the percentage of the used GPU memory to the GPU memory capacity.                                                                                              |
      |                                   |                                                                                                                                                                                   |
      |                                   | **GPU Memory Usage (%)** = Used GPU memory/GPU memory capacity                                                                                                                    |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Used GPU Memory (GiB)             | A metric indicates the used GPU memory.                                                                                                                                           |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0182__section1799803015267:

Viewing Pod Monitoring Data
---------------------------

CCE allows you to view the monitoring date of your pods.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Workloads** from the navigation pane. Then click the workload name of the target workload to list the pods.
#. Click **Monitor** of the target pod to view the monitoring data.
#. You can select statistical **Dimension** and choose time range to view the monitoring data. The data is provided by AOM. You can view the monitoring data of a pod, including the CPU, memory, disk, networking, and GPU.

   .. note::

      If multiple containers exist in a single pod, the monitoring data may vary according to the statistical **Dimension**. For example, if you select **Maximum** or **Minimum** for **Dimension**, the value of each monitoring data is the maximum or minimum value of all containers under the pod. If **Average** is selected, the value of each monitoring data is the average value of all containers in the pod.

   .. table:: **Table 4** Pod monitoring metrics

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Metric                            | Description                                                                                                                                                                                                                   |
      +===================================+===============================================================================================================================================================================================================================+
      | CPU Usage (%)                     | A metric indicates the CPU usage of the pod.                                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                               |
      |                                   | **CPU Usage (%)** = Used CPU cores/Total number of limited CPU cores of all running containers in the pod (If the limited CPU cores of all running containers are not specified, the number of the node's CPU cores is used.) |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Used CPU Cores (cores)            | A metric indicates the number of used CPU cores.                                                                                                                                                                              |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Physical Memory Usage (%)         | A metric indicates the physical memory usage of the pod.                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                               |
      |                                   | **Physical Memory Usage (%)** = Used physical memory/Sum of physical memory limits of all running containers in the pod (If not specified, the value of the node's physical memory is used.)                                  |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Used Physical Memory (GiB)        | A metric indicates the amount of the used physical memory.                                                                                                                                                                    |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Disk Read Rate                    | A metric indicates the data volume read from a disk per second. The unit is KB/s.                                                                                                                                             |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Disk Write Rate                   | A metric indicates the data volume written to a disk per second. The unit is KB/s.                                                                                                                                            |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Downlink Rate (BPS) (KB/s)        | A metric indicates the speed at which data is downloaded from the Internet.                                                                                                                                                   |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Uplink Rate (BPS) (KB/s)          | A metric indicates the speed at which data is uploaded from the node to the Internet.                                                                                                                                         |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | GPU Usage (%)                     | A metric indicates the GPU usage of the pod.                                                                                                                                                                                  |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | GPU Memory Usage (%)              | A metric indicates the percentage of the used GPU memory to the GPU memory capacity.                                                                                                                                          |
      |                                   |                                                                                                                                                                                                                               |
      |                                   | **GPU Memory Usage (%)** = Used GPU memory/GPU memory capacity                                                                                                                                                                |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Used GPU Memory (GiB)             | A metric indicates the used GPU memory of the pod.                                                                                                                                                                            |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
