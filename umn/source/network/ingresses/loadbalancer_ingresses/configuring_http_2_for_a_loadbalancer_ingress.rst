:original_name: cce_10_0694.html

.. _cce_10_0694:

Configuring HTTP/2 for a LoadBalancer Ingress
=============================================

Ingresses can use HTTP/2 to expose Services. Connections from the load balancer to your application use HTTP/1.X by default. If your application is capable of receiving HTTP2 requests, you can add the following field to the ingress annotation to enable the use of HTTP/2:

.. code-block::

   kubernetes.io/elb.http2-enable: 'true'

.. note::

   -  An HTTPS-compliant load balancer supports HTTP/2.
   -  This function is available in clusters of version v1.23.13-r0, v1.25.8-r0, v1.27.5-r0, or v1.28.3-r0.
   -  If the advanced configuration for enabling HTTP/2 or the target annotation is deleted, the ELB configuration will not be modified.

The following shows an example YAML file where an existing load balancer is associated:

.. code-block::

   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: ingress-test
     annotations:
       kubernetes.io/elb.id: <your_elb_id>  # Replace it with the ID of your existing load balancer.
       kubernetes.io/elb.ip: <your_elb_ip>  # Replace it with the IP of your existing load balancer.
       kubernetes.io/elb.port: '443'
       kubernetes.io/elb.http2-enable: 'true' # Enable HTTP/2.
   spec:
     tls:
     - secretName: ingress-test-secret
     rules:
     - host: ''
       http:
         paths:
         - path: '/'
           backend:
             service:
               name: <your_service_name>  # Replace it with the name of your target Service.
               port:
                 number: 8080             # Replace 8080 with the port number of your target Service.
           property:
             ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
           pathType: ImplementationSpecific
     ingressClassName: cce

.. table:: **Table 1** HTTP/2 parameters

   +--------------------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                      | Mandatory       | Type            | Description                                                                                                                                                                                                                          |
   +================================+=================+=================+======================================================================================================================================================================================================================================+
   | kubernetes.io/elb.http2-enable | No              | String          | Whether HTTP/2 is enabled. Request forwarding using HTTP/2 improves the access performance between your application and the load balancer. However, the load balancer still uses HTTP/1.x to forward requests to the backend server. |
   |                                |                 |                 |                                                                                                                                                                                                                                      |
   |                                |                 |                 | Options:                                                                                                                                                                                                                             |
   |                                |                 |                 |                                                                                                                                                                                                                                      |
   |                                |                 |                 | -  **true**: enabled                                                                                                                                                                                                                 |
   |                                |                 |                 | -  **false**: disabled (default value)                                                                                                                                                                                               |
   |                                |                 |                 |                                                                                                                                                                                                                                      |
   |                                |                 |                 | Note: **HTTP/2 can be enabled or disabled only when the listener uses HTTPS.** This parameter is invalid when the listener protocol is HTTP, and defaults to **false**.                                                              |
   +--------------------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
