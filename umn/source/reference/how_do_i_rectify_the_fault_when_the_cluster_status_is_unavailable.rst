:original_name: cce_faq_00039.html

.. _cce_faq_00039:

How Do I Rectify the Fault When the Cluster Status Is Unavailable?
==================================================================

If the cluster is **Unavailable**, perform the following operations to rectify the fault:

Fault Locating
--------------

-  :ref:`Check Item 1: Whether the Security Group Is Modified <cce_faq_00039__section48059154014>`
-  :ref:`Check Item 2: Whether the DHCP Function of the Subnet Is Disabled <cce_faq_00039__section11822101617614>`

.. _cce_faq_00039__section48059154014:

Check Item 1: Whether the Security Group Is Modified
----------------------------------------------------

#. Log in to the management console, choose **Service List** > **Network** > **Virtual Private Cloud**. In the navigation pane, choose **Access Control** > **Security Groups** to find the security group of the master node in the cluster.

   The name of this security group is in the format of cluster name-cce-**control**-ID.

#. Click the security group. On the details page that is displayed, ensure that the security group rules of the master node are the same as those marked by the red frames in the following figure.


   .. figure:: /_static/images/en-us_image_0000001223473841.png
      :alt: **Figure 1** Viewing inbound rules of the security group

      **Figure 1** Viewing inbound rules of the security group

   Inbound rule parameter description:

   -  **4789**: used for network access between containers.
   -  **5443-5444**: used by kubelet of the node to listen to kube-api of the master node.
   -  **9443**: used by canal of the node to listen to canal-api of the master node.
   -  **8445**: used by storage_driver of the node to access csms-storagemgr of the master node.


   .. figure:: /_static/images/en-us_image_0000001178192662.png
      :alt: **Figure 2** Viewing outbound rules of the security group

      **Figure 2** Viewing outbound rules of the security group

.. _cce_faq_00039__section11822101617614:

Check Item 2: Whether the DHCP Function of the Subnet Is Disabled
-----------------------------------------------------------------

#. On the CCE console, click the unavailable cluster. The details page is displayed. View the VPC and subnet where the cluster is located. Assume that the VPC and subnet names are **vpc-test** and **subnet-test** respectively.

#. On the management console, choose **Service List** > **Network** > **Virtual Private Cloud**. Click the VPC name to check whether the DHCP function of the corresponding subnet is enabled.

   If the DHCP function is disabled, the IP address allocated by the subnet will be reclaimed. As a result, the network in the cluster is disconnected and the cluster is abnormal.

   In this case, enable the DHCP function again. After this function is enabled, the subnet allocates the original IP address to the original node and the cluster can automatically recover.

   Currently, the VPC console no longer supports enabling or disabling DHCP for a VPC. You can change the configuration using APIs. For details, see the description of the **dhcp_enable** field in `Updating Subnet Information <https://docs.otc.t-systems.com/api/vpc/vpc_subnet01_0004.html>`__.


   .. figure:: /_static/images/en-us_image_0000001223473843.png
      :alt: **Figure 3** DHCP description in the VPC API Reference

      **Figure 3** DHCP description in the VPC API Reference
