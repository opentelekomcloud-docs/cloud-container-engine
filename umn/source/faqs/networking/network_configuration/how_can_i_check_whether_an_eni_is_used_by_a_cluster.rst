:original_name: cce_faq_00446.html

.. _cce_faq_00446:

How Can I Check Whether an ENI Is Used by a Cluster?
====================================================

Scenarios
---------

Pod subnets can be deleted from CCE Turbo clusters of v1.23.17-r0, v1.25.12-r0, v1.27.9-r0, v1.28.7-r0, v1.29.3-r0, or later versions.

Deleting a pod subnet from a cluster can be risky. It is important to ensure that none of the ENIs currently in use by the cluster belong to the subnet, including those being used by pods and pre-bound to pods.

Procedure
---------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Settings** and click the **Network** tab.

#. In the **Container Network** area, copy the network ID of the subnet. (The default-network is used as an example.)

#. Log in to the VPC console. In the navigation pane, choose **Virtual Private Cloud** > **Subnets**. In the right pane, obtain the target subnet based on the network ID.

#. Click the subnet name to access the details page. Click the **Summary** tab, locate the **Resources** area, click the number next to **Network Interfaces**, and view the network interfaces associated with the subnet.

#. Check the names or descriptions of the network interfaces. If the name or description of a network interface contains the ID of the cluster, it indicates that the network interface is used by the cluster. You can obtain the cluster ID on the **Overview** page of the CCE console.

   To delete the subnet ENIs used in the cluster, submit a service ticket.
