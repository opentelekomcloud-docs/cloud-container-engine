:original_name: cce_10_0284.html

.. _cce_10_0284:

Cloud Native Network 2.0
========================

Model Definition
----------------

Cloud Native Network 2.0 is a proprietary, next-generation container network model that combines VPC network interfaces and supplementary network interfaces. This enables binding either interface type directly to pods, assigning each pod a unique VPC IP address. Cloud Native Network 2.0 also supports ELB passthrough networking and associating security groups and EIPs with individual pods. Because container tunnel encapsulation and NAT are not required, Cloud Native Network 2.0 delivers higher network performance than the container tunnel and VPC networks.


.. figure:: /_static/images/en-us_image_0000002662009209.png
   :alt: **Figure 1** Cloud Native Network 2.0

   **Figure 1** Cloud Native Network 2.0

In a cluster using Cloud Native Network 2.0, pods rely on network interfaces and supplementary network interfaces to access external networks.

-  Pods running on a BMS node use elastic network interfaces to access external networks.
-  Pods running on an ECS use supplementary network interfaces to access external networks. Supplementary network interfaces are attached to the elastic network interfaces of ECSs through VLAN subinterfaces.
-  To run a pod, bind a network interface to it. The maximum number of pods that can run on a single node depends on the number of network interfaces that can be bound to the node and the number of network interface ports available on the node.
-  Traffic for communications between pods on a node, communications between pods on different nodes, and pod access to networks outside a cluster is forwarded through the elastic or supplementary network interfaces of VPC.

Notes and Constraints
---------------------

This network model is available only to CCE Turbo clusters.

Advantages and Disadvantages
----------------------------

**Advantages**

-  VPCs serve as the foundation for constructing container networks. Every pod has its own network interface and IP address, which simplifies network problem-solving and enhances performance.
-  In the same VPC, network interfaces are directly bound to pods in a cluster, so that resources outside the cluster can directly communicate with containers within the cluster.

   .. note::

      Similarly, if the VPC is accessible to other VPCs or data centers, resources in other VPCs or data centers can directly communicate with containers in the cluster, provided there are no conflicts between the network CIDR blocks.

-  Pods can directly use VPC-native load balancing, EIP, and security group capabilities.

**Disadvantages**

Container networks are built on VPCs, with each pod receiving an IP address from the VPC CIDR block. As a result, it is crucial to plan the container CIDR block carefully before creating a cluster.

Applications
------------

-  High performance requirements: Cloud Native Network 2.0 uses VPC networks to construct container networks, eliminating the need for tunnel encapsulation or NAT required by container communications. This makes Cloud Native Network 2.0 ideal for scenarios that demand high bandwidth and low latency.
-  Large-scale networking: Cloud Native Network 2.0 supports 2,000 nodes by default (maximum 20,000) and 100,000 pods.

Recommendation for CIDR Block Planning
--------------------------------------

As explained in :ref:`Cluster Network Structure <cce_10_0010__section1131733719195>`, there are three networks in a cluster: cluster network, container network, and Service network. When planning network addresses, consider the following:

-  All subnets (including those created from the secondary CIDR blocks) in the VPC where the cluster resides cannot conflict with the Service CIDR blocks.
-  **Each CIDR block has sufficient IP addresses**.

   -  The IP addresses in cluster CIDR blocks must match the cluster scale. Otherwise, nodes cannot be created due to insufficient IP addresses.
   -  The IP addresses in container CIDR blocks must match the service scale. Otherwise, pods cannot be created due to insufficient IP addresses.

In Cloud Native Network 2.0, the container CIDR block and node CIDR block share the IP addresses in the VPC CIDR block. The pod subnet and node subnet should not be the same. Otherwise, containers or nodes may fail to be created due to insufficient IP addresses.

You can add subnets to the container CIDR block after creating a cluster to increase the number of available IP addresses. In this case, ensure that the added subnets do not conflict with other subnets in the container CIDR block.

Example of Cloud Native Network 2.0 Access
------------------------------------------

In this example, a CCE Turbo cluster is created, and the cluster contains three ECS nodes.

You can check the basic information about a node on the ECS console. You can see that a primary network interface and an extended network interface are bound to the node. Both of them are elastic network interfaces. The IP address of the extended network interface belongs to the container CIDR block and is used to bind supplementary network interfaces to pods on the node.

