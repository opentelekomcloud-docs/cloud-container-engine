:original_name: cce_02_0327.html

.. _cce_02_0327:

Permissions Policies and Supported Actions
==========================================

This chapter describes fine-grained permissions management for your CCE. If your cloud account does not need individual IAM users, then you may skip over this chapter.

A policy is a set of permissions defined in JSON format. By default, new IAM users do not have any permissions assigned. You need to add a user to one or more groups, and assign permissions policies to these groups. The user then inherits permissions from the groups it is a member of. This process is called authorization. After authorization, the user can perform specified operations on specified cloud services based on the permissions.

There are fine-grained policies and role-based access control (RBAC) policies. An RBAC policy consists of permissions for an entire service. Users in a group with such a policy assigned are granted all of the permissions required for that service. A fine-grained policy consists of API-based permissions for operations on specific resource types. Fine-grained policies, as the name suggests, allow for more fine-grained control than RBAC policies.

.. note::

   -  Fine-grained policies are currently available for open beta testing. You can apply to the fine-grained access control function free of charge.
   -  If you want to allow or deny the access to an API, fine-grained authorization is a good choice.

An account has all of the permissions required to call all APIs, but IAM users must have the required permissions specifically assigned. The permissions required for calling an API are determined by the actions supported by the API. Only users who have been granted permissions can call the API successfully. For example, if an IAM user queries CCE clusters using an API, the user must have been granted permissions that allow the **cce:cluster:list** action.

Supported Actions
-----------------

Operations supported by a fine-grained policy are specific to APIs. The following describes the headers of the action tables provided in this chapter:

-  Permissions: Defined by actions in a custom policy.
-  Actions: Added to a custom policy to control permissions for specific operations.
-  Authorization Scope: A custom policy can be applied to IAM projects or enterprise projects or both. Policies that contain actions supporting both IAM and enterprise projects can be assigned to user groups and take effect in both IAM and Enterprise Management. Policies that only contain actions supporting IAM projects can be assigned to user groups and only take effect for IAM. Such policies will not take effect if they are assigned to user groups in Enterprise Management.
-  APIs: REST APIs that can be called in a custom policy.

.. table:: **Table 1** CCE actions

   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Permissions                                     | Actions            | Authorization Scope    | APIs                                                                       |
   +=================================================+====================+========================+============================================================================+
   | Listing clusters in a specified project         | cce:cluster:list   | Supported:             | GET /api/v3/projects/{project_id}/clusters                                 |
   |                                                 |                    |                        |                                                                            |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Obtaining information about a specified cluster | cce:cluster:get    | Supported:             | GET /api/v3/projects/{project_id}/clusters/{cluster_id}                    |
   |                                                 |                    |                        |                                                                            |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Creating a cluster                              | cce:cluster:create | Supported:             | POST /api/v3/projects/{project_id}/clusters                                |
   |                                                 |                    |                        |                                                                            |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Updating information about a specified cluster  | cce:cluster:update | Supported:             | PUT /api/v3/projects/{project_id}/clusters/{cluster_id}                    |
   |                                                 |                    |                        |                                                                            |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Deleting a cluster                              | cce:cluster:delete | Supported:             | DELETE /api/v3/projects/{project_id}/clusters/{cluster_id}                 |
   |                                                 |                    |                        |                                                                            |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Obtaining a cluster certificate                 | cce:cluster:get    | Supported:             | GET /api/v3/projects/{project_id}/clusters/{cluster_id}/clustercert        |
   |                                                 |                    |                        |                                                                            |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Listing all nodes in a cluster                  | cce:node:list      | Supported:             | GET /api/v3/projects/{project_id}/clusters/{cluster_id}/nodes              |
   |                                                 |                    |                        |                                                                            |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Obtaining information about a specified node    | cce:node:get       | Supported:             | GET /api/v3/projects/{project_id}/clusters/{cluster_id}/nodes/{node_id}    |
   |                                                 |                    |                        |                                                                            |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Creating a node                                 | cce:node:create    | Supported:             | POST /api/v3/projects/{project_id}/clusters/{cluster_id}/nodes             |
   |                                                 |                    |                        |                                                                            |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Updating information about a specified node     | cce:node:update    | Supported:             | PUT /api/v3/projects/{project_id}/clusters/{cluster_id}/nodes/{node_id}    |
   |                                                 |                    |                        |                                                                            |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Deleting a node                                 | cce:node:delete    | Supported:             | DELETE /api/v3/projects/{project_id}/clusters/{cluster_id}/nodes/{node_id} |
   |                                                 |                    |                        |                                                                            |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Obtaining job progress                          | cce:job:get        | Supported:             | GET /api/v3/projects/{project_id}/jobs/{job_id}                            |
   |                                                 |                    |                        |                                                                            |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Listing all node pools in a specified cluster   | cce:nodepool:list  | Supported:             | GET /api/v3/projects/{project_id}/clusters/{cluster_id}/nodepools          |
   |                                                 |                    |                        |                                                                            |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Creating a PersistentVolumeClaim                | cce:storage:create | Supported:             | POST /api/v1/namespaces/{namespace}/cloudpersistentvolumeclaims            |
   |                                                 |                    |                        |                                                                            |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Deleting a PersistentVolumeClaim                | cce:storage:delete | Supported:             | DELETE /api/v1/namespaces/{namespace}/cloudpersistentvolumeclaims/{name}   |
   |                                                 |                    |                        |                                                                            |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Creating a PersistentVolume                     | cce:storage:create | Supported:             | POST /api/v1/cloudpersistentvolumes                                        |
   |                                                 |                    |                        |                                                                            |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Deleting a PersistentVolume                     | cce:storage:delete | Supported:             | DELETE /api/v1/cloudpersistentvolumes/{name}                               |
   |                                                 |                    |                        |                                                                            |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
   | Operating on Kubernetes resources               | cce:kubernetes:\*  | Supported:             | -  /api/\*                                                                 |
   |                                                 |                    |                        | -  /apis/\*                                                                |
   |                                                 |                    | -  IAM projects        |                                                                            |
   |                                                 |                    | -  Enterprise projects |                                                                            |
   +-------------------------------------------------+--------------------+------------------------+----------------------------------------------------------------------------+
