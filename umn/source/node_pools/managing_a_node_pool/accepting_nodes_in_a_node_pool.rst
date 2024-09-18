:original_name: cce_10_0886.html

.. _cce_10_0886:

Accepting Nodes in a Node Pool
==============================

If you want to add a newly created ECS to a node pool in a cluster, or remove a node from a node pool and add it to the node pool again, accept the node.

.. important::

   -  When an ECS is accepted for management, the ECS OS will be reset to the standard image provided by CCE to ensure node stability.
   -  LVM information, including volume groups (VGs), logical volumes (LVs), and physical volumes (PVs) will be deleted from the system disks and data disks attached to the selected ECSs during acceptance. Ensure that the information has been backed up.
   -  During the acceptance of an ECS, do not perform any operation on the ECS through the ECS console.
   -  After a node is managed by a node pool, if a scaling-in task is triggered in the node pool, the node will be deleted.

Procedure
---------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane. In the right pane, click the **Node Pools** tab.
#. Choose **More** > **Accept Node** in the operation bar of the target node pool.
#. Select one or more nodes that meet the requirements.

   -  The nodes and the current node pool are deployed in the same VPC and subnet.
   -  The nodes and the current node pool belong to the same enterprise project.
   -  The billing mode of the nodes must be the same as that of the current node pool. For example, a pay-per-use node pool can accept only pay-per-use nodes.
   -  The cloud server group of the nodes must be the same as that of the current node pool.
   -  The nodes must be running and cannot be labeled with **CCE-Dynamic-Provisioning-Node**.
   -  Data disks must be attached to the nodes. A local disk (disk-intensive disk) or a data disk of at least 20 GiB can be attached to the node, and any data disks already attached cannot be smaller than 10 GiB.
   -  The flavor of the nodes must be at least 2 vCPUs and 4 GiB memory, and only one NIC can be bound to each of the nodes.
   -  Only the cloud servers that have the same flavor, AZ, resource reservation, system disk, and data disk configurations as the node pool can be added for batch acceptance.
   -  Partitioned disks on cloud servers will not be accepted as data disks. Back up data and clear the disks before node acceptance.

#. Click **OK**.
