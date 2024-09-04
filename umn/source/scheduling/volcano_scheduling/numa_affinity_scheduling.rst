:original_name: cce_10_0425.html

.. _cce_10_0425:

NUMA Affinity Scheduling
========================

Background
----------

When a node runs many CPU-bound pods, the workload can move to different CPU cores depending on whether the pod is throttled and which CPU cores are available at scheduling time. Many workloads are not sensitive to this migration and work fine without any intervention. However, in workloads where CPU cache affinity and scheduling latency significantly affect workload performance, additional latency will occur when CPU cores are from different NUMA nodes. To resolve this issue, kubelet allows you to use Topology Manager to replace the CPU management policies to determine node allocation.

Both the CPU Manager and Topology Manager are kubelet components, but they have the following limitations:

-  The scheduler is not topology-aware. Therefore, the workload may be scheduled on a node and then fail on the node due to the Topology Manager. This is unacceptable for TensorFlow jobs. If any worker or ps failed on node, the job will fail.
-  The managers are node-level that results in an inability to match the best node for NUMA topology in the whole cluster.

Volcano targets to lift the limitation to make scheduler NUMA topology aware so that:

-  Pods are not scheduled to the nodes that NUMA topology does not match.
-  Pods are scheduled to the most suitable node for NUMA topology.

For more information, see https://github.com/volcano-sh/volcano/blob/master/docs/design/numa-aware.md.

Application Scope
-----------------

-  CPU resource topology scheduling
-  Pod-level topology policies

.. _cce_10_0425__section2430103110429:

Pod Scheduling Prediction
-------------------------

After a topology policy is configured for pods, Volcano predicts matched nodes based on the topology policy. The scheduling process is as follows:

#. Volcano filters nodes with the same policy based on the topology policy configured for pods. The topology policy provided by Volcano is the same as that provided by the `topology manager <https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/>`__.
#. Among the nodes where the same policy applies, Volcano selects the nodes whose CPU topology meets the policy requirements for scheduling.

+-------------------------+-----------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Volcano Topology Policy | Node Scheduling                                                 |                                                                                                                                                                                                                                                         |
+=========================+=================================================================+=========================================================================================================================================================================================================================================================+
|                         | 1. Filter nodes with the same policy.                           | 2. Check whether node's CPU topology meets the policy requirements.                                                                                                                                                                                     |
+-------------------------+-----------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| none                    | No filtering:                                                   | None                                                                                                                                                                                                                                                    |
|                         |                                                                 |                                                                                                                                                                                                                                                         |
|                         | -  **none**: schedulable                                        |                                                                                                                                                                                                                                                         |
|                         | -  **best-effort**: schedulable                                 |                                                                                                                                                                                                                                                         |
|                         | -  **restricted**: schedulable                                  |                                                                                                                                                                                                                                                         |
|                         | -  **single-numa-node**: schedulable                            |                                                                                                                                                                                                                                                         |
+-------------------------+-----------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| best-effort             | Filter the nodes with the **best-effort** topology policy.      | Best-effort scheduling:                                                                                                                                                                                                                                 |
|                         |                                                                 |                                                                                                                                                                                                                                                         |
|                         | -  **none**: unschedulable                                      | Pods are preferentially scheduled to a single NUMA node. If a single NUMA node cannot meet the requested CPU cores, the pods can be scheduled to multiple NUMA nodes.                                                                                   |
|                         | -  **best-effort**: schedulable                                 |                                                                                                                                                                                                                                                         |
|                         | -  **restricted**: unschedulable                                |                                                                                                                                                                                                                                                         |
|                         | -  **single-numa-node**: unschedulable                          |                                                                                                                                                                                                                                                         |
+-------------------------+-----------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| restricted              | Filter the nodes with the **restricted** topology policy.       | Restricted scheduling:                                                                                                                                                                                                                                  |
|                         |                                                                 |                                                                                                                                                                                                                                                         |
|                         | -  **none**: unschedulable                                      | -  If the upper CPU limit of a single NUMA node is greater than or equal to the requested CPU cores, pods can only be scheduled to a single NUMA node. If the remaining CPU cores of a single NUMA node are insufficient, the pods cannot be scheduled. |
|                         | -  **best-effort**: unschedulable                               | -  If the upper CPU limit of a single NUMA node is less than the requested CPU cores, pods can be scheduled to multiple NUMA nodes.                                                                                                                     |
|                         | -  **restricted**: schedulable                                  |                                                                                                                                                                                                                                                         |
|                         | -  **single-numa-node**: unschedulable                          |                                                                                                                                                                                                                                                         |
+-------------------------+-----------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| single-numa-node        | Filter the nodes with the **single-numa-node** topology policy. | Pods can only be scheduled to a single NUMA node.                                                                                                                                                                                                       |
|                         |                                                                 |                                                                                                                                                                                                                                                         |
|                         | -  **none**: unschedulable                                      |                                                                                                                                                                                                                                                         |
|                         | -  **best-effort**: unschedulable                               |                                                                                                                                                                                                                                                         |
|                         | -  **restricted**: unschedulable                                |                                                                                                                                                                                                                                                         |
|                         | -  **single-numa-node**: schedulable                            |                                                                                                                                                                                                                                                         |
+-------------------------+-----------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

