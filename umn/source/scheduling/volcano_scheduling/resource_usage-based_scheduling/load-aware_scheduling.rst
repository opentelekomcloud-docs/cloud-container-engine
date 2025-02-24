:original_name: cce_10_0789.html

.. _cce_10_0789:

Load-aware Scheduling
=====================

Volcano Scheduler offers CPU and memory load-aware scheduling for pods and preferentially schedules pods to the node with the lightest load to balance node loads. This prevents an application or node failure due to heavy loads on a single node.

Prerequisites
-------------

-  A cluster of v1.21 or later is available. For details, see :ref:`Creating a CCE Standard/Turbo Cluster <cce_10_0028>`.
-  The Volcano add-on of v1.11.14 or later has been installed. For details, see :ref:`Volcano Scheduler <cce_10_0193>`.
-  The Cloud Native Cluster Monitoring add-on (kube-prometheus-stack) has been installed and it runs in local data storage mode. For details, see :ref:`Cloud Native Cluster Monitoring <cce_10_0406>`.

Features
--------

The native Kubernetes scheduler schedules resources only based on requested resources. However, the actual resource usage of a pod differs greatly from the requested or limited value of the requested resources, which is the cause of cluster load imbalance.

#. The actual resource usage of certain nodes in a cluster is far lower than the resource allocation rate, but no more pods are scheduled onto the nodes, leading to resource waste.
#. The scheduler failed to detect that some nodes in the cluster are overloaded, which could significantly affect the stability of the service.

Volcano resolves the preceding issues based on actual loads. If there are plenty of resources, pods are preferentially scheduled to nodes with the lightest load to balance the load on each node in the cluster.

The status, workload traffic, and requests of a cluster change dynamically, and the resource usage of nodes changes in real time. To prevent extreme load imbalance in a cluster after pod scheduling, Volcano provides load-aware hotspot descheduling for the optimal load balancing of cluster nodes. For details about hotspot descheduling, see :ref:`Descheduling <cce_10_0766>`.

How It Works
------------

Load-aware scheduling is implemented using both Volcano and the CCE cloud native monitoring add-on (kube-prometheus-stack). After load-aware scheduling is enabled, metrics such as CPU and memory loads are defined by following Prometheus adapter rules. Then, the kube-prometheus-stack add-on collects and saves the actual CPU and memory loads of each node based on the defined metric rules. Volcano scores and sorts nodes based on the metric values provided by the kube-prometheus-stack add-on and preferentially schedules pods to the node with the lightest load.

Load-aware scheduling scores each node using the weighted average of the CPU and memory metrics as well as the load-aware scheduling policy and preferentially selects the node with the highest score for scheduling. You can customize the weights of the CPU, memory, and load-aware scheduling policy on the **Scheduling** tab by choosing **Settings** in the navigation pane of the target cluster.

The formula for scoring a node is as follows: Weight of the load-aware scheduling policy x [(1 - CPU usage) x CPU weight + (1 - Memory usage) x Memory weight]/(CPU weight + Memory weight)

-  CPU usage: average CPU usage of all nodes in the target cluster in the last 10 minutes (The collection frequency can be modified in the Prometheus adapter rule.)
-  Memory usage: average memory usage of all nodes in the target cluster in the last 10 minutes

Configuring Load-aware Scheduling
---------------------------------

