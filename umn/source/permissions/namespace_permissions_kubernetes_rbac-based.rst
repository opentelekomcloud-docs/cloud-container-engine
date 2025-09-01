:original_name: cce_10_0189.html

.. _cce_10_0189:

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


.. figure:: /_static/images/en-us_image_0000002218660654.png
   :alt: **Figure 1** Role binding

   **Figure 1** Role binding

On the CCE console, you can assign permissions to a user or user group to access resources in one or multiple namespaces. By default, the CCE console provides the following ClusterRoles:

-  view (read-only): read-only permissions on most resources in all or selected namespaces.
-  edit (development): read-write permissions on most resources in all or selected namespaces. If this ClusterRole is configured for all namespaces, its capability is the same as the O&M permission.
-  admin (O&M): read and write permissions on most resources in all namespaces, and read-only permission on nodes, storage volumes, namespaces, and quota management.
-  cluster-admin (administrator): read and write permissions on all resources in all namespaces.
-  drainage-editor: drain a node.
-  drainage-viewer: view the nodal drainage status but cannot drain a node.

In addition to the preceding typical ClusterRoles, you can define Role and RoleBinding to grant the permissions to add, delete, modify, and obtain global resources (such as nodes, PVs, and CustomResourceDefinitions) and different resources (such as pods, Deployments, and Services) in namespaces for refined permission control.

Cluster Permissions (IAM-based) and Namespace Permissions (Kubernetes RBAC-based)
---------------------------------------------------------------------------------

Users with different cluster permissions (assigned using IAM) have different namespace permissions (assigned using Kubernetes RBAC). :ref:`Table 1 <cce_10_0189__cce_10_0187_table886210176509>` lists the namespace permissions of different users.

.. _cce_10_0189__cce_10_0187_table886210176509:

.. table:: **Table 1** Differences in namespace permissions

   +-------------------------------------------------------------+-----------------------------------------+
   | User                                                        | Clusters of v1.13 and Later             |
   +=============================================================+=========================================+
   | User with the Tenant Administrator permissions              | All namespace permissions               |
   +-------------------------------------------------------------+-----------------------------------------+
   | IAM user with the CCE Administrator role                    | All namespace permissions               |
   +-------------------------------------------------------------+-----------------------------------------+
   | IAM user with the CCE FullAccess or CCE ReadOnlyAccess role | Requires Kubernetes RBAC authorization. |
   +-------------------------------------------------------------+-----------------------------------------+
   | IAM user with the Tenant Guest role                         | Requires Kubernetes RBAC authorization. |
   +-------------------------------------------------------------+-----------------------------------------+

Precautions
-----------

-  After you create a cluster, CCE automatically assigns the cluster-admin permission to you, which means you have full control on all resources and all namespaces in this cluster. The ID of a federated user changes upon each login and logout. Therefore, the user with the permissions is displayed as deleted. In this case, do not delete the permissions. Otherwise, the authentication fails. You are advised to grant the cluster-admin permission to a user group on CCE and add federated users to the user group.
-  A user with the Security Administrator role has all IAM permissions except role switching. For example, an account in the admin user group has this role by default. Only these users can assign permissions on the **Permissions** page on the CCE console.

Configuring Namespace Permissions (on the Console)
--------------------------------------------------

You can regulate users' or user groups' access to Kubernetes resources in a single namespace based on their Kubernetes RBAC roles.

#. Log in to the CCE console. In the navigation pane, choose **Permissions**.

#. Select a cluster for which you want to add permissions from the drop-down list on the right.

#. Click **Add Permission** in the upper right corner.

