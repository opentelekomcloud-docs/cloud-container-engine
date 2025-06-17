:original_name: cce_10_0617.html

.. _cce_10_0617:

Overview
========

Introduction
------------

CCE allows you to mount a volume created from a Scalable File Service (SFS) file system to a container to store data persistently. SFS volumes are commonly used in ReadWriteMany scenarios for large-capacity expansion and cost-sensitive services, such as media processing, content management, big data analysis, and workload process analysis. For services with massive volume of small files, SFS Turbo file systems are recommended.

Expandable to petabytes, SFS provides fully hosted shared file storage, highly available and stable to handle data- and bandwidth-intensive applications

-  **Standard file protocols**: You can mount file systems as volumes to servers, the same as using local directories.
-  **Data sharing**: The same file system can be mounted to multiple servers, so that data can be shared.
-  **Private network**: Users can access data only in private networks of data centers.
-  **Capacity and performance**: The capacity of a single file system is high (PB level) and the performance is excellent (ms-level I/O latency).
-  **Use cases**: Deployments/StatefulSets in the ReadWriteMany mode and jobs created for high-performance computing (HPC), media processing, content management, web services, big data analysis, and workload process analysis

Performance
-----------

CCE supports SFS Capacity-Oriented. For more details, see `File System Types <https://docs.otc.t-systems.com/en-us/usermanual/sfs/sfs_01_0005.html>`__.

.. table:: **Table 1** Performance

   ================= =====================
   Parameter         SFS Capacity-Oriented
   ================= =====================
   Maximum bandwidth 2 GB/s
   Maximum IOPS      2000
   Latency           3-20 ms
   Maximum capacity  4 PB
   ================= =====================

Application Scenarios
---------------------

SFS supports the following mounting modes based on application scenarios:

-  :ref:`Using an Existing SFS File System Through a Static PV <cce_10_0619>`: static creation mode, where you use an existing SFS volume to create a PV and then mount storage to the workload through a PVC. This mode applies if the underlying storage is available.
-  :ref:`Using an SFS File System Through a Dynamic PV <cce_10_0620>`: dynamic creation mode, in which you do not need to create SFS file systems beforehand. Instead, specify a StorageClass when creating a PVC. Then, a file system and PV will be created automatically. This mode applies to scenarios where no underlying storage is available.
