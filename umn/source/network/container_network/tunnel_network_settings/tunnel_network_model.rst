:original_name: cce_10_0282.html

.. _cce_10_0282:

Tunnel Network Model
====================

Model Definition
----------------

A container tunnel network creates a separate network plane for containers by using tunnel encapsulation on the host network plane. The container tunnel network of a CCE cluster uses VXLAN for tunnel encapsulation and Open vSwitch as the virtual switch backend. VXLAN is a protocol that encapsulates Ethernet packets into UDP packets to transmit them through tunnels. Open vSwitch is an open-source virtual switch software that provides functions such as network isolation and data forwarding.

While there may be some performance costs, packet encapsulation and tunnel transmission allow for greater interoperability and compatibility with advanced features, such as network policy-based isolation, in most common scenarios.


.. figure:: /_static/images/en-us_image_0000001981277009.png
   :alt: **Figure 1** Container tunnel network

   **Figure 1** Container tunnel network

In a cluster using the container tunnel model, the communication paths between pods on the same node and between pods on different nodes are different.

-  Inter-pod communication on the same node: Packets are directly forwarded via the OVS bridge on the node.
-  Inter-pod communication on different nodes: Packets are encapsulated in the OVS bridge and then forwarded to the target pod on the peer node through the host NIC.

Advantages and Disadvantages
----------------------------

**Advantages**

-  The container network is decoupled from the node network and is not limited by the VPC quotas and response speed (such as the number of VPC routes, number of ENIs, and creation speed).
-  Network isolation is supported. For details, see :ref:`Configuring Network Policies to Restrict Pod Access <cce_10_0059>`.
-  Bandwidth limits are supported.
-  Large-scale networking with a maximum of 2000 nodes is supported.

**Disadvantages**

-  High encapsulation overhead results in poor performance and makes it difficult to locate network faults.
-  Pods cannot directly use features like EIPs and security groups.
-  Container IP addresses cannot be directly accessed by external networks.

Application Scenarios
---------------------

-  Low requirements on performance: As the container tunnel network requires additional VXLAN tunnel encapsulation, it has about 5% to 15% of performance loss when compared with the other two container network models. Therefore, the container tunnel network applies to the scenarios that do not have high performance requirements, such as web applications, and middle-end and back-end services with a small number of access requests.
-  Large-scale networking: Different from the VPC network that is limited by the VPC route quota, the container tunnel network does not have any restriction on the infrastructure. In addition, the container tunnel network controls the broadcast domain to the node level. The container tunnel network supports a maximum of 2000 nodes.

Container IP Address Management
-------------------------------

The container tunnel network allocates container IP addresses according to the following rules:

-  Container CIDR blocks are separate from node CIDR blocks.
-  IP addresses are allocated by node. One or more CIDR blocks with a fixed size (16 by default) are allocated to each node in a cluster from the container CIDR block.
-  When the IP addresses on a node are used up, you can apply for a new CIDR block.
-  A container CIDR block assigns CIDR blocks to new nodes or existing nodes in a cyclical sequence.
-  IP addresses from one or more CIDR blocks assigned to a node are allocated to pods scheduled to that node in a cyclical manner.


.. figure:: /_static/images/en-us_image_0000001950317472.png
   :alt: **Figure 2** IP address allocation of the container tunnel network

   **Figure 2** IP address allocation of the container tunnel network

Maximum number of nodes that can be created in the cluster using the container tunnel network = Number of IP addresses in the container CIDR block/Size of the IP CIDR block allocated to the node by the container CIDR block at a time (16 by default)

For example, if the container CIDR block is 172.16.0.0/16, the number of IP addresses is 65536. The mask of the container CIDR block allocated to a node is 28. That is, a total of 16 container IP addresses are allocated each time. Therefore, a maximum of 4096 (65536/16) nodes can be created. This is an extreme case. If 4096 nodes are created, a maximum of 16 pods can be created for each node because only a CIDR block with 16 IP addresses is allocated to each node. The number of nodes that can be added to a cluster is also determined by the available IP addresses in the node subnet and the scale of the cluster.

Recommendation for CIDR Block Planning
--------------------------------------

As explained in :ref:`Cluster Network Structure <cce_10_0010__section1131733719195>`, network addresses in a cluster are divided into the cluster network, container network, and service network. When planning network addresses, consider the following factors:

-  **The three CIDR blocks cannot overlap**. Otherwise, a conflict occurs.
-  Ensure that **each CIDR block has sufficient IP addresses**.

   -  The IP addresses in the cluster CIDR block must match the cluster scale. Otherwise, nodes cannot be created due to insufficient IP addresses.
   -  The IP addresses in the container CIDR block must match the service scale. Otherwise, pods cannot be created due to insufficient IP addresses. The number of pods that can be created on each node also depends on other parameter settings.

Example of Container Tunnel Network Access
------------------------------------------

The following is an example of creating a workload in a cluster using the container tunnel network model:

#. Use kubectl to access the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a Deployment in the cluster.

   Create the **deployment.yaml** file. The following shows an example:

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

   Create the workload.

   .. code-block::

      kubectl apply -f deployment.yaml

#. Check the running pods.

   .. code-block::

      kubectl get pod -owide

   Command output:

   .. code-block::

      NAME                       READY   STATUS    RESTARTS   AGE     IP          NODE           NOMINATED NODE   READINESS GATES
      example-5bdc5699b7-5rvq4   1/1     Running   0          3m28s   10.0.0.20   192.168.0.42   <none>           <none>
      example-5bdc5699b7-984j9   1/1     Running   0          3m28s   10.0.0.21   192.168.0.42   <none>           <none>
      example-5bdc5699b7-lfxkm   1/1     Running   0          3m28s   10.0.0.22   192.168.0.42   <none>           <none>
      example-5bdc5699b7-wjcmg   1/1     Running   0          3m28s   10.0.0.52   192.168.0.64   <none>           <none>

#. Use a cloud server in the same VPC to directly access a pod's IP address from outside the cluster. The access failed.

   You can access a pod using its IP address within the pod or from a node in the cluster. In the following example, access a pod's IP address within the pod. *example-5bdc5699b7-5rvq4* is the pod name, and *10.0.0.21* is the pod IP address.

   .. code-block::

      kubectl exec -it example-5bdc5699b7-5rvq4 -- curl 10.0.0.21

   If the following information is displayed, the workload can be properly accessed:

   .. code-block::

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
