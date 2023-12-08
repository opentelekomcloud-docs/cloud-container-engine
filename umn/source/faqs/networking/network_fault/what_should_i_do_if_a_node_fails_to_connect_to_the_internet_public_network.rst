:original_name: cce_faq_00022.html

.. _cce_faq_00022:

What Should I Do If a Node Fails to Connect to the Internet (Public Network)?
=============================================================================

If a node fails to be connected to the Internet, perform the following operations:

Check Item 1: Whether an EIP Has Been Bound to the Node
-------------------------------------------------------

Log in to the ECS console and check whether an EIP has been bound to the ECS corresponding to the node.

If there is an IP address in the EIP column, an EIP has been bound. If there is no IP address in that column, bind one.

Check Item 2: Whether a Network ACL Has Been Configured for the Node
--------------------------------------------------------------------

Log in to the VPC console. In the navigation pane, choose **Access Control** > **Firewalls**. Check whether a firewall has been configured for the subnet where the node is located and whether external access is restricted.
