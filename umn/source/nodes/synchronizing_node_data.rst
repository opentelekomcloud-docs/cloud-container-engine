:original_name: cce_01_0184.html

.. _cce_01_0184:

Synchronizing Node Data
=======================

Scenario
--------

Each node in a cluster is a cloud server or physical machine. After a cluster node is created, you can change the cloud server name or specifications as required.

Some information about CCE nodes is maintained independently from the ECS console. After you change the name, EIP, billing mode, or specifications of an ECS on the ECS console, you need to **synchronize the ECS information** to the corresponding node on the CCE console. After the synchronization, information on both consoles is consistent.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Nodes**.

#. In the same row as the node whose data will be synchronized, choose **More** > **Sync Node data**.

   .. note::

      Alternatively, click the node name, and click **Sync Node Data** in the upper right corner of the node details page.


   .. figure:: /_static/images/en-us_image_0000001144502022.png
      :alt: **Figure 1** Synchronizing node data

      **Figure 1** Synchronizing node data

   After the synchronization is complete, the "Sync success" message is displayed in the upper right corner.
