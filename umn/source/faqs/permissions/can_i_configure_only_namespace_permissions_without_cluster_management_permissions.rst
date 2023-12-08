:original_name: cce_faq_00398.html

.. _cce_faq_00398:

Can I Configure Only Namespace Permissions Without Cluster Management Permissions?
==================================================================================

Namespace permissions and cluster management permissions are independent and complementary to each other.

-  Namespace permissions: apply to clusters and are used to manage operations on cluster resources (such as creating workloads).
-  Cluster management (IAM) permissions: apply to cloud services and used to manage CCE clusters and peripheral resources (such as VPC, ELB, and ECS).

Administrators of the IAM Admin user group can grant cluster management permissions (such as CCE Administrator and CCE FullAccess) to IAM users or grant namespace permissions on a cluster on the CCE console. However, the permissions you have on the CCE console are determined by the IAM system policy. If the cluster management permissions are not configured, you do not have the permissions for accessing the CCE console.

If you only run kubectl commands to work on cluster resources, you only need to obtain the kubeconfig file with the namespace permissions. For details, see :ref:`Can I Use kubectl If the Cluster Management Permissions Are Not Configured? <cce_faq_00400>`. Note that information leakage may occur when you use the kubeconfig file.
