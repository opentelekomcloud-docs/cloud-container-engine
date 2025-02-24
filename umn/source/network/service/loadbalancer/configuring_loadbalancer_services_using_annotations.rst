:original_name: cce_10_0385.html

.. _cce_10_0385:

Configuring LoadBalancer Services Using Annotations
===================================================

You can add annotations to a YAML file to use some CCE advanced functions. This section describes the available annotations when a LoadBalancer service is created.

-  :ref:`Interconnection with ELB <cce_10_0385__section584135019388>`
-  :ref:`Sticky Session <cce_10_0385__section1370139104012>`
-  :ref:`Health Check <cce_10_0385__section1327831714595>`
-  :ref:`HTTP or HTTPS <cce_10_0385__section12416195865818>`
-  :ref:`SNI <cce_10_0385__section7425824134013>`
-  :ref:`Dynamic Adjustment of the Weight of the Backend ECS <cce_10_0385__section3712956145815>`
-  :ref:`Passthrough Capability <cce_10_0385__section5456195255814>`
-  :ref:`Blocklist/Trustlist <cce_10_0385__section79480421873>`
-  :ref:`Host Network <cce_10_0385__section952710224812>`
-  :ref:`Timeout <cce_10_0385__section117351411644>`
-  :ref:`Resource Tags <cce_10_0385__section410825012552>`
-  :ref:`HTTP/2 <cce_10_0385__section17893312104519>`
-  :ref:`Obtaining Client IP Addresses <cce_10_0385__section34631797917>`
-  :ref:`Configuring a Custom EIP <cce_10_0385__section18596171162010>`

.. _cce_10_0385__section584135019388:

Interconnection with ELB
------------------------

.. table:: **Table 1** Annotations for interconnecting with ELB

   +--------------------------------+-----------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------+
   | Parameter                      | Type                                                | Description                                                                                                                                                                                                                                                                                            | Supported Cluster Version                      |
   +================================+=====================================================+========================================================================================================================================================================================================================================================================================================+================================================+
   | kubernetes.io/elb.class        | String                                              | Select a proper load balancer type.                                                                                                                                                                                                                                                                    | v1.9 or later                                  |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     | Options:                                                                                                                                                                                                                                                                                               |                                                |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     | -  **union**: shared load balancer                                                                                                                                                                                                                                                                     |                                                |
   |                                |                                                     | -  **performance**: dedicated load balancer, which can be used only in clusters of v1.17 and later.                                                                                                                                                                                                    |                                                |
   +--------------------------------+-----------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------+
   | kubernetes.io/elb.id           | String                                              | Mandatory **when an existing load balancer is to be associated**.                                                                                                                                                                                                                                      | v1.9 or later                                  |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     | ID of a load balancer.                                                                                                                                                                                                                                                                                 |                                                |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     | **How to obtain**:                                                                                                                                                                                                                                                                                     |                                                |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     | On the management console, click **Service List**, and choose **Networking** > **Elastic Load Balance**. Click the name of the target load balancer. On the **Summary** tab page, find and copy the ID.                                                                                                |                                                |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     | .. note::                                                                                                                                                                                                                                                                                              |                                                |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     |    The system preferentially connects to the load balancer based on the **kubernetes.io/elb.id** field. If this field is not specified, the **spec.loadBalancerIP** field is used (optional and available only in 1.23 and earlier versions).                                                          |                                                |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     |    Do not use the **spec.loadBalancerIP** field to connect to the load balancer. This field will be discarded by Kubernetes. For details, see `Deprecation <https://github.com/kubernetes/kubernetes/blob/8f2371bcceff7962ddb4901c36536c6ff659755b/CHANGELOG/CHANGELOG-1.24.md#changes-by-kind-13>`__. |                                                |
   +--------------------------------+-----------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------+
   | kubernetes.io/elb.autocreate   | :ref:`Table 15 <cce_10_0385__table148341447193017>` | Mandatory **when load balancers are automatically created**.                                                                                                                                                                                                                                           | v1.9 or later                                  |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     | **Example**                                                                                                                                                                                                                                                                                            |                                                |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     | -  Automatically created shared load balancer with an EIP bound:                                                                                                                                                                                                                                       |                                                |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     |    '{"type":"public","bandwidth_name":"cce-bandwidth-1551163379627","bandwidth_chargemode":"traffic","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_bgp","name":"james"}'                                                                                                                |                                                |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     | -  Automatically created shared load balancer with no EIP bound:                                                                                                                                                                                                                                       |                                                |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     |    {"type":"inner","name":"A-location-d-test"}                                                                                                                                                                                                                                                         |                                                |
   +--------------------------------+-----------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------+
   | kubernetes.io/elb.subnet-id    | String                                              | Optional **when load balancers are automatically created**.                                                                                                                                                                                                                                            | Mandatory for clusters earlier than v1.11.7-r0 |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     | ID of the subnet where the cluster is located. The value can contain 1 to 100 characters.                                                                                                                                                                                                              | Discarded in clusters later than v1.11.7-r0    |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     | -  Mandatory when a cluster of v1.11.7-r0 or earlier is to be automatically created.                                                                                                                                                                                                                   |                                                |
   |                                |                                                     | -  Optional for clusters later than v1.11.7-r0.                                                                                                                                                                                                                                                        |                                                |
   +--------------------------------+-----------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------+
   | kubernetes.io/elb.lb-algorithm | String                                              | Specifies the load balancing algorithm of the backend server group. The default value is **ROUND_ROBIN**.                                                                                                                                                                                              | v1.9 or later                                  |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     | Options:                                                                                                                                                                                                                                                                                               |                                                |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     | -  **ROUND_ROBIN**: weighted round robin algorithm                                                                                                                                                                                                                                                     |                                                |
   |                                |                                                     | -  **LEAST_CONNECTIONS**: weighted least connections algorithm                                                                                                                                                                                                                                         |                                                |
   |                                |                                                     | -  **SOURCE_IP**: source IP hash algorithm                                                                                                                                                                                                                                                             |                                                |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     | .. note::                                                                                                                                                                                                                                                                                              |                                                |
   |                                |                                                     |                                                                                                                                                                                                                                                                                                        |                                                |
   |                                |                                                     |    If this parameter is set to **SOURCE_IP**, the weight setting (**weight** field) of backend servers bound to the backend server group is invalid, and sticky session cannot be enabled.                                                                                                             |                                                |
   +--------------------------------+-----------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------+

