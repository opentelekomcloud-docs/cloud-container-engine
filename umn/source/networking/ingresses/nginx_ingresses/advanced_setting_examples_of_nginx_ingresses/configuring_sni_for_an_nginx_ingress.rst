:original_name: cce_10_1076.html

.. _cce_10_1076:

Configuring SNI for an Nginx Ingress
====================================

A Server Name Indication (SNI) certificate is an extended server certificate that allows one IP:port pair to support multiple domain names externally. It uses different security certificates depending on the client's requested domain to secure HTTPS connections.

Configure SNI by setting both the default server certificate and the SNI certificate associated with the specific domain name. In the SSL handshake, the client provides the requested domain name. The load balancer then checks for the matching SNI certificate based on the domain name. It returns this certificate if available. Otherwise, it uses the default server certificate.

Prerequisites
-------------

-  An available workload has been deployed in the cluster for external access. If no workload is available, deploy a workload by referring to :ref:`Creating a Deployment <cce_10_0047>`, :ref:`Creating a StatefulSet <cce_10_0048>`, or :ref:`Creating a DaemonSet <cce_10_0216>`.

-  The workload uses a ClusterIP or NodePort Service for external access.

-  You have obtained a trusted server certificate and an SNI certificate from your certificate provider.

-  An IngressTLS or kubernetes.io/tls secret is available for storing each certificate. For details, see :ref:`Creating a Secret <cce_10_0153>`.

   In this example, the secret name of the server certificate is **test**, and that of the SNI certificate is **example-test**.

Using the Console
-----------------

.. note::

   -  The **SNI** option is available only when HTTPS is used.

   -  Only one domain name can be specified for each SNI certificate. Wildcard-domain certificates are supported.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Services & Ingresses** in the navigation pane, click the **Ingresses** tab, and click **Create Ingress** in the upper right corner.
