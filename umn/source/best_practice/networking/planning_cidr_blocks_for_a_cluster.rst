:original_name: cce_bestpractice_00004.html

.. _cce_bestpractice_00004:

Planning CIDR Blocks for a Cluster
==================================

Before creating a cluster on CCE, determine the number of VPCs, number of subnets, container CIDR blocks, and Services for access based on service requirements.

This topic describes the addresses in a CCE cluster in a VPC and how to plan CIDR blocks.

Basic Concepts
--------------

-  **VPC CIDR Block**

   Virtual Private Cloud (VPC) enables you to provision logically isolated, configurable, and manageable virtual networks for cloud servers, cloud containers, and cloud databases. You can configure IP address ranges, subnets, security groups, and more in a VPC, and assign EIPs and bandwidth to build your service system.

-  **Subnet CIDR Block**

   A subnet is a network that manages ECS network planes. It supports IP address management and DNS. ECSs in a subnet are assigned IP addresses from this subnet.


   .. figure:: /_static/images/en-us_image_0000002484118422.png
      :alt: **Figure 1** VPC CIDR block architecture

      **Figure 1** VPC CIDR block architecture

   By default, ECSs in all subnets of the same VPC can communicate with one another, but ECSs in different VPCs cannot.

   You can create a VPC peering connection to enable ECSs in different VPCs to communicate with each other.

-  **Container (Pod) CIDR Block**

   Pod is a Kubernetes concept. Each pod has an IP address.

   When creating a cluster on CCE, you can specify the pod (container) CIDR block. The container CIDR block cannot overlap with the VPC CIDR block or subnet CIDR block. For example, if the subnet CIDR block is 192.168.0.0/16, the container CIDR block of the cluster cannot be 192.168.0.0/18 or 192.168.64.0/18 because these addresses are covered by 192.168.0.0/16.

-  **Container Subnet** (Only for CCE Turbo Clusters)

   In a CCE Turbo cluster, a container is assigned an IP address from the CIDR block of a VPC. The container subnet can overlap with the subnet CIDR block. Note that the subnet you select determines the maximum number of pods in the cluster.

-  **Service CIDR Block**

   Service is also a Kubernetes concept. Each Service has an address. When creating a cluster on CCE, you can specify the Service CIDR block. Similarly, the Service CIDR block cannot overlap with the VPC CIDR block, subnet CIDR block, or container CIDR block. The Service CIDR block can be used only within a cluster.

Notes and Constraints
---------------------

To access a CCE cluster through a VPN, the VPN cannot conflict with the VPC CIDR block where the cluster resides and the container CIDR block.

Single-VPC Single-Cluster Scenarios
-----------------------------------

**CCE Clusters**: include clusters in VPC network model and container tunnel network model. :ref:`Figure 2 <cce_bestpractice_00004__en-us_topic_0099587154_fig15791152874920>` shows the CIDR block planning of a cluster.

-  VPC CIDR block: specifies the VPC CIDR block where the cluster resides. The size of this CIDR block affects the maximum number of nodes that can be created in the cluster.
-  Subnet CIDR block: specifies the subnet CIDR block where the node in the cluster resides. The subnet CIDR block is included in the VPC CIDR block. Different nodes in the same cluster can be allocated to different subnet CIDR blocks.
-  Container CIDR block: cannot overlap with the VPC CIDR block or subnet CIDR block.
-  Service CIDR block: cannot overlap with the VPC CIDR block, subnet CIDR block, or container CIDR block.

.. _cce_bestpractice_00004__en-us_topic_0099587154_fig15791152874920:

.. figure:: /_static/images/en-us_image_0000002484118416.png
   :alt: **Figure 2** Network CIDR block planning in single-VPC single-cluster scenarios (CCE cluster)

   **Figure 2** Network CIDR block planning in single-VPC single-cluster scenarios (CCE cluster)

:ref:`Figure 3 <cce_bestpractice_00004__fig19746213285>` shows the CIDR block planning for a **CCE Turbo cluster** (Cloud Native Network 2.0).

