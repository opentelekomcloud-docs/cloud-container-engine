:original_name: cce_faq_00209.html

.. _cce_faq_00209:

What Should I Do If a Pod Fails to Be Evicted?
==============================================

What Is Eviction
----------------

When an exception occurs on a node, Kubernetes evicts the pods on the node to ensure the workload availability.

In Kubernetes, both kube-controller-manager and kubelet can evict pods.

-  **Eviction implemented by kube-controller-manager**

   kube-controller-manager consists of multiple controllers, and eviction is implemented by the node controller. The controller periodically checks the status of all nodes. When a node is in the **NotReady** state for a period of time, all pods on the node are evicted.

   kube-controller-manager provides the following startup parameters to control evictions:

   -  **pod-eviction-timeout**: an interval when a node is down, after which pods on that node are evicted. The default interval is 5 minutes.
   -  **node-eviction-rate**: a rate at which nodes are evicted, which is implemented by the token bucket traffic control algorithm. The default value is **0.1**, indicating that 0.1 nodes are evicted per second. Note that this rate is not the rate at which pods are evicted, but the rate at which nodes are evicted. That is, one node is cleared every 10 seconds.
   -  **secondary-node-eviction-rate**: secondary eviction rate. When a large number of nodes are down in the cluster, the eviction rate decreases. The default value is **0.01**.
   -  **unhealthy-zone-threshold**: threshold for a zone to be considered unhealthy. This parameter determines when to enable the secondary eviction rate. The default value is **0.55**. That is, if the percentage of down nodes in a zone exceeds 55%, the zone is unhealthy.
   -  **large-cluster-size-threshold**: threshold for a cluster to be considered large. When the number of nodes in a zone exceeds this threshold, the zone is considered as a large cluster. If the percentage of down nodes in a large cluster exceeds 55%, the eviction rate is reduced to 0.01. If the cluster is a small one, the eviction rate is reduced to 0.

-  **Eviction implemented by kubelet**

   If resources of a node are to be used up, kubelet executes the eviction policy based on the pod priority, resource usage, and resource request. If pods have the same priority, the pod that uses the most resources or requests for the most resources will be evicted first.

   kube-controller-manager evicts all pods on a node, whereas kubelet evicts certain pods on a node. Pods to be evicted are determined by the pod QoS. kubelet periodically checks the memory and disk resources of the node. If the resources are insufficient, pods are evicted based on the priority.

   There are soft eviction thresholds and hard eviction thresholds.

   -  **Soft eviction threshold**: A grace period is set for node resources. kubelet will reclaim node resources associated with this threshold if that grace period is exceeded. If the node resource usage reaches this threshold but falls below it before the grace period is exceeded, kubelet will not evict pods on the node.
   -  **Hard eviction threshold**: Pods are immediately evicted once this threshold is reached.

   kubelet provides the following parameters to control evictions:

   -  **eviction-soft**: soft eviction thresholds. If, for example, **memory.available** is less than 1.5 GiB, pods will be evicted only after the grace period specified by **eviction-soft-grace-period** is exceeded.
   -  **eviction-soft-grace-period**: an eviction grace period. It is used to terminate the grace period of a pod to be evicted by **eviction-soft**. The default value is 90 seconds.
   -  **eviction-max-pod-grace-period**: the maximum allowed grace period to use when terminating pods in response to a soft eviction threshold being met
   -  **eviction-pressure-transition-period**: a duration for which kubelet has to wait before transitioning out of an eviction pressure condition. The default value is 5 minutes. If the time exceeds the threshold, the node is set to **memory pressure** or **disk pressure**, and then pod eviction is started.
   -  **eviction-minimum-reclaim**: the minimum number of resources that must be reclaimed in each eviction
   -  **eviction-hard**: hard eviction thresholds. If, for example, **memory.available** is less than 1 GiB, the pod will be evicted.

Fault Locating
--------------

If the pods are not evicted when the node is faulty, perform the following steps to locate the fault:

After the following command is run, the command output shows that many pods are in the **Evicted** state.

.. code-block::

   kubectl get pods

Check results will be recorded in kubelet logs of the node. You can run the following command to search for the information:

.. code-block::

   cat /var/paas/sys/log/kubernetes/kubelet.log | grep -i Evicted -C3

Troubleshooting Process
-----------------------

Troubleshooting methods are sorted based on the occurrence probability of the possible causes. You are advised to check the possible causes from high probability to low probability to quickly locate the cause of the problem.

If the fault persists after a possible cause is rectified, check other possible causes.

-  :ref:`Check Item 1: Whether Tolerations Have Been Configured on the Pod <cce_faq_00209__section156641841181420>`
-  :ref:`Check Item 2: Whether the Conditions for Stopping Pod Eviction Are Met <cce_faq_00209__section9833172419151>`
-  :ref:`Check Item 3: Whether the Allocated Resources of the Container Are the Same as Those of the Node <cce_faq_00209__section14911135124710>`
-  :ref:`Check Item 4: Whether the Pod Fails Continuously and Is Redeployed <cce_faq_00209__section127261381585>`


.. figure:: /_static/images/en-us_image_0000001656255658.png
   :alt: **Figure 1** Troubleshooting process for pod eviction exception

   **Figure 1** Troubleshooting process for pod eviction exception

.. _cce_faq_00209__section156641841181420:

Check Item 1: Whether Tolerations Have Been Configured on the Pod
-----------------------------------------------------------------

Use kubectl, or locate the row containing the target workload and choose **More** > **Edit YAML** in the **Operation** column to check whether tolerance is configured for the workload. For details, see `Taints and Tolerations <https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/>`__.

.. _cce_faq_00209__section9833172419151:

Check Item 2: Whether the Conditions for Stopping Pod Eviction Are Met
----------------------------------------------------------------------

If the number of nodes in a cluster is smaller than 50 and the number of faulty nodes accounts for over 55% of the total nodes, the pod eviction will be suspended. In this case, Kubernetes will not attempt to evict the workload on the faulty node. For details, see `Rate limits on eviction <https://kubernetes.io/docs/concepts/architecture/nodes/#rate-limits-on-eviction>`__.

.. _cce_faq_00209__section14911135124710:

Check Item 3: Whether the Allocated Resources of the Container Are the Same as Those of the Node
------------------------------------------------------------------------------------------------

An evicted container is frequently scheduled to the original node.

**Possible Causes**

A node evicts a container based on the node resource usage. The evicted container is scheduled based on the allocated node resources. Eviction and scheduling are based on different rules. Therefore, an evicted container may be scheduled to the original node again.

**Solution**

Properly allocate resources to each container.

.. _cce_faq_00209__section127261381585:

Check Item 4: Whether the Pod Fails Continuously and Is Redeployed
------------------------------------------------------------------

A pod in the workload fails and is being redeployed constantly.

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
