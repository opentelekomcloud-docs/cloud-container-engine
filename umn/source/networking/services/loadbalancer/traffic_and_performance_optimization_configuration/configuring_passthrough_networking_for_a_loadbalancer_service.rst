:original_name: cce_10_0355.html

.. _cce_10_0355:

Configuring Passthrough Networking for a LoadBalancer Service
=============================================================

Applications
------------

kube-proxy, which is responsible for forwarding intra-cluster traffic, adds the IP addresses of load balancers associated with the LoadBalancer Services to nodes' local forwarding rules by default. When a client from within a cluster accesses the IP address of a load balancer, the traffic is directly forwarded to the destination instead of being forwarded by the load balancer.

Solution
--------

CCE supports passthrough networking. You can configure the **kubernetes.io/elb.pass-through** annotation for the LoadBalancer Services so that the load balancers forward the intra-cluster access to the IP addresses of the load balancers associated with the Services to their backend pods.


.. figure:: /_static/images/en-us_image_0000001927010505.png
   :alt: **Figure 1** Passthrough networking illustration

   **Figure 1** Passthrough networking illustration

-  CCE standard clusters

   When a LoadBalancer Service is accessed from within the cluster, traffic is routed to the backend pods using iptables or IPVS by default.

   When a LoadBalancer Service (configured with **elb.pass-through**) is accessed from within the cluster, traffic is first forwarded to the load balancer, then to the nodes, and finally routed to the backend pods using iptables or IPVS.

-  CCE Turbo clusters

   When a LoadBalancer Service is accessed from within the cluster, traffic is routed to the backend pods using iptables or IPVS by default.

   When a LoadBalancer Service is accessed from a pod within the cluster, passthrough will be used by default. The client connects to the load balancer's private IP address, and traffic is routed directly to the pods through the load balancer.

Constraints
-----------

-  In a CCE standard cluster, after passthrough networking is configured using a dedicated load balancer, the private IP address of the load balancer cannot be accessed from the node where a workload pod resides or other pods on the same node as the workload.
-  This function is only available in clusters v1.19 and later.
-  In IPVS, the passthrough settings of Services connected to the same load balancer must be the same.
-  If node-level (local) service affinity is used, **kubernetes.io/elb.pass-through** is automatically set to **onlyLocal** to enable passthrough networking.

Procedure
---------

This section describes how to create a Deployment using an Nginx image and create a Service with passthrough networking enabled.

#. Use kubectl to access the cluster.

#. Use the Nginx image to create a Deployment.

   Create an **nginx-deployment.yaml** file. The file content is as follows:

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

   Deploy the workload.

   .. code-block::

      kubectl create -f nginx-deployment.yaml

#. Create a LoadBalancer Service and set **kubernetes.io/elb.pass-through** to **true**.

   The content of the **nginx-elb-svc.yaml** file is shown below. In this example, a shared load balancer named **james** is automatically created.

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          kubernetes.io/elb.pass-through: "true"
          kubernetes.io/elb.class: union
          kubernetes.io/elb.autocreate: '{"type":"public","bandwidth_name":"cce-bandwidth","bandwidth_chargemode":"traffic","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_bgp","name":"james"}'
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

#. Create the Service.

   .. code-block::

      kubectl create -f nginx-elb-svc.yaml

Verification
------------

#. Log in to the ELB console and check the load balancer (named **james** in this example) associated with the Service.

#. Click the load balancer name and click the **Monitoring** tab.

   There are 0 connections to the load balancer.

#. Log in to an Nginx container in the cluster using kubectl and access the IP address of the load balancer.

   a. Obtain the Nginx containers in the cluster.

      .. code-block::

         kubectl get pod

      Information similar to the following is displayed:

      .. code-block::

         NAME                     READY   STATUS    RESTARTS   AGE
         nginx-7c4c5cc6b5-vpncx   1/1     Running   0          9m47s
         nginx-7c4c5cc6b5-xj5wl   1/1     Running   0          9m47s

   b. Log in to an Nginx container.

      .. code-block::

         kubectl exec -it nginx-7c4c5cc6b5-vpncx -- /bin/sh

   c. Access the load balancer IP address.

      .. code-block::

         curl **.**.**.**

#. Wait for a while and check the monitoring data on the ELB console.

   If a new access connection is displayed, the load balancer can forward access as expected.
