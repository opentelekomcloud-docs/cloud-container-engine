:original_name: cce_10_0689.html

.. _cce_10_0689:

Routing a LoadBalancer Ingress to Multiple Services
===================================================

Ingresses can route to multiple backend Services based on different matching policies. The **spec** field in the YAML file is set as below. You can access **www.example.com/foo**, **www.example.com/bar**, and **foo.example.com/** to route to three different backend Services.

.. important::

   The URL registered in an ingress forwarding policy must be the same as the URL used to access the backend Service. Otherwise, a 404 error will be returned.

   For example, the default access URL of the Nginx application is **/usr/share/nginx/html**. When adding **/test** to the ingress forwarding policy, ensure the access URL of your Nginx application contains **/usr/share/nginx/html/test**. Otherwise, error 404 will be returned.

.. code-block::

   ...
   spec:
     rules:
     - host: 'www.example.com'
       http:
         paths:
         - path: '/foo'
           backend:
             serviceName: <your_service_name>  # Replace it with the name of your target Service.
             servicePort: 80
           property:
             ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
         - path: '/bar'
           backend:
             serviceName: <your_service_name>  # Replace it with the name of your target Service.
             servicePort: 80
           property:
             ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
     - host: 'foo.example.com'
       http:
         paths:
         - path: '/'
           backend:
             serviceName: <your_service_name>  # Replace it with the name of your target Service.
             servicePort: 80
           property:
             ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
