:original_name: cce_10_0437.html

.. _cce_10_0437:

Security Groups
===============

Check Items
-----------

Check whether the **Protocol & Port** of the worker node security groups is set to **ICMP: All** and whether the security group rule with the source IP address set to the master node security group has been deleted.

.. note::

   This check item is performed only for clusters using VPC networking. For clusters using other networking, skip this check item.

Solution
--------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Overview**. In the **Networking Configuration** area, view the default node security group.
#. Click the name of the default node security group to go to the details page. Ensure that the **inbound rules** contain the rule in the figure below. This rule allows the master node to access worker nodes using the **ICMP** protocol.
#. If the rule does not exist, click **Add Rule** to add the inbound rule to the node security group.

   -  **Priority**: Set it to **1**.
   -  **Action**: Select **Allow**.
   -  **Type**: Select **IPv4**.
   -  **Protocol & Port**: Select **Protocols/ICMP** and select **All** for **Port**.
   -  **Source**: Select **Security Group** and set it to the master node security group. The master node security group is in the format of *cluster-name*\ **-control-**\ *xxx*. You can search for the security group by cluster name.
