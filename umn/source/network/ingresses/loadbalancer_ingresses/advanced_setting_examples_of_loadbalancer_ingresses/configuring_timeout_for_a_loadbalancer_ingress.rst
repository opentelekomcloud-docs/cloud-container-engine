:original_name: cce_10_0730.html

.. _cce_10_0730:

Configuring Timeout for a LoadBalancer Ingress
==============================================

LoadBalancer ingresses support the following timeout settings:

-  Idle timeout setting for client connections: maximum duration for keeping a connection when no client request is received. If no request is received during this period, the load balancer closes the connection and establishes a new one with the client when the next request arrives.
-  Timeout for waiting for a request from a client: If the client fails to send a request header to the load balancer during the timeout duration or the interval for sending body data exceeds a specified period, the load balancer will release the connection.
-  Timeout setting for waiting for a response from a backend server: If the backend server fails to respond during the timeout duration, the load balancer will stop waiting and return HTTP 504 Gateway Timeout to the client.

.. note::

   If you delete the timeout configuration when updating an ingress, the timeout configuration of the existing listener will be reset to the default value.

Prerequisites
-------------

-  A CCE standard or Turbo cluster is available. The table below shows which cluster versions support the timeout configuration.

   +-----------------------+-----------------------+-------------------------------------+
   | Timeout Type          | Load Balancer Type    | Supported Cluster Version           |
   +=======================+=======================+=====================================+
   | Idle Timeout          | Dedicated             | -  v1.19: v1.19.16-r30 or later     |
   |                       |                       | -  v1.21: v1.21.10-r10 or later     |
   |                       |                       | -  v1.23: v1.23.8-r10 or later      |
   |                       |                       | -  v1.25: v1.25.3-r10 or later      |
   |                       |                       | -  Other clusters of later versions |
   +-----------------------+-----------------------+-------------------------------------+
   | Request Timeout       | Dedicated             |                                     |
   +-----------------------+-----------------------+-------------------------------------+
   | Response Timeout      | Dedicated             |                                     |
   +-----------------------+-----------------------+-------------------------------------+

-  An available workload has been deployed in the cluster for external access. If no workload is available, deploy a workload by referring to :ref:`Creating a Deployment <cce_10_0047>`, :ref:`Creating a StatefulSet <cce_10_0048>`, or :ref:`Creating a DaemonSet <cce_10_0216>`.
-  A Service for external access has been configured for the workload. :ref:`Services Supported by LoadBalancer Ingresses <cce_10_0094__section3565202819276>` lists the Service types supported by LoadBalancer ingresses.

Using the CCE Console to Configure Timeout
------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Services & Ingresses**. Click the **Ingresses** tab and click **Create Ingress** in the upper right corner.
#. Configure ingress parameters.

   .. note::

      This example explains only key parameters for configuring timeout. You can configure other parameters as required. For details, see :ref:`Creating a LoadBalancer Ingress on the Console <cce_10_0251>`.

   .. table:: **Table 1** Key parameters

      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Parameter             | Description                                                                                                                                                                                                                                                                                                          | Example                                                      |
      +=======================+======================================================================================================================================================================================================================================================================================================================+==============================================================+
      | Name                  | Enter an ingress name.                                                                                                                                                                                                                                                                                               | ingress-test                                                 |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Load Balancer         | Select a load balancer to be associated with the ingress or automatically create a load balancer. A load balancer can be dedicated or shared.                                                                                                                                                                        | Dedicated                                                    |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Listener              | -  **External Protocol**: **HTTP** and **HTTPS** are available.                                                                                                                                                                                                                                                      | -  External Protocol: HTTP                                   |
      |                       | -  **External Port**: specifies the port of the load balancer listener.                                                                                                                                                                                                                                              | -  External Port: 80                                         |
      |                       | -  **Advanced Options**                                                                                                                                                                                                                                                                                              | -  Advanced Options                                          |
      |                       |                                                                                                                                                                                                                                                                                                                      |                                                              |
      |                       |    -  **Idle Timeout (s)**: specifies the idle timeout of a client connection. If there are no requests reaching the load balancer during the timeout duration, the load balancer will disconnect the connection from the client and establish a new connection when there is a new request.                         |    -  Idle Timeout (s): 60                                   |
      |                       |                                                                                                                                                                                                                                                                                                                      |    -  Request Timeout (s): 60                                |
      |                       |    -  **Request Timeout (s)**: specifies the timeout duration for waiting for a client request.                                                                                                                                                                                                                      |    -  Response Timeout (s): 60                               |
      |                       |                                                                                                                                                                                                                                                                                                                      |                                                              |
      |                       |       Specifically:                                                                                                                                                                                                                                                                                                  |                                                              |
      |                       |                                                                                                                                                                                                                                                                                                                      |                                                              |
      |                       |       If the client fails to send a request header to the load balancer during the timeout duration, the request will be interrupted.                                                                                                                                                                                |                                                              |
      |                       |                                                                                                                                                                                                                                                                                                                      |                                                              |
      |                       |       If the interval between two consecutive request bodies reaching the load balancer is greater than the timeout duration, the connection will be disconnected.                                                                                                                                                   |                                                              |
      |                       |                                                                                                                                                                                                                                                                                                                      |                                                              |
      |                       |    -  **Response Timeout (s)**: specifies the timeout duration for waiting for a response from a backend server. After a request is forwarded to the backend server, if the backend server does not respond during the timeout duration, the load balancer will stop waiting and return HTTP 504 Gateway Timeout.    |                                                              |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Forwarding Policy     | -  **Domain Name**: Enter an actual domain name to be accessed. If it is left blank, the ingress can be accessed through the IP address. Ensure that the domain name has been registered and licensed. Once a forwarding policy is configured with a domain name specified, you must use the domain name for access. | -  Domain Name: You do not need to configure this parameter. |
      |                       | -  **Path Matching Rule**: Select **Prefix match**, **Exact match**, or **RegEx match**.                                                                                                                                                                                                                             | -  Path Matching Rule: Prefix match                          |
      |                       | -  **Path**: Enter the path provided by a backend application for external access. The path added must be valid in the backend application, or the forwarding cannot take effect.                                                                                                                                    | -  Path: /                                                   |
      |                       | -  **Destination Service**: Select an existing Service or create a Service. Any Services that do not match the search criteria will be filtered out automatically.                                                                                                                                                   | -  Destination Service: nginx                                |
      |                       | -  **Destination Service Port**: Select the access port of the destination Service.                                                                                                                                                                                                                                  | -  Destination Service Port: 80                              |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+

