:original_name: cce_10_0284.html

.. _cce_10_0284:

Cloud Native 2.0 Network
========================

Model Definition
----------------

Developed by CCE, Cloud Native 2.0 network deeply integrates Elastic Network Interfaces (ENIs) and sub-ENIs of Virtual Private Cloud (VPC). Container IP addresses are allocated from the VPC CIDR block. ELB passthrough networking is supported to direct access requests to containers. Security groups and elastic IPs (EIPs) are bound to deliver high performance.


.. figure:: /_static/images/en-us_image_0000001750950096.png
   :alt: **Figure 1** Cloud Native Network 2.0

   **Figure 1** Cloud Native Network 2.0

**Pod-to-pod communication**

-  Pods on BMS nodes use ENIs, whereas pods on ECS nodes use Sub-ENIs. Sub-ENIs are attached to ENIs through VLAN sub-interfaces.
-  On the same node: Packets are forwarded through the VPC ENI or sub-ENI.
-  Across nodes: Packets are forwarded through the VPC ENI or sub-ENI.

Advantages and Disadvantages
----------------------------

**Advantages**

-  As the container network directly uses VPC, it is easy to locate network problems and provide the highest performance.
-  External networks in a VPC can be directly connected to container IP addresses.
-  The load balancing, security group, and EIP capabilities provided by VPC can be directly used by pods.

**Disadvantages**

The container network directly uses VPC, which occupies the VPC address space. Therefore, you must properly plan the container CIDR block before creating a cluster.

Application Scenarios
---------------------

-  High performance requirements and use of other VPC network capabilities: Cloud Native Network 2.0 directly uses VPC, which delivers almost the same performance as the VPC network. Therefore, it applies to scenarios that have high requirements on bandwidth and latency.
-  Large-scale networking: Cloud Native Network 2.0 supports a maximum of 2000 ECS nodes and 100,000 containers.

Recommendation for CIDR Block Planning
--------------------------------------

As described in :ref:`Cluster Network Structure <cce_10_0010__section1131733719195>`, network addresses in a cluster can be divided into three parts: node network, container network, and service network. When planning network addresses, consider the following aspects:

-  The three CIDR blocks cannot overlap. Otherwise, a conflict occurs. All subnets (including those created from the secondary CIDR block) in the VPC where the cluster resides cannot conflict with the container and Service CIDR blocks.
-  Ensure that each CIDR block has sufficient IP addresses.

   -  The IP addresses in the node CIDR block must match the cluster scale. Otherwise, nodes cannot be created due to insufficient IP addresses.
   -  The IP addresses in the container CIDR block must match the service scale. Otherwise, pods cannot be created due to insufficient IP addresses.

In the Cloud Native Network 2.0 model, the container CIDR block and node CIDR block share the network addresses in a VPC. It is recommended that the container subnet and node subnet not use the same subnet. Otherwise, containers or nodes may fail to be created due to insufficient IP resources.

In addition, a subnet can be added to the container CIDR block after a cluster is created to increase the number of available IP addresses. In this case, ensure that the added subnet does not conflict with other subnets in the container CIDR block.


.. figure:: /_static/images/en-us_image_0000001865760133.png
   :alt: **Figure 2** Configuring CIDR blocks

   **Figure 2** Configuring CIDR blocks

Example of Cloud Native Network 2.0 Access
------------------------------------------

Create a CCE Turbo cluster, which contains three ECS nodes.

Access the details page of one node. You can see that the node has one primary ENI and one extended ENI, and both of them are ENIs. The extended ENI belongs to the container CIDR block and is used to mount a sub-ENI to the pod.

Create a Deployment in the cluster.

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

The IP addresses of all pods are sub-ENIs, which are mounted to the ENI (extended ENI) of the node.

For example, the extended ENI of node 10.1.0.167 is 10.1.17.172. On the **Network Interfaces** page of the Network Console, you can see that three sub-ENIs are mounted to the extended ENI 10.1.17.172, which is the IP address of the pod.

In the VPC, the IP address of the pod can be successfully accessed.
