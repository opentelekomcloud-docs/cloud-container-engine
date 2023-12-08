:original_name: cce_10_0694.html

.. _cce_10_0694:

ELB Ingresses Using HTTP/2
==========================

Ingresses can use HTTP/2 to expose Services. Connections from the load balancer to your application use HTTP/1.X by default. If your application is capable of receiving HTTP2 requests, you can add the following field to the ingress annotation to enable the use of HTTP/2:

.. code-block::

   kubernetes.io/elb.http2-enable: 'true'

The following shows the YAML file for associating with an existing load balancer:

**For clusters of v1.21 or earlier:**

.. code-block::

   apiVersion: networking.k8s.io/v1beta1
   kind: Ingress
   metadata:
     name: ingress-test
     annotations:
       kubernetes.io/elb.id: <your_elb_id>  # Replace it with the ID of your existing load balancer.
       kubernetes.io/elb.ip: <your_elb_ip>  # Replace it with the IP of your existing load balancer.
       kubernetes.io/elb.port: '443'
       kubernetes.io/ingress.class: cce
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
             serviceName: <your_service_name>  # Replace it with the name of your target Service.
             servicePort: 80                   # Replace it with the port number of your target Service.
           property:
             ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

**For clusters of v1.23 or later:**

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

Table 6 HTTP/2 parameters

+--------------------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Parameter                      | Mandatory       | Type            | Description                                                                                                                                                                                                                                                                                                                      |
+================================+=================+=================+==================================================================================================================================================================================================================================================================================================================================+
| kubernetes.io/elb.http2-enable | No              | Bool            | Whether HTTP/2 is enabled. Request forwarding using HTTP/2 improves the access performance between your application and the load balancer. However, the load balancer still uses HTTP 1.X to forward requests to the backend server. **This parameter is supported in clusters of v1.19.16-r0, v1.21.3-r0, and later versions.** |
|                                |                 |                 |                                                                                                                                                                                                                                                                                                                                  |
|                                |                 |                 | Options:                                                                                                                                                                                                                                                                                                                         |
|                                |                 |                 |                                                                                                                                                                                                                                                                                                                                  |
|                                |                 |                 | -  **true**: enabled                                                                                                                                                                                                                                                                                                             |
|                                |                 |                 | -  **false**: disabled (default value)                                                                                                                                                                                                                                                                                           |
|                                |                 |                 |                                                                                                                                                                                                                                                                                                                                  |
|                                |                 |                 | Note: **HTTP/2 can be enabled or disabled only when the listener uses HTTPS.** This parameter is invalid when the listener protocol is HTTP, and defaults to **false**.                                                                                                                                                          |
+--------------------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
