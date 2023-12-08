:original_name: cce_10_0084.html

.. _cce_10_0084:

Enabling ICMP Security Group Rules
==================================

Scenario
--------

If a workload uses UDP for both load balancing and health check, enable ICMP security group rules for the backend servers.

Procedure
---------

#. Log in to the ECS console, find the ECS corresponding to any node where the workload runs, and click the ECS name. On the displayed ECS details page, record the security group name.
#. Log in to the VPC console. In the navigation pane on the left, choose **Access Control > Security Groups**. In the security group list on the right, click the security group name obtained in step 1.
#. On the page displayed, click the **Inbound Rules** tab and click **Add Rule** to add an inbound rule for ECS. Then, click **OK**.

   .. note::

      -  You only need to add security group rules to any node where the workload runs.
      -  The security group must have rules to allow access from the CIDR block 100.125.0.0/16.