The following example shows how to create a workload in a cluster that uses Cloud Native Network 2.0.

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a Deployment in the cluster.

   Create the **deployment.yaml** file. The following shows an example:

   .. code-block::

      kind: Deployment
      apiVersion: apps/v1
      metadata:
        name: example
        namespace: default
      spec:
        replicas: 6
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

      NAME                       READY   STATUS    RESTARTS   AGE   IP            NODE         NOMINATED NODE   READINESS GATES
      example-5bdc5699b7-54v7g   1/1     Running   0          7s    10.1.18.2     10.1.0.167   <none>           <none>
      example-5bdc5699b7-6dzx5   1/1     Running   0          7s    10.1.18.216   10.1.0.186   <none>           <none>
      example-5bdc5699b7-gq7xs   1/1     Running   0          7s    10.1.16.63    10.1.0.144   <none>           <none>
      example-5bdc5699b7-h9rvb   1/1     Running   0          7s    10.1.16.125   10.1.0.167   <none>           <none>
      example-5bdc5699b7-s9fts   1/1     Running   0          7s    10.1.16.89    10.1.0.144   <none>           <none>
      example-5bdc5699b7-swq6q   1/1     Running   0          7s    10.1.17.111   10.1.0.167   <none>           <none>

   All pods use supplementary network interfaces, which are bound to the extended network interface of the node.

   For example, the IP address of the extended network interface on the node (10.1.0.167) is 10.1.17.172. On the network interfaces page, you can see that there are three supplementary network interfaces bound to the extended network interface whose IP address is **10.1.17.172**. The IP addresses bound to these supplementary network interfaces are the IP addresses of the pods running on the node.

#. Log in to an ECS in the same VPC and access the IP address of a pod from outside the cluster. In this example, the accessed pod IP address is *10.1.18.2*.

   .. code-block::

      curl 10.1.18.2

   If the following information is displayed, the workload can be accessed:

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

Network Interface Pre-binding Policies in CCE Turbo Clusters
------------------------------------------------------------

In Cloud Native Network 2.0, each pod requires a network interface or supplementary network interface. However, interface creation and binding is slower than pod scaling, severely affecting container startup during large-scale batch creation. To address this, Cloud Native Network 2.0 provides dynamic pre-binding: Network interfaces or supplementary network interfaces are pre-created and pre-configured on nodes, forming a ready-to-use pool. When a pod starts, it obtains interfaces directly from this pool. This accelerates pod startup while maximizing IP utilization.

Network Interface Pre-binding Policies in CCE Turbo Clusters

#. **Add a node and configure network interfaces and pre-bound network interfaces for the node.**

   Pre-bound network interfaces are allocated based on node type:

   -  **ECS node**: Pods on ECS nodes use supplementary network interfaces, which must be mounted to a primary network interface. The pre-binding rules are as follows:

      When an ECS node is added to a cluster, a primary network interface is bound to carry supplementary network interfaces. Once bound, supplementary network interfaces can be mounted for pods. In addition to interfaces currently in use, a pool of pre-bound supplementary network interfaces is maintained for future pod scheduling.

      The total network interfaces bound to an ECS node equals **Primary interfaces + Active supplementary interfaces + Pre-bound supplementary interfaces**. The value of **Primary interfaces** is **1** in clusters v1.19.16-r40, v1.21.11-r0, v1.23.9-r0, v1.25.4-r0, v1.27.1-r0, and later. In clusters earlier than these versions, the value is the rounded-up result of *Max supplementary interfaces/64*. The maximum supplementary interfaces is determined by ECS flavor.

