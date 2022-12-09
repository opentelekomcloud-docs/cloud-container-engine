:original_name: cce_01_0187.html

.. _cce_01_0187:

Permissions Overview
====================

CCE permissions management allows you to assign permissions to IAM users and user groups under your tenant accounts. CCE combines the advantages of Identity and Access Management (IAM) and Kubernetes Role-based Access Control (RBAC) authorization to provide a variety of authorization methods, including IAM fine-grained authorization, IAM token authorization, cluster-scoped authorization, and namespace-wide authorization.

If you need to perform refined permissions management on CCE clusters and related resources, for example, to control the access of employees in different departments to cloud resources, you can perform multi-dimensional permissions management on CCE.

This section describes the CCE permissions management mechanism and related concepts. If your account has met your service requirements, you can skip the configurations in this chapter.

CCE Permissions Management
--------------------------

CCE permissions are described as follows:

-  :ref:`Cluster-level permissions <cce_01_0188>`: Cluster-level permissions management evolves out of the system policy authorization feature of IAM. IAM users in the same user group have the same permissions. On IAM, you can configure system policies to describe which IAM user groups can perform which operations on cluster resources. For example, you can grant user group A to create and delete cluster X, add a node, or install an add-on, while granting user group B to view information about cluster X.

   Cluster-level permissions involve CCE non-Kubernetes APIs and support fine-grained IAM policies and enterprise project management capabilities.

-  :ref:`Namespace-level permissions <cce_01_0189>`: You can regulate users' or user groups' access to Kubernetes resources in a single namespace based on their Kubernetes RBAC roles. CCE has also been enhanced based on open-source capabilities. It supports RBAC authorization based on IAM user or user group, and RBAC authentication on access to APIs using IAM tokens.

   Namespace-level permissions involve CCE Kubernetes APIs and are enhanced based on the Kubernetes RBAC capabilities. Namespace-level permissions can be granted to IAM users or user groups for authentication and authorization, but are independent of fine-grained IAM policies.

   Starting from version 1.11.7-r2, CCE clusters allow you to configure namespace permissions. Clusters earlier than v1.11.7-r2 have all namespace permissions by default.

In general, you configure CCE permissions in two scenarios. The first is creating and managing clusters and related resources, such as nodes. The second is creating and using Kubernetes resources in the cluster, such as workloads and Services.


.. figure:: /_static/images/en-us_image_0000001168537057.png
   :alt: **Figure 1** Illustration on CCE permissions

   **Figure 1** Illustration on CCE permissions

These permissions allow you to manage resource users at a finer granularity.

.. _cce_01_0187__section1464135853519:

Cluster Permissions (IAM-based) and Namespace Permissions (Kubernetes RBAC-based)
---------------------------------------------------------------------------------

Users with different cluster permissions (assigned using IAM) have different namespace permissions (assigned using Kubernetes RBAC). :ref:`Table 1 <cce_01_0187__table886210176509>` lists the namespace permissions of different users.

.. _cce_01_0187__table886210176509:

.. table:: **Table 1** Differences in namespace permissions

   +------------------------------------------------+----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------+
   | User                                           | Clusters Earlier Than v1.11.7-r2 | Clusters of v1.11.7-r2                                                                                                            |
   +================================================+==================================+===================================================================================================================================+
   | User with the Tenant Administrator permissions | All namespace permissions        | -  Has all namespace permissions when using CCE on the console.                                                                   |
   |                                                |                                  | -  Requires Kubernetes RBAC authorization when using CCE via :ref:`kubectl <cce_01_0140>`.                                        |
   |                                                |                                  |                                                                                                                                   |
   |                                                |                                  | .. note::                                                                                                                         |
   |                                                |                                  |                                                                                                                                   |
   |                                                |                                  |    When such a user accesses the CCE console, an administrator group is added. Therefore, the user has all namespace permissions. |
   +------------------------------------------------+----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------+
   | IAM user with the CCE Administrator role       | All namespace permissions        | -  Has all namespace permissions when using CCE on the console.                                                                   |
   |                                                |                                  | -  Requires Kubernetes RBAC authorization when using CCE via :ref:`kubectl <cce_01_0140>`.                                        |
   |                                                |                                  |                                                                                                                                   |
   |                                                |                                  | .. note::                                                                                                                         |
   |                                                |                                  |                                                                                                                                   |
   |                                                |                                  |    When such a user accesses the CCE console, an administrator group is added. Therefore, the user has all namespace permissions. |
   +------------------------------------------------+----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------+
   | IAM user with the CCE Viewer role              | All namespace permissions        | Requires Kubernetes RBAC authorization.                                                                                           |
   +------------------------------------------------+----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------+
   | IAM user with the Tenant Guest role            | All namespace permissions        | Requires Kubernetes RBAC authorization.                                                                                           |
   +------------------------------------------------+----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------+

kubectl Permissions
-------------------

You can use :ref:`kubectl <cce_01_0107>` to access Kubernetes resources in a cluster.

When you access a cluster using kubectl, CCE uses the kubeconfig.json file generated on the cluster for authentication. This file contains user information, based on which CCE determines which Kubernetes resources can be accessed by kubectl. The permissions recorded in a kubeconfig.json file vary from user to user. The permissions that a user has are listed in :ref:`Table 1 <cce_01_0187__table886210176509>`.
