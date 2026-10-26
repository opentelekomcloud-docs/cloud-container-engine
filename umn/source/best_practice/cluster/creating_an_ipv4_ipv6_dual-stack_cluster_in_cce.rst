:original_name: cce_bestpractice_00222.html

.. _cce_bestpractice_00222:

Creating an IPv4/IPv6 Dual-Stack Cluster in CCE
===============================================

This section describes how to create a VPC with an IPv6 CIDR block and deploy a cluster and a node with IPv6 addresses, enabling the nodes to access IPv6 services on the Internet.

Overview
--------

IPv6 addresses are used to handle IPv4 address exhaustion. If a worker node (such as an ECS) in the current cluster uses IPv4, the node can run in dual-stack mode after IPv6 is enabled. Specifically, the node has both IPv4 and IPv6 addresses, which can be used to access the intranet or the Internet.

Applications
------------

-  If your application serves IPv6 clients, use IPv6 EIPs or IPv4/IPv6 dual-stack.
-  If your application needs to provide Services for users who use IPv6 clients and analyze the access request data, you can only use IPv4/IPv6 dual-stack.
-  If internal communication is required between your application systems or between your application system and another system (such as the database system), you can use only IPv4/IPv6 dual-stack.

Notes and Constraints
---------------------

-  Clusters that support IPv4/IPv6 dual-stack are listed in the table below.

   +----------------------+--------------------------+---------------------+---------------------------------------------------------------------------------------------------------+
   | Cluster Type         | Cluster Network          | Version             | Remarks                                                                                                 |
   +======================+==========================+=====================+=========================================================================================================+
   | CCE standard cluster | Tunnel network           | v1.15 or later      | IPv4/IPv6 dual-stack will be generally available for clusters of v1.23.                                 |
   |                      |                          |                     |                                                                                                         |
   |                      |                          |                     | ELB dual-stack is not supported.                                                                        |
   +----------------------+--------------------------+---------------------+---------------------------------------------------------------------------------------------------------+
   | CCE Turbo cluster    | Cloud Native network 2.0 | v1.23.8-r0 or later | Currently, secure containers that use Kata do not support IPv4/IPv6 dual-stack.                         |
   |                      |                          |                     |                                                                                                         |
   |                      |                          | v1.25.3-r0 or later | Only ECS-VM or ECS-PM (c6.22xlarge.4.physical or c7.32xlarge.4.physical) supports IPv4/IPv6 dual-stack. |
   +----------------------+--------------------------+---------------------+---------------------------------------------------------------------------------------------------------+

-  Worker nodes and master nodes in a Kubernetes cluster use IPv4 addresses to communicate with each other.
-  Only one IPv6 address can be bound to each network interface.
-  When IPv4/IPv6 dual-stack is enabled for a cluster, DHCP unlimited lease cannot be enabled for the selected node subnet.
-  If a dual-stack cluster is used, do not change the load balancer protocol version on the ELB console.
-  ELB dual-stack can only be used in CCE Turbo clusters with the restrictions below.

   +-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------+
   | Application           | Dedicated Load Balancer                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Shared Load Balancer    |
   +=======================+=====================================================================================================================================================================================================================================================================================================================================================================================================================================================================================+=========================+
   | LoadBalancer ingress  | Dual stack is supported.                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Only IPv4 is supported. |
   |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                         |
   |                       | Layer 7 dedicated load balancers can only communicate with their backend servers using IPv4. If an ingress uses IPv4/IPv6 dual-stack, related alarms will be generated. Backends with IPv6 addresses cannot be added to the associated load balancer. You can view the related alarms by referring to the events of the corresponding ingress.                                                                                                                                      |                         |
   +-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------+
   | Nginx ingress         | Dual-stack is supported when the following conditions are met:                                                                                                                                                                                                                                                                                                                                                                                                                      | Only IPv4 is supported. |
   |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                         |
   |                       | -  In clusters from v1.19 to v1.23, NGINX Ingress Controller of 2.1.7 or later is used.                                                                                                                                                                                                                                                                                                                                                                                             |                         |
   |                       | -  In clusters of v1.25 or later, NGINX Ingress Controller of 2.2.5 or later is used.                                                                                                                                                                                                                                                                                                                                                                                               |                         |
   +-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------+
   | LoadBalancer Service  | -  Layer 7 (HTTP/HTTPS): Dual-stack is supported.                                                                                                                                                                                                                                                                                                                                                                                                                                   | Only IPv4 is supported. |
   |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                         |
   |                       |    Layer 7 dedicated load balancers can only communicate with their backend servers using IPv4. If a Service uses IPv4/IPv6 dual-stack, related alarms will be generated. Backends with IPv6 addresses cannot be added to the associated load balancer. You can view the related alarms by referring to the events of the corresponding Service. To avoid alarms, you can select IPv4 when creating a Service and select a dedicated Layer 7 load balancer with dual-stack enabled. |                         |
   |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                         |
   |                       | -  Layer 4 (TCP/UDP): Dual-stack is supported.                                                                                                                                                                                                                                                                                                                                                                                                                                      |                         |
   +-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------+

