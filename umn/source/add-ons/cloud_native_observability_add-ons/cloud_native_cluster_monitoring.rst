:original_name: cce_10_0406.html

.. _cce_10_0406:

Cloud Native Cluster Monitoring
===============================

Introduction
------------

kube-prometheus-stack uses Prometheus-operator and Prometheus to provide easy-to-use, end-to-end Kubernetes cluster monitoring.

Open source community: https://github.com/prometheus/prometheus

Notes and Constraints
---------------------

-  By default, the kube-state-metrics component of the add-on does not collect labels and annotations of Kubernetes resources. To collect these labels and annotations, manually enable the collection function in the startup parameters and check whether the corresponding metrics are added to the collection whitelist of ServiceMonitor named **kube-state-metrics**. For details, see :ref:`Collecting All Labels and Annotations of a Pod <cce_10_0406__section163751639151115>`.

-  In 3.8.0 and later versions, component metrics in the kube-system and monitoring namespaces are not collected by default. If you have workloads in the two namespaces, use :ref:`Pod Monitor <cce_10_0373__section3497144813213>` or :ref:`Service Monitor <cce_10_0373__section92241661317>` to collect these metrics.

-  In 3.8.0 and later versions, etcd-server, kube-controller, kube-scheduler, autoscaler, fluent-bit, volcano-agent, volcano-scheduler and otel-collector metrics are not collected by default. Enable the collection as required.

   To enable this function, on the **ConfigMaps and Secrets** page, expand the dropdown list of **Namespace**, and select **monitoring**. Locate the row that contains the configuration item named **persistent-user-config**, and click **Edit YAML** in the operation column. Remove the **serviceMonitorDisable** or **podMonitorDisable** configuration in the **customSettings** field as required or set the configuration to an empty array.

   .. code-block::

      ...
         customSettings:
            podMonitorDisable: []
            serviceMonitorDisable: []

Permissions
-----------

The node-exporter component of the kube-prometheus-stack add-on needs to read the Docker info data from the **/var/run/docker.sock** directory on the host for monitoring the Docker disk space.

The following permission is required for running node-exporter:

-  cap_dac_override: reads the Docker info data.

Installing the Add-on
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console. Choose **Add-ons** in the navigation pane, locate **Cloud Native Cluster Monitoring** on the right, and click **Install**.

#. On the **Install Add-on** page, configure the specifications.

   -  **Deployment Mode**: This parameter is available for the Cloud Native Cluster Monitoring version 3.7.1 or later.

      -  **Agent mode**: Few resources are required. In this mode, HPA is not supported.
      -  **Server mode**: More resources are required. In this mode, all Cloud Native Cluster Monitoring functions are supported.

   -  **Containers**: component instance created by the add-on. For details, see :ref:`Components <cce_10_0406__section0377457163618>`. You can select or customize a specification as required.

#. Configure related parameters.

   -  **Interconnecting with AOM**: Report Prometheus data to AOM. After this function is enabled, you can select the corresponding AOM instance.
   -  **Connect to Third Party**: To report Prometheus data to a third-party monitoring system, enter the address and token of the third-party monitoring system and determine whether to skip certificate authentication.
   -  **User-defined Metrics Service Discovery**: Application metrics are automatically collected in the form of service discovery.
   -  **Prometheus HA**: The Prometheus-server, Prometheus-operator, thanos-query, custom-metrics-apiserver and alertmanager components are deployed in multi-instance mode in the cluster.
   -  **Number of collected shards**: Collected targets are distributed among different Prometheus shards. This increases the upper limit of the metrics collection throughput but will consume more resources. Therefore, this parameter is recommended for large-scale clusters.
   -  **Collection Interval**: Configure the collection interval.
   -  **Storage**: Select the type and size of the disk for storing monitoring data. **After the add-on is uninstalled, the storage volumes are not deleted** if this add-on is deployed in the server mode.

      .. note::

         An available PVC named **pvc-prometheus-server** exists in namespace **monitoring** and will be used as the storage source.

   -  **Scheduling Policies**: Support node affinity, taint, and tolerations. Multiple scheduling policies can be configured. If no affinity node label key or toleration node taint key is configured, this function is disabled by default.

      -  **Range**: You can select the add-on pods for which the scheduling policy takes effect. By default, the scheduling policy takes effect for all pods. If a pod is specified, the scheduling policies configured for all pods are overwritten.
      -  **Affinity Node Label Key**: Enter a node label key to set node affinity for the add-on pods.
      -  **Affinity Node Label Value**: Enter a node label value to set node affinity for the add-on pods.
      -  **Toleration Node Taint Key**: A component can be scheduled to a node that has the taint key you specify.

