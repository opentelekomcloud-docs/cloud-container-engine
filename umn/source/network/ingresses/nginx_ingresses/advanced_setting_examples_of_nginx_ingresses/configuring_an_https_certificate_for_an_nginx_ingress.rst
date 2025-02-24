:original_name: cce_10_0693.html

.. _cce_10_0693:

Configuring an HTTPS Certificate for an Nginx Ingress
=====================================================

HTTPS certificates can be configured for ingresses to provide security services.

#. Use kubectl to access the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Ingress supports two TLS secret types: kubernetes.io/tls and IngressTLS. IngressTLS is used as an example. For details, see :ref:`Creating a Secret <cce_10_0153>`. For details about examples of the kubernetes.io/tls secret and its description, see `TLS secrets <https://kubernetes.io/docs/concepts/configuration/secret/#tls-secret>`__.

   Create a YAML file named **ingress-test-secret.yaml**. The file name can be customized.

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

   Check the created secret.

   .. code-block::

      kubectl get secrets

   If information similar to the following is displayed, the secret has been created:

   .. code-block::

      NAME                         TYPE                                  DATA      AGE
      ingress-test-secret          IngressTLS                            2         13s

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   .. code-block::

      vi ingress-test.yaml

   **For clusters of v1.23 or later:**

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ingress-test
        namespace: default
      spec:
        tls:
        - hosts:
          - foo.bar.com
          secretName: ingress-test-secret  # Replace it with your TLS key certificate.
        rules:
          - host: foo.bar.com
            http:
              paths:
                - path: /
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
        annotations:
          kubernetes.io/ingress.class: nginx
      spec:
        tls:
        - hosts:
          - foo.bar.com
          secretName: ingress-test-secret   # Replace it with your TLS key certificate.
        rules:
        - host: foo.bar.com
          http:
            paths:
            - path: '/'
              backend:
                serviceName: <your_service_name>  # Replace it with the name of your target Service.
                servicePort: <your_service_port>  # Replace it with the port number of your target Service.
        ingressClassName: nginx

#. Create an ingress.

   .. code-block::

      kubectl create -f ingress-test.yaml

   If information similar to the following is displayed, the ingress has been created:

   .. code-block::

      ingress/ingress-test created

#. Check the created ingress.

   .. code-block::

      kubectl get ingress

   If information similar to the following is displayed, the ingress has been created:

   .. code-block::

      NAME          CLASS   HOSTS     ADDRESS          PORTS   AGE
      ingress-test  nginx   *         121.**.**.**     80      10s

#. Enter **https://121.**.**.*\*:443** in the address box of the browser to access the workload (for example, :ref:`Nginx workload <cce_10_0047__section155246177178>`).

   **121.**.**.*\*** indicates the IP address of the unified load balancer.
