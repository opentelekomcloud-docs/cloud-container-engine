:original_name: cce_10_0858.html

.. _cce_10_0858:

Redirecting Traffic from an Nginx Ingress to a LoadBalancer Ingress
===================================================================

.. caution::

   This section contains important information. Ignoring the information may affect your services.

Context
-------

The Kubernetes contributor community recently released a blog post `Ingress NGINX Retirement: What You Need to Know <https://kubernetes.io/blog/2025/11/11/ingress-nginx-retirement/>`__, in which SIG Network and the Security Response Committee jointly announced the upcoming retirement of Ingress NGINX.

Key points of this notice:

-  The project will be maintained until March 2026. Afterward, there will be no official support.
-  There will be no further releases.
-  There will be no function bug fixes and no updates to resolve any security vulnerabilities (including high-risk CVEs that may be discovered).
-  The GitHub repositories will be made read-only and left available for reference.
-  Existing deployments of Ingress NGINX will continue to function, and installation packages will remain available. However, no more updates to security vulnerabilities have caused a strong reaction among community and enterprise users. Although the official recommended that all Ingress NGINX users begin migration to Gateway API or any one of other 20+ active ingress controllers, most enterprises are concerned about the following issues:

   -  Can the new controller be compatible with the annotations of the Nginx ingress?
   -  How do I redirect traffic in grayscale mode without service interruptions?
   -  How do I roll back in seconds if traffic is abnormal?

CCE LoadBalancer ingresses can help you smoothly redirect traffic from Nginx ingresses. We provide a complete redirection solution that has been verified in production. This solution covers the annotation replacement table, grayscale traffic redirection, monitoring and alarms, and rollback in seconds, which can ensure no service interruptions and hitless rollout.

LoadBalancer Ingress Overview
-----------------------------

As a hosted ingress solution of CCE, LoadBalancer ingresses have many advantages over the community edition nginx-ingress in enterprise-class application scenarios. The core advantage is the hosted architecture. You do not need to deploy or maintain the controller. CCE is responsible for high-availability deployment, fault self-healing, and version iteration, greatly reducing O&M complexity and labor costs.

With the distributed architecture of ELB, this solution provides powerful performance and auto scaling, which can easily handle high-concurrency traffic. You can achieve automatic workload scale-out without manually optimizing configurations. This ensures service stability during peak hours. In addition, this solution is deeply integrated with services such as WAF, certificate hosting, and monitoring and logging. Security and compliance capabilities can be enabled with just one click, without extra development and integration, simplifying the process of implementing enterprise-level functions.

Its visualized pages and comprehensive fault diagnosis tools make using the Kubernetes ingress easier, and improve rule synchronization and troubleshooting efficiency. For enterprises' core services running on CCE, LoadBalancer ingresses are the optimal choice with cost-effectiveness, security, and reliability, as well as high performance, stability, and ease of use.

For details about the comparison between LoadBalancer ingresses and Nginx ingresses, see :ref:`Comparison Between LoadBalancer Ingresses and Nginx Ingresses <cce_10_0850>`.

Traffic Redirection Process
---------------------------

You can resolve the same domain name to the IP addresses of the load balancers used by an Nginx ingress and a LoadBalancer ingress and adjust the DNS records' weights of the two ingresses to redirect traffic. This redirection is transparent to the clients. The following figure shows the process of DNS-based traffic redirection.

.. note::

   DNS-based traffic redirection is not the only way to achieve transparent redirection. The examples in this section are for reference only.

|image1|

As the technology stacks are different, the annotations of Nginx ingresses need to be converted into the LoadBalancer ingress specifications for traffic redirection. For details, see :ref:`Table 1 <cce_10_0858__table1555102343311>`. For more LoadBalancer ingress configuration specifications, see :ref:`Configuring Advanced LoadBalancer Ingress Functions Using Annotations <cce_10_0695>`.

A LoadBalancer ingress supports 80% to 90% of Nginx rules, but does not support Nginx-specific functions, such as **nginx.ingress.kubernetes.io/auth-\*** (authentication required on the ELB console) or **nginx.ingress.kubernetes.io/configuration-snippet** (no equivalent ELB rule for custom Nginx configuration).

.. _cce_10_0858__table1555102343311:

