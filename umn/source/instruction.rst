:original_name: cce_qs_0001.html

.. _cce_qs_0001:

Instruction
===========

This document provides instructions for getting started with the Cloud Container Engine (CCE).

Procedure
---------

Complete the following tasks to get started with CCE.


.. figure:: /_static/images/en-us_image_0000001178352608.png
   :alt: **Figure 1** Procedure for getting started with CCE

   **Figure 1** Procedure for getting started with CCE

#. **Authorize an IAM user to use CCE.**

   The accounts have the permission to use CCE. However, IAM users created by the accounts do not have the permission. You need to manually assign the permission to IAM users.

#. **Create a cluster.**

   For details on how to create a regular Kubernetes cluster, see `Creating a CCE Cluster <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0028.html>`__.

#. **Create a workload from images or a chart.**

   Select existing images/chart, or create new images/chart.

   -  For details on how to create a workload from images, see `Workloads <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0046.html>`__.
   -  For details on how to create a workload from a chart, see `Charts (Helm) <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0019.html>`__.

#. **View workload status and logs. Upgrade, scale, and monitor the workload.**

   For details, see `Managing Workloads and Jobs <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0007.html>`__.

FAQs
----

#. **Is CCE suitable for users who are not familiar with Kubernetes?**

   Yes. The CCE console is easy-to-use.

#. **Is CCE suitable for users who have little experience in building images?**

   Yes. You can select images from **Third-party Images**, and **Shared Images** pages on the CCE console. The **My Images** page displays only the images created by you. For details, see `Workloads <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0046.html>`__.

#. **How do I create a workload using CCE?**

   Create a cluster and then create a workload in the cluster.

#. **How do I create a workload accessible to public networks?**

   CCE provides different types of Services for workload access in diverse scenarios. Currently, CCE provides two access types to expose a workload to public networks: NodePort and LoadBalancer. For details, see `Networking <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0020.html>`__.

#. **How can I allow multiple workloads in the same cluster to access each other?**

   Select the access type ClusterIP, which allows workloads in the same cluster to use their cluster-internal domain names to access each other.

   Cluster-internal domain names are in the format of <self-defined service name>.<workload's namespace>.svc.cluster.local:<port number>. For example, nginx.default.svc.cluster.local:80.

   Example:

   Assume that workload A needs to access workload B in the same cluster. Then, you can create a `ClusterIP <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0011.html>`__ Service for workload B. After the ClusterIP Service is created, workload B is reachable at <self-defined service name>.<workload B's namespace>.svc.cluster.local:<port number>.