#. Click **Install**.

   After the add-on is installed, you may need to perform the following operations:

   -  To use custom metrics to create an auto scaling policy, ensure that the add-on is in the server mode and then perform the following steps:

      a. Collect custom metrics reported by applications to Prometheus. For details, see :ref:`Monitoring Custom Metrics Using Cloud Native Cluster Monitoring <cce_10_0373>`.
      b. Aggregate these custom metrics collected by Prometheus to the API server for the HPA policy to use. For details, see :ref:`Creating an HPA Policy Using Custom Metrics <cce_10_0406__section11927514174016>`.

   -  To use this add-on to provide system resource metrics (such as CPU and memory usage) for workload auto scaling, enable the Metric API. For details, see :ref:`Providing Resource Metrics Through the Metrics API <cce_10_0406__section17830202915211>`. After the configuration, you can use Prometheus to collect system resource metrics. (This configuration is not recommended).

.. _cce_10_0406__section0377457163618:

Components
----------

All Kubernetes resources created during kube-prometheus-stack add-on installation are created in the namespace named **monitoring**.

.. table:: **Table 1** Add-on components

   +--------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+-----------------+
   | Component                                  | Description                                                                                                                                                                                                                                                                                                                                            | Supported Deployment Mode | Resource Type   |
   +============================================+========================================================================================================================================================================================================================================================================================================================================================+===========================+=================+
   | prometheusOperator                         | Deploys and manages the Prometheus Server based on CustomResourceDefinitions (CRDs), and monitors and processes the events related to these CRDs. It is the control center of the entire system.                                                                                                                                                       | Agent/Server              | Deployment      |
   |                                            |                                                                                                                                                                                                                                                                                                                                                        |                           |                 |
   | (workload name: prometheus-operator)       |                                                                                                                                                                                                                                                                                                                                                        |                           |                 |
   +--------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+-----------------+
   | prometheus                                 | A Prometheus Server cluster deployed by the operator based on the Prometheus CRDs that can be regarded as StatefulSets.                                                                                                                                                                                                                                | Agent/Server              | StatefulSet     |
   |                                            |                                                                                                                                                                                                                                                                                                                                                        |                           |                 |
   | (workload name: prometheus-server)         |                                                                                                                                                                                                                                                                                                                                                        |                           |                 |
   +--------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+-----------------+
   | alertmanager                               | Alarm center of the add-on. It receives alarms sent by Prometheus and manages alarm information by deduplicating, grouping, and distributing.                                                                                                                                                                                                          | Server                    | StatefulSet     |
   |                                            |                                                                                                                                                                                                                                                                                                                                                        |                           |                 |
   | (workload name: alertmanager-alertmanager) |                                                                                                                                                                                                                                                                                                                                                        |                           |                 |
   +--------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+-----------------+
   | thanosSidecar                              | Available only in HA mode. Runs with prometheus-server in the same pod to implement persistent storage of Prometheus metric data.                                                                                                                                                                                                                      | Server                    | Container       |
   +--------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+-----------------+
   | thanosQuery                                | Available only in HA mode. Entry for PromQL query when Prometheus is in HA scenarios. It can delete duplicate metrics from Store or Prometheus.                                                                                                                                                                                                        | Server                    | Deployment      |
   +--------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+-----------------+
   | adapter                                    | Aggregates custom metrics to the native Kubernetes API Server.                                                                                                                                                                                                                                                                                         | Server                    | Deployment      |
   |                                            |                                                                                                                                                                                                                                                                                                                                                        |                           |                 |
   | (workload name: custom-metrics-apiserver)  |                                                                                                                                                                                                                                                                                                                                                        |                           |                 |
   +--------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+-----------------+
   | kubeStateMetrics                           | Converts the Prometheus metric data into a format that can be identified by Kubernetes APIs. By default, the kube-state-metrics component does not collect all labels and annotations of Kubernetes resources. To collect all labels and annotations, see :ref:`Collecting All Labels and Annotations of a Pod <cce_10_0406__section163751639151115>`. | Agent/Server              | Deployment      |
   |                                            |                                                                                                                                                                                                                                                                                                                                                        |                           |                 |
   | (workload name: kube-state-metrics)        | .. note::                                                                                                                                                                                                                                                                                                                                              |                           |                 |
   |                                            |                                                                                                                                                                                                                                                                                                                                                        |                           |                 |
   |                                            |    If the components run in multiple pods, only one pod provides metrics.                                                                                                                                                                                                                                                                              |                           |                 |
   +--------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+-----------------+
   | nodeExporter                               | Deployed on each node to collect node monitoring data.                                                                                                                                                                                                                                                                                                 | Agent/Server              | DaemonSet       |
   |                                            |                                                                                                                                                                                                                                                                                                                                                        |                           |                 |
   | (workload name: node-exporter)             |                                                                                                                                                                                                                                                                                                                                                        |                           |                 |
   +--------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+-----------------+
   | clusterProblemDetector                     | Monitors cluster exceptions.                                                                                                                                                                                                                                                                                                                           | Server                    | Deployment      |
   |                                            |                                                                                                                                                                                                                                                                                                                                                        |                           |                 |
   | (workload name: cluster-problem-detector)  |                                                                                                                                                                                                                                                                                                                                                        |                           |                 |
   +--------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+-----------------+

