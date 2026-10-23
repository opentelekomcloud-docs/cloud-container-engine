:original_name: cce_bestpractice_00004.html

.. _cce_bestpractice_00004:

Planning CIDR Blocks for a Cluster
==================================

Before creating a cluster on CCE, determine the number of VPCs, the number of subnets, pod CIDR block, and Service CIDR block based on service requirements.

This section describes the IP addresses in a CCE cluster in a VPC and how to plan CIDR blocks.

Basic Concepts
--------------

-  **VPC CIDR Block**

   A VPC enables you to provision logically isolated, configurable, and manageable virtual networks for cloud servers, cloud containers, and cloud databases. You can configure CIDR blocks, subnets, security groups, and more in a VPC, and assign EIPs and bandwidth to your service systems.

-  **Subnet CIDR Block**

   A subnet manages ECS network planes. It supports IP address management and DNS. ECSs in a subnet are assigned IP addresses from this subnet.


   .. figure:: /_static/images/en-us_image_0000002661850887.png
      :alt: **Figure 1** VPC CIDR block architecture

      **Figure 1** VPC CIDR block architecture

   By default, ECSs in all subnets of the same VPC can communicate with one another, but ECSs in different VPCs cannot.

   You can create a VPC peering connection to enable ECSs in different VPCs to communicate with each other.

-  **Container (Pod) CIDR Block**

   Pod is a Kubernetes concept. Each pod has an IP address.

   When creating a cluster on CCE, you can specify the pod CIDR block. The pod CIDR block cannot overlap with the VPC CIDR block or subnet CIDR block. For example, if the subnet CIDR block is 192.168.0.0/16, the pod CIDR block of the cluster cannot be 192.168.0.0/18 or 192.168.64.0/18 because these CIDR blocks are covered by 192.168.0.0/16.

-  **Container Subnet** (Only for CCE Turbo Clusters)

   In a CCE Turbo cluster, a container is assigned an IP address from the VPC CIDR block. The container subnet can overlap with the subnet CIDR block. Note that the subnet you select determines the maximum number of pods in the cluster.

-  **Service CIDR Block**

   Service is also a Kubernetes concept. Each Service has an IP address. When creating a cluster on CCE, you can specify the Service CIDR block. Similarly, the Service CIDR block cannot overlap with the VPC CIDR block, subnet CIDR block, or pod CIDR block. The Service CIDR block can be used only within a cluster.

Notes and Constraints
---------------------

To access a CCE cluster through a VPN, ensure that the VPN does not conflict with the cluster's VPC CIDR block or the pod CIDR block.

Single-VPC Single-Cluster Scenarios
-----------------------------------

**CCE standard clusters** can use the VPC or container tunnel networks. :ref:`Figure 2 <cce_bestpractice_00004__en-us_topic_0099587154_fig15791152874920>` shows the CIDR block planning of a CCE standard cluster.

-  A VPC CIDR block is where the cluster resides. The size of this CIDR block affects the maximum number of nodes that can be created in the cluster.
-  A subnet CIDR block is where some nodes in the cluster reside. It is included in the VPC CIDR block. Nodes in the same cluster can be allocated with different subnet CIDR blocks.
-  A pod CIDR block cannot overlap with the VPC CIDR block or subnet CIDR block.
-  A Service CIDR block cannot overlap with the VPC CIDR block, subnet CIDR block, or pod CIDR block.

.. _cce_bestpractice_00004__en-us_topic_0099587154_fig15791152874920:

.. figure:: /_static/images/en-us_image_0000002662010827.png
   :alt: **Figure 2** CIDR block planning for a CCE standard cluster

   **Figure 2** CIDR block planning for a CCE standard cluster

:ref:`Figure 3 <cce_bestpractice_00004__fig19746213285>` shows the CIDR block planning for a **CCE Turbo cluster** that uses a Cloud Native 2.0 network.

-  A VPC CIDR block is where the cluster resides. The size of this CIDR block affects the maximum number of nodes that can be created in the cluster.
-  A subnet CIDR block is where some nodes in the cluster reside. It is included in the VPC CIDR block. Nodes in the same cluster can be allocated with different subnet CIDR blocks.
-  The pod CIDR block is allocated from the VPC CIDR block and may overlap with the subnet CIDR block, or even be identical to it. Note that the pod CIDR block size determines the maximum number of pods in the cluster, as IP addresses are allocated directly to pods from the VPC CIDR block. Set a larger CIDR block for the container subnet to ensure there are enough IP addresses for containers.
-  A Service CIDR block cannot overlap with the subnet CIDR block or the pod CIDR block.

