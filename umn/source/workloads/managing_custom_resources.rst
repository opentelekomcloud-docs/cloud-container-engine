:original_name: cce_10_0833.html

.. _cce_10_0833:

Managing Custom Resources
=========================

Custom Resource Definition (CRD) is an extension of Kubernetes APIs. When default Kubernetes resources cannot meet service requirements, you can use CRDs to define new resource types. According to CRD, you can create custom resources in a cluster to meet service requirements. CRD allows you to create new resource types without adding new Kubernetes API servers. This makes cluster management more flexible.

Creating a CRD
--------------

#. Log in to the CCE console.
#. Click the cluster name to go to the cluster console, choose **Custom Resources** in the navigation pane, and click the **Create from YAML** in the upper right corner.
#. Edit the YAML file to create the CRD that matches your service's requirements. For details, see `Extend the Kubernetes API with CustomResourceDefinitions <https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/>`__.
#. Click **OK**.

Viewing CRDs and Their Resources
--------------------------------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Custom Resources** in the navigation pane.
#. On the **Custom Resources** page, view CRDs and their resources.

   -  View a CRD and its YAML.

      All CRDs in the cluster as well as their API groups, API versions, and resource application scopes are listed. Click **View YAML** in the **Operation** column of a CRD to view its YAML.

      You can enter a keyword in the search box to search for target resource types.

   -  View the resources of a CRD.

      Locate a CRD in the list and click **View Details** in the **Operation** column to view the resources.

Common Issues
-------------

This section covers common issues encountered when managing custom resources.

IAM User Cannot View or Edit CRDs
---------------------------------

**Symptom**

You can see a permission denied error when attempting to view or edit a CRD.

.. code-block::

   customresourcedefinitions.apiextensions.k8s.io is forbidden: User "xxx" cannot list resource "customresourcedefinitions" in API group "apiextensions.k8s.io" at the cluster scope

**Solution**

Grant the IAM user Kubernetes RBAC permissions for CRD resources. For details, see :ref:`Using kubectl to Configure Namespace Permissions <cce_10_0189__section1273861718819>`. The requiredscope is as follows:

-  API group (**apiGroups**): **apiextensions.k8s.io**
-  Resources: **customresourcedefinitions**

The following example demonstrates read-only access:

.. code-block::

   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRole
   metadata:
     name: crd-reader  # Cluster role name
   rules:
   - apiGroups: ["apiextensions.k8s.io"]      # API group of CRD resources
     resources: ["customresourcedefinitions"] # CRD resource object
     verbs: ["get", "list", "watch"]          # Retain only the read-only permission.
   ---
   # Specify the user or group to bind.
   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRoleBinding
   metadata:
     name: crd-reader-binding
   subjects:
   - kind: User  # Alternatively, user group
     name: 0c97ac3cb280f4d91fa7c0096739e1f8    # IAM user ID or user group ID
     apiGroup: rbac.authorization.k8s.io
   roleRef:
     kind: ClusterRole
     name: crd-reader  # Cluster role name
     apiGroup: rbac.authorization.k8s.io

IAM User Cannot View CRs
------------------------

**Symptom**

You can see a permission denied error when attempting to view or edit a custom resource (CR). The following example uses podmonitors:

.. code-block::

   Error from server (Forbidden): podmonitors.monitoring.coreos.com is forbidden: User "xxx" cannot list resource "podmonitors" in API group "monitoring.coreos.com" at the cluster scope

**Solution**

To view a CR, an IAM user must have Kubernetes RBAC permissions for that specific CR type. For details, see :ref:`Using kubectl to Configure Namespace Permissions <cce_10_0189__section1273861718819>`. The required permissions are as follows:

-  API group (**apiGroups**): The API group of the parent CRD, for example, **monitoring.coreos.com**
-  Resources: The CR resource name, for example, **podmonitors**

The following example demonstrates read-only access:

.. code-block::

   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRole
   metadata:
     name: crd-cr-reader
   rules:
   # 1. Grant permission to view the CRD definition.
   - apiGroups: ["apiextensions.k8s.io"]
     resources: ["customresourcedefinitions"]
     verbs: ["get", "list", "watch"]
   # 2. Grant permission to view the CR instance.
   - apiGroups: ["Your CRD API groups"]    # Example: monitoring.coreos.com
     resources: ["Your CR resource name"]         # Example: podmonitors
     verbs: ["get", "list", "watch"]     # Retain only the read-only permission.
   ---
   # Specify the user or group to bind.
   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRoleBinding
   metadata:
     name: crd-cr-reader-binding
   subjects:
   - kind: User  # Alternatively, user group
     name: 0c97ac3cb280f4d91fa7c0096739e1f8    # IAM user ID or user group ID
     apiGroup: rbac.authorization.k8s.io
   roleRef:
     kind: ClusterRole
     name: crd-cr-reader  # Cluster role name
     apiGroup: rbac.authorization.k8s.io
