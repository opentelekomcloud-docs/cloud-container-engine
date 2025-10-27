:original_name: cce_10_0949.html

.. _cce_10_0949:

Configuring an Advanced Forwarding Policy for a LoadBalancer Ingress
====================================================================

When a LoadBalancer ingress is created in a CCE cluster, CCE automatically provisions a listener for the load balancer associated with the ingress on ELB and adds forwarding policies. By default, the ingress routes incoming client requests based on the domain name and path and directs the requests through the listener to the appropriate backend server, either a node or a pod, for processing. During LoadBalancer ingress creation, CCE allows you to define custom forwarding rules and actions for flexible, fine-grained traffic distribution.


.. figure:: /_static/images/en-us_image_0000002434080236.png
   :alt: **Figure 1** How an advanced forwarding policy operates

   **Figure 1** How an advanced forwarding policy operates

An advanced forwarding policy operates as follows:

#. The client initiates a request to the LoadBalancer ingress.
#. The load balancer evaluates the request against the forwarding rules defined in the advanced forwarding policy.
#. The load balancer forwards the request to the appropriate backend server based on the action specified in the forwarding rule.
#. The backend server returns a response to the client.

Prerequisites
-------------

-  A CCE standard or Turbo cluster is available, and the cluster version meets the requirements.
-  The cluster can be accessed using kubectl. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

Notes and Constraints
---------------------

-  This feature is only available when dedicated load balancers are used.

.. note::

   CCE allows you to configure and limiting on requests, based on ELB's advanced forwarding for LoadBalancer ingresses. These functions are currently being rolled out, and availability depends on the actual regions that have been officially opened. To use some advanced forwarding actions that are not available on the console yet, submit a service ticket to enable required functions.

Forwarding Rules
----------------

LoadBalancer ingresses support advanced traffic routing by allowing you to configure forwarding rules using the **kubernetes.io/elb.conditions**.\ *<svc-name>* annotation. Incoming requests are evaluated against these rules. If the conditions are met, the requests are forwarded accordingly.

If you set up different rules for the same Service using the same annotation, they must all be matched for a request to go through. But if you set multiple values in the same rule, then the request only needs to match one of them. For example, if both Method and QueryString are configured, the traffic can be distributed only when both rules are met. However, if the Method value includes both **GET** and **POST**, the traffic can be distributed only when both rules are met and the request method is either **GET** or **POST**.

.. note::

   You can configure up to 10 advanced forwarding conditions (including domain names and paths) per ingress. Each individual value within a rule counts as one forwarding condition.

   For example, if you define a domain name and a path for an ingress, there are two forwarding conditions, leaving eight remaining. If you then specify two HTTP methods, for example, GET and POST, two more forwarding conditions are used.

