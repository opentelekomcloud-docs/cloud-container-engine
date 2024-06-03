:original_name: cce_10_0683.html

.. _cce_10_0683:

Configuring an HTTP or HTTPS Service
====================================

Constraints
-----------

-  Only clusters of v1.19.16 or later support HTTP or HTTPS.

   .. _cce_10_0683__table13284135311563:

   .. table:: **Table 1** Scenarios where a load balancer supports HTTP or HTTPS

      +-------------------------+------------------------------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | ELB Type                | Application scenario                           | Whether to Support HTTP or HTTPS | Description                                                                                                                                                                        |
      +=========================+================================================+==================================+====================================================================================================================================================================================+
      | Shared load balancer    | Interconnecting with an existing load balancer | Yes                              | None                                                                                                                                                                               |
      +-------------------------+------------------------------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      |                         | Automatically creating a load balancer         | Yes                              | None                                                                                                                                                                               |
      +-------------------------+------------------------------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Dedicated load balancer | Interconnecting with an existing load balancer | Yes (A YAML file is required.)   | -  For versions earlier than v1.19.16-r50, v1.21.11-r10, v1.23.9-r10, v1.25.4-r10 and v1.27.1-r10, the load balancer flavor must **support both the layer-4 and layer-7 routing**. |
      |                         |                                                |                                  | -  For v1.19.16-r50, v1.21.11-r10, v1.23.9-r10, v1.25.4-r10, v1.27.1-r10, and later versions, the load balancer flavor **must support layer-7 routing**.                           |
      +-------------------------+------------------------------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      |                         | Automatically creating a load balancer         | Yes (A YAML file is required.)   | -  For versions earlier than v1.19.16-r50, v1.21.11-r10, v1.23.9-r10, v1.25.4-r10 and v1.27.1-r10, the load balancer flavor must **support both the layer-4 and layer-7 routing**. |
      |                         |                                                |                                  | -  For v1.19.16-r50, v1.21.11-r10, v1.23.9-r10, v1.25.4-r10, v1.27.1-r10, and later versions, the load balancer flavor **must support layer-7 routing**.                           |
      +-------------------------+------------------------------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

-  Do not connect an ingress and a Service that uses HTTP or HTTPS to the same listener of the same load balancer. Otherwise, a port conflict occurs.

Using kubectl
-------------

If a Service is HTTP/HTTPS-compliant, add the following annotations:

-  **kubernetes.io/elb.protocol-port**: "https:443,http:80"

   The value of **protocol-port** must be the same as the port in the **spec.ports** field of the Service. The format is *Protocol:Port*. The port matches the one in the **service.spec.ports** field and is released as the corresponding protocol.

-  **kubernetes.io/elb.cert-id**: "17e3b4f4bc40471c86741dc3aa211379"

   **cert-id** indicates the certificate ID in ELB certificate management. When **https** is configured for **protocol-port**, the certificate of the ELB listener will be set to the server certificate. When multiple HTTPS Services are released, they will use the same certificate.

The following is a configuration example for automatically creating a dedicated load balancer, in which key configurations are marked in red:

-  Different ELB types and cluster versions have different requirements on flavors. For details, see :ref:`Table 1 <cce_10_0683__table13284135311563>`.
-  The two ports in **spec.ports** must correspond to those in **kubernetes.io/elb.protocol-port**. In this example, ports 443 and 80 are enabled with HTTPS and HTTP, respectively.

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     annotations:
   # Specify the Layer 4 and Layer 7 flavors in the parameters for automatically creating a load balancer.
       kubernetes.io/elb.autocreate: '
         {
             "type": "public",
             "bandwidth_name": "cce-bandwidth-1634816602057",
             "bandwidth_chargemode": "traffic",
             "bandwidth_size": 5,
             "bandwidth_sharetype": "PER",
             "eip_type": "5_bgp",
             "available_zone": [
                 ""
             ],
             "l7_flavor_name": "L7_flavor.elb.s2.small",
             "l4_flavor_name": "L4_flavor.elb.s1.medium"
         }'
       kubernetes.io/elb.class: performance  # Dedicated load balancer
       kubernetes.io/elb.protocol-port: "https:443,http:80"  # HTTP/HTTPS and port number, which must be the same as the port numbers in spec.ports
       kubernetes.io/elb.cert-id: "17e3b4f4bc40471c86741dc3aa211379"  # Certificate ID of the LoadBalancer Service
     labels:
       app: nginx
       name: test
     name: test
     namespace: default
   spec:
     ports:
     - name: cce-service-0
       port: 443
       protocol: TCP
       targetPort: 80
     - name: cce-service-1
       port: 80
       protocol: TCP
       targetPort: 80
     selector:
       app: nginx
       version: v1
     sessionAffinity: None
     type: LoadBalancer

Use the preceding example configurations to create a Service. In the new ELB load balancer, you can see that the listeners on ports 443 and 80 are created.
