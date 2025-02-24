:original_name: cce_faq_00097.html

.. _cce_faq_00097:

How Do I Troubleshoot Problems Occurred When Accepting Nodes into a CCE Cluster?
================================================================================

Overview
--------

This section describes how to troubleshoot the problems occurred when you accept or add existing ECSs to a CCE cluster.

.. important::

   -  While an ECS is being accepted into a cluster, the operating system of the ECS will be reset to the standard OS image provided by CCE to ensure node stability. The CCE console prompts you to select the operating system and the login mode during the reset.
   -  The ECS system and data disks will be formatted while the ECS is being accepted into a cluster. Ensure that data in the disks has been backed up.
   -  While an ECS is being accepted into a cluster, do not perform any operation on the ECS through the ECS console.

Notes and Constraints
---------------------

-  ECSs can be managed.

Prerequisites
-------------

The cloud servers to be managed must meet the following requirements:

-  The node to be accepted must be in the **Running** state and not used by other clusters. In addition, the node to be accepted does not carry the CCE-Dynamic-Provisioning-Node tag.
-  The node to be accepted and the cluster must be in the same VPC. (If the cluster version is earlier than v1.13.10, the node to be accepted and the CCE cluster must be in the same subnet.)
-  Data disks must be attached to the nodes to be managed if the system components of these nodes are separately stored. A local disk (disk-intensive disk) or a data disk of at least 20 GiB can be attached to the node, and any data disks already attached cannot be smaller than 10 GiB.
-  The node to be accepted has 2-core or higher CPU, 4 GiB or larger memory, and only one NIC.
-  Only cloud servers with the same data disk configuration can be accepted in batches for management.
-  If IPv6 is enabled for a cluster, only nodes in a subnet with IPv6 enabled can be accepted and managed. If IPv6 is not enabled for the cluster, only nodes in a subnet without IPv6 enabled can be accepted.
-  Nodes in a CCE Turbo cluster must support sub-ENIs or be bound to at least 16 ENIs. For details about the node flavors, see the node flavors that can be selected on the console when you create a node.
-  Data disks that have been partitioned will be ignored during node management. Ensure that there is at least one unpartitioned data disk meeting the specifications is attached to the node.

Procedure
---------

View the cluster log information to locate the failure cause and rectify the fault.

#. Log in to the CCE console and click **Operation Records** above the cluster list to view operation records.
#. Click the record of the **Failed** status to view error information.
#. Rectify the fault based on the error information and accept the node into a cluster again.

Common Issues
-------------

If a node fails to be managed, a message will be displayed, indicating that the disk partitioning does not work:

.. code-block::

   Install config-prepare failed: exit status 1, output: [ Mon Jul 17 14:26:10 CST 2023 ] start install config-prepare\nNAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINT\nsda 8:0 0 40G 0 disk \n└─sda1 8:1 0 40G 0 part /\nsdb 8:16 0 100G 0 disk \n└─sdb1 8:17 0 100G 0 part
   disk /dev/sda has been partition, will skip this device\nRaw disk /dev/sdb has been partition, will skip this device\nwarning: selector can not match any evs volume

To resolve this issue, attach an unpartitioned data disk of 20 GiB or higher to the node. After the node is managed, the unpartitioned data disk is used to store the container engine and kubelet. You can perform operations on the partitioned data disk that does not work as required.
