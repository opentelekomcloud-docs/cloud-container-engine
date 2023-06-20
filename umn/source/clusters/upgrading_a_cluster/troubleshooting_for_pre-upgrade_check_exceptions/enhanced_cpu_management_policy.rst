:original_name: cce_10_0480.html

.. _cce_10_0480:

Enhanced CPU Management Policy
==============================

Check Item
----------

Check whether the current cluster version and the target version support enhanced CPU policy.

Solution
--------

**Scenario**: The current cluster version uses the enhanced CPU management policy, but the target cluster version does not support the enhanced CPU management policy.

Upgrade the cluster to a version that supports the enhanced CPU management policy. The following table lists the cluster versions that support the enhanced CPU management policy.

.. table:: **Table 1** Cluster versions that support the enhanced CPU policy

   ================ =============================
   Cluster Version  Enhanced CPU Policy Supported
   ================ =============================
   v1.17 or earlier No
   v1.19            No
   v1.21            No
   v1.23 or later   Yes
   ================ =============================
