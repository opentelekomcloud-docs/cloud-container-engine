:original_name: cce_10_0628.html

.. _cce_10_0628:

OBS Overview
============

Introduction
------------

Object Storage Service (OBS) provides massive, secure, and cost-effective data storage for you to store data of any type and size. You can use it in enterprise backup/archiving, video on demand (VoD), video surveillance, and many other scenarios.

-  **Standard APIs**: With HTTP RESTful APIs, OBS allows you to use client tools or third-party tools to access object storage.
-  **Data sharing**: Servers, embedded devices, and IoT devices can use the same path to access shared object data in OBS.
-  **Public/Private networks**: OBS allows data to be accessed from public networks to meet Internet application requirements.
-  **Capacity and performance**: No capacity limit; high performance (I/O latency within 10 ms).
-  **Use cases**: Deployments/StatefulSets in the **ReadOnlyMany** mode and jobs created for big data analysis, static website hosting, online VoD, gene sequencing, intelligent video surveillance, backup and archiving, and enterprise cloud boxes (web disks). You can create object storage by using the OBS console, tools, and SDKs.

OBS Specifications
------------------

OBS provides multiple storage classes to meet customers' requirements on storage performance and costs.

-  Object buckets provide reliable, high-performance, secure, and budget-friendly storage for data. They have no restrictions on the quantity of files or storage capacity.

   -  Standard: features low latency and high throughput. It is therefore good for storing frequently (multiple times per month) accessed files or small files (less than 1 MB). Its application scenarios include big data analytics, mobile apps, hot videos, and social apps.
   -  OBS Infrequent Access: applicable to storing semi-frequently accessed (less than 12 times a year) data requiring quick response. Its application scenarios include file synchronization or sharing, and enterprise-level backup. This storage class has the same durability, low latency, and high throughput as the Standard storage class, with a lower cost, but its availability is slightly lower than the Standard storage class.

-  Parallel file systems are a sub-product of OBS designed to provide high-performance file semantics for big data scenarios.

Performance
-----------

Every time an OBS volume is mounted to a container workload, a resident process is created in the backend. When a workload uses too many OBS volumes or reads and writes a large number of object storage files, resident processes will consume a significant amount of memory. The amount of memory required in these scenarios is listed in :ref:`Table 1 <cce_10_0628__table1422659132012>`. To ensure stable running of the workload, make sure that the number of OBS volumes used does not exceed the requested memory. For example, if the workload requests 4 GiB of memory, the number of OBS volumes should be **no more than** 4.

.. _cce_10_0628__table1422659132012:

.. table:: **Table 1** Memory required by a single object storage resident process

   ==================================================== =============
   Test Item                                            Memory Usage
   ==================================================== =============
   Long-term stable running                             About 50 MiB
   Concurrent write to a 10-MB file from two processes  About 110 MiB
   Concurrent write to a 10-MB file from four processes About 220 MiB
   Write to a 100-GB file from a single process         About 300 MiB
   ==================================================== =============

Application Scenarios
---------------------

OBS supports the following mounting modes based on application scenarios:

-  :ref:`Using an Existing OBS Bucket Through a Static PV <cce_10_0379>`: static creation mode, where you use an existing OBS volume to create a PV and then mount storage to the workload through a PVC. This mode applies if the underlying storage is available.
-  :ref:`Using an OBS Bucket Through a Dynamic PV <cce_10_0630>`: dynamic creation mode, in which you do not need to create OBS volumes beforehand. Instead, specify a StorageClass when creating a PVC. Then, an OBS volume and PV will be created automatically. This mode applies to scenarios where no underlying storage is available.