.. _cce_bestpractice_00004__fig19746213285:

.. figure:: /_static/images/en-us_image_0000002631691596.png
   :alt: **Figure 3** CIDR block planning for a CCE Turbo cluster

   **Figure 3** CIDR block planning for a CCE Turbo cluster

Single-VPC Multi-Cluster Scenarios
----------------------------------

**Clusters Using the VPC Networks**

Pod packets are forwarded through VPC routes. CCE automatically configures a route table on the VPC routes for each pod CIDR block. The network scale is limited by the VPC route table. :ref:`Figure 4 <cce_bestpractice_00004__en-us_topic_0099587154_fig69527530400>` shows the CIDR block planning of such a cluster.

-  A VPC CIDR block is where the cluster resides. The size of this CIDR block affects the maximum number of nodes that can be created in the cluster.
-  A subnet CIDR block cannot overlap with any pod CIDR block.
-  If there are multiple clusters that use the VPC networks in a single VPC, the pod CIDR blocks of all clusters cannot overlap with each other because these clusters use the same route table. In this case, if the node security group in a cluster allows the pod CIDR block from the peer cluster, pods in the cluster can directly access pods in the peer cluster through the pod IP addresses.
-  A Service CIDR block can be used only in clusters. Therefore, the Service CIDR blocks of different clusters can overlap with each other, but cannot overlap with the subnet CIDR block or pod CIDR block of other clusters.

.. _cce_bestpractice_00004__en-us_topic_0099587154_fig69527530400:

.. figure:: /_static/images/en-us_image_0000002631531702.png
   :alt: **Figure 4** Network planning for multiple clusters using the VPC networks

   **Figure 4** Network planning for multiple clusters using the VPC networks

**Clusters Using the Tunnel Networks**

In a container tunnel network, the container network is an overlay network built on top of the VPC network. Although there is a small amount of performance overhead due to tunnel encapsulation, this kind of network offers strong generality, excellent interoperability, and comprehensive support for advanced features such as network policy-based isolation, making it suitable for most application scenarios. :ref:`Figure 5 <cce_bestpractice_00004__en-us_topic_0099587154_fig8672112184219>` shows the CIDR block planning of the cluster.

-  A VPC CIDR block is where the cluster resides. The size of this CIDR block affects the maximum number of nodes that can be created in the cluster.
-  A subnet CIDR block cannot overlap with any pod CIDR block.
-  The pod CIDR blocks of all clusters can overlap with each other. In this case, pods in different clusters cannot be directly accessed through pod IP addresses. Services are needed for accessing pods in different clusters. The LoadBalancer Services are recommended.
-  A Service CIDR block can be used only in clusters. Therefore, the Service CIDR blocks of different clusters can overlap with each other, but cannot overlap with the subnet CIDR block or pod CIDR block of other clusters.

.. _cce_bestpractice_00004__en-us_topic_0099587154_fig8672112184219:

.. figure:: /_static/images/en-us_image_0000002631531704.png
   :alt: **Figure 5** Network planning for multiple clusters using the tunnel networks

   **Figure 5** Network planning for multiple clusters using the tunnel networks

CCE Turbo Clusters Using the **Cloud Native 2.0 Networks**

In a Cloud Native 2.0 network, container IP addresses are allocated from the VPC CIDR block. Clusters using this kind of network support multiple VPC-based capabilities, such as ELB passthrough networking to pods and direct association of security groups with containers. They can deliver faster network connection and forwarding.

-  A VPC CIDR block is where a cluster resides. In a CCE Turbo cluster, the CIDR block size affects the total number of nodes and containers that can be created in the cluster.
-  There is no special restriction on the subnet CIDR blocks in CCE Turbo clusters.
-  A container subnet CIDR block is included in the VPC CIDR block. CIDR blocks of container subnets in different clusters can overlap with each other or with the subnet CIDR block. However, you are advised to use different pod CIDR blocks for different clusters and ensure that the pod CIDR blocks have sufficient IP addresses. In this case, if the cluster's network interface security group allows the peer cluster's pod CIDR block, pods in these clusters can directly access each other through the IP addresses.
-  Service CIDR blocks are cluster-scoped and can overlap across clusters. However, they must not overlap with any subnet CIDR block or pod CIDR block within the same cluster.


