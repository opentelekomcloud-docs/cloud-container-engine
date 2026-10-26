:original_name: cce_10_0084.html

.. _cce_10_0084:

Enabling ICMP Security Group Rules
==================================

Scenario
--------

When using a UDP load balancer, health checks also use UDP. Since UDP is connectionless, ICMP is required to verify network connectivity. Therefore, ICMP security group rules must be enabled for backend servers.

Procedure
---------

#. Log in to the VPC console and choose **Access Control** > **Security Groups**.
#. In the security group list, locate the security group of the cluster. Click **Manage Rules** in the **Operation** column. On the page displayed, click **Add Rule** to add the inbound rules below.

   +--------------+--------------------+---------------------------------------------------------------------------------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Cluster Type | Load Balancer Type | Security Group                                                                              | Protocol & Port | Allowed Source CIDR Block                                                                                                                                                                                |
   +==============+====================+=============================================================================================+=================+==========================================================================================================================================================================================================+
   | CCE Standard | Shared             | Node security group, which is named in the format of "{Cluster name}-cce-node-{Random ID}". | All ICMP ports  | 100.125.0.0/16 for the shared load balancer                                                                                                                                                              |
   |              |                    |                                                                                             |                 |                                                                                                                                                                                                          |
   |              |                    | If a custom node security group is bound to the cluster, select the target security group.  |                 |                                                                                                                                                                                                          |
   +--------------+--------------------+---------------------------------------------------------------------------------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   |              | Dedicated          | Node security group, which is named in the format of "{Cluster name}-cce-node-{Random ID}". | All ICMP ports  | Backend subnet of the load balancer                                                                                                                                                                      |
   |              |                    |                                                                                             |                 |                                                                                                                                                                                                          |
   |              |                    | If a custom node security group is bound to the cluster, select the target security group.  |                 | To obtain subnet CIDR blocks, log in to the ELB console, choose **Load Balancers**, and click the name of the target load balancer. On the **Summary** tab, click the link following the backend subnet. |
   +--------------+--------------------+---------------------------------------------------------------------------------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | CCE Turbo    | Shared             | Node security group, which is named in the format of "{Cluster name}-cce-node-{Random ID}". | All ICMP ports  | 100.125.0.0/16 for the shared load balancer                                                                                                                                                              |
   |              |                    |                                                                                             |                 |                                                                                                                                                                                                          |
   |              |                    | If a custom node security group is bound to the cluster, select the target security group.  |                 |                                                                                                                                                                                                          |
   +--------------+--------------------+---------------------------------------------------------------------------------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   |              | Dedicated          | ENI security group, which is named in the format of "{Cluster name}-cce-eni-{Random ID}".   | All ICMP ports  | Backend subnet of the load balancer                                                                                                                                                                      |
   |              |                    |                                                                                             |                 |                                                                                                                                                                                                          |
   |              |                    | If a custom security group is bound to the cluster, select the target security group.       |                 | To obtain subnet CIDR blocks, log in to the ELB console, choose **Load Balancers**, and click the name of the target load balancer. On the **Summary** tab, click the link following the backend subnet. |
   +--------------+--------------------+---------------------------------------------------------------------------------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **OK**.
