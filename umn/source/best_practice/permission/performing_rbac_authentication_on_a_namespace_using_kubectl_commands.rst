:original_name: cce_bestpractice_0300.html

.. _cce_bestpractice_0300:

Performing RBAC Authentication on a Namespace Using kubectl Commands
====================================================================

Background
----------

CCE permissions are classified into cluster permissions and namespace permissions. Namespace permissions are based on Kubernetes RBAC and can be used to grant permissions on resources in clusters and namespaces.

The CCE console provides four default ClusterRole roles: cluster-admin, admin, edit, and view. These roles apply uniformly to all resources within a namespace. However, they do not allow granular permission configuration (create, delete, modify, and get) for specific resource types, such as pods, Deployments, or Services, within that namespace.

Solution
--------

Kubernetes RBAC enables you to easily control permissions on namespace resources.

-  Roles: define a set of rules for accessing Kubernetes resources in a namespace.
-  RoleBindings: define the relationship between users and roles.
-  ClusterRoles: define a set of rules for accessing Kubernetes resources in a cluster (including all namespaces).
-  ClusterRoleBindings: define the relationship between users and cluster roles.

Roles and ClusterRoles specify actions that can be performed on specific resources. RoleBindings and ClusterRoleBindings bind Roles to specific users, user groups, or service accounts.


.. figure:: /_static/images/en-us_image_0261301557.png
   :alt: **Figure 1** Role binding

   **Figure 1** Role binding

The user in the preceding figure can be an IAM user or user group in CCE. You can efficiently control permissions on namespace resources through RoleBindings.

The section describes how to use Kubernetes RBAC to grant user **user-example** with the permission for viewing pods. (This is the only permission the user has.)

Prerequisites
-------------

RBAC is supported only on clusters of v1.11.7-r2 or later.

Creating an IAM User and User Group
-----------------------------------

Log in to the IAM console and create an IAM user named **user-example** and a user group named **cce-role-group**. For details about how to create an IAM user and user group, see `Creating a User and Adding the User to a User Group <https://docs.otc.t-systems.com/en-us/usermanual/iam/iam_01_0031.html>`__ and `Creating a User Group and Assigning Permissions <https://docs.otc.t-systems.com/en-us/usermanual/iam/iam_01_0030.html>`__.

Grant the **CCE FullAccess** permission to the **cce-role-group** user group. For details about how to grant permissions to a user group, see `Creating a User Group and Assigning Permissions <https://docs.otc.t-systems.com/en-us/usermanual/iam/iam_01_0030.html>`__.

:ref:`CCE FullAccess <cce_10_0189>` has the permissions for cluster operations (such as cluster creation), but does not have the permissions to operate Kubernetes resources (such as viewing pods).

Creating a Cluster
------------------

Log in to CCE and create a cluster.

.. important::

   Do not use IAM user **user-example** to create a cluster because CCE automatically assigns the cluster-admin permissions of all namespaces in the cluster to the user who creates the cluster. That is, the user can fully control the resources in the cluster and all its namespaces.

Log in to the CCE console as IAM user **user-example**, :ref:`download the kubectl configuration file in the cluster and access the cluster <cce_10_0107>`, and run the following command to obtain the pod information. (The output shows that **user-example** does not have the permission to view the pods or other resources.) This indicates that **user-example** does not have the permissions to operate Kubernetes resources.

.. code-block::

   # kubectl get pod
   Error from server (Forbidden): pods is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot list resource "pods" in API group "" in the namespace "default"
   # kubectl get deploy
   Error from server (Forbidden): deployments.apps is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot list resource "deployments" in API group "apps" in the namespace "default"

Creating a Role and RoleBinding
-------------------------------

Log in to the CCE console, :ref:`download the kubectl configuration file in the cluster and access the cluster <cce_10_0107>`, and create a Role and RoleBinding.

.. note::

   Log in as the account used to create the cluster because CCE automatically assigns the cluster-admin permissions to the account, which means that the account has the permissions to create Roles and RoleBindings. Alternatively, you can use IAM users who have the permissions to create Roles and RoleBindings.

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

After creating a Role, you can bind the Role to a specific user. This is called RoleBinding. The following shows an example:

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
     name: 0c97ac3cb280f4d91fa7c0096739e1f8    # IAM user ID
     apiGroup: rbac.authorization.k8s.io

The **subjects** section binds a Role with an IAM user so that the IAM user can obtain the permissions defined in the Role, as shown in the following figure.


.. figure:: /_static/images/en-us_image_0262051194.png
   :alt: **Figure 2** Binding a Role to a user

   **Figure 2** Binding a Role to a user

You can also specify a user group in the **subjects** section. In this case, all users in the user group obtain the permissions defined in the Role.

.. code-block::

   subjects:
   - kind: Group
     name: 0c96fad22880f32a3f84c009862af6f7    # User group ID
     apiGroup: rbac.authorization.k8s.io

Verification
------------

Use IAM user **user-example** to connect to the cluster and view the pods. The pods can be viewed.

.. code-block::

   # kubectl get pod
   NAME                                   READY   STATUS    RESTARTS   AGE
   nginx-658dff48ff-7rkph                 1/1     Running   0          4d9h
   nginx-658dff48ff-njdhj                 1/1     Running   0          4d9h
   # kubectl get pod nginx-658dff48ff-7rkph
   NAME                     READY   STATUS    RESTARTS   AGE
   nginx-658dff48ff-7rkph   1/1     Running   0          4d9h

Try querying Deployments and Services in the namespace. The output shows **user-example** does not have the corresponding permissions. Try querying the pods in namespace kube-system. The output shows **user-example** does not have the corresponding permission, either. This indicates that the IAM user **user-example** has only the GET and LIST Pod permissions in the **default** namespace, which is the same as expected.

.. code-block::

   # kubectl get deploy
   Error from server (Forbidden): deployments.apps is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot list resource "deployments" in API group "apps" in the namespace "default"
   # kubectl get svc
   Error from server (Forbidden): services is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot list resource "services" in API group "" in the namespace "default"
   # kubectl get pod --namespace=kube-system
   Error from server (Forbidden): pods is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot list resource "pods" in API group "" in the namespace "kube-system"