.. figure:: /_static/images/en-us_image_0000002631531692.png
   :alt: **Figure 6** Network planning for multiple clusters using the Cloud Native 2.0 networks

   **Figure 6** Network planning for multiple clusters using the Cloud Native 2.0 networks

**Clusters Using Different Networks**

For a VPC that contains clusters using different networks, comply with the following rules when creating a cluster:

-  All these clusters are in the same VPC CIDR block. Ensure that there are sufficient available IP addresses in the VPC.
-  A subnet CIDR block cannot overlap with any pod CIDR block. Even in some scenarios, for example, where there are also CCE Turbo clusters, the subnet CIDR block can overlap with the pod (subnet) CIDR block. However, this is not recommended.
-  The pod CIDR blocks of the clusters that use **the VPC networks** cannot overlap with each other.
-  The Service CIDR blocks of all these clusters can overlap with each other, but cannot overlap with any subnet CIDR block or pod CIDR block of the clusters.

Cross-VPC Cluster Connection
----------------------------

Different VPCs cannot communicate with each other. A VPC peering connection can connect VPCs in the same region. For two VPC networks that are connected, you can use route tables to configure the packets to be sent to the peer VPC.

**Clusters Using the VPC Networks**

To allow clusters that use the VPC networks to access each other across VPCs, add routes to both ends of the VPC peering after a VPC peering connection is created.


.. figure:: /_static/images/en-us_image_0000002662010839.png
   :alt: **Figure 7** Network planning for clusters in different VPCs

   **Figure 7** Network planning for clusters in different VPCs

When creating a VPC peering connection for clusters in different VPCs, pay attention to the following points:

-  The VPCs to which the clusters belong must not overlap with each other. In each cluster, the subnet CIDR block cannot overlap with the pod CIDR block.
-  The pod CIDR blocks of the clusters at both ends cannot overlap with each other, but the Service CIDR blocks can.
-  If the request end cluster uses the VPC network, **check whether the node security group in the peer cluster allows the pod CIDR block of the request end cluster**. If yes, pods in these clusters can directly access each other through the pod IP addresses. Similarly, if nodes running in the clusters at the both ends of the VPC peering connection need to access each other, the node security group must allow the VPC CIDR block of the peer cluster.
-  Both VPC route tables must include routes to the peer CIDR block. For example, the route table of VPC 1 must include a route to the VPC 2 CIDR block, and likewise, the route table of VPC 2 must include a route to the VPC 1 CIDR block.

   -  **Add the peer cluster's VPC CIDR block**: After adding the route of the VPC CIDR block, pods can access nodes in the other cluster, such as reaching a NodePort Service.
   -  **Add the peer cluster's pod CIDR block**: After adding the route of the pod CIDR block, pods can directly access pods in the other cluster using their IP addresses.

**Clusters Using the Tunnel Networks**

To allow clusters that use the tunnel networks to access each other across VPCs, add routes to both ends of the VPC peering after a VPC peering connection is created.


.. figure:: /_static/images/en-us_image_0000002661850897.png
   :alt: **Figure 8** Network planning for clusters using the tunnel networks

   **Figure 8** Network planning for clusters using the tunnel networks

Pay attention to the following:

-  The VPC CIDR blocks of the clusters at the two ends must not overlap with each other.
-  The pod CIDR blocks of the clusters can overlap with each other, so can the Service CIDR blocks.
-  If the request end cluster uses the tunnel network, check whether the node security group in the destination cluster allows the VPC CIDR block (including the node subnets) of the request end cluster. If yes, nodes in both clusters can access each other. However, pods in these clusters cannot directly access each other using pod IP addresses. To achieve this, Services are needed. The LoadBalancer Services are recommended.
-  Both VPC route tables must include routes to the peer cluster's VPC CIDR block. For example, the route table of VPC 1 must include a route to the VPC 2 CIDR block, and likewise, the route table of VPC 2 must include a route to the VPC 1 CIDR block. After the routes of the VPC CIDR blocks are added, pods can access nodes in the other cluster, such as reaching a NodePort Service.