.. table:: **Table 1** ELB advanced forwarding rules

   +---------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+
   | Forwarding Rule     | Description                                                                                                                                                                                             | Reference                                                                                                                                                                                                 | Cluster Version                     |
   +=====================+=========================================================================================================================================================================================================+===========================================================================================================================================================================================================+=====================================+
   | HTTP request method | Route HTTP requests based on the request type (such as GET and POST). If a request matches the specified rule, the defined forwarding action is used.                                                   | For details about the configuration example and parameters of advanced forwarding rules, see :ref:`Configuring Advanced Forwarding Rules for a LoadBalancer Ingress <cce_10_0949__section1439911272208>`. | -  v1.23: v1.23.18-r10 or later     |
   |                     |                                                                                                                                                                                                         |                                                                                                                                                                                                           | -  v1.25: v1.25.16-r0 or later      |
   |                     | An annotation example is as follows:                                                                                                                                                                    |                                                                                                                                                                                                           | -  v1.27: v1.27.16-r0 or later      |
   |                     |                                                                                                                                                                                                         |                                                                                                                                                                                                           | -  v1.28: v1.28.13-r0 or later      |
   |                     | .. code-block::                                                                                                                                                                                         |                                                                                                                                                                                                           | -  v1.29: v1.29.8-r0 or later       |
   |                     |                                                                                                                                                                                                         |                                                                                                                                                                                                           | -  v1.30: v1.30.4-r0 or later       |
   |                     |    kubernetes.io/elb.conditions.<svc-name>: |                                                                                                                                                           |                                                                                                                                                                                                           | -  Other clusters of later versions |
   |                     |      [{                                                                                                                                                                                                 |                                                                                                                                                                                                           |                                     |
   |                     |        "type": "Method",                                                                                                                                                                                |                                                                                                                                                                                                           |                                     |
   |                     |        "methodConfig": {                                                                                                                                                                                |                                                                                                                                                                                                           |                                     |
   |                     |          "values": [                                                                                                                                                                                    |                                                                                                                                                                                                           |                                     |
   |                     |            "GET",                                                                                                                                                                                       |                                                                                                                                                                                                           |                                     |
   |                     |            "POST"                                                                                                                                                                                       |                                                                                                                                                                                                           |                                     |
   |                     |          ]                                                                                                                                                                                              |                                                                                                                                                                                                           |                                     |
   |                     |        }                                                                                                                                                                                                |                                                                                                                                                                                                           |                                     |
   |                     |      }]                                                                                                                                                                                                 |                                                                                                                                                                                                           |                                     |
   +---------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+
   | HTTP request header | Route requests based on the specific information (such as **User-Agent** and **Content-Type**) in HTTP request headers. If a request matches the specified rule, the defined forwarding action is used. |                                                                                                                                                                                                           |                                     |
   |                     |                                                                                                                                                                                                         |                                                                                                                                                                                                           |                                     |
   |                     | An annotation example is as follows:                                                                                                                                                                    |                                                                                                                                                                                                           |                                     |
   |                     |                                                                                                                                                                                                         |                                                                                                                                                                                                           |                                     |
   |                     | .. code-block::                                                                                                                                                                                         |                                                                                                                                                                                                           |                                     |
   |                     |                                                                                                                                                                                                         |                                                                                                                                                                                                           |                                     |
   |                     |    kubernetes.io/elb.conditions.<svc-name>: |                                                                                                                                                           |                                                                                                                                                                                                           |                                     |
   |                     |      [{                                                                                                                                                                                                 |                                                                                                                                                                                                           |                                     |
   |                     |        "type": "Header",                                                                                                                                                                                |                                                                                                                                                                                                           |                                     |
   |                     |        "headerConfig": {                                                                                                                                                                                |                                                                                                                                                                                                           |                                     |
   |                     |          "key": "gray-hello",                                                                                                                                                                           |                                                                                                                                                                                                           |                                     |
   |                     |          "values": [                                                                                                                                                                                    |                                                                                                                                                                                                           |                                     |
   |                     |            "value1",                                                                                                                                                                                    |                                                                                                                                                                                                           |                                     |
   |                     |            "value2"                                                                                                                                                                                     |                                                                                                                                                                                                           |                                     |
   |                     |          ]                                                                                                                                                                                              |                                                                                                                                                                                                           |                                     |
   |                     |        }                                                                                                                                                                                                |                                                                                                                                                                                                           |                                     |
   |                     |      }]                                                                                                                                                                                                 |                                                                                                                                                                                                           |                                     |
   +---------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+
   | Cookie              | Route requests based on the specified keys and values in cookies. If a request matches the specified rule, the defined forwarding action is used.                                                       |                                                                                                                                                                                                           |                                     |
   |                     |                                                                                                                                                                                                         |                                                                                                                                                                                                           |                                     |
   |                     | An annotation example is as follows:                                                                                                                                                                    |                                                                                                                                                                                                           |                                     |
   |                     |                                                                                                                                                                                                         |                                                                                                                                                                                                           |                                     |
   |                     | .. code-block::                                                                                                                                                                                         |                                                                                                                                                                                                           |                                     |
   |                     |                                                                                                                                                                                                         |                                                                                                                                                                                                           |                                     |
   |                     |    kubernetes.io/elb.conditions.<svc-name>: |                                                                                                                                                           |                                                                                                                                                                                                           |                                     |
   |                     |      [{                                                                                                                                                                                                 |                                                                                                                                                                                                           |                                     |
   |                     |        "type": "Header",                                                                                                                                                                                |                                                                                                                                                                                                           |                                     |
   |                     |        "headerConfig": {                                                                                                                                                                                |                                                                                                                                                                                                           |                                     |
   |                     |          "key": "gray-hello",                                                                                                                                                                           |                                                                                                                                                                                                           |                                     |
   |                     |          "values": [                                                                                                                                                                                    |                                                                                                                                                                                                           |                                     |
   |                     |            "value1",                                                                                                                                                                                    |                                                                                                                                                                                                           |                                     |
   |                     |            "value2"                                                                                                                                                                                     |                                                                                                                                                                                                           |                                     |
   |                     |          ]                                                                                                                                                                                              |                                                                                                                                                                                                           |                                     |
   |                     |        }                                                                                                                                                                                                |                                                                                                                                                                                                           |                                     |
   |                     |      }]                                                                                                                                                                                                 |                                                                                                                                                                                                           |                                     |
   +---------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+
   | Query string        | Route requests based on the specific parameters and values of the query strings in the URLs. If a request matches the specified rule, the defined forwarding action is used.                            |                                                                                                                                                                                                           |                                     |
   |                     |                                                                                                                                                                                                         |                                                                                                                                                                                                           |                                     |
   |                     | An annotation example is as follows:                                                                                                                                                                    |                                                                                                                                                                                                           |                                     |
   |                     |                                                                                                                                                                                                         |                                                                                                                                                                                                           |                                     |
   |                     | .. code-block::                                                                                                                                                                                         |                                                                                                                                                                                                           |                                     |
   |                     |                                                                                                                                                                                                         |                                                                                                                                                                                                           |                                     |
   |                     |    kubernetes.io/elb.conditions.<svc-name>: |                                                                                                                                                           |                                                                                                                                                                                                           |                                     |
   |                     |      [{                                                                                                                                                                                                 |                                                                                                                                                                                                           |                                     |
   |                     |        "type": "QueryString",                                                                                                                                                                           |                                                                                                                                                                                                           |                                     |
   |                     |        "queryStringConfig": {                                                                                                                                                                           |                                                                                                                                                                                                           |                                     |
   |                     |          "key": "querykey",                                                                                                                                                                             |                                                                                                                                                                                                           |                                     |
   |                     |          "values": [                                                                                                                                                                                    |                                                                                                                                                                                                           |                                     |
   |                     |            "queryvalue"                                                                                                                                                                                 |                                                                                                                                                                                                           |                                     |
   |                     |          ]                                                                                                                                                                                              |                                                                                                                                                                                                           |                                     |
   |                     |        }                                                                                                                                                                                                |                                                                                                                                                                                                           |                                     |
   |                     |      }]                                                                                                                                                                                                 |                                                                                                                                                                                                           |                                     |
   +---------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+
   | CIDR block          | Route requests based on the source IP addresses from where the requests originate. If a request matches the specified rule, the defined forwarding action is used.                                      |                                                                                                                                                                                                           |                                     |
   |                     |                                                                                                                                                                                                         |                                                                                                                                                                                                           |                                     |
   |                     | An annotation example is as follows:                                                                                                                                                                    |                                                                                                                                                                                                           |                                     |
   |                     |                                                                                                                                                                                                         |                                                                                                                                                                                                           |                                     |
   |                     | .. code-block::                                                                                                                                                                                         |                                                                                                                                                                                                           |                                     |
   |                     |                                                                                                                                                                                                         |                                                                                                                                                                                                           |                                     |
   |                     |    kubernetes.io/elb.conditions.<svc-name>: |                                                                                                                                                           |                                                                                                                                                                                                           |                                     |
   |                     |      [{                                                                                                                                                                                                 |                                                                                                                                                                                                           |                                     |
   |                     |        "type": "SourceIp",                                                                                                                                                                              |                                                                                                                                                                                                           |                                     |
   |                     |        "sourceIpConfig": {                                                                                                                                                                              |                                                                                                                                                                                                           |                                     |
   |                     |          "values": [                                                                                                                                                                                    |                                                                                                                                                                                                           |                                     |
   |                     |            "192.168.0.0/16",                                                                                                                                                                            |                                                                                                                                                                                                           |                                     |
   |                     |            "172.16.0.0/16"                                                                                                                                                                              |                                                                                                                                                                                                           |                                     |
   |                     |          ]                                                                                                                                                                                              |                                                                                                                                                                                                           |                                     |
   |                     |        }                                                                                                                                                                                                |                                                                                                                                                                                                           |                                     |
   |                     |      }]                                                                                                                                                                                                 |                                                                                                                                                                                                           |                                     |
   +---------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+

