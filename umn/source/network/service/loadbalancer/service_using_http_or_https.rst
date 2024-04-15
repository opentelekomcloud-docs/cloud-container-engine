:original_name: cce_10_0683.html

.. _cce_10_0683:

Service Using HTTP or HTTPS
===========================

Constraints
-----------

-  Only clusters of v1.19.16 or later support HTTP or HTTPS.
-  Do not connect an Ingress and a Service that uses HTTP or HTTPS to the same listener of the same load balancer. Otherwise, a port conflict occurs.

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
