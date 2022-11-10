:original_name: cce_01_0284.html

.. _cce_01_0284:

Cloud Native Network 2.0
========================

Model Definition
----------------

Developed by CCE, Cloud Native Network 2.0 deeply integrates Elastic Network Interfaces (ENIs) and sub-ENIs of Virtual Private Cloud (VPC). Container IP addresses are allocated from the VPC CIDR block. ELB passthrough networking is supported to direct access requests to containers. Security groups and elastic IPs (EIPs) are bound to deliver high performance.


.. figure:: /_static/images/en-us_image_0000001231949185.png
   :alt: **Figure 1** Cloud Native Network 2.0

   **Figure 1** Cloud Native Network 2.0

**Pod-to-pod communication**

-  On the same node: Packets are forwarded through the VPC ENI or sub-ENI.
-  Across nodes: Packets are forwarded through the VPC ENI or sub-ENI.

Notes and Constraints
---------------------

This network model is available only to CCE Turbo clusters.

Advantages and Disadvantages
----------------------------

**Advantages**

-  As the container network directly uses VPC, it is easy to locate network problems and provide the highest performance.
-  External networks in a VPC can be directly connected to container IP addresses.
-  The load balancing, security group, and EIP capabilities provided by VPC can be used directly.

**Disadvantages**

The container network directly uses VPC, which occupies the VPC address space. Therefore, you must properly plan the container CIDR block before creating a cluster.

Application Scenarios
---------------------

-  High performance requirements and use of other VPC network capabilities: Cloud Native Network 2.0 directly uses VPC, which delivers almost the same performance as the VPC network. Therefore, it is applicable to scenarios that have high requirements on bandwidth and latency, such as online live broadcast and e-commerce seckill.
-  Large-scale networking: Cloud Native Network 2.0 supports a maximum of 2000 ECS nodes and 100,000 containers.

Container IP Address Management
-------------------------------

In the Cloud Native Network 2.0 model, BMS nodes use ENIs and ECS nodes use sub-ENIs. The following figure shows how IP addresses are managed on these nodes.


.. figure:: /_static/images/en-us_image_0000001172076961.png
   :alt: **Figure 2** IP address management in Cloud Native Network 2.0

   **Figure 2** IP address management in Cloud Native Network 2.0

-  Pod IP addresses are allocated from **Pod Subnet** you configure from the VPC.
-  ENIs and sub-ENIs bound to an ECS node = Number of ENIs used to bear sub-ENIs + Number of sub-ENIs currently used by pods + Number of sub-ENIs to be bound
-  ENIs bound to a BMS node = Number of ENIs currently used by pods + Number of pre-bound ENIs
-  Pre-binding policy: The system periodically (every 2 minutes by default) checks whether the total number of ENIs on the node. If the low threshold is not reached, the system pre-binds ENIs. If the high threshold is exceeded, the system releases ENIs.
-  On an ECS node, when the number of pre-bound sub-ENIs plus the number of sub-ENIs currently used by the pods is **smaller than** the number of sub-ENIs at the low threshold (sub-ENI quota on the node x low threshold), the system **pre-binds** sub-ENIs to make the numbers equal.
-  On an ECS node, when the number of pre-bound sub-ENIs plus the number of sub-ENIs currently used by the pods is **larger than** the number of sub-ENIs at the high threshold (sub-ENI quota on the node x high threshold), the system **releases** sub-ENIs to make the numbers equal.
-  On a BMS node, when the number of pre-bound ENIs plus the number of ENIs currently used by the pods is **smaller than** the number of ENIs at the low threshold (ENI quota on the node x low threshold), the system **pre-binds** ENIs to make the numbers equal.
-  On a BMS node, when the number of pre-bound ENIs plus the number of ENIs currently used by the pods is **larger than** the number of ENIs at the high threshold (ENI quota on the node x high threshold), the system **releases** ENIs to make the numbers equal.

Recommendation for CIDR Block Planning
--------------------------------------

As described in :ref:`Cluster Network Structure <cce_01_0010__section1131733719195>`, network addresses in a cluster can be divided into three parts: node network, container network, and service network. When planning network addresses, consider the following aspects:

-  The three CIDR blocks cannot overlap. Otherwise, a conflict occurs. All subnets (including those created from the secondary CIDR block) in the VPC where the cluster resides cannot conflict with the container and Service CIDR blocks.
-  Ensure that each CIDR block has sufficient IP addresses.

   -  The IP addresses in the node CIDR block must match the cluster scale. Otherwise, nodes cannot be created due to insufficient IP addresses.
   -  The IP addresses in the container CIDR block must match the service scale. Otherwise, pods cannot be created due to insufficient IP addresses.

In the Cloud Native Network 2.0 model, the container CIDR block and node CIDR block share the network addresses in a VPC. It is recommended that the container subnet and node subnet not use the same subnet. Otherwise, containers or nodes may fail to be created due to insufficient IP resources.

In addition, a subnet can be added to the container CIDR block after a cluster is created to increase the number of available IP addresses. In this case, ensure that the added subnet does not conflict with other subnets in the container CIDR block.


.. figure:: /_static/images/en-us_image_0000001159831938.png
   :alt: **Figure 3** Configuring CIDR blocks

   **Figure 3** Configuring CIDR blocks

Example of Cloud Native Network 2.0 Access
------------------------------------------

Create a CCE Turbo cluster, which contains three ECS nodes.


.. figure:: /_static/images/en-us_image_0000001198867835.png
   :alt: **Figure 4** Cluster network

   **Figure 4** Cluster network

Access the details page of one node. You can see that the node has one primary NIC and one extended NIC, and both of them are ENIs. The extended NIC belongs to the container CIDR block and is used to mount a sub-ENI to the pod.

Create a Deployment on the cluster.

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

View the created pod.

.. code-block::

   $ kubectl get pod -owide
   NAME                       READY   STATUS    RESTARTS   AGE   IP            NODE         NOMINATED NODE   READINESS GATES
   example-5bdc5699b7-54v7g   1/1     Running   0          7s    10.1.18.2     10.1.0.167   <none>           <none>
   example-5bdc5699b7-6dzx5   1/1     Running   0          7s    10.1.18.216   10.1.0.186   <none>           <none>
   example-5bdc5699b7-gq7xs   1/1     Running   0          7s    10.1.16.63    10.1.0.144   <none>           <none>
   example-5bdc5699b7-h9rvb   1/1     Running   0          7s    10.1.16.125   10.1.0.167   <none>           <none>
   example-5bdc5699b7-s9fts   1/1     Running   0          7s    10.1.16.89    10.1.0.144   <none>           <none>
   example-5bdc5699b7-swq6q   1/1     Running   0          7s    10.1.17.111   10.1.0.167   <none>           <none>

The IP addresses of all pods are sub-ENIs, which are mounted to the ENI (extended NIC) of the node.

For example, the extended NIC of node 10.1.0.167 is 10.1.17.172. On the **Network Interfaces** page of the Network Console, you can see that three sub-ENIs are mounted to the extended NIC 10.1.17.172, which is the IP address of the pod.

In the VPC, the IP address of the pod can be successfully accessed.
