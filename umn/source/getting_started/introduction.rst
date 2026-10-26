:original_name: cce_qs_0001.html

.. _cce_qs_0001:

Introduction
============

This section describes how to quickly get started with Cloud Container Engine (CCE) and provides frequently asked questions (FAQs) that you may encounter.

Procedure
---------

Complete the following tasks to get started with CCE.


.. figure:: /_static/images/en-us_image_0000002662012101.png
   :alt: **Figure 1** Procedure for getting started with CCE

   **Figure 1** Procedure for getting started with CCE

#. **Register an account and log in to the CCE console.**

   If you log in to CCE using an account, you have all IAM permissions by default. If you log in as an IAM user, the IAM user must be granted the required permissions.

#. **Create a cluster.**

#. **Deploy a workload (application).**

#. **View workload status and logs. Upgrade, scale, and monitor the workload.**

FAQs
----

#. **Can I use CCE even if I am not familiar with Kubernetes?**

   Yes. The CCE console is easy-to-use, and the *Getting Started* guide helps you quickly understand and use CCE.

#. **Is CCE suitable for users who have little experience in creating images?**

   Yes. CCE not only stores your own images in **My Images** but also allows you to create containerized applications using open-source images.

#. **How do I create a workload using CCE?**

   To create a workload, you need to create a cluster first.

#. **How do I create a workload that can be accessed from the Internet?**

   You have various access types to choose from, each suitable for different scenarios.

#. **How do I allow multiple workloads in the same cluster to access each other?**

   You can create ClusterIP Services for them. ClusterIP Services allow workloads in the same cluster to access each other using the cluster-internal domain names.

   Cluster-internal domain names are in the format of *<custom-service-name>*\ **.**\ *<workload's-namespace>*\ **.svc.cluster.local:**\ *<port-number>*. For example, **nginx.default.svc.cluster.local:80**.
