:original_name: cce_10_0440.html

.. _cce_10_0440:

Discarded Kubernetes Resources
==============================

Check Items
-----------

Check whether there are discarded resources in the clusters.

Solution
--------

**Scenario: The Service in the clusters of v1.25 or later has discarded annotation:** **tolerate-unready-endpoints.**

Error log:

.. code-block::

   some check failed in cluster upgrade: this cluster has deprecated service list: map[***] with deprecated annotation list [tolerate-unready-endpoints]

Check whether the Service provided in the log information contains the annotation of **tolerate-unready-endpoints**. If yes, replace the annotation with the following fields:

.. code-block::

   publishNotReadyAddresses: true
