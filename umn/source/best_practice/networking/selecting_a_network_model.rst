:original_name: cce_bestpractice_00162.html

.. _cce_bestpractice_00162:

Selecting a Network Model
=========================

CCE uses self-proprietary, high-performance container networking add-ons to support the tunnel network, Cloud Native Network 2.0, and VPC network models.

.. caution::

   After a cluster is created, the network model cannot be changed. Exercise caution when selecting a network model.

-  **Tunnel network**: The container network is an overlay tunnel network on top of a VPC network and uses the VXLAN technology. This network model is applicable when there is no high requirements on performance. VXLAN encapsulates Ethernet packets as UDP packets for tunnel transmission. Though at some cost of performance, the tunnel encapsulation enables higher interoperability and compatibility with advanced features (such as network policy-based isolation), meeting the requirements of most applications.


   .. figure:: /_static/images/en-us_image_0000001145545261.png
      :alt: **Figure 1** Container tunnel network

      **Figure 1** Container tunnel network

-  **VPC network**: The container network uses VPC routing to integrate with the underlying network. This network model is applicable to performance-intensive scenarios. The maximum number of nodes allowed in a cluster depends on the route quota in a VPC network. Each node is assigned a CIDR block of a fixed size. VPC networks are free from tunnel encapsulation overhead and outperform container tunnel networks. In addition, as VPC routing includes routes to node IP addresses and container network segment, container pods in the cluster can be directly accessed from outside the cluster.


   .. figure:: /_static/images/en-us_image_0261818875.png
      :alt: **Figure 2** VPC network

      **Figure 2** VPC network

-  **Cloud Native Network 2.0**: The container network deeply integrates the elastic network interface (ENI) capability of VPC, uses the VPC CIDR block to allocate container addresses, and supports passthrough networking to containers through a load balancer.


   .. figure:: /_static/images/en-us_image_0000001352539924.png
      :alt: **Figure 3** Cloud Native Network 2.0

      **Figure 3** Cloud Native Network 2.0

The following table lists the differences between the network models.

.. table:: **Table 1** Networking model comparison

   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------+
   | Dimension              | Tunnel Network                                                                                                                    | VPC Network                                                                                                                                          | Cloud Native Network 2.0                                                                               |
   +========================+===================================================================================================================================+======================================================================================================================================================+========================================================================================================+
   | Core technology        | OVS                                                                                                                               | IPvlan and VPC route                                                                                                                                 | VPC ENI/sub-ENI                                                                                        |
   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------+
   | Applicable Clusters    | CCE cluster                                                                                                                       | CCE cluster                                                                                                                                          | CCE Turbo cluster                                                                                      |
   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------+
   | Network isolation      | Kubernetes native NetworkPolicy for pods                                                                                          | No                                                                                                                                                   | Pods support security group isolation.                                                                 |
   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------+
   | Passthrough networking | No                                                                                                                                | No                                                                                                                                                   | Yes                                                                                                    |
   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------+
   | IP address management  | -  The container CIDR block is allocated separately.                                                                              | -  The container CIDR block is allocated separately.                                                                                                 | The container CIDR block is divided from the VPC subnet and does not need to be allocated separately.  |
   |                        | -  CIDR blocks are divided by node and can be dynamically allocated (CIDR blocks can be dynamically added after being allocated.) | -  CIDR blocks are divided by node and statically allocated (the CIDR block cannot be changed after a node is created).                              |                                                                                                        |
   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------+
   | Performance            | Performance loss due to VXLAN encapsulation                                                                                       | No tunnel encapsulation. Cross-node packets are forwarded through VPC routers, delivering performance equivalent to that of the host network.        | The container network is integrated with the VPC network, eliminating performance loss.                |
   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------+
   | Networking scale       | A maximum of 2,000 nodes are supported.                                                                                           | By default, 200 nodes are supported.                                                                                                                 | A maximum of 2,000 nodes are supported.                                                                |
   |                        |                                                                                                                                   |                                                                                                                                                      |                                                                                                        |
   |                        |                                                                                                                                   | Each time a node is added to the cluster, a route is added to the VPC routing table. Therefore, the cluster scale is limited by the VPC route table. |                                                                                                        |
   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------+
   | Scenario               | -  Common container services                                                                                                      | -  Scenarios that have high requirements on network latency and bandwidth                                                                            | -  Scenarios that have high requirements on network latency, bandwidth, and performance                |
   |                        | -  Scenarios that do not have high requirements on network latency and bandwidth                                                  | -  Containers communicate with VMs using a microservice registration framework, such as Dubbo and CSE.                                               | -  Containers communicate with VMs using a microservice registration framework, such as Dubbo and CSE. |
   +------------------------+-----------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------+

.. important::

   #. The scale of a cluster that uses the VPC network model is limited by the custom routes of the VPC. Therefore, you need to estimate the number of required nodes before creating a cluster.
   #. The scale of a cluster that uses the Cloud Native Network 2.0 model depends on the size of the VPC subnet CIDR block selected for the network attachment definition. Before creating a cluster, evaluate the scale of your cluster.
   #. By default, VPC routing network supports direct communication between containers and hosts in the same VPC. If a peering connection policy is configured between the VPC and another VPC, the containers can directly communicate with hosts on the peer VPC. In addition, in hybrid networking scenarios such as Direct Connect and VPN, communication between containers and hosts on the peer end can also be achieved with proper planning.
