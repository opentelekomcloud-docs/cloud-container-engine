:original_name: cce_10_0515.html

.. _cce_10_0515:

Subnet Quota of Master Nodes
============================

Check Items
-----------

Check whether the number of available IP addresses in the cluster subnet supports rolling upgrade.

Solution
--------

Rolling upgrade is not supported if there are not enough IP addresses in the selected cluster subnet.

Move nodes out of the target subnet and check again. If you are unsure about the impact of migration, submit a service ticket to contact O&M personnel.
