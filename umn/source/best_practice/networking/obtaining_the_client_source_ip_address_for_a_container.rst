:original_name: cce_bestpractice_00035.html

.. _cce_bestpractice_00035:

Obtaining the Client Source IP Address for a Container
======================================================

In containers, multiple types of proxy servers may exist between a client and the container servers. After an external request is forwarded for multiple times, the source IP address of the client cannot be transmitted to the containers. As a result, Services in the containers cannot obtain the real source IP addresses of the client.

Description
-----------

**Layer-7 forwarding:**

Ingresses: If this access mode is used, the client's source IP address is saved in the **X-Forwarded-For** field of the HTTP header by default. No other configuration is required.

-  The LoadBalancer Ingresses use ELB for Layer 7 network access between the Internet and internal network (in the same VPC) based on the ELB service.

**Layer-4 forwarding:**

-  LoadBalancer: Use ELB to achieve load balancing. You can manually enable the **Transfer Client IP Address** option for TCP and UDP listeners of shared load balancers. By default, the **Transfer Client IP Address** option is enabled for TCP and UDP listeners of dedicated load balancers. You do not need to manually enable it.
-  NodePort: The container port is mapped to the node port. If the cluster-level affinity is selected, access requests will be forwarded through the node and the client source IP address cannot be obtained. If the node-level affinity is selected, access requests will not be forwarded and the client source IP address can be obtained.

ELB Ingress
-----------

For the ELB Ingresses (using HTTP- or HTTPS-compliant), the function of obtaining the source IP addresses of the client is enabled by default. No other operation is required.

The real IP address is placed in the **X-Forwarded-For** HTTP header field by the load balancer in the following format:

.. code-block::

   X-Forwarded-For: IP address of the client,Proxy server 1-IP address,Proxy server 2-IP address,...

If you use this method, the first IP address obtained is the IP address of the client.

#. Take the Nginx workload as an example. Before configuring the source IP address, obtain the access logs. **nginx-c99fd67bb-ghv4q** indicates the pod name.

   .. code-block::

      kubectl logs nginx-c99fd67bb-ghv4q

   Information similar to the following is displayed:

   .. code-block::

      ...
      10.0.0.7 - - [17/Aug/2023:01:30:11 +0000] "GET / HTTP/1.1" 200 19 "http://114.114.114.114:9421/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.0.0 Safari/537.36 Edg/115.0.1901.203" "100.125.**.**"

   **100.125.**.*\*** specifies the CIDR block of the load balancer, indicating that the traffic is forwarded through the load balancer.

#. Go to the ELB console and enable the function of obtaining the client IP address of the listener corresponding to the load balancer. **Transparent transmission of source IP addresses is enabled for dedicated load balancers by default. You do not need to manually enable this function.**

   a. Log in to the ELB console.
   b. Click |image1| in the upper left corner of the management console and select a region and a project.
   c. Click **Service List**. Under **Networking**, click **Elastic Load Balance**.
   d. On the **Load Balancers** page, click the name of the load balancer.
   e. Click the **Listeners** tab, locate the row containing the target listener, and click **Edit**. If modification protection exists, disable the protection on the basic information page of the listener and try again.
   f. Enable **Transfer Client IP Address**.

#. (Perform this step only for Nginx ingresses.) Edit the nginx-ingress add-on. In the nginx configuration parameter area, configure the configuration fields and information. For details about the parameter range, see `community document <https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/>`__. After the configuration is complete, update the add-on.

   .. code-block::

      {
          "enable-real-ip": "true",
          "forwarded-for-header": "X-Forwarded-For",
          "proxy-real-ip-cidr": "100.125.0.0/16",
          "keep-alive-requests": "100"
      }

   .. note::

      The **proxy-real-ip-cidr** parameter indicates the CIDR block of the proxy server.

      -  For shared load balancers, add CIDR block 100.125.0.0/16 (reserved only for load balancers and therefore, there is no risk) and the high-defense CIDR block.

      -  For dedicated load balancers, add the CIDR block of the VPC subnet where the ELB resides.

      For details, see `How Can I Transfer the IP Address of a Client? <https://docs.otc.t-systems.com/usermanual/elb/elb_faq_0090.html>`__

#. Access the workload again and view the new access log.

   .. code-block::

      ...
      10.0.0.7 - - [17/Aug/2023:02:43:11 +0000] "GET / HTTP/1.1" 304 0 "http://114.114.114.114:9421/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.0.0 Safari/537.36 Edg/115.0.1901.203" "124.**.**.**"

   The source IP address of the client is obtained.

LoadBalancer
------------

For a LoadBalancer Service, different types of clusters obtain source IP addresses in different scenarios. In some scenarios, source IP addresses cannot be obtained currently.

-  CCE Clusters (using VPC or Tunnel network): Source IP addresses can be obtained when either a shared or dedicated load balancer is used.
-  CCE Turbo Clusters (using the Cloud Native Network 2.0): Source IP addresses can be obtained for dedicated load balancers, and for shared load balancers with hostNetwork enabled.

**VPC and Container Tunnel Network Models**

To obtain source IP addresses, perform the following steps:

#. When creating a LoadBalancer Service on the CCE console, set **Service Affinity** to **Node level** instead of **Cluster level**.
#. Go to the ELB console and enable the function of obtaining the client IP address of the listener corresponding to the load balancer. **Transparent transmission of source IP addresses is enabled for dedicated load balancers by default. You do not need to manually enable this function.**

   a. Log in to the ELB console.
   b. Click |image2| in the upper left corner of the management console and select a region and a project.
   c. Click **Service List**. Under **Networking**, click **Elastic Load Balance**.
   d. On the **Load Balancers** page, click the name of the load balancer.
   e. Click the **Listeners** tab, locate the row containing the target listener, and click **Edit**. If modification protection exists, disable the protection on the basic information page of the listener and try again.
   f. Enable **Transfer Client IP Address**.

**Cloud Native Network 2.0 Model (CCE Turbo Clusters)**

In the Cloud Native Network 2.0 model, when a shared load balancer is used for load balancing, the service affinity cannot be set to **Node level**. As a result, source IP addresses cannot be obtained. To obtain a source IP address, you must use a **dedicated load balancer**. External access to the container does not need to pass through the forwarding plane.

By default, transparent transmission of source IP addresses is enabled for dedicated load balancers. You do not need to manually enable **Transfer Client IP Address** on the ELB console. Instead, you only need to select a dedicated load balancer when creating an ENI LoadBalancer Service on the CCE console.

NodePort
--------

Set the service affinity of a NodePort Service to **Node level** instead of **Cluster level**. That is, set **spec.externalTrafficPolicy** of the Service to **Local**.

.. note::

   When a node (using Cloud Native Network 2.0) accesses a NodePort Service, source IP addresses can be obtained only when hostNetwork is enabled for workloads.

.. |image1| image:: /_static/images/en-us_image_0000001797908741.png
.. |image2| image:: /_static/images/en-us_image_0000001797869741.png