Step 1: Create a VPC with a Subnet
----------------------------------

Before creating VPCs, determine how many VPCs, subnets, and what IP address ranges you will need.

.. note::

   -  The basic operations for IPv4/IPv6 dual-stack are the same as those for IPv4. Only some parameters are different.

Perform the following operations to create a VPC named **vpc-ipv6** and its default subnet **subnet-ipv6**.

#. Log in to the management console.

#. Click |image1| in the upper left corner of the management console and select a region and a project.

#. Choose **Network** > **Virtual Private Cloud**.

#. Click **Create VPC**.

#. Configure the VPC and the subnet following instructions. For details about the mandatory parameters, see :ref:`Table 1 <cce_bestpractice_00222__en-us_topic_0226102195_en-us_topic_0213478735_en-us_topic_0118066459_table65603559163645>` and :ref:`Table 2 <cce_bestpractice_00222__en-us_topic_0226102195_en-us_topic_0213478735_en-us_topic_0118066459_table18912125318159>`.

   .. _cce_bestpractice_00222__en-us_topic_0226102195_en-us_topic_0213478735_en-us_topic_0118066459_table65603559163645:

   .. table:: **Table 1** VPC parameters

      +-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Parameter             | Description                                                                                                                                                                                                                                                                                                 | Example Value         |
      +=======================+=============================================================================================================================================================================================================================================================================================================+=======================+
      | Region                | The desired region. Regions are geographic areas that are physically isolated from each other. The networks inside different regions are not connected to each other, so resources cannot be shared across different regions. For low network latency and quick resource access, select the nearest region. | N/A                   |
      +-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Name                  | A VPC name.                                                                                                                                                                                                                                                                                                 | vpc-ipv6              |
      +-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | IPv4 CIDR Block       | The VPC CIDR block. The subnet CIDR blocks in the VPC must be within this CIDR block.                                                                                                                                                                                                                       | 192.168.0.0/16        |
      |                       |                                                                                                                                                                                                                                                                                                             |                       |
      |                       | The following CIDR blocks are supported:                                                                                                                                                                                                                                                                    |                       |
      |                       |                                                                                                                                                                                                                                                                                                             |                       |
      |                       | 10.0.0.0/8-24                                                                                                                                                                                                                                                                                               |                       |
      |                       |                                                                                                                                                                                                                                                                                                             |                       |
      |                       | 172.16.0.0/12-24                                                                                                                                                                                                                                                                                            |                       |
      |                       |                                                                                                                                                                                                                                                                                                             |                       |
      |                       | 192.168.0.0/16-24                                                                                                                                                                                                                                                                                           |                       |
      +-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+

   .. _cce_bestpractice_00222__en-us_topic_0226102195_en-us_topic_0213478735_en-us_topic_0118066459_table18912125318159:

   .. table:: **Table 2** Subnet parameters

      +--------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Parameter                | Description                                                                                                                                                               | Example Value         |
      +==========================+===========================================================================================================================================================================+=======================+
      | Name                     | A subnet name.                                                                                                                                                            | subnet-ipv6           |
      +--------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | IPv4 CIDR Block          | The IPv4 CIDR block for a subnet. This value must be within the VPC CIDR block.                                                                                           | 192.168.0.0/24        |
      +--------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | IPv6 CIDR Block          | Enabling it will automatically assign an IPv6 CIDR block to a subnet. This function cannot be disabled once enabled. Custom IPv6 CIDR blocks are not supported currently. | N/A                   |
      +--------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Associated Route Table   | The default route table to which a subnet will be associated. You can change the route table to a custom route table.                                                     | Default               |
      +--------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Advanced Settings (IPv6) |                                                                                                                                                                           |                       |
      +--------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Gateway                  | The gateway address of a subnet.                                                                                                                                          | 192.168.0.1           |
      |                          |                                                                                                                                                                           |                       |
      |                          | It is used to communicate with other subnets.                                                                                                                             |                       |
      +--------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | DNS Server Address       | By default, two DNS server addresses are configured. You can change them if necessary. When multiple IP addresses are available, separate them with commas (,).           | 100.125.x.x           |
      +--------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+

#. Click .

Step 2: Create a CCE Cluster
----------------------------

**Creating a CCE Standard Cluster**

