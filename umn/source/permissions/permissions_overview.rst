:original_name: cce_10_0187.html

.. _cce_10_0187:

Permissions Overview
====================

CCE permissions management allows you to assign permissions to IAM users and user groups under your tenant accounts. CCE combines the advantages of Identity and Access Management (IAM) and Kubernetes Role-based Access Control (RBAC) authorization to provide a variety of authorization methods, including IAM fine-grained authorization, IAM token authorization, cluster-scoped authorization, and namespace-wide authorization.

CCE allows you to manage permissions on clusters and related resources at a finer granularity, for example, to control the access of employees in different departments to cloud resources.

This section describes the CCE permissions management mechanism and related concepts. If your account has met your service requirements, you can skip this section.

CCE Permissions Management
--------------------------

CCE permissions are described as follows:

-  **Cluster permissions**: Cluster permissions management evolves out of the system policy authorization feature of IAM. IAM users in the same user group have the same permissions. On IAM, you can configure system policies to describe which IAM user groups can perform which operations on cluster resources. For example, you can grant user group A to create and delete cluster X, add a node, or install an add-on, while granting user group B to view information about cluster X.

   Cluster permissions involve non-Kubernetes APIs in CCE clusters and support fine-grained IAM policies.

-  **Namespace permissions**: You can regulate users' or user groups' access to Kubernetes resources in a single namespace based on their Kubernetes RBAC roles. CCE has also been enhanced based on open-source capabilities. It supports RBAC authorization based on IAM user or user group, and RBAC authentication on access to APIs using IAM tokens.

   Namespace permissions involve CCE Kubernetes APIs and are enhanced based on the Kubernetes RBAC capabilities. Namespace permissions can be granted to IAM users or user groups for authentication and authorization, but are independent of fine-grained IAM policies.

In general, you configure CCE permissions in two scenarios. The first is creating and managing clusters and related resources, such as nodes. The second is creating and using Kubernetes resources in the cluster, such as workloads and Services.


.. figure:: /_static/images/en-us_image_0000002631690654.png
   :alt: **Figure 1** Permission system

   **Figure 1** Permission system

These permissions allow you to manage resource users at a finer granularity.

.. _cce_10_0187__section1464135853519:

Relationship Between IAM Role/Policy Permissions and Namespace Permissions
--------------------------------------------------------------------------

Namespace permissions (assigned through Kubernetes RBAC) vary based on IAM role and policy assignments. :ref:`Table 1 <cce_10_0187__table886210176509>` lists the namespace permissions of different users.

.. _cce_10_0187__table886210176509:

.. table:: **Table 1** Differences in namespace permissions

   +-------------------------------------------------------------+------------------------------------------------------------------------------------------------+
   | User                                                        | Namespace Permissions                                                                          |
   +=============================================================+================================================================================================+
   | IAM user with the Tenant Administrator permissions          | Full namespace access by default. These users can use all Kubernetes resources in the cluster. |
   +-------------------------------------------------------------+------------------------------------------------------------------------------------------------+
   | IAM user with the CCE Administrator role                    | Full namespace access by default. These users can use all Kubernetes resources in the cluster. |
   +-------------------------------------------------------------+------------------------------------------------------------------------------------------------+
   | IAM user with the CCE FullAccess or CCE ReadOnlyAccess role | Kubernetes resources can be accessed only after Kubernetes RBAC authorization.                 |
   +-------------------------------------------------------------+------------------------------------------------------------------------------------------------+

.. note::

   If both storage-related fine-grained permissions and the CCE/tenant administrator role are assigned to a user group, the fine-grained storage permissions are ignored. Administrator role permissions take precedence.

kubectl Permissions
-------------------

You can use :ref:`kubectl <cce_10_0107>` to access Kubernetes resources in a cluster.

When you access a cluster using kubectl, CCE uses the **kubeconfig.json** file generated on the cluster for authentication. This file contains user information, based on which CCE determines which Kubernetes resources can be accessed via kubectl. The permissions recorded in a kubeconfig.json file vary from user to user. The permissions that a user has are listed in :ref:`Table 1 <cce_10_0187__table886210176509>`.
