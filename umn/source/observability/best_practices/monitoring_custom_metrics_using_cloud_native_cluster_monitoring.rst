:original_name: cce_10_0373.html

.. _cce_10_0373:

Monitoring Custom Metrics Using Cloud Native Cluster Monitoring
===============================================================

CCE provides the Cloud Native Cluster Monitoring add-on to monitor custom metrics using Prometheus.

The following procedure uses an Nginx application as an example to describe how to use Prometheus to monitor custom metrics:

#. :ref:`Installing and Accessing Cloud Native Cluster Monitoring <cce_10_0373__section1135613420551>`

   CCE provides an add-on that integrates Prometheus functions. You can install it with several clicks.

#. :ref:`Preparing an Application <cce_10_0373__section2212930134620>`

   Prepare an application image. The application must provide a metric monitoring API for Prometheus to collect data, and the monitoring data must :ref:`comply with the Prometheus specifications <cce_10_0373__section173671127160>`.

#. Monitoring Custom Metrics

   Use the application image to deploy a workload in a cluster. Custom metrics will be automatically reported to Prometheus.

   Use one of the following methods to monitor custom metrics:

   -  :ref:`Method 1: Configuring Pod Annotations for Monitoring Custom Metrics <cce_10_0373__section179021319175>`
   -  :ref:`Method 2: Configuring Service Annotations for Monitoring Custom Metrics <cce_10_0373__section103284201722>`
   -  :ref:`Method 3: Configuring PodMonitor for Monitoring Custom Metrics <cce_10_0373__section3497144813213>`
   -  :ref:`Method 4: Configuring ServiceMonitor for Monitoring Custom Metrics <cce_10_0373__section92241661317>`

Constraints
-----------

-  To use Prometheus to monitor custom metrics, the application needs to provide a metric monitoring API. For details, see :ref:`Prometheus Monitoring Data Collection <cce_10_0373__section173671127160>`.
-  Currently, metrics in the **kube-system** and **monitoring** namespaces cannot be collected when pod and service annotations are used. To collect metrics in the two namespaces, use PodMonitor and ServiceMonitor.
-  The nginx/nginx-prometheus-exporter:0.9.0 image is pulled for the Nginx application. You need to add an EIP for the node where the application is deployed or upload the image to SWR to prevent application deployment failures.

.. _cce_10_0373__section173671127160:

Prometheus Monitoring Data Collection
-------------------------------------

Prometheus periodically calls the metric monitoring API (**/metrics** by default) of an application to obtain monitoring data. The application needs to provide the metric monitoring API for Prometheus to call, and the monitoring data must meet the following specifications of Prometheus:

.. code-block::

   # TYPE nginx_connections_active gauge
   nginx_connections_active 2
   # TYPE nginx_connections_reading gauge
   nginx_connections_reading 0

Prometheus provides clients in various languages. For details about the clients, see `Prometheus CLIENT LIBRARIES <https://prometheus.io/docs/instrumenting/clientlibs/>`__. For details about how to develop an exporter, see `WRITING EXPORTERS <https://prometheus.io/docs/instrumenting/writing_exporters/>`__. The Prometheus community provides various third-party exporters that can be directly used. For details, see `EXPORTERS AND INTEGRATIONS <https://prometheus.io/docs/instrumenting/exporters/>`__.

.. _cce_10_0373__section1135613420551:

Installing and Accessing Cloud Native Cluster Monitoring
--------------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane on the left, choose **Add-ons**. On the displayed page, locate the Cloud Native Cluster Monitoring add-on and click **Install**.

   When installing this add-on, pay attention to the following configurations. Configure other parameters as required. For details, see :ref:`Cloud Native Cluster Monitoring <cce_10_0406>`.

   -  For 3.8.0 or later, enable custom metric collection.
   -  For 3.8.0 or earlier, do not enable custom metric collection.

