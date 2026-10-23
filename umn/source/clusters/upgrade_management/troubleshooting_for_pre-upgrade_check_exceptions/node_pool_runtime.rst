:original_name: cce_10_0517.html

.. _cce_10_0517:

Node Pool Runtime
=================

Check Items
-----------

Check whether an alarm is generated when a cluster is upgraded to v1.27 or later. Docker will be removed in a future release of CCE. Do not use Docker in clusters running v1.27 or later.

Solutions
---------

If your node pool does not use containerd, update the node pool to switch the runtime to containerd. For details, see :ref:`Changing the Container Runtime from Docker to containerd <cce_10_0601>`.
