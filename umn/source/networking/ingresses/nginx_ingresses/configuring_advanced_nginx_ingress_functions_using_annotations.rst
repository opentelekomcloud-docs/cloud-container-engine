:original_name: cce_10_0699.html

.. _cce_10_0699:

Configuring Advanced Nginx Ingress Functions Using Annotations
==============================================================

The nginx-ingress add-on in CCE uses the community chart and image. If the default add-on parameters cannot meet your demands, you can add annotations to define what you need, such as the default backend, timeout, and size of a request body.

This section describes common annotations used for creating an ingress of the Nginx type.

.. note::

   -  The key value of an annotation can only be a string. Other types (such as Boolean values or numeric values) must be enclosed in quotation marks (""), for example, "true", "false", and "100".
   -  Nginx Ingress supports native annotations of the community. For details, see `Annotations <https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/>`__.

-  :ref:`Ingress Type <cce_10_0699__section7819047102916>`
-  :ref:`Interconnecting with HTTPS Backend Services <cce_10_0699__section55781550204513>`
-  :ref:`Creating a Consistent Hashing Rule for Load Balancing <cce_10_0699__section03391948464>`
-  :ref:`Customized Timeout Interval <cce_10_0699__section461255141314>`
-  :ref:`Customizing a Body Size <cce_10_0699__section14361312171417>`
-  :ref:`Two-Way HTTPS Authentication <cce_10_0699__section1358180776>`
-  :ref:`Domain Name Regularization <cce_10_0699__section2048022342319>`
-  :ref:`Helpful Links <cce_10_0699__section20346153710463>`

.. _cce_10_0699__section7819047102916:

Ingress Type
------------

.. table:: **Table 1** Ingress type annotations

   +-----------------------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------+
   | Parameter                   | Type            | Description                                                                                                                                            | Supported Cluster Version         |
   +=============================+=================+========================================================================================================================================================+===================================+
   | kubernetes.io/ingress.class | String          | -  **nginx**: Nginx Ingress is used.                                                                                                                   | Only clusters of v1.21 or earlier |
   |                             |                 | -  **cce**: A proprietary LoadBalancer ingress is used.                                                                                                |                                   |
   |                             |                 |                                                                                                                                                        |                                   |
   |                             |                 | This parameter is mandatory when you create an ingress. The default value is **cce**.                                                                  |                                   |
   |                             |                 |                                                                                                                                                        |                                   |
   |                             |                 | For clusters of v1.23 or later, use the parameter **ingressClassName**. For details, see :ref:`Creating an Nginx Ingress Using kubectl <cce_10_0364>`. |                                   |
   +-----------------------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------+

For details about how to use the preceding annotations, see :ref:`Creating an Nginx Ingress Using kubectl <cce_10_0364>`.

.. _cce_10_0699__section55781550204513:

Interconnecting with HTTPS Backend Services
-------------------------------------------

.. table:: **Table 2** Annotations for interconnecting with HTTPS backend services

   +----------------------------------------------+--------+------------------------------------------------------------------------------------------------------------+
   | Parameter                                    | Type   | Description                                                                                                |
   +==============================================+========+============================================================================================================+
   | nginx.ingress.kubernetes.io/backend-protocol | String | If this parameter is set to **HTTPS**, HTTPS is used to forward requests to the backend service container. |
   +----------------------------------------------+--------+------------------------------------------------------------------------------------------------------------+

For details about application scenarios and use cases, see :ref:`Configuring HTTPS Backend Services for an Nginx Ingress <cce_10_0697>`.

.. _cce_10_0699__section03391948464:

Creating a Consistent Hashing Rule for Load Balancing
-----------------------------------------------------

.. table:: **Table 3** Annotation of consistent hashing for load balancing

   +----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                                    | Type                  | Description                                                                                                                                                     |
   +==============================================+=======================+=================================================================================================================================================================+
   | nginx.ingress.kubernetes.io/upstream-hash-by | String                | Enable consistent hashing for load balancing for backend servers. The parameter value can be an Nginx parameter, a text value, or any combination. For example: |
   |                                              |                       |                                                                                                                                                                 |
   |                                              |                       | -  **nginx.ingress.kubernetes.io/upstream-hash-by: "$request_uri"** indicates that requests are hashed based on the request URI.                                |
   |                                              |                       | -  **nginx.ingress.kubernetes.io/upstream-hash-by: "$request_uri$host"** indicates that requests are hashed based on the request URI and domain name.           |
   |                                              |                       | -  **nginx.ingress.kubernetes.io/upstream-hash-by: "${request_uri}-text-value"** indicates that requests are hashed based on the request URI and text value.    |
   +----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------+

