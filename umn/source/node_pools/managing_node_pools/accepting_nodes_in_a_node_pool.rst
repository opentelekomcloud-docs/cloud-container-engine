:original_name: cce_10_0886.html

.. _cce_10_0886:

Accepting Nodes in a Node Pool
==============================

If you want to add a newly created ECS to a node pool in a cluster, or remove a node from a node pool and add it to the node pool again, accept the node.

.. important::

   -  When an ECS is accepted for management, the ECS OS will be reset to the standard image provided by CCE to ensure node stability.
   -  During the acceptance process, LVM information, including volume groups (VGs), logical volumes (LVs), and physical volumes (PVs), will be deleted from the system disks and data disks attached to the selected ECSs. Ensure this information has been backed up.
   -  During the acceptance of an ECS, do not perform any operation on the ECS through the ECS console.
   -  After a node is added to a node pool, it is subject to deletion if a scale-in task is triggered.

Procedure
---------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane. In the right pane, click the **Node Pools** tab.
#. Choose **More** > **Accept Node** in the operation bar of the target node pool.
#. Select one or more nodes that meet the requirements.

   -  The nodes and the current node pool are deployed in the same VPC and subnet.
   -  The cloud server group of the nodes is the same as that used by the current node pool.
   -  The nodes must be running and cannot be labeled with **CCE-Dynamic-Provisioning-Node**.
   -  Data disks must be attached to the nodes to be managed if the system components of these nodes are stored separately. These nodes can be attached with either a local disk (disk-intensive disk) or a data disk of at least 20 GiB. Additionally, any data disks already attached must not be smaller than 10 GiB.
   -  The node to be accepted must have at least 2 CPU cores, 4 GiB of memory, and only one network interface.
   -  The nodes use the same flavor, AZ, resource reservation, system disk, and data disk as those of the node pool.
   -  Partitioned disks on cloud servers will not be accepted as data disks. Back up data and clear the disks before node acceptance.

#. Click **OK**.

FAQs
----

Can I Add Nodes of Different Flavors to a Node Pool?
----------------------------------------------------

Yes. A CCE node pool can manage multiple node flavors. However, before adding a node, ensure its flavor is included in the node pool's flavor list.

How Do I Move a Node Across Clusters?
-------------------------------------

Cross-cluster node migration is not supported directly. To migrate a node, remove it from the source cluster and add it to the target cluster. Note that this process is subject to specific restrictions and may not be supported in all scenarios, for example, the node flavor may not be available in the target cluster.

Always verify node migration in a test environment before performing it in production.

Will Data on a Node Be Cleared When the Node Is Accepted for Management?
------------------------------------------------------------------------

Yes. When a node is accepted, it is reset to a standard image and **all existing data is cleared**. Back up any required data before proceeding.
