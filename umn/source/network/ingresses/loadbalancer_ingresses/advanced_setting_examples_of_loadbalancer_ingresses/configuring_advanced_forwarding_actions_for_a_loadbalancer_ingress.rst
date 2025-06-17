:original_name: cce_10_0949.html

.. _cce_10_0949:

Configuring Advanced Forwarding Actions for a LoadBalancer Ingress
==================================================================

Dedicated load balancers offer different forwarding actions to effectively distribute traffic. ELB directs client requests to backend servers based on the specified forwarding rules.


.. figure:: /_static/images/en-us_image_0000002218820434.png
   :alt: **Figure 1** How an advanced forwarding action operates

   **Figure 1** How an advanced forwarding action operates

.. _cce_10_0949__table17914047164212:

.. table:: **Table 1** Advanced forwarding actions

   +------------------------------------------+---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+-------------------------------------+
   | Action                                   | Additional Action         | Description                                                                                                                                                                        | Reference                                                                                                    | Cluster Version                     |
   +==========================================+===========================+====================================================================================================================================================================================+==============================================================================================================+=====================================+
   | Forward to a backend server group        | None                      | Default forwarding action of a LoadBalancer ingress. Requests are directly forwarded to backend servers (backend nodes or pods in a cluster) without being processed.              | :ref:`Creating a LoadBalancer Ingress on the Console <cce_10_0251>`                                          | No requirements on cluster versions |
   +------------------------------------------+---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+-------------------------------------+
   | Forward to a backend server group        | Limit traffic on requests | Support traffic limit on requests.                                                                                                                                                 | :ref:`Configuring Traffic Limit for LoadBalancer Ingresses <cce_10_0949__section1026923902417>`              | -  v1.27: v1.27.16-r10 or later     |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  v1.28: v1.28.15-r0 or later      |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  v1.29: v1.29.10-r0 or later      |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  v1.30: v1.30.6-r0 or later       |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  Other clusters of later versions |
   +------------------------------------------+---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+-------------------------------------+
   | Return a specific response body          | None                      | Directly return a fixed response and do not continue to forward the response to the backend server.                                                                                | :ref:`Returning a Specific Response Body for a LoadBalancer Ingress <cce_10_0949__section1848134064919>`     | -  v1.25: v1.25.16-r10 or later     |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  v1.27: v1.27.16-r10 or later     |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  v1.28: v1.28.15-r0 or later      |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  v1.29: v1.29.10-r0 or later      |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  v1.30: v1.30.6-r0 or later       |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  Other clusters of later versions |
   +------------------------------------------+---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+-------------------------------------+
   | Return a fixed response                  | Limit traffic on requests | Support traffic limit on requests.                                                                                                                                                 | :ref:`Configuring Traffic Limit for LoadBalancer Ingresses <cce_10_0949__section1026923902417>`              | -  v1.27: v1.27.16-r10 or later     |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  v1.28: v1.28.15-r0 or later      |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  v1.29: v1.29.10-r0 or later      |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  v1.30: v1.30.6-r0 or later       |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  Other clusters of later versions |
   +------------------------------------------+---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+-------------------------------------+
   | Forward to a custom backend server group |                           | Forward LoadBalancer ingress requests to both pods in the CCE cluster (default backend server group) and the ECS groups (custom backend server group) located outside the cluster. | :ref:`Configuring a Custom Backend Server Group for a LoadBalancer Ingress <cce_10_0949__section8498141262>` | -  v1.25: v1.25.16-r20 or later     |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  v1.27: v1.27.16-r20 or later     |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  v1.28: v1.28.15-r10 or later     |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  v1.29: v1.29.10-r10 or later     |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  v1.30: v1.30.6-r10 or later      |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  v1.31: v1.31.4-r0 or later       |
   |                                          |                           |                                                                                                                                                                                    |                                                                                                              | -  Other clusters of later versions |
   +------------------------------------------+---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+-------------------------------------+

.. note::

   CCE allows you to configure and traffic limit on requests, based on ELB's advanced forwarding for LoadBalancer ingresses. These functions are coming soon. To use some advanced forwarding actions that are not available on the console yet, submit a service ticket to enable required functions.

Prerequisites
-------------

-  A CCE standard or Turbo cluster is available, and the cluster version meets the requirements.
-  The cluster can be accessed using kubectl. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

Notes and Constraints
---------------------

-  This feature is only available when dedicated load balancers are used.

.. _cce_10_0949__section1848134064919:

