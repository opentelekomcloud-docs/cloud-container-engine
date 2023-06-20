:original_name: cce_10_0251.html

.. _cce_10_0251:

Using ELB Ingresses on the Console
==================================

Prerequisites
-------------

-  An ingress provides network access for backend workloads. Ensure that a workload is available in a cluster. If no workload is available, deploy a workload by referring to :ref:`Creating a Deployment <cce_10_0047>`, :ref:`Creating a StatefulSet <cce_10_0048>`, or :ref:`Creating a DaemonSet <cce_10_0216>`.
-  A NodePort Service has been configured for the workload. For details about how to configure the Service, see :ref:`NodePort <cce_10_0142>`.
-  Dedicated load balancers must be the application type (HTTP/HTTPS) supporting private networks (with a private IP).
-  In ELB passthrough networking (CCE Turbo cluster + dedicated load balancer), ELB Ingress supports ClusterIP Services. In other scenarios, ELB Ingress supports NodePort Services.

Precautions
-----------

-  It is recommended that other resources not use the load balancer automatically created by an ingress. Otherwise, the load balancer will be occupied when the ingress is deleted, resulting in residual resources.
-  After an ingress is created, upgrade and maintain the configuration of the selected load balancers on the CCE console. Do not modify the configuration on the ELB console. Otherwise, the ingress service may be abnormal.
-  The URL registered in an ingress forwarding policy must be the same as the URL exposed by the backend Service. Otherwise, a 404 error will be returned.
-  In a cluster using the IPVS proxy mode, if the ingress and Service use the same ELB load balancer, the ingress cannot be accessed from the nodes and containers in the cluster because kube-proxy mounts the LoadBalancer Service address to the ipvs-0 bridge. This bridge intercepts the traffic of the load balancer connected to the ingress. You are advised to use different ELB load balancers for the ingress and Service.

Adding an ELB Ingress
---------------------

This section uses an Nginx workload as an example to describe how to add an ELB ingress.

#. Log in to the CCE console and access the cluster console.

#. Choose **Networking** in the navigation pane, click the **Ingresses** tab, and click **Create Service** in the upper right corner.

