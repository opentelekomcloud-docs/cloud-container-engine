:original_name: cce_10_0406.html

.. _cce_10_0406:

Cloud Native Cluster Monitoring
===============================

Introduction
------------

The Cloud Native Cluster Monitoring add-on (formerly kube-prometheus-stack) uses Prometheus-operator and Prometheus and provides easy-to-use, end-to-end Kubernetes cluster monitoring.

**Open-source community**: https://github.com/prometheus/prometheus

Notes and Constraints
---------------------

-  By default, the kube-state-metrics component of the add-on does not collect labels and annotations of Kubernetes resources. To collect these labels and annotations, manually enable the collection function in the startup parameters and check whether the corresponding metrics are added to the collection trustlist of ServiceMonitor named **kube-state-metrics**. For details, see :ref:`Collecting All Labels and Annotations of a Pod <cce_10_0406__section163751639151115>`.

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

The node-exporter component of the Cloud Native Cluster Monitoring add-on needs to read the Docker info data from the **/var/run/docker.sock** directory on the host for monitoring the Docker disk space.

The following permission is required for running node-exporter:

-  cap_dac_override: reads the Docker info data.

.. _cce_10_0406__section186134814119:

Installing the Add-on
---------------------

.. note::

   The Cloud Native Cluster Monitoring add-on automatically selects a deployment mode based on :ref:`Data Storage Configuration <cce_10_0406__li15556183414307>`. This is supported by Cloud Native Cluster Monitoring 3.7.1 or later.

   -  Original agent mode: Disable **Local Data Storage** and enable at least one of **Report Monitoring Data to AOM** and **Report Monitoring Data to a Third-Party Platform**.

   -  Original server mode: Enable **Local data storage** and **Report Monitoring Data to AOM** or **Report Monitoring Data to a Third-Party Platform**.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Add-ons**. Locate **Cloud Native Cluster Monitoring** on the right and click **Install**.

#. .. _cce_10_0406__li15556183414307:

   On the **Install Add-on** page, enable at least one item in the **Data Storage Configuration** area.

   -  **Report Monitoring Data to AOM**: Report Prometheus data to AOM. After this function is enabled, you can select the corresponding AOM instance. The collected basic metrics are free of charge. Custom metrics are charged by AOM. To interconnect with AOM, you must have certain permissions. Only **users in the** **admin** **user group** can perform this operation.
   -  **Reporting Monitoring Data to a Third-Party Monitoring Platform**: To report Prometheus data to a third-party monitoring system, you need to enter the address and token of the third-party monitoring system and determine whether to skip certificate authentication.
   -  **Local Data Storage**: Select the type and size of a disk for storing monitoring data to store Prometheus data in PVCs in the cluster. **Storage volumes are not deleted along with the add-on.** If **Local Data Storage** is enabled, all components will be deployed. For details, see :ref:`Components <cce_10_0406__section0377457163618>`.

      .. note::

         An available PVC named **pvc-prometheus-server-0** exists in namespace **monitoring** and will be used as the storage source.

#. Configure the add-on **specifications** as needed.

   -  **Add-on Specifications**:

      -  If you selected **Preset**, the system will configure the number of pods and resource quotas for the add-on based on the preset specifications. You can see the configurations on the console.
      -  If you selected **Custom**, you can adjust the number of pods and resource quotas as needed. High availability is not possible with a single pod. If an error occurs on the node where the add-on instance runs, the add-on will fail.

   -  **Prometheus HA**: The Prometheus-server, Prometheus-operator, thanos-query, custom-metrics-apiserver, alertmanager, and kube-state-metrics components are deployed in multi-instance mode in the cluster.
   -  **Number of collected shards** (available after **Local Data Storage** is disabled): When there is a lot of Prometheus data, you can configure this parameter to spread the data across a specific number of Prometheus instances. This will help with storage and querying. Increasing the number of shards reduces the data volume carried by each shard. This can increase the upper limit of the metric collection throughput and cause more resources to be consumed. By default, CCE automatically determines the number of shards based on the cluster scale. It is advised to allocate one shard for every 50 nodes. If you want to enhance collection performance by increasing the number of shards, carefully assess resource utilization and optimize based on your monitoring needs. To ensure system stability, keep the master node's memory usage below 50%.