Forwarding Actions
------------------

LoadBalancer ingresses allow you to define multiple forwarding actions through annotations. This gives you flexible control over how requests are handled. The supported forwarding actions include:

-  (Default) Forward to a backend server group: Additional actions like rewrite, CORS, write/remove header, and limit request are supported. Load balancers handle requests based on these additional actions before forwarding them to backend server groups. Among all the additional actions, limit request has the highest priority.
-  Redirect to another listener: HTTP requests can be redirected to an HTTPS listener.
-  Return a specific response body: Additional actions such as limit request are supported.
-  Forward to a custom backend server group: This action cannot be used together with grayscale release, return a specific response body, or redirect to another URL.

.. table:: **Table 2** ELB advanced forwarding actions

   +---------------------------------------------+-------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+
   | Forwarding Action                           | Additional Action | Description                                                                                                                                                                      | Reference                                                                                                                                                                                           | Cluster Version                     |
   +=============================================+===================+==================================================================================================================================================================================+=====================================================================================================================================================================================================+=====================================+
   | (Default) Forward to a backend server group | Limit request     | Support rate limiting on requests.                                                                                                                                               | For details about the configuration example and parameters, see :ref:`Configuring Limit Request for a LoadBalancer Ingress <cce_10_0949__section1026923902417>`.                                    | -  v1.27: v1.27.16-r10 or later     |
   |                                             |                   |                                                                                                                                                                                  |                                                                                                                                                                                                     | -  v1.28: v1.28.15-r0 or later      |
   |                                             |                   | An annotation example is as follows:                                                                                                                                             |                                                                                                                                                                                                     | -  v1.29: v1.29.10-r0 or later      |
   |                                             |                   |                                                                                                                                                                                  |                                                                                                                                                                                                     | -  v1.30: v1.30.6-r0 or later       |
   |                                             |                   | .. code-block::                                                                                                                                                                  |                                                                                                                                                                                                     | -  Other clusters of later versions |
   |                                             |                   |                                                                                                                                                                                  |                                                                                                                                                                                                     |                                     |
   |                                             |                   |    kubernetes.io/elb.actions.<svc-name>: |                                                                                                                                       |                                                                                                                                                                                                     |                                     |
   |                                             |                   |      [{                                                                                                                                                                          |                                                                                                                                                                                                     |                                     |
   |                                             |                   |         "type": "TrafficLimit",                                                                                                                                                  |                                                                                                                                                                                                     |                                     |
   |                                             |                   |         "trafficLimitConfig": {                                                                                                                                                  |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "QPS": 4,                                                                                                                                                              |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "perSourceIpQps": 2,                                                                                                                                                   |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "burst": 2                                                                                                                                                             |                                                                                                                                                                                                     |                                     |
   |                                             |                   |         }                                                                                                                                                                        |                                                                                                                                                                                                     |                                     |
   |                                             |                   |      }]                                                                                                                                                                          |                                                                                                                                                                                                     |                                     |
   +---------------------------------------------+-------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+
   | Return a specific response body             | None              | Directly return a specific response body and do not continue to forward the response to the backend server.                                                                      | For details about the configuration example and parameters, see :ref:`Configuring Return a Specific Response Body for a LoadBalancer Ingress <cce_10_0949__section1848134064919>`.                  | -  v1.25: v1.25.16-r10 or later     |
   |                                             |                   |                                                                                                                                                                                  |                                                                                                                                                                                                     | -  v1.27: v1.27.16-r10 or later     |
   |                                             |                   | An annotation example is as follows:                                                                                                                                             |                                                                                                                                                                                                     | -  v1.28: v1.28.15-r0 or later      |
   |                                             |                   |                                                                                                                                                                                  |                                                                                                                                                                                                     | -  v1.29: v1.29.10-r0 or later      |
   |                                             |                   | .. code-block::                                                                                                                                                                  |                                                                                                                                                                                                     | -  v1.30: v1.30.6-r0 or later       |
   |                                             |                   |                                                                                                                                                                                  |                                                                                                                                                                                                     | -  Other clusters of later versions |
   |                                             |                   |    kubernetes.io/elb.actions.<svc-name>: |                                                                                                                                       |                                                                                                                                                                                                     |                                     |
   |                                             |                   |      [{                                                                                                                                                                          |                                                                                                                                                                                                     |                                     |
   |                                             |                   |         "type": "FixedResponse",                                                                                                                                                 |                                                                                                                                                                                                     |                                     |
   |                                             |                   |         "fixedResponseConfig": {                                                                                                                                                 |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "contentType": "text/plain",                                                                                                                                           |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "statusCode": "503",                                                                                                                                                   |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "messageBody": "503 error text"                                                                                                                                        |                                                                                                                                                                                                     |                                     |
   |                                             |                   |         }                                                                                                                                                                        |                                                                                                                                                                                                     |                                     |
   |                                             |                   |      }]                                                                                                                                                                          |                                                                                                                                                                                                     |                                     |
   +---------------------------------------------+-------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+
   | Return a specific response body             | Limit request     | Support rate limiting on requests.                                                                                                                                               | For details about the configuration example and parameters, see :ref:`Configuring Limit Request for a LoadBalancer Ingress <cce_10_0949__section1026923902417>`.                                    | -  v1.27: v1.27.16-r10 or later     |
   |                                             |                   |                                                                                                                                                                                  |                                                                                                                                                                                                     | -  v1.28: v1.28.15-r0 or later      |
   |                                             |                   | An annotation example is as follows:                                                                                                                                             |                                                                                                                                                                                                     | -  v1.29: v1.29.10-r0 or later      |
   |                                             |                   |                                                                                                                                                                                  |                                                                                                                                                                                                     | -  v1.30: v1.30.6-r0 or later       |
   |                                             |                   | .. code-block::                                                                                                                                                                  |                                                                                                                                                                                                     | -  Other clusters of later versions |
   |                                             |                   |                                                                                                                                                                                  |                                                                                                                                                                                                     |                                     |
   |                                             |                   |    kubernetes.io/elb.actions.<svc-name>: |                                                                                                                                       |                                                                                                                                                                                                     |                                     |
   |                                             |                   |      [{                                                                                                                                                                          |                                                                                                                                                                                                     |                                     |
   |                                             |                   |         "type": "FixedResponse",                                                                                                                                                 |                                                                                                                                                                                                     |                                     |
   |                                             |                   |         "fixedResponseConfig": {                                                                                                                                                 |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "contentType": "text/plain",                                                                                                                                           |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "statusCode": "503",                                                                                                                                                   |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "messageBody": "503 error text"                                                                                                                                        |                                                                                                                                                                                                     |                                     |
   |                                             |                   |         }                                                                                                                                                                        |                                                                                                                                                                                                     |                                     |
   |                                             |                   |       },                                                                                                                                                                         |                                                                                                                                                                                                     |                                     |
   |                                             |                   |      {                                                                                                                                                                           |                                                                                                                                                                                                     |                                     |
   |                                             |                   |         "type": "TrafficLimit",                                                                                                                                                  |                                                                                                                                                                                                     |                                     |
   |                                             |                   |         "trafficLimitConfig": {                                                                                                                                                  |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "QPS": 4,                                                                                                                                                              |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "perSourceIpQps": 2,                                                                                                                                                   |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "burst": 2                                                                                                                                                             |                                                                                                                                                                                                     |                                     |
   |                                             |                   |         }                                                                                                                                                                        |                                                                                                                                                                                                     |                                     |
   |                                             |                   |      }]                                                                                                                                                                          |                                                                                                                                                                                                     |                                     |
   +---------------------------------------------+-------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+
   | Forward to a custom backend server group    |                   | Forward LoadBalancer ingress requests to both pods in the CCE cluster (default backend server group) and an ECS group (custom backend server group) located outside the cluster. | For details about the configuration example and parameters, see :ref:`Configuring the Forward to a Custom Backend Server Group Action for a LoadBalancer Ingress <cce_10_0949__section8498141262>`. | -  v1.25: v1.25.16-r20 or later     |
   |                                             |                   |                                                                                                                                                                                  |                                                                                                                                                                                                     | -  v1.27: v1.27.16-r20 or later     |
   |                                             |                   | An annotation example is as follows:                                                                                                                                             |                                                                                                                                                                                                     | -  v1.28: v1.28.15-r10 or later     |
   |                                             |                   |                                                                                                                                                                                  |                                                                                                                                                                                                     | -  v1.29: v1.29.10-r10 or later     |
   |                                             |                   | .. code-block::                                                                                                                                                                  |                                                                                                                                                                                                     | -  v1.30: v1.30.6-r10 or later      |
   |                                             |                   |                                                                                                                                                                                  |                                                                                                                                                                                                     | -  v1.31: v1.31.4-r0 or later       |
   |                                             |                   |    kubernetes.io/elb.actions.<svc-name>: |                                                                                                                                       |                                                                                                                                                                                                     | -  Other clusters of later versions |
   |                                             |                   |      [{                                                                                                                                                                          |                                                                                                                                                                                                     |                                     |
   |                                             |                   |         "type": "ForwardPool",                                                                                                                                                   |                                                                                                                                                                                                     |                                     |
   |                                             |                   |         "forwardConfig": [{                                                                                                                                                      |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "serviceName": "test",                                                                                                                                                 |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "servicePort": 80,                                                                                                                                                     |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "weight": 90,                                                                                                                                                          |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "type": "service"                                                                                                                                                      |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           },{                                                                                                                                                                    |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "poolID": "53d8516e-xxxx-xxxx-xxxx-b15ffd6b3cca",                                                                                                                      |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "weight": 70,                                                                                                                                                          |                                                                                                                                                                                                     |                                     |
   |                                             |                   |           "type": "pool"                                                                                                                                                         |                                                                                                                                                                                                     |                                     |
   |                                             |                   |         }]                                                                                                                                                                       |                                                                                                                                                                                                     |                                     |
   |                                             |                   |      }]                                                                                                                                                                          |                                                                                                                                                                                                     |                                     |
   +---------------------------------------------+-------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+

