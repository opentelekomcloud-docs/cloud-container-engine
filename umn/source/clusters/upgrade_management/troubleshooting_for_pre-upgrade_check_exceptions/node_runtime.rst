:original_name: cce_10_0516.html

.. _cce_10_0516:

Node Runtime
============

Check Items
-----------

Check whether an alarm is generated when a cluster is upgraded to v1.27 or later. Do not use Docker in clusters of versions later than 1.27.

Solutions
---------

If your node does not use containerd, reset the node to switch the runtime to containerd.

If you still want to create or use Docker nodes in clusters later than v1.27, you can skip this alarm. However, switch to containerd as soon as possible for better performance and functionality.
