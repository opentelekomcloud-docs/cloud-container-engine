:original_name: cce_bestpractice_00035.html

.. _cce_bestpractice_00035:

Obtaining the Client Source IP Address for a Container
======================================================

Background
----------

There may be different types of proxy servers between a client and a container server. How can a container obtain the real source IP address of the client? This section describes several scenarios you may encounter.

Principles
----------

**Layer-7 forwarding:**

Ingress: If this access mode is used, the client source IP address is saved in the **X-Forwarded-For** HTTP header field by default. No other configuration is required.

-  ELB ingress: A self-developed ingress to implement layer-7 network access between the internet and intranet (in the same VPC) based on ELB. If the backend Service type is **NodePort**, set **Service Affinity** to **Node level**.

**Layer-4 forwarding:**

-  NodePort: In this access mode, the container port is mapped to the node port. If cluster-level affinity is configured, access requests will be forwarded through the node and the client source IP address cannot be obtained. If node-level affinity is configured, access requests are not forwarded and the client source IP address can be obtained.

Ingress
-------

Configure the application server and obtain the IP address of a client from the HTTP header.

The real IP address is placed in the **X-Forwarded-For** HTTP header field by the load balancer in the following format:

.. code-block::

   X-Forwarded-For: IP address of the client,Proxy server 1-IP address,Proxy server 2-IP address,...

If you use this method, the first IP address obtained is the IP address of the client.

.. note::

   -  When adding an ingress, if the backend service is of the NodePort type, set **Service Affinity** to **Node level**, that is, set **spec.externalTrafficPolicy** to **Local**. For details, see :ref:`NodePort <cce_bestpractice_00035__section6340152911914>`.

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
   e. Switch to the **Listeners** tab and click **Modify** on the right of the target listener. If modification protection exists, disable the protection on the basic information page of the listener and try again.
   f. Enable **Obtain Client IP Address**.

#. (Perform this step only for nginx-ingress.) Edit the nginx-ingress add-on. In the nginx configuration parameter area, configure the configuration fields and information. (For details about the parameter range, see `community document <https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/>`__.) After the configuration is complete, update the add-on.

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

.. _cce_bestpractice_00035__section6340152911914:

NodePort
--------

Set the service affinity of a NodePort Service to **Node level** instead of **Cluster level**. That is, set **spec.externalTrafficPolicy** of the Service to **Local**.

.. |image1| image:: /_static/images/en-us_image_0000001701784809.png
