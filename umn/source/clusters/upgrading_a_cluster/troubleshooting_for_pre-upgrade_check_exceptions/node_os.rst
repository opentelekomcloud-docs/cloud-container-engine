:original_name: cce_10_0451.html

.. _cce_10_0451:

Node OS
=======

Check Item
----------

Check whether the OS kernel version of the node is supported by CCE.

Solution
--------

Running nodes depend on the initial standard kernel version when they are created. CCE has performed comprehensive compatibility tests based on this kernel version. A non-standard kernel version may cause unexpected compatibility issues during node upgrade and the node upgrade may fail. For details, see :ref:`High-Risk Operations and Solutions <cce_10_0054>`.

Currently, this type of nodes should not be upgraded. You are advised to reset the node to the standard kernel version before the upgrade by following the instructions in :ref:`Resetting a Node <cce_10_0003>`.
