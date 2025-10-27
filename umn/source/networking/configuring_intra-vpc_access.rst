:original_name: cce_10_0399.html

.. _cce_10_0399:

Configuring Intra-VPC Access
============================

This section describes how to access an intranet from a container (outside the cluster in a VPC), including intra-VPC access and cross-VPC access.

.. _cce_10_0399__section1940319933:

Intra-VPC Access
----------------

The performance of accessing an intranet from a container varies depending on the container network models of a cluster.

-  **Container tunnel network**

   The container tunnel network encapsulates network data packets through tunnels based on the node network. A container can access other resources in the same VPC as long as the node can access the resources. If the access fails, check whether the security group of peer resources allows the access from the node where the container is located.

-  **Cloud Native Network 2.0**

   In the Cloud Native Network 2.0 model, a container is assigned an IP address from the CIDR block of a VPC. The container CIDR block is the subnet of the VPC where the node is located. The container can naturally communicate with other addresses in the VPC. If the access fails, check whether the security group of peer resources allows the access from the container CIDR block.

-  **VPC network**

   The VPC network model uses VPC routes to forward container traffic. The container CIDR block and the node VPC are not in the same CIDR block. When a container accesses other resources in the same VPC, **the security groups of these resources must allow traffic from the container CIDR block**.

   Suppose the cluster CIDR block is 192.168.10.0/24, and the container CIDR block is 172.16.0.0/16.

   There is an ECS whose IP address is 192.168.10.25 in the VPC (outside the cluster). The security group rules of the ECS only allow access from the cluster CIDR block.

   In this case, 192.168.10.25 cannot be pinged from the container.

   .. code-block::

      kubectl exec test01-6cbbf97b78-krj6h  -it -- /bin/sh
      / # ping 192.168.10.25
      PING 192.168.10.25 (192.168.10.25): 56 data bytes
      ^C
      --- 192.168.10.25 ping statistics ---
      104 packets transmitted, 0 packets received, 100% packet loss

   Configure the security group to allow access from the container CIDR block 172.16.0.0/16.

   In this case, 192.168.10.25 can be pinged from the container.

   .. code-block::

      $ kubectl exec test01-6cbbf97b78-krj6h  -it -- /bin/sh
      / # ping 192.168.10.25
      PING 192.168.10.25 (192.168.10.25): 56 data bytes
      64 bytes from 192.168.10.25: seq=0 ttl=64 time=1.412 ms
      64 bytes from 192.168.10.25: seq=1 ttl=64 time=1.400 ms
      64 bytes from 192.168.10.25: seq=2 ttl=64 time=1.299 ms
      64 bytes from 192.168.10.25: seq=3 ttl=64 time=1.283 ms
      ^C
      --- 192.168.10.25 ping statistics ---
      4 packets transmitted, 4 packets received, 0% packet loss

.. _cce_10_0399__section44190754210:

Cross-VPC Access
----------------

Cross-VPC access is implemented by establishing a peering connection between VPCs.

-  In the container tunnel network model, a container can access the peer VPC only when the communication is enabled between the node network and the peer VPC.

-  Cloud Native Network 2.0 is similar to the container tunnel network. You only need to enable the communication between the subnet where the container is located and the peer VPC.

-  Each VPC network has an independent container CIDR block. In addition to the VPC CIDR block, the container CIDR block also needs to be connected.

   Assume that there are two VPCs.

   -  vpc-demo: Its CIDR block is 192.168.0.0/16, the cluster is in vpc-demo, and the container CIDR block is 10.0.0.0/16.
   -  vpc-demo2: Its CIDR block is 10.1.0.0/16.

   Create a peering connection named **peering-demo** (the local VPC is vpc-demo and the peer VPC is vpc-demo2). Add the container CIDR block to the route of the peer VPC.

   After this configuration, you can access the container CIDR block 10.0.0.0/16 in vpc-demo2. During the access, pay attention to the security group configuration and enable the port configuration.

Accessing Other Cloud Services
------------------------------

The cloud services that communicate with CCE through an intranet include RDS, DCS, Kafka, and RabbitMQ.

In addition to the network configurations described in :ref:`Intra-VPC Access <cce_10_0399__section1940319933>` and :ref:`Cross-VPC Access <cce_10_0399__section44190754210>`, you also need to check **whether these cloud services allow external access**. For example, the DCS Redis instance can be accessed only by the IP addresses in its trustlist. Generally, these cloud services can be accessed by IP addresses in the same VPC. However, the container CIDR block in the VPC network model is different from the CIDR block of the VPC. Therefore, you must add the container CIDR block to the trustlist.

What If a Container Fails to Access an Intranet?
------------------------------------------------

If an intranet cannot be accessed from a container, perform the following operations:

#. View the security group rule of the peer server to check whether the container is allowed to access the peer server.

   -  The container tunnel network model needs to allow the IP address of the node where the container is located.
   -  The VPC network model needs to allow the container CIDR block.
   -  The Cloud Native Network 2.0 model needs to allow the subnet where the container is located.

#. Check whether a trustlist is configured for the peer server. For example, the DCS Redis instance can be accessed only by the IP addresses in its trustlist. Add the container and node CIDR blocks to the trustlist.
#. Check whether the container engine is installed on the peer server and whether it conflicts with the container CIDR block in CCE. If a network conflict occurs, the access fails.
