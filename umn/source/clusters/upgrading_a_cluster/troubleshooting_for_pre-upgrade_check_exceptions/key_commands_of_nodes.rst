:original_name: cce_10_0495.html

.. _cce_10_0495:

Key Commands of Nodes
=====================

Check Items
-----------

Whether some key commands that the node upgrade depends on are working

Solution
--------

-  Scenario 1: The package manager command fails to be executed.

   The **rpm** or **dpkg** command fails to be executed. Log in to the node and check whether the following commands are available:

   -  rpm:

      .. code-block::

         rpm -qa

   -  dpkg:

      .. code-block::

         dpkg -l

-  Scenario 2: The **systemctl status** command fails to be executed.

   If the **systemctl status** command on a node is unavailable, many check items will be affected. Log in to the node and check the availability of the following commands:

   .. code-block::

      systemctl status kubelet
