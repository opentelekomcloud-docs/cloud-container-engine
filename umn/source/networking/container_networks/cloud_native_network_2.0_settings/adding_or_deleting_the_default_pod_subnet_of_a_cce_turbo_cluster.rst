:original_name: cce_10_0906.html

.. _cce_10_0906:

Adding or Deleting the Default Pod Subnet of a CCE Turbo Cluster
================================================================

If the pod subnet configured during CCE Turbo cluster creation cannot meet service expansion requirements, you can add a pod subnet for the cluster.

Constraints
-----------

-  This function is only available for CCE Turbo clusters of v1.19 or later.

Modifying the Default Pod Subnet on the Overview Page
-----------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. On the **Overview** page, locate the **Networking Configuration** area and click **Edit**.
#. Add or delete the default pod subnet. You can select one or more subnets in the same VPC. If no other pod subnet is available, go to the VPC console and create one.

   .. caution::

      -  Pod subnets can be deleted from clusters of v1.23.17-r0, v1.25.12-r0, v1.27.9-r0, v1.28.7-r0, v1.29.3-r0, or later versions.
      -  To ensure that containers in a new CIDR block can access related services such as databases and middleware, allow the traffic from the new CIDR block on the required access ports in the security groups of related services.

#. Click **OK**.

Modifying the Default Pod Subnet on the Settings Page
-----------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. On the **Settings** page, click the **Network** tab.
#. In the **Container Network** area, click **Update** in the **Operation** column of **default-network (Default Container Subnet)**.
#. In the **Pod Subnet** area, **deselect** the subnets to be deleted and click **OK**.

Precautions for Deleting Container Subnets
------------------------------------------

When deleting container subnets, pay attention to the following:

-  CCE clusters of v1.25.16-r30, v1.27.16-r30, v1.28.15-r20, v1.29.13-r0, v1.30.10-r0, v1.31.6-r0, v1.32.1-r0, or later support automatic cleanup of requested network interfaces after subnets are deleted.

   -  Pre-allocated network interfaces are automatically reclaimed when the subnet is deleted.
   -  Network interfaces used by pods without a fixed IP address are automatically reclaimed when the pods are deleted.
   -  Network interfaces used by pods with a fixed IP address are automatically reclaimed after the pods are deleted and the fixed IP addresses expire.
   -  The eth1 network interface used by nodes is automatically reclaimed when the nodes are deleted or reset.

-  In CCE clusters of v1.25.16-r30, v1.27.16-r30, v1.28.15-r20, v1.29.13-r0, v1.30.10-r0, v1.31.6-r0, v1.32.1-r0, or earlier, network interfaces that have been allocated are not automatically reclaimed when a subnet is deleted. Before deleting a subnet, ensure that no network interfaces in the current cluster are using the subnet, including those used by pods and pre-allocated network interfaces.

   To find out if any network interfaces are being used by a cluster, copy the ID of the subnet that you plan to delete. Then, access **Network Console** and use this ID to filter the network interfaces and supplementary network interfaces associated with the VPC subnet on the **Network Interfaces** tab page. If the name or description of any of the filtered network interfaces contains a cluster ID, then those network interfaces are being used by the cluster.

-  After a subnet is deleted, the security group of the cluster node will not automatically remove the rules associated with the subnet. Make sure that no network interfaces in the cluster are still using the subnet and manually clear these rules that are associated with it.
