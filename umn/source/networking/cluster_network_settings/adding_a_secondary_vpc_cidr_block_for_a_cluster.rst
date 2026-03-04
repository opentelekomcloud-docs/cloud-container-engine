:original_name: cce_10_0387.html

.. _cce_10_0387:

Adding a Secondary VPC CIDR Block for a Cluster
===============================================

Scenario
--------

When creating a cluster, deploy it in a VPC. If the planned VPC is too small and IP addresses are insufficient, you can use a secondary VPC CIDR block to support your service scaling. This section describes how to add a secondary VPC CIDR block for your cluster.

Notes and Constraints
---------------------

Only CCE standard clusters and CCE Turbo clusters of v1.21 and later are supported.

Planning a Secondary CIDR Block
-------------------------------

Before adding a secondary CIDR block, plan it properly to prevent CIDR conflicts. Note the following points:

-  All subnets (including those created from the secondary CIDR blocks) in the VPC where the cluster resides cannot conflict with the container or Service CIDR block.
-  Do not use CIDR blocks 10.0.0.0/8, 172.16.0.0/12, and 192.168.0.0/16 as secondary CIDR blocks, as they may conflict with IP addresses allocated to cluster master nodes.
-  In a cluster that uses a VPC network, when an ECS outside the cluster (but in the same VPC as it) communicates with the cluster, SNAT is applied. As a result, the pod source IP address is translated to the node IP address.
-  In a cluster that uses a VPC network, once you add a node from a secondary CIDR block, the cluster security group automatically allows traffic from that secondary CIDR block, enabling ECSs in the secondary CIDR block to access pods within the cluster. If no nodes are added from the secondary CIDR block and the cluster's security group does not allow any traffic from it, ECSs in that secondary CIDR block cannot reach pods within the cluster.
-  For a CCE Turbo cluster, you must manually update the network interface security group to allow traffic from the secondary CIDR block, so ECSs in that CIDR block can access pods within the cluster. For details about how to modify a security group, see :ref:`How Can I Configure a Security Group Rule for a Cluster? <cce_faq_00265>`

Procedure
---------

#. Log in to the VPC console. In the navigation pane, choose **Virtual Private Cloud > My VPCs**. In the **Operation** column of the VPC to which the cluster belongs, click **Edit CIDR Block** and then **Add Secondary IPv4 CIDR Block**.

   Enter the secondary CIDR block to be added, for example, 192.169.0.0/16.

#. In the navigation pane, choose **Virtual Private Cloud** > **Subnets**. Click **Create Subnet**. In **IPv4 CIDR Block**, enter the newly added secondary IPv4 CIDR block. Configure other parameters as planned, and click **OK**. Then, you can create subnets in the secondary IPv4 CIDR block for the cluster.

#. After a subnet is created using the secondary IPv4 CIDR block, select the subnet when creating a node or node pool in the **Network Settings** area.