#. After this add-on is installed, deploy workloads and Services. The Prometheus server will be deployed as a StatefulSet in the **monitoring** namespace.

   You can create a public network :ref:`LoadBalancer Service <cce_10_0014>` so that Prometheus can be accessed from external networks.

   a. Log in to the CCE console and click the name of the cluster with Prometheus installed to access the cluster console. In the navigation pane on the left, choose **Services & Ingresses**.

   b. Click **Create from YAML** in the upper right corner to create a public network LoadBalancer Service.

      .. code-block::

         apiVersion: v1
         kind: Service
         metadata:
           name: prom-lb     # Service name, which is customizable.
           namespace: monitoring
           labels:
             app: prometheus
             component: server
           annotations:
             kubernetes.io/elb.id: 038ff***     # Replace 038ff*** with the ID of the public network load balancer in the VPC that the cluster belongs to.
         spec:
           ports:
             - name: cce-service-0
               protocol: TCP
               port: 88             # Service port, which is customizable.
               targetPort: 9090     # Default Prometheus port. Retain the default value.
           selector:                # The label selector can be adjusted based on the label of a Prometheus server instance.
             app.kubernetes.io/name: prometheus
             prometheus: server
           type: LoadBalancer

   c. After the Service is created, enter *Public IP address of the load balancer*\ **:**\ *Service port* in the address box of the browser to access Prometheus.

.. _cce_10_0373__section2212930134620:

Preparing an Application
------------------------

User-developed applications must provide a metric monitoring API, and the monitoring data must comply with the Prometheus specifications. For details, see :ref:`Prometheus Monitoring Data Collection <cce_10_0201__section173671127160>`.

This section uses Nginx as an example to describe how to collect monitoring data. There is a module named **ngx_http_stub_status_module** in Nginx, which provides basic monitoring functions. You can configure the **nginx.conf** file to provide an interface for external systems to access Nginx monitoring data.

#. Log in to a Linux VM that can access to the Internet and run Docker commands.