#. Configure the add-on parameters.

   -  **Custom Metric Collection**: Application metrics are automatically collected in the form of service discovery. After this function is enabled, you need to add related configurations to the target application. For details, see :ref:`Monitoring Custom Metrics Using Cloud Native Cluster Monitoring <cce_10_0373>`.
   -  **Collection Interval**: Configure the collection interval.
   -  **Data Retention** (available only after **Local Data Storage** is enabled): Configure how long the monitoring data can be kept.
   -  **node-exporter Listening Port**: This port uses the host network to listen to and expose metrics on the node for Prometheus collection. The default port number is 9100, but it can be modified if there is a conflict with an existing port.
   -  **Scheduling Policies**: Support node affinity, taint, and tolerations. Multiple scheduling policies can be configured. If no affinity node label key or toleration node taint key is configured, this function is disabled by default.

      -  **Range**: You can select the add-on pods for which the scheduling policy takes effect. By default, the scheduling policy takes effect for all pods. If a pod is specified, the scheduling policies configured for all pods are overwritten.
      -  **Affinity Node Label Key**: Enter a node label key to set node affinity for the add-on pods.
      -  **Affinity Node Label Value**: Enter a node label value to set node affinity for the add-on pods.
      -  **Toleration Node Taint Key**: A component can be scheduled to a node that has the taint key you specified.

#. Click **Install**.

   After the add-on is installed, you may need to perform the following operations:

   -  To use custom metrics to create an auto scaling policy, ensure that **Local Data Storage** is enabled for the add-on and then take the following steps:

      a. Collect custom metrics reported by applications to Prometheus. For details, see :ref:`Monitoring Custom Metrics Using Cloud Native Cluster Monitoring <cce_10_0373>`.
      b. Aggregate these custom metrics collected by Prometheus to the API server for the HPA policy to use. For details, see :ref:`Creating an HPA Policy Using Custom Metrics <cce_10_0406__section11927514174016>`.

   -  To provide system resource metrics (such as CPU and memory usage) for workload auto scaling using this add-on, ensure that **Local Data Storage** is enabled for the add-on and then enable the Metrics API. For details, see :ref:`Providing Basic Resource Metrics Through the Metrics API <cce_10_0406__section17830202915211>`. After the configuration, you can use Prometheus to collect system resource metrics. (This operation is not recommended because it may conflict with the Kubernetes Metric Server add-on.)

.. _cce_10_0406__section0377457163618:

Components
----------

All Kubernetes resources created during Cloud Native Cluster Monitoring add-on installation are created in the namespace named **monitoring**.

