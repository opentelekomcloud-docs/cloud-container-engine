:original_name: cce_bestpractice_10016.html

.. _cce_bestpractice_10016:

Configuring a CCE Cluster
=========================

When you use CCE to create a Kubernetes cluster, there are multiple configuration options and terms. This sections compares the key configurations for CCE clusters and provides recommendations to help you create a cluster that better suits your needs.

Cluster Types
-------------

CCE supports CCE Turbo clusters and CCE standard clusters to meet your requirements. This section describes the differences between these two types of clusters.

.. table:: **Table 1** Cluster types

   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
   | Category        | Subcategory                 | CCE Turbo Cluster                                                                                                              | CCE Standard Cluster                                                                   |
   +=================+=============================+================================================================================================================================+========================================================================================+
   | Cluster         | Positioning                 | Next-gen container cluster designed for Cloud Native 2.0, with accelerated computing, networking, and scheduling               | Standard cluster for common commercial use                                             |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
   |                 | Node type                   | Deployment of VMs                                                                                                              | Deployment of VMs and bare-metal servers                                               |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
   | Networking      | Model                       | **Cloud Native Network 2.0**: applies to large-scale and high-performance scenarios.                                           | **Cloud Native Network 1.0**: applies to common, smaller-scale scenarios.              |
   |                 |                             |                                                                                                                                |                                                                                        |
   |                 |                             | Max networking scale: 2,000 nodes                                                                                              | -  Tunnel network model                                                                |
   |                 |                             |                                                                                                                                | -  VPC network model                                                                   |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
   |                 | Performance                 | Flattens the VPC network and container network into one, achieving zero performance loss.                                      | Overlays the VPC network with the container network, causing certain performance loss. |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
   |                 | Container network isolation | Associates pods with security groups. Unifies security isolation in and out the cluster via security groups' network policies. | -  Tunnel network model: supports network policies for intra-cluster communications.   |
   |                 |                             |                                                                                                                                | -  VPC network model: supports no isolation.                                           |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
   | Security        | Isolation                   | -  VM: runs common containers, isolated by cgroups.                                                                            | Runs common containers, isolated by cgroups.                                           |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+

Cluster Versions
----------------

Due to the fast iteration, many bugs are fixed and new features are added in the new Kubernetes versions. The old versions will be gradually eliminated. When creating a cluster, select the latest commercial version supported by CCE.

.. _cce_bestpractice_10016__section13189203510317:

Network Models
--------------

This section describes the network models supported by CCE clusters. You can select one model based on your requirements.

.. important::

   After clusters are created, the network models cannot be changed. Exercise caution when selecting the network models.

.. table:: **Table 2** Network model comparison

   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------+
   | Dimension              | Tunnel Network                                                                                                                    | VPC Network                                                                                                                                                          | Cloud Native Network 2.0                                                                                   |
   +========================+===================================================================================================================================+======================================================================================================================================================================+============================================================================================================+
   | Application scenarios  | -  Common container service scenarios                                                                                             | -  Scenarios that have high requirements on network latency and bandwidth                                                                                            | -  Scenarios that have high requirements on network latency, bandwidth, and performance                    |
   |                        | -  Scenarios that do not have high requirements on network latency and bandwidth                                                  | -  Containers can communicate with VMs using a microservice registration framework, such as Dubbo and CSE.                                                           | -  Containers can communicate with VMs using a microservice registration framework, such as Dubbo and CSE. |
   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------+
   | Core technology        | OVS                                                                                                                               | IPvlan and VPC route                                                                                                                                                 | VPC ENI/sub-ENI                                                                                            |
   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------+
   | Applicable clusters    | CCE standard cluster                                                                                                              | CCE standard cluster                                                                                                                                                 | CCE Turbo cluster                                                                                          |
   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------+
   | Network isolation      | Kubernetes native NetworkPolicy for pods                                                                                          | No                                                                                                                                                                   | Pods support security group isolation.                                                                     |
   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------+
   | Passthrough networking | No                                                                                                                                | No                                                                                                                                                                   | Yes                                                                                                        |
   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------+
   | IP address management  | -  The container CIDR block is allocated separately.                                                                              | -  The container CIDR block is allocated separately.                                                                                                                 | The container CIDR block is divided from the VPC subnet and does not need to be allocated separately.      |
   |                        | -  CIDR blocks are divided by node and can be dynamically allocated (CIDR blocks can be dynamically added after being allocated.) | -  CIDR blocks are divided by node and statically allocated (the CIDR block cannot be changed after a node is created).                                              |                                                                                                            |
   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------+
   | Network performance    | Performance loss due to VXLAN encapsulation                                                                                       | No tunnel encapsulation. Cross-node packets are forwarded through VPC routers, delivering performance equivalent to that of the host network.                        | The container network is integrated with the VPC network, eliminating performance loss.                    |
   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------+
   | Networking scale       | A maximum of 2000 nodes are supported.                                                                                            | Suitable for small- and medium-scale networks due to the limitation on VPC routing tables. It is recommended that the number of nodes be less than or equal to 1000. | A maximum of 2000 nodes are supported.                                                                     |
   |                        |                                                                                                                                   |                                                                                                                                                                      |                                                                                                            |
   |                        |                                                                                                                                   | Each time a node is added to the cluster, a route is added to the VPC routing tables. Therefore, the cluster scale is limited by the VPC routing tables.             |                                                                                                            |
   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------+

