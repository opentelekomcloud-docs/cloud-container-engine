:original_name: cce_faq_00447.html

.. _cce_faq_00447:

How Can I Delete a Security Group Rule Associated with a Deleted Subnet?
========================================================================

Scenarios
---------

Pod subnets can be deleted from CCE Turbo clusters of v1.23.17-r0, v1.25.12-r0, v1.28.7-r0, or later versions.

When you delete a subnet, CCE does not automatically remove the security group rules associated with the subnet in the default node security group created by CCE. You must manually delete these rules.

Procedure
---------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Settings** and click the **Network** tab.
#. In the **Container Network** area, copy the IPv4 CIDR block of the subnet. (The default-network is used as an example.)
#. In the navigation pane, choose **Overview**. In the **Networking Configuration** area, click the name of the default node security group.
#. On the page displayed, click the **Inbound Rules** tab, locate the row containing the subnet CIDR block based on the source IP address, and find the corresponding security group rule.
#. Click **Delete** in the **Operation** column.
