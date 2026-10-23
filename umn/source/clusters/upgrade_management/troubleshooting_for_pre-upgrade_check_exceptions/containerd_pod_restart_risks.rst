:original_name: cce_10_0510.html

.. _cce_10_0510:

containerd Pod Restart Risks
============================

Check Items
-----------

Check whether the service pods running on a containerd node are restarted when containerd is upgraded.

Solutions
---------

containerd on your node may need to be restarted. To minimize the impact on service containers, upgrade the cluster when the impact on services is controllable (for example, during off-peak hours).

If you need help, submit a service ticket to contact O&M personnel.
