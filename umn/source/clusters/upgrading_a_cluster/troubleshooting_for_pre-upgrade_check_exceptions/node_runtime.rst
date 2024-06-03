:original_name: cce_10_0516.html

.. _cce_10_0516:

Node Runtime
============

Check Items
-----------

Check whether an alarm is generated when a cluster is upgraded to v1.27 or later. Do not use Docker in clusters of versions later than 1.27.

Solution
--------

If the runtime on your node is not containerd, change the runtime of the node to containerd by resetting the node.