For example, two NUMA nodes provide resources, each with a total of 32 CPU cores. The following table lists resource allocation.

+-------------+----------------------+--------------------------------+--------------------------------+
| Worker Node | Node Topology Policy | Total CPU Cores on NUMA Node 1 | Total CPU Cores on NUMA Node 2 |
+=============+======================+================================+================================+
| Node 1      | best-effort          | 16                             | 16                             |
+-------------+----------------------+--------------------------------+--------------------------------+
| Node 2      | restricted           | 16                             | 16                             |
+-------------+----------------------+--------------------------------+--------------------------------+
| Node 3      | restricted           | 16                             | 16                             |
+-------------+----------------------+--------------------------------+--------------------------------+
| Node 4      | single-numa-node     | 16                             | 16                             |
+-------------+----------------------+--------------------------------+--------------------------------+

:ref:`Figure 1 <cce_10_0425__fig1216082014438>` shows the scheduling of a pod after a topology policy is configured.

-  When 9 CPU cores are requested by a pod and the **best-effort** topology policy is used, Volcano selects node 1 whose topology policy is also **best-effort**, and this policy allows the pod to be scheduled to multiple NUMA nodes. Therefore, the requested 9 CPU cores will be allocated to two NUMA nodes, and the pod can be scheduled to node 1.
-  When 9 CPU cores are requested by a pod and the **restricted** topology policy is used, Volcano selects nodes 2 and 3 whose topology policy is also **restricted**, and each node provides a total of 9 CPU cores. However, the remaining CPU cores on node 2 or 3 are less than the requested. Therefore, the pod cannot be scheduled.
-  When 17 CPU cores are requested by a pod and the **restricted** topology policy is used, Volcano selects nodes 2 and 3 whose topology policy is also **restricted**, this policy allows the pod to be scheduled to multiple NUMA nodes, and the upper CPU limit of both the nodes is less than 17. Then, the pod can be scheduled to node 3.
-  When 17 CPU cores are requested by a pod and the **single-numa-node** topology policy is used, Volcano selects nodes whose topology policy is also **single-numa-node**. However, no node can provide a total of 17 CPU cores. Therefore, the pod cannot be scheduled.

.. _cce_10_0425__fig1216082014438:

.. figure:: /_static/images/en-us_image_0000001981436181.png
   :alt: **Figure 1** Comparison of NUMA scheduling policies

   **Figure 1** Comparison of NUMA scheduling policies

Scheduling Priority
-------------------

A topology policy aims to schedule pods to the optimal node. In this example, each node is scored to sort out the optimal node.

Principle: Schedule pods to the worker nodes that require the fewest NUMA nodes.

The scoring formula is as follows:

score = weight x (100 - 100 x numaNodeNum/maxNumaNodeNum)

Parameters:

-  **weight**: the weight of NUMA Aware Plugin.
-  **numaNodeNum**: the number of NUMA nodes required for running the pod on worker nodes.
-  **maxNumaNodeNum**: the maximum number of NUMA nodes required for running the pod among all worker nodes.

For example, three nodes meet the CPU topology policy for a pod and the weight of NUMA Aware Plugin is set to **10**.