The following shows how to use the preceding annotations:

-  Associate an existing load balancer. For details, see :ref:`Using kubectl to Create a Service (Using an Existing Load Balancer) <cce_10_0681__section74196215320>`.

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        name: nginx
        annotations:
          kubernetes.io/elb.id: <your_elb_id>                         # Load balancer ID. Replace it with the actual value.
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

-  Automatically create a load balancer. For details, see :ref:`Using kubectl to Create a Service (Automatically Creating a Load Balancer) <cce_10_0681__section6422152185311>`.

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

   +-------------------------------------------+---------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+---------------------------+
   | Parameter                                 | Type                                              | Description                                                                                                                            | Supported Cluster Version |
   +===========================================+===================================================+========================================================================================================================================+===========================+
   | kubernetes.io/elb.session-affinity-mode   | String                                            | Source IP address-based sticky session means that access requests from the same IP address are forwarded to the same backend server.   | v1.9 or later             |
   |                                           |                                                   |                                                                                                                                        |                           |
   |                                           |                                                   | -  Disabling sticky session: Do not configure this parameter.                                                                          |                           |
   |                                           |                                                   | -  Enabling sticky session: Set this parameter to **SOURCE_IP**, indicating that the sticky session is based on the source IP address. |                           |
   |                                           |                                                   |                                                                                                                                        |                           |
   |                                           |                                                   | .. note::                                                                                                                              |                           |
   |                                           |                                                   |                                                                                                                                        |                           |
   |                                           |                                                   |    When **kubernetes.io/elb.lb-algorithm** is set to **SOURCE_IP** (source IP hash), sticky session cannot be enabled.                 |                           |
   +-------------------------------------------+---------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+---------------------------+
   | kubernetes.io/elb.session-affinity-option | :ref:`Table 18 <cce_10_0385__table3340195463412>` | Sticky session timeout.                                                                                                                | v1.9 or later             |
   +-------------------------------------------+---------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+---------------------------+

