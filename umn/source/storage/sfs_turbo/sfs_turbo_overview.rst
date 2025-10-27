:original_name: cce_10_0624.html

.. _cce_10_0624:

SFS Turbo Overview
==================

Introduction
------------

CCE allows you to mount storage volumes created by SFS Turbo file systems to a path of a container to meet data persistence requirements. SFS Turbo file systems are fast, on-demand, and scalable, which are suitable for scenarios with a massive number of small files, such as DevOps, containerized microservices, and enterprise office applications.

Expandable to 320 TB, SFS Turbo provides fully hosted shared file storage, which is highly available and stable, to support small files and applications requiring low latency and high IOPS.

-  **Standard file protocols**: You can mount file systems as volumes to servers, the same as using local directories.
-  **Data sharing**: The same file system can be mounted to multiple servers, so that data can be shared.
-  **Private network**: Users can access data only in private networks of data centers.
-  **Data isolation**: The on-cloud storage service provides exclusive cloud file storage, which delivers data isolation and ensures IOPS performance.
-  **Use cases**: Deployments/StatefulSets in the ReadWriteMany mode, DaemonSets, and jobs created for high-traffic websites, log storage, DevOps, and enterprise OA applications

Application Scenarios
---------------------

SFS Turbo supports the following mounting modes:

-  :ref:`Using an Existing SFS Turbo File System Through a Static PV <cce_10_0625>`: static creation mode, where you use an existing SFS volume to create a PV and then mount storage to the workload through a PVC.
-  :ref:`Dynamically Creating an SFS Turbo Subdirectory Using StorageClass <cce_bestpractice_00253_0>`: SFS Turbo allows you to dynamically create subdirectories and mount them to containers so that SFS Turbo can be shared and the SFS Turbo storage capacity can be used more economically and properly.
