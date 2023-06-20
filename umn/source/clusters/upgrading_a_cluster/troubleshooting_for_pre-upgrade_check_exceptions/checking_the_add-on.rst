:original_name: cce_10_0433.html

.. _cce_10_0433:

Checking the Add-on
===================

Check Item
----------

Check the following aspects:

-  Check whether the add-on status is normal.
-  Check whether the add-on supports the target version.

Solution
--------

-  **Scenario 1: The add-on status is abnormal.**

   Log in to the CCE console and go to the target cluster. Choose **O&M** > **Add-ons** to view and handle the abnormal add-on.

-  **Scenario 2: The target version does not support the current add-on.**

   The add-on cannot be automatically upgraded with the cluster. Log in to the CCE console and go to the target cluster. Choose **O&M** > **Add-ons** to manually upgrade the add-on.

-  **Scenario 3: The add-on does not support the target cluster even if the add-on is upgraded to the latest version. In this case, go to the cluster console and choose Cluster Information > O&M > Add-ons in the navigation pane to manually uninstall the add-on.**

   For details about the supported add-on versions and replacement solutions, see the :ref:`help document <cce_10_0277>`.
