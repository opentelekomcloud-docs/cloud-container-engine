:original_name: cce_10_0400.html

.. _cce_10_0400:

Accessing the Internet from a Container
=======================================

Containers can access the Internet in either of the following ways:

-  Bind an EIP to the node where the container is located if the network model is VPC or tunnel.
-  Bind an EIP to the target pod if the cluster uses a Cloud Native 2.0 network. For details, see :ref:`Configuring an EIP for a Pod in a CCE Turbo Cluster <cce_10_0734>`.
-  Configure SNAT rules through NAT Gateway.

You can use NAT Gateway to enable pods in a VPC to access the Internet. NAT Gateway provides source network address translation (SNAT), which translates private IP addresses to a public IP address using the assigned EIP. Pods in the VPC can share this EIP to access the Internet. :ref:`Figure 1 <cce_10_0400__en-us_topic_0261817696_en-us_topic_0241700138_en-us_topic_0144420145_fig34611314153619>` shows the SNAT architecture. SNAT allows the pods in a VPC to access the Internet without being bound to EIPs. SNAT supports a large number of concurrent connections, which makes it suitable for applications involving a large number of requests and connections.

.. _cce_10_0400__en-us_topic_0261817696_en-us_topic_0241700138_en-us_topic_0144420145_fig34611314153619:

.. figure:: /_static/images/en-us_image_0000002484119606.png
   :alt: **Figure 1** SNAT

   **Figure 1** SNAT

To enable a container to access the Internet, perform the following steps:

#. Obtain an EIP.

   a. Log in to the EIP console.
   b. On the **EIPs** page, click **Assign EIP**.
   c. Configure parameters as required.

      .. note::

         Set **Region** to the region where container pods are located.

#. Create a NAT gateway.

   a. Click |image1| in the upper left corner and choose **Networking** > **NAT Gateway** in the expanded list.
   b. On the **Public Network Gateways** page, click **Create Public NAT Gateway** in the upper right corner.
   c. Configure parameters as required.

      .. note::

         Select the same VPC.

#. Configure an SNAT rule and bind the EIP to the subnet.

   a. On the page displayed, click the name of the NAT gateway for which you want to add the SNAT rule.
   b. On the **SNAT Rules** tab page, click **Add SNAT Rule**.
   c. Configure parameters as required.

   .. note::

      SNAT rules take effect by CIDR block. As different container network models use different communication modes, the subnet needs to be selected according to the following rules:

      -  Tunnel network and VPC network: Select the subnet where the node is located, that is, the subnet selected during node creation.
      -  Cloud Native Network 2.0: Select the subnet where the container is located, that is, the container subnet selected during cluster creation.

      If there are multiple CIDR blocks, you can create multiple SNAT rules or customize a CIDR block as long as the CIDR block contains the container subnet (Cloud Native Network 2.0) or the node subnet.

   After the SNAT rule is configured, workloads can access the Internet from the container. The Internet can be pinged from the container.

.. |image1| image:: /_static/images/en-us_image_0000002516199581.png