The following shows how to use the preceding annotations:

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     name: nginx
     annotations:
       kubernetes.io/elb.id: <your_elb_id>                         # Load balancer ID. Replace it with the actual value.
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
   | kubernetes.io/elb.health-check-option  | :ref:`Table 16 <cce_10_0385__table19192143412319>` | ELB health check configuration items.                                                                                                                            | v1.9 or later             |
   +----------------------------------------+----------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+
   | kubernetes.io/elb.health-check-options | :ref:`Table 17 <cce_10_0385__table33328411456>`    | ELB health check configuration items. Each Service port can be configured separately, and you can configure only some ports.                                     | v1.19.16-r5 or later      |
   |                                        |                                                    |                                                                                                                                                                  |                           |
   |                                        |                                                    | .. note::                                                                                                                                                        | v1.21.8-r0 or later       |
   |                                        |                                                    |                                                                                                                                                                  |                           |
   |                                        |                                                    |    Either **kubernetes.io/elb.health-check-option** or **kubernetes.io/elb.health-check-options** can be configured.                                             | v1.23.6-r0 or later       |
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
          kubernetes.io/elb.id: <your_elb_id>                         # Load balancer ID. Replace it with the actual value.
          kubernetes.io/elb.class: union                   # Load balancer type
          kubernetes.io/elb.health-check-flag: 'on'                   # Enable ELB health check.
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

-  For details about how to use **kubernetes.io/elb.health-check-options**, see :ref:`Configuring Health Check on Multiple Ports of a LoadBalancer Service <cce_10_0684>`.

.. _cce_10_0385__section12416195865818:

HTTP or HTTPS
-------------

.. table:: **Table 4** Annotations for using HTTP or HTTPS

   +---------------------------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+
   | Parameter                       | Type            | Description                                                                                                                                                                                                                                             | Supported Cluster Version |
   +=================================+=================+=========================================================================================================================================================================================================================================================+===========================+
   | kubernetes.io/elb.protocol-port | String          | If a Service is HTTP/HTTPS-compliant, configure the protocol and port number in the format of "protocol:port".                                                                                                                                          | v1.19.16 or later         |
   |                                 |                 |                                                                                                                                                                                                                                                         |                           |
   |                                 |                 | where,                                                                                                                                                                                                                                                  |                           |
   |                                 |                 |                                                                                                                                                                                                                                                         |                           |
   |                                 |                 | -  **protocol**: specifies the protocol used by the listener port. The value can be **http** or **https**.                                                                                                                                              |                           |
   |                                 |                 | -  **ports**: Service ports specified by **spec.ports[].port**.                                                                                                                                                                                         |                           |
   +---------------------------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+
   | kubernetes.io/elb.cert-id       | String          | ID of an ELB certificate, which is used as the HTTPS server certificate.                                                                                                                                                                                | v1.19.16 or later         |
   |                                 |                 |                                                                                                                                                                                                                                                         |                           |
   |                                 |                 | To obtain the certificate, log in to the CCE console, choose **Service List** > **Networking** > **Elastic Load Balance**, and click **Certificates** in the navigation pane. In the load balancer list, copy the ID under the target certificate name. |                           |
   +---------------------------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+

For details, see :ref:`Configuring HTTP/HTTPS for a LoadBalancer Service <cce_10_0683>`.

.. _cce_10_0385__section7425824134013:

SNI
---

.. table:: **Table 5** Annotations for using SNIs

   +---------------------------------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------+
   | Parameter                             | Type            | Description                                                                                                                                                                                                                                             | Supported Cluster Version                                 |
   +=======================================+=================+=========================================================================================================================================================================================================================================================+===========================================================+
   | kubernetes.io/elb.tls-certificate-ids | String          | In ELB, the IDs of SNI certificates that must contain a domain name are separated by commas (,).                                                                                                                                                        | v1.23.13-r0, v1.25.8-r0, v1.27.5-r0, v1.28.3-r0, or later |
   |                                       |                 |                                                                                                                                                                                                                                                         |                                                           |
   |                                       |                 | To obtain the certificate, log in to the CCE console, choose **Service List** > **Networking** > **Elastic Load Balance**, and click **Certificates** in the navigation pane. In the load balancer list, copy the ID under the target certificate name. |                                                           |
   +---------------------------------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------+

