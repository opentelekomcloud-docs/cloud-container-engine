:original_name: cce_10_0251.html

.. _cce_10_0251:

Creating a LoadBalancer Ingress on the Console
==============================================

In Kubernetes, an ingress is a resource object that controls how Services within a cluster can be accessed from outside the cluster. You can use ingresses to configure different forwarding rules to access pods in a cluster. The following uses an :ref:`Nginx workload <cce_10_0047__section155246177178>` as an example to describe how to create a LoadBalancer ingress on the console.

Prerequisites
-------------

-  An available workload has been deployed in the cluster for external access. If no workload is available, deploy a workload by referring to :ref:`Creating a Deployment <cce_10_0047>`, :ref:`Creating a StatefulSet <cce_10_0048>`, or :ref:`Creating a DaemonSet <cce_10_0216>`.
-  A Service for external access has been configured for the workload. :ref:`Services Supported by LoadBalancer Ingresses <cce_10_0094__section3565202819276>` lists the Service types supported by LoadBalancer ingresses.

Notes and Constraints
---------------------

-  It is recommended that other resources not use the load balancer automatically created by an ingress. Otherwise, the load balancer will be occupied when the ingress is deleted, resulting in residual resources.
-  After an ingress is created, upgrade and maintain the configuration of the selected load balancers on the CCE console. Do not modify the configuration on the ELB console. Otherwise, the ingress service may be abnormal.
-  The URL registered in an ingress forwarding policy must be the same as the URL used to access the backend Service. Otherwise, a 404 error will be returned.
-  In a cluster using the IPVS proxy mode, if the ingress and Service use the same ELB load balancer, the ingress cannot be accessed from the nodes and containers in the cluster because kube-proxy mounts the LoadBalancer Service address to the ipvs-0 bridge. This bridge intercepts the traffic of the load balancer connected to the ingress. Use different load balancers for the ingress and Service.
-  A dedicated load balancer must be of the application type (HTTP/HTTPS) and support private networks (with a private IP address).
-  If multiple ingresses access the same ELB port in a cluster, the listener configuration items (such as the certificate associated with the listener and the HTTP/2 attribute of the listener) are subject to the configuration of the first ingress.

Adding a LoadBalancer Ingress
-----------------------------

This section uses an Nginx workload as an example to describe how to add a LoadBalancer ingress.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **Services & Ingresses** in the navigation pane, click the **Ingresses** tab, and click **Create Ingress** in the upper right corner.

