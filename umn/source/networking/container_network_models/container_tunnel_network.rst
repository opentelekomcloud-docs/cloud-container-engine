:original_name: cce_10_0282.html

.. _cce_10_0282:

Container Tunnel Network
========================

Container Tunnel Network Model
------------------------------

The container tunnel network is constructed on but independent of the node network through tunnel encapsulation. This network model uses VXLAN to encapsulate Ethernet packets into UDP packets and transmits them in tunnels. Open vSwitch serves as the backend virtual switch. Though at some costs of performance, packet encapsulation and tunnel transmission enable higher interoperability and compatibility with advanced features (such as network policy-based isolation) for most common scenarios.


.. figure:: /_static/images/en-us_image_0000001518222740.png
   :alt: **Figure 1** Container tunnel network

   **Figure 1** Container tunnel network

**Pod-to-pod communication**

-  On the same node: Packets are directly forwarded via the OVS bridge on the node.
-  Across nodes: Packets are encapsulated in the OVS bridge and then forwarded to the peer node.

Advantages and Disadvantages
----------------------------

**Advantages**

-  The container network is decoupled from the node network and is not limited by the VPC quotas and response speed (such as the number of VPC routes, number of elastic ENIs, and creation speed).
-  Network isolation is supported. For details, see :ref:`Network Policies <cce_10_0059>`.
-  Bandwidth limits are supported.
-  Large-scale networking is supported.

**Disadvantages**

-  High encapsulation overhead, complex networking, and low performance
-  Failure to use the load balancing and security group capabilities provided by the VPC
-  External networks cannot be directly connected to container IP addresses.

Applicable Scenarios
--------------------

-  Low requirements on performance: As the container tunnel network requires additional VXLAN tunnel encapsulation, it has about 5% to 15% of performance loss when compared with the other two container network models. Therefore, the container tunnel network is applicable to the scenarios that do not have high performance requirements, such as web applications, and middle-end and back-end services with a small number of access requests.
-  Large-scale networking: Different from the VPC network that is limited by the VPC route quota, the container tunnel network does not have any restriction on the infrastructure. In addition, the container tunnel network controls the broadcast domain to the node level. The container tunnel network supports a maximum of 2000 nodes.

Container IP Address Management
-------------------------------

The container tunnel network allocates container IP addresses according to the following rules:

-  The container CIDR block is allocated separately, which is irrelevant to the node CIDR block.
-  IP addresses are allocated by node. One or more CIDR blocks with a fixed size (16 by default) are allocated to each node in a cluster from the container CIDR block.
-  When the IP addresses on a node are used up, you can apply for a new CIDR block.
-  The container CIDR block cyclically allocates CIDR blocks to new nodes or existing nodes in sequence.
-  Pods scheduled to a node are cyclically allocated IP addresses from one or more CIDR blocks allocated to the node.


.. figure:: /_static/images/en-us_image_0000001569182773.png
   :alt: **Figure 2** IP address allocation of the container tunnel network

   **Figure 2** IP address allocation of the container tunnel network

Maximum number of nodes that can be created in the cluster using the container tunnel network = Number of IP addresses in the container CIDR block / Size of the IP CIDR block allocated to the node by the container CIDR block at a time (16 by default)

For example, if the container CIDR block is 172.16.0.0/16, the number of IP addresses is 65536. If 16 IP addresses are allocated to a node at a time, a maximum of 4096 (65536/16) nodes can be created in the cluster. This is an extreme case. If 4096 nodes are created, a maximum of 16 pods can be created for each node because only 16 IP CIDR block\\s are allocated to each node. In addition, the number of nodes that can be created in a cluster also depends on the node network and cluster scale.

Recommendation for CIDR Block Planning
--------------------------------------

As described in :ref:`Cluster Network Structure <cce_10_0010__section1131733719195>`, network addresses in a cluster can be divided into three parts: node network, container network, and service network. When planning network addresses, consider the following aspects:

-  The three CIDR blocks cannot overlap. Otherwise, a conflict occurs.
-  Ensure that each CIDR block has sufficient IP addresses.

   -  The IP addresses in the node CIDR block must match the cluster scale. Otherwise, nodes cannot be created due to insufficient IP addresses.
   -  The IP addresses in the container CIDR block must match the service scale. Otherwise, pods cannot be created due to insufficient IP addresses. The number of pods that can be created on each node also depends on other parameter settings.

Example of Container Tunnel Network Access
------------------------------------------

Create a cluster that uses the container tunnel network model. Create a Deployment in the cluster.

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
             resources:
               limits:
                 cpu: 250m
                 memory: 512Mi
               requests:
                 cpu: 250m
                 memory: 512Mi
         imagePullSecrets:
           - name: default-secret

View the created pod.

.. code-block::

   $ kubectl get pod -owide
   NAME                       READY   STATUS    RESTARTS   AGE     IP          NODE           NOMINATED NODE   READINESS GATES
   example-5bdc5699b7-5rvq4   1/1     Running   0          3m28s   10.0.0.20   192.168.0.42   <none>           <none>
   example-5bdc5699b7-984j9   1/1     Running   0          3m28s   10.0.0.21   192.168.0.42   <none>           <none>
   example-5bdc5699b7-lfxkm   1/1     Running   0          3m28s   10.0.0.22   192.168.0.42   <none>           <none>
   example-5bdc5699b7-wjcmg   1/1     Running   0          3m28s   10.0.0.52   192.168.0.64   <none>           <none>

In this case, the IP address of the pod cannot be directly accessed outside the cluster in the same VPC. This is a feature of the container tunnel network.

However, the pod can be accessed from a node in the cluster or in the pod. As shown in the following figure, the pod can be accessed directly from the container.

.. code-block::

   $ kubectl exec -it example-5bdc5699b7-5rvq4 -- curl 10.0.0.21
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
