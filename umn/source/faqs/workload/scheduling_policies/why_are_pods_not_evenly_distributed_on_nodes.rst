:original_name: cce_faq_00314.html

.. _cce_faq_00314:

Why Are Pods Not Evenly Distributed on Nodes?
=============================================

Pod Scheduling Principles in Kubernetes
---------------------------------------

The kube-scheduler component in Kubernetes is responsible for pod scheduling. For each newly created pod or other unscheduled pods, kube-scheduler selects an optimal node from them to run on. kube-scheduler selects a node for a pod in a 2-step operation: filtering and scoring. In the filtering step, all nodes where it is feasible to schedule the pod are filtered out. In the scoring step, kube-scheduler ranks the remaining nodes to choose the most suitable pod placement. Finally, kube-scheduler schedules the pod to the node with the highest score. If there is more than one node with the equal scores, kube-scheduler selects one of them at random.

BalancedResourceAllocation is only one of the scoring priorities. Other scoring items may also cause uneven distribution. For details about scheduling, see `Kubernetes Scheduler <https://kubernetes.io/docs/concepts/scheduling-eviction/kube-scheduler/>`__ and `Scheduling Policies <https://kubernetes.io/docs/reference/scheduling/policies/>`__.

Possible Causes of Why Pods Are Not Evenly Distributed on Nodes
---------------------------------------------------------------

-  Resource configurations may vary between nodes, including differences in CPU and memory size. This can result in the pods' defined requests not being met, even if a node's actual load is low. As a result, the pod cannot be scheduled to the node.
-  Custom scheduling policies can be used to schedule pods based on affinity and anti-affinity rules, resulting in uneven distribution of pods across nodes.
-  Nodes can have taints that prevent unscheduled pods from being assigned to them if tolerations are not set.
-  Certain workloads may have unique distribution constraints. For instance, if an EVS disk is attached to a workload, the workload pods can only be scheduled to nodes within the same AZ as the disk.
-  Certain pods may require specific resources, such as GPUs. In such cases, the scheduler can only assign those pods to GPU nodes.
-  The health and status of a node can impact scheduling decisions. If a node is unhealthy, it may not be able to accept new pods.

Possible Causes of Why Pod Loads Are Unevenly Distributed on Nodes
------------------------------------------------------------------

When allocating pods, kube-scheduler does not take into account the actual load of applications. This can result in some nodes being heavily loaded while others are lightly loaded, especially if the application load is uneven.