#. Configure ingress parameters.

   -  **Name**: Customize the name of an ingress, for example, **ingress-demo**.

   -  **Interconnect with Nginx**: This option is displayed only after the :ref:`NGINX Ingress Controller <cce_10_0034>` add-on is installed. If this option is available, the NGINX Ingress Controller add-on has been installed. Enabling this option will create an Nginx ingress. Disable it if you want to create a LoadBalancer ingress. For details, see :ref:`Creating an Nginx Ingress on the Console <cce_10_0390>`.

   -  **Load Balancer**: Select a load balancer type and creation mode.

      A load balancer can be dedicated or shared. A dedicated load balancer must be of the application type (HTTP/HTTPS) and support private networks.

      You can select **Use existing** or **Auto create** to obtain a load balancer. For details about the configuration of different creation modes, see :ref:`Table 1 <cce_10_0251__cce_10_0681_table026610571395>`.

      .. _cce_10_0251__cce_10_0681_table026610571395:

      .. table:: **Table 1** Load balancer configurations

         +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | How to Create                     | Configuration                                                                                                                                                                                                                                                                            |
         +===================================+==========================================================================================================================================================================================================================================================================================+
         | Use existing                      | Only the load balancers in the same VPC as the cluster can be selected. If no load balancer is available, click **Create Load Balancer** to create one on the ELB console.                                                                                                               |
         +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Auto create                       | -  **Instance Name**: Enter a load balancer name.                                                                                                                                                                                                                                        |
         |                                   | -  **AZ**: available only to dedicated load balancers. You can create load balancers in multiple AZs to improve service availability. You can deploy a load balancer in multiple AZs for high availability.                                                                              |
         |                                   | -  **Frontend Subnet**: available only to dedicated load balancers. It is used to allocate IP addresses for load balancers to provide services externally.                                                                                                                               |
         |                                   | -  **Backend Subnet**: available only to dedicated load balancers. It is used to allocate IP addresses for load balancers to access the backend service.                                                                                                                                 |
         |                                   | -  **Network Specifications**, **Application-oriented Specifications**, or **Specifications** (available only to dedicated load balancers)                                                                                                                                               |
         |                                   |                                                                                                                                                                                                                                                                                          |
         |                                   |    -  **Elastic**: applies to fluctuating traffic, billed based on total traffic. Clusters of v1.21.10-r10, v1.23.8-r10, v1.25.3-r10, and later versions support elastic specifications.                                                                                                 |
         |                                   |    -  **Fixed**: applies to stable traffic, billed based on specifications.                                                                                                                                                                                                              |
         |                                   |                                                                                                                                                                                                                                                                                          |
         |                                   | -  **EIP**: If you select **Auto create**, you can configure the size of the public network bandwidth.                                                                                                                                                                                   |
         |                                   | -  **Resource Tag**: You can add resource tags to classify resources. You can create **predefined tags** on the TMS console. The predefined tags are available to all resources that support tags. You can use these tags to improve the tag creation and resource migration efficiency. |
         +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  .. _cce_10_0251__li6851318392:

      **Listener**: An ingress configures a listener for the load balancer, which listens to requests from the load balancer and distributes traffic. After the configuration is complete, a listener is created on the load balancer. The default listener name is *k8s__<Protocol type>_<Port number>*, for example, *k8s_HTTP_80*.

      -  **External Protocol**: **HTTP** and **HTTPS** are available.

      -  **External Port**: port number that is open to the ELB service address. The port number is configurable.

      -  **Access Control**

         -  **Inherit ELB Configurations**: CCE does not modify the existing access control configurations on the ELB console.
         -  **Allow all IP addresses**: No access control is configured.
         -  **Trustlist**: Only the selected IP address group can access the load balancer.
         -  **Blocklist**: The selected IP address group cannot access the load balancer.

      -  **Certificate Source**: TLS secret and ELB server certificates are supported.

         -  **TLS secret**: For details about how to create a secret certificate, see :ref:`Creating a Secret <cce_10_0153>`.
         -  **ELB server certificate**: Use a certificate created in the ELB service.

      -  **Server Certificate**: When an HTTPS listener is created for a load balancer, bind a certificate to the load balancer to support encrypted authentication for HTTPS data transmission.

         .. note::

            If there is already an HTTPS ingress for the chosen port on the load balancer, the certificate of the new HTTPS ingress must be the same as the certificate of the existing ingress. This means that a listener has only one certificate. If two certificates, each with a different ingress, are added to the same listener of the same load balancer, only the certificate added earliest takes effect on the load balancer.

      -  **SNI**: stands for Server Name Indication (SNI), which is an extended protocol of TLS. SNI allows multiple TLS-compliant domain names for external access using the same IP address and port number, and different domain names can use different security certificates. After SNI is enabled, the client is allowed to submit the requested domain name when initiating a TLS handshake request. After receiving the TLS request, the load balancer searches for the certificate based on the domain name in the request. If the certificate corresponding to the domain name is found, the load balancer returns the certificate for authorization. Otherwise, the default certificate (server certificate) is returned for authorization.

         .. note::

            -  The **SNI** option is available only when **HTTPS** is used.

            -  This function is supported only in clusters of v1.15.11 or later.
            -  Only one domain name can be specified for each SNI certificate. Wildcard-domain certificates are supported.
            -  For ingresses connected to the same ELB port, do not configure SNIs with the same domain name but different certificates. Otherwise, the SNIs will be overwritten.

      -  **Security Policy**: combinations of different TLS versions and supported cipher suites available to HTTPS listeners.

         For details about security policies, see ELB User Guide.

         .. note::

            -  **Security Policy** is available only when **HTTPS** is selected.
            -  This function is supported only in clusters of v1.17.9 or later.

      -  **Backend Protocol**:

         When the :ref:`listener <cce_10_0251__li6851318392>` is HTTP-compliant, only **HTTP** can be selected.

         If it is an :ref:`HTTPS listener <cce_10_0251__li6851318392>`, this parameter can be set to **HTTP** or **HTTPS**.

      -  **Advanced Options**

         +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------+
         | Configuration         | Description                                                                                                                                                                                                                                                                       | Restrictions                                                                                             |
         +=======================+===================================================================================================================================================================================================================================================================================+==========================================================================================================+
         | Idle Timeout          | Timeout for an idle client connection. If there are no requests reaching the load balancer during the timeout duration, the load balancer will disconnect the connection from the client and establish a new connection when there is a new request.                              | None                                                                                                     |
         +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------+
         | Request Timeout       | Timeout for waiting for a request from a client. There are two cases:                                                                                                                                                                                                             | None                                                                                                     |
         |                       |                                                                                                                                                                                                                                                                                   |                                                                                                          |
         |                       | -  If the client fails to send a request header to the load balancer during the timeout duration, the request will be interrupted.                                                                                                                                                |                                                                                                          |
         |                       | -  If the interval between two consecutive request bodies reaching the load balancer is greater than the timeout duration, the connection will be disconnected.                                                                                                                   |                                                                                                          |
         +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------+
         | Response Timeout      | Timeout for waiting for a response from a backend server. After a request is forwarded to the backend server, if the backend server does not respond during the timeout duration, the load balancer will stop waiting and return HTTP 504 Gateway Timeout.                        | None                                                                                                     |
         +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------+
         | HTTP2                 | Whether to use HTTP/2 for a client to communicate with a load balancer. Request forwarding using HTTP/2 improves the access performance between your application and the load balancer. However, the load balancer still uses HTTP/1.x to forward requests to the backend server. | This function is available only when the :ref:`listener <cce_10_0251__li6851318392>` is HTTPS-compliant. |
         +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------+

   -  **Forwarding Policy**: When the access address of a request matches the forwarding policy (a forwarding policy consists of a domain name and URL, for example, 10.117.117.117:80/helloworld), the request is forwarded to the corresponding target Service for processing. You can click |image1| to add multiple forwarding policies.

      -  **Domain Name**: Enter an actual domain name to be accessed. If it is left blank, the ingress can be accessed through the IP address. Ensure that the domain name has been registered and licensed. Once a forwarding policy is configured with a domain name specified, you must use the domain name for access.
      -  **Path Matching Rule**:

         -  **Prefix match**: If the URL is set to **/healthz**, the URL that meets the prefix can be accessed, for example, **/healthz/v1** and **/healthz/v2**.
         -  **Exact match**: The URL can be accessed only when it is fully matched. For example, if the URL is set to **/healthz**, only /healthz can be accessed.
         -  **RegEX match**: The URL is matched based on the regular expression. For example, if the regular expression is **/[A-Za-z0-9_.-]+/test**, all URLs that comply with this rule can be accessed, for example, **/abcA9/test** and **/v1-Ab/test**. Two regular expression standards are supported: POSIX and Perl.

      -  **Path**: access path, for example, **/healthz**

         .. note::

            The access path added here must exist in the backend application. Otherwise, the forwarding fails.

            For example, the default access URL of the Nginx application is **/usr/share/nginx/html**. When adding **/test** to the ingress forwarding policy, ensure the access URL of your Nginx application contains **/usr/share/nginx/html/test**. Otherwise, error 404 will be returned.

      -  **Destination Service**: Select an existing Service or create a Service. Any Services that do not match the search criteria will be filtered out automatically.
      -  **Destination Service Port**: Select the access port of the destination Service.
      -  **Set ELB**:

         -  .. _cce_10_0251__li8170555132211:

            **Algorithm**: Three algorithms are available: weighted round robin, weighted least connections algorithm, or source IP hash.

            .. note::

               -  **Weighted round robin**: Requests are forwarded to different servers based on their weights, which indicate server processing performance. Backend servers with higher weights receive proportionately more requests, whereas equal-weighted servers receive the same number of requests. This algorithm is often used for short connections, such as HTTP services.
               -  **Weighted least connections**: In addition to the weight assigned to each server, the number of connections processed by each backend server is considered. Requests are forwarded to the server with the lowest connections-to-weight ratio. Building on **least connections**, the **weighted least connections** algorithm assigns a weight to each server based on their processing capability. This algorithm is often used for persistent connections, such as database connections.
               -  **Source IP hash**: The source IP address of each request is calculated using the hash algorithm to obtain a unique hash key, and all backend servers are numbered. The generated key allocates the client to a particular server. This enables requests from different clients to be distributed in load balancing mode and ensures that requests from the same client are forwarded to the same server. This algorithm applies to TCP connections without cookies.

         -  **Sticky Session**: This function is disabled by default. Options are as follows:

            -  **Load balancer cookie**: Enter the **Stickiness Duration** , which ranges from 1 to 1440 minutes.

            .. note::

               -  When the :ref:`distribution policy <cce_10_0251__li8170555132211>` uses the source IP hash, sticky session cannot be set.
               -  Dedicated load balancers in the clusters of a version earlier than v1.21 do not support sticky sessions. If sticky sessions are required, use shared load balancers.

         -  **Health Check**: Set the health check configuration of the load balancer. If this function is enabled, the following configurations are supported:

            +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
            | Parameter                         | Description                                                                                                                                                                                                                                             |
            +===================================+=========================================================================================================================================================================================================================================================+
            | Protocol                          | When the protocol of the target Service port is TCP, more protocols including HTTP are supported.                                                                                                                                                       |
            |                                   |                                                                                                                                                                                                                                                         |
            |                                   | -  **Check Path** (supported only by HTTP for health check): specifies the health check URL. The check path must start with a slash (/) and contain 1 to 80 characters.                                                                                 |
            +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
            | Port                              | By default, the service port (NodePort or container port of the Service) is used for health check. You can also specify another port for health check. After the port is specified, a service port named **cce-healthz** will be added for the Service. |
            |                                   |                                                                                                                                                                                                                                                         |
            |                                   | -  **Node Port**: If a shared load balancer is used or no ENI instance is associated, the node port is used as the health check port. If this parameter is not specified, a random port is used. The value ranges from 30000 to 32767.                  |
            |                                   | -  **Container Port**: When a dedicated load balancer is associated with an ENI instance, the container port is used for health check. The value ranges from 1 to 65535.                                                                                |
            +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
            | Check Period (s)                  | Specifies the maximum interval between health checks. The value ranges from 1 to 50.                                                                                                                                                                    |
            +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
            | Timeout (s)                       | Specifies the maximum timeout duration for each health check. The value ranges from 1 to 50.                                                                                                                                                            |
            +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
            | Max. Retries                      | Specifies the maximum number of health check retries. The value ranges from 1 to 10.                                                                                                                                                                    |
            +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      -  **Operation**: Click **Delete** to delete the configuration.

   -  **Annotation**: Ingresses provide some advanced CCE functions, which are implemented by annotations. When you use kubectl to create a container, annotations will be used. For details, see :ref:`Automatically Creating a Load Balancer While Creating an Ingress <cce_10_0252__section3675115714214>` or :ref:`Associating an Existing Load Balancer to an Ingress While Creating the Ingress <cce_10_0252__section32300431736>`.

