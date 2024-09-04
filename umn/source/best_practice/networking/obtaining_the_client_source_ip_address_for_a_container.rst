:original_name: cce_bestpractice_00035.html

.. _cce_bestpractice_00035:

Obtaining the Client Source IP Address for a Container
======================================================

In containers, multiple types of proxy servers may exist between a client and the container servers. After an external request is forwarded for multiple times, the source IP address of the client cannot be transmitted to the containers. As a result, Services in the containers cannot obtain the real source IP addresses of the client.

Description
-----------

**Layer-7 forwarding:**

Ingresses: If this access mode is used, the client's source IP address is saved in the **X-Forwarded-For** field of the HTTP header by default. No other configuration is required.

-  LoadBalancer Ingresses use ELB for Layer 7 network access between the Internet and internal network (in the same VPC) based on the ELB service.

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

LoadBalancer
------------

For a LoadBalancer Service, different types of clusters obtain source IP addresses in different scenarios. In some scenarios, source IP addresses cannot be obtained currently.

-  CCE Clusters (using VPC or Tunnel network): Source IP addresses can be obtained when either a shared or dedicated load balancer is used.
-  CCE Turbo Clusters (using the Cloud Native Network 2.0): Source IP addresses can be obtained for dedicated load balancers, and for shared load balancers with hostNetwork enabled.

**VPC and Container Tunnel Network Models**

To enable the function of obtaining the source IP address on the console, perform the following steps:

#. When creating a LoadBalancer Service on the CCE console, set **Service Affinity** to **Node-level** instead of **Cluster-level**.
#. Go to the ELB console and enable the function of obtaining the client IP address of the listener corresponding to the load balancer. **Transparent transmission of source IP addresses is enabled for dedicated load balancers by default. You do not need to manually enable this function.**

   a. Log in to the ELB console.
   b. Click |image1| in the upper left corner of the management console and select a region and a project.
   c. Click **Service List**. Under **Networking**, click **Elastic Load Balance**.
   d. On the **Load Balancers** page, click the name of the load balancer.
   e. Click the **Listeners** tab, locate the row containing the target listener, and click **Edit**. If modification protection exists, disable the protection on the basic information page of the listener and try again.
   f. Enable **Transfer Client IP Address**.

**Cloud Native Network 2.0 Model (CCE Turbo Clusters)**

In the Cloud Native Network 2.0 model, when a shared load balancer is used for load balancing, the service affinity cannot be set to **Node-level**. As a result, source IP addresses cannot be obtained. To obtain a source IP address, you must use a **dedicated load balancer**. External access to the container does not need to pass through the forwarding plane.

By default, transparent transmission of source IP addresses is enabled for dedicated load balancers. You do not need to manually enable **Transfer Client IP Address** on the ELB console. Instead, you only need to select a dedicated load balancer when creating a LoadBalancer Service on the CCE console.

NodePort
--------

Set the service affinity of a NodePort Service to **Node-level** instead of **Cluster-level**. That is, set **spec.externalTrafficPolicy** of the Service to **Local**.

.. note::

   When a node (using Cloud Native Network 2.0) accesses a NodePort Service, source IP addresses can be obtained only when hostNetwork is enabled for workloads.

.. |image1| image:: /_static/images/en-us_image_0000001950314864.png