#. Click **OK**.

Using kubectl to Configure Timeout
----------------------------------

#. Use kubectl to access the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   .. code-block::

      vi ingress-test.yaml

   An example YAML file of an ingress associated with an existing load balancer is as follows:

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: test
        namespace: default
        annotations:
          kubernetes.io/elb.port: '80'
          kubernetes.io/elb.id: <your_elb_id>    # In this example, an existing dedicated load balancer is used. Replace its ID with the ID of your dedicated load balancer.
          kubernetes.io/elb.class: performance
          kubernetes.io/elb.keepalive_timeout: '300'  # Timeout setting for client connections
          kubernetes.io/elb.client_timeout: '60'      # Timeout duration for waiting for a request from a client
          kubernetes.io/elb.member_timeout: '60'      # Timeout for waiting for a response from a backend server
      spec:
        rules:
          - host: ''
            http:
              paths:
                - path: /
                  backend:
                    service:
                      name: test
                      port:
                        number: 80
                  property:
                    ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
                  pathType: ImplementationSpecific
        ingressClassName: cce

   .. table:: **Table 2** Key annotation parameters

      +-------------------------------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                           | Mandatory       | Type            | Description                                                                                                                                                                                                                                                                        |
      +=====================================+=================+=================+====================================================================================================================================================================================================================================================================================+
      | kubernetes.io/elb.keepalive_timeout | No              | String          | Timeout for client connections. If there are no requests reaching the load balancer during the timeout duration, the load balancer will disconnect the connection from the client and establish a new connection when there is a new request.                                      |
      |                                     |                 |                 |                                                                                                                                                                                                                                                                                    |
      |                                     |                 |                 | The value ranges from 0 to 4000 seconds. The default value is **60**.                                                                                                                                                                                                              |
      +-------------------------------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.client_timeout    | No              | String          | Timeout for waiting for a request from a client. There are two cases:                                                                                                                                                                                                              |
      |                                     |                 |                 |                                                                                                                                                                                                                                                                                    |
      |                                     |                 |                 | -  If the client fails to send a request header to the load balancer during the timeout duration, the request will be interrupted.                                                                                                                                                 |
      |                                     |                 |                 | -  If the interval between two consecutive request bodies reaching the load balancer is greater than the timeout duration, the connection will be disconnected.                                                                                                                    |
      |                                     |                 |                 |                                                                                                                                                                                                                                                                                    |
      |                                     |                 |                 | The value ranges from **1** to **300** (in seconds). The default value is **60**.                                                                                                                                                                                                  |
      +-------------------------------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.member_timeout    | No              | String          | Timeout for waiting for a response from a backend server. After a request is forwarded to the backend server, if the backend server does not respond within the duration specified by **member_timeout**, the load balancer will stop waiting and return HTTP 504 Gateway Timeout. |
      |                                     |                 |                 |                                                                                                                                                                                                                                                                                    |
      |                                     |                 |                 | The value ranges from **1** to **300** (in seconds). The default value is **60**.                                                                                                                                                                                                  |
      +-------------------------------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Create an ingress.

   .. code-block::

      kubectl create -f ingress-test.yaml

   If information similar to the following is displayed, the ingress has been created:

   .. code-block::

      ingress/ingress-test created

#. Check the created ingress.

   .. code-block::

      kubectl get ingress

   If information similar to the following is displayed, the ingress has been created:

   .. code-block::

      NAME          CLASS    HOSTS     ADDRESS          PORTS   AGE
      ingress-test  cce      *         121.**.**.**     80      10s