.. _cce_10_0949__section1439911272208:

Configuring Advanced Forwarding Rules for a LoadBalancer Ingress
----------------------------------------------------------------

LoadBalancer ingresses can route traffic based on custom HTTP request methods, request headers, query strings, and source CIDR blocks. In this example, traffic is routed to the **svc-hello1** Service only when the request path is **/hello1** and the following advanced forwarding rules are met:

-  The request method is **GET** or **POST**.
-  The key in the request header is **gray-hello**, and the value is **value1** or **value2**.
-  The key of the cookie is **cookiekey1** and the value is **cookievalue1**, or the key of the cookie is **cookiekey2** and the value is **cookievalue2**.
-  The key of the query string is **querykey**, and the value is **queryvalue**.
-  The source CIDR block is **192.168.0.0/16** or **172.16.0.0/16**.

The operations and parameter settings are as follows:

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
          # The following annotation indicates that the advanced forwarding rules are configured for the svc-hello1 Service. The Service name must be the same as that in the backend field.
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
                        "key": "cookiekey1",
                        "value": "cookievalue1"
                      },
                      {
                        "key": "cookiekey2",
                        "value": "cookievalue2"
                      }
                    ]
                  }
                },
                {
                  "type": "QueryString",
                  "queryStringConfig": {
                    "key": "querykey",
                    "values": [
                      "queryvalue"
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

   .. table:: **Table 3** Annotation for advanced forwarding rules

      +----------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                    | Type                  | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
      +==============================================+=======================+=====================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | kubernetes.io/elb.conditions.\ *${svc_name}* | String                | Configure advanced forwarding rules. *${svc_name}* indicates the Service name, which can contain a maximum of 48 characters.                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                              |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                              |                       | If the annotation value is set to *[]*, the advanced forwarding rules will be deleted.                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                              |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                              |                       | The annotation value is a JSON array. For details, see :ref:`Table 4 <cce_10_0949__en-us_topic_0000002078490189_table18956183913383>`.                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                              |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                              |                       | .. caution::                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                              |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                              |                       |    CAUTION:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
      |                                              |                       |    If you set up different rules for the same condition array, they must all be matched for a request to go through. But if you set multiple values in the same rule, then the request only needs to match one of them. For example, if both Method and QueryString are configured, the traffic can be distributed only when both rules are met. However, if the Method value includes both **GET** and **POST**, the traffic can be distributed only when both rules are met and the request method is either **GET** or **POST**. |
      +----------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0949__en-us_topic_0000002078490189_table18956183913383:

   .. table:: **Table 4** Array structure

      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter             | Description                                                                                                                                                                                                                                      | Example                                                                                                                                                    |
      +=======================+==================================================================================================================================================================================================================================================+============================================================================================================================================================+
      | type                  | Matching type. Options:                                                                                                                                                                                                                          | None                                                                                                                                                       |
      |                       |                                                                                                                                                                                                                                                  |                                                                                                                                                            |
      |                       | -  **Method**: HTTP requests are forwarded based on the matched method. This parameter must be used with **methodConfig**. **Method** can be configured only once.                                                                               |                                                                                                                                                            |
      |                       | -  **Header**: HTTP requests are forwarded based on the matched header. This parameter must be used with **headerConfig**.                                                                                                                       |                                                                                                                                                            |
      |                       | -  **Cookie**: HTTP requests are forwarded based on the matched cookie. This parameter must be used with **cookieConfig**.                                                                                                                       |                                                                                                                                                            |
      |                       | -  **QueryString**: HTTP requests are forwarded based on the matched character string. This parameter must be used with **queryStringConfig**.                                                                                                   |                                                                                                                                                            |
      |                       | -  **SourceIp**: HTTP requests are forwarded based on the matched CIDR block. This parameter must be used with **sourceIpConfig**. **SourceIp** can be configured only once.                                                                     |                                                                                                                                                            |
      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | methodConfig          | An HTTP request method that is used to forward requests. This parameter is used only when **type** is set to **Method**.                                                                                                                         | You do not need to configure keys for **methodConfig**. You only need to configure values (an array) for **methodConfig**.                                 |
      |                       |                                                                                                                                                                                                                                                  |                                                                                                                                                            |
      |                       | Multiple methods can be concurrently configured, including **GET**, **POST**, **PUT**, **DELETE**, **PATCH**, **HEAD**, and **OPTIONS**.                                                                                                         | .. code-block::                                                                                                                                            |
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
      |                       | -  Multiple values can be configured for a key. A value can only contain letters, digits, and special characters ``!#$%&'()*+,.\/:;<=>?@[]^-_'{|}~.`` Asterisks (*) and question marks (?) can be used as wildcard characters.                   |      "headerConfig": {                                                                                                                                     |
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
      |                       |                                                                                                                                                                                                                                                  |              "key": "cookiekey1",                                                                                                                          |
      |                       |                                                                                                                                                                                                                                                  |              "value": "cookievalue1"                                                                                                                       |
      |                       |                                                                                                                                                                                                                                                  |            },                                                                                                                                              |
      |                       |                                                                                                                                                                                                                                                  |            {                                                                                                                                               |
      |                       |                                                                                                                                                                                                                                                  |               "key": "cookiekey2",                                                                                                                         |
      |                       |                                                                                                                                                                                                                                                  |               "value": "cookievalue2"                                                                                                                      |
      |                       |                                                                                                                                                                                                                                                  |            }                                                                                                                                               |
      |                       |                                                                                                                                                                                                                                                  |         ]                                                                                                                                                  |
      |                       |                                                                                                                                                                                                                                                  |      }                                                                                                                                                     |
      |                       |                                                                                                                                                                                                                                                  |    }                                                                                                                                                       |
      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | queryStringConfig     | A character string that is used to forward requests. If the character string in a request matches the one in the configured forwarding rule, the request will be forwarded. This parameter is used only when **type** is set to **QueryString**. | You can only configure one key for each **queryStringConfig** rule. If you need multiple keys, configure multiple **QueryStringConfig** rules.             |
      |                       |                                                                                                                                                                                                                                                  |                                                                                                                                                            |
      |                       | A query string consists of a key and one or more values. Configure the key and values separately.                                                                                                                                                | .. code-block::                                                                                                                                            |
      |                       |                                                                                                                                                                                                                                                  |                                                                                                                                                            |
      |                       | -  A key can only contain letters, digits, and special characters ``!$'()*+,./:;=?@^-_'.``                                                                                                                                                       |    {                                                                                                                                                       |
      |                       | -  Multiple values can be configured for a key. A value can only contain letters, digits, and special characters ``!$'()*+,./:;=?@^-_'.`` Asterisks (*) and question marks (?) can be used as wildcard characters.                               |       "type": "QueryString",                                                                                                                               |
      |                       |                                                                                                                                                                                                                                                  |       "queryStringConfig": {                                                                                                                               |
      |                       |                                                                                                                                                                                                                                                  |          "key": "querykey",                                                                                                                                |
      |                       |                                                                                                                                                                                                                                                  |          "values": [                                                                                                                                       |
      |                       |                                                                                                                                                                                                                                                  |              "queryvalue"                                                                                                                                  |
      |                       |                                                                                                                                                                                                                                                  |          ]                                                                                                                                                 |
      |                       |                                                                                                                                                                                                                                                  |       }                                                                                                                                                    |
      |                       |                                                                                                                                                                                                                                                  |    }                                                                                                                                                       |
      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | sourceIpConfig        | A CIDR block that is used to forward requests. This parameter is used only when **type** is set to **SourceIp**.                                                                                                                                 | You do not need to configure keys for **sourceIpConfig**. You only need to configure values (an array) for **sourceIpConfig**.                             |
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

