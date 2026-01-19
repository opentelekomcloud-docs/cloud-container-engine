:original_name: cce_10_0789.html

.. _cce_10_0789:

Load-aware Scheduling
=====================

Volcano Scheduler offers CPU and memory load-aware scheduling for pods and preferentially schedules pods to the node with the lightest load to balance node loads. This prevents an application or node failure due to heavy loads on a single node.

Prerequisites
-------------

-  A cluster of v1.21 or later is available. For details, see :ref:`Creating a CCE Standard/Turbo Cluster <cce_10_0028>`.
-  The Volcano add-on of v1.11.14 or later has been installed. For details, see :ref:`Volcano Scheduler <cce_10_0193>`.
-  The Cloud Native Cluster Monitoring add-on (kube-prometheus-stack) has been installed and it runs in local data storage mode. For details, see :ref:`Cloud Native Cluster Monitoring <cce_10_0406>`. If you use an on-premises Prometheus monitoring system, Prometheus 2.35.0 or later is recommended. For details, see :ref:`Using On-premises Prometheus <cce_10_0789__section423012214818>`.
-  Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

Features
--------

The Kubernetes native scheduler schedules resources only based on requested resources. However, the actual resource usage of a pod differs greatly from the requested or limited value of the requested resources, which is the cause of cluster load imbalance.

#. The actual resource usage of certain nodes in a cluster is far lower than the resource allocation rate, but no more pods are scheduled onto the nodes, leading to resource waste.
#. The scheduler failed to detect that some nodes in the cluster are overloaded, which could significantly affect the stability of the service.

Volcano resolves the preceding issues based on actual loads. If there are plenty of resources, pods are preferentially scheduled to nodes with the lightest load to balance the load on each node in the cluster.

The status, workload traffic, and requests of a cluster change dynamically, and the resource usage of nodes changes in real time. To prevent extreme load imbalance in a cluster after pod scheduling, Volcano provides load-aware hotspot descheduling for the optimal load balancing of cluster nodes. For details about hotspot descheduling, see :ref:`Descheduling <cce_10_0766>`.

How It Works
------------

Load-aware scheduling is implemented using both Volcano and the CCE cloud native monitoring add-on (kube-prometheus-stack). After load-aware scheduling is enabled, metrics such as CPU and memory loads are defined by following Prometheus adapter rules. Then, the kube-prometheus-stack add-on collects and saves the actual CPU and memory loads of each node based on the defined metric rules. Volcano scores and sorts nodes based on the metric values provided by the kube-prometheus-stack add-on and preferentially schedules pods to the node with the lightest load.

Load-aware scheduling scores each node using the weighted average of the CPU and memory metrics as well as the load-aware scheduling policy and preferentially selects the node with the highest score for scheduling. You can create custom weights for the CPU, memory, and load-aware scheduling policy on the **Scheduling** tab by choosing **Settings** in the navigation pane of the target cluster.

The formula for scoring a node is as follows: Weight of the load-aware scheduling policy x [(1 - CPU usage) x CPU weight + (1 - Memory usage) x Memory weight]/(CPU weight + Memory weight)

-  CPU usage: average CPU usage of all nodes in the target cluster in the last 10 minutes (The collection frequency can be modified in the Prometheus adapter rule.)
-  Memory usage: average memory usage of all nodes in the target cluster in the last 10 minutes

Using Cloud Native Cluster Monitoring
-------------------------------------

