:original_name: cce_10_0896.html

.. _cce_10_0896:

Configuring a Custom Header Forwarding Policy for a LoadBalancer Ingress
========================================================================

Dedicated load balancer ingresses support custom header forwarding policies. You can configure different header key-value pairs to determine the backend Service to which data is forwarded.

Prerequisites
-------------

-  A CCE standard or Turbo cluster is available, and the cluster version meets the following requirements:

   -  v1.23: v1.23.16-r0 or later
   -  v1.25: v1.25.11-r0 or later
   -  v1.27: v1.27.8-r0 or later
   -  v1.28: v1.28.6-r0 or later
   -  v1.29: v1.29.2-r0 or later
   -  Other clusters of later versions

-  The cluster can be accessed using kubectl. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

Using kubectl
-------------

#. Use kubectl to access the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   .. code-block::

      vi ingress-test.yaml

   An example YAML file of an ingress created using an existing load balancer is as follows:

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ingress-test
        namespace: default
        annotations:
          kubernetes.io/elb.port: '80'
          kubernetes.io/elb.id: 08eab934-1636-4d90-a4cd-cb3fa4330411
          kubernetes.io/elb.class: performance  # A dedicated load balancer is required.
          # Path /a can be accessed only through Header key1=value1 or key1=value2. The accessed Service is svc-a:80.
          kubernetes.io/elb.headers.svc-a: |
            {
                "key": "key1",
                "values": [
                    "value1",
                    "value2"
                ]
            }
          # Path /b can be accessed only through Header key2=value1 or key2=value2. The accessed Service is svc-b:81.
          kubernetes.io/elb.headers.svc-b: |
            {
                "key": "key2",
                "values": [
                    "value1",
                    "value2"
                ]
            }
      spec:
        rules:
          - host: ''
            http:
              paths:
                - path: /a
                  backend:
                    service:
                      name: svc-a
                      port:
                        number: 80
                  property:
                    ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
                  pathType: ImplementationSpecific
                - path: /b
                  backend:
                    service:
                      name: svc-b
                      port:
                        number: 81
                  property:
                    ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
                  pathType: ImplementationSpecific
        ingressClassName: cce

   .. table:: **Table 1** Annotations for configuring a custom header forwarding policy

      +-------------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                 | Type                  | Description                                                                                                                                                                                                                                        |
      +===========================================+=======================+====================================================================================================================================================================================================================================================+
      | kubernetes.io/elb.headers.\ *${svc_name}* | String                | Custom header of the Service associated with an ingress. *${svc_name}* is the Service name.                                                                                                                                                        |
      |                                           |                       |                                                                                                                                                                                                                                                    |
      |                                           |                       | Format: a JSON string, for example, {"key": "test", "values": ["value1", "value2"]}                                                                                                                                                                |
      |                                           |                       |                                                                                                                                                                                                                                                    |
      |                                           |                       | -  **key/value** indicates the key-value pair of the custom header. A maximum of eight values can be configured.                                                                                                                                   |
      |                                           |                       |                                                                                                                                                                                                                                                    |
      |                                           |                       |    Enter 1 to 40 characters for a key. Only letters, digits, hyphens (-), and underscores (_) are allowed.                                                                                                                                         |
      |                                           |                       |                                                                                                                                                                                                                                                    |
      |                                           |                       |    Enter 1 to 128 characters for a value. Asterisks (``*``) and question marks (?) are allowed, but spaces and double quotation marks are not allowed. An asterisk can match zero or more characters, and a question mark can match one character. |
      |                                           |                       |                                                                                                                                                                                                                                                    |
      |                                           |                       | -  After a custom header forwarding policy is configured for an ingress, a grayscale release policy cannot be created for the ingress.                                                                                                             |
      |                                           |                       |                                                                                                                                                                                                                                                    |
      |                                           |                       | -  Enter 1 to 51 characters for *${svc_name}*.                                                                                                                                                                                                     |
      +-------------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

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

      NAME          CLASS    HOSTS     ADDRESS          PORTS   AGE
      ingress-test  cce      *         121.**.**.**     80      10s