.. _cce_10_0949__section1848134064919:

Configuring Return a Specific Response Body for a LoadBalancer Ingress
----------------------------------------------------------------------

You can configure a specific response status code and content for a LoadBalancer ingress. In this example, when a user accesses the ingress, the status code 503 and **"503 error text"** are returned.

The operations and parameter settings are as follows:

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
          kubernetes.io/elb.id: *****
          kubernetes.io/elb.port: "80"
          # The following annotation applies to the test-service Service and is used to configure the return a specific request body action for it.
          kubernetes.io/elb.actions.test-service: |
           [{
             "type": "FixedResponse",
             "fixedResponseConfig": {
                 "contentType": "text/plain",
                 "statusCode": "503",
                 "messageBody": "503 error text"
             }
           }]
        name: ingress-test
        namespace: default
      spec:
        ingressClassName: cce
        rules:
          - http:
              paths:
                - backend:
                    service:
                      name: test-service
                      port:
                        number: 80
                  path: /
                  pathType: ImplementationSpecific
                  property:
                    ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

   .. table:: **Table 5** Annotations for advanced forwarding actions

      +-------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                 | Type                  | Description                                                                                                                                     |
      +===========================================+=======================+=================================================================================================================================================+
      | kubernetes.io/elb.actions.\ *${svc_name}* | String                | Configure an advanced forwarding action for an ingress. *${svc_name}* indicates the Service name, which can contain a maximum of 51 characters. |
      |                                           |                       |                                                                                                                                                 |
      |                                           |                       | If the annotation value is set to *[]*, all advanced forwarding actions will be deleted.                                                        |
      |                                           |                       |                                                                                                                                                 |
      |                                           |                       | The annotation value of a fixed response is a JSON string array. For details, see :ref:`Table 6 <cce_10_0949__table14853154410493>`.            |
      +-------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0949__table14853154410493:

   .. table:: **Table 6** Parameters for a fixed response

      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------+
      | Parameter             | Description                                                                                                                                                            | Example                                    |
      +=======================+========================================================================================================================================================================+============================================+
      | type                  | The value is fixed at **FixedResponse**, indicating that a fixed response will be returned.                                                                            | None                                       |
      |                       |                                                                                                                                                                        |                                            |
      |                       | .. note::                                                                                                                                                              |                                            |
      |                       |                                                                                                                                                                        |                                            |
      |                       |    For advanced forwarding actions, you can add a maximum of one fixed response configuration to an annotation.                                                        |                                            |
      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------+
      | fixedResponseConfig   | -  **contentType**: format of the returned content. The options are **text/plain**, **text/css**, **text/html**, **application/javascript**, and **application/json**. | .. code-block::                            |
      |                       | -  **statusCode**: By default, 2xx, 4xx, and 5xx status codes are supported.                                                                                           |                                            |
      |                       | -  (Mandatory) **messageBody**: Enter 0 to 1024 characters.                                                                                                            |    {                                       |
      |                       |                                                                                                                                                                        |       "type": "FixedResponse",             |
      |                       |    .. important::                                                                                                                                                      |       "fixedResponseConfig": {             |
      |                       |                                                                                                                                                                        |          "contentType": "text/plain",      |
      |                       |       NOTICE:                                                                                                                                                          |          "statusCode": "503",              |
      |                       |       This parameter cannot be left empty when :ref:`rate limiting <cce_10_0949__section1026923902417>` is configured.                                                 |          "messageBody": "503 error text"   |
      |                       |                                                                                                                                                                        |        }                                   |
      |                       |                                                                                                                                                                        |    }                                       |
      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------+

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