-  VPC CIDR block: specifies the VPC CIDR block where the cluster resides. The size of this CIDR block affects the maximum number of nodes that can be created in the cluster.
-  Subnet CIDR block: specifies the subnet CIDR block where the node in the cluster resides. The subnet CIDR block is included in the VPC CIDR block. Different nodes in the same cluster can be allocated to different subnet CIDR blocks.
-  Container subnet CIDR block: The container subnet is included in the VPC CIDR block and can overlap with the subnet CIDR block or even be the same as the subnet CIDR block. Note that the container subnet size determines the maximum number of containers in the cluster because IP addresses in the VPC are directly allocated to containers. Set a larger IP address segment for the container subnet to prevent insufficient container IP addresses.
-  Service CIDR block: cannot overlap with the subnet CIDR block or the container CIDR block.

.. _cce_bestpractice_00004__fig19746213285:

.. figure:: /_static/images/en-us_image_0000002516198379.png
   :alt: **Figure 3** Network CIDR block planning in single-VPC single-cluster scenarios (CCE Turbo cluster)

   **Figure 3** Network CIDR block planning in single-VPC single-cluster scenarios (CCE Turbo cluster)

Single-VPC Multi-Cluster Scenarios
----------------------------------

**VPC network model**

Pod packets are forwarded through VPC routes. CCE automatically configures a routing table on the VPC routes to each container CIDR block. The network scale is limited by the VPC route table. :ref:`Figure 4 <cce_bestpractice_00004__en-us_topic_0099587154_fig69527530400>` shows the CIDR block planning of the cluster.

-  VPC CIDR Block: specifies the VPC CIDR block where the cluster resides. The size of this CIDR block affects the maximum number of nodes that can be created in the cluster.
-  Subnet CIDR Block: The subnet CIDR block in each cluster cannot overlap with the container CIDR block.
-  Container CIDR Block: If multiple VPC network model clusters exist in a single VPC, the container CIDR blocks of all clusters cannot overlap because the clusters use the same routing table. In this case, if the node security group allows container CIDR block from the peer cluster, pods in one cluster can directly access pods in another cluster through the pod IP addresses.
-  Service CIDR Block: can be used only in clusters. Therefore, the Service CIDR blocks of different clusters can overlap, but cannot overlap with the cluster subnet CIDR block and container subnet CIDR block.

.. _cce_bestpractice_00004__en-us_topic_0099587154_fig69527530400:

.. figure:: /_static/images/en-us_image_0000002484118428.png
   :alt: **Figure 4** VPC network - multi-cluster scenario

   **Figure 4** VPC network - multi-cluster scenario

**Tunnel network model**

Though at some cost of performance, the tunnel encapsulation enables higher interoperability and compatibility with advanced features (such as network policy-based isolation), meeting the requirements of most applications. :ref:`Figure 5 <cce_bestpractice_00004__en-us_topic_0099587154_fig8672112184219>` shows the CIDR block planning of the cluster.

-  VPC CIDR Block: specifies the VPC CIDR block where the cluster resides. The size of this CIDR block affects the maximum number of nodes that can be created in the cluster.
-  Subnet CIDR Block: The subnet CIDR block in each cluster cannot overlap with the container CIDR block.
-  Container CIDR Block: The container CIDR blocks of all clusters can overlap. In this case, pods in different clusters cannot be directly accessed through pod IP addresses. Services are required for accessing pods in different clusters. The LoadBalancer Services are recommended.
-  Service CIDR Block: can be used only in clusters. Therefore, the Service CIDR blocks of different clusters can overlap, but cannot overlap with the cluster CIDR block and container subnet CIDR block.

.. _cce_bestpractice_00004__en-us_topic_0099587154_fig8672112184219:

.. figure:: /_static/images/en-us_image_0000002516078397.png
   :alt: **Figure 5** Tunnel network - multi-cluster scenario

   **Figure 5** Tunnel network - multi-cluster scenario

**Cloud Native 2.0 network model** (CCE Turbo Clusters)

In this mode, container IP addresses are allocated from the VPC CIDR block. ELB passthrough networking is supported to direct access requests to containers. Security groups and multiple types of VPC networks can be bound to deliver high performance.

-  VPC CIDR Block: specifies the VPC CIDR block where the cluster resides. In a CCE Turbo cluster, the CIDR block size affects the total number of nodes and containers that can be created in the cluster.
-  Subnet CIDR Block: There is no special restriction on the subnet CIDR blocks in CCE Turbo clusters.
-  Container Subnet: The CIDR block of the container subnet is included in the VPC CIDR block. Container subnets in different clusters can overlap with each other or overlap with the subnet CIDR block. However, you are advised to stagger the container CIDR blocks of different clusters and ensure that the container subnet CIDR blocks have sufficient IP addresses. In this case, if the elastic network interface security group of the cluster allows the container CIDR block of the peer cluster, pods in different clusters can directly access each other through IP addresses.
-  Service CIDR Block: can be used only in clusters. Therefore, the Service CIDR blocks of different clusters can overlap, but cannot overlap with the cluster CIDR block and container subnet CIDR block.