#. Create an **nginx.conf** file. Add the server configuration under **http** to enable Nginx to provide an interface for the external systems to access the monitoring data.

   .. code-block::

      user  nginx;
      worker_processes  auto;

      error_log  /var/log/nginx/error.log warn;
      pid        /var/run/nginx.pid;

      events {
          worker_connections  1024;
      }

      http {
          include       /etc/nginx/mime.types;
          default_type  application/octet-stream;
          log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                            '$status $body_bytes_sent "$http_referer" '
                            '"$http_user_agent" "$http_x_forwarded_for"';

          access_log  /var/log/nginx/access.log  main;
          sendfile        on;
          #tcp_nopush     on;
          keepalive_timeout  65;
          #gzip  on;
          include /etc/nginx/conf.d/*.conf;

          server {
            listen 8080;
            server_name  localhost;
            location /stub_status {
               stub_status on;
               access_log off;
            }
          }
      }

#. Use this configuration to create an image and a Dockerfile file.

   .. code-block::

      vi Dockerfile

   The content of Dockerfile is as follows:

   .. code-block::

      FROM nginx:1.21.5-alpine
      ADD nginx.conf /etc/nginx/nginx.conf
      EXPOSE 80
      CMD ["nginx", "-g", "daemon off;"]

#. Use this Dockerfile to build an image and upload it to SWR. The image name is **nginx:exporter**.

   a. In the navigation pane, choose **My Images**. In the upper right corner, click **Upload Through Client**. On the displayed dialog box, click **Generate a temporary login command** and click |image1| to copy the command.

   b. Run the login command copied in the previous step on the node. If the login is successful, the message "Login Succeeded" is displayed.

   c. Run the following command to build an image named nginx. The image version is exporter.

      .. code-block::

         docker build -t nginx:exporter .

   d. Tag the image and upload it to the image repository. Change the image repository address and organization name based on your requirements.

      .. code-block::

         docker tag nginx:exporter {swr-address}/{group}/nginx:exporter
         docker push {swr-address}/{group}/nginx:exporter

#. View application metrics.

   a. Use **nginx:exporter** to create a workload.

   b. :ref:`Access the container <cce_10_00356>` and use http://<ip_address>:8080/stub_status to obtain nginx monitoring data. **<ip_address>** indicates the IP address of the container. Information similar to the following is displayed.

      .. code-block::

         # curl http://127.0.0.1:8080/stub_status
         Active connections: 3
         server accepts handled requests
          146269 146269 212
         Reading: 0 Writing: 1 Waiting: 2

.. _cce_10_0373__section179021319175:

Method 1: Configuring Pod Annotations for Monitoring Custom Metrics
-------------------------------------------------------------------

When the annotation settings of pods comply with the Prometheus data collection rules, Prometheus automatically collects the metrics exposed by the pods.

The format of the monitoring data provided by **nginx:exporter** does not meet the requirements of Prometheus. Convert the data format to the format required by Prometheus. To convert the format of Nginx metrics, use `nginx-prometheus-exporter <https://github.com/nginxinc/nginx-prometheus-exporter>`__. Deploy **nginx:exporter** and **nginx-prometheus-exporter** in the same pod and add the following annotations during deployment. Then Prometheus can automatically collect metrics.

.. code-block::

   kind: Deployment
   apiVersion: apps/v1
   metadata:
     name: nginx-exporter
     namespace: default
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: nginx-exporter
     template:
       metadata:
         labels:
           app: nginx-exporter
         annotations:
           prometheus.io/scrape: "true"
           prometheus.io/port: "9113"
           prometheus.io/path: "/metrics"
           prometheus.io/scheme: "http"
       spec:
         containers:
           - name: container-0
             image: 'nginx:exporter'      # Replace it with the address of the image you uploaded to SWR.
             resources:
               limits:
                 cpu: 250m
                 memory: 512Mi
               requests:
                 cpu: 250m
                 memory: 512Mi
           - name: container-1
             image: 'nginx/nginx-prometheus-exporter:0.9.0'
             command:
               - nginx-prometheus-exporter
             args:
               - '-nginx.scrape-uri=http://127.0.0.1:8080/stub_status'
         imagePullSecrets:
           - name: default-secret

Where,

-  **prometheus.io/scrape** indicates whether to enable Prometheus to collect pod monitoring data. The value is **true**.
-  **prometheus.io/port** indicates the port for collecting monitoring data, which varies depending on the application. In this example, the port is 9113.
-  **prometheus.io/path** indicates the URL of the API for collecting monitoring data. If this parameter is not set, the default value **/metrics** is used.
-  **prometheus.io/scheme**: protocol used for data collection. The value can be **http** or **https**.

After the application is successfully deployed, :ref:`access Prometheus <cce_10_0373__section1135613420551>` to query custom metrics by job name.

The custom metrics related to Nginx can be obtained. In the following, the job name indicates that the metrics are reported based on the pod configuration.

.. code-block::

   nginx_connections_accepted{cluster="2048c170-8359-11ee-9527-0255ac1000cf", cluster_category="CCE", cluster_name="cce-test", container="container-0", instance="10.0.0.46:9113", job="monitoring/kubernetes-pods", kubernetes_namespace="default", kubernetes_pod="nginx-exporter-77bf4d4948-zsb59", namespace="default", pod="nginx-exporter-77bf4d4948-zsb59", prometheus="monitoring/server"}

.. _cce_10_0373__section103284201722:

Method 2: Configuring Service Annotations for Monitoring Custom Metrics
-----------------------------------------------------------------------

When the annotation settings of Services comply with the Prometheus data collection rules, Prometheus automatically collects the metrics exposed by the Services.

You can use Service annotations in the same way as pod annotations. However, their application scenarios are different. Pod annotations focus on pod resource usage metrics while Service annotations focus on metrics such as requests for a Service.

The following is an example configuration:

.. code-block::

   kind: Deployment
   apiVersion: apps/v1
   metadata:
     name: nginx-test
     namespace: default
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: nginx-test
     template:
       metadata:
         labels:
           app: nginx-test
       spec:
         containers:
           - name: container-0
             image: 'nginx:exporter'      # Replace it with the address of the image you uploaded to SWR.
             resources:
               limits:
                 cpu: 250m
                 memory: 512Mi
               requests:
                 cpu: 250m
                 memory: 512Mi
           - name: container-1
             image: 'nginx/nginx-prometheus-exporter:0.9.0'
             command:
               - nginx-prometheus-exporter
             args:
               - '-nginx.scrape-uri=http://127.0.0.1:8080/stub_status'
         imagePullSecrets:
           - name: default-secret

The following is an example Service configuration:

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     name: nginx-test
     labels:
       app: nginx-test
     namespace: default
     annotations:
       prometheus.io/scrape: "true"  # Value true indicates that service discovery is enabled.
       prometheus.io/port: "9113"  # Set it to the port on which metrics are exposed.
       prometheus.io/path: "/metrics" # Enter the URI path under which metrics are exposed. Generally, the value is /metrics.
   spec:
     selector:
       app: nginx-test
     externalTrafficPolicy: Cluster
     ports:
       - name: cce-service-0
         targetPort: 80
         nodePort: 0
         port: 8080
         protocol: TCP
       - name: cce-service-1
         protocol: TCP
         port: 9113
         targetPort: 9113
     type: NodePort

After the application is successfully deployed, :ref:`access Prometheus <cce_10_0373__section1135613420551>` to query custom metrics. In the following, the Service name indicates the metrics are reported based on the Service configuration.

.. code-block::

   nginx_connections_accepted{app="nginx-test", cluster="2048c170-8359-11ee-9527-0255ac1000cf", cluster_category="CCE", cluster_name="cce-test", instance="10.0.0.38:9113", job="nginx-test", kubernetes_namespace="default", kubernetes_service="nginx-test", namespace="default", pod="nginx-test-78cfb65889-gtv7z", prometheus="monitoring/server", service="nginx-test"}

.. _cce_10_0373__section3497144813213:

Method 3: Configuring PodMonitor for Monitoring Custom Metrics
--------------------------------------------------------------

Cloud Native Cluster Monitoring allows you to configure metric collection tasks based on PodMonitor and ServiceMonitor. Prometheus Operator watches PodMonitor. The reload mechanism of Prometheus is used to trigger a hot update of the Prometheus collection tasks to the Prometheus instance.

To use CRDs defined by Prometheus Operator on GitHub, visit https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack/charts/crds/crds.

The following is an example configuration:

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nginx-test2
     namespace: default
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: nginx-test2
     template:
       metadata:
         labels:
           app: nginx-test2
       spec:
         containers:
         - image: nginx:exporter     # Replace it with the address of the image you uploaded to SWR.
           name: container-0
           ports:
           - containerPort: 9113      # Port on which metrics are exposed.
             name: nginx-test2        # Application name used when PodMonitor is configured.
             protocol: TCP
           resources:
             limits:
               cpu: 250m
               memory: 300Mi
             requests:
               cpu: 100m
               memory: 100Mi
         - name: container-1
           image: 'nginx/nginx-prometheus-exporter:0.9.0'
           command:
             - nginx-prometheus-exporter
           args:
             - '-nginx.scrape-uri=http://127.0.0.1:8080/stub_status'
         imagePullSecrets:
           - name: default-secret

The following is an example PodMonitor configuration:

.. code-block::

   apiVersion: monitoring.coreos.com/v1
   kind: PodMonitor
   metadata:
     name: podmonitor-nginx   # PodMonitor name
     namespace: monitoring    # Namespace that PodMonitor belongs to. monitoring is recommended.
   spec:
     namespaceSelector:       # An selector matching the namespace where the workload is located
       matchNames:
       - default              # Namespace that the workload belongs to
     jobLabel: podmonitor-nginx
     podMetricsEndpoints:
     - interval: 15s
       path: /metrics            # Path under which metrics are exposed by the workload
       port: nginx-test2         # Port on which metrics are exposed by the workload
       tlsConfig:
         insecureSkipVerify: true
     selector:
       matchLabels:
         app: nginx-test2   # Label carried by the pod, which can be selected by the selector

After the application is successfully deployed, :ref:`access Prometheus <cce_10_0373__section1135613420551>` to query custom metrics. In the following, the job name indicates the metrics are reported based on the PodMonitor configuration.

.. code-block::

   nginx_connections_accepted{cluster="2048c170-8359-11ee-9527-0255ac1000cf", cluster_category="CCE", cluster_name="cce-test", container="container-0", endpoint="nginx-test2", instance="10.0.0.44:9113", job="monitoring/podmonitor-nginx", namespace="default", pod="nginx-test2-746b7f8fdd-krzfp", prometheus="monitoring/server"}

.. _cce_10_0373__section92241661317:

Method 4: Configuring ServiceMonitor for Monitoring Custom Metrics
------------------------------------------------------------------

Cloud Native Cluster Monitoring allows you to configure metric collection tasks based on PodMonitor and ServiceMonitor. Prometheus Operator watches ServiceMonitor. The reload mechanism of Prometheus is used to trigger a hot update of the Prometheus collection tasks to the Prometheus instance.

To use CRDs defined by Prometheus Operator on GitHub, visit https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack/charts/crds/crds.

The following is an example configuration:

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nginx-test3
     namespace: default
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: nginx-test3
     template:
       metadata:
         labels:
           app: nginx-test3
       spec:
         containers:
         - image: nginx:exporter        # Replace it with the address of the image you uploaded to SWR.
           name: container-0
           resources:
             limits:
               cpu: 250m
               memory: 300Mi
             requests:
               cpu: 100m
               memory: 100Mi
         - name: container-1
           image: 'nginx/nginx-prometheus-exporter:0.9.0'
           command:
             - nginx-prometheus-exporter
           args:
             - '-nginx.scrape-uri=http://127.0.0.1:8080/stub_status'
         imagePullSecrets:
           - name: default-secret

The following is an example Service configuration:

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     name: nginx-test3
     labels:
       app: nginx-test3
     namespace: default
   spec:
     selector:
       app: nginx-test3
     externalTrafficPolicy: Cluster
     ports:
       - name: cce-service-0
         targetPort: 80
         nodePort: 0
         port: 8080
         protocol: TCP
       - name: servicemonitor-ports
         protocol: TCP
         port: 9113
         targetPort: 9113
     type: NodePort

The following is an example ServiceMonitor configuration:

.. code-block::

   apiVersion: monitoring.coreos.com/v1
   kind: ServiceMonitor
   metadata:
     name: servicemonitor-nginx
     namespace: monitoring
   spec:
     # Configure the name of the port on which metrics are exposed.
     endpoints:
     - path: /metrics
       port: servicemonitor-ports
     jobLabel: servicemonitor-nginx
     # Application scope of a collection task. If this parameter is not set, the default value default is used.
     namespaceSelector:
       matchNames:
       - default
     selector:
       matchLabels:
         app: nginx-test3

After the application is successfully deployed, :ref:`access Prometheus <cce_10_0373__section1135613420551>` to query custom metrics. In the following, the endpoint name indicates the metrics are reported based on the ServiceMonitor configuration.

.. code-block::

   nginx_connections_accepted{cluster="2048c170-8359-11ee-9527-0255ac1000cf", cluster_category="CCE", cluster_name="cce-test", endpoint="servicemonitor-ports", instance="10.0.0.47:9113", job="nginx-test3", namespace="default", pod="nginx-test3-6f8bccd9-f27hv", prometheus="monitoring/server", service="nginx-test3"}

.. |image1| image:: /_static/images/en-us_image_0000001950316676.png