Returning a Specific Response Body for a LoadBalancer Ingress
-------------------------------------------------------------

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
          kubernetes.io/elb.id: 034baaf0-40e8-4e39-b0d9-bf6e5b883cf9
          kubernetes.io/elb.port: "80"
          # Configure the capability of returning a fixed response body for the Service named test-service.
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
                        number: 8888
                  path: /
                  pathType: ImplementationSpecific
                  property:
                    ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

   .. table:: **Table 2** Annotations for advanced forwarding actions

      +-------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                 | Type                  | Description                                                                                                                                     |
      +===========================================+=======================+=================================================================================================================================================+
      | kubernetes.io/elb.actions.\ *${svc_name}* | String                | Configure an advanced forwarding action for an ingress. *${svc_name}* indicates the Service name, which can contain a maximum of 51 characters. |
      |                                           |                       |                                                                                                                                                 |
      |                                           |                       | If the annotation value is set to *[]*, all advanced forwarding actions will be deleted.                                                        |
      |                                           |                       |                                                                                                                                                 |
      |                                           |                       | The annotation value of a fixed response is a JSON string array. For details, see :ref:`Table 3 <cce_10_0949__table14853154410493>`.            |
      +-------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0949__table14853154410493:

   .. table:: **Table 3** Parameters for a fixed response

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
      |                       | -  **messageBody**: Enter 0 to 1024 characters.                                                                                                                        |    {                                       |
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

Configuring Traffic Limit for LoadBalancer Ingresses
----------------------------------------------------

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
          kubernetes.io/elb.id: 034baaf0-40e8-4e39-b0d9-bf6e5b883cf9
          kubernetes.io/elb.port: "80"
          # Configure ELB traffic limit for the test-service Service.
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
                        number: 8888
                  path: /
                  pathType: ImplementationSpecific
                  property:
                    ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

   .. table:: **Table 4** Annotations for advanced forwarding actions

      +-------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                 | Type                  | Description                                                                                                                                     |
      +===========================================+=======================+=================================================================================================================================================+
      | kubernetes.io/elb.actions.\ *${svc_name}* | String                | Configure an advanced forwarding action for an ingress. *${svc_name}* indicates the Service name, which can contain a maximum of 51 characters. |
      |                                           |                       |                                                                                                                                                 |
      |                                           |                       | If the annotation value is set to *[]*, all advanced forwarding actions will be deleted.                                                        |
      |                                           |                       |                                                                                                                                                 |
      |                                           |                       | The value of an annotation for configuring traffic limit is a JSON array. For details, see :ref:`Table 5 <cce_10_0949__table20270193914244>`.   |
      +-------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0949__table20270193914244:

   .. table:: **Table 5** Parameters for configuring traffic limit

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
      |                       |                                                                                                                                                                                                                                                                                                                                                                                                                  |        }                        |
      |                       |                                                                                                                                                                                                                                                                                                                                                                                                                  |    }                            |
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

Configuring a Custom Backend Server Group for a LoadBalancer Ingress
--------------------------------------------------------------------

.. important::

   -  Configuring a custom backend server group for a LoadBalancer ingress is subject to the capabilities of your ELB service. Before using this feature, submit a service ticket to enable the required ELB capabilities.
   -  Custom backend server groups cannot be used for grayscale release or fixed responses.
   -  If you configure a custom backend server group for a LoadBalancer ingress, the **number** field for the backend Services of the ingress will be invalid and the port name must be set to **use-annotation**. For details, see the example provided in this section.
   -  When configuring a custom backend server group for a LoadBalancer ingress, ensure that each Service name and backend server group ID are unique.

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
          kubernetes.io/elb.id: 034baaf0-40e8-4e39-b0d9-bf6e5b883cf9
          kubernetes.io/elb.port: "80"
          # Configure a custom backend server group for Service forward-demo, where the Service name in paths must match forward-demo and the port name must be use-annotation.
          kubernetes.io/elb.actions.forward-demo: |
          [{
             "type": "ForwardPool",
             "forwardConfig": [{
                "serviceName": "test",
                "servicePort": 80,
                "weight": 90,
                "type": "service"
                },{
                  "poolID": "53d8516e-xxxx-xxxx-xxxx-b15ffd6b3cca",
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
                      name: forward-demo
                      port:
                        name: use-annotation   # When forwardPool is enabled, this parameter becomes invalid, and the port name must be set to use-annotation.
                  path: /
                  pathType: ImplementationSpecific
                  property:
                    ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

   .. table:: **Table 6** Annotations for advanced forwarding actions

      +-------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                 | Type                  | Description                                                                                                                                                       |
      +===========================================+=======================+===================================================================================================================================================================+
      | kubernetes.io/elb.actions.\ *${svc_name}* | String                | Configure an advanced forwarding action for an ingress. *${svc_name}* indicates the Service name, which can contain a maximum of 51 characters.                   |
      |                                           |                       |                                                                                                                                                                   |
      |                                           |                       | If the annotation value is set to *[]*, all advanced forwarding actions will be deleted.                                                                          |
      |                                           |                       |                                                                                                                                                                   |
      |                                           |                       | The value of an annotation for configuring a custom backend server group is a JSON string array. For details, see :ref:`Table 7 <cce_10_0949__table15041441668>`. |
      +-------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0949__table15041441668:

   .. table:: **Table 7** Parameters for configuring a custom backend server group

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

      NAME          CLASS    HOSTS     ADDRESS          PORTS   AGE
      ingress-test  cce      *         121.**.**.**     80      10s