#. After installing the Cloud Native Cluster Monitoring add-on, you need to enable Metrics API to provide container resource metrics such as CPU usage and memory usage.

   .. note::

      Resource metrics can be provided by Metrics API only when local data storage is enabled for the Cloud Native Cluster Monitoring add-on.

   Check whether the Metrics API has been enabled for the cluster. If so, skip this step.

   .. code-block::

      kubectl get APIServices | grep v1beta1.metrics.k8s.io

   If any command output is displayed, Metrics API is enabled. Skip this step and go to the next step to add metric collection rules.

   If no Metrics API is found, you can manually create an APIService object to enable it.

   a. Create a file named **metrics-apiservice.yaml**. Example file content:

      .. code-block::

         apiVersion: apiregistration.k8s.io/v1
         kind: APIService
         metadata:
           labels:
             app: custom-metrics-apiserver
             release: cceaddon-prometheus
           name: v1beta1.metrics.k8s.io
         spec:
           group: metrics.k8s.io
           groupPriorityMinimum: 100
           insecureSkipTLSVerify: true
           service:
             name: custom-metrics-apiserver
             namespace: monitoring
             port: 443
           version: v1beta1
           versionPriority: 100

   b. Create an APIService object.

      .. code-block::

         kubectl create -f metrics-apiservice.yaml

   c. Check whether the Metrics API has been enabled for the cluster.

      .. code-block::

         kubectl get APIServices | grep v1beta1.metrics.k8s.io

      .. note::

         After Metrics API is enabled, if you need to uninstall the Cloud Native Cluster Monitoring add-on, run the following kubectl and delete the APIService object. Otherwise, the residual APIService resources will cause the Kubernetes Metrics Server add-on to fail to be installed.

         .. code-block::

            kubectl delete APIService v1beta1.metrics.k8s.io

#. Add collection rules for custom metrics.

   a. Modify the **user-adapter-config** configuration item.

      .. code-block::

         kubectl edit configmap user-adapter-config -n monitoring

   b. Add collection rules to the **rules** field.

      In the example rules below, the rules in **config.yaml** are new ones and others are existing ones. Custom metric collection rules are related to the Cloud Native Cluster Monitoring add-on version. Select the rules based on the add-on version.

      -  For Cloud Native Cluster Monitoring of v3.12.0 or earlier versions:

         .. code-block::

            ...
            data:
              config.yaml: |
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
            ...

      -  For Cloud Native Cluster Monitoring of v3.12.1 or later versions:

         .. code-block::

            ...
            data:
              config.yaml: |
                rules:
                - seriesQuery: '{__name__=~"node_cpu_seconds_total"}'
                  resources:
                    overrides:
                      node:
                        resource: node
                  name:
                    matches: node_cpu_seconds_total
                    as: node_cpu_usage_avg
                  metricsQuery: avg_over_time((1 - avg (irate(<<.Series>>{mode="idle"}[5m])) by (node))[10m:30s])
                - seriesQuery: '{__name__=~"node_memory_MemTotal_bytes"}'
                  resources:
                    overrides:
                      node:
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
                        node:
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
                        node:
                          resource: node
                        namespace:
                          resource: namespace
                        pod:
                          resource: pod
                    containerLabel: container
                  window: 1m
            ...

      The relevant parameters are as follows:

      -  **Rules for collecting the average CPU usage**

         -  **node_cpu_usage_avg**: average CPU usage of nodes. The name of this metric cannot be changed.

         -  **metricsQuery: avg_over_time((1 - avg (irate(<<.Series>>{mode="idle"}[5m])) by (instance))[10m:30s])**: statement for obtaining nodes' average CPU usage.

            **metricsQuery** indicates to obtain the average CPU usage of all nodes in the target cluster in the last 10 minutes. To change the period, for example, to the last 5 or 30 minutes, change **10m** in red to **5m** or **30m**.

      -  **Rules for collecting the average memory usage**

         -  **node_memory_usage_avg**: average memory usage of nodes. The name of this metric cannot be changed.

         -  **metricsQuery: avg_over_time(((1-node_memory_MemAvailable_bytes/<<.Series>>))[10m:30s])**: statement for obtaining nodes' average memory usage.

            **metricsQuery** indicates to obtain the average memory usage of all nodes in the target cluster in the last 10 minutes. To change the period, for example, to the last 5 or 30 minutes, change **10m** in red to **5m** or **30m**.

   c. Redeploy the **custom-metrics-apiserver** workload in the **monitoring** namespace.

      .. code-block::

         kubectl rollout restart deployment custom-metrics-apiserver -n monitoring

   d. Verify that the custom rules are configured successfully.

      #. Run the following command. If the custom metric information is returned, the Prometheus metric collection is configured correctly.

         .. code-block::

            kubectl get --raw=/apis/custom.metrics.k8s.io/v1beta1

         |image1|

      #. Obtain nodes in the cluster.

         .. code-block::

            kubectl get nodes

         Run the following command on any node. Replace *xxxx* with the obtained value of **node_name**. If you want to query the resource information of all nodes, replace *xxxx* with an asterisk (``*``).

         .. code-block::

            kubectl get --raw=/apis/custom.metrics.k8s.io/v1beta1/nodes/xxxx/node_cpu_usage_avg

         Information similar to the following is displayed.

         |image2|

