:original_name: cce_10_0430.html

.. _cce_10_0430:

Cluster Overview
================

Cloud Container Engine (CCE) is a `Kubernetes <https://kubernetes.io/>`__ cluster hosting service for enterprises. It manages the entire lifecycle of containerized applications and delivers scalable, high-performance solutions for deploying and managing cloud native applications.

Cluster Network
---------------

A cluster network consists of the following three networks:

-  Node network: assigns IP addresses to nodes in a cluster.
-  Container network: assigns IP addresses to containers in a cluster. Currently, multiple container network models are supported, and each model has its own operating principle.
-  Service network: assigns a fixed IP address to each Service used to access containers.

When you create a cluster, select an appropriate CIDR block for each network. Ensure that the CIDR blocks do not conflict with each other and have sufficient available IP addresses. **You cannot change the container network model after the cluster is created.** Select the right container network model in advance.

You are advised to learn about the cluster network and container network models before creating a cluster. For details, see :ref:`Container Networks <cce_10_0280>`.

Number of Master Nodes in a Cluster and Cluster Scale
-----------------------------------------------------

In a CCE cluster, the number of master nodes does not determine the cluster scale. They are two different aspects:

-  Master nodes: When creating a cluster on CCE, you have the option to choose one or three master nodes. If you choose three master nodes, the cluster will have high availability.

-  Cluster scale: refers to the maximum number of worker nodes that can be managed by a cluster. For example, you can choose a cluster management scale of 50 or 200 nodes when creating the cluster. The flavors of the master nodes are influenced by the cluster scale. Higher cluster scales require higher master node flavors.

   It is possible to change the cluster scale after creating the cluster. However, you can only increase the scale and cannot decrease it. For details, see :ref:`Changing a Cluster Scale <cce_10_0403>`.

.. _cce_10_0430__section2048514305592:

Cluster Lifecycle
-----------------

.. table:: **Table 1** Cluster statuses

   +-------------+----------------------------------------------------------------+
   | Status      | Description                                                    |
   +=============+================================================================+
   | Creating    | A cluster is being created and requesting for cloud resources. |
   +-------------+----------------------------------------------------------------+
   | Running     | A cluster is running properly.                                 |
   +-------------+----------------------------------------------------------------+
   | Hibernating | A cluster is hibernating.                                      |
   +-------------+----------------------------------------------------------------+
   | Awaking     | A cluster is being woken up.                                   |
   +-------------+----------------------------------------------------------------+
   | Upgrading   | A cluster is being upgraded.                                   |
   +-------------+----------------------------------------------------------------+
   | Resizing    | The cluster flavor is being changed.                           |
   +-------------+----------------------------------------------------------------+
   | Unavailable | A cluster is unavailable.                                      |
   +-------------+----------------------------------------------------------------+
   | Deleting    | A cluster is being deleted.                                    |
   +-------------+----------------------------------------------------------------+
