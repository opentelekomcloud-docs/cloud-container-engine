:original_name: cce_10_0687.html

.. _cce_10_0687:

Configuring an HTTPS Certificate for a LoadBalancer Ingress
===========================================================

Ingresses support SSL or TLS certificates, allowing you to secure your Services with HTTPS.

You are allowed to use either of the following ways to configure an ingress certificate in a cluster:

-  Using a cluster TLS certificate: First import a certificate into a secret. CCE automatically provisions the certificate on the ELB with a name prefixed by **k8s_plb_default**. CCE-managed certificates **cannot be modified or deleted through the ELB console**. To modify the certificate, update the secret where the certificate is imported to on CCE.
-  Using a certificate created on the ELB console: You are allowed to directly use certificates created on the ELB console. There is no need to manually configure the cluster secrets, and you can modify the certificates on the ELB console.

.. note::

   If multiple ingresses share the same external port on a load balancer, you are advised to use the same certificate and security policy for these ingresses. Otherwise, the configuration of the first created ingress will take precedence. For details, see :ref:`Configuring a Same Port for Multiple Ingresses <cce_10_0687__section1768932010462>`.

Prerequisites
-------------

-  An available workload has been deployed in the cluster for external access. If no workload is available, deploy a workload by referring to :ref:`Creating a Deployment <cce_10_0047>`, :ref:`Creating a StatefulSet <cce_10_0048>`, or :ref:`Creating a DaemonSet <cce_10_0216>`.
-  A Service for external access has been configured for the workload. :ref:`Services Supported by LoadBalancer Ingresses <cce_10_0094__section3565202819276>` lists the Service types supported by LoadBalancer ingresses.
-  You can obtain a trusted certificate from a certificate provider.

Using the CCE Console
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Services and Ingresses** in the navigation pane, click the **Ingresses** tab, and click **Create Ingress** in the upper right corner.
#. Configure ingress parameters.

   .. note::

      This example explains only key parameters for configuring HTTPS certificates. You can configure other parameters as required. For details, see :ref:`Creating a LoadBalancer Ingress on the Console <cce_10_0251>`.

   .. table:: **Table 1** Key parameters

      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Parameter             | Description                                                                                                                                                                                                                                                                | Example                                                      |
      +=======================+============================================================================================================================================================================================================================================================================+==============================================================+
      | Name                  | Enter an ingress name.                                                                                                                                                                                                                                                     | ingress-test                                                 |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Load Balancer         | Select a load balancer to be associated with the ingress or automatically create a load balancer.                                                                                                                                                                          | Shared                                                       |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Listener              | -  **Frontend Protocol**: Select **HTTPS** when configuring a certificate for an ingress.                                                                                                                                                                                  | -  Frontend Protocol: HTTPS                                  |
      |                       |                                                                                                                                                                                                                                                                            | -  External Port: 443                                        |
      |                       | -  **External Port**: specifies the port of the load balancer listener. The default HTTPS port is 443.                                                                                                                                                                     | -  Certificate Source: TLS secret                            |
      |                       |                                                                                                                                                                                                                                                                            | -  Server Certificate: test                                  |
      |                       | -  **Certificate Source**: Select **TLS secret**.                                                                                                                                                                                                                          |                                                              |
      |                       |                                                                                                                                                                                                                                                                            |                                                              |
      |                       | -  **Server Certificate**: **kubernetes.io/tls** and **IngressTLS** are supported.                                                                                                                                                                                         |                                                              |
      |                       |                                                                                                                                                                                                                                                                            |                                                              |
      |                       |    If no certificate is available, you can create a TLS certificate. For details about parameter settings, see :ref:`Creating a Secret <cce_10_0153>`.                                                                                                                     |                                                              |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Forwarding Policy     | -  **Domain Name**: Enter an actual domain name to be accessed. If it is left blank, the ingress can be accessed through an IP address. The domain name must be registered and filed. Once used by a forwarding policy, only that domain name will be accepted for access. | -  Domain Name: You do not need to configure this parameter. |
      |                       | -  **Path Matching Rule**: Choose **Prefix match**, **Exact match**, or **RegEx match**.                                                                                                                                                                                   | -  Path Matching Rule: Prefix match                          |
      |                       | -  **Path**: comes from a backend application for external access. It must work in the backend application. Otherwise, forwarding will not take effect.                                                                                                                    | -  Path: /                                                   |
      |                       | -  **Destination Service**: Select an existing Service. Only Services that meet the requirements are automatically displayed in the Service list.                                                                                                                          | -  Destination Service: nginx                                |
      |                       | -  **Destination Service Port**: Select the access port of the destination Service.                                                                                                                                                                                        | -  Destination Service Port: 80                              |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+