.. figure:: /_static/images/en-us_image_0000002483958434.png
   :alt: **Figure 6** Cloud Native 2.0 network - multi-cluster scenario

   **Figure 6** Cloud Native 2.0 network - multi-cluster scenario

**Clusters using different networks**

When a VPC contains clusters created with different network models, comply with the following rules when creating a cluster:

-  VPC CIDR Block: In this scenario, all clusters are located in the same VPC CIDR block. Ensure that there are sufficient available IP addresses in the VPC.
-  Subnet CIDR Block: Ensure that the subnet CIDR block does not overlap with the container CIDR block. Even in some scenarios (for example, coexistence with CCE Turbo clusters), the subnet CIDR block can overlap with the container (subnet) CIDR block. However, this is not recommended.
-  Container CIDR Block: Ensure that the container CIDR blocks of clusters in **VPC network model** do not overlap.
-  Service CIDR Block: The Service CIDR blocks of all clusters can overlap, but cannot overlap with the subnet CIDR block and container CIDR block of the cluster.

Cross-VPC Cluster Interconnection
---------------------------------

If VPCs cannot communicate with each other, a VPC peering connection is used to ensure communication between VPCs. When two VPC networks are interconnected, you can configure the packets to be sent to the peer VPC in the route table.

**Clusters using VPC networks**

To allow clusters that use VPC networks to access each other across VPCs, add routes to the two ends of the VPC peering after a VPC peering connection is created.


.. figure:: /_static/images/en-us_image_0000002484118432.png
   :alt: **Figure 7** VPC network - VPC interconnection scenario

   **Figure 7** VPC network - VPC interconnection scenario

When creating a VPC peering connection between containers across VPCs, pay attention to the following points:

-  The VPC to which the clusters belong must not overlap. In each cluster, the subnet CIDR block cannot overlap with the container CIDR block.
-  The container CIDR blocks of clusters at both ends cannot overlap, but the Service CIDR blocks can.
-  If the request end cluster uses the VPC network, check whether the node security group in the target cluster allows the container CIDR block of the request end cluster. If yes, pods in one cluster can directly access pods in another cluster through pod IP addresses. Similarly, if nodes running in the clusters at the two ends of the VPC peering connection need to access each other, the node security group must allow the VPC CIDR block of the peer cluster.
-  You need to add routes for accessing the peer VPC CIDR block to the VPC route tables at both ends. For example, you need to add a route for accessing the CIDR block of VPC 2 to the route table of VPC 1, and add a route for accessing VPC 1 to the route table of VPC 2.

   -  **Add the VPC CIDR block of the peer cluster**: After the route of the VPC CIDR block is added, a pod in a cluster can access a node in another cluster, for example, through the port of a NodePort Service.
   -  **Add peer container subnet CIDR block**: After the route of the container subnet CIDR block is added, a pod can directly access pods in another other cluster through container IP addresses.

**Clusters using tunnel networks**

To allow clusters that use tunnel networks to access each other across VPCs, add routes to the two ends of the VPC peering after a VPC peering connection is created.


.. figure:: /_static/images/en-us_image_0000002483958456.png
   :alt: **Figure 8** Tunnel network - VPC interconnection scenario

   **Figure 8** Tunnel network - VPC interconnection scenario

Pay attention to the following:

-  The VPC CIDR blocks of the clusters at the two ends must not overlap.
-  The container CIDR blocks of all clusters can overlap, so do the Service CIDR blocks.
-  If the request end cluster uses the tunnel network, check whether the node security group in the target cluster allows the VPC CIDR block (including the node subnets) of the request end cluster. If yes, nodes in one cluster can access nodes in another cluster. However, pods in different clusters cannot be directly accessed using pod IP addresses. Access between pods in different clusters requires Services. The LoadBalancer Services are recommended.
-  You need to add routes for accessing the peer network CIDR block to the VPC routing tables at both ends. For example, you need to add a route for accessing the CIDR block of VPC 2 to the route table of VPC 1, and add a route for accessing VPC 1 to the route table of VPC 2. After the route of the VPC CIDR block is added, the pod can access a node in another cluster, for example, through the port of a NodePort Service.

