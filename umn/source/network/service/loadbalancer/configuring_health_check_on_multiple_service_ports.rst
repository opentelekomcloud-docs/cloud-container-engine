:original_name: cce_10_0684.html

.. _cce_10_0684:

Configuring Health Check on Multiple Service Ports
==================================================

The annotation field related to the health check of the LoadBalancer Service is upgraded from **Kubernetes.io/elb.health-check-option** to **Kubernetes.io/elb.health-check-options**. Each Service port can be configured separately, and you can configure only some ports. If the port protocol does not need to be configured separately, the original annotation field is still available and does not need to be modified.

Constraints
-----------

-  This feature is available in the following versions:

   -  v1.19: v1.19.16-r5 or later
   -  v1.21: v1.21.8-r0 or later
   -  v1.23: v1.23.6-r0 or later
   -  v1.25: v1.25.2-r0 or later
   -  Versions later than v1.25

-  **kubernetes.io/elb.health-check-option** and **kubernetes.io/elb.health-check-options** cannot be configured at the same time.
-  The **target_service_port** field is mandatory and must be unique.
-  For a TCP port, the health check protocol can only be TCP or HTTP. For a UDP port, the health check protocol must be UDP.

Procedure
---------

The following is an example of using the **kubernetes.io/elb.health-check-options** annotation:

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     name: nginx
     namespace: default
     labels:
       app: nginx
       version: v1
     annotations:
       kubernetes.io/elb.class: union         # Load balancer type
       kubernetes.io/elb.id: <your_elb_id>    # ELB ID. Replace it with the actual value.
       kubernetes.io/elb.lb-algorithm: ROUND_ROBIN  # Load balancer algorithm
       kubernetes.io/elb.health-check-flag: 'on'    # Enable ELB health check.
       kubernetes.io/elb.health-check-options: '[
       {
           "protocol": "TCP",
           "delay": "5",
           "timeout": "10",
           "max_retries": "3",
           "target_service_port": "TCP:1",
           "monitor_port": "22"
       },
       {
           "protocol": "HTTP",
           "delay": "5",
           "timeout": "10",
           "max_retries": "3",
           "path": "/",
           "target_service_port": "TCP:2",
           "monitor_port": "22",
                   "expected_codes": "200-399,401,404"
       }
       ]'
   spec:
     selector:
       app: nginx
       version: v1
     externalTrafficPolicy: Cluster
     ports:
       - name: cce-service-0
         targetPort: 1
         nodePort: 0
         port: 1
         protocol: TCP
       - name: cce-service-1
         targetPort: 2
         nodePort: 0
         port: 2
         protocol: TCP
     type: LoadBalancer
     loadBalancerIP: **.**.**.**

.. table:: **Table 1** elb.health-check-options

   +---------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter           | Mandatory       | Type            | Description                                                                                                                                  |
   +=====================+=================+=================+==============================================================================================================================================+
   | target_service_port | Yes             | String          | Port for health check specified by spec.ports. The value consists of the protocol and port number, for example, TCP:80.                      |
   +---------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | monitor_port        | No              | String          | Re-specified port for health check. If this parameter is not specified, the service port is used by default.                                 |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 | .. note::                                                                                                                                    |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 |    Ensure that the port is in the listening state on the node where the pod is located. Otherwise, the health check result will be affected. |
   +---------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | delay               | No              | String          | Health check interval (s)                                                                                                                    |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 | Value range: 1 to 50. Default value: **5**                                                                                                   |
   +---------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | timeout             | No              | String          | Health check timeout, in seconds.                                                                                                            |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 | Value range: 1 to 50. Default value: **10**                                                                                                  |
   +---------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | max_retries         | No              | String          | Maximum number of health check retries.                                                                                                      |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 | Value range: 1 to 10. Default value: **3**                                                                                                   |
   +---------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | protocol            | No              | String          | Health check protocol.                                                                                                                       |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 | Default value: protocol of the associated Service                                                                                            |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 | Value options: TCP, UDP, or HTTP                                                                                                             |
   +---------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | path                | No              | String          | Health check URL. This parameter needs to be configured when the protocol is **HTTP**.                                                       |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 | Default value: **/**                                                                                                                         |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 | Value range: 1-80 characters                                                                                                                 |
   +---------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------+