#. Click **OK**.

Using kubectl
-------------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Ingress supports two TLS secret types: kubernetes.io/tls and IngressTLS. IngressTLS is used as an example. For details, see :ref:`Creating a Secret <cce_10_0153>`. For details about examples of the kubernetes.io/tls secret and its description, see `TLS secrets <https://kubernetes.io/docs/concepts/configuration/secret/#tls-secret>`__.

   Create a YAML file named **ingress-test-secret.yaml**. You can change the file name if you want to.

   .. code-block::

      vi ingress-test-secret.yaml

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

   .. code-block::

      kubectl create -f ingress-test-secret.yaml

   If information similar to the following is displayed, the secret has been created:

   .. code-block::

      secret/ingress-test-secret created

#. Check the created secret.

   .. code-block::

      kubectl get secrets

   If information similar to the following is displayed, the secret has been created:

   .. code-block::

      NAME                         TYPE                                  DATA      AGE
      ingress-test-secret          IngressTLS                            2         13s

#. Create a YAML file named **ingress-test.yaml**. You can change the file name if you want to.

   .. code-block::

      vi ingress-test.yaml

   .. note::

      Default security policy (kubernetes.io/elb.tls-ciphers-policy) is supported only in clusters v1.17.17 or later.

   An example YAML file of an ingress associated with an automatically created load balancer is as follows:

   **For clusters v1.23 or later:**

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
                "eip_type": "5_bgp", # EIP
                "available_zone": [
                    "eu-de-01"
                ], # The AZ where the load balancer is located
                "elb_virsubnet_ids":["b4bf8152-6c36-4c3b-9f74-2229f8e640c9"],  # Subnet network ID
                "l7_flavor_name": "L7_flavor.elb.s1.small"   # Layer 7 load balancer flavor name
             }'
          kubernetes.io/elb.tls-ciphers-policy: tls-1-2
      spec:
        tls:
        - secretName: ingress-test-secret # Server certificate name
        rules:
        - host: ''
          http:
            paths:
            - path: '/'
              backend:
                service:
                  name: <your_service_name>  # Replace it with your target Service name.
                  port:
                    number: 80             # Replace it with your target Service port.
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
              pathType: ImplementationSpecific
        ingressClassName: cce

   **For clusters v1.21 or earlier:**

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
                "eip_type": "5_bgp",    # EIP
                "available_zone": [
                    "eu-de-01"
                ],   # The AZ where the load balancer is located
                "elb_virsubnet_ids":["b4bf8152-6c36-4c3b-9f74-2229f8e640c9"],   # Subnet network ID
                "l7_flavor_name": "L7_flavor.elb.s1.small"       # Layer 7 load balancer flavor name
             }'
          kubernetes.io/elb.tls-ciphers-policy: tls-1-2
      spec:
        tls:
        - secretName: ingress-test-secret # Server certificate name
        rules:
        - host: ''
          http:
            paths:
            - path: '/'
              backend:
                serviceName: <your_service_name>  # Replace it with your target Service name.
                servicePort: 80      # Replace it with your target Service port.
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

   .. table:: **Table 2** Key parameters

      +--------------------------------------+-----------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                            | Mandatory       | Type             | Description                                                                                                                                        |
      +======================================+=================+==================+====================================================================================================================================================+
      | kubernetes.io/elb.tls-ciphers-policy | No              | String           | The default value is **tls-1-2**, which is the default security policy used by the listener and takes effect only when HTTPS is used.              |
      |                                      |                 |                  |                                                                                                                                                    |
      |                                      |                 |                  | Options:                                                                                                                                           |
      |                                      |                 |                  |                                                                                                                                                    |
      |                                      |                 |                  | -  **tls-1-0**                                                                                                                                     |
      |                                      |                 |                  | -  **tls-1-1**                                                                                                                                     |
      |                                      |                 |                  | -  **tls-1-2**                                                                                                                                     |
      |                                      |                 |                  | -  **tls-1-2-strict**                                                                                                                              |
      |                                      |                 |                  | -  **tls-1-0-with-1-3** (dedicated load balancer)                                                                                                  |
      |                                      |                 |                  | -  **tls-1-2-fs** (dedicated load balancer)                                                                                                        |
      |                                      |                 |                  | -  **tls-1-2-fs-with-1-3** (dedicated load balancer)                                                                                               |
      |                                      |                 |                  |                                                                                                                                                    |
      |                                      |                 |                  | For details of cipher suites for each security policy, see :ref:`Table 3 <cce_10_0687__table9419191416246>`.                                       |
      +--------------------------------------+-----------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
      | tls                                  | No              | Array of strings | When HTTPS is used, this parameter must be added to specify the secret certificate.                                                                |
      |                                      |                 |                  |                                                                                                                                                    |
      |                                      |                 |                  | Multiple independent domain names and certificates can be added. For details, see :ref:`Configuring SNI for a LoadBalancer Ingress <cce_10_0688>`. |
      +--------------------------------------+-----------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
      | secretName                           | No              | String           | This parameter is mandatory if HTTPS is used. Set this parameter to the name of the created secret.                                                |
      +--------------------------------------+-----------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0687__table9419191416246:

   .. table:: **Table 3** **tls_ciphers_policy** parameters

      +-----------------------------------------------+-----------------------+-------------------------------+
      | Security Policy                               | TLS Version           | Cipher Suite                  |
      +===============================================+=======================+===============================+
      | **tls-1-0**                                   | TLS 1.2               | ECDHE-RSA-AES256-GCM-SHA384   |
      |                                               |                       |                               |
      |                                               | TLS 1.1               | ECDHE-RSA-AES128-GCM-SHA256   |
      |                                               |                       |                               |
      |                                               | TLS 1.0               | ECDHE-ECDSA-AES256-GCM-SHA384 |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES128-GCM-SHA256 |
      |                                               |                       |                               |
      |                                               |                       | AES128-GCM-SHA256             |
      |                                               |                       |                               |
      |                                               |                       | AES256-GCM-SHA384             |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES128-SHA256     |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-RSA-AES128-SHA256       |
      |                                               |                       |                               |
      |                                               |                       | AES128-SHA256                 |
      |                                               |                       |                               |
      |                                               |                       | AES256-SHA256                 |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES256-SHA384     |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-RSA-AES256-SHA384       |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES128-SHA        |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-RSA-AES128-SHA          |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-RSA-AES256-SHA          |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES256-SHA        |
      |                                               |                       |                               |
      |                                               |                       | AES128-SHA                    |
      |                                               |                       |                               |
      |                                               |                       | AES256-SHA                    |
      +-----------------------------------------------+-----------------------+-------------------------------+
      | **tls-1-1**                                   | TLS 1.2               |                               |
      |                                               |                       |                               |
      |                                               | TLS 1.1               |                               |
      +-----------------------------------------------+-----------------------+-------------------------------+
      | **tls-1-2**                                   | TLS 1.2               |                               |
      +-----------------------------------------------+-----------------------+-------------------------------+
      | tls-1-2-strict                                | TLS 1.2               | ECDHE-RSA-AES256-GCM-SHA384   |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-RSA-AES128-GCM-SHA256   |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES256-GCM-SHA384 |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES128-GCM-SHA256 |
      |                                               |                       |                               |
      |                                               |                       | AES128-GCM-SHA256             |
      |                                               |                       |                               |
      |                                               |                       | AES256-GCM-SHA384             |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES128-SHA256     |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-RSA-AES128-SHA256       |
      |                                               |                       |                               |
      |                                               |                       | AES128-SHA256                 |
      |                                               |                       |                               |
      |                                               |                       | AES256-SHA256                 |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES256-SHA384     |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-RSA-AES256-SHA384       |
      +-----------------------------------------------+-----------------------+-------------------------------+
      | tls-1-0-with-1-3 (dedicated load balancer)    | TLS 1.3               | ECDHE-RSA-AES256-GCM-SHA384   |
      |                                               |                       |                               |
      |                                               | TLS 1.2               | ECDHE-RSA-AES128-GCM-SHA256   |
      |                                               |                       |                               |
      |                                               | TLS 1.1               | ECDHE-ECDSA-AES256-GCM-SHA384 |
      |                                               |                       |                               |
      |                                               | TLS 1.0               | ECDHE-ECDSA-AES128-GCM-SHA256 |
      |                                               |                       |                               |
      |                                               |                       | AES128-GCM-SHA256             |
      |                                               |                       |                               |
      |                                               |                       | AES256-GCM-SHA384             |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES128-SHA256     |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-RSA-AES128-SHA256       |
      |                                               |                       |                               |
      |                                               |                       | AES128-SHA256                 |
      |                                               |                       |                               |
      |                                               |                       | AES256-SHA256                 |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES256-SHA384     |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-RSA-AES256-SHA384       |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES128-SHA        |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-RSA-AES128-SHA          |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-RSA-AES256-SHA          |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES256-SHA        |
      |                                               |                       |                               |
      |                                               |                       | AES128-SHA                    |
      |                                               |                       |                               |
      |                                               |                       | AES256-SHA                    |
      |                                               |                       |                               |
      |                                               |                       | TLS_AES_128_GCM_SHA256        |
      |                                               |                       |                               |
      |                                               |                       | TLS_AES_256_GCM_SHA384        |
      |                                               |                       |                               |
      |                                               |                       | TLS_CHACHA20_POLY1305_SHA256  |
      |                                               |                       |                               |
      |                                               |                       | TLS_AES_128_CCM_SHA256        |
      |                                               |                       |                               |
      |                                               |                       | TLS_AES_128_CCM_8_SHA256      |
      +-----------------------------------------------+-----------------------+-------------------------------+
      | tls-1-2-fs (dedicated load balancer)          | TLS 1.3               | ECDHE-RSA-AES256-GCM-SHA384   |
      |                                               |                       |                               |
      |                                               | TLS 1.2               | ECDHE-RSA-AES128-GCM-SHA256   |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES256-GCM-SHA384 |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES128-GCM-SHA256 |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES128-SHA256     |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-RSA-AES128-SHA256       |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES256-SHA384     |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-RSA-AES256-SHA384       |
      |                                               |                       |                               |
      |                                               |                       | TLS_AES_128_GCM_SHA256        |
      |                                               |                       |                               |
      |                                               |                       | TLS_AES_256_GCM_SHA384        |
      |                                               |                       |                               |
      |                                               |                       | TLS_CHACHA20_POLY1305_SHA256  |
      |                                               |                       |                               |
      |                                               |                       | TLS_AES_128_CCM_SHA256        |
      |                                               |                       |                               |
      |                                               |                       | TLS_AES_128_CCM_8_SHA256      |
      +-----------------------------------------------+-----------------------+-------------------------------+
      | tls-1-2-fs-with-1-3 (dedicated load balancer) | TLS 1.3               | ECDHE-RSA-AES256-GCM-SHA384   |
      |                                               |                       |                               |
      |                                               | TLS 1.2               | ECDHE-RSA-AES128-GCM-SHA256   |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES256-GCM-SHA384 |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES128-GCM-SHA256 |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES128-SHA256     |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-RSA-AES128-SHA256       |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-ECDSA-AES256-SHA384     |
      |                                               |                       |                               |
      |                                               |                       | ECDHE-RSA-AES256-SHA384       |
      |                                               |                       |                               |
      |                                               |                       | TLS_AES_128_GCM_SHA256        |
      |                                               |                       |                               |
      |                                               |                       | TLS_AES_256_GCM_SHA384        |
      |                                               |                       |                               |
      |                                               |                       | TLS_CHACHA20_POLY1305_SHA256  |
      |                                               |                       |                               |
      |                                               |                       | TLS_AES_128_CCM_SHA256        |
      |                                               |                       |                               |
      |                                               |                       | TLS_AES_128_CCM_8_SHA256      |
      +-----------------------------------------------+-----------------------+-------------------------------+

