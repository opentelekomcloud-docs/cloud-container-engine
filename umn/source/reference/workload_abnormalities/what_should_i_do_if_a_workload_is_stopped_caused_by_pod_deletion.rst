:original_name: cce_faq_00012.html

.. _cce_faq_00012:

What Should I Do If a Workload Is Stopped Caused by Pod Deletion?
=================================================================

Cause:
------

The **metadata.enable** field in the YAML file of the workload is **false**. As a result, the pod of the workload is deleted and the workload is in the stopped status.

|image1|

Problem
-------

The workload status is **Stopped**.

Solution
--------

Delete the **enable** field or set it to **true**.

.. |image1| image:: /_static/images/en-us_image_0000001178034096.png
