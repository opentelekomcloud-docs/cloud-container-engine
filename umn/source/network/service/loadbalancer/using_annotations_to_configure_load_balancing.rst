:original_name: cce_10_0385.html

.. _cce_10_0385:

Using Annotations to Configure Load Balancing
=============================================

You can add annotations to a YAML file to use some CCE advanced functions. This section describes the available annotations when a LoadBalancer service is created.

-  :ref:`Interconnection with ELB <cce_10_0385__section584135019388>`
-  :ref:`Sticky Session <cce_10_0385__section1370139104012>`
-  :ref:`Health Check <cce_10_0385__section1327831714595>`
-  :ref:`HTTP Protocol <cce_10_0385__section12416195865818>`
-  :ref:`Dynamic Adjustment of the Weight of the Backend ECS <cce_10_0385__section3712956145815>`
-  :ref:`Pass-through Capability <cce_10_0385__section5456195255814>`
-  :ref:`Host Network <cce_10_0385__section952710224812>`
-  :ref:`Timeout <cce_10_0385__section117351411644>`

.. _cce_10_0385__section584135019388:

Interconnection with ELB
------------------------

.. table:: **Table 1** Annotations for interconnecting with ELB

   +--------------------------------+----------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------+
   | Parameter                      | Type                                               | Description                                                                                                                                                                                                                                                                                            | Supported Cluster Version                      |
   +================================+====================================================+========================================================================================================================================================================================================================================================================================================+================================================+
   | kubernetes.io/elb.class        | String                                             | Select a proper load balancer type.                                                                                                                                                                                                                                                                    | v1.9 or later                                  |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    | The value can be:                                                                                                                                                                                                                                                                                      |                                                |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    | -  **union**: shared load balancer                                                                                                                                                                                                                                                                     |                                                |
   |                                |                                                    | -  **performance**: dedicated load balancer, which can be used only in clusters of v1.17 and later.                                                                                                                                                                                                    |                                                |
   +--------------------------------+----------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------+
   | kubernetes.io/elb.id           | String                                             | Mandatory **when an existing load balancer is to be associated**.                                                                                                                                                                                                                                      | v1.9 or later                                  |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    | ID of a load balancer.                                                                                                                                                                                                                                                                                 |                                                |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    | **How to obtain**:                                                                                                                                                                                                                                                                                     |                                                |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    | On the management console, click **Service List**, and choose **Networking** > **Elastic Load Balance**. Click the name of the target load balancer. On the **Summary** tab page, find and copy the ID.                                                                                                |                                                |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    | .. note::                                                                                                                                                                                                                                                                                              |                                                |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    |    The system preferentially connects to the load balancer based on the **kubernetes.io/elb.id** field. If this field is not specified, the **spec.loadBalancerIP** field is used (optional and available only in 1.23 and earlier versions).                                                          |                                                |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    |    Do not use the **spec.loadBalancerIP** field to connect to the load balancer. This field will be discarded by Kubernetes. For details, see `Deprecation <https://github.com/kubernetes/kubernetes/blob/8f2371bcceff7962ddb4901c36536c6ff659755b/CHANGELOG/CHANGELOG-1.24.md#changes-by-kind-13>`__. |                                                |
   +--------------------------------+----------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------+
   | kubernetes.io/elb.autocreate   | :ref:`Table 9 <cce_10_0385__table148341447193017>` | Mandatory **when load balancers are automatically created**.                                                                                                                                                                                                                                           | v1.9 or later                                  |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    | **Example:**                                                                                                                                                                                                                                                                                           |                                                |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    | -  If a public network load balancer will be automatically created, set this parameter to the following value:                                                                                                                                                                                         |                                                |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    |    '{"type":"public","bandwidth_name":"cce-bandwidth-1551163379627","bandwidth_chargemode":"traffic","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_bgp","name":"james"}'                                                                                                                |                                                |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    | -  If a private network load balancer will be automatically created, set this parameter to the following value:                                                                                                                                                                                        |                                                |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    |    {"type":"inner","name":"A-location-d-test"}                                                                                                                                                                                                                                                         |                                                |
   +--------------------------------+----------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------+
   | kubernetes.io/elb.subnet-id    | String                                             | Optional **when load balancers are automatically created**.                                                                                                                                                                                                                                            | Mandatory for clusters earlier than v1.11.7-r0 |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    | ID of the subnet where the cluster is located. The value can contain 1 to 100 characters.                                                                                                                                                                                                              | Discarded in clusters later than v1.11.7-r0    |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    | -  Mandatory when a cluster of v1.11.7-r0 or earlier is to be automatically created.                                                                                                                                                                                                                   |                                                |
   |                                |                                                    | -  Optional for clusters later than v1.11.7-r0.                                                                                                                                                                                                                                                        |                                                |
   +--------------------------------+----------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------+
   | kubernetes.io/elb.lb-algorithm | String                                             | Specifies the load balancing algorithm of the backend server group. The default value is **ROUND_ROBIN**.                                                                                                                                                                                              | v1.9 or later                                  |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    | Options:                                                                                                                                                                                                                                                                                               |                                                |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    | -  **ROUND_ROBIN**: weighted round robin algorithm                                                                                                                                                                                                                                                     |                                                |
   |                                |                                                    | -  **LEAST_CONNECTIONS**: weighted least connections algorithm                                                                                                                                                                                                                                         |                                                |
   |                                |                                                    | -  **SOURCE_IP**: source IP hash algorithm                                                                                                                                                                                                                                                             |                                                |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    | .. note::                                                                                                                                                                                                                                                                                              |                                                |
   |                                |                                                    |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                    |    If this parameter is set to **SOURCE_IP**, the weight setting (**weight** field) of backend servers bound to the backend server group is invalid, and sticky session cannot be enabled.                                                                                                             |                                                |
   +--------------------------------+----------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------+

