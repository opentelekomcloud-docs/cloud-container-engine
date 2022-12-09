:original_name: cce_01_0214.html

.. _cce_01_0214:

Hibernating and Waking Up a Cluster
===================================

Scenario
--------

If you do not need to use a cluster temporarily, you are advised to hibernate the cluster to save cluster management costs.

After a cluster is hibernated, resources such as workloads cannot be created or managed in the cluster.

A hibernated cluster can be quickly woken up and used normally.

Hibernating a Cluster
---------------------

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Clusters**.
#. Choose **More** > **Hibernate** for the target cluster.
#. In the dialog box displayed, check the precautions and click **Yes**. Wait until the cluster is hibernated.

   .. important::

      -  After a cluster is hibernated, resources, such as worker nodes (ECSs), bound EIPs, and bandwidth, are still billed based on their own billing modes. To shut down nodes, select **Stop all nodes in the cluster** in the dialog box or see :ref:`Stopping a Node <cce_01_0036>`.

#. When the cluster status changes from **Hibernating** to **Hibernation**, the cluster is hibernated.

Waking Up a Cluster
-------------------

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Clusters**.
#. Choose **More** > **Wake**.
#. In the dialog box displayed, click **Yes** and wait until the cluster is woken up.
#. When the cluster status changes from **Waking** to **Available**, the cluster is woken up.

   .. note::

      After the cluster is woken up, billing will be resumed for the resources on the master node.
