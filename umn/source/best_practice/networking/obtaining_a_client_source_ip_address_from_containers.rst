:original_name: cce_bestpractice_00035.html

.. _cce_bestpractice_00035:

Obtaining a Client Source IP Address from Containers
====================================================

When using containers, clients may communicate with them through multiple proxy servers. However, this can cause issues with transferring the clients' source IP addresses to the containers' services. This section describes how to effectively obtain the client source IP address from a container based on different network solutions provided by CCE clusters.

Description
-----------

The method for obtaining client source IP addresses may vary with the network settings. The following shows some typical network configurations and their corresponding solutions:

-  **Ingress (Layer 7 forwarding)**: When accessing an application at Layer 7, the client's source IP address is automatically saved in the **X-Forwarded-For** field of the HTTP header. No additional configurations are needed to obtain the client's source IP address.
-  **Service (Layer 4 forwarding)**: The method and principle for obtaining source IP addresses depend on the type of Service being used.

   -  LoadBalancer Service: A load balancer is used as the traffic entry. Both shared and dedicated load balancers are supported.

      -  For a shared load balancer, you need to enable the function of obtaining client IP addresses on the listeners.
      -  By default, the function of obtaining client IP addresses is enabled for a dedicated load balancer listener.

   -  NodePort Service: Container ports are mapped to node ports, which are used as the entry for external services. The capability of obtaining client source IP addresses depends on the service affinity.

      -  For a NodePort Service with a cluster-level service affinity, traffic is forwarded within the cluster, which means the backend containers of the Service cannot access the client source IP address.
      -  For a NodePort Service with node-level service affinity, the traffic can directly reach the container without any forwarding. This allows the backend containers of the Service to obtain the client source IP address.

LoadBalancer Ingresses
----------------------

For the LoadBalancer ingresses (using HTTP- or HTTPS-compliant), the function of obtaining the client source IP addresses is enabled by default. No other operation is required.

A client source IP address is placed in the **X-Forwarded-For** field of the HTTP header by the load balancer. The format is as follows:

.. code-block::

   X-Forwarded-For: <client-source-IP-address>, <proxy-server-1-IP-address>, <proxy-server-2-IP-address>, ...

The first IP address obtained from the **X-Forwarded-For** field is the client source IP address.

Nginx Ingresses
---------------

-  For an Nginx ingress that uses a dedicated load balancer, transparent transmission of source IP addresses is enabled by default. This means that you can easily obtain the client source IP address without any additional configurations.
-  For an Nginx ingress that uses a shared load balancer, take the following steps to obtain the client source IP address:

#. Take the Nginx workload as an example. Before configuring the source IP address, obtain the access logs. **nginx-c99fd67bb-ghv4q** indicates the pod name.

   .. code-block::

      kubectl logs nginx-c99fd67bb-ghv4q

   Information similar to the following is displayed:

   .. code-block::

      ...
      10.0.0.7 - - [17/Aug/2023:01:30:11 +0000] "GET / HTTP/1.1" 200 19 "http://114.114.114.114:9421/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.0.0 Safari/537.36 Edg/115.0.1901.203" "100.125.**.**"

   **100.125.**.*\*** specifies the CIDR block of the load balancer, indicating that the traffic is forwarded through the load balancer.

#. Enable **Transfer Client IP Address**. **This operation is required only when shared load balancers are used. For dedicated load balancers, source IP address-based transparent transmission is enabled by default.**

   a. Click |image1| in the upper left corner of the management console and select a region and a project.
   b. Choose **Service List** > **Networking** > **Elastic Load Balance**.
   c. On the **Elastic Load Balance** page, click the name of the load balancer.
   d. Click the **Listeners** tab, locate the row containing the listener, and click **Edit**. If modification protection exists, disable the protection on the basic information page of the listener and try again.
   e. Enable **Transfer Client IP Address**.

