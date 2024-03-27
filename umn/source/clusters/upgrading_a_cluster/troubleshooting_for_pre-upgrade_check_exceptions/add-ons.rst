:original_name: cce_10_0433.html

.. _cce_10_0433:

Add-ons
=======

Check Item
----------

Check the following aspects:

-  Check whether the add-on status is normal.
-  Check whether the add-on support the target version.

Solution
--------

-  **Scenario 1: The add-on status is abnormal.**

   Log in to the CCE console and go to the target cluster. Choose **O&M** > **Add-ons** to view and handle the abnormal add-on.

-  **Scenario 2: The target version does not support the current add-on.**

   The add-on cannot be automatically upgraded with the cluster. Log in to the CCE console and go to the target cluster. Choose **O&M** > **Add-ons** to manually upgrade the add-on.

-  **Scenario 3: After the add-on is upgraded to the latest version, the target cluster version is still not supported.**

   Log in to the CCE console and go to the target cluster. Choose **O&M** > **Add-ons** to manually uninstall the add-on. For details about the supported add-on versions and replacement solutions, see the :ref:`Help <cce_10_0277>` document.

-  **Scenario 4: The add-on configuration does not meet the upgrade requirements. Upgrade the add-on and try again.**

   The following error information is displayed during the pre-upgrade check:

   .. code-block::

      please upgrade addon [ ] in the page of addon managecheck and try again

   Log in to the CCE console and go to the target cluster. Choose **O&M** > **Add-ons** to manually upgrade the add-on.
