:original_name: cce_faq_00204.html

.. _cce_faq_00204:

What Should I Do If a Container Fails to Access the Internet?
=============================================================

If a container cannot access the Internet, check whether the node where the container is located can access the Internet. Then check whether the network configuration of the container is correct. For example, check whether the DNS configuration can resolve the domain name.

Check Item 1: Whether the Node Can Access the Internet
------------------------------------------------------

#. Log in to the ECS console.

#. Check whether an EIP has been bound to the ECS (node) or whether the ECS has a NAT gateway configured.

   If no EIP is displayed, bind an EIP to the ECS.

Check Item 2: Whether a Network ACL Has Been Configured for the Node
--------------------------------------------------------------------

#. Log in to the VPC console.
#. In the navigation pane on the left, choose **Access Control** > **Network ACLs**.
#. Check whether a network ACL has been configured for the subnet where the node is located and whether external access is restricted.

Check Item 3: Whether the DNS Configuration of the Container Is Correct
-----------------------------------------------------------------------

Run **cat /etc/resolv.conf** in the container to check the DNS configuration. An example is as follows:

.. code-block::

   nameserver 10.247.x.x
   search default.svc.cluster.local svc.cluster.local cluster.local
   options ndots:5

If **nameserver** is set to **10.247.x.x**, DNS is connected to the CoreDNS of the cluster. Ensure that the CoreDNS of the cluster is running properly. If another IP address is displayed, an in-cloud or on-premises DNS server is used. Ensure that the domain name resolution is correctly configured.
