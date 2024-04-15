:original_name: cce_10_0613.html

.. _cce_10_0613:

Overview
========

To achieve persistent storage, CCE allows you to mount the storage volumes created from Elastic Volume Service (EVS) disks to a path of a container. When the container is migrated within an AZ, the mounted EVS volumes are also migrated. By using EVS volumes, you can mount the remote file directory of a storage system to a container so that data in the data volume is permanently preserved. Even if the container is deleted, the data in the data volume is still stored in the storage system.

EVS Disk Performance Specifications
-----------------------------------

EVS performance metrics include:

-  IOPS: number of read/write operations performed by an EVS disk per second
-  Throughput: amount of data read from and written into an EVS disk per second
-  Read/write I/O latency: minimum interval between two consecutive read/write operations on an EVS disk

.. table:: **Table 1** EVS disk performance specifications

   +----------------------------------+-------------------------------------+-----------------------------------+---------------------------------+
   | Parameter                        | Ultra-high I/O                      | High I/O                          | Common I/O                      |
   +==================================+=====================================+===================================+=================================+
   | Max. capacity (GiB)              | -  System disk: 1,024               | -  System disk: 1,024             | -  System disk: 1,024           |
   |                                  | -  Data disk: 32,768                | -  Data disk: 32,768              | -  Data disk: 32,768            |
   +----------------------------------+-------------------------------------+-----------------------------------+---------------------------------+
   | Max. IOPS                        | 50,000                              | 5000                              | 2200                            |
   +----------------------------------+-------------------------------------+-----------------------------------+---------------------------------+
   | Max. throughput (MiB/s)          | 350                                 | 150                               | 50                              |
   +----------------------------------+-------------------------------------+-----------------------------------+---------------------------------+
   | Burst IOPS limit                 | 16,000                              | 5000                              | 2200                            |
   +----------------------------------+-------------------------------------+-----------------------------------+---------------------------------+
   | Disk IOPS                        | Min. (50,000, 1800 + 50 x Capacity) | Min. (5000, 1800 + 8 x Capacity)  | Min. (2200, 500 + 2 x Capacity) |
   +----------------------------------+-------------------------------------+-----------------------------------+---------------------------------+
   | Disk throughput (MiB/s)          | Min. (350, 120 + 0.5 x Capacity)    | Min. (150, 100 + 0.15 x Capacity) | 50                              |
   +----------------------------------+-------------------------------------+-----------------------------------+---------------------------------+
   | Single-queue access latency (ms) | 1                                   | 1-3                               | 5-10                            |
   +----------------------------------+-------------------------------------+-----------------------------------+---------------------------------+
   | API name                         | SSD                                 | SAS                               | SATA                            |
   +----------------------------------+-------------------------------------+-----------------------------------+---------------------------------+

Application Scenarios
---------------------

EVS disks can be mounted in the following modes based on application scenarios:

-  :ref:`Using an Existing EVS Disk Through a Static PV <cce_10_0614>`: static creation mode, where you use an existing EVS disk to create a PV and then mount storage to the workload through a PVC. This mode applies to scenarios where the underlying storage is available.
-  :ref:`Using an EVS Disk Through a Dynamic PV <cce_10_0615>`: dynamic creation mode, where you do not need to create EVS volumes in advance. Instead, specify a StorageClass during PVC creation and an EVS disk and a PV will be automatically created. This mode applies to scenarios where no underlying storage is available.
-  :ref:`Dynamically Mounting an EVS Disk to a StatefulSet <cce_10_0616>`: Only StatefulSets support this mode. Each pod is associated with a unique PVC and PV. After a pod is rescheduled, the original data can still be mounted to it based on the PVC name. This mode applies to StatefulSets with multiple pods.
