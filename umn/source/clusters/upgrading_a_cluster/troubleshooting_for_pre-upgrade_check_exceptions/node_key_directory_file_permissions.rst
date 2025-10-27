:original_name: cce_10_0447.html

.. _cce_10_0447:

Node Key Directory File Permissions
===================================

Check Items
-----------

Check whether the root directory permissions are properly assigned.

Solution
--------

-  **Scenario 1: The error message "user paas must have at least read and execute permissions on the root directory" is displayed.**

   Solution: Change the permission on the root directory to the default permission 555. If the permission on the root directory of the node is modified, user **paas** does not have the read permission on the root directory. As a result, restarting the component failed during the upgrade.