#. Create an ingress.

   .. code-block::

      kubectl create -f ingress-test.yaml

   If information similar to the following is displayed, the ingress has been created:

   .. code-block::

      ingress.networking.k8s.io/ingress-test created

#. Check the created ingress.

   .. code-block::

      kubectl get ingress

   If information similar to the following is displayed, the ingress has been created:

   .. code-block::

      NAME          CLASS    HOSTS     ADDRESS          PORTS   AGE
      ingress-test  cce      *         121.**.**.**     80,443  10s

#. Enter **https://121.**.**.*\*:443** in the address bar of the browser to access the workload (for example, :ref:`Nginx workload <cce_10_0047__section155246177178>`).

   **121.**.**.*\*** indicates the IP address of the unified load balancer.

Using the CCE Console to Configure a Certificate Created on the ELB Console
---------------------------------------------------------------------------

.. note::

   -  If both an ELB certificate and a TLS certificate are specified for the same ingress, the ingress will use the ELB certificate.
   -  CCE does not check whether an ELB certificate is valid. It only checks whether the certificate is present.
   -  Only ingresses in clusters v1.19.16-r2, v1.21.5-r0, v1.23.3-r0, or later support ELB certificates.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Services and Ingresses** in the navigation pane, click the **Ingresses** tab, and click **Create Ingress** in the upper right corner.
