:original_name: cce_faq_00209.html

.. _cce_faq_00209:

What Should I Do If a Pod Fails to Be Evicted?
==============================================

Principle of Eviction
---------------------

When a node is abnormal, Kubernetes will evict pods on the node to ensure workload availability.

In Kubernetes, both kube-controller-manager and kubelet can evict pods.

-  **Eviction implemented by kube-controller-manager**

   kube-controller-manager consists of multiple controllers, and eviction is implemented by node controller. node controller periodically checks the status of all nodes. If a node is in the **NotReady** state for a period of time, all pods on the node will be evicted.

   kube-controller-manager supports the following startup parameters:

   -  **pod-eviction-timeout**: indicates an interval when a node is down, after which pods on that node are evicted. The default interval is 5 minutes.
   -  **node-eviction-rate**: indicates the number of nodes to be evicted per second. The default value is **0.1**, indicating that pods are evicted from one node every 10 seconds.
   -  **secondary-node-eviction-rate**: specifies a rate at which nodes are evicted in the second grade. If a large number of nodes are down in the cluster, the eviction rate will be reduced to **secondary-node-eviction-rate**. The default value is **0.01**.
   -  **unhealthy-zone-threshold**: specifies a threshold for an AZ to be considered unhealthy. The default value is **0.55**, meaning that if the percentage of faulty nodes in an AZ exceeds 55%, the AZ will be considered unhealthy.
   -  **large-cluster-size-threshold**: specifies a threshold for a cluster to be considered large. The parameter defaults to **50**. If there are more nodes than this threshold, the cluster is considered as a large one. If there are more than 55% faulty nodes in a cluster, the eviction rate is reduced to 0.01. If the cluster is a small one, the eviction rate is reduced to 0, which means, pods running on the nodes in the cluster will not be evicted.

-  **Eviction implemented by kubelet**

   If resources of a node are to be used up, kubelet executes the eviction policy based on the pod priority, resource usage, and resource request. If pods have the same priority, the pod that uses the most resources or requests for the most resources will be evicted first.

   kube-controller-manager evicts all pods on a faulty node, while kubelet evicts some pods on a faulty node. kubelet periodically checks the memory and disk resources of nodes. If the resources are insufficient, it will evict some pods based on the priority. For details about the pod eviction priority, see `Pod selection for kubelet eviction <https://kubernetes.io/docs/concepts/scheduling-eviction/node-pressure-eviction/#pod-selection-for-kubelet-eviction>`__.

   There are soft eviction thresholds and hard eviction thresholds.

   -  **Soft eviction thresholds**: A grace period is configured for node resources. kubelet will reclaim node resources associated with these thresholds if that grace period elapses. If the node resource usage reaches these thresholds but falls below them before the grace period elapses, kubelet will not evict pods on the node.

      You can configure soft eviction thresholds using the following parameters:

      -  **eviction-soft**: indicates a soft eviction threshold. If a node's `eviction signal <https://kubernetes.io/docs/concepts/scheduling-eviction/node-pressure-eviction/#eviction-signals>`__ reaches a certain threshold, for example, **memory.available<1.5Gi**, kubelet will not immediately evict some pods on the node but wait for a grace period configured by **eviction-soft-grace-period**. If the threshold is reached after the grace period elapses, kubelet will evict some pods on the node.
      -  **eviction-soft-grace-period**: indicates an eviction grace period. If a pod reaches the soft eviction threshold, it will be terminated after the configured grace period elapses. This parameter indicates the time difference for a terminating pod to respond to the threshold being met. The default grace period is 90 seconds.
      -  **eviction-max-pod-grace-period**: indicates the maximum allowed grace period to use when terminating pods in response to a soft eviction threshold being met.

   -  **Hard eviction thresholds**: Pods are immediately evicted once these thresholds are reached.

      You can configure hard eviction thresholds using the following parameters:

      **eviction-hard**: indicates a hard eviction threshold. When the `eviction signal <https://kubernetes.io/docs/concepts/scheduling-eviction/node-pressure-eviction/#eviction-signals>`__ of a node reaches a certain threshold, for example, **memory.available<1Gi**, which means, when the available memory of the node is less than 1 GiB, a pod eviction will be triggered immediately.

      kubelet supports the following default hard eviction thresholds:

      -  **memory.available<100Mi**
      -  **nodefs.available<10%**
      -  **imagefs.available<15%**
      -  **nodefs.inodesFree<5%** (for Linux nodes)

   kubelet also supports other parameters:

   -  **eviction-pressure-transition-period**: indicates a period for which the kubelet has to wait before transitioning out of an eviction pressure condition. The default value is 5 minutes. If the time exceeds the threshold, the node is set to **DiskPressure** or **MemoryPressure**. Then some pods running on the node will be evicted. This parameter can prevent mistaken eviction decisions when a node is oscillating above and below a soft eviction threshold in some cases.
   -  **eviction-minimum-reclaim**: indicates the minimum number of resources that must be reclaimed in each eviction. This parameter can prevent kubelet from repeatedly evicting pods because only a small number of resources are reclaimed during pod evictions in some cases.

Fault Locating
--------------

