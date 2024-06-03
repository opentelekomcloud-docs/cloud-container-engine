:original_name: cce_10_0688.html

.. _cce_10_0688:

Configuring SNI for a LoadBalancer Ingress
==========================================

An SNI certificate is an extended server certificate that allows the same IP address and port number to provide multiple access domain names for external systems. Different security certificates can be used based on the domain names requested by clients to ensure HTTPS communication security.

When configuring SNI, you need to add a certificate associated with a domain name. The client submits the requested domain name information when initiating an SSL handshake request. After receiving the SSL request, the load balancer searches for the certificate based on the domain name. If the certificate is found, the load balancer will return it to the client. If the certificate is not found, the load balancer will return the default server certificate.

.. note::

   -  This function is supported only in clusters of v1.15.11 and later.
   -  The **SNI** option is available only when HTTPS is used.

   -  Only one domain name can be specified for each SNI certificate. Wildcard-domain certificates are supported.
   -  Security policy (kubernetes.io/elb.tls-ciphers-policy) is supported only in clusters of v1.17.11 or later.

You can enable SNI when the preceding conditions are met. The following uses the automatic creation of a load balancer as an example. In this example, **sni-test-secret-1** and **sni-test-secret-2** are SNI certificates. The domain names specified by the certificates must be the same as those in the certificates.

**For clusters of v1.21 or earlier:**

.. code-block::

   apiVersion: networking.k8s.io/v1beta1
   kind: Ingress
   metadata:
     name: ingress-test
     annotations:
       kubernetes.io/elb.class: performance
       kubernetes.io/ingress.class: cce
       kubernetes.io/elb.port: '443'
       kubernetes.io/elb.autocreate:
         '{
             "type": "public",
             "bandwidth_name": "cce-bandwidth-******",
             "bandwidth_chargemode": "traffic",
             "bandwidth_size": 5,
             "bandwidth_sharetype": "PER",
             "eip_type": "5_bgp",
             "available_zone": [
                 "eu-de-01"
             ],
             "elb_virsubnet_ids":["b4bf8152-6c36-4c3b-9f74-2229f8e640c9"],
             "l7_flavor_name": "L7_flavor.elb.s1.small"
          }'
       kubernetes.io/elb.tls-ciphers-policy: tls-1-2
   spec:
     tls:
     - secretName: ingress-test-secret
     - hosts:
         - example.top  # Domain name specified when a certificate is issued
       secretName: sni-test-secret-1
     - hosts:
         - example.com  # Domain name specified when a certificate is issued
       secretName: sni-test-secret-2
     rules:
     - host: example.com
       http:
         paths:
         - path: '/'
           backend:
             serviceName: <your_service_name>  # Replace it with the name of your target Service.
             servicePort: 80
           property:
             ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

**For clusters of v1.23 or later:**

.. code-block::

   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: ingress-test
     annotations:
       kubernetes.io/elb.class: performance
       kubernetes.io/elb.port: '443'
       kubernetes.io/elb.autocreate:
         '{
             "type": "public",
             "bandwidth_name": "cce-bandwidth-******",
             "bandwidth_chargemode": "traffic",
             "bandwidth_size": 5,
             "bandwidth_sharetype": "PER",
             "eip_type": "5_bgp",
             "available_zone": [
                 "eu-de-01"
             ],
             "elb_virsubnet_ids":["b4bf8152-6c36-4c3b-9f74-2229f8e640c9"],
             "l7_flavor_name": "L7_flavor.elb.s1.small"
          }'
       kubernetes.io/elb.tls-ciphers-policy: tls-1-2
   spec:
     tls:
     - secretName: ingress-test-secret
     - hosts:
         - example.top  # Domain name specified when a certificate is issued
       secretName: sni-test-secret-1
     - hosts:
         - example.com  # Domain name specified when a certificate is issued
       secretName: sni-test-secret-2
     rules:
     - host: example.com
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
