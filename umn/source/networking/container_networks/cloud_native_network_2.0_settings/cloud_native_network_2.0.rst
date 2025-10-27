:original_name: cce_10_0284.html

.. _cce_10_0284:

Cloud Native Network 2.0
========================

Model Definition
----------------

The proprietary, next-generation Cloud Native Network 2.0 combines the network interfaces and supplementary network interfaces of VPC. This allows you to bind network interfaces or supplementary network interfaces to pods, giving each pod unique IP address within the VPC. Cloud Native Network 2.0 also has features like ELB passthrough networking and association of security groups and EIPs with pods. Because container tunnel encapsulation and NAT are not required, Cloud Native Network 2.0 delivers higher network performance than the container tunnel and VPC networks.


.. figure:: /_static/images/en-us_image_0000002467678269.png
   :alt: **Figure 1** Cloud Native Network 2.0

   **Figure 1** Cloud Native Network 2.0

In a cluster using Cloud Native Network 2.0, pods rely on network interfaces and supplementary network interfaces to access external networks.

-  Pods running on a BMS node use elastic network interfaces to access external networks.
-  Pods running on an ECS use supplementary network interfaces to access external networks. Supplementary network interfaces are attached to the elastic network interfaces of ECSs through VLAN subinterfaces.
-  To run a pod, bind a network interface to it. The maximum number of pods that can run on a single node depends on the number of network interfaces that can be bound to the node and the number of network interface ports available on the node.
-  Traffic for communications between pods on a node, communications between pods on different nodes, and pod access to networks outside a cluster is forwarded through the elastic or supplementary network interfaces of VPC.

Constraints
-----------

This network model is available only to CCE Turbo clusters.

Advantages and Disadvantages
----------------------------

**Advantages**

-  VPCs serve as the foundation for constructing container networks. Every pod has its own network interface and IP address, which simplifies network problem-solving and enhances performance.
-  In the same VPC, network interfaces are directly bound to pods in a cluster, so that resources outside the cluster can directly communicate with containers within the cluster.

   .. note::

      Similarly, if the VPC is accessible to other VPCs or data centers, resources in other VPCs or data centers can directly communicate with containers in the cluster, provided there are no conflicts between the network CIDR blocks.

-  The load balancing, security group, and EIP capabilities provided by VPC can be directly used by pods.

**Disadvantages**

Container networks are built on VPCs, with each pod receiving an IP address from the VPC CIDR block. As a result, it is crucial to plan the container CIDR block carefully before creating a cluster.

Application Scenarios
---------------------

-  High performance requirements: Cloud Native Network 2.0 uses VPC networks to construct container networks, eliminating the need for tunnel encapsulation or NAT required by container communications. This makes Cloud Native Network 2.0 ideal for scenarios that demand high bandwidth and low latency.
-  Large-scale networking: Cloud Native Network 2.0 supports up to 2,000 ECS nodes and 100,000 pods.

Recommendation for CIDR Block Planning
--------------------------------------

As explained in :ref:`Cluster Network Structure <cce_10_0010__section1131733719195>`, there are three networks in a cluster: cluster network, container network, and Service network. When planning network addresses, consider the following:

-  All subnets (including those created from the secondary CIDR blocks) in the VPC where the cluster resides cannot conflict with the Service CIDR blocks.
-  **Each CIDR block has sufficient IP addresses**.

   -  The IP addresses in cluster CIDR blocks must match the cluster scale. Otherwise, nodes cannot be created due to insufficient IP addresses.
   -  The IP addresses in container CIDR blocks must match the service scale. Otherwise, pods cannot be created due to insufficient IP addresses.

In Cloud Native Network 2.0, the container CIDR block and node CIDR block share the IP addresses in the VPC CIDR block. The container subnet and node subnet should not be the same. Otherwise, containers or nodes may fail to be created due to insufficient IP addresses.

In addition, a secondary CIDR block can be added to the container CIDR block after a cluster is created to increase the number of available IP addresses. In this case, the secondary CIDR block cannot with the container CIDR block.

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

In Cloud Native Network 2.0, ECS nodes use supplementary network interfaces. The allocation policies are as follows:

