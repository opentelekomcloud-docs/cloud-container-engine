:original_name: cce_10_0924.html

.. _cce_10_0924:

Changing a Custom EIP for a LoadBalancer Service
================================================

You can customize the EIP bound to a load balancer that is automatically created by CCE by adding the **kubernetes.io/elb.custom-eip-id** annotation to a Service.

Prerequisites
-------------

-  A Kubernetes cluster is available and the cluster version meets the following requirements:

   -  v1.23: v1.23.18-r0 or later
   -  v1.25: v1.25.13-r0 or later
   -  v1.27: v1.27.10-r0 or later
   -  v1.28: v1.28.8-r0 or later
   -  v1.29: v1.29.4-r0 or later
   -  v1.30: v1.30.1-r0 or later

-  The cluster can be accessed using kubectl. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

Notes and Constraints
---------------------

-  A custom EIP for a Service can be configured only when the Service is being updated, and the Service's annotation contains **kubernetes.io/elb.eip-id**.
-  A custom EIP must be unbound to any resources.
-  After you configure a custom EIP for a load balancer, if the existing EIP bound to the load balancer was automatically created by CCE during load balancer creation and is not being used by any other resources, the existing EIP will be deleted automatically when the associated Service is deleted. However, if the existing EIP was manually created, it will be unbound from the load balancer when you delete the Service, and you will need to manually delete the EIP.

Using kubectl
-------------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Automatically create a load balancer with an EIP bound when creating a Service. For details, see :ref:`Using kubectl to Create a Service (Automatically Creating a Load Balancer) <cce_10_0681__section6422152185311>`.

   An example YAML file of a Service created using a dedicated load balancer is as follows:

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          kubernetes.io/elb.autocreate: '{"type":"public","bandwidth_name":"aaaaa","bandwidth_chargemode":"bandwidth","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_g-vm","name":"xxx","available_zone":["xxx"],"elb_virsubnet_ids":["fc0c61cd-c987-49c4-99a4-b7d816b57581"],"l7_flavor_name":"","l4_flavor_name":"L4_flavor.elb.pro.max","vip_subnet_cidr_id":"cf35b03f-c6ca-4f75-aa70-e2166cb1f800"}'
          kubernetes.io/elb.eip-id: 8560972c-2cc5-4699-94d6-e46f146eb73d     # ID of the EIP automatically assigned during load balancer creation
          kubernetes.io/elb.class: performance
          kubernetes.io/elb.id: 0e78a84a-7deb-4747-aeb6-09b6a820b001
        labels:
          app: test-svc
          version: v1
        name: test-eip
        namespace: default
      spec:
        allocateLoadBalancerNodePorts: true
        clusterIP: 10.247.93.235
        clusterIPs:
        - 10.247.93.235
        externalTrafficPolicy: Cluster
        internalTrafficPolicy: Cluster
        ipFamilies:
        - IPv4
        ipFamilyPolicy: SingleStack
        loadBalancerIP: *.*.*.*
        ports:
        - name: cce-service-0
          nodePort: 31354
          port: 80
          protocol: TCP
          targetPort: 80
        selector:
          app: test-svc
          version: v1
        sessionAffinity: None
        type: LoadBalancer
      status:
        loadBalancer:
          ingress:
          - ip: *.*.*.*
          - ip: 192.168.0.15

