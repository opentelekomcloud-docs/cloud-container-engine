:original_name: cce_10_0957.html

.. _cce_10_0957:

Using the AccessPolicy API to Manage Namespace Permissions (Kubernetes RBAC-based)
==================================================================================

Prerequisites
-------------

-  Before assigning permissions to users or user groups, you need to review the information provided in :ref:`Namespace Permissions (Kubernetes RBAC-based) <cce_10_0189>`.
-  To use an access policy, an IAM user or user group must have the specific permissions. These include CreateAccessPolicy, UpdateAccessPolicy, DeleteAccessPolicy, ListAccessPolicy, and GetAccessPolicy. Learn about the CCE system policies that can be added to a user group and select appropriate ones as needed. For details about how to assign permissions to a user group, see :ref:`Granting Cluster Permissions to an IAM User <cce_10_0188>`.

Configuring Namespace Permissions Through APIs
----------------------------------------------

You can regulate users' or user groups' access to Kubernetes resources in a single namespace based on their Kubernetes RBAC roles. Users with the configuration permission can configure access policies through APIs.

|image1|

#. Call the API to create an access policy.

   **POST /api/v3/access-policies**

   Request body:

   .. code-block::

      {
          "kind": "AccessPolicy",
          "apiVersion": "v3",
          "name": "policy-test",
          "clusters": [ "*" ],
          "accessScope": {
              "namespaces": [ "ns1", "ns2" ]
          },
          "policyType": "CCEAdminPolicy/CCEClusterAdminPolicy/CCEEditPolicy/CCEViewPolicy",
          "principal": {
              "type": "user/group/agency",
              "ids": ["id1", "id2" ]
          }
      }

   +-----------------------+-------------------------+----------------------------------------------------------------------------------------------------------+
   | Parameter             | Item                    | Description                                                                                              |
   +=======================+=========================+==========================================================================================================+
   | name                  | Name                    | Name of the access policy.                                                                               |
   +-----------------------+-------------------------+----------------------------------------------------------------------------------------------------------+
   | clusters              | Cluster list            | IDs of the clusters to which access policies are to be added. Wildcard characters (``*``) are supported. |
   +-----------------------+-------------------------+----------------------------------------------------------------------------------------------------------+
   | namespaces            | Namespaces              | Namespaces to which access policies are to be added. Wildcard characters (``*``) are supported.          |
   +-----------------------+-------------------------+----------------------------------------------------------------------------------------------------------+
   | policyType            | Permission type         | Permission type. Options:                                                                                |
   |                       |                         |                                                                                                          |
   |                       |                         | -  **CCEClusterAdminPolicy**: administrator permission                                                   |
   |                       |                         | -  **CCEAdminPolicy**: O&M permission                                                                    |
   |                       |                         | -  **CCEEditPolicy**: developer permission                                                               |
   |                       |                         | -  **CCEViewPolicy**: read-only permission                                                               |
   +-----------------------+-------------------------+----------------------------------------------------------------------------------------------------------+
   | principalType         | User or user group type | Type of a user or user group. Options:                                                                   |
   |                       |                         |                                                                                                          |
   |                       |                         | -  **user**: user                                                                                        |
   |                       |                         | -  **group**: user group                                                                                 |
   |                       |                         | -  **agency**: agency account                                                                            |
   +-----------------------+-------------------------+----------------------------------------------------------------------------------------------------------+
   | principalId           | User or user group list | IDs of the users or user groups to be authorized.                                                        |
   +-----------------------+-------------------------+----------------------------------------------------------------------------------------------------------+

#. The CCE management plane automatically synchronizes the access policies to the associated cluster. Run the **kubectl** command. Then, RoleBinding or ClusterRoleBinding will be created in the cluster.

   .. note::

      The synchronization will take several minutes to take effect.

   a. Check RoleBinding in the cluster.

      .. code-block::

         kubectl get rolebinding

      In the following command output, **clusterrole_access_policy_069fcc2116c347b89869eae3cd38ba23** is the automatically created RoleBinding:

      .. code-block::

         NAME                                                         ROLE                AGE
         clusterrole_access_policy_069fcc2116c347b89869eae3cd38ba23   ClusterRole/admin   18s

   b. Check RoleBinding details.

      .. code-block::

         kubectl get rolebinding clusterrole_access_policy_069fcc2116c347b89869eae3cd38ba23 -oyaml

      Command output:

      .. code-block::

         apiVersion: rbac.authorization.k8s.io/v1
         kind: RoleBinding
         metadata:
           annotations:
         CCE.com/IAM: "true"
           label:
         rbac.kubernetes.io/GeneratedByAccessPolicy: "true"
           creationTimestamp: "2021-06-24T01:30:08Z"
           name: clusterrole_admin_group0c96fad22880f32a3f84c009862af6f7
           resourceVersion: "36963685"
           selfLink: /apis/rbac.authorization.k8s.io/v1/namespaces/default/rolebindings/clusterrole_admin_group0c96fad22880f32a3f84c009862af6f7
           uid: 6c6f46a6-8584-47da-83f5-9eef1f7b75d6
         roleRef:
           apiGroup: rbac.authorization.k8s.io
           kind: ClusterRole
           name: admin
         subjects:
         - apiGroup: rbac.authorization.k8s.io
           kind: Group
           name: 0c96fad22880f32a3f84c009862af6f7

#. Log in to the CCE console as the newly authorized IAM user for verification.

   In the service list, choose **Cloud Container Engine**. On the CCE console, access the cluster and choose the target namespace. If only the **default** namespace is authorized and the **kube-system** namespace is unavailable, the access policy has taken effect.

.. |image1| image:: /_static/images/en-us_image_0000002218820614.png
