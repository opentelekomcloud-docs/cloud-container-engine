:original_name: cce_10_0447.html

.. _cce_10_0447:

Node Key Directory File Permissions
===================================

Check Items
-----------

Check whether the owner and owner group of the files in the **/var/paas** directory used by the CCE are both **paas**.

Solution
--------

-  **Scenario 1: The error message "xx file permission has been changed!" is displayed.**

   Solution: Enable CCE to use the **/var/paas** directory to manage nodes and store file data whose owner and owner group are both **paas**.

   During the current cluster upgrade, the owner and owner group of the files in the **/var/paas** directory are reset to paas.

   Check whether file data in the current service pod is stored in the **/var/paas** directory. If yes, do not use this directory, remove abnormal files from this directory, and check again. After the check is passed, proceed with the upgrade.

   .. code-block::

      find /var/paas -not \( -user paas -o -user root \) -print

-  **Scenario 2: The error message "user paas must have at least read and execute permissions on the root directory" is displayed.**

   Solution: Change the permission on the root directory to the default permission 555. If the permission on the root directory of the node is modified, user **paas** does not have the read permission on the root directory. As a result, restarting the component failed during the upgrade.
