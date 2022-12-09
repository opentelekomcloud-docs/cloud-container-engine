:original_name: cce_01_0251.html

.. _cce_01_0251:

Using ELB Ingresses on the Console
==================================

Prerequisites
-------------

-  An ingress provides network access for backend workloads. Ensure that a workload is available in a cluster. If no workload is available, deploy a workload by referring to :ref:`Creating a Deployment <cce_01_0047>`, :ref:`Creating a StatefulSet <cce_01_0048>`, or :ref:`Creating a DaemonSet <cce_01_0216>`.
-  A NodePort Service has been configured for the workload. For details about how to configure the Service, see :ref:`NodePort <cce_01_0142>`.

Precautions
-----------

-  It is recommended that other resources not use the load balancer automatically created by an ingress. Otherwise, the load balancer will be occupied when the ingress is deleted, resulting in residual resources.
-  After an ingress is created, upgrade and maintain the configuration of the selected load balancers on the CCE console. Do not modify the configuration on the ELB console. Otherwise, the ingress service may be abnormal.
-  The URL registered in an ingress forwarding policy must be the same as the URL exposed by the backend Service. Otherwise, a 404 error will be returned.

Adding an ELB Ingress
---------------------

This section uses an Nginx workload as an example to describe how to add an ELB ingress.

#. Log in to the CCE console.

#. In the navigation pane, choose **Resource Management** > **Network**. On the **Ingresses** tab page, select the corresponding cluster and namespace.

#. Click **Create Ingress** to access the ingress configuration page.

   Set the ingress parameters as required. The key parameters are as follows:

   -  **Access Type**: Use a load balancer to access Services. Requests can be forwarded only to NodePort Services.

   -  **Ingress Name**: Specify a name of an ingress, for example, **ingress-demo**.

   -  **Cluster Name**: Select the cluster to which the ingress is to be added.

   -  **Namespace**: Select the namespace to which the ingress is to be added.

   -  **ELB Configuration**: Ingress uses the load balancer of the ELB service to provide layer-7 network access. You can select an existing load balancer or have the system automatically create a new one. To manually create a load balancer, click **Create Load Balancer** and then click the refresh button.

      .. important::

         -  It is recommended that other resources not use the load balancer automatically created by an ingress. Otherwise, the load balancer will be occupied when the ingress is deleted, resulting in residual resources.
         -  Dedicated load balancers are supported only when the cluster version is 1.17 or later.
         -  To interconnect with an existing dedicated load balancer, ensure that HTTP is supported and the network type supports private networks.

      **Elastic Load Balancer**: The selected or created load balancer must be in the same VPC as the current cluster, and it must match the load balancer type (private or public network).

      You can create **public network** or **private network** load balancers. The default value is **Public network**.

      -  **Public network**: After you attach an EIP to a load balancer, the load balancer can distribute requests from the Internet to backend servers.

         -  **Enterprise Project**: Select an enterprise project in which the load balancer is created.
         -  **Change Configuration**: When selecting **Public network** > **Automatically created**, you can click **Change Configuration** to modify the name, specifications, billing mode, and bandwidth of the ELB instance to be created.

      -  **Private network**: After you attach a private IP address to a load balancer, the load balancer can distribute requests from the clients in the same VPC to backends.

         -  **Enterprise Project**: Select an enterprise project in which the load balancer is created.

   -  **Listener Configuration**: Ingress configures a listener for the load balancer, which listens to requests from the load balancer and distributes traffic. After the configuration is complete, a listener is created on the load balancer. The default listener name is *k8s__<Protocol type>_<Port number>*, for example, *k8s_HTTP_80*.

      -  **Front-End Protocol**: **HTTP** and **HTTPS** are available.

      -  **External Port**: Port number that is open to the ELB service address. The port number can be specified randomly.

      -  **Server Certificate**: When an HTTPS listener is created for a load balancer, you need to bind a certificate to the load balancer to support encrypted authentication for HTTPS data transmission. For details on how to create a secret, see :ref:`Creating a Secret <cce_01_0153>`.

         .. note::

            If there is already an HTTPS ingress for the chosen port on the load balancer, the certificate of the new HTTPS ingress must be the same as the certificate of the existing ingress. This means that a listener has only one certificate. If two certificates, each with a different ingress, are added to the same listener of the same load balancer, only the certificate added earliest takes effect on the load balancer.

      -  **SNI**: Click |image1| to enable the Server Name Indication (SNI) function. SNI is an extended protocol of TLS. It allows multiple TLS-based access domain names to be provided for external systems using the same IP address and port number. Different domain names can use different security certificates. After SNI is enabled, the client is allowed to submit the requested domain name when initiating a TLS handshake request. After receiving the TLS request, the load balancer searches for the certificate based on the domain name in the request. If the certificate corresponding to the domain name is found, the load balancer returns the certificate for authorization. Otherwise, the default certificate (server certificate) is returned for authorization.

         .. note::

            -  The **SNI** option is available only when **HTTPS** is selected.

            -  This function is supported only for clusters of v1.15.11 and later.
            -  Specify the domain name for the SNI certificate. Only one domain name can be specified for each certificate. Wildcard-domain certificates are supported.

      -  **Security Policy**: combinations of different TLS versions and supported cipher suites available to HTTPS listeners.

         For details about security policies, see ELB User Guide.

         .. note::

            -  **Security Policy** is available only when **HTTPS** is selected.
            -  This function is supported only for clusters of v1.17.9 and later.

   -  **Forwarding Policies**: When the access address of a request matches the forwarding policy (a forwarding policy consists of a domain name and URL, for example, 10.117.117.117:80/helloworld), the request is forwarded to the corresponding target Service for processing. Click **Add Forwarding Policies** to add multiple forwarding policies.

      -  **Domain Name**: actual domain name. Ensure that the domain name has been registered and archived. Once a domain name rule is configured, you must use the domain name for access.
      -  Rule Matching

         -  **Prefix match**: If the URL is set to **/healthz**, the URL that meets the prefix can be accessed. For example, **/healthz/v1** and **/healthz/v2**.
         -  **Exact match**: The URL can be accessed only when it is fully matched. For example, if the URL is set to **/healthz**, only /healthz can be accessed.
         -  **Regular expression**: The URL is matched based on the regular expression. For example, if the regular expression is **/[A-Za-z0-9_.-]+/test**, all URLs that comply with this rule can be accessed, for example, **/abcA9/test** and **/v1-Ab/test**. Two regular expression standards are supported: POSIX and Perl.

      -  **URL**: access path to be registered, for example, **/healthz**.
      -  **Target Service**: Select an existing Service or create a Service. Services that do not meet search criteria are automatically filtered out.
      -  **Service Access Port**: Select the access port of the target Service.
      -  **ELB Settings**: If multiple routes use the same Service, they are using the same Service load balancing configuration.

         -  **Algorithm Type**: Three algorithms are available: weighted round robin, weighted least connections algorithm, or source IP hash. For details about the allocation policies, see :ref:`LoadBalancer <cce_01_0014>`.

         -  **Sticky Session**: This function is disabled by default. After this function is enabled, you need to select a sticky session type and set the sticky session duration.

            **ELB cookie**: The load balancer generates a cookie after receiving a request from the client. All subsequent requests with the cookie are routed to the same backend server for processing.

            **Application cookie**: The application deployed on the backend server generates a cookie after receiving the first request from the client. All subsequent requests that contain the cookie are routed to this backend server. This sticky session type is supported by shared load balancers.

         -  **Health Check**: This function is disabled by default. To enable this function, set parameters as prompted. For details about the parameters, see `Configuring a Health Check <https://docs.otc.t-systems.com/en-us/usermanual/elb/en-us_topic_0162227063.html>`__.

      -  **Operation**: Click **Delete** to delete the configuration.

