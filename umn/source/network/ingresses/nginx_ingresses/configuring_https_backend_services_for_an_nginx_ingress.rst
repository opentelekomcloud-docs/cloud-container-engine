:original_name: cce_10_0697.html

.. _cce_10_0697:

Configuring HTTPS Backend Services for an Nginx Ingress
=======================================================

Ingress can function as a proxy for backend services using different protocols. By default, the backend proxy channel of an ingress is an HTTP channel. To create an HTTPS channel, add the following configuration to the **annotations** field:

.. code-block:: text

   nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"

An ingress configuration example is as follows:

**For clusters of v1.23 or later:**

.. code-block::

   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: ingress-test
     namespace: default
     annotations:
       nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
   spec:
     tls:
       - secretName: ingress-test-secret  # Replace it with your TLS key certificate.
     rules:
       - host: ''
         http:
           paths:
             - path: '/'
               backend:
                 service:
                   name: <your_service_name>  # Replace it with the name of your target Service.
                   port:
                     number: <your_service_port>  # Replace it with the port number of your target Service.
               property:
                 ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
               pathType: ImplementationSpecific
     ingressClassName: nginx

**For clusters of v1.21 or earlier:**

.. code-block::

   apiVersion: networking.k8s.io/v1beta1
   kind: Ingress
   metadata:
     name: ingress-test
     namespace: default
     annotations:
       kubernetes.io/ingress.class: nginx
       nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
   spec:
     tls:
       - secretName: ingress-test-secret  # Replace it with your TLS key certificate.
     rules:
       - host: ''
         http:
           paths:
             - path: '/'
               backend:
                 serviceName: <your_service_name>  # Replace it with the name of your target Service.
                 servicePort: <your_service_port>  # Replace it with the port number of your target Service.
