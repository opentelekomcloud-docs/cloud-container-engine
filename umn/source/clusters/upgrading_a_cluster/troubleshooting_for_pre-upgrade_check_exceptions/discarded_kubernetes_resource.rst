:original_name: cce_10_0440.html

.. _cce_10_0440:

Discarded Kubernetes Resource
=============================

Check Item
----------

Check whether there are discarded resources in the clusters.

Solution
--------

**Scenario 1: The PodSecurityPolicy resource object has been discarded since clusters of v1.25.**

|image1|

Run the **kubectl get psp -A** command in the cluster to obtain the existing PSP object.

If these two objects are not used, skip the check. Otherwise, upgrade the corresponding functions to PodSecurity by referring to :ref:`Pod Security <cce_10_0465>`.

**Scenario 2: The discarded annotation (tolerate-unready-endpoints) exists in Services in clusters of 1.25 or later.**

|image2|

Check whether the Service in the log information contains the annotation **tolerate-unready-endpoints**. If yes, delete the annotation and add the following field to the spec of the corresponding Service to replace the annotation:

.. code-block::

   publishNotReadyAddresses: true

.. |image1| image:: /_static/images/en-us_image_0000001569022901.png
.. |image2| image:: /_static/images/en-us_image_0000001517903056.png