#. Modify the Service configurations and add the **kubernetes.io/elb.custom-eip-id** annotation.

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          kubernetes.io/elb.autocreate: '{"type":"public","bandwidth_name":"aaaaa","bandwidth_chargemode":"bandwidth","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_g-vm","name":"xxx","available_zone":["xxx"],"elb_virsubnet_ids":["fc0c61cd-c987-49c4-99a4-b7d816b57581"],"l7_flavor_name":"","l4_flavor_name":"L4_flavor.elb.pro.max","vip_subnet_cidr_id":"cf35b03f-c6ca-4f75-aa70-e2166cb1f800"}'
          kubernetes.io/elb.eip-id: 8560972c-2cc5-4699-94d6-e46f146eb73d     # ID of the EIP automatically assigned during load balancer creation
          kubernetes.io/elb.custom-eip-id: 88c197a1-cb85-4b38-b672-1d60dc5d00db  # ID of the custom EIP
          kubernetes.io/elb.class: performance
          kubernetes.io/elb.id: 0e78a84a-7deb-4747-aeb6-09b6a820b001
        labels:
          app: test-svc
          version: v1
        name: test-eip
        namespace: default
      spec:
        allocateLoadBalancerNodePorts: true
        clusterIP: 10.247.93.235
        clusterIPs:
        - 10.247.93.235
        externalTrafficPolicy: Cluster
        internalTrafficPolicy: Cluster
        ipFamilies:
        - IPv4
        ipFamilyPolicy: SingleStack
        loadBalancerIP: *.*.*.*
        ports:
        - name: cce-service-0
          nodePort: 31354
          port: 80
          protocol: TCP
          targetPort: 80
        selector:
          app: test-svc
          version: v1
        sessionAffinity: None
        type: LoadBalancer
      status:
        loadBalancer:
          ingress:
          - ip: *.*.*.*
          - ip: 192.168.0.15

   .. table:: **Table 1** Key parameters

      +---------------------------------+-----------------------+------------------------------------------------------------+
      | Parameter                       | Type                  | Description                                                |
      +=================================+=======================+============================================================+
      | kubernetes.io/elb.custom-eip-id | String                | ID of the custom EIP, which can be seen on the EIP console |
      |                                 |                       |                                                            |
      |                                 |                       | The EIP must be bindable.                                  |
      +---------------------------------+-----------------------+------------------------------------------------------------+

#. After the Service is updated, check the Service again.

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          kubernetes.io/elb.autocreate: '{"type":"public","bandwidth_name":"aaaaa","bandwidth_chargemode":"bandwidth","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_g-vm","name":"xxx","available_zone":["xxx"],"elb_virsubnet_ids":["fc0c61cd-c987-49c4-99a4-b7d816b57581"],"l7_flavor_name":"","l4_flavor_name":"L4_flavor.elb.pro.max","vip_subnet_cidr_id":"cf35b03f-c6ca-4f75-aa70-e2166cb1f800"}'
          kubernetes.io/elb.eip-id: 8560972c-2cc5-4699-94d6-e46f146eb73d     # ID of the EIP automatically assigned during load balancer creation
          kubernetes.io/elb.custom-eip-id: 88c197a1-cb85-4b38-b672-1d60dc5d00db  # ID of the custom EIP
          kubernetes.io/elb.custom-eip-status: '{"id":"88c197a1-cb85-4b38-b672-1d60dc5d00db","public_ip_address":"2.2.2.2"}' # After the custom EIP is configured, record the EIP and its ID.
          kubernetes.io/elb.class: performance
          kubernetes.io/elb.id: 0e78a84a-7deb-4747-aeb6-09b6a820b001
        labels:
          app: test-svc
          version: v1
        name: test-eip
        namespace: default
      spec:
        allocateLoadBalancerNodePorts: true
        clusterIP: 10.247.93.235
        clusterIPs:
        - 10.247.93.235
        externalTrafficPolicy: Cluster
        internalTrafficPolicy: Cluster
        ipFamilies:
        - IPv4
        ipFamilyPolicy: SingleStack
        loadBalancerIP: 2.2.2.2
        ports:
        - name: cce-service-0
          nodePort: 31354
          port: 80
          protocol: TCP
          targetPort: 80
        selector:
          app: test-svc
          version: v1
        sessionAffinity: None
        type: LoadBalancer
      status:
        loadBalancer:
          ingress:
          - ip: 2.2.2.2
          - ip: 192.168.0.15