For details about application scenarios and use cases, see :ref:`Configuring Consistent Hashing for Load Balancing of an Nginx Ingress <cce_10_0698>`.

.. _cce_10_0699__section461255141314:

Customized Timeout Interval
---------------------------

.. table:: **Table 4** Customized timeout interval annotations

   +---------------------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                                         | Type                  | Description                                                                                                                            |
   +===================================================+=======================+========================================================================================================================================+
   | nginx.ingress.kubernetes.io/proxy-connect-timeout | String                | Customized connection timeout interval. You do not need to set the unit when setting the timeout interval. The default unit is second. |
   |                                                   |                       |                                                                                                                                        |
   |                                                   |                       | Example:                                                                                                                               |
   |                                                   |                       |                                                                                                                                        |
   |                                                   |                       | .. code-block::                                                                                                                        |
   |                                                   |                       |                                                                                                                                        |
   |                                                   |                       |    nginx.ingress.kubernetes.io/proxy-connect-timeout: '120'                                                                            |
   +---------------------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0699__section14361312171417:

Customizing a Body Size
-----------------------

.. table:: **Table 5** Annotations of customizing a body size

   +---------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                                   | Type                  | Description                                                                                                                                                                                                                                                                                           |
   +=============================================+=======================+=======================================================================================================================================================================================================================================================================================================+
   | nginx.ingress.kubernetes.io/proxy-body-size | String                | When the body size in a request exceeds the upper limit, error 413 will be returned to the client. You can use this parameter to adjust the upper limit of the body size. The basic unit of the parameter value is byte. You can use units such as KB, MB, and GB. The unit conversion is as follows: |
   |                                             |                       |                                                                                                                                                                                                                                                                                                       |
   |                                             |                       | 1 KB = 1024 bytes, 1 MB = 1024 KB, 1 GB =1024 MB                                                                                                                                                                                                                                                      |
   |                                             |                       |                                                                                                                                                                                                                                                                                                       |
   |                                             |                       | Example:                                                                                                                                                                                                                                                                                              |
   |                                             |                       |                                                                                                                                                                                                                                                                                                       |
   |                                             |                       | .. code-block::                                                                                                                                                                                                                                                                                       |
   |                                             |                       |                                                                                                                                                                                                                                                                                                       |
   |                                             |                       |    nginx.ingress.kubernetes.io/proxy-body-size: 8m                                                                                                                                                                                                                                                    |
   +---------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0699__section1358180776:

Two-Way HTTPS Authentication
----------------------------

Nginx Ingress supports two-way HTTPS authentication between the server and client to ensure secure connections.

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Run the following command to create a self-signed CA certificate:

   .. code-block::

      openssl req -x509 -sha256 -newkey rsa:4096 -keyout ca.key -out ca.crt -days 356 -nodes -subj '/CN=Ingress Cert Authority'

   Expected output:

   .. code-block::

      Generating a RSA private key
      .............++++
      ................................................++++
      writing new private key to 'ca.key'
      -----

#. Create a server certificate.

   a. Run the following command to create a request file for generating a server certificate:

      .. code-block::

         openssl req -new -newkey rsa:4096 -keyout server.key -out server.csr -nodes -subj '/CN=example.com'

      Expected output:

      .. code-block::

         Generating a RSA private key
         .....................................................++++
         ..........++++
         writing new private key to 'server.key'
         -----

   b. Run the following command to issue the server request file using the root certificate to generate the server certificate:

      .. code-block::

         openssl x509 -req -sha256 -days 365 -in server.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out server.crt

      Expected output:

      .. code-block::

         Signature ok
         subject=CN = example.com
         Getting CA Private Key

#. Create a client certificate.

   a. Run the following command to create a request file for generating a client certificate:

      .. code-block::

         openssl req -new -newkey rsa:4096 -keyout client.key -out client.csr -nodes -subj '/CN=Ingress'

      Expected output:

      .. code-block::

         Generating a RSA private key
         .................................++++
         ................................................++++
         writing new private key to 'client.key'
         -----

   b. Run the following command to issue the client request file using the root certificate to generate the client certificate:

      .. code-block::

         openssl x509 -req -sha256 -days 365 -in client.csr -CA ca.crt -CAkey ca.key -set_serial 02 -out client.crt

      Expected output:

      .. code-block::

         Signature ok
         subject=CN = Ingress
         Getting CA Private Key