#. Configure ingress parameters.

   .. note::

      This example explains only key parameters for configuring SNI certificates. You can configure other parameters as needed. For details, see :ref:`Creating an Nginx Ingress on the Console <cce_10_0390>`.

   .. table:: **Table 1** Key parameters

      +-------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------+
      | Parameter               | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Example Value                             |
      +=========================+==============================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================+===========================================+
      | Name                    | Your custom ingress name.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | ingress-test                              |
      +-------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------+
      | Interconnect with Nginx | If enabled, created ingresses will interconnect with the NGINX Ingress controller.                                                                                                                                                                                                                                                                                                                                                                                                                           | Enabled                                   |
      +-------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------+
      | Controller Name         | The name of the NGINX Ingress controller installed in the cluster. The default value is **nginx**. You can :ref:`install multiple NGINX Ingress controllers <cce_10_0034__section1057051834311>` and customize their names as needed.                                                                                                                                                                                                                                                                        | nginx                                     |
      +-------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------+
      | TLS                     | -  **Frontend Protocol**: The options are **HTTP** and **HTTPS**. By default, NGINX Ingress Controller listens on ports 80 for HTTP and 443 for HTTPS. In this use case, select **HTTPS**.                                                                                                                                                                                                                                                                                                                   | -  **Frontend Protocol**: **HTTPS**       |
      |                         | -  **Certificate Source**: the source of a certificate for encrypting and authenticating HTTPS data transmission.                                                                                                                                                                                                                                                                                                                                                                                            | -  **Certificate Source**: **TLS secret** |
      |                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | -  **Server Certificate**: **test**       |
      |                         |    -  If you select **TLS secret**, you need to further specify the **Server Certificate**. Create an IngressTLS or kubernetes.io/tls secret for storing each certificate. For details, see :ref:`Creating a Secret <cce_10_0153>`.                                                                                                                                                                                                                                                                          | -  **SNI**:                               |
      |                         |    -  If you select **Default certificate**, NGINX Ingress Controller will use its default certificate for encryption and authentication. You can override this default while installing :ref:`NGINX Ingress Controller <cce_10_0034>`. Otherwise, the controller's built-in certificate will be used                                                                                                                                                                                                        |                                           |
      |                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    -  **Domain Name**: **example.com**    |
      |                         | -  **SNI**: an extended protocol of TLS. SNI enables several TLS domain names to share one IP:port pair while using different security certificates for each domain name. With SNI enabled, the client can send the requested domain name during the TLS handshake. The load balancer uses the domain name from the TLS request to find the matching certificate. It returns that certificate if found. Otherwise, it provides the default server certificate. Both certificates are used for authorization. |    -  **Certificate**: **example-test**   |
      +-------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------+
      | Forwarding Policy       | -  **Domain Name**: the one to be accessed. If it is left blank, the ingress can be accessed through an IP address. Ensure that the domain name has been registered and licensed. Once a forwarding policy uses it, only that domain name will be accepted for access.                                                                                                                                                                                                                                       | -  **Domain Name**: **example.com**       |
      |                         | -  **Path Matching**: If **Default** is selected, **Prefix match** will be used. You can also select Exact match as needed.                                                                                                                                                                                                                                                                                                                                                                                  | -  **Path Matching**: **Default**         |
      |                         | -  **Path**: comes from a backend application for external access. It must work in the backend application. Otherwise, forwarding will not take effect.                                                                                                                                                                                                                                                                                                                                                      | -  **Path**: **/**                        |
      |                         | -  **Destination Service**: Select an existing Service. Only Services that meet the requirements are automatically displayed in the Service list.                                                                                                                                                                                                                                                                                                                                                            | -  **Destination Service**: **nginx**     |
      |                         | -  **Destination Service Port**: Select the access port of the destination Service.                                                                                                                                                                                                                                                                                                                                                                                                                          | -  **Destination Service Port**: **80**   |
      +-------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------+

#. Click **OK**.

Using kubectl
-------------

.. note::

   -  The **SNI** option is available only when HTTPS is used.

   -  Only one domain name can be specified for each SNI certificate. Wildcard-domain certificates are supported.

The SNI certificate **example-test** is used as an example. The domain name must match the certificate exactly.

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   .. code-block::

      vi ingress-test.yaml

   An example YAML file of an ingress associated with an automatically created load balancer is as follows:

   **For clusters of v1.23 or later:**

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ingress-test
        namespace: default
      spec:
        tls:
        - secretName: test # The server certificate must be specified. If it is not specified, the default certificate in NGINX Ingress Controller will be used.
        - hosts:
          - example.com  # The domain name specified in the SNI certificate
          secretName: example-test  # Replace it with your SNI certificate.
        rules:
          - host: example.com
            http:
              paths:
                - path: /
                  backend:
                    service:
                      name: <your_service_name>  # Replace it with your target Service name.
                      port:
                        number: <your_service_port>  # Replace it with your target Service port.
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
        annotations:
          kubernetes.io/ingress.class: nginx
      spec:
        tls:
        - secretName: test # The server certificate must be specified. If it is not specified, the default certificate in NGINX Ingress Controller will be used.
        - hosts:
          - example.com  # The domain name specified in the SNI certificate
          secretName: example-test   # Replace it with your SNI certificate.
        rules:
        - host: example.com
          http:
            paths:
            - path: '/'
              backend:
                serviceName: <your_service_name>  # Replace it with your target Service name.
                servicePort: <your_service_port>  # Replace it with your target Service port.

#. Create an ingress.

   .. code-block::

      kubectl create -f ingress-test.yaml

   If information similar to the following is displayed, the ingress has been created:

   .. code-block::

      ingress/ingress-test created

#. Check the created ingress.

   .. code-block::

      kubectl get ingress

   If information similar to the following is displayed, the ingress is successfully created:

   .. code-block::

      NAME           CLASS  HOSTS          ADDRESS          PORTS   AGE
      ingress-test   cce    example.com    121.**.**.**     80,443  10s

#. Use HTTPS to access the ingress. *${ELB_IP}* is the IP address accessed by the target ingress.

   .. code-block::

      curl -H "Host:example.com" -k https://${ELB_IP}:443

   If the ingress can be accessed, the certificate is correctly configured.
