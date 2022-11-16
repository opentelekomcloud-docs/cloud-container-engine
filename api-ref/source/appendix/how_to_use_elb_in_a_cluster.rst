:original_name: cce_02_0087.html

.. _cce_02_0087:

How to Use ELB in a Cluster
===========================

This section describes how to use the Elastic Load Balancer (ELB) in a cluster created by CCE.

Procedure
---------

Create a Service.

.. note::

   Before enabling sticky session, ensure that the following conditions are met:

   -  The workload protocol is TCP.
   -  Anti-affinity has been configured between pods of the workload. That is, all pods of the workload are deployed on different nodes.

Automatically creating an ELB:

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     annotations:
       kubernetes.io/elb.class: union
       kubernetes.io/session-affinity-mode: SOURCE_IP
       kubernetes.io/elb.subnet-id: 5083f225-9bf8-48fa-9c8b-67bd9693c4c0
       kubernetes.io/elb.autocreate: "{\"type\":\"public\",\"bandwidth_name\":\"cce-bandwidth-1551163379627\",\"bandwidth_chargemode\":\"traffic\",\"bandwidth_size\":5,\"bandwidth_sharetype\":\"PER\",\"eip_type\":\"5_bgp\",\"name\":\"james\"}"
     labels:
       app: nginx
     name: nginx
   spec:
     externalTrafficPolicy: Local
     ports:
     - name: service0
       port: 80
       protocol: TCP
       targetPort: 80
     selector:
       app: nginx
     type: LoadBalancer

Using an existing ELB:

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     annotations:
       kubernetes.io/elb.class: union
       kubernetes.io/session-affinity-mode: SOURCE_IP
       kubernetes.io/elb.id: 3c7caa5a-a641-4bff-801a-feace27424b6
       kubernetes.io/elb.subnet-id: 5083f225-9bf8-48fa-9c8b-67bd9693c4c0
     labels:
       app: nginx
     name: nginx
   spec:
     loadBalancerIP: 10.78.42.242
     externalTrafficPolicy: Local
     ports:
     - name: service0
       port: 80
       protocol: TCP
       targetPort: 80
     selector:
       app: nginx
     type: LoadBalancer

.. table:: **Table 1** Key parameters

   +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                           | Type                                                            | Description                                                                                                                                                                                                                                                                                                              |
   +=====================================+=================================================================+==========================================================================================================================================================================================================================================================================================================================+
   | kubernetes.io/elb.class             | String                                                          | Mandatory and must be set to **union** if an enhanced load balancer is in use.                                                                                                                                                                                                                                           |
   +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | kubernetes.io/session-affinity-mode | String                                                          | Optional. If sticky session is enabled, add this parameter.                                                                                                                                                                                                                                                              |
   |                                     |                                                                 |                                                                                                                                                                                                                                                                                                                          |
   |                                     |                                                                 | The value **SOURCE_IP** indicates that listeners ensure sticky session based on source IP addresses.                                                                                                                                                                                                                     |
   +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | kubernetes.io/elb.id                | String                                                          | Optional. This parameter is mandatory if an existing ELB is used.                                                                                                                                                                                                                                                        |
   |                                     |                                                                 |                                                                                                                                                                                                                                                                                                                          |
   |                                     |                                                                 | It indicates the ID of an enhanced load balancer.                                                                                                                                                                                                                                                                        |
   +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | kubernetes.io/elb.subnet-id         | String                                                          | Optional. This parameter is mandatory only if a load balancer will be automatically created. For clusters of v1.11.7-r0 or later, this parameter can be left unspecified.                                                                                                                                                |
   +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | kubernetes.io/elb.autocreate        | :ref:`elb.autocreate <cce_02_0087__table19417184671919>` object | Optional. This parameter is mandatory if a public network load balancer will be automatically created. The system will create an enhanced load balancer and an EIP. This parameter is also mandatory if a private network load balancer will be automatically created. The system will create an enhanced load balancer. |
   |                                     |                                                                 |                                                                                                                                                                                                                                                                                                                          |
   |                                     |                                                                 | **Example:**                                                                                                                                                                                                                                                                                                             |
   |                                     |                                                                 |                                                                                                                                                                                                                                                                                                                          |
   |                                     |                                                                 | -  Value for a public network load balancer that is automatically created: "{\\"type\\":\\"public\\",\\"bandwidth_name\\":\\"cce-bandwidth-1551163379627\\",\\"bandwidth_chargemode\\":\\"traffic\\",\\"bandwidth_size\\":5,\\"bandwidth_sharetype\\":\\"PER\\",\\"eip_type\\":\\"5_bgp\\",\\"name\\":\\"james\\"}"      |
   |                                     |                                                                 | -  Value for a private network load balancer that is automatically created: "{\\"type\\":\\"inner\\"}"                                                                                                                                                                                                                   |
   +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | loadBalancerIP                      | String                                                          | Private IP address of a private network load balancer or public IP address of a public network load balancer.                                                                                                                                                                                                            |
   +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | externalTrafficPolicy               | String                                                          | Optional. If sticky session is enabled, add this parameter so requests are transferred to a fixed node. If a LoadBalancer Service with this parameter set to **Local** is created for a workload, the workload can be accessed only when the client is installed on the same node as the server.                         |
   +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | port                                | Integer                                                         | Access port that is registered on the load balancer and mapped to the cluster-internal IP address.                                                                                                                                                                                                                       |
   +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | targetPort                          | String                                                          | Container port on the CCE console.                                                                                                                                                                                                                                                                                       |
   +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0087__table19417184671919:

.. table:: **Table 2** Data structure of the **elb.autocreate** field

   +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | Parameter             | Type                  | Description                                                                                                                 |
   +=======================+=======================+=============================================================================================================================+
   | name                  | String                | Name of the load balancer that is automatically created.                                                                    |
   |                       |                       |                                                                                                                             |
   |                       |                       | The value is a string of 1 to 64 characters that consist of letters, digits, underscores (_), and hyphens (-).              |
   +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | type                  | String                | Network type of the load balancer.                                                                                          |
   |                       |                       |                                                                                                                             |
   |                       |                       | -  **public**: public network load balancer.                                                                                |
   |                       |                       | -  **inner**: private network load balancer.                                                                                |
   +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | bandwidth_name        | String                | Bandwidth name. The default value is **cce-bandwidth-*****\***.                                                             |
   |                       |                       |                                                                                                                             |
   |                       |                       | The value is a string of 1 to 64 characters that consist of letters, digits, underscores (_), hyphens (-), and periods (.). |
   +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | bandwidth_chargemode  | String                | Bandwidth billing mode.                                                                                                     |
   |                       |                       |                                                                                                                             |
   |                       |                       | The value is **traffic**, indicating that the billing is based on traffic.                                                  |
   +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | bandwidth_size        | Integer               | Bandwidth size.                                                                                                             |
   +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | bandwidth_sharetype   | String                | Bandwidth sharing mode.                                                                                                     |
   |                       |                       |                                                                                                                             |
   |                       |                       | -  **PER**: dedicated bandwidth.                                                                                            |
   |                       |                       | -  **WHOLE**: shared bandwidth.                                                                                             |
   +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | eip_type              | String                | EIP type.                                                                                                                   |
   +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------+
