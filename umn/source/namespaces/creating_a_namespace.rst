:original_name: cce_01_0278.html

.. _cce_01_0278:

Creating a Namespace
====================

When to Use Namespaces
----------------------

A namespace is a collection of resources and objects. Multiple namespaces can be created inside a cluster and isolated from each other. This enables namespaces to share the same cluster Services without affecting each other.

For example, you can deploy workloads in a development environment into one namespace, and deploy workloads in a testing environment into another namespace.

Prerequisites
-------------

At least one cluster has been created. For details, see :ref:`Creating a CCE Cluster <cce_01_0028>`.

Notes and Constraints
---------------------

A maximum of 6,000 Services can be created in each namespace. The Services mentioned here indicate the Kubernetes Service resources added for workloads.

Namespace Types
---------------

Namespaces can be created in either of the following ways:

-  Created automatically: When a cluster is up, the **default**, **kube-public**, **kube-system**, and **kube-node-lease** namespaces are created by default.

   -  **default**: All objects for which no namespace is specified are allocated to this namespace.
   -  **kube-public**: Resources in this namespace can be accessed by all users (including unauthenticated users), such as public add-ons and container charts.
   -  **kube-system**: All resources created by Kubernetes are in this namespace.
   -  **kube-node-lease**: Each node has an associated Lease object in this namespace. The object is periodically updated by the node. Both NodeStatus and NodeLease are considered as heartbeats from a node. In versions earlier than v1.13, only NodeStatus is available. The NodeLease feature is introduced in v1.13. NodeLease is more lightweight than NodeStatus. This feature significantly improves the cluster scalability and performance.

-  Created manually: You can create namespaces to serve separate purposes. For example, you can create three namespaces, one for a development environment, one for joint debugging environment, and one for test environment. You can also create one namespace for login services and one for game services.


Creating a Namespace
--------------------

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Namespaces**. Click **Create Namespace**.

#. Set the parameters listed in :ref:`Table 1 <cce_01_0278__table5523151617575>`. The parameters marked with an asterisk (*) are mandatory.

   .. _cce_01_0278__table5523151617575:

   .. table:: **Table 1** Parameters for creating a namespace

      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                              |
      +===================================+==========================================================================================================================================================================================================================================================================================================+
      | \* Namespace                      | Unique name of the created namespace.                                                                                                                                                                                                                                                                    |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Cluster                        | Cluster to which the namespace belongs.                                                                                                                                                                                                                                                                  |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Node Affinity                     | If this parameter is set to on, workloads in this namespace will be scheduled only to nodes with specified labels. To add labels to a node, choose **Resource Management** > **Nodes** > **Manage Labels**.                                                                                              |
      |                                   |                                                                                                                                                                                                                                                                                                          |
      |                                   | This parameter is displayed only for clusters of v1.13.10-r0 and later.                                                                                                                                                                                                                                  |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Description                       | Description about the namespace.                                                                                                                                                                                                                                                                         |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Set Resource Quotas               | Resource quotas can limit the amount of resources available in namespaces, achieving resource allocation by namespace.                                                                                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                                          |
      |                                   | .. important::                                                                                                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                                                          |
      |                                   |    NOTICE:                                                                                                                                                                                                                                                                                               |
      |                                   |    **You are advised to set resource quotas in the namespace as required to prevent cluster or node exceptions caused by resource overload.**                                                                                                                                                            |
      |                                   |                                                                                                                                                                                                                                                                                                          |
      |                                   |    For example, the default number of pods that can be created on each node in a cluster is 110. If you create a cluster with 50 nodes, you can create a maximum of 5,500 pods. Therefore, you can set a resource quota to ensure that the total number of pods in all namespaces does not exceed 5,500. |
      |                                   |                                                                                                                                                                                                                                                                                                          |
      |                                   | Quotas can be configured for the following resources:                                                                                                                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                                                          |
      |                                   | -  CPU (cores)                                                                                                                                                                                                                                                                                           |
      |                                   | -  Memory (MiB)                                                                                                                                                                                                                                                                                          |
      |                                   | -  StatefulSet                                                                                                                                                                                                                                                                                           |
      |                                   | -  Deployment                                                                                                                                                                                                                                                                                            |
      |                                   | -  Job                                                                                                                                                                                                                                                                                                   |
      |                                   | -  Cron job                                                                                                                                                                                                                                                                                              |
      |                                   | -  Pod                                                                                                                                                                                                                                                                                                   |
      |                                   | -  Service                                                                                                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                                                          |
      |                                   | Enter an integer. If the quota of a resource is set to **0**, no limit is posed on the resource.                                                                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                                                                                                                          |
      |                                   | If you want to limit the CPU or memory quota, you must specify the CPU or memory request value when creating a workload.                                                                                                                                                                                 |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. When the configuration is complete, click **OK**.