-  Node A: One NUMA node provides the CPU resources required by the pod (numaNodeNum = 1).
-  Node B: Two NUMA nodes provide the CPU resources required by the pod (numaNodeNum = 2).
-  Node C: Four NUMA nodes provide the CPU resources required by the pod (numaNodeNum = 4).

According to the preceding formula, **maxNumaNodeNum** is **4**.

-  score(Node A) = 10 x (100 - 100 x 1/4) = 750
-  score(Node B) = 10 x (100 - 100 x 2/4) = 500
-  score(Node C) = 10 x (100 - 100 x 4/4) = 0

Therefore, the optimal node is Node A.

Enabling NUMA Affinity Scheduling for Volcano
---------------------------------------------

#. Enable static CPU management. For details, see :ref:`Enabling the CPU Management Policy <cce_10_0351__section173918176434>`.

#. Configure a CPU topology policy.

   a. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Nodes**. On the right of the page, click the **Node Pools** tab and choose **More** > **Manage** in the **Operation** column of the target node pool.

   b. Change the kubelet **Topology Management Policy (topology-manager-policy)** value to the required CPU topology policy.

      Valid topology policies include **none**, **best-effort**, **restricted**, and **single-numa-node**. For details, see :ref:`Pod Scheduling Prediction <cce_10_0425__section2430103110429>`.

#. Enable the numa-aware add-on and the **resource_exporter** function.

   **Volcano 1.7.1 or later**

   a. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Add-ons**. On the right of the page, locate the **Volcano** add-on and click **Edit**. In the **Parameters** area, configure Volcano scheduler parameters.

      .. code-block::

         ...
             "default_scheduler_conf": {
                 "actions": "allocate, backfill, preempt",
                 "tiers": [
                     {
                         "plugins": [
                             {
                                 "name": "priority"
                             },
                             {
                                 "name": "gang"
                             },
                             {
                                 "name": "conformance"
                             }
                         ]
                     },
                     {
                         "plugins": [
                             {
                                 "name": "drf"
                             },
                             {
                                 "name": "predicates"
                             },
                             {
                                 "name": "nodeorder"
                             }
                         ]
                     },
                     {
                         "plugins": [
                             {
                                 "name": "cce-gpu-topology-predicate"
                             },
                             {
                                 "name": "cce-gpu-topology-priority"
                             },
                             {
                                 "name": "cce-gpu"
                             },
                             {
                                 // add this also enable resource_exporter
                                 "name": "numa-aware",
                                 // the weight of the NUMA Aware Plugin
                                 "arguments": {
                                    "weight": "10"
                                 }
                             }
                         ]
                     },
                     {
                         "plugins": [
                             {
                                 "name": "nodelocalvolume"
                             },
                             {
                                 "name": "nodeemptydirvolume"
                             },
                             {
                                 "name": "nodeCSIscheduling"
                             },
                             {
                                 "name": "networkresource"
                             }
                         ]
                     }
                 ]
             },
         ...

   **Volcano earlier than 1.7.1**

   a. The **resource_exporter_enable** parameter is enabled for the Volcano add-on to collect node NUMA information.

      .. code-block::

         {
            "plugins": {
               "eas_service": {
                  "availability_zone_id": "",
                  "driver_id": "",
                  "enable": "false",
                  "endpoint": "",
                  "flavor_id": "",
                  "network_type": "",
                  "network_virtual_subnet_id": "",
                  "pool_id": "",
                  "project_id": "",
                  "secret_name": "eas-service-secret"
               }
            },
            "resource_exporter_enable": "true"
         }

      After this function is enabled, you can view the NUMA topology information of the current node.

      .. code-block::

         kubectl get numatopo
         NAME              AGE
         node-1            4h8m
         node-2            4h8m
         node-3            4h8m

   b. Enable the Volcano numa-aware algorithm add-on.

      **kubectl edit cm -n kube-system volcano-scheduler-configmap**

      .. code-block::

         kind: ConfigMap
         apiVersion: v1
         metadata:
           name: volcano-scheduler-configmap
           namespace: kube-system
         data:
           default-scheduler.conf: |-
             actions: "allocate, backfill, preempt"
             tiers:
             - plugins:
               - name: priority
               - name: gang
               - name: conformance
             - plugins:
               - name: overcommit
               - name: drf
               - name: predicates
               - name: nodeorder
             - plugins:
               - name: cce-gpu-topology-predicate
               - name: cce-gpu-topology-priority
               - name: cce-gpu
             - plugins:
               - name: nodelocalvolume
               - name: nodeemptydirvolume
               - name: nodeCSIscheduling
               - name: networkresource
                 arguments:
                   NetworkType: vpc-router
               - name: numa-aware # add it to enable numa-aware plugin
                 arguments:
                   weight: 10 # the weight of the NUMA Aware Plugin