#. Enable load-aware scheduling.

   After Volcano is installed, you can enable or disable load-aware scheduling on the **Scheduling** page by choosing **Settings** in the navigation pane. This function is disabled by default.

   a. Log in to the CCE console and click the cluster name to access the cluster console.
   b. Choose **Settings** in the navigation pane and click the **Scheduling** tab.
   c. In the **Resource Utilization-optimized Scheduling** area, modify the load-aware scheduling settings.

      .. note::

         For optimal load-aware scheduling, disable bin packing because this policy preferentially schedules pods to the node with the maximal resources allocated based on pods' requested resources. This affects load-aware scheduling to some extent. For details about the combination of multiple policies, see :ref:`Configuration Cases for Resource Usage-based Scheduling <cce_10_0813>`.

      +--------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Parameter                            | Description                                                                                                                                                                                                                                                   | Default Value         |
      +======================================+===============================================================================================================================================================================================================================================================+=======================+
      | Load-Aware Scheduling Policy Weight  | A larger value indicates a higher weight of the load-aware policy in overall scheduling.                                                                                                                                                                      | 5                     |
      +--------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | CPU Weight                           | A larger value indicates CPU resources will be preferentially balanced.                                                                                                                                                                                       | 1                     |
      +--------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Memory Weight                        | A larger value indicates memory resources will be preferentially balanced.                                                                                                                                                                                    | 1                     |
      +--------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Actual load threshold effective mode | -  Soft constraint: When the actual CPU or memory load of a node reaches the threshold, new tasks will be preferentially allocated to underutilized nodes, but this node can still be scheduled.                                                              | Hard constraint       |
      |                                      | -  Hard constraint: When the actual CPU or memory load of a node reaches the threshold, no new tasks can be scheduled to this node.                                                                                                                           |                       |
      +--------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Actual CPU Load Threshold            | When a node's CPU usage goes beyond this threshold, workloads will be scheduled based on how the load threshold takes effect. New workloads will be preferentially or forcibly scheduled to other nodes. Existing workloads on the nodes are not affected.    | 80                    |
      +--------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Actual Memory Load Threshold         | When a node's memory usage goes beyond this threshold, workloads will be scheduled based on how the load threshold takes effect. New workloads will be preferentially or forcibly scheduled to other nodes. Existing workloads on the nodes are not affected. | 80                    |
      +--------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+

.. _cce_10_0789__section423012214818:

Using On-premises Prometheus
----------------------------

