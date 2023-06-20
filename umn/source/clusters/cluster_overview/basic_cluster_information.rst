:original_name: cce_10_0430.html

.. _cce_10_0430:

Basic Cluster Information
=========================

`Kubernetes <https://kubernetes.io/>`__ is an open source container orchestration engine for automating deployment, scaling, and management of containerized applications.

For developers, Kubernetes is a cluster operating system. Kubernetes provides service discovery, scaling, load balancing, self-healing, and even leader election, freeing developers from infrastructure-related configurations.

When using Kubernetes, it is like you run a large number of servers as one and the method for deploying applications in Kubernetes is always the same.

Kubernetes Cluster Architecture
-------------------------------

A Kubernetes cluster consists of master nodes (Masters) and worker nodes (Nodes). Applications are deployed on worker nodes, and you can specify the nodes for deployment.

.. note::

   For a cluster created on CCE, the master node is hosted by CCE. You only need to create a node.

The following figure shows the architecture of a Kubernetes cluster.


.. figure:: /_static/images/en-us_image_0000001568822869.png
   :alt: **Figure 1** Kubernetes cluster architecture

   **Figure 1** Kubernetes cluster architecture

**Master node**

A master node is the machine where the control plane components run, including API server, Scheduler, Controller manager, and etcd.

-  API server: functions as a transit station for components to communicate with each other, receives external requests, and writes information to etcd.
-  Controller manager: performs cluster-level functions, such as component replication, node tracing, and node fault fixing.
-  Scheduler: schedules containers to nodes based on various conditions (such as available resources and node affinity).
-  etcd: serves as a distributed data storage component that stores cluster configuration information.

In a production environment, multiple master nodes are deployed to ensure high cluster availability. For example, you can deploy three master nodes for your CCE cluster.

**Worker node**

A worker node is a compute node in a cluster, that is, a node running containerized applications. A worker node has the following components:

-  kubelet: communicates with the container runtime, interacts with the API server, and manages containers on the node.
-  kube-proxy: serves as an access proxy between application components.
-  Container runtime: functions as the software for running containers. You can download images to build your container runtime, such as Docker.

Master Nodes and Cluster Scale
------------------------------

When you create a cluster on CCE, you can have one or three master nodes. Three master nodes can create a cluster in HA mode.

The master node specifications decide the number of nodes that can be managed by a cluster. You can select the cluster management scale, for example, 50 or 200 nodes.

Cluster Network
---------------

From the perspective of the network, all nodes in a cluster are located in a VPC, and containers are running on the nodes. You need to configure node-node, node-container, and container-container communication.

A cluster network can be divided into three network types:

-  Node network: IP addresses are assigned to nodes in a cluster.
-  Container network: IP addresses are assigned to containers in a cluster for communication. Currently, multiple container network models are supported, and each model has its own working mechanism.
-  Service network: A Service is a Kubernetes object used to access containers. Each Service has a fixed IP address.

When you create a cluster, select a proper CIDR block for each network. Ensure that the CIDR blocks do not conflict with each other and have sufficient available IP addresses. **You cannot change the container network model after the cluster is created.** Plan the container network model properly in advance.

You are advised to learn about the cluster network and container network models before creating a cluster. For details, see :ref:`Container Network Models <cce_10_0280>`.

Cluster Lifecycle
-----------------

.. table:: **Table 1** Cluster status

   +-------------+-------------------------------------------------------------------+
   | Status      | Description                                                       |
   +=============+===================================================================+
   | Creating    | A cluster is being created and is requesting for cloud resources. |
   +-------------+-------------------------------------------------------------------+
   | Running     | A cluster is running properly.                                    |
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
