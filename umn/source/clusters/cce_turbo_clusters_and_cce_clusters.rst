:original_name: cce_01_0342.html

.. _cce_01_0342:

CCE Turbo Clusters and CCE Clusters
===================================

Comparison Between CCE Turbo Clusters and CCE Clusters
------------------------------------------------------

The following table lists the differences between CCE Turbo clusters and CCE clusters:

.. table:: **Table 1** Cluster types

   +-----------------+-----------------------------+----------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
   | Dimensions      | Sub-dimension               | CCE Turbo Cluster                                                                                                                | CCE Cluster                                                                                                                          |
   +=================+=============================+==================================================================================================================================+======================================================================================================================================+
   | Cluster         | Positioning                 | Next-generation container cluster for Cloud Native 2.0 with accelerated computing, networking, and scheduling                    | Standard cluster for common commercial use                                                                                           |
   +-----------------+-----------------------------+----------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
   |                 | Node type                   | Hybrid deployment of VMs and bare-metal servers                                                                                  | Hybrid deployment of VMs                                                                                                             |
   +-----------------+-----------------------------+----------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
   | Network         | Network model               | **Cloud Native Network 2.0**: applies to large-scale and high-performance scenarios.                                             | **Cloud-native network 1.0** for scenarios that do not require high performance or involve large-scale deployment.                   |
   |                 |                             |                                                                                                                                  |                                                                                                                                      |
   |                 |                             | Networking scale: 2000 nodes                                                                                                     | -  Tunnel network model                                                                                                              |
   |                 |                             |                                                                                                                                  | -  VPC network model                                                                                                                 |
   +-----------------+-----------------------------+----------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
   |                 | Network performance         | The VPC network and container network are flattened into one, achieving zero performance loss.                                   | The VPC network is overlaid with the container network, causing certain performance loss.                                            |
   +-----------------+-----------------------------+----------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
   |                 | Container network isolation | Pods can be directly associated with security groups to configure isolation policies for resources inside and outside a cluster. | -  Tunnel network model: Network isolation policies are supported for intra-cluster communication (by configuring network policies). |
   |                 |                             |                                                                                                                                  | -  VPC network model: Isolation is not supported.                                                                                    |
   +-----------------+-----------------------------+----------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
   | Security        | Isolation                   | -  Bare-metal server: You can select secure containers for VM-level isolation.                                                   | Common containers are deployed and isolated by Cgroups.                                                                              |
   |                 |                             | -  VM: Common containers are deployed.                                                                                           |                                                                                                                                      |
   +-----------------+-----------------------------+----------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+

QingTian Architecture
---------------------

|image1|

The QingTian architecture consists of data plane (software-hardware synergy) and management plane (Alkaid Smart Cloud Brain). The data plane innovates in five dimensions: simplified data center, diversified computing power, QingTian cards, ultra-fast engines, and simplified virtualization, to fully offload and accelerate compute, storage, networking, and security components. VMs, bare metal servers, and containers can run together. As a distributed operating system, the Alkaid Smart Cloud Brain focuses on the cloud, AI, and 5G, and provide all-domain scheduling to achieve cloud-edge-device collaboration and governance.

.. |image1| image:: /_static/images/en-us_image_0000001212924318.png
