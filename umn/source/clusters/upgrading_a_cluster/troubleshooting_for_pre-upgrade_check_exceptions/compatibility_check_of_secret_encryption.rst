:original_name: cce_10_0520.html

.. _cce_10_0520:

Compatibility Check of Secret Encryption
========================================

Check Items
-----------

Check whether the target version supports secret encryption. If it does not, clusters that have this feature enabled cannot be upgraded to the target version.

Solution
--------

Secret encryption is supported in CCE clusters of v1.27 or later versions. The feature is available in the following versions:

-  v1.27: v1.27.10-r0 or later
-  v1.28: v1.28.8-r0 or later
-  v1.29: v1.29.4-r0 or later
-  Other clusters of later versions

If secret encryption has been enabled in the cluster before the upgrade, the target cluster version must also support this feature. Select a target version that supports secret encryption for the upgrade.