#. Configure ingress parameters.

   .. note::

      This example explains only key parameters for configuring HTTPS certificates. You can configure other parameters as required. For details, see :ref:`Creating a LoadBalancer Ingress on the Console <cce_10_0251>`.

   .. table:: **Table 4** Key parameters

      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Parameter             | Description                                                                                                                                                                                                                                                                | Example                                                      |
      +=======================+============================================================================================================================================================================================================================================================================+==============================================================+
      | Name                  | Enter an ingress name.                                                                                                                                                                                                                                                     | ingress-test                                                 |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Load Balancer         | Select a load balancer to be associated with the ingress or automatically create a load balancer.                                                                                                                                                                          | Shared                                                       |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Listener              | -  **Frontend Protocol**: Select **HTTPS**.                                                                                                                                                                                                                                | -  Frontend Protocol: HTTPS                                  |
      |                       |                                                                                                                                                                                                                                                                            | -  External Port: 443                                        |
      |                       | -  **External Port**: specifies the port of the load balancer listener. The default HTTPS port is 443.                                                                                                                                                                     | -  Certificate Source: ELB server certificate                |
      |                       |                                                                                                                                                                                                                                                                            | -  Server Certificate: cert-test                             |
      |                       | -  **Certificate Source**: Select **ELB server certificate**.                                                                                                                                                                                                              |                                                              |
      |                       |                                                                                                                                                                                                                                                                            |                                                              |
      |                       | -  **Server Certificate**: Use a certificate created on ELB.                                                                                                                                                                                                               |                                                              |
      |                       |                                                                                                                                                                                                                                                                            |                                                              |
      |                       |    If no certificate is available, go to the ELB console and create one.                                                                                                                                                                                                   |                                                              |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Forwarding Policy     | -  **Domain Name**: Enter an actual domain name to be accessed. If it is left blank, the ingress can be accessed through an IP address. The domain name must be registered and filed. Once used by a forwarding policy, only that domain name will be accepted for access. | -  Domain Name: You do not need to configure this parameter. |
      |                       | -  **Path Matching Rule**: Choose **Prefix match**, **Exact match**, or **RegEx match**.                                                                                                                                                                                   | -  Path Matching Rule: Prefix match                          |
      |                       | -  **Path**: comes from a backend application for external access. It must work in the backend application. Otherwise, forwarding will not take effect.                                                                                                                    | -  Path: /                                                   |
      |                       | -  **Destination Service**: Select an existing Service. Only Services that meet the requirements are automatically displayed in the Service list.                                                                                                                          | -  Destination Service: nginx                                |
      |                       | -  **Destination Service Port**: Select the access port of the destination Service.                                                                                                                                                                                        | -  Destination Service Port: 80                              |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+