#. Confirm the cluster name and select the namespace to assign permissions for. For example, select **All namespaces**, the target user or user group, and select the permissions.

   .. note::

      If you do not have IAM permissions, you cannot select users or user groups when configuring permissions for other users or user groups. In this case, you can enter a user ID or user group ID.

   Permissions can be customized as required. After selecting **Custom** for **Permission Type**, click **Add Custom Role** on the right of the **Custom** parameter. In the dialog box displayed, enter a name and select a rule. After the custom rule is created, you can select a value from the **Custom** drop-down list box.

   Custom permissions are classified into ClusterRole and Role. Each ClusterRole or Role contains a group of rules that represent related permissions. For details, see `Using RBAC Authorization <https://kubernetes.io/docs/reference/access-authn-authz/rbac/>`__.

   -  A ClusterRole is a cluster-level resource that can be used to configure cluster access permissions.
   -  A Role is used to configure access permissions in a namespace. When creating a Role, specify the namespace to which the Role belongs.

#. Click **OK**.

.. _cce_10_0189__section1273861718819:

Using kubectl to Configure Namespace Permissions
------------------------------------------------

.. note::

   When you access a cluster using kubectl, CCE uses the **kubeconfig** file generated on the cluster for authentication. This file contains user information, based on which CCE determines which Kubernetes resources can be accessed via kubectl. Since the **kubeconfig** file contains user identity details, the permissions associated with that user are inherited when accessing the cluster via kubectl. For details about user permissions, see :ref:`Cluster Permissions (IAM-based) and Namespace Permissions (Kubernetes RBAC-based) <cce_10_0187__section1464135853519>`.

In addition to cluster-admin, admin, edit, and view, you can define Roles and RoleBindings to configure the permissions to add, delete, modify, and obtain resources, such as pods, Deployments, and Services, in the namespace.

The definition of a Role is simple. You just specify a namespace and some rules. For example, the following rules allow you to perform GET and LIST operations on pods in the **default** namespace.

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

After creating a Role, you can bind the Role to a specific user, which is called RoleBinding. The following shows an example:

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
   :alt: **Figure 2** Binding a Role to a user

   **Figure 2** Binding a Role to a user

You can also specify a user group in the **subjects** section. In this case, all users in the user group obtain the permissions defined in the Role.

.. code-block::

   ...
   subjects:
   - kind: Group
     name: 0c96fad22880f32a3f84c009862af6f7    # User group ID
     apiGroup: rbac.authorization.k8s.io

To verify namespace permissions, perform the following operations:

#. Log in to the CCE console as the IAM user **user-example**, obtain the **kubeconfig** file, and use it to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Obtain the pod information.

   .. code-block::

      kubectl get pod

   The following shows the pod information:

   .. code-block::

      NAME                                   READY   STATUS    RESTARTS   AGE
      deployment-389584-2-6f6bd4c574-2n9rk   1/1     Running   0          4d7h
      deployment-389584-2-6f6bd4c574-7s5qw   1/1     Running   0          4d7h
      deployment-3895841-746b97b455-86g77    1/1     Running   0          4d7h
      deployment-3895841-746b97b455-twvpn    1/1     Running   0          4d7h
      nginx-658dff48ff-7rkph                 1/1     Running   0          4d9h
      nginx-658dff48ff-njdhj                 1/1     Running   0          4d9h

#. Check other types of resources, for example, Deployments.

   .. code-block::

      kubectl get deploy

   If the following information is displayed, you do not have permission to view the Deployments:

   .. code-block::

      # kubectl get deploy
      Error from server (Forbidden): deployments.apps is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot list resource "deployments" in API group "apps" in the namespace "default"

#. Check the pod information in the **kube-system** namespace.

   .. code-block::

      kubectl get pod --namespace=kube-system

   If the following information is displayed, you do not have permission to view the pod information in the **kube-system** namespace:

   .. code-block::

      Error from server (Forbidden): pods is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot list resource "pods" in API group "" in the namespace "kube-system"

This indicates that the IAM user **user-example** has only the GET and LIST Pod permissions in the **default** namespace, which is the same as expected.

.. _cce_10_0189__section914518255314:

Example: Assigning Cluster Administrator Permissions (cluster-admin)
--------------------------------------------------------------------

