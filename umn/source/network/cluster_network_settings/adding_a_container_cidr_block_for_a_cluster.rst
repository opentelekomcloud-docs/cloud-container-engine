:original_name: cce_10_0680.html

.. _cce_10_0680:

Adding a Container CIDR Block for a Cluster
===========================================

Scenario
--------

If the container CIDR block (container subnet in a CCE Turbo cluster) set during CCE cluster creation is insufficient, you can add a container CIDR block for the cluster.

Constraints
-----------

-  This function applies to CCE clusters and CCE Turbo clusters of v1.19 or later, but not to clusters using container tunnel networking.
-  The container CIDR block or container subnet cannot be deleted after being added. Exercise caution when performing this operation.

Adding a Container CIDR Block for a CCE Cluster
-----------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. On the **Cluster Information** page, click **Add Container CIDR Block** in the **Networking Configuration** area.
#. Configure the container CIDR block to be added. You can click |image1| to add multiple container CIDR blocks at a time.

   .. note::

      New container CIDR blocks cannot conflict with service CIDR blocks, VPC CIDR blocks, and existing container CIDR blocks.

#. Click **OK**.

Adding a Container Subnet for a CCE Turbo Cluster
-------------------------------------------------

#. Log in to the CCE console and access the CCE Turbo cluster console.
#. On the **Cluster Information** page, locate the **Networking Configuration** area and click **Add Pod Subnet**.
#. Select a container subnet in the same VPC. You can add multiple container subnets at a time. If no other container subnet is available, go to the VPC console to create one.
#. Click **OK**.

.. |image1| image:: /_static/images/en-us_image_0000001647417744.png
