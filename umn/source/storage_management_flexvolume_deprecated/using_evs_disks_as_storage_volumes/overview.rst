:original_name: cce_10_0310.html

.. _cce_10_0310:

Overview
========

To achieve persistent storage, CCE allows you to mount the storage volumes created from Elastic Volume Service (EVS) disks to a path of a container. When the container is migrated, the mounted EVS volumes are also migrated. By using EVS volumes, you can mount the remote file directory of storage system into a container so that data in the data volume is permanently preserved even when the container is deleted.


.. figure:: /_static/images/en-us_image_0000001517903060.png
   :alt: **Figure 1** Mounting EVS volumes to CCE

   **Figure 1** Mounting EVS volumes to CCE

Description
-----------

-  **User-friendly**: Similar to formatting disks for on-site servers in traditional layouts, you can format block storage (disks) mounted to cloud servers, and create file systems on them.
-  **Data isolation**: Each server uses an independent block storage device (disk).
-  **Private network**: User can access data only in private networks of data centers.
-  **Capacity and performance**: The capacity of a single volume is limited (TB-level), but the performance is excellent (ms-level read/write I/O latency).
-  **Restriction**: EVS disks that have partitions or have non-ext4 file systems cannot be imported.
-  **Applications**: HPC, enterprise core applications running in clusters, enterprise application systems, and development and testing. These volumes are often used by single-pod Deployments and jobs, or exclusively by each pod in a StatefulSet. EVS disks are non-shared storage and cannot be attached to multiple nodes at the same time. If two pods are configured to use the same EVS disk and the two pods are scheduled to different nodes, one pod cannot be started because the EVS disk cannot be attached to it.
