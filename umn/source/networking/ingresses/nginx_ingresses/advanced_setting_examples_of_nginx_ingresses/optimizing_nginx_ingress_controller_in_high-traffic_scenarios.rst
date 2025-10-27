:original_name: cce_10_0919.html

.. _cce_10_0919:

Optimizing NGINX Ingress Controller in High-Traffic Scenarios
=============================================================

Ingress objects provide Layer 7 protocols like HTTP and HTTPS for clusters. Among the available options, Nginx ingresses are widely used. CCE has developed a featured open-source add-on called NGINX Ingress Controller, which is an enhanced version of the community solution. This add-on offers advanced Layer 7 load balancing features. In high-concurrency scenarios, the pre-allocated resources such as CPU and memory and network connections of the add-on may not be robust enough to handle the application needs. This can affect the application performance. This section describes how to optimize NGINX Ingress Controller in high-traffic scenarios.

.. caution::

   The optimization involves the rolling upgrade of the NGINX ingress container. You are advised to perform the optimization during off-peak hours.

Prerequisites
-------------

The NGINX Ingress Controller add-on has been installed in the target cluster.

Suggestions
-----------

The following provides some suggestions on optimizing the NGINX Ingress Controller add-on in high-traffic scenarios.

Using a High-Performance Node
-----------------------------

In high-concurrency scenarios, ingresses typically need a large number of CPUs and network connections, so you can select a computing-plus ECS to run the add-on instances.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **Nodes** in the navigation pane and click the **Node Pools** tab on the right.

#. Click **Create Node Pool** in the upper right corner to create a node pool and add two nodes to the node pool. The node pool can be dedicated for the NGINX Ingress Controller add-on. For details, see :ref:`Creating a Node Pool <cce_10_0012>`.

   For high-concurrency scenarios, you can select a high-performance ECS such as the general computing-plus c7.8xlarge.2 ECS, with 32 cores, 64 GiB of memory, a maximum bandwidth of 30 Gbit/s, and 5.5 million PPS.

#. In the **Advanced Settings** area, configure labels and taints for the node pool.

   -  Configure a taint and set its key to **nginx-ingress-pod-reserved**, value to **true**, and the effect to **NoExecute**.
   -  Configure a label and set its key to **nginx-ingress-pod-reserved** and value to **true**.

#. In the navigation pane, choose **Add-ons**, edit the configurations of NGINX Ingress Controller, and modify the scheduling policies.

   -  **Node Affinity**: Set it to **Customize affinity** and configure the add-on to be affinity for the **nginx-ingress-pod-reserved: true** label.
   -  **Toleration**: Add a toleration and configure the add-on to tolerate the taint whose label is **nginx-ingress-pod-reserved: true** and the taint policy is **NoExecute**.

Modifying the NGINX Ingress Controller Configuration
----------------------------------------------------

You can perform the following operations when installing or editing NGINX Ingress Controller:

-  Change the resource limit of the **nginx-ingress-controller** container. If an application processes HTTP requests with an RPS of 300,000, you can configure the CPU and memory limits, as well as the requested CPU and memory, to 16 CPU cores and 20 GiB of memory.
-  Set the number of pods to be greater than or equal to 2.

-  Disable the metric collection of the add-on.

   If you do not need to obtain metrics, you are advised to disable metric collection to reduce the CPU and memory usage of the Nginx container.

