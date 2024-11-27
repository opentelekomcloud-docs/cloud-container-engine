:original_name: cce_10_0517.html

.. _cce_10_0517:

Node Pool Runtime
=================

Check Items
-----------

Check whether an alarm is generated when a cluster is upgraded to v1.27 or later. Do not use Docker in clusters of versions later than 1.27.

Solution
--------

If your node pool's runtime is not containerd, change it to containerd by updating the node pool.