#. Click **OK**.

Using kubectl to Configure a Certificate Created on the ELB Console
-------------------------------------------------------------------

To use an ELB certificate, you can specify the **kubernetes.io/elb.tls-certificate-ids** annotation.

.. note::

   -  If both an ELB certificate and a TLS certificate are specified for the same ingress, the ingress will use the ELB certificate.
   -  CCE does not check whether an ELB certificate is valid. It only checks whether the certificate is present.
   -  Only ingresses in clusters v1.19.16-r2, v1.21.5-r0, v1.23.3-r0, or later support ELB certificates.

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file named **ingress-test.yaml**. You can change the file name if you want to.

   .. code-block::

      vi ingress-test.yaml

   An example YAML file of an ingress associated with an existing load balancer is as follows:

   **For clusters v1.23 or later:**

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ingress-test
        namespace: default
        annotations:
          kubernetes.io/elb.port: '443'               # The port on the interconnected load balancer
          kubernetes.io/elb.id: 0b9a6c4d-bd8b-45cc-bfc8-ff0f9da54e95    # ELB ID
          kubernetes.io/elb.class: union                 # Load balancer type
          kubernetes.io/elb.tls-ciphers-policy: tls-1-2    # Default security policy, which is tls-1-2 by default
          kubernetes.io/elb.tls-certificate-ids: 058cc023690d48a3867ad69dbe9cd6e5,b98382b1f01c473286653afd1ed9ab63  # ELB certificate IDs
      spec:
        rules:
          - host: ''
            http:
              paths:
                - path: '/'
                  backend:
                    service:
                      name: <your_service_name>  # Replace it with your target Service name.
                      port:
                    number: 80             # Replace it with your target Service port.
                  property:
                    ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
                  pathType: ImplementationSpecific
        ingressClassName: cce

   **For clusters v1.21 or earlier:**

   .. code-block::

      apiVersion: networking.k8s.io/v1beta1
      kind: Ingress
      metadata:
        name: ingress-test
        annotations:
          kubernetes.io/ingress.class: cce   # This parameter is mandatory when you create an ingress. The default value is cce, indicating that proprietary LoadBalancer ingresses are used.
          kubernetes.io/elb.port: '443'    # The port on the interconnected load balancer
          kubernetes.io/elb.id: 0b9a6c4d-bd8b-45cc-bfc8-ff0f9da54e95    # ELB ID
          kubernetes.io/elb.class: union     # Load balancer type
          kubernetes.io/elb.tls-ciphers-policy: tls-1-2    # Default security policy, which is tls-1-2 by default
          kubernetes.io/elb.tls-certificate-ids: 058cc023690d48a3867ad69dbe9cd6e5,b98382b1f01c473286653afd1ed9ab63  # ELB certificate IDs
      spec:
        rules:
        - host: ''
          http:
            paths:
            - path: '/'
              backend:
                serviceName: <your_service_name>  # Replace it with your target Service name.
                servicePort: 80     # Replace it with your target Service port.
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

   .. table:: **Table 5** Key parameters

      +---------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                             | Type                  | Description                                                                                                                                                                                                                                            |
      +=======================================+=======================+========================================================================================================================================================================================================================================================+
      | kubernetes.io/elb.tls-certificate-ids | String                | ELB certificate IDs, which are separated by comma (,). The list length is greater than or equal to 1. The first ID in the list is the server certificate, and the other IDs are SNI certificates in which a domain name must be contained.             |
      |                                       |                       |                                                                                                                                                                                                                                                        |
      |                                       |                       | If an SNI certificate cannot be found based on the domain name requested by the client, the server certificate will be returned by default.                                                                                                            |
      |                                       |                       |                                                                                                                                                                                                                                                        |
      |                                       |                       | To obtain the certificate, log in to the CCE console, choose **Service List** > **Networking** > **Elastic Load Balance**, and choose **Certificates** in the navigation pane. In the certificate list, copy the ID under the target certificate name. |
      +---------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Create an ingress.

   .. code-block::

      kubectl create -f ingress-test.yaml

   If information similar to the following is displayed, the ingress has been created:

   .. code-block::

      ingress.networking.k8s.io/ingress-test created

