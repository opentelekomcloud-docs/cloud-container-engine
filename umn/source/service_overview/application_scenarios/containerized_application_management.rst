:original_name: cce_productdesc_0007.html

.. _cce_productdesc_0007:

Containerized Application Management
====================================

Application Scenario
--------------------

In CCE, you can run clusters with x86 and Arm nodes. Create and manage Kubernetes clusters. Deploy containerized applications in them. All done in CCE.

-  Containerized web applications: CCE clusters interconnect with middleware such as GaussDB and Redis and support HA DR, auto scaling, public network release, and gray upgrade, helping you quickly deploy web service applications.
-  Middleware deployment platform: CCE clusters can be used as middleware deployment platforms to implement stateful applications with StatefulSets and PVCs. In addition, load balancers can be used to expose middleware services.
-  Jobs and cron jobs: Job and cron job applications can be containerized to reduce the dependency on the host system. Global resource scheduling secures the resource usage during task running and improves the overall resource usage in the cluster.


.. figure:: /_static/images/en-us_image_0000001851584048.png
   :alt: **Figure 1** CCE cluster

   **Figure 1** CCE cluster

Benefits
--------

Containerization requires less resources to deploy application. Services are not uninterrupted during upgrades.

Advantages
----------

-  Multiple types of workloads

   Runs Deployments, StatefulSets, DaemonSets, jobs, and cron jobs to meet different needs.

-  Application upgrade

   Upgrades your apps in replace or rolling mode (by proportion or by number of pods), or rolls back the upgrades.

-  Auto scaling

   Auto scales your nodes and workloads according to the policies you set.


.. figure:: /_static/images/en-us_image_0000001851742760.png
   :alt: **Figure 2** Workload

   **Figure 2** Workload
