:original_name: cce_10_0448.html

.. _cce_10_0448:

Kubelet
=======

Check Items
-----------

Check whether the kubelet on the node is running properly.

Solution
--------

-  **Scenario 1: The kubelet status is abnormal.**

   If the kubelet malfunctions, the node is unavailable. Restore the node and check again. For details, see

-  **Scenario 2: The cce-pause version is incorrect.**

   The version of the pause container image on which kubelet depends is not cce-pause:3.1. If you continue the upgrade, pods will restart in batches. Currently, the upgrade is not supported. Contact technical support.
