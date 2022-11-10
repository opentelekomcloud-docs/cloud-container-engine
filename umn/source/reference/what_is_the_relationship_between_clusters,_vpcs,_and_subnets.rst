:original_name: cce_faq_00266.html

.. _cce_faq_00266:

What Is the Relationship Between Clusters, VPCs, and Subnets?
=============================================================

A VPC is similar to a private local area network (LAN) managed by a home gateway whose IP address is 192.168.0.0/16. A VPC is a private network built on the cloud and provides basic network environment for running elastic cloud servers (ECSs), elastic load balancers (ELBs), and middleware. Networks of different scales can be configured based on service requirements. Generally, you can set the CIDR block to 10.0.0.0/8-24, 172.16.0.0/12-24, or 192.168.0.0/16-24. The largest CIDR block is 10.0.0.0/8, which corresponds to a class A network.

A VPC can be divided into multiple subnets. Security groups are configured to determine whether these subnets can communicate with each other. This ensures that subnets can be isolated from each other, so that you can deploy different services on different subnets.

A cluster consists of one or more ECSs (also known as nodes) in the same VPC. It provides a computing resource pool for running containers.

As shown in :ref:`Figure 1 <cce_faq_00266__en-us_topic_0241129829_fig43963373019>`, a region may comprise of multiple VPCs. A VPC consists of one or more subnets. The subnets communicate with each other through a subnet gateway. A cluster is created in a subnet. There are three scenarios:

-  Different clusters are created in different VPCs.
-  Different clusters are created in the same subnet.
-  Different clusters are created in different subnets.

.. _cce_faq_00266__en-us_topic_0241129829_fig43963373019:

.. figure:: /_static/images/en-us_image_0000001178352590.png
   :alt: **Figure 1** Relationship between clusters, VPCs, and subnets

   **Figure 1** Relationship between clusters, VPCs, and subnets
