:original_name: cce_faq_00262.html

.. _cce_faq_00262:

How Do I Prevent a Container on a Node from Being Evicted?
==========================================================

Context
-------

During workload scheduling, two containers on a node may compete for resources. As a result, kubelet evicts both containers. This section describes how to set a policy to retain one of the containers.

Solution
--------

kubelet uses the following criteria to evict a pod:

-  Quality of Service (QoS) class: `BestEffort <https://kubernetes.io/docs/concepts/workloads/pods/pod-qos/#besteffort>`__, `Burstable <https://kubernetes.io/docs/concepts/workloads/pods/pod-qos/#burstable>`__, and `Guaranteed <https://kubernetes.io/docs/concepts/workloads/pods/pod-qos/#guaranteed>`__
-  Consumed resources based on the pod scheduling request

Pods of different QoS classes are evicted in the following sequence:

BestEffort -> Burstable -> Guaranteed

-  BestEffort pods: These pods have the lowest priority. They will be the first to be killed if the system runs out of memory.
-  Burstable pods: These pods will be killed if the system runs out of memory and no BestEffort pods exist.
-  Guaranteed pods: These pods will be killed if the system runs out of memory and no Burstable or BestEffort pods exist.

.. note::

   -  If a pod is killed because of excessive resource usage (while the node resources are still sufficient), the system tends to restart the pod on the same node.
   -  If resources are sufficient, you can assign the QoS class of Guaranteed to all pods. In this way, more compute resources are used to improve service performance and stability, reducing troubleshooting time and costs.
   -  To improve resource utilization, assign the QoS class of Guaranteed to service pods and Burstable or BestEffort to other pods (for example, filebeat).