#. Log in to the CCE console and create a CCE cluster.

   Complete the network settings by referring to the configuration below.

   -  **VPC**: Select **vpc-ipv6**.
   -  **Default Node Subnet**: Select a subnet with IPv6 enabled.
   -  **IPv6**: Enable it. After it is enabled, cluster resources, including nodes and workloads, can be accessed through IPv6 CIDR blocks.
   -  **Network Model**: Select **Tunnel network**.
   -  **Network Policies**: It is enabled by default to restrict the objects that can be accessed by pods.
   -  **Container CIDR Block**: A proper mask must be configured for the pod CIDR block. The mask determines the number of available nodes in a cluster. If there is an improper mask for the pod CIDR block, there will be only a small number of available nodes in the cluster.

#. Create a node.

   The CCE console displays the nodes that support IPv6. You can directly select a node.

   After the creation is complete, access the cluster details page. Then, click the node name to go to the ECS details page and view the automatically allocated IPv6 address.

**Creating a CCE Turbo Cluster**

#. Log in to the CCE console and create a CCE Turbo cluster.

   Complete the network settings by referring to the configuration below.

   -  **Network Model**: Select **Cloud Native Network 2.0**.
   -  **IPv6**: Enable it. After it is enabled, cluster resources, including nodes and workloads, can be accessed through IPv6 CIDR blocks.
   -  **VPC**: Select **vpc-ipv6**.
   -  **Default Node Subnet**: Only subnets with IPv6 enabled can be selected.
   -  **Pod Subnet**: Only subnets with IPv6 enabled can be selected.
   -  **Service CIDR Block**: A proper mask must be configured for the pod CIDR block. The mask determines the number of available nodes in a cluster. If the mask of the pod CIDR block in the cluster is set improperly, there will be only a small number of available nodes in the cluster.
   -  **IPv6 Service CIDR Block**: determines the maximum number of Services that can be created. The value cannot be changed after being specified. The default value is **fc00::/112**. To customize the CIDR block, ensure that the CIDR block meets the requirements below.

      -  The IPv6 Service CIDR block must belong to the **fc00::/8** CIDR block.
      -  The prefix length of an IPv6 address ranges from 112 to 120. You can adjust the number of IPv6 addresses by adjusting the prefix value. A maximum of 65536 IPv6 addresses are allowed.

#. Create a node.

   The CCE console displays the nodes that support IPv6. You can directly select a node.

   After the creation is complete, access the cluster details page. Then, click the node name to go to the ECS details page and view the automatically allocated IPv6 address.

Step 3: Apply for Shared Bandwidth and Add an IPv6 Address to the Shared Bandwidth
----------------------------------------------------------------------------------

If you already have shared bandwidth, simply add the to the shared bandwidth.

**Applying for a Shared Bandwidth**

#. Log in to the management console.
#. Click |image2| in the upper left corner and select a region and a project.
#. Choose **Service List** > **Network** > **Virtual Private Cloud**.
#. In the navigation pane, choose **Elastic IP and Bandwidth** > **Shared Bandwidths**.
#. In the upper right corner, click **Assign Shared Bandwidth**. On the displayed page, configure parameters following instructions.

   .. table:: **Table 3** Parameters

      +-----------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
      | Parameter | Description                                                                                                                                                                                                                                                                                                 | Example Value |
      +===========+=============================================================================================================================================================================================================================================================================================================+===============+
      | Region    | The desired region. Regions are geographic areas that are physically isolated from each other. The networks inside different regions are not connected to each other, so resources cannot be shared across different regions. For low network latency and quick resource access, select the nearest region. | N/A           |
      +-----------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
      | Bandwidth | Specifies the shared bandwidth size in Mbit/s.                                                                                                                                                                                                                                                              | 10            |
      +-----------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
      | Name      | Specifies the name of the shared bandwidth.                                                                                                                                                                                                                                                                 | Bandwidth-001 |
      +-----------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+

#. Click **Assign Now**.

**Adding an IPv6 Address to the Shared Bandwidth**

#. On the shared bandwidth list page, locate the row containing the shared bandwidth and click **Add Public IP Address** in the **Operation** column.
#. Add the to the shared bandwidth.
#. Click **OK**.

**Verifying the Result**

Log in to an ECS and ping an IPv6 address on the Internet to verify the connectivity. **ping6 ipv6.baidu.com** is used as an example here. The execution result is shown in :ref:`Figure 1 <cce_bestpractice_00222__en-us_topic_0226102195_en-us_topic_0213478735_en-us_topic_0118066459_fig12339172511196>`.

.. _cce_bestpractice_00222__en-us_topic_0226102195_en-us_topic_0213478735_en-us_topic_0118066459_fig12339172511196:

.. figure:: /_static/images/en-us_image_0000002631531514.png
   :alt: **Figure 1** Result verification

   **Figure 1** Result verification

.. |image1| image:: /_static/images/en-us_image_0000002661850703.png
.. |image2| image:: /_static/images/en-us_image_0000002631691420.png