If the pods are not evicted when the node is faulty, perform the following steps to locate the fault:

After the following command is run, the command output shows that many pods are in the **Evicted** state.

.. code-block::

   kubectl get pods

Check results will be recorded in kubelet logs of the node. You can run the following command to search for the information:

.. code-block::

   cat /var/log/cce/kubernetes/kubelet.log | grep -i Evicted -C3

Troubleshooting Process
-----------------------

The issues here are described in order of how likely they are to occur.

Check these causes one by one until you find the cause of the fault.

-  :ref:`Check Item 1: Whether the Node Is Under Resource Pressure <cce_faq_00209__section1147819574162>`
-  :ref:`Check Item 2: Whether Tolerations Have Been Configured for the Workload <cce_faq_00209__section156641841181420>`
-  :ref:`Check Item 3: Whether the Conditions for Stopping Pod Eviction Are Met <cce_faq_00209__section9833172419151>`
-  :ref:`Check Item 4: Whether the Allocated Resources of the Pod Are the Same as Those of the Node <cce_faq_00209__section14911135124710>`
-  :ref:`Check Item 5: Whether the Workload Pod Fails Continuously and Is Redeployed <cce_faq_00209__section127261381585>`

.. _cce_faq_00209__section1147819574162:

Check Item 1: Whether the Node Is Under Resource Pressure
---------------------------------------------------------

If a node suffers resource pressure, kubelet will change the `node status <https://kubernetes.io/docs/reference/node/node-status/#condition>`__ and add taints to the node. Perform the following steps to check whether the corresponding taint exists on the node:

.. code-block::

   $ kubectl describe node 192.168.0.37
   Name:               192.168.0.37
   ...
   Taints:             key1=value1:NoSchedule
   ...

.. table:: **Table 1** Statuses of nodes with resource pressure and solutions

   +----------------+------------------------------------+------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
   | Node Status    | Taint                              | Eviction Signal                                                              | Description                                                                                                                |
   +================+====================================+==============================================================================+============================================================================================================================+
   | MemoryPressure | node.kubernetes.io/memory-pressure | memory.available                                                             | The available memory on the node reaches the eviction thresholds.                                                          |
   +----------------+------------------------------------+------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
   | DiskPressure   | node.kubernetes.io/disk-pressure   | nodefs.available, nodefs.inodesFree, imagefs.available or imagefs.inodesFree | The available disk space and inode on the root file system or image file system of the node reach the eviction thresholds. |
   +----------------+------------------------------------+------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
   | PIDPressure    | node.kubernetes.io/pid-pressure    | pid.available                                                                | The available process identifier on the node is below the eviction thresholds.                                             |
   +----------------+------------------------------------+------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+

.. _cce_faq_00209__section156641841181420:

Check Item 2: Whether Tolerations Have Been Configured for the Workload
-----------------------------------------------------------------------

Use kubectl or locate the row containing the target workload and choose **More** > **Edit YAML** in the **Operation** column to check whether tolerance is configured for the workload. For details, see `Taints and Tolerations <https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/>`__.

.. _cce_faq_00209__section9833172419151:

Check Item 3: Whether the Conditions for Stopping Pod Eviction Are Met
----------------------------------------------------------------------

In a cluster that runs less than 50 worker nodes, if the number of faulty nodes accounts for over 55% of the total nodes, the pod eviction will be suspended. In this case, Kubernetes will not attempt to evict the workload on the faulty node. For details, see `Rate limits on eviction <https://kubernetes.io/docs/concepts/architecture/nodes/#rate-limits-on-eviction>`__.

.. _cce_faq_00209__section14911135124710:

Check Item 4: Whether the Allocated Resources of the Pod Are the Same as Those of the Node
------------------------------------------------------------------------------------------

An evicted pod will be frequently scheduled to the original node.

**Possible Causes**

Pods on a node are evicted based on the node resource usage. The evicted pods are scheduled based on the allocated node resources. Eviction and scheduling are based on different rules. Therefore, an evicted container may be scheduled to the original node again.

**Solution**

Properly allocate resources to each container.

.. _cce_faq_00209__section127261381585:

Check Item 5: Whether the Workload Pod Fails Continuously and Is Redeployed
---------------------------------------------------------------------------

A workload pod fails and is being redeployed constantly.

**Analysis**

After a pod is evicted and scheduled to a new node, if pods in that node are also being evicted, the pod will be evicted again. Pods may be evicted repeatedly.

If a pod is evicted by kube-controller-manager, it would be in the **Terminating** state. This pod will be automatically deleted only after the node where the container is located is restored. If the node has been deleted or cannot be restored due to other reasons, you can forcibly delete the pod.

If a pod is evicted by kubelet, it would be in the **Evicted** state. This pod is only used for subsequent fault locating and can be directly deleted.

**Solution**

Run the following command to delete the evicted pods:

.. code-block::

   kubectl get pods <namespace> | grep Evicted | awk '{print $1}' | xargs kubectl delete pod <namespace>

In the preceding command, *<namespace>* indicates the namespace name. Configure it based on your requirements.

References
----------

`Kubelet does not delete evicted pods <https://github.com/kubernetes/kubernetes/issues/55051>`__
