:original_name: cce_10_0447.html

.. _cce_10_0447:

Node Key Directory File Permissions
===================================

Check Item
----------

Check whether the key directory **/var/paas** on the nodes contain files with abnormal owners or owner groups.

Solution
--------

CCE uses the **/var/paas** directory to manage nodes and store file data whose owner and owner group are both paas.

During the current cluster upgrade, the owner and owner group of the files in the **/var/paas** directory are reset to paas.

Check whether file data is stored in the **/var/paas** directory. If yes, do not use this directory, remove abnormal files from this directory, and check again. Otherwise, the upgrade is prohibited.