.. _cce_10_0949__section1026923902417:

Configuring Limit Request for a LoadBalancer Ingress
----------------------------------------------------

You can configure limit request for a LoadBalancer ingress. In this example, when a user accesses the ingress, limit request is applied. The Queries Per Second (QPS) is set to **4**, with a per-client source IP address QPS limit of **2**. Additionally, up to 2 extra requests in a burst above the QPS limit is allowed during brief traffic spikes.

The operations and parameter settings are as follows:

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
          kubernetes.io/elb.id: *****
          kubernetes.io/elb.port: "80"
          # The following annotation applies to the test-service Service and is used to configure the limit request action for it.
          kubernetes.io/elb.actions.test-service: |
           [{
             "type": "TrafficLimit",
             "trafficLimitConfig": {
                 "QPS": 4,
                 "perSourceIpQps": 2,
                 "burst": 2
             }
           }]
        name: ingress-test
        namespace: default
      spec:
        ingressClassName: cce
        rules:
          - http:
              paths:
                - backend:
                    service:
                      name: test-service
                      port:
                        number: 80
                  path: /
                  pathType: ImplementationSpecific
                  property:
                    ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

   .. table:: **Table 7** Annotations for advanced forwarding actions

      +-------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                 | Type                  | Description                                                                                                                                     |
      +===========================================+=======================+=================================================================================================================================================+
      | kubernetes.io/elb.actions.\ *${svc_name}* | String                | Configure an advanced forwarding action for an ingress. *${svc_name}* indicates the Service name, which can contain a maximum of 51 characters. |
      |                                           |                       |                                                                                                                                                 |
      |                                           |                       | If the annotation value is set to *[]*, all advanced forwarding actions will be deleted.                                                        |
      |                                           |                       |                                                                                                                                                 |
      |                                           |                       | The value of an annotation for configuring traffic limit is a JSON array. For details, see :ref:`Table 8 <cce_10_0949__table20270193914244>`.   |
      +-------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0949__table20270193914244:

   .. table:: **Table 8** Parameters for configuring traffic limit

      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------+
      | Parameter             | Description                                                                                                                                                                                                                                                                                                                                                                                                      | Example                         |
      +=======================+==================================================================================================================================================================================================================================================================================================================================================================================================================+=================================+
      | type                  | The value is fixed at **TrafficLimit**, indicating that traffic limit is enabled for a load balancer.                                                                                                                                                                                                                                                                                                            | None                            |
      |                       |                                                                                                                                                                                                                                                                                                                                                                                                                  |                                 |
      |                       | .. note::                                                                                                                                                                                                                                                                                                                                                                                                        |                                 |
      |                       |                                                                                                                                                                                                                                                                                                                                                                                                                  |                                 |
      |                       |    For advanced forwarding actions, you can add a maximum of one traffic limit configuration to an annotation.                                                                                                                                                                                                                                                                                                   |                                 |
      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------+
      | trafficLimitConfig    | -  **QPS**: the total traffic limit, which specifies the maximum QPS value. The value ranges from 0 to 100000. Value 0 means no limit. If the number of requests reaches the specified value, new requests will be discarded and 503 Service Unavailable will be returned to the client.                                                                                                                         | .. code-block::                 |
      |                       |                                                                                                                                                                                                                                                                                                                                                                                                                  |                                 |
      |                       | -  (Optional) **perSourceIpQps**: traffic limit per client source IP address. The value ranges from 0 to 100000. Value 0 means no limit.                                                                                                                                                                                                                                                                         |    {                            |
      |                       |                                                                                                                                                                                                                                                                                                                                                                                                                  |       "type": "TrafficLimit",   |
      |                       |    If both **QPS** and **perSourceIpQps** are configured, the latter value must be smaller than the former. If the number of requests reaches the specified value, new requests will be discarded and 503 Service Unavailable will be returned to the client.                                                                                                                                                    |       "trafficLimitConfig": {   |
      |                       |                                                                                                                                                                                                                                                                                                                                                                                                                  |          "QPS": "4",            |
      |                       | -  (Optional) **burst**: the size of a burst buffer. The value ranges from 0 to 100000. The burst rate enables temporary surges above the average rate to manage sudden spikes in requests. For instance, if the limit is set to 5 but the burst rate is 10, five more requests can be processed within 1 second. However, if the number of requests exceeds 10, only five requests are allowed within 1 second. |          "perSourceIpQps": "2", |
      |                       |                                                                                                                                                                                                                                                                                                                                                                                                                  |          "burst": "2"           |
      |                       | .. caution::                                                                                                                                                                                                                                                                                                                                                                                                     |        }                        |
      |                       |                                                                                                                                                                                                                                                                                                                                                                                                                  |    }                            |
      |                       |    CAUTION:                                                                                                                                                                                                                                                                                                                                                                                                      |                                 |
      |                       |    After **trafficLimitConfig** is configured, deleting a single field in it on CCE does not remove the corresponding configuration from ELB. To disable rate limiting, set the parameter value to **0**. To completely remove the rate limiting, delete the entire **trafficLimitConfig** field.                                                                                                                |                                 |
      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------+

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

