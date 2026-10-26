:original_name: cce_faq_00266.html

.. _cce_faq_00266:

What Is the Relationship Between Clusters, VPCs, and Subnets?
=============================================================

A VPC is similar to a private local area network (LAN) managed by a home gateway whose IP address is 192.168.0.0/16. A VPC is a private network built on the cloud and provides basic network environment for running ECSs, load balancers, and middleware. Choose a CIDR block based on your service requirements. You can typically use 10.0.0.0/8, 172.16.0.0/12, or 192.168.0.0/16 as the base network, with subnet prefix lengths ranging from the base mask down to /24.

A subnet is a logical subdivision of a VPC. You can partition a VPC into multiple subnets and apply security groups, route tables, and network ACLs to control inter-subnet communication. This isolation allows you to deploy different services in separate subnets.

A cluster is one or a group of ECSs (also known as nodes) in the same VPC. It provides computing resource pools for running containers.

As shown in :ref:`Figure 1 <cce_faq_00266__fig43963373019>`, a region may consist of multiple VPCs. A VPC consists of one or more subnets. The subnets communicate with each other through a subnet gateway. A cluster is created in a subnet. There are three scenarios:

-  Different clusters are created in different VPCs.
-  Different clusters are created in the same subnet.
-  Different clusters are created in different subnets.

.. _cce_faq_00266__fig43963373019:

.. figure:: /_static/images/en-us_image_0000002631532244.png
   :alt: **Figure 1** Relationship between clusters, VPCs, and subnets

   **Figure 1** Relationship between clusters, VPCs, and subnets
