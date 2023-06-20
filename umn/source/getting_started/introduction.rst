:original_name: cce_qs_0001.html

.. _cce_qs_0001:

Introduction
============

This section describes how to use Cloud Container Engine (CCE) and provides frequently asked questions (FAQs) to help you quickly get started with CCE.

Procedure
---------

Complete the following tasks to get started with CCE.


.. figure:: /_static/images/en-us_image_0000001499760912.png
   :alt: **Figure 1** Procedure for getting started with CCE

   **Figure 1** Procedure for getting started with CCE

#. **Register an account and grant permissions to IAM users.**

   An account has the permissions to use CCE. However, IAM users created by an account do not have the permission. You need to manually grant the permission to IAM users. For details, see .

#. **Create a cluster.**

   For details on how to create a regular Kubernetes cluster, see :ref:`Creating a Kubernetes Cluster <cce_qs_0008>`.

#. **Create a workload from an image or chart.**

   -  :ref:`Creating a Deployment (Nginx) from an Image <cce_qs_0003>`
   -  :ref:`Deploying WordPress and MySQL That Depend on Each Other <cce_qs_0007>`

#. **View workload status and logs. Upgrade, scale, and monitor the workload.**

FAQs
----

#. **Is CCE suitable for users who are not familiar with Kubernetes?**

   Yes. The CCE console is easy-to-use, and the *Getting Started* guide helps you quickly understand and use CCE.

#. **Is CCE suitable for users who have little experience in building images?**

   In addition to storing images created by yourself in **My Images**, CCE allows you to create containerized applications using open source images. For details, see :ref:`Creating a Deployment (Nginx) from an Image <cce_qs_0003>`.

#. **How do I create a workload using CCE?**

   Create a cluster and then create a workload in the cluster. For details, see :ref:`Creating a Deployment (Nginx) from an Image <cce_qs_0003>`.

#. **How do I create a workload accessible to public networks?**

   CCE provides different workload access types to address diverse scenarios.

#. **How can I allow multiple workloads in the same cluster to access each other?**

   Set **Service Type** to **ClusterIP**, which allows workloads in the same cluster to use their cluster-internal domain names to access each other.

   Cluster-internal domain names are in the format of <self-defined service name>.<workload's namespace>.svc.cluster.local:<port number>. For example, nginx.default.svc.cluster.local:80.
