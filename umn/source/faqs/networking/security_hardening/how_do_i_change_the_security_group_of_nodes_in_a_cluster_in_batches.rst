:original_name: cce_faq_00392.html

.. _cce_faq_00392:

How Do I Change the Security Group of Nodes in a Cluster in Batches?
====================================================================

Notes and Constraints
---------------------

Do not add more than 1000 instances to the same security group. Otherwise, the security group performance may deteriorate.

Procedure
---------

#. Log in to the VPC console and select the desired region and project in the upper left corner.

#. In the navigation pane on the left, choose **Access Control** > **Security Groups**.

#. On the **Security Groups** page, click **Manage Instance** in the **Operation** column.

#. On the **Servers** tab page, click **Add**.

#. Select the servers to be added to the security group and click **OK**. You can also search for servers by name, ID, private IP address, status, enterprise project, or tag.

   You can change the maximum number of servers displayed on a page in the lower left corner to add a maximum of 20 servers to a security group at a time.

   .. note::

      After the node is added to a new security group, the original security group is retained. To remove the instance, click **Manage Instance** of the original security group and select the node servers to be removed.
