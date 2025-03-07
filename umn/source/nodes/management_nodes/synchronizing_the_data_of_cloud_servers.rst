:original_name: cce_10_0184.html

.. _cce_10_0184:

Synchronizing the Data of Cloud Servers
=======================================

Scenario
--------

Each node in a cluster is a cloud server or physical machine. After a cluster node is created, you can change the cloud server name or specifications as required. Modifying node specifications will affect services. Perform the operation on nodes one by one.

Some information of CCE nodes is maintained independently from the ECS console. After you change the name, EIP, or specifications of an ECS on the ECS console, synchronize the ECS with the target node on the CCE console. After the synchronization, information on both consoles is consistent.

Notes and Constraints
---------------------

-  Data, including the VM status, ECS names, number of CPUs, size of memory, ECS specifications, and public IP addresses, can be synchronized.
-  The following data cannot be synchronized: OS, image ID, and disk configuration.

Synchronizing the Data of a Cloud Server
----------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Nodes**. On the displayed page, click the **Nodes** tab.

#. Locate the target node and choose **More** > **Sync Server Data** in the **Operation** column.

   After the synchronization is complete, the **ECS data synchronization requested** message is displayed in the upper right corner.
