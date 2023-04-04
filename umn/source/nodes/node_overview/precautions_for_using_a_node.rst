:original_name: cce_10_0461.html

.. _cce_10_0461:

Precautions for Using a Node
============================

Introduction
------------

A container cluster consists of a set of worker machines, called nodes, that run containerized applications. A node can be a virtual machine (VM) or a physical machine (PM), depending on your service requirements. The components on a node include kubelet, container runtime, and kube-proxy.

.. note::

   A Kubernetes cluster consists of master nodes and worker nodes. The nodes described in this section refer to **worker nodes**, the computing nodes of a cluster that run containerized applications.

CCE uses high-performance Elastic Cloud Servers (ECSs) as nodes to build highly available Kubernetes clusters.

.. _cce_10_0461__section1667513391595:

Supported Node Specifications
-----------------------------

Different regions support different node flavors, and node flavors may be changed or sold out. You are advised to log in to the CCE console and check whether the required node flavors are supported on the page for creating nodes.

Underlying File Storage System of Docker
----------------------------------------

-  In clusters of v1.15.6 or earlier, the underlying file storage system uses the XFS format.
-  In clusters of v1.15.11 or later, after a node is created or reset, the underlying file storage system uses the ext4 format.

For containerized applications that use the XFS format, pay attention to the impact of the underlying file storage format change. (The sequence of files in different file systems is different. For example, some Java applications reference a JAR package, but the directory contains multiple versions of the JAR package. If the version is not specified, the actual referenced package is determined by the system file.)

Run the **docker info \| grep "Backing Filesystem"** command to check the format of the Docker underlying storage file used by the current node.

Paas User and User Group
------------------------

When you create a node in a CCE cluster, a paas user or user group is created on the node by default. CCE components and CCE add-ons on a node run as a non-root user (paas user/user group) to minimize the running permission. If the paas user or user group is modified, CCE components and pods may fail to run properly.

.. important::

   The normal running of CCE components depends on the paas user or user group. Pay attention to the following requirements:

   -  Do not modify the directory permission and container directory permission on a node.
   -  Do not change the GID and UID of the paas user or user group.
   -  Do not directly use the paas user or user group to set the user and group to which the service file belongs.

Node Lifecycle
--------------

A lifecycle indicates the node statuses recorded from the time when the node is created through the time when the node is deleted or released.

.. table:: **Table 1** Node statuses

   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------+
   | Status                | Status Attribute      | Description                                                                                                                        |
   +=======================+=======================+====================================================================================================================================+
   | Running               | Stable state          | The node is running properly and is connected to the cluster.                                                                      |
   |                       |                       |                                                                                                                                    |
   |                       |                       | Nodes in this state can provide services.                                                                                          |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------+
   | Unavailable           | Stable state          | The node is not running properly.                                                                                                  |
   |                       |                       |                                                                                                                                    |
   |                       |                       | Instances in this state no longer provide services. In this case, perform the operations in :ref:`Resetting a Node <cce_10_0003>`. |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------+
   | Creating              | Intermediate state    | The node has been created but is not running.                                                                                      |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------+
   | Installing            | Intermediate state    | The Kubernetes software is being installed on the node.                                                                            |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------+
   | Deleting              | Intermediate state    | The node is being deleted.                                                                                                         |
   |                       |                       |                                                                                                                                    |
   |                       |                       | If this state stays for a long time, an exception occurs.                                                                          |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------+
   | Stopped               | Stable state          | The node is stopped properly.                                                                                                      |
   |                       |                       |                                                                                                                                    |
   |                       |                       | A node in this state cannot provide services. You can start the node on the ECS console.                                           |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------+
   | Error                 | Stable state          | The node is abnormal.                                                                                                              |
   |                       |                       |                                                                                                                                    |
   |                       |                       | Instances in this state no longer provide services. In this case, perform the operations in :ref:`Resetting a Node <cce_10_0003>`. |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------+
