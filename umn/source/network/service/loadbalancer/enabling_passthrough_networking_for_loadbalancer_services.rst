:original_name: cce_10_0355.html

.. _cce_10_0355:

Enabling Passthrough Networking for LoadBalancer Services
=========================================================

Challenges
----------

A Kubernetes cluster can publish applications running on a group of pods as Services, which provide unified layer-4 access entries. For a Loadbalancer Service, kube-proxy configures the LoadbalanceIP in **status** of the Service to the local forwarding rule of the node by default. When a pod accesses the load balancer from within the cluster, the traffic is forwarded within the cluster instead of being forwarded by the load balancer.

kube-proxy is responsible for intra-cluster forwarding. kube-proxy has two forwarding modes: iptables and IPVS. iptables is a simple polling forwarding mode. IPVS has multiple forwarding modes but it requires modifying the startup parameters of kube-proxy. Compared with iptables and IPVS, load balancers provide more flexible forwarding policies as well as health check capabilities.

Solution
--------

CCE supports passthrough networking. You can configure the **annotation** of **kubernetes.io/elb.pass-through** for the Loadbalancer Service. Intra-cluster access to the Service load balancer address is then forwarded to backend pods by the load balancer.


.. figure:: /_static/images/en-us_image_0000001695736965.png
   :alt: **Figure 1** Passthrough networking illustration

   **Figure 1** Passthrough networking illustration

-  CCE clusters

   When a LoadBalancer Service is accessed within the cluster, the access is forwarded to the backend pods using iptables/IPVS by default.

   When a LoadBalancer Service (configured with elb.pass-through) is accessed within the cluster, the access is first forwarded to the load balancer, then the nodes, and finally to the backend pods using iptables/IPVS.

-  CCE Turbo clusters

   When a LoadBalancer Service is accessed within the cluster, the access is forwarded to the backend pods using iptables/IPVS by default.

   When a LoadBalancer Service (configured with elb.pass-through) is accessed within the cluster, the access is first forwarded to the load balancer, and then to the pods.

Constraints
-----------

-  After passthrough networking is configured for a dedicated load balancer, containers on the node where the workload runs cannot be accessed through the Service.
-  Passthrough networking is not supported for clusters of v1.15 or earlier.
-  In IPVS network mode, the pass-through settings of Service connected to the same ELB must be the same.

Procedure
---------

This section describes how to create a Deployment using an Nginx image and create a Service with passthrough networking enabled.

#. Use the Nginx image to create a Deployment.

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nginx
      spec:
        replicas: 2
        selector:
          matchLabels:
            app: nginx
        template:
          metadata:
            labels:
              app: nginx
          spec:
            containers:
            - image: nginx:latest
              name: container-0
              resources:
                limits:
                  cpu: 100m
                  memory: 200Mi
                requests:
                  cpu: 100m
                  memory: 200Mi
            imagePullSecrets:
            - name: default-secret

#. Create a LoadBalancer Service and configure **kubernetes.io/elb.pass-through** to **true**.

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          kubernetes.io/elb.pass-through: "true"
          kubernetes.io/elb.class: union
          kubernetes.io/elb.autocreate: '{"type":"public","bandwidth_name":"cce-bandwidth","bandwidth_chargemode":"bandwidth","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_bgp","name":"james"}'
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

   A shared load balancer named **james** is automatically created. Use **kubernetes.io/elb.subnet-id** to specify the VPC subnet where the load balancer is located. The load balancer and the cluster must be in the same VPC.

Verification
------------

Check the ELB load balancer corresponding to the created Service. The load balancer name is **james**. The number of ELB connections is **0**, as shown in the following figure.

|image1|

Use kubectl to connect to the cluster, go to an Nginx container, and access the ELB address. The access is successful.

.. code-block::

   # kubectl get pod
   NAME                     READY   STATUS    RESTARTS   AGE
   nginx-7c4c5cc6b5-vpncx   1/1     Running   0          9m47s
   nginx-7c4c5cc6b5-xj5wl   1/1     Running   0          9m47s
   # kubectl exec -it nginx-7c4c5cc6b5-vpncx -- /bin/sh
   # curl 120.46.141.192
   <!DOCTYPE html>
   <html>
   <head>
   <title>Welcome to nginx!</title>
   <style>
       body {
           width: 35em;
           margin: 0 auto;
           font-family: Tahoma, Verdana, Arial, sans-serif;
       }
   </style>
   </head>
   <body>
   <h1>Welcome to nginx!</h1>
   <p>If you see this page, the nginx web server is successfully installed and
   working. Further configuration is required.</p>

   <p>For online documentation and support please refer to
   <a href="http://nginx.org/">nginx.org</a>.<br/>
   Commercial support is available at
   <a href="http://nginx.com/">nginx.com</a>.</p>

   <p><em>Thank you for using nginx.</em></p>
   </body>
   </html>

Wait for a period of time and view the ELB monitoring data. A new access connection is created for the ELB, indicating that the access passes through the ELB load balancer as expected.

|image2|

.. |image1| image:: /_static/images/en-us_image_0000001647576552.png
.. |image2| image:: /_static/images/en-us_image_0000001647417300.png
