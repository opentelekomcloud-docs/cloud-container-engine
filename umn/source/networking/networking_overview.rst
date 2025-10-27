:original_name: cce_10_0010.html

.. _cce_10_0010:

Networking Overview
===================

The CCE cluster network architecture is based on the Kubernetes native network model. Combined with the cloud infrastructure capabilities, the architecture builds a three-layer communication system covering nodes, containers, and services. It is built to efficiently forward intra-cluster and inter-cluster traffic, discover services, and isolate networks. It meets the requirements of all scenarios that cover small- and medium-sized applications and large-scale microservice architectures.

You can learn about the cluster network from the following aspects:

-  Cluster network structure (:ref:`Cluster Network Structure <cce_10_0010__section1131733719195>`): A cluster consists of multiple nodes, and each node runs multiple pods (containers). To ensure the communications between nodes, between nodes and pods, and between pods, a cluster requires:

   -  **A node network**: enables all nodes in a cluster to communicate with each other.
   -  **A container network**: enables all pods in a cluster to communicate with each other using IP addresses without NAT.
   -  **A Service network**: ensures Services in a cluster can be accessed by other pods or Services in that cluster through stable virtual IP addresses.

-  **Pod access in a cluster**: Kubernetes provides Services (:ref:`Service <cce_10_0010__section1860619221134>`) and ingresses (:ref:`Ingress <cce_10_0010__section1248852094313>`) for pod access. This section summarizes common network access scenarios. You can select the appropriate scenario based on site requirements. For details about the network access scenarios, see :ref:`Access Scenarios <cce_10_0010__section1286493159>`.

.. _cce_10_0010__section1131733719195:

Cluster Network Structure
-------------------------

Cluster networks are the core of Kubernetes. They ensure that containers in a cluster can communicate with each other and with external systems. There are:

-  **Node network**: CCE uses VPC subnets as the node network of a cluster. The available IP addresses of a subnet limit the maximum number of nodes that can be created in a cluster. For example, a subnet with a mask of /24 can allocate a maximum of 254 node IP addresses. The number of nodes that can be created in a cluster is also affected by the container network. For details, see container network models.

-  **Container network**: Pods in a cluster are allocated independent IP addresses. All pods in a cluster are on a flat network and can be accessed using their IP addresses without NAT. Kubernetes uses `Container Network Interface (CNI) <https://github.com/containernetworking/cni>`__ to standardize the network between containers. Network model plugins are used to allocate independent IP addresses to pods for flat network communications in a cluster. Different network models have different allocation principles.


   .. figure:: /_static/images/en-us_image_0000002467719141.png
      :alt: **Figure 1** Container network

      **Figure 1** Container network

   Currently, CCE supports the following container network models:

   -  :ref:`Container tunnel network <cce_10_0282>`: This network model is constructed based on the node network through tunnel encapsulation, but it is independent of the node network. It uses VXLAN to encapsulate Ethernet packets into UDP packets and transmits them in tunnels. Open vSwitch serves as the backend virtual switch.
   -  :ref:`VPC network <cce_10_0283>`: This network model seamlessly combines VPC routing with the underlying network, making it ideal for high-performance scenarios. However, the maximum number of nodes allowed in a cluster is determined by the VPC route quota. Each node in a cluster that uses a VPC network is running in a subnet with a fixed number of IP addresses. The VPC network model outperforms the container tunnel network model in terms of performance because it does not have tunnel encapsulation overhead. In addition, as the routes destined for nodes and containers are added to a VPC route table, containers can be directly accessed from outside the cluster.
   -  :ref:`Cloud Native Network 2.0 <cce_10_0284>` is a next-generation model developed by CCE and combines the network interfaces and supplementary network interfaces of VPC. Pod IP addresses are allocated from the VPC CIDR block. ELB passthrough networking is supported to forward requests to containers. Security groups and EIPs are associated to deliver high performance.

   The performance, networking scale, and application scenarios of a container network vary depending on the container network model. For details about the functions and features of different container network models, see :ref:`Overview <cce_10_0281>`.

