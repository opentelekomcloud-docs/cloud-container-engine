:original_name: cce_10_0437.html

.. _cce_10_0437:

Security Groups
===============

Check Items
-----------

Check whether the **Protocol & Port** of the worker node security groups are set to **ICMP: All** and whether the security group with the source IP address set to the master node security group is deleted.

.. note::

   This check item is performed only for clusters using VPC networking. For clusters using other networking, skip this check item.

Solution
--------

Log in to the VPC console, choose **Access Control** > **Security Groups**, and enter the target cluster name in the search box. Two security groups are expected to display:

-  The security group name is **cluster name-node-xxx**. This security group is associated with the worker nodes.
-  The security group name is **cluster name-control-xxx**. This security group is associated with the master nodes.

Click the node security group and ensure that the following rules are configured to allow the master node to access the node using **ICMP**.

If the preceding security group rule is unavailable, add the rule with the following configurations to the node security group: Set **Protocol & Port** to **Protocols/ICMP** and **All**, and **Source** to **Security group** and the master security group.
