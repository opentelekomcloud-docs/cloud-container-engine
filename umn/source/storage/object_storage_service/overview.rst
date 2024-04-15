:original_name: cce_10_0628.html

.. _cce_10_0628:

Overview
========

Introduction
------------

Object Storage Service (OBS) provides massive, secure, and cost-effective data storage capabilities for you to store data of any type and size. You can use it in enterprise backup/archiving, video on demand (VoD), video surveillance, and many other scenarios.

-  **Standard APIs**: With HTTP RESTful APIs, OBS allows you to use client tools or third-party tools to access object storage.
-  **Data sharing**: Servers, embedded devices, and IoT devices can use the same path to access shared object data in OBS.
-  **Public/Private networks**: OBS allows data to be accessed from public networks to meet Internet application requirements.
-  **Capacity and performance**: No capacity limit; high performance (read/write I/O latency within 10 ms).
-  **Use cases**: Deployments/StatefulSets in the **ReadOnlyMany** mode and jobs created for big data analysis, static website hosting, online VOD, gene sequencing, intelligent video surveillance, backup and archiving, and enterprise cloud boxes (web disks). You can create object storage by using the OBS console, tools, and SDKs.

OBS Specifications
------------------

OBS provides multiple storage classes to meet customers' requirements on storage performance and costs.

-  Parallel File System (PFS, **recommended**): It is an optimized high-performance file system provided by OBS. It provides millisecond-level access latency, TB/s-level bandwidth, and million-level IOPS, and can quickly process HPC workloads. PFS outperforms OBS buckets.
-  Object bucket (**not recommended**):

   -  Standard: features low latency and high throughput. It is therefore good for storing frequently (multiple times per month) accessed files or small files (less than 1 MB). Its application scenarios include big data analytics, mobile apps, hot videos, and social apps.
   -  OBS Infrequent Access: applicable to storing semi-frequently accessed (less than 12 times a year) data requiring quick response. Its application scenarios include file synchronization or sharing, and enterprise-level backup. This storage class has the same durability, low latency, and high throughput as the Standard storage class, with a lower cost, but its availability is slightly lower than the Standard storage class.

Application Scenarios
---------------------

OBS supports the following mounting modes based on application scenarios:

-  :ref:`Using an Existing OBS Bucket Through a Static PV <cce_10_0379>`: static creation mode, where you use an existing OBS volume to create a PV and then mount storage to the workload through a PVC. This mode applies to scenarios where the underlying storage is available.
-  :ref:`Using an OBS Bucket Through a Dynamic PV <cce_10_0630>`: dynamic creation mode, where you do not need to create OBS volumes in advance. Instead, specify a StorageClass during PVC creation and an OBS volume and a PV will be automatically created. This mode applies to scenarios where no underlying storage is available.
