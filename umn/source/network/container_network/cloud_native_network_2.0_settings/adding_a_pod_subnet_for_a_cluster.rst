:original_name: cce_10_0906.html

.. _cce_10_0906:

Adding a Pod Subnet for a Cluster
=================================

Scenario
--------

If the pod subnet configured during CCE Turbo cluster creation cannot meet service expansion requirements, you can add a pod subnet for the cluster.

Notes and Constraints
---------------------

-  This function is available only for CCE Turbo clusters of v1.19 or later.

Procedure
---------

#. Log in to the CCE console and access the CCE Turbo cluster console.
#. On the **Overview** page, locate the **Networking Configuration** area and click **Add**.
#. Select a pod subnet in the same VPC. You can add multiple pod subnets at a time. If no other pod subnet is available, go to the VPC console and create one.
#. Click **OK**.
