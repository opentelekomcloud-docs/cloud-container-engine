:original_name: cce_10_0925.html

.. _cce_10_0925:

Configuring a Custom EIP for a LoadBalancer Ingress
===================================================

You can customize the EIP bound to a load balancer that is automatically created by CCE by adding the **kubernetes.io/elb.custom-eip-id** annotation to an ingress.

Prerequisites
-------------

-  A CCE standard or Turbo cluster is available, and the cluster version meets the following requirements:

   -  v1.23: v1.23.18-r0 or later
   -  v1.25: v1.25.13-r0 or later
   -  v1.27: v1.27.10-r0 or later
   -  v1.28: v1.28.8-r0 or later
   -  v1.29: v1.29.4-r0 or later
   -  v1.30: v1.30.1-r0 or later
   -  Other clusters of later versions

-  The cluster can be accessed using kubectl. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

Notes and Constraints
---------------------

-  A custom EIP for an ingress can be configured only when an ingress is being updated, and the ingress' annotation contains **kubernetes.io/elb.eip-id**.
-  A custom EIP must be unbound to any resources.
-  After you configure a custom EIP for a load balancer, if the existing EIP bound to the load balancer was automatically created by CCE during load balancer creation and is not being used by any other resources, the existing EIP will be deleted automatically when the associated ingress is deleted. However, if the existing EIP was manually created, it will be unbound from the load balancer when you delete the ingress, and you will need to manually delete the EIP.

Using kubectl
-------------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Automatically create a load balancer with an EIP bound when creating an ingress. For details, see :ref:`Automatically Creating a Load Balancer While Creating an Ingress <cce_10_0252__section3675115714214>`.

   An example YAML file of an ingress created using a shared load balancer is as follows:

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        annotations:
          kubernetes.io/elb.autocreate: '{"type":"public","bandwidth_name":"test-eip
      ","bandwidth_chargemode":"bandwidth","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_g-vm","name":"test-eip"}'
          kubernetes.io/elb.class: union
          kubernetes.io/elb.eip-id: 10183660-0bb7-47d4-a899-18891b1ab2f7     # ID of the EIP automatically assigned during load balancer creation, which cannot be changed
          kubernetes.io/elb.id: aed5d5c9-65eb-42ab-9f80-57825cbae309
          kubernetes.io/elb.ip: 1.1.1.1
          kubernetes.io/elb.port: "80"
        name: test-eip
        namespace: default
      spec:
        ingressClassName: cce
        rules:
        - http:
            paths:
            - backend:
                service:
                  name: test-eip
                  port:
                    number: 80
              path: /
              pathType: ImplementationSpecific
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
      status:
        loadBalancer:
          ingress:
          - ip: 192.168.1.138

#. Modify the ingress configurations and add the **kubernetes.io/elb.custom-eip-id** annotation.

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        annotations:
          kubernetes.io/elb.autocreate: '{"type":"public","bandwidth_name":"test-eip
      ","bandwidth_chargemode":"bandwidth","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_g-vm","name":"test-eip"}'
          kubernetes.io/elb.class: union
          kubernetes.io/elb.eip-id: 10183660-0bb7-47d4-a899-18891b1ab2f7     # ID of the EIP automatically assigned during load balancer creation, which cannot be changed
          kubernetes.io/elb.custom-eip-id: 57bf8bb2-8c7d-4d07-8799-aae16a421802  # ID of the custom EIP
          kubernetes.io/elb.id: aed5d5c9-65eb-42ab-9f80-57825cbae309
          kubernetes.io/elb.ip: 1.1.1.1
          kubernetes.io/elb.port: "80"
        name: test-eip
        namespace: default
      spec:
        ingressClassName: cce
        rules:
        - http:
            paths:
            - backend:
                service:
                  name: test-eip
                  port:
                    number: 80
              path: /
              pathType: ImplementationSpecific
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
      status:
        loadBalancer:
          ingress:
          - ip: 192.168.1.138

   .. table:: **Table 1** Key parameters

      +---------------------------------+-----------------------+------------------------------------------------------------+
      | Parameter                       | Type                  | Description                                                |
      +=================================+=======================+============================================================+
      | kubernetes.io/elb.custom-eip-id | String                | ID of the custom EIP, which can be seen on the EIP console |
      |                                 |                       |                                                            |
      |                                 |                       | The EIP must be bindable.                                  |
      +---------------------------------+-----------------------+------------------------------------------------------------+

#. After the ingress is updated, check the ingress again.

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        annotations:
          kubernetes.io/elb.autocreate: '{"type":"public","bandwidth_name":"test-eip
      ","bandwidth_chargemode":"bandwidth","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_g-vm","name":"test-eip"}'
          kubernetes.io/elb.class: union
          kubernetes.io/elb.eip-id: 10183660-0bb7-47d4-a899-18891b1ab2f7     # ID of the EIP automatically assigned during load balancer creation, which cannot be changed
          kubernetes.io/elb.custom-eip-id: 57bf8bb2-8c7d-4d07-8799-aae16a421802  # ID of the custom EIP
          kubernetes.io/elb.custom-eip-status: '{"id":"57bf8bb2-8c7d-4d07-8799-aae16a421802","public_ip_address":"3.3.3.3"}' # After the custom EIP is configured, record the EIP's ID and IP address.
          kubernetes.io/elb.id: aed5d5c9-65eb-42ab-9f80-57825cbae309
          kubernetes.io/elb.ip: 1.1.1.1
          kubernetes.io/elb.port: "80"
        name: test-eip
        namespace: default
      spec:
        ingressClassName: cce
        rules:
        - http:
            paths:
            - backend:
                service:
                  name: test-eip
                  port:
                    number: 80
              path: /
              pathType: ImplementationSpecific
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
      status:
        loadBalancer:
          ingress:
          - ip: 192.168.1.138
