:original_name: cce_faq_00314.html

.. _cce_faq_00314:

Why Are Pods Not Evenly Distributed to Nodes?
=============================================

The kube-scheduler component in Kubernetes is responsible pod scheduling. For each newly created pod or other unscheduled pods, kube-scheduler selects an optimal node from them to run on. kube-scheduler selects a node for a pod in a 2-step operation: filtering and scoring. In the filtering step, all nodes where it is feasible to schedule the pod are filtered out. In the scoring step, kube-scheduler ranks the remaining nodes to choose the most suitable pod placement. Finally, kube-scheduler schedules the pod to the node with the highest score. If there is more than one node with the equal scores, kube-scheduler selects one of them at random.

BalancedResourceAllocation is only one of the scoring priorities. Other scoring items may also cause uneven distribution. For details about scheduling, see `Kubernetes Scheduler <https://kubernetes.io/docs/concepts/scheduling-eviction/kube-scheduler/>`__ and `Scheduling Policies <https://kubernetes.io/docs/reference/scheduling/policies/>`__.
