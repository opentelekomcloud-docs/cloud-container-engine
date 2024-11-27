:original_name: cce_productdesc_0008.html

.. _cce_productdesc_0008:

Related Services
================

CCE works with the following cloud services and requires permissions to access them.


.. figure:: /_static/images/en-us_image_0000001897903549.png
   :alt: **Figure 1** Relationships between CCE and other services

   **Figure 1** Relationships between CCE and other services

Relationships Between CCE and Other Services
--------------------------------------------

.. table:: **Table 1** Relationships between CCE and other services

   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Service                           | Relationship                                                                                                                                                                                                                                                                      |
   +===================================+===================================================================================================================================================================================================================================================================================+
   | ECS                               | An ECS with multiple EVS disks is a node in CCE. You can choose ECS specifications during node creation.                                                                                                                                                                          |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | VPC                               | For security reasons, all clusters created by CCE must run in VPCs. When creating a namespace, create a VPC or bind an existing VPC to the namespace so all containers in the namespace will run in this VPC.                                                                     |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | ELB                               | CCE works with ELB to load balance a workload's access requests across multiple pods.                                                                                                                                                                                             |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | NAT Gateway                       | The NAT Gateway service provides source network address translation (SNAT), which translates private IP addresses to a public IP address by binding an elastic IP address (EIP) to the gateway.                                                                                   |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SWR                               | An image repository is used to store and manage Docker images.                                                                                                                                                                                                                    |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | EVS                               | EVS disks can be attached to cloud servers and scaled to a higher capacity whenever needed.                                                                                                                                                                                       |
   |                                   |                                                                                                                                                                                                                                                                                   |
   |                                   | An ECS with multiple EVS disks is a node in CCE. You can choose ECS specifications during node creation.                                                                                                                                                                          |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | OBS                               | OBS provides stable, secure, cost-efficient, and object-based cloud storage for data of any size. With OBS, you can create, modify, and delete buckets, as well as uploading, downloading, and deleting objects.                                                                  |
   |                                   |                                                                                                                                                                                                                                                                                   |
   |                                   | CCE allows you to create an OBS volume and attach it to a path inside a container.                                                                                                                                                                                                |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS                               | SFS is a shared, fully managed file storage service. Compatible with the Network File System protocol, SFS file systems can elastically scale up to petabytes, thereby ensuring top performance of data-intensive and bandwidth-intensive applications.                           |
   |                                   |                                                                                                                                                                                                                                                                                   |
   |                                   | You can use SFS file systems as persistent storage for containers and attach the file systems to containers when creating a workload.                                                                                                                                             |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | AOM                               | AOM collects container log files in formats like .log from CCE and dumps them to AOM. On the AOM console, you can easily query and view log files. In addition, AOM monitors CCE resource usage. You can define metric thresholds for CCE resource usage to trigger auto scaling. |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Cloud Trace Service (CTS)         | CTS records operations on your cloud resources, allowing you to obtain, audit, and backtrack resource operation requests initiated from the management console or open APIs as well as responses to these requests.                                                               |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
