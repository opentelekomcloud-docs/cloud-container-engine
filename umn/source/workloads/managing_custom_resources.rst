:original_name: cce_10_0833.html

.. _cce_10_0833:

Managing Custom Resources
=========================

Custom Resource Definition (CRD) is an extension of Kubernetes APIs. When default Kubernetes resources cannot meet service requirements, you can use CRDs to define new resource types. According to CRD, you can create custom resources in a cluster to meet service requirements. CRD allows you to create new resource types without adding new Kubernetes API servers. This makes cluster management more flexible.

Creating a CRD
--------------

#. Log in to the CCE console.
#. Click the cluster name to go to the cluster console, choose **Custom Resources** in the navigation pane, and click the **Create from YAML** in the upper right corner.
#. Customize the YAML file to create a CRD based on service requirements. For details, see `Extend the Kubernetes API with CustomResourceDefinitions <https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/>`__.
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

      Locate a CDR in the list and click **View Details** in the **Operation** column to view the resources.