#. After the configuration is complete, click **Create**. After the ingress is created, it is displayed in the ingress list.

   On the ELB console, you can view the ELB automatically created through CCE. The default name is **cce-lb-ingress.UID**. Click the ELB name to access its details page. On the **Listeners** tab page, view the route settings of the ingress, including the URL, listener port, and backend server group port.

   .. important::

      After the ingress is created, upgrade and maintain the selected load balancer on the CCE console. Do not maintain the load balancer on the ELB console. Otherwise, the ingress service may be abnormal.

#. Access the /healthz interface of the workload, for example, workload **defaultbackend**.

   a. Obtain the access address of the /healthz interface of the workload. The access address consists of the load balancer IP address, external port, and mapping URL, for example, 10.**.**.**:80/healthz.

   b. Enter the URL of the /healthz interface, for example, http://10.**.**.**:80/healthz, in the address box of the browser to access the workload, as shown in :ref:`Figure 1 <cce_01_0251__fig17115192714367>`.

      .. _cce_01_0251__fig17115192714367:

      .. figure:: /_static/images/en-us_image_0000001192723194.png
         :alt: **Figure 1** Accessing the /healthz interface of defaultbackend

         **Figure 1** Accessing the /healthz interface of defaultbackend

Updating an Ingress
-------------------

After adding an ingress, you can update its port, domain name, and route configuration. The procedure is as follows:

.. note::

   You can modify the load balancer settings, including algorithm, sticky session, and health check configurations, after you select a Service in **Forwarding Policies** on the CCE console. Do not modify these configurations on the ELB console.

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Network**. On the **Ingresses** tab page, filter ingresses by cluster and namespace, and click **Update** for the ingress to be updated.

#. On the **Update Ingress** page, modify the required parameters.

   The parameters are the same as those set during creation.

#. Click **Submit**. The ingress will be updated for the workload.

.. |image1| image:: /_static/images/en-us_image_0000001238163131.png
