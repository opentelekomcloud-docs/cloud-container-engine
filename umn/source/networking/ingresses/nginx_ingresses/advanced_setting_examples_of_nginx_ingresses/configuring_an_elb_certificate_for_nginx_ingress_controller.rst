:original_name: cce_10_0947.html

.. _cce_10_0947:

Configuring an ELB Certificate for NGINX Ingress Controller
===========================================================

CCE provides the following options for configuring ingress certificates for the Nginx Ingress Controller add-on:

-  Secret certificate. Import the required certificate to a CCE secret and specify the default server certificate (**default-ssl-certificate**) for the add-on.
-  ELB certificate. Use an ELB certificate created on the ELB console. There is no need to specify a secret certificate for the add-on.


.. figure:: /_static/images/en-us_image_0000002483959602.png
   :alt: **Figure 1** Differences between the two methods of configuring certificates for the NGINX Ingress Controller

   **Figure 1** Differences between the two methods of configuring certificates for the NGINX Ingress Controller

This section describes how to configure an ELB certificate for the NGINX Ingress Controller add-on and use that ELB certificate to manage the certificates used by requests.

Prerequisites
-------------

-  The version of the NGINX Ingress Controller add-on installed in the cluster must be 2.2.104, 2.6.53, 3.0.30, or later.
-  Before enabling the NGINX Ingress Controller add-on, it is necessary to ensure that the associated dedicated load balancer meets the required protocols. For example, an application load balancer supports only HTTP and HTTPS. If TCP is required, the load balancer must support both application and network load balancing.
-  With an ELB certificate configured, the default server certificate (**default-ssl-certificate**) of the NGINX Ingress Controller add-on cannot be used. All external requests must carry the ELB certificate to access services within the cluster.

Procedure
---------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Add-ons**. Locate **NGINX Ingress Controller** on the right and click **Install**.

   If the add-on has been installed, click **Manage**, select the pod for which you want to configure an ELB certificate, and click **Edit**.

#. In the **Extended Parameter Settings** area, modify the parameters settings.

   a. Configure the **service.annotations** parameter and check whether the protocol is correct.

      Example:

      .. code-block::

         ...
                 "service": {
                    "annotations": {
                       "kubernetes.io/elb.class": "performance",
                       "kubernetes.io/elb.id": "*****",
                       "kubernetes.io/elb.protocol-port": "https:443,http:80",
                       "kubernetes.io/elb.cert-id": "*****"
                 },
         ...

      +---------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                       | Value Type            | Description                                                                                                                                                                                                                                                                   |
      +=================================+=======================+===============================================================================================================================================================================================================================================================================+
      | kubernetes.io/elb.class         | String                | Load balancer type.                                                                                                                                                                                                                                                           |
      |                                 |                       |                                                                                                                                                                                                                                                                               |
      |                                 |                       | -  **union**: shared load balancer                                                                                                                                                                                                                                            |
      |                                 |                       | -  **performance**: dedicated load balancer                                                                                                                                                                                                                                   |
      +---------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.id            | String                | The load balancer ID. You can view the load balancer ID on the ELB console.                                                                                                                                                                                                   |
      +---------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.protocol-port | String                | The listener port of the NGINX Ingress Controller add-on. The listener protocol must match the type supported by the associated load balancer. For example, if the HTTP/HTTPS protocol is used, a dedicated load balancer that supports application load balancing is needed. |
      |                                 |                       |                                                                                                                                                                                                                                                                               |
      |                                 |                       | -  HTTP/HTTPS: If ports 443 and 80 are used for HTTPS and HTTP, respectively, the parameter value is *https:443,http:80*.                                                                                                                                                     |
      |                                 |                       | -  TLS: If ports 443 and 80 are used for TLS, the parameter value is *tls:443,tls:80*.                                                                                                                                                                                        |
      |                                 |                       |                                                                                                                                                                                                                                                                               |
      |                                 |                       |    .. note::                                                                                                                                                                                                                                                                  |
      |                                 |                       |                                                                                                                                                                                                                                                                               |
      |                                 |                       |       -  To configure TLS for ingresses, make sure the cluster version is v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, or later.                                                                                                                                          |
      |                                 |                       |       -  TLS relies on ELB. Before enabling TLS on an ingress, check whether TLS is supported in the current region.                                                                                                                                                          |
      +---------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.cert-id       | String                | ID of the ELB certificate.                                                                                                                                                                                                                                                    |
      |                                 |                       |                                                                                                                                                                                                                                                                               |
      |                                 |                       | To obtain the certificate, log in to the CCE console, choose **Service List** > **Networking** > **Elastic Load Balance**, and click **Certificates** in the navigation pane. In the load balancer list, copy the ID under the target certificate name.                       |
      +---------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      For details about the preceding parameters, see :ref:`Configuring HTTP/HTTPS for a LoadBalancer Service <cce_10_0683>`.

   b. Configure the **targetPort** parameter to direct the load balancer access traffic to the HTTP port of the container.

      Example:

      .. code-block::

         ...
                 "targetPorts": {
                     "http": "http",
                    "https": "http"
                 },
         ...

      .. note::

         Once an ELB certificate is configured, the default server certificate (**default-ssl-certificate**) configured for the NGINX Ingress Controller becomes unavailable and invalid. It is essential for external requests to include the configured ELB certificate. Otherwise, they will not be processed correctly.

#. Configure other mandatory parameters as required and click **Install**. For details about the parameters, see :ref:`NGINX Ingress Controller <cce_10_0034>`.

#. After completing the configuration, choose **Services & Ingresses** in the navigation pane, switch to the **kube-system** namespace, and check the protocols and listening ports of the add-on. In this example, the protocol is HTTP and HTTPS instead of TCP.
