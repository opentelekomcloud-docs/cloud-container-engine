:original_name: cce_10_0531.html

.. _cce_10_0531:

Add-on Configuration Consistency
================================

Check Items
-----------

Manual modifications to add-on configuration parameters (typically ConfigMaps), instead of modifications through the CCE console or APIs, may be overwritten after an upgrade, potentially affecting service operation.

Solution
--------

-  **Non-forced upgrades**

   -  When upgrading your cluster, if the add-on version is compatible with both the source and target cluster versions, you need to determine whether to upgrade the add-on version.
   -  If the add-on configurations are inconsistent but a forced upgrade is not required, you can skip this check item and leave the add-on upgrade option unselected during the cluster upgrade.

-  **Forced upgrades**

   -  If the add-on version is **no longer compatible** with the upgraded cluster version, you must upgrade the add-on version.
   -  If the add-on configurations are inconsistent and a forced upgrade is required, you cannot skip this check item. In this case, reconfigure the add-on parameters through the **CCE console or APIs** to ensure configuration consistency before proceeding with the upgrade.
