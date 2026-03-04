:original_name: cce_10_0944.html

.. _cce_10_0944:

Creating an HPA Policy with Custom Metrics
==========================================

Kubernetes' default HPA policy only allows for auto scaling based on CPU and memory usage. However, in more complex service scenarios, this may not be sufficient to meet routine O&M requirements. To resolve this issue, you can configure HPA policies with custom metrics, allowing for flexible workload scaling.

This section uses an example to describe how to deploy an Nginx application, which uses the **container_cpu_usage_core_per_second** metric exposed by Prometheus to identify the number of CPU cores used by a container on a per-second basis. For more information about Prometheus metrics, see `METRIC TYPES <https://prometheus.io/docs/concepts/metric_types/>`__.

Step 1: Install Cloud Native Cluster Monitoring
-----------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Add-ons**. Locate **Cloud Native Cluster Monitoring** on the right and click **Install**.

   Prioritize the configurations listed below and adjust any other configurations as needed. For details, see :ref:`Cloud Native Cluster Monitoring <cce_10_0406>`.

   -  **Data Storage Configuration**: Local data storage is mandatory, and other configurations are optional.
   -  **Custom Metric Collection**: Enable this option in this example. If this option is not enabled, custom metrics cannot be collected.

#. Click **Install**.

Step 2: Create a Workload
-------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane of the cluster console, choose **Workloads**. Then, click **Create Workload** in the upper right corner. Create an Nginx workload. For details, see :ref:`Creating a Deployment <cce_10_0047>`.

Step 3: Modify the Configuration File
-------------------------------------

#. In the navigation pane of the cluster console, choose **ConfigMaps and Secrets** and switch to the **monitoring** namespace.

#. Update **user-adapter-config**. You can modify the **rules** field in **user-adapter-config** to convert the metrics exposed by Prometheus to metrics that can be associated with HPA.

   Add the following example rule:

   .. code-block::

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

Step 4: Verify HPA Scaling
--------------------------

#. Choose **Workloads** in the navigation pane. Locate the target workload and choose **More** > **Auto Scaling** in the **Operation** column.
#. Set **Policy Type** to **HPA+CronHPA** and enable an HPA policy. You can use the custom metrics configured in **rules** to create the HPA policy.
#. Click the workload name and switch to the **Auto Scaling** tab. You can find that the HPA policy has been triggered.

.. |image1| image:: /_static/images/en-us_image_0000002483959146.png