.. _cce_10_0949__section8498141262:

Configuring the Forward to a Custom Backend Server Group Action for a LoadBalancer Ingress
------------------------------------------------------------------------------------------

.. important::

   -  Configuring a custom backend server group for a LoadBalancer ingress is subject to the capabilities of your ELB service. Before using this feature, submit a service ticket to enable the required ELB capabilities.
   -  The forward to a custom backend server group action cannot be used together with grayscale release, return a specific response body, or redirect to another URL.
   -  If you configure the forward to a custom backend server group action for a LoadBalancer ingress, the **number** field for the backend Services of the ingress will be invalid and the Service port name must be set to **use-annotation**. For details, see the example provided in this section.
   -  When configuring the forward to a custom backend server group action for a LoadBalancer ingress, ensure that each Service name and backend server group ID are unique.
   -  If you configure the **kubernetes.io/elb.slowstart** or **kubernetes.io/elb.pool-protocol** annotation when configuring the forward to a custom backend server group action for a LoadBalancer ingress, the annotation applies only to the default backend server groups automatically created by CCE. It does not apply to any custom backend server groups configured on ELB.

You can forward requests from an ingress to multiple custom backend server groups by weight. In this example, when a user accesses **example.com**, traffic is distributed between two backend server groups. Some is distributed to the backend server group associated with the **test** Service, assigned a weight of **90**. The remaining traffic is directed to the load balancer's backend server group, which has a weight of **70**.

The operations and parameter settings are as follows:

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
          kubernetes.io/elb.id: *****
          kubernetes.io/elb.port: "80"
          # The following annotation is used to configure a backend server group associated with a Service and a load balancer's backend server group for the ingress. forward-demo is the Service name in the backend field.
          kubernetes.io/elb.actions.forward-demo: |
           [{
             "type": "ForwardPool",
             "forwardConfig": [{
                "serviceName": "test",
                "servicePort": 80,
                "weight": 90,
                "type": "service"
                },{
                "poolID": "*****",
                "weight": 70,
                "type": "pool"
             }]
           }]
        name: ingress-test
        namespace: default
      spec:
        ingressClassName: cce
        rules:
          - host: example.com
            http:
              paths:
                - backend:
                    service:
                      name: forward-demo   # When configuring a custom backend server group, ensure that it has the same name as the Service specified in the annotation. In this example, the Service name is forward-demo.
                      port:
                        name: use-annotation   # When you configure a custom backend server group, the original port.number field becomes invalid. You must instead specify the port.name parameter and set it to use-annotation.
                  path: /
                  pathType: ImplementationSpecific
                  property:
                    ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

   .. table:: **Table 9** Annotations for advanced forwarding actions

      +-------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                 | Type                  | Description                                                                                                                                                        |
      +===========================================+=======================+====================================================================================================================================================================+
      | kubernetes.io/elb.actions.\ *${svc_name}* | String                | Configure an advanced forwarding action for an ingress. *${svc_name}* indicates the Service name, which can contain a maximum of 51 characters.                    |
      |                                           |                       |                                                                                                                                                                    |
      |                                           |                       | If the annotation value is set to *[]*, all advanced forwarding actions will be deleted.                                                                           |
      |                                           |                       |                                                                                                                                                                    |
      |                                           |                       | The value of an annotation for configuring a custom backend server group is a JSON string array. For details, see :ref:`Table 10 <cce_10_0949__table15041441668>`. |
      +-------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0949__table15041441668:

   .. table:: **Table 10** Parameters for configuring a custom backend server group

      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Parameter             | Description                                                                                                                                    | Example                                                      |
      +=======================+================================================================================================================================================+==============================================================+
      | type                  | The value is fixed at **ForwardPool**, indicating that a custom backend server group is configured for a load balancer.                        | None                                                         |
      |                       |                                                                                                                                                |                                                              |
      |                       | .. note::                                                                                                                                      |                                                              |
      |                       |                                                                                                                                                |                                                              |
      |                       |    A maximum of one custom backend server group can be added to an advanced forwarding action annotation.                                      |                                                              |
      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | forwardConfig         | .. note::                                                                                                                                      | .. code-block::                                              |
      |                       |                                                                                                                                                |                                                              |
      |                       |    A maximum of five custom backend server groups can be added to this parameter.                                                              |    {                                                         |
      |                       |                                                                                                                                                |        "type": "ForwardPool",                                |
      |                       | -  **type**: type of a custom backend server group, which can be **service** or **pool**.                                                      |        "forwardConfig": [{                                   |
      |                       |                                                                                                                                                |           "serviceName": "test",                             |
      |                       |    -  **service**: CCE automatically creates a backend server group for each Service and manages its lifecycle.                                |           "servicePort": 80,                                 |
      |                       |    -  **pool**: You need to create and maintain the backend server group on the ELB console.                                                   |           "weight": 90,                                      |
      |                       |                                                                                                                                                |           "type": "service"                                  |
      |                       | -  **poolID**: mandatory when **type** is set to **pool**.                                                                                     |           },{                                                |
      |                       |                                                                                                                                                |           "poolID": "53d8516e-xxxx-xxxx-xxxx-b15ffd6b3cca",  |
      |                       |    This parameter specifies the ID of the backend server group under the target ingress's load balancer. The ID must be unique for each group. |           "weight": 70,                                      |
      |                       |                                                                                                                                                |           "type": "pool"                                     |
      |                       | -  **serviceName**: mandatory when **type** is set to **service**.                                                                             |        }]                                                    |
      |                       |                                                                                                                                                |    }                                                         |
      |                       |    Before configuring a Service name, ensure that it is unique and the Service exists in the cluster.                                          |                                                              |
      |                       |                                                                                                                                                |                                                              |
      |                       | -  **servicePort**: mandatory when **type** is set to **service**.                                                                             |                                                              |
      |                       |                                                                                                                                                |                                                              |
      |                       |    This parameter specifies a Service port.                                                                                                    |                                                              |
      |                       |                                                                                                                                                |                                                              |
      |                       | -  **weight**: weight for distributing traffic to each backend server group, ranging from 0 to 100.                                            |                                                              |
      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+

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

      NAME          CLASS    HOSTS         ADDRESS          PORTS   AGE
      ingress-test  cce      example.com   121.**.**.**     80      10s

