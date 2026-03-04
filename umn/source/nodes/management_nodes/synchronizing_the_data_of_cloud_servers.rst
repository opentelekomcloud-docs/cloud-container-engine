:original_name: cce_10_0184.html

.. _cce_10_0184:

Synchronizing the Data of Cloud Servers
=======================================

Scenario
--------

Each node in a cluster is a cloud server. After a cluster node is created, you can change the cloud server name or specifications as required. Modifying node specifications will affect services. Perform the operation on nodes one by one.

Some CCE node information is maintained independently of the cloud server console. After changing a cloud server's name, EIP, or specifications on the cloud server console, synchronize it with the target node on the CCE console to ensure consistency.

Constraints
-----------

-  Data, including the statuses, names, number of CPUs, memory, specifications, and public IP addresses of cloud servers, can be synchronized.
-  The following data cannot be synchronized: OS, image ID, and disk configuration.

Synchronizing the Data of a Cloud Server
----------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Nodes**. On the displayed page, click the **Nodes** tab.

#. Locate the target node and choose **More** > **Sync Server Data** in the **Operation** column.

   After the synchronization is complete, the "ECS data synchronization requested" message is displayed in the upper right corner.
