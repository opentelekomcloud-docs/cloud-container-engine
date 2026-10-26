:original_name: cce_faq_00417.html

.. _cce_faq_00417:

How Do I Configure an Access Policy for a Cluster?
==================================================

After the public API Server address is bound to the cluster, modify the security group rules of port 5443 on the master node to harden the access control policy of the cluster.

#. Log in to the CCE console. On the **Cluster Management** page, hover over the cluster name, click |image1| next to **Cluster ID**, and copy and save the value.
#. Log in to the VPC console. In the navigation pane, choose **Access Control** > **Security Groups**.
#. Select **Description** as the filter criterion and search for the target security group by cluster ID.
#. Locate the row that contains the security group (starting with *{CCE cluster name}*\ **-cce-control**) of the master node and click **Manage Rules** in the **Operation** column.
#. On the page displayed, locate the row that contains port 5443 and click **Modify** in the **Operation** column to modify its inbound rules.
#. Change the source IP address that can be accessed as required. For example, if the IP address used by the client to access the API Server is **100.*.*.\***, you can add an inbound rule for port 5443 and set the source IP address to **100.*.*.\***.

   .. note::

      In addition to the client IP address, the port must allow traffic from and to the CIDR blocks of the VPC and container to ensure that the API Server can be accessed from the cluster.

#. Click **Confirm**.

.. |image1| image:: /_static/images/en-us_image_0000002631532144.png
