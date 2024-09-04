:original_name: cce_10_0403.html

.. _cce_10_0403:

Changing Cluster Scale
======================

Scenario
--------

CCE allows you to change the number of nodes managed in a cluster.

Notes and Constraints
---------------------

-  A cluster that has only one master node supports fewer than 1000 worker nodes.
-  The number of master nodes cannot be changed when you modify cluster specifications.
-  A cluster can only be scaled out to a larger specification, but cannot be scaled in.
-  To ensure proper cluster functionality, perform any specifications changes during off-peak hours. This is because master nodes will need to be powered off and on, which can disrupt normal operations.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Clusters**.

#. Locate the cluster whose specifications need to be modified, click **...** to view more operations on the cluster, and choose **Modify Specifications**.

#. On the page displayed, select a new cluster scale.

#. Click **Next** to confirm the specifications and click **OK**.

   You can click **Operation Records** in the upper right corner to view the cluster change history. The status changes from **Executing** to **Successful**, indicating that the cluster specifications are successfully changed.

   .. note::

      After the cluster scale is changed to 1000 nodes or more, some parameter values of the cluster will be automatically adjusted to ensure the cluster performance. For details, see :ref:`Modifying Cluster Configurations <cce_10_0213>`.
