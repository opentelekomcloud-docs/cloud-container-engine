:original_name: cce_faq_00204.html

.. _cce_faq_00204:

What Should I Do If a Container Fails to Access the Internet?
=============================================================

If a container cannot access the Internet, check whether the node where the container is located can access the Internet. Then check whether the network configuration of the container is correct. For example, check whether the DNS configuration can resolve the domain name.

Check Item 1: Whether the Node Can Access the Internet
------------------------------------------------------

#. Log in to the ECS console.

#. Check whether the ECS corresponding to the node has been bound to an EIP or has a NAT gateway configured.

   An EIP has been bound, as shown in :ref:`Figure 1 <cce_faq_00204__fig30686701>`. If no EIP is displayed, bind an EIP to the ECS.

   .. _cce_faq_00204__fig30686701:

   .. figure:: /_static/images/en-us_image_0000001668067156.png
      :alt: **Figure 1** Node with an EIP bound

      **Figure 1** Node with an EIP bound

Check Item 2: Whether a Network ACL Has Been Configured for the Node
--------------------------------------------------------------------

#. Log in to the VPC console.
#. In the navigation pane on the left, choose **Access Control** > **Network ACLs**.
#. Check whether a network ACL has been configured for the subnet where the node is located and whether external access is restricted.

Check Item 3: Whether the DNS Configuration of the Container Is Correct
-----------------------------------------------------------------------

Run the **cat /etc/resolv.conf command** command in the container to check the DNS configuration. An example is as follows:

.. code-block::

   nameserver 10.247.x.x
   search default.svc.cluster.local svc.cluster.local cluster.local
   options ndots:5

If **nameserver** is set to **10.247.x.x**, DNS is connected to the CoreDNS of the cluster. Ensure that the CoreDNS of the cluster is running properly. If another IP address is displayed, an in-cloud or on-premises DNS server is used. Ensure that the domain name resolution is correct.
