:original_name: cce_10_0786.html

.. _cce_10_0786:

Auto Scaling
============

Auto Scale-Out Configuration
----------------------------

CCE Cluster Autoscaler comprehensively checks the resource statuses of an entire cluster. When the load of a microservice is high (for example, the CPU or memory usage is too high), it will add more pods to reduce the load.

**Node Capacity Expansion Conditions**

-  Auto scale-out when the workload cannot be scheduled: If a workload pod cannot be scheduled, the system automatically scales out the node pool with auto scaling enabled. If the pod has been configured to be affinity for a node, the system will not automatically add more nodes.

   Such auto scaling works with an HPA policy. For details, see :ref:`Using HPA and CA for Auto Scaling of Workloads and Nodes <cce_10_0300>`.

-  User-defined policy switch: specifies whether to automatically scale out a node pool based on the :ref:`node scaling policies <cce_10_0209>`. This function is enabled by default.

**Upper limit of resources to be expanded**

-  **Total Nodes**: specifies how many nodes can be present in a cluster during scale-out. If there are more nodes in the cluster than the specified value, the cluster will not add nodes. The default value is determined by how many nodes a cluster can manage at most.
-  **Total Cores**: specifies how many cores on all nodes can be present in a cluster during scale-out. If there are more cores in the cluster than the specified value, the cluster will not add nodes. By default, the number is not limited.
-  **Total Memory** (GiB): specifies the upper limit of the total memory of all nodes in a cluster during scale-out. If the total memory exceeds the specified value, the cluster will not add nodes. By default, the number is not limited.

.. note::

   When the total number of nodes, CPUs, and memory is collected, unavailable nodes in custom node pools are included but unavailable nodes in the default node pool are not included.

**Scale-Out Priority**

You can drag and drop node pools in the list to adjust their scale-out priorities.

Auto Scale-In Configuration
---------------------------

CCE Cluster Autoscaler comprehensively checks the resource statuses of an entire cluster. Once it confirms that workload pods can be scheduled and run properly, it automatically obtains nodes for scale-in.

**Node Scale-In Conditions**

-  **Node Resource Condition**: When the requested cluster node resources (both CPU and memory) are lower than a certain percentage (50% by default) for a period of time (10 minutes by default), a cluster scale-in is triggered.
-  **Node Status Condition**: If a node is unavailable for a specified period of time, the node will be automatically reclaimed. The default value is 20 minutes.
-  **Scale-in Exception Scenarios**: When a node meets the following exception scenarios, CCE will not scale in the node even if the node resources or status meets scale-in conditions:

   #. Resources on other nodes in the cluster are insufficient.
   #. Scale-in protection is enabled on the node. To enable or disable node scale-in protection, choose **Nodes** in the navigation pane and then click the **Nodes** tab. Locate the target node, choose **More**, and then enable or disable node scale-in protection in the **Operation** column.
   #. There is a pod with the non-scale label on the node.
   #. Policies such as reliability have been configured on some containers on the node.
   #. There are non-DaemonSet containers in the **kube-system** namespace on the node.
   #. (Optional) A container managed by a third-party pod controller is running on a node. Third-party pod controllers are for custom workloads except Kubernetes-native workloads such as Deployments and StatefulSets. Such controllers can be created using `CustomResourceDefinitions <https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions>`__.

**Node Scale-In Policy**

.. table:: **Table 1** Node scale-in policy configurations

   +----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | Item                                   | Description                                                                                                                                                                                                                                                                             | Default Value         |
   +========================================+=========================================================================================================================================================================================================================================================================================+=======================+
   | Number of Concurrent Scale-In Requests | Maximum number of idle nodes that can be deleted concurrently.                                                                                                                                                                                                                          | 10                    |
   |                                        |                                                                                                                                                                                                                                                                                         |                       |
   |                                        | Only idle nodes can be concurrently scaled in. Nodes that are not idle can only be scaled in one by one.                                                                                                                                                                                |                       |
   |                                        |                                                                                                                                                                                                                                                                                         |                       |
   |                                        | .. note::                                                                                                                                                                                                                                                                               |                       |
   |                                        |                                                                                                                                                                                                                                                                                         |                       |
   |                                        |    During a node scale-in, if the pods on the node do not need to be evicted (such as DaemonSet pods), the node is idle. Otherwise, the node is not idle.                                                                                                                               |                       |
   +----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | Node Recheck Timeout                   | Interval for rechecking a node that could not be removed                                                                                                                                                                                                                                | 5 minutes             |
   +----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | Cooldown Time                          | Cooldown period for starting scale-in evaluation again after auto scale-in is triggered in a cluster                                                                                                                                                                                    | 10 minutes            |
   |                                        |                                                                                                                                                                                                                                                                                         |                       |
   |                                        | .. note::                                                                                                                                                                                                                                                                               |                       |
   |                                        |                                                                                                                                                                                                                                                                                         |                       |
   |                                        |    If both auto scale-out and scale-in exist in a cluster, set this parameter to 0 minutes. This prevents the node scale-in from being blocked due to continuous scale-out of some node pools or retries upon a scale-out failure, which results in unexpected waste of node resources. |                       |
   +----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   |                                        | Cooldown period for starting scale-in evaluation again after auto scale-out is triggered in a cluster                                                                                                                                                                                   | 10 minutes            |
   +----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   |                                        | Cooldown period for starting scale-in evaluation again after auto scale-in triggered in a cluster failed                                                                                                                                                                                | 3 minutes             |
   +----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
