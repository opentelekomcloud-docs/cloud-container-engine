:original_name: cce_10_0283.html

.. _cce_10_0283:

VPC Network
===========

Model Definition
----------------

The VPC network uses VPC routing to integrate with the underlying network. This network model is suitable for performance-intensive scenarios. The maximum number of nodes allowed in a cluster depends on the VPC route quota. Each node is assigned a CIDR block of a fixed size. This networking model is free from tunnel encapsulation overhead and outperforms the container tunnel network model. In addition, as VPC routing includes routes to node IP addresses and the container CIDR block, container pods in a cluster can be directly accessed from outside the cluster.


.. figure:: /_static/images/en-us_image_0000001568822773.png
   :alt: **Figure 1** VPC network model

   **Figure 1** VPC network model

**Pod-to-pod communication**

-  On the same node: Packets are directly forwarded through IPVlan.
-  Across nodes: Packets are forwarded to the default gateway through default routes, and then to the peer node via the VPC routes.

Advantages and Disadvantages
----------------------------

**Advantages**

-  No tunnel encapsulation is required, so network problems are easy to locate and the performance is high.
-  External networks in a VPC can be directly connected to container IP addresses.

**Disadvantages**

-  The number of nodes is limited by the VPC route quota.
-  Each node is assigned a CIDR block of a fixed size, which leads to a waste of IP addresses in the container CIDR block.
-  Pods cannot directly use functionalities such as EIPs and security groups.

Applicable Scenarios
--------------------

-  High performance requirements: As no tunnel encapsulation is required, the VPC network model delivers the performance close to that of a VPC network when compared with the container tunnel network model. Therefore, the VPC network model is applicable to scenarios that have high requirements on performance, such as AI computing and big data computing.
-  Small- and medium-scale networking: The VPC network is limited by the VPC route quota. Currently, a maximum of 200 nodes are supported by default. If there are large-scale networking requirements, you can increase the VPC route quota.

.. _cce_10_0283__section1574982552114:

Container IP Address Management
-------------------------------

The VPC network allocates container IP addresses according to the following rules:

-  The container CIDR block is allocated separately.
-  IP addresses are allocated by node. One CIDR block with a fixed size (which is configurable) is allocated to each node in a cluster from the container CIDR block.
-  The container CIDR block cyclically allocates CIDR blocks to new nodes in sequence.
-  Pods scheduled to a node are cyclically allocated IP addresses from CIDR blocks allocated to the node.


.. figure:: /_static/images/en-us_image_0000001569022889.png
   :alt: **Figure 2** IP address management of the VPC network

   **Figure 2** IP address management of the VPC network

Maximum number of nodes that can be created in the cluster using the VPC network = Number of IP addresses in the container CIDR block /Number of IP addresses in the CIDR block allocated to the node by the container CIDR block

For example, if the container CIDR block is 172.16.0.0/16, the number of IP addresses is 65536. The mask of the container CIDR block allocated to the node is 25. That is, the number of container IP addresses on each node is 128. Therefore, a maximum of 512 (65536/128) nodes can be created. In addition, the number of nodes that can be created in a cluster also depends on the node network and cluster scale.

Recommendation for CIDR Block Planning
--------------------------------------

As described in :ref:`Cluster Network Structure <cce_10_0010__section1131733719195>`, network addresses in a cluster can be divided into three parts: node network, container network, and service network. When planning network addresses, consider the following aspects:

-  The three CIDR blocks cannot overlap. Otherwise, a conflict occurs.
-  Ensure that each CIDR block has sufficient IP addresses.

   -  The IP addresses in the node CIDR block must match the cluster scale. Otherwise, nodes cannot be created due to insufficient IP addresses.
   -  The IP addresses in the container CIDR block must match the service scale. Otherwise, pods cannot be created due to insufficient IP addresses. The number of pods that can be created on each node also depends on other parameter settings.

Assume that a cluster contains 200 nodes and the network model is VPC network.

In this case, the number of available IP addresses in the selected node subnet must be greater than 200. Otherwise, nodes cannot be created due to insufficient IP addresses.

The container CIDR block is 10.0.0.0/16, and the number of available IP addresses is 65536. As described in :ref:`Container IP Address Management <cce_10_0283__section1574982552114>`, the VPC network is allocated a CIDR block with the fixed size (using the mask to determine the maximum number of container IP addresses allocated to each node). For example, if the upper limit is 128, the cluster supports a maximum of 512 (65536/128) nodes, including the three master nodes.

Example of VPC Network Access
-----------------------------

Create a cluster using the VPC network model. The cluster contains one node.

.. code-block::

   $ kubectl get node
   NAME           STATUS   ROLES    AGE   VERSION
   192.168.0.99   Ready    <none>   9d    v1.17.17-r0-CCE21.6.1.B004-17.37.5

Check the VPC routing table. The destination address 172.16.0.0/25 is the container CIDR block allocated to the node, and the next hop is the corresponding node. When the container IP address is accessed, the VPC route forwards the access request to the next-hop node. This indicates that the VPC network model uses VPC routes.

Create a Deployment in the cluster.

.. code-block::

   kind: Deployment
   apiVersion: apps/v1
   metadata:
     name: example
     namespace: default
   spec:
     replicas: 4
     selector:
       matchLabels:
         app: example
     template:
       metadata:
         labels:
           app: example
       spec:
         containers:
           - name: container-0
             image: 'nginx:perl'
         imagePullSecrets:
           - name: default-secret

Check the pod.

.. code-block::

   $ kubectl get pod -owide
   NAME                       READY   STATUS    RESTARTS   AGE   IP           NODE           NOMINATED NODE   READINESS GATES
   example-86b9779494-l8qrw   1/1     Running   0          14s   172.16.0.6   192.168.0.99   <none>           <none>
   example-86b9779494-svs8t   1/1     Running   0          14s   172.16.0.7   192.168.0.99   <none>           <none>
   example-86b9779494-x8kl5   1/1     Running   0          14s   172.16.0.5   192.168.0.99   <none>           <none>
   example-86b9779494-zt627   1/1     Running   0          14s   172.16.0.8   192.168.0.99   <none>           <none>

In this case, the IP address of the pod can be directly accessed from a node outside the cluster in the same VPC. This is a feature of the VPC network feature.

The pod can also be accessed from a node in the same cluster or in the pod. As shown in the following figure, the pod can be accessed directly from the container.

.. code-block::

   $ kubectl exec -it example-86b9779494-l8qrw -- curl 172.16.0.7
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
