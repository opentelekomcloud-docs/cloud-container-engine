:original_name: cce_10_0530.html

.. _cce_10_0530:

SNATIPRanges
============

Check Items
-----------

Check whether the SNATIPRanges value has changed after the upgrade. This check is available only for CCE Turbo clusters.

What Is SNATIPRanges?
---------------------

In a CCE Turbo cluster, the CIDR blocks in SNATIPRanges are reserved for the cloud service. When a pod is created, the SNATIPRanges configuration will be automatically written into the pod's route table for the pod to access other cloud services.

To check the SNATIPRanges value of a cluster, run the following command:

.. code-block::

   kubectl get yc default -oyaml | grep snatIPRanges

You can also run the **ip route** command in the pod to check the pod's route table.

|image1|

Solution
--------

When you update SNATIPRanges in a region's backbone configuration, the change takes effect after the cluster is upgraded. However, the pod's route table will not be automatically updated. To fix this, simply restart the pod to refresh its route table.

CCE automatically checks if the SNATIPRanges configuration has been updated before a cluster upgrade. You can manually skip this check item and restart the pod after the upgrade to update its route table.

.. |image1| image:: /_static/images/en-us_image_0000002218820242.png
