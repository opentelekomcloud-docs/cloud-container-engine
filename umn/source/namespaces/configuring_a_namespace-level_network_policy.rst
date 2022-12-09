:original_name: cce_01_0286.html

.. _cce_01_0286:

Configuring a Namespace-level Network Policy
============================================

You can configure a namespace-level network policy after enabling network isolation.

By default, **Network Isolation** is disabled for namespaces. For example, if network isolation is off for namespace **default**, **all workloads in the current cluster** can access the workloads in namespace **default**.

To prevent other workloads from accessing the workloads in namespace **default**, perform the following steps:

.. important::

   Only clusters that use the tunnel network model support network isolation.

Prerequisites
-------------

-  You have created a Kubernetes cluster. For details, see :ref:`Creating a CCE Cluster <cce_01_0028>`.
-  You have created a namespace. For details, see :ref:`Creating a Namespace <cce_01_0278>`.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Namespaces**.

#. Select the cluster to which the namespace belongs from the **Clusters** drop-down list.

#. At the row of a namespace (for example, **default**), switch on **Network Isolation**.

   After network isolation is enabled, workloads in namespace **default** can access each other but they cannot be accessed by workloads in other namespaces.


   .. figure:: /_static/images/en-us_image_0000001144779784.png
      :alt: **Figure 1** Namespace-level network policy

      **Figure 1** Namespace-level network policy

Network Isolation Description
-----------------------------

Enabling network isolation is to create a network policy in a namespace. The network policy selects all pods in the namespace and prevents pods in other namespaces from accessing.

.. code-block::

   kind: NetworkPolicy
   apiVersion: networking.k8s.io/v1
   metadata:
       name: deny-default
       namespace: default
   spec:
       ingress:
           - from:
             - podSelector: {}
       podSelector: {}                     # {} indicates that all pods are selected.

You can also customize a network policy. For details, see :ref:`Network Policies <cce_01_0059>`.