-  Optimize the global configuration by doing as follows:

   -  **Increase the maximum number of requests that a keepalive connection can handle.**

      Nginx provides the **keepalive_requests** parameter to control how many requests a single keepalive connection between a client and an upstream server can handle. This parameter defaults to 100.

      If the number of requests in a keepalive connection exceeds this limit, the connection will be disconnected and then re-established. For an ingress in a private network, a single client may have a high QPS, such as 10,000 QPS. This can cause Nginx to frequently disconnect keepalive connections from the client, resulting in a large number of connections in the TIME_WAIT state.

      To avoid this, it is recommended that you increase the maximum number of requests for a keepalive connection between Nginx and the client in high-concurrency scenarios. This can be achieved by setting **keep-alive-requests** in Nginx ingresses to **10000**. For details, see `keep-alive-requests <https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#keep-alive-requests>`__.

      You can also control the number of requests in a keepalive connection between the client and the upstream server by setting **upstream-keepalive-requests**. For details, see `upstream-keepalive-requests <https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#upstream-keepalive-requests>`__.

      .. caution::

         The **upstream-keepalive-requests** parameter is not needed in a non-high-concurrency scenario. If you set this parameter to a high value, it may cause a load imbalance. This is because when Nginx maintains a keepalive connection with the upstream for too long, the number of times connections scheduled will decrease, resulting in a traffic load imbalance.

   -  **Increase the maximum number of idle keepalive connections.**

      You can configure the **keepalive** parameter for the connections between Nginx and an upstream server. This parameter specifies the maximum number of idle connections and defaults to **320**.

      In a high-concurrency scenario, there are many requests and connections. However, in a real production environment, requests are not evenly distributed, and some connections may be temporarily idle. If the number of idle connections increases, they will be terminated. This can result in Nginx frequently disconnecting and reconnecting to the upstream server, leading to a significant increase in the TIME_WAIT connections. To avoid this, it is recommended that you set **keepalive** to **1000** in high-concurrency scenarios. For details, see `upstream-keepalive-connections <https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#upstream-keepalive-connections>`__.

   -  **Increase the maximum number of connections for a single worker.**

      The **max-worker-connections** parameter sets the maximum number of connections that each worker process can handle. For high-concurrency scenarios, it is recommended that you increase this value to, for instance, **65536**, to enable Nginx to manage more connections. For details, see `max-worker-connections <https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#max-worker-connections>`__.

   -  **Reduce the gateway timeout.**

      Nginx connects to an upstream pod via TCP and communicates with it, using three timeout parameter configurations.

      -  **proxy-connect-timeout**: specifies the timeout for establishing a connection between Nginx and an upstream pod. The default value for an Nginx ingress is 5 seconds. Nginx and services communicate with each other in the same data center over a private network, so the timeout can be reduced to, for example, 3 seconds. For details, see `proxy-connect-timeout <https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#proxy-connect-timeout>`__.
      -  **proxy-read-timeout** and **proxy-send-timeout**: control the timeout of read and write operations between Nginx and an upstream pod. The values for an Nginx ingress default to 60 seconds. However, if there are service exceptions that cause a sharp increase in response time, abnormal requests can occupy the ingress gateway for an extended period. To prevent this, it is recommended that, after the latency of all normal service requests reached P99.99, you reduce the read/write timeout between the gateway and upstream pod to a suitable value, such as 30 seconds. This allows Nginx to interrupt abnormal requests promptly and prevent prolonged breakdowns. For details, see `proxy-read-timeout <https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#proxy-read-timeout>`__ and `proxy-send-timeout <https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#proxy-send-timeout>`__.

Optimizing Nginx Kernel Parameters
----------------------------------

.. caution::

   Before modifying a kernel parameter, it is important to have a full understanding of its meaning and function. Exercise caution when making changes, as incorrect settings can cause unexpected system errors and instability.

   You need to make sure that:

   #. You have fully understood the functions and impacts of kernel parameters. This helps you set kernel parameters correctly.
   #. The parameter values you entered must be valid and meet the expectation, or the modification will not take effect.

You can optimize kernel parameters of Nginx ingresses and configure kernel parameters using **initContainers** by doing as follows:

By default, CCE enables kernel parameter tuning for the NGINX Ingress Controller add-on of 2.2.75, 2.6.26, 3.0.1, and later versions.

