:original_name: cce_10_0512.html

.. _cce_10_0512:

GPU/NPU Pod Rebuilding Risk
===========================

Check Items
-----------

Check whether the node on which GPU/NPU service containers run is rebuilt when kubelet is restarted during the upgrade of the current cluster. If yes, the services will be affected.

Solutions
---------

Upgrade the cluster when the impact on services is controllable (for example, during off-peak hours) to minimize the impact. If you need help, contact O&M personnel.
