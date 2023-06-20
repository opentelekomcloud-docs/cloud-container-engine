:original_name: cce_10_0437.html

.. _cce_10_0437:

Checking the Security Group
===========================

Check Item
----------

Check whether the security group allows the master node to access nodes using ICMP.

Solution
--------

Log in to the VPC console, choose **Access Control** > **Security Groups**, and enter the target cluster name in the search box. Two security groups are displayed:

-  The security group name is **cluster name-node-xxx**. This security group is associated with the user nodes.
-  The security group name is **cluster name-control-xxx**. This security group is associated with the master nodes.

Click the security group of the node user and ensure that the following rules are configured to allow the master node to access the node using **ICMP**.

Otherwise, add a rule to the node security group. Set **Source** to **Security group**.
