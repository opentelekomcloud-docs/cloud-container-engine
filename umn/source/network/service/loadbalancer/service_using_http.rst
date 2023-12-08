:original_name: cce_10_0683.html

.. _cce_10_0683:

Service Using HTTP
==================

Constraints
-----------

-  Only clusters of v1.19.16 or later support HTTP.

-  Do not connect the ingress and Service that uses HTTP to the same listener of the same load balancer. Otherwise, a port conflict occurs.

-  Layer-7 routing of ELB can be enabled for Services. Both shared and dedicated ELB load balancers can be interconnected.

   Restrictions on dedicated ELB load balancers are as follows:

   -  To interconnect with an existing dedicated load balancer, the load balancer flavor **must support both the layer-4 and layer-7 routing**. Otherwise, the load balancer will not work as expected.
   -  If you use an automatically created load balancer, you cannot use the CCE console to automatically create a layer-7 dedicated load balancer. Instead, you can use YAML to create a layer-7 dedicated load balancer, use both the layer-4 and layer-7 capabilities of the exclusive ELB instance (that is, specify the layer-4 and layer-7 flavors in the annotation of kubernetes.io/elb.autocreate).


Service Using HTTP
------------------

The following annotations need to be added:

-  **kubernetes.io/elb.protocol-port**: "https:443,http:80"

   The value of **protocol-port** must be the same as the port in the **spec.ports** field of the Service. The format is *Protocol:Port*. The port matches the one in the **service.spec.ports** field and is released as the corresponding protocol.

-  **kubernetes.io/elb.cert-id**: "17e3b4f4bc40471c86741dc3aa211379"

   **cert-id** indicates the certificate ID in ELB certificate management. When **https** is configured for **protocol-port**, the certificate of the ELB listener will be set to the **cert-id** certificate. When multiple HTTPS services are released, the same certificate is used.

The following is a configuration example. The two ports in **spec.ports** correspond to those in **kubernetes.io/elb.protocol-port**. Ports 443 and 80 are enabled for HTTPS and HTTP requests, respectively.

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     annotations:
       # When an ELB load balancer is automatically created, both layer-4 and layer-7 flavors need to be specified.
       kubernetes.io/elb.autocreate: '
         {
             "type": "public",
             "bandwidth_name": "cce-bandwidth-1634816602057",
             "bandwidth_chargemode": "bandwidth",
             "bandwidth_size": 5,
             "bandwidth_sharetype": "PER",
             "eip_type": "5_bgp",
             "available_zone": [
                 ""
             ],
             "l7_flavor_name": "L7_flavor.elb.s2.small"
         }'
       kubernetes.io/elb.class: performance
       kubernetes.io/elb.protocol-port: "https:443,http:80"
       kubernetes.io/elb.cert-id: "17e3b4f4bc40471c86741dc3aa211379"
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
