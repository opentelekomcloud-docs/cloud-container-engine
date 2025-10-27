:original_name: cce_10_0278.html

.. _cce_10_0278:

Creating a Namespace
====================

Scenario
--------

A namespace is a collection of resources and objects. Multiple namespaces can be created inside a cluster and isolated from each other. This enables namespaces to share the same cluster Services without affecting each other.

For example, you can deploy workloads in a development environment into one namespace, and deploy workloads in a testing environment into another namespace.

Prerequisites
-------------

At least one cluster has been created.

Notes and Constraints
---------------------

A maximum of 6000 Services can be created in each namespace. The Services mentioned here indicate the Kubernetes Service resources added for workloads.

Namespace Types
---------------

Namespaces can be categorized based on how they are created.

-  Created automatically: When a cluster is up, the **default**, **kube-public**, **kube-system**, and **kube-node-lease** namespaces are created by default.

   -  **default**: All objects for which no namespace is specified are allocated to this namespace.
   -  **kube-public**: Resources in this namespace can be accessed by all users (including unauthenticated users) so that some resources in the cluster can be readable in the entire cluster. This is a reserved Kubernetes namespace. Its common attributes are only conventions but not requirements.
   -  **kube-system**: All resources created by Kubernetes are in this namespace.
   -  **kube-node-lease**: Each node has an associated Lease object in this namespace. The object is periodically updated by the node. Both NodeStatus and NodeLease are considered as heartbeats from a node. In versions earlier than v1.13, only NodeStatus is available. The NodeLease feature is introduced in v1.13. NodeLease is more lightweight than NodeStatus. This feature significantly improves the cluster scalability and performance.

-  Created manually: You can create namespaces to serve separate purposes. For example, you can create three namespaces, one for a development environment, one for joint debugging environment, and one for test environment. You can also create one namespace for login services and one for game services.
-  Service mesh namespace: When ASM is used, namespaces named **istio-system**, **asm-system**, **istio-operator**, and **mantis-system** will be automatically created. These namespaces do not support quota management. Manually creating namespaces with these names will also affect these namespaces.

Creating a Namespace on the Console
-----------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **Namespaces** in the navigation pane and click **Create Namespace** in the upper right corner.

#. Set namespace parameters based on :ref:`Table 1 <cce_10_0278__table5523151617575>`.

   .. _cce_10_0278__table5523151617575:

   .. table:: **Table 1** Parameters for creating a namespace

      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                  |
      +===================================+==============================================================================================================================================================================================================================================================+
      | Name                              | Unique name of the created namespace.                                                                                                                                                                                                                        |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Description                       | Description about the namespace.                                                                                                                                                                                                                             |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Quota Management                  | Resource quotas can limit the number of resources available in namespaces, achieving resource allocation by namespace.                                                                                                                                       |
      |                                   |                                                                                                                                                                                                                                                              |
      |                                   | .. important::                                                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                              |
      |                                   |    NOTICE:                                                                                                                                                                                                                                                   |
      |                                   |    **Configure resource quotas for each namespace to prevent overloading the cluster or nodes with excessive resources.**                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                              |
      |                                   |    For example, if your cluster resources are limited but you need to use multiple namespaces to isolate resources of different services, you can configure resource quotas for each namespace to maintain cluster stability and proper resource allocation. |
      |                                   |                                                                                                                                                                                                                                                              |
      |                                   | Enter a value. If a resource quota is not specified, there will be no limit imposed on the resource.                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                                                                              |
      |                                   | If you want to limit the CPU or memory quota, you must specify the CPU or memory request value when creating a workload.                                                                                                                                     |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. After the configuration is complete, click **OK**.

Creating a Namespace Using kubectl
----------------------------------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a namespace.

   -  Method 1: Use a YAML file to configure the namespace and run the **kubectl apply** command to create it.

      a. Create a **custom-namespace.yaml** file with the following data:

         .. code-block::

            apiVersion: v1
            kind: Namespace
            metadata:
              name: custom-namespace

      b. Run the following command to create the namespace:

         .. code-block::

            kubectl apply -f custom-namespace.yaml

         Command output:

         .. code-block::

            namespace/custom-namespace created

   -  Method 2: Run the **kubectl create namespace** command to create a namespace.

      .. code-block::

         kubectl create namespace custom-namespace

#. Run the following command to verify the creation:

   .. code-block::

      kubectl get namespaces

   The **custom-namespace** namespace is displayed in the list.

   For more details about how to configure a namespace, see `Manage Memory, CPU, and API Resources <https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/>`__.
