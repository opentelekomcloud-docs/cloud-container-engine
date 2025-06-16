:original_name: cce_10_0432.html

.. _cce_10_0432:

Upgrade Management
==================

Check Items
-----------

Check whether the target cluster is under upgrade management.

Solution
--------

CCE may temporarily restrict the cluster upgrade due to the following reasons:

-  The cluster is identified as the core production cluster.
-  Other O&M tasks are either in progress or planned, such as the 3-AZ reconstruction on master nodes.
-  If the cluster contains Docker nodes but their OSs are different from that of the node pool, reset these nodes and run the pre-upgrade check again.

To resolve this issue, contact technical support based on logs displayed on the console.
