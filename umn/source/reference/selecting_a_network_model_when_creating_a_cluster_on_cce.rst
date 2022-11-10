:original_name: cce_bestpractice_00162.html

.. _cce_bestpractice_00162:

Selecting a Network Model When Creating a Cluster on CCE
========================================================

CCE uses high-performance container networking add-ons, which support the tunnel network and VPC network models.

.. caution::

   After a cluster is created, the network model cannot be changed. Exercise caution when selecting a network model.

-  **Tunnel network**: The container network is an overlay tunnel network on top of a VPC network and uses the VXLAN technology. This network model is applicable when there is no high requirements on performance. VXLAN encapsulates Ethernet packets as UDP packets for tunnel transmission. Though at some cost of performance, the tunnel encapsulation enables higher interoperability and compatibility with advanced features (such as network policy-based isolation), meeting the requirements of most applications.


   .. figure:: /_static/images/en-us_image_0000001223393893.png
      :alt: **Figure 1** Container tunnel network

      **Figure 1** Container tunnel network

-  **VPC network**: The container network uses VPC routing to integrate with the underlying network. This network model is applicable to performance-intensive scenarios. The maximum number of nodes allowed in a cluster depends on the route quota in a VPC network. Each node is assigned a CIDR block of a fixed size. VPC networks are free from tunnel encapsulation overhead and outperform container tunnel networks. In addition, as VPC routing includes routes to node IP addresses and the container CIDR block, container pods in the cluster can be directly accessed from outside the cluster.


   .. figure:: /_static/images/en-us_image_0000001178034108.png
      :alt: **Figure 2** VPC network

      **Figure 2** VPC network

The following table lists the differences between the network models.

.. table:: **Table 1** Network comparison

   +------------------------------+----------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+
   | Dimension                    | Tunnel Network                                                                   | VPC Network                                                                                                    |
   +==============================+==================================================================================+================================================================================================================+
   | Core component               | OVS                                                                              | IPVlan                                                                                                         |
   +------------------------------+----------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+
   | Applicable clusters          | Hybrid cluster                                                                   | Hybrid cluster                                                                                                 |
   |                              |                                                                                  |                                                                                                                |
   |                              | VM cluster                                                                       | VM cluster                                                                                                     |
   +------------------------------+----------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+
   | Support for network policies | Yes                                                                              | No                                                                                                             |
   |                              |                                                                                  |                                                                                                                |
   | (networkpolicy)              |                                                                                  |                                                                                                                |
   +------------------------------+----------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+
   | Support for ENI              | No                                                                               | Yes. The container network is deeply integrated with the VPC network, and ENI is used for pods to communicate. |
   +------------------------------+----------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+
   | IP address management        | IP addresses can be migrated.                                                    | -  Each node is allocated with a small subnet.                                                                 |
   |                              |                                                                                  | -  A static route is added on the VPC router with the next hop set to the node IP address.                     |
   +------------------------------+----------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+
   | Network performance          | Performance loss due to VXLAN tunnel encapsulation                               | -  No performance loss as no tunnel encapsulation is required; performance comparable to bare metal networks   |
   |                              |                                                                                  | -  Data forwarded across nodes through the VPC router                                                          |
   +------------------------------+----------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+
   | Networking scale             | A maximum of 2,000 nodes are supported.                                          | Limited by the VPC route table.                                                                                |
   +------------------------------+----------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+
   | External dependency          | None                                                                             | Static route table of the VPC router                                                                           |
   +------------------------------+----------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+
   | Application scenarios        | -  Common container service scenarios                                            | -  Scenarios that have high requirements on network latency and bandwidth                                      |
   |                              | -  Scenarios that do not have high requirements on network latency and bandwidth | -  Containers can communicate with VMs using a microservice registration framework, such as Dubbo and CSE.     |
   +------------------------------+----------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+

.. important::

   #. The actual cluster scale is limited by the quota of custom routes of the VPC. Therefore, estimate the number of required nodes before creating a VPC.
   #. By default, the VPC network model supports direct communication between containers and hosts in the same VPC. If a peering connection policy is configured between the VPC and another VPC, the containers can directly communicate with hosts on the peer VPC. In addition, in hybrid networking scenarios such as Direct Connect and VPN, communication between containers and hosts on the peer end can also be achieved with proper planning.