Using Volcano to Configure NUMA Affinity Scheduling
---------------------------------------------------

#. Refer to the following examples for configuration.

   a. Example 1: Configure NUMA affinity for a Deployment.

      .. code-block::

         kind: Deployment
         apiVersion: apps/v1
         metadata:
           name: numa-tset
         spec:
           replicas: 1
           selector:
             matchLabels:
               app: numa-tset
           template:
             metadata:
               labels:
                 app: numa-tset
               annotations:
                 volcano.sh/numa-topology-policy: single-numa-node    # Configure the topology policy.
             spec:
               containers:
                 - name: container-1
                   image: nginx:alpine
                   resources:
                     requests:
                       cpu: 2           # The value must be an integer and must be the same as that in limits.
                       memory: 2048Mi
                     limits:
                       cpu: 2           # The value must be an integer and must be the same as that in requests.
                       memory: 2048Mi
               imagePullSecrets:
               - name: default-secret

   b. Example 2: Create a Volcano job and enable NUMA affinity for it.

      .. code-block::

         apiVersion: batch.volcano.sh/v1alpha1
         kind: Job
         metadata:
           name: vj-test
         spec:
           schedulerName: volcano
           minAvailable: 1
           tasks:
             - replicas: 1
               name: "test"
               topologyPolicy: best-effort   # set the topology policy for task
               template:
                 spec:
                   containers:
                     - image: alpine
                       command: ["/bin/sh", "-c", "sleep 1000"]
                       imagePullPolicy: IfNotPresent
                       name: running
                       resources:
                         limits:
                           cpu: 20
                           memory: "100Mi"
                   restartPolicy: OnFailure

#. Analyze NUMA scheduling.

   The following table shows example NUMA nodes.

   +-------------+-------------------------+--------------------------------------+--------------------------------------+
   | Worker Node | Topology Manager Policy | Allocatable CPU Cores on NUMA Node 0 | Allocatable CPU Cores on NUMA Node 1 |
   +=============+=========================+======================================+======================================+
   | Node 1      | single-numa-node        | 16                                   | 16                                   |
   +-------------+-------------------------+--------------------------------------+--------------------------------------+
   | Node 2      | best-effort             | 16                                   | 16                                   |
   +-------------+-------------------------+--------------------------------------+--------------------------------------+
   | Node 3      | best-effort             | 20                                   | 20                                   |
   +-------------+-------------------------+--------------------------------------+--------------------------------------+

   In the preceding examples,

   -  In example 1, 2 CPU cores are requested by a pod, and the **single-numa-node** topology policy is used. Therefore, the pod will be scheduled to node 1 with the same policy.
   -  In example 2, 20 CPU cores are requested by a pod, and the **best-effort** topology policy is used. The pod will be scheduled to node 3 because it can allocate all the requested 20 CPU cores onto one NUMA node, while node 2 can do so on two NUMA nodes.

Checking NUMA Node Usage
------------------------

Run the **lscpu** command to check the CPU usage of the current node.

.. code-block::

   # Check the CPU usage of the current node.
   lscpu
   ...
   CPU(s):              32
   NUMA node(s):        2
   NUMA node0 CPU(s):   0-15
   NUMA node1 CPU(s):   16-31

Then, check the NUMA node usage.

.. code-block::

   # Check the CPU allocation of the current node.
   cat /var/lib/kubelet/cpu_manager_state
   {"policyName":"static","defaultCpuSet":"0,10-15,25-31","entries":{"777870b5-c64f-42f5-9296-688b9dc212ba":{"container-1":"16-24"},"fb15e10a-b6a5-4aaa-8fcd-76c1aa64e6fd":{"container-1":"1-9"}},"checksum":318470969}

The preceding example shows that two containers are running on the node. One container uses CPU cores 1 to 9 of NUMA node 0, and the other container uses CPU cores 16 to 24 of NUMA node 1.