The following shows how to use the preceding annotations:

-  Associating an existing load balancer. For details, see :ref:`Using kubectl to Create a Service (Using an Existing Load Balancer) <cce_10_0681__section74196215320>`.

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        name: nginx
        annotations:
          kubernetes.io/elb.id: <your_elb_id>                         # ELB ID. Replace it with the actual value.
          kubernetes.io/elb.class: performance                        # Load balancer type
          kubernetes.io/elb.lb-algorithm: ROUND_ROBIN                   # Load balancer algorithm
      spec:
        selector:
           app: nginx
        ports:
        - name: service0
          port: 80
          protocol: TCP
          targetPort: 80
        type: LoadBalancer

-  Automatically creating a load balancer. For details, see :ref:`Using kubectl to Create a Service (Automatically Creating a Load Balancer) <cce_10_0681__section6422152185311>`.

   Shared load balancer:

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          kubernetes.io/elb.class: union
          kubernetes.io/elb.autocreate: '{
            "type": "public",
            "bandwidth_name": "cce-bandwidth-1551163379627",
            "bandwidth_chargemode": "traffic",
            "bandwidth_size": 5,
            "bandwidth_sharetype": "PER",
            "eip_type": "5_bgp"
          }'

          kubernetes.io/elb.lb-algorithm: ROUND_ROBIN     # Load balancer algorithm
        labels:
          app: nginx
        name: nginx
      spec:
        ports:
        - name: service0
          port: 80
          protocol: TCP
          targetPort: 80
        selector:
          app: nginx
        type: LoadBalancer

   Dedicated load balancer:

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        name: nginx
        labels:
          app: nginx
        namespace: default
        annotations:
          kubernetes.io/elb.class: performance
          kubernetes.io/elb.autocreate: '{
            "type": "public",
            "bandwidth_name": "cce-bandwidth-1626694478577",
            "bandwidth_chargemode": "traffic",
            "bandwidth_size": 5,
            "bandwidth_sharetype": "PER",
            "eip_type": "5_bgp",
            "available_zone": [
               ""
            ],
            "l4_flavor_name": "L4_flavor.elb.s1.small"
          }'

          kubernetes.io/elb.lb-algorithm: ROUND_ROBIN     # Load balancer algorithm
      spec:
        selector:
          app: nginx
        ports:
        - name: cce-service-0
          targetPort: 80
          nodePort: 0
          port: 80
          protocol: TCP
        type: LoadBalancer

.. _cce_10_0385__section1370139104012:

