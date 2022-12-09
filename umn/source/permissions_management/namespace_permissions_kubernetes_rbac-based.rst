:original_name: cce_01_0189.html

.. _cce_01_0189:

Namespace Permissions (Kubernetes RBAC-based)
=============================================


Namespace Permissions (Kubernetes RBAC-based)
---------------------------------------------

You can regulate users' or user groups' access to Kubernetes resources in a single namespace based on their Kubernetes RBAC roles. The RBAC API declares four kinds of Kubernetes objects: Role, ClusterRole, RoleBinding, and ClusterRoleBinding, which are described as follows:

-  Role: defines a set of rules for accessing Kubernetes resources in a namespace.
-  RoleBinding: defines the relationship between users and roles.
-  ClusterRole: defines a set of rules for accessing Kubernetes resources in a cluster (including all namespaces).
-  ClusterRoleBinding: defines the relationship between users and cluster roles.

Role and ClusterRole specify actions that can be performed on specific resources. RoleBinding and ClusterRoleBinding bind roles to specific users, user groups, or ServiceAccounts. Illustration:


.. figure:: /_static/images/en-us_image_0000001142984374.png
   :alt: **Figure 1** Role binding

   **Figure 1** Role binding

On the CCE console, you can assign permissions to a user or user group to access resources in one or multiple namespaces. By default, the CCE console provides the following ClusterRoles:

-  view: read-only permission on most resources in all or selected namespaces.
-  edit: read and write permissions on most resources in all or selected namespaces. If this ClusterRole is configured for all namespaces, its capability is the same as the O&M permission.
-  admin: read and write permissions on most resources in all namespaces, and read-only permission on nodes, storage volumes, namespaces, and quota management.
-  cluster-admin: read and write permissions on all resources in all namespaces.

.. _cce_01_0189__section207514572488:

Cluster Permissions (IAM-based) and Namespace Permissions (Kubernetes RBAC-based)
---------------------------------------------------------------------------------

Users with different cluster permissions (assigned using IAM) have different namespace permissions (assigned using Kubernetes RBAC). :ref:`Table 1 <cce_01_0189__cce_01_0187_table886210176509>` lists the namespace permissions of different users.

.. _cce_01_0189__cce_01_0187_table886210176509:

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

Prerequisites
-------------

-  Kubernetes RBAC authorization can be used for clusters of v1.11.7-r2 and later. Ensure that you have deployed a supported cluster version. For details about upgrading a cluster, see :ref:`Performing Replace/Rolling Upgrade (v1.13 and Earlier) <cce_01_0120>`.
-  After you create a cluster of v1.11.7-r2 or later, CCE automatically assigns the cluster-admin permission to you, which means you have full control on all resources in all namespaces in the cluster.
-  A user with the Security Administrator role has all IAM permissions except role switching. Only these users can assign permissions on the **Permissions Management** page on the CCE console.

Configuring Namespace Permissions (on the Console)
--------------------------------------------------

You can regulate users' or user groups' access to Kubernetes resources in a single namespace based on their Kubernetes RBAC roles.

#. Log in to the CCE console. In the navigation pane, choose **Permissions Management**.
#. On the displayed page, click the **Namespace-Level Permissions** tab. In the upper right corner of the namespace permissions list, select the cluster that contains the namespace whose access will be managed, and click **Add Permissions**.
#. Confirm the cluster name and select the namespace to assign permissions for. For example, select **All namespaces**, the target user or user group, and select the permissions.
#. Click **Create**.

.. _cce_01_0189__section1273861718819:

Using kubectl to Configure Namespace Permissions
------------------------------------------------

.. note::

   When you access a cluster using kubectl, CCE uses the kubeconfig.json file generated on the cluster for authentication. This file contains user information, based on which CCE determines which Kubernetes resources can be accessed by kubectl. The permissions recorded in a kubeconfig.json file vary from user to user. The permissions that a user has are listed in :ref:`Cluster Permissions (IAM-based) and Namespace Permissions (Kubernetes RBAC-based) <cce_01_0189__section207514572488>`.

In addition to cluster-admin, admin, edit, and view, you can define Roles and RoleBindings to configure the permissions to add, delete, modify, and query resources, such as pods, Deployments, and Services, in the namespace.

The procedure for creating a Role is very simple. To be specific, specify a namespace and then define rules. The rules in the following example are to allow GET and LIST operations on pods in the default namespace.

.. code-block::

   kind: Role
   apiVersion: rbac.authorization.k8s.io/v1
   metadata:
     namespace: default                          # Namespace
     name: role-example
   rules:
   - apiGroups: [""]
     resources: ["pods"]                         # The pod can be accessed.
     verbs: ["get", "list"]                      # The GET and LIST operations can be performed.

