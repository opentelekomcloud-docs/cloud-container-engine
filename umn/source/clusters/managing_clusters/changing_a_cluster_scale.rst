:original_name: cce_10_0403.html

.. _cce_10_0403:

Changing a Cluster Scale
========================

Scenario
--------

A cluster scale specifies the maximum number of nodes a cluster can manage. If the current cluster scale cannot meet your requirements, you can scale it out.

Constraints
-----------

-  A cluster that has only one master node supports fewer than 1,000 worker nodes.
-  The number of master nodes cannot be changed when you change the cluster scale.
-  A cluster can only be scaled out, but cannot be scaled in.
-  During the cluster scale-out, master nodes are updated in rolling mode, and the cluster management function is restricted. To minimize the impact on services, you are advised to scale out the cluster during off-peak hours. Do not perform other operations (such as creating workloads) during this period.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Clusters**.

#. In the cluster list on the right, locate the target cluster. Click |image1| and choose **Modify Specifications** from the drop-down list.

#. On the page displayed, select a new cluster scale.

#. Click **Next** to confirm the specifications and click **OK**. Scaling out a cluster is typically completed within 30 minutes. The exact duration varies based on the cluster scale.

   You can click **Operation Records** in the upper right corner to view the cluster change history. The status changes from **Executing** to **Successful**, indicating that the cluster scale has been changed.

Helpful Links
-------------

When a cluster is scaled to 1,000 nodes or more, some parameter values of the cluster are adjusted accordingly to ensure optimal performance. For details, see :ref:`Modifying Cluster Configurations <cce_10_0213>`.

.. |image1| image:: /_static/images/en-us_image_0000002483959168.png
