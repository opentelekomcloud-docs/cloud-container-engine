:original_name: cce_01_0002.html

.. _cce_01_0002:

Cluster Overview
================

`Kubernetes <https://kubernetes.io/>`__ is a containerized application software system that can be easily deployed and managed. It facilitates container scheduling and orchestration.

For application developers, Kubernetes can be regarded as a cluster operating system. Kubernetes provides functions such as service discovery, scaling, load balancing, self-healing, and even leader election, freeing developers from infrastructure-related configurations.

When using Kubernetes, it is like you run a large number of servers as one on which your applications run. Regardless of the number of servers in a Kubernetes cluster, the method for deploying applications in Kubernetes is always the same.

Kubernetes Cluster Architecture
-------------------------------

A Kubernetes cluster consists of master nodes (masters) and worker nodes (nodes). Applications are deployed on worker nodes, and you can specify the nodes for deployment.

The following figure shows the architecture of a Kubernetes cluster.


.. figure:: /_static/images/en-us_image_0267028603.png
   :alt: **Figure 1** Kubernetes cluster architecture

   **Figure 1** Kubernetes cluster architecture

**Master node**

A master node is the machine where the control plane components run, including API server, Scheduler, Controller manager, and etcd.

-  API server: functions as a transit station for components to communicate with each other, receives external requests, and writes information to etcd.
-  Controller manager: performs cluster-level functions, such as component replication, node tracing, and node fault fixing.
-  Scheduler: schedules containers to nodes based on various conditions (such as available resources and node affinity).
-  etcd: serves as a distributed data storage component that stores cluster configuration information.

In the production environment, multiple master nodes are deployed to ensure cluster high availability. For example, you can deploy three master nodes for your CCE cluster.

**Worker node**

A worker node is a compute node in a cluster, that is, a node running containerized applications. A worker node has the following components:

-  kubelet: communicates with the container runtime, interacts with the API server, and manages containers on the node.
-  kube-proxy: serves as an access proxy between application components.
-  Container runtime: functions as the software for running containers. You can download images to build your container runtime, such as Docker.

Number of Master Nodes and Cluster Scale
----------------------------------------

When you create a cluster on CCE, the number of master nodes can be set to 1 or 3. Three master nodes can be deployed to create a cluster in HA mode.

The master node specifications determine the number of nodes that can be managed by a cluster. When creating a cluster, you can select the cluster management scale, for example, 50 or 200 nodes.

Cluster Network
---------------

From the perspective of the network, all nodes in a cluster are located in a VPC, and containers are running on the nodes. You need to configure node-node, node-container, and container-container communication.

A cluster network can be divided into three network types:

-  Node network: IP addresses are assigned to nodes in a cluster.
-  Container network: IP addresses are assigned to containers in a cluster for communication between them. Currently, multiple container network models are supported, and each model has its own working mechanism.
-  Service network: A service is a Kubernetes object used to access containers. Each Service has a fixed IP address.

When you create a cluster, select a proper CIDR block for each network to ensure that the CIDR blocks do not conflict with each other and each CIDR block has sufficient available IP addresses. **After a cluster is created, the container network model cannot be modified.** Plan the container network model properly before creating a cluster.

You are advised to learn about the cluster network and container network models before creating a cluster. For details, see :ref:`Overview <cce_01_0010>`.

Cluster Security Groups
-----------------------

When a cluster is created, the following security groups are created to ensure cluster security:

-  *Cluster name*-cce-control-*Random number*: security group of the master node.

   Observe the following principles when configuring security groups:

   -  The source IP addresses defined in the security group rules must be permitted.
   -  **4789** (required only for clusters using the container tunnel network model): used for network access between containers.
   -  **5443** and **5444**: ports to which kube-apiserver of the master node listens. These ports must permit requests from VPC and container CIDR blocks.
   -  **9443**: used by the network add-on of a worker node to access the master node.
   -  **8445**: used by the storage add-on of a worker node to access the master node.

-  *Cluster name*-cce-node-*Random number*: security group of a worker node.

   Observe the following principles when configuring security groups:

   -  The source IP addresses defined in the security group rules must be permitted.
   -  **4789** (required only for clusters using the container tunnel network model): used for network access between containers.
   -  **10250**: used by the master node to access the kubelet component of a worker node (for example, run the kubectl exec {pod} command).
   -  **30000**-**32767**: external access port (Nodeport) of a node. These ports need be specified when you create a Service. These ports must permit requests from VPC, container, and ELB CIDR blocks.

After a cluster is created, you can view the created security group on the VPC console.

.. warning::

   Do not delete the security groups and related rules automatically configured during cluster creation. Otherwise, the cluster will exhibit unexpected behavior.

Cluster Lifecycle
-----------------

.. table:: **Table 1** Cluster status

   +-------------+-------------------------------------------------------------------+
   | Status      | Description                                                       |
   +=============+===================================================================+
   | Creating    | A cluster is being created and is requesting for cloud resources. |
   +-------------+-------------------------------------------------------------------+
   | Normal      | A cluster is running properly.                                    |
   +-------------+-------------------------------------------------------------------+
   | Scaling-out | A node is being added to a cluster.                               |
   +-------------+-------------------------------------------------------------------+
   | Scaling-in  | A node is being deleted from a cluster.                           |
   +-------------+-------------------------------------------------------------------+
   | Hibernating | A cluster is hibernating.                                         |
   +-------------+-------------------------------------------------------------------+
   | Awaking     | A cluster is being woken up.                                      |
   +-------------+-------------------------------------------------------------------+
   | Upgrading   | A cluster is being upgraded.                                      |
   +-------------+-------------------------------------------------------------------+
   | Unavailable | A cluster is unavailable.                                         |
   +-------------+-------------------------------------------------------------------+
   | Deleting    | A cluster is being deleted.                                       |
   +-------------+-------------------------------------------------------------------+


.. figure:: /_static/images/en-us_image_0000001160731158.png
   :alt: **Figure 2** Cluster status transition

   **Figure 2** Cluster status transition
