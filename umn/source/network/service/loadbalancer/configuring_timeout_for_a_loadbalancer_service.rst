:original_name: cce_10_0729.html

.. _cce_10_0729:

Configuring Timeout for a LoadBalancer Service
==============================================

LoadBalancer Services allow you to configure timeout, which is the maximum duration for keeping a connection if no request is received from the client. If there are no requests reaching the load balancer after the timeout duration elapses, the load balancer will disconnect the connection with the client and establish a new connection when there is a new request.

Constraints
-----------

-  This feature takes effect only in the following versions:

   -  v1.19: v1.19.16-r30 or later
   -  v1.21: v1.21.10-r10 or later
   -  v1.23: v1.23.8-r10 or later
   -  v1.25: v1.25.3-r10 or later

-  The timeout can be configured only for the LoadBalancer Services using dedicated load balancers.
-  If you delete the timeout configuration during Service update, the timeout configuration on the existing listeners will be retained.

Procedure
---------

Use annotations to configure timeout. The following shows an example:

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     annotations:
       kubernetes.io/elb.id: <your_elb_id>    # In this example, an existing dedicated load balancer is used. Replace its ID with the ID of your dedicated load balancer.
       kubernetes.io/elb.class: performance  # Load balancer type
       kubernetes.io/elb.keepalive_timeout: '300'  # Timeout setting for client connections
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

   +-------------------------------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                           | Mandatory       | Type            | Description                                                                                                                                                                                                                                          |
   +=====================================+=================+=================+======================================================================================================================================================================================================================================================+
   | kubernetes.io/elb.keepalive_timeout | No              | Integer         | Timeout for client connections. If there are no requests reaching the load balancer after the timeout duration elapses, the load balancer will disconnect the connection with the client and establish a new connection when there is a new request. |
   |                                     |                 |                 |                                                                                                                                                                                                                                                      |
   |                                     |                 |                 | Value:                                                                                                                                                                                                                                               |
   |                                     |                 |                 |                                                                                                                                                                                                                                                      |
   |                                     |                 |                 | -  For TCP listeners, the value ranges from **10** to **4000**. The default value is **300**.                                                                                                                                                        |
   |                                     |                 |                 | -  For HTTP, HTTPS, and TERMINATED_HTTPS listeners, the value ranges from **10** to **4000**. The default value is **60**.                                                                                                                           |
   |                                     |                 |                 | -  For UDP listeners, the value ranges from **10** to **4000**. The default value is **300**.                                                                                                                                                        |
   +-------------------------------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
