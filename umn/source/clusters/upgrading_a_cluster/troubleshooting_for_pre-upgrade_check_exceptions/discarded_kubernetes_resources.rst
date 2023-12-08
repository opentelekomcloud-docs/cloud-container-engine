:original_name: cce_10_0440.html

.. _cce_10_0440:

Discarded Kubernetes Resources
==============================

Check Item
----------

Check whether there are discarded resources in the clusters.

Solution
--------

**Scenario 1: The PodSecurityPolicy resource object has been discarded since clusters of 1.25.**

Run the **kubectl get psp -A** command in the cluster to obtain the existing PSP object.

If these two objects are not used, skip the check. Otherwise, upgrade the corresponding functions to PodSecurity by referring to :ref:`Pod Security <cce_10_0465>`.

**Scenario 2: The Service in the clusters of 1.25 or later has discarded annotation:** **tolerate-unready-endpoints.**

Check whether the Service provided in the log information contains the annotation of **tolerate-unready-endpoints**. If yes, replace the annotation with the following fields:

.. code-block::

   publishNotReadyAddresses: true