#. Run the **ls** command to check the created certificates.

   Expected output:

   .. code-block::

      ca.crt  ca.key  client.crt  client.csr  client.key  server.crt  server.csr  server.key

#. Run the following command to create a secret of the CA certificate:

   .. code-block::

      kubectl create secret generic ca-secret --from-file=ca.crt=ca.crt

   Expected output:

   .. code-block::

      secret/ca-secret created

#. Run the following command to create a secret of the server certificate:

   .. code-block::

      kubectl create secret generic tls-secret --from-file=tls.crt=server.crt --from-file=tls.key=server.key

   Expected output:

   .. code-block::

      secret/tls-secret created

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   .. code-block::

      vi ingress-test.yaml

   -  **For clusters of v1.23 or later:**

      .. code-block::

         apiVersion: networking.k8s.io/v1
         kind: Ingress
         metadata:
           annotations:
             nginx.ingress.kubernetes.io/auth-tls-verify-client: "on"
             nginx.ingress.kubernetes.io/auth-tls-secret: "default/ca-secret"   # Replace it with your CA certificate secret.
             nginx.ingress.kubernetes.io/auth-tls-verify-depth: "1"
             nginx.ingress.kubernetes.io/auth-tls-pass-certificate-to-upstream: "true"
           name: ingress-test
           namespace: default
         spec:
           rules:
           - host: example.com
             http:
               paths:
               - backend:
                   service:
                     name: nginx-test  # Replace it with the name of your target Service.
                     port:
                       number: 80  # Replace it with the port of your target Service.
                 path: /
                 pathType: ImplementationSpecific
           tls:
           - hosts:
             - example.com
             secretName: tls-secret   # Replace it with your TLS certificate secret.
           ingressClassName: nginx

   -  **For clusters of v1.21 or earlier:**

      .. code-block::

         apiVersion: networking.k8s.io/v1beta1
         kind: Ingress
         metadata:
           annotations:
             kubernetes.io/ingress.class: nginx
             nginx.ingress.kubernetes.io/auth-tls-verify-client: "on"
             nginx.ingress.kubernetes.io/auth-tls-secret: "default/ca-secret"   # Replace it with your CA certificate secret.
             nginx.ingress.kubernetes.io/auth-tls-verify-depth: "1"
             nginx.ingress.kubernetes.io/auth-tls-pass-certificate-to-upstream: "true"
           name: ingress-test
           namespace: default
         spec:
           rules:
           - host: example.com
             http:
               paths:
               - path: '/'
                 backend:
                   serviceName: nginx-test  # Replace it with the name of your target Service.
                   servicePort: 80  # Replace it with the port of your target Service.
           tls:
           - hosts:
             - example.com
             secretName: tls-secret   # Replace it with your TLS certificate secret.

#. Run the following command to create an ingress:

   .. code-block::

      kubectl create -f ingress-test.yaml

   Expected output:

   .. code-block::

      ingress.networking.k8s.io/ingress-test created

#. Run the following command to obtain the IP address of the ingress:

   .. code-block::

      kubectl get ingress

   Expected output:

   .. code-block::

      NAME         CLASS   HOSTS         ADDRESS      PORTS     AGE
      nginx-test   nginx   example.com   10.3.xx.xx   80, 443   27m

#. Run the following command to update the IP address of the ingress into the **hosts** file and replace the following IP address with the actual IP address of the ingress:

   .. code-block::

      echo "10.3.xx.xx  example.com" | sudo tee -a /etc/hosts

   Expected output:

   .. code-block::

      10.3.xx.xx  example.com

#. Verify the configuration.

   -  The client does not send the certificate for access.

      .. code-block::

         curl --cacert ./ca.crt  https://example.com -k

      Expected output:

      .. code-block::

         <html>
         <head><title>400 No required SSL certificate was sent</title></head>
         <body>
         <center><h1>400 Bad Request</h1></center>
         <center>No required SSL certificate was sent</center>
         <hr><center>nginx</center>
         </body>
         </html>

   -  The client sends the certificate for access.

      .. code-block::

         curl --cacert ./ca.crt --cert ./client.crt --key ./client.key https://example.com

      Expected output:

      .. code-block::

         <!DOCTYPE html>
         <html>
         <head>
         <title>Welcome to nginx!</title>
         <style>
         body {
             width: 35em;
             margin: 0 auto;
             font-family: Tahoma, Verdana, Arial, sans-serif;
         }
         </style>
         </head>
         <body>
         <h1>Welcome to nginx!</h1>
         <p>If you see this page, the nginx web server is successfully installed and
         working. Further configuration is required.</p>

         <p>For online documentation and support please refer to
         <a href="http://nginx.org/">nginx.org</a>.<br/>
         Commercial support is available at
         <a href="http://nginx.com/">nginx.com</a>.</p>

         <p><em>Thank you for using nginx.</em></p>
         </body>
         </html>

