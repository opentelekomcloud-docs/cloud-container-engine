:original_name: cce_01_0188.html

.. _cce_01_0188:

Cluster Permissions (IAM-based)
===============================

CCE cluster permissions are assigned based on IAM **system policies** and **custom policies**. You can use user groups to assign permissions to IAM users.

.. caution::

   **Cluster permissions** are configured only for cluster-related resources (such as clusters and nodes). You must also configure :ref:`namespace permissions <cce_01_0189>` to operate Kubernetes resources (such as workloads and Services).

Prerequisites
-------------

-  A user with the Security Administrator role has all IAM permissions except role switching. Only these users can view user groups and their permissions on the **Permissions Management** page on the CCE console.

Configuration
-------------

On the CCE console, when you choose **Permissions Management** > **Cluster-Level Permissions** to create a user group, you will be directed to the IAM console to complete the process. After the user group is created and its permissions are configured, you can view the information on the **Cluster-Level Permissions** tab page. This section describes the operations in IAM.

Process Flow
------------


.. figure:: /_static/images/en-us_image_0000001120226646.png
   :alt: **Figure 1** Process of assigning CCE permissions

   **Figure 1** Process of assigning CCE permissions

#. .. _cce_01_0188__li10176121316284:

   Create a user group and assign permissions to it.

   Create a user group on the IAM console, and assign CCE permissions, for example, the CCE Viewer policy to the group.

   .. note::

      CCE is deployed by region. On the IAM console, select **Region-specific projects** when assigning CCE permissions.

#. Create a user and add it to a user group.

   Create a user on the IAM console and add the user to the group created in :ref:`1 <cce_01_0188__li10176121316284>`.

#. Log in and verify permissions.

   Log in to the management console as the user you created, and verify that the user has the assigned permissions.

   -  Log in to the management console and switch to the CCE console. Click **Create** **Cluster** in the upper right corner. If you fail to do so (assuming that only the CCE Viewer role is assigned), the permission control policy takes effect.
   -  Switch to the console of any other service. If a message appears indicating that you do not have the required permissions to access the service, the CCE Viewer policy takes effect.

.. _cce_01_0188__section1437818291149:

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

   A policy with only "Deny" permissions must be used in conjunction with other policies to take effect. If the permissions assigned to a user contain both "Allow" and "Deny", the "Deny" permissions take precedence over the "Allow" permissions.

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

CCE Cluster Permissions and Enterprise Projects
-----------------------------------------------

CCE supports resource management and permission allocation by cluster and enterprise project.

Note that:

-  IAM projects are based on physical isolation of resources, whereas enterprise projects provide global logical groups of resources, which better meet the actual requirements of enterprises. In addition, IAM policies can be managed based on enterprise projects. Therefore, you are advised to use enterprise projects for permissions management.
-  When there are both IAM projects and enterprise projects, IAM preferentially matches the IAM project policies.
-  When creating a cluster or node using purchased cloud resources, ensure that IAM users have been granted the required permissions in the enterprise project to use these resources. Otherwise, the cluster or node may fail to be created.

CCE Cluster Permissions and IAM RBAC
------------------------------------

CCE is compatible with IAM system roles for permissions management. You are advised to use fine-grained policies provided by IAM to simplify permissions management.

CCE supports the following roles:

-  Basic IAM roles:

   -  te_admin (Tenant Administrator): Users with this role can call all APIs of all services except IAM.
   -  readonly (Tenant Guest): Users with this role can call APIs with the read-only permissions of all services except IAM.

-  Custom CCE administrator role: CCE Administrator

.. note::

   -  Tenant Administrator and Tenant Guest are special IAM system roles. After any system or custom policy is configured, Tenant Administrator and Tenant Guest take effect as system policies to achieve compatibility with IAM RBAC and ABAC scenarios.

   -  If a user has the Tenant Administrator or CCE Administrator system role, the user has the cluster-admin permissions in Kubernetes RBAC and the permissions cannot be removed after the cluster is created.

      If the user is the cluster creator, the cluster-admin permissions in Kubernetes RBAC are granted to the user by default. The permissions can be manually removed after the cluster is created.

      -  Method 1: Choose **Permissions Management** > **Namespace-Level Permissions** > **Delete** at the same role as cluster-creator on the CCE console.
      -  Method 2: Delete **ClusterRoleBinding: cluster-creator** through the API or kubectl.

When RBAC and IAM policies co-exist, the backend authentication logic for open APIs or console operations on CCE is as follows:

|image1|

.. caution::

   Certain CCE APIs involve namespace-level permissions or key operations and therefore, they require special permissions:

   Using clusterCert to obtain the cluster kubeconfig: cceadm/teadmin

.. |image1| image:: /_static/images/en-us_image_0000001086743939.png
