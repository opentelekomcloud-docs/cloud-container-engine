:original_name: cce_10_0434.html

.. _cce_10_0434:

Checking the Helm Chart
=======================

Check Item
----------

Check whether the current HelmRelease record contains discarded Kubernetes APIs that are not supported by the target cluster version. If yes, the Helm chart may be unavailable after the upgrade.

Solution
--------

Convert the discarded Kubernetes APIs to APIs that are compatible with both the source and target versions.

.. note::

   This item has been automatically processed in the upgrade process. You can ignore this item.
