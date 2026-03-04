:original_name: cce_10_0495.html

.. _cce_10_0495:

Key Node Commands
=================

Check Items
-----------

Whether some key commands that the node upgrade depends on are working

Solutions
---------

-  Scenario 1: Executing the RPM package manager command failed.

   Executing **rpm** failed. In this case, log in to the affected node and check whether the following command is available:

   .. code-block::

      rpm -qa

   If the command cannot be used, run the following command:

   .. code-block::

      rpm --rebuilddb

   If the fault persists, reset the node. For details, see :ref:`Resetting a Node <cce_10_0003>`.

-  Scenario 2: Executing the dpkg package manager command failed.

   Executing **dpkg** failed. In this case, log in to the affected node and check whether the following command is available:

   .. code-block::

      dpkg -l

   If the command cannot be used, reset the node. For details, see :ref:`Resetting a Node <cce_10_0003>`.

-  Scenario 2: Executing **systemctl status** failed.

   If **systemctl status** cannot be used on a node, many check items will fail. In this case, log in to the affected node and check whether the following command is available:

   .. code-block::

      systemctl status kubelet

   If **systemctl** cannot be used, reset the node. For details, see :ref:`Resetting a Node <cce_10_0003>`.
