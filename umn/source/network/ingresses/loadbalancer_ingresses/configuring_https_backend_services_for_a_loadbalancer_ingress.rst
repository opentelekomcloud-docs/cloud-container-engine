:original_name: cce_10_0691.html

.. _cce_10_0691:

Configuring HTTPS Backend Services for a LoadBalancer Ingress
=============================================================

Ingresses can interconnect with backend services of different protocols. By default, the backend proxy channel of an ingress is HTTP-compliant. To create an HTTPS channel, add the following configuration to the **annotations** field:

.. code-block:: text

   kubernetes.io/elb.pool-protocol: https

Notes and Constraints
---------------------

-  This function is available only in clusters of v1.23.8, v1.25.3, or later.
-  Ingresses can interconnect with HTTPS backend services only when dedicated load balancers are used.
-  When an ingress interconnects with an HTTPS backend service, the ingress protocol must be HTTPS.

Configuration Example
---------------------

An ingress configuration example is as follows:

.. code-block::

   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: ingress-test
     namespace: default
     annotations:
       kubernetes.io/elb.port: '443'
       kubernetes.io/elb.id: <your_elb_id>    # In this example, an existing dedicated load balancer is used. Replace its ID with the ID of your dedicated load balancer.
       kubernetes.io/elb.class: performance
       kubernetes.io/elb.pool-protocol: https  # Interconnected HTTPS backend service
       kubernetes.io/elb.tls-ciphers-policy: tls-1-2
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
                     number: 80
               property:
                 ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
               pathType: ImplementationSpecific
     ingressClassName: cce
