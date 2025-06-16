:original_name: cce_10_0403.html

.. _cce_10_0403:

Changing a Cluster Scale
========================

Scenario
--------

A cluster scale specifies the maximum number of nodes a cluster can manage. If the current cluster scale cannot meet your requirements, you can scale it out.

Notes and Constraints
---------------------

-  A cluster that has only one master node supports fewer than 1000 worker nodes.
-  The number of master nodes cannot be changed when you modify cluster specifications.
-  A cluster can only be scaled out to a larger specification, but cannot be scaled in.
-  To ensure proper cluster functionality, perform any specifications changes during off-peak hours. This is because master nodes will need to be powered off and on, which can interrupt normal operations.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Clusters**.

#. In the cluster list on the right, locate the target cluster. Click |image1| and choose **Modify Specifications** from the drop-down list.

#. On the page displayed, select a new cluster scale.

#. Click **Next** to confirm the specifications and click **OK**.

   You can click **Operation Records** in the upper right corner to view the cluster change history. The status changes from **Executing** to **Successful**, indicating that the cluster specifications are successfully changed.

Documentation
-------------

When a cluster is scaled to 1000 nodes or more, some parameter values of the cluster are automatically adjusted to ensure optimal performance. For details, see :ref:`Modifying Cluster Configurations <cce_10_0213>`.

.. |image1| image:: /_static/images/en-us_image_0000002218820166.png
