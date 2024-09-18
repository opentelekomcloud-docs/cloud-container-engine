:original_name: cce_10_0494.html

.. _cce_10_0494:

sudo
====

Check Items
-----------

Check whether the sudo commands and sudo-related files of the node are working.

Solution
--------

-  Scenario 1: The sudo command fails to be executed.

   During the in-place cluster upgrade, the sudo command must be available. Log in to the node and run the following command to check whether the sudo command is available:

   .. code-block::

      sudo echo hello

-  Scenario 2: Key files cannot be modified.

   During the in-place cluster upgrade, the **/etc/sudoers** and **/etc/sudoers.d/sudoerspaas** files are modified to obtain the sudo permission and update the components (such as Docker and kubelet) whose owner and owner group are **root** and related configuration files on the node. Log in to the node and run the following command to check whether the file can be modified:

   .. code-block::

      lsattr -l /etc/sudoers.d/sudoerspaas /etc/sudoers

   If **immutable** is displayed in the command output, the file is locked by the **i** lock and cannot be modified. You are advised to remove the **i** lock.

   .. code-block::

      chattr -i /etc/sudoers.d/sudoerspaas /etc/sudoers