#. Click **OK**. After the ingress is created, it is displayed in the ingress list.

   On the ELB console, you can check the load balancer automatically created through CCE. The default name is **cce-lb-<ingress.UID>**. Click the load balancer name to go to the details page. On the **Listeners** tab page, check the listener and forwarding policy of the target ingress.

   .. important::

      After an ingress is created, upgrade and maintain the selected load balancer on the CCE console. Do not modify the configuration on the ELB console. Otherwise, the ingress service may be abnormal.

#. Access the /healthz interface of the workload, for example, workload **defaultbackend**.

   a. Obtain the access address of the **/healthz** interface of the workload. The access address consists of the load balancer IP address, external port, and mapping URL, for example, 10.**.**.**:80/healthz.

   b. Enter the URL of the /healthz interface, for example, http://10.**.**.**:80/healthz, in the address box of the browser to access the workload, as shown in :ref:`Figure 1 <cce_10_0251__fig17115192714367>`.

      .. _cce_10_0251__fig17115192714367:

      .. figure:: /_static/images/en-us_image_0000002101679001.png
         :alt: **Figure 1** Accessing the /healthz interface of defaultbackend

         **Figure 1** Accessing the /healthz interface of defaultbackend

.. |image1| image:: /_static/images/en-us_image_0000002065638946.png