#. Check the created ingress.

   .. code-block::

      kubectl get ingress

   If information similar to the following is displayed, the ingress has been created:

   .. code-block::

      NAME          CLASS    HOSTS     ADDRESS          PORTS   AGE
      ingress-test  cce      *         121.**.**.**     80,443  10s

.. _cce_10_0687__section1768932010462:

Configuring a Same Port for Multiple Ingresses
----------------------------------------------

In a cluster, multiple ingresses can share a listener, allowing them to use the same port on a single load balancer. When two ingresses are set up with HTTPS certificates, the server certificate that is used will be based on the configuration of the earliest ingress.

Starting from v1.21.15-r0, v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.29.1-r0, or v1.28.4-r0, the YAML file of ingresses includes **annotation: kubernetes.io/elb.listener-master-ingress**. This annotation specifies the server certificate configured and used by ingresses.

For example, the configurations of two ingresses are as follows:

+-------------------------------------------+------------------+------------------+
| Ingress Name                              | ingress1         | ingress2         |
+-------------------------------------------+------------------+------------------+
| Namespace                                 | default          | default          |
+-------------------------------------------+------------------+------------------+
| Creation Time                             | 2024-04-01       | 2024-04-02       |
+-------------------------------------------+------------------+------------------+
| Protocol                                  | HTTPS            | HTTPS            |
+-------------------------------------------+------------------+------------------+
| Load Balancer                             | elb1             | elb1             |
+-------------------------------------------+------------------+------------------+
| Port                                      | 443              | 443              |
+-------------------------------------------+------------------+------------------+
| kubernetes.io/elb.listener-master-ingress | default/ingress1 | default/ingress1 |
+-------------------------------------------+------------------+------------------+

In the configurations of the two ingresses, **annotation: kubernetes.io/elb.listener-master-ingress** is present and the value is **default/ingress1**, indicating that the server certificates that take effect for the two ingresses are the server certificates configured for **ingress1** in the **default** namespace.

.. note::

   If ingresses in separate namespaces use the same listener and TLS certificates, the secrets associated with the TLS certificates may not be displayed normally for the later-created ingress due to namespace isolation.

To update a server certificate, modify the certificate in the **ingress1** configuration within the **default** namespace. Once the modification is made, the certificates used by both **ingress1** and **ingress2** will be updated accordingly.

To find the ingress with the active certificate configuration, run the following command:

.. code-block::

   kubectl get ingress -n ${namespace} ${ingress_name}  -oyaml | grep kubernetes.io/elb.listener-master-ingress
