:original_name: cce_10_0201.html

.. _cce_10_0201:

Monitoring Custom Metrics on AOM
================================

CCE allows you to upload custom metrics to AOM. ICAgent on a node periodically calls the metric monitoring API configured on a workload to read monitoring data and then uploads the data to AOM.


.. figure:: /_static/images/en-us_image_0000001981436085.png
   :alt: **Figure 1** Using ICAgent to collect monitoring metrics

   **Figure 1** Using ICAgent to collect monitoring metrics

The custom metric API of a workload can be configured when the workload is created. The following procedure uses an Nginx application as an example to describe how to report custom metrics to AOM.

#. :ref:`Preparing an Application <cce_10_0201__section14984815298>`

   Prepare an application image. The application must provide a metric monitoring API for ICAgent to collect data, and the monitoring data must :ref:`comply with the Prometheus specifications <cce_10_0201__section173671127160>`.

#. :ref:`Deploying Applications and Converting Nginx Metrics <cce_10_0201__section1539954011362>`

   Use the application image to deploy a workload in a cluster. Custom metrics are automatically reported.

#. :ref:`Verification <cce_10_0201__section42551081185>`

   Go to AOM to check whether the custom metrics are successfully collected.

Constraints
-----------

-  The ICAgent is compatible with the monitoring data specifications of `Prometheus <https://prometheus.io/>`__. The custom metrics provided by pods can be collected by the ICAgent only when they meet the monitoring data specifications of Prometheus. For details, see :ref:`Prometheus Monitoring Data Collection <cce_10_0201__section173671127160>`.
-  The ICAgent supports only `Gauge <https://prometheus.io/docs/concepts/metric_types/>`__ metrics.
-  The interval for the ICAgent to call the custom metric API is 1 minute, which cannot be changed.

.. _cce_10_0201__section173671127160:

Prometheus Monitoring Data Collection
-------------------------------------

Prometheus periodically calls the metric monitoring API (**/metrics** by default) of an application to obtain monitoring data. The application needs to provide the metric monitoring API for Prometheus to call, and the monitoring data must meet the following specifications of Prometheus:

.. code-block::

   # TYPE nginx_connections_active gauge
   nginx_connections_active 2
   # TYPE nginx_connections_reading gauge
   nginx_connections_reading 0

Prometheus provides clients in various languages. For details about the clients, see `Prometheus CLIENT LIBRARIES <https://prometheus.io/docs/instrumenting/clientlibs/>`__. For details about how to develop an exporter, see `WRITING EXPORTERS <https://prometheus.io/docs/instrumenting/writing_exporters/>`__. The Prometheus community provides various third-party exporters that can be directly used. For details, see `EXPORTERS AND INTEGRATIONS <https://prometheus.io/docs/instrumenting/exporters/>`__.

.. _cce_10_0201__section14984815298:

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

.. _cce_10_0201__section1539954011362:

Deploying Applications and Converting Nginx Metrics
---------------------------------------------------

The format of the monitoring data provided by **nginx:exporter** does not meet the requirements of Prometheus. Convert the data format to the format required by Prometheus. To convert the format of Nginx metrics, use `nginx-prometheus-exporter <https://github.com/nginxinc/nginx-prometheus-exporter>`__, as shown in the following figure.


.. figure:: /_static/images/en-us_image_0000001981436089.png
   :alt: **Figure 2** Using exporter to convert the data format

   **Figure 2** Using exporter to convert the data format

Deploy **nginx:exporter** and **nginx-prometheus-exporter** in the same pod.

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
           metrics.alpha.kubernetes.io/custom-endpoints: '[{"api":"prometheus","path":"/metrics","port":"9113","names":""}]'
       spec:
         containers:
           - name: container-0
             image: 'nginx:exporter'  # Replace it with the address of the image you uploaded to SWR.
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

.. note::

   The **nginx/nginx-prometheus-exporter:0.9.0** image needs to be pulled from the public network. Therefore, a public IP address needs to be bound to each node in the cluster.

nginx-prometheus-exporter requires a startup command. **nginx-prometheus-exporter -nginx.scrape-uri=http://127.0.0.1:8080/stub_status** is used to obtain Nginx monitoring data.

In addition, add an annotation **metrics.alpha.kubernetes.io/custom-endpoints: '[{"api":"prometheus","path":"/metrics","port":"9113","names":""}]'** to the pod.

.. _cce_10_0201__section42551081185:

Verification
------------

After an application is deployed, you can access Nginx to construct some access data and check whether the corresponding monitoring data can be obtained in AOM.

#. Obtain the pod name of Nginx.

   .. code-block::

      $ kubectl get pod
      NAME                              READY   STATUS    RESTARTS   AGE
      nginx-exporter-78859765db-6j8sw   2/2     Running   0          4m

#. Log in to the container and run commands to access Nginx.

   .. code-block::

      $ kubectl exec -it nginx-exporter-78859765db-6j8sw -- /bin/sh
      Defaulting container name to container-0.
      Use 'kubectl describe pod/nginx-exporter-78859765db-6j8sw -n default' to see all of the containers in this pod.
      / # curl http://localhost
      <!DOCTYPE html>
      <html>
      <head>
      <title>Welcome to nginx!</title>
      <style>
      html { color-scheme: light dark; }
      body { width: 35em; margin: 0 auto;
      font-family: Tahoma, Verdana, Arial, sans-serif; }
      </style>
      </head>
      <body>
      <h1>Welcome to nginx!</h1>
      <p>If you see this page, the nginx web server is successfully installed and
      working. Further configuration is required.</p>

      <p>For online documentation and support please refer to
      <a href="http://nginx.org/">nginx.org</a>.<br/>
      Commercial support is available at
      <a href="http://nginx.com/">nginx.com</a>.</p>

      <p><em>Thank you for using nginx.</em></p>
      </body>
      </html>
      / #

#. Log in to AOM. In the navigation pane, choose **Monitoring** > **Metric Monitoring** to view Nginx-related metrics, for example, **nginx_connections_active**.

.. |image1| image:: /_static/images/en-us_image_0000001950316676.png
