:original_name: cce_10_0857.html

.. _cce_10_0857:

Nginx Ingress Usage Suggestions
===============================

Nginx ingresses use NGINX Ingress Controller (:ref:`NGINX Ingress Controller <cce_10_0034>`) in clusters to balance load and control access for traffic. The stability of NGINX Ingress Controller, which is deployed in clusters and uses open-source community charts and images, is closely tied to the configurations used and the current status of the cluster. This section provides practical tips on how to use NGINX Ingress Controller effectively. You can refer to the following configurations for the best results.

Optimized NGINX Ingress Controller Settings
-------------------------------------------

-  **Configure a proper number of replicas and resource limits.**

   The default number of pods for NGINX Ingress Controller installed through Settings is 2, but you can modify it according to your service needs.

   When NGINX Ingress Controller is deployed, multiple pods will be allocated to different AZs or different nodes in the same AZ by default.

   Additionally, configure proper resource limits for NGINX Ingress Controller to prevent traffic interruption caused by OOM. It is a good practice to set the CPU limit to 1000m or higher and the memory limit to 2 GiB or higher.

-  **Deploy NGINX Ingress Controller on a dedicated node for better performance and stability of Nginx Ingress.**

   When creating a node, configure taints on it. During the installation of NGINX Ingress Controller, add the taint of the node to the tolerance policy.

-  **Optimize the Nginx ingress performance.**

   NGINX Ingress Controller performance optimization involves system parameter optimization and Nginx parameter optimization.

   -  System parameter optimization: Some common OS parameters have been optimized by default to improve system performance. However, you should also optimize other parameters like connection queue size and source port range for better results. The optimized system parameters ensure that Nginx can handle a high volume of concurrent requests and access the backend without running out of ports.
   -  Nginx parameter optimization

      -  **In a high-concurrency environment**, increase the maximum number of keepalive connections between Nginx and the client. This helps to avoid generating a large number of TIME_WAIT connections.
      -  Increase the maximum number of connections to a single worker node for NGINX Ingress Controller to handle a high volume of concurrent requests.
      -  **In a high-concurrency environment**, you are advised to set the maximum number of idle keepalive connections to 1000.
      -  Configure gateway timeout. Make sure that the persistent connection timeout period of backend services is greater than or equal to the connection timeout period of NGINX Ingress Controller.

-  **Configure HPA for NGINX Ingress Controller.**

   NGINX Ingress Controller can typically handle bursts of service traffic. However, if you need additional capacity in heavy load scenarios, you can configure HPA to automatically expand the capacity for NGINX Ingress Controller. For details, see :ref:`Creating an HPA Policy <cce_10_0208>`.

-  **Configure a proper preStop hook for backend services.**

   During the rolling update of a backend service, NGINX Ingress Controller will remove the pods that are being terminated from the backend server, but will retain the connections for requests that are still being processed. If the backend service pods exit immediately after receiving the end signal, the in-progress requests may fail or some traffic may be forwarded to pods that have already exited, leading to traffic loss. To prevent this issue, configure a preStop hook in the backend service pods. This will allow the pods to continue working for a period of time after being removed.

   The following shows a workload configuration example:

   .. code-block::

      ...
      spec:
        template:
          spec:
            containers:
            - name: app
              lifecycle:
                # Configure a preStop hook for pods to exit after 30 seconds.
                # The sleep command must exist in the container.
                preStop:
                  exec:
                    command:
                      - sleep
                      - '30'

Observability of NGINX Ingress Controller
-----------------------------------------

With CCE's :ref:`Cloud Native Cluster Monitoring <cce_10_0406>`, you can easily monitor NGINX Ingress Controller and gain valuable insights into the status of your ingress traffic.

Advanced Functions of NGINX Ingress Controller
----------------------------------------------

-  **Multiple NGINX Ingress Controllers allowed**

   Deploying multiple NGINX Ingress Controllers in a cluster is a great way to isolate your application's internal network from the external network. For details, see :ref:`Installing Multiple NGINX Ingress Controllers <cce_10_0034__section1057051834311>`.

-  **Blue-green deployment or grayscale release of applications through NGINX Ingress Controller**

-  **Interconnecting an Nginx ingress with different backend services**

   By default, an Nginx ingress accesses backend services using HTTP.

   -  HTTPS: For details, see :ref:`Configuring HTTPS Backend Services for an Nginx Ingress <cce_10_0697>`.
