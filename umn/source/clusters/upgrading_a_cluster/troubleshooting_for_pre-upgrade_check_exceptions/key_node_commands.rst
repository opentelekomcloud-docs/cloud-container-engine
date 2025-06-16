:original_name: cce_10_0495.html

.. _cce_10_0495:

Key Node Commands
=================

Check Items
-----------

Whether some key commands that the node upgrade depends on are working

Solution
--------

-  Scenario 1: Executing the package manager command failed.

   Executing the **rpm** or **dpkg** command failed. In this case, log in to the affected node and check whether the following commands are available:

   .. code-block::

      rpm -qa

   If the preceding command is unavailable, run the following command:

   .. code-block::

      rpm --rebuilddb

-  Scenario 2: The **systemctl status** command fails to be executed.

   If the **systemctl status** command on a node is unavailable, many check items will be affected. Log in to the node and check the availability of the following commands:

   .. code-block::

      systemctl status kubelet

If the fault persists, reset the node. For details, see :ref:`Resetting a Node <cce_10_0003>`.
