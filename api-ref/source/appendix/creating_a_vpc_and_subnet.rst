:original_name: cce_02_0100.html

.. _cce_02_0100:

Creating a VPC and Subnet
=========================

Context
-------

To provide a secure and isolated network environment for CCE, create a VPC before creating a cluster.

If you have already created a VPC, you do not need to create it again.

Creating a VPC
--------------

#. On the management console, click **Service List**, and choose **Network** > **Virtual Private Cloud** to launch the VPC console.

#. On the VPC console, click **Create VPC**.

#. The created VPC is displayed in the list. Click its name and obtain the VPC ID, which will be required in :ref:`Creating a Cluster <cce_02_0236>`.


   .. figure:: /_static/images/en-us_image_0233731209.png
      :alt: **Figure 1** Obtaining the VPC ID

      **Figure 1** Obtaining the VPC ID

Creating a Subnet
-----------------

#. On the management console, click **Service List**, and choose **Network** > **Virtual Private Cloud** to launch the VPC console.

#. In the navigation pane, choose **Subnets**. On the displayed page, click **Create Subnets**.

#. After the subnet is created, click its name to obtain the network ID, which will be required in :ref:`Creating a Cluster <cce_02_0236>`.


   .. figure:: /_static/images/en-us_image_0233732535.png
      :alt: **Figure 2** Obtaining the network ID of a subnet

      **Figure 2** Obtaining the network ID of a subnet