#. Set ingress parameters.

   -  **Name**: Specify a name of an ingress, for example, **ingress-demo**.

   -  **Load Balancer**

      Select the load balancer to interconnect. Only load balancers in the same VPC as the cluster are supported. If no load balancer is available, click **Create Load Balancer** to create one on the ELB console.

      Dedicated load balancers must support HTTP and the network type must support private networks.

   -  .. _cce_10_0251__li6851318392:

      **Listener**: Ingress configures a listener for the load balancer, which listens to requests from the load balancer and distributes traffic. After the configuration is complete, a listener is created on the load balancer. The default listener name is *k8s__<Protocol type>_<Port number>*, for example, *k8s_HTTP_80*.

      -  **Front-End Protocol**: **HTTP** and **HTTPS** are available.

      -  **External Port**: Port number that is open to the ELB service address. The port number can be specified randomly.

      -  **Server Certificate**: When an HTTPS listener is created for a load balancer, you need to bind a certificate to the load balancer to support encrypted authentication for HTTPS data transmission.

         .. note::

            If there is already an HTTPS ingress for the chosen port on the load balancer, the certificate of the new HTTPS ingress must be the same as the certificate of the existing ingress. This means that a listener has only one certificate. If two certificates, each with a different ingress, are added to the same listener of the same load balancer, only the certificate added earliest takes effect on the load balancer.

      -  **SNI**: Server Name Indication (SNI) is an extended protocol of TLS. It allows multiple TLS-based access domain names to be provided for external systems using the same IP address and port. Different domain names can use different security certificates. After SNI is enabled, the client is allowed to submit the requested domain name when initiating a TLS handshake request. After receiving the TLS request, the load balancer searches for the certificate based on the domain name in the request. If the certificate corresponding to the domain name is found, the load balancer returns the certificate for authorization. Otherwise, the default certificate (server certificate) is returned for authorization.

         .. note::

            -  The **SNI** option is available only when **HTTPS** is selected.

            -  This function is supported only for clusters of v1.15.11 and later.
            -  Specify the domain name for the SNI certificate. Only one domain name can be specified for each certificate. Wildcard-domain certificates are supported.

      -  **Security Policy**: combinations of different TLS versions and supported cipher suites available to HTTPS listeners.

         For details about security policies, see ELB User Guide.

         .. note::

            -  **Security Policy** is available only when **HTTPS** is selected.
            -  This function is supported only for clusters of v1.17.9 and later.

      -  Backend protocol

         If the :ref:`listener <cce_10_0251__li6851318392>` uses HTTP, only **HTTP** can be selected.

         If the :ref:`listener <cce_10_0251__li6851318392>` uses HTTPS, you can select **HTTP** or **HTTPS**.

   -  **Forwarding Policies**: When the access address of a request matches the forwarding policy (a forwarding policy consists of a domain name and URL, for example, 10.117.117.117:80/helloworld), the request is forwarded to the corresponding target Service for processing. Click |image1| to add multiple forwarding policies.

      -  **Domain Name**: actual domain name. Ensure that the domain name has been registered and archived. Once a domain name rule is configured, you must use the domain name for access.

      -  **URL Matching Rule**:

         -  **Prefix match**: If the URL is set to **/healthz**, the URL that meets the prefix can be accessed. For example, **/healthz/v1** and **/healthz/v2**.
         -  **Exact match**: The URL can be accessed only when it is fully matched. For example, if the URL is set to **/healthz**, only /healthz can be accessed.
         -  **Regular expression**: The URL is matched based on the regular expression. For example, if the regular expression is **/[A-Za-z0-9_.-]+/test**, all URLs that comply with this rule can be accessed, for example, **/abcA9/test** and **/v1-Ab/test**. Two regular expression standards are supported: POSIX and Perl.

      -  **URL**: access path to be registered, for example, **/healthz**.

         .. note::

            The URL added here must exist in the backend application. Otherwise, the forwarding fails.

            For example, the default access URL of the Nginx application is **/usr/share/nginx/html**. When adding **/test** to the ingress forwarding policy, ensure that your Nginx application contains the same URL, that is, **/usr/share/nginx/html/test**, otherwise, 404 is returned.

      -  **Destination Service**: Select an existing Service or create a Service. Services that do not meet search criteria are automatically filtered out.

      -  .. _cce_10_0251__li118614181492:

         **Destination Service Port**: Select the access port of the destination Service.

      -  **Set ELB**:

         -  **Distribution Policy**: Three algorithms are available: weighted round robin, weighted least connections algorithm, or source IP hash.

            .. note::

               -  **Weighted round robin**: Requests are forwarded to different servers based on their weights, which indicate server processing performance. Backend servers with higher weights receive proportionately more requests, whereas equal-weighted servers receive the same number of requests. This algorithm is often used for short connections, such as HTTP services.
               -  **Weighted least connections**: In addition to the weight assigned to each server, the number of connections processed by each backend server is also considered. Requests are forwarded to the server with the lowest connections-to-weight ratio. Building on **least connections**, the **weighted least connections** algorithm assigns a weight to each server based on their processing performance. This algorithm is often used for persistent connections, such as database connections.
               -  **Source IP hash**: The source IP address of each request is calculated using the hash algorithm to obtain a unique hash key, and all backend servers are numbered. The generated key allocates the client to a particular server. This allows requests from different clients to be routed based on source IP addresses and ensures that a client is directed to the same server as always. This algorithm applies to TCP connections without cookies.

         -  **Type**: This function is disabled by default. You can select **Load balancer cookie**.
         -  **Health Check**: configured for the load balancer. When TCP is selected during the :ref:`port settings <cce_10_0251__li118614181492>`, you can choose either TCP or HTTP. Currently, UDP is not supported. By default, the service port (Node Port and container port of the Service) is used for health check. You can also specify another port for health check. After the port is specified, a service port named **cce-healthz** will be added for the Service.

      -  **Operation**: Click **Delete** to delete the configuration.

   -  **Annotation**: Ingresses provide some advanced CCE functions, which are implemented by annotations. When you use kubectl to create a container, annotations will be used. For details, see :ref:`Creating an Ingress - Automatically Creating a Load Balancer <cce_10_0252__section3675115714214>` and :ref:`Creating an Ingress - Interconnecting with an Existing Load Balancer <cce_10_0252__section32300431736>`.

#. After the configuration is complete, click **OK**. After the ingress is created, it is displayed in the ingress list.

   On the ELB console, you can view the ELB automatically created through CCE. The default name is **cce-lb-ingress.UID**. Click the ELB name to access its details page. On the **Listeners** tab page, view the route settings of the ingress, including the URL, listener port, and backend server group port.

   .. important::

      After the ingress is created, upgrade and maintain the selected load balancer on the CCE console. Do not maintain the load balancer on the ELB console. Otherwise, the ingress service may be abnormal.

#. Access the /healthz interface of the workload, for example, workload **defaultbackend**.

   a. Obtain the access address of the **/healthz** interface of the workload. The access address consists of the load balancer IP address, external port, and mapping URL, for example, 10.**.**.**:80/healthz.

   b. Enter the URL of the /healthz interface, for example, http://10.**.**.**:80/healthz, in the address box of the browser to access the workload, as shown in :ref:`Figure 1 <cce_10_0251__fig17115192714367>`.

      .. _cce_10_0251__fig17115192714367:

      .. figure:: /_static/images/en-us_image_0000001518062672.png
         :alt: **Figure 1** Accessing the /healthz interface of defaultbackend

         **Figure 1** Accessing the /healthz interface of defaultbackend

.. |image1| image:: /_static/images/en-us_image_0000001568822825.png
