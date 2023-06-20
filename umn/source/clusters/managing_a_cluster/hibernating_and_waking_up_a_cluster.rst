:original_name: cce_10_0214.html

.. _cce_10_0214:

Hibernating and Waking Up a Cluster
===================================

Scenario
--------

If you do not need to use a cluster temporarily, you are advised to hibernate the cluster.

After a cluster is hibernated, resources such as workloads cannot be created or managed in the cluster.

A hibernated cluster can be quickly woken up and used normally.

Notes and Constraints
---------------------

During cluster wakeup, the master node may fail to be started due to insufficient resources. As a result, the cluster fails to be woken up. Wait for a while and wake up the cluster again.

Hibernating a Cluster
---------------------

#. Log in to the CCE console. In the navigation pane, choose **Clusters**.
#. Click |image1| next to the cluster to be hibernated.
#. In the dialog box displayed, check the precautions and click **Yes**. Wait until the cluster is hibernated.

Waking Up a Cluster
-------------------

#. Log in to the CCE console. In the navigation pane, choose **Clusters**.
#. Click |image2| next to the cluster to be woken up.
#. When the cluster status changes from **Waking** to **Running**, the cluster is woken up. It takes about 3 to 5 minutes to wake up the cluster.

.. |image1| image:: /_static/images/en-us_image_0000001517743460.png
.. |image2| image:: /_static/images/en-us_image_0000001569182589.png