#. Install `prometheus-adapter <https://github.com/kubernetes-sigs/prometheus-adapter.git>`__ in the cluster.

   a. Run the following commands to install prometheus-adapter:

      .. code-block::

         git clone https://github.com/kubernetes-sigs/prometheus-adapter.git
         cd prometheus-adapter/deploy/manifests
         kubectl apply -f .

   b. Modify the configuration for prometheus-adapter to connect to prometheus-server.

      .. code-block::

         kubectl edit deployment prometheus-adapter -n monitoring

      Change the value of **prometheus-url** as follows:

      -  prometheus-adapter supports HTTPS and HTTP. You can configure the protocol based on your custom-built Prometheus setup.

         -  If TLS or HTTPS has been enabled for Prometheus, use HTTPS.
         -  If TLS or HTTPS is not enabled for Prometheus and data is exposed only within the cluster via Services, use HTTP.

      -  Change the default domain name to the IP address and port of Prometheus Service. You can run the **kubectl get service -n monitoring** command to query the IP address and port.

      .. code-block::

         ...
               containers:
                 - name: prometheus-adapter
                   image: registry.k8s.io/prometheus-adapter/prometheus-adapter:v0.12.0
                   args:
                     - --cert-dir=/var/run/serving-cert
                     - --config=/etc/adapter/config.yaml
                     - --prometheus-url=http://10.21.72.124:9090/
                     - --secure-port=6443
                     - --tls-cipher-suites=TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305,TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA,TLS_RSA_WITH_AES_128_GCM_SHA256,TLS_RSA_WITH_AES_256_GCM_SHA384,TLS_RSA_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_AES_256_CBC_SHA
         ...

   c. Verify that the native **node_cpu_seconds_total** and **node_memory_MemAvailable_bytes** metrics can be obtained on Prometheus.

      |image3|

#. Enable Custom Metrics API to provide container resource metrics.

   Check whether the custom Metrics API has been enabled for the cluster. If so, skip this step.

   .. code-block::

      kubectl get APIServices | grep v1beta1.custom.metrics.k8s.io

   If any command output is displayed, Custom Metrics API has been enabled. If no command output is displayed, perform the following operations to register the APIService object. (Note that the Service name and namespace must be consistent with those in the actual installation environment.) The following uses the default values of kube-prometheus.

   a. Create a file named **custom-metrics-apiservice.yaml**. Example file content:

      .. code-block::

         apiVersion: apiregistration.k8s.io/v1
         kind: APIService
         metadata:
           labels:
             app.kubernetes.io/component: metrics
             app.kubernetes.io/name: prometheus-adapter
             app.kubernetes.io/part-of: prometheus-adapter
           name: v1beta1.custom.metrics.k8s.io
         spec:
           group: custom.metrics.k8s.io
           groupPriorityMinimum: 100
           insecureSkipTLSVerify: true
           service:
             name: prometheus-adapter
             namespace: monitoring
             port: 443
           version: v1beta1
           versionPriority: 100

   b. Create an APIService object.

      .. code-block::

         kubectl create -f custom-metrics-apiservice.yaml

   c. Check whether the custom Metrics API has been enabled for the cluster.

      .. code-block::

         kubectl get APIServices | grep v1beta1.custom.metrics.k8s.io

#. Add collection rules for custom metrics.

   a. Modify the **adapter-config** configuration item.

      .. code-block::

         kubectl edit configmap adapter-config -n monitoring

   b. Add collection rules to the **rules** field.

      Example collection rules:

      .. code-block::

         ...
         data:
           config.yaml: >
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
         ...

   c. Redeploy the prometheus-adapter workload in the **monitoring** namespace.

      .. code-block::

         kubectl rollout restart deployment prometheus-adapter -n monitoring

   d. Verify that the custom rules are configured successfully.

      #. Run the following command. If the custom metric information is returned, the Prometheus metric collection is configured correctly.

         .. code-block::

            kubectl get --raw=/apis/custom.metrics.k8s.io/v1beta1

         |image4|

      #. Obtain nodes in the cluster.

         .. code-block::

            kubectl get nodes

         Run the following command on any node. Replace *xxxx* with the obtained value of **node_name**. If you want to query the resource information of all nodes, replace *xxxx* with an asterisk (``*``).

         .. code-block::

            kubectl get --raw=/apis/custom.metrics.k8s.io/v1beta1/nodes/xxxx/node_cpu_usage_avg

         Information similar to the following is displayed.

         |image5|