.. _cce_10_0699__section2048022342319:

Domain Name Regularization
--------------------------

Nginx Ingress allows you to configure the **nginx.ingress.kubernetes.io/server-alias** annotation to configure regular expressions for domain names.

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   .. code-block::

      vi ingress-test.yaml

   For example, the regular expression **~^www\\.\\d+\\.example\\.com$,abc.example.com** indicates that you can access the ingress using **www.**\ *{One or more digits}*\ **.example.com** and **abc.example.com**.

   -  **For clusters of v1.23 or later:**

      .. code-block::

         apiVersion: networking.k8s.io/v1
         kind: Ingress
         metadata:
           annotations:
             nginx.ingress.kubernetes.io/server-alias: '~^www\.\d+\.example\.com$,abc.example.com'
           name: ingress-test
           namespace: default
         spec:
           rules:
           - host: example.com
             http:
               paths:
               - backend:
                   service:
                     name: nginx-93244  # Replace it with the name of your target Service.
                     port:
                       number: 80  # Replace it with the port of your target Service.
                 path: /
                 pathType: ImplementationSpecific
           ingressClassName: nginx

   -  **For clusters of v1.21 or earlier:**

      .. code-block::

         apiVersion: networking.k8s.io/v1beta1
         kind: Ingress
         metadata:
           annotations:
             kubernetes.io/ingress.class: nginx
             nginx.ingress.kubernetes.io/server-alias: '~^www\.\d+\.example\.com$,abc.example.com'
           name: ingress-test
           namespace: default
         spec:
           rules:
           - host: example.com
             http:
               paths:
               - path: '/'
                 backend:
                   serviceName: nginx-test  # Replace it with the name of your target Service.
                   servicePort: 80  # Replace it with the port of your target Service.

#. Run the following command to create an ingress:

   .. code-block::

      kubectl create -f ingress-test.yaml

   Expected output:

   .. code-block::

      ingress.networking.k8s.io/ingress-test created

#. Check the NGINX Ingress Controller configuration.

   a. Run the following command to check the NGINX Ingress Controller pods:

      .. code-block::

         kubectl get pods -n kube-system | grep nginx-ingress-controller

      Expected output:

      .. code-block::

         cceaddon-nginx-ingress-controller-68d7bcc67-dxxxx        1/1     Running   0          18h
         cceaddon-nginx-ingress-controller-68d7bcc67-cxxxx        1/1     Running   0          18h

   b. Run the following command to check the NGINX Ingress Controller configuration:

      .. code-block::

         kubectl exec -n kube-system cceaddon-nginx-ingress-controller-68d7bcc67-dxxxx -- bash -c 'cat /etc/nginx/nginx.conf | grep -C3 "example.com"'

      Expected output:

      .. code-block::

                  ## start server example.com
                  server {
                           server_name example.com abc.example.com ~^www\.\d+\.example\.com$ ;

                           listen 80  ;
                           listen [::]:80  ;
         --
                           }

                  }
                  ## end server example.com

#. Run the following command to obtain the IP address of the ingress:

   .. code-block::

      kubectl get ingress

   Expected output:

   .. code-block::

      NAME         CLASS   HOSTS         ADDRESS      PORTS   AGE
      nginx-test   nginx   example.com   10.3.xx.xx   80      14m

#. Use different rules to test service access.

   -  Run the following command to access the service through **Host: example.com**:

      .. code-block::

         curl -H "Host: example.com" 10.3.xx.xx/

      It is expected that the web page can be accessed properly.

   -  Run the following command to access the service through **Host: www.123.example.com**:

      .. code-block::

         curl -H "Host: www.123.example.com" 10.3.xx.xx/

      It is expected that the web page can be accessed properly.

   -  Run the following command to access the service through **Host: www.321.example.com**:

      .. code-block::

         curl -H "Host: www.321.example.com" 10.3.xx.xx/

      It is expected that the web page can be accessed properly.

.. _cce_10_0699__section20346153710463:

Helpful Links
-------------

For details about annotation parameters supported by Nginx Ingress, see `Annotations <https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/>`__.
