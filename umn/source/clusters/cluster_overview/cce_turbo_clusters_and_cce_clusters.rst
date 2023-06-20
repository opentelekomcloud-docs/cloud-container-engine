:original_name: cce_10_0342.html

.. _cce_10_0342:

CCE Turbo Clusters and CCE Clusters
===================================

Comparison Between CCE Turbo Clusters and CCE Clusters
------------------------------------------------------

The following table lists the differences between CCE Turbo clusters and CCE clusters:

.. table:: **Table 1** Cluster types

   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------+
   | Dimension       | Sub-dimension               | CCE Turbo cluster                                                                                                              | CCE cluster                                                                                    |
   +=================+=============================+================================================================================================================================+================================================================================================+
   | Cluster         | Positioning                 | Next-gen container cluster, with accelerated computing, networking, and scheduling. Designed for Cloud Native 2.0              | Standard cluster for common commercial use                                                     |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------+
   |                 | Node type                   | Deployment of VMs                                                                                                              | Hybrid deployment of VMs and bare metal servers                                                |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------+
   | Network         | Model                       | **Cloud Native Network 2.0**: applies to large-scale and high-performance scenarios.                                           | **Cloud-native network 1.0**: applies to common, smaller-scale scenarios.                      |
   |                 |                             |                                                                                                                                |                                                                                                |
   |                 |                             | Max networking scale: 2,000 nodes                                                                                              | -  Container tunnel network model                                                              |
   |                 |                             |                                                                                                                                | -  VPC network model                                                                           |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------+
   |                 | Network performance         | Flattens the VPC network and container network into one. No performance loss.                                                  | Overlays the VPC network with the container network, causing certain performance loss.         |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------+
   |                 | Container network isolation | Associates pods with security groups. Unifies security isolation in and out the cluster via security groups' network policies. | -  Container tunnel network model: supports network policies for intra-cluster communications. |
   |                 |                             |                                                                                                                                | -  VPC network model: supports no isolation.                                                   |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------+
   | Security        | Isolation                   | -  VM: runs common containers, isolated by cgroups.                                                                            | Common containers are deployed and isolated by cgroups.                                        |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------+

QingTian Architecture
---------------------

|image1|

The QingTian architecture consists of data plane (software-hardware synergy) and management plane (Alkaid Smart Cloud Brain). The data plane innovates in five dimensions: simplified data center, diversified computing power, QingTian cards, ultra-fast engines, and simplified virtualization, to fully offload and accelerate compute, storage, networking, and security components. VMs, bare metal servers, and containers can run together. As a distributed operating system, the Alkaid Smart Cloud Brain focuses on the cloud, AI, and 5G, and provide all-domain scheduling to achieve cloud-edge-device collaboration and governance.

.. |image1| image:: /_static/images/en-us_image_0000001517743452.png