#. **Pre-bound network interfaces**: **dynamic** network interface pre-binding policies

   -  CCE provides four parameters for the dynamic network interface pre-binding policy. Set these parameters properly.

      .. table:: **Table 1** Parameters of the dynamic network interface pre-binding policy

         +--------------------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                        | Default Value   | Description                                                                                                                                                                                                                                                                                                                                    | Suggestion                                                                                                                                                                                                                |
         +==================================================+=================+================================================================================================================================================================================================================================================================================================================================================+===========================================================================================================================================================================================================================+
         | Minimum Number of Container ENIs Bound to a Node | 10              | Minimum number of network interfaces bound to a node. The value can be a number or a percentage.                                                                                                                                                                                                                                               | Configure this parameter based on the number of pods typically running on most nodes.                                                                                                                                     |
         |                                                  |                 |                                                                                                                                                                                                                                                                                                                                                |                                                                                                                                                                                                                           |
         |                                                  |                 | -  Number: The value must be an integer from **0** to **65535**. The value **10** indicates that at least 10 pod network interfaces must be bound to a node. If the number you specified exceeds the pod network interface quota of the node, the network interface quota will be used.                                                        |                                                                                                                                                                                                                           |
         |                                                  |                 | -  Percentage: The value ranges from 1% to 100%. For example, the value **10%** indicates that if the network interface quota of a node is 128, at least 12 (rounded down) network interfaces must be bound to a node.                                                                                                                         |                                                                                                                                                                                                                           |
         |                                                  |                 |                                                                                                                                                                                                                                                                                                                                                |                                                                                                                                                                                                                           |
         |                                                  |                 | Set both **nic-minimum-target** and **nic-maximum-target** to the same value or percentage.                                                                                                                                                                                                                                                    |                                                                                                                                                                                                                           |
         +--------------------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Upper Limit of Pre-bound Container ENIs          | 0               | After the number of network interfaces bound to a node exceeds the **nic-maximum-target** value, CCE will not proactively pre-bind network interfaces.                                                                                                                                                                                         | Configure this parameter based on the maximum number of pods running on most nodes.                                                                                                                                       |
         |                                                  |                 |                                                                                                                                                                                                                                                                                                                                                |                                                                                                                                                                                                                           |
         |                                                  |                 | Checking the upper limit of pre-bound pod network interfaces is enabled only when the value of this parameter is greater than or equal to the minimum number of pod network interfaces (**nic-minimum-target**) bound to a node. The value can be a number or a percentage.                                                                    |                                                                                                                                                                                                                           |
         |                                                  |                 |                                                                                                                                                                                                                                                                                                                                                |                                                                                                                                                                                                                           |
         |                                                  |                 | -  Number: The value must be an integer from **0** to **65535**. The value **0** indicates that the check on the upper limit of pre-bound pod network interfaces is disabled. If the number you specified exceeds the pod network interface quota of the node, the network interface quota will be used.                                       |                                                                                                                                                                                                                           |
         |                                                  |                 | -  Percentage: The value ranges from 1% to 100%. For example, a value of 50% means that if a node's network interface quota is 128, the maximum number of pre-bound network interfaces is 64 (rounded down).                                                                                                                                   |                                                                                                                                                                                                                           |
         |                                                  |                 |                                                                                                                                                                                                                                                                                                                                                |                                                                                                                                                                                                                           |
         |                                                  |                 | Set both **nic-minimum-target** and **nic-maximum-target** to the same value or percentage.                                                                                                                                                                                                                                                    |                                                                                                                                                                                                                           |
         +--------------------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Container ENIs Dynamically Pre-bound to a Node   | 2               | The target number of network interfaces to be pre-bound to a node before it starts.                                                                                                                                                                                                                                                            | Set the parameter value to the number of pods that can be scaled out instantaneously within 10 seconds on most nodes.                                                                                                     |
         |                                                  |                 |                                                                                                                                                                                                                                                                                                                                                |                                                                                                                                                                                                                           |
         |                                                  |                 | When the sum of the **nic-warm-target** value and the number of network interfaces already bound to the node exceeds the **nic-maximum-target** value, CCE will pre-bind the number of network interfaces specified by the difference between the **nic-maximum-target** value and the current number of network interfaces bound to the node. |                                                                                                                                                                                                                           |
         +--------------------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Threshold for Unbinding Pre-bound Container ENIs | 2               | Pre-bound network interfaces on a node will only be unbound and reclaimed if the difference between the number of idle network interfaces and the **nic-warm-target** value exceeds the threshold. The value can only be a number.                                                                                                             | Configure this parameter based on the difference between the number of pods that are frequently scaled on most nodes within minutes and the number of pods that are instantly scaled out on most nodes within 10 seconds. |
         |                                                  |                 |                                                                                                                                                                                                                                                                                                                                                |                                                                                                                                                                                                                           |
         |                                                  |                 | -  A large value will accelerate pod startup but slow down the unbinding of idle pod network interfaces and decrease the IP address usage. Exercise caution when performing this operation.                                                                                                                                                    |                                                                                                                                                                                                                           |
         |                                                  |                 | -  A small value will speed up the unbinding of idle pod network interfaces and increase the IP address usage but will slow down pod startup, especially when a large number of pods increase instantaneously.                                                                                                                                 |                                                                                                                                                                                                                           |
         +--------------------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  The container networking component maintains a scalable pre-bound network interface pool for each node. The component checks and calculates the number of **pre-bound network interfaces** or **idle network interfaces** every 10 seconds.

      -  **Number of pre-bound network interfaces = min(nic-maximum-target - Number of bound network interfaces, max(nic-minimum-target - Number of bound network interfaces, nic-warm-target - Number of idle network interfaces)**
      -  **Number of network interfaces to be unbound = min(Number of idle network interfaces - nic-warm-target - nic-max-above-warm-target, Number of bound network interfaces - nic-minimum-target)**

   -  The number of pre-binding network interfaces on the node remains in the following range:

      -  **Minimum number of network interfaces to be pre-bound = min(max(nic-minimum-target - Number of bound network interfaces, nic-warm-target), nic-maximum-target - Number of bound network interfaces)**
      -  **Maximum number of network interfaces to be pre-bound = max(nic-warm-target + nic-max-above-warm-target, Number of bound network interfaces - nic-minimum-target)**

#. **Create a pod to allocate a network interface.**

   When a pod is created, an idle pre-bound network interface (the earliest unused one) will be preferentially allocated from the pool. If no idle network interface is available, a new network interface will be created or a new supplementary network interface will be bound to the pod.

#. **Delete the pod to release the network interface.**

   -  When the pod is deleted, the corresponding network interface is released back to the pre-bound network interface pool of the node and can be bound to another pod within a 2-minute cooldown period. If the network interface is not bound to any pod within 2 minutes and needs to be released based on the pool's calculation result, it will be released.

   -  When a node is deleted, all of its network interfaces are released. The network interfaces are released back to the pool, and the supplementary network interfaces are deleted.

Helpful Links
-------------

-  For details about the maximum number of pods that can run on a node in a cluster with different network models, see :ref:`Maximum Number of Pods That Can Be Created on a Node <cce_10_0348>`.
