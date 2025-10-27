:original_name: cce_10_0844.html

.. _cce_10_0844:

Configuring Workload Scaling Based on GPU Monitoring Metrics
============================================================

In a standard or Turbo cluster, you can configure HPA policies for workloads that use GPU resources based on :ref:`GPU monitoring metrics <cce_10_0955>`. This enables applications to automatically scale out during peak hours and scale in during off-peak hours, optimizing resource utilization and reducing costs.

Prerequisites
-------------

-  A cluster is available, and there are GPU nodes and GPU related services running in the cluster.

-  The :ref:`CCE AI Suite (NVIDIA GPU) <cce_10_0141>` add-on has been installed in the cluster, and the add-on metrics API is working properly. You can log in to the GPU node and run the following command:

   .. code-block::

      curl {Pod IP}:2112/metrics

   In the preceding command, *{Pod IP}* must be the pod IP address of nvidia-gpu-device-plugin in CCE AI Suite (NVIDIA GPU), and metric results are expected to be returned.

-  The :ref:`Cloud Native Cluster Monitoring <cce_10_0406>` add-on of v3.9.5 or later has been installed in the cluster, and it is deployed in local data storage mode.

Collecting GPU Metrics
----------------------

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **ConfigMaps and Secrets**.

#. Select the **monitoring** namespace. On the **ConfigMaps** tab, locate the row containing **user-adapter-config** and click **Update**.

#. On the **Update ConfigMap** page, click **Edit** in the **Operation** column of the **config.yaml** file in the **Data** pane. Then, add a custom metric collection rule under the **rules** field. Click **OK**.

   You can add multiple collection rules by adding multiple configurations under the **rules** field. For details, see `Metrics Discovery and Presentation Configuration <https://github.com/kubernetes-sigs/prometheus-adapter/blob/master/docs/config.md>`__.

   The following is an example of a custom rule for collecting **cce_gpu_memory_utilization**. For details about more GPU metrics, see :ref:`GPU Metrics <cce_10_0955>`.

   .. code-block::

      rules:
      - seriesQuery: '{__name__=~"cce_gpu_memory_utilization",container!="",namespace!="",pod!=""}'
        seriesFilters: []
        resources:
          overrides:
            namespace:
              resource: namespace
            pod:
              resource: pod
        metricsQuery: sum(last_over_time(<<.Series>>{<<.LabelMatchers>>}[1m])) by (<<.GroupBy>>)

#. Redeploy the **custom-metrics-apiserver** workload in the **monitoring** namespace.

#. After the restart, check whether the metrics in the target pod are normal (replace the namespace and service pod names).

   a. Obtain metrics.

      .. code-block::

         kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta1"

      Information similar to the following is displayed:

      .. code-block::

         {"kind":"APIResourceList","apiVersion":"v1","groupVersion":"custom.metrics.k8s.io/v1beta1","resources":[{"name":"pods/cce_gpu_memory_utilization","singularName":"","namespaced":true,"kind":"MetricValueList","verbs":["get"]},{"name":"namespaces/cce_gpu_memory_utilization","singularName":"","namespaced":false,"kind":"MetricValueList","verbs":["get"]}]}

   b. Obtain workload metric values.

      .. code-block::

         kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta1/namespaces/default/pods/test-gpu-hpa-68667fdd94-grmd2/cce_gpu_memory_utilization"

      Information similar to the following is displayed:

      .. code-block::

         {"kind":"MetricValueList","apiVersion":"custom.metrics.k8s.io/v1beta1","metadata":{},"items":[{"describedObject":{"kind":"Pod","namespace":"default","name":"test-gpu-hpa-68667fdd94-grmd2","apiVersion":"/v1"},"metricName":"cce_gpu_memory_utilization","timestamp":"2024-01-10T08:36:44Z","value":"20","selector":null}]}

Creating an Auto Scaling Policy
-------------------------------

#. Choose **Workloads** in the navigation pane. Locate the target workload and choose **Auto Scaling** in the **Operation** column.

#. Set **Policy Type** to **HPA+CronHPA** and enable HPA.

   You can select GPU monitoring parameters in **Custom Policy** to create an auto scaling policy.

#. Return to the **Scaling Policies** page and check whether the HPA policy has been created.
