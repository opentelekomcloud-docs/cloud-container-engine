:original_name: cce_10_0613.html

.. _cce_10_0613:

EVS Overview
============

To achieve persistent storage, CCE allows you to mount the storage volumes created from Elastic Volume Service (EVS) disks to a path of a container. When the container is migrated within an AZ, the mounted EVS volumes are also migrated. By using EVS volumes, you can mount the remote file directory of a storage system to a container so that data in the data volume is permanently preserved. Even if the container is deleted, the data in the data volume is still stored in the storage system.

.. note::

   Common I/O disks are a previous-generation product and are no longer available for creation.

EVS Disk Performance Specifications
-----------------------------------

EVS performance metrics include:

-  IOPS: the number of input/output operations performed by an EVS disk per second
-  Throughput: the amount of data read from and written into an EVS disk per second
-  I/O latency: the minimum interval between two consecutive I/O operations on an EVS disk

.. table:: **Table 1** EVS disk performance specifications

   +----------------------------------+----------------------------------------------------------------------------------------------------------------+---------------------------------------+--------------------------------------+--------------------------------------+------------------------------------+------------------------------------------+
   | Parameter                        | General Purpose SSD V2                                                                                         | Extreme SSD                           | General Purpose SSD                  | Ultra-high I/O                       | High I/O                           | Common I/O (Previous Generation Product) |
   +==================================+================================================================================================================+=======================================+======================================+======================================+====================================+==========================================+
   | Max. capacity (GiB)              | -  System disk: 1,024                                                                                          | -  System disk: 1,024                 | -  System disk: 1,024                | -  System disk: 1,024                | -  System disk: 1,024              | -  System disk: 1,024                    |
   |                                  | -  Data disk: 32,768                                                                                           | -  Data disk: 32,768                  | -  Data disk: 32,768                 | -  Data disk: 32,768                 | -  Data disk: 32,768               | -  Data disk: 32,768                     |
   +----------------------------------+----------------------------------------------------------------------------------------------------------------+---------------------------------------+--------------------------------------+--------------------------------------+------------------------------------+------------------------------------------+
   | Max. IOPS                        | 128,000                                                                                                        | 128,000                               | 20,000                               | 50,000                               | 5000                               | 2200                                     |
   +----------------------------------+----------------------------------------------------------------------------------------------------------------+---------------------------------------+--------------------------------------+--------------------------------------+------------------------------------+------------------------------------------+
   | Max. throughput (MiB/s)          | 1000                                                                                                           | 1000                                  | 250                                  | 350                                  | 150                                | 50                                       |
   +----------------------------------+----------------------------------------------------------------------------------------------------------------+---------------------------------------+--------------------------------------+--------------------------------------+------------------------------------+------------------------------------------+
   | Burst IOPS limit                 | NA                                                                                                             | 64,000                                | 8000                                 | 16,000                               | 5000                               | 2200                                     |
   +----------------------------------+----------------------------------------------------------------------------------------------------------------+---------------------------------------+--------------------------------------+--------------------------------------+------------------------------------+------------------------------------------+
   | Disk IOPS                        | Preconfigured ranging from 3000 to 128000 and must be less than or equal to 500 multiplying the capacity (GiB) | Min. (128,000, 1,800 + 50 x Capacity) | Min. (20,000, 1,800 + 12 x Capacity) | Min. (50,000, 1,800 + 50 x Capacity) | Min. (5,000, 1,800 + 8 x Capacity) | Min. (2,200, 500 + 2 x Capacity)         |
   +----------------------------------+----------------------------------------------------------------------------------------------------------------+---------------------------------------+--------------------------------------+--------------------------------------+------------------------------------+------------------------------------------+
   | Disk throughput (MiB/s)          | Preconfigured ranging from 125 to 1,000 and must be less than or equal to the IOPS divided by 4                | Min. (1,000, 120 + 0.5 x Capacity)    | Min. (250, 100 + 0.5 x Capacity)     | Min. (350, 120 + 0.5 x Capacity)     | Min. (150, 100 + 0.15 x Capacity)  | 50                                       |
   +----------------------------------+----------------------------------------------------------------------------------------------------------------+---------------------------------------+--------------------------------------+--------------------------------------+------------------------------------+------------------------------------------+
   | Single-queue access latency (ms) | 1                                                                                                              | Sub-millisecond                       | 1                                    | 1                                    | 1-3                                | 5-10                                     |
   +----------------------------------+----------------------------------------------------------------------------------------------------------------+---------------------------------------+--------------------------------------+--------------------------------------+------------------------------------+------------------------------------------+
   | API name                         | GPSSD2                                                                                                         | ESSD                                  | GPSSD                                | SSD                                  | SAS                                | SATA                                     |
   +----------------------------------+----------------------------------------------------------------------------------------------------------------+---------------------------------------+--------------------------------------+--------------------------------------+------------------------------------+------------------------------------------+

Application Scenarios
---------------------

EVS disks can be mounted in the following modes based on application scenarios:

-  :ref:`Using an Existing EVS Disk Through a Static PV <cce_10_0614>`: static creation mode, where you use an existing EVS disk to create a PV and then mount storage to the workload through a PVC. This mode applies if the underlying storage is available.
-  :ref:`Using an EVS Disk Through a Dynamic PV <cce_10_0615>`: dynamic creation mode, in which you do not need to create EVS volumes beforehand. Instead, specify a StorageClass when creating a PVC. Then, an EVS volume and PV will be created automatically. This mode applies to scenarios where no underlying storage is available.
-  :ref:`Dynamically Mounting an EVS Disk to a StatefulSet <cce_10_0616>`: available only for StatefulSets. In this mode, each pod is associated with a unique PVC and PV. After a pod is rescheduled, the original data can still be mounted to it based on the PVC name. This mode applies to StatefulSets with multiple pods.
