:original_name: cce_10_0729.html

.. _cce_10_0729:

Configuring Timeout for a LoadBalancer Service
==============================================

LoadBalancer Services allow you to configure timeout, which is the maximum duration for keeping a connection if no request is received from the client. If no request is received during this period, the load balancer closes the connection and establishes a new one with the client when the next request arrives.

Notes and Constraints
---------------------

-  The following table lists the scenarios where timeout can be configured for a Service.

   +------------------+----------------------+------------------------------------+-------------------------------------+
   | Timeout Type     | Load Balancer Type   | Restrictions                       | Supported Cluster Version           |
   +==================+======================+====================================+=====================================+
   | Idle timeout     | Dedicated            | None                               | -  v1.19: v1.19.16-r30 or later     |
   |                  |                      |                                    | -  v1.21: v1.21.10-r10 or later     |
   |                  |                      |                                    | -  v1.23: v1.23.8-r10 or later      |
   |                  |                      |                                    | -  v1.25: v1.25.3-r10 or later      |
   |                  |                      |                                    | -  Other clusters of later versions |
   +------------------+----------------------+------------------------------------+-------------------------------------+
   | Idle timeout     | Shared               | UDP is not supported.              | -  v1.23: v1.23.13-r0 or later      |
   |                  |                      |                                    | -  v1.25: v1.25.8-r0 or later       |
   |                  |                      |                                    | -  v1.27: v1.27.5-r0 or later       |
   |                  |                      |                                    | -  v1.28: v1.28.3-r0 or later       |
   |                  |                      |                                    | -  Other clusters of later versions |
   +------------------+----------------------+------------------------------------+-------------------------------------+
   | Request timeout  | Dedicated and shared | Only HTTP and HTTPS are supported. |                                     |
   +------------------+----------------------+------------------------------------+-------------------------------------+
   | Response timeout | Dedicated and shared | Only HTTP and HTTPS are supported. |                                     |
   +------------------+----------------------+------------------------------------+-------------------------------------+

-  After timeout is configured, if you delete the timeout configuration on the CCE console or delete the target annotation from the YAML file, the configuration on the ELB will be retained.

Using the CCE Console
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Services & Ingresses**. In the upper right corner, click **Create Service**.
#. Configure Service parameters. In this example, only mandatory parameters required for configuring timeout are listed. For details about how to configure other parameters, see :ref:`Creating a LoadBalancer Service <cce_10_0681__section84162025538>`.

   -  **Service Name**: Specify a Service name, which can be the same as the workload name.
   -  **Service Type**: Select **LoadBalancer**.
   -  **Selector**: Add a label and click **Confirm**. The Service will use this label to select pods. You can also click **Reference Workload Label** to use the label of an existing workload. In the dialog box that is displayed, select a workload and click **OK**.
   -  **Load Balancer**: Select a load balancer type and creation mode.

      -  A load balancer can be dedicated or shared.
      -  This section uses an existing load balancer as an example. For details about the parameters for automatically creating a load balancer, see :ref:`Table 1 <cce_10_0681__table026610571395>`.

   -  **Ports**

      -  **Protocol**: protocol that the load balancer complies. Timeout cannot be configured for a UDP-compliant shared load balancer.

      -  **Service Port**: port used by the Service. The port number ranges from 1 to 65535.

      -  **Container Port**: listener port of the workload. For example, Nginx uses port 80 by default.

      -  .. _cce_10_0729__li8911126175719:

         **Frontend Protocol**: Select a protocol for the listener. If HTTP/HTTPS is not enabled, only the idle timeout can be configured.

   -  **Listener**

      -  **Advanced Options**: Select a proper option.

         +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+
         | Configuration         | Description                                                                                                                                                                                                                                                | Restrictions                                                                                                 |
         +=======================+============================================================================================================================================================================================================================================================+==============================================================================================================+
         | Idle Timeout          | Timeout for an idle client connection. If there are no requests reaching the load balancer during the timeout duration, the load balancer will disconnect the connection from the client and establish a new connection when there is a new request.       | This configuration is not supported if the port of a shared load balancer uses UDP.                          |
         +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+
         | Request Timeout       | Timeout for waiting for a request from a client. There are two cases:                                                                                                                                                                                      | This parameter is available only after :ref:`HTTP/HTTPS <cce_10_0729__li8911126175719>` is enabled on ports. |
         |                       |                                                                                                                                                                                                                                                            |                                                                                                              |
         |                       | -  If the client fails to send a request header to the load balancer during the timeout duration, the request will be interrupted.                                                                                                                         |                                                                                                              |
         |                       | -  If the interval between two consecutive request bodies reaching the load balancer is greater than the timeout duration, the connection will be disconnected.                                                                                            |                                                                                                              |
         +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+
         | Response Timeout      | Timeout for waiting for a response from a backend server. After a request is forwarded to the backend server, if the backend server does not respond during the timeout duration, the load balancer will stop waiting and return HTTP 504 Gateway Timeout. | This parameter is available only after :ref:`HTTP/HTTPS <cce_10_0729__li8911126175719>` is enabled on ports. |
         +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+

#. Click **OK**.

Using kubectl
-------------

Use annotations to configure timeout. The following shows an example:

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     annotations:
       kubernetes.io/elb.id: <your_elb_id>    # In this example, an existing dedicated load balancer is used. Replace its ID with the ID of your dedicated load balancer.
       kubernetes.io/elb.class: performance  # Load balancer type
       kubernetes.io/elb.keepalive_timeout: '300'  # Timeout setting for client connections
       kubernetes.io/elb.client_timeout: '60'      # Timeout for waiting for a request from a client
       kubernetes.io/elb.member_timeout: '60'      # Timeout for waiting for a response from a backend server
     name: nginx
   spec:
     ports:
     - name: service0
       port: 80
       protocol: TCP
       targetPort: 80
     selector:
       app: nginx
     type: LoadBalancer

.. table:: **Table 1** Key annotation parameters

   +-------------------------------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                           | Mandatory       | Type            | Description                                                                                                                                                                                                                                                                        |
   +=====================================+=================+=================+====================================================================================================================================================================================================================================================================================+
   | kubernetes.io/elb.keepalive_timeout | No              | String          | Timeout for client connections. If there are no requests reaching the load balancer during the timeout duration, the load balancer will disconnect the connection from the client and establish a new connection when there is a new request.                                      |
   |                                     |                 |                 |                                                                                                                                                                                                                                                                                    |
   |                                     |                 |                 | Value:                                                                                                                                                                                                                                                                             |
   |                                     |                 |                 |                                                                                                                                                                                                                                                                                    |
   |                                     |                 |                 | -  For TCP listeners, the value ranges from **10** to **4000** (in seconds). The default value is **300**.                                                                                                                                                                         |
   |                                     |                 |                 | -  For HTTP, HTTPS, and TERMINATED_HTTPS listeners, the value ranges from **0** to **4000** (in seconds). The default value is **60**.                                                                                                                                             |
   |                                     |                 |                 | -  For UDP listeners, the value ranges from **10** to **4000** (in seconds). The default value is **300**.                                                                                                                                                                         |
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