-  **Service network**: Services are a kind of Kubernetes resource object. Each Service has a fixed IP address. Kubernetes provides stable access entries for pods through Services. When creating a cluster on CCE, you can specify the Service CIDR block. Service CIDR blocks cannot overlap with the node CIDR blocks or container CIDR blocks. They can be used only in a cluster.

.. _cce_10_0010__section1860619221134:

Service
-------

In Kubernetes, pods are considered ephemeral and can be replaced at any time. When a pod is destroyed or replaced, its network resources also change. You need to provide a stable access method for pods. Kubernetes uses a Service to provide a fixed access entry for a group of pods with the same functions and balances the load among these pods.

As shown in the following figure, a Service is associated with a group of pods through a selector. When the IP address and port of the Service are accessed, traffic is distributed to these pods. When pods change, the Service automatically updates the backend forwarding rules to ensure that the latest pods can be accessed through the Service.


.. figure:: /_static/images/en-us_image_0258889981.png
   :alt: **Figure 2** Accessing pods through a Service

   **Figure 2** Accessing pods through a Service

You can configure the following types of Services:

-  ClusterIP: used to make the Service only reachable from within a cluster.
-  NodePort: used for access from outside a cluster. A NodePort Service is accessed through the port on the node.
-  LoadBalancer: used for access from outside a cluster. It is an extension of NodePort, to which a load balancer routes, and external systems only need to access the load balancer.

For details about the Service, see :ref:`Service Overview <cce_10_0249>`.

.. _cce_10_0010__section1248852094313:

Ingress
-------

Services forward requests using TCP and UDP at Layer 4. Ingresses forward requests using HTTP and HTTPS at Layer 7. Domain names and paths can be used for access of finer granularities.


.. figure:: /_static/images/en-us_image_0258961458.png
   :alt: **Figure 3** An ingress and associated Services

   **Figure 3** An ingress and associated Services

For details about the ingress, see :ref:`Ingress Overview <cce_10_0094>`.

DNS
---

CCE uses CoreDNS to implement service discovery in a cluster. For example, a client can access backend pods through a ClusterIP Service whose name is mapped to a cluster-scoped virtual IP address. This approach decouples the invoking between applications in a cluster from specific IP addresses and deployment environments. For details about the cluster DNS settings, see :ref:`DNS Overview <cce_10_0360>`.


.. figure:: /_static/images/en-us_image_0000002467679373.png
   :alt: **Figure 4** Example of domain name resolution in a cluster

   **Figure 4** Example of domain name resolution in a cluster

.. _cce_10_0010__section1286493159:

Access Scenarios
----------------

Workload access scenarios can be categorized as follows:

-  Intra-cluster access: A ClusterIP Service is used for workloads in the same cluster to access each other.
-  Access from outside a cluster: A Service (NodePort or LoadBalancer type) or an ingress is recommended for a workload outside a cluster to access workloads in the cluster.

   -  Access through the public network: An EIP should be bound to the node or load balancer.
   -  Access through the private network: The workload can be accessed through the internal IP address of the node or load balancer. If workloads are located in different VPCs, a peering connection is required to enable communication between different VPCs.

-  The workload can access the external network as follows:

   -  Accessing a private network: The workload accesses the private network address, but the implementation method varies depending on container network models. Ensure that the peer security group allows access from the container CIDR block.
   -  Accessing a public network: Assign an EIP to the node where the workload runs (when a VPC network or tunnel network is used), bind an EIP to the pod IP address (when Cloud Native Network 2.0 is used), or configure an SNAT rule on the NAT gateway. For details, see :ref:`Accessing the Internet from a Container <cce_10_0400>`.


.. figure:: /_static/images/en-us_image_0000002434080684.png
   :alt: **Figure 5** Network access diagram

   **Figure 5** Network access diagram
