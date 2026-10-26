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

-  Only clusters v1.17 or later support dedicated load balancers.
-  It is recommended that other resources not use the load balancer automatically created by an ingress. Otherwise, the load balancer will be occupied when the ingress is deleted, resulting in residual resources.
-  After an ingress is created, upgrade and maintain the configuration of the selected load balancers on the CCE console. Do not modify the configuration on the ELB console. Otherwise, the ingress service may be abnormal.
-  The URL registered in an ingress forwarding policy must be the same as the URL used to access the backend Service. Otherwise, a 404 error will be returned.
-  In an IPVS cluster, if the LoadBalancer ingress and Service share the same load balancer, the ingress cannot be accessed from within the cluster's nodes and containers. This is because kube-proxy binds the LoadBalancer Service address to the ipvs-0 bridge, which intercepts the ELB traffic. Use different load balancers for the LoadBalancer ingress and Service.
-  Dedicated load balancers must be of the application type (HTTP/HTTPS) and support private networks (private IP addresses).
-  If multiple ingresses access the same ELB port in a cluster, the listener configuration items (such as the certificate associated with the listener and the HTTP/2 attribute of the listener) are subject to the configuration of the first ingress.

Adding a LoadBalancer Ingress
-----------------------------

This section uses an Nginx workload as an example to describe how to add a LoadBalancer ingress.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **Services and Ingresses** in the navigation pane, click the **Ingresses** tab, and click **Create Ingress** in the upper right corner.