.. table:: **Table 1** Add-on components

   +--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------+-----------------+
   | Component                                                                                                                                                                | Description                                                                                                                                                                                                                                                                                                                                            | Supported Deployment Mode  | Resource Type   |
   +==========================================================================================================================================================================+========================================================================================================================================================================================================================================================================================================================================================+============================+=================+
   | prometheusOperator                                                                                                                                                       | The control center of the entire system. It deploys and manages Prometheus server based on Prometheus CustomResourceDefinitions (CRDs), and monitors and processes the changes of these CRDs.                                                                                                                                                          | All                        | Deployment      |
   |                                                                                                                                                                          |                                                                                                                                                                                                                                                                                                                                                        |                            |                 |
   | (workload name: prometheus-operator)                                                                                                                                     |                                                                                                                                                                                                                                                                                                                                                        |                            |                 |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------+-----------------+
   | prometheus                                                                                                                                                               | The core component of the Prometheus monitoring system. It is used to collect and store metrics and report the metrics to AOM or a third-party monitoring platform. If local storage is enabled, the metric data can be stored in local PVCs.                                                                                                          | All                        | StatefulSet     |
   |                                                                                                                                                                          |                                                                                                                                                                                                                                                                                                                                                        |                            |                 |
   | (If the local data storage is used, the workload name is **prometheus-server**. If the local data storage is not used, the workload name is **prometheus-lightweight**.) |                                                                                                                                                                                                                                                                                                                                                        |                            |                 |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------+-----------------+
   | alertmanager                                                                                                                                                             | Alarm center of the add-on. It receives alarms sent by Prometheus and manages alarm information by deduplicating, grouping, and distributing.                                                                                                                                                                                                          | Local data storage enabled | StatefulSet     |
   |                                                                                                                                                                          |                                                                                                                                                                                                                                                                                                                                                        |                            |                 |
   | (workload name: alertmanager-alertmanager)                                                                                                                               |                                                                                                                                                                                                                                                                                                                                                        |                            |                 |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------+-----------------+
   | thanosSidecar                                                                                                                                                            | Available only in HA mode. Runs with prometheus-server in the same pod to implement persistent storage of Prometheus metric data.                                                                                                                                                                                                                      | Local data storage enabled | Container       |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------+-----------------+
   | thanosQuery                                                                                                                                                              | Available only in HA mode. Entry for PromQL query when Prometheus is in HA scenarios. It can delete duplicate metrics from Store or Prometheus.                                                                                                                                                                                                        | Local data storage enabled | Deployment      |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------+-----------------+
   | adapter                                                                                                                                                                  | Aggregates custom metrics to the native Kubernetes API Server.                                                                                                                                                                                                                                                                                         | Local data storage enabled | Deployment      |
   |                                                                                                                                                                          |                                                                                                                                                                                                                                                                                                                                                        |                            |                 |
   | (workload name: custom-metrics-apiserver)                                                                                                                                |                                                                                                                                                                                                                                                                                                                                                        |                            |                 |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------+-----------------+
   | kubeStateMetrics                                                                                                                                                         | Converts the Prometheus metric data into a format that can be identified by Kubernetes APIs. By default, the kube-state-metrics component does not collect all labels and annotations of Kubernetes resources. To collect all labels and annotations, see :ref:`Collecting All Labels and Annotations of a Pod <cce_10_0406__section163751639151115>`. | All                        | Deployment      |
   |                                                                                                                                                                          |                                                                                                                                                                                                                                                                                                                                                        |                            |                 |
   | (workload name: kube-state-metrics)                                                                                                                                      | .. note::                                                                                                                                                                                                                                                                                                                                              |                            |                 |
   |                                                                                                                                                                          |                                                                                                                                                                                                                                                                                                                                                        |                            |                 |
   |                                                                                                                                                                          |    If the components run in multiple pods, only one pod provides metrics.                                                                                                                                                                                                                                                                              |                            |                 |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------+-----------------+
   | nodeExporter                                                                                                                                                             | Deployed on each node to collect node monitoring data.                                                                                                                                                                                                                                                                                                 | All                        | DaemonSet       |
   |                                                                                                                                                                          |                                                                                                                                                                                                                                                                                                                                                        |                            |                 |
   | (workload name: node-exporter)                                                                                                                                           |                                                                                                                                                                                                                                                                                                                                                        |                            |                 |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------+-----------------+

.. _cce_10_0406__section17830202915211:

Providing Basic Resource Metrics Through the Metrics API
--------------------------------------------------------

.. note::

   If **Local Data Storage** is enabled for the Cloud Native Cluster Monitoring add-on, basic resource metrics can only be provided through Metrics API.

Resource metrics of containers and nodes, such as CPU and memory usage, can be obtained through the Kubernetes Metrics API. Resource metrics can be directly accessed, for example, by using the **kubectl top** command, or used by HPA or CustomedHPA policies for auto scaling.

The add-on can provide the Kubernetes Metrics API that is disabled by default. To enable the Metrics API, create the following APIService object:

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

Run the **kubectl top pod -n monitoring** command. If information similar to the following is displayed, the Metrics API can be accessed:

.. code-block::

   NAME                                                      CPU(cores)   MEMORY(bytes)
   ......
   custom-metrics-apiserver-d4f556ff9-l2j2m                  38m          44Mi
   ......

.. important::

   To uninstall the add-on, run the following kubectl command and delete the APIService object. Otherwise, residual APIService resources may prevent the installation of the Kubernetes Metrics Server add-on.

   .. code-block::

      kubectl delete APIService v1beta1.metrics.k8s.io

.. _cce_10_0406__section11927514174016:

Creating an HPA Policy Using Custom Metrics
-------------------------------------------

HPA policies can only be used when Cloud Native Cluster Monitoring is deployed with **Local Data Storage** enabled. You can configure custom metrics required by HPA policies in the **user-adapter-config** ConfigMap.