**Clusters using Cloud Native 2.0 networks (CCE Turbo clusters)**

After creating a VPC peering connection, add routes of the VPC peering connection to both ends so that the two VPCs can communicate with each other. Pay attention to the following:

-  The VPC CIDR blocks of the clusters at the two ends must not overlap.
-  If the request end cluster uses the Cloud Native 2.0 network, check whether the elastic network interface security group (named in the format of *{Cluster name}*\ **-cce-eni-**\ *{Random ID}*) of the target cluster allows the VPC CIDR block (including the node subnets and container CIDR block) of the request end cluster. If yes, pods in one cluster can directly access pods in another cluster through the pod IP addresses. Similarly, if nodes in the clusters at the two ends of the VPC peering need to access each other, allow the VPC CIDR block of the peer cluster in the node security group (named in the format of *{Cluster name}*\ **-cce-node-**\ *{Random ID}*).
-  You need to add routes for accessing the peer network CIDR block to the VPC routing tables at both ends. For example, you need to add a route for accessing the CIDR block of VPC 2 to the route table of VPC 1, and add a route for accessing VPC 1 to the route table of VPC 2. After the route of the VPC CIDR block is added, the pod can access pod IP addresses or nodes in another cluster.

**Clusters using different networks**

If clusters using different networks need to communicate with each other across VPCs, every one of them may serve as the request end or destination end. Pay attention to the following:

-  The VPC CIDR block to which the cluster belongs cannot overlap with the VPC CIDR block of the peer cluster.

-  Cluster subnet CIDR blocks cannot overlap with the container CIDR blocks.

-  Container CIDR blocks in different clusters cannot overlap with each other.

-  If pods or nodes in different clusters need to access each other, the security groups of the clusters on both ends must allow the corresponding CIDR blocks based on the following rules:

   -  If the request end cluster uses the VPC network, the node security group of the target cluster must allow the VPC CIDR block (including the node subnets and container CIDR block) of the request end cluster.
   -  If the request end cluster uses the tunnel network, the node security group of the target cluster must allow the VPC CIDR block (including the node subnets) of the request end cluster.
   -  If the request end cluster uses the Cloud Native 2.0 network, the elastic network interface security group and node security group of the target cluster must allow the VPC CIDR block (including node subnets and container CIDR block) of the request end cluster.

-  You need to add routes for accessing the peer network CIDR block to the VPC routing tables at both ends. For example, you need to add a route for accessing the CIDR block of VPC 2 to the route table of VPC 1, and add a route for accessing VPC 1 to the route table of VPC 2. After the route of the VPC CIDR block is added, the pod can access a node in another cluster, for example, through the port of a NodePort Service.

   If a cluster uses the VPC network, the VPC routing tables at both ends must contain its container CIDR block. After the route of the container subnet CIDR block is added, the pod can directly access pods in another cluster through container IP addresses.

Cloud Service Access from a CCE Cluster
---------------------------------------

CCE clusters can be used with other cloud services, such as RDS and Kafka. To ensure stable communication and avoid network conflicts, a cluster's network plan must be carefully designed. Specifically, the network CIDR blocks used by other services must not overlap with those used by the cluster. The following requirements apply based on the cluster's network:

-  **For a cluster that uses a VPC network**: The network CIDR blocks of other cloud services must not overlap with the container or Service CIDR block within the cluster.
-  **For a cluster that uses a tunnel network**: The network CIDR blocks of other cloud services must not overlap with the container or Service CIDR block within the cluster.
-  **For a CCE Turbo cluster that uses a Cloud Native 2.0 network**: The network CIDR blocks of other cloud services must not overlap with the Service CIDR block within the cluster.

.. caution::

   -  In addition to the network CIDR block planning, it is essential to verify whether the target cloud service supports external access and whether the relevant security group rules or ACL configurations permit communication.
   -  If the CCE cluster and the cloud services reside in different VPCs, their VPC CIDR blocks must remain non-overlapping. To enable communication between the cluster and external services across VPCs, a VPC peering connection can be established.

VPC-IDC Scenarios
-----------------

Similar to the VPC interconnection scenario, some CIDR blocks in the VPC are routed to the IDC. The pod IP addresses of CCE clusters cannot overlap with the addresses within these CIDR blocks. To access the pod IP addresses in the cluster from the IDC, configure the route table to the private line for the IDC.