You can use the cluster-admin role to assign all permissions on a cluster. This role contains the permissions for all cluster resources.

In the following example kubectl output, a ClusterRoleBinding has been created to associate the cluster-admin role with the user group **cce-role-group**.

.. code-block::

   kubectl get clusterrolebinding

Command output:

.. code-block::

   NAME                                                              ROLE                           AGE
   clusterrole_cluster-admin_group0c96fad22880f32a3f84c009862af6f7   ClusterRole/cluster-admin      61s

Run the following command to view the ClusterRoleBinding details:

.. code-block::

   kubectl get clusterrolebinding clusterrole_cluster-admin_group0c96fad22880f32a3f84c009862af6f7 -oyaml

Command output:

.. code-block::

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

Access the cluster as an authorized user. If cluster-level resources like StorageClasses can be accessed, the permission configuration is effective.

.. code-block::

   kubectl get sc

If the following information is displayed, the permission configuration is effective:

.. code-block::

   NAME                PROVISIONER                     RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
   csi-disk            everest-csi-provisioner         Delete          Immediate              true                   75d
   csi-disk-topology   everest-csi-provisioner         Delete          WaitForFirstConsumer   true                   75d
   csi-nas             everest-csi-provisioner         Delete          Immediate              true                   75d
   csi-obs             everest-csi-provisioner         Delete          Immediate              false                  75d
   ...

Example: Assigning Namespace O&M Permissions (admin)
----------------------------------------------------

The admin role has the read and write permissions on most namespace resources. You can grant the admin permission on all namespaces to a user or user group.

In the following example kubectl output, a ClusterRoleBinding has been created to associate the admin role with the user group **cce-role-group**.

.. code-block::

   kubectl get clusterrolebinding

Command output:

.. code-block::

   NAME                                                      ROLE                AGE
   clusterrole_admin_group0c96fad22880f32a3f84c009862af6f7   ClusterRole/admin   18s

Run the following command to view the ClusterRoleBinding details:

.. code-block::

   kubectl get clusterrolebinding clusterrole_admin_group0c96fad22880f32a3f84c009862af6f7 -oyaml

Command output:

.. code-block::

   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRoleBinding
   metadata:
     annotations:
       CCE.com/IAM: "true"
     creationTimestamp: "2021-06-24T01:30:08Z"
     name: clusterrole_admin_group0c96fad22880f32a3f84c009862af6f7
     resourceVersion: "36963685"
     uid: 6c6f46a6-8584-47da-83f5-9eef1f7b75d6
   roleRef:
     apiGroup: rbac.authorization.k8s.io
     kind: ClusterRole
     name: admin
   subjects:
   - apiGroup: rbac.authorization.k8s.io
     kind: Group
     name: 0c96fad22880f32a3f84c009862af6f7

Access the cluster as an authorized user. Cluster-level resources like StorageClasses can be accessed.

.. code-block::

   kubectl get sc

Command output:

.. code-block::

   NAME                PROVISIONER                     RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
   csi-disk            everest-csi-provisioner         Delete          Immediate              true                   75d
   csi-disk-topology   everest-csi-provisioner         Delete          WaitForFirstConsumer   true                   75d
   csi-nas             everest-csi-provisioner         Delete          Immediate              true                   75d
   csi-obs             everest-csi-provisioner         Delete          Immediate              false                  75d
   ...

If the namespace cannot be created, the permission configuration is effective.

.. code-block::

   kubectl create namespace my-namespace

Command output:

.. code-block::

   Error from server (Forbidden): namespaces is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot create resource "namespaces" in API group "" at the cluster scope

Example: Assigning Namespace Developer Permissions (edit)
---------------------------------------------------------

The edit role has the read and write permissions on most namespace resources. You can assign this role to a user or user group for specific namespaces.

In the following example kubectl output, a RoleBinding has been created, the edit role is bound to the user group **cce-role-group**, and the target namespace is the default namespace.

.. code-block::

   kubectl get rolebinding

