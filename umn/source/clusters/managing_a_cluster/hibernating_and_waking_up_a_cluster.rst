:original_name: cce_10_0214.html

.. _cce_10_0214:

Hibernating and Waking Up a Cluster
===================================

Scenario
--------

If you do not need to use a cluster temporarily, hibernate the cluster.

After a cluster is hibernated, resources such as workloads cannot be created or managed in the cluster.

A hibernated cluster can be quickly woken up and used properly.

Constraints
-----------

-  A cluster may fail to be woken up if the master nodes cannot start due to insufficient resources. Wait for a while and wake up the cluster again.
-  After a cluster is hibernated, it takes 3 to 5 minutes to initialize data. Services can be delivered only after the cluster runs properly.

Hibernating a Cluster
---------------------

#. Log in to the CCE console. In the navigation pane, choose **Clusters**.
#. Locate the cluster to be hibernated, click **...** to view more operations on the cluster, and choose **Hibernate**.
#. In the dialog box displayed, check the precautions and click **Yes**. Wait until the cluster is hibernated.

Waking Up a Cluster
-------------------

#. Log in to the CCE console. In the navigation pane, choose **Clusters**.
#. Click **Wake Up** in the row of the target cluster.
#. When the cluster status changes from **Waking up** to **Running**, the cluster is woken up. It takes about 3 to 5 minutes to wake up the cluster.