#. Configure ingress parameters.

   -  **Name**: Enter a name for the ingress, for example, **ingress-demo**.

   -  **Namespace**: namespace that the LoadBalancer ingress belongs to.

   -  **Interconnect with Nginx**: This option is displayed only after the :ref:`NGINX Ingress Controller <cce_10_0034>` add-on is installed. If this option is available, the NGINX Ingress Controller add-on has been installed. Enabling this option will create an Nginx ingress. Disable it if you want to create a LoadBalancer ingress. For details, see :ref:`Creating an Nginx Ingress on the Console <cce_10_0390>`.

   -  **Protocol Version**: Select a protocol version of the LoadBalancer ingress. This parameter is only available for IPv4/IPv6 dual-stack clusters.

   -  **Load Balancer**: Select a load balancer type and creation mode.

      A load balancer can be dedicated or shared. A dedicated load balancer must be of the application type (HTTP/HTTPS) and support private networks.

      You can select **Use existing** or **Auto create** to obtain a load balancer. For details about the configuration of different creation modes, see :ref:`Table 1 <cce_10_0251__en-us_topic_0000001583656029_table026610571395>`.

      .. _cce_10_0251__en-us_topic_0000001583656029_table026610571395:

      .. table:: **Table 1** Load balancer settings

         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Creation Method                   | Configuration                                                                                                                                                                                                                                                                   |
         +===================================+=================================================================================================================================================================================================================================================================================+
         | Use existing                      | Only load balancers in the same VPC as the cluster can be selected. If no load balancer is available, click **Create Load Balancer** to create one on the ELB console.                                                                                                          |
         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Auto create                       | -  **Instance Name**: Enter a load balancer name.                                                                                                                                                                                                                               |
         |                                   | -  **AZ**: available only for dedicated load balancers. You can create load balancers in multiple AZs to improve service availability. If disaster recovery is required, select multiple AZs.                                                                                   |
         |                                   | -  **Frontend Subnet**: available only for dedicated load balancers. It is used to allocate IP addresses for load balancers to provide services externally.                                                                                                                     |
         |                                   | -  **Backend Subnet**: available only for dedicated load balancers. It is used to allocate IP addresses for load balancers to access the backend service.                                                                                                                       |
         |                                   | -  **Network Specifications**, **Application-oriented Specifications**, or **Specifications** (available only for dedicated load balancers)                                                                                                                                     |
         |                                   |                                                                                                                                                                                                                                                                                 |
         |                                   |    -  **Elastic**: applies to fluctuating traffic. You are billed based on actual Load Balancer Capacity Unit (LCU) usage. Clusters v1.21.10-r10, v1.23.8-r10, v1.25.3-r10, and later versions support elastic specifications.                                                  |
         |                                   |    -  **Fixed**: applies to stable traffic, billed based on specifications.                                                                                                                                                                                                     |
         |                                   |                                                                                                                                                                                                                                                                                 |
         |                                   | -  **EIP**: If you select **Auto assign**, you can configure the bandwidth for the public network.                                                                                                                                                                              |
         |                                   | -  **Resource Tag**: You can add resource tags to classify resources. You can create **predefined tags** on the TMS console. These tags are available to all resources that support tags. You can use these tags to improve the tag creation and resource migration efficiency. |
         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  .. _cce_10_0251__li6851318392:

      **Listener**: An ingress configures a listener for the load balancer, which listens to requests from the load balancer and distributes traffic. After the configuration is complete, a listener is created on the load balancer. The default listener name is *k8s_<Protocol type>_<Port number>*, for example, *k8s_HTTP_80*.

      -  **Frontend Protocol**: Set the protocol of the load balancer listener for establishing connections with clients.

      -  **External Port**: port number that is open to the ELB service address. The port number is configurable. If you select an existing load balancer, the listener ports of different clusters cannot be reused. Multiple ingresses within the same cluster can share the same listener port, but the listener configuration that works is determined by the earliest ingress configuration.

      -  **Access Control**

         -  **Inherit ELB Configurations**: CCE does not modify the existing access control configurations on the ELB console.
         -  **Allow all IP addresses**: No access control is configured.
         -  **Trustlist**: Only the selected IP address group can access the load balancer.
         -  **Blocklist**: The selected IP address group cannot access the load balancer.

         .. note::

            For clusters v1.25.16-r10, v1.27.16-r10, v1.28.15-r0, v1.29.10-r0, v1.30.6-r0, v1.31.1-r0, or later, when using a dedicated load balancer, you can select a maximum of five IP address groups at a time for access control.

      -  **Certificate Source**: TLS secret and ELB server certificates are supported.

         -  **TLS secret**: For details about how to create a secret certificate, see :ref:`Creating a Secret <cce_10_0153>`.
         -  **ELB server certificate**: Use a certificate created on ELB.

      -  **Server Certificate**: When an HTTPS listener is created for a load balancer, bind a certificate to the load balancer to support encrypted authentication for HTTPS data transmission.

         .. note::

            If there is already an HTTPS ingress for the chosen port on the load balancer, the certificate of the new HTTPS ingress must be the same as the certificate of the existing ingress. This means that a listener has only one certificate. If two certificates, each with a different ingress, are added to the same listener of the same load balancer, only the certificate added earliest takes effect on the load balancer.

      -  **SNI**: stands for Server Name Indication (SNI), which is an extended protocol of TLS. SNI enables several TLS domain names to share one IP:port pair while using different security certificates for each domain name. With SNI enabled, the client can send the requested domain name during the TLS handshake. The load balancer uses the domain name from the TLS request to find the matching certificate. If found, it returns that certificate. Otherwise, it returns the default server certificate.

         .. note::

            -  The **SNI** option is available only when **HTTPS** is used.

            -  This function is supported only in clusters v1.15.11 or later.
            -  Only one domain name can be specified for each SNI certificate. Wildcard-domain certificates are supported.
            -  For ingresses connected to the same ELB port, do not configure SNIs with the same domain name but different certificates. Otherwise, the SNIs will be overwritten.

      -  **Security Policy**: combinations of different TLS versions and supported cipher suites available to HTTPS listeners. The default value is **TLS-1-2**.

         For details about security policies, see *Elastic Load Balance User Guide*.

         .. note::

            -  **Security Policy** is available only when **HTTPS** is selected.
            -  This function is supported only in clusters v1.17.9 or later.

      -  **Backend Protocol**:

         When the :ref:`listener <cce_10_0251__li6851318392>` is HTTP-compliant, only **HTTP** can be selected.

         If it is an :ref:`HTTPS listener <cce_10_0251__li6851318392>`, this parameter can be set to **HTTP** or **HTTPS**.

      -  **Advanced Options**

         +-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------+
         | Configuration         | Description                                                                                                                                                                                                                                                                                 | Constraint                                                                                               |
         +=======================+=============================================================================================================================================================================================================================================================================================+==========================================================================================================+
         | Idle Timeout (s)      | The duration for which a client connection can remain idle before being terminated. If there are no requests reaching the load balancer during this period, the load balancer will disconnect the connection from the client and establish a new connection when there is a new request.    | This configuration is not supported when UDP is used on a shared load balancer.                          |
         +-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------+
         | Request Timeout (s)   | The duration within which a request from a client must be received. There are two cases:                                                                                                                                                                                                    | This function is available only for HTTP and HTTPS listeners.                                            |
         |                       |                                                                                                                                                                                                                                                                                             |                                                                                                          |
         |                       | -  If the client fails to send a request header to the load balancer during this period, the request will be interrupted.                                                                                                                                                                   |                                                                                                          |
         |                       | -  If the interval between two consecutive request bodies reaching the load balancer exceeds this period, the connection will be disconnected.                                                                                                                                              |                                                                                                          |
         +-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------+
         | Response Timeout (s)  | Timeout for backend server response. If the backend server does not respond within this period after receiving a request, the load balancer will stop waiting and return an HTTP 504 error.                                                                                                 | This function is available only for HTTP and HTTPS listeners.                                            |
         +-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------+
         | Enable HTTP/2         | Whether to enable HTTP/2 for HTTPS requests between the client and the load balancer. Request forwarding using HTTP/2 improves access performance between your application and the load balancer. However, the load balancer still uses HTTP/1.x to forward requests to the backend server. | This function is available only when the :ref:`listener <cce_10_0251__li6851318392>` is HTTPS-compliant. |
         +-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------+

   -  **Forwarding Policy**: When a request's access address matches the forwarding policy (which consists of a domain name, port, and URL, for example, 10.117.117.117:80/helloworld), the request is forwarded to the target Service for processing. You can click |image1| to add multiple forwarding policies.

      -  **Domain Name**: Enter an actual domain name to be accessed. If it is left blank, the ingress can be accessed through an IP address. The domain name must be registered and filed. Once used by a forwarding policy, only that domain name will be accepted for access.
      -  **Path Matching Rule**:

         -  **Prefix match**: If the URL is set to **/healthz**, the URL that meets the prefix can be accessed, for example, **/healthz/v1** and **/healthz/v2**.
         -  **Exact match**: The URL can be accessed only when it is fully matched. For example, if the URL is set to **/healthz**, only /healthz can be accessed.
         -  **RegEx match**: The URL is matched based on the regular expression. For example, if the regular expression is **/[A-Za-z0-9_.-]+/test**, all URLs that comply with this rule can be accessed, for example, **/abcA9/test** and **/v1-Ab/test**. Two regular expression standards are supported: POSIX and Perl.

      -  **Path**: access path, for example, **/healthz**

         .. note::

            The access path added here must exist in the backend application. Otherwise, the forwarding fails.

            For example, the default access URL of the Nginx application is **/usr/share/nginx/html**. When adding **/test** to the ingress forwarding policy, ensure the access URL of your Nginx application contains **/usr/share/nginx/html/test**. Otherwise, error 404 will be returned.

      -  **Destination Service**: Select an existing Service. Only Services that meet the requirements are automatically displayed in the Service list.
      -  **Destination Service Port**: Select the access port of the destination Service. The destination Service port must use TCP.
      -  **Set ELB**:

         -  .. _cce_10_0251__li8170555132211:

            **Algorithm**: You can select **Weighted round robin**, **Weighted least connections**, or **Source IP hash**.

            .. note::

               -  **Weighted round robin**: Requests are distributed to backend servers in proportion to their assigned weights, which reflect relative processing capacity. Equal-weight servers receive the same number of connections. This algorithm is suitable for short-lived connections, such as HTTP services.
               -  **Weighted least connections**: Requests are forwarded to the server with the lowest connections-to-weight ratio. In addition to each server's weight, this algorithm accounts for the number of active connections currently being processed. It is suitable for long-lived connections, such as database connections.
               -  **Source IP hash**: The source IP address of each request is hashed to generate a consistent key, which maps the client to a specific backend server. This distributes requests across clients while ensuring session persistence for the same client. This algorithm is suitable for TCP connections where cookie-based session affinity is unavailable.

         -  **Sticky Session**: This function is disabled by default. Options are as follows:

            -  **Load balancer cookie**: Enter the **Stickiness Duration**, which ranges from 1 to 1440 minutes.

            .. note::

               -  When the :ref:`distribution policy <cce_10_0251__li8170555132211>` uses the source IP hash, the sticky session cannot be set.
               -  Dedicated load balancers in the clusters earlier than v1.21 do not support sticky sessions. If sticky sessions are required, use shared load balancers.

         -  **Health Check**: Set the health check configuration of the load balancer. If this function is enabled, the following configurations are supported:

            +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
            | Parameter                         | Description                                                                                                                                                                                                                                             |
            +===================================+=========================================================================================================================================================================================================================================================+
            | Protocol                          | HTTP and TCP are supported.                                                                                                                                                                                                                             |
            |                                   |                                                                                                                                                                                                                                                         |
            |                                   | -  **Check Path**: This parameter is only available for HTTP health checks. It specifies the health check URL. The check path must start with a slash (/) and contain 1 to 80 characters.                                                               |
            +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
            | Port                              | By default, the service port (NodePort or container port of the Service) is used for health check. You can also specify another port for health check. After the port is specified, a service port named **cce-healthz** will be added for the Service. |
            |                                   |                                                                                                                                                                                                                                                         |
            |                                   | -  **Node Port**: If a shared load balancer is used or no ENI instance is associated, the node port is used as the health check port. If this parameter is not specified, a random port is used. The value ranges from 30000 to 32767.                  |
            |                                   | -  **Container Port**: When a dedicated load balancer is associated with a network interface, the container port is used for health check. The value ranges from 1 to 65535.                                                                            |
            +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
            | Check Period (s)                  | The maximum interval between health checks. The value ranges from 1 to 50.                                                                                                                                                                              |
            +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
            | Timeout (s)                       | The maximum wait time for each health check response. The value ranges from 1 to 50.                                                                                                                                                                    |
            +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
            | Max. Retries                      | The maximum retry attempts before marking a backend as unhealthy. The value ranges from 1 to 10.                                                                                                                                                        |
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

      .. figure:: /_static/images/en-us_image_0000002631690682.png
         :alt: **Figure 1** Accessing the /healthz interface of defaultbackend

         **Figure 1** Accessing the /healthz interface of defaultbackend

.. |image1| image:: /_static/images/en-us_image_0000002631530784.png