HTTPS must be enabled. For details, see :ref:`Configuring SNI for a LoadBalancer Service <cce_10_0841>`.

.. _cce_10_0385__section3712956145815:

Dynamic Adjustment of the Weight of the Backend ECS
---------------------------------------------------

.. table:: **Table 6** Annotations for dynamically adjusting the weight of the backend ECS

   +-----------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+
   | Parameter                         | Type            | Description                                                                                                                                                                 | Supported Cluster Version |
   +===================================+=================+=============================================================================================================================================================================+===========================+
   | kubernetes.io/elb.adaptive-weight | String          | Dynamically adjust the weight of the load balancer backend server based on the number pods on the server. In this way, the requests received by each pod are more balanced. | v1.21 or later            |
   |                                   |                 |                                                                                                                                                                             |                           |
   |                                   |                 | -  **true**: enabled                                                                                                                                                        |                           |
   |                                   |                 | -  **false**: disabled                                                                                                                                                      |                           |
   +-----------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------+

.. note::

   This parameter is invalid in passthrough networking, where dedicated load balancers are used in a CCE Turbo cluster.

The following shows how to use the preceding annotations:

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     name: nginx
     annotations:
       kubernetes.io/elb.id: <your_elb_id>                         # Load balancer ID. Replace it with the actual value.
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

Passthrough Capability
----------------------

.. table:: **Table 7** Annotations for passthrough capability

   +--------------------------------+--------+--------------------------------------------------------------------------------------------------------+---------------------------+
   | Parameter                      | Type   | Description                                                                                            | Supported Cluster Version |
   +================================+========+========================================================================================================+===========================+
   | kubernetes.io/elb.pass-through | String | Whether the access requests from within the cluster to the Service pass through the ELB load balancer. | v1.19 or later            |
   +--------------------------------+--------+--------------------------------------------------------------------------------------------------------+---------------------------+

For details, see :ref:`Configuring Passthrough Networking for a LoadBalancer Service <cce_10_0355>`.

.. _cce_10_0385__section79480421873:

Blocklist/Trustlist
-------------------

.. table:: **Table 8** Annotations for ELB access control

   +------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------+
   | Parameter                    | Type            | Description                                                                                                                                                                                                                                                                                                | Supported Cluster Version                                 |
   +==============================+=================+============================================================================================================================================================================================================================================================================================================+===========================================================+
   | kubernetes.io/elb.acl-id     | String          | -  If this parameter is not specified, CCE does not modify access control on the ELB.                                                                                                                                                                                                                      | v1.23.12-r0, v1.25.7-r0, v1.27.4-r0, v1.28.2-r0, or later |
   |                              |                 |                                                                                                                                                                                                                                                                                                            |                                                           |
   |                              |                 | -  If this parameter is left empty, all IP addresses are allowed to access the load balancer.                                                                                                                                                                                                              |                                                           |
   |                              |                 |                                                                                                                                                                                                                                                                                                            |                                                           |
   |                              |                 | -  If this parameter is set to the IP address group ID of the load balancer, access control is enabled and you need to configure an IP address blocklist or trustlist for the load balancer. Additionally, you need to configure both **kubernetes.io/elb.acl-status** and **kubernetes.io/elb.acl-type**. |                                                           |
   |                              |                 |                                                                                                                                                                                                                                                                                                            |                                                           |
   |                              |                 |    **How to obtain**:                                                                                                                                                                                                                                                                                      |                                                           |
   |                              |                 |                                                                                                                                                                                                                                                                                                            |                                                           |
   |                              |                 |    Log in to the console. In the **Service List**, choose **Networking** > **Elastic Load Balance**. On the Network Console, choose **Elastic Load Balance** > **IP Address Groups** and copy the **ID** of the target IP address group.                                                                   |                                                           |
   +------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------+
   | kubernetes.io/elb.acl-status | String          | This parameter is mandatory when you configure an IP address blocklist or trustlist for a load balancer. Options:                                                                                                                                                                                          | v1.23.12-r0, v1.25.7-r0, v1.27.4-r0, v1.28.2-r0, or later |
   |                              |                 |                                                                                                                                                                                                                                                                                                            |                                                           |
   |                              |                 | -  **on**: Access control is enabled.                                                                                                                                                                                                                                                                      |                                                           |
   |                              |                 | -  **off**: Access control is disabled.                                                                                                                                                                                                                                                                    |                                                           |
   +------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------+
   | kubernetes.io/elb.acl-type   | String          | This parameter is mandatory when you configure an IP address blocklist or trustlist for a load balancer. Options:                                                                                                                                                                                          | v1.23.12-r0, v1.25.7-r0, v1.27.4-r0, v1.28.2-r0, or later |
   |                              |                 |                                                                                                                                                                                                                                                                                                            |                                                           |
   |                              |                 | -  **black**: indicates a blocklist. The selected IP address group cannot access the load balancer.                                                                                                                                                                                                        |                                                           |
   |                              |                 | -  **white**: indicates a trustlist. Only the selected IP address group can access the load balancer.                                                                                                                                                                                                      |                                                           |
   +------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------+

