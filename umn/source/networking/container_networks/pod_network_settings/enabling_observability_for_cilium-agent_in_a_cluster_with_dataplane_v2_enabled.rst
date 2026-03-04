:original_name: cce_10_1064.html

.. _cce_10_1064:

Enabling Observability for cilium-agent in a Cluster with DataPlane V2 Enabled
==============================================================================

In a cluster with DataPlane V2 enabled, the cilium-agent component is deployed on each node. You can enable the observability for this component by referring to this section.

Prerequisites
-------------

-  CCE DataPlane V2 is released with restrictions. To use this feature, submit a service ticket to CCE.
-  Currently, only CCE standard clusters of v1.27.16-r50, v1.28.15-r40, v1.29.15-r0, v1.30.14-r0, v1.31.10-r0, v1.32.6-r0, and later versions that have DataPlane V2 enabled support the observability of the cilium-agent component.

Step 1: Configure a ConfigMap and Rebuild yangtse-cilium
--------------------------------------------------------

#. Connect to your cluster using kubectl. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create the **cilium-config.yaml** file and specify the following native ConfigMap configuration of the Cilium community:

   .. code-block::

      apiVersion: v1
      data:
        prometheus-serve-addr: :9962
      kind: ConfigMap
      metadata:
        name: cilium-config
        namespace: kube-system

   .. table:: **Table 1** Parameters

      +-----------------------+------------------------------------------------------------------+-----------------------------------------------------------------------+
      | Parameter             | Description                                                      | Remarks                                                               |
      +=======================+==================================================================+=======================================================================+
      | prometheus-serve-addr | Address for accessing the metrics of the cilium-agent component. | This parameter is set to **:9962** here. The colon cannot be omitted. |
      +-----------------------+------------------------------------------------------------------+-----------------------------------------------------------------------+

#. Create the ConfigMap.

   .. code-block::

      kubectl apply -f cilium-config.yaml

#. Roll back and rebuild yangtse-cilium to apply the configuration.

   .. code-block::

      uuid=$(uuidgen)
      kubectl patch daemonset -nkube-system yangtse-cilium --type='json' -p="[{\"op\": \"add\", \"path\": \"/spec/template/metadata/annotations/change-id\", \"value\": \"$uuid\"}]"

.. _cce_10_1064__section558903294616:

Step 2: Install Add-ons
-----------------------

Install the Cloud Native Cluster Monitoring and Grafana add-ons in the cluster.

#. Install the Cloud Native Cluster Monitoring add-on. For details, see :ref:`Cloud Native Cluster Monitoring <cce_10_0406>`.
#. Install the Grafana add-on. For details, see :ref:`Grafana <cce_10_0828>`.

Step 3: Configure a Cilium Observability Task
---------------------------------------------

#. Create the **cilium-monitor.yaml** file. You can specify the following configuration to create a ServiceMonitor for Cilium observability.

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          prometheus.io/port: "9962"
          prometheus.io/scrape: "true"
        labels:
          k8s-app: cilium
        name: cilium
        namespace: kube-system
      spec:
        clusterIP: None
        clusterIPs:
        - None
        internalTrafficPolicy: Cluster
        ipFamilies:
        - IPv4
        ipFamilyPolicy: SingleStack
        ports:
        - name: metrics
          port: 9962
          protocol: TCP
          targetPort: prometheus
        selector:
          k8s-app: cilium
        sessionAffinity: None
        type: ClusterIP
      ---
      apiVersion: monitoring.coreos.com/v1
      kind: ServiceMonitor
      metadata:
        name: cilium
        namespace: monitoring
      spec:
        endpoints:
        - honorLabels: true
          path: /metrics
          port: metrics
          relabelings:
          - action: labelmap
            regex: __meta_kubernetes_service_label_(.+)
          scheme: http
        jobLabel: cilium
        namespaceSelector:
          matchNames:
          - kube-system
        selector:
          matchLabels:
            k8s-app: cilium

#. Create the ServiceMonitor.

   .. code-block::

      kubectl apply -f cilium-monitor.yaml

Step 4: Configure the Cilium Metric Dashboard
---------------------------------------------

The Cilium official website provides a Grafana dashboard for Cilium metrics. Compared with the monotonous metrics of Prometheus, the Grafana dashboard is more attractive. You can access the dashboard to view Cilium metrics.

#. Prepare the configuration file of the Cilium metric dashboard. Open the `Prometheus sample file <https://raw.githubusercontent.com/cilium/cilium/v1.17.6/examples/kubernetes/addons/prometheus/monitoring-example.yaml>`__ in the Cilium community, locate **cilium-dashboard.json** in the file, and copy it.

#. Log in to Grafana, choose **Dashboards**, and import the **cilium-dashboard.json** file.

   .. caution::

      If you have enabled **Interconnect with AOM** when installing the Grafana add-on in :ref:`Step 2: Install Add-ons <cce_10_1064__section558903294616>`, change the **datasource** field in the **cilium-dashboard.json** file to **prometheus-aom**, and then import the file.

#. Access the dashboard to view Cilium metrics. For details about Cilium metrics, see `cilium-metrics <https://docs.cilium.io/en/v1.17/observability/metrics/#cilium-metrics>`__.

   |image1|

.. |image1| image:: /_static/images/en-us_image_0000002516079245.png
