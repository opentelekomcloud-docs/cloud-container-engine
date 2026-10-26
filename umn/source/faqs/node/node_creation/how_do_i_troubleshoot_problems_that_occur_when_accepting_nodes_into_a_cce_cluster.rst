:original_name: cce_faq_00097.html

.. _cce_faq_00097:

How Do I Troubleshoot Problems That Occur When Accepting Nodes into a CCE Cluster?
==================================================================================

Overview
--------

This section describes how to troubleshoot the problems that occur when you accept or add existing ECSs to a CCE cluster.

.. important::

   -  While an ECS is being accepted into a cluster, the operating system of the ECS will be reset to the standard OS image provided by CCE to ensure node stability. The CCE console prompts you to select the operating system and the login mode during the reset.
   -  The ECS system and data disks will be formatted while the ECS is being accepted into a cluster. Ensure that data in the disks has been backed up.
   -  During the acceptance of an ECS, do not perform any operation on the ECS through the ECS console.

Notes and Constraints
---------------------

-  ECSs can be managed.

Prerequisites
-------------

The cloud servers to be managed must meet the following requirements:

-  The node to be accepted must be **Running** or **Stopped**, must not be used by other clusters, and must not have the **CCE-Dynamic-Provisioning-Node** label.
-  The node to be accepted and the cluster must be in the same VPC. (If the cluster version is earlier than v1.13.10, the node to be accepted and the CCE cluster must be in the same subnet.)
-  Data disks must be attached to the nodes to be managed if the system components of these nodes are stored separately. These nodes can be attached with either a local disk (disk-intensive disk) or a data disk of at least 20 GiB. Additionally, any data disks already attached must not be smaller than 10 GiB.
-  The node to be accepted must have at least 2 CPU cores, 4 GiB of memory, and only one network interface.
-  Only cloud servers with the same data disk configuration can be accepted in batches for management.
-  For IPv6-enabled clusters, only nodes with IPv6 enabled in the cluster's subnet can be accepted.
-  CCE Turbo clusters require that each node support supplementary network interfaces. Alternatively, you must bind at least 16 network interfaces per node. For details about node flavors, see the options provided on the console when creating a node.
-  Data disks that have been partitioned will be ignored during node management. Ensure that there is at least one unpartitioned data disk meeting the specifications attached to the node.

Procedure
---------

View the cluster log information to locate the failure cause and rectify the fault.

#. Log in to the CCE console and click **Operation Records** above the cluster list to view operation records.
#. View error information for records in the **Failed** state.
#. Rectify the fault based on the error information and accept the node into a cluster again.

Common Issues
-------------

Failed to Accept Node Because No Unpartitioned Disk Found
---------------------------------------------------------

**Symptom**

The node acceptance fails with the following error message:

.. code-block::

   disk /dev/vda has been partition, will skip this device\nRaw disk /dev/vdb has been partition, will skip this device\nwarning: selector can not match any evs volume

|image1|

**Possible Cause**

Both **/dev/vda** and **/dev/vdb** are partitioned. The node skips partitioned disks when selecting a data disk, causing the acceptance to fail.

Log in to the affected node and check the disk information.

.. code-block::

   lsblk

|image2|

Check whether any disks are available for use as CCE data disks. In the example above, **vda** is partitioned into **vda1**, and **vdb** is partitioned into **vdb1**. The CCE disk selection logic skips partitioned disks. If no unpartitioned disks are available, the reset or acceptance fails.

**Solution**

-  Solution 1: Attach an unpartitioned data disk of at least 20 GiB to the node. After the node is managed, this disk is used to store the container runtime and kubelet. Partitioned data disks are ignored and left untouched. You can manage them as needed.

-  Solution 2: Clear existing disk partitions and retry.

   .. caution::

      Clearing disk partitions erases all data on the disk. Back up any required data before proceeding.

   Run **fdisk** **<data_disk_name>**, enter **d**, specify the partition number, and enter **w** to save the changes.

   Example result

   |image3|

.. |image1| image:: /_static/images/en-us_image_0000002631532178.png
.. |image2| image:: /_static/images/en-us_image_0000002631692082.png
.. |image3| image:: /_static/images/en-us_image_0000002661851375.png