The following shows how to use the preceding annotations:

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     name: nginx
     annotations:
       kubernetes.io/elb.id: <your_elb_id>                    # Load balancer ID. Replace it with the actual value.
       kubernetes.io/elb.class: performance                   # Load balancer type
       kubernetes.io/elb.acl-id: <your_acl_id>               # ID of an IP address group for accessing a load balancer
       kubernetes.io/elb.acl-status: 'on'                    # Enable access control.
       kubernetes.io/elb.acl-type: 'white'                   # Trustlist for access control
   spec:
     selector:
        app: nginx
     ports:
     - name: service0
       port: 80
       protocol: TCP
       targetPort: 80
     type: LoadBalancer

.. _cce_10_0385__section952710224812:

Host Network
------------

.. table:: **Table 9** Annotations for host network

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
       kubernetes.io/elb.id: <your_elb_id>                         # Load balancer ID. Replace it with the actual value.
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

.. table:: **Table 10** Annotation for configuring timeout

   +-------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------+
   | Parameter                           | Type            | Description                                                                                                                                                                                                                                                                        | Supported Cluster Version                                                                |
   +=====================================+=================+====================================================================================================================================================================================================================================================================================+==========================================================================================+
   | kubernetes.io/elb.keepalive_timeout | String          | Timeout for client connections. If there are no requests reaching the load balancer during the timeout duration, the load balancer will disconnect the connection from the client and establish a new connection when there is a new request.                                      | Dedicated load balancers: v1.19.16-r30, v1.21.10-r10, v1.23.8-r10, v1.25.3-r10, or later |
   |                                     |                 |                                                                                                                                                                                                                                                                                    |                                                                                          |
   |                                     |                 | Value:                                                                                                                                                                                                                                                                             | Shared load balancers: v1.23.13-r0, v1.25.8-r0, v1.27.5-r0, v1.28.3-r0, or later         |
   |                                     |                 |                                                                                                                                                                                                                                                                                    |                                                                                          |
   |                                     |                 | -  For TCP listeners, the value ranges from **10** to **4000** (in seconds). The default value is **300**.                                                                                                                                                                         |                                                                                          |
   |                                     |                 | -  For HTTP, HTTPS, and TERMINATED_HTTPS listeners, the value ranges from **0** to **4000** (in seconds). The default value is **60**.                                                                                                                                             |                                                                                          |
   |                                     |                 | -  For UDP listeners, this parameter does not take effect.                                                                                                                                                                                                                         |                                                                                          |
   +-------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------+
   | kubernetes.io/elb.client_timeout    | String          | Timeout for waiting for a request from a client. There are two cases:                                                                                                                                                                                                              | v1.23.13-r0, v1.25.8-r0, v1.27.5-r0, v1.28.3-r0, or later                                |
   |                                     |                 |                                                                                                                                                                                                                                                                                    |                                                                                          |
   |                                     |                 | -  If the client fails to send a request header to the load balancer during the timeout duration, the request will be interrupted.                                                                                                                                                 |                                                                                          |
   |                                     |                 | -  If the interval between two consecutive request bodies reaching the load balancer is greater than the timeout duration, the connection will be disconnected.                                                                                                                    |                                                                                          |
   |                                     |                 |                                                                                                                                                                                                                                                                                    |                                                                                          |
   |                                     |                 | The value ranges from **1** to **300** (in seconds). The default value is **60**.                                                                                                                                                                                                  |                                                                                          |
   +-------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------+
   | kubernetes.io/elb.member_timeout    | String          | Timeout for waiting for a response from a backend server. After a request is forwarded to the backend server, if the backend server does not respond within the duration specified by **member_timeout**, the load balancer will stop waiting and return HTTP 504 Gateway Timeout. | v1.23.13-r0, v1.25.8-r0, v1.27.5-r0, v1.28.3-r0, or later                                |
   |                                     |                 |                                                                                                                                                                                                                                                                                    |                                                                                          |
   |                                     |                 | The value ranges from **1** to **300** (in seconds). The default value is **60**.                                                                                                                                                                                                  |                                                                                          |
   +-------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------+

