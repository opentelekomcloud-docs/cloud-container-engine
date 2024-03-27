:original_name: cce_10_0687.html

.. _cce_10_0687:

Configuring HTTPS Certificates for ELB Ingresses
================================================

Ingress supports TLS certificate configuration and secures your Services with HTTPS.

Currently, you can use the TLS secret certificate configured in the cluster and the ELB certificate.

.. note::

   If HTTPS is enabled for the same port of the same load balancer of multiple ingresses, you must select the same certificate.

Using a TLS Secret Certificate
------------------------------

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Ingress supports two TLS secret types: kubernetes.io/tls and IngressTLS. IngressTLS is used as an example. For details, see :ref:`Creating a Secret <cce_10_0153>`. For details about examples of the kubernetes.io/tls secret and its description, see `TLS Secret <https://kubernetes.io/docs/concepts/configuration/secret/#tls-secret>`__.

   Run the following command to create a YAML file named **ingress-test-secret.yaml** (the file name can be customized):

   **vi ingress-test-secret.yaml**

   **The YAML file is configured as follows:**

   .. code-block::

      apiVersion: v1
      data:
        tls.crt: LS0******tLS0tCg==
        tls.key: LS0tL******0tLS0K
      kind: Secret
      metadata:
        annotations:
          description: test for ingressTLS secrets
        name: ingress-test-secret
        namespace: default
      type: IngressTLS

   .. note::

      In the preceding information, **tls.crt** and **tls.key** are only examples. Replace them with the actual files. The values of **tls.crt** and **tls.key** are Base64-encoded.

#. Create a secret.

   **kubectl create -f ingress-test-secret.yaml**

   If information similar to the following is displayed, the secret is being created:

   .. code-block::

      secret/ingress-test-secret created

   View the created secret.

   **kubectl get secrets**

   If information similar to the following is displayed, the secret has been created:

   .. code-block::

      NAME                         TYPE                                  DATA      AGE
      ingress-test-secret          IngressTLS                            2         13s

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   **vi ingress-test.yaml**

   .. note::

      Default security policy (kubernetes.io/elb.tls-ciphers-policy) is supported only in clusters of v1.17.17 or later.

   **The following uses the automatically created load balancer as an example. The YAML file is configured as follows:**

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
                "bandwidth_chargemode": "bandwidth",
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
        rules:
        - host: foo.bar.com
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
                "bandwidth_chargemode": "bandwidth",
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
        rules:
        - host: foo.bar.com
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

   .. table:: **Table 1** Key parameters

      +--------------------------------------+-----------------+------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                            | Mandatory       | Type             | Description                                                                                                                                                            |
      +======================================+=================+==================+========================================================================================================================================================================+
      | kubernetes.io/elb.tls-ciphers-policy | No              | String           | The default value is **tls-1-2**, which is the default security policy used by the listener and takes effect only when HTTPS is used.                                  |
      |                                      |                 |                  |                                                                                                                                                                        |
      |                                      |                 |                  | Options:                                                                                                                                                               |
      |                                      |                 |                  |                                                                                                                                                                        |
      |                                      |                 |                  | -  tls-1-0                                                                                                                                                             |
      |                                      |                 |                  | -  tls-1-1                                                                                                                                                             |
      |                                      |                 |                  | -  tls-1-2                                                                                                                                                             |
      |                                      |                 |                  | -  tls-1-2-strict                                                                                                                                                      |
      |                                      |                 |                  |                                                                                                                                                                        |
      |                                      |                 |                  | For details of cipher suites for each security policy, see :ref:`Table 2 <cce_10_0687__table9419191416246>`.                                                           |
      +--------------------------------------+-----------------+------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | tls                                  | No              | Array of strings | When HTTPS is used, this parameter must be added to specify the secret certificate.                                                                                    |
      |                                      |                 |                  |                                                                                                                                                                        |
      |                                      |                 |                  | Multiple independent domain names and certificates can be added. For details, see :ref:`Configuring the Server Name Indication (SNI) for ELB Ingresses <cce_10_0688>`. |
      +--------------------------------------+-----------------+------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | secretName                           | No              | String           | This parameter is mandatory if HTTPS is used. Set this parameter to the name of the created secret.                                                                    |
      +--------------------------------------+-----------------+------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0687__table9419191416246:

   .. table:: **Table 2** **tls_ciphers_policy** parameter description

      +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Security Policy       | TLS Version           | Cipher Suite                                                                                                                                                                                                                                                                                                                                                                                          |
      +=======================+=======================+=======================================================================================================================================================================================================================================================================================================================================================================================================+
      | tls-1-0               | TLS 1.2               | ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:AES128-SHA256:AES256-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES128-SHA:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:AES128-SHA:AES256-SHA |
      |                       |                       |                                                                                                                                                                                                                                                                                                                                                                                                       |
      |                       | TLS 1.1               |                                                                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                       |                                                                                                                                                                                                                                                                                                                                                                                                       |
      |                       | TLS 1.0               |                                                                                                                                                                                                                                                                                                                                                                                                       |
      +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | tls-1-1               | TLS 1.2               |                                                                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                       |                                                                                                                                                                                                                                                                                                                                                                                                       |
      |                       | TLS 1.1               |                                                                                                                                                                                                                                                                                                                                                                                                       |
      +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | tls-1-2               | TLS 1.2               |                                                                                                                                                                                                                                                                                                                                                                                                       |
      +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | tls-1-2-strict        | TLS 1.2               | ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:AES128-SHA256:AES256-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384                                                                                                               |
      +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Create an ingress.

   **kubectl create -f ingress-test.yaml**

   If information similar to the following is displayed, the ingress has been created.

   .. code-block::

      ingress/ingress-test created

   View the created ingress.

   **kubectl get ingress**

   If information similar to the following is displayed, the ingress has been created and the workload is accessible.

   .. code-block::

      NAME             HOSTS     ADDRESS          PORTS   AGE
      ingress-test     *         121.**.**.**     80      10s