Command output:

.. code-block::

   NAME                                                      ROLE                AGE
   clusterrole_edit_group0c96fad22880f32a3f84c009862af6f7   ClusterRole/edit    18s

Run the following command to view the ClusterRoleBinding details:

.. code-block::

   kubectl get clusterrolebinding clusterrole_edit_group0c96fad22880f32a3f84c009862af6f7 -oyaml

Command output:

.. code-block::

   apiVersion: rbac.authorization.k8s.io/v1
   kind: RoleBinding
   metadata:
     annotations:
       CCE.com/IAM: "true"
     creationTimestamp: "2021-06-24T01:30:08Z"
     name: clusterrole_edit_group0c96fad22880f32a3f84c009862af6f7
     namespace: default
     resourceVersion: "36963685"
     uid: 6c6f46a6-8584-47da-83f5-9eef1f7b75d6
   roleRef:
     apiGroup: rbac.authorization.k8s.io
     kind: ClusterRole
     name: edit
   subjects:
   - apiGroup: rbac.authorization.k8s.io
     kind: Group
     name: 0c96fad22880f32a3f84c009862af6f7

Access the cluster as an authorized user. Then, you can obtain and create resources in the default namespace.

.. code-block::

   kubectl get pod

Command output:

.. code-block::

   NAME                    READY   STATUS    RESTARTS   AGE
   test-568d96f4f8-brdrp   1/1     Running   0          33m
   test-568d96f4f8-cgjqp   1/1     Running   0          33m

However, you do not have permission to access resources in the **kube-system** namespace.

.. code-block::

   kubectl get pod -n kube-system

Command output:

.. code-block::

   Error from server (Forbidden): pods is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot list resource "pods" in API group "" in the namespace "kube-system"

You do not have permission to access cluster-level resources, such as PVs.

.. code-block::

   kubectl get pv

Command output:

.. code-block::

   Error from server (Forbidden): persistentvolumes is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot list resource "persistentvolumes" in API group "" at the cluster scope

Example: Assigning Read-Only Namespace Permissions (view)
---------------------------------------------------------

The view role has the read-only permissions on a namespace. You can assign permissions to users to view one or multiple namespaces.

In the following example kubectl output, a RoleBinding has been created, the view role is bound to the user group **cce-role-group**, and the target namespace is the default namespace.

.. code-block::

   kubectl get rolebinding

Command output:

.. code-block::

   NAME                                                     ROLE               AGE
   clusterrole_view_group0c96fad22880f32a3f84c009862af6f7   ClusterRole/view   7s

Run the following command to view the ClusterRoleBinding details:

.. code-block::

   kubectl get rolebinding clusterrole_view_group0c96fad22880f32a3f84c009862af6f7 -oyaml

Command output:

.. code-block::

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

Access the cluster as an authorized user. Then, you can obtain resources in the default namespace.

.. code-block::

   kubectl get pod

Command output:

.. code-block::

   NAME                    READY   STATUS    RESTARTS   AGE
   test-568d96f4f8-brdrp   1/1     Running   0          40m
   test-568d96f4f8-cgjqp   1/1     Running   0          40m
   # kubectl run -i --tty --image tutum/dnsutils dnsutils --restart=Never --rm /bin/sh
   Error from server (Forbidden): pods is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot create resource "pods" in API group "" in the namespace "default"

Failed to create the resource.

.. code-block::

   kubectl run -i --tty --image tutum/dnsutils dnsutils --restart=Never --rm /bin/sh

Command output:

.. code-block::

   Error from server (Forbidden): pods is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot create resource "pods" in API group "" in the namespace "default"

Example: Assigning Permissions for a Specific Kubernetes Resource Object
------------------------------------------------------------------------

You can assign permissions on a specific Kubernetes resource object, such as pod, Deployment, and Service. For details, see :ref:`Using kubectl to Configure Namespace Permissions <cce_10_0189__section1273861718819>`.
