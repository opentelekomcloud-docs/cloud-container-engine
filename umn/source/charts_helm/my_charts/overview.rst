:original_name: cce_01_0191.html

.. _cce_01_0191:

Overview
========

CCE uses Helm, a Kubernetes package manager, to simplify deployment and management of packages (also called charts). A chart is a collection of files that describe a related set of Kubernetes resources. The use of charts handles all the complexity in Kubernetes resource installation and management, making it possible to achieve unified resource scheduling and management.

.. note::

   Helm is a tool for packaging Kubernetes applications. For more information, see `Helm documentation <https://helm.sh/>`__.

Custom charts simplify workload deployment.

This section describes how to create a workload using a custom chart. You can use multiple methods to create an orchestration chart on the CCE console.

Notes and Constraints
---------------------

-  The number of charts that can be uploaded by a single user is limited. The value displayed on the console of each region is the allowed quantity.
-  CCE uses Helm v2.12. If you use Helm v3 or later to manage CCE, compatibility problems may occur.
-  A chart with multiple versions consumes the same amount of portion of chart quota.
-  Users with chart operation permissions can perform multiple operations on clusters. Therefore, exercise caution when assigning users the chart lifecycle management permissions, including uploading charts and creating, deleting, and updating chart releases.
