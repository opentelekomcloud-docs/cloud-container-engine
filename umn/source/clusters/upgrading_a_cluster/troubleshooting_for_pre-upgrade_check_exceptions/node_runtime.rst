:original_name: cce_10_0516.html

.. _cce_10_0516:

Node Runtime
============

Check Items
-----------

Check whether an alarm is generated when a cluster is upgraded to v1.27 or later. Do not use Docker in clusters of versions later than 1.27.

Solutions
---------

If your node's runtime is not containerd, change it to containerd by resetting the node.

If you plan to create and use Docker nodes in clusters of versions later than v1.27, ignore this alarm. However, you are advised to switch to containerd as soon as possible for a better user experience and more powerful functions.