#. Obtain resource metrics by calling the metrics API and add custom metric collection rules.

   After the kube-prometheus-stack add-on is installed, enable the function of automatically obtaining resource metrics through the metrics API. For details, see :ref:`Providing Resource Metrics Through the Metrics API <cce_10_0406__section17830202915211>`.

   Add custom metric collection rules. For details, see :ref:`Creating an HPA Policy Using Custom Metrics <cce_10_0406__section11927514174016>`. In the following example rules, the rules in red are new ones and those in black are existing ones:

   .. code-block::

      rules:
        - seriesQuery: '{__name__=~"node_cpu_seconds_total"}'
          resources:
            overrides:
              instance:
                resource: node
          name:
            matches: node_cpu_seconds_total
            as: node_cpu_usage_avg
          metricsQuery: avg_over_time((1 - avg (irate(<<.Series>>{mode="idle"}[5m])) by (instance))[10m:30s])
        - seriesQuery: '{__name__=~"node_memory_MemTotal_bytes"}'
          resources:
            overrides:
              instance:
                resource: node
          name:
            matches: node_memory_MemTotal_bytes
            as: node_memory_usage_avg
          metricsQuery: avg_over_time(((1-node_memory_MemAvailable_bytes/<<.Series>>))[10m:30s])
      resourceRules:
        cpu:
          containerQuery: sum(rate(container_cpu_usage_seconds_total{<<.LabelMatchers>>,container!="",pod!=""}[1m])) by (<<.GroupBy>>)
          nodeQuery: sum(rate(container_cpu_usage_seconds_total{<<.LabelMatchers>>, id='/'}[1m])) by (<<.GroupBy>>)
          resources:
            overrides:
              instance:
                resource: node
              namespace:
                resource: namespace
              pod:
                resource: pod
          containerLabel: container
        memory:
          containerQuery: sum(container_memory_working_set_bytes{<<.LabelMatchers>>,container!="",pod!=""}) by (<<.GroupBy>>)
          nodeQuery: sum(container_memory_working_set_bytes{<<.LabelMatchers>>,id='/'}) by (<<.GroupBy>>)
          resources:
            overrides:
              instance:
                resource: node
              namespace:
                resource: namespace
              pod:
                resource: pod
          containerLabel: container
        window: 1m

   -  **Rules for collecting the average CPU usage**

      -  **node_cpu_usage_avg**: average CPU usage of nodes. The name of this metric cannot be changed.

      -  **metricsQuery: avg_over_time((1 - avg (irate(<<.Series>>{mode="idle"}[5m])) by (instance))[10m:30s])**: statement for obtaining nodes' average CPU usage.

         **metricsQuery** indicates to obtain the average CPU usage of all nodes in the target cluster in the last 10 minutes. To change the period, for example, to the last 5 or 30 minutes, change **10m** in red to **5m** or **30m**.

   -  **Rules for collecting the average memory usage**

      -  **node_memory_usage_avg**: average memory usage of nodes. The name of this metric cannot be changed.

      -  **metricsQuery: avg_over_time(((1-node_memory_MemAvailable_bytes/<<.Series>>))[10m:30s])**: statement for obtaining nodes' average memory usage.

         **metricsQuery** indicates to obtain the average memory usage of all nodes in the target cluster in the last 10 minutes. To change the period, for example, to the last 5 or 30 minutes, change **10m** in red to **5m** or **30m**.

#. Enable load-aware scheduling.

   After Volcano is installed, you can enable or disable load-aware scheduling on the **Scheduling** page by choose **Settings** in the navigation pane. This function is disabled by default.

   a. Log in to the CCE console.
   b. Click the cluster name to access the cluster console. Choose **Settings** in the navigation pane. In the right pane, click the **Scheduling** tab.
   c. In the **Resource utilization optimization scheduling** area, modify the load-aware scheduling settings.

      .. note::

         For optimal load-aware scheduling, disable bin packing because this policy preferentially schedules pods to the node with the maximal resources allocated based on pods' requested resources. This affects load-aware scheduling to some extent. For details about the combination of multiple policies, see :ref:`Configuration Cases for Resource Usage-based Scheduling <cce_10_0813>`.

      +--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Parameter                            | Description                                                                                                                                                                                      | Default Value         |
      +======================================+==================================================================================================================================================================================================+=======================+
      | Load-Aware Scheduling Policy Weight  | A larger value indicates a higher weight of the load-aware policy in overall scheduling.                                                                                                         | 5                     |
      +--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | CPU Weight                           | A larger value indicates CPU resources will be preferentially balanced.                                                                                                                          | 1                     |
      +--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Memory Weight                        | A larger value indicates memory resources will be preferentially balanced.                                                                                                                       | 1                     |
      +--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Actual load threshold effective mode | -  Soft constraint: When the actual CPU or memory load of a node reaches the threshold, new tasks will be preferentially allocated to underutilized nodes, but this node can still be scheduled. | Hard constraint       |
      |                                      | -  Hard constraint: When the actual CPU or memory load of a node reaches the threshold, no new tasks can be scheduled to this node.                                                              |                       |
      +--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Actual CPU Load Threshold            | When a node's CPU usage goes beyond this threshold, pods will be preferentially or forcibly scheduled to other nodes based on how the load threshold takes effect.                               | 80                    |
      +--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Actual Memory Load Threshold         | When a node's memory usage goes beyond this threshold, pods will be preferentially or forcibly scheduled to other nodes based on how the load threshold takes effect.                            | 80                    |
      +--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