.. important::

   To use Prometheus to monitor custom metrics, the application needs to provide a metric monitoring API. For details, see :ref:`Prometheus Monitoring Data Collection <cce_10_0373__section173671127160>`.

In this section, the nginx metric (nginx_connections_accepted) in :ref:`Monitoring Custom Metrics Using Cloud Native Cluster Monitoring <cce_10_0373>` is used as an example.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **ConfigMaps and Secrets**. Switch to the **monitoring** namespace, click the **ConfigMaps** tab, locate the row containing **user-adapter-config** (or **adapter-config**), and click **Update**.

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

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Workloads**. Switch to the **monitoring** namespace, click the **Deployments** tab, and click the name of the **kube-state-metrics** workload. On the page displayed, click the **Containers** tab and click **Edit** on the right.

#. In the **Lifecycle** area of the container settings, edit the startup command.

   To collect labels, add the following information to the end of the original **kube-state-metrics** startup parameter:

   .. code-block::

      --metric-labels-allowlist=pods=[*],nodes=[node,failure-domain.beta.kubernetes.io/zone,topology.kubernetes.io/zone]

   To collect annotations, add parameters in the startup parameters in the same way.

   .. code-block::

      --metric-annotations-allowlist=pods=[*],nodes=[node,failure-domain.beta.kubernetes.io/zone,topology.kubernetes.io/zone]

   .. important::

      When editing the startup command, do not modify other original startup parameters. Otherwise, the component may be abnormal.

#. **kube-state-metrics** starts to collect the labels/annotations of pods and nodes and checks whether **kube_pod_labels/kube_pod_annotations** is in the collection task of Prometheus.

   .. code-block::

      kubectl get servicemonitor kube-state-metrics -nmonitoring -oyaml | grep kube_pod_labels

For more kube-state-metrics startup parameters, see `kube-state-metrics/cli-arguments <https://github.com/kubernetes/kube-state-metrics/blob/v2.2.3/docs/cli-arguments.md>`__.

Release History
---------------

.. table:: **Table 2** Cloud Native Cluster Monitoring add-on

   +-----------------+---------------------------+--------------------------------------+----------------------------------------------------------------------------+
   | Add-on Version  | Supported Cluster Version | New Feature                          | Community Version                                                          |
   +=================+===========================+======================================+============================================================================+
   | 3.12.1          | v1.21                     | CCE clusters v1.32 are supported.    | `2.53.2 <https://github.com/prometheus/prometheus/releases/tag/v2.53.2>`__ |
   |                 |                           |                                      |                                                                            |
   |                 | v1.23                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.25                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.27                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.28                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.29                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.30                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.31                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.32                     |                                      |                                                                            |
   +-----------------+---------------------------+--------------------------------------+----------------------------------------------------------------------------+
   | 3.12.0          | v1.21                     | -  CCE clusters v1.31 are supported. | `2.53.2 <https://github.com/prometheus/prometheus/releases/tag/v2.53.2>`__ |
   |                 |                           | -  Upgraded Prometheus.              |                                                                            |
   |                 | v1.23                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.25                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.27                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.28                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.29                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.30                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.31                     |                                      |                                                                            |
   +-----------------+---------------------------+--------------------------------------+----------------------------------------------------------------------------+
   | 3.11.0          | v1.21                     | CCE clusters v1.30 are supported.    | `2.37.8 <https://github.com/prometheus/prometheus/releases/tag/v2.37.8>`__ |
   |                 |                           |                                      |                                                                            |
   |                 | v1.23                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.25                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.27                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.28                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.29                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.30                     |                                      |                                                                            |
   +-----------------+---------------------------+--------------------------------------+----------------------------------------------------------------------------+
   | 3.10.1          | v1.21                     | Upgraded NodeExporter to 1.8.0.      | `2.37.8 <https://github.com/prometheus/prometheus/releases/tag/v2.37.8>`__ |
   |                 |                           |                                      |                                                                            |
   |                 | v1.23                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.25                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.27                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.28                     |                                      |                                                                            |
   |                 |                           |                                      |                                                                            |
   |                 | v1.29                     |                                      |                                                                            |
   +-----------------+---------------------------+--------------------------------------+----------------------------------------------------------------------------+