.. _cce_10_0406__section17830202915211:

Providing Resource Metrics Through the Metrics API
--------------------------------------------------

Resource metrics of containers and nodes, such as CPU and memory usage, can be obtained through the Kubernetes Metrics API. Resource metrics can be directly accessed, for example, by using the **kubectl top** command, or used by HPA or CustomedHPA policies for auto scaling.

The add-on can provide the Kubernetes Metrics API that is disabled by default. To enable the API, create the following APIService object:

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

You can save the object as a file, name it **metrics-apiservice.yaml**, and run the following command:

.. code-block::

   kubectl create -f metrics-apiservice.yaml

Run the **kubectl top pod -n monitoring** command. If the following information is displayed, the Metrics API can be accessed:

.. code-block::

   # kubectl top pod -n monitoring
   NAME                                                      CPU(cores)   MEMORY(bytes)
   ......
   custom-metrics-apiserver-d4f556ff9-l2j2m                  38m          44Mi
   ......

.. important::

   To uninstall the add-on, run the following kubectl command and delete the APIService object. Otherwise, the metrics-server add-on cannot be installed due to residual APIService resources.

   .. code-block::

      kubectl delete APIService v1beta1.metrics.k8s.io

.. _cce_10_0406__section11927514174016:

Creating an HPA Policy Using Custom Metrics
-------------------------------------------

HPA policies can only be used when Cloud Native Cluster Monitoring is deployed in the server mode. You can configure custom metrics required by HPA policies in the **user-adapter-config** ConfigMap.

.. important::

   To use Prometheus to monitor custom metrics, the application needs to provide a metric monitoring API. For details, see :ref:`Prometheus Monitoring Data Collection <cce_10_0373__section173671127160>`.

In this section, the nginx metric (nginx_connections_accepted) in :ref:`Monitoring Custom Metrics Using Cloud Native Cluster Monitoring <cce_10_0373>` is used as an example.

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **ConfigMaps and Secrets**.

#. Click the **ConfigMaps** tab, select the **monitoring** namespace, locate the row containing **user-adapter-config** (or **adapter-config**), and click **Update**.

