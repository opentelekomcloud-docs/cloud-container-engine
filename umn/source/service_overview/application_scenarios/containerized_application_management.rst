:original_name: cce_productdesc_0007.html

.. _cce_productdesc_0007:

Containerized Application Management
====================================

Application Scenarios
---------------------

CCE clusters enable the management of both x86 and Arm resources. With CCE, you can effortlessly create Kubernetes clusters, deploy containerized applications, and effectively manage and maintain them.

-  Containerized web applications: CCE clusters interconnect with middleware and support HA DR, auto scaling, public network release, and gray upgrade, helping you quickly deploy web service applications.
-  Middleware deployment platforms: CCE clusters serve as effective platforms for deploying middleware services. You can run stateful applications in CCE clusters by configuring resources like StatefulSets and PVCs and configure load balancers to expose these services to external networks.
-  Jobs and CronJobs: Job and CronJob applications can be containerized to reduce the dependency on the host system. Global resource scheduling secures the resource usage during task running and improves the overall resource utilization in the cluster.


.. figure:: /_static/images/en-us_image_0000002434236556.png
   :alt: **Figure 1** CCE cluster

   **Figure 1** CCE cluster

Benefits
--------

Containerization helps reduce resource costs for application deployment, enhances the efficiency of deployment and upgrades, and ensures service continuity through unified and automated O&M during upgrades.

Advantages
----------

-  Multiple types of workloads

   CCE supports Deployments, StatefulSets, DaemonSets, jobs, and CronJobs for diverse needs.

-  Application upgrades

   CCE supports replace upgrades, rolling upgrades (by proportion or by number of pods), and upgrade rollback.

-  Auto scaling

   CCE supports auto scaling of nodes and workloads.


.. figure:: /_static/images/en-us_image_0000002467675109.png
   :alt: **Figure 2** Workload

   **Figure 2** Workload
