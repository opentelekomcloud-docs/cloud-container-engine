:original_name: cce_faq_00311.html

.. _cce_faq_00311:

Why Is "Error from server (Forbidden)" Displayed When I Use kubectl?
====================================================================

Symptom
-------

When you use kubectl to create or query Kubernetes resources, the following output is returned:

# kubectl get deploy Error from server (Forbidden): deployments.apps is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot list resource "deployments" in API group "apps" in the namespace "default"

Possible Cause
--------------

This user has no permissions to operate Kubernetes resources.

Solution
--------

Assign permissions to the user.

#. Log in to the CCE console. In the navigation pane, choose **Permissions**.

#. Select a cluster for which you want to add permissions from the drop-down list on the right.

#. Click **Add Permissions** in the upper right corner.

#. Confirm the cluster name and select the namespace to assign permissions for. For example, select **All namespaces**, the target user or user group, and select the permissions.

   .. note::

      If you do not have IAM permissions, you cannot select users or user groups when configuring permissions for other users or user groups. In this case, you can enter a user ID or user group ID.

   Permissions can be customized as required. After selecting **Custom** for **Permission Type**, click **Add Custom Role** on the right of the **Custom** parameter. In the dialog box displayed, enter a name and select a rule. After the custom rule is created, you can select a value from the **Custom** drop-down list box.

   Custom permissions are classified into ClusterRole and Role. Each ClusterRole or Role contains a group of rules that represent related permissions. For details, see `Using RBAC Authorization <https://kubernetes.io/docs/reference/access-authn-authz/rbac/>`__.

   -  A ClusterRole is a cluster-level resource that can be used to configure cluster access permissions.
   -  A Role is used to configure access permissions in a namespace. When creating a Role, specify the namespace to which the Role belongs.

#. Click **OK**.
