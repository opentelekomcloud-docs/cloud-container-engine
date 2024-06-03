:original_name: cce_10_0163.html

.. _cce_10_0163:

Configuring Container Specifications
====================================

Scenario
--------

CCE allows you to set resource requirements and limits, such as CPU and RAM, for added containers during workload creation. Kubernetes also allows using YAML to set requirements of other resource types.

Request and Limit
-----------------

For **CPU** and **Memory**, the meanings of **Request** and **Limit** are as follows:

-  **Request**: The system schedules a pod to the node that meets the requirements for workload deployment based on the request value.
-  **Limit**: The system limits the resources used by the workload based on the limit value.

If a node has sufficient resources, the pod on this node can use more resources than requested, but no more than limited.

For example, if you set the memory request of a container to 1 GiB and the limit value to 2 GiB, a pod is scheduled to a node with 8 GiB CPUs with no other pod running. In this case, the pod can use more than 1 GiB memory when the load is heavy, but the memory usage cannot exceed 2 GiB. If a process in a container attempts to use more than 2 GiB resources, the system kernel attempts to terminate the process. As a result, an out of memory (OOM) error occurs.

.. note::

   When creating a workload, you are advised to set the upper and lower limits of CPU and memory resources. If the upper and lower resource limits are not set for a workload, a resource leak of this workload will make resources unavailable for other workloads deployed on the same node. In addition, workloads that do not have upper and lower resource limits cannot be accurately monitored.

Configuration Description
-------------------------

In real-world scenarios, the recommended ratio of **Request** to **Limit** is about 1:1.5. For some sensitive services, the recommended ratio is 1:1. If the **Request** is too small and the **Limit** is too large, node resources are oversubscribed. During service peaks, the memory or CPU of a node may be used up. As a result, the node is unavailable.

-  CPU quota: The unit of CPU resources is core, which can be expressed by quantity or an integer suffixed with the unit (m). For example, 0.1 core in the quantity expression is equivalent to 100m in the expression. However, Kubernetes does not allow CPU resources whose precision is less than 1m.

   .. table:: **Table 1** CPU quotas

      +-------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter   | Description                                                                                                                                                                                                                                                                             |
      +=============+=========================================================================================================================================================================================================================================================================================+
      | CPU request | Minimum number of CPU cores required by a container. Resources are scheduled for the container based on this value. The container can be scheduled to this node only when the total available CPU on the node is greater than or equal to the number of containerized CPU applications. |
      +-------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | CPU limit   | Maximum number of CPU cores available for a container.                                                                                                                                                                                                                                  |
      +-------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   **Recommended configuration**

   Actual available CPU of a node >= Sum of CPU limits of all containers on the current node >= Sum of CPU requests of all containers on the current node. You can view the actual available CPUs of a node on the CCE console (**Resource Management** > **Nodes** > **Allocatable**).

-  Memory quota: The default unit of memory resources is byte. You can also use an integer with the unit suffix, for example, 100 Mi. Note that the unit is case-sensitive.

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

   The allocatable resources are calculated based on the resource request value (**Request**), which indicates the upper limit of resources that can be requested by pods on this node, but does not indicate the actual available resources of the node (for details, see :ref:`Example of CPU and Memory Quota Usage <cce_10_0163__section17887209103612>`). The calculation formula is as follows:

   -  Allocatable CPU = Total CPU - Requested CPU of all pods - Reserved CPU for other resources
   -  Allocatable memory = Total memory - Requested memory of all pods - Reserved memory for other resources

.. _cce_10_0163__section17887209103612:

Example of CPU and Memory Quota Usage
-------------------------------------

Assume that a cluster contains a node with 4 CPU cores and 8 GiB memory. Two pods (pod 1 and pod 2) have been deployed on the cluster. Pod 1 oversubscribes resources (that is **Limit** > **Request**). The specifications of the two pods are as follows.

===== =========== ========= ============== ============
Pod   CPU Request CPU Limit Memory Request Memory Limit
===== =========== ========= ============== ============
Pod 1 1 core      2 cores   1 GiB          4 GiB
Pod 2 2 cores     2 cores   2 GiB          2 GiB
===== =========== ========= ============== ============

The CPU and memory usage of the node is as follows:

-  Allocatable CPUs = 4 cores - (1 core requested by pod 1 + 2 cores requested by pod 2) = 1 core
-  Allocatable memory = 8 GiB - (1 GiB requested by pod 1 + 2 GiB requested by pod 2) = 5 GiB

In this case, the remaining 1 core 5 GiB can be used by the next new pod.

If pod 1 is under heavy load during peak hours, it will use more CPUs and memory within the limit. Therefore, the actual allocatable resources are fewer than 1 core 5 GiB.

Quotas of Other Resources
-------------------------

Typically, nodes support local ephemeral storage, which is provided by locally mounted writable devices or RAM. Ephemeral storage does not ensure long-term data availability. Pods can use local ephemeral storage to buffer data and store logs, or mount emptyDir storage volumes to containers. For details, see `Local ephemeral storage <https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#local-ephemeral-storage>`__.

Kubernetes allows you to specify the requested value and limit value of ephemeral storage in container configurations to manage the local ephemeral storage. The following attributes can be configured for each container in a pod:

-  spec.containers[].resources.limits.ephemeral-storage

-  spec.containers[].resources.requests.ephemeral-storage

In the following example, a pod contains two containers. The requested value of each container for local ephemeral storage is 2 GiB, and the limit value is 4 GiB. Therefore, the requested value of the pod for local ephemeral storage is 4 GiB, the limit value is 8 GiB, and the emptyDir volume uses 500 MiB of the local ephemeral storage.

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     name: frontend
   spec:
     containers:
     - name: container-1
       image: <example_app_image>
       resources:
         requests:
           ephemeral-storage: "2Gi"
         limits:
           ephemeral-storage: "4Gi"
       volumeMounts:
       - name: ephemeral
         mountPath: "/tmp"
     - name: container-2
       image: <example_log_aggregator_image>
       resources:
         requests:
           ephemeral-storage: "2Gi"
         limits:
           ephemeral-storage: "4Gi"
       volumeMounts:
       - name: ephemeral
         mountPath: "/tmp"
     volumes:
       - name: ephemeral
         emptyDir:
           sizeLimit: 500Mi