**CCE Turbo Clusters Using the Cloud Native 2.0 Networks**

After creating a VPC peering connection, add routes of the VPC peering connection to both ends so that the two VPCs can communicate with each other. Pay attention to the following:

-  The VPC CIDR blocks of the clusters at the two ends must not overlap with each other.
-  If the request end cluster uses the Cloud Native 2.0 network, check whether the network interface security group (named in the format of *{Cluster name}*\ **-cce-eni-**\ *{Random ID}*) of the destination cluster allows the VPC CIDR block (including the node subnets and pod CIDR block) of the request end cluster. If yes, pods in these clusters can directly access each other through the pod IP addresses. Similarly, to enable nodes in these clusters to access each other, allow the VPC CIDR block of the peer cluster in the node security group (named in the format of *{Cluster name}*\ **-cce-node-**\ *{Random ID}*).
-  Both VPC route tables must include routes to the peer cluster's VPC CIDR block. For example, the route table of VPC 1 must include a route to the VPC 2 CIDR block, and likewise, the route table of VPC 2 must include a route to the VPC 1 CIDR block. After the routes of the VPC CIDR blocks are added, pods can access pods or nodes in the other cluster.

**Clusters Using Different Networks**

If clusters using different networks need to communicate with each other across VPCs, every one of them may serve as the request end or destination end. Pay attention to the following:

-  The VPC CIDR block of a cluster cannot overlap with the VPC CIDR block of the other cluster.

-  The subnet CIDR blocks cannot overlap with any of the pod CIDR blocks.

-  The pod CIDR blocks in these clusters cannot overlap with each other.

-  If pods or nodes in these clusters need to access each other, the security groups in the clusters on both ends must allow the corresponding CIDR blocks based on the following rules:

   -  If the request end cluster uses a VPC network, the node security group of the destination cluster must allow the VPC CIDR block (including the node subnets and pod CIDR block) of the request end cluster.
   -  If the request end cluster uses a tunnel network, the node security group of the destination cluster must allow the VPC CIDR block (including the node subnets) of the request end cluster.
   -  If the request end cluster uses a Cloud Native 2.0 network, the network interface security group and node security group of the destination cluster must allow the VPC CIDR block (including node subnets and pod CIDR block) of the request end cluster.

-  Both VPC route tables must include routes to the peer cluster's VPC CIDR block. For example, the route table of VPC 1 must include a route to the VPC 2 CIDR block, and likewise, the route table of VPC 2 must include a route to the VPC 1 CIDR block. After the routes of the VPC CIDR blocks are added, pods can access nodes in the other cluster, such as reaching a NodePort Service.

   If one of the clusters uses a VPC network, both VPC route tables must also include the pod CIDR blocks. After the routes of the pod CIDR blocks are added, pods can directly access pods in the other cluster using their IP addresses.

Access to Other Cloud Services from a CCE Cluster
-------------------------------------------------

CCE clusters can be used with other cloud services, such as RDS and Kafka. To ensure stable communication and avoid network conflicts, a cluster's network must be carefully planned. Specifically, the CIDR blocks used by other services must not overlap with those used by the cluster. The following requirements apply based on the cluster's network:

-  **For a cluster that uses a VPC network**: The CIDR blocks of other cloud services must not overlap with the pod CIDR block or Service CIDR block within the cluster.
-  **For a cluster that uses a tunnel network**: The CIDR blocks of other cloud services must not overlap with the pod CIDR block or Service CIDR block within the cluster.
-  **For a CCE Turbo cluster that uses a Cloud Native 2.0 network**: The CIDR blocks of other cloud services must not overlap with the Service CIDR block within the cluster.

.. caution::

   -  In addition to the CIDR block planning, it is essential to verify whether the target cloud service supports external access and whether the relevant security group rules or ACL configurations permit communication.
   -  If the CCE cluster and the cloud services reside in different VPCs, their VPC CIDR blocks must remain non-overlapping. To enable communication between the cluster and external services across VPCs, a VPC peering connection can be established.

VPC-IDC Scenarios
-----------------

Similar to the VPC connection scenario, some CIDR blocks in the VPC may be routed to an IDC, so the pod IP addresses of the CCE cluster must not overlap with those CIDR blocks. If the IDC needs to access pods in the cluster, corresponding routes to the private line must also be configured in the IDC route table.
