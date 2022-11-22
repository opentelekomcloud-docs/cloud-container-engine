:original_name: cce_faq_00140.html

.. _cce_faq_00140:

Instance Eviction Exception
===========================

When a node is faulty, pods on the node are evicted to ensure workload availability. If the pods are not evicted when the node is faulty, perform the following steps:

Check Item 1: Whether Tolerations Have Been Configured on the Pod
-----------------------------------------------------------------

Use kubectl or choose **More** > **Edit YAML** next to the corresponding workload to check whether tolerations are installed on the workload. For details, see https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/.

Check Item 2: Whether the Conditions for Stopping Pod Eviction Are Met
----------------------------------------------------------------------

If the number of nodes in a cluster is smaller than 50 and the number of faulty nodes accounts for over 55% of the total nodes, the pod eviction will be suspended. In this case, K8s will attempt to evict the workload on the faulty node. For details, see https://kubernetes.io/docs/concepts/architecture/nodes/.