.. table:: **Table 1** Comparison of common annotations

   +--------------------------------------------+----------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | Function                                   | Example Nginx Annotation                                                                     | Example of Equivalent LoadBalancer Annotation                                                                     |
   +============================================+==============================================================================================+===================================================================================================================+
   | **Specifying ingress class**               | kubernetes.io/ingress.class: "nginx"                                                         | spec.ingressClassName: "cce" or kubernetes.io/ingress.class: "cce" (v1.21 and earlier)                            |
   +--------------------------------------------+----------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | **Associating the load balancer ID**       | None (The load balancer is specified when the NGINX Ingress Controller add-on is installed.) | kubernetes.io/elb.id: "<elb-uuid>"                                                                                |
   +--------------------------------------------+----------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | **Load balancer type/specifications**      | None                                                                                         | kubernetes.io/elb.class: "union" (shared) or kubernetes.io/elb.class: "performance" (dedicated)                   |
   +--------------------------------------------+----------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | **Frontend port**                          | None                                                                                         | kubernetes.io/elb.port: "80"                                                                                      |
   +--------------------------------------------+----------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | **Automatically creating a load balancer** | None                                                                                         | kubernetes.io/elb.autocreate: '{"type":"inner", "name": "A-location-d-test"}'                                     |
   +--------------------------------------------+----------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | **Rewriting a URL**                        | nginx.ingress.kubernetes.io/rewrite-target: /$2                                              | kubernetes.io/elb.rewrite-target: "/$2"                                                                           |
   +--------------------------------------------+----------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | **Redirecting HTTP to HTTPS**              | nginx.ingress.kubernetes.io/ssl-redirect: "true"                                             | kubernetes.io/elb.ssl-redirect: "true"                                                                            |
   |                                            |                                                                                              |                                                                                                                   |
   |                                            |                                                                                              | kubernetes.io/elb.listen-ports: '[{"HTTP":80},{"HTTPS":443}]'                                                     |
   +--------------------------------------------+----------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | **IP address whitelist**                   | nginx.ingress.kubernetes.io/whitelist-source-range: "192.168.0.0/16"                         | kubernetes.io/elb.acl-id: '<acl-uid>'                                                                             |
   |                                            |                                                                                              |                                                                                                                   |
   |                                            |                                                                                              | kubernetes.io/elb.acl-status: 'on'                                                                                |
   |                                            |                                                                                              |                                                                                                                   |
   |                                            |                                                                                              | kubernetes.io/elb.acl-type: 'black'                                                                               |
   +--------------------------------------------+----------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | **CORS support**                           | nginx.ingress.kubernetes.io/enable-cors: "true"                                              | kubernetes.io/elb.cors-allow-origin: 'http://example.com'                                                         |
   |                                            |                                                                                              |                                                                                                                   |
   |                                            | nginx.ingress.kubernetes.io/cors-allow-origin: "``*``"                                       | kubernetes.io/elb.cors-allow-headers: 'fake-header-1'                                                             |
   |                                            |                                                                                              |                                                                                                                   |
   |                                            |                                                                                              | kubernetes.io/elb.cors-expose-headers: 'fake-header-2'                                                            |
   |                                            |                                                                                              |                                                                                                                   |
   |                                            |                                                                                              | kubernetes.io/elb.cors-allow-methods: 'GET,POST'                                                                  |
   |                                            |                                                                                              |                                                                                                                   |
   |                                            |                                                                                              | kubernetes.io/elb.cors-allow-credentials: 'true'                                                                  |
   |                                            |                                                                                              |                                                                                                                   |
   |                                            |                                                                                              | kubernetes.io/elb.cors-max-age: '3600'                                                                            |
   +--------------------------------------------+----------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | **TLS certificate**                        | spec.tls.secretName (TLS secret)                                                             | kubernetes.io/elb.tls-certificate-id: "<cert-id>" (load balancer certificate) or spec.tls.secretName (TLS secret) |
   +--------------------------------------------+----------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | **Grayscale release**                      | nginx.ingress.kubernetes.io/canary: "true"                                                   | kubernetes.io/elb.canary: "true"                                                                                  |
   |                                            |                                                                                              |                                                                                                                   |
   |                                            | nginx.ingress.kubernetes.io/canary-weight: "20"                                              | kubernetes.io/elb.canary-weight: "100"                                                                            |
   +--------------------------------------------+----------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | **Slow startup**                           | No direct support                                                                            | kubernetes.io/elb.slow-start: "30"                                                                                |
   +--------------------------------------------+----------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | **Tags**                                   | None                                                                                         | kubernetes.io/elb.tags: '{"key1":"value1","key2":"value2"}'                                                       |
   +--------------------------------------------+----------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | **HTTP/2**                                 | nginx.ingress.kubernetes.io/ssl-protocols: "TLSv1.2 TLSv1.3"                                 | kubernetes.io/elb.http2-enable: "true"                                                                            |
   +--------------------------------------------+----------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | **gRPC support**                           | nginx.ingress.kubernetes.io/backend-protocol: "GRPC"                                         | kubernetes.io/elb.pool-protocol: "grpc"                                                                           |
   +--------------------------------------------+----------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+

Example Scenario
----------------

Assume that your company is using a CCE cluster to run services. You have set up domain-based and URL-based forwarding rules for the ingress using NGINX Ingress Controller. The services are accessible externally through a domain name hosted on DNS. When a client requests access to the domain name example.com, the Nginx ingress forwards the request to the pods associated with the Service based on the forwarding rules created on the ingress.

|image2|

