:original_name: cce_10_0296.html

.. _cce_10_0296:

Node Scaling Mechanisms
=======================

Kubernetes HPA is designed for pods. However, if the cluster resources are insufficient, you can only add nodes. Scaling of cluster nodes could be laborious. Now with clouds, you can add or delete nodes by simply calling APIs.

`autoscaler <https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler>`__ is a component provided by Kubernetes for auto scaling of cluster nodes based on the pod scheduling status and resource usage.

Prerequisites
-------------

Before using the node scaling function, you must install the :ref:`autoscaler <cce_10_0154>` add-on of v1.13.8 or later.

How autoscaler Works
--------------------

The cluster autoscaler (CA) goes through two processes.

-  Scale-out: The CA checks all unschedulable pods every 10 seconds and selects a node pool that meets the requirements for scale-out based on the policy you set.
-  Scale-in: The CA scans all nodes every 10 seconds. If the number of pod requests on a node is less than the user-defined percentage for scale-in, the CA simulates whether the pods on the node can be migrated to other nodes. If yes, the node will be removed after an idle time window.

As described above, if a cluster node is idle for a period of time (10 minutes by default), scale-in is triggered, and the idle node is removed.

However, a node cannot be removed from a cluster if the following pods exist:

#. Pods that do not meet specific requirements set in PodDisruptionBudget
#. Pods that cannot be scheduled to other nodes due to constraints such as affinity and anti-affinity policies
#. Pods that have the **"cluster-autoscaler.kubernetes.io/safe-to-evict": "false"** annotation
#. Pods (except those created by DaemonSets in the kube-system namespace) that exist in the kube-system namespace on the node
#. Pods that are not created by the controller (Deployment/ReplicaSet/job/StatefulSet)

autoscaler Architecture
-----------------------

:ref:`Figure 1 <cce_10_0296__fig114831750115719>` shows the autoscaler architecture and its core modules:

.. _cce_10_0296__fig114831750115719:

.. figure:: /_static/images/en-us_image_0000001569182553.png
   :alt: **Figure 1** autoscaler architecture

   **Figure 1** autoscaler architecture

**Description**

-  **Estimator**: Evaluates the number of nodes to be added to each node pool to host unschedulable pods.
-  **Simulator**: Finds the nodes that meet the scale-in conditions in the scale-in scenario.
-  **Expander**: Selects an optimal node from the node pool picked out by the Estimator based on the user-defined policy in the scale-out scenario. Currently, the Expander has the following policies:

   -  **Random**: Selects a node pool randomly. If you have not specified a policy, **Random** is set by default.
   -  **most-Pods**: Selects the node pool that can host the largest number of unschedulable pods after the scale-out. If multiple node pools meet the requirement, a random node pool will be selected.
   -  **least-waste**: Selects the node pool that has the least CPU or memory resource waste after scale-out.
   -  **price**: Selects the node pool in which the to-be-added nodes cost least for scale-out.
   -  **priority**: Selects the node pool with the highest weight. The weights are user-defined.

Currently, CCE supports all policies except **price**. By default, CCE add-ons use the **least-waste** policy.