For details, see :ref:`Configuring Timeout for a LoadBalancer Service <cce_10_0729>`.

.. _cce_10_0385__section410825012552:

Resource Tags
-------------

.. table:: **Table 11** Annotations

   +------------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------+
   | Parameter              | Type            | Description                                                                                                                | Supported Cluster Version                     |
   +========================+=================+============================================================================================================================+===============================================+
   | kubernetes.io/elb.tags | String          | Add resource tags to a load balancer. This parameter can be configured only when a load balancer is automatically created. | v1.23.11-r0, v1.25.6-r0, v1.27.3-r0, or later |
   |                        |                 |                                                                                                                            |                                               |
   |                        |                 | A tag is in the format of "key=value". Use commas (,) to separate multiple tags.                                           |                                               |
   +------------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------+

For details, see :ref:`Using kubectl to Create a Service (Automatically Creating a Load Balancer) <cce_10_0681__section6422152185311>`.

.. _cce_10_0385__section17893312104519:

HTTP/2
------

.. table:: **Table 12** Annotations of using HTTP/2

   +--------------------------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------+
   | Parameter                      | Type            | Description                                                                                                                                                                                                                          | Supported Cluster Version                                 |
   +================================+=================+======================================================================================================================================================================================================================================+===========================================================+
   | kubernetes.io/elb.http2-enable | String          | Whether HTTP/2 is enabled. Request forwarding using HTTP/2 improves the access performance between your application and the load balancer. However, the load balancer still uses HTTP/1.x to forward requests to the backend server. | v1.23.13-r0, v1.25.8-r0, v1.27.5-r0, v1.28.3-r0, or later |
   |                                |                 |                                                                                                                                                                                                                                      |                                                           |
   |                                |                 | Options:                                                                                                                                                                                                                             |                                                           |
   |                                |                 |                                                                                                                                                                                                                                      |                                                           |
   |                                |                 | -  **true**: enabled                                                                                                                                                                                                                 |                                                           |
   |                                |                 | -  **false**: disabled (default value)                                                                                                                                                                                               |                                                           |
   |                                |                 |                                                                                                                                                                                                                                      |                                                           |
   |                                |                 | Note: **HTTP/2 can be enabled or disabled only when the listener uses HTTPS.** This parameter is invalid and defaults to **false** when the listener protocol is HTTP.                                                               |                                                           |
   +--------------------------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------+

For details, see :ref:`Configuring HTTP/2 for a LoadBalancer Service <cce_10_0842>`.

.. _cce_10_0385__section34631797917:

Obtaining Client IP Addresses
-----------------------------

.. table:: **Table 13** Annotations for obtaining client IP addresses

   +-----------------------------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------+
   | Parameter                               | Type            | Description                                                                                                                      | Supported Cluster Version                                              |
   +=========================================+=================+==================================================================================================================================+========================================================================+
   | kubernetes.io/elb.transparent-client-ip | String          | This parameter is available only when a shared load balancer is used to create a LoadBalancer Service that is TCP/UDP-compliant. | v1.23.17-r0, v1.25.12-r0, v1.27.9-r0, v1.28.7-r0, v1.29.3-r0, or later |
   |                                         |                 |                                                                                                                                  |                                                                        |
   |                                         |                 | -  **true**: Enabling the function of obtaining the client source IP address.                                                    |                                                                        |
   |                                         |                 | -  **false**: Disabling the function of obtaining the client source IP address.                                                  |                                                                        |
   +-----------------------------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------+