To develop services, you must transfer data from the Nginx ingress to a LoadBalancer ingress. To maintain service stability, you want to keep the DNS domain name and IP addresses of backend servers unchanged. To meet this requirement, you can configure LoadBalancer ingress rules in the CCE cluster to preserve the previous forwarding rules. Then, adjust the weights of the DNS records to redirect traffic from the Nginx ingress to the LoadBalancer ingress.

|image3|

After all traffic is redirected to the LoadBalancer ingress, you can delete the DNS record and resources of the Nginx ingress. This process is transparent to the client.

|image4|

.. caution::

   -  Redirect traffic during off-peak hours.
   -  Before redirecting traffic, compare the forwarding rules of the Nginx ingress and LoadBalancer ingress and conduct tests to ensure that the two ingresses have the same access functions. This prevents services from being affected during traffic redirection.

Step 1: Configure the Target LoadBalancer Ingress
-------------------------------------------------

#. Create the target LoadBalancer ingress. This example uses a basic LoadBalancer ingress that directs traffic to the original backend service, with no complex forwarding rules added. In actual services, you need to analyze the original Nginx ingress forwarding rules and add alternatives to the LoadBalancer ingress. For details, see :ref:`Table 1 <cce_10_0858__table1555102343311>`.

   In this example, an existing load balancer is used to create an ingress, and the **elbingress.yaml** is for reference only. You can also choose to automatically create a load balancer associated with the ingress. For more information, see :ref:`Creating a LoadBalancer Ingress Using kubectl <cce_10_0252>`.

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ingress-test
        annotations:
          kubernetes.io/elb.id: <your_elb_id>  # Replace it with the ID of your existing load balancer.
          kubernetes.io/elb.ip: <your_elb_ip>  # Replace it with the IP address of your existing load balancer.
          kubernetes.io/elb.class: performance  # Load balancer type. performance indicates a dedicated load balancer.
          kubernetes.io/elb.port: '80'
      spec:
        rules:
        - host: 'example.com'            # Replace it with your domain name.
          http:
            paths:
            - path: '/'
              backend:
                service:
                  name: <your_service_name>  # Replace it with the name of your target Service, which is the same as the Service associated with the original Nginx ingress.
                  port:
                    number: 8080             # Replace 8080 with the port number of your target Service.
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
              pathType: ImplementationSpecific
        ingressClassName: cce

#. Run the following command to create the ingress:

   .. code-block::

      kubectl apply -f elbingress.yaml

#. After the LoadBalancer ingress is created, **fully verify** this ingress to ensure that its access function is the same as that of the Nginx ingress.

Step 2: Gradually Redirect Traffic to the LoadBalancer Ingress
--------------------------------------------------------------

Before redirecting traffic, ensure you have created an A record to map the domain name to the public IP address of the load balancer used by the Nginx ingress. You can configure weighted DNS records to redirect traffic from the Nginx ingress to the LoadBalancer ingress.

To redirect traffic, do as follows:

#. Go to Public Zones.

#. On the **Public Zones** page, click the domain name (**example.com**) of the public zone.

#. The **Record Sets** page is displayed. You can find an A record, which contains the public IP address of the load balancer used by the Nginx ingress. Set the weight of this A record to **100**.

#. Add an A record set. To do so, click **Add Record Set**, and set **Value** to the public IP address of the load balancer used by the LoadBalancer ingress and **Weight** to **0**.

#. .. _cce_10_0858__li11651337503:

   Decrease the DNS record weight of the Nginx ingress (for example, to **90**) and increase the DNS record weight of the LoadBalancer ingress (for example, to **10**) without affecting any services.

#. .. _cce_10_0858__li12947455204214:

   Verify the traffic redirection effect and ensure that some traffic has been forwarded to the LoadBalancer ingress and there is no exception.

#. Repeat :ref:`5 <cce_10_0858__li11651337503>` to :ref:`6 <cce_10_0858__li12947455204214>` to gradually decrease the DNS record weight of the Nginx ingress to **0** and increase the DNS record weight of the LoadBalancer ingress to **100**.

Step 3: Delete Unnecessary Resources
------------------------------------

After all persistent connections to the Nginx ingress are released and there is no more traffic being forwarded to it, you can safely release any unnecessary resources after observing for a period of time.

#. Delete the DNS record of the Nginx ingress.
#. Delete the resources of the Nginx ingress from the cluster.
#. Uninstall NGINX Ingress Controller.
#. Delete the load balancer used by the Nginx ingress.

Helpful Links
-------------

-  For details about the comparison between LoadBalancer ingresses and Nginx ingresses, see :ref:`Comparison Between LoadBalancer Ingresses and Nginx Ingresses <cce_10_0850>`.
-  For more annotations supported by LoadBalancer ingresses, see :ref:`Configuring Advanced LoadBalancer Ingress Functions Using Annotations <cce_10_0695>`.

.. |image1| image:: /_static/images/en-us_image_0000002542806497.png
.. |image2| image:: /_static/images/en-us_image_0000002511366536.png
.. |image3| image:: /_static/images/en-us_image_0000002542806499.png
.. |image4| image:: /_static/images/en-us_image_0000002511366538.png