Sticky Session
--------------

.. table:: **Table 2** Annotations for sticky session

   +-------------------------------------------+---------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+
   | Parameter                                 | Type                                              | Description                                                                                                                                      | Supported Cluster Version |
   +===========================================+===================================================+==================================================================================================================================================+===========================+
   | kubernetes.io/elb.session-affinity-mode   | String                                            | Source IP address-based sticky session is supported. That is, access requests from the same IP address are forwarded to the same backend server. | v1.9 or later             |
   |                                           |                                                   |                                                                                                                                                  |                           |
   |                                           |                                                   | -  Disabling sticky session: Do not configure this parameter.                                                                                    |                           |
   |                                           |                                                   | -  Enabling sticky session: Set this parameter to **SOURCE_IP**, indicating that the sticky session is based on the source IP address.           |                           |
   |                                           |                                                   |                                                                                                                                                  |                           |
   |                                           |                                                   | .. note::                                                                                                                                        |                           |
   |                                           |                                                   |                                                                                                                                                  |                           |
   |                                           |                                                   |    When **kubernetes.io/elb.lb-algorithm** is set to **SOURCE_IP** (source IP hash), sticky session cannot be enabled.                           |                           |
   +-------------------------------------------+---------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+
   | kubernetes.io/elb.session-affinity-option | :ref:`Table 12 <cce_10_0385__table3340195463412>` | Sticky session timeout.                                                                                                                          | v1.9 or later             |
   +-------------------------------------------+---------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+

The following shows how to use the preceding annotations:

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     name: nginx
     annotations:
       kubernetes.io/elb.id: <your_elb_id>                         # ELB ID. Replace it with the actual value.
       kubernetes.io/elb.class: union                   # Load balancer type
       kubernetes.io/elb.session-affinity-mode: SOURCE_IP          # The sticky session type is source IP address.
       kubernetes.io/elb.session-affinity-option: '{"persistence_timeout": "30"}'     # Stickiness duration (min)
   spec:
     selector:
        app: nginx
     ports:
     - name: service0
       port: 80
       protocol: TCP
       targetPort: 80
     type: LoadBalancer

.. _cce_10_0385__section1327831714595:

Health Check
------------

.. table:: **Table 3** Annotations for health check

   +----------------------------------------+----------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+
   | Parameter                              | Type                                               | Description                                                                                                                                                      | Supported Cluster Version |
   +========================================+====================================================+==================================================================================================================================================================+===========================+
   | kubernetes.io/elb.health-check-flag    | String                                             | Whether to enable the ELB health check.                                                                                                                          | v1.9 or later             |
   |                                        |                                                    |                                                                                                                                                                  |                           |
   |                                        |                                                    | -  Enabling health check: Leave blank this parameter or set it to **on**.                                                                                        |                           |
   |                                        |                                                    | -  Disabling health check: Set this parameter to **off**.                                                                                                        |                           |
   |                                        |                                                    |                                                                                                                                                                  |                           |
   |                                        |                                                    | If this parameter is enabled, the :ref:`kubernetes.io/elb.health-check-option <cce_10_0385__table19192143412319>` field must also be specified at the same time. |                           |
   +----------------------------------------+----------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+
   | kubernetes.io/elb.health-check-option  | :ref:`Table 10 <cce_10_0385__table19192143412319>` | ELB health check configuration items.                                                                                                                            | v1.9 or later             |
   +----------------------------------------+----------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+
   | kubernetes.io/elb.health-check-options | :ref:`Table 11 <cce_10_0385__table33328411456>`    | ELB health check configuration item. Each Service port can be configured separately, and you can configure only some ports.                                      | v1.19.16-r5 or later      |
   |                                        |                                                    |                                                                                                                                                                  |                           |
   |                                        |                                                    | .. note::                                                                                                                                                        | v1.21.8-r0 or later       |
   |                                        |                                                    |                                                                                                                                                                  |                           |
   |                                        |                                                    |    **kubernetes.io/elb.health-check-option** and **kubernetes.io/elb.health-check-options** cannot be configured at the same time.                               | v1.23.6-r0 or later       |
   |                                        |                                                    |                                                                                                                                                                  |                           |
   |                                        |                                                    |                                                                                                                                                                  | v1.25.2-r0 or later       |
   +----------------------------------------+----------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+