-  **Increase the size of the connection queue.**

   In a high-concurrency scenario, the connection queue may overflow if it is too small, resulting in the failure to establish some connections. The size of the connection queue for the process listener socket is determined by the **net.core.somaxconn** kernel parameter. By modifying this parameter, you can increase the size of the Nginx ingress connection queue.

   When a process uses the listen system to listen on ports, it passes in the **backlog** parameter, which sets the size of the socket connection queue. The value of **backlog** cannot exceed that of **somaxconn**. The Go program standard library uses the **somaxconn** value as the default queue size when listening. However, Nginx does not read **somaxconn** when listening on the socket. Instead, it reads **nginx.conf**. In the configuration items for listening ports in **nginx.conf**, you can set the **backlog** parameter to specify the connection queue size for Nginx listening port. The following shows an example configuration:

   .. code-block::

      server {
          listen  80  backlog=1024;
          ...

   If the value **backlog** is not specified, the default value **511** is used. By default, the maximum size of the connection queue for the Nginx listening port is 511, even if the value of **somaxconn** is greater than **511**. This can lead to connection queue overflow in high-concurrency scenarios.

   The NGINX Ingress Controller can automatically read and use the value of **somaxconn** as the **backlog** value, which is then written to the generated `nginx.conf <https://github.com/kubernetes/ingress-nginx/blob/controller-v0.34.1/internal/ingress/controller/nginx.go#L592>`__ file. This means that the connection queue size for an Nginx ingress is determined solely by **somaxconn**, and the default size in CCE is **4096**. In a high-concurrency scenario, it is recommended that you run the following command to set **somaxconn** to **65535**:

   .. code-block::

      sysctl -w net.core.somaxconn=65535

-  **Expand the range of source ports.**

   In a high-concurrency scenario, an Nginx ingress establishes connections with an upstream server using a large number of source ports. The range of source ports is randomly selected from the range defined in the **net.ipv4.ip_local_port_range** kernel parameter. In such scenarios, a small port range can quickly exhaust source ports, leading to abnormal connections. The default source port range for pods created in CCE is 32768 to 60999. To expand the range to 1024 to 65535, run the following command:

   .. code-block::

      sysctl -w net.ipv4.ip_local_port_range="1024 65535"

-  **Adjust TIME_WAIT.**

   You can enable TIME_WAIT reuse for Nginx ingresses, allowing TIME_WAIT connections to be reused for new TCP connections. Additionally, reducing the value of **net.ipv4.tcp_fin_timeout** in FIN_WAIT2 state and **net.netfilter.nf_conntrack_tcp_timeout_time_wait** in the TIME_WAIT state can help release resources occupied by them more quickly. To enable TIME_WAIT reuse, run the following commands:

   .. code-block::

      sysctl -w net.ipv4.tcp_fin_timeout=15
      sysctl -w net.netfilter.nf_conntrack_tcp_timeout_time_wait=30

Add **initContainers** to NGINX Ingress Controller pods and configure the preceding kernel parameters. The following shows an example:

.. code-block::

   ...
   initContainers:
     - name: setsysctl
       image: ***(By default, CCE uses the nginx-ingress image of the community.)
       securityContext:
         runAsUser: 0
         runAsGroup: 0
         capabilities:
           add:
             - SYS_ADMIN
           drop:
             - ALL
       command:
         - sh
         - -c
         - |
           if [ "$POD_IP" != "$HOST_IP" ]; then
              mount -o remount rw /proc/sys
              if [ $? -eq 0 ]; then
                sysctl -w net.core.somaxconn=65535
                sysctl -w net.ipv4.ip_local_port_range="1024 65535"
                sysctl -w net.ipv4.tcp_fin_timeout=15
                sysctl -w net.netfilter.nf_conntrack_tcp_timeout_time_wait=30
              else
                echo "Failed to remount /proc/sys as read-write. Skipping sysctl commands."
              fi
            fi
       env:
         - name: POD_IP
           valueFrom:
             fieldRef:
               apiVersion: v1
               fieldPath: status.podIP
         - name: HOST_IP
           valueFrom:
             fieldRef:
               apiVersion: v1
               fieldPath: status.hostIP
