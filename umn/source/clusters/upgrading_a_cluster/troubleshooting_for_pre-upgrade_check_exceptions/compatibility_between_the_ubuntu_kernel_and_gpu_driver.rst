:original_name: cce_10_0521.html

.. _cce_10_0521:

Compatibility Between the Ubuntu Kernel and GPU Driver
======================================================

Check Items
-----------

Make sure that CCE AI Suite (NVIDIA GPU) and Ubuntu nodes are compatible before using them in a cluster. If the Ubuntu kernel is 5.15.0-113-generic, the driver of the GPU add-on must be 535.161.08 or later.

Solution
--------

This issue may occur when you create or reset an Ubuntu node after an upgrade. In this case, upgrade the CCE AI Suite (NVIDIA GPU) driver to version 535.161.08 or later, and restart the node.