-  **apiGroups** indicates the API group to which the resource belongs.
-  **resources** indicates the resources that can be operated. Pods, Deployments, ConfigMaps, and other Kubernetes resources are supported.
-  **verbs** indicates the operations that can be performed. **get** indicates querying a specific object, and **list** indicates listing all objects of a certain type. Other value options include **create**, **update**, and **delete**.

For details, see `Using RBAC Authorization <https://kubernetes.io/docs/reference/access-authn-authz/rbac/>`__.

After creating a Role, you can bind the Role to a specific user, which is called RoleBinding. The following is an example.

.. code-block::

   kind: RoleBinding
   apiVersion: rbac.authorization.k8s.io/v1
   metadata:
     name: RoleBinding-example
     namespace: default
     annotations:
       CCE.com/IAM: 'true'
   roleRef:
     kind: Role
     name: role-example
     apiGroup: rbac.authorization.k8s.io
   subjects:
   - kind: User
     name: 0c97ac3cb280f4d91fa7c0096739e1f8 # User ID of the user-example
     apiGroup: rbac.authorization.k8s.io

The **subjects** section binds a Role with an IAM user so that the IAM user can obtain the permissions defined in the Role, as shown in the following figure.


.. figure:: /_static/images/en-us_image_0262051194.png
   :alt: **Figure 2** A RoleBinding binds the Role to the user.

   **Figure 2** A RoleBinding binds the Role to the user.

You can also specify a user group in the **subjects** section. In this case, all users in the user group obtain the permissions defined in the Role.

.. code-block::

   subjects:
   - kind: Group
     name: 0c96fad22880f32a3f84c009862af6f7    # User group ID
     apiGroup: rbac.authorization.k8s.io

Use the IAM user user-example to connect to the cluster and obtain the pod information. The following is an example of the returned pod information.

.. code-block::

   # kubectl get pod
   NAME                                   READY   STATUS    RESTARTS   AGE
   deployment-389584-2-6f6bd4c574-2n9rk   1/1     Running   0          4d7h
   deployment-389584-2-6f6bd4c574-7s5qw   1/1     Running   0          4d7h
   deployment-3895841-746b97b455-86g77    1/1     Running   0          4d7h
   deployment-3895841-746b97b455-twvpn    1/1     Running   0          4d7h
   nginx-658dff48ff-7rkph                 1/1     Running   0          4d9h
   nginx-658dff48ff-njdhj                 1/1     Running   0          4d9h
   # kubectl get pod nginx-658dff48ff-7rkph
   NAME                     READY   STATUS    RESTARTS   AGE
   nginx-658dff48ff-7rkph   1/1     Running   0          4d9h

Try querying Deployments and Services in the namespace. The output shows **user-example** does not have the required permissions. Try querying the pods in namespace kube-system. The output shows **user-example** does not have the required permissions, neither. This indicates that the IAM user **user-example** has only the GET and LIST Pod permissions in the default namespace, which is the same as expected.

.. code-block::

   # kubectl get deploy
   Error from server (Forbidden): deployments.apps is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot list resource "deployments" in API group "apps" in the namespace "default"
   # kubectl get svc
   Error from server (Forbidden): services is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot list resource "services" in API group "" in the namespace "default"
   # kubectl get pod --namespace=kube-system
   Error from server (Forbidden): pods is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot list resource "pods" in API group "" in the namespace "kube-system"

Example: Assigning All Cluster Permissions (cluster-admin)
----------------------------------------------------------

You can use the cluster-admin role to assign all permissions on a cluster. This role contains the permissions for cluster resources (such as PVs and StorageClasses).

In the following example kubectl output, a ClusterRoleBinding has been created and binds the cluster-admin role to the user group **cce-role-group**.

.. code-block::

   # kubectl get clusterrolebinding
   NAME                                                              ROLE                           AGE
   clusterrole_cluster-admin_group0c96fad22880f32a3f84c009862af6f7   ClusterRole/cluster-admin      61s

   # kubectl get clusterrolebinding clusterrole_cluster-admin_group0c96fad22880f32a3f84c009862af6f7 -oyaml
   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRoleBinding
   metadata:
     annotations:
       CCE.com/IAM: "true"
     creationTimestamp: "2021-06-23T09:15:22Z"
     name: clusterrole_cluster-admin_group0c96fad22880f32a3f84c009862af6f7
     resourceVersion: "36659058"
     selfLink: /apis/rbac.authorization.k8s.io/v1/clusterrolebindings/clusterrole_cluster-admin_group0c96fad22880f32a3f84c009862af6f7
     uid: d6cd43e9-b4ca-4b56-bc52-e36346fc1320
   roleRef:
     apiGroup: rbac.authorization.k8s.io
     kind: ClusterRole
     name: cluster-admin
   subjects:
   - apiGroup: rbac.authorization.k8s.io
     kind: Group
     name: 0c96fad22880f32a3f84c009862af6f7