Cluster CIDR Blocks
-------------------

There are node CIDR blocks, container CIDR blocks, and Service CIDR blocks in CCE clusters. When planning network addresses, note that:

-  These three types of CIDR blocks cannot overlap with each other. Otherwise, a conflict will occur. All subnets (including those created from the secondary CIDR block) in the VPC where the cluster resides cannot conflict with the container and Service CIDR blocks.
-  There are sufficient IP addresses in each CIDR block.

   -  The IP addresses in a node CIDR block must match the cluster scale. Otherwise, nodes cannot be created due to insufficient IP addresses.
   -  The IP addresses in a container CIDR block must match the service scale. Otherwise, pods cannot be created due to insufficient IP addresses.

In complex scenarios, for example, multiple clusters use the same VPC or clusters are interconnected across VPCs, determine the number of VPCs, the number of subnets, the container CIDR blocks, and the communication modes of the Service CIDR blocks. For details, see :ref:`Planning CIDR Blocks for a Cluster <cce_bestpractice_00004>`.

Service Forwarding Modes
------------------------

kube-proxy is a key component of a Kubernetes cluster. It is responsible for load balancing and forwarding between a Service and its backend pod.

CCE supports the iptables and IPVS forwarding modes.

-  IPVS allows higher throughput and faster forwarding. It applies to scenarios where the cluster scale is large or the number of Services is large.
-  iptables is the traditional kube-proxy mode. This mode applies to the scenario where the number of Services is small or there are a large number of short concurrent connections on the client.

If high stability is required and the number of Services is less than 2000, the iptables forwarding mode is recommended. In other scenarios, the IPVS forwarding mode is recommended.

Node Specifications
-------------------

The minimum specifications of a node are 2 vCPUs and 4 GiB memory. Evaluate based on service requirements before configuring the nodes. However, using many low-specification ECSs is not the optimal choice. The reasons are as follows:

-  The upper limit of network resources is low, which may result in a single-point bottleneck.
-  Resources may be wasted. If each container running on a low-specification node needs a lot of resources, the node cannot run multiple containers and there may be idle resources in it.

Advantages of using large-specification nodes are as follows:

-  The upper limit of the network bandwidth is high. This ensures higher resource utilization for high-bandwidth applications.
-  Multiple containers can run on the same node, and the network latency between containers is low.
-  The efficiency of pulling images is higher. This is because an image can be used by multiple containers on a node after being pulled once. Low-specifications ECSs cannot respond promptly because the images are pulled many times and it takes more time to scale these nodes.

Additionally, select a proper vCPU/memory ratio based on your requirements. For example, if a service container with large memory but fewer CPUs is used, configure the specifications with the vCPU/memory ratio of 1:4 for the node where the container resides to reduce resource waste.

Container Engines
-----------------

CCE supports the containerd and Docker container engines. **containerd is recommended for its shorter traces, fewer components, higher stability, and less consumption of node resources**. Since Kubernetes 1.24, Dockershim is removed and Docker is no longer supported by default. For details, see `Kubernetes is Moving on From Dockershim: Commitments and Next Steps <https://kubernetes.io/blog/2022/01/07/kubernetes-is-moving-on-from-dockershim/>`__. CCE clusters 1.27 do not support the Docker container engine.

Use containerd in typical scenarios. The Docker container engine is supported only in the following scenarios:

-  Docker in Docker (usually in CI scenarios)
-  Running the Docker commands on the nodes
-  Calling Docker APIs

Node OS
-------

Service container runtimes share the kernel and underlying calls of nodes. To ensure compatibility, select a Linux distribution version that is the same as or close to that of the final service container image for the node OS.
