:original_name: cce_10_0342.html

.. _cce_10_0342:

CCE Turbo Clusters and CCE Clusters
===================================

Comparison Between CCE Turbo Clusters and CCE Clusters
------------------------------------------------------

The following table lists the differences between CCE Turbo clusters and CCE clusters.

.. table:: **Table 1** Cluster types

   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
   | Category        | Subcategory                 | CCE Turbo Cluster                                                                                                              | CCE Cluster                                                                            |
   +=================+=============================+================================================================================================================================+========================================================================================+
   | Cluster         | Positioning                 | Next-gen container cluster designed for Cloud Native 2.0, with accelerated computing, networking, and scheduling               | Standard cluster for common commercial use                                             |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
   |                 | Node type                   | Deployment of VMs                                                                                                              | Deployment of VMs                                                                      |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
   | Networking      | Model                       | **Cloud Native Network 2.0**: applies to large-scale and high-performance scenarios.                                           | **Cloud Native Network 1.0**: applies to common, smaller-scale scenarios.              |
   |                 |                             |                                                                                                                                |                                                                                        |
   |                 |                             | Max networking scale: 2,000 nodes                                                                                              | -  Tunnel network model                                                                |
   |                 |                             |                                                                                                                                | -  VPC network model                                                                   |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
   |                 | Performance                 | Flattens the VPC network and container network into one, achieving zero performance loss.                                      | Overlays the VPC network with the container network, causing certain performance loss. |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
   |                 | Container network isolation | Associates pods with security groups. Unifies security isolation in and out the cluster via security groups' network policies. | -  Tunnel network model: supports network policies for intra-cluster communications.   |
   |                 |                             |                                                                                                                                | -  VPC network model: supports no isolation.                                           |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
   | Security        | Isolation                   | -  VM: runs common containers, isolated by cgroups.                                                                            | Runs common containers, isolated by cgroups.                                           |
   +-----------------+-----------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