Combined Application Scenarios
------------------------------

Advanced forwarding rules and actions can be combined to meet various requirements. Some examples are shown in the table below.

+----------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+----------------------------------+
| Rules to Be Applied When an Ingress Is Accessed                                  | Actions to Be Performed                                                                                                    | Target Service (Backend Service) |
+==================================================================================+============================================================================================================================+==================================+
| -  Domain name: **example.com**                                                  | The following actions will be performed during forwarding:                                                                 | test                             |
| -  Request path: **/path1**                                                      |                                                                                                                            |                                  |
| -  Other advanced forwarding rules:                                              | -  Write the user-defined request header **aa:aa**.                                                                        |                                  |
|                                                                                  | -  Delete the request header with the key **dd**.                                                                          |                                  |
|    -  Request method: GET or POST                                                |                                                                                                                            |                                  |
|    -  Request header: **gray-hello:value1** or **gray-hello:value2**             |                                                                                                                            |                                  |
|    -  Request cookie: **cookiekey1:cookievalue1** or **cookiekey2:cookievalue2** |                                                                                                                            |                                  |
|    -  Query string: **querykey:queryvalue**                                      |                                                                                                                            |                                  |
|    -  CIDR block: **192.168.0.0/16** or **172.16.0.0/16**                        |                                                                                                                            |                                  |
+----------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+----------------------------------+
| -  Domain name: **example.com**                                                  | The following actions will be performed during forwarding:                                                                 | test2                            |
| -  Request path: **/path2**                                                      |                                                                                                                            |                                  |
|                                                                                  | -  Return a specific response with status code 503 and the content **503 error text**.                                     |                                  |
|                                                                                  | -  Set the total QPS limit to 67 and limit the QPS per client source IP address to 3.                                      |                                  |
+----------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+----------------------------------+
| -  Domain name: **example.com**                                                  | The following actions will be performed during forwarding:                                                                 | test and test2                   |
| -  Path: **/path3**                                                              |                                                                                                                            |                                  |
|                                                                                  | -  Forward to the backend server groups associated with the **test** (weight: 90) and **test2** (weight: 10) and Services. |                                  |
|                                                                                  | -  Set the total QPS limit to 67 and limit the QPS per client source IP address to 3.                                      |                                  |
|                                                                                  | -  Write the user-defined request header **aa:aa**.                                                                        |                                  |
+----------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+----------------------------------+

The YAML file example is as follows:

.. code-block::

   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: ingress-demo
     namespace: default
     annotations:
       kubernetes.io/elb.class: performance
       kubernetes.io/elb.id: *****
       kubernetes.io/elb.listener-master-ingress: default/ingress-demo
       kubernetes.io/elb.port: '80'
       # The following annotation applies to the test Service and is used to add multiple load balancer forwarding matching conditions to it.
       kubernetes.io/elb.conditions.test: |
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
                     "key": "cookiekey1",
                     "value": "cookievalue1"
                   },
                   {
                     "key": "cookiekey2",
                     "value": "cookievalue2"
                   }
                 ]
               }
             },
             {
               "type": "QueryString",
               "queryStringConfig": {
                 "key": "querykey",
                 "values": [
                   "queryvalue"
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
       # The following annotation applies to the test Service and is used to configure the write/remove header action for it.
       kubernetes.io/elb.actions.test: |
         [{
             "type": "InsertHeader",
             "InsertHeaderConfig": {
                 "key": "aa",
                 "value_type": "USER_DEFINED",
                 "value": "aa"
             }
         },{
             "type": "RemoveHeader",
             "RemoveHeaderConfig": {
                 "key": "dd"
             }
         }]
       # The following annotation applies to the test2 Service and is used to configure the return a specific response body and limit request actions for it.
       kubernetes.io/elb.actions.test2: |
           [{
             "type": "FixedResponse",
             "fixedResponseConfig": {
                 "contentType": "text/plain",
                 "statusCode": "503",
                 "messageBody": "503 error text"
             }},{
            "type": "TrafficLimit",
            "TrafficLimItConfig": {
                "perSourceIpQps": 3,
                "qps": 67
                   }}
            ]
       # The following annotation applies to the test3 Service and is used to configure the write header, limit request, and forward to custom backend server groups (associated with test and test2) actions for it.
       kubernetes.io/elb.actions.test3: |
         [{
             "type": "InsertHeader",
             "InsertHeaderConfig": {
                 "key": "aa",
                 "value_type": "USER_DEFINED",
                 "value": "aa"
             }
         },{
          "type": "ForwardPool",
          "forwardConfig": [{
               "serviceName": "test",
               "servicePort": 80,
               "weight": 90,
               "type": "service"
            },{
               "serviceName": "test2",
               "servicePort": 80,
               "weight": 10,
               "type": "service"
            }]
          },{
          "type": "TrafficLimit",
          "TrafficLimItConfig": {
              "perSourceIpQps": 3,
              "qps": 67
              }}
          ]
   spec:
     ingressClassName: cce
     rules:
       - host: example.com
         http:
           paths:
             - path: /path1
               pathType: ImplementationSpecific
               backend:
                 service:
                   name: test
                   port:
                     number: 80
               property:
                 ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
             - path: /path2
               pathType: ImplementationSpecific
               backend:
                 service:
                   name: test2
                   port:
                     number: 80
               property:
                 ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
             - path: /path3
               pathType: ImplementationSpecific
               backend:
                 service:
                   name: test3
                   port:
                     name: use-annotation  # If the forward to custom backend server group ("type": "ForwardPool") action is specified, port.name must be set to use-annotation.
               property:
                 ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
