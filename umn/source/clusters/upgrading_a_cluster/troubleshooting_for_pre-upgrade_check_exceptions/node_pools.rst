:original_name: cce_10_0436.html

.. _cce_10_0436:

Node Pools
==========

Check Items
-----------

-  Check the node pool status.
-  Check whether the node pool OS or container runtime is supported after the upgrade.

Solution
--------

-  **Scenario: The node pool malfunctions.**

   Log in to the CCE console and click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane and view the status of the affected node pool on the **Node Pools** tab. If the node pool is being scaled, wait until the node pool scaling is complete.

-  **Scenario: The node pool OS is not supported.**

   The runtime and OS vary depending on the cluster version. This issue typically occurs when a cluster of an earlier version is upgraded to v1.27 or later.

   Log in to the CCE console and click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane, view the status of the affected node pool on the **Node Pools** tab, and click **Upgrade**. Change the supported OSs based on the pre-upgrade check result, and click **OK**.

   If there are nodes in the affected node pool, choose **More** > **Synchronize** in the operation column to synchronize the OS of the existing nodes. For details, see :ref:`Synchronizing Node Pools <cce_10_0654>`.
