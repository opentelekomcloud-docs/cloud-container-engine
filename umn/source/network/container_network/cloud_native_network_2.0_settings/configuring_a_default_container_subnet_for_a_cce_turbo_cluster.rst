:original_name: cce_10_0906.html

.. _cce_10_0906:

Configuring a Default Container Subnet for a CCE Turbo Cluster
==============================================================

Scenario
--------

If the pod subnet configured during CCE Turbo cluster creation cannot meet service expansion requirements, you can add a pod subnet for the cluster.

Notes and Constraints
---------------------

-  This function is available only for CCE Turbo clusters of v1.19 or later.

Procedure for Adding a Default Subnet
-------------------------------------

#. Log in to the CCE console and access the CCE Turbo cluster console.
#. On the **Overview** page, locate the **Networking Configuration** area and click **Add**.
#. Select a pod subnet in the same VPC. You can add multiple pod subnets at a time. If no other pod subnet is available, go to the VPC console and create one.
#. Click **OK**.

Procedure for Deleting a Default Subnet
---------------------------------------

.. note::

   Pod subnets can be deleted from clusters of v1.23.17-r0, v1.25.12-r0, v1.27.9-r0, v1.28.7-r0, v1.29.3-r0, or later versions.

#. Log in to the CCE console and access the CCE Turbo cluster console.
#. On the **Settings** page, click the **Network** tab.
#. In the **Container Network** area, click **Update** in the **Operation** column of **default-network (Default Container Subnet)**.
#. In the **Pod Subnet** area, **deselect** the subnets to be deleted and click **OK**.

   .. important::

      -  Deleting a pod subnet is risky. Make sure that no ENI in the cluster is using the subnet that you plan to delete. This includes ENIs that are being used by pods and prebound ENIs in the cluster.

         To find out if any ENIs are being used by a cluster, copy the ID of the subnet that you plan to delete. Then, access **Network Console** and use this ID to filter the ENIs and sub-ENIs associated with the VPC subnet on the **Network Interfaces** tab page. If the name or description of any of the filtered ENIs contains a cluster ID, then those ENIs are being used by the cluster.

      -  After a subnet is deleted, the security group of the cluster node will not automatically remove the rules associated with the subnet. Make sure that no ENIs in the cluster are still using the subnet and manually clear these rules that are associated with it.