Connect to the cluster as an authorized user. If the PVs and StorageClasses can be queried, the permission configuration takes effect.

.. code-block::

   # kubectl get pv
   No resources found
   # kubectl get sc
   NAME                PROVISIONER                     RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
   csi-disk            everest-csi-provisioner         Delete          Immediate              true                   75d
   csi-disk-topology   everest-csi-provisioner         Delete          WaitForFirstConsumer   true                   75d
   csi-nas             everest-csi-provisioner         Delete          Immediate              true                   75d
   csi-obs             everest-csi-provisioner         Delete          Immediate              false                  75d
   csi-sfsturbo        everest-csi-provisioner         Delete          Immediate              true                   75d

Example: Assigning All Namespace Permissions (admin)
----------------------------------------------------

The admin role contains all permissions on a namespace. You can assign permissions to users to access one or multiple namespaces.

In the following example kubectl output, a RoleBinding has been created, the admin role is bound to the user group **cce-role-group**, and the target namespace is the default namespace.

.. code-block::

   # kubectl get rolebinding
   NAME                                                      ROLE                AGE
   clusterrole_admin_group0c96fad22880f32a3f84c009862af6f7   ClusterRole/admin   18s
   # kubectl get rolebinding clusterrole_admin_group0c96fad22880f32a3f84c009862af6f7 -oyaml
   apiVersion: rbac.authorization.k8s.io/v1
   kind: RoleBinding
   metadata:
     annotations:
       CCE.com/IAM: "true"
     creationTimestamp: "2021-06-24T01:30:08Z"
     name: clusterrole_admin_group0c96fad22880f32a3f84c009862af6f7
     namespace: default
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

Connect to a cluster as an authorized user. In this example, you can create and query resources in the default namespace, but cannot query resources in the kube-system namespace or cluster resources.

.. code-block::

   # kubectl get pod
   NAME                    READY   STATUS    RESTARTS   AGE
   test-568d96f4f8-brdrp   1/1     Running   0          33m
   test-568d96f4f8-cgjqp   1/1     Running   0          33m
   # kubectl get pod -nkube-system
   Error from server (Forbidden): pods is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot list resource "pods" in API group "" in the namespace "kube-system"
   # kubectl get pv
   Error from server (Forbidden): persistentvolumes is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot list resource "persistentvolumes" in API group "" at the cluster scope

Example: Assigning Read-Only Namespace Permissions (view)
---------------------------------------------------------

The view role has the read-only permissions on a namespace. You can assign permissions to users to view one or multiple namespaces.

In the following example kubectl output, a RoleBinding has been created, the view role is bound to the user group **cce-role-group**, and the target namespace is the default namespace.

.. code-block::

   # kubectl get rolebinding
   NAME                                                     ROLE               AGE
   clusterrole_view_group0c96fad22880f32a3f84c009862af6f7   ClusterRole/view   7s

   # kubectl get rolebinding clusterrole_view_group0c96fad22880f32a3f84c009862af6f7 -oyaml
   apiVersion: rbac.authorization.k8s.io/v1
   kind: RoleBinding
   metadata:
     annotations:
       CCE.com/IAM: "true"
     creationTimestamp: "2021-06-24T01:36:53Z"
     name: clusterrole_view_group0c96fad22880f32a3f84c009862af6f7
     namespace: default
     resourceVersion: "36965800"
     selfLink: /apis/rbac.authorization.k8s.io/v1/namespaces/default/rolebindings/clusterrole_view_group0c96fad22880f32a3f84c009862af6f7
     uid: b86e2507-e735-494c-be55-c41a0c4ef0dd
   roleRef:
     apiGroup: rbac.authorization.k8s.io
     kind: ClusterRole
     name: view
   subjects:
   - apiGroup: rbac.authorization.k8s.io
     kind: Group
     name: 0c96fad22880f32a3f84c009862af6f7

Connect to the cluster as an authorized user. In this example, you can query resources in the default namespace but cannot create resources.

.. code-block::

   # kubectl get pod
   NAME                    READY   STATUS    RESTARTS   AGE
   test-568d96f4f8-brdrp   1/1     Running   0          40m
   test-568d96f4f8-cgjqp   1/1     Running   0          40m
   # kubectl run -i --tty --image tutum/dnsutils dnsutils --restart=Never --rm /bin/sh
   Error from server (Forbidden): pods is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot create resource "pods" in API group "" in the namespace "default"

Example: Assigning Permissions for a Specific Kubernetes Resource Object
------------------------------------------------------------------------

You can assign permissions on a specific Kubernetes resource object, such as pod, Deployment, and Service. For details, see :ref:`Using kubectl to Configure Namespace Permissions <cce_01_0189__section1273861718819>`.