#. In **Data**, click **Edit** for the **config.yaml** file to add a custom metric collection rule under the **rules** field. Click **OK**.

   You can add multiple collection rules by adding multiple configurations under the **rules** field. For details, see `Metrics Discovery and Presentation Configuration <https://github.com/kubernetes-sigs/prometheus-adapter/blob/master/docs/config.md>`__.

   Example custom metric rule:

   .. code-block::

      rules:
      # Match the metric whose name is nginx_connections_accepted. The metric name must be confirmed. Otherwise, the HPA controller cannot get the metric.
      - seriesQuery: '{__name__=~"nginx_connections_accepted",container!="POD",namespace!="",pod!=""}'
        resources:
          # Specify pod and namespace resources.
          overrides:
            namespace:
              resource: namespace
            pod:
              resource: pod
        name:
          #Use nginx_connections_accepted"
          matches: "nginx_connections_accepted"
          #Use nginx_connections_accepted_per_second to represent the metric. The name is the custom metric name in a custom HPA policy.
          as: "nginx_connections_accepted_per_second"
          # Calculate rate(nginx_connections_accepted[2m]) to specify the number of requests received per second.
        metricsQuery: 'rate(<<.Series>>{<<.LabelMatchers>>,container!="POD"}[2m])'

#. Redeploy the **custom-metrics-apiserver** workload in the **monitoring** namespace.

#. In the navigation pane, choose **Workloads**. Locate the workload for which you want to create an HPA policy and choose **More** > **Auto Scaling**. In the **Custom Policy** area, you can select the preceding parameters to create an auto scaling policy.

.. _cce_10_0406__section163751639151115:

Collecting All Labels and Annotations of a Pod
----------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Workloads**.

#. Switch to the **monitoring** namespace, click the **Deployments** tab, and click the name of the **kube-state-metrics** workload. On the page displayed, click the **Containers** tab and click **Edit** on the right.

#. In the **Lifecycle** area of the container settings, edit the startup command.

   To collect labels, add the following information to the end of the original **kube-state-metrics** startup parameter:

   .. code-block::

      --metric-labels-allowlist=pods=[*],nodes=[node,failure-domain.beta.kubernetes.io/zone,topology.kubernetes.io/zone]

   To collect annotations, add parameters in the startup parameters in the same way.

   .. code-block::

      --metric-annotations-allowlist=pods=[*],nodes=[node,failure-domain.beta.kubernetes.io/zone,topology.kubernetes.io/zone]

   .. important::

      When editing the startup command, do not modify other original startup parameters. Otherwise, the component may be abnormal.

#. **kube-state-metrics** starts to collect the labels/annotations of pods and nodes and checks whether **kube_pod_labels/kube_pod_annotations** is in the collection task of CloudScope.

   .. code-block::

      kubectl get servicemonitor kube-state-metrics -nmonitoring -oyaml | grep kube_pod_labels

For more kube-state-metrics startup parameters, see `kube-state-metrics/cli-arguments <https://github.com/kubernetes/kube-state-metrics/blob/v2.2.3/docs/cli-arguments.md>`__.

Change History
--------------

.. table:: **Table 2** Release history

   +-----------------+---------------------------+--------------------------------------------------+----------------------------------------------------------------------------+
   | Add-on Version  | Supported Cluster Version | New Feature                                      | Community Version                                                          |
   +=================+===========================+==================================================+============================================================================+
   | 3.10.1          | v1.21                     | The NodeExporter component is upgraded to 1.8.0. | `2.37.8 <https://github.com/prometheus/prometheus/releases/tag/v2.37.8>`__ |
   |                 |                           |                                                  |                                                                            |
   |                 | v1.23                     |                                                  |                                                                            |
   |                 |                           |                                                  |                                                                            |
   |                 | v1.25                     |                                                  |                                                                            |
   |                 |                           |                                                  |                                                                            |
   |                 | v1.27                     |                                                  |                                                                            |
   |                 |                           |                                                  |                                                                            |
   |                 | v1.28                     |                                                  |                                                                            |
   |                 |                           |                                                  |                                                                            |
   |                 | v1.29                     |                                                  |                                                                            |
   +-----------------+---------------------------+--------------------------------------------------+----------------------------------------------------------------------------+
