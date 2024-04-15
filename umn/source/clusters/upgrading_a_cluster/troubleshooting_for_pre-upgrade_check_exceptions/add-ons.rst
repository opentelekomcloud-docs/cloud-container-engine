:original_name: cce_10_0433.html

.. _cce_10_0433:

Add-ons
=======

Check Items
-----------

Check the following items:

-  Check whether the add-on status is normal.
-  Check whether the add-on support the target version.

Solution
--------

-  **Scenario 1: The add-on malfunctions.**

   Log in to the CCE console and click the cluster name to access the cluster console. Choose **Add-ons** in the navigation pane and obtain add-ons. Then, handle malfunctional add-ons.

-  **Scenario 2: The target cluster version does not support the current add-on version.**

   The add-on cannot be automatically upgraded with the cluster due to compatibility issues. In this case, log in to the CCE console and click the cluster name to access the cluster console. Choose **Add-ons** in the navigation pane and manually upgrade the add-on.

-  **Scenario 3: After the add-on is upgraded to the latest version, it is still not supported by the target cluster version.**

   Log in to the CCE console and click the cluster name to access the cluster console. Choose **Add-ons** in the navigation pane and manually uninstall the add-on. For details about the supported add-on versions and substitutions, see the :ref:`Help <cce_10_0277>` document.

-  **Scenario 4: The add-on configuration does not meet the upgrade requirements. Upgrade the add-on and try again.**

   The following error information is displayed during the pre-upgrade check:

   .. code-block::

      please upgrade addon [ ] in the page of addon managecheck and try again

   In this case, log in to the CCE console and click the cluster name to access the cluster console. Choose **Add-ons** in the navigation pane and manually upgrade the add-on.