#. Access the workload again and view the new access logs.

   .. code-block::

      ...
      10.0.0.7 - - [17/Aug/2023:02:43:11 +0000] "GET / HTTP/1.1" 304 0 "http://114.114.114.114:9421/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.0.0 Safari/537.36 Edg/115.0.1901.203" "124.**.**.**"

   The client source IP address has been obtained.

.. note::

   You can enable WAF for the load balancers used by an NGINX Ingress Controller in a cluster, but different WAF modes will affect how the NGINX Ingress Controller obtains the client IP addresses.

   -  **Load balancer access in WAF cloud CNAME access mode**

      When using the cloud CNAME access mode, requests go through WAF and are checked for protection before being sent to the load balancer. This means that even if a load balancer has transparent transmission of source IP addresses enabled, the client will receive the back-to-source IP address of WAF. Consequently, the NGINX Ingress Controller is unable to obtain the client IP addresses by default. In this case, you can edit the NGINX Ingress Controller add-on and add the following configuration to the add-on parameters:

      .. code-block::

         {
              "enable-real-ip": "true",
              "use-forwarded-headers": "true",
              "proxy-real-ip-cidr": <Back-to-source IP address you obtained from WAF>
         }

   -  **Load balancer access in the cloud WAF mode**

      This mode is transparent (non-inline deployment) and supports only dedicated load balancers. In this mode, NGINX Ingress Controller can obtain the client IP addresses by default.

LoadBalancer Services
---------------------

In LoadBalancer Services, the ability to obtain the client IP addresses varies across different types of clusters, and in some scenarios, this ability is not supported.

-  CCE standard clusters using the VPC or tunnel networks: Client IP addresses can be obtained when either a shared or dedicated load balancer is used.
-  CCE Turbo clusters using the Cloud Native 2.0 networks: Client IP addresses can be obtained when dedicated load balancers and shared load balancers with hostNetwork enabled are used.

**VPC and Tunnel Networks**

To enable the function of obtaining the client IP address on the console, take the following steps:

#. When creating a LoadBalancer Service on the CCE console, set **Service Affinity** to **Node-level** instead of **Cluster-level**.
#. Go to the ELB console and enable the function of obtaining the client IP addresses of the load balancer listener. **Transparent transmission of client IP addresses is enabled for dedicated load balancers by default. You do not need to manually enable this function.**

   a. Click |image2| in the upper left corner of the management console and select a region and a project.
   b. Choose **Service List** > **Networking** > **Elastic Load Balance**.
   c. On the **Elastic Load Balance** page, click the name of the load balancer.
   d. Click the **Listeners** tab, locate the row containing the listener, and click **Edit**. If modification protection exists, disable the protection on the basic information page of the listener and try again.
   e. Enable **Transfer Client IP Address**.

**Cloud Native Network 2.0 (CCE Turbo Clusters)**

When a LoadBalancer Service associated with a shared load balancer is created:

-  For workloads with hostNetwork enabled, you can set **Service Affinity** to **Node-level** to obtain the client IP addresses.
-  For other workloads, you cannot set **Service Affinity** to **Node-level**, so the client IP addresses cannot be obtained.

**Dedicated load balancers** are recommended. External requests can be directly sent to containers. By default, transparent transmission of client IP addresses is enabled for dedicated load balancers. You do not need to manually enable **Transfer Client IP Address** on the ELB console. Instead, you only need to select a dedicated load balancer when creating a LoadBalancer Service on the CCE console.

NodePort Services
-----------------

Set the service affinity of a NodePort Service to **Node-level** instead of **Cluster-level**. That is, set **spec.externalTrafficPolicy** of the Service to **Local**.

.. note::

   In clusters using the Cloud Native 2.0 networks, if NodePort Services are used, only workloads with hostNetwork enabled support node-level service affinity. Therefore, only such workloads can obtain client IP addresses.

.. |image1| image:: /_static/images/en-us_image_0000002631531244.png
.. |image2| image:: /_static/images/en-us_image_0000002631691146.png
