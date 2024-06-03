:original_name: cce_10_0510.html

.. _cce_10_0510:

Check containerd pod restart risk
=================================

Check Items
-----------

Check whether the service container running on the node may restart when the containerd component is upgraded on the node that uses containerd in the current cluster.

Solution
--------

Ensure that the cluster is upgraded when the impact on services is controllable (for example, during off-peak hours) to mitigate the impact of service container restart. If you need help, contact O&M personnel.
