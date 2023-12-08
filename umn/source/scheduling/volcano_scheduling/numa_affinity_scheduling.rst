:original_name: cce_10_0425.html

.. _cce_10_0425:

NUMA Affinity Scheduling
========================

Background
----------

When the node runs many CPU-bound pods, the workload can move to different CPU cores depending on whether the pod is throttled and which CPU cores are available at scheduling time. Many workloads are not sensitive to this migration and thus work fine without any intervention. However, in workloads where CPU cache affinity and scheduling latency significantly affect workload performance, the kubelet allows alternative CPU management policies to determine some placement preferences on the node.

Both the CPU Manager and Topology Manager are kubelet components, but they have the following limitations:

-  The scheduler is not topology-aware. Therefore, the workload may be scheduled on a node and then fail on the node due to the Topology Manager. This is unacceptable for TensorFlow jobs. If any worker or ps failed on node, the job will fail.
-  The managers are node-level that results in an inability to match the best node for NUMA topology in the whole cluster.

For more information, see https://github.com/volcano-sh/volcano/blob/master/docs/design/numa-aware.md.

Volcano targets to resolve the limitation to make scheduler NUMA topology aware so as to achieve the following:

-  Do not schedule pods to the nodes which NUMA topology does not match.
-  Schedule pods to the best node for NUMA topology.

Application Scope
-----------------

-  Support CPU resource topology scheduling
-  Support pod-level topology policies

.. _cce_10_0425__section2430103110429:

Scheduling Prediction
---------------------

For pods with the topology policy, predicate the matched node list.

+-----------------------------------+---------------------------------------------------------------------------------------------------+
| policy                            | action                                                                                            |
+===================================+===================================================================================================+
| none                              | 1. No filter action                                                                               |
+-----------------------------------+---------------------------------------------------------------------------------------------------+
| best-effort                       | 1. Filter out the node with the topology policy **best-effort**.                                  |
+-----------------------------------+---------------------------------------------------------------------------------------------------+
| restricted                        | 1. Filter out the node with the topology policy **restricted**.                                   |
|                                   |                                                                                                   |
|                                   | 2. Filter out the node that the CPU topology meets the CPU requirements for **restricted**.       |
+-----------------------------------+---------------------------------------------------------------------------------------------------+
| single-numa-node                  | 1. Filter out the node with the topology policy **single-numa-node**.                             |
|                                   |                                                                                                   |
|                                   | 2. Filter out the node that the CPU topology meets the CPU requirements for **single-numa-node**. |
+-----------------------------------+---------------------------------------------------------------------------------------------------+


.. figure:: /_static/images/en-us_image_0000001647417448.png
   :alt: **Figure 1** Comparison of NUMA scheduling policies

   **Figure 1** Comparison of NUMA scheduling policies

Scheduling Priority
-------------------

Topology policy aims to schedule pods to the optimal node. In this example, each node is scored to sort out the optimal node.

Principle: Schedule pods to the worker nodes that require the fewest NUMA nodes.

The scoring formula is as follows:

score = weight \* (100 - 100 \* numaNodeNum / maxNumaNodeNum)

Parameter description:

-  **weight**: indicates the weight of NUMA Aware Plugin.
-  **numaNodeNum**: indicates the number of NUMA nodes required for running the pod on the worker node.
-  **maxNumaNodeNum**: indicates the maximum number of NUMA nodes in a pod of all worker nodes.

Enabling Volcano to Support NUMA Affinity Scheduling
----------------------------------------------------

#. Enable the CPU management policy. For details, see :ref:`Enabling the CPU Management Policy <cce_10_0351__section173918176434>`.

#. Configure a CPU topology policy.

   a. Log in to the CCE console, click the cluster name, access the cluster details page, and choose **Nodes** in the navigation pane. On the page displayed, click the **Node Pools** tab. Choose **More** > **Manage** in the **Operation** column of the target node pool.

   b. Change the value of **topology-manager-policy** under **kubelet** to the required CPU topology policy. As shown in the following figure, the CPU topology policy is **best-effort**.

      The valid topology policies are **none**, **best-effort**, **restricted**, and **single-numa-node**. For details about these policies, see :ref:`Scheduling Prediction <cce_10_0425__section2430103110429>`.

      |image1|

#. Enable the numa-aware add-on and the **resource_exporter** function.

   **volcano 1.7.1 or later**

   a. Log in to the CCE console and access the cluster console. In the navigation pane, choose **Add-ons**. On the right of the page, locate the **volcano** add-on and click **Edit**. In the **Parameters** area, configure Volcano scheduler parameters.

      .. code-block::

         {
             "ca_cert": "",
             "default_scheduler_conf": {
                 "actions": "allocate, backfill",
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
             "server_cert": "",
             "server_key": ""
         }

   **volcano earlier than 1.7.1**

   a. The **resource_exporter_enable** parameter is enabled for the volcano add-on to collect node NUMA information.

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

   b. Enable the volcano numa-aware algorithm add-on.

      **kubectl edit cm -n kube-system volcano-scheduler-configmap**

      .. code-block::

         kind: ConfigMap
         apiVersion: v1
         metadata:
           name: volcano-scheduler-configmap
           namespace: kube-system
         data:
           default-scheduler.conf: |-
             actions: "allocate, backfill"
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

Using Volcano to Support NUMA Affinity Scheduling
-------------------------------------------------

#. Configure NUMA affinity for Deployments. The following is an example:

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
              volcano.sh/numa-topology-policy: single-numa-node    # set the topology policy
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

#. Create a volcano job and use NUMA affinity.

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

#. Check the NUMA usage.

   .. code-block::

      # Check the CPU usage of the current node.
      lscpu
      ...
      CPU(s):              32
      NUMA node(s):        2
      NUMA node0 CPU(s):   0-15
      NUMA node1 CPU(s):   16-31

      # Check the CPU allocation of the current node.
      cat /var/lib/kubelet/cpu_manager_state
      {"policyName":"static","defaultCpuSet":"0,10-15,25-31","entries":{"777870b5-c64f-42f5-9296-688b9dc212ba":{"container-1":"16-24"},"fb15e10a-b6a5-4aaa-8fcd-76c1aa64e6fd":{"container-1":"1-9"}},"checksum":318470969}

.. |image1| image:: /_static/images/en-us_image_0000001695737101.png