-  Pod IP addresses are allocated from the VPC subnets configured for the container network.
-  To add an ECS node to a cluster, bind the elastic network interface that carries the supplementary network interfaces first to the ECS. After the elastic network interface is bound, supplementary network interfaces are bound.
-  Number of elastic network interfaces bound to an ECS node: **For clusters of v1.19.16-r40, v1.21.11-r0, v1.23.9-r0, v1.25.4-r0, v1.27.1-r0, and later versions, the value is 1. For clusters of earlier versions, the value is the maximum number of supplementary network interfaces that can be bound to the node divided by 64 (rounded up).**
-  Number of network interfaces bound to an ECS node = **Number of elastic network interfaces used to bear supplementary network interfaces + Number of supplementary network interfaces currently used by pods + Number of pre-bound supplementary network interfaces**
-  When a pod is created, an available network interface is randomly allocated from the pre-binding network interface pool of the node.
-  When a pod is deleted, the network interface is released back to the network interface pool of the node.
-  When a node is deleted, the network interfaces are all released. The elastic network interfaces are released backed to the pool. The supplementary network interfaces are deleted.

Cloud Native 2.0 networks support **dynamic** network interface pre-binding policies. The following table lists the scenarios.

.. table:: **Table 1** Comparison between network interface pre-binding policies

   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Policy                            | Dynamic Network Interface Pre-binding Policy (Default)                                                                                                                                             |
   +===================================+====================================================================================================================================================================================================+
   | Management policy                 | **nic-minimum-target**: minimum number of network interfaces (pre-bound and unused + used) bound to a node.                                                                                        |
   |                                   |                                                                                                                                                                                                    |
   |                                   | **nic-maximum-target**: If the number of network interfaces bound to a node exceeds the value of this parameter, the system does not proactively pre-bind network interfaces.                      |
   |                                   |                                                                                                                                                                                                    |
   |                                   | **nic-warm-target**: minimum number of pre-bound network interfaces on a node.                                                                                                                     |
   |                                   |                                                                                                                                                                                                    |
   |                                   | **nic-max-above-warm-target**: Network interfaces are unbound and reclaimed only when the number of idle network interfaces minus the number of **nic-warm-target** is greater than the threshold. |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Application scenario              | Accelerates pod startup while improving IP resource utilization. This mode applies to scenarios where the number of IP addresses in the container network segment is insufficient.                 |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

CCE provides four parameters for the dynamic network interface pre-binding policy. Set these parameters properly.

