:original_name: cce_01_0163.html

.. _cce_01_0163:

Setting Container Specifications
================================

Scenario
--------

CCE allows you to set resource limits for added containers during workload creation. You can request and limit the CPU and memory quotas used by each pod in the workload.

Meanings
--------

For **CPU** and **Memory**, the meanings of **Request** and **Limit** are as follows:

-  If **Request** is selected, the system schedules the pod to the node that meets the requirements for workload deployment based on the request value.
-  If **Request** is deselected, the system schedules the pod to a random node for workload deployment.
-  If **Limit** is selected, the system limits the resources used by the workload based on the preset value.
-  If **Limit** is deselected, the system does not limit the resources used by the pod. If the memory resources used by the pod exceed the memory allocated to the node, the workload or node may be unavailable.

.. note::

   When creating a workload, you are advised to set the upper and lower limits of CPU and memory resources. If the upper and lower resource limits are not set for a workload, a resource leak of this workload will make resources unavailable for other workloads deployed on the same node. In addition, workloads that do not have upper and lower resource limits cannot be accurately monitored.

For **GPU** quotas, the meanings of **Use** and **Any GPU type** are as follows:

-  If **Use** is selected, the system schedules the pod to a node that meets the requirements for workload deployment based on the configured value.
-  **Any GPU type** is selected by default and cannot be deselected. This option indicates that the resources used by pods are not limited.

Configuration Description
-------------------------

-  CPU quotas:

   .. table:: **Table 1** Description of CPU quotas

      +-------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter   | Description                                                                                                                                                                                                                                                                             |
      +=============+=========================================================================================================================================================================================================================================================================================+
      | CPU request | Minimum number of CPU cores required by a container. Resources are scheduled for the container based on this value. The container can be scheduled to this node only when the total available CPU on the node is greater than or equal to the number of containerized CPU applications. |
      +-------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | CPU limit   | Maximum number of CPU cores available for a container.                                                                                                                                                                                                                                  |
      +-------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   **Recommended configuration**

   Actual available CPU of a node >= Sum of CPU limits of all containers on the current node >= Sum of CPU requests of all containers on the current node. You can view the actual available CPUs of a node on the CCE console (**Resource Management** > **Nodes** > **Allocatable**).

-  Memory quotas:

   .. table:: **Table 2** Description of memory quotas

      +----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter      | Description                                                                                                                                                                                                                                                                                |
      +================+============================================================================================================================================================================================================================================================================================+
      | Memory request | Minimum amount of memory required by a container. Resources are scheduled for the container based on this value. The container can be scheduled to this node only when the total available memory on the node is greater than or equal to the number of containerized memory applications. |
      +----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Memory Limit   | Maximum amount of memory available for a container. When the memory usage exceeds the configured memory limit, the instance may be restarted, which affects the normal use of the workload.                                                                                                |
      +----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   **Recommended configuration**

   Actual available memory of a node >= Sum of memory limits of all containers on the current node >= Sum of memory requests of all containers on the current node. You can view the actual available memory of a node on the CCE console (**Resource Management** > **Nodes** > **Allocatable**).

.. note::

   The allocatable resources are calculated based on the resource request value (**Request**), which indicates the upper limit of resources that can be requested by pods on this node, but does not indicate the actual available resources of the node. The calculation formula is as follows:

   -  Allocatable CPU = Total CPU - Requested CPU of all pods - Reserved CPU for other resources
   -  Allocatable memory = Total memory - Requested memory of all pods - Reserved memory for other resources

Example
-------

Assume that a cluster contains a node with 4 cores and 8 GB. A workload containing two pods has been deployed on the cluster. The resources of the two pods (pods 1 and 2) are as follows: {CPU request, CPU limit, memory request, memory limit} = {1 core, 2 cores, 2 GB, 2 GB}.

The CPU and memory usage of the node is as follows:

-  Allocatable CPU = 4 cores - (1 core requested by pod 1 + 1 core requested by pod 2) = 2 cores
-  Allocatable memory = 8 GB - (2 GB requested by pod 1 + 2 GB requested by pod 2) = 4 GB

Therefore, the remaining 2 cores and 4 GB can be used by the next new pod.
