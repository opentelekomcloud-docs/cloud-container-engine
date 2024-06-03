:original_name: cce_10_0730.html

.. _cce_10_0730:

Configuring Timeout for a LoadBalancer Ingress
==============================================

LoadBalancer ingresses support the following timeout settings:

-  Idle timeout setting for client connections: Maximum duration for keeping a connection when no client request is received. If no request is received during this period, the load balancer closes the connection and establishes a new one with the client when the next request arrives.
-  Timeout for waiting for a request from a client: If the client fails to send a request header to the load balancer during the timeout duration or the interval for sending body data exceeds a specified period, the load balancer will release the connection.
-  Timeout setting for waiting for a response from a backend server: If the backend server fails to respond during the timeout duration, the load balancer will stop waiting and return HTTP 504 Gateway Timeout to the client.

Constraints
-----------

-  The following table lists the scenarios where timeout can be configured for a Service.

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

-  If you delete the timeout configuration during an ingress update, the timeout configuration on the existing listeners will be retained.

Using kubectl
-------------

An ingress configuration example is as follows:

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
       kubernetes.io/elb.member_timeout: '60'      # Timeout duration for waiting for a response from a backend server
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

.. table:: **Table 1** Key annotation parameters

   +-------------------------------------+-----------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                           | Mandatory       | Type            | Description                                                                                                                                                                                                                                                                                 |
   +=====================================+=================+=================+=============================================================================================================================================================================================================================================================================================+
   | kubernetes.io/elb.keepalive_timeout | No              | String          | Timeout for client connections. If there are no requests reaching the load balancer during the timeout duration, the load balancer will disconnect the connection from the client and establish a new connection when there is a new request.                                               |
   |                                     |                 |                 |                                                                                                                                                                                                                                                                                             |
   |                                     |                 |                 | The value ranges from **0** to **4000** (in seconds). The default value is **60**.                                                                                                                                                                                                          |
   +-------------------------------------+-----------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | kubernetes.io/elb.client_timeout    | No              | String          | Timeout for waiting for a request from a client. There are two situations:                                                                                                                                                                                                                  |
   |                                     |                 |                 |                                                                                                                                                                                                                                                                                             |
   |                                     |                 |                 | -  If the client fails to send a request header to the load balancer during the timeout duration, the request will be interrupted.                                                                                                                                                          |
   |                                     |                 |                 | -  If the interval between two consecutive request bodies reaching the load balancer is greater than the timeout duration, the connection will be disconnected.                                                                                                                             |
   |                                     |                 |                 |                                                                                                                                                                                                                                                                                             |
   |                                     |                 |                 | The value ranges from **1** to **300** (in seconds). The default value is **60**.                                                                                                                                                                                                           |
   +-------------------------------------+-----------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | kubernetes.io/elb.member_timeout    | No              | String          | Timeout duration for waiting for a response from a backend server. After a request is forwarded to the backend server, if the backend server does not respond within the duration specified by **member_timeout**, the load balancer will stop waiting and return HTTP 504 Gateway Timeout. |
   |                                     |                 |                 |                                                                                                                                                                                                                                                                                             |
   |                                     |                 |                 | The value ranges from **1** to **300** (in seconds). The default value is **60**.                                                                                                                                                                                                           |
   +-------------------------------------+-----------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
