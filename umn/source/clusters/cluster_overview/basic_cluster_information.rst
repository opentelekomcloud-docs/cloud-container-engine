:original_name: cce_10_0430.html

.. _cce_10_0430:

Basic Cluster Information
=========================

`Kubernetes <https://kubernetes.io/>`__ is an open source container orchestration engine for automating deployment, scaling, and management of containerized applications.

For developers, Kubernetes is a cluster operating system. Kubernetes provides service discovery, scaling, load balancing, self-healing, and even leader election, freeing developers from infrastructure-related configurations.

Cluster Network
---------------

A cluster network can be divided into three network types:

-  Node network: IP addresses are assigned to nodes in a cluster.
-  Container network: IP addresses are assigned to containers in a cluster for communication. Currently, multiple container network models are supported, and each model has its own working mechanism.
-  Service network: A Service is a Kubernetes object used to access containers. Each Service has a static IP address.

When you create a cluster, select a proper CIDR block for each network. Ensure that the CIDR blocks do not conflict with each other and have sufficient available IP addresses. **You cannot change the container network model after the cluster is created.** Plan the container network model properly in advance.

You are advised to learn about the cluster network and container network models before creating a cluster. For details, see :ref:`Container Network Models <cce_10_0280>`.

Master Nodes and Cluster Scale
------------------------------

When you create a cluster on CCE, you can have one or three master nodes. Three master nodes will be deployed in a cluster for HA.

The master node specifications decide the number of nodes that can be managed by a cluster. You can select the cluster management scale, for example, 50 or 200 nodes.

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
   | Hibernating | A cluster is hibernating.                                         |
   +-------------+-------------------------------------------------------------------+
   | Awaking     | A cluster is being woken up.                                      |
   +-------------+-------------------------------------------------------------------+
   | Upgrading   | A cluster is being upgraded.                                      |
   +-------------+-------------------------------------------------------------------+
   | Resizing    | The cluster flavor is being changed.                              |
   +-------------+-------------------------------------------------------------------+
   | Unavailable | A cluster is unavailable.                                         |
   +-------------+-------------------------------------------------------------------+
   | Deleting    | A cluster is being deleted.                                       |
   +-------------+-------------------------------------------------------------------+
