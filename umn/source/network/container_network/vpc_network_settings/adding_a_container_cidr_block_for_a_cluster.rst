:original_name: cce_10_0680.html

.. _cce_10_0680:

Adding a Container CIDR Block for a Cluster
===========================================

Scenario
--------

If the container CIDR block configured during CCE cluster creation cannot meet service expansion requirements, you can add a container CIDR block for the cluster.

Notes and Constraints
---------------------

-  This function is available only for clusters of v1.19 or later using a VPC network.
-  An added container CIDR block cannot be deleted.

Adding a Container CIDR Block for a CCE Standard Cluster
--------------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. On the **Overview** page, locate the **Networking Configuration** area and click **Add**.
#. Configure the container CIDR block to be added. You can click |image1| to add multiple container CIDR blocks at a time.

   .. note::

      New container CIDR blocks cannot conflict with service CIDR blocks, VPC CIDR blocks, and existing container CIDR blocks.

#. Click **OK**.

.. |image1| image:: /_static/images/en-us_image_0000001981436665.png
