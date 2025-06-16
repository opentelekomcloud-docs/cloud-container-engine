:original_name: cce_10_0940.html

.. _cce_10_0940:

Configuring Advanced Forwarding Rules for a LoadBalancer Ingress
================================================================

Ingresses offer diverse forwarding rules that can match listeners based on different request parameters like HTTP request methods, headers, query strings, CIDR blocks, and cookies. Each listener is associated with an ELB access port. This facilitates flexible service distribution and resource allocation.


.. figure:: /_static/images/en-us_image_0000002253779921.png
   :alt: **Figure 1** How an advanced forwarding rule operates

   **Figure 1** How an advanced forwarding rule operates

Prerequisites
-------------

-  A CCE standard or Turbo cluster is available, and the cluster version meets the following requirements:

   -  v1.23: v1.23.18-r10 or later
   -  v1.25: v1.25.16-r0 or later
   -  v1.27: v1.27.16-r0 or later
   -  v1.28: v1.28.13-r0 or later
   -  v1.29: v1.29.8-r0 or later
   -  v1.30: v1.30.4-r0 or later
   -  Other clusters of later versions

-  The cluster can be accessed using kubectl. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

Notes and Constraints
---------------------

-  This feature is only available when dedicated load balancers are used.

Using kubectl
-------------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   .. code-block::

      vi ingress-test.yaml

   An example YAML file of an ingress created using an existing load balancer is as follows:

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        annotations:
          kubernetes.io/elb.class: performance
          kubernetes.io/elb.id: ab53c3b2-xxxx-xxxx-xxxx-5ac3eb2887be
          kubernetes.io/elb.port: '80'
          # Access the svc-hello1 service. Ensure that this service is available.
          kubernetes.io/elb.conditions.svc-hello1: |
              [
                {
                  "type": "Method",
                  "methodConfig": {
                    "values": [
                      "GET",
                      "POST"
                    ]
                  }
                },
                {
                  "type": "Header",
                  "headerConfig": {
                    "key": "gray-hello",
                    "values": [
                      "value1",
                      "value2"
                    ]
                  }
                },
                {
                  "type": "Cookie",
                  "cookieConfig": {
                    "values": [
                      {
                        "key": "querystringkey1",
                        "value": "querystringvalue2"
                      },
                      {
                        "key": "querystringkey3",
                        "value": "querystringvalue4"
                      }
                    ]
                  }
                },
                {
                  "type": "QueryString",
                  "queryStringConfig": {
                    "key": "testKey",
                    "values": [
                      "testValue"
                    ]
                  }
                },
                {
                  "type": "SourceIp",
                  "sourceIpConfig": {
                    "values": [
                      "192.168.0.0/16",
                      "172.16.0.0/16"
                    ]
                  }
                }
              ]
        name: ingress-test
      spec:
        ingressClassName: cce
        rules:
         - http:
            paths:
            - path: /hello1
              pathType: ImplementationSpecific
              backend:
                service:
                  name: svc-hello1
                  port:
                    number: 80

   .. table:: **Table 1** Annotations for advanced forwarding rules

      +----------------------------------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                    | Type                  | Description                                                                                                                                                                                                                                                                                                                                                                                                                                    |
      +==============================================+=======================+================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | kubernetes.io/elb.conditions.\ *${svc_name}* | String                | Configure an advanced forwarding rule. *${svc_name}* indicates the Service name, which can contain a maximum of 48 characters.                                                                                                                                                                                                                                                                                                                 |
      |                                              |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                              |                       | If the annotation value is set to *[]*, the advanced forwarding rule is deleted.                                                                                                                                                                                                                                                                                                                                                               |
      |                                              |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                              |                       | An annotation value is in the form of a JSON array. For details, see :ref:`Table 2 <cce_10_0940__table18956183913383>`.                                                                                                                                                                                                                                                                                                                        |
      |                                              |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                              |                       | .. important::                                                                                                                                                                                                                                                                                                                                                                                                                                 |
      |                                              |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                              |                       |    NOTICE:                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                              |                       |    The rules in a condition array are connected by an AND relationship, while the values in the same rule block are connected by an OR relationship. For example, if both Method and QueryString are configured, the target traffic can be distributed only when both rules are met. However, if the Method value is GET or POST, the target traffic can be distributed only when both rules are met and the Method value must be GET or POST. |
      +----------------------------------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0940__table18956183913383:

   .. table:: **Table 2** Array structure

      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter             | How to Use                                                                                                                                                                                                                                       | Example                                                                                                                                                    |
      +=======================+==================================================================================================================================================================================================================================================+============================================================================================================================================================+
      | type                  | Matching type. Options:                                                                                                                                                                                                                          | None                                                                                                                                                       |
      |                       |                                                                                                                                                                                                                                                  |                                                                                                                                                            |
      |                       | -  **Method**: HTTP requests are forwarded based on the matched method. This parameter must be used with **methodConfig**. This parameter can be configured only once.                                                                           |                                                                                                                                                            |
      |                       | -  **Header**: HTTP requests are forwarded based on the matched header. This parameter must be used with **headerConfig**.                                                                                                                       |                                                                                                                                                            |
      |                       | -  **Cookie**: HTTP requests are forwarded based on the matched cookie. This parameter must be used with **cookieConfig**.                                                                                                                       |                                                                                                                                                            |
      |                       | -  **QueryString**: HTTP requests are forwarded based on the matched character string. This parameter must be used with **queryStringConfig**.                                                                                                   |                                                                                                                                                            |
      |                       | -  **SourceIp**: HTTP requests are forwarded based on the matched CIDR block. This parameter must be used with **sourceIpConfig**. This parameter can be configured only once.                                                                   |                                                                                                                                                            |
      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | methodConfig          | An HTTP request method that is used to forward requests. This parameter is used only when **type** is set to **Method**.                                                                                                                         | You only need to configure values (an array) for **methodConfig**.                                                                                         |
      |                       |                                                                                                                                                                                                                                                  |                                                                                                                                                            |
      |                       | Multiple request methods can be concurrently configured, including **GET**, **POST**, **PUT**, **DELETE**, **PATCH**, **HEAD**, and **OPTIONS**.                                                                                                 | .. code-block::                                                                                                                                            |
      |                       |                                                                                                                                                                                                                                                  |                                                                                                                                                            |
      |                       |                                                                                                                                                                                                                                                  |    {                                                                                                                                                       |
      |                       |                                                                                                                                                                                                                                                  |      "type": "Method",                                                                                                                                     |
      |                       |                                                                                                                                                                                                                                                  |      "methodConfig": {                                                                                                                                     |
      |                       |                                                                                                                                                                                                                                                  |         "values": [                                                                                                                                        |
      |                       |                                                                                                                                                                                                                                                  |            "GET",                                                                                                                                          |
      |                       |                                                                                                                                                                                                                                                  |            "POST"                                                                                                                                          |
      |                       |                                                                                                                                                                                                                                                  |         ]                                                                                                                                                  |
      |                       |                                                                                                                                                                                                                                                  |      }                                                                                                                                                     |
      |                       |                                                                                                                                                                                                                                                  |    }                                                                                                                                                       |
      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | headerConfig          | An HTTP request header that is used to forward requests. This parameter is used only when **type** is set to **Header**.                                                                                                                         | You can only configure one key for each **headerConfig** rule. If you need multiple keys, configure multiple **headerConfig** rules.                       |
      |                       |                                                                                                                                                                                                                                                  |                                                                                                                                                            |
      |                       | -  A key can only contain letters, digits, underscores (_), and hyphens (-).                                                                                                                                                                     | .. code-block::                                                                                                                                            |
      |                       |                                                                                                                                                                                                                                                  |                                                                                                                                                            |
      |                       |    The first letter of the **User-agent** and **Connection** HTTP request headers must be capitalized.                                                                                                                                           |    {                                                                                                                                                       |
      |                       |                                                                                                                                                                                                                                                  |      "type": "Header",                                                                                                                                     |
      |                       | -  Multiple values can be configured for a key. A value can only contain letters, digits, and special characters (``!#$%&'()*+,.\/:;<=>?@[]^-_'{|}~``). Asterisks (*) and question marks (?) can be used as wildcard characters.                 |      "headerConfig": {                                                                                                                                     |
      |                       |                                                                                                                                                                                                                                                  |         "key": "gray-hello",                                                                                                                               |
      |                       |                                                                                                                                                                                                                                                  |         "values": [                                                                                                                                        |
      |                       |                                                                                                                                                                                                                                                  |            "value1",                                                                                                                                       |
      |                       |                                                                                                                                                                                                                                                  |            "value2"                                                                                                                                        |
      |                       |                                                                                                                                                                                                                                                  |         ]                                                                                                                                                  |
      |                       |                                                                                                                                                                                                                                                  |      }                                                                                                                                                     |
      |                       |                                                                                                                                                                                                                                                  |    }                                                                                                                                                       |
      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | cookieConfig          | A cookie that is used to forward requests. This parameter is used only when **type** is set to **Cookie**. A cookie consists of a key and a value, which must be configured separately.                                                          | When configuring **cookieConfig**, you can configure multiple key-value pairs in **values**, and these pairs will have an OR relationship with each other. |
      |                       |                                                                                                                                                                                                                                                  |                                                                                                                                                            |
      |                       | -  A key can contain 1 to 100 characters and cannot start or end with a space.                                                                                                                                                                   | .. code-block::                                                                                                                                            |
      |                       | -  A value can contain 1 to 100 characters. Configure a value for each key.                                                                                                                                                                      |                                                                                                                                                            |
      |                       |                                                                                                                                                                                                                                                  |    {                                                                                                                                                       |
      |                       | You can enter multiple key-value pairs that can contain letters, digits, and special characters (:literal:`!%'"()*+,./:=?@^-_`~`).                                                                                                               |      "type": "Cookie",                                                                                                                                     |
      |                       |                                                                                                                                                                                                                                                  |      "cookieConfig": {                                                                                                                                     |
      |                       |                                                                                                                                                                                                                                                  |         "values": [                                                                                                                                        |
      |                       |                                                                                                                                                                                                                                                  |            {                                                                                                                                               |
      |                       |                                                                                                                                                                                                                                                  |              "key": "querystringkey1",                                                                                                                     |
      |                       |                                                                                                                                                                                                                                                  |              "value": "querystringvalue2"                                                                                                                  |
      |                       |                                                                                                                                                                                                                                                  |            },                                                                                                                                              |
      |                       |                                                                                                                                                                                                                                                  |            {                                                                                                                                               |
      |                       |                                                                                                                                                                                                                                                  |               "key": "querystringkey3",                                                                                                                    |
      |                       |                                                                                                                                                                                                                                                  |               "value": "querystringvalue4"                                                                                                                 |
      |                       |                                                                                                                                                                                                                                                  |            }                                                                                                                                               |
      |                       |                                                                                                                                                                                                                                                  |         ]                                                                                                                                                  |
      |                       |                                                                                                                                                                                                                                                  |      }                                                                                                                                                     |
      |                       |                                                                                                                                                                                                                                                  |    }                                                                                                                                                       |
      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | queryStringConfig     | A character string that is used to forward requests. If the character string in a request matches the one in the configured forwarding rule, the request will be forwarded. This parameter is used only when **type** is set to **QueryString**. | You can only configure one key for each **queryStringConfig** rule. If you need multiple keys, configure multiple **QueryStringConfig** rules.             |
      |                       |                                                                                                                                                                                                                                                  |                                                                                                                                                            |
      |                       | A query string consists of a key and one or more values. Configure the key and values separately.                                                                                                                                                | .. code-block::                                                                                                                                            |
      |                       |                                                                                                                                                                                                                                                  |                                                                                                                                                            |
      |                       | -  A key can only contain letters, digits, and special characters (``!$'()*+,./:;=?@^-_'``).                                                                                                                                                     |    {                                                                                                                                                       |
      |                       | -  Multiple values can be configured for a key. A value can only contain letters, digits, and special characters (``!$'()*+,./:;=?@^-_'``). Asterisks (*) and question marks (?) can be used as wildcard characters.                             |       "type": "QueryString",                                                                                                                               |
      |                       |                                                                                                                                                                                                                                                  |       "queryStringConfig": {                                                                                                                               |
      |                       |                                                                                                                                                                                                                                                  |          "key": "testKey",                                                                                                                                 |
      |                       |                                                                                                                                                                                                                                                  |          "values": [                                                                                                                                       |
      |                       |                                                                                                                                                                                                                                                  |              "testValue"                                                                                                                                   |
      |                       |                                                                                                                                                                                                                                                  |          ]                                                                                                                                                 |
      |                       |                                                                                                                                                                                                                                                  |       }                                                                                                                                                    |
      |                       |                                                                                                                                                                                                                                                  |    }                                                                                                                                                       |
      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | sourceIpConfig        | A CIDR block that is used to forward requests. This parameter is used only when **type** is set to **SourceIp**.                                                                                                                                 | You only need to configure values (an array) for **sourceIpConfig**.                                                                                       |
      |                       |                                                                                                                                                                                                                                                  |                                                                                                                                                            |
      |                       | Example CIDR block: 192.168.1.0/24 or 2020:50::44/127                                                                                                                                                                                            | .. code-block::                                                                                                                                            |
      |                       |                                                                                                                                                                                                                                                  |                                                                                                                                                            |
      |                       |                                                                                                                                                                                                                                                  |    {                                                                                                                                                       |
      |                       |                                                                                                                                                                                                                                                  |       "type": "SourceIp",                                                                                                                                  |
      |                       |                                                                                                                                                                                                                                                  |       "sourceIpConfig": {                                                                                                                                  |
      |                       |                                                                                                                                                                                                                                                  |          "values": [                                                                                                                                       |
      |                       |                                                                                                                                                                                                                                                  |             "192.168.0.0/16",                                                                                                                              |
      |                       |                                                                                                                                                                                                                                                  |             "172.16.0.0/16"                                                                                                                                |
      |                       |                                                                                                                                                                                                                                                  |          ]                                                                                                                                                 |
      |                       |                                                                                                                                                                                                                                                  |       }                                                                                                                                                    |
      |                       |                                                                                                                                                                                                                                                  |    }                                                                                                                                                       |
      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+

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
