:original_name: cce_10_0188.html

.. _cce_10_0188:

Granting Cluster Permissions to an IAM User
===========================================

CCE cluster-level permissions are assigned based on **IAM system policies** and **custom policies**. You can use user groups to assign permissions to IAM users.

.. caution::

   -  Cluster permissions are granted to users for operating cluster-related resources only (such as clusters and nodes). To operate Kubernetes resources like workloads and Services, you must be granted the :ref:`namespace permissions <cce_10_0189>` at the same time.
   -  When viewing a cluster on the CCE console, the information displayed depends on the namespace permissions. If you have no namespace permissions, you cannot view the resources in the cluster. For details, see :ref:`Permission Dependency of the CCE Console <cce_10_0190>`.

Prerequisites
-------------

-  A user with the Security Administrator role (for example, your account) has all IAM permissions except role switching. Only these users can view user groups and their permissions on the **Permissions** page on the CCE console.

Configuration
-------------

On the CCE console, when you choose **Permissions** > **Cluster-Level Permissions** to create a user group, you will be directed to the IAM console to complete the process. After the user group is created and its permissions are configured, you can view the information on the **Cluster-Level Permissions** tab page. This section describes the operations in IAM.

Process Flow
------------


.. figure:: /_static/images/en-us_image_0000001981276821.png
   :alt: **Figure 1** Process of assigning CCE permissions

   **Figure 1** Process of assigning CCE permissions

#. .. _cce_10_0188__li10176121316284:

   Create a user group and assign permissions to it.

   Create a user group on the IAM console, and assign CCE permissions, for example, the **CCE ReadOnlyAccess** policy to the group.

   .. note::

      CCE is deployed by region. On the IAM console, select **Region-specific projects** when assigning CCE permissions.

#. Create a user and add it to a user group.

   Create a user on the IAM console and add the user to the group created in :ref:`1 <cce_10_0188__li10176121316284>`.

#. Log in and verify permissions.

   Log in to the management console as the user you created, and verify that the user has the assigned permissions.

   -  Log in to the management console, switch to the CCE console, and create a cluster. If you fail to do so (assuming that only the **CCE ReadOnlyAccess** permission is assigned), the **CCE ReadOnlyAccess** policy has already taken effect.
   -  Switch to the console of any other service. If a message appears indicating that you do not have the required permissions for accessing the service, the **CCE ReadOnlyAccess** policy has already taken effect.

System-defined Roles
--------------------

Roles are a type of coarse-grained authorization mechanism that defines service-level permissions based on user responsibilities. Only a limited number of service-level roles are available for authorization. Roles are not ideal for fine-grained authorization and least privilege access.

The preset system role for CCE in IAM is **CCE Administrator**. When assigning this role to a user group, you must also select other roles and policies on which this role depends, such as **Tenant Guest**, **Server Administrator**, **ELB Administrator**, **OBS Administrator**, **SFS Administrator**, **SWR Admin**, and **APM FullAccess**.

System-defined Policies
-----------------------

The system policies preset for CCE in IAM are **CCE FullAccess** and **CCE ReadOnlyAccess**.

-  **CCE FullAccess**: common operation permissions on CCE cluster resources, excluding the namespace-level permissions for the clusters (with Kubernetes RBAC enabled) and the privileged administrator operations, such as agency configuration and cluster certificate generation
-  **CCE ReadOnlyAccess**: permissions to view CCE cluster resources, excluding the namespace-level permissions of the clusters (with Kubernetes RBAC enabled)

.. note::

   The **CCE Admin** and **CCE Viewer** roles will be discarded soon. You are advised to use **CCE FullAccess** and **CCE ReadOnlyAccess**.

Custom Policies
---------------

Custom policies can be created as a supplement to the system-defined policies of CCE.

You can create custom policies in either of the following ways:

-  Visual editor: Select cloud services, actions, resources, and request conditions. This does not require knowledge of policy syntax.
-  JSON: Edit JSON policies from scratch or based on an existing policy.

This section provides examples of common custom CCE policies.

**Example Custom Policies:**

-  Example 1: Creating a cluster named **test**

   .. code-block::

      {
          "Version": "1.1",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Action": [
                      "cce:cluster:create"
                  ]
              }
          ]
      }

-  Example 2: Denying node deletion

   A policy with only "Deny" permissions must be used with other policies. If the permissions assigned to a user contain both "Allow" and "Deny", the "Deny" permissions take precedence over the "Allow" permissions.

   The following method can be used if you need to assign permissions of the **CCEFullAccess** policy to a user but you want to prevent the user from deleting nodes (**cce:node:delete**). Create a custom policy for denying node deletion, and attach both policies to the group to which the user belongs. Then, the user can perform all operations on CCE except deleting nodes. The following is an example of a deny policy:

   .. code-block::

      {
          "Version": "1.1",
          "Statement": [
              {
                  "Effect": "Deny",
                  "Action": [
                      "cce:node:delete"
                  ]
              }
          ]
      }

-  Example 3: Defining permissions for multiple services in a policy

   A custom policy can contain the actions of multiple services that are of the global or project-level type. The following is an example policy containing actions of multiple services:

   .. code-block::

      {
          "Version": "1.1",
          "Statement": [
              {
                  "Action": [
                      "ecs:cloudServers:resize",
                      "ecs:cloudServers:delete",
                      "ecs:cloudServers:delete",
                      "ims:images:list",
                      "ims:serverImages:create"
                  ],
                  "Effect": "Allow"
              }
          ]
      }

CCE Cluster Permissions and IAM RBAC
------------------------------------

CCE is compatible with IAM system roles for permissions management. Use fine-grained policies provided by IAM to simplify permissions management.

CCE supports the following roles:

-  Basic IAM roles:

   -  te_admin (Tenant Administrator): Users with this role can call all APIs of all services except IAM.
   -  readonly (Tenant Guest): Users with this role can call APIs with the read-only permissions of all services except IAM.

-  Custom CCE administrator role: CCE Administrator

.. note::

   If a user has the Tenant Administrator or CCE Administrator system role, the user has the cluster-admin permissions in Kubernetes RBAC and the permissions cannot be removed after the cluster is created.

   If the user is the cluster creator, the cluster-admin permissions in Kubernetes RBAC are granted to the user by default. The permissions can be manually removed after the cluster is created.

   -  Method 1: Choose **Permissions Management** > **Namespace-Level Permissions** > **Delete** in the same role as cluster-creator on the CCE console.
   -  Method 2: Delete **ClusterRoleBinding: cluster-creator** through the API or kubectl.

When RBAC and IAM policies co-exist, the backend authentication logic for open APIs or console operations on CCE is as follows.

|image1|

.. |image1| image:: /_static/images/en-us_image_0000001950317280.png