For details, see :ref:`Enabling a LoadBalancer Service to Obtain the Client IP Address <cce_10_0916>`.

.. _cce_10_0385__section18596171162010:

Configuring a Custom EIP
------------------------

.. table:: **Table 14** Annotations of custom EIP configurations

   +---------------------------------+-----------------+------------------------------------------------------------+-----------------------------------------------------------------------------------------------+
   | Parameter                       | Type            | Description                                                | Supported Cluster Version                                                                     |
   +=================================+=================+============================================================+===============================================================================================+
   | kubernetes.io/elb.custom-eip-id | String          | ID of the custom EIP, which can be seen on the EIP console | v1.23.18-r0, v1.25.13-r0, v1.27.10-r0, v1.28.8-r0, v1.29.4-r0, v1.30.1-r0, and later versions |
   |                                 |                 |                                                            |                                                                                               |
   |                                 |                 | The EIP must be bindable.                                  |                                                                                               |
   +---------------------------------+-----------------+------------------------------------------------------------+-----------------------------------------------------------------------------------------------+

For details, see :ref:`Configuring a Custom EIP for a LoadBalancer Service <cce_10_0924>`.

Parameters for Automatically Creating a Load Balancer
-----------------------------------------------------

.. _cce_10_0385__table148341447193017:

.. table:: **Table 15** elb.autocreate data structure

   +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter             | Mandatory                             | Type             | Description                                                                                                                                                                                                                                                                                                                                           |
   +=======================+=======================================+==================+=======================================================================================================================================================================================================================================================================================================================================================+
   | name                  | No                                    | String           | Name of the automatically created load balancer.                                                                                                                                                                                                                                                                                                      |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | The value can contain 1 to 64 characters. Only letters, digits, underscores (_), hyphens (-), and periods (.) are allowed.                                                                                                                                                                                                                            |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | Default: **cce-lb+service.UID**                                                                                                                                                                                                                                                                                                                       |
   +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | type                  | No                                    | String           | Network type of the load balancer.                                                                                                                                                                                                                                                                                                                    |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | -  **public**: public network load balancer                                                                                                                                                                                                                                                                                                           |
   |                       |                                       |                  | -  **inner**: private network load balancer                                                                                                                                                                                                                                                                                                           |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | Default: **inner**                                                                                                                                                                                                                                                                                                                                    |
   +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | bandwidth_name        | Yes for public network load balancers | String           | Bandwidth name. The default value is **cce-bandwidth-**\ ``******``.                                                                                                                                                                                                                                                                                  |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | The value can contain 1 to 64 characters. Only letters, digits, underscores (_), hyphens (-), and periods (.) are allowed.                                                                                                                                                                                                                            |
   +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | bandwidth_chargemode  | No                                    | String           | Bandwidth mode.                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | -  **traffic**: billed by traffic                                                                                                                                                                                                                                                                                                                     |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | Default: **traffic**                                                                                                                                                                                                                                                                                                                                  |
   +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | bandwidth_size        | Yes for public network load balancers | Integer          | Bandwidth size. The value ranges from 1 Mbit/s to 2000 Mbit/s by default. Configure this parameter based on the bandwidth range allowed in your region.                                                                                                                                                                                               |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | The minimum increment for bandwidth adjustment varies depending on the bandwidth range.                                                                                                                                                                                                                                                               |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | -  The minimum increment is 1 Mbit/s if the allowed bandwidth does not exceed 300 Mbit/s.                                                                                                                                                                                                                                                             |
   |                       |                                       |                  | -  The minimum increment is 50 Mbit/s if the allowed bandwidth ranges from 300 Mbit/s to 1000 Mbit/s.                                                                                                                                                                                                                                                 |
   |                       |                                       |                  | -  The minimum increment is 500 Mbit/s if the allowed bandwidth exceeds 1000 Mbit/s.                                                                                                                                                                                                                                                                  |
   +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | bandwidth_sharetype   | Yes for public network load balancers | String           | Bandwidth sharing mode.                                                                                                                                                                                                                                                                                                                               |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | -  **PER**: dedicated bandwidth                                                                                                                                                                                                                                                                                                                       |
   +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | eip_type              | Yes for public network load balancers | String           | EIP type.                                                                                                                                                                                                                                                                                                                                             |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | -  **5_bgp**: dynamic BGP                                                                                                                                                                                                                                                                                                                             |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | The specific type varies with regions. For details, see the EIP console.                                                                                                                                                                                                                                                                              |
   +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | vip_subnet_cidr_id    | No                                    | String           | Subnet where a load balancer is located. The subnet must belong to the VPC where the cluster resides.                                                                                                                                                                                                                                                 |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | If this parameter is not specified, the ELB load balancer and the cluster are in the same subnet.                                                                                                                                                                                                                                                     |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | This field can be specified only for clusters of v1.21 or later.                                                                                                                                                                                                                                                                                      |
   +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | vip_address           | No                                    | String           | Private IP address of the load balancer. Only IPv4 addresses are supported.                                                                                                                                                                                                                                                                           |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | The IP address must be in the ELB CIDR block. If this parameter is not specified, an IP address will be automatically assigned from the ELB CIDR block.                                                                                                                                                                                               |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | This parameter is available only in clusters of v1.23.11-r0, v1.25.6-r0, v1.27.3-r0, or later versions.                                                                                                                                                                                                                                               |
   +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | available_zone        | Yes                                   | Array of strings | AZ where the load balancer is located.                                                                                                                                                                                                                                                                                                                |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | You can obtain all supported AZs by `getting the AZ list <https://docs.otc.t-systems.com/api/elb/ListAvailabilityZones.html>`__.                                                                                                                                                                                                                      |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                                                                                                                                        |
   +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | l4_flavor_name        | Yes                                   | String           | Flavor name of the layer-4 load balancer.                                                                                                                                                                                                                                                                                                             |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | You can obtain all supported types by `getting the flavor list <https://docs.otc.t-systems.com/api/elb/ListFlavors.html>`__.                                                                                                                                                                                                                          |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                                                                                                                                        |
   +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | l7_flavor_name        | No                                    | String           | Flavor name of the layer-7 load balancer.                                                                                                                                                                                                                                                                                                             |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | You can obtain all supported types by `getting the flavor list <https://docs.otc.t-systems.com/api/elb/ListFlavors.html>`__.                                                                                                                                                                                                                          |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | This parameter is available only for dedicated load balancers. The value of this parameter must be the same as that of **l4_flavor_name**, that is, both are elastic specifications or fixed specifications.                                                                                                                                          |
   +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | elb_virsubnet_ids     | No                                    | Array of strings | Subnet where the backend server of the load balancer is located. If this parameter is left blank, the default cluster subnet is used. Load balancers occupy different number of subnet IP addresses based on their specifications. Do not use the subnet CIDR blocks of other resources (such as clusters and nodes) as the load balancer CIDR block. |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                                                                                                                                        |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | Example:                                                                                                                                                                                                                                                                                                                                              |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | .. code-block::                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  |    "elb_virsubnet_ids": [                                                                                                                                                                                                                                                                                                                             |
   |                       |                                       |                  |       "14567f27-8ae4-42b8-ae47-9f847a4690dd"                                                                                                                                                                                                                                                                                                          |
   |                       |                                       |                  |     ]                                                                                                                                                                                                                                                                                                                                                 |
   +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | ipv6_vip_virsubnet_id | No                                    | String           | ID of the IPv6 subnet where the load balancer resides. IPv6 must be enabled for the corresponding subnet. This parameter is mandatory only when the dual-stack clusters are used.                                                                                                                                                                     |
   |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
   |                       |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                                                                                                                                        |
   +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0385__table19192143412319:

.. table:: **Table 16** elb.health-check-option data structure

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

.. table:: **Table 17** elb.health-check-options

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

.. table:: **Table 18** elb.session-affinity-option data structure

   +---------------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------+
   | Parameter           | Mandatory       | Type            | Description                                                                                                                  |
   +=====================+=================+=================+==============================================================================================================================+
   | persistence_timeout | Yes             | String          | Sticky session timeout, in minutes. This parameter is valid only when **elb.session-affinity-mode** is set to **SOURCE_IP**. |
   |                     |                 |                 |                                                                                                                              |
   |                     |                 |                 | Value range: 1 to 60. Default value: **60**                                                                                  |
   +---------------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------+