.. table:: **Table 2** Parameters of the dynamic network interface pre-binding policy

   +---------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                 | Default Value   | Description                                                                                                                                                                                                                                                                                                                                    | Suggestion                                                                                                                                                                                                          |
   +===========================+=================+================================================================================================================================================================================================================================================================================================================================================+=====================================================================================================================================================================================================================+
   | nic-minimum-target        | 10              | Minimum number of network interfaces bound to a node. The value can be a number or a percentage.                                                                                                                                                                                                                                               | Set these parameters based on the number of pods.                                                                                                                                                                   |
   |                           |                 |                                                                                                                                                                                                                                                                                                                                                |                                                                                                                                                                                                                     |
   |                           |                 | -  Value: The value must be a positive integer. For example, 10 indicates that at least 10 network interfaces are bound to a node. If the number you specified exceeds the network interface quota of the node, the network interface quota will be used.                                                                                      |                                                                                                                                                                                                                     |
   |                           |                 | -  Percentage: The value ranges from 1% to 100%. For example, the value **10%** indicates that, if the network interface quota of a node is 128, at least 12 (rounded down) network interfaces must be bound to a node.                                                                                                                        |                                                                                                                                                                                                                     |
   |                           |                 |                                                                                                                                                                                                                                                                                                                                                |                                                                                                                                                                                                                     |
   |                           |                 | Set both **nic-minimum-target** and **nic-maximum-target** to the same value or percentage.                                                                                                                                                                                                                                                    |                                                                                                                                                                                                                     |
   +---------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | nic-maximum-target        | 0               | After the number of network interfaces bound to a node exceeds the **nic-maximum-target** value, CCE will not proactively pre-bind network interfaces.                                                                                                                                                                                         | Set these parameters based on the number of pods.                                                                                                                                                                   |
   |                           |                 |                                                                                                                                                                                                                                                                                                                                                |                                                                                                                                                                                                                     |
   |                           |                 | Checking the upper limit of pre-bound container network interfaces is enabled only when the value of this parameter is greater than or equal to the minimum number of container network interfaces (**nic-minimum-target**) bound to a node. The value can be a number or a percentage.                                                        |                                                                                                                                                                                                                     |
   |                           |                 |                                                                                                                                                                                                                                                                                                                                                |                                                                                                                                                                                                                     |
   |                           |                 | -  Value: The value must be a positive integer. The value **0** indicates that checking the upper limit of pre-bound container network interfaces is disabled. If the number you specified exceeds the network interface quota of the node, the network interface quota will be used.                                                          |                                                                                                                                                                                                                     |
   |                           |                 | -  Percentage: The value ranges from 1% to 100%. For example, a value of 50% means that if a node's network interface quota is 128, the maximum number of pre-bound network interfaces is 64 (rounded down).                                                                                                                                   |                                                                                                                                                                                                                     |
   |                           |                 |                                                                                                                                                                                                                                                                                                                                                |                                                                                                                                                                                                                     |
   |                           |                 | Set both **nic-minimum-target** and **nic-maximum-target** to the same value or percentage.                                                                                                                                                                                                                                                    |                                                                                                                                                                                                                     |
   +---------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | nic-warm-target           | 2               | Minimum number of pre-bound network interfaces on a node. The value must be a number.                                                                                                                                                                                                                                                          | Set this parameter to the number of pods that can be scaled out instantaneously within 10 seconds.                                                                                                                  |
   |                           |                 |                                                                                                                                                                                                                                                                                                                                                |                                                                                                                                                                                                                     |
   |                           |                 | When the sum of the **nic-warm-target** value and the number of network interfaces already bound to the node exceeds the **nic-maximum-target** value, CCE will pre-bind the number of network interfaces specified by the difference between the **nic-maximum-target** value and the current number of network interfaces bound to the node. |                                                                                                                                                                                                                     |
   +---------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | nic-max-above-warm-target | 2               | Only when the number of idle network interfaces on a node minus the value of **nic-warm-target** is greater than the threshold, the pre-bound network interfaces will be unbound and reclaimed. The value can only be a number.                                                                                                                | Set this parameter based on the difference between the number of pods that are frequently scaled on most nodes within minutes and the number of pods that are instantly scaled out on most nodes within 10 seconds. |
   |                           |                 |                                                                                                                                                                                                                                                                                                                                                |                                                                                                                                                                                                                     |
   |                           |                 | -  A large value will accelerate pod startup but slow down the unbinding of idle container network interfaces and decrease the IP address usage. **Exercise caution when performing this operation.**                                                                                                                                          |                                                                                                                                                                                                                     |
   |                           |                 | -  A small value will speed up the unbinding of idle container network interfaces and increase the IP address usage but will slow down pod startup, especially when a large number of pods increase instantaneously.                                                                                                                           |                                                                                                                                                                                                                     |
   +---------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. note::

   The preceding parameters support global configuration at the cluster level and custom settings at the node pool level. The latter takes priority over the former.

The container networking component maintains a scalable pre-bound network interface pool for each node. The component checks and calculates the number of pre-bound network interfaces or idle network interfaces every 10 seconds.

-  **Number of pre-bound network interfaces = min(nic-maximum-target - Number of bound network interfaces, max(nic-minimum-target - Number of bound network interfaces, nic-warm-target - Number of idle network interfaces)**
-  **Number of network interfaces to be unbound = min(Number of idle network interfaces - nic-warm-target - nic-max-above-warm-target, Number of bound network interfaces - nic-minimum-target)**

The number of pre-binding network interfaces on the node remains in the following range:

-  **Minimum number of network interfaces to be pre-bound = min(max(nic-minimum-target - Number of bound network interfaces, nic-warm-target), nic-maximum-target - Number of bound network interfaces)**
-  **Maximum number of network interfaces to be pre-bound = max(nic-warm-target + nic-max-above-warm-target, Number of bound network interfaces - nic-minimum-target)**

When a pod is created, an idle pre-bound network interface (the earliest unused one) will be preferentially allocated from the pool. If no idle network interface is available, a new network interface will be created or a new supplementary network interface will be bound to the pod.

When the pod is deleted, the corresponding network interface is released back to the pre-bound network interface pool of the node, enters a 2-minute cooldown period, and can be bound to another pod. If the network interface is not bound to any pod within 2 minutes, it will be released.

Helpful Links
-------------

-  For details about the maximum number of pods that can run on a node in a cluster with different network models, see :ref:`Maximum Number of Pods That Can Be Created on a Node <cce_10_0348>`.
