:original_name: cce_bestpractice_00035.html

.. _cce_bestpractice_00035:

Obtaining the Client Source IP Address for a Container
======================================================

Background
----------

There may be different types of proxy servers between a client and a container server. How can a container obtain the real source IP address of the client? This section describes several scenarios you may encounter.

Principles
----------

|image1|

**Layer-7 forwarding:**

Ingress: If this access mode is used, the client source IP address is saved in the **X-Forwarded-For** HTTP header field by default. No other configuration is required.

-  ELB ingress: A self-developed ingress to implement layer-7 network access between the internet and intranet (in the same VPC) based on ELB. If the backend Service type is **NodePort**, set **Service Affinity** to **Node level**.

**Layer-4 forwarding:**

-  LoadBalancer: Use ELB to achieve load balancing. You can manually enable the **Obtain Client IP Address** option for TCP and UDP listeners of shared load balancers. By default, the **Obtain Client IP Address** option is enabled for TCP and UDP listeners of dedicated load balancers. You do not need to manually enable it.
-  NodePort: In this access mode, the container port is mapped to the node port. If cluster-level affinity is configured, access requests will be forwarded through the node and the client source IP address cannot be obtained. If node-level affinity is configured, access requests are not forwarded and the client source IP address can be obtained.

Ingress
-------

Configure the application server and obtain the IP address of a client from the HTTP header.

The real IP address is placed in the **X-Forwarded-For** HTTP header field by the load balancer in the following format:

.. code-block::

   X-Forwarded-For: IP address of the client,Proxy server 1-IP address,Proxy server 2-IP address,...

If you use this method, the first IP address obtained is the IP address of the client.

For details, see `How Can I Obtain the IP Address of a Client? <https://docs.otc.t-systems.com/usermanual/elb/elb_faq_0090.html>`__

.. note::

   -  When adding an ingress, if the backend service is of the NodePort type, set **Service Affinity** to **Node level**, that is, set **spec.externalTrafficPolicy** to **Local**. For details, see :ref:`NodePort <cce_bestpractice_00035__section6340152911914>`.

LoadBalancer
------------

For a LoadBalancer Service, different types of clusters obtain source IP addresses in different scenarios. In some scenarios, source IP addresses cannot be obtained currently.

**VPC and Container Tunnel Network Models**

To obtain source IP addresses, perform the following steps:

#. When creating a LoadBalancer Service on the CCE console, set **Service Affinity** to **Node level** instead of **Cluster level**.
#. Go to the ELB console and enable the function of obtaining the client IP address of the listener corresponding to the load balancer. **Transparent transmission of source IP addresses is enabled for dedicated load balancers by default. You do not need to manually enable this function.**

   a. Log in to the ELB console.
   b. Click |image2| in the upper left corner to select the desired region and project.
   c. Click **Service List**. Under **Networking**, click **Elastic Load Balance**.
   d. On the **Load Balancers** page, click the name of the load balancer.
   e. Click **Listeners**.

      -  To add a listener, click **Add Listener**.
      -  To modify a listener, locate the listener and click the edit button on the right of its name.

   f. Enable **Obtain Client IP Address**.

.. _cce_bestpractice_00035__section6340152911914:

NodePort
--------

Set the service affinity of a NodePort Service to **Node level** instead of **Cluster level**. That is, set **spec.externalTrafficPolicy** of the Service to **Local**.

.. |image1| image:: /_static/images/en-us_image_0000001176818150.png
.. |image2| image:: /_static/images/en-us_image_0000001221501677.png
