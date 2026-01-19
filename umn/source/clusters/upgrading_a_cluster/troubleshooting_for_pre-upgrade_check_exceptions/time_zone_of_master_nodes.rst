:original_name: cce_10_0529.html

.. _cce_10_0529:

Time Zone of Master Nodes
=========================

Check Items
-----------

Check whether the time zone of the master nodes matches the cluster's time zone. If they are different, the master nodes will be updated to match the cluster's time zone during a rolling upgrade.

If there is a CronJob in your cluster, it may unexpectedly trigger an execution after the upgrade.

Solutions
---------

Before the upgrade, disable the CronJob and perform the pre-upgrade check. After the upgrade is complete, enable the CronJob.
