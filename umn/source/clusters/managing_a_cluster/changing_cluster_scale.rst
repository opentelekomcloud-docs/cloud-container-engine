:original_name: cce_10_0403.html

.. _cce_10_0403:

Changing Cluster Scale
======================

Scenario
--------

CCE allows you to change the number of nodes managed in a cluster.

Constraints
-----------

-  This function is supported for clusters of v1.15 and later versions.
-  Starting from v1.15.11, the number of nodes in a cluster can be changed to 2000. The number of nodes in a single master node cannot be changed to 1000 or more.
-  The number of master nodes cannot be changed when you modify cluster specifications.
-  Currently, a cluster can only be scaled out to a larger specification, but cannot be scaled in.
-  During the specifications change, master nodes will be powered off and on, and the cluster cannot run properly. Perform the change during off-peak hours.
-  Changing the cluster scale does not affect the services running in the cluster. However, the control plane (master nodes) will be interrupted for a short period of time. You are advised not to perform any other operations (such as creating workloads) during the change.
-  Change failures will trigger a cluster rollback to the normal state. If the rollback fails, submit a service ticket.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Clusters**.

#. Locate the cluster whose specifications need to be modified, click **...** to view more operations on the cluster, and choose **Specification change**.

#. On the page displayed, select a new cluster scale.

#. Click **Next** to confirm the specifications and click **OK**.

   You can click **Operation Records** in the upper right corner to view the cluster change history. The status changes from **Executing** to **Successful**, indicating that the cluster specifications are successfully changed.

   .. note::

      After the cluster scale is changed to 1000 nodes or more, some parameter values of the cluster will be automatically adjusted to ensure the cluster performance. For details, see :ref:`Cluster Configuration Management <cce_10_0213>`.
