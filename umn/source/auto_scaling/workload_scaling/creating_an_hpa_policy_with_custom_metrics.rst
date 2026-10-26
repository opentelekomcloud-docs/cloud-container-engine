:original_name: cce_10_0944.html

.. _cce_10_0944:

Creating an HPA Policy with Custom Metrics
==========================================

Kubernetes' default HPA policy only allows for auto scaling based on CPU and memory usage. However, in more complex service scenarios, this may not be sufficient to meet routine O&M requirements. To resolve this issue, you can configure HPA policies with custom metrics, allowing for flexible workload scaling.

This section uses an example to describe how to deploy an Nginx application, which uses the **container_cpu_usage_core_per_second** metric exposed by Prometheus to identify the number of CPU cores used by a container on a per-second basis. For more information about Prometheus metrics, see `Metric types <https://prometheus.io/docs/concepts/metric_types/>`__.

How It Works
------------

CCE fully integrates with the open-source Prometheus ecosystem. It supports monitoring of various custom metrics and auto scaling based on those metrics. The following figure shows the architecture.


.. figure:: /_static/images/en-us_image_0000002662009385.png
   :alt: **Figure 1** Using an HPA policy with custom metrics

   **Figure 1** Using an HPA policy with custom metrics

The core process for implementing HPA auto scaling with custom metrics is as follows:

#. **Exposing metrics**: Applications instrument their code to expose key metrics (such as queue length, online user count, and requests per second).
#. **Metric collection**: If an application cannot directly expose metrics, deploy an Exporter to scrape data and convert it into a Prometheus-compatible format.
#. **Metric storage**: Prometheus periodically scrapes metrics from Exporters or applications and stores them as time-series data for querying and analysis.
#. **Metric translation**: defines the mapping between PromQL queries and Kubernetes custom metric API names in the adapter configuration file (**user-adapter-config**).
#. **API registration**: registers the custom metric API with the Kubernetes API server so that HPA can query custom metrics through a unified interface, like CPU metrics.
#. **Scaling**: HPA periodically queries custom metric values, calculates the desired replica count, and scales the workload accordingly.

Step 1: Install Cloud Native Cluster Monitoring
-----------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Add-ons**. Locate **Cloud Native Cluster Monitoring** on the right and click **Install**.

   Prioritize the configurations listed below and adjust any other configurations as needed. For details, see :ref:`Cloud Native Cluster Monitoring <cce_10_0406>`.

   -  **Data Storage Configuration**: Local data storage is mandatory, and other configurations are optional.
   -  Auto Service Discovery: To :ref:`use annotations to monitor custom metrics <cce_10_0373>`, enable Auto Service Discovery of the add-on. Otherwise, metrics cannot be automatically discovered and collected.

#. Click **Install**.

Step 2: Register the Metrics API
--------------------------------

Register Prometheus as the service that provides the Metrics API. For details, see :ref:`Providing Basic Resource Metrics Through the Metrics API <cce_10_0406__section17830202915211>`. If :ref:`Kubernetes Metrics Server <cce_10_0205>` has been installed in the cluster, the Metrics API is provided by default. No manual registration is required.

Step 3: Create a Workload
-------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane of the cluster console, choose **Workloads**. Then, click **Create Workload** in the upper right corner. Create an Nginx workload. For details, see :ref:`Creating a Deployment <cce_10_0047>`.

Step 4: Modify the Configuration File
-------------------------------------

#. In the navigation pane of the cluster console, choose **ConfigMaps and Secrets** and switch to the **monitoring** namespace.

#. Update **user-adapter-config**. You can modify the **rules** field in **user-adapter-config** to convert the metrics exposed by Prometheus to metrics that can be associated with HPA.

   Add the following example rule:

   .. code-block::

      ...
      data:
        config.yaml: |
          rules:
          - seriesQuery: 'container_cpu_usage_seconds_total{namespace!="",pod!=""}'
            seriesFilters: []
            resources:
              overrides:
                namespace:
                  resource: namespace
                pod:
                  resource: pod
            name:
              matches: "^(.*)_seconds_total"
              as: "${1}_core_per_second"
            metricsQuery: 'sum(rate(<<.Series>>{<<.LabelMatchers>>}[1m])) by (<<.GroupBy>>)'
      ...

   In this example, the existing **container_cpu_usage_seconds_total** metrics are aggregated into the **container_cpu_usage_core_per_second** metric, which is then used for HPA policies. For details, see `Metrics Discovery and Presentation Configuration <https://github.com/kubernetes-sigs/prometheus-adapter/blob/master/docs/config.md>`__.

   -  **seriesQuery**: PromQL request data, which specifies the metrics to be obtained by users. You can configure this parameter as needed.
   -  **metricsQuery**: aggregates the PromQL query data in **seriesQuery**.
   -  **resources**: data labels in PromQL, which are used to match resources. The resources here refer to api-resource such as pods, namespaces, and nodes in a cluster. You can run **kubectl api-resources -o wide** to check the resources. The key corresponds to **LabelName** in the Prometheus data. Ensure that the Prometheus metric data contains **LabelName**.
   -  **name**: indicates that Prometheus metric names are converted to readable metric names based on regular expression matching. In this example, **container_cpu_usage_seconds_total** is converted to **container_cpu_usage_core_per_second**.

#. Redeploy the **custom-metrics-apiserver** workload in the **monitoring** namespace.

#. Wait for about 15 seconds and run the following command to check whether the metric has been added.

   .. code-block::

      kubectl get --raw  "/apis/custom.metrics.k8s.io/v1beta1/namespaces/default/pods/*/container_cpu_usage_core_per_second"

   |image1|

Step 5: Verify HPA Scaling
--------------------------

#. Choose **Workloads** in the navigation pane. Locate the target workload and choose **More** > **Auto Scaling** in the **Operation** column.
#. Set **Policy Type** to **HPA+CronHPA** and enable an HPA policy. You can use the custom metrics configured in **rules** to create the HPA policy.
#. Click the workload name and switch to the **Auto Scaling** tab. You can find that the HPA policy has been triggered.

.. |image1| image:: /_static/images/en-us_image_0000002662009381.png
