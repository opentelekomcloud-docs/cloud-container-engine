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

   -  rpm:

      .. code-block::

         rpm -qa

      Run the following command for recovery:

      .. code-block::

         rpm --rebuilddb

   -  dpkg:

      .. code-block::

         dpkg -l

      Run the following command for recovery:

      .. code-block::

         dpkg --configure -a

-  Scenario 2: The **systemctl status** command fails to be executed.

   If the **systemctl status** command on a node is unavailable, many check items will be affected. Log in to the node and check the availability of the following commands:

   .. code-block::

      systemctl status kubelet

-  Scenario 3: Executing the Python command failed.

   Check whether the command can be executed on the node.

   .. code-block::

      /usr/bin/python --version