#. Enter **https://121.**.**.*\*:443** in the address box of the browser to access the workload (for example, :ref:`Nginx workload <cce_10_0047__section155246177178>`).

   **121.**.**.*\*** indicates the IP address of the unified load balancer.

Using the ELB Certificate
-------------------------

To use the ELB certificate, you can specify the annotations **kubernetes.io/elb.tls-certificate-ids**.

.. note::

   #. If you specify both the IngressTLS certificate and the ELB certificate, the latter is used.
   #. CCE does not check whether the ELB certificate is valid. It only checks whether the certificate exists.
   #. Only clusters of v1.19.16-r2, v1.21.5-r0, v1.23.3-r0, or later support the ELB certificate.

**For clusters of v1.21 or earlier:**

.. code-block::

   apiVersion: networking.k8s.io/v1beta1
   kind: Ingress
   metadata:
     name: ingress-test
     annotations:
       kubernetes.io/ingress.class: cce
       kubernetes.io/elb.port: '443'
       kubernetes.io/elb.id: 0b9a6c4d-bd8b-45cc-bfc8-ff0f9da54e95
       kubernetes.io/elb.class: union
       kubernetes.io/elb.tls-certificate-ids: 058cc023690d48a3867ad69dbe9cd6e5,b98382b1f01c473286653afd1ed9ab63
   spec:
     rules:
     - host: ''
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
     namespace: default
     annotations:
       kubernetes.io/elb.port: '443'
       kubernetes.io/elb.id: 0b9a6c4d-bd8b-45cc-bfc8-ff0f9da54e95
       kubernetes.io/elb.class: union
       kubernetes.io/elb.tls-certificate-ids: 058cc023690d48a3867ad69dbe9cd6e5,b98382b1f01c473286653afd1ed9ab63
   spec:
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