-  The following shows how to use **kubernetes.io/elb.health-check-option**:

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        name: nginx
        annotations:
          kubernetes.io/elb.id: <your_elb_id>                         # ELB ID. Replace it with the actual value.
          kubernetes.io/elb.class: union                   # Load balancer type
          kubernetes.io/elb.health-check-flag: 'on'                   # Enable the ELB health check function.
          kubernetes.io/elb.health-check-option: '{
            "protocol":"TCP",
            "delay":"5",
            "timeout":"10",
            "max_retries":"3"
          }'
      spec:
        selector:
           app: nginx
        ports:
        - name: service0
          port: 80
          protocol: TCP
          targetPort: 80
        type: LoadBalancer

-  For details about how to use **kubernetes.io/elb.health-check-options**, see :ref:`Configuring Health Check for Multiple Ports <cce_10_0684>`.

.. _cce_10_0385__section12416195865818:

HTTP Protocol
-------------

.. table:: **Table 4** Annotations for using HTTP protocols

   +---------------------------------+--------+--------------------------------------------------------------+---------------------------+
   | Parameter                       | Type   | Description                                                  | Supported Cluster Version |
   +=================================+========+==============================================================+===========================+
   | kubernetes.io/elb.protocol-port | String | Layer-7 forwarding configuration port used by the Service.   | v1.19.16 or later         |
   +---------------------------------+--------+--------------------------------------------------------------+---------------------------+
   | kubernetes.io/elb.cert-id       | String | HTTP certificate used by the Service for Layer-7 forwarding. | v1.19.16 or later         |
   +---------------------------------+--------+--------------------------------------------------------------+---------------------------+

For details about the application scenarios, see :ref:`Service Using HTTP or HTTPS <cce_10_0683>`.

.. _cce_10_0385__section3712956145815:

Dynamic Adjustment of the Weight of the Backend ECS
---------------------------------------------------

.. table:: **Table 5** Annotations for dynamically adjusting the weight of the backend ECS

   +-----------------------------------+-----------------+-------------------------------------------------------------------------------------------------------------------------------------+---------------------------+
   | Parameter                         | Type            | Description                                                                                                                         | Supported Cluster Version |
   +===================================+=================+=====================================================================================================================================+===========================+
   | kubernetes.io/elb.adaptive-weight | String          | Dynamically adjusts the weight of the load balancer backend ECS based on pods. The requests received by each pod are more balanced. | v1.21 or later            |
   |                                   |                 |                                                                                                                                     |                           |
   |                                   |                 | -  **true**: enabled                                                                                                                |                           |
   |                                   |                 | -  **false**: disabled                                                                                                              |                           |
   |                                   |                 |                                                                                                                                     |                           |
   |                                   |                 | This parameter applies only to clusters of v1.21 or later and is invalid in passthrough networking.                                 |                           |
   +-----------------------------------+-----------------+-------------------------------------------------------------------------------------------------------------------------------------+---------------------------+

The following shows how to use the preceding annotations:

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     name: nginx
     annotations:
       kubernetes.io/elb.id: <your_elb_id>                         # ELB ID. Replace it with the actual value.
       kubernetes.io/elb.class: union                   # Load balancer type
       kubernetes.io/elb.adaptive-weight: 'true'                   # Enable dynamic adjustment of the weight of the backend ECS.
   spec:
     selector:
        app: nginx
     ports:
     - name: service0
       port: 80
       protocol: TCP
       targetPort: 80
     type: LoadBalancer

.. _cce_10_0385__section5456195255814:

Pass-through Capability
-----------------------

.. table:: **Table 6** Annotations for pass-through capability

   +--------------------------------+--------+--------------------------------------------------------------------------------------------------------+---------------------------+
   | Parameter                      | Type   | Description                                                                                            | Supported Cluster Version |
   +================================+========+========================================================================================================+===========================+
   | kubernetes.io/elb.pass-through | String | Whether the access requests from within the cluster to the Service pass through the ELB load balancer. | v1.19 or later            |
   +--------------------------------+--------+--------------------------------------------------------------------------------------------------------+---------------------------+