#. After installing Volcano, enable load-aware scheduling on the **Settings** > **Scheduling** page.

   a. Log in to the CCE console and click the cluster name to access the cluster console.

   b. In the navigation pane, choose **Settings**. Then, click the **Scheduling** tab.

   c. In **Expert mode**, click **Try Now**.

   d. In the Volcano Scheduler settings, load-aware scheduling is disabled by default. You need to add the parameters in expert mode to enable this function.

      .. note::

         For optimal load-aware scheduling, disable bin packing because this policy preferentially schedules pods to the node with the maximal resources allocated based on pods' requested resources. This affects load-aware scheduling to some extent. For details about the combination of multiple policies, see :ref:`Configuration Cases for Resource Usage-based Scheduling <cce_10_0813>`.

      .. code-block::

         ...
         default_scheduler_conf:
           actions: allocate, backfill, preempt
           metrics:
             interval: 30s
             type: prometheus_adaptor
           tiers:
             - plugins:
                 - name: priority
                 - enableJobStarving: false
                   enablePreemptable: false
                   name: gang
                 - name: conformance
                 - name: oversubscription
             - plugins:
                 - enablePreemptable: false
                   name: drf
                 - name: predicates
                 - name: nodeorder
                 - arguments:
                     cpu.weight: 1
                     memory.weight: 1
                     thresholds:
                        cpu: 80
                        mem: 80
                     usage.weight: 5
                   enablePredicate: true
                   name: usage
             - plugins:
                 - name: cce-gpu-topology-predicate
                 - name: cce-gpu-topology-priority
                 - name: xgpu
             - plugins:
                 - name: nodelocalvolume
                 - name: nodeemptydirvolume
                 - name: nodeCSIscheduling
                 - name: networkresource
         ...

      .. table:: **Table 1** Parameters for load-aware scheduling

         +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter             | Example Value         | Description                                                                                                                                                                                                                                                                                                |
         +=======================+=======================+============================================================================================================================================================================================================================================================================================================+
         | cpu.weight            | 1                     | The CPU weight. The value ranges from 0 to 200. A larger value indicates CPU resources will be preferentially balanced.                                                                                                                                                                                    |
         +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | memory.weight         | 1                     | The memory weight. The value ranges from 0 to 200. A larger value indicates memory resources will be preferentially balanced.                                                                                                                                                                              |
         +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | thresholds.cpu        | 80                    | The actual CPU load threshold. The value ranges from 1 to 100. If a node's actual CPU usage exceeds the threshold, new workloads will be preferentially or forcibly scheduled to other nodes based on the constraints defined in enablePredicate. Existing workloads on this node remain unaffected.       |
         +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | thresholds.mem        | 80                    | The actual memory load threshold. The value ranges from 1 to 100. If a node's actual memory usage exceeds the threshold, new workloads will be preferentially or forcibly scheduled to other nodes based on the constraints defined in enablePredicate. Existing workloads on this node remain unaffected. |
         +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | usage.weight          | 5                     | The weight for a load-aware scheduling policy. The value ranges from 0 to 200. A larger value indicates a higher weight of the load-aware policy in overall scheduling.                                                                                                                                    |
         +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | enablePredicate       | true                  | How a load threshold takes effect. The value can be **true** or **false**.                                                                                                                                                                                                                                 |
         |                       |                       |                                                                                                                                                                                                                                                                                                            |
         |                       |                       | -  **true**: Hard constraints are enabled. If a node's actual CPU or memory load reaches the threshold, no new workloads will be scheduled to this node.                                                                                                                                                   |
         |                       |                       | -  **false**: Soft constraints are enabled. If a node's actual CPU or memory load reaches the threshold, CCE will preferentially schedule new workloads to nodes whose CPU or memory load has not reached the threshold. However, workloads may still be scheduled to the node.                            |
         +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | name                  | usage                 | The name of the enabled add-on. The value is fixed as **usage**.                                                                                                                                                                                                                                           |
         +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. |image1| image:: /_static/images/en-us_image_0000002483959498.png
.. |image2| image:: /_static/images/en-us_image_0000002516079443.png
.. |image3| image:: /_static/images/en-us_image_0000002516079449.png
.. |image4| image:: /_static/images/en-us_image_0000002483959494.png
.. |image5| image:: /_static/images/en-us_image_0000002516079445.png
