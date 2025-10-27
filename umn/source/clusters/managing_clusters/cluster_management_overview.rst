:original_name: cce_10_0962.html

.. _cce_10_0962:

Cluster Management Overview
===========================

Efficient, stable operation of containerized applications relies on proper management of CCE standard or Turbo clusters. CCE standard and Turbo clusters offer comprehensive management capabilities, including cluster configuration, resource scheduling, security control, and lifecycle management. Configure, optimize, and manage clusters according to your service needs to ensure stable, high-performance operation of containerized applications.

Cluster Configurations and Resource Management
----------------------------------------------

Cluster configuration and resource management enable flexible parameter adjustments and dynamic resource allocation. This allows you to optimize cluster performance and efficiently utilize resources based on service requirements. For details, see :ref:`Table 1 <cce_10_0962__table2913225175110>`.

.. _cce_10_0962__table2913225175110:

.. table:: **Table 1** Cluster configuration and resource management features

   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Feature                                                      | Description                                                                                                                                                                                                                                                                                                                                        |
   +==============================================================+====================================================================================================================================================================================================================================================================================================================================================+
   | :ref:`Modifying Cluster Configurations <cce_10_0213>`        | Cluster configuration parameters define node behavior, resource allocation, communication rules, and scaling policies in a distributed system. They affect the cluster's performance, stability, scalability, and fault tolerance. You can customize the core components of a CCE standard or Turbo cluster by adjusting these parameter settings. |
   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Enabling Overload Control for a Cluster <cce_10_0602>` | Overload control dynamically limits concurrent external requests based on the resource pressure of the master node, ensuring the stability and reliability of both the master node and the cluster.                                                                                                                                                |
   |                                                              |                                                                                                                                                                                                                                                                                                                                                    |
   |                                                              | In a CCE standard or Turbo cluster, you can enable cluster overload control, monitor cluster overload, and configure overload alarms to mitigate the risk of cluster overload.                                                                                                                                                                     |
   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Changing a Cluster Scale <cce_10_0403>`                | A cluster scale specifies the maximum number of nodes a cluster can manage. If the current cluster scale does not meet your requirements, you can change it to handle changing workloads.                                                                                                                                                          |
   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Cluster Security and Access Control
-----------------------------------

A security group is a virtual firewall that controls inbound and outbound network traffic for a cluster. You can modify the default security group rules of cluster nodes to enhance network security based on your needs. For details, see :ref:`Changing the Default Security Group of a Node <cce_10_0426>`.