For details about the application scenarios, see :ref:`Enabling Passthrough Networking for LoadBalancer Services <cce_10_0355>`.

.. _cce_10_0385__section952710224812:

Host Network
------------

.. table:: **Table 7** Annotations for host network

   +-------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------+---------------------------+
   | Parameter                     | Type            | Description                                                                                                      | Supported Cluster Version |
   +===============================+=================+==================================================================================================================+===========================+
   | kubernetes.io/hws-hostNetwork | String          | If the pod uses **hostNetwork**, the ELB forwards the request to the host network after this annotation is used. | v1.9 or later             |
   |                               |                 |                                                                                                                  |                           |
   |                               |                 | Options:                                                                                                         |                           |
   |                               |                 |                                                                                                                  |                           |
   |                               |                 | -  **true**: enabled                                                                                             |                           |
   |                               |                 | -  **false** (default): disabled                                                                                 |                           |
   +-------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------+---------------------------+

The following shows how to use the preceding annotations:

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     name: nginx
     annotations:
       kubernetes.io/elb.id: <your_elb_id>                         # ELB ID. Replace it with the actual value.
       kubernetes.io/elb.class: union                   # Load balancer type
       kubernetes.io/hws-hostNetwork: 'true'                     # The load balancer forwards the request to the host network.
   spec:
     selector:
        app: nginx
     ports:
     - name: service0
       port: 80
       protocol: TCP
       targetPort: 80
     type: LoadBalancer

.. _cce_10_0385__section117351411644:

Timeout
-------

.. table:: **Table 8** Annotation for configuring timeout

   +-------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------+
   | Parameter                           | Type            | Description                                                                                                                                                                                                                                          | Supported Cluster Version                                       |
   +=====================================+=================+======================================================================================================================================================================================================================================================+=================================================================+
   | kubernetes.io/elb.keepalive_timeout | Integer         | Timeout for client connections. If there are no requests reaching the load balancer after the timeout duration elapses, the load balancer will disconnect the connection with the client and establish a new connection when there is a new request. | v1.19.16-r30, v1.21.10-r10, v1.23.8-r10, v1.25.3-r10, and later |
   |                                     |                 |                                                                                                                                                                                                                                                      |                                                                 |
   |                                     |                 | Value:                                                                                                                                                                                                                                               |                                                                 |
   |                                     |                 |                                                                                                                                                                                                                                                      |                                                                 |
   |                                     |                 | -  For TCP listeners, the value ranges from **10** to **4000** (in seconds). The default value is **300**.                                                                                                                                           |                                                                 |
   |                                     |                 | -  For HTTP, HTTPS, and TERMINATED_HTTPS listeners, the value ranges from **10** to **4000** (in seconds). The default value is **60**.                                                                                                              |                                                                 |
   |                                     |                 | -  For UDP listeners, this parameter does not take effect.                                                                                                                                                                                           |                                                                 |
   +-------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------+

For details about the application scenarios, see :ref:`Configuring Timeout for a LoadBalancer Service <cce_10_0729>`.

Data Structure
--------------

.. _cce_10_0385__table148341447193017:

.. table:: **Table 9** elb.autocreate data structure

   +----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter            | Mandatory                             | Type             | Description                                                                                                                                                                                                                                                                                                                                           |
   +======================+=======================================+==================+=======================================================================================================================================================================================================================================================================================================================================================+
   | name                 | No                                    | String           | Name of the automatically created load balancer.                                                                                                                                                                                                                                                                                                      |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | The value can contain 1 to 64 characters. Only letters, digits, underscores (_), hyphens (-), and periods (.) are allowed.                                                                                                                                                                                                                            |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | Default: **cce-lb+service.UID**                                                                                                                                                                                                                                                                                                                       |
   +----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | type                 | No                                    | String           | Network type of the load balancer.                                                                                                                                                                                                                                                                                                                    |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | -  **public**: public network load balancer                                                                                                                                                                                                                                                                                                           |
   |                      |                                       |                  | -  **inner**: private network load balancer                                                                                                                                                                                                                                                                                                           |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | Default: **inner**                                                                                                                                                                                                                                                                                                                                    |
   +----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | bandwidth_name       | Yes for public network load balancers | String           | Bandwidth name. The default value is **cce-bandwidth-**\ ``******``.                                                                                                                                                                                                                                                                                  |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | The value can contain 1 to 64 characters. Only letters, digits, underscores (_), hyphens (-), and periods (.) are allowed.                                                                                                                                                                                                                            |
   +----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | bandwidth_chargemode | No                                    | String           | Bandwidth mode.                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | -  **traffic**: billed by traffic                                                                                                                                                                                                                                                                                                                     |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | Default: **traffic**                                                                                                                                                                                                                                                                                                                                  |
   +----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | bandwidth_size       | Yes for public network load balancers | Integer          | Bandwidth size. The default value is 1 to 2000 Mbit/s. Configure this parameter based on the bandwidth range allowed in your region.                                                                                                                                                                                                                  |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | The minimum increment for bandwidth adjustment varies depending on the bandwidth range.                                                                                                                                                                                                                                                               |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | -  The minimum increment is 1 Mbit/s if the allowed bandwidth does not exceed 300 Mbit/s.                                                                                                                                                                                                                                                             |
   |                      |                                       |                  | -  The minimum increment is 50 Mbit/s if the allowed bandwidth ranges from 300 Mbit/s to 1000 Mbit/s.                                                                                                                                                                                                                                                 |
   |                      |                                       |                  | -  The minimum increment is 500 Mbit/s if the allowed bandwidth exceeds 1000 Mbit/s.                                                                                                                                                                                                                                                                  |
   +----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | bandwidth_sharetype  | Yes for public network load balancers | String           | Bandwidth sharing mode.                                                                                                                                                                                                                                                                                                                               |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | -  **PER**: dedicated bandwidth                                                                                                                                                                                                                                                                                                                       |
   +----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | eip_type             | Yes for public network load balancers | String           | EIP type.                                                                                                                                                                                                                                                                                                                                             |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | -  **5_bgp**: dynamic BGP                                                                                                                                                                                                                                                                                                                             |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | The specific type varies with regions. For details, see the EIP console.                                                                                                                                                                                                                                                                              |
   +----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | vip_address          | No                                    | String           | Specifies the private IP address of the load balancer. Only IPv4 addresses are supported.                                                                                                                                                                                                                                                             |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | The IP address must be in the ELB CIDR block. If this parameter is not specified, an IP address will be automatically assigned from the ELB CIDR block.                                                                                                                                                                                               |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | This parameter is available only for clusters of v1.23.11-r0, v1.25.6-r0, v1.27.3-r0, or later versions.                                                                                                                                                                                                                                              |
   +----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | available_zone       | Yes                                   | Array of strings | AZ where the load balancer is located.                                                                                                                                                                                                                                                                                                                |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                                                                                                                                        |
   +----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | l4_flavor_name       | Yes                                   | String           | Flavor name of the layer-4 load balancer.                                                                                                                                                                                                                                                                                                             |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                                                                                                                                        |
   +----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | l7_flavor_name       | No                                    | String           | Flavor name of the layer-7 load balancer.                                                                                                                                                                                                                                                                                                             |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | This parameter is available only for dedicated load balancers. The value of this parameter must be the same as that of **l4_flavor_name**, that is, both are elastic specifications or fixed specifications.                                                                                                                                          |
   +----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | elb_virsubnet_ids    | No                                    | Array of strings | Subnet where the backend server of the load balancer is located. If this parameter is left blank, the default cluster subnet is used. Load balancers occupy different number of subnet IP addresses based on their specifications. Do not use the subnet CIDR blocks of other resources (such as clusters and nodes) as the load balancer CIDR block. |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                                                                                                                                        |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | Example:                                                                                                                                                                                                                                                                                                                                              |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  | .. code-block::                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                      |                                       |                  |    "elb_virsubnet_ids": [                                                                                                                                                                                                                                                                                                                             |
   |                      |                                       |                  |       "14567f27-8ae4-42b8-ae47-9f847a4690dd"                                                                                                                                                                                                                                                                                                          |
   |                      |                                       |                  |     ]                                                                                                                                                                                                                                                                                                                                                 |
   +----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0385__table19192143412319:

.. table:: **Table 10** elb.health-check-option data structure

   +-----------------+-----------------+-----------------+----------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type            | Description                                                                            |
   +=================+=================+=================+========================================================================================+
   | delay           | No              | String          | Health check interval (s)                                                              |
   |                 |                 |                 |                                                                                        |
   |                 |                 |                 | Value range: 1 to 50. Default value: **5**                                             |
   +-----------------+-----------------+-----------------+----------------------------------------------------------------------------------------+
   | timeout         | No              | String          | Health check timeout, in seconds.                                                      |
   |                 |                 |                 |                                                                                        |
   |                 |                 |                 | Value range: 1 to 50. Default value: **10**                                            |
   +-----------------+-----------------+-----------------+----------------------------------------------------------------------------------------+
   | max_retries     | No              | String          | Maximum number of health check retries.                                                |
   |                 |                 |                 |                                                                                        |
   |                 |                 |                 | Value range: 1 to 10. Default value: **3**                                             |
   +-----------------+-----------------+-----------------+----------------------------------------------------------------------------------------+
   | protocol        | No              | String          | Health check protocol.                                                                 |
   |                 |                 |                 |                                                                                        |
   |                 |                 |                 | Value options: TCP or HTTP                                                             |
   +-----------------+-----------------+-----------------+----------------------------------------------------------------------------------------+
   | path            | No              | String          | Health check URL. This parameter needs to be configured when the protocol is **HTTP**. |
   |                 |                 |                 |                                                                                        |
   |                 |                 |                 | Default value: **/**                                                                   |
   |                 |                 |                 |                                                                                        |
   |                 |                 |                 | Value range: 1-80 characters                                                           |
   +-----------------+-----------------+-----------------+----------------------------------------------------------------------------------------+

.. _cce_10_0385__table33328411456:

.. table:: **Table 11** Data structure description of the **elb.health-check-options** field

   +---------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter           | Mandatory       | Type            | Description                                                                                                                                  |
   +=====================+=================+=================+==============================================================================================================================================+
   | target_service_port | Yes             | String          | Port for health check specified by spec.ports. The value consists of the protocol and port number, for example, TCP:80.                      |
   +---------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | monitor_port        | No              | String          | Re-specified port for health check. If this parameter is not specified, the service port is used by default.                                 |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 | .. note::                                                                                                                                    |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 |    Ensure that the port is in the listening state on the node where the pod is located. Otherwise, the health check result will be affected. |
   +---------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | delay               | No              | String          | Health check interval (s)                                                                                                                    |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 | Value range: 1 to 50. Default value: **5**                                                                                                   |
   +---------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | timeout             | No              | String          | Health check timeout, in seconds.                                                                                                            |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 | Value range: 1 to 50. Default value: **10**                                                                                                  |
   +---------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | max_retries         | No              | String          | Maximum number of health check retries.                                                                                                      |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 | Value range: 1 to 10. Default value: **3**                                                                                                   |
   +---------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | protocol            | No              | String          | Health check protocol.                                                                                                                       |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 | Default value: protocol of the associated Service                                                                                            |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 | Value options: TCP, UDP, or HTTP                                                                                                             |
   +---------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | path                | No              | String          | Health check URL. This parameter needs to be configured when the protocol is **HTTP**.                                                       |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 | Default value: **/**                                                                                                                         |
   |                     |                 |                 |                                                                                                                                              |
   |                     |                 |                 | Value range: 1-80 characters                                                                                                                 |
   +---------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0385__table3340195463412:

.. table:: **Table 12** elb.session-affinity-option data structure

   +---------------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------+
   | Parameter           | Mandatory       | Type            | Description                                                                                                                  |
   +=====================+=================+=================+==============================================================================================================================+
   | persistence_timeout | Yes             | String          | Sticky session timeout, in minutes. This parameter is valid only when **elb.session-affinity-mode** is set to **SOURCE_IP**. |
   |                     |                 |                 |                                                                                                                              |
   |                     |                 |                 | Value range: 1 to 60. Default value: **60**                                                                                  |
   +---------------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------+
