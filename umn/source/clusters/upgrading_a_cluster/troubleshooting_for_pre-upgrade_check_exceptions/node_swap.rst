:original_name: cce_10_0507.html

.. _cce_10_0507:

Node Swap
=========

Check Items
-----------

Check whether swap has been enabled on CCE nodes.

Solution
--------

By default, swap is disabled on CCE nodes. Check the necessity of enabling swap manually and determine the impact of disabling this function.

If there is no impact, run the **swapoff -a** command to disable swap and perform the check again.
